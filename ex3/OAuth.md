
# The OAuth 2.0 Authorization Framework

## Introduction

OAuth introduces an authorization layer with which clients can request access to resources controlled by a resource owner and hosted by a resource server.
Using OAuth, clients obtain an access token -- a string denoting a specific scope, lifetime, and other access attributes.
Access tokens are issued to third-party clients by an authorization server with the approval of the resource owner.
The client uses the access token to access the protected resources hosted by the resource server.

For example, an end-user (resource owner) can grant a printing service (client) access to her protected photos stored at a photo-sharing service (resource server), without sharing her username and password with the printing service.
Instead, she authenticates directly with a server trusted by the photo-sharing service (authorization server), which issues the printing service delegation-specific credentials (access token).

This specification is designed for use with HTTP.
The use of OAuth over any protocol other than HTTP is out of scope.

### Roles

OAuth defines four roles:

* **resource owner** An entity capable of granting access to a protected resource.
When the resource owner is a person, it is referred to as an end-user.
* **resource server** The server hosting the protected resources, capable of accepting and responding to protected resource requests using access tokens.
* **client** An application making protected resource requests on behalf of the resource owner and with its authorization.
The term "client" does not imply any particular implementation characteristics (e.g.,
whether the application executes on a server, a desktop, or other devices).
* **authorization server** The server issuing access tokens to the client after successfully authenticating the resource owner and obtaining authorization.

The interaction between the authorization server and resource server is beyond the scope of this specification.
The authorization server may be the same server as the resource server or a separate entity.
A single authorization server may issue access tokens accepted by multiple resource servers.

### Protocol Flow

```
+--------+                               +---------------+
|        |--(1)- Authorization Request ->|   Resource    |
|        |                               |     Owner     |
|        |<-(2)-- Authorization Grant ---|               |
|        |                               +---------------+
|        |
|        |                               +---------------+
|        |--(3)-- Authorization Grant -->| Authorization |
| Client |                               |     Server    |
|        |<-(4)----- Access Token -------|               |
|        |                               +---------------+
|        |
|        |                               +---------------+
|        |--(5)----- Access Token ------>|    Resource   |
|        |                               |     Server    |
|        |<-(6)--- Protected Resource ---|               |
+--------+                               +---------------+
```

The abstract OAuth 2.0 flow illustrated avoce describes the interaction between the four roles and includes the following steps:

1. The client requests authorization from the resource owner.
The authorization request can be made directly to the resource owner (as shown), or preferably indirectly via the authorization server as an intermediary.
2. The client receives an authorization grant, which is a credential representing the resource owner's authorization,
expressed using one of four grant types defined in this specification or using an extension grant type.
The authorization grant type depends on the method used by the client to request authorization and the types supported by the authorization server.
3. The client requests an access token by authenticating with the authorization server and presenting the authorization grant.
4. The authorization server authenticates the client and validates the authorization grant, and if valid, issues an access token.
5. The client requests the protected resource from the resource server and authenticates by presenting the access token.
6. The resource server validates the access token, and if valid,
serves the request.

The preferred method for the client to obtain an authorization grant from the resource owner (depicted in steps (1) and (2)) is to use the authorization server as an intermediary, which is illustrated in Section [Authorization Code Grant](#authorization-code-grant).

### Authorization Grant

An authorization grant is a credential representing the resource owner's authorization (to access its protected resources) used by the client to obtain an access token.
This specification defines one grant type: authorization code.

#### Authorization Code

The authorization code is obtained by using an authorization server as an intermediary between the client and resource owner.
Instead of requesting authorization directly from the resource owner, the client directs the resource owner to an authorization server (via its user-agent), which in turn directs the resource owner back to the client with the authorization code.

Before directing the resource owner back to the client with the authorization code, the authorization server authenticates the resource owner and obtains authorization.
Because the resource owner only authenticates with the authorization server, the resource owner's credentials are never shared with the client.

### Access Token

Access tokens are credentials used to access protected resources.
An access token is a string representing an authorization issued to the client.
The string is usually opaque to the client.
Tokens represent specific scopes and durations of access, granted by the resource owner, and enforced by the resource server and authorization server.

### TLS

Implementations MUST use Transport Layer Security (TLS) whenever they establish connections of any kind.

# Client Registration

Before initiating the protocol, the client registers with the authorization server.
The means through which the client registers with the authorization server are beyond the scope of this specification but typically involve end-user interaction with an HTML registration form.

Client registration does not require a direct interaction between the client and the authorization server.
When supported by the authorization server, registration can rely on other means for establishing trust and obtaining the required client properties (e.g., redirection URI, client type).
For example, registration can be accomplished using a self-issued or third-party-issued assertion,
or by the authorization server performing client discovery using a trusted channel.

When registering a client, the client developer SHALL provide its client redirection URIs as described in Section [Redirection Endpoint](#redirection-endpoint).

### Client Types

OAuth assumes clients capable of maintaining the confidentiality of shared secrets.

### Client Identifier

The authorization server issues the registered client a client identifier -- a unique string representing the registration information provided by the client.
The client identifier is not a secret; it is exposed to the resource owner and MUST NOT be used alone for client authentication.
The client identifier is unique to the authorization server.

The client identifier string size is left undefined by this specification.
The client should avoid making assumptions about the identifier size.
The authorization server SHOULD document the size of any identifier it issues.

### Client Authentication

The authorization server issues the registered client a client password to establish a client authentication method.
The client password size is left undefined by this specification.
The authorization server MUST support including the
client credentials in the request-body using the following
parameters:

* **client_id** REQUIRED. The client identifier issued to the client during the registration process described by Section [Client Identifier](#client-identifier).
* **client_secret** REQUIRED. The client secret.

The parameters can only be transmitted in the request-body and MUST NOT be included in the request URI.

Since client authentication method involves a password, the authorization server MUST protect any endpoint utilizing it against brute force attacks.

## Protocol Endpoints

The authorization process utilizes two authorization server endpoints (HTTP resources):

* Authorization endpoint - used by the client to obtain authorization from the resource owner via user-agent redirection.
* Token endpoint - used by the client to exchange an authorization grant for an access token, typically with client authentication.

As well as one client endpoint:

* Redirection endpoint - used by the authorization server to return responses containing authorization credentials to the client via the resource owner user-agent.

### Authorization Endpoint

The authorization endpoint is used to interact with the resource owner and obtain an authorization grant.
The authorization server MUST first verify the identity of the resource owner by verifying the client secret established in [Client Authentication](#client-authentication).

#### Response Type

The authorization endpoint is used by the authorization code grant type and implicit grant type flows.
The client informs the authorization server of the desired grant type using the following parameter:

* **response_type** REQUIRED. The value MUST be "code" for requesting an authorization code as described by Section 4.1.1, [...].

#### Redirection Endpoint

After completing its interaction with the resource owner, the authorization server directs the resource owner's user-agent back to the client.
The authorization server redirects the user-agent to the client's redirection endpoint previously established with the authorization server during the client registration process or when making the authorization request.

##### Registration Requirements

The authorization server SHOULD require all clients to register their redirection endpoint prior to utilizing the authorization endpoint.

The authorization server SHOULD require the client to provide the complete redirection URI (the client MAY use the "state" request parameter to achieve per-request customization).
If requiring the registration of the complete redirection URI is not possible, the authorization server SHOULD require the registration of the URI scheme, authority, and path (allowing the client to dynamically vary only the query component of the redirection URI when requesting authorization).

The authorization server MAY allow the client to register multiple redirection endpoints.

##### Dynamic Configuration

If multiple redirection URIs have been registered, if only part of the redirection URI has been registered, or if no redirection URI has been registered, the client MUST include a redirection URI with the authorization request using the "redirect_uri" request parameter.

When a redirection URI is included in an authorization request, the authorization server MUST compare and match the value received against at least one of the registered redirection URIs (or URI components), if any redirection URIs were registered.
If the client registration included the full redirection URI, the authorization server MUST compare the two URIs using simple string comparison.

##### Invalid Endpoint

If an authorization request fails validation due to a missing,
invalid, or mismatching redirection URI, the authorization server SHOULD inform the resource owner of the error and MUST NOT automatically redirect the user-agent to the invalid redirection URI.

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

To request an access token, the client obtains authorization from the resource owner.
The authorization is expressed in the form of an authorization grant, which the client uses to request the access token.
OAuth defines four grant types: authorization code, implicit,
resource owner password credentials, and client credentials.
It also provides an extension mechanism for defining additional grant types.

### Authorization Code Grant

The authorization code grant type is used to obtain both access tokens and refresh tokens and is optimized for confidential clients.
Since this is a redirection-based flow, the client must be capable of interacting with the resource owner's user-agent (typically a web browser) and capable of receiving incoming requests (via redirection)
from the authorization server.

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
+---------+       (w/ Optional Refresh Token)
```

Note: The lines illustrating steps (1), (2), and (3) are broken into two parts as they pass through the user-agent.

The flow illustrated above includes the following steps:

1. The client initiates the flow by directing the resource owner's user-agent to the authorization endpoint.
The client includes its client identifier, requested scope, local state, and a redirection URI to which the authorization server will send the user-agent back once access is granted (or denied).
2. The authorization server authenticates the resource owner (via the user-agent) and establishes whether the resource owner grants or denies the client's access request.
3. Assuming the resource owner grants access, the authorization server redirects the user-agent back to the client using the redirection URI provided earlier (in the request or during client registration).
The redirection URI includes an authorization code and any local state provided by the client earlier.
4. The client requests an access token from the authorization server's token endpoint by including the authorization code received in the previous step.
When making the request, the client authenticates with the authorization server.
The client includes the redirection URI used to obtain the authorization code for verification.
5. The authorization server authenticates the client, validates the authorization code, and ensures that the redirection URI received matches the URI used to redirect the client in step (3).
If valid, the authorization server responds back with an access token and, optionally, a refresh token.

#### Authorization Request

The client constructs the request URI by adding the following parameters to the query component of the authorization endpoint URI using the "application/x-www-form-urlencoded" format, per Appendix B:

* **client_id** REQUIRED. The client identifier as described in Section [Client Identifier](#client-identifier).
* **redirect_uri** OPTIONAL. As described in Section [Redirection Endpoint](#redirection-endpoint).
* **scope** OPTIONAL. The scope of the access request as described by Section [Access Token Scope](#access-token-scope).
* **state** RECOMMENDED. An opaque value used by the client to maintain state between the request and callback.
The authorization server includes this value when redirecting the user-agent back to the client.
The parameter SHOULD be used for preventing cross-site request forgery.

The client directs the resource owner to the constructed URI using an HTTP redirection response, or by other means available to it via the user-agent.

For example, the client directs the user-agent to make the following HTTP request using TLS (with extra line breaks for display purposes only):

```
GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz
   &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
Host: server.example.com
```

The authorization server validates the request to ensure that all required parameters are present and valid.
If the request is valid,
the authorization server authenticates the resource owner and obtains an authorization decision (by asking the resource owner or by establishing approval via other means).

When a decision is established, the authorization server directs the user-agent to the provided client redirection URI using an HTTP redirection response, or by other means available to it via the user-agent.

#### Authorization Response

If the resource owner grants the access request, the authorization server issues an authorization code and delivers it to the client by adding the following parameters to the query component of the redirection URI using the "application/x-www-form-urlencoded" format:

* **code** REQUIRED. The authorization code generated by the authorization server.
The authorization code MUST expire shortly after it is issued to mitigate the risk of leaks.
A maximum authorization code lifetime of 10 minutes is RECOMMENDED. The client MUST NOT use the authorization code more than once.
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

* **grant_type** REQUIRED. Value MUST be set to "authorization_code".
* **code** REQUIRED. The authorization code received from the authorization server.
* **redirect_uri** REQUIRED, if the "redirect_uri" parameter was included in the authorization request as described in Section [Authorization Request](#authorization-request), and their values MUST be identical.
* **client_id** REQUIRED.

The client MUST authenticate with the authorization server as described in Section [Client Authentication](#client-authentication).

For example, the client makes the following HTTP request using TLS (with extra line breaks for display purposes only):

```
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA
&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb
```

The authorization server MUST:

* authenticate the client,
* ensure that the authorization code was issued to the authenticated client,
* verify that the authorization code is valid, and
* ensure that the "redirect_uri" parameter is present if the "redirect_uri" parameter was included in the initial authorization request as described in Section [Authorization Request](#authorization-request), and if included ensure that their values are identical.

#### Access Token Response

If the access token request is valid and authorized, the authorization server issues an access token.
If the request client authentication failed or is invalid, the authorization server returns an error response.

An example successful response:

```
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache

{
   "access_token":"2YotnFZFEjr1zCsicMWpAA",
   "token_type":"example",
   "expires_in":3600,
   "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
   "example_parameter":"example_value"
}
```

## Issuing an Access Token

If the access token request is valid and authorized, the authorization server issues an access token.
If the request failed client authentication or is invalid, the authorization server returns an error response.

### Successful Response

The authorization server issues an access token and optional refresh token, and constructs the response by adding the following parameters to the entity-body of the HTTP response with a 200 (OK) status code:

* **access_token** REQUIRED. The access token issued by the authorization server.
* **expires_in** RECOMMENDED. The lifetime in seconds of the access token.
For example, the value "3600" denotes that the access token will expire in one hour from the time the response was generated.
If omitted, the authorization server SHOULD provide the expiration time via other means or document the default value.
* **scope** OPTIONAL, if identical to the scope requested by the client; otherwise, REQUIRED.
The scope of the access token as described by Section [Access Token Scope](#access-token-scope).

The parameters are included in the entity-body of the HTTP response using the "application/json" media type.
The parameters are serialized into a JavaScript Object Notation (JSON) structure by adding each parameter at the highest structure level.
Parameter names and string values are included as JSON strings.
Numerical values are included as JSON numbers.
The order of parameters does not matter and can vary.

The authorization server MUST include the HTTP "Cache-Control"
response header field with a value of "no-store" in any response containing tokens, credentials, or other sensitive information, as well as the "Pragma" response header field with a value of "no-cache".

For example:

```
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache

{
   "access_token":"2YotnFZFEjr1zCsicMWpAA",
   "token_type":"example",
   "expires_in":3600,
   "example_parameter":"example_value"
}
```

## Accessing Protected Resources

The client accesses protected resources by presenting the access token to the resource server.
The resource server MUST validate the access token and ensure that it has not expired and that its scope covers the requested resource.
The methods used by the resource server to validate the access token (as well as any error responses) are beyond the scope of this specification but generally involve an interaction or coordination between the resource server and the authorization server.
