### Generates new Keypair.
```go
package main

import (
	"fmt"
	"github.com/proximax-storage/nem2-crypto-go"
)

func main() {

	KeyPair, _ := crypto.NewRandomKeyPair()

	fmt.Printf("PublicKey:\t%x\n",KeyPair.PublicKey.Raw)
	fmt.Printf("PrivateKey:\t%x",KeyPair.PrivateKey.Raw)
}

```
### Create an Address from a given public key.
* The Address structure describes an public Address, NetworkType.
* first param - A a public key in hex.
* second param - A NetworkType:
  * MainNet = Main net network.
  * TestNet = Test net network.
  * Mijin = Mijin net network.
  * MijinTest= Mijin test net network.
* Return - A Account struct
```go
package main

import (
	"fmt"
	"github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
)

func main() {
	pk := "04dd376196603c44a19fd500492e5de12de9ed353de070a788cb21f210645613"

	Address, _ := sdk.NewAddressFromPublicKey(pk, sdk.MijinTest)

	fmt.Printf("Address:\t\t%v\n",Address.Address)
	fmt.Printf("NetworkType:\t%v",Address.Type)
}
```
### Create an Account from a given private key.
* The Account structure describes an account private key, public key and address.
* first param - A a private key in hex.
* second param - A NetworkType:
  * MainNet = Main net network.
  * TestNet = Test net network.
  * Mijin = Mijin net network.
  * MijinTest= Mijin test net network.
* Return - A Account struct
```go
package main

import (
	"fmt"
	"github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
)

const alicePrivateKey = "04dd376196603c44a19fd500492e5de12de9ed353de070a788cb21f210645613"

func main() {
	aliceAccount, _ := sdk.NewAccountFromPrivateKey(alicePrivateKey, sdk.MijinTest)

	fmt.Printf("Address:\t%v\n", aliceAccount.Address)
	fmt.Printf("PrivateKey:\t%x\n", aliceAccount.KeyPair.PrivateKey.Raw)
	fmt.Printf("PublicKey:\t%x", aliceAccount.KeyPair.PublicKey.Raw)
}
```
