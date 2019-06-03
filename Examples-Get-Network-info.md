
### Get the current network type of the chain

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

    // choose any here
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

    actualNetworkType, err := client.Network.GetNetworkType(context.Background())
    if err != nil {
        fmt.Printf("Network.GetNetworkType returned error: %s", err)
        return
    }
    fmt.Printf("%s\n", networkType)
}
```

