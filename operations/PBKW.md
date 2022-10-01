# PBKD (Password-Based Key Wrapping)

Derive a unique encryption key from a password, then use it to wrap
the key.

## Related Types

* [`local-pw`](../types/local-pw.md) for `local` tokens
* [`secret-pw`](../types/secret-pw.md) for `public` tokens

## Definitions

**crypto_generichash()** is BLAKE2b with a variable-length output that defaults
to 256 bits (32 bytes), as implemented in [libsodium](https://libsodium.gitbook.io/doc/hashing/generic_hashing).

**long2bytes()** converts a 64-bit unsigned integer into a sequence of 8 octets
(big-endian byte order).

**int2bytes()** converts a 32-bit unsigned integer into a sequence of 4 octets
(big-endian byte order).

## PASERK Versions

### Versions 1 and 3

Algorithms: PBKDF2, HMAC-SHA384, AES-256-CTR, SHA-384

The header `h` will depend on the exact version and mode being used
(with a trailing period). It will be one of
(`k1.local-pw.`, `k1.secret-pw.`, `k3.local-pw.`, `k3.secret-pw.`).

#### V1/V3 Encryption

Given a plaintext key (`ptk`), password (`pw`), and iteration
count (`i`, defaults to 100,000):

1. Generate a random 256-bit (32 byte) salt (`s`).
2. Derive the 256-bit (32 byte) pre-key `k` from the password and salt.
   `k = PBKDF2-SHA384(pw, s, i)`
3. Derive the encryption key (`Ek`) from `SHA-384(0xFF || k)`, truncated to
   the 32 most significant bytes (`Ek = hash[0:31]`).
4. Derive the authentication key (`Ak`) from `SHA-384(0xFE || k)`.
5. Generate a random 128-bit nonce (`n`).
6. Encrypt the plaintext key `ptk` with `Ek` and `n` to obtain the 
   encrypted data key `edk`.
   `edk = AES-256-CTR(msg=ptk, key=Ek, nonce=n)`
7. Calculate the authentication tag `t` over 
   `h`, `s`, `i`, `n`, and `edk`.
   ```
   t = HMAC-SHA-384(
       msg = h || s || int2bytes(i) || n || edk,
       key = Ak
   )
   ```
8. Return `h`, `s`, `i`, `n`, `edk`, `t`.

#### V1/V3 Decryption

Given a password (`pw`), salt (`s`), iteration count (`i`), nonce (`n`),
encrypted data key (`edk`), and authentication tag `t`:

1. Assert that the header `h` is correct for the expected version of the wrapped key.
2. Derive the pre-key `k` from the password and salt.
   `k = PBKDF2-SHA384(pw, s, i)`
3. Derive the authentication key (`Ak`) from `SHA-384(0xFE || k)`.
4. Recalculate the authentication tag `t2` over
   `h`, `s`, `i`, `n`, and `edk`.
   ```
   t2 = HMAC-SHA-384(
       msg = h || s || int2bytes(i) || n || edk,
       key = Ak
   )
   ```
5. Compare `t` with `t2` using a constant-time string comparison function.
   If it fails, abort.
6. Derive the encryption key (`Ek`) from `SHA-384(0xFF || k)`.
7. Decrypt the encrypted key (`edk`) with `Ek` and `n` to obtain the
   plaintext key `ptk`.
   `ptk = AES-256-CTR(msg=edk, key=Ek, nonce=n)`
8. Return `ptk`


### Versions 2 and 4

Algorithms: Argon2id, BLAKE2b, XChaCha20

The header `h` will depend on the exact version and mode being used
(with a trailing period). It will be one of 
(`k2.local-pw.`, `k2.secret-pw.`, `k4.local-pw.`, `k4.secret-pw.`).

#### V2/V4 Encryption

Given a plaintext key (`ptk`), password (`pw`), memory cost (`mem`),
time cost (`time`), and parallelism degree (`para`):

1. Generate a random 128-bit (16 byte) salt (`s`).
2. Derive the 256-bit (32 byte) pre-key `k` from the password and salt.
   `k = Argon2id(pw, s, mem, time, para)`
3. Derive the encryption key (`Ek`) from `crypto_generichash(0xFF || k)`.
4. Derive the authentication key (`Ak`) from `crypto_generichash(0xFE || k)`.
5. Generate a random 192-bit (24 byte) nonce (`n`).
6. Encrypt the plaintext key (`ptk`) with `Ek` and `n` to obtain the
   encrypted data key `edk`.
   `edk = XChaCha20(msg=ptk, key=Ek, nonce=n)`
7. Calculate the authentication tag `t` over
   `h`, `s`, `mem`, `time`, `para`, `n`, and `edk`.
   ```
   t = crypto_generichash(
       msg = h || s || long2bytes(mem) || int2bytes(time) || int2bytes(para) || n || edk,
       key = Ak,
       length = 32 # 32 bytes, 256 bits
   )
   ```
8. Return `h`, `s`, `mem`, `time`, `para`, `n`, `edk`, `t`.

#### V2/V4 Decryption

Given a password (`pw`), salt (`s`), memory cost (`mem`),
time cost (`time`), parallelism degree (`para`), nonce (`n`),
encrypted data key (`edk`), and authentication tag `t`:

1. Assert that the header `h` is correct for the expected version of the wrapped key.
2. Derive the pre-key `k` from the password and salt.
   `k = Argon2id(pw, s, mem, time, para)`
3. Derive the authentication key (`Ak`) from `crypto_generichash(0xFE || k)`.
4. Recalculate the authentication tag `t2` over
   `h`, `s`, `mem`, `time`, `para`, `n`, and `edk`.
   ```
   t2 = crypto_generichash(
       msg = h || s || long2bytes(mem) || int2bytes(time) || int2bytes(para) || n || edk,
       key = Ak,
       length = 32 # 32 bytes, 256 bits
   )
   ```
5. Compare `t` with `t2` using a constant-time string comparison function.
   If it fails, abort.
6. Derive the encryption key (`Ek`) from `crypto_generichash(0xFF || k)`.
7. Decrypt the encrypted key (`edk`) with `Ek` and `n` to obtain the
   plaintext key `ptk`.
   `ptk = XChaCha20(msg=edk, key=Ek, nonce=n)`
8. Return `ptk`.
