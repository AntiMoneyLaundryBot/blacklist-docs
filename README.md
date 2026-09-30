# AMLBot blockchain address blacklist

This blacklist is regularly maintained and updated by internal company staff from different sources, including automated scam detection systems. 

You can try it out having an API key with the following CURL command:
  ```
curl -X 'GET' \
  'https://blacklist.amlbot.com/api/addresses/TFWzDGKox7TLyRfVivFdDHAp5x7BVD3j8N' \
  -H 'accept: application/hal+json' \
  -H 'X-API-KEY: <YOUR_API_KEY_HERE>'
  ```

# Access
API Key is required to access blacklist. Please contact kyc@amlbot.com

# Fields description
  ```
{
  "address": "TFWzDGKox7TLyRfVivFdDHAp5x7BVD3j8N",
  "addressInfo": {
    "network": "TRON",
    "type": "terrorism financing",
    "createdAt": "2024-05-22T10:16:38.658Z",
    "deletedAt": null
  }
}
```

1. ```address``` = blacklist address value
2. ```network``` = blockchain name
3. ```type``` = type of blacklisted address, i.e. "scam", or "malware", or "honeypot".
4. ```createdAt``` = date/time address was reported

# Examples
```
{
  "address": "0x3bc3eb780be7b08e88e0c2b289edbe60dde3830d",
  "addressInfo": {
    "network": "ethereum",
    "type": "stolen_coins",
    "createdAt": "2024-06-08T10:40:45.517Z",
    "deletedAt": null
  }
}
```

``` 
{
  "address": "bc1qa00swfw3450z3mamf6u0zu2afzspxvnh3zjy8g",
  "addressInfo": {
    "network": "bitcoin",
    "type": "stolen_coins",
    "createdAt": "2024-06-08T10:40:13.913Z",
    "deletedAt": null
  }
}
```

``` 
{
  "address": "cosmos17fm6kk9r98nhvas49p5lzzr5qtkmh5hruwf692",
  "addressInfo": {
    "network": "cosmos",
    "type": "stolen_coins",
    "createdAt": "2024-06-08T10:39:25.049Z",
    "deletedAt": null
  }
}
```

# Type values

Possible type values are taken from the following list: 

| Type                    | Label                     | Description                                                                                   |
|-------------------------|---------------------------|-----------------------------------------------------------------------------------------------|
| child_exploitation      | Child Exploitation        | Entities associated with child exploitation.                                                  |
| dark_market             | Dark Market               | An online marketplace which operates via darknets and is used for trading illegal products for cryptocurrency. |
| dark_service            | Dark Service              | An organization which operates via darknets and offers illegal services for cryptocurrency.    |
| enforcement_action      | Enforcement Action Related| The entity is subject to legal proceedings with the judicial authorities.                     |
| exchange_fraudulent     | Fraudulent Exchange       | Exchanges involved in exit scams, illegal behavior, or whose funds have been confiscated by government authorities. |
| gambling                | Gambling                  | Coins associated with unlicensed online games                                                 |
| illegal_service         | Illegal Service           | Coins associated with illegal activities.                                                     |
| malware                 | Malware                   | Malware                                                                                       |
| mixer                   | Mixing Service            | Coins that passed via a mixer to make tracking difficult or impossible. Mixers are mainly used for money laundering. |
| ransom                  | Extortion / Ransom        | Coins obtained by extortion or blackmail.                                                     |
| sanctions               | Sanctions                 | Entities subject to sanctions.                                                                |
| scam                    | Scam                      | Coins that were obtained by deception, Phishing address, Address poisoning                    |
| stolen_coins            | Stolen Coins              | Theft, Coins obtained by stealing someone else's cryptocurrency.                              |
| terrorism_financing     | Terrorism                 | Entities associated with terrorism financing.                                                 |

# Tags

A tag is an extra fact about an address on a network, alongside its `type`. An address can have many tags. Every tag is attributed to the user who wrote it, and tags follow the privacy of the address: if you cannot see an address's labels, you cannot see its tags.

## Tag format

- Regex: `^[a-z0-9_]+(\.[a-z0-9_]+)*$`
- 1 to 128 characters, lowercase only (an uppercase tag is rejected with 400, never converted).
- Convention: `<namespace>.<value>[.<value>...]`, where the namespace is the first segment. Examples: `geo.uk.london`, `deposit.binance`.
- There is no registry of tags and no edit. To change a tag, remove it and add the new one.

## Add a tag

`PUT /v1/black-list/addresses/{address}/tags/{tag}?network=<network>`

`network` is required and must be a supported network name. There is no request body. The permission is the same as for creating an address label.

```
curl -X 'PUT' \
  'https://api-blacklist.amlbot.com/v1/black-list/addresses/TExampleAddressDoNotUse000000000000/tags/deposit.binance?network=tron' \
  -H "X-API-KEY: $API_KEY"
```

```
{
  "address": "TExampleAddressDoNotUse000000000000",
  "network": "tron",
  "tag": "deposit.binance",
  "added": true
}
```

Adding a tag that is already present is a success with `"added": false`, and nothing is written.

## Remove a tag

`DELETE /v1/black-list/addresses/{address}/tags/{tag}?network=<network>`

```
curl -X 'DELETE' \
  'https://api-blacklist.amlbot.com/v1/black-list/addresses/TExampleAddressDoNotUse000000000000/tags/deposit.binance?network=tron' \
  -H "X-API-KEY: $API_KEY"
```

Removal is permanent; the history is kept in the audit log.

**Warning:** never send `.` or `..` as a tag. HTTP clients collapse these path segments, and `DELETE .../addresses/{address}/tags/..` becomes `DELETE /v1/black-list/addresses/{address}/`, which is the address-label delete.

## Status codes

| Status | Meaning                                                                                                   |
|--------|-----------------------------------------------------------------------------------------------------------|
| 200    | Add succeeded. `added` is `true` for a new tag and `false` if it was already present.                    |
| 204    | Remove succeeded.                                                                                         |
| 400    | Validation error: bad tag format, or a missing or unknown `network`.                                      |
| 403    | The key does not have the permission to write addresses.                                                  |
| 404    | Remove: the tag is absent, or the address is not visible to you. Add: the address is not visible to you. The two cases are deliberately identical. |
| 500    | `Tag write failed`. Retrying is safe: a repeated add returns `added: false`.                              |

## Address and network normalisation

- The address is normalised for the network before it is stored or looked up. EVM addresses are stored lowercased under `network: "evm_eoa"`, so `ethereum`, `polygon` and `evm_eoa` all address the same tags.
- Known limits, the same as for labels:
  - a `0x...` address tagged on a non-EVM network keeps its letter case, and is found only when that network is given in `blockchains` (with the same letter case);
  - a non-`0x` address tagged on an EVM network is found only when `blockchains` is not given.

## Reading tags

Tags are returned by `GET /v1/black-list/addresses?address=...` as a top-level `tags` array of `{network, tag}`, after `publicBlacklist` and `privateBlacklist`:

```
{
  "publicBlacklist": [],
  "privateBlacklist": [],
  "tags": [
    { "network": "tron", "tag": "deposit.binance" }
  ]
}
```

- `tags` is always present (`[]` when there are none, or when they are not visible to you).
- It covers all networks of the address, or only those in `blockchains` if you filter.
- It is not affected by `fields`, which trims label rows only.
- An address with tags but no labels returns empty label lists plus its `tags`.
- Tags are sorted by network, then by tag.

## Legacy endpoint

The legacy `GET /api/addresses/{address}` is unchanged. It never returns tags, and its 404 still means the address is clear.
