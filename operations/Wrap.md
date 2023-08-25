# Key-Wrapping (with Symmetric-Key Cryptography)

PASERK uses symmetric-key encryption to wrap PASETO keys.

This is the most flexible mode in PASERK, as it offers a way to integrate
PASERK with multiple key management solutions.

## Related Types

* [`local-wrap`](../types/local-wrap.md) for `local` tokens
* [`secret-wrap`](../types/secret-wrap.md) for `public` tokens

Unlike other PASERKs, the `[data]` portion is not base64url-encoded when
with these types.

## Data Format

The `[data]` payload described in the respective type above will further
be segmented as follows.

    [prefix].[encrypted key]

Where `[prefix]` is a distinct lowercase alphanumeric string that
uniquely identifies the key-wrapping encryption protocol in use.

### Custom Key-Wrapping Protocol Guidelines

PASERK Implementors **MAY** define custom wrapping protocols, but they **MUST**
support the PASERK standard wrapping protocol (`pie`).

Implementors **SHOULD** register their prefix in this document so that
interoperability can be assured by other implementations.

Implementations **MUST** authenticate the `version`, `type`, and `prefix`
alongside their ciphertext. Implementors **MAY** authenticate additional
data beyond what we require.

Implementations **MUST** return an ASCII string. 

Implementations **SHOULD** Return a URL-safe string (i.e., using base64url
from [RFC 4648](https://datatracker.ietf.org/doc/html/rfc4648)).

### Registered Prefixes

| Prefix    | Key-Wrapping Protocol                            | Owner                                                   |
|-----------|--------------------------------------------------|---------------------------------------------------------|
| `aws-kms` | [AWS Key Management Service](Wrap/aws-kms.md)    | [Amazon Web Services](https://aws.amazon.com)           |
| `pie`     | [PASERK standard wrapping protocol](Wrap/pie.md) | [Paragon Initiative Enterprises](https://paragonie.com) |
