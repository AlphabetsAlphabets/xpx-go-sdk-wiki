>**Note**\
Transaction below requires different mosaics, so during the execution going on exchange. Consequently, before starting should be created new exchange offers.

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
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // A valid private key
    privateKey = "63485A29E5D1AA15696095DCE792AACD014B85CBC8E473803406DEE20EC71958"
)

var timeout = time.Minute*4

var client *sdk.Client

var wsClient websocket.CatapultClient

func main() {
    ctx, cancel := context.WithTimeout(context.Background(), timeout)
    defer cancel()

    conf, err := sdk.NewConfig(ctx, []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client = sdk.NewClient(nil, conf)

    // Create websocket client
    wsClient, err = websocket.NewClient(ctx, conf)
    if err != nil {
        panic(err)
    }

    defer wsClient.Close()
    // Create an owner
    owner, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // Create a new drive account
    driveAccount, err := client.NewAccount()
    if err != nil {
        fmt.Printf("NewAccount returned error: %s", err)
        return
    }

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
    if err != nil {
        fmt.Printf("NewPrepareDriveTransaction returned error: %s", err)
        return
    }
    //Aggregate
    driveTx.ToAggregate(driveAccount.PublicAccount)

    //Create NewBondedAggregateTransaction
    aggTx, err := client.NewCompleteAggregateTransaction(
       sdk.NewDeadline(time.Hour),
       []sdk.Transaction{driveTx},
    )

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
    if err != nil {
       fmt.Printf("Transaction.Announce returned error: %s", err)
       return
    }

    wg.Wait()

    drive, err := client.Storage.GetDrive(ctx, driveAccount.PublicAccount)
    if err != nil {
        fmt.Printf("Storage.GetDrive returned error: %s", err)
        return
    }

    println(drive.String())
}
```

### JoinToDriveTransaction
Join To Drive Transaction is used to join to a drive. For creating use **NewJoinToDriveTransaction()**

Following parameters is required:
 - **driveKey** - public account of a drive

>**Note**\
A replicator must have enough count of storage units to join.

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
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // A valid private key. Change it
    replicatorPrivateKey = "349FB8AC38C9C4BD393B2E90E2CAB4ECBFA4E8088A6D840075BDEA1E22259956"
)

var timeout = time.Minute*4

var client *sdk.Client

var wsClient websocket.CatapultClient

func main() {
    ctx, cancel := context.WithTimeout(context.Background(), timeout)
    defer cancel()

    conf, err := sdk.NewConfig(ctx, []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

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

    //----------
    //Use example above to create a drive
    //------------

    // Create a replicator. It must have storage units
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
    // Catapult-api-rest server.
    baseUrl = "http://54.255.165.100:3000"
    // A valid private key
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
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

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

    // Create an owner
    owner, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // Create a new drive account
    driveAccount, err := client.NewAccount()
    if err != nil {
        fmt.Printf("NewAccount returned error: %s", err)
        return
    }

    //-----------------------
    //Use examples above for:
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
 - **driveKey** - public account of a drive
 - **files** - files hashes

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
    // Catapult-api-rest server.
    baseUrl = "http://54.255.165.100:3000"
    // A valid private key. Change it
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
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

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

    // Create an owner
    owner, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // Create a new drive account
    driveAccount, err := client.NewAccount()
    if err != nil {
        fmt.Printf("NewAccount returned error: %s", err)
        return
    }

    // Create a replicator. It must have storage units
    replicator, err := client.NewAccountFromPrivateKey(replicatorPrivateKey)
    handleError(err)

    //-----------------------
    //Use examples above for:
    // - prepare a new drive
    // - join replicators
    // - upload a file
    //-----------------------

    // some file hash
    fileHash, _ := sdk.StringToHash("AA2d2427E105A9B60DF634553849135DF629F1408A018D02B07A70CAFFB43093")

    depositTx, err := client.NewFilesDepositTransaction(
        sdk.NewDeadline(time.Hour),
        driveAccount.PublicAccount,
        []*sdk.File{
            {
                FileHash: fileHash,
            },
        },
    )

    // Sign transaction
    signedDepositTx, err := replicator.Sign(depositTx)
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    wg.Add(1)
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
    if err != nil {
        fmt.Printf("Transaction.Announce returned error: %s", err)
        return
    }

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
    // Catapult-api-rest server.
    baseUrl = "http://54.255.165.100:3000"
    // A valid private key
    privateKey = "63485A29E5D1AA15696095DCE792AACD014B85CBC8E473803406DEE20EC71958"
)

var timeout = time.Minute*4

var client *sdk.Client

var wsClient websocket.CatapultClient

func main() {
    ctx, cancel := context.WithTimeout(context.Background(), timeout)
    defer cancel()

    conf, err := sdk.NewConfig(ctx, []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

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

    // Create an owner
    owner, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // Create a new drive account
    driveAccount, err := client.NewAccount()
    if err != nil {
        fmt.Printf("NewAccount returned error: %s", err)
        return
    }

    //-----------------------------------------
    // Use example above to prepare a new drive
    //-----------------------------------------

    // End drive by owner
    endDriveTx, err := client.NewEndDriveTransaction(
        sdk.NewDeadline(time.Hour),
        driveAccount.PublicAccount,
    )

    // Sign transaction
    signedEndTx, err := owner.Sign(endDriveTx)
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    wg.Add(1)
    // add handler
    err = wsClient.AddConfirmedAddedHandlers(owner.Address, func (info sdk.Transaction) bool {
        fmt.Println("Drive has been terminated!")
        wg.Done()

        return true
    })

    // Announce transaction
    _, err = client.Transaction.Announce(context.Background(), signedEndTx)
    if err != nil {
        fmt.Printf("Transaction.Announce returned error: %s", err)
        return
    }

    wg.Wait()
}
```

#### EndDriveTransaction By Replicators
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
    // Catapult-api-rest server.
    baseUrl = "http://54.255.165.100:3000"
    // A valid private key
    privateKey = "63485A29E5D1AA15696095DCE792AACD014B85CBC8E473803406DEE20EC71958"
)

var timeout = time.Minute*4

var client *sdk.Client

var wsClient websocket.CatapultClient

func main() {
    ctx, cancel := context.WithTimeout(context.Background(), timeout)
    defer cancel()

    conf, err := sdk.NewConfig(ctx, []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

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

    // Create an owner
    owner, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // Create a new drive account
    driveAccount, err := client.NewAccount()
    if err != nil {
        fmt.Printf("NewAccount returned error: %s", err)
        return
    }

    //-----------------------------------------
    // Use example above to prepare a new drive,
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
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    wg.Add(1)
    // add handler
    err = wsClient.AddConfirmedAddedHandlers(driveAccount.Address, func (info sdk.Transaction) bool {
        fmt.Println("Drive has been terminated!")
        wg.Done()

        return true
    })

    // Announce transaction
    _, err = client.Transaction.Announce(context.Background(), signedEndTx)
    if err != nil {
        fmt.Printf("Transaction.Announce returned error: %s", err)
        return
    }

    wg.Wait()
}
```

### DriveFilesRewardTransaction
Drive Files Reward Transaction is used to reward replicators for files downloading. For creating use **NewDriveFilesRewardTransaction()**

Following parameters is required:
 - **uploadInfos** - info about uploads
 
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

    // Create an replicator that add a new exchange offer from
    owner, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // A new replicator
    replicator, err := client.NewAccount()
    if err != nil {
        fmt.Printf("NewAccount returned error: %s", err)
        return
    }

    // Create a new drive account
    driveAccount, err := client.NewAccount()
    if err != nil {
        fmt.Printf("NewAccount returned error: %s", err)
        return
    }

    //-----------------------
    //Use examples above for:
    // - prepare a new drive
    // - join replicators
    // - upload a file
    //-----------------------

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

    // Create an replicator that add a new exchange offer from
    owner, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // Create a new drive account
    driveAccount, err := client.NewAccount()
    if err != nil {
        fmt.Printf("NewAccount returned error: %s", err)
        return
    }

    //-----------------------
    //Use examples above for:
    // - prepare a new drive
    // - join replicators
    // - upload a file
    //-----------------------

    // Create a new start drive verification transaction
    startVerificationTx, err := client.NewStartDriveVerificationTransaction(
        sdk.NewDeadline(time.Hour),
        driveAccount.PublicAccount,
    )

    // Sign transaction
    signedVerificationTx, err := owner.Sign(startVerificationTx)
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    wg := &sync.WaitGroup{}
    wg.Add(1)
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
    if err != nil {
        fmt.Printf("Transaction.Announce returned error: %s", err)
        return
    }
    
    wg.Wait()
}
```

### EndDriveVerificationTransaction
End Drive Verification Transaction is used to end drive verification. For creating use **NewEndDriveVerificationTransaction()**

Following parameters is required:
 - **Failures** - replicator that failed verification and block hash where was it.

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

    // Create an replicator that add a new exchange offer from
    owner, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    // Create a new drive account
    driveAccount, err := client.NewAccount()
    if err != nil {
        fmt.Printf("NewAccount returned error: %s", err)
        return
    }

    //-----------------------
    //Use examples above for:
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

    // Sign transaction
    signedEndVerificationTx, err := owner.Sign(endVerificationTx)
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    wg := &sync.WaitGroup{}
    wg.Add(1)
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
    if err != nil {
        fmt.Printf("Transaction.Announce returned error: %s", err)
        return
    }
    
    wg.Wait()
}
```

### Start file download transaction
Returns `StartFileDownloadTransaction`. For creating use **NewStartFileDownloadTransaction()**

Following parameters is required:
 - **PublicAccount** - public account of a drive
 - **[]*DownloadFile** - downloaded files

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
    privateKey = "28FCECEA252231D2C86E1BCF7DD541552BDBBEFBB09324758B3AC199B4AA7B78"
)

func main() {
    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    //Create an account that add a new exchange offer from
    account, err := client.NewAccountFromPrivateKey(privateKey)
    if err != nil {
       fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
       return
    }

    var driveAccount *sdk.Account
    
    // Create a new replicator
    replicator, err := client.NewAccount()
    if err != nil {
        fmt.Printf("NewAccount returned error: %s", err)
        return
    }
    
    //-----------------------------
    //Create a new driveAccount
    //Prepare the drive
    //Join replicators to the drive
    //Upload a file
    //-----------------------------

    fileHash, _ := sdk.StringToHash("AA2d2427E105A9B60DF634553849135DF629F1408A018D02B07A70CAFFB43093") // some file hash
    fileSize := 50 // some file size

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

    signedDownloadTx, err := owner.SignWithCosignatures(downloadTx, []*sdk.Account{})
    handleError(err)

    wg.Add(1)
    err = wsClient.AddConfirmedAddedHandlers(owner.Address, func(transaction sdk.Transaction) bool {
       fmt.Println("Deposited!")
       wg.Done()

       return true
    })
    handleError(err)

    // Announce transaction
    _, err = client.Transaction.Announce(ctx, signedDownloadTx)
    if err != nil {
       fmt.Printf("Transaction.Announce returned error: %s", err)
       return
    }
}
```

### End file download transaction