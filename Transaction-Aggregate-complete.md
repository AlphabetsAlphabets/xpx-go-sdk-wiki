### Aggregate complete transaction
An aggregate transaction is complete if before announcing it to the network, all cosigners have signed it. If valid, it will be included in a block.
* **In this case, one private key can sign all the transactions in the aggregate, so it is aggregate complete.**
- Sending transactions to different recipients atomically
  * Aggregate transactions accept the following parameters:
    * **Inner Transaction**: Transactions initiated by different accounts. An aggregate transaction can contain up to 1000 inner transactions. Other aggregate transactions are not allowed as inner transactions.
````go
package main

import (
	"context"
	"fmt"
	"github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
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
	privateKey = "24CEC4F2DD1A28EBB2C0CF6D4D181BA2C0F1E215C42B9059EEFB65B1FAEE1B99"
)

func main() {

	// Testnet config default
	conf, err := sdk.NewConfig(baseUrl,networkType)
	if err != nil {
		panic(err)
	}

	// Create an account from a private key
	acc, err := sdk.NewAccountFromPrivateKey(privateKey, networkType)
	if err != nil {
	    panic(err)
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	deadline := sdk.NewDeadline(time.Hour*1)

	// Create a new transfer type transaction
	ttxOne, err := sdk.NewTransferTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		deadline,
		// The address of the recipient account.
		sdk.NewAddress("SCW2KXPU3MCFUASDJAWFPOYYXQHFX76ESSX4QXDN", networkType),
		// The array of mosaic to be sent.
		[]*sdk.Mosaic{sdk.Xpx(10)},
		// The transaction message of 1024 characters.
		sdk.NewPlainMessage(""),
		networkType,
	)

	ttxTwo, err := sdk.NewTransferTransaction(
	    // The maximum amount of time to include the transaction in the blockchain.
		deadline,
	    // The address of the recipient account.
		sdk.NewAddress("SCTJXIHO62UZDNA3A3RHQUXP3EWXMCUGWSJAV2CM", networkType),
	    // The array of mosaic to be sent.
		[]*sdk.Mosaic{sdk.Xpx(10)},
	    // The transaction message of 1024 characters.
		sdk.NewPlainMessage(""),
		networkType,
	)

	// Convert an aggregate transaction to an inner transaction including transaction signer.
	ttxOne.ToAggregate(acc.PublicAccount)
	ttxTwo.ToAggregate(acc.PublicAccount)

	// Create an aggregate complete transaction
	aggregateTransaction, err := sdk.NewCompleteAggregateTransaction(deadline, []sdk.Transaction{ttxOne, ttxTwo}, networkType)
	if err != nil {
		panic(err)
	}

	// Sign transaction
	stx, err := acc.Sign(aggregateTransaction)
	if err != nil {
		panic(fmt.Errorf("TransaferTransaction signing returned error: %s", err))
	}

	// Announce transaction
	restTx, err := client.Transaction.Announce(context.Background(), stx)
	if err != nil {
		panic(err)
	}
	fmt.Printf("%s\n", restTx)
	fmt.Printf("Hash: \t\t%v\n", stx.Hash)
	fmt.Printf("Signer: \t%X\n", acc.KeyPair.PublicKey.Raw)
}
````
