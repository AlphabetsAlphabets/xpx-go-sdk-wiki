
### Get the current network type of the chain

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
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

    actualNetworkType, err := client.Network.GetNetworkType(context.Background())
    if err != nil {
        fmt.Printf("Network.GetNetworkType returned error: %s", err)
        return
    }
    fmt.Printf("%s\n", actualNetworkType)
}
```

### Get the current config of the chain

```go
package main

import (
    "context"
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
)

const (
    // Catapult-api-rest server.
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

    config, err := client.Network.GetNetworkConfig(context.Background())
    if err != nil {
        fmt.Printf("Network.GetNetworkConfig returned error: %s", err)
        return
    }
    fmt.Printf("%s\n", config.String())
}
```

### Get config on height

- Following parameters required:
  - **Height** - blockchain height on which you want to get config

```go
package main

import (
    "context"
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
)

const (
    // Catapult-api-rest server.
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

    config, err := client.Network.GetNetworkConfigAtHeight(context.Background(), 0)
    if err != nil {
        fmt.Printf("Network.GetNetworkConfig returned error: %s", err)
        return
    }
    fmt.Printf("%s\n", config.String())
}
```

### Get blockchain config fields

```go
package main

import (
    "context"
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
)

const (
    // Catapult-api-rest server.
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

    config, err := client.Network.GetNetworkConfigAtHeight(context.Background(), 0)
    if err != nil {
        fmt.Printf("Network.GetNetworkConfig returned error: %s", err)
        return
    }

    for sectionName, fields := range config.BlockChainConfig.Sections {
        for fieldName, value := range fields.Fields {
            fmt.Printf("Section name - %s, field name - %s, field value - %s", sectionName, fieldName, value.String())
        }
    }
}
```

