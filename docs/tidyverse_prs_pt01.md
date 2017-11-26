---
title: "PRs in the Tidyverse: Part 1"
author: "Mara Averick"
date: "2017-11-26"
output:
  html_document:
    keep_md: TRUE
---






## TL;DR

**The Big Picture**: Documentation should always be fixed at its source. For 
object documentation in the tidyverse, this means the `.R` files. 

From the GitHub repo, your workflow should be: `fork`, `clone`, `check`, 
(`branch`), `edit`, render using `document()`, `check`, `push`, `PR`.




```
Dear Sir or Madam Maintainer,

Whence I was reviewing your package documentation, I came upon a grammatical 
flaw in your prose. This was ever so troubling. 
I recommend you look at the fifth line down in the third paragraph of the...

Ever so humbly yours,

Rando
```

This document is an effort to avoid the scenario above. Though Yihui Xie's 
[You Do Not Need to Tell Me I Have A Typo in My Documentation](https://yihui.name/en/2013/06/fix-typo-in-documentation/) is right
on many levels. 

![Source: You Do Not Need to Tell Me I Have A Typo in My Documentation by Yihui Xie](/Users/maraaverick/pullreq/docs/imgs/yihui_typos.png)  


But, I've also been on the other side of the screen: seeing a typo, 
and wanting to fix it without hassling the maintainers, but have given up half-way
through because I didn't know _where_ to make the change it. I know the correct 
answer is _somewhere_ upstream, but I might not be exactly sure where. 
In some instances, this isn't the case— I know that a `README.md` file is the 
output of a `README.Rmd`, if it exists. But with package documentation 
(vignettes, pkgdown sites, etc) it's not as readily apparent.

![R Markdown README to Markdown README](/Users/maraaverick/pullreq/docs/imgs/readme_rmd_to_md.png)

You don't need to be an R neophyte for the above to be true.[^1]
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
 * [testthat](http://testthat.r-lib.org/)
 * [pkgdown](https://hadley.github.io/pkgdown/index.html)

If you already know all about those packages and how to use them, you probably 
don't need to be reading this. If you have heard those package names, and maybe
use devtools to install packages from non-CRAN sources, you're in the right place.
👍

## Where do typos come from?

Open scene: you're reading up on the `summarise.R` function in dplyr and notice 
that there's an error in subject-verb agreement in the following fragment, 
`"Data frames are the only backend that supports..."`. The subject 
(data _frames_) is plural, but the verb (_supports_) is singular.

You click on the GitHub icon, which takes you to the `dplyr` repo, but where are 
you supposed to make your edit? The page you were looking at was HTML. 
However, that HTML was the output of a different file. 

This means that, if you modify the HTML file, the correction would show up in 
the product, but not its source. The package maintainer would then have to 
either fix the source file by hand, or risk losing the change the next time the 
website output is rendered. The relationship is unidirectional.

### A detour into `pkgdown` 

Here is (almost) everything you need to know about pkgdown for the purposes of 
this guide: it makes static HTML sites for R packages. It's special because it 
leverages the pre-existing conventions for writing R documentation to render 
these sites, so the content only needs to be edited in one place.[^pkgdown]

![R to man/ to docs/](https://i.imgur.com/rI1zTnc.gif)

Different components of a pkgdown site come from different parts of an R 
package, some of which have already been rendered by 
[`roxygen2`](https://github.com/klutometis/roxygen). 

```
# Folders in the dplyr package directory

/dplyr
├── R
├── data
├── data-raw
├── docs
|  ├── articles
|  ├── news
|  └── reference
├── inst
|  ├── db
|  └── include
├── man
|  └── figures
├── revdep
├── src
├── tests
|  └── testthat
└── vignettes
   └── internals
```

I _highly_ recommend you take a quick look at Hilary Parker's 
[_Writing an R package from scratch_](https://hilaryparker.com/2014/04/29/writing-an-r-package-from-scratch/) 
as it covers the roxygen basics really well. (Heck, if you're not feeling up to 
the five/six minutes of reading, just skim **Step 4**).

### What about roxygen?

It's well worth your time to learn about roxygen2, as you'll need it if and/or 
when you decide to develop your _own_ package. But, big picture: it takes 
specially formatted comments from the R source codes and turns them into `.Rd` 
files for package documentation (i.e. the stuff that shows up in the viewer 
pane when you type in `?function`). Below are the comments for a function that 
adds two numbers (taken from Hadley Wickhams's [Generating Rd Files](https://cran.r-project.org/web/packages/roxygen2/vignettes/rd.html) 
vignette for roxygen2).


```r
#' Add together two numbers
#'
#' @param x A number
#' @param y A number
#' @return The sum of \code{x} and \code{y}
#' @examples
#' add(1, 1)
#' add(10, 1)
add <- function(x, y) {
  x + y
}
```

Function references are just one part of a pkgdown site/package documentation, 
but it's whats in our example. So...

![R to Rd](https://i.imgur.com/MzZiB2h.gif)

Both roxygen2- and pkgdown-generated documents will say `"do not edit..."` on 
the first or second line.

This is a not-so-subtle hint that you should (wait for it) _not_ edit those files!

For example, the typo _exists_ in `summarise.html`, but (even if you didn't know 
about pkgdown and roxygen) you'd never edit that because it says `do not edit by 
hand`. 

![dplyr summarise.html](/Users/maraaverick/pullreq/docs/imgs/summarise_html_combo.png)

### Over in the repo...

For _most_ function references there will be an file `function.R`. However, if 
go to the dplyr repo and to the `R/` folder, `summarise.R` isn't there! 

![summarise.R doesn't exist](/Users/maraaverick/pullreq/docs/imgs/no_summarise_r_file.png)

But, since GitHub repos are searchable, we can just look for our text that way.[^2] 
We've already covered `summarise.html` (namely, the fact that we won't be editing 
it), so it looks like `manip.R` is source file.

![dplyr repo search for "backend that supports"](/Users/maraaverick/pullreq/docs/imgs/repo_search_text.png)

## Your very own `dplyr`

Though you _could_ edit the `.R` file from GitHub's web interface, the change 
would only show up in one of three places (the `.R` file, but not in `.Rd`, or 
`.html`). So your next move will be to **fork** dplyr. Now, from *your* dplyr repo 
you'll get the proper URL so that you can **clone** the repo locally on your 
machine.

![Clone repo from your dplyr fork](/Users/maraaverick/pullreq/docs/imgs/clone_repo.png)

The subsequent steps will, for the most part, follow the [**Existing project, GitHub first**](http://happygitwithr.com/existing-github-first.html) chapter of Jenny 
Bryan's Happy Git with R. From **File** >> **New Project**, you can clone your 
copy of dplyr by putting the URL you copied from GitHub into the aptly-named 
**Repository URL** field. You should open this up in a new R session, so be sure 
to tick the appropriate radio button.

![New project from Git in RStudio](/Users/maraaverick/pullreq/docs/imgs/new_r_project_from_git.png)


### Branching and baseline

In addition to cloning the files in the package, you should now have two "new" 
tabs in your environment pane: **Build** and **Git**.

![RStudio before and after cloning repo](/Users/maraaverick/pullreq/docs/imgs/before_after_new_proj.png)

### Optional: branch out

In the **Git** tab, click on the icon with purple boxes to create a new 
**branch**. It's nice to give it an informative name, for example: `summarise-typo`. 

![Git branch icon](/Users/maraaverick/pullreq/docs/imgs/local_git_branch.png)


### CMD Check

Now you're ready to get your baseline using R CMD check. You can do this by 
clicking on the **Check** icon in the **Build** tab. Note that this may take a 
little while, which doesn't necessarily mean that anything's wrong. It's also 
likely that there are some parameters passed to `devtools::check()`, which is 
totally fine.

 + ![Check icon in RStudio Build pane](/Users/maraaverick/pullreq/docs/imgs/build_check_button.png)

It's worth taking a look at the arguments, if only to help you understand any 
errors or warnings returned. For example, the warnings re. missing vignettes 
aren't surprising, given `--no-build-vignettes` was passed as a `build_arg`.


```r
devtools::check(build_args = c('--no-build-vignettes'))
```

![dplyr R CMD check results](/Users/maraaverick/pullreq/docs/imgs/dplyr_cmd_check.png)

#### Frequently found failures

 * Missing packages. Solution? Install the packages.
 
 ![Missing suggested packages](/Users/maraaverick/pullreq/docs/imgs/dplyr_cmd_check_old.png)
 
 * Assorted things totally unrelated to what I'm trying to fix
 
   + Go back to the package repo and click on the `build` badge, this 
   will take you to Travis CI, where you can see the logs from the most recent 
   build of the package, including the output of `R CMD check`
   
   ![Travis CI R CMD check](/Users/maraaverick/pullreq/docs/imgs/travis_build_dplyr.png)
   
   + If you're fixing something in a function reference (like this example), try 
   running `devtools::check_man()` and see what happens. 
   
You're trying to establish a baseline to make sure that _you_ didn't break 
anything. So, if you find that you can make your edit without _changing_ the 
output of R CMD check,things are going well.
   
### Now you're ready to make your change!

Now you're ready to fix that typo... Remember, the `.R` file is the source,
so it's the only place we'll be fixing anything by hand. Once you've edited
`manip.R`, you'll run `devtools::document()`, which will create the downstream
files, including `summarise.Rd`.

You'll then reload your library.

![Command history from checking, editing, and rebuilding dplyr documentation](/Users/maraaverick/pullreq/docs/imgs/command_history.png)

That done, you can now run `devtools::check()` _again_, to ensure your changes
haven't introduced any new errors. Don't forget to keep the same `build_args`
from the first time around. You can even just click the **Check** icon.

![Check updated package](/Users/maraaverick/pullreq/docs/imgs/devtools_check.png)

#### n.b. outside of the Tidyverse YMMV

Because the majority of R users work with the CRAN version of a package, 
the documentation on tidyverse.org is _intentially_ out of sync with the source 
code under development. 


```r
devtools::document()
```




----

[^1]: True confession: I really didn't understand where those `do not edit by hand` files came from until I did an rOpenSci [package review](https://ropensci.org/blog/2017/08/22/first-package-review/) for Nick Tierney's 🌟 [`visdat`](http://visdat.njtierney.com/). Which, yes, is totally one of the great reasons to sign up to do one, too!

[^pkgdown]: You can learn about it much greater detail on the [`pkgdown` site](https://hadley.github.io/pkgdown/index.html)...which was rendered using, wait for it: pkgdown.

[^2]: The same text is also found in the `summarise.Rd` file, but `.Rd` files do not show up in GitHub searches by default.


