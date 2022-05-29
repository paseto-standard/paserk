# Key-Wrapping: pie

This document describes a specific type of [key-wrapping](../Wrap.md)
supported by PASERK. See the parent document for more information.

## Algorithm Lucidity

### Encryption (Key Wrapping)

Before performing any key-wrapping, the following checks **MUST** be performed.

1. The plaintext key (`ptk`) must be intended for the same PASERK/PASETO version
   as the wrapping key (`wk`).
2. If the operation is `local-wrap`, an asymmetric key **MUST NOT** be provided,
   and a symmetric key **MUST** be provided.
3. If the operation is `secret-wrap`, an asymmetric key **MUST** be provided, 
   and a symmetric key **MUST NOT** be provided.

### Decryption (Key Unwrapping)

Before returning the decrypted plaintext key, the following checks **MUST** be performed.

1. If the operation is `local-wrap`, an asymmetric key **MUST NOT** be provided,
   and a symmetric key **MUST** be provided.
   * Additionally, the decrypted plaintext key **MUST** be only 32 bytes long.
2. If the operation is `secret-wrap`, an asymmetric key **MUST** be provided,
   and a symmetric key **MUST NOT** be provided.
   * Additionally, the decrypted plaintext key **MUST** be the appropriate size for
     the secret key of a given version. (See table below.)

| PASERK/PASETO Version | Secret Key Length Constraints |
|---|---|
| Version 1 | At least 1600 bytes. |
| Version 2 | 64 bytes |
| Version 3 | 48 bytes |
| Version 4 | 64 bytes |

## PASERK Versions

### Versions 1 and 3

Algorithms: AES-256-CTR, HMAC-SHA384

The header `h` will depend on the exact version and mode being used
, as well as the prefix `pie` (with a trailing period). It will be one of
(`k1.local-wrap.pie.`, `k1.secret-wrap.pie.`, `k3.local-wrap.pie.`, 
`k3.secret-wrap.pie.`).

#### V1/V3 Encryption

Given a plaintext key `ptk` and wrapping key `wk`:

1. [Enforce Algorithm Lucidity](#algorithm-lucidity)
2. Generate a 256 bit (32 bytes) random nonce, `n`.
3. Derive the encryption key `Ek` and CTR nonce `n2` as:
   ```
   x = HMAC-SHA384(msg = 0x80 || n, key = wk)
   Ek = x[0:32]
   n2 = x[32:]
   ```
4. Derive the authentication key `Ak` as:  
   `Ak = HMAC-SHA384(msg = 0x81 || n, key = wk)`
5. Encrypt the plaintext key `ptk` with `Ek` and `n2` to obtain the 
   wrapped key `c`:  
   `c = AES-256-CTR(msg = ptk, key = Ek, nonce = n2)`
6. Calculate the authentication tag `t` as:
   `t = HMAC-SHA384(msg = h || n || c, key = Ak)`
7. Return `base64url(t || n || c)`.

#### V1/V3 Decryption

Given a base64url-encoded encrypted key `b`, and the wrapping key `wk`:

1. Decode `b` from Base64url. The first 48 bytes of the decoded bytes will
   be the authentication tag `t`. The next 32 bytes will be the nonce `n`.
   The remaining bytes will be the wrapped key, `c`. 
2. Derive the authentication key `Ak` as:    
   `Ak = HMAC-SHA384(msg = 0x81 || n, key = wk)`
3. Recalculate the authentication tag `t2` as:
   `t2 = HMAC-SHA384(msg = h || n || c, key = Ak)`
4. Compare `t` with `t2` in constant-time. If it doesn't match, abort.
5. Derive the encryption key `Ek` and CTR nonce `n2` as:
   ```
   x = HMAC-SHA384(msg = 0x80 || n, key = wk)
   Ek = x[0:32]
   n2 = x[32:]
   ``` 
6. Decrypt the wrapped key `c` with `Ek` and `n2` to obtain the
   plaintext key `ptk`:  
   `ptk = AES-256-CTR(msg = c, key = Ek, nonce = n2)`
7. Return `ptk`.

### Version 2 and 4

Algorithms: XChaCha20, BLAKE2b

The header `h` will depend on the exact version and mode being used
, as well as the prefix `pie` (with a trailing period). It will be one of
(`k2.local-wrap.pie.`, `k2.secret-wrap.pie.`, `k4.local-wrap.pie.`,
`k4.secret-wrap.pie.`).

#### V2/V4 Encryption

Given a plaintext key `ptk` and wrapping key `wk`:

1. [Enforce Algorithm Lucidity](#algorithm-lucidity)
2. Generate a 256 bit (32 bytes) random nonce, `n`.
3. Derive the encryption key `Ek` and XChaCha nonce `n2` as:
   ```
   x = crypto_generichash(msg = 0x80 || n, key = wk, length = 56)
   Ek = x[0:32]
   n2 = x[32:]
   ```
4. Derive the authentication key `Ak` as:
   `Ak = crypto_generichash(msg = 0x81 || n, key = wk, length = 32)`
   (This will return a 256-bit (32-byte) output.)
5. Encrypt the plaintext key `ptk` with `Ek` and `n2` to obtain the
   wrapped key `c`:  
   `c = XChaCha20(msg = ptk, key = Ek, nonce = n2)`
6. Calculate the authentication tag `t` as:
   `t = crypto_generichash(msg = h || n || c, key = Ak, length = 32)`
   (This will return a 256-bit (32-byte) output.)
7. Return `base64url(t || n || c)`.

#### V2/V4 Decryption

Given a base64url-encoded encrypted key `b`, and the wrapping key `wk`:

1. Decode `b` from Base64url. The first 32 bytes of the decoded bytes will
   be the authentication tag `t`. The next 32 bytes will be the nonce `n`.
   The remaining bytes will be the wrapped key, `c`.
2. Derive the authentication key `Ak` as:
   `Ak = crypto_generichash(msg = 0x81 || n, key = wk, length = 32)`
   (This will return a 256-bit (32-byte) output.)
3. Recalculate the authentication tag `t2` as:
   `t2 = crypto_generichash(msg = h || n || c, key = Ak, length = 32)`
   (This will return a 256-bit (32-byte) output.)
4. Compare `t` with `t2` in constant-time. If it doesn't match, abort.
5. Derive the encryption key `Ek` and XChaCha nonce `n2` as:
   ```
   x = crypto_generichash(msg = 0x80 || n, key = wk, length = 56)
   Ek = x[0:32]
   n2 = x[32:]
   ```
6. Decrypt the wrapped key `c` with `Ek` and `n2` to obtain the
   plaintext key `ptk`:  
   `ptk = XChaCha20(msg = c, key = Ek, nonce = n2)`
7. [Enforce Algorithm Lucidity](#algorithm-lucidity)
8. Return `ptk`.
