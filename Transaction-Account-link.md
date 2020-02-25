### Account link transaction

Account link transaction is used to delegate the account importance to a proxy account.
To create Account link transaction use **NewAccountLinkTransaction()**.

- Following parameters required:
  - **PublicAccount** - The remote account to which we want to delegate importance.
  - **AccountLinkAction** - The type of operation `AccountLink`/`AccountUnlink`

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
    // A valid public key
    publicKey = "6EED33183360FC92CD8EBD61EB8859FB984EC164B94CF496398C91BC5B0769B3"
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

    // Create an account from a public key
    remoteAccount, err := client.NewAccountFromPublicKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
        return
    }

    // Create a address alias transaction
    transaction, err := client.NewAccountLinkTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour),
        // The remote account to which signer wants to delegate importance.
        remoteAccount,
        // The type of action ( in this case we want to link entities ).
        sdk.AccountLink,
    )
    if err != nil {
        fmt.Printf("NewAccountLinkTransaction returned error: %s", err)
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
