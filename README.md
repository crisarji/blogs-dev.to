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

## Features

This is a nice features list that could work as base when listing up the topics related to a post

|           | Included | ◾ Skipped |
| --------- | :------: | :--------: |
| Feature 1 |    ✅    |     ❌     |
| Feature 2 |    ✅    |     ❌     |
| Feature 3 |    ✅    |     ❌     |

_Tip_:Try to publish when all the features are ✅ instead of ❌

## Blog posts

- [Authentication with Vue(x)+Firebase](https://dev.to/crisarji/authentication-with-vue-x-firebase-31dc)

- [Adding roles to the authentication with Vue(x)+Firebase](https://dev.to/crisarji/adding-roles-to-the-authentication-with-vue-x-firebase-2o62)

- [Tailwind Feature: animate-pulse](https://dev.to/crisarji/tailwind-feature-animate-pulse-3nbn)

- [Firebase Firestore Rules](https://dev.to/crisarji/firebase-firestore-rules-3mpc)

- [Github Workflow Secrets](https://dev.to/crisarji/3-steps-for-handling-github-workflow-secrets-26om)

- [Vue Slots + Reusable Components](https://dev.to/crisarji/vue-slots-and-reusable-components-5g3p)

- [My Vue Experience after 3 projects in 1.5 years](https://dev.to/crisarji/my-vue-experience-after-3-projects-in-18-months-456c)

- [How I met your... Scraper?](https://dev.to/crisarji/how-i-met-your-scraper-4n6o)

- [What & Why Senior skills](https://dev.to/crisarji/the-whats-whys-in-a-fe-senior-position-1kp7)

- [Spooky Dev Stories](https://dev.to/crisarji/spooky-dev-stories-5345)
