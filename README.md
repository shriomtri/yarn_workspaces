::: {.news-container}
[](https://yarnpkg.com/getting-started/migration){.news-overlay}

::: {.news-inner}
::: {.news-line}
[Important:]{.news-highlight} This documentation covers Yarn 1
(Classic).
:::

::: {.news-line}
For Yarn 2+ docs and migration guide, see yarnpkg.com.
:::
:::
:::

::: {.container}
[Yarn]{.sr-only}

::: {.clearfix .hidden-lg-up}
:::

::: {#navbar .collapse .navbar-toggleable-md}
-   [Getting
    Started](https://classic.yarnpkg.com/en/docs/getting-started){.nav-link}
-   [Docs](https://classic.yarnpkg.com/en/docs){.nav-link}
-   [Packages](https://classic.yarnpkg.com/en/packages){.nav-link}
-   [Blog](https://classic.yarnpkg.com/blog){.nav-link}

```{=html}
<!-- -->
```
-   English
    ::: {#dropdownNavLanguageMenu .dropdown-menu aria-labelledby="dropdownNavLanguage"}
    [English](https://classic.yarnpkg.com/en/docs/workspaces){.dropdown-item
    .active}
    [Español](https://classic.yarnpkg.com/es-ES/docs/workspaces){.dropdown-item}
    [Français](https://classic.yarnpkg.com/fr/docs/workspaces){.dropdown-item}
    [Bahasa
    Indonesia](https://classic.yarnpkg.com/id-ID/docs/workspaces){.dropdown-item}
    [日本語](https://classic.yarnpkg.com/ja/docs/workspaces){.dropdown-item}
    [Português
    (Brasil)](https://classic.yarnpkg.com/pt-BR/docs/workspaces){.dropdown-item}
    [Русский](https://classic.yarnpkg.com/ru/docs/workspaces){.dropdown-item}
    [Türkçe](https://classic.yarnpkg.com/tr/docs/workspaces){.dropdown-item}
    [Українська](https://classic.yarnpkg.com/uk/docs/workspaces){.dropdown-item}
    [中文](https://classic.yarnpkg.com/zh-Hans/docs/workspaces){.dropdown-item}
    [繁體中文](https://classic.yarnpkg.com/zh-Hant/docs/workspaces){.dropdown-item}
    :::
-   Discord
    [Discord]{.sr-only}
-   Twitter
    [Twitter]{.sr-only}
-   Facebook
    [Facebook]{.sr-only}
-   GitHub
    [GitHub]{.sr-only}
:::
:::

::: {#search}
::: {.ais-InstantSearch__root}
::: {.full-searchbox}
::: {.ais-SearchBox}
:::
:::
:::
:::

::: {role="main"}
::: {.hero}
::: {.container}
Workspaces {#workspaces .hero-text .display-4}
==========
:::
:::

::: {.container}
::: {.row}
::: {.col-md-8 .guide}
::: {.guide-content}
Workspaces are a new way to set up your package architecture that's
available by default starting from Yarn 1.0. It allows you to setup
multiple packages in such a way that you only need to run
`yarn install`{.highlighter-rouge} once to install all of them in a
single pass.

### Why would you want to do this? [](#toc-why-would-you-want-to-do-this){#toc-why-would-you-want-to-do-this .toc} {#why-would-you-want-to-do-this-}

-   Your dependencies can be linked together, which means that your
    workspaces can depend on one another while always using the most
    up-to-date code available. This is also a better mechanism than
    `yarn link`{.highlighter-rouge} since it only affects your workspace
    tree rather than your whole system.

-   All your project dependencies will be installed together, giving
    Yarn more latitude to better optimize them.

-   Yarn will use a single lockfile rather than a different one for each
    project, which means fewer conflicts and easier reviews.

### How to use it? [](#toc-how-to-use-it){#toc-how-to-use-it .toc} {#how-to-use-it-}

Add the following in a `package.json`{.highlighter-rouge} file. Starting
from now on, we'll call this directory the "workspace root":

**package.json**

::: {.language-json .highlighter-rouge}
::: {.highlight}
``` {.rougeHighlight}
{
  "private": true,
  "workspaces": ["workspace-a", "workspace-b"]
}
```
:::
:::

Note that the `private: true`{.highlighter-rouge} is required!
Workspaces are not meant to be published, so we've added this safety
measure to make sure that nothing can accidentally expose them.

After this file has been created, create two new subfolders named
`workspace-a`{.highlighter-rouge} and `workspace-b`{.highlighter-rouge}.
In each of them, create another `package.json`{.highlighter-rouge} file
with the following content:

**workspace-a/package.json:**

::: {.language-json .highlighter-rouge}
::: {.highlight}
``` {.rougeHighlight}
{
  "name": "workspace-a",
  "version": "1.0.0",

  "dependencies": {
    "cross-env": "5.0.5"
  }
}
```
:::
:::

**workspace-b/package.json:**

::: {.language-json .highlighter-rouge}
::: {.highlight}
``` {.rougeHighlight}
{
  "name": "workspace-b",
  "version": "1.0.0",

  "dependencies": {
    "cross-env": "5.0.5",
    "workspace-a": "1.0.0"
  }
}
```
:::
:::

Finally, run `yarn install`{.highlighter-rouge} somewhere, ideally
inside the workspace root. If everything works well, you should now have
a similar file hierarchy:

::: {.highlighter-rouge}
::: {.highlight}
``` {.rougeHighlight}
/package.json
/yarn.lock

/node_modules
/node_modules/cross-env
/node_modules/workspace-a -> /workspace-a

/workspace-a/package.json
/workspace-b/package.json
```
:::
:::

*Note: don't look for `/node_modules/workspace-b`{.highlighter-rouge}.
It won't be there unless some other package use it as a dependency.*

And that's it! Requiring `workspace-a`{.highlighter-rouge} from a file
located in `workspace-b`{.highlighter-rouge} will now use the exact code
currently located inside your project rather than what is published on
npm, and the `cross-env`{.highlighter-rouge} package has been correctly
deduped and put at the root of your project to be used by both
`workspace-a`{.highlighter-rouge} and `workspace-b`{.highlighter-rouge}.

Please note the fact that `/workspace-a`{.highlighter-rouge} is aliased
as `/node_modules/workspace-a`{.highlighter-rouge} via a symlink. That's
the trick that allows you to require the package as if it was a normal
one! You also need to know that the
`/workspace-a/package.json#name`{.highlighter-rouge} field is used and
not the folder name. This means that if the
`/workspace-a/package.json`{.highlighter-rouge}
`name`{.highlighter-rouge} field was `"pkg-a"`{.highlighter-rouge}, the
alias will be the following:
`/node_modules/pkg-a -> /workspace-a`{.highlighter-rouge} and you will
be able to import code from `/workspace-a`{.highlighter-rouge} with
`const pkgA = require("pkg-a");`{.highlighter-rouge} (or maybe
`import pkgA from "pkg-a";`{.highlighter-rouge}).

### How does it compare to Lerna? [](#toc-how-does-it-compare-to-lerna){#toc-how-does-it-compare-to-lerna .toc} {#how-does-it-compare-to-lerna-}

Yarn's workspaces are the low-level primitives that tools like Lerna can
(and [do](https://github.com/lerna/lerna/pull/899)!) use. They will
never try to support the high-level feature that Lerna offers, but by
implementing the core logic of the resolution and linking steps inside
Yarn itself we hope to enable new usages and improve performance.

### Tips & Tricks [](#toc-tips-tricks){#toc-tips-tricks .toc} {#tips--tricks-}

-   The `workspaces`{.highlighter-rouge} field is an array containing
    the paths to each workspace. Since it might be tedious to keep track
    of each of them, this field also accepts glob patterns! For example,
    Babel reference all of their packages through a single
    `packages/*`{.highlighter-rouge} directive.

-   Workspaces are stable enough to be used in large-scale applications
    and shouldn't change anything from the way the regular installs
    work, but if you think they're breaking something, you can disable
    them by adding the following line into your Yarnrc file:

    ::: {.highlighter-rouge}
    ::: {.highlight}
    ``` {.rougeHighlight}
    workspaces-experimental false
    ```
    :::
    :::

-   If you're only making changes to a single workspace, use
    [--focus](https://classic.yarnpkg.com/blog/2018/05/18/focused-workspaces)
    to quickly install sibling dependencies from the registry rather
    than building all of them from scratch.

### Limitations & Caveats [](#toc-limitations-caveats){#toc-limitations-caveats .toc} {#limitations--caveats-}

-   The package layout will be different between your workspace and what
    your users will get (the workspace dependencies will be hoisted
    higher into the filesystem hierarchy). Making assumptions about this
    layout was already hazardous since the hoisting process is not
    standardized, so theoretically nothing new here. If you encounter
    issues, try using [the `nohoist`{.highlighter-rouge}
    option](https://classic.yarnpkg.com/blog/2018/02/15/nohoist/)

-   In the example above, if `workspace-b`{.highlighter-rouge} depends
    on a different version than the one referenced in
    `workspace-a`{.highlighter-rouge}'s package.json, the dependency
    will be installed from npm rather than linked from your local
    filesystem. This is because some packages actually need to use the
    previous versions in order to build the new ones (Babel is one of
    them).

-   Be careful when publishing packages in a workspace. If you are
    preparing your next release and you decided to use a new dependency
    but forgot to declare it in the `package.json`{.highlighter-rouge}
    file, your tests might still pass locally if another package already
    downloaded that dependency into the workspace root. However, it will
    be broken for consumers that pull it from a registry, since the
    dependency list is now incomplete so they have no way to download
    the new dependency. Currently there is no way to throw a warning in
    this scenario.

-   Workspaces must be descendants of the workspace root in terms of
    folder hierarchy. You cannot and must not reference a workspace that
    is located outside of this filesystem hierarchy.

-   Nested workspaces are not supported at this time.
:::

::: {.guide-controls}

------------------------------------------------------------------------

::: {.clearfix}
:::
:::
:::

::: {.col-md-4}
-   [Workspaces]{.nav-link .active}
:::
:::
:::
:::

------------------------------------------------------------------------

::: {.container}
::: {.footer-left}
[Yarn]{.footer-item} [[Distributed under BSD
License](https://github.com/yarnpkg/yarn/blob/master/LICENSE)]{.footer-item}
[[Code of
Conduct](https://classic.yarnpkg.com/en/org/code-of-conduct)]{.footer-item}
:::

::: {.footer-right}
[[Edit this
page](https://github.com/yarnpkg/website/edit/master/lang/en/docs/workspaces.md)]{.footer-item}
:::
:::
