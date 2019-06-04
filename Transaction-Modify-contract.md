### Contract modify transaction
Contract transactions are used by ProximaX decentralized storage to store information about stakeholders of stored files in form of contracts in blockchain. Building contract including creating multisig account and aggregate bounded transaction.

````go
package main

import (
	"context"
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

  // customer account
  customer = ...
  // contract multisig account
  contract = ...
  // replicator accounts
  replicator1 = ...
  replicator2 = ...
  // verificator accounts
  verificator1 = ...
  verificator2 = ...
  // valid file hash
  fileHash = "cf893ffcc47c33e7f68ab1db56365c156b0736824a0c1e273f9e00b8df8f01eb"
)

func main() {

	// Testnet config default
	conf, err := sdk.NewConfig(baseUrl,networkType)
	if err != nil {
		panic(err)
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)


	// Create a modify contract transaction
	trx, err := sdk.NewModifyContractTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour * 1),
    // Duration of contract in blocks
    1000,
    // Hash of file which is being uploaded
    fileHash,
    // adding customer to the contract
    []*sdk.MultisigCosignatoryModification{ {sdk.Add, customer.PublicAccount} },
    // adding replicators to the contract
    []*sdk.MultisigCosignatoryModification{ {sdk.Add, replicator1.PublicAccount}, {sdk.Add, replicator2.PublicAccount} },
    // adding verificators to the contract
    []*sdk.MultisigCosignatoryModification{ {sdk.Add, verificator1.PublicAccount}, {sdk.Add, verificator2.PublicAccount} },
		networkType,
	)
  if err != nil {
    panic(err)
  }

  // Sign create contract transaction by contract account
  signedTrx, err := contract.Sign(trx)
  if err != nil {
    panic(err)
  }

  // Announce transaction to the blockchain
  _, err = client.Transaction.Announce(context.Background(), signedTrx)
  if err != nil {
    panic(err)
  }

	// wait for the transaction to be confirmed! (very important)
    // you can use websockets to wait explicitly for transaction
    // to be in certain state, instead of hard waiting
	time.Sleep(time.Second * 30)
}
````
