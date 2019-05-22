# Web API, Azure AD, Python
This code example demonstrates how to use Authlib with Python to create a Web API that is secured with Azure AD.

## Setup

1. Install miniconda
2. Create a conda environment `conda create -n p36 python=3.6`. 
3. Activate the environment `conda activate p36`
4. Install the requirements `pip install -r requirements.txt`

## Register an AAD App
Register an AAD App and get the following information,
1. Client ID
2. Client secret
3. AppID URI
4. Tenant ID

Ensure that you setup the following two redirect URIs
```
  http://localhost:3000/callback
  http://localhost:3000
```

## Run the application

Run `python server.py`, you are now listening on http://localhost:3000

## Get an Access token.

You will need an access token to call this function. In order to get the access token, open browser in private mode and visit
```
https://login.microsoftonline.com/<tenant_name>.onmicrosoft.com/oauth2/v2.0/authorize?response_type=code&client_id=<client_id_api>&redirect_uri=https://<yournodejsfunction>.azurewebsites.net/callback&scope=openid
```

This will prompt you to perform authentication, and it will return a code. 
Use that code in the following request to get an access token, remember to put in the code and client secret.

```
curl -X POST \
https://login.microsoftonline.com/<tenant_name>.onmicrosoft.com/oauth2/v2.0/token \
-H 'Accept: */*' \
-H 'Cache-Control: no-cache' \
-H 'Connection: keep-alive' \
-H 'Content-Type: application/x-www-form-urlencoded' \
-H 'Host: login.microsoftonline.com' \
-H 'accept-encoding: gzip, deflate' \
-H 'cache-control: no-cache' \
-d 'redirect_uri=https%3A%2F%2F<yournodejsfunction>.azurewebsites.net%2Fcallback&client_id=<client_id_api>&grant_type=authorization_code&code=<put code here>&client_secret=<put client secret here>&scope=<app_id_uri>%2F.default'
```

## Call the API

Call the API as below,

```
curl -X GET \
  http://localhost:3000/api/private \
  -H 'Authorization: Bearer <access_token>' \
  -H 'cache-control: no-cache'
```

Ensure you are able to call this API succesfully, and it shows the details of the currently logged in user.

Now edit the access token to something random (i.e. invalid access token).
Ensure that your call now fails, and you get a 401.