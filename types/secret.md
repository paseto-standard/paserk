# PASERK Type: secret

This is a plaintext serialization of a secret key for PASETO `public` tokens.

## Format

    k[version].secret.[data]

### Version 1

The `[data]` portion will be DER ASN.1 PKCS#1 RSA Private Key,
**WITHOUT** PEM encoding (base64 and the `-----` prefixes/suffixes).

### Versions 2 and 4

The `[data]` portion will be the Ed25519 secret key as raw bytes.

### Version 3

The `[data]` portion will be the P-384 secret key as raw bytes.