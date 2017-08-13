===================
easyauth Public API
===================

The public API is just a modification of the private API: it uses the same endpoints, but operates differently.

user
####

GET
+++

Returns details about a single user.

Request
-------

**URL:** ``GET /users/<id>``

+-----------+------------------------------------------------------------------+
| Parameter | Description                                                      |
+===========+==================================================================+
| apikey    | Your public API key.                                             |
+-----------+------------------------------------------------------------------+
| id        | The ID of the user being looked up.                              |
+-----------+------------------------------------------------------------------+
| nonce     | A cryptographic nonce used to validate response hmac.            |
+-----------+------------------------------------------------------------------+
| hmac      | ``hmac-sha256(secret_key, concatenate(nonce, id)``               |
+-----------+------------------------------------------------------------------+

Response
--------

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 200           |::                                              | The user's  |
|               |                                                | information.|
|               | {                                              |             |
|               |     "status":"success",                        |             |
|               |     "user":{                                   |             |
|               |         "id": ID,                              |             |
|               |         "name":"User's name",                  |             |
|               |         "email":"User's email",                |             |
|               |         "admin": true or false,                |             |
|               |         "certificate":"URL to certificate"     |             |
|               |     },                                         |             |
|               |     "hmac":hmac-sha256(secret_key, concatenate(|             |
|               |                       nonce, id, name, email)) |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 401           |::                                              | Indicates   |
|               |                                                | the user is |
|               | {                                              | not         |
|               |     "status":"error",                          | authorized  |
|               |     "reason":"Unauthorized"                    | to make this|
|               | }                                              | request.    |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 404           |::                                              | Indicates   |
|               |                                                | no such user|
|               | {                                              | exists.     |
|               |     "status":"error",                          |             |
|               |     "reason":"No such user"                    |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 400           |::                                              | Indicates a |
|               |                                                | malformed   |
|               | {                                              | request.    |
|               |     "status":"error",                          |             |
|               |     "reason":"Bad request"                     |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+

certificate
###########

GET
+++

Returns information about a certificate.

Request
-------

**URL:** ``GET /certificates/<serial>``

+-----------+------------------------------------------------------------------+
| Parameter | Description                                                      |
+===========+==================================================================+
| apikey    | Your public API key.                                             |
+-----------+------------------------------------------------------------------+
| serial    | The serial of the certificate being looked up.                   |
+-----------+------------------------------------------------------------------+
| nonce     | A cryptographic nonce used to validate response hmac.            |
+-----------+------------------------------------------------------------------+
| hmac      | ``hmac-sha256(secret_key, concatenate(nonce, serial)``           |
+-----------+------------------------------------------------------------------+

Response
--------

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 200           |::                                              | The         |
|               |                                                | data about  |
|               | {                                              | the         |
|               |     "status":"success",                        | certificate.|
|               |     "certificate":{                            |             |
|               |         "serial":serial,                       |             |
|               |         "active":true or false,                |             |
|               |         "valid_until":date,                    |             |
|               |         "user":"GET /users/id",                |             |
|               |     }                                          |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 401           |::                                              | Indicates   |
|               |                                                | the user is |
|               | {                                              | not         |
|               |     "status":"error",                          | authorized  |
|               |     "reason:" "Unauthorized"                   | to make this|
|               | }                                              | request.    |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 404           |::                                              | Indicates   |
|               |                                                | no such     |
|               | {                                              | certificate |
|               |     "status":"error",                          | exists.     |
|               |     "reason":"No such user"                    |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 400           |::                                              | Indicates a |
|               |                                                | malformed   |
|               | {                                              | request.    |
|               |     "status":"error",                          |             |
|               |     "reason":"Bad request"                     |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
