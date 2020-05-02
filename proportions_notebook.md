This lecture was taken from Statistical Analysis and Reporting in R,
Jacob O. Wobbrock, Ph.D. available here:
<a href="http://depts.washington.edu/acelab/proj/Rstats/index.html" class="uri">http://depts.washington.edu/acelab/proj/Rstats/index.html</a>

Proportions.R (Tests of Proportion & Association)

One Sample

#### Binomial test

When to use this test?

\*Notes taken from
here:<a href="https://www.youtube.com/watch?v=WOoS7nVkfDk" class="uri">https://www.youtube.com/watch?v=WOoS7nVkfDk</a>
by Matthew E.Clapham

*Type of Data*: Categorial, only have two categories in a single sample

*Purpose*: To test if Category has an expected count(Goodness of fit).

The *goodness of fit* of a statistical model describes how well it fits
a set of observations. Measures of goodness of fit typically summarize
the discrepancy between observed values and the values expected under
the model in question.

    knitr::opts_chunk$set(echo = TRUE)
    # pull in the csv
    df <- read.csv("data/Proportions/0F0LBs_binomial.csv")
    df

    ##     S Y
    ## 1   1 y
    ## 2   2 y
    ## 3   3 x
    ## 4   4 y
    ## 5   5 y
    ## 6   6 x
    ## 7   7 y
    ## 8   8 x
    ## 9   9 y
    ## 10 10 y
    ## 11 11 x
    ## 12 12 y
    ## 13 13 y
    ## 14 14 y
    ## 15 15 y
    ## 16 16 x
    ## 17 17 x
    ## 18 18 y
    ## 19 19 y
    ## 20 20 x
    ## 21 21 y
    ## 22 22 x
    ## 23 23 y
    ## 24 24 y
    ## 25 25 y
    ## 26 26 y
    ## 27 27 y
    ## 28 28 y
    ## 29 29 y
    ## 30 30 y
    ## 31 31 y
    ## 32 32 y
    ## 33 33 x
    ## 34 34 x
    ## 35 35 y
    ## 36 36 y
    ## 37 37 y
    ## 38 38 y
    ## 39 39 y
    ## 40 40 x
    ## 41 41 x
    ## 42 42 y
    ## 43 43 y
    ## 44 44 x
    ## 45 45 y
    ## 46 46 x
    ## 47 47 x
    ## 48 48 x
    ## 49 49 y
    ## 50 50 y
    ## 51 51 x
    ## 52 52 y
    ## 53 53 y
    ## 54 54 y
    ## 55 55 x
    ## 56 56 y
    ## 57 57 y
    ## 58 58 y
    ## 59 59 x
    ## 60 60 y

##### Create factors

Subject id is nominal and Y is categorical

    df$S = factor(df$S) # Subject id is nominal (unused)
    df$Y = factor(df$Y) # Y is an outcome of exactly two categories

Column Y is made up of two values: y, x

    xt = xtabs( ~ Y, data=df) # make counts

19, 41

binom.test

Exact Binomial Test Performs an exact test of a simple null hypothesis
about the probability of success in a Bernoulli experiment.
<a href="https://www.rdocumentation.org/packages/stats/versions/3.1.1/topics/binom.test" class="uri">https://www.rdocumentation.org/packages/stats/versions/3.1.1/topics/binom.test</a>

    binom.test(xt, p=0.5, alternative="two.sided")

    ## 
    ##  Exact binomial test
    ## 
    ## data:  xt
    ## number of successes = 19, number of trials = 60, p-value = 0.006218
    ## alternative hypothesis: true probability of success is not equal to 0.5
    ## 95 percent confidence interval:
    ##  0.2025755 0.4495597
    ## sample estimates:
    ## probability of success 
    ##              0.3166667

#### Multinomial test

*Type of Data*: Categorial, 3+ categories in a single sample

*Purpose*: To test if Categories match with expected count(Goodness of
fit).

We will use the XNomial library
<a href="https://cran.r-project.org/web/packages/XNomial/index.html" class="uri">https://cran.r-project.org/web/packages/XNomial/index.html</a>

    # df is a long-format data table w/columns for subject (S) and N-category outcome (Y)
    if (!require("XNomial")) install.packages("XNomial");  

    ## Loading required package: XNomial

    library(XNomial) # for xmulti

    df_multi <- read.csv("data/Proportions/0F0LBs_multinomial.csv")

    df_multi$S = factor(df$S) # Subject id is nominal (unused)
    df$Y = factor(df$Y) # Y is an outcome of ≥2 categories

    xt = xtabs( ~ Y, data=df) # make counts

    xmulti(xt, rep(1/length(xt), length(xt)), statName="Prob")

    ## 
    ## P value (Prob) = 0.006218
