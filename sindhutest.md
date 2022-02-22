# Introduction

This document captures the steps to fetch metadata of an access/refresh token.

# Audience

Partner engineering team.


# Use cases

The procedures captured in this document allow you to: 

1.	Look up metadata of a token such as permissions, token TTL, and so on
2.	Know the scope associated with the token you are using
3.	Use the token introspection endpoints 


> Note: Currently, token introspection endpoint is available in the Partnering tool. Exposing a similar endpoint to help partners can resolve any permission related issues.


# Benefits

*	Easy permissions migration
*	The Token introspection endpoint is useful to programmatically generate access token using refresh token when access token expires. You can check TTL to go through the refresh token flow and get a new AT, instead of trying to use an expired access token.
*	Decrease in calls or emails related to failing API with permission issues


# Prerequisites

The given access or refresh token for which you want to fetch the metadata. If you do not have one, refer to the procedure (to cross refer the content) to generate the token. 


# Working with Introspection Endpoint

The objective is to return metadata information if you have the access/refresh token. This is required both in RestliGateway and GraphQLGateway.

As per IETF RFC recommendation, the protected resource calls the introspection endpoint using an HTTP POST request with parameters sent as "application/x-www-form-urlencoded" data.


## Authorization 

To prevent token scanning attacks, RFC recommends the following ways of authorization to access this endpoint:

1.	Client authentication as described in OAuth 2.0 [RFC6749](https://tools.ietf.org/html/rfc6749).
2.	Separate OAuth 2.0 access token such as the bearer token

The approach 1 below is using client authentication and approach 2 is using separate Oauth 2.0 bearer token to access token introspection endpoint.

## Approach 1: Token Introspection for RestliGateway Users

This approach includes adding token introspection endpoint in oauth2 authorization server.

![Workflow!](/images/workflow.JPG "workflow")

1.	Client to call this endpoint by passing token parameter **client_id** and **client_secret** in the request post body for client authentication.
2.	Implement post endpoint in oauth-frontend.
3.	Partner to pass token, **client_id**, and **client_secret**. 
4.	Oauth-frontend validates the client by validating AT. 
5.	Implementation can open the access token and validate with passed in **client_id** and **client_secret** to return the metadata of the access token if validation is successful. 
6.	Oauth-frontend calls login-server to fetch the token metadata information.


## Benefits

1.	Any valid authenticated third party application can access this endpoint with valid **client_id** and **client_secret**.
2.	By adding this endpoint in oauth-frontend, it’s easier for partners to access all the oauth related information at one place.


	> Note: You must add basic fuse limitation to introspection endpoint to counter token phishing attack.



## Metadata

Below are the metadata of the access/refresh token that can be included in the response and is useful to partners(HSEC approval required to finalize these fields):


* Client_Id(required field)
* PersonUrn of the user with the access token. To check with HSEC if it is safe to return this field. This is an optional field in case of 2 legged AT.
* List of scope member permissions that the user has granted for the app in this access token. Optional field in case of 2 legged AT.
* Access token expiry/TTL. This is an optional field. Currently the non-expiry token is not supported but good to have this optional for future use.
* Active (required boolean field). True if AT is active and false in case of expired/revoked. To check with HSEC if we can return status field with active/expired/revoked status.
* Access token created time(required field). 
* Last authorized at(required field).


## Token Introspection Request

Make the following HTTP POST request with a Content-Type header of x-www-form-urlencoded:

### POST

<https://www.linkedin.com/oauth/v2/introspectToken>


| Parameter     | Description                                                                            | Required (Yes/No) |
| ------------- | ---------------------------------------------------------------------------------------| ----------------- |
| client_id     | Application client Id                                                                  | Yes               |
| client_secret | Application client secret                                                              | Yes               |
| token         | The string value of the access token or refresh token returned from the token endpoint | Yes               |


## Sample Request

|POST         | /oauth/v2/introspectToken HTTP/1.1 |
|Host         | www.linkedin.com                   |
|Content-Type | application/x-www-form-urlencoded  |


```

token={your_token}&client_id={your_client_id}&client_secret={your_client_secret}
```

## Token Introspection Response

A successful token introspection request returns a JSON object containing the following fields:


| Parameter     | Description                                                                                                                      | Required (Yes/No) |                             
| ------------- | -------------------------------------------------------------------------------------------------------------------------------- | ----------------- |  
| active        | Boolean indicator of whether or not the presented token is currently active                                                      | Yes               |                                      
| status        | Status of the token                                                                                                              | No                |                                           
| scope         | A JSON string containing comma separated list of scopes associated with this token                                               | No                |                 
| client_id     | Client identifier for the OAuth 2.0 client that requested this token                                                             | No                |             
| created_at    | Integer timestamp, measured in the number of seconds  since January 1 1970 UTC, indicating when this token was originally issued | No                |
| expires_at    | Integer timestamp, measured in the number of seconds since January 1 1970 UTC, indicating when this token will expire            | No                |
| authorized_at | Integer timestamp, measured in the number of seconds since January 1 1970 UTC, indicating when token was authorized              | No                |


# Sample Responses

### Token Revoked

```
curli -d 'token=AQWLmNqvW4JCbEePhbGpLhr7OesejxQC9YhxisJvIY2t6Pv8zCjGksak3NaLt4gBgU1dyV8SdX5X2fA1ebcJ7eVdvwX7Ii_m3pSr_2OveUf4NpAr5vnkeNJXa7av6KBn4IKQhJ7ao0YqI91g6miZ3puQBksYFoTtckOBVvH4z-T_C4it5kcVsGTKcvZn5aQ9JdNABKTLhSeoFu6Q52cRWWaT_jbdHU6E4jjazjNQUKOZiKA6h5pnh2ppSJcQUUwCvBiSfiA2oe205Kp3txqvYkPdkDrEHuCqlUHDixQhWcepWL3pkV4fKLLj9kNJq0X2dCRYLSuTCZXYuye6yYsDfxmwt1Dp_Q&client_id=84o1i5mjq59xuv&client_secret=Yn4GaPGMZSwmG2J3' https://www.linkedin-ei.com/oauth/v2/introspectToken -v -H "Content-Type: application/x-www-form-urlencoded"
  {
    "active": false,
    "client_id": "84o1i5mjq59xuv",
    "created_at": 1587497291083,
    "status": "revoked",
    "expires_in": 1587497620127,
    "scope": "r_basicprofile,rw_organization,w_share"
}
```

### Token Expired 

```
{
    "active": false,
    "client_id": "84o1i5mjq59xuv",
    "authorized_at": 1587497291000,
    "created_at": 1587497291083,
    "status": "expired",
    "expires_in": 1587497620127,
    "scope": "r_basicprofile,rw_organization,w_share"
}
```

### Valid token

```
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

Passed in client information doesn’t match the token information (If client credentials passed in the request doesn’t match with the client information in the access token)

```
{
    "active": false
}
```

### Error Response

| Description	                                            | Error response          |
| --------------------------------------------------------- | ----------------------- |
| If client credentials passed in the request is not valid	| HTTP 401 (Unauthorized) |
| If access token verification results in member_restricted | HTTP 401(Unauthorized)  |
| If app hits fuse throttling for this endpoint	            | HTTP 429                |


# References

*	[OAuth 2.0 Token Introspection](https://tools.ietf.org/html/rfc7662)
*	Implementation proposed in this document is based on the IETF RFC for Token Introspection: <https://tools.ietf.org/html/rfc7662>
