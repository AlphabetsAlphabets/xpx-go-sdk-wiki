### Modify multisig account transaction
Modify multisig account transaction is used to change properties of a multisig account.
  * Minimum Approval Delta
    * The number of signatures needed to approve a transaction.
  * Minimum Removal Delta
    * The number of signatures needed to remove a cosignatory.
  * Modifications
    * Array of cosigner accounts added or removed from the multisignature account.
      * Multsig cosignatory modification type. Supported types are:
        * 0: Add cosignatory.
        * 1: Remove cosignatory.
````go
acc1, err := sdk.NewAccountFromPublicKey("6A3C6BA2975BD0C959F54BD2424CC2150C22F4137E1EFC097BBE768615FC36C7", networkType)
acc2, err := sdk.NewAccountFromPublicKey("5AACF12FC96E5A093F52F53F885B28D42349EF377C6DD857963E2DE9CF1F69D4", networkType)

modifyMultisigAccount, err := sdk.NewModifyMultisigAccountTransaction(
	sdk.NewDeadline(time.Hour*1),
	1,
	1,
	[]*sdk.MultisigCosignatoryModification{
		{
			sdk.MultisigCosignatoryModificationType(0),
			acc1,
		},
		{
			sdk.MultisigCosignatoryModificationType(0),
			acc2,
		},
	},
	sdk.MijinTest)
// Sign transaction
stx, err := acc.Sign(modifyMultisigAccount)
if err != nil {
	panic(fmt.Errorf("NewModifyMultisigAccountTransaction signing returned error: %s", err))
}

// Announce transaction
restTx, err := client.Transaction.Announce(context.Background(), stx)
if err != nil {
	panic(err)
}
fmt.Printf("%s\n", restTx)
fmt.Printf("Hash: \t\t%v\n", stx.Hash)
fmt.Printf("Signer: \t%X\n\n", acc.KeyPair.PublicKey.Raw)
````
