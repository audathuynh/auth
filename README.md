# auth
Authentication and Authorisation for micro-services

## Authentication

This is the procedure for the authentication that I see people are using in practice.

### Step 1: C &rarr; A: C<sub>id</sub>, K<sub>c</sub>

In the step 1, client or user `C` sends C<sub>id</sub>, K<sub>c</sub> to the authentication server.

The C<sub>id</sub> can be the username or user id of the user `C`.

K<sub>c</sub> can be the password that the user inputted.

### Step 2: A &rarr; C: Ticket<sub>CS</sub>

In the step 2, the authentication server verifies the information of the user id C<sub>id</sub> and the key K<sub>c</sub> in the system. If the information is correct, a ticket Ticket<sub>CS</sub> will be returned to the client.

Client will use the ticket Ticket<sub>CS</sub> in the next requests to access resources on the server `S`.

Recently, people prefer to use [JSON Web Tokens - JWT](https://jwt.io) to implement the ticket Ticket<sub>CS</sub>.
In the approach, the ticket Ticket<sub>CS</sub> can be formulated as a triple of `header`, `payload`, and {header, payload}<sub>K</sub>, where {header, payload}<sub>K</sub> denotes that the information of `header` and `payload` is encrypted by a key `K`.
{header, payload}<sub>K</sub> can be seen as a signature signed by the authentication server `A` and only the server `A` can verify if Ticket<sub>CS</sub> is valid or not.
We design Ticket<sub>CS</sub>, we can choose the key `K` as a secret key in symmetric encryption or a public key in asymmetric encryption.

### Step 3: C &rarr; S: request, Ticket<sub>CS</sub>

In the step 3, the client sends requests to the server `S`. The ticket Ticket<sub>CS</sub> is attached in each request to the server `S`. `S` can be a resource server or it can be a gateway server for several resource servers in the architecture of micro-services.
The server `S` needs to verify Ticket<sub>CS</sub> to make sure that the request is authenticated before the server `S` performs the request from `C`.
The server `S` may need to ask the authentication server `A` to verify the ticket or it verifies the ticket itself, before the server `S` performs the request from `C`.

When [JSON Web Tokens - JWT](https://jwt.io) is used, Ticket<sub>CS</sub> can be verified by using the knowledge of the key `K` if symmetric encryption is used, or by using the knowedge of the private key for the public key `K` if asymmetric encryption is used.

### Step 4: S &rarr; C: result

In the step 4, the server `S` returns the result to the client `C`.

## The problem of the authentication process

TODO:
