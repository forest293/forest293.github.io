# Chapter17 ：Time as a categorical predictor


This chapter considers models where time is treated as a categorical variable

The models presented in this chapter will use the mixed command to fit a model that is a hybrid of a traditional repeated-measures analysis of variance (ANOVA) and a mixed model.

Like the repeated-measures ANOVA, there will be one fixed intercept (rather than having random intercepts that we commonly see when using the mixed command). By specifying the noconstant option in the random-effects portion of the mixed command, a fixed intercept will be estimated.

To account for the nonindependence of residuals across time points, we will use the residuals() option within the random-effects portion of the mixed command. This allows us to model the structure of the residual covariances across time points. The following examples will use an unstructured residual covariance, which estimates a separate residual variance for each time point and a separate residual correlation among each pair of the time points.

## 1 Example 1: Time treated as a categorical variable

Sleep was measured at three time points, the first a control (baseline) condition and the second and third measurements while taking a sleep medication.

here are two aims of this study. The first aim is to assess the initial impact of sleep medication on duration of sleep.So the second aim is to the assess the sustained effectiveness of the medication on sleep, comparing the amount of sleep in the second and third months

``` 
  use sleep_cat3.dta
  summarize
``` 
>Note! Wide and long datasets
>>Data for this kind of study might be stored with one observation per person and three variables representing the different time points. Sometimes, this is called a multivariate format, and Stata would call this a wide format. If your dataset is in that kind of form, you can use the reshape command to convert it to a long format.

Let’s now use the mixed command to predict sleep from month, treating month as a categorical variable. 

1. Specifying || id: introduces the random-effects part of the model and indicates that the observations are nested within id. 
2. Next, the noconstant option is specified in the random-effects options so only one fixed intercept is fit. 
3. Furthermore, the residuals() option is included to specify covariance structure of the residuals between months (within each level of the person’s ID). The residuals() option specifies an unstructured residual covariance among the different months within each person. This accounts for the nonindependence of the observations among time points for each person.

``` 
  mixed sleep i.month || id:,noconstant residuals(unstructured,t(month))nolog 
``` 

        Mixed-effects ML regression                          Number of obs    =    300
        Group variable: id                                   Number of groups =    100
                                                             Obs per group:
                                                                          min =      3
                                                                          avg =    3.0
                                                                          max =      3
                                                             Wald chi2(2)     =  35.94
        Log likelihood = -1437.3712                          Prob > chi2      = 0.0000
        
        ------------------------------------------------------------------------------
               sleep | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
               month |
                  2  |      21.07       3.83     5.50   0.000        13.56       28.58
                  3  |      18.43       3.81     4.84   0.000        10.97       25.89
                     |
               _cons |     348.24       3.14   110.73   0.000       342.08      354.40
        ------------------------------------------------------------------------------
        
        ------------------------------------------------------------------------------
          Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
        -----------------------------+------------------------------------------------
        id:                  (empty) |
        -----------------------------+------------------------------------------------
        Residual: Unstructured       |
                             var(e1) |     989.04     139.87        749.61     1304.95
                             var(e2) |     928.55     131.32        703.77     1225.14
                             var(e3) |     731.36     103.43        554.31      964.96
                          cov(e1,e2) |     224.17      98.42         31.27      417.06
                          cov(e1,e3) |     135.43      86.12        -33.37      304.22
                          cov(e2,e3) |     107.72      83.11        -55.17      270.61
        ------------------------------------------------------------------------------
        LR test vs. linear model: chi2(5) = 11.61                 Prob > chi2 = 0.0405
        
        Note: The reported degrees of freedom assumes the null hypothesis is not on the boundary of the parameter
              space. If this is not true, then the reported test is conservative.

Before we interpret the coefficients from this model, let’s use the margins command to compute the predicted mean of sleep for each level of month, as shown below. 

``` 
  margins month,nopvalues 
``` 

        Adjusted predictions                                       Number of obs = 300
        
        Expression: Linear prediction, fixed portion, predict()
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
               month |
                  1  |     348.24       3.14        342.08      354.40
                  2  |     369.31       3.05        363.34      375.28
                  3  |     366.67       2.70        361.37      371.97
        --------------------------------------------------------------


We can use the marginsplot command to create a graph showing the predicted mean of sleep across the three months, as shown below.
``` 
  marginsplot
```  


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.6.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Estimated minutes of sleep at night by month</strong></figcaption>
</figure>

Before making those specific tests, let’s test the overall null hypothesis that the average number of minutes of sleep is equal across all three months. This is tested using the contrast command below. This test is significant, indicating that the average number of minutes of sleep is not equal across all months

``` 
  contrast month
```  

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df        chi2     P>chi2
        -------------+----------------------------------
        sleep        |
               month |          2       35.94     0.0000
        ------------------------------------------------
        
Now, let’s use the contrast command to perform the specific comparisons of interest. Let’s compare the amount of sleep in the second month with the first month to test the **initial impact of the sleep medication**. Let’s also compare the third month with the second month to assess the extent to which any impact was sustained between the second and third month. 

``` 
  contrast ar.month,nowald effects 
```  

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------------------------------------
                     |   Contrast   Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
        sleep        |
               month |
           (2 vs 1)  |      21.07       3.83     5.50   0.000        13.56       28.58
           (3 vs 2)  |      -2.64       3.80    -0.69   0.487       -10.09        4.81
        ------------------------------------------------------------------------------


The first test is significant. In the second month, participants slept (on average) 21.07 minutes longer than in the first month. The second test is not significant. The predicted mean of sleep in the third month was not significantly different from the second month. The effectiveness of the medication did not significantly diminish in the third month compared with the second month.

If we wanted a comparison of every pair of months, we can use the pwcompare command.

``` 
  pwcompare month,effects
```  

        Pairwise comparisons of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------------------------------------
                     |                            Unadjusted           Unadjusted
                     |   Contrast   Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
        sleep        |
               month |
             2 vs 1  |      21.07       3.83     5.50   0.000        13.56       28.58
             3 vs 1  |      18.43       3.81     4.84   0.000        10.97       25.89
             3 vs 2  |      -2.64       3.80    -0.69   0.487       -10.09        4.81
        ------------------------------------------------------------------------------


The output also shows the estimate of the correlation of the residuals between each pair of time points. For example, the correlation of the residuals at month 1 with month 2 is 0.23. This residual covariance structure is called an unstructured covariance and was requested specifying the unstructured residual type within the residuals() option. 

>Note! Why not use repeated-measures ANOVA?
>>The mixed command is more flexible and powerful than a traditional repeated-measures ANOVA.
>>1. First, a repeated-measures ANOVA omits any observation with missing data (by using listwise deletion). The mixed command retains subjects who might have missing data and performs the estimation on the time observations that are present.
>>2. Second, a traditional repeated-measures ANOVA is restricted to only two different types of covariance structures, exchangeable (compound symmetry) and unstructured (multivariate). There are many situations where an exchangeable structure is overly simple and untenable, and the unstructured covariance is overly complicated and estimates too many superfluous parameters. By contrast, the mixed command permits you to choose from a variety of covariance structures such as banded, autoregressive, or Toeplitz, as described in section 17.5. Although the output looks a little bit different, the mixed command combined with the residuals() option offers all the features from a repeated-measures ANOVA, with the added benefit of retaining subjects who have missing time points and the ability to estimate additional residual covariance structures.

>The unstructured residual type specified within the residual() option specifies an unstructured covariance matrix, and the t(month) suboption specifies that observations are repeated across months. Section 17.5 provides more information about how to select among residual covariance structures.

## 2 Example 2: Time (categorical) by two groups

This study includes 100 participants in the treatment group and 100 participants in the control group. The main predictors for this example are group (a two-level categorical variable) and month (a three-level categorical variable). 

``` 
  use sleep_catcat23.dta
  list in 1/6,sepby(id)
  summarize

  mixed sleep i.group##i.month || id:,noconstant residuals(un,t(month))nolog
```  

        Mixed-effects ML regression                          Number of obs    =    600
        Group variable: id                                   Number of groups =    200
                                                             Obs per group:
                                                                          min =      3
                                                                          avg =    3.0
                                                                          max =      3
                                                             Wald chi2(5)     =  68.47
        Log likelihood =   -2879.78                          Prob > chi2      = 0.0000
        
        -------------------------------------------------------------------------------
                sleep | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        --------------+----------------------------------------------------------------
                group |
          Medication  |      -7.08       4.38    -1.62   0.106       -15.66        1.50
                      |
                month |
                   2  |      -3.73       3.75    -1.00   0.319       -11.07        3.61
                   3  |      -0.57       4.26    -0.13   0.893        -8.91        7.77
                      |
          group#month |
        Medication#2  |      28.06       5.30     5.30   0.000        17.67       38.45
        Medication#3  |      24.94       6.02     4.14   0.000        13.14       36.74
                      |
                _cons |     350.63       3.10   113.24   0.000       344.56      356.70
        -------------------------------------------------------------------------------
        
        ------------------------------------------------------------------------------
          Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
        -----------------------------+------------------------------------------------
        id:                  (empty) |
        -----------------------------+------------------------------------------------
        Residual: Unstructured       |
                             var(e1) |     958.68      95.87        788.05     1166.26
                             var(e2) |     721.30      72.13        592.92      877.48
                             var(e3) |     980.26      98.03        805.78     1192.50
                          cov(e1,e2) |     138.07      59.61         21.25      254.90
                          cov(e1,e3) |      63.24      68.69        -71.40      197.87
                          cov(e2,e3) |     119.72      60.06          2.01      237.43
        ------------------------------------------------------------------------------
        LR test vs. linear model: chi2(5) = 15.70                 Prob > chi2 = 0.0078
        
        Note: The reported degrees of freedom assumes the null hypothesis is not on the boundary of the parameter
              space. If this is not true, then the reported test is conservative.

We can estimate the mean sleep by group and month using the margins command below. 


``` 
  margins month#group,nopvalues
  marginsplot,noci
```  

        Adjusted predictions                                       Number of obs = 600
        
        Expression: Linear prediction, fixed portion, predict()
        
        ---------------------------------------------------------------
                      |            Delta-method
                      |     Margin   std. err.     [95% conf. interval]
        --------------+------------------------------------------------
          month#group |
           1#Control  |     350.63       3.10        344.56      356.70
        1#Medication  |     343.55       3.10        337.48      349.62
           2#Control  |     346.90       2.69        341.64      352.16
        2#Medication  |     367.88       2.69        362.62      373.14
           3#Control  |     350.06       3.13        343.92      356.20
        3#Medication  |     367.92       3.13        361.78      374.06
        ---------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.7.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Estimated sleep by month and treatment group</strong></figcaption>
</figure>

Before testing our two main questions of interest, let’s assess the overall interaction of group by month using the contrast command below. The overall interaction is significant.


``` 
  contrast group#month 
```  

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df        chi2     P>chi2
        -------------+----------------------------------
        sleep        |
         group#month |          2       30.21     0.0000
        ------------------------------------------------

Now to test our questions of interest regarding the initial effect of medication and the sustained effect of medication, we can apply the ar. contrast operator to month and interact that with group, as shown below.

``` 
  contrast ar.month#group,nowald pveffects
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -----------------------------------------------------------------------
                                       |   Contrast   Std. err.      z    P>|z|
        -------------------------------+---------------------------------------
        sleep                          |
                           month#group |
        (2 vs 1) (Medication vs base)  |      28.06       5.30     5.30   0.000
        (3 vs 2) (Medication vs base)  |      -3.12       5.41    -0.58   0.564
        -----------------------------------------------------------------------
        
The first contrast compares the change in sleep in month 2 versus month 1 for the treatment group versus the control group. This test is significant. The change in sleep for the medication group was 28.06 minutes more than the change for the control group. 

The second contrast compares the change for month 3 versus month 2 for the treatment group versus the control group. This comparison is not significant.

## 3 Example 3: Time (categorical) by three groups

This study includes 300 participants, 100 assigned to each of the three treatment groups. The main variables of interest in this study are treatment group (with three levels) and month (with three levels). 


``` 
  use sleep_catcat33.dta
  list in 1/6,sepby(id)
  summarize

  mixed sleep i.group##i.month ||id:,noconstant residuals(un,t(month)) nolog
```

    Mixed-effects ML regression                          Number of obs    =    900
    Group variable: id                                   Number of groups =    300
                                                         Obs per group:
                                                                      min =      3
                                                                      avg =    3.0
                                                                      max =      3
                                                         Wald chi2(8)     = 284.84
    Log likelihood = -4318.4068                          Prob > chi2      = 0.0000
    
    -------------------------------------------------------------------------------
            sleep | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    --------------+----------------------------------------------------------------
            group |
      Medication  |       2.60       4.25     0.61   0.540        -5.72       10.92
       Education  |       3.17       4.25     0.75   0.455        -5.15       11.49
                  |
            month |
               2  |       8.06       3.72     2.17   0.030         0.77       15.35
               3  |      13.35       4.06     3.29   0.001         5.39       21.31
                  |
      group#month |
    Medication#2  |      21.88       5.26     4.16   0.000        11.57       32.19
    Medication#3  |      18.22       5.75     3.17   0.002         6.96       29.48
     Education#2  |       7.11       5.26     1.35   0.177        -3.20       17.42
     Education#3  |      34.49       5.75     6.00   0.000        23.23       45.75
                  |
            _cons |     344.70       3.00   114.80   0.000       338.82      350.58
    -------------------------------------------------------------------------------
    
    ------------------------------------------------------------------------------
      Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
    -----------------------------+------------------------------------------------
    id:                  (empty) |
    -----------------------------+------------------------------------------------
    Residual: Unstructured       |
                         var(e1) |     901.52      73.61        768.20     1057.98
                         var(e2) |     836.25      68.28        712.59      981.38
                         var(e3) |     918.42      74.99        782.60     1077.81
                      cov(e1,e2) |     177.08      51.16         76.80      277.35
                      cov(e1,e3) |      84.56      52.76        -18.85      187.97
                      cov(e2,e3) |     160.48      51.44         59.66      261.30
    ------------------------------------------------------------------------------
    LR test vs. linear model: chi2(5) = 24.71                 Prob > chi2 = 0.0002
    
    Note: The reported degrees of freedom assumes the null hypothesis is not on the boundary of the parameter
          space. If this is not true, then the reported test is conservative.
    

The margins and marginsplot commands are used below to estimate the predicted mean of sleep by group and month and to graph the results. 


``` 
  margins month#group,nopvalues
  marginsplot,noci
```

    Adjusted predictions                                       Number of obs = 900
    
    Expression: Linear prediction, fixed portion, predict()
    
    ---------------------------------------------------------------
                  |            Delta-method
                  |     Margin   std. err.     [95% conf. interval]
    --------------+------------------------------------------------
      month#group |
       1#Control  |     344.70       3.00        338.82      350.58
    1#Medication  |     347.30       3.00        341.42      353.18
     1#Education  |     347.87       3.00        341.99      353.75
       2#Control  |     352.76       2.89        347.09      358.43
    2#Medication  |     377.24       2.89        371.57      382.91
     2#Education  |     363.04       2.89        357.37      368.71
       3#Control  |     358.05       3.03        352.11      363.99
    3#Medication  |     378.87       3.03        372.93      384.81
     3#Education  |     395.71       3.03        389.77      401.65
    ---------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.8.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong> Sleep by month and treatment group</strong></figcaption>
</figure>

Let’s now use the contrast command to test the group by month interaction. This overall interaction is significant.

``` 
  contrast group#month
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df        chi2     P>chi2
        -------------+----------------------------------
        sleep        |
         group#month |          4       62.05     0.0000
        ------------------------------------------------


Let’s form an interaction contrast in which we apply reference group contrasts to treatment group (r.group) and reverse adjacent group contrasts to month (ar.month).

``` 
  contrast ar.month#r.group,nowald pveffects
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        --------------------------------------------------------------------------
                                          |   Contrast   Std. err.      z    P>|z|
        ----------------------------------+---------------------------------------
        sleep                             |
                              month#group |
        (2 vs 1) (Medication vs Control)  |      21.88       5.26     4.16   0.000
         (2 vs 1) (Education vs Control)  |       7.11       5.26     1.35   0.177
        (3 vs 2) (Medication vs Control)  |      -3.66       5.35    -0.68   0.494
         (3 vs 2) (Education vs Control)  |      27.38       5.35     5.11   0.000
        --------------------------------------------------------------------------


Let’s test the interaction of treatment (education versus medication) by month, but now let’s compare each month with the baseline month: month 2 versus 1 and month 3 versus 1. This is obtained by interacting the comparison of group 3 versus group 2(ar3.group) by each month compared with month 1 (r.month). This is tested using the contrast command.

``` 
  contrast ar3.group#r.month,nowald pveffects
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------------------------------
                                            |   Contrast   Std. err.      z    P>|z|
        ------------------------------------+---------------------------------------
        sleep                               |
                                group#month |
        (Education vs Medication) (2 vs 1)  |     -14.77       5.26    -2.81   0.005
        (Education vs Medication) (3 vs 1)  |      16.27       5.75     2.83   0.005
        ----------------------------------------------------------------------------

## 4 Comparing models with different residual covariance structures

The selection of the covariance structure impacts the estimates of the standard errors of the coefficients, but not the point estimates of the coefficients.

When you have only three time points, an unstructured covariance can be a good choice. However, **as the number of time points increases, the number of variances and covariances estimated by an unstructured covariance matrix increases dramatically.** For example, if you have five time points, an unstructured covariance estimates five variances and 10 covariances (a total of 15 parameters). In such cases, **you might consider more parsimonious covariance structures, such as the exchangeable, ar (autoregressive), or banded residual types.**

This leads to the question of how to choose among models using different covariance structures. 

My first recommendation would be to select a residual covariance structure that is grounded in theory or suggested by previous research. However, such information may be scarce or nonexistent. In such cases, **you can fit different covariance structures seeking the residual covariance structure that combines the fewest parameters with the best measure of fit—using, for example, Akaike information criterion (AIC) or Bayesian information criterion (BIC).** Stata makes this process easy, as illustrated below

``` 
  use sleep_cat3.dta

  mixed sleep i.month || id:,noconstant residuals(unstructured,t(month))
  estimate store m_un

  mixed sleep i.month || id:,noconstant residuals(exchangeable,t(month))
  estimate store m_ex

  mixed sleep i.month || id:,noconstant residuals(ar 1,t(month))
  estimate store m_ar1
```

Using the dataset from example 1, models are fit using three different covariance structures: unstructured, exchangeable, and ar 1. After fitting each model, the estimates store command is used to store the estimates from the respective model.


``` 
  estimate stats m_un m_ex m_ar1
```

        Akaike's information criterion and Bayesian information criterion
        
        -----------------------------------------------------------------------------
               Model |          N   ll(null)  ll(model)      df        AIC        BIC
        -------------+---------------------------------------------------------------
                m_un |        900          .  -4367.631       9   8753.262   8796.484
                m_ex |        900          .  -4372.205       5   8754.409   8778.421
               m_ar1 |        900          .   -4370.91       5    8751.82   8775.832
        -----------------------------------------------------------------------------
        Note: BIC uses N = number of observations. See [R] IC note.


**Remember that when it comes to AIC and BIC, smaller is better.** 

The ar 1 and exchangeable models have smaller AIC values than the unstructured model. Likewise, the ar 1 and exchangeable models also have smaller BIC values than the unstructured model. 

The ar 1 and exchangeable models also have the added benefit of including four fewer residual covariance parameters (5 versus 9). 

The ar 1 and exchangeable covariance structure appear to provide a fairly similar quality of fit, and both fit better than the unstructured covariance structure.

>Warning! Likelihood-ratio test
>>It is tempting to ask whether the difference in covariance structures is significantly different and to want to use a command like lrtest to test whether one covariance structure fits significantly better than another. A key assumption of a likelihood-ratio test is that one model is nested within another model, where one model can be created from the other by omitting one or more parameters. In many (or perhaps most) cases, the models formed by comparing two different residual covariance structures are not nested within each other and the likelihood-ratio test is not valid. However, the AIC and BIC indices can be used even when models are not nested within each other.

>See [ME] mixed for a list of all available covariance structures you can choose within the residuals() option. Furthermore, chapter 7 of Singer and Willett (2003) provides additional descriptions of these residual covariance structures, including information to help you choose among the different structures.

## 5  Analyses with small samples
The mixed command uses large-sample methods. Applying such methods with smallsample sizes may lead to overly liberal statistical tests (that is, greater type I error rates).

The mixed command allows you to specify the dfmethod() option to select testing methods that are more appropriate for small-sample sizes. Specifying the dfmethod() option is easy—knowing the best method to select is not so easy. 

For an introduction to the different small-sample methods you can choose from, I encourage you to see help mixed, especially the section in the PDF documentation titled Small-sample inference for fixed effects. That section describes five different small-sample adjustment methods: residual, repeated, anova, satterthwaite, and kroger. 

This leaves two remaining methods to consider: dfmethod(kroger) and dfmethod(satterthwaite). The dfmethod(kroger) option (described in Kenward and Roger (1997)) and dfmethod(satterthwaite) option (described in Satterthwaite (1946)) offer methods that are more applicable when making inferences with longitudinal models with small-sample sizes. 

**There will likely be continued research in this area, and the potential for new techniques and options to arise. For now, it seems that the Kenward–Roger method is probably the most generally useful method available, although sensitivity analyses considering the Satterthwaite method would seem prudent.**

``` 
  use sleep_catcat23small.dta
  count
  tab group month
  sort id month 
  list in 1/20,sepby(id)
  summarize
```

use large-sample statistical methods.(note how the significance of each parameter is tested using z-test)

``` 
  mixed sleep i.group##i.month || id:,noconstant residuals(un,t(month)) 
```

    Performing gradient-based optimization: 
    Iteration 0:  Log likelihood = -364.19065  
    Iteration 1:  Log likelihood = -363.52715  
    Iteration 2:  Log likelihood = -363.12776  
    Iteration 3:  Log likelihood =  -363.1244  
    Iteration 4:  Log likelihood =  -363.1244  
    
    Computing standard errors ...
    
    Mixed-effects ML regression                          Number of obs    =     80
    Group variable: id                                   Number of groups =     30
                                                         Obs per group:
                                                                      min =      1
                                                                      avg =    2.7
                                                                      max =      3
                                                         Wald chi2(5)     =  27.47
    Log likelihood =  -363.1244                          Prob > chi2      = 0.0000
    
    -------------------------------------------------------------------------------
            sleep | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    --------------+----------------------------------------------------------------
            group |
      Medication  |     -19.76       9.34    -2.12   0.034       -38.06       -1.47
                  |
            month |
               2  |      -6.20       8.58    -0.72   0.470       -23.01       10.62
               3  |     -20.99      10.16    -2.07   0.039       -40.91       -1.07
                  |
      group#month |
    Medication#2  |      41.98      11.93     3.52   0.000        18.58       65.37
    Medication#3  |      48.40      14.15     3.42   0.001        20.68       76.13
                  |
            _cons |     361.38       6.60    54.75   0.000       348.45      374.32
    -------------------------------------------------------------------------------
    
    ------------------------------------------------------------------------------
      Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
    -----------------------------+------------------------------------------------
    id:                  (empty) |
    -----------------------------+------------------------------------------------
    Residual: Unstructured       |
                         var(e1) |     612.04     163.59        362.46     1033.48
                         var(e2) |     459.72     122.77        272.38      775.92
                         var(e3) |     505.46     145.96        287.00      890.21
                      cov(e1,e2) |      40.15     105.05       -165.75      246.05
                      cov(e1,e3) |    -111.18     115.76       -338.07      115.71
                      cov(e2,e3) |      60.67     110.88       -156.66      277.99
    ------------------------------------------------------------------------------
    LR test vs. linear model: chi2(5) = 2.13                  Prob > chi2 = 0.8305
    
    Note: The reported degrees of freedom assumes the null hypothesis is not on the boundary of the parameter
          space. If this is not true, then the reported test is conservative.
    
The contrast command is used below to compare the change in sleep across months between the medication group versus the control group. 

``` 
  contrast ar.month#group,nowald pveffects 
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -----------------------------------------------------------------------
                                       |   Contrast   Std. err.      z    P>|z|
        -------------------------------+---------------------------------------
        sleep                          |
                           month#group |
        (2 vs 1) (Medication vs base)  |      41.98      11.93     3.52   0.000
        (3 vs 2) (Medication vs base)  |       6.43      11.54     0.56   0.577
        -----------------------------------------------------------------------


**Given the sample size of this analysis, our results might be more defensible if we chose an analysis technique more appropriate for small-sample sizes.** The mixed command below adds the dfmethod(kroger) option to use the methods presented in Kenward and Roger (1997).**This method is only supported with REML4 estimation, so the mixed command also specifies the reml option.**

``` 
  mixed sleep i.group##i.month || id:,noconstant residuals(un,t(month)) dfmethod(kroger) reml nolog
```
    Mixed-effects REML regression                        Number of obs    =     80
    Group variable: id                                   Number of groups =     30
                                                         Obs per group:
                                                                      min =      1
                                                                      avg =    2.7
                                                                      max =      3
    DF method: Kenward–Roger                             DF:          min =  27.51
                                                                      avg =  28.41
                                                                      max =  29.07
                                                         F(5, 30.73)      =   4.60
    Log restricted-likelihood =  -346.5452               Prob > F         = 0.0030
    
    -------------------------------------------------------------------------------
            sleep | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    --------------+----------------------------------------------------------------
            group |
      Medication  |     -19.75       9.74    -2.03   0.052       -39.68        0.17
                  |
            month |
               2  |      -6.19       9.01    -0.69   0.498       -24.62       12.25
               3  |     -20.98      10.69    -1.96   0.060       -42.86        0.90
                  |
      group#month |
    Medication#2  |      41.96      12.50     3.36   0.002        16.34       67.59
    Medication#3  |      48.39      14.85     3.26   0.003        17.95       78.83
                  |
            _cons |     361.38       6.89    52.46   0.000       347.29      375.46
    -------------------------------------------------------------------------------
    
    ------------------------------------------------------------------------------
      Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
    -----------------------------+------------------------------------------------
    id:                  (empty) |
    -----------------------------+------------------------------------------------
    Residual: Unstructured       |
                         var(e1) |     658.97     182.75        382.66     1134.81
                         var(e2) |     495.06     137.19        287.58      852.21
                         var(e3) |     550.86     165.99        305.17      994.35
                      cov(e1,e2) |      42.92     117.06       -186.52      272.37
                      cov(e1,e3) |    -119.12     129.78       -373.47      135.24
                      cov(e2,e3) |      65.14     124.45       -178.78      309.06
    ------------------------------------------------------------------------------
    LR test vs. linear model: chi2(5) = 1.95                  Prob > chi2 = 0.8566
    
    Note: The reported degrees of freedom assumes the null hypothesis is not on the boundary of the parameter
          space. If this is not true, then the reported test is conservative.

To obtain inferences using small-sample methods, I included the small option on the contrast command.

As before, let’s focus our attention on the first result, comparing the change in sleep from month 1 to month 2. From month 1 to month 2, the sleep time increased by 41.96 minutes more for the medication group than for the control group. That increase, tested using a test, was significant $(t(27.7) =3.36 ,p = 0.002 )$. Because we specified the small option, a $t$ test was used instead of a $z$test (a large-sample method).

``` 
  contrast ar.month#group,nowald pveffects small
```
        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -------------------------------------------------------------------------------------
                                       |   Contrast   Std. err.           df       t    P>|t|
        -------------------------------+-----------------------------------------------------
        sleep                          |
                           month#group |
        (2 vs 1) (Medication vs base)  |      41.96      12.50          27.7     3.36   0.002
        (3 vs 2) (Medication vs base)  |       6.43      12.24          24.8     0.53   0.604
        -------------------------------------------------------------------------------------

If you prefer, you could specify the dfmethod(satterthwaite) option to specify the Satterthwaite method. This method is only supported with REML estimation, so the mixed command also specifies the reml option.

``` 
  mixed sleep i.group##i.month || id:,noconstant residuals(un,t(month)) reml dfmethod(satterthwaite) nolog
```

    Mixed-effects REML regression                        Number of obs    =     80
    Group variable: id                                   Number of groups =     30
                                                         Obs per group:
                                                                      min =      1
                                                                      avg =    2.7
                                                                      max =      3
    DF method: Satterthwaite                             DF:          min =  27.51
                                                                      avg =  28.41
                                                                      max =  29.07
                                                         F(5, 27.90)      =   5.08
    Log restricted-likelihood =  -346.5452               Prob > F         = 0.0019
    
    -------------------------------------------------------------------------------
            sleep | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    --------------+----------------------------------------------------------------
            group |
      Medication  |     -19.75       9.69    -2.04   0.051       -39.56        0.05
                  |
            month |
               2  |      -6.19       8.90    -0.69   0.493       -24.41       12.03
               3  |     -20.98      10.57    -1.98   0.057       -42.62        0.66
                  |
      group#month |
    Medication#2  |      41.96      12.39     3.39   0.002        16.57       67.35
    Medication#3  |      48.39      14.71     3.29   0.003        18.23       78.55
                  |
            _cons |     361.38       6.85    52.76   0.000       347.37      375.39
    -------------------------------------------------------------------------------
    
    ------------------------------------------------------------------------------
      Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
    -----------------------------+------------------------------------------------
    id:                  (empty) |
    -----------------------------+------------------------------------------------
    Residual: Unstructured       |
                         var(e1) |     658.97     182.75        382.66     1134.81
                         var(e2) |     495.06     137.19        287.58      852.21
                         var(e3) |     550.86     165.99        305.17      994.35
                      cov(e1,e2) |      42.92     117.06       -186.52      272.37
                      cov(e1,e3) |    -119.12     129.78       -373.47      135.24
                      cov(e2,e3) |      65.14     124.45       -178.78      309.06
    ------------------------------------------------------------------------------
    LR test vs. linear model: chi2(5) = 1.95                  Prob > chi2 = 0.8566
    
    Note: The reported degrees of freedom assumes the null hypothesis is not on the boundary of the parameter
          space. If this is not true, then the reported test is conservative.

Again, let’s use the contrast command, as shown below, to compare the change in sleep across months between the medication and control group using small-sample inference methods (by including the small option). 

``` 
  contrast ar.month#group,nowald pveffects small 
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -------------------------------------------------------------------------------------
                                       |   Contrast   Std. err.           df       t    P>|t|
        -------------------------------+-----------------------------------------------------
        sleep                          |
                           month#group |
        (2 vs 1) (Medication vs base)  |      41.96      12.39          27.7     3.39   0.002
        (3 vs 2) (Medication vs base)  |       6.43      12.02          24.8     0.53   0.598
        -------------------------------------------------------------------------------------


let’s focus our attention on the first result, comparing the change in sleep from month 1 to month 2. Frommonth 1 to month 2, the sleep time increased by 41.96 minutes more for the medication group than for the control group. That increase, tested using a test, was significant $[t(27.7 = 3.39),p =0.002]$.

For overviews of methods of estimation with mixed models with small-sample sizes, see [ME] mixed, particularly the Small-sample inference for fixed effects section, as well as Yang (2015) and Schaalje, McBride, and Fellingham (2002). For more details about the dfmethod(kroger) option, see Kenward and Roger (1997). For more details about the dfmethod(satterthwaite) option, see Satterthwaite (1946).

>The residual and anova methods are only appropriate when errors are assumed to be independent, and the repeated method is appropriate for balanced repeated-measures designs with spherical correlation structures.


>REML stands for restricted maximum likelihood, also known as residual maximum likelihood. For more details, see the Likelihood versus restricted likelihood section in [ME] mixed.
