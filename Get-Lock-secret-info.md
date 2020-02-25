### Get Lock Secrets By Account
Returns `[]*SecretLockInfo` of an account. For getting use `Lock.GetSecretLockInfosByAccount()`

Following parameters required:
 - **PublicAccount** - PublicAccount of some account

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
	// Private key of some exist account. Change it
	privateKey = "809CD6699B7F38063E28F606BD3A8AECA6E13B1E688FE8E733D13DB843BC14B7"
)

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)
    
    //Get some account
    account, err := client.NewAccountFromPrivateKey(privateKey)
	if err != nil {
		fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
		return
	}

    //get secretLockInfos by account
    infosByAccount, err := client.Lock.GetSecretLockInfosByAccount(context.Background(), account.PublicAccount)
	for _, v := range infosByAccount {
		fmt.Println(v.CompositeHash)
	}
}
```

### Get Lock Secrets By Secret
Returns `[]*SecretLockInfo` of an account. For getting use `Lock.GetSecretLockInfosBySecret()`

Following parameters required:
 - **Secret** - hash of some secret

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
	// Private key of some exist account. Change it
	privateKey = "809CD6699B7F38063E28F606BD3A8AECA6E13B1E688FE8E733D13DB843BC14B7"
)

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)
    
    //create some proof
	proofB := make([]byte, 8)
	_, err = rand.Read(proofB)
	if err != nil {
		fmt.Printf("rand.Read returned error: %s", err)
		return
	}

	proof := sdk.NewProofFromBytes(proofB)
	secret, _ := proof.Secret(sdk.SHA3_256)
	infosBySecret, err := client.Lock.GetSecretLockInfosBySecret(context.Background(), &secret.Hash)
	for _, v := range infosBySecret {
		fmt.Println(v)
	}
}
```

### Get Lock Secret By CompositeHash
Returns `*SecretLockInfo` of an account. For getting use `Lock.GetSecretLockInfo()`

Following parameters required:
 - **Hash** - some compositeHash

```go
package main

import (
	"context"
	"encoding/hex"
	"fmt"
	"github.com/proximax-storage/go-xpx-chain-sdk/sdk"
)

const (
	// Catapult-api-rest server.
	baseUrl = "http://localhost:3000"
	// Private key of some exist account. Change it
	privateKey = "809CD6699B7F38063E28F606BD3A8AECA6E13B1E688FE8E733D13DB843BC14B7"
)

func main() {
	conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
	if err != nil {
		fmt.Printf("NewConfig returned error: %s", err)
		return
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)
    
    //Some hex compositeHash
	bytes, err := hex.DecodeString("11a4cc303e91ae150c6c487ec048a2a07298042427094f2ea6701c25aa565b6c")
	compositeHash := &sdk.Hash{}
	copy(compositeHash[:], bytes)
	
	infosByCompositeHash, err := client.Lock.GetSecretLockInfo(context.Background(), compositeHash)
	fmt.Println(infosByCompositeHash)
}
```