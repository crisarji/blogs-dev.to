---
published: true
title: Vue Slots and Reusable components 
description: Reusable components in Vue(2/3) approaching slots
tags: javascript, vue, github
cover_image: https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/vue-slots/assets/cover.png
series:
canonical_url:
---

## Vue Slots and Reusable components

Hello developer pal!, glad to see you here.

In this post, we will take a look at the way a simple feature as it is `slot` in _vue_ could spare a lot of time and effort when having the same components displaying different data.

Sometimes, we need to replicate the same task with different data, for instance, you have a design system which determines that every single list element throughout the site needs to be consistent, this means having the same height, width, font, etc. Sounds fair!

For accomplishing this, there are 2 options:

1. `Copy + Paste` the same listing component in multiple places; the fallback of this approach comes when, for example, the _font_ of the list item needs to be updated; if you, by chance, have in the project 1, 2 or 3 list items, you can manually make the change, all good!, but what happens when your project has a whole bunch of listing components?, it would be required go one by one and make the change; the effort on dev, qa, and the risk of letting one scenario out of the scope is high.

2. Create a `Reusable Component`, this will allow it to make the change in one only place and affect all of them at once!, here the `slot` concept comes handy, when using a `slot`, you can have the same look and feel, behavior, properties and others available for all the listing components.

## Show Me The Code

The steps to be focused on are:

1. Having 1 reusable listing component
2. Having different view components, injected with different data
3. Having different view components, displaying different elements

Let me share to you the GitHub code [here](https://github.com/crisarji/vuejs-slots).

## 1. Having 1 reusable listing component

How would it look a `slot` for the example purposes?:

```html
<template>
  <div>
    <ul>
      <li v-for="(element, index) in elements" :key="element.id">
        <slot name="element" v-bind:element="element" v-bind:index="index"></slot>
      </li>
    </ul>
  </div>
</template>
```

In the snippet above, there is a `v-for` directive which loops over the array in a property named `elements`; what it is happening is that every single element, and its index, are available to be part of the template, the `slot` has the option for _binding_ values, these are available as soon as the `slot` is invoked.

This simple is to have a reusable component with `slots`!, the html elements can be modified as required: add css, new attributes, new binding props, everything will be ready to be used when invoked the component(check the [GitHub](https://github.com/crisarji/vuejs-slots) repo for a nicer experience including `Tailwind`).

## 2. Having different view components, injected with different data

For the sake of the exercise, 3 different arrays are present for feeding the view components:

```js
export const agents = [
  { id: '07531267-D', name: 'Remedios Carmona', calls: 12, deals: 5 },
  { id: '91958619', name: 'Dalal Heidema', calls: 20, deals: 12 },
  { id: 'NNaN31539321', name: 'Julien Legrand', calls: 25, deals: 17 },
  { id: '1NNaN60472038', name: 'Karina da Cunha', calls: 33, deals: 25 },
  { id: '060469-1435', name: 'Evelyn Scheerer', calls: 40, deals: 35 },
];

export const supervisors = [
  { id: '75687', name: 'Idelso Gonçalves', agents: 10, teams: 1, gender: 'male' },
  { id: '2NNaN64983816', name: 'Cassandra Leroy', agents: 20, teams: 2, gender: 'female' },
  { id: 'N901057Z', name: 'Arron Johnston', agents: 30, teams: 3, gender: 'male' },
  { id: '18066349671', name: 'Siham Reitan', agents: 40, teams: 4, gender: 'female' },
  { id: '48926083', name: 'Mariam Linde', agents: 50, teams: 5, gender: 'female' },
];

export const managers = [
  { id: 'NaNNA831undefined', name: 'Niilo Keranen', department: 'IT', gender: 'male' },
  { id: '2NNaN40789264', name: 'Leana Deschamps', department: 'Operations', gender: 'female' },
  { id: '283707860', name: 'Irma Boyd', department: 'HHRR', gender: 'female' },
  { id: '290471', name: 'Nicole Oehme', department: 'ACC', gender: 'female' },
  { id: '1NNaN44873525', name: 'Antonin Rey', department: 'Facilities', gender: 'male' },
];
```

As you can notice, the `views` will be `Agents`, `Supervisors` and `Managers`; as aforementioned, this items must be listed up following a design system specifications(width, height, font, typo) for keeping the consistency.

Let's suppose that the design system ask for something like these:

`Agents` view:
<br/>

<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/vue-slots/assets/agent-list.png">
</p>
<br/>

`Supervisors` view:
<br/>

<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/vue-slots/assets/supervisor-list.png">
</p>
<br/>

`Managers` view:
<br/>

<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/vue-slots/assets/manager-list.png">
</p>
<br/>

After taking a look at the mocking data and the requirements, what is changing are the fields to display, the order, and the background, since the `Copy+Paste` approach is discarded, the other possible way to go is the _Reusable Components_.

## 3. Having different view components, displaying different elements

Let's dig a bit into the implementation of the _Reusable Component_ and _slots_.

The very first view is the one for `Agents`, we need to import the _reusable component_, register it, add an input property for feeding the list of elements and render it out(skipped steps in here, you can check the source code for more info), let focus in the `template`

`Agents` view:

```html
<template>
  <custom-list :listElements="listElements" itemStyling="justify-between bg-gray-300">
    <template v-slot:element="{ element }">
      <div class="flex w-1/5"></div>
      <div class="flex w-2/5"></div>
      <div class="flex w-2/5"></div>
    </template>
  </custom-list>
</template>
```

A couple of interesting things from above snippet:

- `custom-list`(our reusable component) is expecting for the list of elements and an item styling, also, it is accessing the `element` binding, so the data to be display is dynamic.

What about the second view?

`Supervisors` view:

```html
<template>
  <custom-list :listElements="listElements" itemStyling="justify-between bg-blue-300">
    <template v-slot:element="{ element }">
      <div class="flex w-2/5"></div>
      <div class="flex w-1/5"></div>
      <div class="flex justify-end w-2/5"></div>
    </template>
  </custom-list>
</template>
```

This is pretty similar to the previous one, the biggest difference is that a different color is send to affect the background(in case you are not familiar with Tailwind, it is this `bg-blue-300`), and the order of the divs(the avatar from the mocks will be placed at the center)

Last but not least:

`Managers` view:

```html
<template>
  <custom-list :listElements="listElements" itemStyling="justify-between bg-green-300">
    <template v-slot:element="{ element, index }">
      <div class="flex w-2/5"></div>
      <div class="flex justify-center w-2/5"></div>
      <div class="flex w-1/5"></div>
    </template>
  </custom-list>
</template>
```

Also similar to the other 2(that's the idea), but changing the background color again, the order of the elements to be displayed(avatar at the end) and also including the other binding property declared in the _reusable component_, the `index`.

After all the steps above, this is the output:

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/vue-slots/assets/vue-slots-switching.gif">
</p>
<br/>

_Notes_:

1. You can find the repo for [Vue2](https://github.com/crisarji/vuejs-slots) and [Vue3](https://github.com/crisarji/vue3-slots)
2. Feel free to fork it and/or import it in an online code editor(I tried but there seems to be a known [error](https://stackoverflow.com/questions/66112768/how-to-fix-vue-is-not-defined-in-vue-3-vue-router-4) )

_Challenge_: The `CustomList` component could be register globally, in case you want to stop importing it throughout the site

## Conclusion

As shown above, the `slots` could spare some precious development time, help with the scalability and be customized as required; maybe you could have a better way to do it, let's discuss in a thread below!

Thanks for reading!
