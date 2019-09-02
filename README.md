# auth
Authentication and Authorisation for micro-services

## Interaction model with JSON Web Tokens (JWT)

This is the procedure of authentication for micro-services that is often used in practice.

### Step 1: C &rarr; A: C<sub>id</sub>, K<sub>c</sub>

In step 1, client or user `C` sends C<sub>id</sub> and K<sub>c</sub> to the authentication server `A`.

The C<sub>id</sub> can be the username or user id of the user `C`.

K<sub>c</sub> can be the password that the user inputted.

### Step 2: A &rarr; C: T<sub>CS</sub>

In step 2, the authentication server `A` verifies the information of the user id C<sub>id</sub> and the key K<sub>c</sub> in the system. If the information is correct, a ticket T<sub>CS</sub> will be returned to the client `C`.

The client `C` will use the ticket T<sub>CS</sub> in the requests to access resources on the server `S` in the next step.

When [JSON Web Tokens - JWT](https://jwt.io) is used to implement the ticket T<sub>CS</sub>, the ticket T<sub>CS</sub> is formulated as a triple of `header`, `payload`, and {header, payload}<sub>K</sub>, where {header, payload}<sub>K</sub> denotes that the information of `header` and `payload` is encrypted by a secret key `K`.
{header, payload}<sub>K</sub> can be seen as a signature signed by the authentication server `A`.

### Step 3: C &rarr; S: T<sub>CS</sub>, request

In step 3, the client `C` sends requests to the server `S`. The ticket T<sub>CS</sub> is attached in each request to the server `S`. The server `S` can be a resource service or it can be a gateway server for accessing several resource services in the architecture of micro-services.
The server `S` needs to verify T<sub>CS</sub> to make sure that the request is authenticated before the server `S` performs the request from the client `C`.
In the process, the server `S` may need to ask the authentication server `A` to verify the ticket or it may verify the ticket itself, before the server `S` performs the request from `C`.

When [JSON Web Tokens - JWT](https://jwt.io) is used, the ticket T<sub>CS</sub> can be verified by using the knowledge of the secrete key `K` and the algorithm that we used to obtain {header, payload}<sub>K</sub>.

### Step 4: S &rarr; C: result

In step 4, the server `S` returns the result to the client `C`.

## Problems with the authentication in the model

When JWT is used, the ticket is designed so that the server can verify it. There are problems with the solution.

### Problem 1

I am the client, I have just sent you (the authentication server `A`) my username and password, and I have received a JWT ticket. 
How do I verify that the JWT ticket I have obtained is really from you?

### Problem 2

I assume that the ticket can be verified that it is from you. 
Is the ticket for me only? Can other people steal and use it? Can someone just use a ticket which they know it can be verified and send it to me so that I would use the ticket to send my important data in my requests?

### Problem 3

I have used my username and password to log-on on different apps. 
Can each ticket be used only in each session on each app?

### Problem 4

I have just sent you, the server `S`, a request with a ticket from you, and I have received a result. 
How do I verify that the result is for the request data I have sent to `S` and it is not from someone else?

## Problems with the authorisation in the model

As mentioned above, `S` in the interaction model is a resource server or can be a gateway to access several resource services.
The ticket T<sub>CS</sub> is used to access any resource server or resource services in the system.
There is no design in the interaction model to say if the client can access a particular resource server or a resource service.

In practice, we may have several resource servers/services in the system. 
Each resource server/service manages several resources inside.
Therefore, a verfication is necessary to check whether the client can access a resource service, before we conduct another check to see if the client can access a particular resource in the resource service.

## A proposed solution for authentication only

In this section, I describe an interaction model to deal with the problems that I have mentioned above.
I have not resolved the issues of authorisation in this proposal yet. 
The problem of authorisation in the model can be resolved easily and will be presented in the next section.

### Step 1: C &rarr; A: C<sub>id</sub>, K<sub>c</sub>, n<sub>1</sub>

In step 1, the client `C` sends C<sub>id</sub>, K<sub>c</sub>, and a nounce n<sub>1</sub> to the server `A`.
The C<sub>id</sub> can be the username or user id of the user `C`.
K<sub>c</sub> can be the password that the user inputted.
The nounce n<sub>1</sub> can be a random string or a timestamp generated by the client `C`.

### Step 2: A &rarr; C: {K<sub>CS</sub>, n<sub>1</sub>}<sub>K<sub>C</sub></sub>, T<sub>CS</sub>={C<sub>id</sub>, t<sub>1</sub>, t<sub>2</sub>, K<sub>CS</sub>}<sub>K<sub>S</sub></sub>

In step 2, the server `A` verifies C<sub>id</sub> and K<sub>C</sub> in the system. 
If it is successful, the server `A` generates a random key K<sub>CS</sub> for the communication between `C` and `S`.
Then the server `A` encrypts the key K<sub>CS</sub> and the nounce n<sub>1</sub> together by using the key K<sub>C</sub> to obtain {K<sub>CS</sub>, n<sub>1</sub>}<sub>K<sub>C</sub></sub> to send to the client `C`.
Since the client `C` knows the key K<sub>C</sub>, the client `C` can decrypt and get the key K<sub>CS</sub> and the nounce n<sub>1</sub> in the result. 
The nounce can be verified by comparing with the nounce that the client `C` generated and sent to `A` in the step 1.

Moreover, a ticket T<sub>CS</sub> for the communication between `C` and `S` is also created and returned to the client `C`.
In the ticket, the values t<sub>1</sub> and t<sub>2</sub> are to define the starting time and ending time for the validity of the ticket.
The ticket T<sub>CS</sub> is encrypted by using a secret key K<sub>S</sub>, which is known by the server `S`.
Therefore, the server `S` can decrypt the ticket T<sub>CS</sub> to get the key K<sub>CS</sub> inside the ticket in the next step.
Asymmetric cryptography can be considered to encrypt and decrypt T<sub>CS</sub> if necessary.

### Step 3: C &rarr; S: {C<sub>id</sub>, n<sub>2</sub>}<sub>K<sub>CS</sub></sub>, T<sub>CS</sub>, request

In step 3, the client `C` uses the key K<sub>CS</sub> obtained from the server `A` in the step 2 to encrypt C<sub>id</sub> and a nounce n<sub>2</sub> to have {C<sub>id</sub>, n<sub>2</sub>}<sub>K<sub>CS</sub></sub>.
The client `C` attachs {C<sub>id</sub>, n<sub>2</sub>}<sub>K<sub>CS</sub></sub> and the ticket T<sub>CS</sub> in each request to send to the server `S`.
The request can be encrypted by using the key K<sub>CS</sub> if necessary.

Since the server `S` knows the key K<sub>S</sub>, it can verify and decrypt the ticket T<sub>CS</sub> to obtain the key K<sub>CS</sub>.
Then the server `S` uses the key K<sub>CS</sub> to decrypt {C<sub>id</sub>, n<sub>2</sub>}<sub>K<sub>CS</sub></sub> in each request to verify C<sub>id</sub> and get the nounce n<sub>2</sub> generated by the client `C`.

### Step 4: S &rarr; C: {n<sub>2</sub>}<sub>K<sub>CS</sub></sub>, result

In this step, the server `S` performs the request to have a result. 
The result can be encrypted by the key K<sub>CS</sub> if neccessary.
The server `S` uses the key K<sub>CS</sub> to encrypt the nounce n<sub>2</sub> to return to the client in the result.
The client `C` can verify the result from the server `S` by using the key K<sub>CS</sub> to get the nounce n<sub>2</sub> and compare it with the nounce that the client `C` generated and sent to `S` in the step 3.

## A proposed solution for authentication and authorisation

More details will be added later.
