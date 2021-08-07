# PASERK Type: secret-pw

This PASERK is a secret key intended for `public` PASETOs, encrypted with a password.

## Format

    k[version].secret-pw.[data]

Where `[data]` is the base64url-encoded payload, defined by each version.

### ID Serialization

See [sid](sid.md).

## Operations

See [PBKW](../operations/PBKW.md) for the algorithm used.

## PASERK Versions

### Version 1

See [V1/V3 Encryption in the PBKW document](../operations/PBKW.md#v1v3-encryption)
for how these values are derived.

| Starting Byte | Length | Definition                  |
|---------------|--------|-----------------------------|
| 0             | 32     | PBKDF2-SHA384 Salt (`salt`) |
| 32            | 4      | Iteration count (`i`)       |
| 36            | 16     | AES-CTR nonce (`n`)         |
| 52            | varies | Encrypted Data Key (`edk`)  |
| varies        | 48     | Authentication tag (`t`)    |

Total decoded length: 100 bytes longer than your PEM-encoded RSA private key.

A typical RSA private key, when PEM-encoded, is about 1674 bytes long. 
Therefore, you can estimate the data payload for a `k1.secret-pw` PASERK to be
`1774` bytes. There **MAY** be variability, so parsers should permit some flex.

The iteration count (`i`) MUST be encoded as a 32-bit unsigned
integer in big-endian byte order.

### Versions 2 and 4

See [V2/V4 Encryption in the PBKW document](../operations/PBKW.md#v2v4-encryption)
for how these values are derived.

| Starting Byte | Length | Definition                    |
|---------------|--------|-------------------------------|
| 0             | 16     | Argon2id Salt (`salt`)        |
| 16            | 8      | Argon2id Memory Cost (`mem`)  |
| 24            | 4      | Argon2id Time Cost (`time`)   |
| 28            | 4      | Argon2id Parallelism (`para`) |
| 32            | 24     | XChaCha20 nonce (`n`)         |
| 56            | 64     | Encrypted Secret Key (`esk`)  |
| 120           | 32     | Authentication tag (`t`)      |

Total decoded length: `152 bytes`

The Argon2id parameters (`mem`, `time`, `para`) MUST be encoded as
unsigned integers in big-endian byte order. `mem` is 64-bit; the
other parameters are 32-bit.

### Version 3

See [V1/V3 Encryption in the PBKW document](../operations/PBKW.md#v1v3-encryption)
for how these values are derived.

| Starting Byte | Length | Definition                   |
|---------------|--------|------------------------------|
| 0             | 32     | PBKDF2-SHA384 Salt (`salt`)  |
| 32            | 4      | Iteration count (`i`)        |
| 36            | 16     | AES-CTR nonce (`n`)          |
| 52            | 48     | Encrypted Secret Key (`esk`) |
| 100           | 48     | Authentication tag (`t`)     |

Total decoded length: `148 bytes`

The iteration count (`i`) MUST be encoded as a 32-bit unsigned
integer in big-endian byte order.

## Examples

```
k1.secret-pw.0DZQcctMMWQO_EKykDaXl3LBtEfO5fZOQNio6wgwiQEAAAPoH_FRjm1ayzKKQNxc0T_ph1pHj0cPX8uyDbJqcHFbNbNQyLGrNgHontvyshbUfiBUoAbKhlit9xYl_vcFUyw6B0YUn5hO8BMWSeGRXT3p9070fmPUlB5WBrZVREz5kS8_oEPlcRPqX8dXMtUNDoOo5ag_zXQ_CXgDzkfF9IVSMyGgSWYiwPgTXrmdXPwzAcqez73uAaNNPYu7KmM-cnmwSErW_IckQ36k7vcvtdg048ywr0sGcf4yZ_BvQETyyrikTYrFaCT1mviyKjBMnLNfG6Ga7hiFLJai28B9z1G8jyvArtQGrfZBv5ujlTvW8RfgkK5alQIH3RUSpFXUWH4IJzfkywqp6UzWnqLw_agvepV2YaEcH3XyAy8w5wzMOumKc9veeGVjjgH0703euemehZm-l8EJT-k7H18geABOJjNOQupOEPMQ0E4ZYOu5XjgEEFFAaFLoJyGdi5bqnvyR_KGLdEtgzsqNYi2DEN2UwiJdQU6rCNoHHAuWnrqqH9YGhSI7CQm0TVLtICzJuR-xLMfsZ7VwPSqOj03VKt2ufqlSPc4o7VrddIjWaXDstk8fmrbn_Q1mDp3a-YmwGZvR46kKoNmmACgRw7xWZZUSYezlvOmtbSySLjDJLO-BrqHIAHoOgEpr5yQNCTaQ3qcPAOZ6dkDb0o7BHKb0lAs1opWOIE6niP2E1uOJ7gTmwUsN7tbBNvH7SRhilikmXqQPAHBueqIj2MdrcAfpempewXo0wdx2lUx-dhGy_p1B4X_3tDitbgE4SvOgH8tWD4WVRRr1tHS3TjsI8hQHP7iLoEIxMFwkKljSleNNAJlTowjnfrhXPKmgTEc-w64Wpj32jU0mGQcBFSEwZ_1oGKbM6HcxIKkDRg1Wcd3irkYWmzdlQy4pXgQoGEgGRImoev7l1OFz2cJsWNr-DEzuiRnooSQa2chR7HVjhsuGfPonomrKC9VLEjwI5GC1GisjmMXoHK1DWmw31hMVcFTKu07O2VhSBL3ySdkJ__6HMAzMJ3dmcwaQjuiQ_plM7nVI4BA9ZdR3Ke7bNYsoWjPcs6P7XpFEGpeF6tDql7B-mD00vsq_9PL2GQ4VqHdXTlhvM3oRv39dayxegr9I0HhHfHx99cd5YYu2oEP5OaHiqgUdckkJPgqwe7fuMrOsvMlmniWsNESQZMOHhmsPSg0WzqxXvf72aE2yLNV9FivC3GzU_QQc8mgod1ssWSDpqEXgxffZKi7NmiQehIuBr34zQHgqJJacoJ7SMeUYAz_upsfp32FXPu0T7rWj7g7QsPG1oNSVa9rZG8l36ddSKLZTY-6QmRfBumU1rfBdoNg5M1-iZu3skB1Ly_8JbU6nw0aQZvAeJfvx3_-94-92XCI_v9ZSmTXEBugYfs5SpmkCx6J0QL7Url5QYqd97jNeQMPg76f99Wix_wHtAGFwyW0lklwHYYuMWHJS3Giao5osDiFynwbTVZTnDyJZwCBR3T3qQt7aauTvRS7c-Q6MV47yU9qP_KPoeL6fv4S5pRXprAtqjwANLrjiGVG3w014eS5UDuVHNe8qqg2qizILbiyxFIK_DsFWq0ZYkifUlfqg_pi6L55XuRC30IWGp1JePYytHr_5JKay5nydyQr-D027W7vin5UK4f2-Mls_q0_99_fjRC26qnWSTv9ainsPCTd-ijaWnKzD20cq-edWRPnEjIfjyOc3iDZmKKqXAxgZ6U5D4IKRqiQklx4a3QnAjJF2qmQy3NEW7GVq9V3On4rViQugit3MhMfRp5kLxRigiI0XWp6FqZ8FuCO11rB2irYakAO8mNUEZ6ghtt2diiNs2lntzRj26Ji-u5J7qSP6TIgRl1YyAcZdLVNTckCCfl_u0jPPbdsk2yE46NaPA8Dx901Ky2WndVnVkA6y5_H-gu3FV3Dm954jWVwohHlxDmYQ9asZdQlZQPf4nb3D7m4J5435L9A0F4MGDrS7gnc-l7TXb26efIzcC9eXYlko9E9xkPuDEEcXnD89G5XIBfaV_URoq9uQ3K95Df5AcZRlmZg87JzJP5jGz6sjbxEWrqX2AjZi30FpRSK4VmcG8PLavaGGjUiVSgR2X_lcIkT5TGe_UioGG5SghTgZCHZGZNOnV4vzWztCrKVP32qnxsl6qJ9r9H4UxCaF4ELYJ6rrYB3FY6Jq5EXvz5_qs4IeaqpIFKtLtt2RhtBNR6M-JXBXJ1Wx8wrNqWa7xdO4eH0_342vy-dWZb2LCsMtjlZ3lPdrctrvijwA4m6f3cqzKW31I1g3YaCKRzTopmxDrCxvi_nXJTZfDf9aySvF6DIYjg
``` 

```
k2.secret-pw.4E0InkfeEjv3iWwyvfWl8AAAAAAEAAAAAAAAAgAAAAFSlCXdviXQomKxRdLuDJtSoFKWW5Qnz0kATDg_8la44lKegKQNJlV0ZBHckGohtQUaS0KqmIvJH2WtAM9dweKnlisHQYUiQfyS4-VyFBmhYaAhOFiRJ5__-vZRDpM08DMt_OP94_eeSWHwLcqX2gzpFU9CHy5m5No
```

```
k3.secret-pw.mXsR2qVqmcDxmSWeQCnCwNeIxe5RDQ3ehnQvdXFj-YgAAAPoFI8eRXCL8PFpVW_CWOvGHnvMPy0BkMlKF1AtmBYGKold9i-ALC2oflkemYdbncrHbiKGd8zfjTQu2tTo2ayOMHybk_-hhopwJ2IUallYfLfUzPuqvtOQfVxXLtUBPnmR75dhRiPDgzdIO1OMbqa3Z1LDevvzbrcPyhHqmJSZioeJ7j1Mu8DJOvrIK0pWHmjDq_eg4YFnaOgz7I3Tkxx89A
```

```
k4.secret-pw.dkyi7kfzHnVTCqTq1AvLyQAAAAAQAAAAAAAAAwAAAAFWNLgB_yXNkk4W9NiXgeTkNnB3Vjuk_-TFQ-vMUxNX-Ha3k42djov9rHVykMHkrSGUemYFwpot9uNHnXOWtJCVIwdYAwZmt_uRSJ2rRTElanT6mWXojuBUy2k1lxD-iZ10pVPkJ-Kvv_SLEhLQ8RS7wqFW8RfFGyw
```
