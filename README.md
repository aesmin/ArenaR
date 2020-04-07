# ArenaR - Data generator for Arena - interactive XAI dashboard

[![Codecov test coverage](https://codecov.io/gh/ModelOriented/ArenaR/branch/master/graph/badge.svg)](https://codecov.io/gh/ModelOriented/ArenaR?branch=master)
[![R build status](https://github.com/ModelOriented/ArenaR/workflows/R-CMD-check/badge.svg)](https://github.com/ModelOriented/ArenaR/actions)

## Examples generated using ArenaR
[Apartments from 2009-2010 price per m2](https://arena.drwhy.ai/?data=https://gist.githubusercontent.com/piotrpiatyszek/e90d62f8896637001b9110cbe143956f/raw/15a1c75488122580dc5766e3ca7474949ba89678/data.json)  
[FIFA 20 Players value](https://arena.drwhy.ai/?data=https://gist.githubusercontent.com/piotrpiatyszek/db055c7ba325c964b22e52dc87a0019f/raw/ce3687e1d6e595d792f84bca0f07dd216f161d75/data.json)  
[HR Status classification](https://arena.drwhy.ai/?data=https://gist.githubusercontent.com/piotrpiatyszek/42841017d32d89e1ca9ca0c89da94b88/raw/052bbed2b8a519e833663940225f16792cf337ca/data.json)

## How to use it

### Installation
```r
devtools::install_github("ModelOriented/ArenaR")
```

### Articles
[Create Live Arena](https://arenar.drwhy.ai/articles/articles/arena_live.html)  
[Create Static Arena](https://arenar.drwhy.ai/articles/articles/arena_static.html)  
[Using ArenaR with classificators](https://arenar.drwhy.ai/articles/articles/classification.html)  

### Live version - fast for testing on huge data frames
```r
library(arenar)
library(gbm)
library(DALEX)
library(dplyr)

# Create models and DALEX explainers
model_gbm100 <- gbm(m2.price ~ ., data = apartments, n.trees = 100)
expl_gbm100 <- explain(
  model_gbm100,
  data = apartments,
  y = apartments$m2.price,
  label = "gbm [100 trees]"
)

model_gbm500 <- gbm(m2.price ~ ., data = apartments, n.trees = 500)
expl_gbm500 <- explain(
  model_gbm500,
  data = apartments,
  y = apartments$m2.price,
  label = "gbm [500 trees]"
)

arena_new(live = TRUE) %>%
  # Pushing explainers for each models
  arena_push_model(expl_gbm100) %>%
  arena_push_model(expl_gbm500) %>%
  # Push dataframe of observations
  arena_push_observations(apartments) %>%
  # Run server of default port and ip
  arena_run()
```

### Generating static files - easy to share
```r
library(arenar)
library(gbm)
library(DALEX)
library(dplyr)

# Create models and DALEX explainers
model_gbm100 <- gbm(m2.price ~ ., data = apartments, n.trees = 100)
expl_gbm100 <- explain(
  model_gbm100,
  data = apartments,
  y = apartments$m2.price,
  label = "gbm [100 trees]"
)

model_gbm500 <- gbm(m2.price ~ ., data = apartments, n.trees = 500)
expl_gbm500 <- explain(
  model_gbm500,
  data = apartments,
  y = apartments$m2.price,
  label = "gbm [500 trees]"
)

# Take only few observations
observations <- apartments %>% filter(construction.year >= 2009)
# Observations' names are taken from rownames
rownames(observations) <- paste0(
  observations$district,
  " ",
  observations$surface,
  "m2 "
)

arena_new() %>%
  # Pushing explainers for each models
  arena_push_model(expl_gbm100) %>%
  arena_push_model(expl_gbm500) %>%
  # Push dataframe of observations
  arena_push_observations(observations) %>%
  # Upload calculated arena files to Gist and open Arena in browser
  arena_upload()
```
