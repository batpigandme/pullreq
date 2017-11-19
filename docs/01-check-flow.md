---
title: "Check flow"
author: "Mara Averick"
date: "11/16/2017"
output: 
  html_document:
    keep_md: TRUE
---



## Scenario

Fixing documentation (e.g. typos) seems _to me_ to be the lowest hanging 
fruit/most likely scenario for someone's first PR in the tidyverse.

## Outline

This PR flow starts with the following four steps:

 1.  Fork
 2.  Clone
 3.  R CMD check (`devtools::check()`)
 4.  Branch
 
## Fork & Clone

Universal.

## Check (aka Baseline)

Per [googledrive CONTRIBUTING.md](https://github.com/tidyverse/googledrive/blob/master/CONTRIBUTING.md)

> Make sure the package still passes R CMD check locally for you. 
> It's a good idea to do that before you touch anything, so you have a baseline.

### FAIL 1: `R CMD check` ❌

 * e.g. me with dplyr, today
 
 ![](/Users/maraaverick/pullreq/img/dplyr_cmd_check.png)
 
 * Not surprising that vignettes that weren't built were not found. That said, 
   there should be a way for contributor to determine whether or not this is an
   OK baseline.
   
     + Make sure that the errors at baseline are the same before and after you 
       make your modification
    
     + Install missing packages
     
     + Build missing vignettes

### `R CMD check  succeeded`

  * e.g. 
