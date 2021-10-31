---
published: true
title: The Whats & Whys in a FE Senior position
description: Concepts to handle when opting in for a senior position and the reason behind
tags: javascript, webframeworks, techskills, senior
cover_image: https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/sr-techskills-what-why-2021/assets/what-and-why-2021.png
series:
canonical_url:
---

## Tech Skills: The Whats & Whys in a FE Senior position

Hello developer pal!, glad to see you here.

Most of the time, we heard that some knowledge is <span style="color: red">required</span> for certain position, usually we heard `What` is required, but what about the `Why` is it required?.
In this post, I'll share my thoughts in the reasons why some tech skills are mandatory when being/opting-in for a _Frontend Senior position_, I'll leave aside the must of `HTML`, `CSS`, `GIT`(or some other version control tools).

## Show Me The Topics

The topics to be focused on are:

1. `Programming Languages & Web Frameworks`
2. `CSS Frameworks & Preprocessors`
3. `Design Systems`
4. `Testing/Debugging`
5. `DevOps/Automation`
6. `State Managers`
7. `SPA/SSR/SSG`
8. `Rest/Graphql`
9. `Build Process: under the hood`

_Disclaimer_: This post comes from my own experience and what I have seen so far in the area, not saying this is the best way to go, nor the worst, any contribution is more than welcome in the threads below!

## Programming Languages & Web Frameworks

Want to know `What` to learn in this 2021?, take a look:

<br/>
<p align="center">
  <img width="90%" src="https://raw.githubusercontent.com/crisarji/blogs-dev.to/master/blog-posts/sr-techskills-what-why-2021/assets/most-demanded-programming-languages-2021.png">
</p>
<br/>

> Source: [Most Popular Languages 2021](https://cdn.ucberkeleybootcamp.com/wp-content/uploads/sites/106/2020/12/most-demand-programming-languages-2021.jpg)

As a FE dev, having [Javascript](https://www.javascript.com/) or [Python](https://www.python.org/) is a must, the most used languages worldwide now.

`Why` should you learn either or both of them? because they are the core for the main Web Frameworks out there, for sure you have heard about these:

- `Angular`
- `Django`
- `React`
- `Flask`
- `Vue`

The list of web frameworks aforementioned is built on top of either, `Javascript` or `Python`. Having a robust base will help you to understand how to tackle an obstacle, or at least will give you a hunch, also will allow you to move from one framework to another easier.

## CSS Frameworks & Preprocessors

`What` can I find when starting in a new project?

- CSS Frameworks:
  - [Bootstrap](https://getbootstrap.com/)
  - [Tailwind](https://tailwindcss.com/)
  - [Materialize](https://materializecss.com/)
- CSS Preprocessors:
  - [Sass](https://sass-lang.com/)
  - [Less](https://lesscss.org/)

Nice!, all the heavy lifting related to the styling ready to be used!

`Why` should I care when it is only plug & play?

Well, because you do need to know the small hacks that could spare some precious time for you and your team!; for instance how to declare a _variable_, a _mixin_, the corresponding _breakpoints_, even adding a _media query_ changes a little depending on the framework. Also, you need to know your preprocessor for avoiding duplication of _classes_, how to apply _specificity_(in the right way), writing less and getting more.

## Design Systems

`What` is a design system?, well as I exposed in a [previous post](https://dev.to/crisarji/my-vue-experience-after-3-projects-in-18-months-456c), this is a converging point for different areas, which has 3 fundamental goals:

> 1.  `UI/UX` does not repeat itself
> 2.  `WebDev` has one-and-only-one source of truth
> 3.  `QA` can evacuate questions on its own
>
> Source: [My Vue Experience after 3 projects in 1.5 years](https://dev.to/crisarji/my-vue-experience-after-3-projects-in-18-months-456c)

`Why` should I remotely care about other areas such as `UI/UX`?

Because nothing last forever, you could be so comfortable in your current project, with a design system already defined that you just use, and tomorrow you loose the client, the account is abruptly finished, you had a discussion with a stakeholder and you are expelled, then you move to another project, and you are asked to start working from scratch.

Wouldn't it be better to start fresh with some knowledge than with no knowledge at all?, well you should care about this point since it is easier to talk with the `UI/UX` person in charge of defining the Design System when both understand what's going on, the pros and cons, the different approaches, the changes, discussions, and agreements are easier when both areas are on the same page.

_TIP_: Among the best tools for accomplishing this sync up is [Storybook](https://storybook.js.org/), if you are not familiar yet, you could give it a try, thanks me later.

## Testing/Debugging

`What` are the unit testing tools you are more familiar with?, let me guess:

- `Mocha`
- `Chai`
- `Jest`
- `Karma`
- `Jasmine`

`Why` all his hype with the unit testing when you know you did it in the right way?

Easy!, it is our responsibility to ensure that new components, services, changes on the state of the application does not break higher environments, and does not break other pieces of the application added by other people.

A well performed unit testing is a relief, and can help to have a high coverage, also help the next dev to understand faster and better, even you can understand better the code when writing the unit tests.

_TIP_: Maybe you would like to take a look at [Husky](https://www.npmjs.com/package/husky), this little fellow has some awesome features, for instance, you can set the rules for writing the commits your historical will have, also you can run all the tests before committing, when all tests success it allows the addition, otherwise it does not commit.

## DevOps/Automation

`What` are some of the responsibilities _DevOps_ and _Automation_ people have in your project?:

- `Responsible of CI/CD`
- `Create environments`
- `Check Pipes and Jobs statuses`
- `Run the automation alongside the Jobs`
- `Create, check, correct and fix test suites`

`Why` is it important to know about these processes when there is a person in charge of it?

Because sometimes the DevOps, Automation and Dev are the same, it is a bad practice(from my perspective), but how could you be sure this is not gonna happen to you sooner or later?, if so you better rise the flag, but some other times you could be unblocking your own team, let me elaborate.

DevOps and Automation Testers are human beings too, these persons can have PTOs, sick days, appointments, capacitations, for any number of reasons can be out of work, in case of issues, what would it happen?, is your team ok with the fact to wait from 1 to several days before a fix is done in pipes or releases?, knowing about devops and automation processes can help to unblock not only your team, also others(in case you do have granted permissions, otherwise, you'll have some free time to read a book or take a couple courses)

_TIP_: there are plenty of platforms for working and understanding `CI/CD`, you can check for instance [GitHubActions](https://github.com/features/actions), [TravisCI](https://travis-ci.org/), [CircleCI](https://circleci.com/) before jumping to [Azure](https://docs.microsoft.com/en-us/azure/devops-project/azure-devops-project-github), [AWS](https://aws.amazon.com/es/getting-started/projects/set-up-ci-cd-pipeline/) or [Jenkins](https://www.cloudbees.com/jenkins/what-is-jenkins)

## State Managers

`What` are those libs, patterns or packages you have heard of?:

- `Redux`
- `RxJS`
- `React/Redux`
- `NgRx`
- `Vuex`

`Why` should I know any of those when http, services and local storage are available?

Well, because sometimes you will not be handling small apps/sites, when starting a client project, or even one on your own, keep in mind always the scalability of the project; local storage and its derivatives are an option, but a state manager for a SPA could be a better option. Adding new features involves add new setters, getters, actions, services interacting to each other, the final goal is to keep up to date the state for as long as the user has a session, hit the DB as few as possible and display required info in the fastest way.

## SPA, SSR, SSG

`Why` should I know something other than SPA?, `What` could they aport to my experience?

Maybe you are used to write a _SPA_ for every single scenario, but what if I told you that there are some other valid approaches for fulfilling a client expectations?

Not everyone needs a _SPA_, maybe the client needs a particular feature for holding a blog post, or a wiki, which changes every once in a while, or barely changes at all, would not it be better a _Static Site_?, maybe a _Server Side Generator_ does the job with a better performance, `Gastby`?, `VuePress`? those are valid options!

Or even better, the users need to interact with the page, and needs a better _SEO_ positioning, what about a _Server Side Rendering_?, `Nuxt` or `Next` could be a better approach(yes, we'll always have `WordPress`, up to you)!

Perhaps you cant decide the right approach because you dont know the difference between them, well for being/opting to a Senior position you should, this is part of the tech conversations you could be holding with a stakeholder, a Product Owner, or some other peers when deciding what's the best for business. You can read a bit more bout the differences [here](https://graphcms.com/blog/difference-spa-ssg-ssr)

## Rest, Graphql

`Why` is Rest always on the table?, `What` is this trend for Graphql when I already master Rest?

The only constant is the change, even more in technology; some years ago, there was something called [SOAP](https://en.wikipedia.org/wiki/SOAP), the most used way to interact with http request/responses in `xml` format; later we had(and still have) [Rest](https://en.wikipedia.org/wiki/Representational_state_transfer) has been hanging around for a while, the days of interacting with `XMLs` are over(for most of the devs out there), plenty of services rely on Rest now; [Graphql](https://graphql.org/) is the new member of the family, even though it is not a protocal, instead a query language, makes the API interaction cleaner, it is easy to learn and use, and has a lot of support, so better start learning now!

## Build Process: Under the hood

`Why` should you know what happens in the backstage?, is not enough knowing `What` are the steps to follow?

Errors during the building phase of an application are not odd, in fact, several times they are expected, knowing about the process under the hood can give some guidance related to the issues and how to fix them; also what if the client complaints about a lot of console errors, the performance of the page is too low, the build takes a lot of time to get deployed, all of these could be part of a lack of knowledge in the process of building and deploying.
Required flags, changing the build environment, optimize the build, tree shaking the build, handling alerts on the build size, all of this needs to be covered; learning about [Webpack](https://webpack.js.org/) and [Rollup](https://rollupjs.org/guide/en/) could have a big impact in your next deploy.

## Conclusion

As shown above, there is a sort of gap between the `what` is required for being/opting-in for a Senior position and `why` it is required; it is not the fact to be an expert in all the areas, but reading and practicing a bit will help you to sharp the skills and when the time comes it gets easier to fit in in a project!

Thanks for reading!
