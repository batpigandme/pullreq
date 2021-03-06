---
title: "PRs in the Tidyverse: Part 1"
author: "Mara Averick"
date: "2017-11-28"
output:
  html_document:
    keep_md: TRUE
---






## TL;DR

**The Big Picture**: Documentation should always be fixed at its source. For 
*function references* in the tidyverse, this means making changes in `.R` files. 

Starting from the GitHub repo of the target package (in this case, dplyr), this
will take you through the following workflow:

  * Fork <!--html_preserve--><i class="fa  fa-code-fork "></i><!--/html_preserve-->
  * Clone <!--html_preserve--><i class="fa  fa-clone "></i><!--/html_preserve-->
  * Check <!--html_preserve--><i class="fa  fa-check-circle-o "></i><!--/html_preserve-->
  * (Branch) <!--html_preserve--><i class="fa  fa-share-alt "></i><!--/html_preserve-->
  * Edit <!--html_preserve--><i class="fa  fa-pencil "></i><!--/html_preserve-->
  * Render (using `devtools::document()`) <!--html_preserve--><i class="fa  fa-file "></i><!--/html_preserve-->
  * Check <!--html_preserve--><i class="fa  fa-check-circle-o "></i><!--/html_preserve-->
  * Commit/Push <!--html_preserve--><i class="fa  fa-upload "></i><!--/html_preserve-->
  * Submit <!--html_preserve--><i class="fa  fa-star "></i><!--/html_preserve-->

## Preamble-y

```
Dear Sir or Madam Maintainer,

Whence I was reviewing your package documentation, I came upon a grammatical 
flaw in your prose. This was ever so troubling. 
I recommend you look at the fifth line down in the third paragraph of the...

Ever so humbly yours,

Rando
```

This document is an effort to avoid the scenario above. Yihui Xie's [You Do Not Need to Tell Me I Have A Typo in My Documentation](https://yihui.name/en/2013/06/fix-typo-in-documentation/) 
is right on many levels, but, if you're unfamiliar with the inner workings of an 
R package, this is easier said than done.

![Source: You Do Not Need to Tell Me I Have A Typo in My Documentation by Yihui Xie](/Users/maraaverick/pullreq/imgs/yihui_typos.png)  


In learning R, I've encountered this scenario several times: seeing a typo,
and wanting to fix it without hassling the maintainers, but giving up half-way
through because I wasn't sure _where_ to make the change(s). The correct answer
is _somewhere_ upstream. With the proliferation of R Markdown, many R neophytes
will know that a `README.md` file is the output of a `README.Rmd`, if it exists.
But with package documentation (vignettes, pkgdown sites, etc) this is not as
readily apparent.

![R Markdown README to Markdown README](/Users/maraaverick/pullreq/imgs/readme_rmd_to_md.png)

You don't need to be new to R for the circumstances described above to occur.[^1] 
No one wins in this scenario. Nobody wants typos, and making this kind of minor
fix is a great way to get familiar with the mechanics of making contributions.[^sweet_feats]

An alternate title to this could've been **Packages for people who don't
actually want to write packages**. Expect hand-waiving and references to magic,
invisible happenings, because this is a bare-minimum, forensic approach.

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
and this is intended as a complement those guides. This is meant to make
the existing [tidyverse contribution guidelines](https://www.tidyverse.org/contribute/), 
and [The tidyverse style guide](http://style.tidyverse.org/)
more accessible. It's also worth visiting Jim Hester's post, [Contributing code to the tidyverse](https://www.tidyverse.org/articles/2017/08/contributing/),
regardless of whether or not you plan to change a single letter, or help with
implementing a major modification.

In addition to the [tidyverse packages](https://www.tidyverse.org/packages/), 
you'll need:  

 * [roxygen2](https://github.com/klutometis/roxygen)
 * [devtools](https://github.com/hadley/devtools)
 * [testthat](http://testthat.r-lib.org/)
 * [pkgdown](https://hadley.github.io/pkgdown/index.html)

If you already know all about those packages and how to use them, you probably
don't need to be reading this. If you have heard those package names, and maybe
use devtools to install packages from non-CRAN sources, you're in the right
place.
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

### A detour into `pkgdown` & `roxygen2`

Here is (almost) everything you need to know about pkgdown for the purposes of 
this guide: it makes static HTML sites for R packages. It's special because it 
leverages the pre-existing conventions for writing R documentation to render 
these sites, so the content only needs to be edited in one place.[^pkgdown]

##### Image options...

![R to man/ to docs/](https://i.imgur.com/rI1zTnc.gif)

![roxygen R to man folders](https://i.imgur.com/hRKGQ80.gif)


![roxygen R to Rd files](https://i.imgur.com/qXq6jJP.gif)

#### End image options

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
as it covers the roxygen2 basics really well. (If you're not feeling up to 
the five/six minutes of reading, just skim **Step 4**).


Learning about roxygen2 will also benefit you later on, as you'll need it if
and/or when you decide to develop your _own_ package. But, big picture: it takes
specially formatted comments from the R source codes and turns them into `.Rd`
files for package documentation (i.e. the stuff that shows up in the viewer pane
when you type in `?function`). Below are the comments for a function that adds
two numbers (taken from Hadley Wickhams's [Generating Rd Files](https://cran.r-project.org/web/packages/roxygen2/vignettes/rd.html) 
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
but it's what's in our example.[^rstudio_roxygen]

![R to Rd](https://i.imgur.com/MzZiB2h.gif)

Both roxygen2- and pkgdown-generated documents will say `"do not edit..."` on 
the first or second line.

This is a not-so-subtle hint that you should (wait for it) _not_ edit those
files!

For example, the typo _exists_ in `summarise.html`, but (even if you didn't know 
about pkgdown and roxygen) you'd never edit that because it says `do not edit by 
hand`. 

![dplyr summarise.html](/Users/maraaverick/pullreq/imgs/summarise_html_combo.png)

### Over in the repo...

For _most_ function references there will be an file `function.R`. However, if 
go to the dplyr repo and to the `R/` folder, `summarise.R` isn't there! 

![summarise.R doesn't exist](/Users/maraaverick/pullreq/imgs/no_summarise_r_file.png)

But, since GitHub repos are searchable, we can just look for our text that way.[^2] 

![dplyr repo search for "backend that supports"](/Users/maraaverick/pullreq/imgs/repo_search_text.png)

We've already covered `summarise.html` (namely, the fact that we won't be
editing it), so it looks like `manip.R` is source file we're after.

!["...backend that supports" in `manip.R`](/Users/maraaverick/pullreq/imgs/manipr_top_bottom.png)

## Your very own `dplyr`

Though you _could_ edit the `.R` file from GitHub's web interface, the change 
would only show up in one of three places (the `.R` file, but not in `.Rd`, or 
`.html`). So your next move will be to **fork** dplyr. Now, from *your* dplyr repo 
you'll get the proper URL so that you can **clone** the repo locally on your 
machine.

![Clone repo from your dplyr fork](/Users/maraaverick/pullreq/imgs/clone_repo.png)

![Copy URL from _your_ fork to clone repo](/Users/maraaverick/pullreq/imgs/gh_clone_from_your_fork.png)

The subsequent steps will, for the most part, follow the [**Existing project, GitHub first**](http://happygitwithr.com/existing-github-first.html) chapter of Jenny 
Bryan's Happy Git with R. From **File** >> **New Project**, you can clone your 
copy of dplyr by putting the URL you copied from GitHub into the aptly-named 
**Repository URL** field. You should open this up in a new R session, so be sure 
to tick the appropriate radio button.

![New project from Git in RStudio](/Users/maraaverick/pullreq/imgs/new_r_project_from_git.png)


### Branching and baseline

In addition to to the cloned files, you should now have two "new" tabs in your
environment pane: **Build** and **Git**.

![RStudio before and after cloning repo](/Users/maraaverick/pullreq/imgs/before_after_new_proj.png)

#### Optional: branch out

In the **Git** tab, click on the icon with purple boxes to create a new 
**branch**. It's nice to give it an informative name, for example: `summarise-typo`. 

![Git branch icon](/Users/maraaverick/pullreq/imgs/local_git_branch.png)


### CMD Check

Now you're ready to establish your baseline using R CMD check. You can do this
by clicking on the **Check** icon in the **Build** tab. Note that this may take
a little while, which does _not_ necessarily mean that anything's wrong. It's
also possible that there are some parameters passed to `devtools::check()`,
which is fine.

![Check icon in RStudio Build pane](/Users/maraaverick/pullreq/imgs/build_check_button.png)

It's worth taking a look at the build arguments, if only to help you understand
any errors or warnings returned. For example, the warnings re. missing vignettes
aren't surprising, given `--no-build-vignettes` was passed as a `build_arg`.


```r
devtools::check(build_args = c('--no-build-vignettes'))
```

![dplyr R CMD check results](/Users/maraaverick/pullreq/imgs/dplyr_cmd_check.png)

#### Frequently found failures

 * Missing packages. 
    
    + Solution? Install the packages.
 
    ![Missing suggested packages](/Users/maraaverick/pullreq/imgs/dplyr_cmd_check_old.png)
 
 * Assorted things totally unrelated to what I'm trying to fix
 
   + Go back to the package repo and click on the `build` badge, this 
   will take you to Travis CI, where you can see the logs from the most recent 
   build of the package, including the output of `R CMD check`
   
   ![Travis CI R CMD check](/Users/maraaverick/pullreq/imgs/travis_build_dplyr.png)
   
   + If you're fixing something in a function reference (like this example), try 
   running `devtools::check_man()` and see what happens. 
   
   + Visit the package's GitHub repo, and see if there are any related Issues.
   
You're establishing a baseline to make sure that _you_ didn't break 
anything. So, if you find that you can make your edit without _changing_ the 
output of R CMD check, everything is 🆗.
   
### Time to make your change!

Now you're ready to fix that typo... Remember, the `.R` file is the source,
so it's the only place we'll be fixing anything by hand. Once you've edited
`manip.R`, you'll run `devtools::document()`, which will create downstream
documentation files, including `summarise.Rd`.

You'll then reload your library.

![Command history from checking, editing, and rebuilding dplyr documentation](/Users/maraaverick/pullreq/imgs/command_history.png)

That done, you can now run `devtools::check()` _again_, to ensure your changes
haven't introduced any new errors. Don't forget to keep the same `build_args`
from the first time around. You can even just click the **Check** icon.

![Run `devtools::check()`](/Users/maraaverick/pullreq/imgs/devtools_check.png)

As you can see (below), there are still two warnings after running `check`, but,
since these are the _same ones_ established at baseline, we're good to go.

![Check warnings on updated package](/Users/maraaverick/pullreq/imgs/R_CMD_check_success.png)

#### n.b. outside of the Tidyverse YMMV

Because the majority of R users work with the CRAN version of a package, the
documentation on tidyverse.org is _intentially_ out of sync with the source code
under development.

Other maintainers may prefer `pkgdown::build_site()`, so it's worth asking if
it's not clear based on a repo or project's contributing guidelines.

### Git going

Now you can navigate to the appropriate **Git** UI either by using the Git
pulldown menu and selecting **Commit** or by clicking on either the **Diff**
or **Commit** buttons in the Git tab in your environment pane. You can also use
keyboard shortcuts to take you to the same place.[^shortcuts]

![RStudio Version Control Keyboard Shortcuts](/Users/maraaverick/pullreq/imgs/rstudio_git_shortcuts.png)

You will then see an interface that includes all of the files you have added,
deleted, or edited; as well as a space in which you can write your Git commit
message. The art of the [perfect commit message](https://wiki.openstack.org/
wiki/GitCommitMessages) is heavily documented, but, for now, just focus on
something short (fewer than 50 characters) that starts with a verb and describes
what you did. If you need to add more details, there will be space for that when
you make your pull request.

![Git diff and commit pane in RStudio](/Users/maraaverick/pullreq/imgs/rstudio_git_diff.png)

Once you're happy with your message, and have made sure that you've only changed
the files you intended to, go ahead and hit the **Commit** button.

![Git commit from RStudio](/Users/maraaverick/pullreq/imgs/rstudio_git_commit.png)

And, finally, you'll be ready to `git push`.

![Git push from RStudio](/Users/maraaverick/pullreq/imgs/git_push.png)


### Readying your Pull Request

Time to go back to your GitHub <!--html_preserve--><i class="fa  fa-github "></i><!--/html_preserve--> repo and make sure
everything is as it should be.
Click on **Compare** to view the changes you've made— these should be identical
to the changes you saw in the Git diff view in RStudio.

![GitHub Compare](/Users/maraaverick/pullreq/imgs/github_compare_button.png)

Depending on whether or not you used a branching workflow, you can look at the
split view to see changes as compared to either your master branch, or to the
`tidyverse/dplyr` master branch.

![GitHub split-view diff](/Users/maraaverick/pullreq/imgs/github_splitview_diff.png)

You can get to a similar interface by clicking on **Compare & pull request**.

![GitHub Compare & pull request](/Users/maraaverick/pullreq/imgs/git_patch_ui.png)

Once you're satisfied that your changes are as they should be, you're ready
to submit your pull request to the master repo (in this case, [**tidyverse/dplyr**](https://github.com/tidyverse/dplyr)). Since this PR is a 
simple typo fix, the pull-request title will probably suffice for what you need 
to say.[^gd_making_a_pr] Make sure that the appropriate base and head forks are
selected before submitting your PR.

![Selecting a base fork and a head fork when submitting a pull request on GitHub](/Users/maraaverick/pullreq/imgs/base_fork_head_fork_pr.png)

### Pull has been requested

It's official, your pull request has been submitted. 🎉

![dplyr open pull requests](/Users/maraaverick/pullreq/imgs/dplyr_open_prs.png)

Now what? Well, it may take some time for your PR to be reviewed. Tidyverse
projects tend to be developed in waves, and you shouldn't interpret any lag time
in feedback or merging as a lack of appreciation. Documentation copy-edits are
valuable, and are hard to see when you've written or read something too many
times. So, your fresh eyes are invaluable.

### Errata

#### What are the little icons next to the pull requests?

The three icons (green check, red x, and yellow circle) indicate the build
status of the repository with the changes you've made from [Travis CI](https://travis-ci.org/) and/or [AppVeyor](https://www.appveyor.com/).

![Pull request icons indicating build status](/Users/maraaverick/pullreq/imgs/build_status.png)

For the most part, you do not need to worry about these if you've made small
changes. If you're curious about what they mean, you _can_ click on the icon and
figure out which, if any, builds failed.

![GitHub pull request build status](/Users/maraaverick/pullreq/imgs/pr_build_status.png)


[^1]: True confession: I really didn't understand where those `do not edit
by hand` files came from until I did an rOpenSci [package review](https://
ropensci.org/blog/2017/08/22/first-package-review/) for Nick Tierney's
🌟 [`visdat`](http://visdat.njtierney.com/). Which,
yes, is totally one of the great reasons to sign up to do one, too!

[^pkgdown]: You can learn about it much greater detail on the [`pkgdown` site](https://hadley.github.io/pkgdown/index.html)...which was rendered using, wait
for it: pkgdown.

[^2]: The same text is also found in the `summarise.Rd` file, but `.Rd` files do
not show up in GitHub searches by default.

[^shortcuts]: There are also some useful keyboard shortcuts you can use. Check
out the [RStudio IDE Cheat Sheet](https://github.com/rstudio/cheatsheets/raw/master/rstudio-ide.pdf) or go to the **Tools** menu, and select **Keyboard Shortcuts Help** for a helpful list.

[^gd_making_a_pr]: For more substantive changes, and/or patches fixing an issue
in the repo, I recommend reading [**Making a pull request**](https://github.com/tidyverse/googledrive/blob/master/CONTRIBUTING.md#making-a-pull-request) in the
[googledrive](http://googledrive.tidyverse.org/) package's [`CONTRIBUTING.md`](https://github.com/tidyverse/googledrive/blob/master/CONTRIBUTING.md).

[^rstudio_roxygen]: For a brief, visual overview of some of the RStudio-roxygen2 
integration features, see [Writing Package Documentation](https://support.rstudio.com/hc/en-us/articles/200532317-Writing-Package-Documentation) from RStudio Support.

[^sweet_feats]: Plus, I'd way rather have package maintainers working on those
sweet feature requests I submitted than hunting around for a renegade letter or
two in the function reference.
