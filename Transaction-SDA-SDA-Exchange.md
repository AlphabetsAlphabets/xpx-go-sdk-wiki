### Place SDA-SDA Exchange Offer Transaction
Place SDA-SDA Exchange Offer Transaction is used to create a new SDA-SDA exchange offer. To create, use NewPlaceSdaExchangeOfferTransaction()

The following parameters are required:
* **Deadline** - deadline of the transaction
* **PlaceSdaOffer** - array of the PlaceSdaOffer
  * **SdaOffer**
    * **MosaicGive** - mosaic to be given
    * **MosaicGet** - mosaic to be gained
* **Duration** - duration of the SDA-SDA offer

**Note:** Offers cannot be placed for special mosaics - Storage (SO), Streaming (SM), Review, Supercontract (SC)

```go
package main

import (
	"context"
	"fmt"
	"sync"
	"time"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
	// Private key of some exist account
	privateKey = "2C8178EF9ED7A6D30ABDC1E4D30D68B05861112A98B1629FBE2C8D16FDE97A1C"
)

var timeout = time.Minute * 5

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

	// Some account that exchanges SDAs
	account, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	var wg sync.WaitGroup
	wg.Add(1)
	// add handler to wait while exchange will be created
	err = wsClient.AddConfirmedAddedHandlers(account.Address, func(info sdk.Transaction) bool {
		fmt.Printf("Confirmed transaction: %s", info.String())
		wg.Done()

		return true
	})
	if err != nil {
		panic(err)
	}

	// Send a new NewPlaceSdaExchangeOfferTransaction
	transaction, err := client.NewPlaceSdaExchangeOfferTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour),
		// An array of new SDA-SDA offers
		[]*sdk.PlaceSdaOffer{
			{
				SdaOffer: sdk.SdaOffer{
					MosaicGive: &sdk.Mosaic{AssetId: ExampleMosaicId1, Amount: 1000},
					MosaicGet:  &sdk.Mosaic{AssetId: ExampleMosaicId2, Amount: 2000},
				},
				sdk.Duration(1000),
			},
		},
	)
	if err != nil {
		fmt.Printf("NewPlaceSdaExchangeOfferTransaction returned error: %s", err)
		return
	}

	//Sign transaction
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

	// Get the account's SDA-SDA Offers
	userSdaExchangeInfo, err := client.SdaExchange.GetAccountSdaExchangeInfo(context.Background(), account.PublicAccount)
	if err != nil {
		fmt.Printf("SdaExchange.GetAccountSdaExchangeInfo returned error: %s", err)
		return
	}

	fmt.Println(userSdaExchangeInfo.String())
}
```

### Remove SDA-SDA Exchange Offer Transaction
Remove SDA-SDA Exchange Offer Transaction is used to remove an existing SDA-SDA exchange offer. To remove, use **NewRemoveSdaExchangeOfferTransaction()**

The following parameters are required:
* **Deadline** - deadline of the transaction
* **RemoveSdaOffer **- array of the RemoveSdaOffer
  * **RemoveSdaOffer**
    * **AssetIdGive** - mosaic that was to be given
    * ****AssetIdGet**** - mosaic that was to be gained

Before using this example, [create a new SDA-SDA exchange offer](https://github.com/proximax-storage/go-xpx-chain-sdk/wiki/_new#place-sda-sda-exchange-offer-transaction) first.

```go
package main

import (
	"context"
	"fmt"
	"time"

	"github.com/pkg/errors"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
	// Private key of some existing account
	privateKey = "2C8178EF9ED7A6D30ABDC1E4D30D68B05861112A98B1629FBE2C8D16FDE97A1C"
)

var timeout = time.Minute * 2

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
	//start listen websocket
	go wsClient.Listen()

	// Some account that exchanges SDAs
	account, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	//create chan for message
	ch := make(chan string)

	// add handler to wait while exchange will be created
	if err = wsClient.AddConfirmedAddedHandlers(account.Address, func(info sdk.Transaction) bool {
		ch <- "Removed!"

		return true
	}); err != nil {
		panic(err)
	}

	//Sends a NewRemoveSdaExchangeOfferTransaction
	removeSdaTrx, err := client.NewRemoveSdaExchangeOfferTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour),
		// An array of SDA-SDA offers to remove
		[]*sdk.RemoveSdaOffer{
			{
				AssetIdGive: ExampleNamespaceId1,
				AssetIdGet:  ExampleNamespaceId2,
			},
		},
	)

	// Sign transaction
	signedSdaExchangeTransaction, err := account.Sign(removeSdaTrx)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedSdaExchangeTransaction)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}

	//wait message. If didn't get a message after timeout then panic
	select {
	case msg := <-ch:
		println(msg)
	case <-ctx.Done():
		panic(errors.New("Did not remove"))
	}
}
```