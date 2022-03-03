# Token Introspection

## Intoduction

Token introspection allows you to ascertain the statuses of your access tokens, that is, if they are active or have expired and also helps check their Time-to-Live (TTL). This, along with the scope associated with each access token, helps you decide if new access tokens have to be generated thereby avoiding instances of your API calls failing due to expired tokens.

## Authentication

This API makes use of the OAuth 2.0 protocol for authorization. For more information about the specific RFC used for this API, see  [OAuth 2.0 Token Introspection](https://tools.ietf.org/html/rfc6749) (RFC7662).

## Endpoint

You use this endpoint to fetch data related to access tokens:

```sh
POST /oauth/v2/introspectToken
```

## API

| Item         | Details                           |
| ------------ | --------------------------------- |
| Operation    | Token Introspection               |
| Method       | POST                              |
| Protocol     | HTTP/1.1                          |
| Content Type | application/x-www-form-urlencoded |

### Request

##### Sample Code

```sh
POST https://www.linkedin.com/oauth/v2/introspectToken
```

You include the following parameters in the request:
| Parameter | Description | Necessity|
| ------ | ------ | ------|
| token | String representing the token returned by the token endpoint | Mandatory |
| client_id    | String representing the client ID of the application | Mandatory |
| client_secret | String representing the client secret of the application | Mandatory |

### Response

##### Sample Code

```sh
{
    "active": true,
    "client_id": "84o1i5mjq59xuv",
    "authorized_at": 1587497291000,
    "created_at": 1587497291083,
    "status": "active",
    "expires_in": 1587497620127,
    "scope": "r_basicprofile,rw_organization,w_share"
}
```

In case of successful token introspection, the following parameters are returned as response:
| Parameter | Description | Necessity |
| ------ | ------ | ------ |
| active | Boolean indicating if the returned token is currently active | Mandatory |
| status | String indicating if the status of the token is active/expired/revoked | Optional |
| scope | String containing comma separated list of scopes associated with the returned token | Optional |
| client_id | String representing the client ID of the application | Optional |
| created_at | Time elapsed in seconds since the January 01, 1970, indicating when the returned token was originally issued | Optional |
| expires_at | Time elapsed in seconds since the January 01, 1970, indicating when the returned token will expire | Optional |
| authorized_at | Time elapsed in seconds since the January 01, 1970, indicating when the returned token was authorized | Optional |

##### HTTP Status Codes

| Status Code | Description                                             |
| ----------- | ------------------------------------------------------- |
| 401         | Invalid client_id OR necessary authorization is missing |
| 429         | Too many requests have been made to the API             |
