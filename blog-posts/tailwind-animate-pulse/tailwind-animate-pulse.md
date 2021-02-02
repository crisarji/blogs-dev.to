---
published: false
title: "Tailwind Feature: animate-pulse"
cover_image: "https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/tailwind-animate-pulse/assets/tailwind-animate-pulse-tw.gif"
description: "Roles addition to the previous authentication using Firebase user claims, Firebase functions and Vue routing"
tags: javascript, vue, tailwind
series:
canonical_url:
---

## What's animate-pulse in Tailwind?

Hello developer pal!, glad to see you here, have you ever been wandering on your social networks and suddenly you find a post which caught your attention, you want to see the comments, click in the link, and have the impression that a whole bunch of comments are coming cause you see a sort of template which fades in and out, and after a few milliseconds, it loads 1 comment, or sometimes 0!? it is a a mix of feelings, you are half happy/ half disappointed; well today we will be talking about this really cool feature, then you can pull the leg to your own users!.

For this endeavor, it is this reaaaally cool feature included in TailwindCss, the `animate-pulse` you can read more information right [here](https://tailwindcss.com/docs/animation#pulse).

## Show Me The Code

_Disclaimer_: For this post, want to show 1 possible approach used in some other projects, maybe not the best way to do it, maybe not the worst, please feel free to start a thread in the comments section below in case of question, suggestions, improvements.

The 3 main pieces to be focus on are:

1.  The use of Tailwind.
2.  A practical example using VueJS.
3.  The code posted in Stackblitz(so you can play around)

Let me share to you the Stackblitz code [here](https://stackblitz.com/edit/tailwindcss-animate-pulse).

## Want some explanation? chop-chop!

The `animate-pulse` causes this fade in/out effect, it is a nice way for letting the user know that something is happening under the hood and keep them hooked!, let see step by step how to accomplish something like this.

## Step 1

### Set a list of elements

For example purposes, a `employees.js` file is being consumed, it has the elements to be used for loading the component; notice that this can be changed for an API response or any other resource invocation for feeding the component up.

Every element has a shared structure that will be important for the pulse effect a bit further.

```js
  ...
  const employees = [
  {
    id: "Emp-001",
    name: "Yusha Beil",
    avatar: "https://i.pinimg.com/736x/3f/94/70/3f9470b34a8e3f526dbdb022f9f19cf7.jpg",
    isActive: true
  },
  {
    id: "Emp-002",
    name: "Fearne Greene",
    avatar: "https://i.pinimg.com/736x/3f/94/70/3f9470b34a8e3f526dbdb022f9f19cf7.jpg",
    isActive: true
  },
  {
    id: "Emp-003",
    name: "Keegan Cortes",
    avatar: "https://i.pinimg.com/736x/3f/94/70/3f9470b34a8e3f526dbdb022f9f19cf7.jpg",
    isActive: true
  },
  {
    id: "Emp-004",
    name: "Anton Chaney",
    avatar: "https://i.pinimg.com/736x/3f/94/70/3f9470b34a8e3f526dbdb022f9f19cf7.jpg",
    isActive: false
  },
  {
    id: "Emp-005",
    name: "Ruari Mellor",
    avatar: "https://i.pinimg.com/736x/3f/94/70/3f9470b34a8e3f526dbdb022f9f19cf7.jpg",
    isActive: false
  }
];

export default employees;

  ...
```

## Step 2

### Have some props ready to be filled

There are 2 important properties required when following this approach: 1 the `listElements` and 2 `isLoading`(or any other named flag for noticing when an operation still pending).

It is important to remark that as the `listElements` is faking an API call, the `isLoading` is mocking a state flag, this could be a flag shared in `Vuex` state manager for keeping the interaction up to date at any time.

```js
  ...
  props: {
    listElements: {
      type: Array,
      default: () => [...employees] //Note: this employees comes from the imported file, in a real scenario the default would be `[]` and the prop will be feeded externally
    },
    isLoading: {
      type: Boolean,
      default: true
    }
  },
  ...
```

_Note_: For testing purposes, the `isLoading` flag is set after a `setTimeout` in the `mounted` hook, thus mimicking the state manager behavior as follows:

```js
  mounted() {
    setTimeout(() => (this.isLoading = false), 3000); //Note: When interacting with a state manager(e.g. Vuex), remove this hook and let the flag to be filled from a state getter.
  }
```

## Step 3

### Have a data prop ready with a list of fake elements to be loop through

Here is where the tweaks start on!, handling this list of fake elements will allow the component to iterate over a ready-to-use list at any time

```js
  ...
  data: {
    name: "Example List!",
    fakeElements: [1, 2, 3, 4, 5]
  },
  ...
```

You can add as many fake elements as you want; for not overwhelming the example only 5 are added here, it matches the number of entries to be added after flipping the flag `isLoading`.

## Step 4

### Let a computed property to determine which list to render

Another small tweak for switching between the fake list(always ready) and the real list of elements.

This way it is for sure that the html will have a list of elements at any time, the trick is just to flip between the real deal and the fake stuff, as easy as that!

```js
  ...
  computed: {
    renderList() {
      return this.isLoading ? this.fakeElements : this.listElements;
    }
  }
  ...
```

## Step 5

### The component html

Vuejs allows to set classes to the html elements as expressions, that's the final piece of code, add the `animate-pulse` for the more relevant elements depending on the flag value `isLoading`.

Since the fake list is always ready to be looped, and it is composed of barely valid elements, none of the properties are shown, therefore the effect can be used as long as the flag does not change in all the desired elements.

```html
<div>
  <h1>{{name}}</h1>
  <div class="w-screen bg-transparent flex items-center justify-around">
    <ul class="w-full max-w-md overflow-auto">
      <li
        v-for="element in renderList"
        :key="element.id"
        class="p-4 mb-3 flex items-center justify-between bg-white shadow rounded-lg cursor-move"
      >
        <template>
          <div class="flex items-center">
            <img
              class="w-10 h-10 rounded-full"
              :src="element.avatar"
              :alt="element.name"
              :class="{'animate-pulse bg-gray-400' : isLoading}"
            />
            <p
              class="ml-2 text-gray-700 font-semibold font-sans tracking-wide break-all md:break-words"
              :class="{'animate-pulse bg-gray-400 w-48 h-6' : isLoading}"
            >
              {{element.name}}
            </p>
          </div>
          <div class="flex items-center">
            <button
              class="btn mx-4 px-4 rounded"
              :class="[{'animate-pulse w-12 h-6' : isLoading}, element.isActive ? 'bg-green-400' : 'bg-red-400']"
              @click="editItem(element)"
            >
              <span class="capitalize text-white">{{isLoading ? '' : 'Edit'}}</span>
            </button>
          </div>
        </template>
      </li>
    </ul>
  </div>
</div>
```

In the code above, notice a few thing, for example, when loading the values, the `animate-pulse` is applied from 1 to N elements, this means that it is possible to include the animation on any html element depending on your own requirements, thus it is possible to match the styles even with no data available.

A good example is the _button_ element, the width and height is given by the text content rendered on it; when loading, we don't want to show the label to be used, so there is no way to calculate that value, the easiest way to get rid off this issue is to assign a width and height as long as the real data is not available, and that's exactly what happens on
`{'animate-pulse w-12 h-6' : isLoading}`, the effect is active with a Tailwind height and width whilst processing the real data.

## Conclusion

AS shown above, this Tailwind feature is pretty cool and can be integrated with api calls results and loading flags in the state manager to make the user feel more comfortable when interacting with your site/app; maybe you could have a better way to do it, let's discuss in a thread below!

Thanks for reading!
