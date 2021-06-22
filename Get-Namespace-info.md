
### Get namespace information

- Following parameters required:
  - **NamespaceID** - namespace identifier to get info about

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

	// Generate Id from namespaceName
	namespaceId, _ := sdk.NewNamespaceIdFromName("proximax")

	namespace, err := client.Namespace.GetNamespaceInfo(context.Background(), namespaceId)
	if err != nil {
		fmt.Printf("Namespace.GetNamespaceInfo returned error: %s", err)
		return
	}
	fmt.Println(namespace.String())
}
```

### Get namespaces an account owns

- Following parameters required:
  - **Address** - address of an account to get a namespaces from
  - **namespaceID** - id of some Namespace
  - **pageSize** - pagination

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
	// valid owner address
	rawAddress = "SAUUKSFBYHI57KTEXQNJWHOKXITF7T4BXON3GVTJ"
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

	// Generate Address struct
	address, err := sdk.NewAddressFromBase32(rawAddress)
	if err != nil {
		fmt.Printf("NewAddressFromBase32 returned error: %s", err)
		return
	}

	namespace := "someNamespace"
	namespaceID, err := sdk.NewNamespaceIdFromName(namespace)
	if err != nil {
		fmt.Println("NewNamespaceIdFromName: ", err)
		return
	}

	namespaces, err := client.Namespace.GetNamespaceInfosFromAccount(context.Background(), address, namespaceID, 0)
	if err != nil {
		fmt.Printf("Namespace.GetNamespaceInfosFromAccount returned error: %s", err)
		return
	}

	for _, namespace := range namespaces {
		fmt.Println(namespace.String())
	}
}
```

### Get readable names for a set of namespaces

- Following parameters required:
  - **[]NamespaceID** - array of namespace identifiers to get info about

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
  - **namespaceID** - id of some Namespace

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
	// valid addresses
	rawAddressOne = "SAUUKSFBYHI57KTEXQNJWHOKXITF7T4BXON3GVTJ"
	rawAddressTwo = "JTVG3NOXB4T7FTIXKOHWJNQXETK75IHYBFSKUUAS"
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

