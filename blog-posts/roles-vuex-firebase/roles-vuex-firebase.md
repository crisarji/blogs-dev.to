---
published: true
title: "Adding roles to the authentication with Vue(x)+Firebase"
cover_image: "https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/roles-vuex-firebase/assets/vue-roles-firebase.png"
description: "Roles addition to the previous authentication using Firebase user claims, Firebase functions and Vue routing"
tags: javascript, vue, vuex, firebase
series:
canonical_url:
---

## Greetings and Recap

Hello again developer pal!, if you have come across this repo on purpose great! thanks for reading, otherwise, maybe you want to take a look at its predecesor [login-vuex-firebase](https://dev.to/crisarji/authentication-with-vue-x-firebase-31dc).

Anyway, let me tell you that this repo is the next step to the _authentication_ using _Vuex_ and _Firebase_, yes, this is for the _authorization_; it is a simple one, using an assigned role for some users by email. I can bet that there are several ways to do it, I wont go too deep because:

1.  These are my first posts so I am taking it easy.
2.  Want to give you a sample, you are allowed to fork and reimplement as much as you want/require.
3.  Any feedback is more than welcome as a PR or thread in this post.

If you checked my aforementioned code and post, you remember we ended up having a functional authentication like this:

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/roles-vuex-firebase/assets/login-vue-firebase.gif">
</p>
<br/>

So far so good!, but what would happen if you want to limitate the access to the users?, depending whether dealing with an `admin` or a `player`(yeap these are the couple roles we could have for this case), we want to have a way to allow certain views to the `admin` and some others to the `player`, something like this:

<br/>
<h3>Admin</h3>
<p>Allow the access as an administrator to a <strong>dashboard</strong> page, but forbid to access other users page</p>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/roles-vuex-firebase/assets/login-admin.gif" alt="Login Admin">
</p>
<br/>

<br/>
<h3>Player</h3>
<p>Allow the access as a player to a <strong>landing</strong> page, but forbid to access admin pages</p>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/roles-vuex-firebase/assets/login-player.gif" alt="Login Player">
</p>
<br/>

Too much text and gifs, let see the code!

## Show Me The Code

_Disclaimer_: For the last post, I mentioned that there are plenty of posts related to `Firebase` and how to set it up, and that you should have a basic knowledge of the platform, at least have 1 project and the API Keys available. In this ocassion I'll be a bit more picky, it is imperative to have some knowledge of `Firebase functions`, in case you are not familiar you can read about it [here](https://firebase.google.com/docs/functions).
Also, for running functions there are 2 main requirements: 1. node version when deploying must be 10 or above, 2. some interactions may require an upgrade from `Spark` to `Blaze` plan.

Let me share to you the Github code [here](https://github.com/crisarji/roles-vuex-firebase), you can find the requirements for running the app locally, also a `functions` folder which is required for the roles implementation; since it is still in an early stage, no live demo yet.

## Want some explanation? sure thing! keep reading below

As you already know, we are diving in a bay called `Firebase`, we'll interact a bit more with one of its islands the `Firebase console`, so please have an active project, that will make it easier for you to follow the explanations, I'll split them into steps for trying to make it easier to read.

## Step 1

### Roles collection on Firebase

Since the goal is give you an idea of what you can do with the platform `roles` collection only requires 2 properties: one for the _email_ and one for the _isAdmin_, remember that you can make it suit your requirements whatever other way you want or need.

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/roles-vuex-firebase/assets/roles-entry.png" alt="Login Player">
</p>
<br/>

Now on, whenever a user with this email is created, `Firebase` on its own will turn it into an `admin` user, any other user will be treated as a `player` role, keep reading to see the how!

## Step 2

### Firebase and Custom Claims

First thing to know is the way the platform exposes the authorization interaction, this is through the use of _Custom Claims_ and _Security Rules_; we are aboarding the first in here. According to the official documentation:

```
The Firebase Admin SDK supports defining custom attributes on user accounts. This provides the ability to implement various access control strategies, including role-based access control, in Firebase apps. These custom attributes can give users different levels of access (roles), which are enforced in an application's security rules.
```

What does that mean?, well in summay it means that after creating a new user, we can append some new attributes to the `claims` object present in the background, and we can take advantage of that behavior for handling _roles_, not too hard to follow right?

You can read much more about _Claims_ [here](https://firebase.google.com/docs/auth/admin/custom-claims) in case you are not convinced with my shallow explanation.

## Step 3

### Setting Custom Claims

For setting a custom claim is necessary to make a couple changes in the previous code we used for the login.

First of all, a small tweak needs to be done on _signup_ action on `store/modules/authentication.js`; just flip the _enable_ to `false`:

```js
  ...
  async signup({ commit }, payload) {
    commit('setLoading', true);
    await fb.auth.createUserWithEmailAndPassword(payload.email, payload.password)
      .then(firebaseData => {
        fb.usersCollection.doc(firebaseData.user.uid).set({
          nickname: payload.nickname,
          name: payload.name,
          email: payload.email,
          enable: false // <= this from true to false
        })
          .then(_ => {
  ...
  ...
  ...
```

This will force every single created user to be flipped to _enable = true_ manually or programatically.

You could ask yourself _Why would I disable every new user?_, well imagine that you have a selected group of users for your application, you dont want to control the signup but the signin, so you can filter who interacts with your before hand.

Important: take into account that what we just did was to disconnect the user created in our custom `users` collection, remember that this is an extension for the `authorization user`, this last is the one which possesses the `claim` that we need to modify for the role.

So, how can we add the `claim` for a brand new created user?, well with a predefined [trigger background function](https://firebase.google.com/docs/functions/firestore-events) of course!

Long story short => `Firebase` has some triggers to be used out of the box in cases of create, update, delete, etc a user; the trigger we do care in particular is `onCreate`.

After knowing this, in the _root_ folder of the project, there is new folder called `functions`, it is a simple structure with an _index.js_, a _package.json_, and a few more required-but-simple files, take a look at the first:

_index.js_

```js
const functions = require('firebase-functions');
const admin = require('firebase-admin');
admin.initializeApp();
exports.processSignUp = functions.auth.user().onCreate(async user => {
  if (user.email) {
    const adminUsers = admin.firestore().collection('adminUsers');
    const snapshot = await adminUsers.where('email', '==', user.email).get();
    const customClaims = snapshot.empty ? { player: true } : { admin: true };
    return admin
      .auth()
      .setCustomUserClaims(user.uid, customClaims)
      .then(_ => {
        if (!snapshot.empty) {
          const userUpdate = admin.firestore().collection('users');
          userUpdate.doc(user.uid).set({
            nickname: user.email,
            name: user.email,
            email: user.email,
            enable: true,
          });
          functions.logger.info(`User with email ${user.email} was added as admin and enabled!`);
        }
        const metadataRef = admin.database().ref('metadata/' + user.uid);
        return metadataRef.set({ refreshTime: new Date().getTime() });
      })
      .catch(error => {
        functions.logger.error(`There was an error whilst adding ${user.email} as admin`, error);
        return;
      });
  }
  functions.logger.console.warn(`There was no email supplied for user, no role added.`);
  return;
});
```

Saw that?, in only 32 lines of code(it could be even less) resides all the logic for checking the role, add it if required, modify the extended user and report the execution status of the function, let's split it bit by bit.

This code imports the required modules, initialize the app and registers the trigger for the `OnCreate`; therefore whenever a new user is added, via `signUp` or `manually` it will pass through this function.

```js
const functions = require('firebase-functions');
const admin = require('firebase-admin');

admin.initializeApp();

exports.processSignUp = functions.auth.user().onCreate(async user => {
  ...
  ...
  ...
```

Next, if no email is registered for any reason, the logger exposed by _firebase-functions_ writes in the web logs

```js
if (user.email) {
  ...
  ...
  }
  functions.logger.console.warn(`There was no email supplied for user, no role added.`);
  return;
});
```

In case a valid email is in place(this should be almost always), the function will look for the `roles` collection, will execute a query _where_ looking up for the email, in case of match, the `snapshot` will not be empty, thus the `customClaim` is set as _admin_, otherwise it will be dealing with a _player_

```js
exports.processSignUp = functions.auth.user().onCreate(async user => {
  if (user.email) {
    const adminUsers = admin.firestore().collection('adminUsers');
    const snapshot = await adminUsers.where('email', '==', user.email).get();
    const customClaims = snapshot.empty ? { player: true } : { admin: true };
    return admin
```

The final step is `setCustomUserClaims` using the _uid_ identifying the user and the _customClaim_ which determines whether dealing with an _admin_ or a _player_; also notice that in case the function is dealing with an _admin_ it will add a new record in the extended _users_ collection(pretty much what we do in the _signup_ action in our _authentication_ module).

```js
const customClaims = snapshot.empty ? { player: true } : { admin: true };
return admin
  .auth()
  .setCustomUserClaims(user.uid, customClaims)
  .then(_ => {
    if (!snapshot.empty) {
      const userUpdate = admin.firestore().collection('users');
      userUpdate.doc(user.uid).set({
        nickname: user.email,
        name: user.email,
        email: user.email,
        enable: true,
      });
      functions.logger.info(`User with email ${user.email} was added as admin and enabled!`);
    }
    const metadataRef = admin.database().ref('metadata/' + user.uid);
    return metadataRef.set({ refreshTime: new Date().getTime() });
  })
  .catch(error => {
    functions.logger.error(`There was an error whilst adding ${user.email} as admin`, error);
    return;
  });
```

Look the code above, among the props notice the _enable = true_, this has a double purpose:

1. Enable the admin user immediately
2. Allows the creation of admin users directly from `Firebase console` instead of going through the whole signup process

So, something like this is possible, easier and more viable than running the whole signup:

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/roles-vuex-firebase/assets/adding-admin-user.png" alt="Login Player">
</p>
<br/>

In case you were wondering, yes, this user above is the same added in the [Step 1](#step-1).

## Step 4

### Deploying the processSignUp function

Hope you have followed the previous steps, may look a bit complicated, but after a couple more reads will be cristal clear!, so for the next step we need to deploy the `processSignUp` function, let's take a look at `Firebase's` console first:

In console, in `Functions` section, if no functions created a 2-steps wizard will appear

Step 1
<br/>

<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/roles-vuex-firebase/assets/config-functions-step1.png" alt="Config Functions Step1">
</p>
<br/>

Step 2
<br/>

<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/roles-vuex-firebase/assets/config-functions-step2.png" alt="Config Functions Step2">
</p>
<br/>

Final Panel
<br/>

<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/roles-vuex-firebase/assets/functions-panel.png" alt="Functions Panel">
</p>
<br/>

Now, how to deploy the function in Firebase?, it is an easy process(the followinf steps must be executed inside `functions` folder):

Connect your `functions` with your `Firebase` project executing:

```sh
  firebase use --add
```

Pick the project and an alias(this works better when multiple projects exist under the same instance)

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/roles-vuex-firebase/assets/firebase-use-add.png" alt="Firebase Use Add">
</p>
<br/>

Next, run the script:

```sh
  npm run deploy
```

After that, the deploy should be completed and successful

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/roles-vuex-firebase/assets/function-deploy-successful.png" alt="Firebase Use Add">
</p>
<br/>

Now if you navigate to the `Firebase functions console` again, there must be a new entry for the just created function

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/roles-vuex-firebase/assets/panel-deployed-function.png" alt="Firebase Use Add">
</p>
<br/>

And that's it! every time a matching-role user is added, an information message will be displayed in the records of the function

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/roles-vuex-firebase/assets/function-execution-successful.png" alt="Firebase Use Add">
</p>
<br/>

## Step 5

### New Routes to be validated

The routes are pretty much the same, just add the new views, add a _meta_ attribute with the custom prop `requiresAuth`, and register them.

```js
  ...
  const routerOptions = [
    { path: '/', component: 'Landing', meta: { requiresAuth: true } },
    { path: '/auth', component: 'Auth' },
    { path: '/landing', component: 'Landing', meta: { requiresAuth: true } },
    { path: '/dashboard', component: 'Dashboard', meta: { requiresAuth: true } },
    { path: '*', component: 'Auth' },
  ];

  const routes = routerOptions.map(route => {
    return {
      ...route,
      component: () => import(/* webpackChunkName: "{{route.component}}" */ `../views/${route.component}.vue`)
    };
  });

  Vue.use(Router);
  ...
```

Remember the method _beforeEach_? now is more important than before, the `claims` added in the `processSignUp` are checked before navigating to every single view; when an `admin` tries to navigate a `player` page, is inmediately redirected to its scope of enabled view(s) and vice versa; this way the app is ready to `authenticate` and `authorize` users(in a simple way)

```ts
  ...
  const router = new Router({
    mode: 'history',
    routes
  });
  router.beforeEach((to, from, next) => {
  auth.onAuthStateChanged(userAuth => {
    if (userAuth) {
      auth.currentUser.getIdTokenResult()
        .then(({claims}) => {
          if (claims.admin) {
            if (to.path !== '/dashboard')
              return next({
                path: '/dashboard',
              });
          }
          if (claims.player) {
            if (to.path !== '/landing')
              return next({
                path: '/landing',
              });
          }
        })
      }
      const requiresAuth = to.matched.some(record => record.meta.requiresAuth);
      const isAuthenticated = auth.currentUser;
      if (requiresAuth && !isAuthenticated) {
        next('/auth');
      } else {
        next();
      }
    })
    next();
  });
  ...
```

## Conclusion

Protect the app views is possible using `Firebase` and `Vue`, it is a bit trickier than the simple login but not impossible; maybe you could have a better way to do it, let's discuss in a thread below!

Thanks for reading!
