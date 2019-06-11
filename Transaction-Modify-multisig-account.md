
### Modify multisig account transaction

Modify multisig account transaction is used to create multisig
account or edit it's properties. This is done via a
**NewModifyMultisigAccountTransaction()**.

- Following parameters are required:
  - **Minimum Approval Delta** - The number of signatures needed to approve a transaction.
  - **Minimum Removal Delta** - The number of signatures needed to remove a cosignatory.
  - **Modifications** - Array of cosigner accounts added or removed from the
    multisignature account. Multsig cosignatory modification type. Supported types are:
      - `sdk.Add`: Add cosignatory.
      - `sdk.Remove`: Remove cosignatory.

#### How to create multisig account

```go
package main

import (
    "context"
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "time"
)

const (
    // Types of network.
    networkType = sdk.MijinTest
    // Future multisig private key
    multisigPrivateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
    // Cosignature public keys
    cosignatoryOnePublicKey = "16DBE8DC29CF06338133DEE64FC49B461CE489BF9588BE3B9670B5CB19C89369"
    cosignatoryTwoPublicKey = "461CE489BF9588BE3B9670B5CB19C8936916DBE8DC29CF06338133DEE64FC49B"
    cosignatoryThreePublicKey = "29CF06338133DEE64FC49BCB19C8936916DBE8DC461CE489BF9588BE3B9670B5"
    // Minimal approval count
    minimalApproval = 3
    // Minimal removal count
    minimalRemoval = 2
)

func main() {

    conf, err := sdk.NewConfig(baseUrl, networkType, time.Second * 10)
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Create an account from a private key
    multisig, err := sdk.NewAccountFromPrivateKey(multisigPrivateKey , networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    cosignerOne, err := sdk.NewAccountFromPublicKey(cosignatoryOnePublicKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
        return
    }
    cosignerTwo, err := sdk.NewAccountFromPublicKey(cosignatoryTwoPublicKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
        return
    }
    cosignerThree, err := sdk.NewAccountFromPublicKey(cosignatoryThreePublicKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
        return
    }

    transaction, err := sdk.NewModifyMultisigAccountTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour * 1),
        // The number of signatures needed to approve a transaction.
        minimalApproval,
        // The number of signatures needed to remove a cosignatory.
        minimalRemoval,
        // Array of cosigner accounts added or removed from the multisignature account.
        []*sdk.MultisigCosignatoryModification{
            {sdk.Add, cosignerOne,},
            {sdk.Add, cosignerTwo,},
            {sdk.Add, cosignerThree,},
        },
        networkType,
    )
    if err != nil {
        fmt.Printf("NewModifyMultisigAccountTransaction returned error: %s", err)
        return
    }

    // Sign transaction
    signedTransaction, err := multisig.Sign(transaction)
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    // Announce transaction
    _, err := client.Transaction.Announce(context.Background(), signedTransaction)
    if err != nil {
        fmt.Printf("Transaction.Announce returned error: %s", err)
        return
    }
}
```

#### How to modify existing multisig account

- Following parameters are required:
  - **Minimum Approval Delta** - The number of signatures needed to approve a transaction.
  - **Minimum Removal Delta** - The number of signatures needed to remove a cosignatory.
  - **Modifications** - Array of cosigner accounts added or removed from the
    multisignature account. Multsig cosignatory modification type. Supported types are:
      - `sdk.Add`: Add cosignatory.
      - `sdk.Remove`: Remove cosignatory.

```go
package main

import (
    "context"
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "time"
)

const (
    // Types of network.
    networkType = sdk.MijinTest
    // Existing multisig private key
    multisigPrivateKey = "3B9670B5CB19C893694FC49B461CE489BF9588BE16DBE8DC29CF06338133DEE6"
    // Cosignature public keys
    cosignatoryOnePrivateKey = "16DBE8DC29CF06338133DEE64FC49B461CE489BF9588BE3B9670B5CB19C89369"
    cosignatoryTwoPrivateKey = "461CE489BF9588BE3B9670B5CB19C8936916DBE8DC29CF06338133DEE64FC49B"
    cosignatoryToRemovePrivateKey  = "29CF06338133DEE64FC49BCB19C8936916DBE8DC461CE489BF9588BE3B9670B5"
    // Minimal approval count
    minimalApproval = -1
    // Minimal removal count
    minimalRemoval = -1
)

func main() {

    conf, err := sdk.NewConfig(baseUrl, networkType, time.Second * 10)
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    // Create an account from a private key
    multisig, err := sdk.NewAccountFromPrivateKey(multisigPrivateKey , networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    cosignerOne, err := sdk.NewAccountFromPrivateKey(cosignatoryOnePrivateKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }
    cosignerTwo, err := sdk.NewAccountFromPrivateKey(cosignatoryTwoPrivateKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }
    cosignerToRemove, err := sdk.NewAccountFromPrivateKey(cosignatoryToRemovePublicKey , networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPrivateKey returned error: %s", err)
        return
    }

    transaction, err := sdk.NewModifyMultisigAccountTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour * 1),
        // The number of signatures needed to approve a transaction.
        minimalApproval,
        // The number of signatures needed to remove a cosignatory.
        minimalRemoval,
        // Array of cosigner accounts added or removed from the multisignature account.
        []*sdk.MultisigCosignatoryModification{
            {sdk.Remove, cosignerToRemove,},
        },
        networkType,
    )
    if err != nil {
        fmt.Printf("NewModifyMultisigAccountTransaction returned error: %s", err)
        return
    }
    // Make modify multisig transaction part of aggregate bounded
    transaction.ToAggregate(multisig.PublicAccount)

    // Create actual aggregate bounded transaction
    aggregateBoundedTransaction, err := sdk.NewBondedAggregateTransaction(
        sdk.NewDeadline(time.Hour * 1),
        []sdk.Transaction{transaction},
        networkType
    )
    if err != nil {
        fmt.Printf("NewBondedAggregateTransaction returned error: %s", err)
        return
    }

    // Sign it from multisig
    signedAggregateBoundedTransaction, err := multisig.Sign(aggregateBoundedTransaction)
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    // Create lock funds transaction for aggregate bounded
    lockFundsTrx, err := sdk.NewLockFundsTransaction(
        // The maximum amount of time to include the transaction in the blockchain.
        sdk.NewDeadline(time.Hour * 1),
        // Funds to lock
        sdk.XpxRelative(10),
        // Duration of lock transaction in blocks
        big.NewInt(1000),
        // Aggregate bounded transaction for lock
        signedAggregateBoundedTransaction,
        networkType
    )
    if err != nil {
        fmt.Printf("NewLockFundsTransaction returned error: %s", err)
        return
    }

    // Sign transaction
    signedTransaction, err := multisig.Sign(lockFundsTrx)
    if err != nil {
        fmt.Printf("Sign returned error: %s", err)
        return
    }

    // Announce transaction
    _, err := client.Transaction.Announce(context.Background(), signedTransaction)
    if err != nil {
        fmt.Printf("Transaction.Announce returned error: %s", err)
        return
    }

    // Wait for lock funds transaction to be harvested
    time.Sleep(30 * time.Second)

    // Announce aggregate bounded transaction
    _, _ = client.Transaction.AnnounceAggregateBonded(ctx, signedAggregateBoundedTransaction)
    if err != nil {
        fmt.Printf("Transaction.AnnounceAggregateBonded returned error: %s", err)
        return
    }

    // Wait for aggregate bounded transaction to be harvested
    time.Sleep(30 * time.Second)


    // Create cosignature transaction from first cosigner
    signatureOneCosignatureTransaction := sdk.NewCosignatureTransactionFromHash(signedAggregateBoundedTransaction.Hash)
    signedSignatureOneCosignatureTransaction, err := cosignerOne.SignCosignatureTransaction(signatureOneCosignatureTransaction)
    if err != nil {
        fmt.Printf("SignCosignatureTransaction returned error: %s", err)
        return
    }

    // Announce transaction
    _, err = client.Transaction.AnnounceAggregateBoundedCosignature(context.Background(), signedSignatureOneCosignatureTransaction)
    if err != nil {
        fmt.Printf("AnnounceAggregateBoundedCosignature returned error: %s", err)
        return
    }

    // Create cosignature transaction from second cosigner
    signatureTwoCosignatureTransaction := sdk.NewCosignatureTransactionFromHash(signedAggregateBoundedTransaction.Hash)
    signedSignatureTwoCosignatureTransaction, err := cosignerTwo.SignCosignatureTransaction(signatureTwoCosignatureTransaction)
    if err != nil {
        fmt.Printf("SignCosignatureTransaction returned error: %s", err)
        return
    }

    // Announce transaction
    _, err = client.Transaction.AnnounceAggregateBoundedCosignature(context.Background(), signedSignatureTwoCosignatureTransaction)
    if err != nil {
        fmt.Printf("AnnounceAggregateBoundedCosignature returned error: %s", err)
        return
    }

    // Wait cosignature transaction to be harvested
    time.Sleep(30 * time.Second)
}
```

