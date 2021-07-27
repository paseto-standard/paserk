# PASERK Type: seal

This PASERK is a secret key intended for `local` PASETOs, encrypted with an
asymmetric wrapping key.

## Format

    k[version].seal.[data]

### ID Serialization

See [lid](lid.md).

## Operations

See [PKE](../operations/PKE.md) for the algorithms used to perform
asymmetric encryption.

When serializing, `[data]` will be the base64url-encoded payload consisting
of the return values of the appropriate Encryption operation from the
[PKE](../operaitons/PKE.md) document, for this PASERK version.
These values will be concatenated together in the order the PKE document specifies
prior to base64url encoding.
