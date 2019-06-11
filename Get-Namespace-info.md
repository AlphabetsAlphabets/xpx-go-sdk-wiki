
### Get namespace information

- Following parameters required:
  - **NamespaceID** - namespace identifier to get info about

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

    // Generate Id from namespaceName
    namespaceId, _ := sdk.NewNamespaceIdFromName("proximax")

    namespace, err := client.Namespace.GetNamespaceInfo(context.Background(), namespaceId)
    if err != nil {
        fmt.Printf("Namespace.GetNamespaceInfo returned error: %s", err)
        return
    }
    fmt.Printf("%s\n", namespace.String())
}
```

### Get namespaces an account owns

- Following parameters required:
  - **Address** - address of account to get namespaces from
  - **pageSize** - pagination
  - **nsId** - TODO

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
    // Types of network.
    networkType = sdk.MijinTest
    // valid owner address
    rawAddress = "SAUUKSFBYHI57KTEXQNJWHOKXITF7T4BXON3GVTJ"
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

    // Generate Address struct
    address, err := sdk.NewAddressFromBase32(rawAddress)
    if err != nil {
        fmt.Printf("NewAddressFromBase32 returned error: %s", err)
        return
    }

    namespaces, err := client.Namespace.GetNamespaceInfosFromAccount(context.Background(), address, nil, 0)
    if err != nil {
        fmt.Printf("Namespace.GetNamespaceInfosFromAccount returned error: %s", err)
        return
    }
    for _, namespace := range namespaces {
        fmt.Printf("%s\n", namespace.String())
    }
}
```

### Get readable names for a set of namespaces

- Following parameters required:
  - **[]NamespaceID** - namespace identifiers to get info about

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

    // Generate Ids from namespace names
    proximaxId, err := sdk.NewNamespaceIdFromName("proximax")
    if err != nil {
        fmt.Printf("NewNamespaceIdFromName returned error: %s", err)
        return
    }
    mynamespaceId, err := sdk.NewNamespaceIdFromName("mynamespace")
    if err != nil {
        fmt.Printf("NewNamespaceIdFromName returned error: %s", err)
        return
    }

    namespaceNames, err := client.Namespace.GetNamespaceNames(context.Background(), []*sdk.NamespaceId{proximaxId, mynamespaceId})
    if err != nil {
        fmt.Printf("Namespace.GetNamespaceNames returned error: %s", err)
        return
    }
    for _, name := range namespaceNames {
        fmt.Printf("%s\n", name.String())
    }
}
```

### Get namespace infos for a given array of addresses

- Following parameters required:
  - **[]Address** - addresses of accounts to get namespaces from
  - **pageSize** - pagination
  - **nsId** - TODO

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
    // Types of network.
    networkType = sdk.MijinTest
    // valid addresses
    rawAddressOne = "SAUUKSFBYHI57KTEXQNJWHOKXITF7T4BXON3GVTJ"
    rawAddressTwo = "JTVG3NOXB4T7FTIXKOHWJNQXETK75IHYBFSKUUAS"
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

    // Generate Address struct
    addressOne, err := sdk.NewAddressFromBase32(rawAddressOne)
    if err != nil {
        fmt.Printf("NewAddressFromBase32 returned error: %s", err)
        return
    }
    addressTwo, err := sdk.NewAddressFromBase32(rawAddressTwo)
    if err != nil {
        fmt.Printf("NewAddressFromBase32 returned error: %s", err)
        return
    }

    namespaces, err := client.Namespace.GetNamespaceInfosFromAccounts(context.Background(), []*sdk.Address{addressOne, addressTwo}, nil, 0)
    if err != nil {
        fmt.Printf("Namespace.GetNamespaceInfosFromAccounts returned error: %s", err)
        return
    }
    for _, namespace := range namespaces {
        fmt.Printf("%s\n", namespace.String())
    }
}
```

