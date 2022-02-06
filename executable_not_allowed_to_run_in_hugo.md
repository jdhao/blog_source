---
title: "External Executable Not Allowed to Run after Hugo v0.91?"
date: 2022-02-02T13:20:20+08:00
draft: false
tags: [Hugo]
categories: [Blog]
---

Today I upgrade [Hugo](https://github.com/gohugoio/hugo) from v0.75 to the latest version (v0.92).
When I run the deployment script to build the site and push, I saw the following error:

<!--more-->

> error: Error building site:
> "/Users/jdhao/Blog/content/post/back-propagation-in-mlp-explained.pdc:1:1":
> access denied: "pandoc" is not whitelisted in policy "security.exec.allow";

# The cause

From the [release note of v0.91](https://github.com/gohugoio/hugo/releases/tag/v0.91.0), we can find the reason (the part about `New Security Configuration`).

> This release also adds some new security hardening measures for the Hugo
> build runtime in the form of a new security configuration. There are some
> rarely used features in Hugo that would be good to have disabled by default.
> One example would be the "external helpers".

So Hugo v0.91 introduces a set of security config meant to harden the building environment.
If we use external tools like `pandoc` (as the Markdown renderer), `nvim` or `emacs` (as the [new content editor](https://gohugo.io/getting-started/configuration/#newcontenteditor)),
we now need to allow them explicitly in our configuration. Otherwise, they are not allowed to run.

# Solution

The solution is simple. Edit the `config.toml` under site root, and add the following security section:

```toml
[security]
  enableInlineShortcodes = false
  [security.exec]
    allow = ['^dart-sass-embedded$', '^go$', '^npx$', '^postcss$', '^pandoc$', '^nvim$']
    osEnv = ['(?i)^(PATH|PATHEXT|APPDATA|TMP|TEMP|TERM)$']

  [security.funcs]
    getenv = ['^HUGO_']

  [security.http]
    methods = ['(?i)GET|POST']
    urls = ['.*']
```

Add the needed executable to the `security.exec.allow` white list. After that, the error should disappear.

# References

+ security issues:
    + https://github.com/gohugoio/hugo/issues/9315
    + https://github.com/gohugoio/hugo/issues/9420
