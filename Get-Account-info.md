
### Get AccountInfo for an account

Returns AccountInfo for an account.

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "golang.org/x/net/context"
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

    address, _ := sdk.NewAddressFromPublicKey(publicKey, networkType)

    conf, err := sdk.NewConfig(baseUrl,networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Get AccountInfo for an account.
    // Param address - A Address struct.
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

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "golang.org/x/net/context"
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

    conf, err := sdk.NewConfig(baseUrl,networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    addressOne, _ := sdk.NewAddressFromPublicKey(publicKeyOne, networkType)

    addressTwo, _ := sdk.NewAddressFromPublicKey(publicKeyTwo, networkType)

    // Get AccountsInfo for different accounts.
    // Param adds - slice of Address.
    accountsInfo, err := client.Account.GetAccountsInfo(context.Background(), addressOne, addressTwo)
    if err != nil {
        fmt.Printf("Account.GetAccountsInfo returned error: %s", err)
        return
    }
    for _, accInfo := range accountsInfo {
        fmt.Printf("%s\n", accInfo.String())
    }
}
```

### Get confirmed transactions information

Gets an array of confirmed transaction for which an account is signer or recipient.

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "golang.org/x/net/context"
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

    account, _ := sdk.NewAccountFrompublicKey(publicKey, networkType)

    conf, err := sdk.NewConfig(baseUrl, networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Get confirmed transactions information.
    // Param account - A Account struct
    transactions, err := client.Account.Transactions(context.Background(), account)
    if err != nil {
        fmt.Printf("Account.Transactions returned error: %s", err)
        return
    }

    for _, txs := range transactions {
        fmt.Printf("%s\n", txs.String())
    }
}
```

### Get incoming transactions information

- Gets an array of transactions for which an account is the recipient.
A transaction is said to be incoming regarding an account if the account is the recipient of a transaction.
  - Param account - A PublicAccount struct

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "golang.org/x/net/context"
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


    account, _ := sdk.NewAccountFromPublicKey(publicKey, networkType)

    conf, err := sdk.NewConfig(baseUrl, networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Get confirmed transactions information.
    // Param account - A PublicAccount struct
    transactions, err := client.Account.IncomingTransactions(context.Background(), account)
    if err != nil {
        fmt.Printf("Account.IncomingTransactions returned error: %s", err)
        return
    }

    for _, txs := range transactions {
        fmt.Printf("%s\n", txs.String())
    }
}
```

### Get outgoing transactions information

- Gets an array of transactions for which an account is the sender.
A transaction is said to be outgoing egarding an account if the account is the sender of a transaction.
  - Param account - A PublicAccount struct

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "golang.org/x/net/context"
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

    account, _ := sdk.NewAccountFromPublicKey(publicKey, networkType)

    conf, err := sdk.NewConfig(baseUrl, networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Get confirmed transactions information.
    // Param account - A PublicAccount struct
    transactions, err := client.Account.OutgoingTransactions(context.Background(), account)
    if err != nil {
        fmt.Printf("Account.OutgoingTransactions returned error: %s", err)
        return
    }

    for _, txs := range transactions {
        fmt.Printf("%s\n", txs.String())
    }
}
```

### Get unconfirmed transactions information

- Gets the array of transactions for which an account is the sender or
receiver and which have not yet been included in a block.
  - Param account - A PublicAccount struct
  - Param opt - A AccountTransactionsOption struct

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "golang.org/x/net/context"
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

    account, _ := sdk.NewAccountFromPublicKey(publicKey, networkType)

    conf, err := sdk.NewConfig(baseUrl, networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Get confirmed transactions information.
    // Param account - A PublicAccount struct
    transactions, err := client.Account.UnconfirmedTransactions(context.Background(), account)
    if err != nil {
        fmt.Printf("Account.UnconfirmedTransactions returned error: %s", err)
        return
    }

    for _, txs := range transactions {
        fmt.Printf("%s\n", txs.String())
    }
}
```

### Get multisig account information.

  * Param address - Address struct.

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "golang.org/x/net/context"
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

    multisig, _ := sdk.NewAccountFromPublicKey(publicKey, networkType)

    conf, err := sdk.NewConfig(baseUrl, networkType)
    if err != nil {
        panic(err)
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Get confirmed transactions information.
    // Param account - A Address struct
    multisigAccountInfo, err := client.Account.GetMultisigAccountInfo(context.Background(), multisig.Address)
    if err != nil {
        fmt.Printf("Account.GetMultisigAccountInfo returned error: %s", err)
        return
    }
    fmt.Printf("%s\n", getMultisigAccountInfo.String() )
}
```
