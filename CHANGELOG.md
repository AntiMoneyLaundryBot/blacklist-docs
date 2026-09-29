# Changelog

## 2026-09-29 - Address tags (#19)

### Added
- Tags: extra facts about an address on a network, attributed to the writer and following the address's privacy. Format `^[a-z0-9_]+(\.[a-z0-9_]+)*$`, 1-128 characters, lowercase.
- `PUT /v1/black-list/addresses/{address}/tags/{tag}?network=<network>` to add a tag, and `DELETE` on the same path to remove one.
- A top-level `tags` array of `{network, tag}` on `GET /v1/black-list/addresses`, always present and unaffected by `fields`.
- Every tag add and remove is recorded in the audit log (`TAG_ADD` / `TAG_REMOVE`).

### Unchanged
- The legacy `GET /api/addresses/{address}`: no tags, and its 404 still means the address is clear.
