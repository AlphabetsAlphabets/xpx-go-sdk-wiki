
Transactions are actions taken on the blockchain that change its state.

Once a transaction is initiated, it's still unconfirmed and thus
not yet accepted by the network. At this point, it's not clear
if it will get included in a block. Never rely on a transaction
which has the state unconfirmed.

### [Transfer Transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Transfer)

* Transfer transaction is used to send assets between two accounts. It can hold a message of length 1024.

### [Register namespace transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Register-namespace)

* Register namespace transaction is used to create and re-rental a namespace or subnamespace.

### [Mosaic definition transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Mosaic-definition)

* Mosaic definition transaction is used to create a new mosaic.

### [Mosaic supply change transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Mosaic-supply-change)

* Mosaic supply change transaction is used to assign supply to a mosaic.

### [Modify multisig account transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Modify-multisig-account)

* Modify multisig account transaction is used to change properties of a multisig account.

### [Aggregate complete transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Aggregate-complete)

* Aggregate complete transaction is used to execute multiple transactions from single account at the same time.

### [Aggregate bounded transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Aggregate-bounded)

* Aggregate bounded transaction is used to execute multiple transactions from multiple account at the same time.

### [Account properties transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Account-properties)

* Account properties transaction is used to update properties of account.

### [Modify metadata transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Modify-metadata)

* Modify metadata transaction is used to update metadata for Account, Namespace or Mosaic.

### [Alias transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Alias)

* Alias transaction is used to link address or mosaic to namespace.

### [Account link transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Account-link)

* Account link is used to delegate own account's importance.

#### Note: the connections to the server api are asynchronous and do not return the status of the transaction, to know the status you must use the websocket module of go-xpx-catapult-sdk.
