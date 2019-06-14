# TronGrid

TronGrid v3 (TG3) uses a set of NodeJS apps to talk with Redis and PostgreSQL to provide a simple, fast and reliable query interface for the Tron API.

### For a reference to the legacy(v2) version please refer to this version of the [README](https://github.com/tronprotocol/tron-grid/blob/legacy/README.md)

## Notes:

### Versioning

TronGrid v3 (TG3) will use api versioning moving forward. As this is the first iteration of the improved TronGrid, we will start with **v1**. ex: `https://api.trongrid.io/v1`

### Endpoints

1. [Accounts](#accounts)
2. [Assets](#assets)
3. [Blocks](#blocks)
4. [Contracts](#contracts)
5. [Network](#network)
6. [Proposals](#proposals)
7. [Transactions](#transactions)
8. [Witnesses](#witnesses)

### Parameters, Queries, & Return Values

- Addresses in TG3 can be passed in base58 or hex formats.
- Query parameters can be passed in camelCase or snake_case.
- All returned JSON properties will be in snake_case (at the first level at least)
- **NB:** In this document, we will primarily use base58 and snake_case formats

## APIs

### Accounts

#### 1. Get Account Info By Address

- _GET_ https://api.trongrid.io/v1/accounts/:address
- JavaTron (JT) API:
  - `/wallet/getaccount`
- Usage:
  - Returns information about a specific account
- Params:
  - address The account’s address in base58 or hex format (0x... and 41...)
- Options:
  - `only_confirmed` Shows only the situation at latest confirmed block.
    `true` | `false` default `false`
- ex: https://api.trongrid.io/v1/accounts/TLCuBEirVzB6V4menLZKw1jfBTFMZbuKq7only_confirmed=false
- Return example:

```JSON
    {
      "success": true,
      "meta": {
        "at": 1558109062846,
        "page_size": 1
      },
      "data": [
        {
          "account_resource": {
            "energy_usage": 6027620,
            "frozen_balance_for_energy": {
              "expire_time": 1558164300000,
              "frozen_balance": 2116000000000
            },
            "latest_consume_time_for_energy": 1558108998000
          },
          "address": "41704833c02883b3261f7baf62f8cb19b4b0c2e64e",
          "allowance": 704953,
          "asset": [...],
          "assetV2": [...],
          "asset_issued_ID": "31303031343736",
          "asset_issued_name": "47616d65546f6b656e",
          "balance": 4196409173,
          "create_time": 1529897991000,
          "free_asset_net_usageV2": [...],
          "is_witness": true,
          "latest_consume_free_time": 1557905064000,
          "latest_opration_time": 1558108998000,
          "latest_withdraw_time": 1557905064000
        }
      ]
    }
```

#### 2. Get Transactions By Account Address

- _GET_ https://api.trongrid.io/v1/accounts/:address/transactions
- JavaTron (JT) API:
  - `/walletextension/gettransactionfromthis`
  - `/walletextension/gettransactiontothis`
- Usage:
  - Returns all the transactions related to a specified account.
- Params:
  `address` The account’s address
- Options:
  - `only_confirmed` Shows only confirmed.
    `true` | `false` default `false`
  - `only_unconfirmed` Shows only unconfirmed.
    `true` | `false` default `false`
  - `only_to` Only transaction to address.
    `true` | `false` default `false`
  - `only_from` Only transaction from address.
    `true` | `false` default `false`
  - `limit` The requested number of transaction per page. Default `20`. Max `200`.
  - `fingerprint` The fingerprint of the last transaction returned by the previous page
  - `order_by` Pre sorts the results during the query.
    Example:
    `order_by=block_number,asc`
    `order_by=block_timestamp,desc`
  - `min_block_timestamp` The minimum transaction timestamp default `0`
    Alias: `min_timestamp`
  - `max_block_timestamp` The maximum transaction timestamp default `now`
    Alias: `max_timestamp`
- ex: (N.B. Filter are non exclusives.)
  - GET https://api.trongrid.io/v1/accounts/TLCuBEirVzB6V4menLZKw1jfBTFMZbuKq/transactions?only_to=true&only_from=true
    is equivalent to
  - GET https://api.trongrid.io/v1/accounts/TLCuBEirVzB6V4menLZKw1jfBTFMZbuKq/transactions

#### 3. Get Account Resources By Address

- _GET_ https://api.trongrid.io/v1/accounts/:address/resources
- JavaTron (JT) API:
  - `/wallet/getaccountresource`
- Usage:
  - Returns the resources associated to a specific account.
- Params:
  - `address` The account’s address
- ex: https://api.trongrid.io/v1/accounts/TLCuBEirVzB6V4menLZKw1jfBTFMZbuKq/resources

```JSON
    {
      "free_net_used": 4740,
      "free_net_limit": 5000,
      "asset_net_used": [...],
      "asset_net_limit": [...],
      "total_net_limit": 43200000000,
      "total_net_weight": 7001650727,
      "energy_used": 366327641,
      "energy_limit": 402999576,
      "total_energy_limit": 100000000000,
      "total_energy_weight": 496278437
    }

```

#### 4. Create An Account

- _POST_ https://api.trongrid.io/v1/accounts
- JavaTron (JT) API: `/wallet/createaccount`
- Usage:
  - Creates the transaction for the creation of a new account starting from an existing account.
- Payload:
  - `creator` Address of the account creating the new account
  - `address` Address of the new account

#### 5. Update Account Name

- _PUT_ https://api.trongrid.io/v1/accounts/:address
- JavaTron (JT) API: `/wallet/updateaccount`
- Usage:
  - Returns the transaction to change the name of an account. It requires that the account exists.
- Params:
  - `address` The account’s address
- Options:

  - `only_confirmed` Shows only confirmed.
    `true` | `false` default `false`
  - `only_unconfirmed` Shows only unconfirmed.
    `true` | `false` default `false`

- Payload:
  - `name` New name of the new account

#### 6. Update Account Resources

- _PUT_ https://api.trongrid.io/v1/accounts/:address/balances?action=
- JavaTron (JT) API:
  - `/wallet/freezebalance`
  - `/wallet/unfreezebalance`
- Usage:
  - Returns the transaction to freeze a certain amount of TRX in the balance.
- Params:
  - `address` The account’s address
- Options: (**required**)
  - `action` The action to be performed.
  - Example:
    `action=freeze`
    `action=unfreeze`
- Payload:
  - `amount` Amount to be frozen/unfrozen in SUN (**only freeze**)
  - `duration` Duration of the freeze (**only freeze**)
  - `resource` Requested resource
    Accepted values:
    `BANDWIDTH`
    `ENERGY`
  - `receiver_address` Address of the account receiving the bandwidth/energy, if any (**optional**)
- ex: https://api.trongrid.io/v1/accounts/:address/balances?action=freeze

#### 7. Withdraw Witnesses Rewards By Address

- _POST_ https://api.trongrid.io/v1/accounts/:address/witnesses
- JavaTron (JT) API: `/wallet/withdrawbalance`
- Usage:
  - Creates a transaction to allow witnesses to receive their SR rewards. Can be used once/24 hrs.
- Params:
  - `address` The address of the witness

### Assets

#### 1. Get All Assets

- _GET_ https://api.trongrid.io/v1/assets
- Usage:
  - Returns all the assets.
- Options:
  - `order_by` Sorts the results.
    Accepted fields:
    `total_supply,asc` | `total_supply,desc`
    `start_time,asc` | `start_time,desc`
    `end_time,asc` | `end_time,desc`
    `id,asc` | `id,desc`
  - Example:
    `order_by=total_supply,asc`

#### 2. Get Assets By Identifier

- _GET_ https://api.trongrid.io/v1/assets/:identifier
- JavaTron (JT) API:
  - `/wallet/getassetissuebyaccount`
  - `/wallet/getassetissuebyid`
- Usage:
  - Returns all the assets with the specified id or owner.
- Params:
  - `identifier` The identifier to be used to retrieve the asset
    It can be the ID of the asset, or the issuer address
- Options:
  - `only_confirmed` Shows only the situation at latest confirmed block.
    `true` | `false` default `false`

#### 3. Get Assets By Name

- _GET_ https://api.trongrid.io/v1/assets/:name/list
- JavaTron (JT) API:
  - `/wallet/getassetissuelistbyname`
  - `/wallet/getassetissuelist`
- Usage:
  - Returns all the assets with the specified name.
- Params:
  - `name` The name of the asset(s)
- Options:
  - `limit` The requested number of assets per page. Default `20`. Max `200`.
    When there is a pagination, the minimum limit is set to `20`.
  - `fingerprint` The fingerprint of the last asset returned by the previous page.
  - `order_by` Pre sorts the results during the query.
    - Example:
      `order_by=total_supply,asc` (starts from the rarest token)
      `order_by=start_time,desc` (starts from the most recent ICO)
  - `only_confirmed` Shows only the situation at latest confirmed block.
    `true` | `false` default `false`

#### 4. Create Asset By Owner

- _POST_ https://api.trongrid.io/v1/assets
- JavaTron (JT) API: `/wallet/createassetissue`
- Usage:
  - Creates the transaction for the creation of an asset from the owner. Right now, the TVM allows only one asset per account.
- Payload:
  - `owner` Issuer address
  - `name` Name of the asset
  - `abbr` Abbreviation of the asset
  - `description` Description of the asset
  - `url` Url of the entity who is selling the asset
  - `total_supply` Total supply
  - `trx_ratio` Initial ratio with TRX
  - `token_ratio` Initial ratio
  - `start_time` Starting sale time
  - `end_time` Ending sale time
  - `vote_score`:
  - `free_asset_net_limit`:
  - `public_free_asset_net_limit`:
  - `frozen_amount` TRX frozen to create the asset
  - `frozen_days` for how many days
  - `precision` Decimals of the asset. For Trx10 in smart contracts.

#### 5. Participate In Asset ICO

- _POST_ https://api.trongrid.io/v1/assets/:id/sales
- JavaTron (JT) API: `/wallet/participateassetissue`
- Usage:
  - Creates the transaction to purchase an asset during an ICO.
- Params:
  - `id` The token ID
- Payload:
  - `buyer` Address of the buyer of the asset
  - `issuer` Address of the issuer of the asset
  - `amount` Amount of assets to be purchased

#### 6. Transfer An Asset

- _POST_ https://api.trongrid.io/v1/assets/:id/transfers
- JavaTron (JT) API: `/wallet/transferasset`
- Usage:
  - Creates the transaction to transfer an asset.
- Params:
  - `id` The token ID
- Payload:
  - `owner` Owner address
  - `recipient` Address to which the token are send
  - `amount` Amount of assets to be transferred

#### 7. Freeze Or Unfreeze Asset

- _PUT_ https://api.trongrid.io/v1/assets/:id?action=
- JavaTron (JT) API:
  - `/wallet/updateasset`
  - `/wallet/unfreezeasset`
- Usage:
  - The update action returns the transaction to update an asset.
  - The unfreeze action returns the transaction to Unfreeze the asset at the end of the ICO to make it tradable.
- Params:
  - `id` The asset ID
  - `action` The action to performs.
    Accepted values:
    - `update`
    - `unfreeze`
- Payload (**only for update**):
  - `owner` Issuer address
  - `description` New description
  - `url` New url
  - `limit` New bandwidth limit
  - `public_limit` New free bandwidth limit

### Blocks

#### 1. Returns Events By Block Identifier

- _GET_ https://api.trongrid.io/v1/blocks/:identifier/events
- Usage:
  - Returns all the events in the specified block. Depending on the data, the entire block can be confirmed or unconfirmed.
- Params:
  - `identifier` It can be either latest, a block number or a block id.

#### 2. Returns Block Information By Identifier

- _GET_ https://api.trongrid.io/v1/blocks/:identifier
- Usage:
  - Returns the block info with of a specified block.
- Params:
  - `identifier` It can be either 'latest', a block number,
    - Example:
      `6000000` or a block id, for example (for 6000000):
      `00000000005b8d808efbb017b671ca00945a16fa909ef03f210f002ccf3a7719`
- JavaTron (JT) API:
  - `/wallet/getnowblock`
  - `/wallet/getblockbyid`
  - `/wallet/getblockbynum`

#### 3. Get Blocks By Intervals

- _GET_ https://api.trongrid.io/v1/blocks/:identifier1/:identifier2
- JavaTron (JT) API: `/wallet/getblockbylimitnext`
- Usage:
  - Returns a certain number of blocks.
- Params:
  - `identifier1` It can be either a block number or a block id.
  - `identifier2` It can be either a block number or a block id.
- ex:
  - https://api.trongrid.io/v1/blocks/50000/50010
  - returns the blocks in the interval

### Contracts

#### 1. Get Events By Contract Address

- _GET_ https://api.trongrid.io/v1/contracts/:address/events
- Usage:
  - Returns the events emitted by a smart contract.
- Params:
  - `address` The address of the deployed contract.
- Options:
  - `only_confirmed` Shows only confirmed.
    `true` | `false` default `false`
  - `only_unconfirmed` Shows only unconfirmed.
    `true` | `false` default `false`
  - `event_name` The name of the event
  - `block_number` The block number for which the events are required
  - `min_block_timestamp` The minimum block timestamp default `0`
    Alias: `min_timestamp`
  - `max_block_timestamp` The maximum block timestamp default `now`
    Alias: `max_timestamp`
  - `limit` For pagination. Limit 20
  - `fingerprint` The fingerprint of last event retrieved in the page
  - `order_by` Sort the events.
    - Accepted values:
      `block_timestamp,asc` (alias: `timestamp,asc`)
      `block_timestamp,desc` (default)

#### 2. Get Transactions By Contract Address

- _GET_ https://api.trongrid.io/v1/contracts/:address/transactions
- Usage:
  - Returns the transactions related a smart contract.
- Params:
  - `address` The address of the deployed contract.
- Options:
  - `only_confirmed` Shows only confirmed.
    `true` | `false` default `false`
  - `only_unconfirmed` Shows only unconfirmed.
    `true` | `false` default `false`
  - `min_block_timestamp` The minimum block timestamp default `0`
    Alias: `min_timestamp`
  - `max_block_timestamp` The maximum block timestamp default `now`
    Alias: `max_timestamp`
  - `limit` For pagination. Limit `20`
  - `fingerprint` The fingerprint of last event retrieved in the page
  - `order_by` Sort the events.
    - Accepted values:
      `block_timestamp,asc` (alias: `timestamp,asc`)
      `block_timestamp,desc` (default)

#### 3. Get Contract Information By Contract Address

- _GET_ https://api.trongrid.io/v1/contracts/:address
- JavaTron (JT) API: `/wallet/getcontract`
- Usage:
  - Returns the data of a deployed contract.
- Params:

  - `address` The address of the deployed contract (both hex or base58 format supported)

#### 4. Update Contract

- _PUT_ https://api.trongrid.io/v1/contracts/:address
- JavaTron (JT) API:
  - `/wallet/updateenergylimit`
  - `/wallet/updatesetting`
- Usage:
  - If only one option is set, returns the transaction to be signed and broadcasted to update the specified option. If both options are set, it returns an array containing the two transactions.
- Params:
  - `address` The address of the deployed contract.
- Options:
  - `origin_energy_limit` The new energy limit of the contract
  - `user_fee_percentage` The new fee percentage to be paid by the user
- Payload:
  - `sender` The address of the sender

### Network

#### 1. Get Network Information

- _GET_ https://api.trongrid.io/v1/network
- Usage:
  - Returns general info about the network.
- JavaTron (JT) API: `/wallet/getnodeinfo`

#### 2. Get Current Network Parameters

- _GET_ https://api.trongrid.io/v1/network/parameters
- Usage:
  - Returns the current parameters of the chain.
- JavaTron (JT) API: `/wallet/getchainparameters`

#### 3. Get Next Network Maintenance Tine

- _GET_ https://api.trongrid.io/v1/network/next_maintenance
- Usage:
  - Returns the timestamp of the next net maintenance.
- JavaTron (JT) API: `/wallet/getnextmaintenancetime`

#### 4. List All Connected Nodes

- _GET_ https://api.trongrid.io/v1/network/connected_nodes
- JavaTron (JT) API: `/wallet/listnodes`
- Usage:
  - Returns the list of all the nodes connected to the TronGrid node.

### Proposals

#### 1. List All Proposals

- _GET_ https://api.trongrid.io/v1/proposals
- JavaTron (JT) API: `/wallet/listproposals`
- Usage:
  - Returns all the proposals currently in the network.

#### 2. Get Proposal By ID

- _GET_ https://api.trongrid.io/v1/proposals/:id
- JavaTron (JT) API: `/wallet/getproposalbyid`
- Usage:
  - Returns a proposal by its ID.
- Params:
  - `id` The id of the proposal

### Transactions

#### 1. Get Events By Transaction ID

- _GET_ https://api.trongrid.io/v1/transactions/:id/events
- Usage:
  - Returns the events emitted by a transaction.
- Params:
  - `id` The id of the transaction

#### 2. Get Transactions By Block Number

- _GET_ https://api.trongrid.io/v1/transactions?block_number=
- Usage:
  - Returns the number of transactions in a specific block.
- JavaTron (JT) API: `/wallet/gettransactioncountbyblocknum`
- Params:
  - `block_number` The number of the block containing the transactions to be retrieved

#### 3. Get Transaction By ID

- _GET_ https://api.trongrid.io/v1/transactions/:id
- JavaTron (JT) API: `/wallet/gettransactionbyid`
- Usage:
  - Returns a transaction by its ID.
- Params:
  - `id` The id of the transaction

#### 4.Get Transaction Information By ID

- _GET_ https://api.trongrid.io/v1/transactions/:id/info
- JavaTron (JT) API: `/wallet/gettransactioninfobyid`
- Usage:
  - Returns the info related to a transaction by its ID.
- Params:
  - `id` The id of the transaction

#### 5. Create Transfer Transaction

- _POST_ https://api.trongrid.io/v1/transactions
- JavaTron (JT) API: `/wallet/createtransaction`
- Usage:
  - Returns the transaction for a transfer of TRX from sender to recipient.
- Payload:
  - `sender` The address of the sender
  - `recipient` The address of the recipient
  - `amount` The amount of TRX to be sent, in SUN

#### 6. Broadcast Signed Transaction

- _POST_ https://api.trongrid.io/v1/transactions/broadcast
- JavaTron (JT) API: `/wallet/broadcasttransaction`
- Usage:
  - Broadcasts the transaction to the network.
- Payload:
  - `id` The ID of the transaction
  - `signature` The signature of the transaction
  - `data` The raw data of the transaction, in JSON format

### Witnesses

#### 1. List All Witnesses

- _GET_ https://api.trongrid.io/v1/witnesses
- JavaTron (JT) API: `/wallet/listwitnesses`
- Usage:
  - Lists all the witnesses.

#### 2. Create Witness

- _POST_ https://api.trongrid.io/v1/witnesses
- JavaTron (JT) API: `/wallet/createwitness`
- Usage:
  - Create a new witness.
- Payload:
  - `witness` The address of the witness
  - `url` The url of the witness

#### 3. Update Witness URL

- _PUT_ https://api.trongrid.io/v1/witnesses/:address
- JavaTron (JT) API: `/wallet/updatewitness`
- Usage:
  - Update the url of a witness.
- Params:
  - `address` The address of the witness
- Payload:
  - `url` The new url of the witness

#### 4. Vote For Witness By Address

- _POST_ https://api.trongrid.io/v1/witnesses/:address/votes
- JavaTron (JT) API: `/wallet/votewitnessaccount`
- Usage:
  - Allow voter to vote for a specified witness.
- Params:
  - `address` The address of the witness
- Payload:

  - `voter` The address of the voter
  - `amount` The number of votes

### ------

## TronWeb compatibility

To keep compatibility with TronWeb, TronGrid supports also the legacy format.  
The Following are Four Methods for Polling:

## The Following are Four Methods for Polling:

### 1. By Contract Address:<br>

https://api.trongrid.io/event/contract/TEEXEWrkMFKapSMJ6mErg39ELFKDqEs6w3

### 2. By Contract Address and Event Name:<br>

curl https://api.trongrid.io/event/contract/TEEXEWrkMFKapSMJ6mErg39ELFKDqEs6w3/DiceResult

### 3. By Contract Address, Event Name, and Block Height:

https://api.trongrid.io/event/contract/TEEXEWrkMFKapSMJ6mErg39ELFKDqEs6w3/DiceResult/7273383

### 4. By Transaction ID:<br>

https://api.trongrid.io/event/transaction/d74ba9c3947b509db385fe2df5fb1dc49f10fb33da93e1e5903d897714ef0f5c

## Request Parameters:<br>

`fromTimestamp` sets a time stamp, default 0, returning all events after or before that timestamp. For example:

https://api.trongrid.io/event/contract/TEEXEWrkMFKapSMJ6mErg39ELFKDqEs6w3?fromTimestamp=1541547888000

For retro-compatibility you can pass `since` instead of `fromTimestamp`.

`size` indicates the number of results returned. Default is 20, maximum is 200. Example:

https://api.trongrid.io/event/contract/TMJnJcHfdP5rhmXVkwRYb1a9A6gS46PUm6/Notify?size=10

`page` is no more supported.

`sort` indicates the order. By default the order is descending. To explicitly indicate it, use

```
sort=block_timestamp
```

for ascending order and

```
sort=-block_timestamp
```

for descending order. For example:

https://api.trongrid.io/event/contract/TEEXEWrkMFKapSMJ6mErg39ELFKDqEs6w3?fromTimestamp=1541547888000&sort=block_timestamp

`fingerprint` is necessary for pagination. Any time you require an API that could return more data that the indicate size, you will see that the latest element has the property `_fingerprint`. To get the next page, you can just call again the same API adding the parameter `fingerprint=[previous _fingerint parameter]`. For example:

https://api.trongrid.io/event/contract/TEEXEWrkMFKapSMJ6mErg39ELFKDqEs6w3?fingerprint=e1E1OqO3vrhmwE23

`onlyConfirmed` returns only the confirmed events.

`onlyUnconfirmed` returns only unconfirmed events.

If you pass both it returns an error.
