### Transfer Transaction
Transfer transaction is used to send assets between two accounts. It can hold a message of length 1024.
* Following parameters are required:
  * **Recipient**
    * The address of the recipient account.
  * **Mosaics**
    * The array of mosaic to be sent.
  * **Message**
    * The transaction message of 1024 characters.

```go
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
	privateKey = "5D9513282B65A12A1B68DCB67DB64245721F7AE7822BE441FE813173803C512C"
)

func main() {

	// Testnet config default
	conf, err := sdk.NewConfig(baseUrl,networkType)
	if err != nil {
		panic(err)
	}

	// Create an account from a private key
	acc, err := sdk.NewAccountFromPrivateKey(privateKey, networkType)

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Create a new transfer type transaction
	ttx, err := sdk.NewTransferTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour*1),
		// The address of the recipient account.
		sdk.NewAddress("SBILTA367K2LX2FEXG5TFWAS7GEFYAGY7QLFBYKC", networkType),
		// The array of mosaic to be sent.
		[]*sdk.Mosaic{sdk.Xpx(10000000)},
		// The transaction message of 1024 characters.
		sdk.NewPlainMessage(""),
		networkType,
	)

	// Sign transaction
	stx, err := acc.Sign(ttx)
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
```
