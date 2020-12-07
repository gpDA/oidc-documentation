## How GLShare fixed the issues
##### Issues
There are 2 errors while GLShare tries to achieve `silent-auth` successfully, 
1) An error occured at authenticateOidcSilent: Frame window timed out

2) Silent Renew and the "login_required" Error When Using oidc-client

`Again, please slack @gp if you are struggling with the similar issues`

#### router.ts
```javascript
// router.ts

store.registerModule('oidcModule', vuexOidcCreateStoreModule(oidcSettings, {
  dispatchEventsOnWindow: true,
},
// Optional OIDC event listeners
{
  userLoaded: (user: any) => console.log('OIDC user is loaded:', user),
  userUnloaded: () => console.log('OIDC user is unloaded'),
  accessTokenExpiring: () => {
    console.log('Access token will expire');
    const userManager = vuexOidcCreateUserManager(oidcSettings);
    userManager.signinSilentCallback()
      .catch((reason: any) => {
        console.log(`Handle renew callback failed: ${reason}`);
      });
  },
  accessTokenExpired: () => {
    console.log('Access token did expire')
    const userManager = vuexOidcCreateUserManager(oidcSettings);
    userManager.signoutRedirect();
  },
  userSignedOut: () => console.log('OIDC user is signed out'),
  oidcError: (payload: any) => console.log(`An error occured at ${payload.context}:`, payload.error),
  automaticSilentRenewError: (payload: any) => console.log('Automatic silent renew failed.', payload.error),
}));

router.beforeEach(vuexOidcCreateRouterMiddleware(store, 'oidcStore'))
```

#### App.vue
```javascript
// App.vue
  computed: {
    ...mapGetters(['oidcAccessToken']),
    ...mapGetters(['oidcAccessTokenExp']),
    ...mapGetters(['oidcUser']),
  },
  watch: {
    oidcAccessTokenExp(newVal, oldVal) {
      if (newVal !== oldVal) {
        if (this.oidcAccessToken) {
          setTokenApi(this.oidcAccessToken);
        }
      }
    },
  },
```

#### oidcSettings.ts
```javascript
// ...
  /* OPTIONAL */
  silent_redirect_uri: `${process.env.VUE_APP_OIDC_REDIRECT_URI}/silent-auth`,
  post_logout_redirect_uri: `${process.env.VUE_APP_OIDC_REDIRECT_URI}/logout`,
  automaticSilentRenew: true,
  automaticSilentSignin: true,
  revokeAccessTokenOnSignout: true,
  acrValues: '',
```

#### OidcCallback.vue
```javascript
  methods: {
    ...mapActions(['oidcSignInCallback']),
  },
  mounted() {
    if (this.silentMode) {
      vuexOidcProcessSilentSignInCallback();
      return;
    }
    this.oidcSignInCallback()
      .then((redirectPath: any) => {
        this.$router.push(redirectPath);
      })
      .catch(() => {
        // go back home, if the token is invalid the user will be redirected to sso
        this.$router.push({ name: 'home' });
      });
  },
```

## Vuex-oidc package
##### Update the package

Install the most updated package is the simplest but most powerful way to fix any issues when using third-party packages

Try to update your package

`3.10.1` is the most updated `vuex-oidc` package as of December 2nd 2020

## Debugging TIP - 1
##### check the data from provider

If you are curious about what is the exact data that the application will get back from the provider, you can enter the following URL in the browser
```
https://${OIDC_PROVIDER}/.well-known/openid-configuration

// In our case,
It will be like

https://${VUE_APP_OIDC_AUTHORITY}/.well-known/openid-configuration
```