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


# Incidents

An incident is a named event, such as an exchange hack, that addresses can be linked to. Each incident owns one tag of the form `incident.<slug>.<yyyymmdd>`. Adding that tag to an address (the tag route above) links the address to the incident. Reading the incident's addresses, or an address's incidents, is done with the routes below. All paths are under `/v1/black-list`; the key goes in `X-API-KEY`.

An incident looks like this:

```
{
  "id": "00000000-0000-4000-8000-000000000000",
  "name": "Example exchange hack",
  "tag": "incident.example_hack.20261030",
  "organization": "example_org",
  "isPublicInfo": false,
  "description": null,
  "incidentDate": "2026-10-30",
  "reference": null,
  "status": "ACTIVE",
  "createdAt": "2026-10-30T12:00:00.000Z",
  "updatedAt": "2026-10-30T12:00:00.000Z",
  "createdBy": "user (example_org)"
}
```

## Routes

| # | Route | Purpose | Permission |
|---|-------|---------|------------|
| 1 | `POST /incidents` | Create an incident. Returns 201. | write |
| 2 | `GET /incidents/{incidentId}` | Get an incident by id. | read |
| 3 | `GET /incidents/by-tag/{tag}` | Get an incident by its tag. | read |
| 4 | `GET /incidents` | Search incidents. | read |
| 5 | `PATCH /incidents/{incidentId}` | Update an incident. | write |
| 6 | `DELETE /incidents/{incidentId}` | Delete an incident (soft delete). | write; owner organisation or `write_address_global` |
| 7 | `GET /incidents/{incidentId}/addresses` | List the addresses tagged with the incident. | read |
| 8 | `GET /addresses/{address}/incidents` | List the incidents of an address. | read |

### 1. Create: `POST /incidents`

Body: `name` (1 to 256 characters, required), `incidentDate` (`YYYY-MM-DD`, required), `isPublicInfo` (default `false`), `description`, `reference`, `tag`. Unknown fields are rejected with 400.

- Without `tag`, the tag is built from the name as `incident.<slug>.<yyyymmdd>`.
- An explicit `tag` must match `^incident\.[a-z0-9_]+\.[0-9]{8}$` and be at most 128 characters. **Its date part must equal `incidentDate` written as `yyyymmdd`, otherwise the request is rejected with 400 (`tag date must equal incidentDate`).** An explicit tag overrides the slug only, never the date.
- Status codes: 201 created; 400 validation (including an empty slug, a tag over 128 characters, or a date mismatch); 403 `isPublicInfo: true` without `write_address_global`; 409 the tag is already taken (a deleted incident keeps its tag); 500.

```
curl -X 'POST' \
  'https://api-blacklist.amlbot.com/v1/black-list/incidents' \
  -H "X-API-KEY: $API_KEY" -H 'Content-Type: application/json' \
  -d '{"name": "Example exchange hack", "incidentDate": "2026-10-30", "tag": "incident.example_hack.20261030"}'
```

### 2 and 3. Get

`GET /incidents/{incidentId}` and `GET /incidents/by-tag/{tag}` return the incident, or 404 when it does not exist or you cannot see it. A deleted incident (`"status": "DELETED"`) is returned only to its owner organisation and to keys with `read_address_global`.

### 4. Search: `GET /incidents`

Query: `name` (case-insensitive substring), `status` (`ACTIVE` or `DELETED`, one value, default `ACTIVE`), `isPublicInfo` (`true` or `false`), `limit` (1 to 100, default 50), `cursor`.

The response is `{ "items": [...], "nextCursor": "..." | null }`. A page may be shorter than `limit`, or even empty, before the end of the results. **Keep following `nextCursor` until it is `null`.** A bad cursor gives 400.

### 5. Update: `PATCH /incidents/{incidentId}`

Body: any of `name`, `description`, `incidentDate`, `reference`, `isPublicInfo`. The tag cannot be changed: `tag`, or any other unknown field, gives 400. Other codes: 403 (making an incident public without `write_address_global`, or not the owner), 404.

### 6. Delete: `DELETE /incidents/{incidentId}`

Returns 204, also when the incident is already deleted. 403 if you are neither the owner organisation nor a holder of `write_address_global`; 404 if it is absent or not visible. The incident row is kept with `status: DELETED` and its tag stays reserved.

### 7. Addresses of an incident: `GET /incidents/{incidentId}/addresses`

Query: `limit` (1 to 100, default 50), `cursor`. Response: `{ "items": [{ "address", "network", "tag", "createdAt", "createdBy" }], "nextCursor": "..." | null }`. Only addresses whose labels you can see are listed. Pages may be short or empty, so follow `nextCursor` until `null`. A garbled cursor, or one from another incident, gives 400; an absent or invisible incident gives 404. `network` is `evm_eoa` for every EVM chain.

### 8. Incidents of an address: `GET /addresses/{address}/incidents`

Query: `network` (optional; normalised like the rest of the API). Response: `{ "items": [{ "network", "tag", "incident": { ... } }] }`. An address you cannot see returns `"items": []` with 200. If an incident lookup fails the call returns 500 and never a partial list.

```
curl -X 'GET' \
  'https://api-blacklist.amlbot.com/v1/black-list/addresses/TExampleAddressDoNotUse000000000000/incidents?network=tron' \
  -H "X-API-KEY: $API_KEY"
```

## Visibility rules

- **An active incident is visible** if it is public (`isPublicInfo: true`), or your organisation owns it, or your key has `read_address_global`.
- **A deleted incident is visible** only to its owner organisation and to keys with `read_address_global`.
- **Only a key with `write_address_global` can create or publish a public incident.**

### Adding and removing an incident tag (D3)

The tag route (`PUT /addresses/{address}/tags/{tag}`) treats `incident.*` tags specially:

- **Add:** the tag must belong to an incident that is active and visible to you. Otherwise the answer is 404, with the same body as for a hidden address, so a 404 does not tell you whether the incident exists.
- **Remove:** allowed for an active incident visible to you. For a deleted incident, only the owner organisation and keys with `write_address_global` may remove the tag. A tag with no incident cannot be removed through this route (404).
- Tags in other namespaces behave exactly as described in the Tags section.

### Reading incident tags (D4)

In `tags` on `GET /addresses` and in route 8, an `incident.*` tag is shown only when its incident is visible to you (rules above). A tag whose incident does not exist, or lost the claim on its tag, is hidden. If the incident lookup fails, the request fails with 500 rather than return a list that might include hidden incidents. When an address has no `incident.*` tags, the response is exactly what it was before.

### Private incidents and the incident listing (D7)

The same visibility rules apply to every incident route: search, get, by-tag, the address listing and route 8. An incident you cannot see is a 404 (or is missing from a list), never a 403. An incident whose tag was claimed by another incident (an orphan) is visible only to its owner organisation and to global readers, and is never found by tag.

### LabelSniffer

LabelSniffer carries only public incidents and private incidents owned by the org `amlbot`.
