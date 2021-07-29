
### Get mosaic levy information

- Following parameters required:
  - **MosaicID** - mosaic identifier

```go
package main

package catapult_acceptance_tests

import (
	"context"
	"math/rand"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
	// A valid private key
	ownerPrivateKey = "819F72066B17FFD71B8B4142C5AEAE4B997B0882ABDF2C263B02869382BD93A0"
)

func main()  {
	random := rand.Rand{}
	
	// Testnet default config
	config, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		panic(err)
	}
	
	// Use the default http client
	client := sdk.NewClient(nil, config)
	
	// Create owner from private key
	owner, err := client.NewAccountFromPrivateKey(ownerPrivateKey)
	if err != nil {
		panic(err)
	}
	
	// suppose a mosaicId of created mosaic before
	mosaicId, err := sdk.NewMosaicIdFromNonceAndOwner(random.Uint32(), owner.PublicAccount.PublicKey)
	if err != nil {
		panic(err)
	}

	//Get levy info
	levyInfo, err := client.Mosaic.GetMosaicLevy(Ctx, mosaicId)
	if err != nil {
		panic(err)
	}
	print(levyInfo.String())
}

```