
[![Build
Status](https://travis-ci.org/chiyahn/R-practice-repository.svg?branch=master)](https://travis-ci.org/chiyahn/R-practice-repository)

### Useful links:
- R package primer: contains a minimal tutorial on R package build workflow, without CI setups. https://kbroman.org/pkg_primer/

## Package Metadata
Once the first package file is created, fill in `DESCRIPTION` to add the package title, author, maintainer information, description, and license.
### Add project dependency
If the package requires methods or dataset from other packages, one can add `Imports` field. For instance, to add `gapminder` as a required package, one can add
```r
Imports: 
    gapminder
```

at the end of `DESCRIPTION` file. Note that this can be also done by running
```r
usethis::use_package("gapminder")
```
in R console. Completing `DESCRIPTION` will give something like 5ae4132. Note that this barely adds a package requirement; to use dependent packages within the package without calling `library`, `NAMESPACE` has to be modified. To do so, add the following line
```r
import(gapminder)
```
at the end of `NAMESPACE`. This will remove the burden of using `gapminder::` to call any method or dataset from `gapminder`.

## Adding functions
All the methods to be used in the package should be contained in `R` directory. Here, I am adding the following function called `getavg` that returns the average of a given numeric vector saved in `getavg.R`:
```r
#' Returns the average of a given numeric vector.
#'
#' @param v A vector of numerics
#'
#' @return The average of v
#'
#' @examples
#' getavg(c(1,3)) # returns (1+3)/2 = 2
#' getavg(c(1,2,3)) # returns (1+2+3)/3 = 2
getavg <- function (v) {
  return (mean(v))
}
```
The comments above the actual method definition are something called *roxygen comments* that can be used to build package manuals using `roxygen`. This is not needed unless you want to create an R package ready for CRAN submission or extra clarity.

Adding `R/getavg.R` will give something like 4f6353a.



## Implementing unit tests
### Sniffing the code
To do some sanity check before actual unit tests, one can load the package in current R session by running
```r
devtools::load_all(".")
```
In RStudio, this can be done by clicking on `Build -> Load All`. Once the package is loaded, `getavg` can be called as the following:
```r
getavg(c(2,4)) # should return 3
```

### Adding `testthat`
In R, unit tests can be performed by the package `testthat`. To set up the package to use `testthat`, run the following:
```r
usethis::use_testthat()
```
which will add `tests/` directory with files for unit tests and `Suggests` feature in `DESCRIPTION` file. It will look something like 7796e22.

### Adding unit tests
All unit tests must be contained in `tests/testthat/`. Usual naming convention is that the unit test file has the name of `test-METHOD_NAME` where `METHOD_NAME` is the method you want to test.

Save the following unit tests as `tests/testthat/test-getavg.R`:
```r
context("getavg")

test_that("check if getavg returns average values", {
  expect_equal(getavg(c(1)), 1) # 1/1 = 1
  expect_equal(getavg(c(1,3)), 2) # (1+3)/2 = 2

  expect_warning(getavg(NULL)) # getavg(NULL) should return a warning
})
```
Once the unit tests are added, it will something like 7d866f0.

### Running unit tests
To run the unit tests, execute the following:
```r
devtools::test()
```
In RStudio, this can be done by clicking on `Build -> Test Package`.



## Using CI Travis
Save the following text file as `.travis.yml`:

```
# R for travis: see documentation at https://docs.travis-ci.com/user/languages/r

language: R
sudo: false
cache: packages
```

Activating the repo from Travis CI will complete implementation. Add the following line in `README.md` to add a badge for build status:

```

[![Build
Status](https://travis-ci.org/YOUR_USERNAME/REPO_NAME.svg?branch=master)](https://travis-ci.org/YOUR_USERNAME/REPO_NAME)
```
where `YOUR_USERNAME` is your username and `REPO_NAME` is the name of repository in Github.




## (Optional, for package generation) Documentation with `Roxygen2`
