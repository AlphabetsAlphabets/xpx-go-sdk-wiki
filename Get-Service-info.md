### Get Drive By Public Account
Get drive is used to get some drive. For getting use **Storage.GetDrive()**

Following parameters is required:
 - **Context** - context
 - **DrivePublicAccount** - the pubcli account of some drive

```go
drive, err := client.Storage.GetDrive(context.Background(), driveAccount.PublicAccount)
handleError(err)

println(drive.String())
```

### Get Drives By An Account Public Account
Get drive is used to get some drive. For getting use **Storage.GetDrive()**

Following parameters is required:
 - **Context** - context
 - **DrivePublicAccount** - the pubcli account of some drive
  
Third parameter can get next values:
- *AllRoles* - all drives on which the account is the participant
- *Owner* - all drives on which the account is the owner
- *Replicator* - all drives on which the account is the replicator

```go
drives, err := client.Storage.GetAccountDrives(context.Background(), owner.PublicAccount, sdk.AllRoles)
handleError(err)

for _, d := range drives {
    println(d.String())
}
```