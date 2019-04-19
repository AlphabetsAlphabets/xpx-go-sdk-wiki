### Get transaction information of transactionId or transactionHash.
  * Param transactionId or transactionHash.
  * Example transactionId: "5BB4D6E2415CD84B7971BBAA"
  * Example transactionHash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"
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
	networkType = sdk.MijinTest
)

// Simple Transaction API request
func main() {

	conf, err := sdk.NewConfig(baseUrl,networkType)
	if err != nil {
		panic(err)
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Get transaction information of transactionId or transactionHash.
	// Param Hash - transactionId or transactionHash.
	// Example transactionId: "5BB4D6E2415CD84B7971BBAA"
	// Example transactionHash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"
	getTransaction, err := client.Transaction.GetTransaction(context.Background(), "5BB4D6E2415CD84B7971BBAA")
	if err != nil {
		fmt.Printf("Transaction.GetTransaction returned error: %s", err)
		return
	}
	fmt.Printf("%s\n\n", getTransaction.String())
}
```
### Get transaction information for a given set of transactionId or transactionHash.
  * Param transactionId or transactionHash.
  * Example transactionId: "5BB4D6E2415CD84B7971BBAA"
  * Example transactionHash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"
```go
var TxHash []string

// Append attachment into transactionHash
TxHash = append(TxHash, "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6")
// Append attachment into transactionHash
TxHash = append(TxHash, "463FDEC912FC4BA3D84FEC31E8293FAE6D142FC271A71E464FDA563F056A6151")

getTransactions, err := client.Transaction.GetTransactions(context.Background(), TxHash)
if err != nil {
	fmt.Printf("Transaction.GetTransactions returned error: %s", err)
	return
}
for _, txInfo := range getTransactions {
	fmt.Printf("%s\n\n", txInfo.String())
}
```
### Get transaction status of transactionHash.
  * Param transactionHash.
  * Example transactionHash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"
```go
getTransactionStatus, err := client.Transaction.GetTransactionStatus(context.Background(), "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6")
if err != nil {
	fmt.Printf("Transaction.GetTransactionStatus returned error: %s", err)
	return
}
fmt.Printf("%s\n\n", getTransactionStatus.String())
```
### Get an array of transaction statuses for a given set of transactionHash.
  * Param transactionHash.
  * Example transactionHash: "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6"
```go
var TxHash []string

// Append attachment into transactionHash
TxHash = append(TxHash, "C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6")
// Append attachment into transactionHash
TxHash = append(TxHash, "463FDEC912FC4BA3D84FEC31E8293FAE6D142FC271A71E464FDA563F056A6151")

getTransactionStatuses, err := client.Transaction.GetTransactionStatuses(context.Background(), TxHash)
if err != nil {
	fmt.Printf("Transaction.GetTransactionStatuses returned error: %s", err)
	return
}
for _, txStatus := range getTransactionStatuses {
	fmt.Printf("%s\n\n", txStatus.String())
}
```
