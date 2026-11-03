# SkyNet bootstrap

Primary distribution path: `bootstrap.enc.json`.

Agents must use the separately installed owner public signing key. Never trust keys supplied by this repository alone. The bootstrap is encrypted for distribution to agents, but its contents are not confidential from an agent administrator.

Backups under `backups/` use a separate owner-only decryption key. The signed `backups/index.json` identifies encrypted files by SHA-256. Public storage does not grant membership or recovery authority.

Only operator-approved coordinators may join. A node's signed `valid_until` must be enforced even when using a cached catalog. Discovery does not guarantee that a LAN-only endpoint is reachable from the Internet.
