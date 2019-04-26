### Get namespace information.
  * Param - A Namespace identifier.
```go
package main

import (
	"encoding/json"
	"fmt"
	"github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
	"golang.org/x/net/context"
)

// Simple Namespace API request
func main() {

	conf, err := sdk.NewConfig("http://localhost:3000",sdk.MijinTest)
	if err != nil {
		panic(err)
	}

	// Use the default http client
	client := sdk.NewClient(nil, conf)

	// Generate Id from namespaceName
	namespaceId, _ := sdk.NewNamespaceIdFromName("proximax")

	getMosaicsFromNamespace, err := client.Namespace.GetNamespace(context.Background(), namespaceId)
	if err != nil {
		fmt.Printf("Namespace.GetNamespace returned error: %s", err)
		return
	}

	getNamespaceJson, _ := json.MarshalIndent(getMosaicsFromNamespace, "", " ")

	fmt.Printf("%s\n\n", string(getNamespaceJson))
}
```
### Get namespaces an account owns.
  * Param add - A Address struct.
  * Param dsid - Identifier of the namespace after which we want the transactions to be returned.
  * Param 0 - The number of namespaces to return.
```go
add, err := sdk.NewAddressFromRaw("SAUUKSFBYHI57KTEXQNJWHOKXITF7T4BXON3GVTJ")
if err != nil {
    panic(err)
}

getNamespacesFromAccount, err := client.Namespace.GetNamespacesFromAccount(context.Background(), add, nil, 0)
if err != nil {
	fmt.Printf("Namespace.GetNamespacesFromAccount returned error: %s", err)
	return
}
getNamespacesFromAccountJson, _ := json.MarshalIndent(getNamespacesFromAccount, "", " ")
fmt.Printf("%s\n\n", string(getNamespacesFromAccountJson))
```
### Get readable names for a set of namespaces.
```go
var NamespaceIDs []*sdk.NamespaceId
namespaceId, _ := sdk.NewNamespaceIdFromName("proximax")
NamespaceIDs = append(NamespaceIDs, namespaceId)

getNamespaceNames, err := client.Namespace.GetNamespaceNames(context.Background(), NamespaceIDs)
if err != nil {
	fmt.Printf("Namespace.GetNamespaceNames returned error: %s", err)
	return
}
getNamespaceNamesJson, _ := json.MarshalIndent(getNamespaceNames, "", " ")
fmt.Printf("%s\n\n", string(getNamespaceNamesJson))
```
### Get an array of NamespaceInfo for a given set of addresses.
  * Param adds - A Addresses type.
  * Param dsid - Identifier of the namespace after which we want the transactions to be returned.
  * Param 0 - The number of namespaces to return.
```go
var Addresses []*sdk.Address

aliceAddress, _ := sdk.NewAddressFromRaw("SCFWMP2M2HP43KJYGOBDVQ3SKX3Q6HFH6HZZ6DNR")

carolAddress, _ := sdk.NewAddressFromRaw("SAUUKSFBYHI57KTEXQNJWHOKXITF7T4BXON3GVTJ")

Addresses = append(Addresses, aliceAddress)

Addresses = append(Addresses, carolAddress)

getNamespacesFromAccounts, err := client.Namespace.GetNamespacesFromAccounts(context.Background(), adds, nil, 0)
if err != nil {
	fmt.Printf("Namespace.getNamespacesFromAccounts returned error: %s", err)
	return
}
getNamespacesFromAccountsJson, _ := json.MarshalIndent(getNamespacesFromAccounts, "", " ")
fmt.Printf("%s\n\n", string(getNamespacesFromAccountsJson))
```
