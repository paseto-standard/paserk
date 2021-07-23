# PASERK Type: secret-pw

This PASERK is a secret key intended for `public` PASETOs, encrypted with a password.

## Format

    k[version].secret-pw.[data]

Where `[data]` is the base64url-encoded payload, defined by each version.

## Operations

See [PBKW](../operations/PBKW.md) for the algorithm used.

## PASERK Versions

### Version 1

See [V1/V3 Encryption in the PBKW document](../operations/PBKW.md#v1v3-encryption)
for how these values are derived.

| Starting Byte | Length | Definition                  |
|---------------|--------|-----------------------------|
| 0             | 32     | PBKDF2-SHA384 Salt (`salt`) |
| 32            | 4      | Iteration count (`i`)       |
| 36            | 16     | AES-CTR nonce (`n`)         |
| 52            | varies | Encrypted Data Key (`edk`)  |
| varies        | 48     | Authentication tag (`t`)    |

Total decoded length: 100 bytes longer than your PEM-encoded RSA private key.

A typical RSA private key, when PEM-encoded, is about 1674 bytes long. 
Therefore, you can estimate the data payload for a `k1.secret-pw` PASERK to be
`1774` bytes. There **MAY** be variability, so parsers should permit some flex.

The iteration count (`i`) MUST be encoded as a 32-bit unsigned
integer in big-endian byte order.

### Versions 2 and 4

See [V2/V4 Encryption in the PBKW document](../operations/PBKW.md#v2v4-encryption)
for how these values are derived.

| Starting Byte | Length | Definition                    |
|---------------|--------|-------------------------------|
| 0             | 16     | Argon2id Salt (`salt`)        |
| 16            | 8      | Argon2id Memory Cost (`mem`)  |
| 24            | 4      | Argon2id Time Cost (`time`)   |
| 28            | 4      | Argon2id Parallelism (`para`) |
| 32            | 24     | XChaCha20 nonce (`n`)         |
| 56            | 64     | Encrypted Secret Key (`esk`)  |
| 120           | 32     | Authentication tag (`t`)      |

Total decoded length: `152 bytes`

The Argon2id parameters (`mem`, `time`, `para`) MUST be encoded as
unsigned integers in big-endian byte order. `mem` is 64-bit; the
other parameters are 32-bit.

### Version 3

See [V1/V3 Encryption in the PBKW document](../operations/PBKW.md#v1v3-encryption)
for how these values are derived.

| Starting Byte | Length | Definition                   |
|---------------|--------|------------------------------|
| 0             | 32     | PBKDF2-SHA384 Salt (`salt`)  |
| 32            | 4      | Iteration count (`i`)        |
| 36            | 16     | AES-CTR nonce (`n`)          |
| 52            | 48     | Encrypted Secret Key (`esk`) |
| 100           | 48     | Authentication tag (`t`)     |

Total decoded length: `148 bytes`

The iteration count (`i`) MUST be encoded as a 32-bit unsigned
integer in big-endian byte order.
