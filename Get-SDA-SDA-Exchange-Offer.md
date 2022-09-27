### Get Account's SDA-SDA Exchange Offers

Returns the `UserSdaExchangeInfo` of an account.

The following parameters are required:
* **Context** - context
* **PublicAccount** - PublicAccount of an account

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
	// Private key of some existing account
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
	account, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	// Get user offers info
	userSdaExchangeInfo, err := client.SdaExchange.GetAccountSdaExchangeInfo(context.Background(), account.PublicAccount)
	if err != nil {
		fmt.Printf("SdaExchange.GetAccountSdaExchangeInfo returned error: %s", err)
		return
	}

	fmt.Println(userSdaExchangeInfo.String())
}
```

### Get SDA-SDA Exchange Offer by AssetId

Returns the `SdaOfferBalance` of an account.

The following parameters are required:
* **Context** - context
* **AssetId** - AssetId of a mosaic
* **offerType** - _give_ or _get_

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

	// Get the balance of the SDA-SDA offers by namespaceId (assetId)
	sdaOfferBalanace, err := client.SdaExchange.GetSdaExchangeOfferByAssetId(context.Background(), sdk.StorageNamespaceId, "give")
	if err != nil {
		fmt.Printf("SdaExchange.GetSdaExchangeOfferByAssetId returned error: %s", err)
		return
	}

	fmt.Println(sdaOfferBalanace)
}

```

### Place SDA-SDA Exchange Offer

Creates a new SDA-SDA offer.

The following parameters are required:
* **Deadline** - deadline of the transaction
* **PlaceSdaOffer** - array of the PlaceSdaOffer
  * **SdaOffer**
    * **MosaicGive** - mosaic to be given (e.g. SO)
    * **MosaicGet** - mosaic to be gained (e.g. SM)
  * **Duration** - duration of the SDA-SDA offer

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
	account, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	placeSdaOfferTrx, err := client.NewPlaceSdaExchangeOfferTransaction(
		sdk.NewDeadline(time.Hour),
		[]*sdk.PlaceSdaOffer{
			{
				sdk.SdaOffer{
					sdk.Storage(100),
					sdk.Streaming(200),
				},
				sdk.Duration(1000),
			},
			{
				sdk.SdaOffer{
					sdk.Streaming(200),
					sdk.Storage(100),
				},
				sdk.Duration(1000),
			},
		},
	)

	// Sign transaction
	signedTransaction, err := account.Sign(placeSdaOfferTrx)
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

### Remove SDA-SDA Exchange Offer

Removes an existing SDA-SDA offer.

The following parameters are required:
* **Deadline** - deadline of the transaction
* **RemoveSdaOffer** - array of the RemoveSdaOffer
  * **RemoveSdaOffer**
    * **AssetIdGive** - mosaic that was to be given
    * **AssetIdGet** - mosaic that was to be gained
 
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
	account, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	removeSdaOfferTrx, err := client.NewRemoveSdaExchangeOfferTransaction(
		sdk.NewDeadline(time.Hour),
		[]*sdk.RemoveSdaOffer{
			{
				sdk.StorageNamespaceId,
				sdk.StreamingNamespaceId,
			},
		},
	)

	// Sign transaction
	signedTransaction, err := account.Sign(removeSdaOfferTrx)
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