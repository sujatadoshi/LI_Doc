## Token Introspection API

This API allows you to fetch the metadata of your access token such as token status, ttl (time-to-live), the scope of permissions granted, and others.

> **Note**: 
You can also use the Partner Dashboard for introspecting the token metadata. Read the [Partner Dashboard Documentation](https://www.google.co.in/) to learn more. 

## API Endpoint

`POST https://www.linkedin.com/oauth/v2/introspectToken`

## Header 

Use the following header with the POST request.  

| Header Name | Description | Required | 
| ---- | ---- | ----- | 
| Content-Type | Set this field to `application/x-www-form-urlencoded` | Mandatory | 


## Request 

```
{
token=<token_to_be_introspected>
&client_id=<client_id>
&client_secret=<client_secret>
}
```

## Request Parameters

| Parameter | Data Type | Description | Required  |
| --------- | -------- | ------- | ----- |
| `client_id` | String | The unique Client ID of your application. | Mandatory |
| `client_secret` | String | The Client Secret of your application. | Mandatory |
| `token` | String | The access token or refresh token returned from the token endpoint. | Mandatory  |


## Response

```json
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

## Response Parameters

| Parameter | Data Type | Description |
| ------- | ------- | ----- |
| `active` | Boolean | The indicator of whether or not the presented token is currently active. Returned values are `true` or `false`. The value `false` is returned if the client credentials passed in the request do not match the client information in the access token. | 
| `client_id`| String| Client identifier for the OAuth 2.0 client that requested this token. | 
| `authorized_at` | Integer | The timestamp that indicates the time of token authorization. It is measured in the number of seconds since January 1, 1970 UTC. | 
| `created_at` | Integer | The timestamp that indicates the time of token creation. It is measured in the number of seconds since January 1, 1970 UTC. | 
| `expires_at` | Integer | The timestamp that indicates the time of token expiry. It is measured in the number of seconds since January 1, 1970 UTC.  | 
| `status` | String | The status of your token. The token statuses that are returned are `active`, `expired`, or `revoked`. | 
| `scope` | String | A list of comma-separated values of the scopes associated with this token.  |  

## Error Codes

| Error Code | Description |
|----- | ------ | 
| `HTTP 401 (Unauthorized)` | Returned when the client credentials passed in the request are not valid. Check your credentials and try again. |
| `HTTP 401 (Unauthorized)` | Returned when the access token verification results in `member_restricted`. Check the member permissions and try again. |
| `HTTP 429` | Returned when the app hits the fuse throttling for this endpoint. |
