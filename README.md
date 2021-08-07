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

| PASERK Type                           | Meaning                                                                     | PASETO Compatibility | \[data\] Encoded? | Safe in Footer? | 
|---------------------------------------|-----------------------------------------------------------------------------|----------------------|-------------------|-----------------|
| [`lid`](types/lid.md)                 | Unique Identifier for a separate PASERK for `local` PASETOs.                | `local`              | Yes               | Yes             |
| [`local`](types/local.md)             | Symmetric key for `local` tokens.                                           | `local`              | Yes               | **No**          |
| [`seal`](types/seal.md)               | Symmetric key wrapped using asymmetric encryption.                          | `local`              | Yes               | Yes             |
| [`local-wrap`](types/local-wrap.md)   | Symmetric key wrapped by another symmetric key.                             | `local`              | No                | Yes             |
| [`local-pw`](types/local-pw.md)       | Symmetric key wrapped using password-based encryption.                      | `local`              | Yes               | Yes             |
| [`sid`](types/sid.md)                 | Unique Identifier for a separate PASERK for `public` PASETOs. (Secret Key)  | `public`             | Yes               | Yes             |
| [`public`](types/public.md)           | Public key for verifying `public` tokens.                                   | `public`             | Yes               | **No**          |
| [`pid`](types/pid.md)                 | Unique Identifier for a separate PASERK for `public` PASETOs. (Public Key)  | `public`             | Yes               | Yes             |
| [`secret`](types/secret.md)           | Secret key for signing `public` tokens.                                     | `public`             | Yes               | **No**          |
| [`secret-wrap`](types/secret-wrap.md) | Asymmetric secret key wrapped by another symmetric key.                     | `public`             | No                | Yes             |
| [`secret-pw`](types/secret-pw.md)     | Asymmetric secret key wrapped using password-based encryption.              | `public`             | Yes               | Yes             |

The version of a PASERK **MUST** match the version of the PASETO it's used with.
For example, a `k2.local.` PASERK must be used with `v2.local.` tokens.
Cross-version support is explicitly **NOT** permitted.

Each [**type**](types) is a composition of one or more [**operations**](operations).

As one of the design criteria, it **MUST** be safe for users to store the PASERK ID
(resulting in `lid`, `sid`, or `pid` types) in the footer (or, in v3/v4 of PASETO, as
an implicit assertion).

Any of these PASERK types **MAY** be stored directly in the PASETO footer, because the
encoded key is also wrapped with additional layers of cryptography. The wrapping key
**MUST NOT** be disclosed: `local-wrap`, `seal`, `secret-wrap`.

All other types **MUST NOT** be included in a PASETO footer.

## Examples of Valid PASERKs in a PASETO Footer

```json
{"kid":"k4.lid.iVtYQDjr5gEijCSjJC3fQaJm7nCeQSeaty0Jixy8dbsk"}
```
```json
{"kid":"k4.pid.9ShR3xc8-qVJ_di0tc9nx0IDIqbatdeM2mqLFBJsKRHs"}
```
```json
{"kid":"k4.sid.gHYyx8y5YzqKEZeYoMDqUOKejdSnY_AWhYZiSCMjR1V5"}
```

The PASERK key ID is always okay to include in a PASETO footer.

-----

```json
{"kid":"k4.local-wrap.pie.pu-fBxwoXrICYjeumh77cJ6la4svNGrjshQ7W_ygiJzm80LQBB1e6yqODDq6HO8c0UNY_dzLkzZC62Z81eleoIYUChwymEx23KbTQDinWaOQoKkRantNkrD5o0eo8iCS"}
```
```json
{"kid":"k4.k4.secret-wrap.pie.jLhVAJYWaOcKiFvnKv6kFEQxSGV9BQuW1Qt4jRwr6yHiNeQf2h1GQ0czBJZpveX5T0R0YZv2OEenf8uyLqwamDJUbtS-GdYp_TXT1OJCwGJb2UpEHvcSOciH2PVCEiTrLM9n_mAI4SWXDfw4xYenmINDhi8EiPaPKvsOU64YBvY"}
```

Wrapped keys are encrypted. As long as the wrapping key is confidential, this is safe.

-----

```json
{"kid":"k4.seal.3-VOL4pX5b7eV3uMhYHfOhJNN77YyYtd7wYXrH9rRucKNmq0aO-6AWIFU4xOXUCBk0mzBZeWAPAKrvejqixqeRXm-MQXt8yFGHmM1RzpdJw80nabbyDIsNCpBwltU-uj"}
```

Sealed keys are wrapped using public-key encryption. As long as the unwrapping key (asymmetric
secret key) is confidential, this is safe.

## Examples of Invalid PASERKs in a PASETO Footer

```json
{"kid":"k4.local.cHFyc3R1dnd4eXp7fH1-f4CBgoOEhYaHiImKi4yNjo8"}
```

This is a `local` type, and it is forbidden. Critically, it would disclose a symmetric
key if included in an unencrypted PASETO footer.

```json
{"kid":"k3.secret.cHFyc3R1dnd4eXp7fH1-f4CBgoOEhYaHiImKi4yNjo-QkZKTlJWWl5iZmpucnZ6f"}
```

This is a `secret` type, and it is forbidden. Critically, it would disclose an asymmetric
secret key if included in an unencrypted PASETO footer.

```json
{"kid":"k3.public.AnBxcnN0dXZ3eHl6e3x9fn-AgYKDhIWGh4iJiouMjY6PkJGSk5SVlpeYmZqbnJ2enw"}
```

This is [forbidden by the PASETO specification](https://github.com/paseto-standard/paseto-spec/blob/master/docs/02-Implementation-Guide/01-Payload-Processing.md#key-id-support):

> **IMPORTANT**: Key identifiers MUST be independent of the actual keys
> used by Paseto.
>
> For example, you MUST NOT just drop the public key into the footer for
> a `public` token and have the recipient use the provided public key.
> Doing so would allow an attacker to simply replace the public key with
> one of their own choosing, which will cause the recipient to simply
> accept any signature for any message as valid, which defeats the
> security goals of public-key cryptography.
>
> Instead, it's recommended that implementors and users use a unique
> identifier for each key (independent of the cryptographic key's contents
> itself) that is used in a database or other key-value store to select
> the apppropriate cryptographic key. These search operations MUST fail
> closed if no valid key is found for the given key identifier.

Even though a public key might seem harmless, it risks the encouragement of
in-band negotiation. This could result in users trusting the attacker-provided
public key associated with a PASETO, which would bypass the security offered by
the security token. Therefore, this use case **MUST NOT** ever be permitted.

-----

```json
{"kid":"k4.local-pw._bru5tnkPSFXOtKhBTmW4gAAAAAEAAAAAAAAAgAAAAGKI3PyFS2vyQ9o5qowCR_GUXskLmdV1bjjc3vqnbwN7hVG1lAUCGjElTGIoH-K6lnkHnP4uaFBKWEtB3xFEGzAjzBSnl_JBmwLYK5jstjAV6LnJm_NOt0j"}
```
```json
{"kid":"k4.secret-pw.dkyi7kfzHnVTCqTq1AvLyQAAAAAQAAAAAAAAAwAAAAFWNLgB_yXNkk4W9NiXgeTkNnB3Vjuk_-TFQ-vMUxNX-Ha3k42djov9rHVykMHkrSGUemYFwpot9uNHnXOWtJCVIwdYAwZmt_uRSJ2rRTElanT6mWXojuBUy2k1lxD-iZ10pVPkJ-Kvv_SLEhLQ8RS7wqFW8RfFGyw"}
```

Password-based key-wrapping is provided in PASERK for systems that need
password-protected cryptographic secrets, but password-protected keys **MUST NOT**
be included in a PASETO header.

## PASERK Implementations

* [**PHP** (Reference Implementation)](https://github.com/paragonie/paserk-php)
