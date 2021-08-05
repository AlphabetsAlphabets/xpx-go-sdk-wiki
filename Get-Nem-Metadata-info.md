
### Get Metadata Info for Address

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
	// an account thad added metadata
	privateKey = "809CD6699B7F38063E28F606BD3A8AECA6E13B1E688FE8E733D13DB843BC14B7"
	// another account for which metadata was added
	anotherPrivateKey = "764B3AA022FB929CAA204670A817205DC08F2B172D501F36D4F0EC4EA50AFAE9"
)

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	acc, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		panic(err)
	}

	anotherAcc, err := client.NewAccountFromPrivateKey(anotherPrivateKey)
	if err != nil {
		panic(err)
	}

	hash, err := sdk.CalculateUniqueAccountMetadataId(acc.Address, anotherAcc.PublicAccount, 1)
	if err != nil {
		panic(err)
	}

	metadata, err := client.MetadataNem.GetMetadataNemInfo(context.Background(), hash)
	if err != nil {
		panic(err)
	}

	fmt.Println(metadata)
}
```

### Get Metadata Info for Mosaic

- Following parameters required:
  - **MosaicID** - mosaic identifier to get mosaic properties from

```go
package main

import (
	"context"
	"fmt"
	math "math/rand"
	"time"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
	// an account thad added metadata
	privateKey = "809CD6699B7F38063E28F606BD3A8AECA6E13B1E688FE8E733D13DB843BC14B7"
	// another account for which metadata was added
	mosaicOwnerPrivateKey = "764B3AA022FB929CAA204670A817205DC08F2B172D501F36D4F0EC4EA50AFAE9"
)

// suppose that mosaic already exists
func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)
	
	acc, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		panic(err)
	}

	mosaicOwner, err := client.NewAccountFromPrivateKey(mosaicOwnerPrivateKey)
	if err != nil {
		panic(err)
	}

	r := math.New(math.NewSource(time.Now().UTC().UnixNano()))
	nonce := r.Uint32()
	mosaicId, err := sdk.NewMosaicIdFromNonceAndOwner(nonce, mosaicOwner.PublicAccount.PublicKey)
	if err != nil {
		panic(err)
	}

	hash, _ := sdk.CalculateUniqueMosaicMetadataId(acc.Address, mosaicOwner.PublicAccount, 1, mosaicId)
	if err != nil {
		panic(err)
	}

	metadata, err := client.MetadataNem.GetMetadataNemInfo(context.Background(), hash)
	if err != nil {
		panic(err)
	}

	fmt.Println(metadata)
}
```

### Get Metadata Info for Namespace

- Following parameters required:
  - **NamespaceID** - namespace identifier to get namespace properties from

```go
package main

import (
	"context"
	"encoding/hex"
	"fmt"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
	// an account that added metadata and mosaic owner. Owner and account that added namespace metadata could be different
	privateKey = "809CD6699B7F38063E28F606BD3A8AECA6E13B1E688FE8E733D13DB843BC14B7"
)

// suppose that namespace already exist
func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	owner, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		panic(err)
	}

	nameHex := hex.EncodeToString([]byte("SomeName"))

	namespaceId, err := sdk.NewNamespaceIdFromName(nameHex)
	if err != nil {
		panic(err)
	}

	hash, err := sdk.CalculateUniqueNamespaceMetadataId(owner.Address, owner.PublicAccount, 1, namespaceId)
	if err != nil {
		panic(err)
	}

	metadata, err := client.MetadataNem.GetMetadataNemInfo(context.Background(), hash)
	if err != nil {
		panic(err)
	}

	fmt.Println(metadata)
}
```