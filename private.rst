====================
easyauth Private API
====================

This document describes the internal API used for communication between the
backend server and frontend website. Most of this is intended to be used via
XHR by the client (using an HMAC provided by the frontend server), however some
calls are meant to be used solely by the frontend. Calls meant for the client
will have an ``hmac`` parameter, while calls meant for the frontend server will
have a ``secret`` parameter.

The frontend and backend will each have a unique secret for authorization and
validation purposes. Every response to the frontend containing data will also
contain a token generated with the server's secret.

users
#####

GET
+++

Returns a list of all users, 100 at a time. Meant for use directly by the frontend.

Request
-------

+-----------+-----------------------------------------------------------+
| Parameter | Description                                               |
+===========+===========================================================+
| secret    | The frontend's secret.                                    |
+-----------+-----------------------------------------------------------+
| start     | The starting user ID.                                     |
+-----------+-----------------------------------------------------------+
| nonce     | A cryptographic nonce.                                    |
+-----------+-----------------------------------------------------------+

Response
--------

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 200           | ::                                             | Returns a   |
|               |                                                | list of     |
|               | {                                              | users.      |
|               |     "status": "success",                       |             |
|               |     "users": [list of users],                  |             |
|               |     "token": HMAC-SHA256(secret, nonce)        |             |
|               | }                                              |             |
+---------------+------------------------------------------------+-------------+
| 204           | N/A                                            | Indicates   |
|               |                                                | no users    |
|               |                                                | exist.      |
|               |                                                |             |
|               |                                                |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 403           | ::                                             | Incorrect   |
|               |                                                | secret.     |
|               | {                                              |             |
|               |     "status": "error",                         |             |
|               |     "reason": "Bad secret"                     |             |
|               | }                                              |             |
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


POST
++++

Allows the frontend to create a new user. Note that the passsword is not stored
directly in the database, but rather salted and hashed.

Request
-------

+-----------+-----------------------------------------------------------+
| Parameter | Description                                               |
+===========+===========================================================+
| secret    | The secret for communication between front- and back-end. |
+-----------+-----------------------------------------------------------+
| email     | The email address for the new user.                       |
+-----------+-----------------------------------------------------------+
| name      | The name of the new user.                                 |
+-----------+-----------------------------------------------------------+
| password  | The user's password.                                      |
+-----------+-----------------------------------------------------------+

Response
--------

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 200           | ::                                             | Indicates   |
|               |                                                | the user was|
|               | {                                              | created.    |
|               |     "status": "success"                        |             |
|               |     "id": New user's ID,                       |             |
|               |     "token": HMAC-SHA256(secret, nonce)        |             |
|               | }                                              |             |
+---------------+------------------------------------------------+-------------+
| 403           | ::                                             | Incorrect   |
|               |                                                | secret.     |
|               | {                                              |             |
|               |     "status": "error",                         |             |
|               |     "reason": "Bad secret"                     |             |
|               | }                                              |             |
+---------------+------------------------------------------------+-------------+
| 409           | ::                                             | Indicates   |
|               |                                                | a user with |
|               | {                                              | that email  |
|               |     "status": "error",                         | already     |
|               |     "reason": "Duplicate email",               | exists.     |
|               |     "token": HMAC-SHA256(secret, nonce)        |             |
|               | }                                              |             |
+---------------+------------------------------------------------+-------------+
| 422           | ::                                             | Indicates   |
|               |                                                | an error in |
|               | {                                              | the user's  |
|               |     "status": "error",                         | input. The  |
|               |     "reason": "Invalid email",                 | reason will |
|               |     "token": HMAC-SHA256(secret, nonce)        | provide more|
|               | }                                              | information.|
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

user
####

GET
---

PATCH
-----

DELETE
------

certificates
############

GET
---

POST
----

certificate
###########

GET
---

PATCH
-----

DELETE
------
