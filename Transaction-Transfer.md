### Transfer Transaction

Transfer transaction is used to send assets between two accounts. It can hold a 1024 bytes message.
To create Transfer Transaction use **NewTransferTransaction()**.

- Following parameters required:
  - **Recipient** - The address of the recipient account.
  - **Mosaics** - The array of mosaics to be sent.
  - **Message** - The transaction message of 1024 characters.

```go
package main

import (
    "context"
    "fmt"
    "sync"
    "time"

    "github.com/proximax-storage/go-xpx-chain-sdk/sdk"
    "github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
)

const (
    // Sirius api rest server
    baseUrl = "http://localhost:3000"
    // Valid private key
    privateKey = "819F72066B17FFD71B8B4142C5AEAE4B997B0882ABDF2C263B02869382BD93A0"
)

func main() {

    // Testnet default config
    config, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, config)

    // Create websocket client
    wsClient, err := websocket.NewClient(context.Background(), config)
    if err != nil {
        panic(err)
    }

    defer wsClient.Close()
    go wsClient.Listen()

    // Create account from private key
    account, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        panic(err)
    }

    wg := &sync.WaitGroup{}
    // add handler
    wg.Add(1)
    err = wsClient.AddConfirmedAddedHandlers(account.Address, func(info sdk.Transaction) bool {
        fmt.Printf("ConfirmedAdded Tx Hash: %v \n", info.GetAbstractTransaction().TransactionHash)
        wg.Done()
        return true
    })
    if err != nil {
        panic(err)
    }

    wg.Add(1)
    err = wsClient.AddUnconfirmedAddedHandlers(account.Address, func(info sdk.Transaction) bool {
        fmt.Printf("UnconfirmedAdded Tx Hash: %v \n", info.GetAbstractTransaction().TransactionHash)
        wg.Done()
        return true
    })
    if err != nil {
        panic(err)
    }

    // Create a new transfer type transaction
    transaction, err := client.NewTransferTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour*1),
        // The address of the recipient account.
        sdk.NewAddress("SBILTA367K2LX2FEXG5TFWAS7GEFYAGY7QLFBYKC", client.NetworkType()),
        // The array of mosaic to be sent.
        []*sdk.Mosaic{sdk.Xpx(1)},
        // The transaction message of 1024 characters.
        sdk.NewPlainMessage("Here you go"),
    )
    if err != nil {
        fmt.Printf("NewTransferTransaction returned error: %s", err)
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

    wg.Wait()
}
```

### Transfer Transaction with a Namespace

Transfer transaction is used to send assets between two accounts. It can hold a 1024 bytes message.
To create Transfer Transaction use **NewTransferTransactionWithNamespace()**.

- Following parameters required:
  - **Recipient** - The namespace id of the recipient account.
  - **Mosaics** - The array of mosaics to be sent.
  - **Message** - The transaction message of 1024 characters.

```go
package main


import (
    "context"
    "fmt"
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

    // Create namespace from it's name
    namespaceId, err := sdk.NewNamespaceIdFromName("mynamespace")
    if err != nil {
        fmt.Printf("NewNamespaceIdFromName returned error: %s", err)
        return

    // Create a new transfer type transaction
    transaction, err := client.NewTransferTransactionWithNamespace(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour * 1),
        // The address of the recipient account.
        namespaceId,
        // The array of mosaic to be sent.
        []*sdk.Mosaic{sdk.Xpx(10000000)},
        // The transaction message of 1024 characters.
        sdk.NewPlainMessage("Here you go"),
    )
    if err != nil {
        fmt.Printf("NewTransferTransactionWithNamespace returned error: %s", err)
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
