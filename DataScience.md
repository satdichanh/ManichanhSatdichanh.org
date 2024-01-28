---
title: "Structural Equation Modeling (SEM)"
output: html_document
date: "2024-01-27"
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Manichanh Satdichanh, PhD

Here I am presenting an example of Structural Equation Modeling (SEM) in R based on Generalized Least Squares Models by using a dataset from:

Grace, J. B., & Keeley, J. E. (2006). A structural equation model analysis of postfire plant diversity in California shrublands. Ecological Applications, 16(2), 503–514. [https://doi.org/10.1890/1051-0761(2006)016[0503:ASEMAO]2.0.CO;2](https://doi.org/10.1890/1051-0761(2006)016%5B0503:ASEMAO%5D2.0.CO;2){.uri}


### Fitting SEM using piecewiseSEM package

```{r}
#here we will be using two packages piecewiseSEM and nlme
library(piecewiseSEM)

#getting keeley dataset from piecewiseSEM package

data("keeley")
head(keeley)
summary(keeley)
```

```{r }
# fiting structural equation modeling using piecewiseSEM

library(nlme)
model1 = psem (
  gls (rich ~ elev + abiotic + age + firesev + cover, data = keeley),
  gls (abiotic ~ elev + age + firesev + cover, data = keeley),
  gls (cover ~ age + firesev + hetero, data = keeley),
  data = keeley
  )

summary (model1)
```


```{r }
# fiting structural equation modeling using piecewiseSEM

model2 = psem (
  gls (rich ~ elev + abiotic + age + firesev + cover, data = keeley),
  gls (abiotic ~ elev + age + firesev + cover, data = keeley),
  gls (cover ~ age + firesev + elev, data = keeley),
  data = keeley
)

summary(model2)
```


### Fitting SEM using lavaan package


``` {r }

library(lavaan)
library(lavaanPlot)
library(piecewiseSEM)

data("keeley")
fit =  '
      rich ~ elev + abiotic + age + firesev + cover
      abiotic ~ elev + age + firesev + cover
      cover ~ age + firesev + elev'

lavan1 = sem (fit, data = keeley ) #estimator: ML

summary(lavan1, fit.measures = TRUE, standardized = TRUE) # Std.all: standardized regression coefficients

fitMeasures(lavan1, c("cfi", "rmsea", "srmr", "GFI")) #cfi and GFI > 0.95; rmsea and srmr < 0.08

modificationindices(lavan1, minimum.value = 5) #this is to check if your model need to be modifies

lavaanPlot(model = lavan1, coefs = TRUE, stand = TRUE, 
           sig = 0.05) #standardized regression paths, showing only paths with p<= .05


```



