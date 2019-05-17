### Mosaic supply change transaction
Mosaic supply change transaction is used to assign supply to a mosaic.
* Following parameters are required:
  * **Mosaic Id**
    * Combination of namepsace name and mosaic name. For example “foo.bar:xpx”.
  * **Direction**
    * Could be Increase (0) or Decrease (1).
  * **Delta**
    * The amount of supply to increase or decrease.

```go
    nonce, _ := rand.New(rand.NewSource(time.Now().UTC().UnixNano())).Uint32(),
    mosaicId, _ := sdk.NewMosaicIdFromFullName(nonce, accOne.Address.Address)
    delta := big.NewInt(100000000000)

    mosaicSupplyChange, err := sdk.NewMosaicSupplyChangeTransaction(
      sdk.NewDeadline(time.Hour*1),
      mosaicId,
      sdk.Increase,
      delta,
      sdk.MijinTest,
    )

    // Sign transaction
    stx, err := acc.Sign(mosaicSupplyChange)
    if err != nil {
      panic(fmt.Errorf("NewMosaicSupplyChangeTransaction signing returned error: %s", err))
    }

    // Announce transaction
    restTx, err := client.Transaction.Announce(context.Background(), stx)
    if err != nil {
      panic(err)
    }
    fmt.Printf("%s\n", restTx)
    fmt.Printf("Hash: \t\t%v\n", stx.Hash)
    fmt.Printf("Signer: \t%X\n\n", acc.KeyPair.PublicKey.Raw)
```
