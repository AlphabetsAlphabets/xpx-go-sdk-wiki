
### Aggregate bonded transactions

An aggregate transaction is considered to be bonded if requires signatures from other participants.

- When sending an aggregate bonded transaction, an account must first
announce and get confirmed a Lock Funds Transaction for this aggregate
with at least 10 XPX. This mechanism is required to prevent network spamming.
- Once the related aggregate bonded transaction is confirmed, locked
funds become available again in the account that signed the initial lock funds transaction.

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
    firstPrivateKey  = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
    secondPrivateKey = "6EED33183360FC92CD8EBD61EB8859FB984EC164B94CF496398C91BC5B0769B3"
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
    firstAccount, err := client.NewAccountFromPrivateKey(firstPrivateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }
    secondAccount, err := client.NewAccountFromPrivateKey(secondPrivateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // Create a new transfer type transaction
    firstTransferTransaction, err := client.NewTransferTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour*1),
        // The address of the recipient account.
        secondAccount.Address,
        // The array of mosaic to be sent.
        []*sdk.Mosaic{sdk.Xpx(10)},
        // The transaction message of 1024 characters.
        sdk.NewPlainMessage("Let's exchange 10 xpx -> 20 xem"),
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
        firstAccount.Address,
        // The array of mosaic to be sent.
        []*sdk.Mosaic{sdk.Xem(20)},
        // The transaction message of 1024 characters.
        sdk.NewPlainMessage("Okay"),
    )
    if err != nil {
        fmt.Printf("NewTransferTransaction returned error: %s", err)
        return
    }

    // Convert an aggregate transaction to an inner transaction including transaction signer.
    firstTransferTransaction.ToAggregate(firstAccount.PublicAccount)
    secondTransferTransaction.ToAggregate(secondAccount.PublicAccount)

    aggregateBoundedTransaction, err := client.NewBondedAggregateTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour*1),
        // Inner transactions
        []sdk.Transaction{firstTransferTransaction, secondTransferTransaction},
    )
    if err != nil {
        fmt.Printf("NewBondedAggregateTransaction returned error: %s", err)
        return
    }

    // Sign transaction
    signedAggregateBoundedTransaction, err := firstAccount.Sign(aggregateBoundedTransaction)
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    // Create lock funds transaction for aggregate bounded
    lockFundsTransaction, err := client.NewLockFundsTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour*1),
        // Funds to lock
        sdk.XpxRelative(10),
        // Duration of lock transaction in blocks
        sdk.Duration(1000),
        // Aggregate bounded transaction for lock
        signedAggregateBoundedTransaction,
    )
    if err != nil {
        fmt.Printf("NewLockFundsTransaction returned error: %s", err)
        return
    }

    // Sign transaction
    signedLockFundsTransaction, err := firstAccount.Sign(lockFundsTransaction)
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    // Announce transaction
    _, err = client.Transaction.Announce(context.Background(), signedLockFundsTransaction)
    if err != nil {
        fmt.Printf("Transaction.Announce returned error: %s", err)
        return
    }

    // Wait for lock funds transaction to be harvested
    time.Sleep(30 * time.Second)

    // Announce aggregate bounded transaction
    _, _ = client.Transaction.AnnounceAggregateBonded(context.Background(), signedAggregateBoundedTransaction)
    if err != nil {
        fmt.Printf("Transaction.AnnounceAggregateBonded returned error: %s", err)
        return
    }

    // Wait for aggregate bounded transaction to be harvested
    time.Sleep(30 * time.Second)

    // Create cosignature transaction from first account
    firstAccountCosignatureTransaction := sdk.NewCosignatureTransactionFromHash(signedAggregateBoundedTransaction.Hash)
    signedFirstAccountCosignatureTransaction, err := firstAccount.SignCosignatureTransaction(firstAccountCosignatureTransaction)
    if err != nil {
        fmt.Printf("SignCosignatureTransaction returned error: %s", err)
        return
    }

    // Announce transaction
    _, err = client.Transaction.AnnounceAggregateBondedCosignature(context.Background(), signedFirstAccountCosignatureTransaction)
    if err != nil {
        fmt.Printf("AnnounceAggregateBoundedCosignature returned error: %s", err)
        return
    }

    // Create cosignature transaction from second account
    secondAccountCosignatureTransaction := sdk.NewCosignatureTransactionFromHash(signedAggregateBoundedTransaction.Hash)
    signedSecondAccountCosignatureTransaction, err := secondAccount.SignCosignatureTransaction(secondAccountCosignatureTransaction)
    if err != nil {
        fmt.Printf("SignCosignatureTransaction returned error: %s", err)
        return
    }

    // Announce transaction
    _, err = client.Transaction.AnnounceAggregateBondedCosignature(context.Background(), signedSecondAccountCosignatureTransaction)
    if err != nil {
        fmt.Printf("AnnounceAggregateBoundedCosignature returned error: %s", err)
        return
    }
    // wait for the transaction to be confirmed! (very important)
    // you can use websockets to wait explicitly for transaction
    // to be in certain state, instead of hard waiting
    time.Sleep(30 * time.Second)

}
```

