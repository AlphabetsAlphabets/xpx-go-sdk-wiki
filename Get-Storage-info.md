### Get Drive By Public Account
Get drive is used to get some drive. For getting use **Storage.GetDrive()**

Following parameters is required:
 - **Context** - context
 - **PublicAccount** - the public account of some drive

```go
package main

import (
	"context"
	"fmt"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"
)

func main()  {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Some drive account (create or get)
	driveAccount := &sdk.Account{}

    //Get drive
	drive, err := client.Storage.GetDrive(context.Background(), driveAccount.PublicAccount)
	handleError(err)

	println(drive.String())
}
```

### Get Drives By An Account Public Account
Get account drives is used to get drives of some account. For getting use **Storage.GetAccountDrives()**

Following parameters is required:
 - **Context** - context
 - **DrivePublicAccount** - the public account of some drive
  
Third parameter can get next values:
- *AllRoles* - all drives on which the account is the participant
- *Owner* - all drives on which the account is the owner
- *Replicator* - all drives on which the account is the replicator

```go
package main

import (
	"context"
	"fmt"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"
)

func main()  {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Some drive account (create or get
	owner := &sdk.Account{}

    //Get owner drives
	drives, err := client.Storage.GetAccountDrives(context.Background(), owner.PublicAccount, sdk.AllRoles)
	handleError(err)

	for _, d := range drives {
		println(d.String())
	}
}
```

### Get Downloads Info By Recipient
Get account download infos is used to get download info of some account (recipient). For getting use **Storage.GetAccountDownloadInfos()**

Following parameters is required:
 - **Context** - context
 - **PublicAccount** - the public account of a recipient

```go
package main

import (
	"context"
	"fmt"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"
)

func main()  {
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

    //Create new config
	conf, err := sdk.NewConfig(ctx, []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	//Some recipient (see download example)
	recipient, err := client.NewAccount()
	handleError(err)

    //Get download infos
	downloadInfos, err := client.Storage.GetAccountDownloadInfos(ctx, recipient.PublicAccount)
	handleError(err)

    //Print download infos
	for _, d := range downloadInfos {
		fmt.Println(d)
	}
}
```

### Get Download Info By OperationToken (Hash)
Get download info is used to get info about some download transaction. For getting use **Storage.GetDownloadInfo()**

Following parameters is required:
 - **Context** - context
 - **OperationToken** - the hash of download transaction

```go
package main

import (
	"context"
	"fmt"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"
)

func main()  {
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	//Create new config
	conf, err := sdk.NewConfig(ctx, []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	//Some hash of a transaction
	operationToken := &sdk.Hash{}
    
    //Get download info
	downloadInfo, err := client.Storage.GetDownloadInfo(ctx, operationToken)
	handleError(err)
	
	fmt.Println(downloadInfo)
}
```

### Get Download Info By Drive
Get drive download infos is used to get drive download info. For getting use **Storage.GetDriveDownloadInfos()**

Following parameters is required:
 - **Context** - context
 - **PublicAccount** - the public account of some drive account

```go
package main

import (
	"context"
	"fmt"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"
)

func main()  {
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	//Create new config
	conf, err := sdk.NewConfig(ctx, []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Some drive account (create or get)
	driveAccount := &sdk.Account{}

	//Get download infos
	downloadInfos, err := client.Storage.GetDriveDownloadInfos(ctx, driveAccount.PublicAccount)
	handleError(err)

	//Print download infos
	for _, d := range downloadInfos {
		fmt.Println(d)
	}
}
```

### Get Status of Download By Drive
Get verification status is used to get status of download transaction. For getting use **Storage.GetVerificationStatus()**

Following parameters is required:
 - **Context** - context
 - **PublicAccount** - the public account of some drive account

```go
package main

import (
	"context"
	"fmt"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"
)

func main()  {
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	//Create new config
	conf, err := sdk.NewConfig(ctx, []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Some drive account (create or get)
	driveAccount := &sdk.Account{}

    //Get status
	status, err := client.Storage.GetVerificationStatus(ctx, driveAccount.PublicAccount)
	handleError(err)

	//Print download infos
	fmt.Println(status)
}
```