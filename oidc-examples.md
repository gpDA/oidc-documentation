## Logging Events
##### Related Link
[vuex-odic Github](https://github.com/perarnborg/vuex-oidc/wiki)

It works like logger. Very useful to see when something happens
```javascript
store.registerModule('oidcModule', vuexOidcCreateStoreModule(oidcSettings, {
  dispatchEventsOnWindow: true,
},
// Optional OIDC event listeners
{
  userLoaded: (user: any) => console.log('OIDC user is loaded:', user),
  userUnloaded: () => console.log('OIDC user is unloaded'),
  accessTokenExpiring: () => console.log('Access token will expire'),
  accessTokenExpired: () => console.log('Access token did expire'),
  silentRenewError: () => console.log('OIDC user is unloaded'),
  userSignedOut: () => console.log('OIDC user is signed out'),
  oidcError: (payload: any) => console.log(`An error occured at ${payload.context}:`, payload.error),
  automaticSilentRenewError: (payload: any) => console.log('Automatic silent renew failed.', payload.error),
}));
```

## User Manager
##### Related Link

[Using Auth0 with Vue and oidc-client.js Server](https://www.jerriepelser.com/blog/using-auth0-with-vue-oidc-client-js/)

##### Take-aways from the link
He goes over how to use `userManager` class provided by oidc-client

`userManager` is very useful class

```javascript
import { UserManager, WebStorageStateStore, User } from "oidc-client";
```

[Available userManager Methods from odic-client wiki](https://github.com/IdentityModel/oidc-client-js/wiki)

getUser(): returns promise to load the User object for the currently authenticated user

signinRedirect(): returns promise to trigger a redirect of the current to the **authorization endpoint**

signoutRedirect(): returns promise to trigger a reidrect of the current window to the **end session endpoint**

```javascript
public getUser(): Promise<User | null> {
    return this.userManager.getUser();
}

public login(): Promise<void> {
    return this.userManager.signinRedirect();
}

public logout(): Promise<void> {
    return this.userManager.signoutRedirect();
}
```

##### userManager in our code

You could also access to **userManager** class from `vuex-oidc`

This code is available for
[CarFSSend (GLShare) develop branch](https://bitbucket.org/sidecloud/carfssend/src)

```javascript
import { vuexOidcCreateUserManager } from 'vuex-oidc';

const userManager = vuexOidcCreateUserManager(oidcSettings);
userManager.getUser().then((response: any) => {
    console.log('response', response); // you have all the user related data
})
```

## mapGetters from `vuex-oidc`
##### Related Link
[vuex-odic Github](https://github.com/perarnborg/vuex-oidc/wiki)

##### MapGetters

MapGetters return values

** : Important

** oidcIsAuthenticated: check if oidcAuthentication is working. It is good to use when you want to make sure oidc works in the specific places

** oidcUser

** oidcAccessToken

```javascript
import { mapGetters } from 'vuex';
computed: {
    ...mapGetters(['oidcIsAuthenticated']);
}
```

##### access to MapGetters in methods

You could use returned value of `MapGetters` in `methods`

```javascript
computed: {
    ...mapGetters(['oidcIsAuthenticated']);
}

methods: {
    ifTokenIsExpiring() {
        const timeNow = new Date().getTime();
        const expirationTime: any = this.oidcAccessTokenExp;
        const timeRemaining = expirationTime - timeNow;

        return timeRemaining <= (1000 * 60 * 2);
    }   
}
```

##### watch MapGetters

Also, you could be reactive to the change of the value of `MapGetters`

```javascript
computed: {
    ...mapGetters(['oidcIsAuthenticated']);
}

watch: {
  oidcAccessTokenExp(newVal, oldVal) {
    if (newVal !== oldVal) {
      console.log('++ setting new token ++');
      setTokenApi(this.oidcAccessToken);
    }
  },
},
```

## mapActions from `vuex-oidc`
##### Related Link
[vuex-odic Github](https://github.com/perarnborg/vuex-oidc/wiki)

##### mapActions

** : Important

** oidcSignInCallback: Handles callback from authentication redirect. Has an optional url parameter

```javascript
methods: {
    ...mapActions(['oidcSignInCallback']),
}

mounted() {
    this.oidcSignInCallback().then((redirectPath) => {
        this.$router.push(redirectPath);
    })
}
```