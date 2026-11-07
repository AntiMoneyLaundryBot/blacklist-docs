# Changelog

## 2026-11-06 - Incidents (#279)

### Added
- Incidents: a named event (for example an exchange hack) with one tag `incident.<slug>.<yyyymmdd>`, public or private to the owning organisation.
- `POST /v1/black-list/incidents`, `GET /incidents/{incidentId}`, `GET /incidents/by-tag/{tag}`, `GET /incidents` (cursor paging), `PATCH /incidents/{incidentId}`, `DELETE /incidents/{incidentId}`.
- `GET /v1/black-list/incidents/{incidentId}/addresses` (the addresses tagged with an incident) and `GET /v1/black-list/addresses/{address}/incidents`.
- An explicit incident tag must carry the incident date: its date part must equal `incidentDate` as `yyyymmdd`, else 400.

### Changed
- Adding or removing an `incident.*` tag requires an incident that exists and is visible to you (404 otherwise, same body as a hidden address). `incident.*` tags are hidden from `tags` on `GET /v1/black-list/addresses` when their incident is not visible to you. Responses without incident tags are unchanged.

### Notes
- LabelSniffer carries only public incidents and private incidents owned by the org `amlbot`.

## 2026-09-30 - Address tags (#19)

### Added
- Tags: extra facts about an address on a network, attributed to the writer and following the address's privacy. Format `^[a-z0-9_]+(\.[a-z0-9_]+)*$`, 1-128 characters, lowercase.
- `PUT /v1/black-list/addresses/{address}/tags/{tag}?network=<network>` to add a tag, and `DELETE` on the same path to remove one.
- A top-level `tags` array of `{network, tag}` on `GET /v1/black-list/addresses`, always present and unaffected by `fields`.
- Every tag add and remove is recorded in the audit log (`TAG_ADD` / `TAG_REMOVE`).

### Unchanged
- The legacy `GET /api/addresses/{address}`: no tags, and its 404 still means the address is clear.
