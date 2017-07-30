====================
easyauth Private API
====================

This document describes the internal API used for communication between the
backend server and frontend website. This is intended for XHR requests between
the client and the backend, with authentication done through an API key set by
the frontend. In the future, this may be opened up for public access; as such,
calls which will only be accessible from the frontend will be so noted.

In this document, a URL will be provided for each request. Any parameters that
are part of the URL will be indicated with angle brackets and detailed in the
parameter table. All other parameters must be provided via the proper request
method.


.. contents:: Table of Contents

login
#####

GET
+++

Extends the validity of the user's API key.

Should API access become unrestricted, this method of authentication will not be
supported and will be restricted to the frontend server.

Request
-------

**URL:** ``GET /login``

+-----------+------------------------------------------------------------------+
| Parameter | Description                                                      |
+===========+==================================================================+
| apikey    | The user's API key.                                              |
+-----------+------------------------------------------------------------------+

Response
--------

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 200           |::                                              | Extends the |
|               |                                                | API key for |
|               | {                                              | the user.   |
|               |     "status":"success",                        |             |
|               |     "expires":the ISO 8601 date of expiry      |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 401           |::                                              | Invalid API |
|               |                                                | key.        |
|               | {                                              |             |
|               |     "status":"error",                          |             |
|               |     "error":"Invalid API key"                  |             |
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

POST
++++

Validates a provided username and password, returning an API key associated with
the user if successful.

Should API access become unrestricted, this method of authentication will not be
supported and will be restricted to the frontend server.

Request
-------

**URL:** ``POST /login``

+-----------+------------------------------------------------------------------+
| Parameter | Description                                                      |
+===========+==================================================================+
| email     | The email of the user logging in.                                |
+-----------+------------------------------------------------------------------+
| password  | The password of the user logging in.                             |
+-----------+------------------------------------------------------------------+

Response
--------

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 200           |::                                              | Provides an |
|               |                                                | API key for |
|               | {                                              | the user.   |
|               |     "status":"success",                        |             |
|               |     "apikey":the API key,                      |             |
|               |     "expires":the ISO 8601 date of expiry      |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 401           |::                                              | Incorrect   |
|               |                                                | email or    |
|               | {                                              | password.   |
|               |     "status":"error",                          |             |
|               |     "error":"Incorrect username or password"   |             |
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

users
#####

GET
+++

Returns a list of all users, 100 at a time. Requires admin priviliges.

Request
-------

**URL:** ``GET /users/``

+-----------+------------------------------------------------------------------+
| Parameter | Description                                                      |
+===========+==================================================================+
| apikey    | The API key provided.                                            |
+-----------+------------------------------------------------------------------+
| start     | The starting user ID.                                            |
+-----------+------------------------------------------------------------------+

Response
--------

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 200           |::                                              | Returns a   |
|               |                                                | list of     |
|               | {                                              | users.      |
|               |     "status":"success",                        |             |
|               |     "users": [{                                |             |
|               |         "id":id,                               |             |
|               |         "name":"name",                         |             |
|               |         "details":"url to GET /users/id"       |             |
|               |         }]                                     |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 204           | N/A                                            | Indicates   |
|               |                                                | no users    |
|               |                                                | exist.      |
|               |                                                |             |
|               |                                                |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 403           |::                                              | Indicates   |
|               |                                                | the user is |
|               | {                                              | not         |
|               |     "status":"error",                          | authorized  |
|               |     "reason":"Unauthroized"                    | to make this|
|               | }                                              | request.    |
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


POST
++++

Creates a new user.

Request
-------

**URL:** ``POST /users/``

+-----------+------------------------------------------------------------------+
| Parameter | Description                                                      |
+===========+==================================================================+
| email     | The email address for the new user.                              |
+-----------+------------------------------------------------------------------+
| name      | The name of the new user.                                        |
+-----------+------------------------------------------------------------------+
| password  | The user's password.                                             |
+-----------+------------------------------------------------------------------+

Response
--------

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 202           |::                                              | Indicates   |
|               |                                                | the user was|
|               | {                                              | created and |
|               |     "status":"queued",                         | is awaiting |
|               |     "id": New user's ID                        | email       |
|               | }                                              | validation. |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 401           |::                                              | Indicates   |
|               |                                                | the supplied|
|               | {                                              | password was|
|               |     "status":"error",                          | incorrect.  |
|               |     "reason":"Incorrect password"              |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 403           |::                                              | Indicates   |
|               |                                                | the user is |
|               | {                                              | logged in   |
|               |     "status":"error",                          | and cannot  |
|               |     "reason":"Already authenticated"           | create a    |
|               | }                                              | new user.   |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 409           |::                                              | Indicates   |
|               |                                                | a user with |
|               | {                                              | that email  |
|               |     "status":"error",                          | already     |
|               |     "reason":"Duplicate email"                 | exists.     |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 422           |::                                              | Indicates   |
|               |                                                | an error in |
|               | {                                              | the user's  |
|               |     "status":"error",                          | input. The  |
|               |     "reason":"Invalid email"                   | reason will |
|               | }                                              | provide more|
|               |                                                | information.|
+---------------+------------------------------------------------+-------------+
| 400           |::                                              | Indicates a |
|               |                                                | malformed   |
|               | {                                              | request.    |
|               |     "status":"error",                          |             |
|               |     "reason":"Bad request"                     |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+

user
####

GET
+++

Returns details about a single user. Responds 403 Forbidden unless the user
making the request is requesting their own information or is an admin.

Request
-------

**URL:** ``GET /users/<id>``

+-----------+------------------------------------------------------------------+
| Parameter | Description                                                      |
+===========+==================================================================+
| apikey    | The API key provided.                                            |
+-----------+------------------------------------------------------------------+
| id        | The ID of the user being looked up. If not specified, returns    |
|           | information for the authenticated user.                          |
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
|               |     }                                          |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 403           |::                                              | Indicates   |
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

PATCH
+++++

Allows a user to update their own information. All parameters except ``id`` are
optional, however at least one other *must* be provided.

Should API access become unrestricted, updating a user's information will not be
possible except from the frontend (or another official client). Validating a
user, however, will be possible from third-party clients.

Request
-------

**URL:** ``PATCH /users/<id>``

+-----------------+------------------------------------------------------------+
| Parameter       | Description                                                |
+=================+============================================================+
| apikey          | The API key provided.                                      |
+-----------------+------------------------------------------------------------+
| id              | The ID of the user being modified.                         |
+-----------------+------------------------------------------------------------+
| new_email       | The user's new email address (if specified).               |
+-----------------+------------------------------------------------------------+
| name            | The user's new name (if specified).                        |
+-----------------+------------------------------------------------------------+
| new_password    | The user's new password (if specified).                    |
+-----------------+------------------------------------------------------------+
| valid           | True or false. Used for email validation.                  |
+-----------------+------------------------------------------------------------+
| validation_code | The code sent by email. Required to validate a user.       |
+-----------------+------------------------------------------------------------+

Response
--------

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 200           |::                                              | Indicates   |
|               |                                                | the user's  |
|               | {                                              | information |
|               |     "status":"success",                        | was updated |
|               |     "user":"url to GET /users/id"              | sucessfully.|
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 202           |::                                              | Indicates   |
|               |                                                | the user was|
|               | {                                              | updated and |
|               |     "status":"queued"                          | is awaiting |
|               | }                                              | email       |
|               |                                                | validation. |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 401           |::                                              | Indicates   |
|               |                                                | the supplied|
|               | {                                              | password was|
|               |     "status":"error",                          | incorrect.  |
|               |     "reason":"Incorrect password"              |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 403           |::                                              | Indicates   |
|               |                                                | the user is |
|               | {                                              | not         |
|               |     "status":"error",                          | authorized  |
|               |     "reason:" "Unauthorized"                   | to make this|
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

DELETE
++++++

Allows a user to be deleted.

Should API access become unrestricted, it will not be possible to delete a user
except from the frontend.

Request
-------

**URL:** ``DELETE /users/<id>``

+--------------+---------------------------------------------------------------+
| Parameter    | Description                                                   |
+==============+===============================================================+
| apikey       | The API key provided.                                         |
+--------------+---------------------------------------------------------------+
| id           | The ID of the user being deleted.                             |
+--------------+---------------------------------------------------------------+
| password     | The user's password, for confirmation.                        |
+--------------+---------------------------------------------------------------+

Response
--------

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 200           |::                                              | Indicates   |
|               |                                                | the user    |
|               | {                                              | was deleted |
|               |     "status":"success",                        | sucessfully.|
|               |     "user":"url to GET /users/id"              |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 401           |::                                              | Indicates   |
|               |                                                | the supplied|
|               | {                                              | password was|
|               |     "status":"error",                          | incorrect.  |
|               |     "reason":"Incorrect password"              |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 403           |::                                              | Indicates   |
|               |                                                | the user is |
|               | {                                              | not         |
|               |     "status":"error",                          | authorized  |
|               |     "reason:" "Unauthorized"                   | to make this|
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

certificates
############

GET
+++

Not supported: should a listing of all certificates be required, the store can
be queried in other ways.

POST
++++

Creates a new certificate for the specified user. The format the CSR (and 
returned certificate) should be in is currently unspecified while we decide on
a solution.

Request
-------

**URL:** ``POST /certificates/``

+--------------+---------------------------------------------------------------+
| Parameter    | Description                                                   |
+==============+===============================================================+
| apikey       | The API key provided.                                         |
+--------------+---------------------------------------------------------------+
| user_id      | The ID of the user requesting a new certificate.              |
+--------------+---------------------------------------------------------------+
| csr          | The certificate signing request for the requested certificate.|
+--------------+---------------------------------------------------------------+

Response
--------

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 201           |::                                              | The         |
|               |                                                | certificate |
|               | {                                              | was signed. |
|               |     "status":"success",                        |             |
|               |     "url":"URL to the new certificate"         |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 422           |::                                              | Indicates   |
|               |                                                | that the    |
|               | {                                              | previous    |
|               |     "status":"error",                          | certificate |
|               |     "reason":"Unrevoked certificate",          | has not     |
|               |     "revoke_url":"URL to revoke certificate"   | been        |
|               | }                                              | revoked.    |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 422           |::                                              | Indicates   |
|               |                                                | an error    |
|               | {                                              | with the    |
|               |     "status":"error",                          | request     |
|               |     "reason":"Bad CSR"                         | detailed by |
|               | }                                              | the reason  |
|               |                                                | field.      |
+---------------+------------------------------------------------+-------------+
| 403           |::                                              | Indicates   |
|               |                                                | the user is |
|               | {                                              | not         |
|               |     "status":"error",                          | authorized  |
|               |     "reason:" "Unauthorized"                   | to make this|
|               | }                                              | request.    |
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

Returns information about a certificate. Responds 403 Forbidden unless the user
making the request is requesting their own information or is an admin.

Request
-------

**URL:** ``GET /certificates/<serial>``

+-----------+------------------------------------------------------------------+
| Parameter | Description                                                      |
+===========+==================================================================+
| apikey    | The API key provided.                                            |
+-----------+------------------------------------------------------------------+
| serial    | The serial of the certificate being looked up. If not specified, |
|           | returns information about the authenticated user's certificate.  |
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
|               |     "certificate":{                            | Includes    |
|               |         "serial":serial,                       | the public  |
|               |         "hash":hash,                           | key in JWK_ |
|               |         "valid":true or false,                 | format.     |
|               |         "valid_until":date,                    |             |
|               |         "user":"GET /users/id"                 |             |
|               |     }                                          |             |
|               |     "certificate-jwk":{                        |             |
|               |         "kty":"RSA",                           |             |
|               |         "kid":serial,                          |             |
|               |         "n":modulo,                            |             |
|               |         "e":exponent,                          |             |
|               |         "x5c":base64 certificate chain,        |             |
|               |         "x5t":thumbprint,                      |             |
|               |         "x5t#S256":SHA-256 thumbprint          |             |
|               |     }                                          |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 403           |::                                              | Indicates   |
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

.. _JWK: https://tools.ietf.org/html/rfc7517/

PATCH
+++++

Update a certificate's revocation status. Only works to revoke; a certificate
cannot be reinstated once it is revoked.

Request
-------

**URL:** ``PATCH /certificates/<serial>``

+-----------+------------------------------------------------------------------+
| Parameter | Description                                                      |
+===========+==================================================================+
| apikey    | The API key provided.                                            |
+-----------+------------------------------------------------------------------+
| serial    | The serial of the certificate being revoked.                     |
+-----------+------------------------------------------------------------------+
| valid     | The validity to set. Must be false.                              |
+-----------+------------------------------------------------------------------+

Response
--------


+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 200           |::                                              | Indicates   |
|               |                                                | successful  |
|               | {                                              | revocation. |
|               |     "status":"success",                        |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 422           |::                                              | Indicates   |
|               |                                                | ``valid``   |
|               | {                                              | was set to  |
|               |     "status":"error",                          | true in the |
|               |     "reason":"Cannot unrevoke a certificate"   | request.    |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 401           |::                                              | Indicates   |
|               |                                                | the supplied|
|               | {                                              | password was|
|               |     "status":"error",                          | incorrect.  |
|               |     "reason":"Incorrect password"              |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 403           |::                                              | Indicates   |
|               |                                                | the user is |
|               | {                                              | not         |
|               |     "status":"error",                          | authorized  |
|               |     "reason:" "Unauthorized"                   | to make this|
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

DELETE
++++++

Deletes a certificate. Requires admin privileges.

Request
-------

**URL:** ``DELETE /certificates/<serial>``

+-----------+------------------------------------------------------------------+
| Parameter | Description                                                      |
+===========+==================================================================+
| apikey    | The API key provided.                                            |
+-----------+------------------------------------------------------------------+
| serial    | The serial of the certificate being revoked.                     |
+-----------+------------------------------------------------------------------+

Response
--------

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 200           |::                                              | Indicates   |
|               |                                                | the         |
|               | {                                              | certificate |
|               |     "status":"success",                        | was deleted |
|               |     "user":"url to GET /users/id"              | sucessfully.|
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 403           |::                                              | Indicates   |
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
