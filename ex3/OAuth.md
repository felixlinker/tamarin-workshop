
# The (Simplified) OAuth 2.0 Authorization Framework

## Note

This specification has been altered for educational purposes.
It presents a simplified version of OAuth 2.0 and should not be taken as reference for OAuth 2.0.

## Introduction

OAuth introduces an authorization layer with which clients can request access to resources controlled by an end-user and hosted by a resource server.
Using OAuth, clients obtain an access token -- a string denoting a specific scope, lifetime, and other access attributes.
Access tokens are issued to third-party clients by an authorization server with the approval of the end-user.
The client uses the access token to access the protected resources hosted by the resource server.

For example, an end-user can grant a printing service (client) access to her protected photos stored at a photo-sharing service (resource server), without sharing her username and password with the printing service.
Instead, she authenticates directly with a server trusted by the photo-sharing service (authorization server), which issues the printing service delegation-specific credentials (access token).

This specification is designed for use with HTTP.
The use of OAuth over any protocol other than HTTP is out of scope.

### Roles

OAuth defines four roles:

* **end-user** An entity capable of granting access to a protected resource.
* **resource server** The server hosting the protected resources, capable of accepting and responding to protected resource requests using access tokens.
* **client** An application making protected resource requests on behalf of the end-user and with its authorization.
The term "client" does not imply any particular implementation characteristics (e.g.,
whether the application executes on a server, a desktop, or other devices).
* **authorization server** The server issuing access tokens to the client after successfully authenticating the end-user and obtaining authorization.

The interaction between the authorization server and resource server is beyond the scope of this specification.
The authorization server may be the same server as the resource server or a separate entity.
A single authorization server may issue access tokens accepted by multiple resource servers.

### Authorization Code

An authorization code is a credential representing the end-user's authorization (to access its protected resources) used by the client to obtain an access token.
The authorization code is obtained by using an authorization server by the client directing the end-user to an authorization server (via its user-agent), which in turn directs the end-user back to the client with the authorization code.
Before directing the end-user back to the client with the authorization code, the authorization server authenticates the end-user and obtains authorization.

### Access Token

Access tokens are credentials used to access protected resources.
An access token is a string representing an authorization issued to the client.
The string is usually opaque to the client.
Tokens represent specific scopes and durations of access, granted by the end-user, and enforced by the resource server and authorization server.

### TLS

<!-- TODO: Phrase this better -->

Implementations MUST always use Transport Layer Security (TLS) and `https://` redirect URIs respectively.

# Client Registration

Before initiating the protocol, the client registers with the authorization server.
The means through which the client registers with the authorization server are beyond the scope of this specification but typically involve end-user interaction with an HTML registration form.

Client registration does not require a direct interaction between the client and the authorization server.
When supported by the authorization server, registration can rely on other means for establishing trust and obtaining the required client properties.
For example, registration can be accomplished using a self-issued or third-party-issued assertion,
or by the authorization server performing client discovery using a trusted channel.

When registering a client, the client developer MUST provide its client redirection URIs as described in Section [Redirection Endpoint](#redirection-endpoint).

### Client Types

This specification assumes clients capable of maintaining the confidentiality of shared secrets.

### Client Identifier

The authorization server issues the registered client a client identifier -- a unique string representing the registration information provided by the client.
The client identifier is not a secret; it is exposed to the end-user and MUST NOT be used alone for client authentication.
The client identifier is unique to the authorization server.

### Client Authentication

The authorization server issues the registered client a client password to establish a client authentication method.
The client password size is left undefined by this specification.
The authorization server MUST support including the
client credentials in the request-body using the following
parameters:

* **client_id** REQUIRED. The client identifier issued to the client during the registration process described by Section [Client Identifier](#client-identifier).
* **client_secret** REQUIRED. The client secret.

The parameters can only be transmitted in the request-body and MUST NOT be included in the request URI.

## Protocol Endpoints

The authorization process utilizes two authorization server endpoints (HTTP resources):

* Authorization endpoint - used by the client to obtain authorization from the end-user via user-agent redirection.
* Token endpoint - used by the client to exchange an authorization grant for an access token, typically with client authentication.

As well as one client endpoint:

* Redirection endpoint - used by the authorization server to return responses containing authorization credentials to the client via the end-user user-agent.

### Authorization Endpoint

The authorization endpoint is used to interact with the end-user and obtain an authorization grant.
The authorization server MUST first verify the identity of the end-user by verifying the client secret established in [Client Authentication](#client-authentication).

#### Redirection Endpoint

After completing its interaction with the end-user, the authorization server directs the end-user's user-agent back to the client.
The authorization server redirects the user-agent to the client's redirection endpoint previously established with the authorization server during the client registration process or when making the authorization request.

##### Registration Requirements

The authorization server MUST require all clients to register their redirection endpoint prior to utilizing the authorization endpoint.
The authorization server MAY allow the client to register multiple redirection endpoints.

For every redirection URI included in an authorization request, the authorization server MUST compare and match the value received against at least one of the registered redirection URIs.
The authorization server MUST compare URIs using simple string comparison.

### Token Endpoint

The token endpoint is used by the client to obtain an access token by presenting its authorization grant.
The means through which the client obtains the location of the token endpoint are beyond the scope of this specification, but the location is typically provided in the service documentation.

The client MUST use the HTTP "POST" method when making access token requests.

#### Client Authentication

Clients MUST authenticate with the authorization server as described in Section [Client Authentication](#client-authentication) when making requests to the token endpoint.

A client MUST send its "client_id" to prevent itself from inadvertently accepting a code intended for a client with a different "client_id".
This protects the client from substitution of the authentication code.

### Access Token Scope

The authorization and token endpoints allow the client to specify the scope of the access request using the "scope" request parameter.
In turn, the authorization server uses the "scope" response parameter to inform the client of the scope of the access token issued.

The value of the scope parameter is expressed as a list of space-delimited, case-sensitive strings.

## Obtaining Authorization

To request an access token, the client obtains authorization from the end-user.
The authorization is expressed in the form of an authorization code, which the client uses to request the access token.

### Authorization Code Grant

The authorization code grant type is used to obtain access tokens.
Since this is a redirection-based flow, the client must be capable of interacting with the end-user's user-agent (typically a web browser) and capable of receiving incoming requests (via redirection) from the authorization server.

```
+----------+
| Resource |
|   Owner  |
|          |
+----------+
     ^
     |
    (2)
+----|-----+          Client Identifier      +---------------+
|         -+----(1)-- & Redirection URI ---->|               |
|  User-   |                                 | Authorization |
|  Agent  -+----(2)-- User authenticates --->|     Server    |
|          |                                 |               |
|         -+----(3)-- Authorization Code ---<|               |
+-|----|---+                                 +---------------+
  |    |                                         ^      v
 (1)  (3)                                        |      |
  |    |                                         |      |
  ^    v                                         |      |
+---------+                                      |      |
|         |>---(4)-- Authorization Code ---------'      |
|  Client |          & Redirection URI                  |
|         |                                             |
|         |<---(5)----- Access Token -------------------'
+---------+
```

Note: The lines illustrating steps (1), (2), and (3) are broken into two parts as they pass through the user-agent.

The flow illustrated above includes the following steps:

1. The client initiates the flow by directing the end-user's user-agent to the authorization endpoint.
The client includes its client identifier, requested scope, local state, and a redirection URI to which the authorization server will send the user-agent back once access is granted (or denied).
2. The authorization server authenticates the end-user (via the user-agent) and establishes whether the end-user grants or denies the client's access request.
3. Assuming the end-user grants access, the authorization server redirects the user-agent back to the client using the redirection URI provided earlier (in the request or during client registration).
The redirection URI includes an authorization code and any local state provided by the client earlier.
4. The client requests an access token from the authorization server's token endpoint by including the authorization code received in the previous step.
When making the request, the client authenticates with the authorization server.
The client includes the redirection URI used to obtain the authorization code for verification.
5. The authorization server authenticates the client, validates the authorization code, and ensures that the redirection URI received matches the URI used to redirect the client in step (3).
If valid, the authorization server responds back with an access token.

#### Authorization Request

The client constructs the request URI by adding the following parameters to the query component of the authorization endpoint URI using the "application/x-www-form-urlencoded" format:

* **client_id** REQUIRED. The client identifier as described in Section [Client Identifier](#client-identifier).
* **redirect_uri** REQUIRED. As described in Section [Redirection Endpoint](#redirection-endpoint).
* **scope** OPTIONAL. The scope of the access request as described by Section [Access Token Scope](#access-token-scope).
* **state** RECOMMENDED. An opaque value used by the client to maintain state between the request and callback.
The authorization server includes this value when redirecting the user-agent back to the client.
The parameter SHOULD be used for preventing cross-site request forgery.

The client directs the end-user to the constructed URI using an HTTP redirection response, or by other means available to it via the user-agent.

For example, the client directs the user-agent to make the following HTTP request using TLS (with extra line breaks for display purposes only):

```
GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz
   &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
Host: server.example.com
```

The authorization server validates the request to ensure that all required parameters are present and valid.
If the request is valid, the authorization server authenticates the end-user and obtains an authorization decision (by asking the end-user or by establishing approval via other means).

When a decision is established, the authorization server directs the user-agent to the provided client redirection URI using an HTTP redirection response, or by other means available to it via the user-agent.

#### Authorization Response

If the end-user grants the access request, the authorization server issues an authorization code and delivers it to the client by adding the following parameters to the query component of the redirection URI using the "application/x-www-form-urlencoded" format:

* **code** REQUIRED. The authorization code generated by the authorization server.
The client MUST NOT use the authorization code more than once.
If an authorization code is used more than once, the authorization server MUST deny the request and SHOULD revoke (when possible) all tokens previously issued based on that authorization code.
The authorization code is bound to the client identifier and redirection URI.

* **state** REQUIRED if the "state" parameter was present in the client authorization request.
The exact value received from the client.

For example, the authorization server redirects the user-agent by sending the following HTTP response:

```
HTTP/1.1 302 Found
Location: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA
         &state=xyz
```

#### Access Token Request

The client makes a request to the token endpoint by sending the following parameters using the "application/x-www-form-urlencoded"
format with a character encoding of UTF-8 in the HTTP request entity-body:

* **code** REQUIRED. The authorization code received from the authorization server.
* **client_id** REQUIRED.

The client MUST authenticate with the authorization server as described in Section [Client Authentication](#client-authentication).

For example, the client makes the following HTTP request using TLS (with extra line breaks for display purposes only):

```
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA
```

The authorization server MUST:

* authenticate the client,
* ensure that the authorization code was issued to the authenticated client,
* verify that the authorization code is valid, and

#### Access Token Response

If the access token request is valid and authorized, the authorization server issues an access token as described in Section [Issuing an Access Token](#issuing-an-access-token).
If the request client authentication failed or is invalid, the authorization server returns an error response.

An example successful response:

```
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Pragma: no-cache

{
   "access_token":"2YotnFZFEjr1zCsicMWpAA",
   "token_type":"example",
   "example_parameter":"example_value"
}
```

## Issuing an Access Token

If the access token request is valid and authorized, the authorization server issues an access token.
If the request failed client authentication or is invalid, the authorization server returns an error response.

### Successful Response

The authorization server issues an access token, and constructs the response by adding the following parameters to the entity-body of the HTTP response with a 200 (OK) status code:

* **access_token** REQUIRED. The access token issued by the authorization server.
* **scope** OPTIONAL, if identical to the scope requested by the client; otherwise, REQUIRED.
The scope of the access token as described by Section [Access Token Scope](#access-token-scope).

The parameters are included in the entity-body of the HTTP response using the "application/json" media type.
The parameters are serialized into a JavaScript Object Notation (JSON) structure by adding each parameter at the highest structure level.
Parameter names and string values are included as JSON strings.
Numerical values are included as JSON numbers.
The order of parameters does not matter and can vary.

For example:

```
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Pragma: no-cache

{
   "access_token":"2YotnFZFEjr1zCsicMWpAA",
   "token_type":"example",
   "example_parameter":"example_value"
}
```
