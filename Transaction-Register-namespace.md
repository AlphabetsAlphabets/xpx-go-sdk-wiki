
### Register root namespace transaction

* Following parameters are required:
  * **Namespace name** - name of namespace to create
  * **Duration** - duration of namespace life in blocks

```go
package main


import (
    "context"
    "fmt"
    "time"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
)

const (
    // Catapult-api-rest server.
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

   // Create a new register root-Namespace type transaction
   transaction, err := client.NewRegisterRootNamespaceTransaction(
	// The maximum amount of time to include the transaction in the blockchain.
	sdk.NewDeadline(time.Hour * 1),
	// The namespace Name.
	"newnamespace",
	// The duration per block.
	sdk.Duration(10),
    )
    if err != nil {
        fmt.Printf("NewRegisterRootNamespaceTransaction returned error: %s", err)
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
