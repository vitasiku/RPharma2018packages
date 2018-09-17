# RPharma2018packages
Collection of favorite packages from RPharma conf

**This is a packrat project** Please use packrat to work here

## Package list

- [Packrat](#packrat) - package dependency manager
- [Archivist](#archivist) - Session manager
- [logR](#logr) - Extended logging solution
- [Diffdf](#diffdf) - data.frame comparison
- Yardstick - linear model 
- RInno - Shiny Apps as Windows Applications

## Packrat

Packrat allows you to store all packages you are using for a certain session/project. Packrat is also
used for this project to store the packages shown here. The main guide for packrat can be found
at the [RStudio blog describing](https://rstudio.github.io/packrat/commands.html) it

```
install.packages("packrat")
packrat::unbundle(“packrat/bundles/…..tar.gz”)

```

## Archivist

an R package designed to improve the management of results
of data analysis. Key functionalities of this package include: 

(i) management of local
and remote repositories which contain R objects and their meta-data (objects’ properties
and relations between them);

(ii) archiving R objects to repositories;

(iii) sharing and
retrieving objects (and it’s pedigree) by their unique hooks;

(iv) searching for objects
with specific properties or relations to other objects;

(v) verification of object’s identity
and context of it’s creation.

### Example for task (ii) - restore models

This example gives a list of models stored inside the package

```
library(archivist)
models <- asearch("pbiecek/graphGallery", patterns = "class:lm")
modelsBIC <- sapply(models, BIC)

sort(modelsBIC)
```

### Example for task (i) - store objects locally

A data.frame comes with this repository and is stored in the `arepo` folder. Your task is
to create a new data.frame, store it in the `arepo_new` folder and add it to the 
restored data.frame. If everything works out the sum of the data.frames shows up
to be 2 for position (1,1).

```
library(archivist)

repo <- "arepo_new"
createLocalRepo(repoDir = repo, default = TRUE)

df <- data.frame(x=c(1,2),y=c(2,3))
saveToRepo(df)

setLocalRepo("arepo")
df2 <- loadFromLocalRepo("4a7369a8c51cb1e7efda0b46dad8195e",value = TRUE)

df_test <- df + df2

print(df_test[1,1]==2)

```

## logR

### Prequisites

First you need to install PostGres via

**Linux**

if you have docker then: docker run --rm -p 127.0.0.1:5432:5432 -e POSTGRES_PASSWORD=postgres --name pg-logr postgres:9.5

**Windows**

https://www.enterprisedb.com/downloads/postgres-postgresql-downloads

install setting password to `postgres` and start via:

C:\Program Files\PostgreSQL\9.5\bin\postgres.exe

### Example 1

```
# create logr table
logR_schema()

# make some logging and calls

logR(1+2) # OK
#[1] 3
logR(log(-1)) # warning
#[1] NaN
f = function() stop("an error")
logR(r <- f()) # stop
#NULL
g = function(n) data.frame(a=sample(letters, n, TRUE))
logR(df <- g(4)) # out rows
#  a
#1 u
#2 c
#3 w
#4 p

# try CTRL+C / 'stop' button to interrupt
logR(Sys.sleep(5))

# wrapper to: dbReadTable(conn = getOption("logR.conn"), name = "logr")
logR_dump()
```

## diffdf

diffdf is a powerful package by colleges from Roche to compare two data.frames

It not only compares the values inside the data.frames but also labels of rows
and columns.

The output is upto now given as pretty human readable.

```
library(diffdf)
iris2 <- iris
for (i in 1:3) iris2[i,i] <- i^2
iris2$new_var <- "hello"
class(iris2$Species) <- "some class"
diffdf(iris, iris2)

```

## Yardstick

yardstick is a package to estimate how well models are working using tidy data principles. The package webpage is https://tidymodels.github.io/yardstick/ for more information.

```
library(yardstick)
library(dplyr)

head(two_class_example)
```

```
##    truth  Class1   Class2 predicted
## 1 Class2 0.00359 0.996411    Class2
## 2 Class1 0.67862 0.321379    Class1
## 3 Class2 0.11089 0.889106    Class2
## 4 Class1 0.73516 0.264838    Class1
## 5 Class2 0.01624 0.983760    Class2
## 6 Class1 0.99928 0.000725    Class1
```

You can use a dplyr-like syntax to compute common performance characteristics of the model and get them back in a data frame:

```
metrics(two_class_example, truth, predicted)
```

```
## # A tibble: 1 x 1
##   accuracy
##      <dbl>
## 1    0.838
```
or

```
two_class_example %>% rmse(Class1, Class2)
```

```
[1] 0.8317232
```

## RInno