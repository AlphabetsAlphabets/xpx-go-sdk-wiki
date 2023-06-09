
>**WebSockets** make possible receiving notifications when a transaction or event occurs in the blockchain.
The notification arrives in real time without the need to poll the API to await a response.


### Subscribe block

The *block channel* notifies for every new block. The message contains the block information.

```go
package main

import (
    "context"
    "fmt"
    "sync"

    "github.com/proximax-storage/go-xpx-chain-sdk/sdk"
    "github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
)

func main() {

	// Testnet default config
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		panic(err)
	}

    // Create websocket client
    wsClient, err := websocket.NewClient(context.Background(), conf)
    if err != nil {
        panic(err)
    }

    defer wsClient.Close()
    go wsClient.Listen()

    wg := &sync.WaitGroup{}
    // add handler

    wg.Add(1)
    err = wsClient.AddBlockHandlers(func(info *sdk.BlockInfo) bool {
        fmt.Printf("Block received with height: %v \n", info.Height)
        wg.Done()
        return true
    })
    if err != nil {
        panic(err)
    }

    wg.Wait()
}
```


### Subscribe confirmed added

The *confirmed added channel* notifies when a transaction related to an address is included in a block. The message contains the transaction.

```go
package main

import (
    "context"
    "fmt"
    "sync"

    "github.com/proximax-storage/go-xpx-chain-sdk/sdk"
    "github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
)

const (
    // Sirius api rest server
    baseUrl = "http://localhost:3000"
    // Valid private key
    privateKey = "24CEC4F2DD1A28EBB2C0CF6D4D181BA2C0F1E215C42B9059EEFB65B1FAEE1B99"
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

    wg.Wait()
}
```


### Subscribe unconfirmed added

The *unconfirmed added channel* notifies when a transaction related to an address is in unconfirmed state and waiting to be included in a block. The message contains the transaction.

```go
package main

import (
    "context"
    "fmt"
    "sync"

    "github.com/proximax-storage/go-xpx-chain-sdk/sdk"
    "github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
)

const (
    // Sirius api rest server
    baseUrl = "http://localhost:3000"
    // Valid private key
    privateKey = "24CEC4F2DD1A28EBB2C0CF6D4D181BA2C0F1E215C42B9059EEFB65B1FAEE1B99"
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
    err = wsClient.AddUnconfirmedAddedHandlers(account.Address, func(info sdk.Transaction) bool {
        fmt.Printf("UnconfirmedAdded Tx Hash: %v \n", info.GetAbstractTransaction().TransactionHash)
        wg.Done()
        return true
    })
    if err != nil {
        panic(err)
    }

    wg.Wait()
}
```


### Subscribe partial added

The *partial added channel* notifies when an aggregate bonded transaction related to an address is in partial state and waiting to have all required cosigners. The message contains a transaction.

```go
package main

import (
    "context"
    "fmt"
    "sync"

    "github.com/proximax-storage/go-xpx-chain-sdk/sdk"
    "github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
)

const (
    // Sirius api rest server
    baseUrl = "http://localhost:3000"
    // Valid private key
    privateKey = "24CEC4F2DD1A28EBB2C0CF6D4D181BA2C0F1E215C42B9059EEFB65B1FAEE1B99"
)

func main() {
    // Testnet config default
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
    err = wsClient.AddPartialAddedHandlers(account.Address, func(info *sdk.AggregateTransaction) bool {
        fmt.Printf("PartialAdded Tx Hash: %v \n", info.GetAbstractTransaction().TransactionHash)
        wg.Done()
        return true
    })
    if err != nil {
        panic(err)
    }

    wg.Wait()
}
```

### Subscribe partial removed

The *partial removed channel* notifies when a transaction related to an address was in partial state but not anymore. The message contains the transaction hash.

```go
package main

import (
    "context"
    "fmt"
    "sync"

    "github.com/proximax-storage/go-xpx-chain-sdk/sdk"
    "github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
)

const (
    // Sirius api rest server
    baseUrl = "http://localhost:3000"
    // Valid private key
    privateKey = "24CEC4F2DD1A28EBB2C0CF6D4D181BA2C0F1E215C42B9059EEFB65B1FAEE1B99"
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
    err = wsClient.AddPartialRemovedHandlers(account.Address, func(info *sdk.PartialRemovedInfo) bool {
        fmt.Printf("PartialRemoved Tx Hash: %v \n", info.Meta.TransactionHash)
        wg.Done()
        return true
    })
    if err != nil {
        panic(err)
    }

    wg.Wait()
}
```

### Subscribe cosignature

The *cosignature channel notifies* when a cosignature signed transaction related to an address is added to an aggregate bonded transaction with partial state. The message contains the cosignature signed transaction.

```go
package main

import (
    "context"
    "fmt"
    "sync"

    "github.com/proximax-storage/go-xpx-chain-sdk/sdk"
    "github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
)

const (
    // Sirius api rest server
    baseUrl = "http://localhost:3000"
    // Valid private key
    privateKey = "24CEC4F2DD1A28EBB2C0CF6D4D181BA2C0F1E215C42B9059EEFB65B1FAEE1B99"
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
    err = wsClient.AddCosignatureHandlers(account.Address, func(info *sdk.SignerInfo) bool {
        fmt.Printf("Cosignature PublicKey: %v \n", info.Signer)
        fmt.Printf("Cosignature Tx Hash: %v \n", info.ParentHash)
        wg.Done()
        return true
    })
    if err != nil {
        panic(err)
    }

    wg.Wait()
}
```

### Subscribe status

The *status channel* notifies when a transaction related to an address rises an error. The message contains the error message and the transaction hash.

```go
package main

import (
    "context"
    "fmt"
    "sync"

    "github.com/proximax-storage/go-xpx-chain-sdk/sdk"
    "github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
)

const (
    // Sirius api rest server
    baseUrl = "http://localhost:3000"
    // Valid private key
    privateKey = "24CEC4F2DD1A28EBB2C0CF6D4D181BA2C0F1E215C42B9059EEFB65B1FAEE1B99"
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
    err = wsClient.AddStatusHandlers(account.Address, func(info *sdk.StatusInfo) bool {
        fmt.Printf("Status: %v \n", info.Status)
        fmt.Printf("Hash: %v \n", info.Hash)
        wg.Done()
        return true
    })
    if err != nil {
        panic(err)
    }

    wg.Wait()
}
```
