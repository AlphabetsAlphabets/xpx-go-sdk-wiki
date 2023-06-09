
### Account properties transactions

#### Address

An account wants receive transactions only from an allowed list of addresses. Also, an account can specify a list of addresses that it doesn't want to receive transactions from. This could be done via a **NewAccountPropertiesAddressTransaction()**.

- Following parameters required:
  - **Property Type** - Type of address property to be updated. Supported types:
    - `sdk.BlockAddress`
    - `sdk.AllowAddress`
  - **Account Properties Address Modifications** - The array of address
  modifications to be added or removed. Modifications consists from:
    - Types of modification update:
      - `sdk.AddProperty`
      - `sdk.RemoveProperty`
    - **Address** - address to Block or Allow transactions from


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
	// Types of network.
	networkType = sdk.MijinTest
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

	// Create an account from a private key
	account, err := sdk.NewAccountFromPrivateKey(privateKey, networkType, client.GenerationHash())
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}
	// Create an account for blocking
	accountToBlock, err := sdk.NewAccount(networkType, client.GenerationHash())
	if err != nil {
		fmt.Printf("NewAccount returned error: %s", err)
		return
	}

	// Create a new account properties address type transaction
	transaction, err := sdk.NewAccountPropertiesAddressTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour * 1),
		// Block transactions from addresses
		sdk.BlockAddress,
		// Account properties to update
		[]*sdk.AccountPropertiesAddressModification{
			{
				sdk.AddProperty,
				accountToBlock.Address,
			},
		},
		networkType,
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

	// wait for the transaction to be confirmed! (very important)
	// you can use websockets to wait explicitly for transaction
	// to be in certain state, instead of hard waiting
	time.Sleep(time.Second * 30)
}
```

#### Mosaic

An account can configure a filter for allowed and disallowe mosaics.
This could be done via a **NewAccountPropertiesMosaicTransaction()**.

- Following parameters required:
  - **Property Type** - Type of address property to be updated. Supported types:
    - `sdk.BlockMosaic`
    - `sdk.AllowMosaic`
  - **Account Properties Mosaic Modifications** - The array of mosaic
  modifications to be added or removed. Modifications consists from:
    - Types of modification update:
      - `sdk.AddProperty`
      - `sdk.RemoveProperty`
    - **MosaicID** - mosaic identifier

```go
package main

import (
	"context"
	"fmt"
	"math/rand"
	"time"
	
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
	// Types of network.
	networkType = sdk.MijinTest
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

	// Create an account from a private key
	account, err := sdk.NewAccountFromPrivateKey(privateKey, networkType, client.GenerationHash())
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	random := rand.New(rand.NewSource(time.Now().UTC().UnixNano()))
	nonce := random.Uint32()
	// Create mosaic id of just published mosaic
	mosaicId, err := sdk.NewMosaicIdFromNonceAndOwner(nonce, account.PublicAccount.PublicKey)
	if err != nil {
		fmt.Printf("NewMosaicIdFromNonceAndOwner returned error: %s", err)
		return
	}

	// Create a new account properties mosaic type transaction
	transaction, err := sdk.NewAccountPropertiesMosaicTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour*1),
		// Allow transactions with passed mosaic
		sdk.AllowMosaic,
		// Account properties to update
		[]*sdk.AccountPropertiesMosaicModification{
			{
				sdk.AddProperty,
				mosaicId,
			},
		},
		networkType,
	)
	if err != nil {
		fmt.Printf("NewAccountPropertiesMosaicTransaction returned error: %s", err)
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

	// wait for the transaction to be confirmed! (very important)
	// you can use websockets to wait explicitly for transaction
	// to be in certain state, instead of hard waiting
	time.Sleep(time.Second * 30)
}
```

#### Entity

An account can allow/block announcing outgoing transactions with a
determined type. By doing so, it increases its security, preventing
the announcement by mistake of undesired transactions.
This is done via a **NewAccountPropertiesEntityTypeTransaction()**.

- Following parameters required:
  - **Property Type** - Type of entity property to be updated. Supported types:
    - `sdk.BlockTransaction`
    - `sdk.AllowTransaction`
  - **Account Properties Entity Modifications** - The array of entity
  modifications to be added\removed. Modifications consists from:
    - Types of modification update:
      - `sdk.AddProperty`
      - `sdk.RemoveProperty`
    - **TransactionType** - type of transaction. Supported types:
      - `sdk.AccountPropertyAddress`
      - `sdk.AccountPropertyMosaic`
      - `sdk.AccountPropertyEntityType`
      - `sdk.AddressAlias`
      - `sdk.AggregateBonded`
      - `sdk.AggregateCompleted`
      - `sdk.LinkAccount`
      - `sdk.Lock`
      - `sdk.MetadataAddress`
      - `sdk.MetadataMosaic`
      - `sdk.MetadataNamespace`
      - `sdk.ModifyContract`
      - `sdk.ModifyMultisig`
      - `sdk.MosaicAlias`
      - `sdk.MosaicDefinition`
      - `sdk.MosaicSupplyChange`
      - `sdk.RegisterNamespace`
      - `sdk.SecretLock`
      - `sdk.SecretProof`

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
	// Types of network.
	networkType = sdk.MijinTest
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

	// Create an account from a private key
	account, err := sdk.NewAccountFromPrivateKey(privateKey, networkType, client.GenerationHash())
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	// Create a new account properties entity type transaction
	transaction, err := sdk.NewAccountPropertiesEntityTypeTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour),
		// Block transactions with entity type
		sdk.BlockTransaction,
		// Account properties to update
		[]*sdk.AccountPropertiesEntityTypeModification{
			{
				sdk.AddProperty,
				sdk.LinkAccount,
			},
		},
		networkType,
	)
	if err != nil {
		fmt.Printf("NewAccountPropertiesEntityTypeTransaction returned error: %s", err)
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

	// wait for the transaction to be confirmed! (very important)
	// you can use websockets to wait explicitly for transaction
	// to be in certain state, instead of hard waiting
	time.Sleep(time.Second * 30)
}
```

