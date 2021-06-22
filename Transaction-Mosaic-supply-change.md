
### Mosaic supply change transaction

Mosaic supply change transaction is used to assign supply to a mosaic.
This could be done via a **NewMosaicSupplyChangeTransaction()**.

- Following parameters are required:
  - **Mosaic Id** - Mosaic identifier.
  - **Direction**
    - `sdk.Increase`
    - `sdk.Decrease`
  - **Delta** - The amount of supply to increase or decrease.

```go
package main

import (
	"context"
	"fmt"
	"math/rand"
	"time"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
	// Private key of some exist account
	privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
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
	account, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	random := rand.New(rand.NewSource(time.Now().UTC().UnixNano()))
	nonce := random.Uint32()

	mosaicId, _ := sdk.NewMosaicIdFromNonceAndOwner(nonce, account.PublicAccount.PublicKey)

	// Create a new mosaic definition type transaction
	transaction, err := client.NewMosaicSupplyChangeTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour*1),
		// Id of mosaic to change supply
		mosaicId,
		// Supply change direction
		sdk.Increase,
		// Delta
		sdk.Duration(100000000000),
	)
	if err != nil {
		fmt.Printf("NewMosaicSupplyChangeTransaction returned error: %s", err)
		return
	}

	// Sign transaction
	signedTransaction, err := account.Sign(transaction)
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
}
```

