# Booster Cloud Framework REST API

The API for a Booster application is very simple and is fully defined by auth endpoints and the [commands](#commands-and-command-handlers-the-write-pipeline)
and [read models](#read-models-the-read-Pipeline) names and structures.

After a successful deployment you'll see an "Outputs:" section in your terminal with several values that you need to use
when doing requests to the API. Those values are:

- `baseURL`: This is the base URL for all your endpoints
- `clientID`: Needed for authentication/authorization endpoints. This is only shown if there are roles defined in your app.

Note that the `Content-Type` for all requests is `application/json`.

## Authentication and Authorization API

The following endpoints are provisioned if your application have at least one role defined. For more information about how
to use roles to restrict the access to your application, see the section [Authentication and Authorization](#authentication-and-authorization).

### Sign-up
Register a user in your application. After a successful invocation, an email will be sent to the user's inbox
with a confirmation link. **Users's won't be able to sign-in before they click in that link**.
###### Endpoint
```http request
POST https://<baseURL>/auth/sign-up
```
###### Request body
> Sign-up response body

```json
{
	"clientId": "string",
	"username": "string",
	"password": "string",
	"userAttributes": {
   		"roles": ["string"]
	}
}
```

Parameter | Description
--------- | -----------
_clientId_ | The application client Id that you got as an output when the application was deployed.
_username_ | The username of the user you want to register. It **must be an email**.
_password_ | The password the user will use to later login into your application and get access tokens.
_userAttributes_ | Here you can specify the attributes of your user. These are:
* _roles_  An array of roles this user will have. You can only specify here roles with the property `allowSelfSignUp = true`

###### Response
An empty body

###### Errors
> Sign-up error response body example: Not specifiying an email as username.

```json
{
    "__type": "InvalidParameterException",
    "message": "Username should be an email."
}
```

You will get a HTTP status code different from 2XX and a body with a message telling you the reason of the error.

### Sign-in
Allows your users to get tokens to be able to make request to restricted endpoints.
Remember that before a user can be signed in into your application, **its email must be confirmed**

###### Endpoint
```http request
POST https://<baseURL>/auth/sign-in
```
###### Request body
> Sign-in request body

```json
{
	"clientId":"string",
	"username":"string",
	"password":"string"
}
```

Parameter | Description
--------- | -----------
_clientId_ | The application client Id that you got as an output when the application was deployed.
_username_ | The username of the user you want to sign in. It must be previously signed up
_password_ | The password used to sign up the user.

###### Response
> Sign-in response body

```json
{
    "accessToken": "string",
    "expiresIn": "string",
    "refreshToken": "string",
    "tokenType": "string"
}
```

Parameter | Description
--------- | -----------
_accessToken_ | The token you can use to access restricted resources. It must be sent in the `Authorization` header (prefixed with the `tokenType`)
_expiresIn_ | The period of time, in seconds, after which the token will expire
_refreshToken_ | The token you can use to get a new access token after it has expired.
_tokenType_ | The type of token used. It is always `Bearer`

###### Errors
> Sign-in error response body example: Login of an user that has not been confirmed

```json
{
    "__type": "UserNotConfirmedException",
    "message": "User is not confirmed."
}
```

You will get a HTTP status code different from 2XX and a body with a message telling you the reason of the error.

### Sign-out
Finalizes the user session by cancelling their tokens.

###### Endpoint
```http request
POST https://<baseURL>/auth/sign-out
```
###### Request body
> Sign-out request body
```json
{
	"accessToken":"string"
}
```

Parameter | Description
--------- | -----------
_accessToken_ | The access token you get in the sign-in call.

###### Response
An empty body
###### Errors
> Sign-out error response body example: Invalid access token specified

```json
{
    "__type": "NotAuthorizedException",
    "message": "Invalid Access Token"
}
```

You will get a HTTP status code different from 2XX and a body with a message telling you the reason of the error.

## Write API (commands submission)

- [ ] TODO: Improve this documentation

`POST https://<baseURL>/commands`

#### Request body:

```json
{
  "typeName": "ChangeCartItem",
  "version": 1,
  "value": {
    "cartId": "demo",
    "sku": "ABC-10",
    "quantity": 1
  }
}
```

## Read API (retrieve a read model)

- [ ] Improve this documentation

### Get a list

`GET https://<baseURL>/readmodels/<read model class name>`

Example:

`GET https://<baseURL>/readmodels/CartReadModel`

### Get a specific read model

`GET https://<baseURL>/readmodels/<read model class name>/<read model ID>`

Example:

`GET https://<baseURL>/readmodels/CartReadModel/42`