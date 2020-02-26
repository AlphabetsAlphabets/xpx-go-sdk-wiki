### Get account's exchange offers
Returns `UserExchangeInfo` of an account. 

Following parameters required:
 - **PublicAccount** - PublicAccount of some account

```go
package main

import (
	"context"
	"fmt"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"
	// Private key of some exist account
	privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
)

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	//Account from private key
	accountSeller, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	// Get user offers info
	userExchangeInfo, err := client.Exchange.GetAccountExchangeInfo(context.Background(), accountSeller.PublicAccount)
	if err != nil {
		fmt.Printf("Exchange.GetAccountExchangeInfo returned error: %s", err)
		return
	}

	println(userExchangeInfo.String())
}
```

### Get exchange offers array
Returns `OffersInfo` of an account.

Following parameters required:
 - **assetId** - assetId of some mosaic
 - **type** - offer type 

 ```go
package main

import (
	"context"
	"fmt"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"
)

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	offerType := sdk.SellOffer //or sdk.BuyOffer

	// Get offers info by namespaceId (assetId)
	offersInfo, err := client.Exchange.GetExchangeOfferByAssetId(context.Background(), sdk.StorageNamespaceId, offerType)
	if err != nil {
		fmt.Printf("Exchange.GetExchangeOfferByAssetId returned error: %s", err)
		return
	}

	fmt.Println(offersInfo)
}
```