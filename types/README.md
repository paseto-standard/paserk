# PASERK Types

| PASERK Type                           | Meaning                                                        | PASETO Compatibility | \[data\] Encoded? |  
|---------------------------------------|----------------------------------------------------------------|----------------------|-------------------|
| [`lid`](lid.md)                 | Unique Identifier for a separate PASERK for `local` PASETOs.   | `local`              | Yes               |
| [`local`](local.md)             | Symmetric key for `local` tokens.                              | `local`              | Yes               |
| [`seal`](seal.md)               | Symmetric key wrapped using asymmetric encryption.             | `local`              | Yes               |
| [`local-wrap`](local-wrap.md)   | Symmetric key wrapped by another symmetric key.                | `local`              | No                |
| [`local-pw`](local-pw.md)       | Symmetric key wrapped using password-based encryption.         | `local`              | Yes               |
| [`pid`](pid.md)                 | Unique Identifier for a separate PASERK for `public` PASETOs.  | `public`             | Yes               |
| [`public`](public.md)           | Public key for verifying `public` tokens.                      | `public`             | Yes               |
| [`secret`](secret.md)           | Secret key for signing `public` tokens.                        | `public`             | Yes               |
| [`secret-wrap`](secret-wrap.md) | Asymmetric secret key wrapped by another symmetric key.        | `public`             | No                |
| [`secret-pw`](secret-pw.md)     | Asymmetric secret key wrapped using password-based encryption. | `public`             | Yes               |
