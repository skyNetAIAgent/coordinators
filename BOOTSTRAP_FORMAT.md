# Bootstrap v2

This is public distribution encoding, not confidential encryption. Anyone knowing these parameters can derive the AES key. Always verify the owner's Ed25519 signature using independently installed trusted keys before using the decrypted configuration.

1. Canonicalize `bootstrap-kdf.json` as UTF-8 JSON with lexicographically sorted keys, no whitespace, and no trailing newline. Its SHA-256 is `889e50d9eb9d6c19d2ac3548c3aa425ecccb08a7415c7f1013848356aab79e22`. Clients pin this hash; a repository cannot silently replace the trusted parameters.
2. `slot = floor(issued_at_unix_seconds / 300)` using POSIX/Unix UTC seconds, with no timezone conversion or astronomy.
3. HKDF-SHA256: IKM = slot encoded as 8-byte unsigned big-endian integer; salt = the 32 raw SHA-256 bytes from step 1; info = ASCII `SKYNET-BOOTSTRAP-V2`, followed by one zero byte, followed by ASCII network ID `skynet-pilot`; output length = 32 bytes.
4. Encrypt the signed manifest using AES-256-GCM and a fresh random 12-byte nonce. Header fields: `format`, `network_id`, `interval_seconds`, `slot`, `config_sha256`, `nonce`. Format is `skynet-bootstrap-v2`. Nonce and ciphertext (including GCM tag) use standard Base64. AAD = ASCII `SKYNET-BOOTSTRAP-V2`, one zero byte, then canonical JSON of the header (all fields except `ciphertext`).
5. A downloader derives the key using the slot in the file, not its current clock. The decrypted signed payload's `issued_at // 300` must match the header slot, and its network ID must match too. Then check signature, expiry, sequence rollback, and each coordinator's `valid_until` using the client's clock. Successful decryption never extends validity.

Test vector: slot `6000000`, the configuration hash above, and network `skynet-pilot` produce key hex `e4954a1f3653ef0ed83844b396ea7e0c8e902e15752aada3f357a3f85626e63e`.

Five-minute windows align to UTC 00:00, 00:05, etc. A download crossing a window boundary remains decryptable. An expired manifest is still rejected. Backup files under `backups/` do NOT use this public key derivation; they retain the separate owner-only decryption key.
