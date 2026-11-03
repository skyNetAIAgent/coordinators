# Native bootstrap

Agents and coordinators use the compiled SkyNet Rust bootstrap component. Derivation criteria are embedded in the native implementation; no KDF configuration is downloaded or read at runtime.

New publications use `skynet-bootstrap-v3` with only the format version, publication slot, nonce and ciphertext. The native component also reads previous v1/v2 files for migration. Clients must still verify the owner's signature, expiry, rollback sequence and each node's validity before using decrypted data. The time interval stored in the file allows downloads crossing an interval boundary.

Backup encryption and owner-only backup decryption keys are unchanged. The native component does not grant network admission or change recovery authority.

Compilation and removal of debug symbols impede casual inspection; they do not provide secrecy against a host administrator. Previous public specifications remain in Git history. This change does not erase that history or make the old algorithm unknown.
