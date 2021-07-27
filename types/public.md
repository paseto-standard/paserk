# PASERK Type: public

This is a plaintext serialization of a public key for PASETO `public` tokens.

## Format

    k[version].public.[data]

### ID Serialization

See [pid](pid.md).

## PASERK Versions

### Version 1

The `[data]` portion will be DER ASN.1 PKCS#1 RSA Public Key
**WITHOUT** PEM encoding (base64 and the `-----` prefixes/suffixes).

### Versions 2 and 4

The `[data]` portion will be the Ed25519 public key as raw bytes.

### Version 3

The `[data]` portion will be the compressed P-384 public key, as specified in
[section 4.3.6, step 2.2 of this document](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.202.2977&rep=rep1&type=pdf).

```
if Y is even:
    [0x02] || [X]
if Y is odd:
    [0x03] || [X]
```

Here, the X coordinate is expressed in big endian byte order.
