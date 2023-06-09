
### Get the current height of the chain

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

// Simple Account API request
func main() {

	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Get BlockInfo by height
	height, err := client.Blockchain.GetBlockchainHeight(context.Background())
	if err != nil {
		fmt.Printf("Blockchain.GetBlockhainHeight returned error: %s", err)
		return
	}
	
	fmt.Println(height)
}
```

### Get BlockInfo for a given block height

- Following parameters required:
  - **Height** - height of block to get info from

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

// Simple Account API request
func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// height of block in blockchain
	height := sdk.Height(1)

	// Get BlockInfo by height
	block, err := client.Blockchain.GetBlockByHeight(context.Background(), height)
	if err != nil {
		fmt.Printf("Blockchain.GetBlockByHeight returned error: %s", err)
		return
	}
	fmt.Printf(block.String())
}
```

### Get an array of BlockInfo for a given block height and limit

- Following parameters required:
  - **Height** - height of block to get info from
  - **Limit** - how many block infos to return

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

// Simple Account API request
func main() {

	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// height of block in blockchain
	height := sdk.Height(1)
	// how many BlockInfo's to return
	limit := sdk.Amount(100)

	// Get BlockInfo's by height with limit
	blocks, err := client.Blockchain.GetBlocksByHeightWithLimit(context.Background(), height, limit)
	if err != nil {
		fmt.Printf("Blockchain.GetBlocksByHeightWithLimit returned error: %s", err)
		return
	}

	for _, block := range blocks {
		fmt.Printf("%s\n", block.String())
	}
}
```

### Get transactions from a block

- Following parameters required:
  - **Height** - height of block to get transactions from

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

// Simple Account API request
func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// height of block in blockchain
	height := sdk.Height(1)

	// Get TransactionInfo's by block height
	transactions, err := client.Blockchain.GetBlockTransactions(context.Background(), height)
	if err != nil {
		fmt.Printf("Blockchain.GetBlockTransactions returned error: %s", err)
		return
	}
	
	for _, transaction := range transactions {
		fmt.Printf("%s\n", transaction.String())
	}
}
```

### Get the current score of the chain

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

// Simple Account API request
func main() {

	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Get score of blockchain
	score, err := client.Blockchain.GetBlockchainScore(context.Background())
	if err != nil {
		fmt.Printf("Blockchain.GetBlockchainScore returned error: %s", err)
		return
	}
	fmt.Printf("%s\n", score)
}
```

### Get the storage information

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

// Simple Account API request
func main() {

	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Get storage of blockchain
	storage, err := client.Blockchain.GetBlockchainStorage(context.Background())
	if err != nil {
		fmt.Printf("Blockchain.GetBlockchainStorage returned error: %s", err)
		return
	}
	fmt.Printf("%s\n", storage)
}
```

