### Network Config Transaction

Network config transaction is used to update pre-defined Siriuc Chain network in runtime.
To create update network config transaction use **NewNetworkConfigTransaction()**.

- Following parameters required:
  - **Duration** - Blocks count, after which updated config will be applied.
  - **BlockChainConfig**
  - **SupportedEntityVersions**

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

	// Create an account from a private key
	nemesisAccount, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	// Firstly get current network config
	currentConfig, err := client.Network.GetNetworkConfig(context.Background())
	if err != nil {
		fmt.Printf("GetNetworkConfig returned error: %s", err)
		return
	}

	// Update it in a way you want, f.e. remove transfer transaction support
	delete(currentConfig.NetworkConfig.Sections, "plugin:catapult.plugins.transfer")

	// Create a new network config type transaction
	transaction, err := client.NewNetworkConfigTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour*1),
		// Blocks count, after which updated config will be applied
		sdk.Duration(500),
		// Blockchain config
		currentConfig.NetworkConfig,
		// Supported entity versions
		currentConfig.SupportedEntityVersions,
	)
	if err != nil {
		fmt.Printf("NewNetworkConfigTransaction returned error: %s", err)
		return
	}

	// Sign transaction
	signedTransaction, err := nemesisAccount.Sign(transaction)
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

### Blockchain Upgrade Transaction

Blockchain upgrade transaction is used to update version of Sirius Chain.
To create update network config transaction use **NewBlockchainUpgradeTransaction()**.

- Following parameters required:
  - **Duration** - Blocks count, after which updated config will be applied;
  - **Version** - New version of Sirius-Chain.

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

	// Create an account from a private key
	nemesisAccount, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	// Firstly get current network config
	currentVersion, err := client.Network.GetNetworkVersion(context.Background())
	if err != nil {
		fmt.Printf("GetNetworkVersion returned error: %s", err)
		return
	}

	// Create a new network config type transaction
	transaction, err := client.NewBlockchainUpgradeTransaction(
		// The maximum amount of time to include the transaction in the blockchain.
		sdk.NewDeadline(time.Hour*1),
		// Blocks count, after which updated config will be applied
		sdk.Duration(500),
		// Sirius-Chain version
		currentVersion.BlockChainVersion+1,
	)
	if err != nil {
		fmt.Printf("NewCatapultUpgradeTransaction returned error: %s", err)
		return
	}

	// Sign transaction
	signedTransaction, err := nemesisAccount.Sign(transaction)
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
