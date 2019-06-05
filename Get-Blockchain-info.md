
### Get the current height of the chain

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "golang.org/x/net/context"
    "math/big"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"

    // Types of network.
    networkType = sdk.MijinTest
)

// Simple Account API request
func main() {

    conf, err := sdk.NewConfig(baseUrl,networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Get BlockInfo by height
    height, err := client.Blockchain.GetBlockhainHeight(context.Background())
    if err != nil {
        fmt.Printf("Blockchain.GetBlockhainHeight returned error: %s", err)
        return
    }
    fmt.Printf("%s\n", height)
}
```

### Get BlockInfo for a given block height

- Param height - Block height

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "golang.org/x/net/context"
    "math/big"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"

    // Types of network.
    networkType = sdk.MijinTest
)

// Simple Account API request
func main() {

    conf, err := sdk.NewConfig(baseUrl,networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // height of block in blockchain
    height := big.NewInt(1)

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

- Param height - Block height
- Param limit - Number of following blocks to be returned.

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "golang.org/x/net/context"
    "math/big"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"

    // Types of network.
    networkType = sdk.MijinTest
)

// Simple Account API request
func main() {

    conf, err := sdk.NewConfig(baseUrl,networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // height of block in blockchain
    height := big.NewInt(1)
    // how many BlockInfo's to return
    limit :=  big.NewInt(100)

    // Get BlockInfo's by height with limit
    blocks, err := client.Blockchain.GetBlocksByHeightWithLimit(context.Background(), height, limit)
    if err != nil {
        fmt.Printf("Blockchain.GetBlocksByHeightWithLimit returned error: %s", err)
        return
    }
    for _, blocks := range block {
        fmt.Printf("%s\n", block.String())
    }
}
```

### Get transactions from a block

- Param height - Block height

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "golang.org/x/net/context"
    "math/big"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"

    // Types of network.
    networkType = sdk.MijinTest
)

// Simple Account API request
func main() {

    conf, err := sdk.NewConfig(baseUrl,networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // height of block in blockchain
    height := big.NewInt(1)

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
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "golang.org/x/net/context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"

    // Types of network.
    networkType = sdk.MijinTest
)

// Simple Account API request
func main() {

    conf, err := sdk.NewConfig(baseUrl,networkType)
    if err != nil {
        panic(err)
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
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "golang.org/x/net/context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"

    // Types of network.
    networkType = sdk.MijinTest
)

// Simple Account API request
func main() {

    conf, err := sdk.NewConfig(baseUrl,networkType)
    if err != nil {
        panic(err)
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
