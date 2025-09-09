# ScanRibbon-SVR (Nextcloud app: `outlookscan_profiles`)

Server component for ScanRibbon that manages group-based profile templates and serves signed manifests to clients. It supports client enrollment via a connect password (issuing a permanent client token), signed JWS manifests with ETag for efficient polling, and an admin GUI (Vue) gated to a specific Nextcloud group. Admin OCS endpoints support automation for CRUD and pin order. :contentReference[oaicite:28]{index=28} :contentReference[oaicite:29]{index=29}

## High-level endpoints
- Client (no login):
  - `POST /apps/outlookscan_profiles/api/v1/groups/{slug}/connect`
  - `GET /apps/outlookscan_profiles/api/v1/groups/{slug}/manifest`
- Admin (Nextcloud OCS, requires NC auth + `OCS-APIRequest: true`):
  - Profiles: `GET/POST/PUT/DELETE /ocs/v2.php/apps/outlookscan_profiles/profiles`
  - Groups: `GET/POST/PUT/DELETE /ocs/v2.php/apps/outlookscan_profiles/groups`
  - Pin order: `GET/PUT /ocs/v2.php/apps/outlookscan_profiles/pinorder/{group}`
  - Clients: `GET /ocs/v2.php/apps/outlookscan_profiles/clients`, `DELETE /clients/{id}` (revoke) :contentReference[oaicite:30]{index=30}

## Data model
Tables (prefix `oc_oscan_`): groups, profiles, group_profiles, group_pinorder, group_secrets, clients (token hashes/prefixes, last seen, revoked). :contentReference[oaicite:31]{index=31}

## Security
- HTTPS only; tokens are bearer credentials; store only Argon2id hashes (with prefixes for display).
- Rate limit `/connect`, lockout on failures, audit admin actions.
- Sign manifests (JWS). Clients verify signatures; support ETag/If-None-Match for polling. :contentReference[oaicite:32]{index=32}

## Dev quick start
- Set up a Nextcloud dev instance.
- Create app folder `apps/outlookscan_profiles` and symlink/clone this repo there.
- Implement `appinfo/info.xml`, `appinfo/routes.php`, controllers for Client & Admin endpoints, and migrations for the tables above.
- Admin UI: SPA under `src/admin` (Vue) served to members of the configured NC group.

## Related client project
- [`ScanRibbon`](https://github.com/<ACCOUNT>/ScanRibbon)
