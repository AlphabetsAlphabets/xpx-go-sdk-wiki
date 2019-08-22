
### Modify metadata transactions

Metadata is a feature of ProximaX Catapult, which allows to attach some
data in format of maps `map[string]string`. Metadata can be attached
to `Address`, `Mosaic`, and `Namespace`.


#### Attach metadata to Address

This is done via a **NewModifyMetadataAddressTransaction()**.

- Following parameters are required:
  - **Address** - address to attach metadata
  - **Metadata Modifications** - an array of Metadata Modification which consists of:
    - MetadataModificationType - type of modification. Supported types:
      - `sdk.AddMetadata`
      - `sdk.RemoveMetadata`
    - Metadata key
    - Metadata value

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
    networkType = sdk.MijinTest
    // Valid private key
    privateKey  = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
)

func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Create an accounts from a private keys
    account, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // Create a modify metadata transaction
    transaction, err := client.NewModifyMetadataAddressTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour * 1),
        // Address where metadata should be attached
        account.PublicAccount.Address,
        // Actual data which will be added/removed
        []*sdk.MetadataModification{
            {
                // add or remove metadata
                sdk.AddMetadata,
                // key which should be used to store data
                "my_key_name",
                // actual data which will be stored in associated key
                "my_data",
            },
        },
    )

    if err != nil {
        fmt.Printf("NewModifyMetadataAddressTransaction returned error: %s", err)
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

    // wait for the transaction to be confirmed! (very important)
    // you can use websockets to wait explicitly for transaction
    // to be in certain state, instead of hard waiting
    time.Sleep(time.Second * 30)

}
```

#### Attach metadata to Mosaic

This is done via a **NewModifyMetadataMosaicTransaction()**.

- Following parameters are required:
  - **Mosaic ID** - mosaic identifier to attach metadata
  - **Metadata Modifications** - an array of Metadata Modification which consists of:
    - MetadataModificationType - type of modification. Supported types:
      - `sdk.AddMetadata`
      - `sdk.RemoveMetadata`
    - Metadata key
    - Metadata value

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
    networkType = sdk.MijinTest
    // Valid private key
    privateKey  = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
)

func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Create an accounts from a private keys
    account, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // nonce of mosaic for which account is owner
    nonce := uint32(0)

    // create mosaic identifier
    mosaicId, err := sdk.NewMosaicIdFromNonceAndOwner(nonce, account.PublicAccount.PublicKey)
    if err != nil {
        fmt.Printf("NewMosaicIdFromNonceAndOwner returned error: %s", err)
        return
    }

    // Create a modify metadata transaction
    transaction, err := sdk.NewModifyMetadataMosaicTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour),
        // Id of mosaic where metadata should be added
        mosaicId,
        // Actual data which will be added/removed
        []*sdk.MetadataModification{
            {
                // add or remove metadata
                sdk.AddMetadata,
                // key which should be used to store data
                "my_key_name",
                // actual data which will be stored in associated key
                "my_data",
            },
        },
        networkType,
    )
    if err != nil {
        fmt.Printf("NewModifyMetadataMosaicTransaction returned error: %s", err)
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

    // wait for the transaction to be confirmed! (very important)
    // you can use websockets to wait explicitly for transaction
    // to be in certain state, instead of hard waiting
    time.Sleep(time.Second * 30)

}
```

#### Attach metadata to Namespace

This is done via a **NewModifyMetadataAddressTransaction()**.

- Following parameters are required:
  - **Namespace Id** - namespace identifier for metadata to add
  - **Metadata Modifications** - an array of Metadata Modification which consists of:
    - MetadataModificationType - type of modification. Supported types:
      - `sdk.AddMetadata`
      - `sdk.RemoveMetadata`
    - Metadata key
    - Metadata value

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
    networkType = sdk.MijinTest
    // Valid private key
    privateKey  = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
    // existing namespace name for which account is owner
    namespaceName = "myspacename"
)

func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Create an accounts from a private keys
    account, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    //Create namespace id from name
    namespaceId, err := sdk.NewNamespaceIdFromName(namespaceName)
    if err != nil {
        fmt.Printf("NewNamespaceIdFromName returned error: %s", err)
        return
    }

    // Create a modify metadata transaction
    transaction, err := client.NewModifyMetadataNamespaceTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour * 1),
        // Id of namespace where metadata should be added
        namespaceId,
        // Actual data which will be added/removed
        []*sdk.MetadataModification{
            {
                // add or remove metadata
                sdk.AddMetadata,
                // key which should be used to store data
                "my_key_name",
                // actual data which will be stored in associated key
                "my_data",
            },
        },
    )

    if err != nil {
        fmt.Printf("NewModifyMetadataNamespaceTransaction returned error: %s", err)
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

    // wait for the transaction to be confirmed! (very important)
    // you can use websockets to wait explicitly for transaction
    // to be in certain state, instead of hard waiting
    time.Sleep(time.Second * 30)

}
```
