# PKE (Public-Key Encryption)

PASERK uses Public-Key encryption to wrap symmetric keys for use in `local` tokens.

## Related Types

* [`seal`](../types/seal.md)

## PASERK Versions

### Version 1

Algorithms: RSA-KEM, AES-256-CTR, SHA-384, HMAC-SHA-384

The constant `h` in the algorithms below will be set to `k1.local.`
(with the trailing period), and the unwrapped key MUST only be used for
`v1.local.` PASETOs.

The RSA keys used for public-key encryption in PASERK are necessarily larger
than the keys used by PASETO. This is an intentional design decision intended
to prevent the use of an RSA keypair for both signing and encryption.

* PASETO v1.public uses 2048-bit RSA, and can only used to sign.
* PASERK v1.seal supports 4096-bit RSA, and can only be used to encrypt.

#### V1 Encryption

Given a plaintext data key (`pdk`) and RSA public key (`pk`) (which **MUST** have
a modulus `n` larger than 2^4095 and smaller than 2^4096 + 1 and a public exponent
`e` equal to 65537).

1. Generate a random number `r` between 0 and the modulus of the public key (`pk`).
2. RSA-encrypt `r` with the `pk`, with no padding mode, to get the RSA ciphertext `c`.
3. Calculate (`Ek`, `n`) as `HMAC-SHA-384(msg = 0x01 || h || r, key = SHA384(c))`.
   The leftmost 256 bits will be the encryption key (`Ek`). The remaining 128 bits
   will be the initial counter (`n)`.
4. Calculate `Ak` as `HMAC-SHA-384(msg = 0x02 || h || r, key = SHA384(c))`.
5. Encrypt the plaintext data key (`pdk`) with `Ek` and `n`, using AES-256-CTR.
   This will be the encrypted data key (`edk`).
6. Calculate the auth tag, `t`, as `HMAC-SHA-384(msg = h || c || edk, key = Ak)`.
7. Return `t`, `edk`, `c`.

#### V1 Decryption

Given an RSA secret key (`sk`), encrypted data key (`edk`),
RSA ciphertext (`c`), and auth tag (`t`).

1. Decrypt `c` using `sk` to get `r`.
2. Calculate `Ak` as 
   `hmac_sha384(msg = 0x02 || h || r, key = SHA384(c))`.
3. Recalculate the auth tag (`t2`) as 
   `hmac_sha384(msg = h || c || edk, key = Ak)`.
4. Compare `t2` with `t`, using a constant-time compare function. If it does not
   match, abort. 
5. Calculate (`Ek`, `n`) as 
   `hmac_sha384(msg = 0x01 || h || r, key = SHA384(c))`.
   The leftmost 256 bits will be the encryption key (`Ek`). The remaining 128 bits
   will be the initial counter (`n)`.
6. Decrypt the encrypted data key (`edk`) with `Ek` and `n`, using AES-256-CTR.
   This will result in the plaintext data key (`pdk`).
7. Return `pdk`.

### Version 2 and 4

Algorithms: X25519, XChaCha20, BLAKE2b

For version 2, the constant `h` in the algorithms below will be set to `k2.local.`
(with the trailing period), and the unwrapped key MUST only be used for
`v2.local.` PASETOs.

For version 4, the constant `h` in the algorithms below will be set to `k4.local.`
(with the trailing period), and the unwrapped key MUST only be used for
`v2.local.` PASETOs.

#### V2/V4 Encryption

Given a plaintext data key (`pdk`), and an Ed25519 public key (`pk`).

1. Calculate the birationally equivalent X25519 public key (`xpk`) from `pk`.
2. Generate a random, ephemeral X25519 keypair (`esk`, `epk`).
3. Calculate the shared secret `xk` from 
   `crypto_scalarmult(esk, xpk)`.
4. Calculate the encryption key `Ek` from
   `BLAKE2b(0x01 || h || xk || epk || xpk)`.
5. Calculate the authentication key `Ak` from 
   `BLAKE2b(0x02 || h || xk || epk || xpk)`.
6. Calculate the nonce `n` from 
   `BLAKE2b-192(epk || xpk)`.
7. Encrypt the plaintext data key (`pdk`) as 
   `XChaCha20(msg = pdk, nonce=n, key=Ek)`.
   This will be the encrypted data key (`edk`).
8. Calculate the auth tag `t` as
   `BLAKE2b(msg = h || epk || edk, key=Ak)`.
9. Return `t`, `epk`, and `edk`.

#### V2/V4 Decryption

Given a sender's ephemeral public key (`epk`), encrypted data key (`edk`),
auth tag (`t`), and Ed25519 secret key (`sk`).

1. Calculate the birationally equivalent X25519 secret key (`xsk`) from `sk`.
2. Calculate the shared secret `xk` from `crypto_scalarmult(xsk, epk)`.
3. Calculate the authentication key `Ak` from
   `BLAKE2b(0x02 || h || xk || epk || xpk)`.
4. Recalculate the auth tag `t2` as `BLAKE2b(msg = h || epk || edk, key=Ak)`.
5. Compare `t2` with `t`, using a constant-time compare function. If it does not
   match, abort.
6. Calculate the encryption key `Ek` from `BLAKE2b(0x01 || h || xk || epk || xpk)`.
7. Calculate the nonce `n` from `BLAKE2b-192(epk || xpk)`.
8. Decrypt the encrypted data key (`edk`) with `Ek` and `n`, using XChaCha20.
   This will result in the plaintext data key (`pdk`).
9. Return `pdk`.

### Version 3

Algorithms: ECDH over NIST P-384, SHA-384, AES-256-CTR, HMAC-SHA-384

The constant `h` in the algorithms below will be set to `k3.local.`
(with the trailing period), and the unwrapped key MUST only be used for
`v3.local.` PASETOs.

#### V3 Encryption

Given a plaintext data key (`pdk`), and a compressed P-384 public key (`pk`).

1. Generate a random, ephemeral P-384 keypair (`esk`, `epk`).
2. Calculate the shared secret `xk` from `ecdh_p384(esk, pk)`.
3. Calculate the encryption key `Ek` and nonce `n` from 
   `SHA384(0x01 || h || xk || epk || xpk)`.
   The leftmost 256 bits (32 bytes) will be `Ek`.
   The remaining 128 bits (16 bytes) will be `n`.
4. Calculate the authentication key `Ak` from 
   `SHA384(0x02 || h || xk || epk || xpk)`.
5. Encrypt the plaintext data key (`pdk`) as
   `aes256ctr_encrypt(msg = pdk, nonce=n, key=Ek)`.
   This will be the encrypted data key (`edk`).
6. Calculate the auth tag `t` as
   `hmac_sha384(msg = h || epk || edk, key=Ak)`.
7. Return `t`, `epk`, and `edk`.

#### V3 Decryption

Given a sender's ephemeral public key (`epk`), encrypted data key (`edk`),
auth tag (`t`), and P-384 secret key (`sk`).

1. Calculate the shared secret `xk` from `ecdh_p384(sk, epk)`.
2. Calculate the authentication key `Ak` from
   `SHA384(0x02 || h || xk || epk || xpk)`.
3. Recalculate the auth tag `t2` as
   `hmac_sha384(msg = h || epk || edk, key=Ak)`.
4. Compare `t2` with `t`, using a constant-time compare function.
   If it does not match, abort.
5. Calculate the encryption key `Ek` and nonce `n` from
   `SHA384(0x01 || h || xk || epk || xpk)`.
   The leftmost 256 bits (32 bytes) will be `Ek`.
   The remaining 128 bits (16 bytes) will be `n`.
6. Decrypt the encrypted data key (`edk`) with `Ek` and `n`, using AES-256-CTR.
   This will result in the plaintext data key (`pdk`).
7. Return `pdk`.

#### V3 Public Key Encoding

Public keys **MUST** use [point compression](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.202.2977&rep=rep1&type=pdf)
with PASERK.

```
if Y is even:
    [0x02] || [X]
if Y is odd:
    [0x03] || [X]
```
