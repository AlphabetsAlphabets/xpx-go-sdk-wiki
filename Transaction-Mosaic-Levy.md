
### Modify Mosaic Levy

- Following parameters are required:
  - **mosaicId** - ID of a mosaic;
  - **Mosaic Levy Properties**:
      - **type** - type of levy. Absoulute(1) or Percentile (2);
      - **levyRecipient** - an account that will get levy;
      - **Fee** - levy amount;
      - **mosaicId** - ID of a mosaic.

```go
package main

import (
	"context"
	"fmt"
	"math/rand"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
	// A valid private key
	ownerPrivateKey = "819F72066B17FFD71B8B4142C5AEAE4B997B0882ABDF2C263B02869382BD93A0"
	// A valid private key
	levyRecipientPrivateKey = "10AB82ACE20F2DC3EDC5E4C77B09420D60FB707B8D4AA86A768B05CF943A93EB"
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

	// Create levyRecipient from private key
	levyRecipient, err := client.NewAccountFromPrivateKey(levyRecipientPrivateKey)
	if err != nil {
		panic(err)
	}

	// suppose a mosaicId of created mosaic before
	mosaicId, err := sdk.NewMosaicIdFromNonceAndOwner(random.Uint32(), owner.PublicAccount.PublicKey)
	if err != nil {
		panic(err)
	}

	// Add levy
	levyTx, err := client.NewMosaicModifyLevyTransaction(
		sdk.NewDeadline(Deadline),
		mosaicId,
		&sdk.MosaicLevy{
			Type:      sdk.LevyAbsoluteFee,
			Recipient: levyRecipient.Address,
			Fee:       sdk.Amount(100),
			MosaicId:  mosaicId,
		},
	)
	if err != nil {
		panic(err)
	}

	// Sign transaction
	signedTransaction, err := owner.Sign(levyTx)
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


### Remove Mosaic Levy

- Following parameters are required:
  - **mosaicId** - ID of a mosaic;

```go
package main

import (
	"context"
	"fmt"
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

	// Remove levy
	removeTx, err := client.NewMosaicRemoveLevyTransaction(sdk.NewDeadline(Deadline), mosaicId)
	if err != nil {
		panic(err)
	}

	// Sign transaction
	signedRemoveTransaction, err := owner.Sign(removeTx)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	// Announce transaction
	_, err = client.Transaction.Announce(context.Background(), signedRemoveTransaction)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}
}
```
