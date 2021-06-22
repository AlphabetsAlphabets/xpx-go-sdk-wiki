
### Get mosaic information

- Following parameters required:
  - **MosaicID** - mosaic identifier

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

	mosaicId, err := sdk.NewMosaicId(0)
	if err != nil {
		fmt.Printf("NewMosaicId returned error: %s", err)
		return
	}

	// Get mosaic information.
	mosaic, err := client.Mosaic.GetMosaicInfo(context.Background(), mosaicId)
	if err != nil {
		fmt.Printf("Mosaic.GetMosaicInfo returned error: %s", err)
		return
	}
	fmt.Printf("%s\n", mosaic.String())
}
```

### Get information for array of mosaics

- Following parameters required:
  - **[]MosaicID** - mosaic IDs

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

	mosaicId1, err := sdk.NewMosaicId(1)
	if err != nil {
		fmt.Printf("NewMosaicId returned error: %s", err)
		return
	}
	mosaicId2, err := sdk.NewMosaicId(2)
	if err != nil {
		fmt.Printf("NewMosaicId returned error: %s", err)
		return
	}

	// Get mosaic information.
	mosaics, err := client.Mosaic.GetMosaicInfos(context.Background(), []*sdk.MosaicId{mosaicId1, mosaicId2})
	if err != nil {
		fmt.Printf("Mosaic.GetMosaicInfos returned error: %s", err)
		return
	}
	for _, mosaic := range mosaics {
		fmt.Printf("%s\n", mosaic.String())
	}
}
```

### Get information for mosaic linked to namespace

- Following parameters required:
  - **AssetId** - mosaic namespace id

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

	namespaceId, err := sdk.NewNamespaceIdFromName("mynamespace")
	if err != nil {
		fmt.Printf("NewNamespaceIdFromName returned error: %s", err)
		return
	}

	mosaic, err := client.Resolve.GetMosaicInfoByAssetId(context.Background(), namespaceId)
	if err != nil {
		fmt.Printf("Resolve.GetMosaicInfoByAssetId returned error: %s", err)
		return
	}

	fmt.Printf("%s\n", mosaic.String())
}
```

### Get information for mosaics linked to namespaces

- Following parameters required:
  - **[]AssetId** - array of namespace id which mosaic linked to

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

	namespaceId1, err := sdk.NewNamespaceIdFromName("one")
	if err != nil {
		fmt.Printf("NewNamespaceIdFromName returned error: %s", err)
		return
	}
	
	namespaceId2, err := sdk.NewNamespaceIdFromName("two")
	if err != nil {
		fmt.Printf("NewNamespaceIdFromName returned error: %s", err)
		return
	}

	mosaics, err := client.Resolve.GetMosaicInfosByAssetIds(context.Background(), namespaceId1, namespaceId2)
	if err != nil {
		fmt.Printf("Resolve.GetMosaicInfosByAssetIds returned error: %s", err)
		return
	}
	
	for _, mosaic := range mosaics {
		fmt.Printf("%s\n", mosaic.String())
	}

	for _, mosaic := range mosaics {
		fmt.Printf("%s\n", mosaic.String())
	}
}
```
