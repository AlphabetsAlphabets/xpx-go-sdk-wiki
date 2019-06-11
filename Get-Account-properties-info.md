### Get Account Properties

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
    // Types of network.
    networkType = sdk.MijinTest
    // A valid private key
    privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
)

func main() {

    conf, err := sdk.NewConfig(baseUrl, networkType, time.Second * 10)
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Create an account from a private key
    account, err := sdk.NewAccountFromPublicKey(publicKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
        return
    }

    info, err := client.Account.GetAccountProperties(context.Background(), account.Address)
    if err != nil {
        fmt.Printf("Account.GetAccountProperties returned error: %s", err)
        return
    }
    fmt.Printf("Address %s\n", info.Address)
    fmt.Printf("Allowed Addresses %s\n", info.AllowedAddresses)
    fmt.Printf("Allowed MosaicId %s\n", info.AllowedMosaicId)
    fmt.Printf("Allowed EntityTypes %s\n", info.AllowedEntityTypes)
    fmt.Printf("Blocked Addresses %s\n", info.AllowedAddresses)
    fmt.Printf("Blocked MosaicId %s\n", info.AllowedMosaicId)
    fmt.Printf("Blocked EntityTypes %s\n", info.AllowedEntityTypes)
}
```

