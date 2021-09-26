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
[PKE](../operations/PKE.md) document, for this PASERK version.
These values will be concatenated together in the order the PKE document specifies
prior to base64url encoding.

## Examples

```
k1.seal.QmXSEH_ei6jX3xvcZ5_l4k53mBTgJSRHWS07MB56tX0hBUqKE-ur-SdjRn68DF_j9BbUSif5kqAn4h7ozfW1IRr4tp26_gVdSN9wUcCMVFpc7kF_unOd8dJlqKuxAxy8pfVRtN5no12JKogWU9EBE1y8iv6k1CDetlU8htQsBhvpcNigBhFooNt8J2UZrNppDSlzO-bqJcz7gPFpqdp31wwCi9PIoaUG52GHTRrm600p6FftQ9V2XdnA5gL0TZQhRG3EstYR9s6p-Irrs7BcqH5QpC0ZRLcnsJHMkSvX79g7fkiJeEHbLRTjE0xUT8sUk6fxgSNJws8XDsBU-4p74OKW3GyZmtbxwhHN6hO4OJHRtV3MoLRJMDUVYCVfDVkEMYXd9kOq9_rRgeP_cCnHhbLncIpEUUfvLB_8OAesU6h88ulKDdsyIs2a7HCrvjfKwni4iz8yAabUf7JTzieoX9zRsYe52i6vmujSgiuiwKS_EbdPTKNiPu308QHfu_bubXYkRXmU6ieziqU1f4FAklxGNK9sZpK_uEYIUrhiT6uqIJmkMU410XFnVwkebd6OGXFoGvvIyaq0iPaF---fHXyCnmI9fyjV0kHy57I18pbDY-ujMUCnjZ-yYYmk2DyhYPURq50yNVptIjx7B8mU660KPWkACO1NUQhIdACDkHseNb7mER71l64A0Hmu9PZB4VHBH4NBhtdvll2vIVcLPk-BdDa1PziJy6QZweIel-IufvLl1awLu67d2D8DJw25NAPuCsdrfZMMqhe02nAmGg
``` 

```
k2.seal.rJZgCJrVrUFRPehByeSoO1wAUxG072_yZcyHKARrNU_ShbDBbW6xKtQl7se_PZZ9z1z484vcWmp0iTWGcVplCj2oZClme5JBCQeYSqc2lDev3xTFgOiRxX71gnnxSBkO
```

```
k3.seal.utQTzb7zGDXC-apbXsIby7yFZxoFFzUb40J1-zszMIjNkf3WLwEmqj9fuQwA8IKfAyWOvtZAxT4--kzAkYEKk492x2bypDps_a1Gsp-L9we22lWEkNVpqA-DWr9uoTRhSWRh87hFNla5ZzrpqMgeP1UOSDGKuRs62gZxFhCzsLka
```

```
k4.seal.3-VOL4pX5b7eV3uMhYHfOhJNN77YyYtd7wYXrH9rRucKNmq0aO-6AWIFU4xOXUCBk0mzBZeWAPAKrvejqixqeRXm-MQXt8yFGHmM1RzpdJw80nabbyDIsNCpBwltU-uj
```
