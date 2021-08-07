# PASERK Type: public

This is a plaintext serialization of a public key for PASETO `public` tokens.

## Format

    k[version].public.[data]

### ID Serialization

See [pid](pid.md).

## PASERK Versions

### Version 1

The `[data]` portion will be DER ASN.1 PKCS#1 RSA Public Key
**WITHOUT** PEM encoding (base64 and the `-----` prefixes/suffixes).

### Versions 2 and 4

The `[data]` portion will be the Ed25519 public key as raw bytes.

### Version 3

The `[data]` portion will be the compressed P-384 public key, as specified in
[section 4.3.6, step 2.2 of this document](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.202.2977&rep=rep1&type=pdf).

```
if Y is even:
    [0x02] || [X]
if Y is odd:
    [0x03] || [X]
```

Here, the X coordinate is expressed in big endian byte order.

## Examples

```
k1.public.MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyaTgTt53ph3p5GHgwoGWwz5hRfWXSQA08NCOwe0FEgALWos9GCjNFCd723nCHxBtN1qd74MSh_uN88JPIbwxKheDp4kxo4YMN5trPaF0e9G6Bj1N02HnanxFLW-gmLbgYO_SZYfWF_M8yLBcu5Y1Ot0ZxDDDXS9wIQTtBE0ne3YbxgZJAZTU5XqyQ1DxdzYyC5lF6yBaR5UQtCYTnXAApVRuUI2Sd6L1E2vl9bSBumZ5IpNxkRnAwIMjeTJB_0AIELh0mE5vwdihOCbdV6alUyhKC1-1w_FW6HWcp_JG1kKC8DPIidZ78Bbqv9YFzkAbNni5eSBOsXVBKG78Zsc8owIDAQAB
``` 

```
k2.public.cHFyc3R1dnd4eXp7fH1-f4CBgoOEhYaHiImKi4yNjo8
```

```
k3.public.AnBxcnN0dXZ3eHl6e3x9fn-AgYKDhIWGh4iJiouMjY6PkJGSk5SVlpeYmZqbnJ2enw
```

```
k4.public.cHFyc3R1dnd4eXp7fH1-f4CBgoOEhYaHiImKi4yNjo8
```
