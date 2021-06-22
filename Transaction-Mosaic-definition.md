
### Mosaic definition transaction

Before a mosaic can be created or transferred, a corresponding definition of the mosaic has to be created and published to the network.
This could be done via a **NewMosaicDefinitionTransaction()**.

- Following parameters are required:
  - **Nonce** - TODO
  - **Public Key** - public key of future mosaic owner
  - **Mosaic properties**:
      - **supply mutable** - a mosaic could have mutable or immutable supply. Immutable supply means that in the future the creator can  increase totol amount count;
      - **transferability** - could be mosaic be transferred;
      - **divisibility** - determines up to what decimal place the mosaic can be divided. Divisibility of 3 means that a mosaic can be divided into smallest parts of 0.001 mosaics. The divisibility must be in the range of 0 and 6.
      - **duration** - duration of mosaics in blocks. Should be less or equal to namespace duration.

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

	// Create a new mosaic definition type transaction
	transaction, err := client.NewMosaicDefinitionTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour*1),
		// Mosaic nonce
		nonce,
		// Public key of mosaic owner
		account.PublicAccount.PublicKey,
		sdk.NewMosaicProperties(
			// supply mutable
			true,
			// transferability
			true,
			// divisibility
			4,
			// duration
			sdk.Duration(10000),
		),
	)
	if err != nil {
		fmt.Printf("NewMosaicDefinitionTransaction returned error: %s", err)
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
