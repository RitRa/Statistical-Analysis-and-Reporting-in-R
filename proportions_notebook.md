-   [Binomial test](#binomial-test)
-   [Multinomial test](#multinomial-test)
-   [Chi-squared test](#chi-squared-test)
-   [Fisher’s exact test](#fishers-exact-test)
-   [G-test](#g-test)

This code was taken from Statistical Analysis and Reporting in R, Jacob
O. Wobbrock, Ph.D. available here:
<a href="http://depts.washington.edu/acelab/proj/Rstats/index.html" class="uri">http://depts.washington.edu/acelab/proj/Rstats/index.html</a>

Proportions.R (Tests of Proportion & Association)

One Sample

### Binomial test

When to use this test?

\*Notes taken from
here:<a href="https://www.youtube.com/watch?v=WOoS7nVkfDk" class="uri">https://www.youtube.com/watch?v=WOoS7nVkfDk</a>
by Matthew E.Clapham

**Type of Data**: Categorial, only have two categories in a single
sample

**Purpose**: To test if Category has an expected count(Goodness of fit).

The **goodness of fit** of a statistical model describes how well it
fits a set of observations. Measures of goodness of fit typically
summarize the discrepancy between observed values and the values
expected under the model in question.

    # pull in the csv
    df <- read.csv("data/Proportions/0F0LBs_binomial.csv")
    head(df)

    ##   S Y
    ## 1 1 y
    ## 2 2 y
    ## 3 3 x
    ## 4 4 y
    ## 5 5 y
    ## 6 6 x

##### Create factors

Subject id is nominal and Y is categorical

    df$S = factor(df$S) # Subject id is nominal (unused)
    df$Y = factor(df$Y) # Y is an outcome of exactly two categories

Column Y is made up of two values: y, x

    xt = xtabs( ~ Y, data=df) # make counts

19, 41

binom.test

**Exact Binomial Test** Performs an exact test of a simple null
hypothesis about the probability of success in a Bernoulli experiment.
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

### Multinomial test

**Type of Data**: Categorial, 3+ categories in a single sample

**Purpose**: To test if Categories match with expected count(Goodness of
fit).

We will use the XNomial library
<a href="https://cran.r-project.org/web/packages/XNomial/index.html" class="uri">https://cran.r-project.org/web/packages/XNomial/index.html</a>

    # df is a long-format data table w/columns for subject (S) and N-category outcome (Y)
    if (!require("XNomial")) install.packages("XNomial");  

    ## Loading required package: XNomial

    library(XNomial) # for xmulti

    df_multi <- read.csv("data/Proportions/0F0LBs_multinomial.csv")
    head(df_multi) 

    ##   S Y
    ## 1 1 z
    ## 2 2 z
    ## 3 3 z
    ## 4 4 x
    ## 5 5 z
    ## 6 6 z

    df_multi$S = factor(df_multi$S) # Subject id is nominal (unused)
    df_multi$Y = factor(df_multi$Y) # Y is an outcome of ≥2 categories
    xt = xtabs( ~ Y, data=df_multi) # make counts
    xt # 3 categorical variables

    ## Y
    ##  x  y  z 
    ## 17  8 35

    xmulti(xt, rep(1/length(xt), length(xt)), statName="Prob")

    ## 
    ## P value (Prob) = 8.756e-05

    # the following gives the equivalent result
    if (!require("RVAideMemoire")) install.packages("RVAideMemoire");  

    ## Loading required package: RVAideMemoire

    ## *** Package RVAideMemoire v 0.9-75 ***

    library(RVAideMemoire) # for multinomial.test

    multinomial.test(df_multi$Y)

    ## 
    ##  Exact multinomial test
    ## 
    ## data:  $(df_multi,Y)
    ## p-value = 8.756e-05

Multinomial post hoc test

    # xt is a table of counts for each category of Y
    library(RVAideMemoire) # for multinomial.multcomp
    multinomial.multcomp(xt, p.method="holm") # xt shows levels

    ## 
    ##  Pairwise comparisons using exact binomial tests 
    ## 
    ## data:  xt 
    ## 
    ##    8       17     
    ## 17 0.10775 -      
    ## 35 0.00013 0.03507
    ## 
    ## P value adjustment method: holm

### Chi-squared test

\*notes taken from
<a href="http://www.sthda.com/english/wiki/chi-square-goodness-of-fit-test-in-r" class="uri">http://www.sthda.com/english/wiki/chi-square-goodness-of-fit-test-in-r</a>

The **chi-square goodness of fit test** is used to compare the observed
distribution to an expected distribution, in a situation where we have
two or more categories in a discrete data. In other words, it compares
multiple observed proportions to expected probabilities.

    ## One-Sample Chi-Squared test
    # df is a long-format data table w/columns for subject (S) and N-category outcome (Y)
    df <- read.csv("data/Proportions/0F0LBs_multinomial.csv")
    head(df)

    ##   S Y
    ## 1 1 z
    ## 2 2 z
    ## 3 3 z
    ## 4 4 x
    ## 5 5 z
    ## 6 6 z

    df$S = factor(df$S) # Subject id is nominal (unused)
    df$Y = factor(df$Y) # Y is an outcome from two or more categories
    xt = xtabs( ~ Y, data=df) # make counts
    xt

    ## Y
    ##  x  y  z 
    ## 17  8 35

    result <-chisq.test(xt)
    result 

    ## 
    ##  Chi-squared test for given probabilities
    ## 
    ## data:  xt
    ## X-squared = 18.9, df = 2, p-value = 7.869e-05

The p-value of the test is 7.869e-05, which is less than the
significiance level alpha =0.05. We can conclude that the outcomes are
not evenly distributed p-value of 7.869e-05.

    # access of the expected value
    result$expected

    ##  x  y  z 
    ## 20 20 20

    # access of the expected value
    result$p.value

    ## [1] 7.868957e-05

    ## Chi-Squared post hoc test
    # xt is a table of counts for each category of Y
    library(RVAideMemoire) # for chisq.multcomp
    chisq.multcomp(xt, p.method="holm") # xt shows levels

    ## 
    ##  Pairwise comparisons using chi-squared tests 
    ## 
    ## data:  xt 
    ## 
    ##    8       17     
    ## 17 0.07186 -      
    ## 35 0.00011 0.02511
    ## 
    ## P value adjustment method: holm

    # for the Chi-Squared values, use qchisq(1-p, df=1), where p is the pairwise p-value.

**One-sample post hoc tests**

A different kind of post hoc test for one sample. For Y’s response
categories (x,y,z), test each proportion against chance.

Two Samples

### Fisher’s exact test

\*notes from
<a href="https://www.youtube.com/watch?v=WTDWk4eJIw0" class="uri">https://www.youtube.com/watch?v=WTDWk4eJIw0</a>
by Matthew E. Clapham

Method for checking for independance, generally in small samples and
small contingency table.

**Types of data**: Categorical, 2x2 contingency table. Technically both
row and column marginals should be fixed prior to data collection.

**Purpose**: To test for assocation between the two counts.

**null hypothesis**: Category frequencies are independent of one another
between the samples (i.e, there is no association amoung categories)

    # df is a long-format data table w/subject (S), categorical factor (X) and outcome (Y)
    df <- read.csv("data/Proportions/1F2LBs_multinomial.csv")
    head(df)

    ##   S X Y
    ## 1 1 a y
    ## 2 2 b x
    ## 3 3 a x
    ## 4 4 b y
    ## 5 5 a y
    ## 6 6 b x

    df$S = factor(df$S) # Subject id is nominal (unused)
    df$X = factor(df$X) # X is a factor of m ≥ 2 levels
    df$Y = factor(df$Y) # Y is an outcome of n ≥ 2 categories

    unique(df$X)

    ## [1] a b
    ## Levels: a b

    unique(df$Y)

    ## [1] y x z
    ## Levels: x y z

    xt = xtabs( ~ X + Y, data=df) # make m × n crosstabs
    xt

    ##    Y
    ## X    x  y  z
    ##   a  3 26  1
    ##   b 14  9  7

    res <- fisher.test(xt)
    res

    ## 
    ##  Fisher's Exact Test for Count Data
    ## 
    ## data:  xt
    ## p-value = 2.432e-05
    ## alternative hypothesis: two.sided

    ## Fisher's post hoc test
    # xt is an m × n crosstabs with categories X and Y
    library(RVAideMemoire) # for fisher.multcomp
    fisher.multcomp(xt, p.method="holm")

    ## 
    ##         Pairwise comparisons using Fisher's exact test for count data
    ## 
    ## data:  xt
    ## 
    ##           x:y x:z      y:z
    ## a:b 0.0006569   1 0.004438
    ## 
    ## P value adjustment method: holm

### G-test

    # df is a long-format data table w/subject (S), categorical factor (X) and outcome (Y)
    library(RVAideMemoire) # for G.test
    df <- read.csv("data/Proportions/1F2LBs_multinomial.csv")
    head(df)

    ##   S X Y
    ## 1 1 a y
    ## 2 2 b x
    ## 3 3 a x
    ## 4 4 b y
    ## 5 5 a y
    ## 6 6 b x

    df$S = factor(df$S) # Subject id is nominal (unused)
    df$X = factor(df$X) # X is a factor of m ≥ 2 levels
    df$Y = factor(df$Y) # Y is an outcome of n ≥ 2 categories

    xt = xtabs( ~ X + Y, data=df) # make m × n crosstabs

    G.test(xt)

    ## 
    ##  G-test
    ## 
    ## data:  xt
    ## G = 21.402, df = 2, p-value = 2.252e-05

References:

<a href="http://www.sthda.com/english/wiki/comparing-proportions-in-r" class="uri">http://www.sthda.com/english/wiki/comparing-proportions-in-r</a>

<a href="http://www.sthda.com/english/wiki/chi-square-goodness-of-fit-test-in-r#what-is-chi-square-goodness-of-fit-test" class="uri">http://www.sthda.com/english/wiki/chi-square-goodness-of-fit-test-in-r#what-is-chi-square-goodness-of-fit-test</a>
