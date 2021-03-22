---
published: true
title: "Firebase: Firestore-Rules"
cover_image: "https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/firebase-firestore-rules/assets/cloud-firestore.png"
description: "Brief explanation about the firestore rules"
tags: javascript, firebase, firestore, rules
series:
canonical_url:
---

## What's Firebase Firestore Rules?

Hello developer pal!, glad to see you here.

Rules, rules and rules, we always hear about rules to follow for interacting with databases, endpoints, programming languages, and well, `Firebase Firestore` is not the exception to the...`Rule`(dammit once again!).

Anyway, when you work with Firebase you see the features related to store some kind of information have their own `Rules` tab, this is the way you can declare for allowing/denying the access to certain resources based on the user who is trying the request.

A bad practice is to keep the resources open for everybody throughout the web, if so, anyone could perform `CRUD` operations on your _site/app_, modify _assets_, or even remove _collections_(and I am pretty sure you don't want that, do you?), you can read more information right [here](https://firebase.google.com/docs/firestore/security/get-started).

## Show Me The Code

_Disclaimer_: For this post, a shallow explanation will be given related to `Firestore ans Security Rules version 2`, released on May 2019

The 3 main pieces to be focus on are:

1.  Default versions for test and prod
2.  Writing rules straight in console vs versioned file
3.  Allow/Deny access according to auth states and functions

## Default versions for test and prod

Whenever you start a new Firebase project, in the section `Firestore/Rules`, creating a new db project will present 2 options, you can opt any of those in, let's see the difference:

### Mode Production

Under this mode, any access is explicitly _denied_, this forces the developer to add some logic for explicitly allowing users to access the resources.

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/firebase-firestore-rules/assets/firestore-opted-mode-prod.png">
</p>
<br/>

The default schema for `production mode` looks like this:

```js
  rules_version = '2';
  service cloud.firestore {
    match /databases/{database}/documents {
      match /{document=**} {
        allow read, write: if false;
      }
    }
  }
```

Something to remark is the fact that the rules keep the track on a historical, this means that is possible to activate a previous rule schema, compare a former version against the most recent one, and even delete unused schemas; this also helps to easily find bugs when adding new docs or collections.

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/firebase-firestore-rules/assets/firestore-rules-edition-historical.png">
</p>
<br/>

### Mode Test

Under this mode, any access is explicitly _allowed_ to any user for the next whole month(by default through a timestamp). This will allow the developer to start the work right away, though, the idea is set the schema as soon as possible for allowing only expected users to consume resources.

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/firebase-firestore-rules/assets/firestore-opted-mode-test.png">
</p>
<br/>

The default schema for `test mode` looks like this:

```js
  rules_version = '2';
  service cloud.firestore {
    match /databases/{database}/documents {
      match /{document=**} {
        allow read, write: if
            request.time < timestamp.date(2021, 4, 20);
      }
    }
  }
```

Just like in production mode, the rules keep the track on a historical, also, a few days before due date, the main email registered will start receiving notifications about the expiring access to the database collections and docs unless a new rule schema is applied.

## Writing rules straight in console vs versioned file

### Straight in Console

Writing straight to `Firebase Console` is an option, it is easy and fast.

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/firebase-firestore-rules/assets/schema-in-console.png">
</p>
<br/>

One more feature with this approach, is the integration with an sort of built-in linter, it determines some syntax issues before publishing, in fact, it throws an error, and the changes wont be published till the issue is fixed.

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/firebase-firestore-rules/assets/schema-in-console-error.png">
</p>
<br/>

### Versioned File

A cleaner way to have the rules is through a versioned file, in your _firebase.json_ file, you can add an entry for `firestore/rules`(and even `indexes`!).

```json
  {
    "hosting": {
      ...
    },
    "firestore": {
      "rules": "firestore.rules",
      "indexes": "firestore.indexes.json"
    },
    "functions": {
      ...
    },
    "emulators": {
      ...
    }
  }

```

Then you can add the `firestore.rules` file and keep the versions in git or any other version handler

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/firebase-firestore-rules/assets/firestore-versioned-file.png">
</p>
<br/>

The flow goes as shown below, if more info required, take a look at the documentation right [here](https://firebase.google.com/docs/firestore/security/get-started?authuser=0).

```sh
  // Set up Firestore in your project directory, creates a .rules file
  firebase init firestore

  // Edit the generated .rules file to your desired security rules
  // ...

  // Deploy your .rules file
  firebase deploy --only firestore:rules
```

## Allow/Deny access according to auth states and functions

Either way the writing of rules goes, the critical part is the access to docs and collections. It is possible to create js functions for avoid duplicating conditionals for every element, I wrote a post related to [Adding roles to the authentication with Vue(x)+Firebase](https://dev.to/crisarji/adding-roles-to-the-authentication-with-vue-x-firebase-2o62) in case you want to check the use of claims and token additions.

So, for example, you could add a function for determining whether a request comes from an `Admin` or a `Regular` user profile, according to the resolution(handle by _Firebase_ itself), the access to different resources is granted or not.

Take a look at the example below:

```js
  rules_version = '2';

  service cloud.firestore {
    match /databases/{database}/documents {
      // true if the user is signed in and the claim is admin
      function isAdmin() {
        return request.auth.uid != null && request.auth.token.admin == true;
      }
      // true if the user is signed in and the claim is regular
      function isRegular() {
        return request.auth.uid != null && request.auth.token.regular == true;
      }

      // Shared collections
      match /settings/{doc} {
        allow read: if isAdmin() || isRegular();
        allow write: if isAdmin();
      }

      ...
      ...
      ...
    }
  }
```

What happened in the code above?:

- The functions created always ask whether the request incoming is related to a user authenticated, otherwise, the access is invalid and the request is denied
- The function `isAdmin()`, when is invoked by an authenticated user, it looks for a particular `token`, in this case, the _admin_ token, if presented, the request is validated
- The function `isRegular()`,just like `isAdmin()` looks for a particular `token`, in this case, the _regular_ token, if presented, the request is validated
- There is a collection of `settings`, when a request for `reading` comes, the fetching is available only for authenticated `users` with a role of `admin` or `regular`
- In the same collection of `settings`, when a request for `writing` comes, the upsert is available only for authenticated `users` with a role of `admin`

This is useful since even when the APIkey of your app/site is available for third-parties, the requests wont do any operations to your data without an _authenticated-and-roled_ user.

Sometimes `read` and `write` could be to macro, you can granulate them a bit more:
_`read` rule can be broken into `get` and `list`
_`write` rule can be broken into `create`, `update`, and `delete`

More info about this topic can be found right [here](https://firebase.google.com/docs/firestore/security/rules-structure?authuser=0)

## Conclusion

As shown above, Firebase Firestore rules are quite powerful, allowing even write some functions in the declared schema for avoid repeating the code over and over again; maybe you could have a better way to do it, let's discuss in a thread below!

Thanks for reading!
