
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
  - **public_account** - account to get outgoing transactions associated

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
        .outgoing_transactions(&public_account, None, None, Some("id"))
        .await;

    match accounts_transactions {
        Ok(accounts) => accounts
            .iter()
            .for_each(|account_txs| println!("{}", account_txs)),
        Err(err) => eprintln!("{}", err),
    }
}
```

### Get unconfirmed transactions information

- Gets the array of transactions for which an account is the sender or
receiver and which have not yet been included in a block.

- Following parameters required:
  - **public_account** - account to get unconfirmed transactions associated

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
        .unconfirmed_transactions(&public_account, None, None, Some("id"))
        .await;

    match accounts_transactions {
        Ok(accounts) => accounts
            .iter()
            .for_each(|account_txs| println!("{}", account_txs)),
        Err(err) => eprintln!("{}", err),
    }
}
```

### Get multisig account information.
- Returns the multisig account information.
- Following parameters required:
  - **account_id** - public key or address of mulstisig account to get information about

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

    let account_id: &str = "VB4JKBIUALJYLYNRB2QJM3TPDGZDOGHSVKDIBR4R";
    //let account_id: &str = "4543E75720C9ED6D9AC5FB360AEBD24223F5E08D442416B70BEC0B4A4446D5A4";

    let multisig = client.account_api().account_multisig(account_id).await;
    match multisig {
        Ok(resp) => println!("{}", resp),
        Err(err) => eprintln!("{}", err),
    }
}
```

### Get multisig account graph information.
- Returns the multisig account graph.
- Following parameters required:
  - **account_id** - public key or address of mulstisig account to get information about

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

    let account_id: &str = "VB4JKBIUALJYLYNRB2QJM3TPDGZDOGHSVKDIBR4R";
    //let account_id: &str = "4543E75720C9ED6D9AC5FB360AEBD24223F5E08D442416B70BEC0B4A4446D5A4";

    let multisig = client.account_api().account_multisig_graph(account_id).await;
    match multisig {
        Ok(resp) => println!("{}", resp),
        Err(err) => eprintln!("{}", err),
    }
}
```



