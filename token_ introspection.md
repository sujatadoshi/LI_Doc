## Token Introspection

This API allows you to fetch metadata information such as permissions granted, token TTL etc., about the access token.

:::note
You can use the Partner Dashboard for introspecting the token. You can go [here](https://www.google.co.in/) to do the same.
:::

## API Endpoint

`POST https://www.linkedin.com/oauth/v2/introspectToken`

## Header 

Use the following header with the POST request. This field is mandatory. 

| ---- | ---- | 
| Content-Type | `application/x-www-form-urlencoded` 

## Request Body

```shell
{ token=<token_to_be_introspected>
&client_id=<client_id>
&client_secret=<client_secret>
}
```

## Request Parameters

| Parameter | Data Type | Description | Required  
| `client_id` | String | The unique client ID of your application. | Mandatory 
| `client_secret` | String | The client secret of your application. | Mandatory 
| `token` | String | The string value of the access token or refresh token returned from the token endpoint. | Mandatory  


## Response 

When the API call is successfully sent, you will recieve any of the following reponses based on your Token state. 
The following responses are returned. 

### Valid Token Response

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

## Response Parameter

| Parameter | Description | Required |
| `active` | Boolean indicator of whether or not the presented token is currently active. | Mandatory
| `status` | The status of your Token | Optional 
| `scope` | A JSON string containing comma-separated list of scopes associated with this token | Optional 
| `client_id`| Client identifier for the OAuth 2.0 client that requested this token. | No
| `created_at` | Integer timestamp, measured in the number of seconds since January 1 1970 UTC, indicating when this token was originally issued | Optional 
| `expires_at` | Integer timestamp, measured in the number of seconds since January 1 1970 UTC, indicating when this token will expire. | Optional 
| `authorized_at` | Integer timestamp, measured in the number of seconds since January 1 1970 UTC, indicating when token was authorized. | Optional | 






