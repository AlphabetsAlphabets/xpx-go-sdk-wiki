> *NOTE*: You can use websockets to wait explicitly for transaction instead of just time.Sleep()

## Pre-requisites
Before you do anything you must do the following:
1. If you don't have a normal account create it [here](https://bctestnetwallet.xpxsirius.io/).
	- When creating an account make sure the network the account is created on is on `Sirius Testnet 1`.
2. Create two more accounts. These two accounts will be the cosigners. 
3. Copy the private key from all three accounts.
4. From those three accounts choose which account will become the multisig account and which two will be cosigners. Make sure that the first cosigner has XPX in the wallet. XPX can be gained by visiting a [faucet](https://bctestnetfaucet.xpxsirius.io/#/). The faucet will ask for the address of the account, the address of an account is directly beneath the name of the account.

On success XPX will be deducted from the first cosigner and the account specified to be converted to a multisig account will be converted to one. 

### Modify multisig account transaction

Modify multisig account transaction is used to create multisig
account or edit it's properties. This is done via a
**NewModifyMultisigAccountTransaction()**.

- Following parameters are required:
  - **Minimum Approval Delta** - The number of signatures needed to approve a transaction.
  - **Minimum Removal Delta** - The number of signatures needed to remove a cosignatory.
  - **Modifications** - Array of cosigner accounts added or removed from the
    multisignature account. Multsig cosignatory modification type. Supported types are:
      - `sdk.Add`: Add cosignatory.
      - `sdk.Remove`: Remove cosignatory.

#### How to create multisig account

```go
package main

import (
	"context"
	"fmt"
	"time"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://bctestnet1.brimstone.xpxsirius.io:3000/"
    // Private key of account that will be turned into a multisig account
	multisigPrivateKey = "PRIVATE KEY"

	// Cosignature public keys
	cosignatoryOnePrivateKey      = "PRIVATE KEYS"
	cosignatoryTwoPrivateKey      = "PRIVATE KEYS"
	// Minimal approval count
	minimalApproval = -1
	// Minimal removal count
	minimalRemoval = -1
)

func HandleError(err error) {
	if err != nil {
		fmt.Printf("Encountered error:\n%s", err)
		panic("Found an error")
	}
}

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	HandleError(err)

	client := sdk.NewClient(nil, conf)

	multisig, err := client.NewAccountFromPrivateKey(multisigPrivateKey)
	HandleError(err)

	cosignerOne, err := client.NewAccountFromPrivateKey(cosignatoryOnePrivateKey)
	HandleError(err)

	cosignerTwo, err := client.NewAccountFromPrivateKey(cosignatoryTwoPrivateKey)
	HandleError(err)

	transaction, err := client.NewModifyMultisigAccountTransaction(
		sdk.NewDeadline(time.Hour*1),
		minimalApproval,
		minimalRemoval,
		[]*sdk.MultisigCosignatoryModification{
			{sdk.Add, cosignerOne.PublicAccount}, // To remove a cosigner use sdk.Remove instead
			{sdk.Add, cosignerTwo.PublicAccount},
		},
	)
	HandleError(err)

	transaction.ToAggregate(multisig.PublicAccount)

	aggregateBondedTransaction, err := client.NewBondedAggregateTransaction(
		sdk.NewDeadline(time.Hour*1),
		[]sdk.Transaction{transaction},
	)
	HandleError(err)

	signedAggregateBoundedTransaction, err := cosignerOne.Sign(aggregateBondedTransaction)
	HandleError(err)

	fmt.Println("ABT Tx Hash: ", signedAggregateBoundedTransaction.Hash.String())

	lockFundsTrx, err := client.NewLockFundsTransaction(
		sdk.NewDeadline(time.Hour*1),
		sdk.XpxRelative(10),
		sdk.Duration(230),
		signedAggregateBoundedTransaction,
	)

	signedLockFundsTransaction, err := cosignerOne.Sign(lockFundsTrx)
	HandleError(err)
	fmt.Println("Lock Fund Tx hash: ", signedLockFundsTransaction.Hash.String())

	_, err = client.Transaction.Announce(context.Background(), signedLockFundsTransaction)
	HandleError(err)

	time.Sleep(30 * time.Second)

	lfStatus, err := client.Transaction.GetTransactionStatus(context.Background(), signedLockFundsTransaction.Hash.String())
	HandleError(err)
	fmt.Println(lfStatus)

	// Announce signedAggregateBoundedTransaction
	_, err = client.Transaction.AnnounceAggregateBonded(context.Background(), signedAggregateBoundedTransaction)
	HandleError(err)

    time.Sleep(30 * time.Second)

	// Create cosignature transaction by the first cosigner
	signatureOneCosignatureTransaction := sdk.NewCosignatureTransactionFromHash(signedAggregateBoundedTransaction.Hash)
	signedSignatureOneCosignatureTransaction, err := cosignerTwo.SignCosignatureTransaction(signatureOneCosignatureTransaction)
	if err != nil {
		fmt.Printf("SignCosignatureTransaction returned error: %s", err)
		return
	}

	// Announce transaction
	_, err = client.Transaction.AnnounceAggregateBondedCosignature(context.Background(), signedSignatureOneCosignatureTransaction)
	if err != nil {
		fmt.Printf("AnnounceAggregateBoundedCosignature returned error: %s", err)
		return
	}

	// Create cosignature transaction by the second cosigner
	signatureTwoCosignatureTransaction := sdk.NewCosignatureTransactionFromHash(signedAggregateBoundedTransaction.Hash)
	signedSignatureTwoCosignatureTransaction, err := cosignerTwo.SignCosignatureTransaction(signatureTwoCosignatureTransaction)
	if err != nil {
		fmt.Printf("SignCosignatureTransaction returned error: %s", err)
		return
	}

	// Announce transaction
	_, err = client.Transaction.AnnounceAggregateBondedCosignature(context.Background(), signedSignatureTwoCosignatureTransaction)
	if err != nil {
		fmt.Printf("AnnounceAggregateBoundedCosignature returned error: %s", err)
		return
	}

	// Wait cosignature transaction to be harvested
	time.Sleep(30 * time.Second)

	info, err := client.Account.GetMultisigAccountInfo(context.TODO(), multisig.Address)
	if err != nil {
		fmt.Printf("GetMultisigAccountInfo returned error: %s", err)
		return
	}
	println(info.String())
    println("If everything is well XPX will be deducted from the first cosigner's account, and the account specificed will be converted to a multisig account.")
}
```

