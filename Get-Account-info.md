
### Get AccountInfo for an account

Returns AccountInfo for an account.

- Following parameters required:
  - **account_id** - The public key or address of the account.

```rust
use xpx_chain_sdk::api::SiriusClient;

#[tokio::main]
async fn main() {
    let node_url = vec!["http://bctestnet1.brimstone.xpxsirius.io:3000"];

    let sirius_client = SiriusClient::new(node_url).await;
    let client = match sirius_client {
        Ok(resp) => resp,
        Err(err) => panic!("{}", err),
    };

    //let account_id: &str = "VC6LFNKEQQEI5DOAA2OJLL4XRPDNPLRJDH6T2B7X";
    let account_id: &str = "5649D09FB884424AB5E3ED16B965CF69E3048A5E641287C319AC3DE995C97FB0";

    let account_info = client.account_api().account_info(account_id).await;
    match account_info {
        Ok(resp) => println!("{}", resp),
        Err(err) => eprintln!("{}", err),
    }
}
```

### Get AccountInfo for multiple accounts

Returns AccountInfo for multiple accounts.

- Following parameters required:
  - **account_ids** - public keys or addresses to get info from

```rust
use xpx_chain_sdk::api::SiriusClient;

#[tokio::main]
async fn main() {
    let node_url = vec!["http://bctestnet1.brimstone.xpxsirius.io:3000"];

    let sirius_client = SiriusClient::new(node_url).await;
    let client = match sirius_client {
        Ok(resp) => resp,
        Err(err) => panic!("{}", err),
    };

    //let account_ids: Vec<&str> = vec![
    //    "VC6LFNKEQQEI5DOAA2OJLL4XRPDNPLRJDH6T2B7X",
    //    "VA46EIYLK4OEUSU5SOUOJLYICJBPRD4QOEP4ET4V",
    //];

    let account_ids: Vec<&str> = vec![
        "5649D09FB884424AB5E3ED16B965CF69E3048A5E641287C319AC3DE995C97FB0",
        "94E15FAC453553A716D86DADE6EA0EB052B42D62326FB684EA54031540DD674F",
    ];

    let accounts_info = client.account_api().accounts_info(account_ids).await;
    match accounts_info {
        Ok(resp) => println!("{:?}", resp),
        Err(err) => eprintln!("{}", err),
    }
}
```

### Get confirmed transactions information

Gets an array of confirmed transaction for which an account is signer or recipient.
- Following parameters required:
  - **public_account** - public account to get transactions associated

```rust
use xpx_chain_sdk::account::PublicAccount;
use xpx_chain_sdk::api::SiriusClient;

#[tokio::main]
async fn main() {
    let node_url = vec!["http://bctestnet1.brimstone.xpxsirius.io:3000"];

    let sirius_client = SiriusClient::new(node_url).await;
    let client = match sirius_client {
        Ok(resp) => resp,
        Err(err) => panic!("{}", err),
    };

    // let network_type = xpx_chain_sdk::network::PUBLIC_TEST;
    let network_type = client.network_type();

    let public_key: &str = "5649D09FB884424AB5E3ED16B965CF69E3048A5E641287C319AC3DE995C97FB0";

    let public_account = PublicAccount::from_public_key(public_key, network_type).unwrap();

    let accounts_transactions = client
        .account_api()
        .transactions(&public_account, None, None, Some("id"))
        .await;

    match accounts_transactions {
        Ok(accounts) => accounts
            .iter()
            .for_each(|account_txs| println!("{}", account_txs)),
        Err(err) => eprintln!("{}", err),
    }
}
```

### Get incoming transactions information

- Gets an array of transactions for which an account is the recipient.
A transaction is said to be incoming regarding an account if the account is the recipient of a transaction.

- Following parameters required:
  - **public_account** - account to get incoming transactions associated

```rust
use xpx_chain_sdk::account::PublicAccount;
use xpx_chain_sdk::api::SiriusClient;

#[tokio::main]
async fn main() {
    let node_url = vec!["http://bctestnet1.brimstone.xpxsirius.io:3000"];

    let sirius_client = SiriusClient::new(node_url).await;
    let client = match sirius_client {
        Ok(resp) => resp,
        Err(err) => panic!("{}", err),
    };

    // let network_type = xpx_chain_sdk::network::PUBLIC_TEST;
    let network_type = client.network_type();

    let public_key: &str = "5649D09FB884424AB5E3ED16B965CF69E3048A5E641287C319AC3DE995C97FB0";

    let public_account = PublicAccount::from_public_key(public_key, network_type).unwrap();

    let accounts_transactions = client
        .account_api()
        .transactions(&public_account, None, None, Some("id"))
        .await;

    match accounts_transactions {
        Ok(accounts) => accounts
            .iter()
            .for_each(|account_txs| println!("{}", account_txs)),
        Err(err) => eprintln!("{}", err),
    }
}
```

### Get outgoing transactions information

- Gets an array of transactions for which an account is the sender.
A transaction is said to be outgoing egarding an account if the account is the sender of a transaction.

- Following parameters required:
  - **Public Account** - account to get outgoing transactions associated with

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
    // A valid public key.
    publicKey = "E17324EAF403B5FD747055ED3ED97CFD1000AF176FB9294C9424A2814D765A76"
)

func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    account, err := sdk.NewAccountFromPublicKey(publicKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
        return
    }

    // Get confirmed transactions information.
    transactions, err := client.Account.OutgoingTransactions(context.Background(), account, nil)
    if err != nil {
        fmt.Printf("Account.OutgoingTransactions returned error: %s", err)
        return
    }

    for _, transaction := range transactions {
        fmt.Printf("%s\n", transaction .String())
    }
}
```

### Get unconfirmed transactions information

- Gets the array of transactions for which an account is the sender or
receiver and which have not yet been included in a block.

- Following parameters required:
  - **Public Account** - account to get unconfirmed transactions associated with

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
    // Private key of some exist account.
    publicKey = "E17324EAF403B5FD747055ED3ED97CFD1000AF176FB9294C9424A2814D765A76"
)

func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    account, err := sdk.NewAccountFromPublicKey(publicKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
        return
    }

    // Get confirmed transactions information.
    // Param account - A PublicAccount struct
    transactions, err := client.Account.UnconfirmedTransactions(context.Background(), account, nil)
    if err != nil {
        fmt.Printf("Account.UnconfirmedTransactions returned error: %s", err)
        return
    }

    for _, transaction := range transactions {
        fmt.Printf("%s\n", transaction.String())
    }
}
```

### Get multisig account information.

- Following parameters required:
  - **Address** - address of mulstisig account to get information about

```go
package main

import (
    "fmt"
    "github.com/proximax-storage/go-xpx-catapult-sdk/sdk"
    "context"
)

const (
    // Catapult-api-rest server.
    baseUrl = "http://localhost:3000"
    // Types of network.
    networkType = sdk.MijinTest
    // A valid public key.
    multisigPublicKey = "E17324EAF403B5FD747055ED3ED97CFD1000AF176FB9294C9424A2814D765A76"
)

func main() {

    conf, err := sdk.NewConfig(context.Background(), []string{baseUrl})
    if err != nil {
        fmt.Printf("NewConfig returned error: %s", err)
        return
    }

    // Use the default http client
    client := sdk.NewClient(nil, conf)

    multisig, err := sdk.NewAccountFromPublicKey(multisigPublicKey, networkType)
    if err != nil {
        fmt.Printf("NewAccountFromPublicKey returned error: %s", err)
        return
    }

    // Get confirmed transactions information.
    multisigAccountInfo, err := client.Account.GetMultisigAccountInfo(context.Background(), multisig.Address)
    if err != nil {
        fmt.Printf("Account.GetMultisigAccountInfo returned error: %s", err)
        return
    }
    fmt.Printf("%s\n", multisigAccountInfo.String() )
}
```

