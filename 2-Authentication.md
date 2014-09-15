# Authorization and authentication

Beddit API utilizes the OAuth2 as authentication protocol. In addition to
standard OAuth2 username/password authentication, backend provides the ability
to authenticate as an application. Authentication information must be sent to
backend within every request, in the Authorization HTTP header.

## Token types

Backend supports two token types: application and user tokens.

### Application tokens

Application authentication is used for requests that do not require user
authentication, for example creating a new user. Token is provided in the
HTTP authorization header in following format:

```
Authorization: Application <application id>:<application secret>
```

### User tokens

The user token is granted by backend to particular user using the flow described
below. Token is provided in the authorization header in following format:

```
Authorization: UserToken <user token>
```

## User authentication: Mobile flow

This flow is used in a case where the end user has trusted her password with the
client app. It conforms to OAuth2 specification section
http://tools.ietf.org/html/rfc6749#section-4.3

### POST /api/v1/auth/authorize

x-www-form-urlencoded field | Value
----------|------
grant_type | password
username | _User's username, typically the email address_
password | _User's password_

**Authentication**

Request must be authenticated by Application specific token.

**Response**

```javascript
{
    "user" : 7,
    "access_token" : "2YotnFZFEjr1zCsicMWpAA",
    "token_type" : "example",
    "expires_in" : 3600
}
```

**Errors**

Error identifier | HTTP Status | Description
-----------------|-------------|------------
invalid_credentials | 400 | The password does not match, suggest reset?


## Checking access token information

### GET /api/v1/auth/token_info

Get information about the access token used in the request.

**Authentication**

Request must be authenticated by User specific token.

**Response**

```javascript
{
  "user" : 1231, // Id of the associated user
  "expires_in" : 3600, // Number of seconds before this token expires
  "token_type": "bearer"
}
```
