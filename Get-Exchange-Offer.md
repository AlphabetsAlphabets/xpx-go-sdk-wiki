### Get Account's Exchange Offers
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
	// Sirius api rest server
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

### Get Exchange Offers Array by Mosaic Namespace
Returns `OffersInfo` of an account.

Following parameters required:
 - **assetId** - assetId of a mosaic
 - **type** - offer type (Buy or Sell)

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

### Add Exchange Offer
Creates a new offer.

Following parameters required:
 - **Deadline** - deadline of transaction;
 - **AddOffer** - array of new AddOffers:
	- **Offer**:
		- **Type** - offer type (Sell or Buy);
		- **Mosaic** - some mosaic (SO, XPX, SM, etc.);
		- **Cost** - a total cost of offered mosaics;
	- **Duration** - duration of offer.

 ```go
package main

const (
	// Sirius api rest server
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

	// Account from private key
	accountSeller, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	addOfferTrx, err := Client.NewAddExchangeOfferTransaction(
		sdk.NewDeadline(time.Hour),
		[]*sdk.AddOffer{
			{
				sdk.Offer{
					Type:   sdk.SellOffer,
					Mosaic: sdk.Storage(100),
					Cost:   sdk.Amount(50),
				},
				sdk.Duration(100),
			},
		},
	)

	// Sign transaction
	signedTransaction, err := accountSeller.Sign(addOfferTrx)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	// Announce transaction
	_, err = client.Transaction.Announce(context.Background(), signedTransaction)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}
}
```

### Exchange Offer
Makes exchange.

Following parameters required:
 - **Deadline** - deadline of transaction;
 - **ExchangeConfirmation** - confirmation of exchange.

 ```go
package main

import (
	"context"
	"fmt"
	"time"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
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

	// Account from private key
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

	mosaicInfo, err := Client.Resolve.GetMosaicInfoByAssetId(Ctx, sdk.StorageNamespaceId)
	if err != nil {
		fmt.Printf("GetMosaicInfoByAssetId returned error: %s", err)
		return
	}

	offerInfo := userExchangeInfo.Offers[sdk.SellOffer][*mosaicInfo.MosaicId]
	confirmation, err := offerInfo.ConfirmOffer(2)

	exchangeOfferTrx, err := Client.NewExchangeOfferTransaction(
		sdk.NewDeadline(time.Hour),
		[]*sdk.ExchangeConfirmation{
			confirmation,
		},
	)

	// Sign transaction
	signedTransaction, err := accountSeller.Sign(exchangeOfferTrx)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	// Announce transaction
	_, err = client.Transaction.Announce(context.Background(), signedTransaction)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}
}
```

### Remove Exchange Offer
Removes an offer.

Following parameters required:
 - **Deadline** - deadline of transaction;
 - **RemoveOffer** - array of offers for remove:
	- **Offer**:
		- **Type** - offer type (Sell or Buy);
		- **AssetID** - namespace ID of some mosaic (SO, XPX, SM, etc.).

 ```go
package main

import (
	"context"
	"fmt"
	"time"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
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

	// Account from private key
	accountSeller, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	removeOfferTrx, err := Client.NewRemoveExchangeOfferTransaction(
		sdk.NewDeadline(time.Hour),
		[]*sdk.RemoveOffer{
			{
				sdk.SellOffer,
				sdk.StorageNamespaceId,
			},
		},
	)

	// Sign transaction
	signedTransaction, err := accountSeller.Sign(removeOfferTrx)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	// Announce transaction
	_, err = client.Transaction.Announce(context.Background(), signedTransaction)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}
}
```