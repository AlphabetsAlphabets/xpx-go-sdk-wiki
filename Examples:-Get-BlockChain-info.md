### Get BlockInfo for a given block height.
  * Param height - Block height
```go
package main

import (
	"encoding/json"
	"fmt"
	"github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
	"golang.org/x/net/context"
	"math/big"
)

// Simple Blockchain API request
func main() {

	conf, err := sdk.NewConfig("http://localhost:3000",sdk.MijinTest)
	if err != nil {
		panic(err)
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	height := big.NewInt(1)

	getBlockByHeight, err := client.Blockchain.GetBlockByHeight(context.Background(), height)
	if err != nil {
		fmt.Printf("Blockchain.GetBlockByHeight returned error: %s", err)
		return
	}
	getBlockByHeightJson, _ := json.MarshalIndent(getBlockByHeight, "", " ")
	fmt.Printf("%s\n\n", string(getBlockByHeightJson))
}
```
### Get transactions from a block.
  * Param height - Block height
```go
height := big.NewInt(1)

getBlockTransactions, err := client.Blockchain.GetBlockTransactions(context.Background(), height)
if err != nil {
	fmt.Printf("Blockchain.GetBlockTransactions returned error: %s", err)
	return
}
fmt.Printf("%s\n\n", getBlockTransactions)
```
### Get the current height of the chain.
```go
getBlockchainHeight, err := client.Blockchain.GetBlockchainHeight(context.Background())
if err != nil {
	fmt.Printf("Blockchain.GetBlockchainHeight returned error: %s", err)
	return
}
fmt.Printf("%s\n\n", getBlockchainHeight)
```
### Get the current score of the chain.
```go
getBlockchainScore, err := client.Blockchain.GetBlockchainScore(context.Background())
if err != nil {
	fmt.Printf("Blockchain.GetBlockchainScore returned error: %s", err)
	return
}
fmt.Printf("%s\n\n", getBlockchainScore)
```
### Get an array of BlockInfo for a given block height and limit.
  * Param height - Block height
  * Param limit - Number of following blocks to be returned.
```go
height := big.NewInt(1)
limit :=  big.NewInt(100)

getBlockchainInfo, err := client.Blockchain.GetBlocksByHeightWithLimit(context.Background(), height, limit)
if err != nil {
	fmt.Printf("Blockchain.GetBlockchainInfo returned error: %s", err)
	return
}
fmt.Printf("%s\n\n", getBlockchainInfo)
```
### Get the storage information.
```go
getBlockchainStorage, err := client.Blockchain.GetBlockchainStorage(context.Background())
if err != nil {
	fmt.Printf("Blockchain.GetBlockchainStorage returned error: %s", err)
	return
}
getBlockchainStorageJson, _ := json.MarshalIndent(getBlockchainStorage, "", " ")
fmt.Printf("%s\n\n", getBlockchainStorageJson)
}
```
