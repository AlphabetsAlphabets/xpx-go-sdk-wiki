### Address Alias Transaction

Address alias transaction is used to link an address to a namespace.
To create Address Alias transaction use **NewAddressAliasTransaction()**.

- Following parameters required:
  - **Address** - The address which we want to link.
  - **NamespaceId** - The namespace id which we want to link.
  - **AliasActionType** - The type of operation `AliasLink`/`AliasUnlink`

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
    // A valid private key
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

    // Create namespace id from it's name
    namespaceId, err := sdk.NewNamespaceIdFromName("super_me")
    if err != nil {
        fmt.Printf("NewNamespaceIdFromName returned error: %s", err)
        return
    }

    // Create a address alias transaction
    transaction, err := client.NewAddressAliasTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour),
        // The address which we want to link
        account.PublicAccount.Address,
        // The namespace id which we want to link.
        namespaceId,
        // The type of action ( in this case we want to link entities ).
        sdk.AliasLink,
    )
    if err != nil {
        fmt.Printf("NewAddressAliasTransaction returned error: %s", err)
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

### Mosaic Alias Transaction

Mosaic alias transaction is used to link a mosaic to a namespace.
To create Mosaic Alias transaction use **NewMosaicAliasTransaction()**.

- Following parameters required:
  - **MosaicId** - The mosaic id which we want to link.
  - **NamespaceId** - The namespace id which we want to link.
  - **AliasActionType** - The type of operation `AliasLink`/`AliasUnlink`

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
    // A valid private key
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

    // Create namespace id from it's name
    namespaceId, err := sdk.NewNamespaceIdFromName("super_me")
    if err != nil {
        fmt.Printf("NewNamespaceIdFromName returned error: %s", err)
        return
    }

    // Create mosaic id from it's id
    mosaicId, err := sdk.NewMosaicId(0)
    if err != nil {
        fmt.Printf("NewMosaicId returned error: %s", err)
        return
    }

    // Create a address alias transaction
    transaction, err := client.NewMosaicAliasTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour),
        // The mosaic id which we want to link.
        mosaicId,
        // The namespace id which we want to link.
        namespaceId,
        // The type of action ( in this case we want to link entities ).
        sdk.AliasLink,
    )
    if err != nil {
        fmt.Printf("NewMosaicAliasTransaction returned error: %s", err)
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

