
### [Basic functions](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Basic-Functions)

- Generates new Keypair.
- Create an Address from a given public key
- Create an Account from a given private key

### [Account gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Get-Account-info)

- Get AccountInfo for an account.
- Get AccountInfos for multiple accounts.
- Get confirmed transactions information.
- Get incoming transactions information.
- Get outgoing transactions information.
- Get unconfirmed transactions information.
- Get multisig account information.

### [Transaction gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Get-Transactions-info)

- Get transaction information by transaction id or hash
- Get transaction informations for a given array of transaction ids or hashes
- Get transaction status for transaction hash or id
- Get transaction statuses for a given array of transaction hashes or ids

### [Mosaic gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Get-Mosaic-info)

- Get mosaic information.
- Get mosaic information for array of mosaics

### [Namespace gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Get-Namespace-info)

- Get namespace information.
- Get namespaces an account owns.
- Get readable names for a set of namespaces.
- Get namespace infos for a given array of addresses

### [Blockchain gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Get-BlockChain-info)

- Get the current height of the chain.
- Get BlockInfo for a given block height.
- Get an array of BlockInfo for a given block height and limit.
- Get transactions from a block.
- Get the current score of the chain.
- Get the storage information.

### [Network gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Get-Network-info)

- Get the current network type of the chain.

### [Metadata gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Get-Metadata-info)

- Get Metadata info for Address
- Get Metadata info for Mosaic
- Get Metadata info for Namespace

### [Account Properties gets](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Get-Account-properties-info)

- Get Account Properties

### [Transfer Transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Transfer)

 - Announce a transfer transaction

### [Register namespace transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Register-namespace)

 - Announce a root namespace
 - Announce a sub namespace

### [Mosaic definition transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Mosaic-definition)

 - Announce a mosaic definition transaction

### [Mosaic supply change transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Mosaic-supply-change)

 - Announce a mosaic supply change transaction

### [Modify multisig account transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Modify-multisig-account)

 - Announce a modify multisig account transaction

### [Aggregate complete transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Aggregate-complete)

 - Announce a aggregate complete transaction

### [Aggregate bonded transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Aggregate)

 - Announce a aggregate bonded transaction

### [Account properties transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Account-properties)

 - Announce a modify address account properties transaction
 - Announce a modify mosaic account properties transaction
 - Announce a modify entity account properties transaction

### [Mofify contract transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Modify-contract)

 - Announce a modify contract transaction

### [Modify metadata transaction](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Transaction-Modify-metadata)

 - Announce a modify account metadata transaction
 - Announce a modify mosaic metadata transaction
 - Announce a modify namespace metadata transaction

### [Websocket](https://github.com/proximax-storage/go-xpx-catapult-sdk/wiki/Websocket)

 - Subscribe a block
 - Subscribe a confirmedAdded
 - Subscribe a unconfirmedAdded
 - Subscribe a unconfirmedRemoved
 - Subscribe a partialAdded
 - Subscribe a partialRemoved
 - Subscribe a cosignature
 - Subscribe a status
