# Key-Wrapping: pie

This document describes a specific type of [key-wrapping](../Wrap.md)
supported by PASERK. See the parent document for more information.

## PASERK Versions

### Versions 1 and 3

Algorithms: AES-256-CTR, HMAC-SHA384

The header `h` will depend on the exact version and mode being used
, as well as the prefix `pie` (with a trailing period). It will be one of
(`k1.local-wrap.pie.`, `k1.secret-wrap.pie.`, `k3.local-wrap.pie.`, 
`k3.secret-wrap.pie.`).

#### V1/V3 Encryption

Given a plaintext key `ptk` and wrapping key `wk`:

1. Generate a 256 bit (32 bytes) random nonce, `n`.
2. Derive the encryption key `Ek` and CTR nonce `n2` as:
   ```
   x = HMAC-SHA384(msg = 0x80 || n, key = wk)
   Ek = x[0:32]
   n2 = x[32:]
   ```
3. Derive the authentication key `Ak` as:  
   `Ak = HMAC-SHA384(msg = 0x81 || n, key = wk)`
4. Encrypt the plaintext key `ptk` with `Ek` and `n2` to obtain the 
   wrapped key `c`:  
   `c = AES-256-CTR(msg = ptk, key = Ek, nonce = n2)`
5. Calculate the authentication tag `t` as:
   `t = HMAC-SHA384(msg = h || n || c, key = Ak)`
6. Return `base64url(t || n || c)`.

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

1. Generate a 256 bit (32 bytes) random nonce, `n`.
2. Derive the encryption key `Ek` and XChaCha nonce `n2` as:
   ```
   x = crypto_generichash(msg = 0x80 || n, key = wk, length = 56)
   Ek = x[0:32]
   n2 = x[32:]
   ```
3. Derive the authentication key `Ak` as:
   `Ak = crypto_generichash(msg = 0x81 || n, key = wk)`
4. Encrypt the plaintext key `ptk` with `Ek` and `n2` to obtain the
   wrapped key `c`:  
   `c = XChaCha20(msg = ptk, key = Ek, nonce = n2)`
5. Calculate the authentication tag `t` as:
   `t = crypto_generichash(msg = h || n || c, key = Ak)`
6. Return `base64url(t || n || c)`.

#### V2/V4 Decryption

Given a base64url-encoded encrypted key `b`, and the wrapping key `wk`:

1. Decode `b` from Base64url. The first 32 bytes of the decoded bytes will
   be the authentication tag `t`. The next 32 bytes will be the nonce `n`.
   The remaining bytes will be the wrapped key, `c`.
2. Derive the authentication key `Ak` as:
   `Ak = crypto_generichash(msg = 0x81 || n, key = wk)`
3. Realculate the authentication tag `t2` as:
   `t2 = crypto_generichash(msg = h || n || c, key = Ak)`
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
7. Return `ptk`.
