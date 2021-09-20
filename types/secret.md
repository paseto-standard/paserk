# PASERK Type: secret

This is a plaintext serialization of a secret key for PASETO `public` tokens.

## Format

    k[version].secret.[data]

### ID Serialization

See [sid](sid.md).

### Version 1

The `[data]` portion will be DER ASN.1 PKCS#1 RSA Private Key,
**WITHOUT** PEM encoding (base64 and the `-----` prefixes/suffixes).

### Versions 2 and 4

The `[data]` portion will be the Ed25519 secret key as raw bytes.

Here, Ed25519 secret key means the clamped 32-byte seed followed by the 32-byte public key,
as used in the NaCl and libsodium APIs, rather than just the clamped 32-byte seed.

### Version 3

The `[data]` portion will be the P-384 secret key as raw bytes.

## Examples

```
k1.secret.MIIEowIBAAKCAQEAyaTgTt53ph3p5GHgwoGWwz5hRfWXSQA08NCOwe0FEgALWos9GCjNFCd723nCHxBtN1qd74MSh_uN88JPIbwxKheDp4kxo4YMN5trPaF0e9G6Bj1N02HnanxFLW-gmLbgYO_SZYfWF_M8yLBcu5Y1Ot0ZxDDDXS9wIQTtBE0ne3YbxgZJAZTU5XqyQ1DxdzYyC5lF6yBaR5UQtCYTnXAApVRuUI2Sd6L1E2vl9bSBumZ5IpNxkRnAwIMjeTJB_0AIELh0mE5vwdihOCbdV6alUyhKC1-1w_FW6HWcp_JG1kKC8DPIidZ78Bbqv9YFzkAbNni5eSBOsXVBKG78Zsc8owIDAQABAoIBAF22jLDa34yKdns3qfd7to-C3D5hRzAcMn6Azvf9qc-VybEI6RnjTHxDZWK5EajSP4_sQ15e8ivUk0JoWdJ53feL-hnQvwsab28gghSghrxM2kGwGA1XgO-SVawqJt8SjvE-Q-__01ZKK0OyA0cDJjX3L9RoPUN_moMeAPFw0hqkFEhm72GSVCEY1eY-cOXmL3icxnsnlUD__SS9q33RxF2y5oiW1edqcRqhW_7L1yYMbxHFUcxWh8WUwjn1AAhoCOUzF8ZB-0X_PPh-1nYoq6xwqL0ZKDwrQ8SDhW_rNDLeO9gic5rl7EetRQRbFvsZ40AdsX2wU-lWFUkB42AjuoECgYEA5z_CXqDFfZ8MXCPAOeui8y5HNDtu30aR-HOXsBDnRI8huXsGND04FfmXR7nkghr08fFVDmE4PeKUk810YJb-IAJo8wrOZ0682n6yEMO58omqKin-iIUVrPXLSLo5CChrqw2J4vgzolzPw3N5I8FJdLomb9FkrV84H-IviPIylyECgYEA3znwAG29QX6ATEfFpGVOcogorHCntd4niaWCq5ne5sFL-EwLeVc1zD9yj1axcDelICDZxCZynU7kDnrQcFkT0bjH_gC8Jk3v7XT9l1UDDqC1b7rm_X5wFIZ_rmNa1rVZhL1o_tKx5tvM2syJ1q95v7NdygFIEIW-qbIKbc6Wz0MCgYBsUZdQD-qx_xAhELX364I2epTryHMUrs-tGygQVrqdiJX5dcDgM1TUJkdQV6jLsKjPs4Vt6OgZRMrnuLMsk02R3M8gGQ25ok4f4nyyEZxGGWnVujn55KzUiYWhGWmhgp18UCkoYa59_Q9ss-gocV9hB9j9Q43vD80QUjiF4z0DQQKBgC7XQX1VibkMim93QAnXGDcAS0ij-w02qKVBjcHkb9mMBhz8GAxGOIu7ZJafYmxhwMyVGB0I1FQeEczYCJUKnBYN6Clsjg6bnBT_z5bJx_Jx1qCzX3Uh6vLjpjc5sf4L39Tyye1u2NXQmZPwB5x9BdcsFConSq_s4K1LJtUT3KFxAoGBANGcQ8nObi3m4wROyKrkCWcWxFFMnpwxv0pW727Hn9wuaOs4UbesCnwmpcMTfzGUDuzYXCtAq2pJl64HG6wsdkWmjBTJEpm6b9ibOBN3qFV2zQ0HyyKlMWxIuVSj9gOo61hF7UH9XB6R4HRdlpBOuIbgAWZ46dkj9_HM9ovdP0Iy
``` 

```
k2.secret.cHFyc3R1dnd4eXp7fH1-f4CBgoOEhYaHiImKi4yNjo8c5WpIyC_5kWKhS8VEYSZ05dYfuTF-ZdQFV4D9vLTcNQ
```

```
k3.secret.cHFyc3R1dnd4eXp7fH1-f4CBgoOEhYaHiImKi4yNjo-QkZKTlJWWl5iZmpucnZ6f
```

```
k4.secret.cHFyc3R1dnd4eXp7fH1-f4CBgoOEhYaHiImKi4yNjo8c5WpIyC_5kWKhS8VEYSZ05dYfuTF-ZdQFV4D9vLTcNQ
```
