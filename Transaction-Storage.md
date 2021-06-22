>**Note**\
Transaction below requires different mosaics, so it includes some exchanges. Consequently, there are should exist exchange offers.

### PrepareDriveTransaction
Prepare Drive Transaction is used to create a new drive. For creating use **NewPrepareDriveTransaction()**

Following parameters is required:
- **Owner** - drive creator
- **Duration** - drive duration (in blocks)
- **BillingPeriod** - payment frequency (in blocks)
- **BillingPrice** - cost of one billing period (in SO)
- **StorageSize** - size of drive (in MB)
- **Replicas** - necessary count of drive copy
- **MinReplicators** - minimal amount of replicators
- **PercentApprovers** - percent approvers for confirming any drive transaction

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
	privateKey = "63485A29E5D1AA15696095DCE792AACD014B85CBC8E473803406DEE20EC71958"
)

var timeout = time.Minute*4

var client *sdk.Client

var wsClient websocket.CatapultClient

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), timeout)
	defer cancel()

	conf, err := sdk.NewConfig(ctx, []string{baseUrl})
	handleError(err)

	// Use the default http client
	client = sdk.NewClient(nil, conf)

	// Create websocket client
	wsClient, err = websocket.NewClient(ctx, conf)
	handleError(err)

	defer wsClient.Close()
	// Some an account that will owner of a new drive
	owner, err := client.NewAccountFromPrivateKey(privateKey)
	handleError(err)

	// Create a new drive account
	driveAccount, err := client.NewAccount()
	handleError(err)

	// Prepare a new drive
	driveTx, err := client.NewPrepareDriveTransaction(
		sdk.NewDeadline(time.Hour),
		owner.PublicAccount,
		sdk.Duration(1000),
		sdk.Duration(100),
		sdk.Amount(500),
		sdk.StorageSize(1000),
		3,
		1,
		67,
	)
	handleError(err)
	//Aggregate
	driveTx.ToAggregate(driveAccount.PublicAccount)

	//Create NewBondedAggregateTransaction
	aggTx, err := client.NewCompleteAggregateTransaction(
		sdk.NewDeadline(time.Hour),
		[]sdk.Transaction{driveTx},
	)
	handleError(err)

	// Sign transaction
	signedAggTx, err := owner.SignWithCosignatures(aggTx, []*sdk.Account{driveAccount})
	if err != nil {
		fmt.Printf("Sign returned error: %s", err)
		return
	}

	var wg sync.WaitGroup
	wg.Add(1)
	// add handler
	err = wsClient.AddConfirmedAddedHandlers(driveAccount.Address, func (info sdk.Transaction) bool {
		wg.Done()

		return true
	})

	go wsClient.Listen()

	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedAggTx)
	handleError(err)

	wg.Wait()

	drive, err := client.Storage.GetDrive(ctx, driveAccount.PublicAccount)
	handleError(err)

	println(drive.String())
}
```

### JoinToDriveTransaction
Join To Drive Transaction is used to join to a drive. For creating use **NewJoinToDriveTransaction()**

Following parameters is required:
 - **publicAccount** - public account of a drive

>**Note**\
A replicator must have enough count of storage units to join.

Before starting use the example [to create a drive](#preparedrivetransaction)
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
	// Private key of some exist account. Change it
	replicatorPrivateKey = "349FB8AC38C9C4BD393B2E90E2CAB4ECBFA4E8088A6D840075BDEA1E22259956"
)

var timeout = time.Minute*4

var client *sdk.Client

var wsClient websocket.CatapultClient

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), timeout)
	defer cancel()

	conf, err := sdk.NewConfig(ctx, []string{baseUrl})
	handleError(err)

	// Use the default http client
	client = sdk.NewClient(nil, conf)

	// Create websocket client
	wsClient, err = websocket.NewClient(ctx, conf)
	handleError(err)
	defer wsClient.Close()

	//start listen
	go wsClient.Listen()

	//----------
	//Create a drive
	//------------

	// A replicator. It must have storage units
	replicator, err := client.NewAccountFromPrivateKey(replicatorPrivateKey)
	handleError(err)

	//Create a new join transaction
	joinTx, err := client.NewJoinToDriveTransaction(
		sdk.NewDeadline(time.Hour),
		driveAccount.PublicAccount,
	)
	handleError(err)

	// Sign transaction
	signedJoinTxOne, err := replicator.Sign(joinTx)
	handleError(err)

	wg.Add(1)
	// Wait confirmed transaction
	err = wsClient.AddConfirmedAddedHandlers(replicator.Address, func(transaction sdk.Transaction) bool {
		fmt.Printf("Replicato confirm INFO: %v\n", transaction.String())
		wg.Done()

		return true
	})
	handleError(err)

	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedJoinTxOne)
	handleError(err)

	if waitTimeout(wg, timeout) {
		panic("Replicator didn't join")
	} else {
		println("Replicator is joined!")
	}
}

func waitTimeout(wg *sync.WaitGroup, timeout time.Duration) bool {
	c := make(chan struct{})
	go func() {
		defer close(c)
		wg.Wait()
	}()
	select {
	case <-c:
		return false
	case <-time.After(timeout):
		return true
	}
}
```

### DriveFileSystemTransaction
Prepare Drive Transaction is used to create a new drive. For creating use **NewDriveFileSystemTransaction()**

Following parameters is required:
 - **DriveKey** - public account of a drive
 - **BewRootHash** - a new root hash of the drive
 - **OldRootHash** - an new root hash of the drive
 - **AddActions** - new added files
 - **RemoveActions** - deleted files

Before starting use the examples:
 - [to create a drive](#preparedrivetransaction)
 - [to join replicator](#jointodrivetransaction)
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
	baseUrl = "http://54.255.165.100:3000"
	// Private key of some exist account
	privateKey = "63485A29E5D1AA15696095DCE792AACD014B85CBC8E473803406DEE20EC71958"
	replicatorPrivateKey = "349FB8AC38C9C4BD393B2E90E2CAB4ECBFA4E8088A6D840075BDEA1E22259956"
)

var timeout = time.Minute*4

var client *sdk.Client

var wsClient websocket.CatapultClient

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), timeout)
	defer cancel()

	conf, err := sdk.NewConfig(ctx, []string{baseUrl})
	handleError(err)

	// Use the default http client
	client = sdk.NewClient(nil, conf)

	// Create websocket client
	wsClient, err = websocket.NewClient(ctx, conf)
	handleError(err)

	defer wsClient.Close()

	//start listen
	go wsClient.Listen()

	// Some an account that will owner of a new drive
	owner, err := client.NewAccountFromPrivateKey(privateKey)
	handleError(err)

	// Create a new drive account
	driveAccount, err := client.NewAccount()
	handleError(err)

	//-----------------------
	// - prepare a new drive
	// - join some replicator
	//-----------------------

	// some file hash
	fileHash, _ := sdk.StringToHash("AA2d2427E105A9B60DF634553849135DF629F1408A018D02B07A70CAFFB43093")
	// some file size
	fileSize := 50

	// some old drive hash - before data changes
	oldRootHash := &sdk.Hash{0}
	// some new drive hash - after data changes
	newRootHash := &sdk.Hash{1}

	//create a new drive file system transaction
	fsTx, err := client.NewDriveFileSystemTransaction(
		sdk.NewDeadline(time.Hour),
		driveAccount.PublicAccount,
		newRootHash,
		oldRootHash,
		[]*sdk.Action{
			{
				FileHash: fileHash,
				FileSize: sdk.StorageSize(fileSize),
			},
		},
		[]*sdk.Action{},
	)
	handleError(err)

	// Sign transaction
	signedFsTx, err := owner.Sign(fsTx)
	handleError(err)

	wg := &sync.WaitGroup{}
	wg.Add(1)
	err = wsClient.AddConfirmedAddedHandlers(owner.Address, func(transaction sdk.Transaction) bool {
		if signedFsTx.Hash.Equal(transaction.GetAbstractTransaction().TransactionHash){
			fmt.Println("FS transaction has been made!")
			wg.Done()
		}

		return true
	})
	handleError(err)

	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedFsTx)
	handleError(err)

	wg.Wait()
}
```

### FilesDepositTransaction
Files Deposit Transaction is used to update a remote drive. For creating use **NewFilesDepositTransaction()**

Following parameters is required:
 - **publicAccount** - public account of a drive
 - **files** - files hashes

Before starting use the examples:
 - [to create a drive](#preparedrivetransaction)
 - [to join replicator](#jointodrivetransaction)
 - [to upload a new file](#drivefilesystemtransaction)

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
	baseUrl = "http://54.255.165.100:3000"
	// Private key of some exist account. Change it
	privateKey = "63485A29E5D1AA15696095DCE792AACD014B85CBC8E473803406DEE20EC71958"
	replicatorPrivateKey = "349FB8AC38C9C4BD393B2E90E2CAB4ECBFA4E8088A6D840075BDEA1E22259956"
)

var timeout = time.Minute*4

var client *sdk.Client

var wsClient websocket.CatapultClient

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), timeout)
	defer cancel()

	conf, err := sdk.NewConfig(ctx, []string{baseUrl})
	handleError(err)

	// Use the default http client
	client = sdk.NewClient(nil, conf)

	// Create websocket client
	wsClient, err = websocket.NewClient(ctx, conf)
	handleError(err)
	defer wsClient.Close()

	//start listen
	go wsClient.Listen()

	// Some an account that will owner of a new drive
	owner, err := client.NewAccountFromPrivateKey(privateKey)
	handleError(err)

	// Create a new drive account
	driveAccount, err := client.NewAccount()
	handleError(err)

	// Create a replicator. It must have storage units
	replicator, err := client.NewAccountFromPrivateKey(replicatorPrivateKey)
	handleError(err)

	//-----------------------
	// - prepare a new drive
	// - join replicators
	// - upload a file
	//-----------------------

	// some file hash
	fileHash, _ := sdk.StringToHash("AA2d2427E105A9B60DF634553849135DF629F1408A018D02B07A70CAFFB43093")

	//New deposit transaction
	depositTx, err := client.NewFilesDepositTransaction(
		sdk.NewDeadline(time.Hour),
		driveAccount.PublicAccount,
		[]*sdk.File{
			{
				FileHash: fileHash,
			},
		},
	)
	handleError(err)

	// Sign transaction
	signedDepositTx, err := replicator.Sign(depositTx)
	handleError(err)

	wg.Add(1)
	//wait to confirmed transaction
	err = wsClient.AddConfirmedAddedHandlers(replicator.Address, func(transaction sdk.Transaction) bool {
		if signedDepositTx.Hash.Equal(transaction.GetAbstractTransaction().TransactionHash){
			fmt.Println("Deposited!")
			wg.Done()
		}

		return true
	})
	handleError(err)

	// Announce transaction
	_, err = client.Transaction.Announce(context.Background(), signedDepositTx)
	handleError(err)

	wg.Wait()

	if waitTimeout(wg, timeout) {
		panic("Deposit didn't make")
	}
}
```

### EndDriveTransaction
End Drive Transaction is used to terminate the drive. It can be sent by an [owner](#enddrivetransaction-by-owner) or a [driverAccount](#enddrivetransaction-by-replicators). For creating use **NewEndDriveTransaction()**

Following parameters is required:
 - **driveKey** - public account of a drive

#### EndDriveTransaction By Owner
Before starting use the examples:
 - [to create a drive](#preparedrivetransaction)

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
	baseUrl = "http://54.255.165.100:3000"
	// Private key of some exist account
	privateKey = "63485A29E5D1AA15696095DCE792AACD014B85CBC8E473803406DEE20EC71958"
)

var timeout = time.Minute*4

var client *sdk.Client

var wsClient websocket.CatapultClient

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), timeout)
	defer cancel()

	conf, err := sdk.NewConfig(ctx, []string{baseUrl})
	handleError(err)

	// Use the default http client
	client = sdk.NewClient(nil, conf)

	// Create websocket client
	wsClient, err = websocket.NewClient(ctx, conf)
	if err != nil {
		panic(err)
	}
	defer wsClient.Close()

	//start listen
	go wsClient.Listen()

	// Some an account that will owner of a new drive
	owner, err := client.NewAccountFromPrivateKey(privateKey)
	handleError(err)

	// Create a new drive account
	driveAccount, err := client.NewAccount()
	handleError(err)

	//-----------------------------------------
	// prepare a new drive
	//-----------------------------------------

	// End drive by owner
	endDriveTx, err := client.NewEndDriveTransaction(
		sdk.NewDeadline(time.Hour),
		driveAccount.PublicAccount,
	)

	// Sign transaction
	signedEndTx, err := owner.Sign(endDriveTx)
	handleError(err)

	wg.Add(1)
	// add handler
	err = wsClient.AddConfirmedAddedHandlers(owner.Address, func (info sdk.Transaction) bool {
		fmt.Println("Drive has been terminated!")
		wg.Done()

		return true
	})

	// Announce transaction
	_, err = client.Transaction.Announce(context.Background(), signedEndTx)
	handleError(err)

	wg.Wait()
}
```

#### EndDriveTransaction By Replicators
Before starting use the examples:
 - [to create a drive](#preparedrivetransaction)
 - [to join replicator](#jointodrivetransaction)

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
	baseUrl = "http://54.255.165.100:3000"
	// Private key of some exist account
	privateKey = "63485A29E5D1AA15696095DCE792AACD014B85CBC8E473803406DEE20EC71958"
)

var timeout = time.Minute*4

var client *sdk.Client

var wsClient websocket.CatapultClient

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), timeout)
	defer cancel()

	conf, err := sdk.NewConfig(ctx, []string{baseUrl})
	handleError(err)

	// Use the default http client
	client = sdk.NewClient(nil, conf)

	// Create websocket client
	wsClient, err = websocket.NewClient(ctx, conf)
	handleError(err)

	defer wsClient.Close()

	//start listen
	go wsClient.Listen()

	// Some an account that will owner of a new drive
	owner, err := client.NewAccountFromPrivateKey(privateKey)
	handleError(err)

	// Create a new drive account
	driveAccount, err := client.NewAccount()
	handleError(err)

	//-----------------------------------------
	// prepare a new drive
	// join replicators
	//-----------------------------------------

	// End drive by replicators
	endDriveTx, err := client.NewEndDriveTransaction(
		sdk.NewDeadline(time.Hour),
		driveAccount.PublicAccount,
	)
	handleError(err)
	endDriveTx.ToAggregate(driveAccount.PublicAccount)

	aggComplTrx, err := client.NewCompleteAggregateTransaction(
		sdk.NewDeadline(time.Hour),
		[]sdk.Transaction{endDriveTx},
	)
	handleError(err)

	// Sign transaction
	signedEndTx, err := replicator.SignWithCosignatures(aggComplTrx, []*sdk.Account{}) //sign with other replicators
	handleError(err)

	wg.Add(1)
	// add handler
	err = wsClient.AddConfirmedAddedHandlers(driveAccount.Address, func (info sdk.Transaction) bool {
		fmt.Println("Drive has been terminated!")
		wg.Done()

		return true
	})

	// Announce transaction
	_, err = client.Transaction.Announce(context.Background(), signedEndTx)
	handleError(err)

	wg.Wait()
}
```

### DriveFilesRewardTransaction
Drive Files Reward Transaction is used to reward replicators for files downloading. For creating use **NewDriveFilesRewardTransaction()**

Following parameters is required:
 - **uploadInfos** - info about uploads

Before starting use the examples:
 - [to create a drive](#preparedrivetransaction)
 - [to join replicator](#jointodrivetransaction)
 - [to upload a new file](#drivefilesystemtransaction)
 - [to make file deposit](#filesdeposittransaction)

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
	handleError(err)

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Create an replicator that add a new exchange offer from
	owner, err := client.NewAccountFromPrivateKey(privateKey)
	handleError(err)

	// A new replicator
	replicator, err := client.NewAccount()
	handleError(err)

	// Create a new drive account
	driveAccount, err := client.NewAccount()
	handleError(err)

	//-----------------------
	// - prepare a new drive
	// - join replicators
	// - upload a file
	// - make file deposit
	//-----------------------

	// Create new DriveFilesRewardTransaction
	driveFilesRewardTx, err := client.NewDriveFilesRewardTransaction(
		sdk.NewDeadline(time.Hour),
		[]*sdk.UploadInfo{
			{
				Participant:  replicator.PublicAccount, // replicator that downloaded a file
				UploadedSize: sdk.Amount(100), // the file size
			},
		},
	)
	handleError(err)
	// Aggregate
	driveFilesRewardTx.ToAggregate(driveAccount.PublicAccount)

	// Create NewCompleteAggregateTransaction
	aggRewardTx, err := client.NewCompleteAggregateTransaction(
		sdk.NewDeadline(time.Hour),
		[]sdk.Transaction{driveFilesRewardTx},
	)

	// Sign transaction
	signedRewardTx, err := driveAccount.SignWithCosignatures(aggRewardTx, []*sdk.Account{replicator})
	handleError(err)

	wg.Add(1)
	// Wait confirmed transaction
	err = wsClient.AddConfirmedAddedHandlers(driveAccount.Address, func(transaction sdk.Transaction) bool {
		fmt.Println("Rewarded!")
		wg.Done()

		return true
	})
	handleError(err)

	// Announce transaction
	_, err = client.Transaction.Announce(context.Background(), signedRewardTx)
	handleError(err)

	wg.Wait()
}
```

### StartDriveVerificationTransaction
Start Drive Verification Transaction is used to start drive verification. It can be sent by an owner or one of the replicators. For creating use **NewStartDriveVerificationTransaction()**

>**Note**\
To start verification the drive must be in the ***InProgress*** state. To start executing replicators have to make an exchange.

Following parameters is required:
 - **driveKey** - public account of a drive

Before starting use the examples:
 - [to create a drive](#preparedrivetransaction)
 - [to join replicator](#jointodrivetransaction)
 - [to upload a new file](#drivefilesystemtransaction)
 - [to make file deposit](#filesdeposittransaction)

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
	handleError(err)

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Create an replicator that add a new exchange offer from
	owner, err := client.NewAccountFromPrivateKey(privateKey)
	handleError(err)

	// Create a new drive account
	driveAccount, err := client.NewAccount()
	handleError(err)

	//-----------------------
	// - prepare a new drive
	// - join replicators
	// - upload a file
	// - make a file deposit
	//-----------------------

	// Create a new start drive verification transaction
	startVerificationTx, err := client.NewStartDriveVerificationTransaction(
		sdk.NewDeadline(time.Hour),
		driveAccount.PublicAccount,
	)
	handleError(err)

	// Sign transaction
	signedVerificationTx, err := owner.Sign(startVerificationTx)
	handleError(err)

	wg := &sync.WaitGroup{}
	wg.Add(1)
	// Wait confirmed transaction
	err = wsClient.AddConfirmedAddedHandlers(owner.Address, func(transaction sdk.Transaction) bool {
		if signedVerificationTx.Hash.Equal(transaction.GetAbstractTransaction().TransactionHash){
			fmt.Println("Verification started!")
			wg.Done()
		}

		return true
	})
	handleError(err)

	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedVerificationTx)
	handleError(err)

	wg.Wait()
}
```

### EndDriveVerificationTransaction
End Drive Verification Transaction is used to end drive verification. For creating use **NewEndDriveVerificationTransaction()**

Following parameters is required:
 - **Failures** - replicator that failed verification and block hash where was it.

Before starting use the examples:
 - [to create a drive](#preparedrivetransaction)
 - [to join replicator](#jointodrivetransaction)
 - [to upload a new file](#drivefilesystemtransaction)
 - [to make file deposit](#filesdeposittransaction)

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
	handleError(err)

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Create an replicator that add a new exchange offer from
	owner, err := client.NewAccountFromPrivateKey(privateKey)
	handleError(err)

	// Create a new drive account
	driveAccount, err := client.NewAccount()
	handleError(err)

	//-----------------------
	// - prepare a new drive
	// - join replicators
	// - upload a file
	// - start verification
	//-----------------------

	// Create a new start drive verification transaction
	endVerificationTx, err := client.NewStartDriveVerificationTransaction(
		sdk.NewDeadline(time.Hour),
		driveAccount.PublicAccount,
	)
	handleError(err)

	// Sign transaction
	signedEndVerificationTx, err := owner.Sign(endVerificationTx)
	handleError(err)

	wg := &sync.WaitGroup{}
	wg.Add(1)
	// Wait confirmed transaction
	err = wsClient.AddConfirmedAddedHandlers(owner.Address, func(transaction sdk.Transaction) bool {
		if signedEndVerificationTx.Hash.Equal(transaction.GetAbstractTransaction().TransactionHash){
			fmt.Println("Verification finished!")
			wg.Done()
		}

		return true
	})
	handleError(err)

	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedEndVerificationTx)
	handleError(err)

	wg.Wait()
}
```

### StartFileDownloadTransaction
Returns `StartFileDownloadTransaction`. For creating use **NewStartFileDownloadTransaction()**

Following parameters is required:
 - **PublicAccount** - public account of a drive
 - **[]*DownloadFile** - downloaded files

Before starting use the examples:
 - [to create a drive](#preparedrivetransaction)
 - [to join replicator](#jointodrivetransaction)
 - [to upload a new file](#drivefilesystemtransaction)

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
	privateKey = "28FCECEA252231D2C86E1BCF7DD541552BDBBEFBB09324758B3AC199B4AA7B78"
)

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	handleError(err)

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	//Create an account that add a new exchange offer from
	account, err := client.NewAccountFromPrivateKey(privateKey)
	handleError(err)

	var driveAccount *sdk.Account

	// Some new replicator
	replicator, err := client.NewAccount()
	handleError(err)

	//-----------------------------
	//Prepare the drive
	//Join replicators to the drive
	//Upload a file
	//-----------------------------

	// some file hash
	fileHash, _ := sdk.StringToHash("AA2d2427E105A9B60DF634553849135DF629F1408A018D02B07A70CAFFB43093")
	// some file size
	fileSize := 50

	//Create a new download transaction
	startFileDownloadTx, err := client.NewStartFileDownloadTransaction(
		sdk.NewDeadline(time.Hour),
		driveAccount.PublicAccount,
		[]*sdk.DownloadFile{
			{
				FileHash: fileHash,
				FileSize: sdk.StorageSize(fileSize),
			},
		},
	)
	handleError(err)
	startFileDownloadTx.ToAggregate(owner.PublicAccount)

	downloadTx, err := client.NewCompleteAggregateTransaction(
		sdk.NewDeadline(time.Hour),
		[]sdk.Transaction{startFileDownloadTx},
	)
	handleError(err)

	//Sign downloadTx
	signedDownloadTx, err := owner.Sign(downloadTx)
	handleError(err)

	wg.Add(1)
	// Wait confirmed transaction
	err = wsClient.AddConfirmedAddedHandlers(owner.Address, func(transaction sdk.Transaction) bool {
		fmt.Println("Started!")
		wg.Done()

		return true
	})
	handleError(err)

	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedDownloadTx)
	handleError(err)
}
```

### EndFileDownloadTransaction
Returns `EndFileDownloadTransaction`. For creating use **NewStartFileDownloadTransaction()**

Following parameters is required:
 - **PublicAccount** - public account of a recipient
 - **OperationToken** - OperationToken of a download transaction (hash)
 - **[]*DownloadFile** - downloaded files

Before starting use the examples:
 - [to create a drive](#preparedrivetransaction)
 - [to join replicator](#jointodrivetransaction)
 - [to upload a new file](#drivefilesystemtransaction)
 - [to start file download](#startfiledownloadtransaction)

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
	privateKey = "28FCECEA252231D2C86E1BCF7DD541552BDBBEFBB09324758B3AC199B4AA7B78"
)

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	handleError(err)

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Some an account that will owner of a new drive
	owner, err := client.NewAccountFromPrivateKey(privateKey)
	handleError(err)

	var driveAccount *sdk.Account

	// Some new replicator
	replicator, err := client.NewAccount()
	handleError(err)

	//-----------------------------
	//Prepare the drive
	//Join replicators to the drive
	//Upload a file
	//Start file download
	//-----------------------------

	// some file hash
	fileHash, _ := sdk.StringToHash("AA2d2427E105A9B60DF634553849135DF629F1408A018D02B07A70CAFFB43093")
	// some file size
	fileSize := 50

	//Create a new EndFileDownloadTransaction
	endFileDownloadTx, err := client.NewEndFileDownloadTransaction(
		sdk.NewDeadline(time.Hour),
		holder.PublicAccount,
		uniqueHash,
		[]*sdk.DownloadFile{
			{
				FileHash: fileHash,
				FileSize: sdk.StorageSize(fileSize),
			},
		},
	)
	handleError(err)
	endFileDownloadTx.ToAggregate(driveAccount.PublicAccount)

	aggEndDownloadTx, err := client.NewCompleteAggregateTransaction(
		sdk.NewDeadline(time.Hour),
		[]sdk.Transaction{endFileDownloadTx},
	)
	handleError(err)

	replicatorWithoutFirst := replicatorAccounts[1:]
	// Send aggEndDownloadTx by first replicator and cosign by others
	signedAggEndDownloadTx, err := replicatorAccounts[0].SignWithCosignatures(aggEndDownloadTx, replicatorWithoutFirst)
	handleError(err)

	wg.Add(1)
	// Wait confirmed transaction
	err = wsClient.AddConfirmedAddedHandlers(driveAccount.Address, func(transaction sdk.Transaction) bool {
		fmt.Println("Ended!")
		wg.Done()

		return true
	})
	handleError(err)

	// Announce transaction
	_, err = client.Transaction.Announce(ctx, signedEndDownloadTx)
	handleError(err)
}
```