### Mosaic definition transaction
 Before a mosaic can be created or transferred, a corresponding definition of the mosaic has to be created and published to the network.
 - **This is done via a mosaic definition transaction.**
 * Following parameters are required:
   * **Name**
     * Name of the mosaic, up to a size limit of 64 characters; must be unique under the domain name.
   * **Namespace name**
     * To be able to create a mosaic definition, an account must rent at least one root namespace which the mosaic definition can then refer to.
   * **Mosaic properties**
     * **supply mutable:** The creator can choose between a definition that allows a mosaic supply change at a later point or a immutable supply. In the first case the creator is only allowed to decrease the supply within the limits of mosaics owned.
     * **transferability:** The creator can choose if the mosaic can be transferred to and from arbitrary accounts, or only allowing itself to be the recipient once transferred for the first time.
     * **levymutable**
     * **divisibility:** Determines up to what decimal place the mosaic can be divided. Divisibility of 3 means that a mosaic can be divided into smallest parts of 0.001 mosaics. The divisibility must be in the range of 0 and 6.
     * **duration:** The number of confirmed blocks we would like to rent our namespace for. Should be inferior or equal to namespace duration.

```go

  mosaicDefinition, err := sdk.NewMosaicDefinitionTransaction(
    sdk.NewDeadline(time.Hour*1),
    rand.New(rand.NewSource(time.Now().UTC().UnixNano())).Uint32(),
    acc.PublicAccount.PublicKey,
    sdk.NewMosaicProperties(
      true,
      true,
      true,
      4,
      big.NewInt(10000)),
    sdk.MijinTest)
  // Sign transaction
  stx, err := acc.Sign(mosaicDefinition)
  if err != nil {
    panic(fmt.Errorf("NewMosaicDefinitionTransaction signing returned error: %s", err))
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
