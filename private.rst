====================
easyauth Private API
====================

This document describes the internal API used for communication between the
backend server and frontend website. This is intended for XHR requests between
the client and the backend, with authentication done through cookies. In the
future, this may be opened up for public access; as such, it is pseudo-RESTful.



users
#####

GET
+++

Returns a list of all users, 100 at a time. Requires admin priviliges.

Request
-------

+-----------+------------------------------------------------------------------+
| Parameter | Description                                                      |
+===========+==================================================================+
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
|               |     "status": "success",                       |             |
|               |     "users": [{                                |             |
|               |         "id":id,                               |             |
|               |         "name":"name",                         |             |
|               |         "details":"url to GET /user/id"        |             |
|               |         }]                                     |             |
|               | }                                              |             |
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
|               |     "status": "error",                         | authorized  |
|               |     "reason": "Unauthroized"                   | to make this|
|               | }                                              | request.    |
+---------------+------------------------------------------------+-------------+
| 400           |::                                              | Indicates a |
|               |                                                | malformed   |
|               |   {                                            | request.    |
|               |     "status":"error",                          |             |
|               |     "reason":"Bad request"                     |             |
|               |   }                                            |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+


POST
++++

Creates a new user. Requires the user to not be authenticated.

Request
-------

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
|               |     "status": "queued",                        | is awaiting |
|               |     "id": New user's ID                        | email       |
|               | }                                              | validation. |
+---------------+------------------------------------------------+-------------+
| 403           |::                                              | Indicates   |
|               |                                                | the user is |
|               | {                                              | logged in   |
|               |     "status": "error",                         | and cannot  |
|               |     "reason": "Already authenticated"          | create a    |
|               | }                                              | new user.   |
+---------------+------------------------------------------------+-------------+
| 409           |::                                              | Indicates   |
|               |                                                | a user with |
|               | {                                              | that email  |
|               |     "status": "error",                         | already     |
|               |     "reason": "Duplicate email"                | exists.     |
|               | }                                              |             |
+---------------+------------------------------------------------+-------------+
| 422           |::                                              | Indicates   |
|               |                                                | an error in |
|               | {                                              | the user's  |
|               |     "status": "error",                         | input. The  |
|               |     "reason": "Invalid email"                  | reason will |
|               | }                                              | provide more|
|               |                                                | information.|
+---------------+------------------------------------------------+-------------+
| 400           |::                                              | Indicates a |
|               |                                                | malformed   |
|               |   {                                            | request.    |
|               |     "status":"error",                          |             |
|               |     "reason":"Bad request"                     |             |
|               |   }                                            |             |
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

+-----------+------------------------------------------------------------------+
| Parameter | Description                                                      |
+===========+==================================================================+
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
|               |     "status": "success",                       |             |
|               |     "id": ID,                                  |             |
|               |     "name": "User's name",                     |             |
|               |     "email": "User's email",                   |             |
|               |     "admin": true or false,                    |             |
|               |     "certificate": "url to current certificate"|             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 403           |::                                              | Indicates   |
|               |                                                | the user is |
|               | {                                              | not         |
|               |     "status": "error",                         | authorized  |
|               |     "reason:" "Unauthorized"                   | to make this|
|               | }                                              | request.    |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 404           |::                                              | Indicates   |
|               |                                                | no such user|
|               | {                                              | exists.     |
|               |     "status": "error",                         |             |
|               |     "reason": "No such user"                   |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 400           |::                                              | Indicates a |
|               |                                                | malformed   |
|               |   {                                            | request.    |
|               |     "status":"error",                          |             |
|               |     "reason":"Bad request"                     |             |
|               |   }                                            |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+

PATCH
+++++

Allows a user to update their own information. All parameters except ``id`` and
``password`` are optional, however at least one other *must* be provided.

Request
-------

+--------------+---------------------------------------------------------------+
| Parameter    | Description                                                   |
+==============+===============================================================+
| id           | The ID of the user being modified.                            |
+--------------+---------------------------------------------------------------+
| password     | The user's password, for confirmation.                        |
+--------------+---------------------------------------------------------------+
| new_email    | The user's new email address (if specified).                  |
+--------------+---------------------------------------------------------------+
| name         | The user's new name (if specified).                           |
+--------------+---------------------------------------------------------------+
| new_password | The user's new password (if specified).                       |
+--------------+---------------------------------------------------------------+


Response
--------

+---------------+------------------------------------------------+-------------+
| Response Code | Response                                       | Description |
+===============+================================================+=============+
| 200           |::                                              | Indicates   |
|               |                                                | the user's  |
|               | {                                              | information |
|               |     "status": "success",                       | was updated |
|               |     "user": "url to GET /user/id"              | sucessfully.|
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 202           |::                                              | Indicates   |
|               |                                                | the user was|
|               | {                                              | updated and |
|               |     "status": "queued",                        | is awaiting |
|               |     "id": New user's ID                        | email       |
|               | }                                              | validation. |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 403           |::                                              | Indicates   |
|               |                                                | the user is |
|               | {                                              | not         |
|               |     "status": "error",                         | authorized  |
|               |     "reason:" "Unauthorized"                   | to make this|
|               | }                                              | request.    |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 404           |::                                              | Indicates   |
|               |                                                | no such user|
|               | {                                              | exists.     |
|               |     "status": "error",                         |             |
|               |     "reason": "No such user"                   |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 400           |::                                              | Indicates a |
|               |                                                | malformed   |
|               |   {                                            | request.    |
|               |     "status":"error",                          |             |
|               |     "reason":"Bad request"                     |             |
|               |   }                                            |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+

DELETE
++++++

Allows a user to be deleted.

Request
-------

+--------------+---------------------------------------------------------------+
| Parameter    | Description                                                   |
+==============+===============================================================+
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
|               |     "status": "success",                       | sucessfully.|
|               |     "user": "url to GET /user/id"              |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 403           |::                                              | Indicates   |
|               |                                                | the user is |
|               | {                                              | not         |
|               |     "status": "error",                         | authorized  |
|               |     "reason:" "Unauthorized"                   | to make this|
|               | }                                              | request.    |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 404           |::                                              | Indicates   |
|               |                                                | no such user|
|               | {                                              | exists.     |
|               |     "status": "error",                         |             |
|               |     "reason": "No such user"                   |             |
|               | }                                              |             |
|               |                                                |             |
+---------------+------------------------------------------------+-------------+
| 400           |::                                              | Indicates a |
|               |                                                | malformed   |
|               |   {                                            | request.    |
|               |     "status":"error",                          |             |
|               |     "reason":"Bad request"                     |             |
|               |   }                                            |             |
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

Creates a new certificate for the specified user.

Request
-------

Response
--------

certificate
###########

GET
+++

Returns information about a certificate.

Request
-------

Response
--------

PATCH
+++++

Not supported, as a certificate cannot be updated once it is signed.

DELETE
++++++

Will revoke a certificate, rather than outright delete it.

Request
-------

Response
--------
