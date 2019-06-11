
### Aggregate complete transaction

Aggregate Complete transaction is a method to execute multiple
transactions at once. To be valid, all transactions included in
aggregate complete should be signed by aggregate complete transaction
owner. If valid, it will be included in a block.

- Following parameters required:
  - Inner transactions - array of transactions signed by aggregate complete owner.
  Other aggregate transactions are not allowed as inner transactions.

```go
package main

import (
    "context"
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "time"
)

const (
    // Types of network.
    networkType = sdk.MijinTest
    // Valid private key
    privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
    // Addresses for transfer
    firstAddressToTransfer = "SCW2KXPU3MCFUASDJAWFPOYYXQHFX76ESSX4QXDN"
    secondAddressToTransfer = "SCTJXIHO62UZDNA3A3RHQUXP3EWXMCUGWSJAV2CM"
)

func main() {

    conf, err := sdk.NewConfig(baseUrl, networkType, time.Second * 10)
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Create an account from a private key
    account, err := sdk.NewAccountFromPrivateKey(privateKey , networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // Create a new transfer type transaction
    firstTransferTransaction, err := sdk.NewTransferTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        time.NewDeadline(time.Hour * 1),
        // The address of the recipient account.
        sdk.NewAddress(firstAddressToTransfer, networkType),
        // The array of mosaic to be sent.
        []*sdk.Mosaic{sdk.Xpx(10)},
        // The transaction message of 1024 characters.
        sdk.NewPlainMessage(""),
        networkType,
    )
    if err != nil {
        fmt.Printf("NewTransferTransaction returned error: %s", err)
        return
    }

    // Create a new transfer type transaction
    secondTransferTransaction, err := sdk.NewTransferTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        time.NewDeadline(time.Hour * 1),
        // The address of the recipient account.
        sdk.NewAddress(secondAddressToTransfer, networkType),
        // The array of mosaic to be sent.
        []*sdk.Mosaic{sdk.Xpx(10)},
        // The transaction message of 1024 characters.
        sdk.NewPlainMessage(""),
        networkType,
    )
    if err != nil {
        fmt.Printf("NewTransferTransaction returned error: %s", err)
        return
    }

    // Convert an aggregate transaction to an inner transaction including transaction signer.
    firstTransferTransaction.ToAggregate(account.PublicAccount)
    secondTransferTransaction.ToAggregate(account.PublicAccount)

    // Create an aggregate complete transaction
    aggregateTransaction, err := sdk.NewCompleteAggregateTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour * 1),
        // Inner transactions
        []sdk.Transaction{firstTransferTransaction, secondTransferTransaction},
        networkType
    )
    if err != nil {
        fmt.Printf("NewCompleteAggregateTransaction returned error: %s", err)
        return
    }

    // Sign transaction
    signedTransaction, err := account.Sign(aggregateTransaction)
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

