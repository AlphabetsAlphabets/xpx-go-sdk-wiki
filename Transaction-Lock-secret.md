### SecretLockTransaction
Secret Lock Transaction is used to create a new secret lock. For creating use **NewSecretLockTransaction()**.

Following parameters required:
 - **mosaic** - a mosaic that will be locked
 - **duration** - how long mosaic will be locked
 - **secret** - generated secret
 - **recipient** - recipient of the mosaic

```go
package main

import (
	"context"
	"fmt"
	"math/rand"
	"sync"
	"time"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
	// Private key of some exist account. Change it
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

	// Create websocket client
	wsClient, err := websocket.NewClient(context.Background(), conf)
	if err != nil {
		panic(err)
	}
	defer wsClient.Close()

	//start listen
	go wsClient.Listen()

	// Create an replicator that add a new exchange offer from
	account, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	//create a new recipient
	recipient, err := client.NewAccount()
	if err != nil {
		fmt.Printf("NewAccount returned error: %s", err)
		return
	}

	//create a new proof
	proofB := make([]byte, 8)
	_, err = rand.Read(proofB)
	if err != nil {
		fmt.Printf("rand.Read returned error: %s", err)
		return
	}

	proof := sdk.NewProofFromBytes(proofB)
	secret, _ := proof.Secret(sdk.SHA3_256)

	// A new secret lock
	secretLockTrx, err := client.NewSecretLockTransaction(sdk.NewDeadline(time.Hour), sdk.XpxRelative(5), sdk.Duration(100), secret, recipient.Address)
	if err != nil {
		fmt.Printf("NewSecretLockTransaction returned error: %s", err)
		return
	}

	// Sign a new secret lock
	signedLockTrx, err := account.Sign(secretLockTrx)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	wg := &sync.WaitGroup{}
	wg.Add(1)
	// add handler
	err = wsClient.AddConfirmedAddedHandlers(account.Address, func (info sdk.Transaction) bool {
	    fmt.Printf("Confirmed transaction: %s", info.String())
		wg.Done()
		return true
	})

	// Announce transaction
	_, err = client.Transaction.Announce(context.Background(), signedLockTrx)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}

	wg.Wait()
	println("Finished")
}
```

### SecretProofTransaction
Secret Proof Transaction is used to create a new secret lock. For creating use **NewSecretProofTransaction()**.

Following parameters required:
 - **hashType** - type of hash
 - **proof** - proof
 - **recipient** - recipient of the mosaic

```go
package main

import (
	"context"
	"fmt"
	"math/rand"
	"sync"
	"time"

	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk/websocket"
)

const (
	// Sirius api rest server
	baseUrl = "http://localhost:3000"
	// Private key of some exist account. Change it
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

	// Create websocket client
	wsClient, err := websocket.NewClient(context.Background(), conf)
	if err != nil {
		panic(err)
	}
	defer wsClient.Close()

	//start listen
	go wsClient.Listen()

	// Create an replicator that add a new exchange offer from
	account, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

	//create a new recipient
	recipient, err := client.NewAccount()
	if err != nil {
		fmt.Printf("NewAccount returned error: %s", err)
		return
	}

	//create a new proof
	proofB := make([]byte, 8)
	_, err = rand.Read(proofB)
	if err != nil {
		fmt.Printf("rand.Read returned error: %s", err)
		return
	}

	//New proof
	proof := sdk.NewProofFromBytes(proofB)
	//New secret from proof
	secret, _ := proof.Secret(sdk.SHA3_256)

	// A new secret lock
	secretLockTrx, err := client.NewSecretLockTransaction(sdk.NewDeadline(time.Hour), sdk.XpxRelative(5), sdk.Duration(100), secret, recipient.Address)
	if err != nil {
		fmt.Printf("NewSecretLockTransaction returned error: %s", err)
		return
	}

	// Sign a new secret lock
	signedLockTrx, err := account.Sign(secretLockTrx)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	wg := &sync.WaitGroup{}
	wg.Add(1)
	// add handler
	err = wsClient.AddConfirmedAddedHandlers(account.Address, func (info sdk.Transaction) bool {
		wg.Done()
		return true
	})

	// Announce transaction
	_, err = client.Transaction.Announce(context.Background(), signedLockTrx)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}

	wg.Wait()

	//Create a new proof transaction
	proofTx, err := client.NewSecretProofTransaction(sdk.NewDeadline(time.Hour), sdk.SHA3_256, proof, recipient.Address)
	if err != nil {
		fmt.Println(err)
		return
	}

	// Sign the proof transaction
	signedProofTrx, err := account.Sign(proofTx)
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	wg.Add(1)
	// add handler
	err = wsClient.AddConfirmedAddedHandlers(account.Address, func (info sdk.Transaction) bool {
		wg.Done()
		return true
	})

	// Announce transaction
	_, err = client.Transaction.Announce(context.Background(), signedProofTrx)
	if err != nil {
		fmt.Printf("Transaction.Announce returned error: %s", err)
		return
	}

	wg.Wait()
	println("Finished")
}
```