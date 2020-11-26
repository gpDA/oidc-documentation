## oidc-client
##### Definition

[oidc-client Github](https://github.com/IdentityModel/oidc-client-js)

A library to provide OpenID Connect (OIDC) and OAuth2 for **client-side**. In other words, Client, Vue in our case, is using a library to communicate with .NET's Identity Server

You may be like "What is Identity Server??"

## Identity Server
##### Definition

[Identity Server](https://identityserver.io/)

It's a protocol. It helps building a protocol using .NET to build identity and access control solutions for modern applications, including single sign-on, identity management, authorization, and API security 

## vuex-oidc
##### Definition

Simply vue(x) store version of vue-oidc

We are primarily use `vuex-oidc`

[vuex-odic Github](https://github.com/perarnborg/vuex-oidc/wiki)

## .env file

.env -> local

.env.qa --> qa

.env.stg --> stg

.env.prod -> prod

```javascript
PORT=82
NODE_ENV=qa
VUE_APP_OIDC_REDIRECT_URI=
VUE_APP_OIDC_AUTHORITY=
VUE_APP_IDP_API_HOST=
VUE_APP_OIDC_CLIENT_ID=
VUE_APP_CARFS_API_HOST=
VUE_APP_GLSHARE_API_HOST=
```

## oidcSettings.ts

`oidcSettings.ts` file usually locates in `/components/auth`

```javascript
const oidcSettings = {
  /* REQUIRED */
  authority: process.env.VUE_APP_OIDC_AUTHORITY,
  client_id: process.env.VUE_APP_OIDC_CLIENT_ID,
  redirect_uri: `${process.env.VUE_APP_OIDC_REDIRECT_URI}/auth`,
  response_type: 'id_token token',
  scope: 'openid profile email username directory CarFSApi GLShareApi',
  /* OPTIONAL */
  silent_redirect_uri: `${process.env.VUE_APP_OIDC_REDIRECT_URI}/silent-auth`,
  post_logout_redirect_uri: `${process.env.VUE_APP_OIDC_REDIRECT_URI}/logout`,
  // loadUserInfo: true, // <by default, true>
  // automaticSilentRenew: true, // <by default, false>
  // AllowOfflineAccess: true,
  // filterProtocolClaims: true,
  // silentRequestTimeout: 10000, // <by default, 10000>
  // revokeAccessTokenOnSignout: true,
  acrValues: '', // you should need this for self-registration
};
```

##### arcValues Example

Available in CarfSSend (GLShare)

```javascript
if(ot.query.otac) {
    oidcSettings.arcValues = `otac:${to.query.otac}`;
}
```

## communicate with TPAuth team

email `tpauthdev`

## default values for tokens from the TPAuth-end

refresh Token : 30 days / 2592000 seconds

Identity Token : 5 minutes / 300 seconds

Access Token : 60 minutes / 3600 seconds

## email template asking the change of token lifespan to TPAuthDev

_ Specify envrionment

_ Speicfy Client ID

_ Specify the values for each tokens

_ Example

```javascript
GLShare QA, STG, PROD (Client ID: "Client_ID")
"authorizationCodeLifetime": 30,
"identityTokenLifetime": 300,
"accessTokenLifetime": 3600,
"refreshTokenLifetime": 2592000,
```

## oidc-client Issues Github tab

If you are struggling for more than few day, you are not alone

Very useful to grap the basic ideas of how oidc-client works

[from oidc-client issues](https://github.com/IdentityModel/oidc-client-js/issues)

## If you are keep struggling

Make sure if you understand 3 tokens "id_token, access_token, refresh_token"

https://auth0.com/blog/securing-single-page-applications-with-refresh-token-rotation/

## I found out that

refresh_token & silent_refresh_token is different

silent_refresh_token occurs when

access_token expires but refresh_token has not yet expired

```
access_token_lifeTime < `silent_refresh_token` > refresh_token_lifeTime
```

Refresh tokens allow the client to obtain more access tokens without needing the user to re-authenticate

In other words, the refresh token is a longer lived token that may have a lifetime up to many years

