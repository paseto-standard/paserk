# Key-Wrapping: aws-kms

This document describes a specific type of [key-wrapping](../Wrap.md)
supported by PASERK. See the parent document for more information.

## Algorithm Lucidity

### Encryption (Key Wrapping)

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

## PASERK Versions

Key wrapping with AWS KMS only supports PASERK Version 3 and 4 and newer, rather than 
either of the two legacy protocol versions. 

### Versions 3 and 4

Algorithms: Whatever KMS does under-the-hood. We treat it as a black box.

The header `h` will depend on the exact version and mode being used
, as well as the prefix `aws-kms` (with a trailing period). It will be one of
(`k3.local-wrap.aws-kms.`, `k3.secret-wrap.aws-kms.`, `k4.local-wrap.aws-kms.`, 
`k4.secret-wrap.aws-kms.`).

#### KMS Encryption

Given a plaintext key `ptk`, a KMS Key `kmskey`, and an optional KMS Encryption Context `ec`:

1. [Enforce Algorithm Lucidity](#algorithm-lucidity)
2. Call `Kms:Encrypt` with the following parameters, then set the result in `edk`:
   * `KeyId` must be set to the ARN or KeyID for `kmskey` 
   * `Plaintext` = `ptk`
   * `EncryptionContext` = `ec` with one additional field:
     * `PaserkHeader` MUST be set to `h`
3. Return `base64url(edk)`

### KMS Decryption

Given a base64url-encoded encrypted key `b`, a KMS Key `kmskey`, and an optional KMS Encryption Context `ec`:

1. [Enforce Algorithm Lucidity](#algorithm-lucidity)
2. Decode `b` from Base64url.
3. Call `Kms:Decrypt` with the following parameters, then set the result in `ptk`:
    * `KeyId` must be set to the ARN or KeyID for `kmskey`
    * `CiphertextBlob` = `b`
    * `EncryptionContext` = `ec` with one additional field:
        * `PaserkHeader` MUST be set to `h`
4. Return `ptk`

## Encryption Context

Libraries are encouraged to allow users an optional interface to provide custom Encryption Context parameters.
