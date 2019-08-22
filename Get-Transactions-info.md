
### Get transaction information by transaction id or hash

- Following parameters required:
  - **Transaction ID or hash** - identifier of transaction to get info about
    - Example transaction id: "5BB4D6E2415CD84B7971BBAA"
    - Example transaction hash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"

```go
package main

import (
    "fmt"
    "time"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
)

// Simple Transaction API request
func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    transaction, err := client.Transaction.GetTransaction(context.Background(), "5BB4D6E2415CD84B7971BBAA")
    if err != nil {
        fmt.Printf("Transaction.GetTransaction returned error: %s", err)
        return
    }
    fmt.Printf("%s\n\n", transaction.String())
}
```

### Get transaction informations for a given array of transaction ids or hashes

- Following parameters required:
  - **[]Transaction ID or hash** - identifiers of transaction to get info about
    - Example transaction id: "5BB4D6E2415CD84B7971BBAA"
    - Example transaction hash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"

```go
package main

import (
    "fmt"
    "time"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
)

// Simple Transaction API request
func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Get transaction informations for transactionIds or transactionHashes
    transactions, err := client.Transaction.GetTransactions(context.Background(), []string{"C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6", "463FDEC912FC4BA3D84FEC31E8293FAE6D142FC271A71E464FDA563F056A6151"})
    if err != nil {
        fmt.Printf("Transaction.GetTransactions returned error: %s", err)
        return
    }
    for _, transaction := range transactions {
        fmt.Printf("%s\n\n", transaction.String())
    }
}
```

### Get transaction status for transaction hash or id

- Following parameters required:
  - **Transaction ID or hash** - identifier of transaction to get its status
    - Example transaction id: "5BB4D6E2415CD84B7971BBAA"
    - Example transaction hash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"

```go
package main

import (
    "fmt"
    "time"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
)

// Simple Transaction API request
func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    transactionStatus, err := client.Transaction.GetTransactionStatus(context.Background(), "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6")
    if err != nil {
        fmt.Printf("Transaction.GetTransactionStatus returned error: %s", err)
        return
    }
    fmt.Printf("%s\n\n", transactionStatus.String())
}
```

### Get transaction statuses for a given array of transaction hashes or ids

- Following parameters required:
  - **[]Transaction ID or hash** - identifier of transaction to get their statuses
    - Example transaction id: "5BB4D6E2415CD84B7971BBAA"
    - Example transaction hash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"

```go
package main

import (
    "fmt"
    "time"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
)

// Simple Transaction API request
func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    transactionStatuses, err := client.Transaction.GetTransactionStatuses(context.Background(), []string{"C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6", "463FDEC912FC4BA3D84FEC31E8293FAE6D142FC271A71E464FDA563F056A6151"})
    if err != nil {
        fmt.Printf("Transaction.GetTransactionStatuses returned error: %s", err)
        return
    }
    for _, transactionStatus := range transactionStatuses {
        fmt.Printf("%s\n\n", transactionStatus.String())
    }
}
```

