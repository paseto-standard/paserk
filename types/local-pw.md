# PASERK Type: local-pw

This PASERK is a key intended for `local` PASETOs, encrypted with a password.

## Format

    k[version].local-pw.[data]

Where `[data]` is the base64url-encoded payload, defined by each version.

## Operations

See [PBKW](../operations/PBKW.md) for the algorithm used.

## PASERK Versions

### Versions 1 and 3

| Starting Byte | Length | Definition                  |
|---------------|--------|-----------------------------|
| 0             | 32     | PBKDF2-SHA384 Salt (`salt`) |
| 32            | 4      | Iteration count (`i`)       |
| 36            | 16     | AES-CTR nonce (`n`)         |
| 52            | 32     | Encrypted Data Key (`edk`)  |
| 84            | 48     | Authentication tag (`t`)    |

Total decoded length: `132 bytes`

The iteration count (`i`) MUST be encoded as a 32-bit unsigned
integer in big-endian byte order.

### Versions 2 and 4

| Starting Byte | Length | Definition                    |
|---------------|--------|-------------------------------|
| 0             | 16     | Argon2id Salt (`salt`)        |
| 16            | 8      | Argon2id Memory Cost (`mem`)  |
| 24            | 4      | Argon2id Time Cost (`time`)   |
| 28            | 4      | Argon2id Parallelism (`para`) |
| 32            | 24     | XChaCha20 nonce (`n`)         |
| 56            | 32     | Encrypted Data Key (`edk`)    |
| 88            | 32     | Authentication tag (`t`)      |

Total decoded length: `120 bytes`

The Argon2id parameters (`mem`, `time`, `para`) MUST be encoded as
unsigned integers in big-endian byte order. `mem` is 64-bit; the
other parameters are 32-bit.
