
### Contract modify transaction

Contract transactions are used by ProximaX decentralized storage
to store information about stakeholders of stored files in form
of contracts in blockchain. Building contract including creating
multisig account and aggregate bounded transaction.
This is done via a **NewModifyContractTransaction()**.

- Following parameters are required:
  - **Duration** - duration of contract in blocks
  - **File hash** - hash of file to be stored
  - **Customer** - public account of customer which want to store file
  - **Replicators** - an array of replicator public accounts
  - **Verifiers** - an array of verifiers public accounts

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
    // A valid private key
    customerPrivateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
    // contract multisig account private key
    contractPrivateKey = "6EED33183360FC92CD8EBD61EB8859FB984EC164B94CF496398C91BC5B0769B3"
    // replicator account private keys
    replicator1PrivateKey = "8DC29CF06338133DEE6B461CE489BF9588BE16DBE3B9670B5CB19C893694FC49"
    replicator2PrivateKey = "94CF496398C91BC5B0769B3EBD61EB8859FB984EC164B6EED33183360FC92CD8"
    // verificator account private keys
    verificator1PrivateKey = "B6EED33183360FC92CD8EBD61EB8859FB984EC16494CF496398C91BC5B0769B3"
    verificator2PrivateKey = "3B9670B5CB19C893694FC49461CE489BF9588BE16DBE8DC29CF06338133DEE6B"
    // valid file hash
    fileHashString = "cf893ffcc47c33e7f68ab1db56365c156b0736824a0c1e273f9e00b8df8f01eb"
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
    customer, err := client.NewAccountFromPrivateKey(customerPrivateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }
    contract, err := client.NewAccountFromPrivateKey(contractPrivateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }
    replicator1, err := client.NewAccountFromPrivateKey(replicator1PrivateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }
    replicator2, err := client.NewAccountFromPrivateKey(replicator2PrivateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }
    verificator1, err := client.NewAccountFromPrivateKey(verificator1PrivateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }
    verificator2, err := client.NewAccountFromPrivateKey(verificator2PrivateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    fileHash, err := sdk.StringToHash(fileHashString)
    if err != nil {
        fmt.Printf("StringToHash returned error: %s", err)
        return
    }

    // Create a modify contract transaction
    transaction, err := client.NewModifyContractTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour*1),
        // Duration of contract in blocks
        sdk.Duration(1000),
        // Hash of file which is being uploaded
        fileHash,
        // adding customer to the contract
        []*sdk.MultisigCosignatoryModification{{sdk.Add, customer.PublicAccount}},
        // adding replicators to the contract
        []*sdk.MultisigCosignatoryModification{{sdk.Add, replicator1.PublicAccount}, {sdk.Add, replicator2.PublicAccount}},
        // adding verificators to the contract
        []*sdk.MultisigCosignatoryModification{{sdk.Add, verificator1.PublicAccount}, {sdk.Add, verificator2.PublicAccount}},
    )

    if err != nil {
        fmt.Printf("NewModifyContractTransaction returned error: %s", err)
        return
    }

    // Sign transaction
    signedTransaction, err := contract.Sign(transaction)
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

