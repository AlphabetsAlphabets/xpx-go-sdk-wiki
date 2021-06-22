
> *NOTE*: You can use websockets to wait explicitly for transaction instead of just time.Sleep()

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
	baseUrl = "http://localhost:3000"
	// Existing multisig private key
	multisigPrivateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
	// Cosignature public keys
	cosignatoryOnePrivateKey      = "16DBE8DC29CF06338133DEE64FC49B461CE489BF9588BE3B9670B5CB19C89369"
	cosignatoryTwoPrivateKey      = "461CE489BF9588BE3B9670B5CB19C8936916DBE8DC29CF06338133DEE64FC49B"
	cosignatoryToRemovePrivateKey = "29CF06338133DEE64FC49BCB19C8936916DBE8DC461CE489BF9588BE3B9670B5"
	// Minimal approval count
	minimalApproval = -1
	// Minimal removal count
	minimalRemoval = -1
)

func main() {

	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Create an account from a private key
	multisig, err := client.NewAccountFromPrivateKey(multisigPrivateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
		return
	}

	cosignerOne, err := client.NewAccountFromPrivateKey(cosignatoryOnePrivateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	cosignerTwo, err := client.NewAccountFromPrivateKey(cosignatoryTwoPrivateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	cosignerToRemove, err := client.NewAccountFromPrivateKey(cosignatoryToRemovePrivateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	transaction, err := client.NewModifyMultisigAccountTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour*1),
		// The number of signatures needed to approve a transaction.
		minimalApproval,
		// The number of signatures needed to remove a cosignatory.
		minimalRemoval,
		// Array of cosigner accounts added or removed from the multisignature account.
		[]*sdk.MultisigCosignatoryModification{
			{sdk.Remove, cosignerToRemove.PublicAccount},
		},
	)
	if err != nil {
		fmt.Printf("NewModifyMultisigAccountTransaction returned error: %s", err)
		return
	}
	// Make modify multisig transaction part of aggregate bounded
	transaction.ToAggregate(multisig.PublicAccount)

	// Create a new aggregate bounded transaction
	aggregateBoundedTransaction, err := client.NewBondedAggregateTransaction(
		sdk.NewDeadline(time.Hour*1),
		[]sdk.Transaction{transaction},
	)
	if err != nil {
		fmt.Printf("NewBondedAggregateTransaction returned error: %s", err)
		return
	}

	// Sign by multisig
	signedAggregateBoundedTransaction, err := multisig.Sign(aggregateBoundedTransaction)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	{ // Create lock funds transaction for aggregate bounded
		lockFundsTrx, err := client.NewLockFundsTransaction(
			// The maximum amount of time to include the transaction in the blockchain.
			sdk.NewDeadline(time.Hour*1),
			// Funds to lock
			sdk.XpxRelative(10),
			// Duration of lock transaction in blocks
			sdk.Duration(100),
			// Aggregate bounded transaction for lock
			signedAggregateBoundedTransaction,
		)
		if err != nil {
			fmt.Printf("NewLockFundsTransaction returned error: %s", err)
			return
		}

		// Sign transaction
		signedTransaction, err := cosignerOne.Sign(lockFundsTrx)
		if err != nil {
			fmt.Printf("Sign returned error: %s", err)
			return
		}

		// Announce transaction
		_, err = client.Transaction.Announce(context.Background(), signedTransaction)
		if err != nil {
			fmt.Printf("Transaction.Announce returned error: %s", err)
			return
		}

		// Wait for lock funds transaction to be harvested
		time.Sleep(30 * time.Second)
	}

	// Announce signedAggregateBoundedTransaction
	_, err = client.Transaction.AnnounceAggregateBonded(context.Background(), signedAggregateBoundedTransaction)
	if err != nil {
		fmt.Printf("Transaction.AnnounceAggregateBonded returned error: %s", err)
		return
	}

	// Wait for signedAggregateBoundedTransaction to be harvested
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
}
```
