### New Metadata

Metadata is a feature of Sirius Chain, which allows to attach some data. Metadata can be attached to `Address`, `Mosaic`, and `Namespace`. Every Metadata transaction should be sent like aggregate transaction

### Attach Metadata to Account

- Following parameters are required:
  - **PublicAccount** - publicAccount to attach metadata
  - **scopedKey** - key of metadata
  - **newValue** - new metadata value that will be set
  - **oldValue** - old metadata value that will be changed

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
	// an account thad added metadata
	privateKey = "809CD6699B7F38063E28F606BD3A8AECA6E13B1E688FE8E733D13DB843BC14B7"
	// another account for which metadata was added
	anotherPrivateKey = "764B3AA022FB929CAA204670A817205DC08F2B172D501F36D4F0EC4EA50AFAE9"
)

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	metadataAdder, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		panic(err)
	}

	metadataRelatedAcc, err := client.NewAccountFromPrivateKey(anotherPrivateKey)
	if err != nil {
		panic(err)
	}

	metadataTx, err := client.NewAccountMetadataTransaction(
		sdk.NewDeadline(1*time.Hour),
		metadataRelatedAcc.PublicAccount,
		1,
		"Hello world",
		"",
	)
	if err != nil {
		panic(err)
	}
	metadataTx.ToAggregate(metadataAdder.PublicAccount)

	abt, err := client.NewBondedAggregateTransaction(
		sdk.NewDeadline(time.Hour),
		[]sdk.Transaction{metadataTx},
	)
	if err != nil {
		panic(err)
	}

	signedAbt, err := metadataAdder.Sign(abt)
	if err != nil {
		panic(err)
	}

	// Create lock funds transaction for aggregate bounded
	lockFundsTransaction, err := client.NewLockFundsTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour*1),
		// Funds to lock
		sdk.XpxRelative(10),
		// Duration of lock transaction in blocks
		sdk.Duration(1000),
		// Aggregate bounded transaction for lock
		signedAbt,
	)
	if err != nil {
		panic(err)
	}

	signedLockFundsTrx, err := metadataAdder.Sign(lockFundsTransaction)
	if err != nil {
		panic(err)
	}

	_, err = client.Transaction.Announce(context.Background(), signedLockFundsTrx)
	if err != nil {
		panic(err)
	}

	// Wait for lock funds transaction to be harvested
	time.Sleep(30 * time.Second)

	_, err = client.Transaction.AnnounceAggregateBonded(context.Background(), signedAbt)
	if err != nil {
		panic(err)
	}

	cosigTrx := sdk.NewCosignatureTransactionFromHash(signedAbt.Hash)
	signedCosigTrx, err := metadataRelatedAcc.SignCosignatureTransaction(cosigTrx)
	if err != nil {
		panic(err)
	}

	_, err = client.Transaction.AnnounceAggregateBondedCosignature(context.Background(), signedCosigTrx)
	if err != nil {
		panic(err)
	}
}
```

### Attach Metadata to Mosaic

- Following parameters are required:
  - **Mosaic ID** - mosaic identifier to attach metadata
  - **TargetPublicAccount** - publicAccount that attaches metadata
  - **scopedKey** - key of metadata
  - **newValue** - new metadata value that will be set
  - **oldValue** - old metadata value that will be changed

```go
package main

import (
	"context"
	"fmt"
	math "math/rand"
	"time"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
	// an account thad added metadata
	privateKey = "809CD6699B7F38063E28F606BD3A8AECA6E13B1E688FE8E733D13DB843BC14B7"
)

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	metadataAdder, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		panic(err)
	}

	r := math.New(math.NewSource(time.Now().UTC().UnixNano()))
	nonce := r.Uint32()

	mosaicId, err := sdk.NewMosaicIdFromNonceAndOwner(nonce, metadataAdder.PublicAccount.PublicKey)
	if err != nil {
		panic(err)
	}

	// NOTE: before NewMosaicMetadataTransaction mosaic should be created with NewMosaicDefinitionTransaction
	metadataTx, err := client.NewMosaicMetadataTransaction(
		sdk.NewDeadline(1*time.Hour),
		mosaicId,
		metadataAdder.PublicAccount,
		1,
		"Hello world",
		"",
	)
	if err != nil {
		panic(err)
	}
	metadataTx.ToAggregate(metadataAdder.PublicAccount)

	abt, err := client.NewBondedAggregateTransaction(
		sdk.NewDeadline(time.Hour),
		[]sdk.Transaction{metadataTx},
	)
	if err != nil {
		panic(err)
	}

	signedAbt, err := metadataAdder.Sign(abt)
	if err != nil {
		panic(err)
	}

	// Create lock funds transaction for aggregate bounded
	lockFundsTransaction, err := client.NewLockFundsTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour*1),
		// Funds to lock
		sdk.XpxRelative(10),
		// Duration of lock transaction in blocks
		sdk.Duration(1000),
		// Aggregate bounded transaction for lock
		signedAbt,
	)
	if err != nil {
		panic(err)
	}

	signedLockFundsTrx, err := metadataAdder.Sign(lockFundsTransaction)
	if err != nil {
		panic(err)
	}

	_, err = client.Transaction.Announce(context.Background(), signedLockFundsTrx)
	if err != nil {
		panic(err)
	}
}
```

### Attach Metadata to Mosaic

- Following parameters are required:
  - **Namespace Id** - namespace identifier for metadata to add
  - **TargetPublicAccount** - publicAccount that attaches metadata
  - **scopedKey** - key of metadata
  - **newValue** - new metadata value that will be set
  - **oldValue** - old metadata value that will be changed

```go
package main

import (
	"context"
	"encoding/hex"
	"fmt"
	"time"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
	// an account thad added metadata
	privateKey = "809CD6699B7F38063E28F606BD3A8AECA6E13B1E688FE8E733D13DB843BC14B7"
)

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	metadataAdder, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		panic(err)
	}

	namespaceId, err := sdk.NewNamespaceIdFromName(hex.EncodeToString([]byte("Name")))
	if err != nil {
		panic(err)
	}

	// NOTE: before NewNamespaceMetadataTransaction namespace should be registered with NewRegisterRootNamespaceTransaction
	metadataTx, err := client.NewNamespaceMetadataTransaction(
		sdk.NewDeadline(1*time.Hour),
		namespaceId,
		metadataAdder.PublicAccount,
		1,
		"Hello world",
		"",
	)
	if err != nil {
		panic(err)
	}
	metadataTx.ToAggregate(metadataAdder.PublicAccount)

	abt, err := client.NewBondedAggregateTransaction(
		sdk.NewDeadline(time.Hour),
		[]sdk.Transaction{metadataTx},
	)
	if err != nil {
		panic(err)
	}

	signedAbt, err := metadataAdder.Sign(abt)
	if err != nil {
		panic(err)
	}

	// Create lock funds transaction for aggregate bounded
	lockFundsTransaction, err := client.NewLockFundsTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour*1),
		// Funds to lock
		sdk.XpxRelative(10),
		// Duration of lock transaction in blocks
		sdk.Duration(1000),
		// Aggregate bounded transaction for lock
		signedAbt,
	)
	if err != nil {
		panic(err)
	}

	signedLockFundsTrx, err := metadataAdder.Sign(lockFundsTransaction)
	if err != nil {
		panic(err)
	}

	_, err = client.Transaction.Announce(context.Background(), signedLockFundsTrx)
	if err != nil {
		panic(err)
	}
}
```