
### Get Metadata info for Address

- Following parameters required:
  - **Address** - address to get account properties from

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
	// Valid account address for Mijin Test network
	address = "SCQALPC7AQHA6OEDCSOF45HEMLKIKFTSFISN2LVG"
	// Valid key which is used to store metadata in address
	key = "my_super_key"
)

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	info, err := client.Metadata.GetMetadataByAddress(context.Background(), address)
	if err != nil {
		fmt.Printf("Metadata.GetMetadataByAddress returned error: %s", err)
		return
	}
	fmt.Printf("Address %s\n", info.Address)
	fmt.Printf("Address Metadata Type %b\n", info.MetadataType)
	fmt.Printf("Metadata for %s: %s\n", key, info.Fields[key])

	// you can get info for multiple addresses at the same time also
	// infos, err := client.Metadata.GetAddressMetadatasInfo(context.Background(), address1, address2)
}
```


### Get Metadata info for Mosaic

- Following parameters required:
  - **MosaicID** - mosaic identifier to get mosaic properties from

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
	// Valid uint 32 nonce
	nonce = 1234
	// Valid public key of mosaic owner
	publicKey = "F5F090D5EC24E572CC9D4EC3B891D8C4435E986620CB38B003EA19EAD13AC724"
	// Valid key which is used to store metadata in address
	key = "my_super_key"
)

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}
	
	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Generate mosaic id
	mosaicId, err := sdk.NewMosaicIdFromNonceAndOwner(nonce, publicKey)
	if err != nil {
		panic(err)
	}

	info, err := client.Metadata.GetMetadataByMosaicId(context.Background(), mosaicId)
	if err != nil {
		fmt.Printf("Metadata.GetMetadataByMosaicId returned error: %s", err)
		return
	}
	fmt.Printf("Mosaic id %s\n", info.MosaicId)
	fmt.Printf("Mosaic Metadata Type %b\n", sdk.MetadataMosaicType == info.MetadataType)
	fmt.Printf("Metadata for %s: %s\n", key, info.Fields[key])

	// you can get info for multiple mosaics at the same time also
	// infos, err := client.Metadata.GetMosaicMetadatasInfo(context.Background(), mosaicId1, mosaicId2)
}
```


### Get Metadata info for Namespace

- Following parameters required:
  - **NamespaceID** - namespace identifier to get namespace properties from

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
	// Valid namespace name
	namespaceName = "ticket"
	// Valid key which is used to store metadata in address
	key = "my_super_key"
)

func main() {

	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Generate namespace id from name
	namespaceId, err := sdk.NewNamespaceIdFromName(namespaceName)
	if err != nil {
		panic(err)
	}

	info, err := client.Metadata.GetMetadataByNamespaceId(context.Background(), namespaceId)
	if err != nil {
		fmt.Printf("Metadata.GetMetadataByNamespaceId returned error: %s", err)
		return
	}
	fmt.Printf("Namespace id %s\n", info.NamespaceId)
	fmt.Printf("Namespace Metadata Type %b\n", sdk.MetadataNamespaceType == info.MetadataType)
	fmt.Printf("Metadata for %s: %s\n", key, info.Fields[key])

	// you can get info for multiple mosaics at the same time also
	// infos, err := client.Metadata.GetNamespaceMetadatasInfo(context.Background(), namespaceId1, namespaceId2)
}
```

