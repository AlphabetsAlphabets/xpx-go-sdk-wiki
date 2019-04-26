Transactions are actions taken on the blockchain that change its state.

Once a transaction is initiated, it is still unconfirmed and thus not yet accepted by the network. At this point, it is not clear if it will get included in a block. Never rely on a transaction which has the state unconfirmed.

### [Transfer Transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transfer-Transaction)
  * Transfer transaction is used to send assets between two accounts. It can hold a message of length 1024.
### [Register namespace transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Register-namespace-transaction)
  * Register namespace transaction is used to create and re-rental a namespace or subnamespace.
### [Mosaic definition transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Mosaic-definition-transaction)
  * Mosaic definition transaction is used to create a new mosaic.
### [Mosaic supply change transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Mosaic-supply-change-transaction)
  * Mosaic supply change transaction is used to assign supply to a mosaic.
### [Modify multisig account transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Modify-multisig-account-transaction)
  * Modify multisig account transaction is used to change properties of a multisig account.
#### Note: the connections to the server api are asynchronous and do not return the status of the transaction, to know the status you must use the websocket module of go-xpx-catapult-sdk.
