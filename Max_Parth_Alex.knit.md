---
title: "Untitled"
author: "Max Anthenelli"
date: "2024-02-14"
output: pdf_document
---


The primary research questions are as follows:
1. Do asthmatics have steeper rates of decline (slope) or lower levels of lung function
(FEV1) than non-asthmatics, independent of smoking history?
regress fev on time for non-asthmatics


```r
tbl_regression(lm(fev~year +year:asthma, data = family_asthma_long), estimate_fun = partial(style_ratio, digits = 3))
```

```
## Table printed with `knitr::kable()`, not {gt}. Learn why at
## https://www.danieldsjoberg.com/gtsummary/articles/rmarkdown.html
## To suppress this message, include `message = FALSE` in code chunk header.
```



|**Characteristic** | **Beta** |  **95% CI**   | **p-value** |
|:------------------|:--------:|:-------------:|:-----------:|
|year               |  0.004   | -0.002, 0.009 |     0.2     |
|year * ever asthma |  -0.005  | -0.015, 0.005 |     0.3     |


2. Do asthmatic smokers have steeper rates of decline or lower levels of lung function
than non-asthmatic smokers? Here you can explore three options 1) using the fixed effect
variable ever smoke, 2) using the time dependent variable for smoking, or 3) using the
current number of cigarettes per day. When using this I would suggest making it a
categorical variable using quartiles etc.



```r
var_label(family_asthma_long$smoke_ever) <- "Ever Smoked"
tbl_regression(lm(fev ~ year + year:asthma + smoke_ever,
                  data = family_asthma_long),
               estimate_fun = partial(style_ratio, digits = 3))
```

```
## Table printed with `knitr::kable()`, not {gt}. Learn why at
## https://www.danieldsjoberg.com/gtsummary/articles/rmarkdown.html
## To suppress this message, include `message = FALSE` in code chunk header.
```



|**Characteristic** | **Beta** |  **95% CI**   | **p-value** |
|:------------------|:--------:|:-------------:|:-----------:|
|year               |  0.004   | -0.001, 0.009 |    0.15     |
|Ever Smoked        |  0.256   | 0.216, 0.296  |   <0.001    |
|year * ever asthma |  -0.005  | -0.015, 0.005 |     0.3     |

```r
tbl_regression(lm(fev ~ year + year:asthma + smk,
                  data = family_asthma_long),
               estimate_fun = partial(style_ratio, digits = 3))
```

```
## Table printed with `knitr::kable()`, not {gt}. Learn why at
## https://www.danieldsjoberg.com/gtsummary/articles/rmarkdown.html
## To suppress this message, include `message = FALSE` in code chunk header.
```



|**Characteristic**        | **Beta** |   **95% CI**   | **p-value** |
|:-------------------------|:--------:|:--------------:|:-----------:|
|year                      |  0.005   | -0.001, 0.010  |    0.087    |
|smoking status, initial q |  -0.136  | -0.160, -0.112 |   <0.001    |
|year * ever asthma        |  -0.006  | -0.016, 0.004  |     0.2     |

```r
tbl_regression(lm(fev ~ year + year:asthma + cncig,
                  data = family_asthma_long),
               estimate_fun = partial(style_ratio, digits = 3))
```

```
## Table printed with `knitr::kable()`, not {gt}. Learn why at
## https://www.danieldsjoberg.com/gtsummary/articles/rmarkdown.html
## To suppress this message, include `message = FALSE` in code chunk header.
```



|**Characteristic** | **Beta** |  **95% CI**   | **p-value** |
|:------------------|:--------:|:-------------:|:-----------:|
|year               |  0.011   | 0.005, 0.017  |   <0.001    |
|cncig              |  0.010   | 0.008, 0.012  |   <0.001    |
|year * ever asthma |  -0.006  | -0.017, 0.004 |     0.2     |

```r
family_asthma %>% count(cncig1, smkever) %>% filter(smkever==0)
```

```
## # A tibble: 2 x 3
##   cncig1 smkever     n
##    <dbl>   <dbl> <int>
## 1  0.200       0     1
## 2 NA           0  1135
```

Hints:
1. When random effects are nested, the order in which they are listed is important. The
order in which they are specified (from left to right) is significant – xtmixed assumes that
the second factor is nested in the first. In this data subjects (id) are nested within
Families (family).
2. Subjects should be tested for a RCM with age but families should not, any adjustment
for serial correlation with time done at the subject level would also remove it at the
family level.
