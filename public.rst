===================
easyauth Public API
===================

This document describes the API for validating users' public keys. It currently
has one method.

certificate
####

validate
++++++++

Request
-------
+--------+-----------+-----------------------------------------------------+
| Method | Parameter | Description                                         |
+========+===========+=====================================================+
| HEAD   | key       | Your API key.                                       |
+--------+-----------+-----------------------------------------------------+
| GET    | hash      | Hash of the key being validated.                    |
+--------+-----------+-----------------------------------------------------+
| GET    | timestamp | The time of the request as a UNIX epoch time.       |
+--------+-----------+-----------------------------------------------------+
| GET    | nonce     | A securely generated, unique, 32-bit random number. |
+--------+-----------+-----------------------------------------------------+

Response
--------
All responses except for 400 Bad Request and 403 Forbidden include a HMAC-SHA256 authentication
code, as indicated by the value of ``1`` in ``"token-format"``. Other token formats may be implemented in the future. This MUST be validated by the client. All responses are in JSON format.

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 200           |                                                | Indicates a |
|               | ::                                             | successful  |
|               |                                                | request.    |
|               |   {                                            |             |
|               |     "hash":The hash of the public key,         |             |
|               |     "status":"success",                        |             |
|               |     "uid":The user's unique ID,                |             |
|               |     "token":HMAC-SHA256(secret,hash+uid+nonce),|             |
|               |     "token-format": 1                          |             |
|               |   }                                            |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 200           |                                                | Indicates   |
|               | ::                                             | that the    |
|               |                                                | provided    |
|               |   {                                            | hash is no  |
|               |     "hash":The hash of the public key,         | longer valid|
|               |     "status":"revoked",                        | and has been|
|               |     "token":HMAC-SHA256(secret,hash+uid+nonce),| revoked.    |
|               |     "token-format": 1                          |             |
|               |   }                                            |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 403           |                                                | Indicates an|
|               | ::                                             | API key was |
|               |                                                | provided but|
|               |   {                                            | is invalid. |
|               |     "hash":The hash of the public key,         |             |
|               |     "status":"error",                          |             |
|               |     "reason":"Invalid API key",                |             |
|               |   }                                            |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 404           |                                                | Indicates no|
|               | ::                                             | certificate |
|               |                                                | with the    |
|               |   {                                            | specified   |
|               |     "status":"error",                          | hash exists.|
|               |     "reason":"Certificate not found",          |             |
|               |     "token":"HMAC-SHA256(secret,hash+nonce),   |             |
|               |     "token-format": 1                          |             |
|               |   }                                            |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 400           |                                                | Indicates a |
|               | ::                                             | malformed   |
|               |                                                | request.    |
|               |   {                                            |             |
|               |     "status":"error",                          |             |
|               |     "reason":"Bad request"                     |             |
|               |   }                                            |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+

