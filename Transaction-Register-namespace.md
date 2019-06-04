
### Register root namespace transaction

* Following parameters are required:
  * **Namespace name**
    * name of namespace to create
  * **Duration**
    * duration of namespace life in blocks

```go
package main

import (
    "context"
    "fmt"
    "math/big"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
)

const (
    networkType = sdk.MijinTest
    // A valid private key
    privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
    // Name of namespace to create
    namespaceName = "mynamespace"
)

func main() {

    conf, err := sdk.NewConfig("http://localhost:3000", networkType)
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Create an account from a private key
    account, err := sdk.NewAccountFromPrivateKey(privateKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // Create a new namespace type transaction
    transaction, err := sdk.NewRegisterRootNamespaceTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour * 1),
        // Name of namespace
        namespaceName,
        // Duration of namespace life in blocks
        big.NewInt(10000),
        networkType,
    )
    if err != nil {
        fmt.Printf("NewRegisterRootNamespaceTransaction returned error: %s", err)
        return
    }

    // Sign transaction
    signedTransaction, err := acc.Sign(transaction)
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    // Announce transaction
    _, err := client.Transaction.Announce(context.Background(), signedTransaction)
    if err != nil {
        fmt.Printf("Transaction.Announce returned error: %s", err)
        return
    }
}
```

### Register sub namespace transaction

* Following parameters are required:
  * **Namespace name**
    * name of namespace to create
  * **Parent namespace name**
    * parent namespace name


```go
package main

import (
    "context"
    "fmt"
    "math/big"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
)

const (
    networkType = sdk.MijinTest
    // A valid private key
    privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
    // Name of namespace to create
    subNamespaceName = "mynamespace"
    // Name of parent namespace
    parentNamespaceName = "myparent"
)

func main() {

    conf, err := sdk.NewConfig("http://localhost:3000", networkType)
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Create an account from a private key
    account, err := sdk.NewAccountFromPrivateKey(privateKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // Create parent namespace id
    parentnamespaceId, err := sdk.NewNamespaceIdFromName(parentNamespaceName)
    if err != nil {
        fmt.Printf("NewNamespaceIdFromName returned error: %s", err)
        return
    }

    // Create a new namespace type transaction
    transaction, err := sdk.NewRegisterSubNamespaceTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour * 1),
        // Name of namespace
        subNamespaceName,
        // Parent namespace id
        parentNamespaceId,
        networkType,
    )
    if err != nil {
        fmt.Printf("NewRegisterSubNamespaceTransaction returned error: %s", err)
        return
    }

    // Sign transaction
    signedTransaction, err := acc.Sign(transaction)
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    // Announce transaction
    _, err := client.Transaction.Announce(context.Background(), signedTransaction)
    if err != nil {
        fmt.Printf("Transaction.Announce returned error: %s", err)
        return
    }
}
```
