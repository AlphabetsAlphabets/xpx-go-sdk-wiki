### Register namespace transaction
Accounts can rent a namespace for an amount of blocks and after a this renew the contract.
This is done via a **NewRegisterRootNamespaceTransaction()** and **NewRegisterSubNamespaceTransaction()**.
- **Create a root namespace**
  * Following parameters are required:
     * **deadline** - The deadline to include the transaction.
     * **namespaceName** - The namespace name.
     * **duration** - The duration of the namespace.
     * **networkType** - The network type.

```go
// Generate Id from namespaceName
namespaceName := "newnamespace"

rootNamespace, err := sdk.NewRegisterRootNamespaceTransaction(
	sdk.NewDeadline(time.Hour*1),
	namespaceName,
	big.NewInt(10000),
	networkType,
)

// Sign transaction
stx, err := acc.Sign(rootNamespace)
if err != nil {
	panic(fmt.Errorf("RegisterRootNamespaceTransaction signing returned error: %s", err))
}
// Announce transaction
restTx, err := client.Transaction.Announce(context.Background(), stx)
if err != nil {
	panic(err)
}
fmt.Printf("%s\n", restTx)
fmt.Printf("Hash: \t\t%v\n", stx.Hash)
fmt.Printf("Signer: \t%X\n", acc.KeyPair.PublicKey.Raw)
```

- **Create a sub namespace**
  * Following parameters are required:
     * **deadline** - The deadline to include the transaction.
     * **namespaceName** - The namespace name.
     * **parentNamespace** - The parent namespace name.
     * **networkType** - The network type.

```go
parentNamespace, _ := sdk.NewNamespaceIdFromName("newnamespace")

namespace := "subnamespace"

SubNamespace, err := sdk.NewRegisterSubNamespaceTransaction(
		sdk.NewDeadline(time.Hour*1),
		namespace,
		parentNamespace,
		networkType,
	)

// Sign transaction
stx, err := acc.Sign(SubNamespace)
if err != nil {
	panic(fmt.Errorf("NewRegisterSubNamespaceTransaction signing returned error: %s", err))
}

// Announce transaction
restTx, err := client.Transaction.Announce(context.Background(), stx)
if err != nil {
	panic(err)
}
fmt.Printf("%s\n", restTx)
fmt.Printf("Hash: \t\t%v\n", stx.Hash)
fmt.Printf("Signer: \t%X\n", acc.KeyPair.PublicKey.Raw)
```
