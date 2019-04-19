### Signing announced aggregate bonded transactions
* You have probably announced an [aggregate bonded transaction](https://github.com/proximax-storage/nem2-sdk-go/wiki/Aggregate-bonded-transactions), but all required cosigners have not signed it yet.

````go
package main

import (
	"context"
	"fmt"
	"github.com/proximax-storage/nem2-sdk-go/sdk"
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
	accTwo, err := sdk.NewAccountFromPrivateKey(privateKey, networkType)

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Fetch all aggregate bonded transactions pending to be signed by your account.
	aggregateBonded, err := client.Account.AggregateBondedTransactions(context.Background(), accTwo.PublicAccount, nil)
	if err != nil {
		fmt.Printf("Account.AggregateBondedTransactions returned error: %s", err)
		return
	}

	// Create a for loop to cosign any aggregate bonded transaction.
	for _, bondedTx := range aggregateBonded {
		cosignatureTransaction, err := sdk.NewCosignatureTransaction(bondedTx)
		if err != nil {
			fmt.Printf("NewCosignatureTransaction returned error: %s", err)
			return
		}

		// Sign Cosignature transaction
		signWithCosignatures, err := accTwo.SignCosignatureTransaction(cosignatureTransaction)
		if err != nil {
			panic(fmt.Errorf("SignCosignatureTransaction returned error: %s", err))
		}

		// Announce Aggregate bonded cosignature
		restlockFundsTx, err := client.Transaction.AnnounceAggregateBondedCosignature(context.Background(), signWithCosignatures)
		if err != nil {
			panic(fmt.Errorf("AnnounceAggregateBondedCosignature returned error: %s", err))
		}
		fmt.Printf("%s\n", restlockFundsTx)
		fmt.Printf("Hash: \t\t%v\n", restlockFundsTx)
		fmt.Printf("Signer: \t%X\n", accTwo.KeyPair.PublicKey.Raw)
	}
}
````
