---
published: false
title: "Authentication with Vue(x)+Firebase"
cover_image: "https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/login-vuex-firebase/assets/vue-firebase.png"
description: "Authentication(login, signup, password recovery) using Vue, Vuex and Firebase"
tags: javascript, vue, vuex, firebase
series:
canonical_url:
---

## Background for this Post and eventual Code

_Note_: no time for my background story?, no problem!, jump straight to the code in [show me the code](#show-me-the-code) section, promise no hard feelings, pal!

Recently, I was added to a WA group, which is related to sports; they share opinions, latest news, fixtures, and what caught my attention the most: a forecast of the scores for a team.

For sharing the forecast of the scores, every single person copies + pastes a template message as a new message!!!; we are talking about 50 persons in the chat, could you imagine the number of messages a couple of days before a match day?, well I don't image it, I see it once a week!

After the 3 first messages, when I understood the pattern, I really committed to myself for making their lives a bit easier(and burn the spam to the ground); so, I thought about creating a small site, where every person has a profile and is able to set her/his forecast in a centralized way, instead of the "Copy+Paste" methodology that lives by now.

If you have borne with me so far, thank you! I think you deserve to see the following code in case you struggle with something similar(I hope you don't, your head will hurt) or you just need to create authentication for any other project in a few lines, I'll try to post the next steps as well for showing you all the solution to the aforementioned problem.

## What's the Goal?

The goal is to have an authentication login, signup and password recovery option available for our users, something like this:

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/login-vuex-firebase/assets/login-vue-firebase.gif" alt="Login Slideshow">
</p>
<br/>

## Show Me The Code

_Disclaimer_: There are plenty of posts related to `Firebase` and how to set it up, for the use of this code, you should have a basic knowledge of the platform, at least have 1 project and the API Keys available.

Let me share to you the Github code [here](https://github.com/crisarji/login-vuex-firebase), you can find the requirements for running the app locally; since it is still in an early stage, no live demo yet.

_Note_: now you have the code, and want also an explanation?, let's go!, teleport yourself to [What The Code](#what-the-code) section, you gonna have a good time!

## What The Code

What the code you just saw?, well, this is a repo specially created for you, who is tempted to create a login using [VueJs](https://vuejs.org/) and [Firebase](https://firebase.google.com/), let me tell you that's a wise choice!.

The main idea in here, is giving you a sort of scaffolding, and spare you some time struggling with the authentication process for new/former users who can access your application.

### Know your root files

_firebase.js_

This is the entry point for `Firebase`, the dependency consumed in you `package.json` file is consumed in here; it would be a good practice keeping the references only into this file, then, if a new module must be included you can be sure that there will be only one place for doing so.

The key pieces for this file are exactly those, the `keys`, after adding them the `Firebase` instance will be alive:

```ts
// firebase init
const firebaseConfig = {
  apiKey: 'YOUR_API_KEY',
  authDomain: 'YOUR_AUTH_DOMAIN',
  databaseURL: 'YOUR_DATABASE_URL',
  projectId: 'YOUR_PROJECT_ID',
  storageBucket: 'YOUR_STORAGE_BUCKET',
  messagingSenderId: 'YOUR_MESSAGING_SENDER_ID',
  appId: 'YOUR_APP_ID',
  measurementId: 'YOUR_MEASUREMENT_ID',
};

firebase.initializeApp(firebaseConfig);
```

The values for filling these props up are available in your `Firebase's` project console, all of them serve to a different purpose, you can adde them all at once or one by one as required by the project.

You can notice at the end of the file, that we are exporting 3 `const`, this is done for making available in the next modules the use of the these `Firebase` instances.

```ts
const db = firebase.firestore();
const auth = firebase.auth();

const usersCollection = db.collection('users');

export { db, auth, usersCollection };
```

_main.js_

Since we already defined the `Firebase` module, we import it into the `main.js`, when the app bootstraps aside the Vue required imports and styles, it will be also available the `firebase` instance; one thing to notice is the fact the the `Vue` app registration is a bit different thant the one you could be used to, this is because we are subscribing to the `onAuthStateChanged` observer, this keeps an open bridge in case of new changes to be available in the app.

```ts
const unsubscribe = auth.onAuthStateChanged(firebaseUser => {
  new Vue({
    el: '#app',
    router,
    store,
    render: h => h(App),
    created() {
      if (firebaseUser) {
        store.dispatch('fetchUserProfile', firebaseUser);
      }
    },
  });
  unsubscribe();
});
```

One more thing is the `created` hook, where an action is dispatched(since we are using Vuex) for fetching the user information and avoid the manual login reauthentication over an over when the user reaches the app. In case you are wondering how this is done, long story short, `Firebase` by default sets a _local_ persistency in IndexDB, according to the documentation:

```
Indicates that the state will be persisted even when the browser window is closed or the activity is destroyed in React Native. An explicit sign out is needed to clear that state. Note that Firebase Auth web sessions are single host origin and will be persisted for a single domain only.
```

In case you want to read about it you can do it [here](https://firebase.google.com/docs/auth/web/auth-state-persistence)

### Know your views

_Auth.vue_

This is the main view in our app, in here we have the different components which integrated as a whole give us the _Authentication_ option.

There are only 3 components imported, they exclude each other, that means that the `toggle` functions you find in here only add/remove from the _DOM_ 2 of them at the time accordigly:

```ts
import LoginForm from '@/components/LoginForm';
import SignUpForm from '@/components/SignUpForm';
import PasswordReset from '@/components/PasswordReset';
```

We could go throughout the conditionals, css, and data props; but the most important pieces for this view are the methods marked with `async/await`:

```ts
  ...
  async login(data) {
    await this.$store.dispatch('login', {
      email: data.email,
      password: data.password,
    })
  },
  ...
```

Why are we awaiting for a _dispatch_?, well, this is because if we need to communicate something from the _state_ as part of a chain of executions, we do need to wait for whatever that happened in the lower layers; for example, when trying to signup, an expected flow could be it:

1. Submit the request for signing up
2. Get the response from the BE
3. I got a response, now what?

&emsp;&emsp; 3.1) User is registered => move it to the login for using the brand new credentials and get into the app

&emsp;&emsp; 3.2) User set a badly formatted email => keep it right there and let it know about the problem, allow it to fix it and retry

See a bit of the point?, there is an alternative flow to be follow when we are not dealing with the happy path, `then`(punch intented here) we need to _await_ before moving to the next step.

### Know your Store

_index.js_

This file is the core of the store, the main properties of the state should reside here. One main advantage is the fact of importing other state modules(such as `authentication.js`) as required to be exposed to the app, and at the same time, the main `state`, `getters`, `mutations`, all of them are available for the lower modules.

<br/>
<p align="left">
  <img width="40%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/login-vuex-firebase/assets/store-index.png" alt="Store index.js">
</p>
<br/>

The approach for this example, is make the state props available in the individual states, so during the interaction between `Vue` and `Firebase`, the state will be notified and changed in case of an error or an information message.

_authentication.js_

This is the module in charge of everything related to the authentication process; login, logout, signup, password recovery, even look for a user information when a successful login is produced.

<br/>
<p align="left">
  <img width="40%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/login-vuex-firebase/assets/store-authentication.png" alt="Store authentication.js">
</p>
<br/>

As you can see, the state is quite simple, only a `userProfile` prop that is set/get according to the result of the operation; let's take a look at the _login_ action:

```ts
  ...
  async login({ commit, dispatch }, payload) {
    commit('setLoading', true);
    await fb.auth.signInWithEmailAndPassword(payload.email, payload.password)
      .then(firebaseData => {
        dispatch('fetchUserProfile', firebaseData.user);
        commit('setLoading', false);
        commit('setError', null);
      })
      .catch(error => {
        commit('setLoading', false);
        commit('setError', { login: error });
      });
  },
  ...
```

What happens in here is that `Firebase` exposes an async _signInWithEmailAndPassword_ method, it needs only the _email_ and _password_, when that operation is resolved, we also need to fetch the user's data(if a valid login) and move the user to a different place, for instance a _Landing_ page.

What about the _logout_?, even simpler!, the exposed async _signOut_ method do it on our behalf, in this case, after the sign out, there is a _commit_ for invalidating the user's profile, and the user then is redirected to the `/auth` view.

```ts
  ...
  async logout({ commit }) {
    await fb.auth.signOut()
    commit('setUserProfile', {})
    router.currentRoute.path !== '/auth' && router.push('/auth');
  },
  ...
```

Let me show you the trickiest, I mean the _signup_ and its exposed method: _createUserWithEmailAndPassword_, ready for a tip with your users?.

```ts
  ...
  async signup({ commit }, payload) {
    commit('setLoading', true);
    await fb.auth.createUserWithEmailAndPassword(payload.email, payload.password)
      .then(firebaseData => {
        fb.usersCollection.doc(firebaseData.user.uid).set({
          nickname: payload.nickname,
          name: payload.name,
          email: payload.email,
          enable: true
        })
          .then(_ => {
            commit('setLoading', false);
            commit('setInformation', { signUp: { code: 'Success', message: `User created!, use your new credentials` } });
            commit('setError', null);
          })
      })
      .catch(error => {
        commit('setLoading', false);
        commit('setInformation', null);
        commit('setError', { signUp: error });
      });
  },
  ...
```

Sure that you noticed a double `then`, why is that?, sometimes you could need, aside the creation of the user and the default profile associated to it, some custom props related to the new user; since this is the case, what is done in here is wait for the outcome of the creation process, if succeeded, the `usersCollection` adds a new record, using the unique userid associated to the brand new profile, for this example porpuses, only `nickname, name, email, enable` are saved in a new document collection, but you can add as many as you require.

Wondering about the _resetPassword_?, another exposed method: _sendPasswordResetEmail_, ready for it! just pass the email.

```ts
  ...
  async resetPassword({ commit }, payload) {
    commit('setLoading', true);
    await fb.auth
      .sendPasswordResetEmail(payload.email)
      .then((_) => {
        commit('setLoading', false);
        commit('setInformation', { resetPassword: { code: 'Success', message: 'Success!, check your email for the password reset link' } });
        commit('setError', null);
      })
      .catch((error) => {
        commit('setLoading', false);
        commit('setInformation', null);
        commit('setError', { resetPassword: error });
      })
  }
  ...
```

You can notice the _setInformation_ committed, remember the previous section of the post?, when it was mentioned the benefits of sharing the state between modules?, well, this is part of it!, it is possible to have a property in the root state for keeping a notification(`store/index.js/information`) and have the individual modules to feed it accordingly(`store/modules/authentication.js/resetPassword`), and if required, notify to subscriber components about this(`views/Auth.vue => getters`). What a nice serie of fortunate events!

<br/>
<p align="center">
  <img width="40%" src="https://media.giphy.com/media/5tvJS6ZZslR9nBYxUA/giphy.gif" alt="Fortunate Events gif">
</p>
<br/>

### Know your Router

_index.js_

We have the views, we have the states, we have the components(`components/LoginForm-PasswordReset-SignUpForm`, I wont review them, they are dummy components with a couple input props, validations, and event emitted), but how do we make it safe?, how to avoid no logged in users to get into the app?

When defining the routes, it is possible to add a _meta_ attribute with a custom prop, in this case `requiresAuth`, every route with that meta flag can be eventually be validated for determine if a user has access or not to a page.

```ts
  ...
  const routerOptions = [
    { path: '/', component: 'Landing', meta: { requiresAuth: true } },
    { path: '/auth', component: 'Auth' },
    { path: '/landing', component: 'Landing', meta: { requiresAuth: true } },
    { path: '*', component: 'Auth' }
  ]
  const routes = routerOptions.map(route => {
    return {
      ...route,
      component: () => import( /* webpackChunkName: "{{route.component}}" */ `../views/${route.component}.vue`)
    }
  })
  Vue.use(Router)
  ...
```

So, when instanciating the `Router`, the method _beforeEach_ is the place for checking the meta data attribute aforementioned. For doing so, the single instance for `Firebase`, which exposes the _auth_ const, gives access to the _currentUser_ property, thus if the page where the user wants to go requires `authentication` and the the `currentUser` is not authenticated or does no even exist, it is redirected to the `/auth` route also known as the `Auth.vue`.

```ts
  ...
  const router = new Router({
    mode: 'history',
    routes
  });
  router.beforeEach((to, from, next) => {
    const requiresAuth = to.matched.some(record => record.meta.requiresAuth);
    const isAuthenticated = auth.currentUser;
    if (requiresAuth && !isAuthenticated) {
      next('/auth');
    } else {
      next();
    }
  });
  ...
```

## Conclusion

As you can see, putting the different pieces of technology together, a simple login can be acomplished!

I hope to have helped you even a little bit with the code or the explanation, any thoughts or suggestions?, please start a thread below!

Thanks for reading!
