---
theme: seriph
background: https://source.unsplash.com/collection/94734566/1920x1080
class: text-center
highlighter: shiki
lineNumbers: false
info: |
  ## How to build a meetup site
  with Astro, Github pages, Github actions, Octokit and Cloudflare workers

  Learn more at [PlanBee-dev](https://github.com/PlanBee-dev/oulu-dev-meetups)
drawings:
  persist: false
transition: slide-left
title: Welcome to Slidev
mdc: true
---

# How to build a meetup site

With Astro, Tailwind, Github pages, Github actions, Octokit and Cloudflare workers

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Let's find out <carbon:arrow-right class="inline"/>
  </span>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
transition: fade-out
---

# What was built?

We wanted to create a simple and easy to use site to organize local developer meetups in our town. So requirements were quite simple. 

- 📝 **(Mostly) static site** - Not much funcitonality should be needed
- 🎨 **Open source** - Anyone can add a meetup by creating a PR in github
- 🧑‍💻 **Yes, some functionality** - Anyone can create a PR for meetup via form in site

<div class="pt-8 px-12">
  <img border="rounded" width="500"  src="/oulu-devmeetups-hero.jpg">
</div>


<!--
You can have `style` tag in markdown to override the style for the current page.
Learn more: https://sli.dev/guide/syntax#embedded-styles
-->

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

<!--
introduction to topic
-->

---
layout: default
---

# So what 's the whole stack then?

- Astro - For static site generation
- Tailwind - for easy styling
- pnpm - For package management
- Github Actions - For continuous integration and deployment
- Github Pages - For hosting the site
- Cloudflare Workers - serverless backend for meetup submission form
- Octokit - Github API wrapper library for posting meetup data
- Vitest - For unit testing
- Playwright - For end to end testing

---
transition: slide-up
level: 2
layout: iframe-right
url: https://astro.build/
---

# Astro


For building blazingly fast content sites.

Generates static html from content like markdown, same way for example like Gatsby or Next.js. 
However Astro is much simpler and lighter.

Can be used with any framework like React or Vue but also works without any framework for simple sites. Like this one we made. 

---
layout: iframe-right
url: https://pnpm.io/
---

# pnpm

Package manager

Supports monorepos out of the box and is fast. pnpm uses hard links instead of copying dependencies which makes installs very fast.

Compatible with npm and Yarn workflows. It can be dropped into existing projects seamlessly.

Caches all packages globally on disk rather than locally inside each project. 


<style>
.footnotes-sep {
  @apply mt-20 opacity-10;
}
.footnotes {
  @apply text-sm opacity-75;
}
.footnote-backref {
  display: none;
}
</style>

---
transition: slide-up
layout: iframe-right
url: https://tailwindcss.com/
---

# Tailwind


Fast styling for lazy and wooden eyed developers.

Use utility classes for styles.

We used also premade components for common things like cards, navbars etc.

Does tree shaking to only include what is used in the final CSS output.

---
layout: iframe-right
url: https://pages.github.com/
---

# Github Pages

For hosting static sites for free.

Simply the easiest way. 

We deploy the built site to the `gh-pages` branch which github hosts for us at https://planbee-dev.github.io/oulu-dev-meetups

---
layout: image-right
image: ./gh_actions_screen.png
---

# Github Actions

Automating workflows

Used for automating issue and PR creation and deployment workflows.

Workflows are defined in YAML files and run on GitHub hosted runners.

Workflows can be triggered by various events like pushes, pull requests, releases, issues, etc.

[Github actions](https://github.com/features/actions)

---
layout: iframe-right
url: https://workers.cloudflare.com/
---

# Cloudflare Workers

For backend

We used workers to connect our form to github issue creating. The form submits to a worker which then creates an issue in github using Octokit API.

Serverless - Workers run without any servers to manage or provision. Code is deployed to Cloudflare's global edge network. Unlike Aws Lambdas, code for workers does not need to be deployed as zipped file. 

Generous free tier and pay-as-you-go pricing make Workers affordable.


---
layout: image-right
image: ./octokit_screen.png
---

# Octokit

Github's official library for managing GitHub interactions like repositories, issues, pull requests etc. 

Used to create issues for meetups in markdown format to GitHub repository.

```ts
const createIssueRes = await octokit.rest.issues.create({
  owner: props.env.GITHUB_REPO_OWNER,
  repo: props.env.GITHUB_REPO_NAME,
  labels: ['meetup'],
  title: props.meetup.title,
  body: getMeetupIssueBody(props.meetup),
}); 
```

[Octokit](https://github.com/octokit/octokit.js)

---
layout: image-right
image: ./vitest_screen.png
---

# Vitest
We use only blazingly fast stuff, you know it!

Testing library which recently got it's 10000th star on GitHub!

Vitest is extremely fast compared to other frameworks like Jest or Mocha. It launches tests in parallel to optimize performance

Vitest tests can run in node, browsers and other environments. This flexibility allows testing universal codebases.

```ts
test('get next meetup from today', () => {
  const meetups = [
    { title: 'Vue Meetup', date: new Date(2023, 8, 3, 10, 0) },
    { title: 'Angular Meetup', date: new Date(2023, 8, 4, 10, 0) },
    { title: 'React Meetup', date: new Date(2023, 8, 5, 10, 0) },
  ];

  vi.setSystemTime(new Date(2023, 8, 4, 10, 0));
  expect(getNextMeetup(meetups).title).toBe('React Meetup');
});
```

[vitest.dev/](https://vitest.dev/)

---
layout: iframe-right
url: https://playwright.dev/
---

# Playwright

Browser testing tool for integration and end-to-end testing.

Used to test Astro's production build by starting a local server and asserting elements on the page.

Supported by `@testing-library` ecosystem for accessibility focused DOM assertions.

```ts
test('main heading is set', async () => {
  const page = await browser.newPage();

  await page.goto('http://localhost:4321/');

  await page.getByRole('heading', {
    level: 1,
    name: 'Developer meetups in Oulu area'
  });
});
```

[https://playwright.dev/](https://playwright.dev/)

---
layout: iframe-right
url: https://ouludevmeetup.com/
---

# Thank you

Site is already live 

Link: [ouludevmeetup.com](https://ouludevmeetup.com)

Repository: [GitHub](https://github.com/PlanBee-dev/oulu-dev-meetups) 
