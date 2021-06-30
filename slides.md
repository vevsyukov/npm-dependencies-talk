---
# try also 'default' to start simple
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
# background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# some information about the slides, markdown enabled
# info: |
#   ## Slidev Starter Template
#   Presentation slides for developers.

#   Learn more at [Sli.dev](https://sli.dev)
download: true
---

# Short intro to npm dependencies

<a href="https://github.com/vevsyukov/npm-dependencies-talk" target="_blank" alt="GitHub"
  class="abs-br m-6 text-xl icon-btn opacity-50 !border-none !hover:text-white">
  <carbon-logo-github />
</a>

---

# Installing dependencies

.

We have dependencies - some constraints that tell what packages and what versions must be present.

Sometimes there are also implicit constraints of the ecosystem.
For example, in python world all packages are on the same level, there can only be a single version of each package in the environment.


We can view the "installation" process as two major steps:

1. Find a solution to dependency constraints -- a set of packages (= name + version) that satisfies them
1. Download that set of packages and install them


---

# A simple approach

Just a list of dependencies.

The app has a `package.json` file with `dependencies` section.

```jsonc
{
  "name": "frontend",
  "dependencies": {
    "vue": "^2.6.11", // >=2.0.0 <3.0.0
    "vuex": "^2.3.1", // >=2.0.0 <3.0.0
  },
}
```

When we want to install packages we run `npm install`

NPM finds a solution and installs them

<!--
This is what we do in webackend for npm packages
 -->

---

# Issues of that approach

.

## Every time we're trying to find a new solution to dependency constraints.

If the algorithm is non-deterministic, every new installation may result in different set of packages.

Even if the algorithm is deterministic, it can still result in a different set of packages if a set of (remotely) available packages changes.
For example, a new version came out.

<!--
So we can have different versions installed on machine and your machine.

Or on my machine and in the container that we're going to push to prod.
-->

---

# Solving the issue: Option 1

Always specify the exact version for all packages

```diff
  {
    "name": "frontend",
    "dependencies": {
-     "vue": "^2.6.11", // >=2.0.0 <3.0.0
-     "vuex": "^2.3.1", // >=2.0.0 <3.0.0
+     "vue": "2.6.11", // ==2.6.11
+     "vuex": "2.3.1", // ==2.3.1
    },
  }
```

This works!

We lost an option of "one-click" upgrading everything, but one can argue it's rarely needed.

---

# Solving the issue: Option 2

Lockfiles

After finding a solution that satisfies our dependencies, we save it to a file

Then we can use it multiple times and on different machines to guarantee that teammates, deployments, and continuous integration install exactly the same dependencies

We don't spend time on finding a solution on future installations

---

# Lockfiles in NPM

Lockfiles are the current default approach

`npm install`
1. Finds a solution to dependency constraints
1. Writes it to `package-lock.json`
1. Installs packages

* it does not honor `package-lock.json` contents, will always overwrite it with the new tree of packages
* the only guarantee it gives is that after it's finished, the installed set of packages will satisfy package.json dependencies

`npm ci`
1. Cleans already installed packages
1. Installs all packages specified in `package-lock.json`

* does not ever write to `package.json` or `package-lock.json`

---

# Cheatsheet

Commands to use for popular cases

Do not manually change `package-lock.json`<br>
Do not manually change `package.json`'s `dependencies`<br>
Commit `package.json` and `package-lock.json` to the repo

Install packages in any non-local environment: `npm ci` <br>
Install packages in a clean local environment: `npm ci` <br>
Install new set of packages locally if they were changed (like after a pull or checkout): `npm ci`

Reset installed packages locally (e.g. you changed dependencies and decided to not proceed):<br>
discard local changes to `package.json` and `package-lock.json` with git, then `npm ci`

Add a new dependency: `npm install --save[-dev] vue@^2.6`

Change version requirements of a dependency: `npm install --save[-dev] vue@^2.6`

Remove a dependency: `npm uninstall --save[-dev] vue`
