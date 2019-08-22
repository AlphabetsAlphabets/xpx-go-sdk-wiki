
### Get AccountInfo for an account

Returns AccountInfo for an account.

- Following parameters required:
  - **Address** - address to get info from

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
    // A valid public key.
    publicKey  = "E17324EAF403B5FD747055ED3ED97CFD1000AF176FB9294C9424A2814D765A76"
)

// Simple Account API request
func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    address, err := sdk.NewAddressFromPublicKey(publicKey, networkType)
    if err != nil {
        fmt.Printf("NewAddressFromPublicKey returned error: %s", err)
        return
    }

    // Get AccountInfo for an account.
    account, err := client.Account.GetAccountInfo(context.Background(), address)
    if err != nil {
        fmt.Printf("Account.GetAccountInfo returned error: %s", err)
        return
    }
    fmt.Printf(account.String())
}
```

### Get AccountInfo for multiple accounts

Returns AccountInfo for multiple accounts.

- Following parameters required:
  - **[]sdk.Address** - addresses to get info from

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
    // A valid public keys
    publicKeyOne = "E17324EAF403B5FD747055ED3ED97CFD1000AF176FB9294C9424A2814D765A76"
    publicKeyTwo = "280F42F3898B79EB1E304D14F873186AC7D2401A787092D7BD1D2D34C6B182F9"
)

// Simple Account API request
func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    addressOne, err := sdk.NewAddressFromPublicKey(publicKeyOne, networkType)
    if err != nil {
        fmt.Printf("NewAddressFromPublicKey returned error: %s", err)
        return
    }

    addressTwo, err := sdk.NewAddressFromPublicKey(publicKeyTwo, networkType)
    if err != nil {
        fmt.Printf("NewAddressFromPublicKey returned error: %s", err)
        return
    }

    // Get AccountsInfo for several accounts.
    accountsInfo, err := client.Account.GetAccountsInfo(context.Background(), addressOne, addressTwo)
    if err != nil {
        fmt.Printf("Account.GetAccountsInfo returned error: %s", err)
        return
    }
    for _, accountInfo := range accountsInfo {
        fmt.Printf("%s\n", accountInfo.String())
    }
}
```

### Get confirmed transactions information

Gets an array of confirmed transaction for which an account is signer or recipient.
- Following parameters required:
  - **Public Account** - account to get transactions associated with

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
    // A valid public key
    publicKey = "E17324EAF403B5FD747055ED3ED97CFD1000AF176FB9294C9424A2814D765A76"
)

func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    account, err := sdk.NewAccountFromPublicKey(publicKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
        return
    }

    // Get confirmed transactions information.
    transactions, err := client.Account.Transactions(context.Background(), account)
    if err != nil {
        fmt.Printf("Account.Transactions returned error: %s", err)
        return
    }

    for _, transaction := range transactions {
        fmt.Printf("%s\n", transaction.String())
    }
}
```

### Get incoming transactions information

- Gets an array of transactions for which an account is the recipient.
A transaction is said to be incoming regarding an account if the account is the recipient of a transaction.

- Following parameters required:
  - **Public Account** - account to get incoming transactions associated with

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
    // A valid public key
    publicKey = "E17324EAF403B5FD747055ED3ED97CFD1000AF176FB9294C9424A2814D765A76"
)

func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    account, err := sdk.NewAccountFromPublicKey(publicKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
        return
    }

    // Get confirmed transactions information.
    transactions, err := client.Account.IncomingTransactions(context.Background(), account, nil)
    if err != nil {
        fmt.Printf("Account.IncomingTransactions returned error: %s", err)
        return
    }

    for _, transaction := range transactions {
        fmt.Printf("%s\n", transaction.String())
    }
}
```

### Get outgoing transactions information

- Gets an array of transactions for which an account is the sender.
A transaction is said to be outgoing egarding an account if the account is the sender of a transaction.

- Following parameters required:
  - **Public Account** - account to get outgoing transactions associated with

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
    // A valid public key.
    publicKey = "E17324EAF403B5FD747055ED3ED97CFD1000AF176FB9294C9424A2814D765A76"
)

func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    account, err := sdk.NewAccountFromPublicKey(publicKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
        return
    }

    // Get confirmed transactions information.
    transactions, err := client.Account.OutgoingTransactions(context.Background(), account)
    if err != nil {
        fmt.Printf("Account.OutgoingTransactions returned error: %s", err)
        return
    }

    for _, transaction := range transactions {
        fmt.Printf("%s\n", transaction .String())
    }
}
```

### Get unconfirmed transactions information

- Gets the array of transactions for which an account is the sender or
receiver and which have not yet been included in a block.

- Following parameters required:
  - **Public Account** - account to get unconfirmed transactions associated with

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
    // A valid private key.
    publicKey = "E17324EAF403B5FD747055ED3ED97CFD1000AF176FB9294C9424A2814D765A76"
)

func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    account, err := sdk.NewAccountFromPublicKey(publicKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
        return
    }

    // Get confirmed transactions information.
    // Param account - A PublicAccount struct
    transactions, err := client.Account.UnconfirmedTransactions(context.Background(), account)
    if err != nil {
        fmt.Printf("Account.UnconfirmedTransactions returned error: %s", err)
        return
    }

    for _, transaction := range transactions {
        fmt.Printf("%s\n", transaction.String())
    }
}
```

### Get multisig account information.

- Following parameters required:
  - **Address** - address of mulstisig account to get information about

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
    // A valid public key.
    multisigPublicKey = "E17324EAF403B5FD747055ED3ED97CFD1000AF176FB9294C9424A2814D765A76"
)

func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    multisig, err := sdk.NewAccountFromPublicKey(publicKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
        return
    }

    // Get confirmed transactions information.
    multisigAccountInfo, err := client.Account.GetMultisigAccountInfo(context.Background(), multisig.Address)
    if err != nil {
        fmt.Printf("Account.GetMultisigAccountInfo returned error: %s", err)
        return
    }
    fmt.Printf("%s\n", multisigAccountInfo.String() )
}
```

