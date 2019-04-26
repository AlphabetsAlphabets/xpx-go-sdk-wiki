### Get the current network type of the chain.
```go
package main

import (
	"fmt"
	"github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
	"golang.org/x/net/context"
)

// Simple Network API request
func main() {

	conf, err := sdk.NewConfig("http://localhost:3000",sdk.MijinTest)
	if err != nil {
		panic(err)
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	getNetworkType, err := client.Network.GetNetworkType(context.Background())
	if err != nil {
		fmt.Printf("Network.GetNetworkType returned error: %s", err)
		return
	}
	fmt.Printf("%s\n\n", getNetworkType)
}
```
