===================
easyauth Public API
===================

This document describes the API for validating users' public keys. It currently has one
method.

user
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
All responses except for 400 Bad Request include a HMAC-SHA256 authentication code. This MUST be validated
by the client. All responses are in JSON format.

+---------------+------------------------------------------------+
| Response Code | Response                                       |
+===============+================================================+
| 200           |                                                |
|               | ::                                             |
|               |                                                |
|               |   {                                            |
|               |     "hash":The hash of the public key,         |
|               |     ”status”:”success”,                        |
|               |     "uid":The user's unique ID,                |
|               |     "token":HMAC-SHA256(secret,hash+uid+nonce) |
|               |   }                                            |
|               |                                                |
+---------------+------------------------------------------------+
| 403           |                                                | 
|               | ::                                             |
|               |                                                |
|               |   {                                            |
|               |     "hash":The hash of the public key,         |
|               |     "status":"error",                          |
|               |     "reason":"Invalid API key",                |
|               |     "token":"HMAC-SHA256(secret,hash+nonce)    |
|               |   }                                            |
|               |                                                |
+---------------+------------------------------------------------+
| 400           |                                                |
|               | ::                                             |
|               |                                                |
|               |   {                                            |
|               |     "status":"error",                          |
|               |     "reason":"Bad request"                     |
|               |   }                                            |
|               |                                                |
+---------------+------------------------------------------------+

