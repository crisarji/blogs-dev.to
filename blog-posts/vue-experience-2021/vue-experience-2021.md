---
published: true
title: My Vue Experience after 3 projects in 18 months 
description: Some of the best practices I learned after 3 projects in 1.5 years
tags: javascript, vue, github
cover_image: https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/vue-experience-2021/assets/3-projects-year-and-half.png
series:
canonical_url:
---

## My Vue Experience after 3 projects in 18 months

Hello developer pal!, glad to see you here.

In this post, I'll share my experience after 3 projects written/maintained in Vue 2.\* during the last 18 months.

## Show Me The Topics

The topics to be focused on are:

1. `Agile Methodology and frameworks used`
2. `Design Systems`
3. `Infrastructure`
4. `Backend`
5. `State Management`
6. `Frontend`

_Disclaimer_: This post comes from my own experience during this lapse, not saying this is the best way to go, nor the worst, any contribution is more than welcome in the threads below!

## Agile Methodology and Frameworks used

Are you familiar with [Agile Software Development](https://www.agilealliance.org/agile-essentials/)?, cause I really am right now!, I really like this simple sentence as a summary:

> Agile is the ability to create and respond to change. It is a way of dealing with, and ultimately succeeding in, an uncertain and turbulent environment.
>
> - Source: [What is Agile?](https://www.agilealliance.org/agile101/)

Nice, simple, straight to the point, isn't it?, during the last year and a half my teams passed over 3 different of its frameworks:

- [Scrum](https://www.agilealliance.org/glossary/scrum): from my perspective, the most interesting, this framework allowed our [cross functional team](https://www.visual-paradigm.com/scrum/what-is-cross-functional-team-in-agile/) to interact as a whole, splitting the work up in 2-weeks sprints, and constantly adjusting the business requirements, it is a quite nice experience!, highly recommended.

- [Kanban](https://www.agilealliance.org/glossary/kanban): my second favorite from top to bottom, a good option when the work to be done does not follow the same path, for instance working on different repos at the same time(MonoRepos, MFEs, Legacy Systems)l; when this happens perhaps _Scrum_ is not enough cause the time-frames.

- [Extreme Programming (XP)](https://www.agilealliance.org/glossary/xp): required for an MVP one of my teams had, from my experience, the most risky one since you dynamically change requirements and some heavy lifting could magically appear/disappear down the road, highly not-recommended unless it is "Extreme"-ly necessary(pun intended), and please be "Extreme"-ly cautious(pun intended X2).

In case you want to read about the `Agile Manifesto`(the corner stone of the methodology) you can do it right [here](http://agilemanifesto.org/principles.html).

## Design System

I learned that after defining the [Methodology](#agile-methodology-and-frameworks-used), it is good to have a robust `Design System`, you have 2 options: reuse an existing one or create one custom from scratch, either way the benefits are amazing!, when present it covers up 3 different areas:

1.  `UI/UX` does not repeat itself
2.  `WebDev` has one-and-only-one source of truth
3.  `QA` can evacuate questions on its own

In case you want to have some existing guide lines, [here](https://designerup.co/blog/10-best-design-systems-and-how-to-learn-and-steal-from-them/) you can find a list of available resources on the wild.

In the teams I was working on, those where custom, a recipe for success was:

- [VueJS](https://vuejs.org/)
- [StoryBook](https://storybook.js.org/)
- [Tailwind](https://tailwindcss.com/)

A great strategy that we found was to create `Vue` components using `Tailwind` and creating its respectives stories on `Storybook`; I can say this is a great approach cause you can define your `Design System` in `Storybook` itself, and publish this to an accessible page for all your team members(under a VPN is even more secure), so it is available for everybody, they can see the components running in an isolated scope before even implementing it!

## Infrastructure

Oh right, we had the [Methodology](#agile-methodology-and-frameworks-used) and the [Design System](#design-system) then what?

I learned that here it comes the `Infrastructure`, well the approaches we had the opportunity to work with where [Jenkins](https://www.jenkins.io/), [TravisCI](https://travis-ci.org/) and [GitHub Actions](https://github.com/features/actions).

For experience, in big projects, `Jenkins` is a great way to go, among all its pros, you can set it up for running on your behalf the _unit testing_ and _end-to-end_ testing before deploying!, in case of failure you are notified and a fix can be included.

In small projects, or side projects, you can use the [TravisCI](https://travis-ci.org/) + [GitHub Actions](https://github.com/features/actions) approach; `GitHub` already has some built-in options for setting up `ymls` and help you with _Merge Requests_ and _Deployments_.

_Note_: [TravisCI](https://travis-ci.org/) gives you 10,000 builds for free with your sign up, for small projects, proof of concepts or side projects it is a great deal!.

## Backend

Also I learned for these projects, that a `Backend` on [NodeJS](https://nodejs.org/en/docs/) and [Firebase](https://firebase.google.com/docs) is easily handle.

[NodeJS](https://nodejs.org/en/docs/) + [Express](https://expressjs.com/) give you the chance to handle the routing for _CRUD_ operations, it is easy to handle the _request/responses_.

[Firebase](https://firebase.google.com/docs) is ready to go as soon as you import it in your [Vue](https://vuejs.org/) project; with a few lines of code you are able to do a lot!; Authentication, Storage, Realtime DB, a whole bunch of options are available for you.

I wrote non-canonical series related to some Firebase features if you want to check them.

## State Management

I learned about [VueJS](https://vuejs.org/) + [Vuex](https://vuex.vuejs.org/). I'm used to Rxjs, NgRx but Vuex is from my experience the easiest; with a little of knowledge you are able to start creating on your own, the separation of concerns through modules, and the way to reference then is crystal clear:

```sh
store/
   ├── index.js
   └── modules/
       ├── module1.store.js
       ├── module2.store.js
       ├── module3.store.js
       ├── module4.store.js
       └── module5.store.js
```

Referencing the modules in the `index.js` make them importable through out your project, this includes `State`, `Getters`, `Mutations`, `Actions`; a new module is just the addition of a new entry in the `index.js`, a deprecated module is the removal of that entry(conditions may apply).

I also learned that you can `namespace` the modules!, then you can differentiate the elements by module instead of having dozens of lines with no context(trust me, with several modules this is amazing for debugging purposes, scalability and visual sake).

A clear example can be found below:

```js
import { mapState, mapGetters, mapActions, mapMutations } from 'vuex';

export default {
  computed: {
    // Accessing root properties
    ...mapState('my_module', ['property']),
    // Accessing getters
    ...mapGetters('my_module', ['property']),
    // Accessing non-root properties
    ...mapState('my_module', {
      property: state => state.object.nested.property,
    }),
  },

  methods: {
    // Accessing actions
    ...mapActions('my_module', ['myAction']),
    // Accessing mutations
    ...mapMutations('my_module', ['myMutation']),
  },
};
```

It doesn't matter how small or how big is your project, it will be ready to scale, you can read here a bit more about [Vuex](https://vuex.vuejs.org/) and [Namespaces](https://www.telerik.com/blogs/10-good-practices-building-maintaining-large-vuejs-projects).

## Frontend

I learned that `Vue` has a smaller learning curve than `Angular`, and it is very similar to `React`(_Note_: assuming you have a strong base of Javascript, otherwise the curve is high on either of them).

As a dev who was mostly involved in `Angular` projects, understanding the core concepts and starting to be productive was easier than expected; I really think that the other way around must be harder, cause `Angular` has its own world.

Also learned about some core concepts that made my development faster and easier:

- [Atomic Design Pattern](https://vuedose.tips/how-to-structure-a-vue-js-app-using-atomic-design-and-tailwindcss/): structure your folders as =>
  - _Atoms_: these are basic components, dummy ones; could be buttons, inputs, dropdowns, any imagiable component small enough to be functional and testable
  - _Molecules_: a group of _Atoms_, at this level just a little logic included, it should not include communication with you state(if possible)
  - _Organisms_: mixture!, can have _Atoms_ and _Molecules_, at this tier communication with you _State_ can be allowed; Mappers, Getters, here are accepted
  - _Templates_: here you add together the aforementioned structures
  - _Pages_: every page you add is an instance that can be accessed from your routing strategy

```js
```

- [Vue Lazy Routing](https://router.vuejs.org/guide/advanced/lazy-loading.html): it is trivially easy to lazy load the route components; after defining your _routerOptions_ in 5 lines of code it is set and done.

```js
import Vue from 'vue';
import Router from 'vue-router';

const routerOptions = [
  { path: '/', component: 'Home' },
  { path: '/home', component: 'Home' },
  { path: '/auth', component: 'Auth' },
  { path: '*', component: 'Auth' },
];

const routes = routerOptions.map(route => {
  return {
    ...route,
    component: () => import(/* webpackChunkName: "{{route.component}}" */ `../views/${route.component}.vue`),
  };
});

Vue.use(Router);
```

```js
```

- [Vue Test Utils](https://vue-test-utils.vuejs.org/): `Vue` has its own utils for testing purposes, and it is quite easy to understand and use, let me show the most simple sample I found:

```js
<template>
  <div class="modal" data-test="modal" aria-modal="true">
    <div class="modal-content">
      <slot></slot>
    </div>
  </div>
</template>
```

```js
import { expect } from 'chai';
import { shallowMount } from '@vue/test-utils';
import BaseModal from '@/components/atoms/BaseModal.vue';

describe('BaseModal', () => {
  context('Template', () => {
    context('existence of the element', () => {
      it("should exist 'modal' element", () => {
        const wrapper = shallowMount(BaseModal);
        expect(wrapper.find("[data-test='modal']").exists()).to.equal(true);
      });
    });
  });
});
```

```js
```

- [Global Registration of Components](https://vuejs.org/v2/guide/components-registration.html): There are components widely used, these are candidates to be registered globally so they can be referenced without importing them.
  An easy way to accomplished this is creating a `_globals.js` file and fill it in with:

```js
// Globally register all base components for convenience, because they
// will be used very frequently. Components are registered using the
// PascalCased version of their file name.

import Vue from 'vue';

// https://webpack.js.org/guides/dependency-management/#require-context
const requireComponent = require.context(
  // Look for files in the current directory
  './atoms',
  // Do not look in subdirectories
  false,
  // Only include .vue files
  /[\w-]+\.vue$/,
);

// For each matching file name...
requireComponent.keys().forEach(fileName => {
  // Get the component config
  const componentConfig = requireComponent(fileName);
  // Get the PascalCase version of the component name
  const componentName = fileName
    // Remove the "./" from the beginning
    .replace(/^\.\//, '')
    // Remove the file extension from the end
    .replace(/\.\w+$/, '');
  Vue.component(componentName, componentConfig.default || componentConfig);
});
```

After that, just import that file in _main.js_ file:

```js
import '@/components/_globals';
```

Some other good practices can be found:

- [Syntax and Logic](https://learnvue.co/2020/01/12-vuejs-best-practices-for-pro-developers/)
- [Slots use](https://www.telerik.com/blogs/10-good-practices-building-maintaining-large-vuejs-projects)
- [Large Scale Vue.js Projects](https://blog.bitsrc.io/4-best-practices-for-large-scale-vue-js-projects-9a533450bdb2)

## Conclusion

As shown above, I had a long way during the last 18 months, I'm giving my two cents in here in the way I understood the concepts, and what I think were the best practices applied; maybe you could have a better way to do it, let's discuss in a thread below!

Thanks for reading!
