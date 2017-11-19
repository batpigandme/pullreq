---
title: "PRs in the Tidyverse: Part 1"
author: "Mara Averick"
output:
  html_document:
    keep_md: TRUE
---



```
Dear Sir or Madam Maintainer,

Whence I was reviewing your package documentation, I came upon a grammatical flaw
in your prose. This was ever so troubling. I recommend you look at the fifth 
line down in the third paragraph of the...

Ever so humbly yours,

Rando
```

This document is an effort to avoid the scenario above. Though Yihui Xie's 
[You Do Not Need to Tell Me I Have A Typo in My Documentation](https://yihui.name/en/2013/06/fix-typo-in-documentation/) is right
on many levels, I've been on the other side of the screen: seeing a typo, 
and wanting to fix it without hassling the maintainers, but have given up half-way
through because I didn't know _where_ to fix it. I know the correct answer lies
somewhere upstream, but I might not be exactly sure where. In some instances, 
this isn't the case— I know that a `README.md` file is the output of a 
`README.Rmd`, if it exists. But with package documentation (vignettes, 
pkgdown sites, etc) it's not as readily apparent.

You don't need to be an R neophyte for the above to be true. True confession: I 
really didn't understand where those `do not edit by hand` files came from until 
I did an rOpenSci [package review](https://ropensci.org/blog/2017/08/22/first-package-review/)
for Nick Tierney's 🌟 [`visdat`](http://visdat.njtierney.com/).[^1]
No one wins in this scenario. Nobody wants typos, and this kind of minor fix is 
a great way to get familiar with the mechanics of making contributions. Plus, 
I'd way rather have package maintainers working on those sweet feature requests 
I submitted than hunting around for a renegade letter or two in the function 
reference.

An alternate title to this could've been **Packages for people who don't actually 
want to write packages**. Expect hand-waiving and references to magic, invisible 
happenings, because this is a bare-minimum, forensic approach.

## What is this?

This is a step-by-step guide to the basics of submitting a pull request 
on [GitHub](https://github.com/tidyverse) for 
[tidyverse](https://www.tidyverse.org/) packages. Though the example we'll work 
through (a one-letter change to some dplyr documentation) is _not_ code, per se, 
you _will_ need some knowledge of R and RStudio in order for this to make sense.

You should also be acquainted with Git and GitHub. If this _isn't_ you, but you 
want to learn more about these things, I highly recommend checking out 
[Happy Git and GitHub for the useR](http://happygitwithr.com/) by Jenny Bryan
(a work in progress, freely available online).

There are many great guides on contributing to open-source projects in general,
and this is intended as a complement those guides. This is meant to make the 
existing [tidyverse contribution guidelines](https://www.tidyverse.org/contribute/),
and [The tidyverse style guide](http://style.tidyverse.org/) more accessible, 
and not to replace them. 

In addition to the [tidyverse packages](https://www.tidyverse.org/packages/), 
you'll need:  

 * [roxygen2](https://github.com/klutometis/roxygen)
 * [devtools](https://github.com/hadley/devtools), and
 * [pkgdown](https://hadley.github.io/pkgdown/index.html)

If you already know all about those packages and how to use them, you probably 
don't need to be reading this. If you have heard those package names, and maybe
use devtools to install packages from non-CRAN sources, you're in the right place.
👍

## Where do typos come from?



[^1]: Which, yes, is totally one of the great reasons to sign up to do one, too!
