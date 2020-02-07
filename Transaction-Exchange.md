### Add Exchange Offer Transaction
Add Exchange Offer Transaction is used to create a new exchange offer. For creating use **NewAddExchangeOfferTransaction()**

Following parameters required:
- **AddOffers** - An array of new offers

```go
package main

import (
    "context"
    "fmt"
    "time"
    
    "github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // A valid private key
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

    // Create an account that add a new exchange offer from
    account, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    offerType := sdk.SellOffer
	mosaic := sdk.Storage(10000000)
	cost := mosaic.Amount / 2

    // Create new offers
    addOffers := []*sdk.AddOffer{
        {
            sdk.Offer{
                offerType,
                mosaic,
                cost,
            },
            sdk.Duration(100),
		},
    }

    // Send a new NewAddExchangeOfferTransaction
    transaction, err := client.NewAddExchangeOfferTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour),
        // Array of new offers
        addOffers,
    )
    if err != nil {
        fmt.Printf("NewAccountPropertiesAddressTransaction returned error: %s", err)
        return
    }

    // Sign transaction
    signedTransaction, err := account.Sign(transaction)
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

### Exchange Offer Transaction
Exchange Offer Transaction is used to make exchange. For creating use **NewExchangeOfferTransaction()**

Following parameters required:
- **ExchangeConfirmation** - An array of offers confirmation.

```go
package main

import (
    "context"
    "fmt"
    "time"
    
    "github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // A valid private key
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

    // Create an account that add a new exchange offer from
    accountSeller, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // Create an account for make exchange with the new offer
    accountBuyer, err := client.NewAccount()
    if err != nil {
        fmt.Printf("NewAccount returned error: %s", err)
        return
    }

    offerType := sdk.SellOffer
	mosaic := sdk.Storage(10000000)
	cost := mosaic.Amount / 2

    addOffers := []*sdk.AddOffer{
        {
            sdk.Offer{
                offerType,
                mosaic,
                cost,
            },
            sdk.Duration(100),
		},
    }

    transaction, err := client.NewAddExchangeOfferTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour),
        // Array of new offers
        addOffers,
    )
    if err != nil {
        fmt.Printf("NewAccountPropertiesAddressTransaction returned error: %s", err)
        return
    }

    // Sign transaction
    signedTransaction, err := accountSeller.Sign(transaction)
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

    // Get user offers info
    userExchangeInfo, err := client.Exchange.GetAccountExchangeInfo(context.Background(), accountSeller.PublicAccount)
	if err != nil {
        fmt.Printf("Exchange.GetAccountExchangeInfo returned error: %s", err)
        return
    }

    // Get mosaic info
    mosaicInfo, err := client.Resolve.GetMosaicInfoByAssetId(context.Background(), mosaic.AssetId)
    if err != nil {
        fmt.Printf("Resolve.GetMosaicInfoByAssetId returned error: %s", err)
        return
    }

    // Get offer info by mosaicId
    offerInfo := userExchangeInfo.Offers[offerType][*mosaicInfo.MosaicId]
    
    // Create a new confirmation with the mosaic amount
    confirmation, err := offerInfo.ConfirmOffer(2)
	if err != nil {
        fmt.Printf("ConfirmOffer returned error: %s", err)
        return
    }

    confs := []*sdk.ExchangeConfirmation{
			        confirmation,
		        }

    // Create exchange
	exchangeOfferTransaction, err := client.NewExchangeOfferTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour),
        // An array of offers confirmation
		confs,
	)
    
    // Sign transaction
    signedExchangeTransaction, err := accountBuyer.Sign(exchangeOfferTransaction)
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    // Announce transaction
    _, err = client.Transaction.Announce(context.Background(), signedExchangeTransaction)
    if err != nil {
        fmt.Printf("Transaction.Announce returned error: %s", err)
        return
    }
}
```

### Remove Exchange Offer Transaction
Exchange Offer Transaction is used to make exchange. For creating use **NewExchangeOfferTransaction()**

Following parameters required:
- **RemoveOffer** - An array of offers to removing.

```go
package main


import (
    "context"
    "fmt"
    "time"

    "github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // A valid private key
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

    // Create an account that add a new exchange offer from
    account, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    offerType := sdk.SellOffer
    mosaic := sdk.Storage(10000000)
    cost := mosaic.Amount / 2

    addOffers := []*sdk.AddOffer{
        {
            sdk.Offer{
                offerType,
                mosaic,
                cost,
            },
            sdk.Duration(100),
        },
    }

    transaction, err := client.NewAddExchangeOfferTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour),
        // Array of new offers
        addOffers,
    )
    if err != nil {
        fmt.Printf("NewAccountPropertiesAddressTransaction returned error: %s", err)
        return
    }

    // Sign transaction
    signedTransaction, err := account.Sign(transaction)
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

    removeOffers := []*sdk.RemoveOffer{
        {
            offerType,
            sdk.StorageNamespaceId,
        },
    }

    transactionRemove, err := client.NewRemoveExchangeOfferTransaction(

        sdk.NewDeadline(time.Hour),
        // An array of offers to removing
        removeOffers,
    )

    // Sign transaction
    signedExchangeTransaction, err := account.Sign(transactionRemove)
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    // Announce transaction
    _, err = client.Transaction.Announce(context.Background(), signedExchangeTransaction)
    if err != nil {
        fmt.Printf("Transaction.Announce returned error: %s", err)
        return
    }
}
```
