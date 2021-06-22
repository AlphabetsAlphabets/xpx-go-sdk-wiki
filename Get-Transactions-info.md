### Get Transaction Information by Transaction ID or Hash

- Following parameters required:
	- **TransactionGroup** - group of transaction (Confirmed, Partial, Unconfirmed)
	- **TransactionID** - ID of transaction
    	- Example transaction id: "5BB4D6E2415CD84B7971BBAA"

```go
package main

import (
	"context"
	"fmt"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
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

	transaction, err := client.Transaction.GetAnyTransaction(context.Background(), "5BB4D6E2415CD84B7971BBAA")
	if err != nil {
		fmt.Printf("Transaction.GetTransactionStatuses returned error: %s", err)
		return
	}

	fmt.Println(transaction)
}
```

### Get Transaction Information by Transaction ID or Hash and Group

- Following parameters required:
	- **TransactionGroup** - group of transaction (Confirmed, Partial, Unconfirmed)
	- **TransactionID** - ID of transaction
    	- Example transaction id: "5BB4D6E2415CD84B7971BBAA"

```go
package main

import (
	"context"
	"fmt"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
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

	transaction, err := client.Transaction.GetTransaction(context.Background(), sdk.Confirmed, "5BB4D6E2415CD84B7971BBAA")
	if err != nil {
		fmt.Printf("Transaction.GetTransaction returned error: %s", err)
		return
	}

	fmt.Println(transaction.String())
}
```

### Get Transactions Informations by Group

- Following parameters required:
	- **TransactionGroup** - group of transaction (Confirmed, Partial, Unconfirmed)
	- **TransactionsPageOptions** - options for obtained page
    	- Example transaction id: "5BB4D6E2415CD84B7971BBAA"
    	- Example transaction hash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"

```go
package main

import (
	"context"
	"fmt"
	
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
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
	transactionPage, err := client.Transaction.GetTransactionsByGroup(context.Background(), sdk.Confirmed, nil)
	if err != nil {
		fmt.Printf("Transaction.GetTransactions returned error: %s", err)
		return
	}
	
	for _, transaction := range transactionPage.Transactions {
		fmt.Printf("%s\n\n", transaction.String())
	}
}
```

### Get Transactions Informations by IDs and Groups

- Following parameters required:
	- **TransactionGroup** - group of transaction (Confirmed, Partial, Unconfirmed)
	- **[]TransactionsIDs** - IDs or hashes of transactions
	- **TransactionsPageOptions** - options for obtained page
    	- Example transaction id: "5BB4D6E2415CD84B7971BBAA"
    	- Example transaction hash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"

```go
package main

import (
	"context"
	"fmt"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
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

	transactions, err := client.Transaction.GetTransactionsByIds(
		context.Background(),
		sdk.Confirmed,
		[]string{"C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6", "463FDEC912FC4BA3D84FEC31E8293FAE6D142FC271A71E464FDA563F056A6151"},
		nil,
	)
	
	if err != nil {
		fmt.Printf("Transaction.GetTransactionStatuses returned error: %s", err)
		return
	}

	for _, trx := range transactions {
		fmt.Println(trx.String())
	}
}
```

### Get Transaction Status by Transaction Hash or ID

- Following parameters required:
	- **Transaction ID or hash** - identifier of transaction to get its status
    	- Example transaction id: "5BB4D6E2415CD84B7971BBAA"
    	- Example transaction hash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"

```go
package main

import (
	"context"
	"fmt"
	
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
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
	fmt.Println(transactionStatus.String())
}
```

### Get Transactions Statuses by Transactions Hashes or IDs

- Following parameters required:
  - **[]TransactionsIDs** - IDs or hashes of transactions:
    - Example transaction id: "5BB4D6E2415CD84B7971BBAA"
    - Example transaction hash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"

```go
package main

import (
	"context"
	"fmt"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
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

	transactionStatuses, err := client.Transaction.GetTransactionsStatuses(context.Background(), []string{"C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6", "463FDEC912FC4BA3D84FEC31E8293FAE6D142FC271A71E464FDA563F056A6151"})
	if err != nil {
		fmt.Printf("Transaction.GetTransactionStatuses returned error: %s", err)
		return
	}

	for _, transactionStatus := range transactionStatuses {
		fmt.Println(transactionStatus.String())
	}
}
```

### Get Transaction Effective Fee by Transaction Hashes or IDs

- Following parameters required:
  - **TransactionID** - ID of transaction
    - Example transaction id: "5BB4D6E2415CD84B7971BBAA"
    - Example transaction hash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"

```go
package main

import (
	"context"
	"fmt"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
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

	trxEffectiveFee, err := client.Transaction.GetTransactionEffectiveFee(context.Background(), "5BB4D6E2415CD84B7971BBAA")
	if err != nil {
		fmt.Printf("Transaction.GetTransactionStatuses returned error: %s", err)
		return
	}

	fmt.Println(trxEffectiveFee)
}
```
