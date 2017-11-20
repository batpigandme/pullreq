---
title: "PRs in the Tidyverse: Part 1"
author: "Mara Averick"
date: "2017-11-20"
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
on many levels. 

![Source: You Do Not Need to Tell Me I Have A Typo in My Documentation by Yihui Xie](https://i.imgur.com/nKeGYGz.png)  


But, I've also been on the other side of the screen: seeing a typo, 
and wanting to fix it without hassling the maintainers, but have given up half-way
through because I didn't know _where_ to make the change it. I know the correct answer is 
_somewhere_ upstream, but I might not be exactly sure where. In some instances, 
this isn't the case— I know that a `README.md` file is the output of a 
`README.Rmd`, if it exists. But with package documentation (vignettes, 
pkgdown sites, etc) it's not as readily apparent.

![R Markdown document to Markdown document](https://i.imgur.com/4euq7r2.png)

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
 * [devtools](https://github.com/hadley/devtools)
 * [pkgdown](https://hadley.github.io/pkgdown/index.html)
 * [testthat](http://testthat.r-lib.org/)

If you already know all about those packages and how to use them, you probably 
don't need to be reading this. If you have heard those package names, and maybe
use devtools to install packages from non-CRAN sources, you're in the right place.
👍

## Where do typos come from?

Open scene: you're reading up on the `summarise.R` function in dplyr and notice 
that there's an error in subject-verb agreement in the following fragment, 
`"Data frames are the only backend that supports..."`. The subject 
(data frame_s_) is plural, but the verb (support_s_) is singular.

You click on the GitHub icon, which takes you to the `dplyr` repo, but where are 
you supposed to make your edit? The page you were looking at was `HTML`. 
However, that HTML was the output of a different file. This means that, if you 
modify the HTML file, the correction would show up in the product, but not its 
source. The package maintainer would then have to either fix the source file by 
hand, or risk losing the change the next time the website output is rendered. 
The relationship is unidirectional.

### A detour into `pkgdown` 

Here is (almost) everything you need to know about pkgdown for the purposes of 
this guide: it makes static HTML sites for R packages. It's special because it 
leverages the pre-existing conventions for writing R documentation to render 
these sites, so the content only needs to be edited in one place.[^pkgdown]

Different components of a pkgdown site come from different parts of an R 
package, some of which have already been rendered by 
[`roxygen2`](https://github.com/klutometis/roxygen). I _highly_ recommend you 
take a quick look at Hilary Parker's [_Writing an R package from scratch_](https://hilaryparker.com/2014/04/29/writing-an-r-package-from-scratch/) 
as it covers the roxygen basics really well. (Heck, if you're not feeling up to 
the five/six minutes of reading, just skim *Step 4*).

## OTHERS MAY DIFFER

Because the majority of R users work with the CRAN version of a package, 
the documentation on tidyverse.org is _intentially_ out of sync with the source 
code under development. Other package developers and maintainers may differ on 
this, depending on their respective workflows.


```r
devtools::document()
```




----

[^1]: Which, yes, is totally one of the great reasons to sign up to do one, too!

[^pkgdown]: You can learn about it much greater detail on the [`pkgdown` site](https://hadley.github.io/pkgdown/index.html)...which was rendered using, wait for it: pkgdown.


