### Modify metadata transactions
Metadata is a feature of ProximaX Catapult, which allows to attach some data in format of maps like `map[string]string`. Metadata can be attached to `Address`, `Mosaic`, and `Namespace`.


#### Attach metadata to Address

````go
package main

import (
	"context"
	"github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
	"time"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"

	// Types of network.
	// MainNet:   104
	// TestNet:   152
	// Mijin:     96
	// MijinTest: 144
	networkType = sdk.MijinTest

	// A valid private key
	privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
)

func main() {

	// Testnet config default
	conf, err := sdk.NewConfig(baseUrl,networkType)
	if err != nil {
		panic(err)
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Create an account from a private key
	account, err := sdk.NewAccountFromPrivateKey(privateKey, networkType)
	if err != nil {
		panic(err)
	}

	// Create a modify metadata transaction
	trx, err := sdk.NewModifyMetadataAddressTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		deadline,
    // Address where metadata should be attached
    account.PublicAccount.Address,
    // Actual data which will be added/removed
    []*sdk.MetadataModification{
      {
        // add or remove metadata
        sdk.AddMetadata,
        // key which should be used to store data
        "my_key_name",
        // actual data which will be stored in associated key
        "my_data",
      },
    },
		networkType,
	)
  if err != nil {
    panic(err)
  }

  // Sign modification by address owner
  signedTrx, err := account.Sign(trx)
  if err != nil {
    panic(err)
  }

  // Announce transaction to the blockchain
  _, err = client.Transaction.Announce(context.Background(), signedTrx)
  if err != nil {
    panic(err)
  }

	// wait for the transaction to be confirmed! (very important)
    // you can use websockets to wait explicitly for transaction
    // to be in certain state, instead of hard waiting
	time.Sleep(time.Second * 30)

}
```

#### Attach metadata to Mosaic

````go
package main

import (
	"context"
	"github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
	"time"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"

	// Types of network.
	// MainNet:   104
	// TestNet:   152
	// Mijin:     96
	// MijinTest: 144
	networkType = sdk.MijinTest

	// A valid private key
	privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
  // Valid mosaic nonce
  nonce = ...
)

func main() {

  // Create account from private key
	account, err := sdk.NewAccountFromPrivateKey(privateKey, networkType)
	if err != nil {
		panic(err)
	}

	// Testnet config default
	conf, err := sdk.NewConfig(baseUrl,networkType)
	if err != nil {
		panic(err)
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

  // Create mosaic id of just published mosaic
  mosaicId, err := sdk.NewMosaicIdFromNonceAndOwner(nonce, account.PublicAccount.PublicKey)
  if err != nil {
    panic(err)
  }

	// Create a modify metadata transaction
	trx, err := sdk.NewModifyMetadataMosaicTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour),
    // Id of mosaic where metadata should be added
    mosaicId,
    // Actual data which will be added/removed
    []*sdk.MetadataModification{
      {
        // add or remove metadata
        sdk.AddMetadata,
        // key which should be used to store data
        "my_key_name",
        // actual data which will be stored in associated key
        "my_data",
      },
    },
		networkType,
	)
  if err != nil {
    panic(err)
  }

  // Sign modification by mosaic owner
  signedTrx, err := account.Sign(trx)
  if err != nil {
    panic(err)
  }

  // Announce transaction to the blockchain
  _, err = client.Transaction.Announce(context.Background(), signedTrx)
  if err != nil {
    panic(err)
  }

	// wait for the transaction to be confirmed! (very important)
    // you can use websockets to wait explicitly for transaction
    // to be in certain state, instead of hard waiting
	time.Sleep(time.Second * 30)

}
```

#### Attach metadata to Namespace

```go
package main

import (
	"context"
	"github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
	"time"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"

	// Types of network.
	// MainNet:   104
	// TestNet:   152
	// Mijin:     96
	// MijinTest: 144
	networkType = sdk.MijinTest

	// Valid private key
	privateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
  // Valid namespace name
  namespaceName = "..."
)

func main() {

  // Create account from private key
	account, err := sdk.NewAccountFromPrivateKey(privateKey, networkType)
	if err != nil {
		panic(err)
	}

	// Testnet config default
	conf, err := sdk.NewConfig(baseUrl,networkType)
	if err != nil {
		panic(err)
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

  //Create namespace id from name
  namespaceId, err := sdk.NewNamespaceIdFromName(namespaceName)
  if err != nil {
    panic(err)
  }

	// Create a modify metadata transaction
	trx, err := sdk.NewModifyMetadataNamespaceTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour),
    // Id of namespace where metadata should be added
    namespaceId,
    // Actual data which will be added/removed
    []*sdk.MetadataModification{
      {
        // add or remove metadata
        sdk.AddMetadata,
        // key which should be used to store data
        "my_key_name",
        // actual data which will be stored in associated key
        "my_data",
      },
    },
		networkType,
	)
  if err != nil {
    panic(err)
  }

  // Sign modification by namespace owner
  signedTrx, err := account.Sign(trx)
  if err != nil {
    panic(err)
  }

  // Announce transaction to the blockchain
  _, err = client.Transaction.Announce(context.Background(), signedTrx)
  if err != nil {
    panic(err)
  }

	// wait for the transaction to be confirmed! (very important)
    // you can use websockets to wait explicitly for transaction
    // to be in certain state, instead of hard waiting
	time.Sleep(time.Second * 30)

}
```
