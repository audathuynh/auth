# auth
Authentication and Authorisation for micro-services

## Authentication

This is the procedure of authentication for micro-services that is often used in practice.

### Step 1: C &rarr; A: C<sub>id</sub>, K<sub>c</sub>

In the step 1, client or user `C` sends C<sub>id</sub>, K<sub>c</sub> to the authentication server `A`.

The C<sub>id</sub> can be the username or user id of the user `C`.

K<sub>c</sub> can be the password that the user inputted.

### Step 2: A &rarr; C: T<sub>CS</sub>

In the step 2, the authentication server `A` verifies the information of the user id C<sub>id</sub> and the key K<sub>c</sub> in the system. If the information is correct, a ticket T<sub>CS</sub> will be returned to the client.

Client will use the ticket T<sub>CS</sub> in the next requests to access resources on the server `S` in the next step.

When [JSON Web Tokens - JWT](https://jwt.io) is used to implement the ticket T<sub>CS</sub>, the ticket T<sub>CS</sub> is formulated as a triple of `header`, `payload`, and {header, payload}<sub>K</sub>, where {header, payload}<sub>K</sub> denotes that the information of `header` and `payload` is encrypted by a key `K`.
{header, payload}<sub>K</sub> can be seen as a signature signed by the authentication server `A`.
When we design T<sub>CS</sub>, the key `K` can be a secret key in symmetric encryption or a public key in asymmetric encryption.

### Step 3: C &rarr; S: request, T<sub>CS</sub>

In the step 3, the client sends requests to the server `S`. The ticket T<sub>CS</sub> is attached in each request to the server `S`. The server `S` can be a resource server or it can be a gateway server for several resource servers in the architecture of micro-services.
The server `S` needs to verify T<sub>CS</sub> to make sure that the request is authenticated before the server `S` performs the request from the client `C`.
In the process, the server `S` may need to ask the authentication server `A` to verify the ticket or it may verify the ticket itself, before the server `S` performs the request from `C`.

When [JSON Web Tokens - JWT](https://jwt.io) is used, T<sub>CS</sub> can be verified by using the knowledge of the key `K` if symmetric encryption is used, or by using the knowedge of the private key for the public key `K` if asymmetric encryption is used.

### Step 4: S &rarr; C: result

In the step 4, the server `S` returns the result to the client `C`.

## The problems of the authentication process when JWT is used

When JWT is used, the ticket is designed so that the server can verify it. There are problems with the solution.

### Problem 1

I am the client, I have just sent you (the server) my username and password and I have received a JWT ticket. 
How do I verify that the JWT ticket I have obtained is really from you?

### Problem 2

I assume that the ticket can be verified that it is from you. 
Is the ticket for me only? Can other people steal and use it?

### Problem 3

I have loggoned on different browsers, can each ticket unique for each session on each browser?
