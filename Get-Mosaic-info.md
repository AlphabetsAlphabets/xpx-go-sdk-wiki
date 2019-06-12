
### Get mosaic information

- Following parameters required:
  - **MosaicID** - mosaic identifier to get info about

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

    conf, err := sdk.NewConfig(baseUrl, networkType, time.Second * 10)
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Get mosaic information.
    mosaic, err := client.Mosaic.GetMosaicInfo(context.Background(), sdk.XpxMosaicId)
    if err != nil {
        fmt.Printf("Mosaic.GetMosaicInfo returned error: %s", err)
        return
    }
    fmt.Printf("%s\n", mosaic.String())
}
```

### Get information for array of mosaics

- Following parameters required:
  - **[]MosaicID** - mosaic identifiers to get info about

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

    conf, err := sdk.NewConfig(baseUrl, networkType, time.Second * 10)
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Get mosaic information.
    mosaics, err := client.Mosaic.GetMosaicInfos(context.Background(), []*sdk.MosaicId{sdk.XpxMosaicId, sdk.XemMosaicId})
    if err != nil {
        fmt.Printf("Mosaic.GetMosaicInfos returned error: %s", err)
        return
    }
    for _, mosaic := range mosaics {
        fmt.Printf("%s\n", mosaic.String())
    }
}
```

