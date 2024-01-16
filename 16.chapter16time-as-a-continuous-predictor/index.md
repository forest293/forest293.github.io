# Chapter16 ：Time as a continuous predictor


This chapter considers models involving the analysis of longitudinal data. 

Such designs involve participants that are observed at more than one time point and time is generally treated as one of the important predictors in the model. Like any predictor, we need to ask ourselves how we want to model the relationship between time and the outcome. **A key distinction is whether time will be treated as a continuous variable or as a categorical variable.**

There are several approaches that can be used for modeling longitudinal data, including repeated-measures analysis of variance (ANOVA), generalized estimating equations (GEE), and multilevel modeling. This chapter will focus on using multilevel modeling for analyzing longitudinal models **where time is treated as level 1 and the person will be treated as level 2.** In such a model, characteristics that change as a function of time are level-1 predictors and characteristics that are a property of the person that do not change over time are level-2 predictors.

## 1 Example 1: Linear effect of time

Let’s begin by considering a simple model in which we look at the linear effect of time on the outcome variable. 

For example, let’s consider a study in which we are looking at the number of minutes people sleep at night over a seven-week period. For the sake of this example, assume that the people were selected for the study because they have recently experienced a stressful event in their life, and the purpose of the study is to understand the natural course of sleep change over the seven weeks after a stressful event. 


``` 
  use sleep_conlin.dta
  list in 1/5
  summarize
``` 
        
             +---------------------+
             | id   obsday   sleep |
             |---------------------|
          1. |  1        1     382 |
          2. |  1        6     382 |
          3. |  1       13     390 |
          4. |  1       21     378 |
          5. |  1       27     401 |
             +---------------------+


The dataset for this study is organized in a long format, with one observation per person per night of observation. 

            Variable |        Obs        Mean    Std. dev.       Min        Max
        -------------+---------------------------------------------------------
                  id |        600          38    21.66677          1         75
              obsday |        600      23.565    14.82854          1         52
               sleep |        600     360.785    48.13086        175        528
        
Instead, we can fit a random-intercept model that accounts for the nonindependence of the residuals within each person. Such a model is fit below first using the xtset command to specify that id is the panel variable and obsday is the time variable. 

We can then use the xtreg command to fit a random-intercept model predicting sleep from obsday

``` 
  xtset id obsday
``` 

        Panel variable: id (weakly balanced)
         Time variable: obsday, 1 to 52, but with gaps
                 Delta: 1 unit

``` 
  xtreg sleep obsday
``` 

        Random-effects GLS regression                   Number of obs     =        600
        Group variable: id                              Number of groups  =         75
        
        R-squared:                                      Obs per group:
             Within  = 0.1652                                         min =          8
             Between = 0.0062                                         avg =        8.0
             Overall = 0.0218                                         max =          8
        
                                                        Wald chi2(1)      =     103.56
        corr(u_i, X) = 0 (assumed)                      Prob > chi2       =     0.0000
        
        ------------------------------------------------------------------------------
               sleep | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
              obsday |       0.51       0.05    10.18   0.000         0.41        0.61
               _cons |     348.80       5.31    65.66   0.000       338.39      359.21
        -------------+----------------------------------------------------------------
             sigma_u |  44.412437
             sigma_e |  18.051717
                 rho |  .85821678   (fraction of variance due to u_i)
        ------------------------------------------------------------------------------

The interpretation of the obsday coefficient is straightforward. For each additional day in the study, nightly minutes of sleep increased by, on average, by 0.51 minutes. 

In this model, the coefficient for obsday is treated as a fixed effect. 

The model recognizes that people randomly vary in terms of their average sleep time at the start of the study (represented by the random intercept). **But perhaps people also vary individually in their trajectory of sleep times across the weeks of the study.**

By adding obsday as a random coefficient (that is, a random slope), the model can account for both individual differences in the average length of sleep at the start of the study (that is, a random intercept) and individual differences in the trajectory of sleep times across the weeks of the study (that is, a random slope for obsday).

``` 
  mixed sleep obsday || id:obsday, covariance(un) nolog
``` 

        Mixed-effects ML regression                          Number of obs    =    600
        Group variable: id                                   Number of groups =     75
                                                             Obs per group:
                                                                          min =      8
                                                                          avg =    8.0
                                                                          max =      8
                                                             Wald chi2(1)     =  20.54
        Log likelihood = -2488.5378                          Prob > chi2      = 0.0000
        
        -----------------------------------------------------------------------------------
                    sleep | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        ------------------+----------------------------------------------------------------
                   obsday |       0.51       0.11     4.53   0.000         0.29        0.73
                    _cons |     348.82       3.31   105.43   0.000       342.34      355.31
        -----------------------------------------------------------------------------------
        
        ------------------------------------------------------------------------------
          Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
        -----------------------------+------------------------------------------------
        id: Unstructured             |
                         var(obsday) |       0.89       0.16          0.63        1.26
                          var(_cons) |     775.84     134.13        552.86     1088.75
                   cov(obsday,_cons) |      14.30       3.56          7.33       21.27
        -----------------------------+------------------------------------------------
                       var(Residual) |     101.23       6.75         88.83      115.35
        ------------------------------------------------------------------------------
        LR test vs. linear model: chi2(3) = 1360.11               Prob > chi2 = 0.0000
        
        Note: LR test is conservative and provided only for reference.
4
The slope of the relationship between sleep duration and observation day is significant $( z= 4.53 , p = 0.000 )$. For each additional day in the study, the participants slept an additional 0.51 minutes.

This model added a random effect for obsday, which we see reflected in the estimate of var(obsday) in the random-effects portion of the output. This representsthe degree to which the sleep trajectories vary between individuals. 

As described in Singer and Willett (2003), we can form a simple one-sided $z$ test to determine if this value is significantly greater than 0. We can compute a $z$ statistic by dividing the estimate (0.8926) by its standard error (0.1553), which yields a $z$-value of 5.75. This value well exceeds 1.645 (the 5% cutoff for a one-sided $z$test), suggesting that there is significant variation in the sleep trajectories among individuals.2

> Singer and Willett (2003)----Simple one-sided $z$ test
>>Singer and Willett (2003) note that although this is a simple test to perform, there is disagreement over its appropriateness and it is probably best treated as a quick, but imprecise, test. They provide a useful discussion of the concerns surrounding this test and provide an alternative test that may be more preferable. The most appropriate method for performing such tests appears to be an unsettled question and the topic of continued research.

>What is "Simple one-sided $z$ test"
>>A simple one-sided z-test is a statistical hypothesis test used to determine whether there is a significant difference between a sample mean and a population mean when the population standard deviation is known. The "one-sided" aspect refers to the direction of the difference that you are interested in: either greater than or less than.

>The basic step for Simple one-sided $z$ test
>>1. Formulate Hypotheses:
>>* Null Hypothesis (H0): μ (population mean) is equal to a specified value.
>>* Alternative Hypothesis (H1): μ is greater than or less than the specified value.
>>2. Choose Significance Level (α):
>>* Common choices include 0.05, 0.01, or 0.10.
>>3. Select the Test Statistic:
>>* For a z-test, you use the z-statistic, which is calculated as (X̄ - μ) / (σ/√n), where X̄ is the sample mean, μ is the population mean under the null hypothesis, σ is the population standard deviation, and n is the sample size.
>>4. Determine the Critical Region:
>>* Based on the chosen significance level and whether it's a one-sided test (right-tailed or left-tailed), find the critical value from the standard normal distribution.
>>5. Make a Decision:
>>* If the test statistic falls into the critical region, reject the null hypothesis. Otherwise, fail to reject the null hypothesis.
>>6. Draw a Conclusion:
>>* State the conclusion in terms of the problem. If you reject the null hypothesis, it suggests evidence in favor of the alternative hypothesis.

>The choice between a one-sided and a two-sided test depends on the research question and the direction of the effect you are investigating. If you are specifically interested in whether the sample mean is greater than (or less than) a certain value, a one-sided test is appropriate. If you are interested in whether the sample mean is different from a certain value (greater or less), a two-sided test is used.

We might be interested in estimating the predicted mean number of minutes slept at night across the weeks of the study. Let’s estimate this for the first 50 days of observation in 7-day increments. 

``` 
  margins,nopvalues at(obsday=(1(7)50)) vsquish  
``` 

        Adjusted predictions                                       Number of obs = 600
        
        Expression: Linear prediction, fixed portion, predict()
        1._at: obsday =  1
        2._at: obsday =  8
        3._at: obsday = 15
        4._at: obsday = 22
        5._at: obsday = 29
        6._at: obsday = 36
        7._at: obsday = 43
        8._at: obsday = 50
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
                 _at |
                  1  |     349.33       3.36        342.74      355.92
                  2  |     352.90       3.81        345.44      360.37
                  3  |     356.48       4.36        347.94      365.01
                  4  |     360.05       4.97        350.32      369.78
                  5  |     363.62       5.62        352.60      374.64
                  6  |     367.20       6.31        354.83      379.56
                  7  |     370.77       7.02        357.02      384.52
                  8  |     374.34       7.74        359.18      389.51
        --------------------------------------------------------------
        
``` 
  marginsplot
``` 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.1.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Minutes of sleep at night by time</strong></figcaption>
</figure>

The margins command would have estimated the effect of the day of observation after holding the covariates constant, and you could have used the at() option to hold the covariates constant at a specified level.

## 2 Example 2: Linear effect of time by a categorical predictor

Let’s consider another sleep study in which participants diagnosed with insomnia were randomly assigned to one of three different treatments to increase the number of minutes of sleep at night. The three different treatments were 1) control group (no treatment), 2) medication group (where a sleep medication is given), or 3) education group (where the participants receive education about how to sleep better and longer).

This model involves a combination of a continuous predictor (time) and a threelevel categorical predictor (treatment group). 

``` 
  use sleep_cat3conlin.dta
  list in 1/5
  summarize
``` 

We can fit a model that predicts sleep from the observation day, the group assignment, and the interaction of these two variables using the mixed command shown below. Note that the random-effects portion of the model specifies that obsday is a random effect. [Thinking in terms of a multilevel model, group#obsday is a crosslevel interaction of a level-2 variable (group) with a level-1 variable (obsday).]


``` 
  mixed sleep i.group##c.obsday || id:obsday,covariance(un)nolog
``` 

        Mixed-effects ML regression                          Number of obs    =    600
        Group variable: id                                   Number of groups =     75
                                                             Obs per group:
                                                                          min =      8
                                                                          avg =    8.0
                                                                          max =      8
                                                             Wald chi2(5)     =  66.55
        Log likelihood = -2482.6017                          Prob > chi2      = 0.0000
        
        -----------------------------------------------------------------------------------
                    sleep | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        ------------------+----------------------------------------------------------------
                    group |
              Medication  |      35.48       9.50     3.74   0.000        16.86       54.10
               Education  |       5.55       9.50     0.58   0.559       -13.07       24.17
                          |
                   obsday |      -0.07       0.18    -0.37   0.715        -0.42        0.29
                          |
           group#c.obsday |
              Medication  |       0.18       0.25     0.71   0.475        -0.32        0.68
               Education  |       0.83       0.25     3.27   0.001         0.33        1.33
                          |
                    _cons |     339.45       6.72    50.53   0.000       326.28      352.61
        -----------------------------------------------------------------------------------
        
        ------------------------------------------------------------------------------
          Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
        -----------------------------+------------------------------------------------
        id: Unstructured             |
                         var(obsday) |       0.72       0.13          0.50        1.03
                          var(_cons) |    1079.51     184.18        772.68     1508.17
                   cov(obsday,_cons) |      22.52       4.23         14.22       30.82
        -----------------------------+------------------------------------------------
                       var(Residual) |     108.37       7.22         95.10      123.48
        ------------------------------------------------------------------------------
        LR test vs. linear model: chi2(3) = 1425.42               Prob > chi2 = 0.0000
        
        Note: LR test is conservative and provided only for reference.


We first use the margins command to estimate the predicted means by group at selected values of obsday. We follow that with the marginsplot command to graph the predicted means computed by the margins command. 


``` 
  margins group,at(obsday=(0 45))
  marginsplot,noci
``` 

        Adjusted predictions                                       Number of obs = 600
        
        Expression: Linear prediction, fixed portion, predict()
        1._at: obsday =  0
        2._at: obsday = 45
        
        -------------------------------------------------------------------------------
                      |            Delta-method
                      |     Margin   std. err.      z    P>|z|     [95% conf. interval]
        --------------+----------------------------------------------------------------
            _at#group |
           1#Control  |     339.45       6.72    50.53   0.000       326.28      352.61
        1#Medication  |     374.93       6.72    55.83   0.000       361.76      388.09
         1#Education  |     345.00       6.72    51.36   0.000       331.83      358.16
           2#Control  |     336.49      13.61    24.71   0.000       309.80      363.17
        2#Medication  |     380.14      13.60    27.94   0.000       353.47      406.80
         2#Education  |     379.42      13.61    27.88   0.000       352.75      406.10
        -------------------------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.2.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Minutes of sleep at night by time and treatment group</strong></figcaption>
</figure>

The slope appears to be slightly negative for the control group. In other words, their sleep durations appear to mildly decrease as a linear function of the observation day. For the medication group, the slope appears to be slightly positive. By contrast, sleep durations increase as a linear function of time for the education group. In other words, the obsday slope is positive for the education group.

Let’s use the margins command to estimate the three slopes depicted in figure. This is computed using the margins command, including **the dydx(obsday) option to estimate the obsday slope.**

``` 
  margins,dydx(obsday) over(group) vsquish
``` 

        Average marginal effects                                   Number of obs = 600
        
        Expression: Linear prediction, fixed portion, predict()
        dy/dx wrt:  obsday
        Over:       group
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |      dy/dx   std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
        obsday       |
               group |
            Control  |      -0.07       0.18    -0.37   0.715        -0.42        0.29
         Medication  |       0.12       0.18     0.65   0.518        -0.24        0.47
          Education  |       0.77       0.18     4.26   0.000         0.41        1.12
        ------------------------------------------------------------------------------

For the education group (group 3), the obsday slope is 0.77 and is significantly different from 0. This is consistent with the positive slope for the education group illustrated in figure above. For the education group, sleep durations increased (on average) by 0.77 minutes for every additional day.

Let’s now form comparisons of these slopes, comparing the slope for each group against group 1 (the control group). The r. contrast operator is applied to group to form reference group comparisons.

``` 
  margins r.group,dydx(obsday) contrast(pveffects nowald)  
``` 

        Contrasts of average marginal effects                      Number of obs = 600
        
        Expression: Linear prediction, fixed portion, predict()
        dy/dx wrt:  obsday
        
        -----------------------------------------------------------------
                                 |   Contrast Delta-method
                                 |      dy/dx   std. err.      z    P>|z|
        -------------------------+---------------------------------------
        obsday                   |
                           group |
        (Medication vs Control)  |       0.18       0.25     0.71   0.475
         (Education vs Control)  |       0.83       0.25     3.27   0.001
        -----------------------------------------------------------------
        
Say that we wanted to compare the predicted mean of sleep duration for each group with the control group across all days of observation. The margins command can be combined with the marginsplot command to create a graph that compares each group with the control group at each of the days of observation (1 to 45 days). 

``` 
  margins r.group,at(obsday=(1(1)45))
  marginsplot, bydimension(group) yline(0) xlabel(1(7)45)recast(line) recastci(rarea) ciopts(fcolor(black%20))
``` 

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.3.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Minutes of sleep at night by time and treatment group</strong></figcaption>
</figure>

The confidence interval for each comparison is shown with a reference line at zero. Where the confidence interval excludes zero, the difference is significant at the 5% level.

## 3 Example 3:Piecewise modeling of time

Time can be modeled in a piecewise fashion by breaking up the days of observation into the baseline phase and the treatment phase. 

In this example, we can assess the slope of the relationship between sleep duration and time (obsday) during the baseline and treatment phases. We can also test for a sudden jump in sleep duration on the 31st day, corresponding to the start of the treatment phase.


``` 
  use sleep_conpw.dta
  list in 1/5,sepby(id)
  summarize
``` 

We need to create some variables to prepare for the piecewise analysis. First, we use the mkspline command to create the variables named obsday1m and obsday2m, placing the knot at 31 days (corresponding to the start of the treatment phase). 

``` 
  mkspline obsday1m 31 obsday2m = obsday,marginal  
``` 

Next, we create the variable trtphase that is coded 0 for the baseline phase (where obsday was 1 to 30) and coded 1 during the treatment phase (where obsday is 31 or more).

``` 
  generate trtphase = 0 if obsday <= 30  
  replace trtphase = 1 if obsday >=31 & !missing(obsday) 
``` 

We are now ready to run a piecewise model that predicts sleep from obsday1m, obsday2m, and trtphase. In this example, obsday1m, obsday2m, and trtphase are also included as random effects.

``` 
  mixed sleep obsday1m obsday2m trtphase || id: obsday1m obsday2m trtphase,covariance(un)nolog 
``` 

        Mixed-effects ML regression                          Number of obs    =    600
        Group variable: id                                   Number of groups =     75
                                                             Obs per group:
                                                                          min =      8
                                                                          avg =    8.0
                                                                          max =      8
                                                             Wald chi2(3)     =  70.48
        Log likelihood = -2601.8889                          Prob > chi2      = 0.0000
        
        ----------------------------------------------------------------------------------------
                         sleep | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -----------------------+----------------------------------------------------------------
                      obsday1m |      -0.01       0.12    -0.05   0.961        -0.24        0.23
                      obsday2m |       0.54       0.17     3.27   0.001         0.22        0.87
                      trtphase |      11.14       1.94     5.74   0.000         7.33       14.94
                         _cons |     349.01      10.64    32.80   0.000       328.15      369.87
        ----------------------------------------------------------------------------------------
        
        ------------------------------------------------------------------------------
          Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
        -----------------------------+------------------------------------------------
        id: Unstructured             |
                       var(obsday1m) |       0.83       0.18          0.55        1.25
                       var(obsday2m) |       0.69       0.33          0.27        1.78
                       var(trtphase) |      59.53      48.90         11.90      297.83
                          var(_cons) |    8425.42    1386.81       6102.18    11633.16
              cov(obsday1m,obsday2m) |      -0.37       0.19         -0.73       -0.00
              cov(obsday1m,trtphase) |      -1.54       2.14         -5.73        2.65
                 cov(obsday1m,_cons) |      32.85      11.53         10.25       55.46
              cov(obsday2m,trtphase) |       2.84       2.78         -2.62        8.29
                 cov(obsday2m,_cons) |      -5.19      15.46        -35.50       25.12
                 cov(trtphase,_cons) |    -133.63     180.15       -486.71      219.45
        -----------------------------+------------------------------------------------
                       var(Residual) |     101.86       8.07         87.20      118.97
        ------------------------------------------------------------------------------
        LR test vs. linear model: chi2(10) = 2057.12              Prob > chi2 = 0.0000
        
        Note: LR test is conservative and provided only for reference.

We can then use the margins command to compute the predicted means for these key days. When obsday equals 31, **we estimate the predicted mean assuming trtphase is 0 and 1, to estimate the jump in the fitted values due to the start of the treatment phase. (The noatlegend option is included to save space.)**

``` 
  margins,at(obsday1m = 1 obsday2m = 0 trtphase=0) ///
          at(obsday1m = 31 obsday2m = 0 trtphase=0) ///
		      at(obsday1m = 31 obsday2m = 0 trtphase=1) ///
	        at(obsday1m = 49 obsday2m = 18 trtphase=1) nopvalues noatlegend
``` 

        Adjusted predictions                                       Number of obs = 600
        
        Expression: Linear prediction, fixed portion, predict()
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
                 _at |
                  1  |     349.01      10.68        328.07      369.94
                  2  |     348.83      12.30        324.72      372.94
                  3  |     359.97      12.16        336.14      383.79
                  4  |     369.65      13.11        343.95      395.34
        --------------------------------------------------------------

We save the predicted means to the current dataset.

``` 
  matrix yhat = r(b)'
  matrix day = (1 \ 31 \ 31 \ 49)
  svmat yhat 
  svmat day
  list yhat1 day1 in 1/4
  
  graph twoway line yhat1 day1,sort xline(31) xlabel(1(7)49 31)
``` 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.4.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Minutes of sleep at night by timep</strong></figcaption>
</figure>

The coefficient for obsday2m reflects the change in the slope for the treatment phase minus the baseline phase. The difference in these slopes is 0.543 and is significant. We can estimate the slope during the treatment phase using the lincom command.

``` 
  lincom obsday1m + obsday2m
``` 

         ( 1)  [sleep]obsday1m + [sleep]obsday2m = 0
        
        ------------------------------------------------------------------------------
               sleep | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 (1) |       0.54       0.16     3.34   0.001         0.22        0.85
        ------------------------------------------------------------------------------

During the treatment phase, each additional day of observation is associated with sleeping 0.538 additional minutes of sleep per night.

Finally, consider the coefficient for trtphase. This represents the jump in sleep at the start of the treatment phase (when observation day is 31). This coefficient is 11.1 and is statistically significant. 

## 4 Example 4: Piecewise effects of time by a categorical predictor

Let’s consider an extension of the previous example that includes a baseline and treatment phase, but where participants are divided into different groups and receive different kinds of treatments during the treatment phase.

The first 30 days of the study are a baseline period during which the sleep is observed, but no treatment is administered to any of the groups. Starting on the 31st day, the medication group receives sleep medication, the sleep education group receives education about how to lengthen their sleep, and the control group receives nothing. 

The first phase of the study (days 1 to 30) is called the baseline phase, and the second phase (day 31 until the end of the study) is called the treatment phase.

We can study the slope of the relationship between sleep duration and time during each of these phases, as well as the change (jump or drop) in sleep that occurs at the transition from baseline to the treatment phase. Furthermore, we can investigate the impact of the treatment group assignment (control, medication, and education) on the slope in each phase, as well as the jump or drop in sleep due to the start of the treatment phase.

``` 
  use sleep_cat3pw.dta
  list in 1/5,sepby(id)
  summarize
``` 

Imagine the educational periods (before high school graduation and after high school graduation) being replaced with the baseline and treatment phases. Also, imagine gender (male and female) being replaced with treatment group assignment (control, medication, and education).

First, the mkspline command is used to create the variables obsday1m and obsday2m, specifying 31 as the knot. By including the marginal option, obsday1m will represent the slope for the baseline period and obsday2m will represent the change in the slope for the treatment period compared with the baseline period.

``` 
  mkspline obsday1m 31 obsday2m = obsday,marginal
``` 

To account for the jump in sleep at the start of the treatment phase, we use the generate and replace commands to create the variable trtphase that is coded: 0 = baseline and 1 = treatment.

``` 
  generate trtphase = 0 if obsday < 31 
  replace trtphase = 1 if obsday >= 31 & !missing(obsday)
  
  label define trtlab 0 "baseline" 1 "treatment"
  label values trtphase trtlab
```
The mixed command for fitting this model is shown below. Note the variables trtphase, obsday1m, and obsday2m are specified as random effects. The nolog, noheader, and noretable options are used to suppress the iteration log, header, and random-effects table to save space.

``` 
  mixed sleep i.group##(i.trtphase c.obsday1m c.obsday2m), || id:trtphase obsday1m obsday2m,covariance(un) nolog noheader noretable noci
``` 

        ---------------------------------------------------------------
                         sleep | Coefficient  Std. err.      z    P>|z|
        -----------------------+---------------------------------------
                         group |
                   Medication  |      -2.07       3.41    -0.61   0.543
                    Education  |      16.31       3.41     4.78   0.000
                               |
                      trtphase |
                    treatment  |      -4.20       2.79    -1.51   0.132
                      obsday1m |      -0.04       0.30    -0.14   0.890
                      obsday2m |      -0.10       0.51    -0.19   0.849
                               |
                group#trtphase |
         Medication#treatment  |      34.28       4.00     8.58   0.000
          Education#treatment  |       1.65       3.97     0.41   0.678
                               |
              group#c.obsday1m |
                   Medication  |       0.40       0.42     0.95   0.343
                    Education  |      -0.14       0.42    -0.34   0.735
                               |
              group#c.obsday2m |
                   Medication  |      -0.29       0.72    -0.40   0.686
                    Education  |       2.48       0.72     3.45   0.001
                               |
                         _cons |     351.36       2.41   145.54   0.000
        ---------------------------------------------------------------

We can now use the margins command to compute the predicted mean of sleep for each group at four key points—for the beginning of the study, the first day of the treatment phase (with and without treatment), and the 49th day of the study. (The noatlegend option is specified to save space.)

``` 
  margins group,at(obsday1m = 1 obsday2m = 0 trtphase=0) ///
                at(obsday1m = 31 obsday2m = 0 trtphase=0) ///
				        at(obsday1m = 31 obsday2m = 0 trtphase=1) ///
				        at(obsday1m = 49 obsday2m = 18 trtphase=1)nopvalues noatlegend
```

        Adjusted predictions                                       Number of obs = 600
        
        Expression: Linear prediction, fixed portion, predict()
        
        ---------------------------------------------------------------
                      |            Delta-method
                      |     Margin   std. err.     [95% conf. interval]
        --------------+------------------------------------------------
            _at#group |
           1#Control  |     351.32       2.36        346.69      355.95
        1#Medication  |     349.65       2.36        345.03      354.28
         1#Education  |     367.49       2.36        362.87      372.11
           2#Control  |     350.08       9.04        332.36      367.80
        2#Medication  |     360.45       9.04        342.74      378.17
         2#Education  |     361.94       9.04        344.21      379.66
           3#Control  |     345.87       9.05        328.13      363.62
        3#Medication  |     390.54       9.08        372.75      408.32
         3#Education  |     359.38       9.06        341.62      377.13
           4#Control  |     343.39      11.26        321.32      365.46
        4#Medication  |     390.03      11.26        367.96      412.11
         4#Education  |     398.91      11.25        376.86      420.96
        ---------------------------------------------------------------

``` 
  *save predicted means as a matrix named -yhat-
  matrix yhat = r(b)'
  *save the day number in a matrix named -day-
  matrix day = (0 \ 0 \ 0 \ 31 \ 31 \ 31 \ 31 \ 31 \ 31 \ 49 \ 49 \ 49 ) 
  *save the group in a matrix named -gp-
  matrix gp = (1 \ 2 \ 3 \ 1 \ 2 \ 3 \ 1 \ 2 \ 3 \ 1 \ 2 \ 3 )
  *save the contents of the matrices to the current dataset
  svmat yhat
  svmat day
  svmat gp

  graph twoway (line yhat1 day1 if gp1==1) (line yhat1 day1 if gp1==2) ///
               (line yhat1 day1 if gp1==3),xline(31)xlabel(1(7)49 30) ///
			         legend(title(Treatment group) ///
			         label(1 "Control")label(2 "Medication") ///
			         label(3 "Education") rows(5))
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.5.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Minutes of sleep at night by time and group</strong></figcaption>
</figure>

we see that the sleep durations for the control group remain much the same across the entire study with a tiny (but trivial) drop in sleep at the start of treatment. 

For the medication group, their sleep durations remain fairly steady during the baseline phase, then jump at the start of treatment, and then remain steady. 

The sleep durations for the education group are fairly steady during the baseline phase, and then show a small dip at the start of treatment but a substantial increase in slope during the treatment phase. 

### 4.1 Baseline slope [means the prior to the treatment phase]

We can test the equality of all the baseline slopes using the contrast command below. This test is not significant.

``` 
  contrast group#c.obsday1m 
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------
                         |         df        chi2     P>chi2
        -----------------+----------------------------------
        sleep            |
        group#c.obsday1m |          2        1.78     0.4108
        ----------------------------------------------------
        
You can estimate the baseline slope for each group using the margins command below. 

``` 
  margins, dydx(obsday1m) over(group) 
```

        Average marginal effects                                   Number of obs = 600
        
        Expression: Linear prediction, fixed portion, predict()
        dy/dx wrt:  obsday1m
        Over:       group
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |      dy/dx   std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
        obsday1m     |
               group |
            Control  |      -0.04       0.30    -0.14   0.890        -0.63        0.55
         Medication  |       0.36       0.30     1.20   0.229        -0.23        0.95
          Education  |      -0.19       0.30    -0.62   0.537        -0.77        0.40
        ------------------------------------------------------------------------------

### 4.2 Change in slopes: Treatment versus baseline

You can test the equality of the changes in slope coefficients for all three groups using the contrast command below. This test is significant.

``` 
  contrast group#c.obsday2m
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------
                         |         df        chi2     P>chi2
        -----------------+----------------------------------
        sleep            |
        group#c.obsday2m |          2       17.95     0.0001
        ----------------------------------------------------

You can estimate the change in slope for each group using the margins command below. The change in slope for the control group and medication groups is not significant. For the education group (group 3), the slope during the treatment phase is significantly greater than the slope during the baseline phase.

``` 
  margins,dydx(obsday2m) over(group)  
```

        Average marginal effects                                   Number of obs = 600
        
        Expression: Linear prediction, fixed portion, predict()
        dy/dx wrt:  obsday2m
        Over:       group
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |      dy/dx   std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
        obsday2m     |
               group |
            Control  |      -0.10       0.51    -0.19   0.849        -1.09        0.90
         Medication  |      -0.39       0.51    -0.76   0.447        -1.39        0.61
          Education  |       2.38       0.51     4.70   0.000         1.39        3.38
        ------------------------------------------------------------------------------

### 4.3 Jump at treatment

We can test the equality of the jump or drop for all three groups using the contrast
command below. This test is significant.
``` 
  contrast group#trtphase
```
        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        --------------------------------------------------
                       |         df        chi2     P>chi2
        ---------------+----------------------------------
        sleep          |
        group#trtphase |          2       92.58     0.0000
        --------------------------------------------------
        
We can estimate the size of the jump or drop in sleep durations at the start of the treatment phase for each group using the contrast command below.

``` 
  contrast trtphase@group,pveffects nowald
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------------------------------
                                        |   Contrast   Std. err.      z    P>|z|
        --------------------------------+---------------------------------------
        sleep                           |
                         trtphase@group |
           (treatment vs base) Control  |      -4.20       2.79    -1.51   0.132
        (treatment vs base) Medication  |      30.08       2.86    10.52   0.000
         (treatment vs base) Education  |      -2.56       2.83    -0.90   0.366
        ------------------------------------------------------------------------

### 4.4 Comparisons among groups

Let’s now use the margins command to compare each group with the control group at each of these days. (The noatlegend option is specified to save space.)

``` 
  margins r.group,at(obsday1m=30 obsday2m=0 trtphase=0) ///
                  at(obsday1m=35 obsday2m=4 trtphase=1) ///
				          at(obsday1m=40 obsday2m=9 trtphase=1) ///
				          at(obsday1m=45 obsday2m=14 trtphase=1) ///
				          contrast(nowald pveffects)noatlegend
```

        Contrasts of adjusted predictions                          Number of obs = 600
        
        Expression: Linear prediction, fixed portion, predict()
        
        -------------------------------------------------------------------
                                   |            Delta-method
                                   |   Contrast   std. err.      z    P>|z|
        ---------------------------+---------------------------------------
                         group@_at |
        (Medication vs Control) 1  |       9.98      12.37     0.81   0.420
        (Medication vs Control) 2  |      45.10      12.87     3.50   0.000
        (Medication vs Control) 3  |      45.65      13.50     3.38   0.001
        (Medication vs Control) 4  |      46.21      14.68     3.15   0.002
         (Education vs Control) 1  |      12.00      12.38     0.97   0.332
         (Education vs Control) 2  |      22.84      12.87     1.77   0.076
         (Education vs Control) 3  |      34.51      13.50     2.56   0.011
         (Education vs Control) 4  |      46.18      14.67     3.15   0.002
        -------------------------------------------------------------------

Focusing on the comparison of the medication group with the control group, the difference is not significant when observation day is 30 ($p=0.420$); prior to the treatment phase. However, the difference is significant at each of the time points tested during the treatment phase, when observation day is 35, 40, and 45 ( = 0.000, 0.001, and 0.002, respectively).

Shifting our attention to the comparison of the education group with the control group, the comparison is not significant when observation day is 30 ($p=0.332$); prior to the start of the treatment phase. The difference remains nonsignificant early in the treatment phase when observation day is 35 ($p=0.076$) but is significant later inthe treatment phase when observation day is 40 ($p=0.011$) and 45 ($p=0.002$).
