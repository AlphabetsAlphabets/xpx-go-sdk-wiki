###  [Basic functions.](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Examples%3A-Basic-Funtions)
 - Generates new Keypair.
 - Generates a Keypair from a private key.
 - Generate Account struct.
 - Generate address from a public key.
 - Generate address from a private key.
## Types of requests

### [Account gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Examples%3A-Get-Account-info)
 - Get AccountInfo for an account.
 - Get AccountsInfo for different accounts.
 - Get confirmed transactions information.
 - Get incoming transactions information.
 - Get outgoing transactions information.
 - Get unconfirmed transactions information.
 - Get aggregate bonded transactions information.
 - Get multisig account information.
 - Get multisig account graph information.

### [Transaction gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Examples%3A-Get-Transactions-info)
 - Get transaction information of transactionId or transactionHash.
 - Get transaction information for a given set of transactionId or transactionHash.
 - Get transaction status of transactionId or transactionHash.
 - Get an array of transaction statuses for a given set of transactionId or transactionHash.

### [Mosaic gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Examples%3A-Get-Mosaic-info)
 - Get mosaic information.
 - Get information for a set of mosaics.
 - Get readable names for a set of mosaics.
 - Get an array of MosaicInfo from mosaics created under provided namespace.

### [Namespace gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Examples%3A-Get-Mosaic-info)
 - Get namespace information.
 - Get namespaces an account owns.
 - Get readable names for a set of namespaces.
 - Get an array of NamespaceInfo for a given set of addresses.

### [BlockChain gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Examples%3A-Get-BlockChain-info)
 - Get BlockInfo for a given block height.
 - Get transactions from a block.
 - Get the current height of the chain.
 - Get the current score of the chain.
 - Get the storage information.
 - Get an array of BlockInfo for a given block height and limit.
### [Network gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Examples%3A-Get-Network-info)
 - Get the current network type of the chain.
## Announces a transaction
### [Transfer Transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transfer-Transaction)
 - Create a transfer transaction
### [Register namespace transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Register-namespace-transaction)
 - Create a root namespace
 - Create a sub namespace
### [Mosaic definition transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Mosaic-definition-transaction)
 - Create a mosaic definition transaction
### [Mosaic supply change transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Mosaic-supply-change-transaction)
 - Create a mosaic supply change transaction
### [Modify multisig account transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Modify-multisig-account-transaction)
 - Create a modify multisig account transaction
### [Aggregate complete transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Aggregate-complete-transaction)
 - Create a aggregate complete transaction
### [Aggregate bonded transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Aggregate-bonded-transactions)
 - Create a aggregate bonded transaction
### [Aggregate Cosignature transaction]()
 - Create a cosignature transaction
### [Lock funds transaction]()
 - Create a lock funds transaction
### [Secret lock transaction]()
 - Create a secret lock transaction
### [Secret proof transaction]()
 - Create a secret proof transaction
### [Websocket](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Websocket)
 - Subscribe a block channel
 - Subscribe a confirmedAdded channel
 - Subscribe a unconfirmedAdded channel
 - Subscribe a unconfirmedRemoved channel
 - Subscribe a partialAdded channel
 - Subscribe a partialRemoved channel
 - Subscribe a cosignature channel
 - Subscribe a status channel
