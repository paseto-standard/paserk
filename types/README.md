# PASERK Types

| PASERK Type                           | Meaning                                                        | PASETO Compatibility | \[data\] Encoded? |  
|---------------------------------------|----------------------------------------------------------------|----------------------|-------------------|
| [`lid`](ptklid.md)                 | Unique Identifier for a separate PASERK for `local` PASETOs.   | `local`              | Yes               |
| [`local`](ptklocal.md)             | Symmetric key for `local` tokens.                              | `local`              | Yes               |
| [`seal`](ptkseal.md)               | Symmetric key wrapped using asymmetric encryption.             | `local`              | Yes               |
| [`local-wrap`](ptklocal-wrap.md)   | Symmetric key wrapped by another symmetric key.                | `local`              | No                |
| [`local-pw`](ptklocal-pw.md)       | Symmetric key wrapped using password-based encryption.         | `local`              | Yes               |
| [`pid`](ptkpid.md)                 | Unique Identifier for a separate PASERK for `public` PASETOs.  | `public`             | Yes               |
| [`public`](ptkpublic.md)           | Public key for verifying `public` tokens.                      | `public`             | Yes               |
| [`secret`](ptksecret.md)           | Secret key for signing `public` tokens.                        | `public`             | Yes               |
| [`secret-wrap`](ptksecret-wrap.md) | Asymmetric secret key wrapped by another symmetric key.        | `public`             | No                |
| [`secret-pw`](ptksecret-pw.md)     | Asymmetric secret key wrapped using password-based encryption. | `public`             | Yes               |
