**WebSockets make possible receiving notifications when a transaction or event occurs in the blockchain.
The notification is received in real time without having to poll the API waiting for a reply.**

### Subscribe block
The block channel notifies for every new block. The message contains the block information.
````go
package main

import (
	"fmt"
	"github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
)

const	baseUrl     = "http://localhost:3000"

func main() {

  // timeout in milliseconds
	// 60000 ms = 60 seconds
  // 0 = without timeout
  ws, err := sdk.NewConnectWs(baseUrl, 60000)
	if err != nil {
		panic(err)
	}

	fmt.Println("websocket negotiated uid:", ws.Uid)
	d, _ := ws.Subscribe.Block()

	for {
		data := <-d.Ch
		fmt.Printf("Block received with height: %v \n", data.Height)
	}
}
````
### Subscribe confirmedAdded
The confirmedAdded channel notifies when a transaction related to an address is included in a block. The message contains the transaction.
````go
const (
	baseUrl     = "http://localhost:3000"
	networkType = sdk.MijinTest
	privateKey  = "24CEC4F2DD1A28EBB2C0CF6D4D181BA2C0F1E215C42B9059EEFB65B1FAEE1B99"
)

acc, err := sdk.NewAccount(privateKey, networkType)
b, _ := ws.Subscribe.ConfirmedAdded(acc.Address.Address)
go func() {
	for {
		data := <-b.Ch
		fmt.Printf("ConfirmedAdded Tx Hash: %v \n", data.GetAbstractTransaction().Hash)
		//b.Unsubscribe()
		fmt.Println("Successful transfer!")
	}
}()
````
### Subscribe unconfirmedAdded
The UnconfirmedAdded channel notifies when a transaction related to an address is in unconfirmed state and waiting to be included in a block. The message contains the transaction.
````go
a, _ := ws.Subscribe.UnconfirmedAdded(acc.Address.Address)
go func() {
	for {
		data := <-a.Ch
		fmt.Printf("UnconfirmedAdded Tx Hash: %v \n", data.GetAbstractTransaction().Hash)
		//a.Unsubscribe()
	}
}()
````
### Subscribe partialAdded
The partialAdded channel notifies when an aggregate bonded transaction related to an address is in partial state and waiting to have all required cosigners. The message contains a transaction.
````go
j, _ := ws.Subscribe.PartialAdded(acc.Address.Address)
go func() {
	for {
		data := <-j.Ch
		fmt.Printf("PartialAdded Tx Hash: %v \n", data.GetAbstractTransaction().Hash)
		//a.Unsubscribe()
	}
}()
````
### Subscribe partialRemoved
The partialRemoved channel notifies when a transaction related to an address was in partial state but not anymore. The message contains the transaction hash.
````go
a, _ := ws.Subscribe.PartialRemoved(acc.Address.Address)
go func() {
	for {
		data := <-a.Ch
		fmt.Printf("PartialRemoved Tx Hash: %v \n", data.Meta.Hash)
		//a.Unsubscribe()
	}
}()
````
### Subscribe cosignature
The partialRemoved channel notifies when a cosignature signed transaction related to an address is added to an aggregate bonded transaction with partial state. The message contains the cosignature signed transaction.
````go
j, _ := ws.Subscribe.Cosignature(acc.Address.Address)
go func() {
	for {
		data := <-j.Ch
		fmt.Printf("Cosignature PublicKey: %v \n", data.Signer)
		fmt.Printf("Cosignature Tx Hash: %v \n", data.ParentHash)
		//a.Unsubscribe()
	}
}()
````
### Subscribe status
The status channel notifies when a transaction related to an address rises an error. The message contains the error message and the transaction hash.
````go
c, _ := ws.Subscribe.Status(acc.Address.Address)
 go func() {
	for {
		data := <-c.Ch
		//c.Unsubscribe()
		fmt.Printf("Status: %v \n", data.Status)
		fmt.Printf("Status: %v \n", data.Hash)
		//panic(fmt.Sprint("Status: ", data.Status))
	}
}()
````
