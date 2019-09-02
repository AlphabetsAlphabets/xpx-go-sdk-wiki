
### Aggregate complete transaction

Aggregate Complete transaction is a method to execute multiple
transactions at once. To be valid, all transactions included in
aggregate complete should be signed. If valid, it will be
included in a block.

- Following parameters required:
  - **Inner transactions** - array of transactions signed by aggregate complete owner.
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
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Valid private key
    privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
    // Addresses for transfer
    firstAddressToTransfer  = "SCW2KXPU3MCFUASDJAWFPOYYXQHFX76ESSX4QXDN"
    secondAddressToTransfer = "SCTJXIHO62UZDNA3A3RHQUXP3EWXMCUGWSJAV2CM"
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

    // Create a new transfer type transaction
    firstTransferTransaction, err := client.NewTransferTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour*1),
        // The address of the recipient account.
        sdk.NewAddress(firstAddressToTransfer, client.NetworkType()),
        // The array of mosaic to be sent.
        []*sdk.Mosaic{sdk.Xpx(10)},
        // The transaction message of 1024 characters.
        sdk.NewPlainMessage(""),
    )
    if err != nil {
        fmt.Printf("NewTransferTransaction returned error: %s", err)
        return
    }

    // Create a new transfer type transaction
    secondTransferTransaction, err := client.NewTransferTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour*1),
        // The address of the recipient account.
        sdk.NewAddress(secondAddressToTransfer, client.NetworkType()),
        // The array of mosaic to be sent.
        []*sdk.Mosaic{sdk.Xpx(10)},
        // The transaction message of 1024 characters.
        sdk.NewPlainMessage(""),
    )
    if err != nil {
        fmt.Printf("NewTransferTransaction returned error: %s", err)
        return
    }

    // Convert an aggregate transaction to an inner transaction including transaction signer.
    firstTransferTransaction.ToAggregate(account.PublicAccount)
    secondTransferTransaction.ToAggregate(account.PublicAccount)

    // Create an aggregate complete transaction
    aggregateTransaction, err := client.NewCompleteAggregateTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour*1),
        // Inner transactions
        []sdk.Transaction{firstTransferTransaction, secondTransferTransaction},
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

