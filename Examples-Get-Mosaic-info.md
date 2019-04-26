### Get mosaic information.
  * Param mosaic - Mosaic identifier.
```go
package main

import (
	"encoding/json"
	"fmt"
	"github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
	"golang.org/x/net/context"
)

// Catapult-api-rest server.
const	(
         baseUrl = "http://localhost:3000"
         networkType = sdk.MijinTest
)

// Simple Mosaic API request
func main() {
	conf, err := sdk.NewConfig(baseUrl,networkType)
	if err != nil {
		panic(err)
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

  nonce := rand.New(rand.NewSource(time.Now().UTC().UnixNano())).Uint32()
  // Create a Mosaic identifier.
  mosaicId, err := sdk.NewMosaicIdFromNonceAndOwner(nonce, accOne.Address.Address)

	// Get mosaic information.
	getMosaic, err := client.Mosaic.GetMosaic(context.Background(), mosaicId)
	if err != nil {
		fmt.Printf("Mosaic.GetMosaic returned error: %s", err)
		return
	}
	getMosaicJson, _ := json.MarshalIndent(getMosaic, "", " ")
	fmt.Printf("%s\n\n", string(getMosaicJson))
}
```
### Get information for a set of mosaics.
  * Param mosaics - A MosaicIds struct.
```go
  // Create the Mosaics identifier.
  xpxNonce := rand.New(rand.NewSource(time.Now().UTC().UnixNano())).Uint32()
  xemNonce := rand.New(rand.NewSource(time.Now().UTC().UnixNano())).Uint32()

  xpxMosaicId, _ := sdk.NewMosaicIdFromNonceAndOwner(xpxNonce, accOne.Address.Address)
  xemMosaicId, _ := sdk.NewMosaicIdFromNonceAndOwner(xemNonce, accOne.Address.Address)

  var mosaics []*sdk.MosaicId
  // Append attachment into Mosaics
  mosaics = append(mosaics, xpxMosaicId)
  // Append attachment into Mosaics
  mosaics = append(mosaics, xemMosaicId)

  getMosaics, err := client.Mosaic.GetMosaics(context.Background(), mosaics)
  if err != nil {
    fmt.Printf("Mosaic.GetMosaics returned error: %s", err)
    return
  }
  getMosaicsJson, _ := json.MarshalIndent(getMosaics, "", " ")
  fmt.Printf("%s\n\n", string(getMosaicsJson))
```
