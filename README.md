# auth
Authentication and Authorisation for micro-services

## Authentication

This is the procedure for the authentication that I see people are using in practice.
```
Step 1: C -> A: C<sub>id</sub>, K<sub>c</sub> 
Step 2: A -> C: Ticket<sub>CS</sub>
Step 3: C -> S: request, Ticket<sub>CS</sub>
Step 4: S -> C: result
```

In the step 1, client or user `C` sends C<sub>id</sub>, K<sub>c</sub> to the authentication server.

The C<sub>id</sub> can be the username or user id of the user `C`.

K<sub>c</sub> can be the password that the user inputted.

In the step 2, the authentication server verifies the information of the user id <mark>C<sub>id</sub></mark> and the key K<sub>c</sub> in the system. If the information is correct, a ticket Ticket<sub>CS</sub> will be returned to the client.

Client will use the ticket Ticket<sub>CS</sub> in the next requests to access resources on the server `S`.

Recently, people prefer to use [JSON Web Tokens - JWT](https://jwt.io) to implement the ticket Ticket<sub>CS</sub>.
In the approach, the ticket Ticket<sub>CS</sub> can be formulated as a triple of `header`, `payload`, and {header,payload}<sub>K</sub>, where {header,payload}<sub>K</sub> denotes that the information of `header` and `payload` is encrypted by a secret key `K`.
{header,payload}<sub>K</sub> can be seen as a signature signed by the authentication server `A` and only the server `A` can verify if the Ticket<sub>CS</sub> is valid or not.


In the step 3, the client sends requests to the server `S`. The ticket Ticket<sub>CS</sub> is attached in each request to the server `S`. The server `S` will ask the server `A` to verify the ticket or it verifies the ticket itself, before the server `S` performs the request from `C`.

In the step 4, the server `S` returns the result to the client `C`.
