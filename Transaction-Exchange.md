### Add Exchange Offer Transaction
Add Exchange Offer Transaction is used to create a new exchange offer. For creating use **NewAddExchangeOfferTransaction()**

Following parameters required:
- **AddOffers** - An array of new offers

```go
package main

import (
	"context"
	"fmt"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
	"sync"
	"time"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"
	// A valid private key
	privateKey = "2C8178EF9ED7A6D30ABDC1E4D30D68B05861112A98B1629FBE2C8D16FDE97A1C"
)

var timeout = time.Minute*5

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), timeout)
	defer cancel()
	
	conf, err := sdk.NewConfig(ctx, []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Create websocket client
	wsClient, err := websocket.NewClient(ctx, conf)
	if err != nil {
		panic(err)
	}

	defer wsClient.Close()
	go func() {
		wsClient.Listen()
	}()

	// Create an account that add a new exchange offer from
	account, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	var wg sync.WaitGroup
	wg.Add(1)
	// add handler to wait while exchange will be created
	err = wsClient.AddConfirmedAddedHandlers(account.Address, func (info sdk.Transaction) bool {
		wg.Done()

		return true
	})
	if err != nil {
		panic(err)
	}

	// Send a new NewAddExchangeOfferTransaction
	transaction, err := client.NewAddExchangeOfferTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour),
		// Array of new offers
		[]*sdk.AddOffer{
			{
				sdk.Offer{
					Type:   sdk.SellOffer,
					Mosaic: sdk.Storage(10000000),
					Cost:   sdk.Amount(10000000),
				},
				sdk.Duration(4),
			},
		},
	)
	if err != nil {
		fmt.Printf("NewAccountPropertiesAddressTransaction returned error: %s", err)
		return
	}

	signedTransaction, err := account.Sign(transaction)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedTransaction)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}

	wg.Wait()

	exchangeInfo, err := client.Exchange.GetAccountExchangeInfo(ctx, account.PublicAccount)
	if err != nil {
		fmt.Printf("Exchange.GetAccountExchangeInfo returned error: %s", err)
		return
	}

	println(exchangeInfo.String())
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
	"github.com/pkg/errors"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
	"time"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"
	// A valid private key
	privateKey = "2C8178EF9ED7A6D30ABDC1E4D30D68B05861112A98B1629FBE2C8D16FDE97A1C"
)

var timeout = time.Minute*5

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), timeout)
	defer cancel()
	
	conf, err := sdk.NewConfig(ctx, []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Create websocket client
	wsClient, err := websocket.NewClient(ctx, conf)
	if err != nil {
		panic(err)
	}

	defer wsClient.Close()
	go func() {
		//start listen websocket
		wsClient.Listen()
	}()

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

	//create chan for message
	ch := make(chan string)

	// add handler to wait while exchange will be created
	if err = wsClient.AddConfirmedAddedHandlers(accountSeller.Address, func (info sdk.Transaction) bool {
		ch <- "Created!"

		return true
	}); err != nil {
		panic(err)
	}

	offerType := sdk.SellOffer
	mosaic := sdk.Storage(10000000)

	// Send a new NewAddExchangeOfferTransaction
	transaction, err := client.NewAddExchangeOfferTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour),
		// Array of new offers
		[]*sdk.AddOffer{
			{
				sdk.Offer{
					Type:   offerType,
					Mosaic: mosaic,
					Cost:   sdk.Amount(10000000),
				},
				sdk.Duration(10),
			},
		},
	)
	if err != nil {
		fmt.Printf("NewAccountPropertiesAddressTransaction returned error: %s", err)
		return
	}

	//sign transaction
	signedTransaction, err := accountSeller.Sign(transaction)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedTransaction)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}

	//wait message. If didn't get a message after timeout then panic
	select {
	case msg := <-ch:
		println(msg)
	case <-ctx.Done():
		panic(errors.New("Did not create"))
	}

	// Get user offers info
	userExchangeInfo, err := client.Exchange.GetAccountExchangeInfo(ctx, accountSeller.PublicAccount)
	if err != nil {
		fmt.Printf("Exchange.GetAccountExchangeInfo returned error: %s", err)
		return
	}

	// Get mosaic info
	mosaicInfo, err := client.Resolve.GetMosaicInfoByAssetId(ctx, mosaic.AssetId)
	if err != nil {
		fmt.Printf("Resolve.GetMosaicInfoByAssetId returned error: %s", err)
		return
	}

	// Get offer info by mosaicId
	offerInfo := userExchangeInfo.Offers[offerType][*mosaicInfo.MosaicId]

	// add handler to wait while exchange will be created
	if err = wsClient.AddConfirmedAddedHandlers(accountBuyer.Address, func (info sdk.Transaction) bool {
		ch <- "Bought!"

		return true
	}); err != nil {
		panic(err)
	}

	//send mosaics to boyer
	txToBuyer, err := client.NewTransferTransaction(
		sdk.NewDeadline(time.Hour),
		accountBuyer.Address,
		[]*sdk.Mosaic{sdk.Xpx(2)},
		sdk.NewPlainMessage(""),
	)
	if err != nil {
		fmt.Printf("NewTransferTransaction returned error: %s", err)
		return
	}
	txToBuyer.ToAggregate(accountSeller.PublicAccount)

	// sign ToBuy transaction
	signedBuy, err := accountSeller.Sign(txToBuyer)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	ctx, cancel = context.WithTimeout(ctx, time.Minute*2)
	defer cancel()

	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedBuy)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}

	// Create a new confirmation with the mosaic amount
	confirmation, err := offerInfo.ConfirmOffer(2)
	if err != nil {
		fmt.Printf("ConfirmOffer returned error: %s", err)
		return
	}

	// Create exchange
	exchangeTrx, err := client.NewExchangeOfferTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour),
		// An array of offers confirmation
		[]*sdk.ExchangeConfirmation{
			confirmation,
		},
	)

	// Sign transaction
	signedExchangeTransaction, err := accountBuyer.Sign(exchangeTrx)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	ctx, cancel = context.WithTimeout(ctx, time.Minute*2)
	defer cancel()

	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedExchangeTransaction)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}

	//wait message. If didn't get a message after timeout then panic
	select {
	case msg := <-ch:
		println(msg)
	case <-ctx.Done():
		panic(errors.New("Did not bought"))
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
"github.com/pkg/errors"
"github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
"time"

"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"
	// A valid private key
	privateKey = "2C8178EF9ED7A6D30ABDC1E4D30D68B05861112A98B1629FBE2C8D16FDE97A1C"
)

var timeout = time.Minute*2

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), timeout)
	defer cancel()

	conf, err := sdk.NewConfig(ctx, []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Create websocket client
	wsClient, err := websocket.NewClient(ctx, conf)
	if err != nil {
		panic(err)
	}

	defer wsClient.Close()
	go func() {
		//start listen websocket
		wsClient.Listen()
	}()

	// Create an account that add a new exchange offer from
	account, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	//create chan for message
	ch := make(chan string)

	// add handler to wait while exchange will be created
	if err = wsClient.AddConfirmedAddedHandlers(account.Address, func (info sdk.Transaction) bool {
		ch <- "Created!"

		return true
	}); err != nil {
		panic(err)
	}

	offerType := sdk.SellOffer
	mosaic := sdk.Storage(10000000)

	// Send a new NewAddExchangeOfferTransaction
	transaction, err := client.NewAddExchangeOfferTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour),
		// Array of new offers
		[]*sdk.AddOffer{
			{
				sdk.Offer{
					Type:   offerType,
					Mosaic: mosaic,
					Cost:   sdk.Amount(10000000),
				},
				sdk.Duration(10),
			},
		},
	)
	if err != nil {
		fmt.Printf("NewAccountPropertiesAddressTransaction returned error: %s", err)
		return
	}

	//sign transaction
	signedTransaction, err := account.Sign(transaction)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedTransaction)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}

	//wait message. If didn't get a message after timeout then panic
	select {
	case msg := <-ch:
		println(msg)
	case <-ctx.Done():
		panic(errors.New("Did not create"))
	}

	// add handler to wait while exchange will be created
	if err = wsClient.AddConfirmedAddedHandlers(account.Address, func (info sdk.Transaction) bool {
		ch <- "Removed!"

		return true
	}); err != nil {
		panic(err)
	}

	transactionRemove, err := client.NewRemoveExchangeOfferTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour),
		// An array of offers to removing
		[]*sdk.RemoveOffer{
			{
				offerType,
				sdk.StorageNamespaceId,
			},
		},
	)

	// Sign transaction
	signedExchangeTransaction, err := account.Sign(transactionRemove)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}
	
	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedExchangeTransaction)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}

	//wait message. If didn't get a message after timeout then panic
	select {
	case msg := <-ch:
		println(msg)
	case <-ctx.Done():
		panic(errors.New("Did not removed"))
	}
}
```
