### Get AccountInfo for an account.
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
	// MainNet:   104
	// TestNet:   152
	// Mijin:     96
	// MijinTest: 144
	networkType = sdk.MijinTest

	// A valid private key.
	publicKeyOne  = "E17324EAF403B5FD747055ED3ED97CFD1000AF176FB9294C9424A2814D765A76"
)

// Simple Account API request
func main() {

	addressOne, _ := sdk.NewAddressFromPublicKey(publicKeyOne, networkType)

	conf, err := sdk.NewConfig(baseUrl,networkType)
	if err != nil {
		panic(err)
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Get AccountInfo for an account.
	// Param address - A Address struct.
	accountInfo, err := client.Account.GetAccountInfo(context.Background(), addressOne)
	if err != nil {
		fmt.Printf("Account.GetAccountInfo returned error: %s", err)
		return
	}
	fmt.Printf(accountInfo.String())
}
```
### Get AccountsInfo for different accounts.
Returns AccountsInfo for different accounts.

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
	// MainNet:   104
	// TestNet:   152
	// Mijin:     96
	// MijinTest: 144
	networkType = sdk.MijinTest

	// A valid private key.
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

	var adds []*sdk.Address

	// Append attachment into Address
	adds = append(adds, addressOne)
	// Append attachment into Address
	adds = append(adds, addressTwo)

	// Get AccountsInfo for different accounts.
	// Param adds - slice of Address.
	accountsInfo, err := client.Account.GetAccountsInfo(context.Background(), adds)
	if err != nil {
		fmt.Printf("Account.GetAccountsInfo returned error: %s", err)
		return
	}
	for _, accInfo := range accountsInfo {
		fmt.Printf("%s\n", accInfo.String())
		fmt.Println("---------------------------")
	}
}
```
### Get confirmed transactions information.
Gets an array of confirmed transaction for which an account is signer or recipient.
```go
accountOne, _ := sdk.NewAccountFromPrivateKey(publicKeyOne, networkType)

conf, err := sdk.NewConfig(baseUrl,networkType)
if err != nil {
	panic(err)
}

// Use the default http client
client := sdk.NewClient(nil, conf)

opt := sdk.AccountTransactionsOption{
	// The number of transactions to return. Should be between 10 and 100, otherwise 10.
	PageSize: 10,
	// Identifier of the transaction after which we want the transactions to be returned. Eje: "5B2B0F61415CD864572BDA46".
	Id:       "",
}

// Get confirmed transactions information.
// Param accountOne - A PublicAccount struct
// Param opt - A AccountTransactionsOption struct
transactions, err := client.Account.Transactions(context.Background(), accountOne, &opt )
if err != nil {
	fmt.Printf("Account.Transactions returned error: %s", err)
	return
}

for _, txs := range transactions {
	fmt.Printf("%s\n\n", txs.String())
}
```
### Get incoming transactions information.
- Gets an array of transactions for which an account is the recipient.
A transaction is said to be incoming regarding an account if the account is the recipient of a transaction.
   * Param accountOne - A PublicAccount struct
   * Param opt - A AccountTransactionsOption struct
```go
incomingTransactions, err := client.Account.IncomingTransactions(context.Background(), accountOne, &opt )
if err != nil {
    fmt.Printf("Account.IncomingTransactions returned error: %s", err)
    return
}
for _, t := range incomingTransactions {
    fmt.Printf("%s\n\n", t.String())
}
```
### Get outgoing transactions information.
- Gets an array of transactions for which an account is the sender.
A transaction is said to be outgoing egarding an account if the account is the sender of a transaction.
   * Param accountOne - A PublicAccount struct
   * Param opt - A AccountTransactionsOption struct
```go
outgoingTransactions, err := client.Account.OutgoingTransactions(context.Background(), accountOne, &opt )
if err != nil {
    fmt.Printf("Account.OutgoingTransactions returned error: %s", err)
	return
}
for _, t := range outgoingTransactions {
    fmt.Printf("%s\n\n", t.String())
}
```
### Get unconfirmed transactions information.
- Gets the array of transactions for which an account is the sender or
receiver and which have not yet been included in a block.
  * Param accountOne - A PublicAccount struct
  * Param opt - A AccountTransactionsOption struct
```go
unconfirmedTransactions, err := client.Account.UnconfirmedTransactions(context.Background(), accountOne, &opt )
if err != nil {
    fmt.Printf("Account.unconfirmedTransactions returned error: %s", err)
	return
}
for _, t := range unconfirmedTransactions {
    fmt.Printf("%s\n\n", t.String())
}
```
### Get multisig account information.
  * Param addressOne - Address struct.
```go
getMultisigAccountInfo, err := client.Account.GetMultisigAccountInfo(context.Background(), addressOne)
if err != nil {
	fmt.Printf("Account.GetMultisigAccountInfo returned error: %s", err)
	return
}
fmt.Printf("%s\n\n", getMultisigAccountInfo.String() )
```
