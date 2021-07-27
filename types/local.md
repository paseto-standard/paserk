# PASERK Type: local

This is a plaintext serialization of a symmetric key for PASETO `local` tokens.

No operations are necessary; just encode as raw bytes.

## Format

    k[version].local.[data]

Where `[data]` is the base64url-encoded raw bytes of the symmetric key.

### ID Serialization

See [lid](lid.md).
