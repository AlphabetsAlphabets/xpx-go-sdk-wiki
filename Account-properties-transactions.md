#### Account properties transactions

#### Address

An account can decide to receive transactions only from an allowed list of addresses. Similarly, an account can specify a list of addresses that donÕt want to receive transactions from.

This is done via a **NewAccountPropertiesAddressTransaction()**.

```go
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

    // A valid private key
    privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
)

func main() {

    // Testnet config default
    conf, err := sdk.NewConfig(baseUrl,networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Create an account from a private key
    account, err := sdk.NewAccountFromPrivateKey(privateKey, networkType)
    if err != nil {
        panic(err)
    }
    // Create an account for blocking
    accountToBlock, err := sdk.NewAccount(networkType)
    if err != nil {
        panic(err)
    }

    // Create account properties address transaction
    trx, err := sdk.NewAccountPropertiesAddressTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour),
        // Block transactions from addresses
        sdk.BlockAddress,
        // Account properties to update
        []*sdk.AccountPropertiesAddressModification{
            {
                sdk.AddProperty,
                accountToBlock.Address,
            },
        },
        // Type of blockchain network
        networkType,
    )
    if err != nil {
        panic(err)
    }

    // Sign modification by address owner
    signedTrx, err := account.Sign(trx)
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
```

#### Mosaic

An account can configure a filter to permit incoming transactions only if all the mosaics attached are allowed. On the other hand, the account can refuse to accept transactions containing a mosaic listed as blocked.

This is done via a **NewAccountPropertiesMosaicTransaction()**.

```go
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

    // A valid private key
    privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
    // Valid mosaic nonce
    nonce = ...
)

func main() {

    // Testnet config default
    conf, err := sdk.NewConfig(baseUrl,networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Create an account from a private key
    account, err := sdk.NewAccountFromPrivateKey(privateKey, networkType)
    if err != nil {
        panic(err)
    }

    // Create mosaic id of just published mosaic
    mosaicId, err := sdk.NewMosaicIdFromNonceAndOwner(nonce, account.PublicAccount.PublicKey)
    if err != nil {
        panic(err)
    }

    // Create account properties address transaction
    trx, err := sdk.NewAccountPropertiesMosaicTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour),
        // Block transactions with passed mosaic
        sdk.BlockMosaic,
        // Account properties to update
        []*sdk.AccountPropertiesMosaicModification{
            {
                sdk.AddProperty,
                mosaicId,
            },
        },
        // Type of blockchain network
        networkType,
    )
    if err != nil {
        panic(err)
    }

    // Sign modification by address owner
    signedTrx, err := account.Sign(trx)
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
```

#### Entity

An account can allow/block announcing outgoing transactions with a determined type. By doing so, it increases its security, preventing the announcement by mistake of undesired transactions.

This is done via a **NewAccountPropertiesEntityTypeTransaction()**.

```go
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

    // A valid private key
    privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
)

func main() {

    // Testnet config default
    conf, err := sdk.NewConfig(baseUrl,networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Create an account from a private key
    account, err := sdk.NewAccountFromPrivateKey(privateKey, networkType)
    if err != nil {
        panic(err)
    }

    // Create account properties address transaction
    trx, err := sdk.NewAccountPropertiesMosaicTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour),
        // Block transactions with entity type
        sdk.BlockTransaction,
        // Account properties to update
        []*sdk.AccountPropertiesEntityTypeModification{
            {
                sdk.AddProperty,
                sdk.LinkAccount,
            },
        },
        // Type of blockchain network
        networkType,
    )
    if err != nil {
        panic(err)
    }

    // Sign modification by address owner
    signedTrx, err := account.Sign(trx)
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
```
