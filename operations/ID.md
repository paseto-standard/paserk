# PASERK IDs

This operation calculates the unique ID for a given PASERK.

## Related Types

* [`lid`](../types/lid.md) for `local` tokens
* [`pid`](../types/pid.md) for `public` tokens

## PASERK Versions

### Versions 1 and 3

Set `h` to the version and token type (for example, `k3.lid.` for
any PASERK intended for `v3.local.` tokens), with a trailing period.

Calculate the SHA-384 hash of `h` and the entire PASERK string,
then truncate to 264 bits (33 bytes, which will encode evenly into a
44-byte base64url-encoded string) to obtain the data `d`
(`d = SHA384(h || p)[0:33]`).

Return `h || base64url(d)`.

### Versions 2 and 4

Set `h` to the version and token type (for example, `k4.pid.` for
any PASERK intended for `v4.public.` tokens), with a trailing period.

Calculate the BLAKE2b-264 hash of `h` and the entire PASERK string
to obtain the data `d` (`d = crypto_generichash(h || p, 33)`).
When base64url-encoded, `d` will produce an unpadded 44-byte string.

Return `h || base64url(d)`.
