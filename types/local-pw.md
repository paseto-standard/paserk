# PASERK Type: local-pw

This PASERK is a key intended for `local` PASETOs, encrypted with a password.

## Format

    k[version].local-pw.[data]

Where `[data]` is the base64url-encoded payload, defined by each version.

### ID Serialization

See [lid](lid.md).

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


## Examples

```
k1.local-pw.TikhC_wHVYtg1T2enmE6eR2KW9KeW_GqtbSU3HQ5kxkAAAPoq1eXdbqYsbIvE7YBFYFlLLgm7RWC4Ks2jLiNDZObdusSFRP45aJ_7Jkt9tzspqRS95ZCLAID3IkSTEg2WpLk7jKJMmtg4V0EK3SkEYC5AJOnGd3VFMlWi0qXQk9ULIvv
``` 

```
k2.local-pw.w_h1_Ul9E041QJl8m9n7lAAAAAAEAAAAAAAAAgAAAAHslzni8NnrQ02dsjBknexAKjxpd-1BKxK-P1ZdSVJYiimOOAuaIu3jLyZSBDx1VNqzyH7MGaQe1YGqcEyT7GghPssmLA46BipTj3ji9xqRHO_7twcqCheD
```

```
k3.local-pw.L4xcw6PFvnL9pJAuhaieAT8_Ko4YlQpx70uYSAa6WakAAAPorOFKZxUYOusjRpqLe7GShAQ2z7v38wL9tUvOP2ffCVmhznjyitHhEGjd1FywEFzIHuBypneYqVSej5J2ZB-8uSahQ2MW1fK5cV1ZMz3myN0CTNk9uxXSJvccR64low9P
```

```
k4.local-pw._bru5tnkPSFXOtKhBTmW4gAAAAAEAAAAAAAAAgAAAAGKI3PyFS2vyQ9o5qowCR_GUXskLmdV1bjjc3vqnbwN7hVG1lAUCGjElTGIoH-K6lnkHnP4uaFBKWEtB3xFEGzAjzBSnl_JBmwLYK5jstjAV6LnJm_NOt0j
```
