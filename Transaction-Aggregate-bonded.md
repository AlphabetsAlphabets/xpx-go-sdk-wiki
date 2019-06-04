### Aggregate bonded transactions
An aggregate transaction is considered to be bonded if requires signatures from other participants.
* When sending an aggregate bonded transaction, an account must first announce and get confirmed a Lock Funds Transaction for this aggregate with at least 10 XPX. This mechanism is required to prevent network spamming.
* Once the related aggregate bonded transaction is confirmed, locked funds become available again in the account that signed the initial lock funds transaction.

````go
package main

import (
	"context"
	"fmt"
	"github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
	"math/big"
	"time"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"

	// Types of network.
	// MainNet:   104
	// TestNet:   152
	// Mijin:     96
	// MijinTest: 144
	networkType = sdk.MijinTest

	// A valid private key.
	privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
	publicKey  = "E79C330B6AD032EB4E4B589387B4D209801D5453B82AE4C8445EB3370F918572"
)

func main() {

	// Testnet config default
	conf, err := sdk.NewConfig(baseUrl,networkType)
	if err != nil {
		panic(err)
	}

	// Create an account from a private key
	accOne, err := sdk.NewAccountFromPrivateKey(privateKey, networkType)

	// Create an account from a public key
	accTwo, err := sdk.NewAccountFromPublicKey(publicKey, networkType)

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	deadline := sdk.NewDeadline(time.Hour * 1)

	// Create a new transfer type transaction
	ttxOne, err := sdk.NewTransferTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		deadline,
		// The address of the recipient account.
		sdk.NewAddress("SBP7IXHOM4A5KGFIGBOXYX6RQFCOAAJCUVKCQMGX", networkType),
		// The array of mosaic to be sent.
		[]*sdk.Mosaic{sdk.Xpx(50)},
		// The transaction message of 1024 characters.
		sdk.NewPlainMessage("Purchase tickets"),
		networkType,
	)

  nonce := rand.New(rand.NewSource(time.Now().UTC().UnixNano())).Uint32()

  mosaicId, err := sdk.NewMosaicIdFromNonceAndOwner(nonce, accOne.Address.Address)
	euro := sdk.Mosaic{
		MosaicId: mosaicId,
		Amount:   big.NewInt(10),
	}

	ttxTwo, err := sdk.NewTransferTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		deadline,
		// The address of the recipient account.
		sdk.NewAddress("SDRXCTNBMDTUGZDJICELNF3Y5KZJLOI2I6E2BXJM", networkType),
		// The array of mosaic to be sent.
		[]*sdk.Mosaic{&euro},
		// The transaction message of 1024 characters.
		sdk.NewPlainMessage("Tickets sale"),
		networkType,
	)

	// Convert an aggregate transaction to an inner transaction including transaction signer.
	ttxOne.ToAggregate(accOne.PublicAccount)
	ttxTwo.ToAggregate(accTwo)

	// Create an aggregate bonded transaction
	aggregateTransaction, err := sdk.NewBondedAggregateTransaction(deadline, []sdk.Transaction{ttxOne, ttxTwo}, networkType)
	if err != nil {
		panic(fmt.Errorf("BondedAggregateTransaction returned error: %s", err))
	}

	// Sign aggregate bonded transaction
	aggregateSigned, err := accOne.Sign(aggregateTransaction)
	if err != nil {
		panic(fmt.Errorf("BondedAggregateTransaction signing returned error: %s", err))
	}

	// Create an LockFunds transaction
	lockFundsTx, err := sdk.NewLockFundsTransaction(deadline, sdk.Xpx(10000000), big.NewInt(10000), aggregateSigned, networkType)
	if err != nil {
		panic(fmt.Errorf("LockFundsTransaction returned error: %s", err))
	}

	// Sign LockFunds transaction
	lockFundsTxSigned, err := accOne.Sign(lockFundsTx)
	if err != nil {
		panic(fmt.Errorf("TransaferTransaction signing returned error: %s", err))
	}

	// Announce LockFunds transaction
	restlockFundsTx, err := client.Transaction.Announce(context.Background(), lockFundsTxSigned)
	if err != nil {
		panic(fmt.Errorf("AnnounceLockFundsTransaction returned error: %s", err))
	}
	fmt.Printf("%s\n", restlockFundsTx)
	fmt.Printf("Hash: \t\t%v\n", lockFundsTxSigned.Hash)
	fmt.Printf("Signer: \t%X\n", accOne.KeyPair.PublicKey.Raw)

	// wait for the LockFunds transaction to be confirmed! (very important)
    // you can use websockets to wait explicitly for transaction
    // to be in certain state, instead of hard waiting
	time.Sleep(time.Second * 15)

	// Announce aggregate bonded transaction
	restTx, err := client.Transaction.AnnounceAggregateBonded(context.Background(), aggregateSigned)
	if err != nil {
		panic(fmt.Errorf("AnnounceAggregateBondedTransaction returned error: %s", err))
	}
	fmt.Printf("%s\n", restTx)
	fmt.Printf("Hash: \t\t%v\n", aggregateSigned.Hash)
	fmt.Printf("Signer: \t%X\n", accOne.KeyPair.PublicKey.Raw)
}
````
* the next step is to [consolidate the transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Signing-announced-aggregate-bonded-transactions) by the missing cosignature.
