## README template

This repo contains different posts on dev.to
Remember always to create a `Draft` post, and check its `Post ID` from your _Dashboard_ on [dev.to](https://dev.to/dashboard) using the command:

```js
$('div[data-article-id]').getAttribute('data-article-id');
```

In case of question, refer to the base the template created [here](https://github.com/maxime1992/dev.to)

Before pushing new changes, try always to execute:

```sh
  yarn install && npm run prettier:write
```

This command is always a headache when deploying to [Travis](https://travis-ci.com), and the [Husky](https://www.npmjs.com/package/husky) `pre-commit` is not working by the time being

This is a template for upcoming posts; quotes are not mandatory for the string values

```
---
published: false
title:
description:
tags:
cover_image:
series:
canonical_url:
---
//
Add some content or at least leave a space
//
```

# crisarji's blog source

https://dev.to/crisarji

Take into account that the assets url is a bit different; GitHub will try to pull those assets from their own centralized path.
So you can find your real deployed assets in:

`https://raw.githubusercontent.com/<<your-github-user>>`

In case you cannot see the assets it is 99% sure that the path is wrong.

## Blog posts

- [Authentication with Vue(x)+Firebase](https://dev.to/crisarji/authentication-with-vue-x-firebase-31dc)

- [Adding roles to the authentication with Vue(x)+Firebase](https://dev.to/crisarji/adding-roles-to-the-authentication-with-vue-x-firebase-2o62)

- [Tailwind Feature: animate-pulse](https://dev.to/crisarji/tailwind-feature-animate-pulse-3nbn)

- [Firebase Firestore Rules](https://dev.to/crisarji/firebase-firestore-rules-3mpc)

- [Github Workflow Secrets](https://dev.to)
