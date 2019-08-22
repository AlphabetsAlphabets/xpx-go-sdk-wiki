
### Get the current network type of the chain

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

