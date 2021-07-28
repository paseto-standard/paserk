# PASERK: Platform-Agnostic Serialized Keys

PASERK is an extension to [PASETO](https://paseto.io) that provides key-wrapping and
serialization.

## Motivation

PASETO provides two types of tokens (called a purpose) in each of its versions:

| Purpose  | Cryptographic Operation                                            |
|----------|--------------------------------------------------------------------|
| `local`  | Symmetric-key authenticated encryption with additional data (AEAD) |
| `public` | Asymmetric-key digital signatures (**no encryption**)              |

These two token modes solve at least 80% of use cases for secure tokens. You can
even solve *unencrypted* symmetric-key authentication by storing your claims in
the unencrypted footer, rather than encrypting them.

The use-cases that PASETO doesn't address out of the box are:

* Key-wrapping
* Asymmetric encryption
* Password-based key encryption

PASERK aims to provide an answer for these circumstances, as well as provide a
consistent standard for the encoding of PASETO keys.

## PASERK

A serialized key in PASERK has the format:

    k[version].[type].[data]

Where `[version]` is an integer, `[data]` is the (*typically* base64url-encoded)
payload data, and `[type]` is one of the items in the following table:

| PASERK Type                           | Meaning                                                                     | PASETO Compatibility | \[data\] Encoded? |  
|---------------------------------------|-----------------------------------------------------------------------------|----------------------|-------------------|
| [`lid`](types/lid.md)                 | Unique Identifier for a separate PASERK for `local` PASETOs.                | `local`              | Yes               |
| [`local`](types/local.md)             | Symmetric key for `local` tokens.                                           | `local`              | Yes               |
| [`seal`](types/seal.md)               | Symmetric key wrapped using asymmetric encryption.                          | `local`              | Yes               |
| [`local-wrap`](types/local-wrap.md)   | Symmetric key wrapped by another symmetric key.                             | `local`              | No                |
| [`local-pw`](types/local-pw.md)       | Symmetric key wrapped using password-based encryption.                      | `local`              | Yes               |
| [`pid`](types/pid.md)                 | Unique Identifier for a separate PASERK for `public` PASETOs. (Public Key)  | `public`             | Yes               |
| [`sid`](types/sid.md)                 | Unique Identifier for a separate PASERK for `public` PASETOs. (Secret Key)  | `public`             | Yes               |
| [`public`](types/public.md)           | Public key for verifying `public` tokens.                                   | `public`             | Yes               |
| [`secret`](types/secret.md)           | Secret key for signing `public` tokens.                                     | `public`             | Yes               |
| [`secret-wrap`](types/secret-wrap.md) | Asymmetric secret key wrapped by another symmetric key.                     | `public`             | No                |
| [`secret-pw`](types/secret-pw.md)     | Asymmetric secret key wrapped using password-based encryption.              | `public`             | Yes               |

The version of a PASERK **MUST** match the version of the PASETO it's used with.
For example, a `k2.local.` PASERK must be used with `v2.local.` tokens.
Cross-version support is explicitly **NOT** permitted.

Each [**type**](types) is a composition of one or more [**operations**](operations).

As one of the design criteria, it must be safe for PASERKs to be sent out-of-band 
with its associated PASETO, or included in the footer of a PASETO.

When PASERKs are not included in the footer,  users **SHOULD** include the PASERK 
ID in the footer (or, in v3/v4 of PASETO, as an implicit assertion).

## PASERK Implementations

* [**PHP** (Reference Implementation)](https://github.com/paragonie/paserk-php)
