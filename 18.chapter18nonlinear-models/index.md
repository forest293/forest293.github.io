# Chapter18 ：Nonlinear models


**The chapter begins with a discussion of logistic regression models(most detailed)**
## 1 Binary logistic regression

### 1.1 A logistic model with one categorical predictor

Let’s consider a simple logistic regression model that predicts whether a person smokes (smoke) by the person’s self-reported social class (class). The variable class is a categorical variable that is coded: 1 = lower class, 2 = working class, 3 = middle class, and 4 = upper class.

```
  logit smoke i.class,nolog
```

        Logistic regression                                     Number of obs = 15,464
                                                                LR chi2(3)    = 198.45
                                                                Prob > chi2   = 0.0000
        Log likelihood = -9904.5707                             Pseudo R2     = 0.0099
        
        --------------------------------------------------------------------------------
                 smoke | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        ---------------+----------------------------------------------------------------
                 class |
        working class  |      -0.32       0.07    -4.37   0.000        -0.46       -0.18
         middle class  |      -0.72       0.07    -9.80   0.000        -0.86       -0.58
          upper class  |      -0.92       0.12    -7.54   0.000        -1.16       -0.68
                       |
                 _cons |      -0.13       0.07    -1.85   0.064        -0.26        0.01
        --------------------------------------------------------------------------------


We can interpret and visualize the results of this model using the contrast, pwcompare, margins, and marginsplot commands, as described in the following sections.


#### 1.1.1 Using the contrast command

If we want to test the overall equality of the four social class groups in terms of their log odds of smoking.

```
  logit smoke i.class,nolog
```
        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df        chi2     P>chi2
        -------------+----------------------------------
               class |          3      196.71     0.0000
        ------------------------------------------------

We can also apply contrast operators to form specific comparisons among the levels of the social class. 

```
  contrast ar.class,nowald pveffects   //(adjacent (previous) level)
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -------------------------------------------------------------------------
                                         |   Contrast   Std. err.      z    P>|z|
        ---------------------------------+---------------------------------------
                                   class |
         (working class vs lower class)  |      -0.32       0.07    -4.37   0.000
        (middle class vs working class)  |      -0.40       0.04   -11.26   0.000
          (upper class vs middle class)  |      -0.20       0.10    -1.92   0.055
        -------------------------------------------------------------------------

This test shows that the four social class groups are not all equal in terms of their log odds of smoking

We can add the **or option** to the contrast command to display the results as odds ratios.


```
  contrast ar.class,nowald pveffects or
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -------------------------------------------------------------------------
                                         | Odds ratio   Std. err.      z    P>|z|
        ---------------------------------+---------------------------------------
                                   class |
         (working class vs lower class)  |       0.73       0.05    -4.37   0.000
        (middle class vs working class)  |       0.67       0.02   -11.26   0.000
          (upper class vs middle class)  |       0.82       0.09    -1.92   0.055
        -------------------------------------------------------------------------

the results can now be interpreted using odds ratios. For example, the odds of smoking for a person who identifies as middle class is 0.669 times the odds of smoking for someone who identifies as working class.

#### 1.1.2 Using the pwcompare command

We can also use the pwcompare command to form comparisons among the levels of the social class. Like the contrast command, these comparisons are made in the logodds metric.

```
  pwcompare class,pveffects
```

        Pairwise comparisons of marginal linear predictions
        
        Margins: asbalanced
        
        -----------------------------------------------------------------------
                                       |                            Unadjusted
                                       |   Contrast   Std. err.      z    P>|z|
        -------------------------------+---------------------------------------
        smoke                          |
                                 class |
         working class vs lower class  |      -0.32       0.07    -4.37   0.000
          middle class vs lower class  |      -0.72       0.07    -9.80   0.000
           upper class vs lower class  |      -0.92       0.12    -7.54   0.000
        middle class vs working class  |      -0.40       0.04   -11.26   0.000
         upper class vs working class  |      -0.60       0.10    -5.80   0.000
          upper class vs middle class  |      -0.20       0.10    -1.92   0.055
        -----------------------------------------------------------------------

The or option can also be added to the pwcompare command to **express the results as exponentiated coefficients (that is, odds ratios).**

```
  pwcompare class,pveffects or
```

        Pairwise comparisons of marginal linear predictions
        
        Margins: asbalanced
        
        -----------------------------------------------------------------------
                                       |                            Unadjusted
                                       | Odds ratio   Std. err.      z    P>|z|
        -------------------------------+---------------------------------------
        smoke                          |
                                 class |
         working class vs lower class  |       0.73       0.05    -4.37   0.000
          middle class vs lower class  |       0.49       0.04    -9.80   0.000
           upper class vs lower class  |       0.40       0.05    -7.54   0.000
        middle class vs working class  |       0.67       0.02   -11.26   0.000
         upper class vs working class  |       0.55       0.06    -5.80   0.000
          upper class vs middle class  |       0.82       0.09    -1.92   0.055
        -----------------------------------------------------------------------


#### 1.1.3 Using the margins and marginsplot commands

Let’s use the margins command to show the predictive margin for the probabilityof smoking separated by class. 

```
  margins class,nopvalues
```

        Adjusted predictions                                    Number of obs = 15,464
        Model VCE: OIM
        
        Expression: Pr(smoke), predict()
        
        ----------------------------------------------------------------
                       |            Delta-method
                       |     Margin   std. err.     [95% conf. interval]
        ---------------+------------------------------------------------
                 class |
          lower class  |       0.47       0.02          0.43        0.50
        working class  |       0.39       0.01          0.38        0.40
         middle class  |       0.30       0.01          0.29        0.31
          upper class  |       0.26       0.02          0.22        0.30
        ----------------------------------------------------------------

>Note! Predictive margins
>>When discussing linear models, the term adjusted mean was used to describe the predicted mean of the outcome after adjusting for the covariates in the model. **A predictive margin is a generalization of an adjusted mean applied to a nonlinear model (such as a logistic regression model).**

We can use the marginsplot command to make a graph of these predictive margins as a function of class. 


```
  marginsplot,xlabel(,angle(45))
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.9.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Predictive margins for the probability of smoking by social class</strong></figcaption>
</figure>

Suppose we apply the ar. contrast coefficient to class using the margins command. This forms adjacent group comparisons on class, comparing each class group with the previous class.

```
  margins ar.class,contrast(nowald pveffects)
```

        Contrasts of adjusted predictions                       Number of obs = 15,464
        Model VCE: OIM
        
        Expression: Pr(smoke), predict()
        
        -------------------------------------------------------------------------
                                         |            Delta-method
                                         |   Contrast   std. err.      z    P>|z|
        ---------------------------------+---------------------------------------
                                   class |
         (working class vs lower class)  |      -0.08       0.02    -4.31   0.000
        (middle class vs working class)  |      -0.09       0.01   -11.34   0.000
          (upper class vs middle class)  |      -0.04       0.02    -2.00   0.046
        -------------------------------------------------------------------------


Comparing the results of the margins ar.class command with the contrast ar.class command, we see that the $z$-values and $p$-values are similar, but not identical. For example, the $p$-value is 0.055 for the comparison of upper class versus middle class from the contrast command and is 0.046 for the same comparison from the margins command. This underscores the fact that **the contrast command performed its test using the log-oddsmetric whereas the margins command used the probability metric.**

You can use the **predict(xb)** option with the margins command to request the use of the linear (log-odds) metric

```
  margins ar.class,contrast(nowald pveffects) predict(xb)  
```

        Contrasts of adjusted predictions                       Number of obs = 15,464
        Model VCE: OIM
        
        Expression: Linear prediction (log odds), predict(xb)
        
        -------------------------------------------------------------------------
                                         |            Delta-method
                                         |   Contrast   std. err.      z    P>|z|
        ---------------------------------+---------------------------------------
                                   class |
         (working class vs lower class)  |      -0.32       0.07    -4.37   0.000
        (middle class vs working class)  |      -0.40       0.04   -11.26   0.000
          (upper class vs middle class)  |      -0.20       0.10    -1.92   0.055
        -------------------------------------------------------------------------

>Note! The default metric for the margins command
>>The margins command used predicted probabilities by default following a logistic regression. We can discover the default prediction metric used by margins after logit by typing help logit postestimation, and then clicking on the Jump to button and selecting margins. This shows us that the default prediction is pr, the predicted probability of the outcome. It also shows us that **we can specify xb to obtain the linear prediction (that is, the logit). You can use this strategy for discovering the default prediction for the margins command following any estimation command, as well as discovering other metrics you can use for the margins command.** Note that some of the options available for the predict command (for example, stdp) are not appropriate for the margins command.

#### 1.1.4 Using the margins command with the pwcompare option

The margins command can be combined with the pwcompare option to bring you the combination of features provided by the margins and pwcompare commands. This allows you to **form pairwise comparisons in any of the metrics supported by the margins command.**

```
  margins class,pwcompare
```

        Pairwise comparisons of adjusted predictions            Number of obs = 15,464
        Model VCE: OIM
        
        Expression: Pr(smoke), predict()
        
        --------------------------------------------------------------------------------
                                       |            Delta-method         Unadjusted
                                       |   Contrast   std. err.     [95% conf. interval]
        -------------------------------+------------------------------------------------
                                 class |
         working class vs lower class  |      -0.08       0.02         -0.11       -0.04
          middle class vs lower class  |      -0.17       0.02         -0.20       -0.13
           upper class vs lower class  |      -0.21       0.03         -0.26       -0.16
        middle class vs working class  |      -0.09       0.01         -0.11       -0.07
         upper class vs working class  |      -0.13       0.02         -0.17       -0.09
          upper class vs middle class  |      -0.04       0.02         -0.08       -0.00
        --------------------------------------------------------------------------------


To display the significance tests of the pairwise comparisons, in lieu of the confidence intervals, we can use the pwcompare(pveffects) option. Note that pveffects is specified as a suboption to the pwcompare() option. 

```
  margins class,pwcompare(pveffects) 
```

        Pairwise comparisons of adjusted predictions            Number of obs = 15,464
        Model VCE: OIM
        
        Expression: Pr(smoke), predict()
        
        -----------------------------------------------------------------------
                                       |            Delta-method    Unadjusted
                                       |   Contrast   std. err.      z    P>|z|
        -------------------------------+---------------------------------------
                                 class |
         working class vs lower class  |      -0.08       0.02    -4.31   0.000
          middle class vs lower class  |      -0.17       0.02    -9.37   0.000
           upper class vs lower class  |      -0.21       0.03    -8.06   0.000
        middle class vs working class  |      -0.09       0.01   -11.34   0.000
         upper class vs working class  |      -0.13       0.02    -6.46   0.000
          upper class vs middle class  |      -0.04       0.02    -2.00   0.046
        -----------------------------------------------------------------------

All six of the pairwise comparisons among the levels of class are statistically significant in **the predicted probability metric.**

You can request that the table be sorted by the size of the difference in the contrast by adding the sort suboption within the pwcompare() option

```
  margins class,pwcompare(pveffects sort)  //be sorted by the size of the difference in the contrast
```

        Pairwise comparisons of adjusted predictions            Number of obs = 15,464
        Model VCE: OIM
        
        Expression: Pr(smoke), predict()
        
        -----------------------------------------------------------------------
                                       |            Delta-method    Unadjusted
                                       |   Contrast   std. err.      z    P>|z|
        -------------------------------+---------------------------------------
                                 class |
           upper class vs lower class  |      -0.21       0.03    -8.06   0.000
          middle class vs lower class  |      -0.17       0.02    -9.37   0.000
         upper class vs working class  |      -0.13       0.02    -6.46   0.000
        middle class vs working class  |      -0.09       0.01   -11.34   0.000
         working class vs lower class  |      -0.08       0.02    -4.31   0.000
          upper class vs middle class  |      -0.04       0.02    -2.00   0.046
        -----------------------------------------------------------------------
        
### 1.2 A logistic model with one continuous predictor

Let’s now briefly consider a model with one continuous predictor, predicting whether a person smokes (smoke) from his or her education level. 


```
  use gss_ivrm.dta
  logit smoke educ,nolog
```

    Logistic regression                                     Number of obs = 16,332
                                                            LR chi2(1)    = 174.04
                                                            Prob > chi2   = 0.0000
    Log likelihood = -10483.854                             Pseudo R2     = 0.0082
    
    ------------------------------------------------------------------------------
           smoke | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            educ |      -0.07       0.01   -13.10   0.000        -0.08       -0.06
           _cons |       0.22       0.07     3.36   0.001         0.09        0.35
    ------------------------------------------------------------------------------

Let’s use the margins and marginsplot commands to visualize the relationship between education and the log odds of smoking. (Note the inclusion of the predict(xb) option on the margins command to specify the use of the log-odds metric.) 

```
  margins,at(educ=(5(1)20))predict(xb) 
```

        Adjusted predictions                                    Number of obs = 16,332
        Model VCE: OIM
        
        Expression: Linear prediction (log odds), predict(xb)
        1._at:  educ =  5
        2._at:  educ =  6
        3._at:  educ =  7
        4._at:  educ =  8
        5._at:  educ =  9
        6._at:  educ = 10
        7._at:  educ = 11
        8._at:  educ = 12
        9._at:  educ = 13
        10._at: educ = 14
        11._at: educ = 15
        12._at: educ = 16
        13._at: educ = 17
        14._at: educ = 18
        15._at: educ = 19
        16._at: educ = 20
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 _at |
                  1  |      -0.12       0.04    -2.95   0.003        -0.20       -0.04
                  2  |      -0.19       0.04    -5.22   0.000        -0.26       -0.12
                  3  |      -0.26       0.03    -8.13   0.000        -0.32       -0.20
                  4  |      -0.33       0.03   -11.91   0.000        -0.38       -0.27
                  5  |      -0.40       0.02   -16.84   0.000        -0.44       -0.35
                  6  |      -0.46       0.02   -23.09   0.000        -0.50       -0.42
                  7  |      -0.53       0.02   -30.20   0.000        -0.57       -0.50
                  8  |      -0.60       0.02   -36.37   0.000        -0.63       -0.57
                  9  |      -0.67       0.02   -39.31   0.000        -0.70       -0.64
                 10  |      -0.74       0.02   -38.80   0.000        -0.78       -0.70
                 11  |      -0.81       0.02   -36.50   0.000        -0.85       -0.76
                 12  |      -0.88       0.03   -33.82   0.000        -0.93       -0.82
                 13  |      -0.94       0.03   -31.36   0.000        -1.00       -0.88
                 14  |      -1.01       0.03   -29.27   0.000        -1.08       -0.94
                 15  |      -1.08       0.04   -27.52   0.000        -1.16       -1.00
                 16  |      -1.15       0.04   -26.08   0.000        -1.24       -1.06
        ------------------------------------------------------------------------------

```
  marginsplot
```
Note how the relationship between education and the **log odds** of smoking is linear. For every additional year of education, the log odds of smoking decreases by 0.07.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.10.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Log odds of smoking by education level</strong></figcaption>
</figure>




Let’s now visualize this relationship in terms of the **probability** of smoking.

```
  margins,at(educ=(5(1)20)) 
```

        Adjusted predictions                                    Number of obs = 16,332
        Model VCE: OIM
        
        Expression: Pr(smoke), predict()
        1._at:  educ =  5
        2._at:  educ =  6
        3._at:  educ =  7
        4._at:  educ =  8
        5._at:  educ =  9
        6._at:  educ = 10
        7._at:  educ = 11
        8._at:  educ = 12
        9._at:  educ = 13
        10._at: educ = 14
        11._at: educ = 15
        12._at: educ = 16
        13._at: educ = 17
        14._at: educ = 18
        15._at: educ = 19
        16._at: educ = 20
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 _at |
                  1  |       0.47       0.01    45.90   0.000         0.45        0.49
                  2  |       0.45       0.01    50.26   0.000         0.44        0.47
                  3  |       0.44       0.01    55.77   0.000         0.42        0.45
                  4  |       0.42       0.01    62.72   0.000         0.41        0.43
                  5  |       0.40       0.01    71.30   0.000         0.39        0.41
                  6  |       0.39       0.00    81.08   0.000         0.38        0.40
                  7  |       0.37       0.00    90.01   0.000         0.36        0.38
                  8  |       0.35       0.00    93.68   0.000         0.35        0.36
                  9  |       0.34       0.00    88.76   0.000         0.33        0.35
                 10  |       0.32       0.00    77.69   0.000         0.32        0.33
                 11  |       0.31       0.00    65.44   0.000         0.30        0.32
                 12  |       0.29       0.01    54.74   0.000         0.28        0.30
                 13  |       0.28       0.01    46.16   0.000         0.27        0.29
                 14  |       0.27       0.01    39.41   0.000         0.25        0.28
                 15  |       0.25       0.01    34.10   0.000         0.24        0.27
                 16  |       0.24       0.01    29.87   0.000         0.22        0.26
        ------------------------------------------------------------------------------

```
  marginsplot
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.11.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Predicted probability of smoking by education level</strong></figcaption>
</figure>

### 1.3 A logistic model with covariates

Let’s add some covariates to this model, predicting smoking from class as well as education, age, and year of interview

```
  logit smoke i.class educ age yrint,nolog  
```

        Logistic regression                                     Number of obs = 15,375
                                                                LR chi2(6)    = 742.33
                                                                Prob > chi2   = 0.0000
        Log likelihood = -9580.0723                             Pseudo R2     = 0.0373
        
        --------------------------------------------------------------------------------
                 smoke | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        ---------------+----------------------------------------------------------------
                 class |
        working class  |      -0.26       0.07    -3.47   0.001        -0.41       -0.11
         middle class  |      -0.46       0.08    -5.89   0.000        -0.61       -0.30
          upper class  |      -0.52       0.13    -4.11   0.000        -0.77       -0.27
                       |
                  educ |      -0.09       0.01   -13.88   0.000        -0.10       -0.08
                   age |      -0.02       0.00   -18.12   0.000        -0.02       -0.02
                 yrint |      -0.03       0.00    -9.48   0.000        -0.04       -0.03
                 _cons |      65.46       6.72     9.74   0.000        52.29       78.63
        --------------------------------------------------------------------------------


```
  contrast class //test the overall effect of class
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df        chi2     P>chi2
        -------------+----------------------------------
               class |          3       49.68     0.0000
        ------------------------------------------------

We can use the margins command to help us interpret this effect by computing the predictive margins of the probability of smoking by class, as shown below.

```
  margins class,nopvalues 
```

        Predictive margins                                      Number of obs = 15,375
        Model VCE: OIM
        
        Expression: Pr(smoke), predict()
        
        ----------------------------------------------------------------
                       |            Delta-method
                       |     Margin   std. err.     [95% conf. interval]
        ---------------+------------------------------------------------
                 class |
          lower class  |       0.43       0.02          0.39        0.46
        working class  |       0.37       0.01          0.36        0.38
         middle class  |       0.32       0.01          0.31        0.34
          upper class  |       0.31       0.02          0.27        0.35
        ----------------------------------------------------------------

We can use marginsplot command to graph these predictive margins

```
  marginsplot,xlabel(,angle(45))  
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.12.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>The predictive marginal probability of smoking by class(adding covariance)</strong></figcaption>
</figure>

**In the predicted probability metric, the size of the effect of a variable can (and will) vary as a function of the value of the covariates. By comparison, in the logit metric (like any linear model), the size of the effect of a variable remains constant regardless of the values of the covariate.**


Let’s explore this point by using the contrast command to estimate the effect of class.
```
  contrast ar.class,nowald pveffects  //Compare each level of class with the previous level of class
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -------------------------------------------------------------------------
                                         |   Contrast   Std. err.      z    P>|z|
        ---------------------------------+---------------------------------------
                                   class |
         (working class vs lower class)  |      -0.26       0.07    -3.47   0.001
        (middle class vs working class)  |      -0.19       0.04    -5.14   0.000
          (upper class vs middle class)  |      -0.07       0.11    -0.64   0.523
        -------------------------------------------------------------------------

These differences are computed and expressed in the **log-odds metric**, the natural (linear) metric for the model. The magnitude of these group differences and their significance would remain constant at any level of the covariates.

Let’s form these same comparisons but instead using the margins command, forming the comparisons using the **predicted probability metric.**

```
  margins ar.class,contrast(nowald pveffects) 
```

    Contrasts of predictive margins                         Number of obs = 15,375
    Model VCE: OIM
    
    Expression: Pr(smoke), predict()
    
    -------------------------------------------------------------------------
                                     |            Delta-method
                                     |   Contrast   std. err.      z    P>|z|
    ---------------------------------+---------------------------------------
                               class |
     (working class vs lower class)  |      -0.06       0.02    -3.41   0.001
    (middle class vs working class)  |      -0.04       0.01    -5.14   0.000
      (upper class vs middle class)  |      -0.01       0.02    -0.65   0.519
    -------------------------------------------------------------------------
    
Let’s repeat this command but specify that we want to estimate the effects at twodifferent values (7 and 18 years) of education

```
margins ar.class,contrast(nowald pveffects)at(educ=(7 18))
```

        Contrasts of predictive margins                         Number of obs = 15,375
        Model VCE: OIM
        
        Expression: Pr(smoke), predict()
        1._at: educ =  7
        2._at: educ = 18
        
        ---------------------------------------------------------------------------
                                           |            Delta-method
                                           |   Contrast   std. err.      z    P>|z|
        -----------------------------------+---------------------------------------
                                 class@_at |
         (working class vs lower class) 1  |      -0.06       0.02    -3.49   0.000
         (working class vs lower class) 2  |      -0.05       0.02    -3.32   0.001
        (middle class vs working class) 1  |      -0.05       0.01    -5.16   0.000
        (middle class vs working class) 2  |      -0.04       0.01    -5.02   0.000
          (upper class vs middle class) 1  |      -0.02       0.03    -0.64   0.521
          (upper class vs middle class) 2  |      -0.01       0.02    -0.65   0.516
        ---------------------------------------------------------------------------
        

Let’s assess the contrasts on class, but this time doing so **when the covariates education, age, and year of interview are held constant at the 25th percentile.** Then, let’s perform the same contrasts, holding these covariates each at the 50th percentile and at the 75th percentile. These tests are performed using the margins command below.

```
  margins ar.class,contrast(nowald pveffects) at((p25) educ age yrint) ///
                                              at((p50) educ age yrint) ///
											                        at((p75) educ age yrint) 
```

    Contrasts of adjusted predictions                       Number of obs = 15,375
    Model VCE: OIM
    
    Expression: Pr(smoke), predict()
    1._at: educ  =   11 (p25)
           age   =   30 (p25)
           yrint = 1980 (p25)
    2._at: educ  =   12 (p50)
           age   =   41 (p50)
           yrint = 1986 (p50)
    3._at: educ  =   14 (p75)
           age   =   59 (p75)
           yrint = 1989 (p75)
    
    ---------------------------------------------------------------------------
                                       |            Delta-method
                                       |   Contrast   std. err.      z    P>|z|
    -----------------------------------+---------------------------------------
                             class@_at |
     (working class vs lower class) 1  |      -0.06       0.02    -3.51   0.000
     (working class vs lower class) 2  |      -0.06       0.02    -3.42   0.001
     (working class vs lower class) 3  |      -0.05       0.02    -3.31   0.001
    (middle class vs working class) 1  |      -0.05       0.01    -5.16   0.000
    (middle class vs working class) 2  |      -0.04       0.01    -5.17   0.000
    (middle class vs working class) 3  |      -0.03       0.01    -5.06   0.000
      (upper class vs middle class) 1  |      -0.02       0.03    -0.64   0.521
      (upper class vs middle class) 2  |      -0.02       0.02    -0.65   0.518
      (upper class vs middle class) 3  |      -0.01       0.02    -0.65   0.516
    ---------------------------------------------------------------------------

 As we have seen in these examples, the **at() option allows us to easily explore the extent to which the effect of class (in the probability metric) differs as a function of the value of the covariates.**

>Note! The margins command and the pwcompare option
>>**You can use the margins command and the at() option combined with the pwcompare option to obtain pairwise comparisons of predicted probabilities while holding predictors constant at the values specified by the at() option.** For example, the following command computes pairwise comparisons among the class groups for the predicted probability ofsmoking when education is held constant at 7 years. 

>>margins class, pwcompare(effects) at(educ=7)


>Compare this with the pwcompare command that **was limited only to the natural metric of the model.** In the case of logistic regression, it is limited only to the logodds metric, or odds-ratios via the or option.

## 2 Multinomial logistic regression

Let’s now consider a multinomial logistic regression model, focusing on how the commands contrast, pwcompare, margins, and marginsplot can be used after fitting such a model. 

Let’s model this happiness rating as a function of gender, class, education, and year of interview. 

The **mlogit** command chooses the most frequent outcome (which was the second outcome, pretty happy) as the base outcome.

```
  use gss_ivrm.dta

  mlogit haprate i.gender i.class educ yrint,nolog
```

    Multinomial logistic regression                        Number of obs =  48,409
                                                           LR chi2(12)   = 2076.07
                                                           Prob > chi2   =  0.0000
    Log likelihood = -44799.143                            Pseudo R2     =  0.0226
    
    --------------------------------------------------------------------------------
           haprate | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    ---------------+----------------------------------------------------------------
    not_too_happy  |
            gender |
           Female  |       0.02       0.03     0.67   0.502        -0.04        0.08
                   |
             class |
    working class  |      -0.99       0.05   -20.60   0.000        -1.08       -0.90
     middle class  |      -1.19       0.05   -23.33   0.000        -1.29       -1.09
      upper class  |      -0.89       0.10    -8.65   0.000        -1.09       -0.69
                   |
              educ |      -0.08       0.00   -16.13   0.000        -0.09       -0.07
             yrint |       0.00       0.00     2.55   0.011         0.00        0.01
             _cons |      -6.38       2.65    -2.41   0.016       -11.59       -1.18
    ---------------+----------------------------------------------------------------
    pretty_happy   |  (base outcome)
    ---------------+----------------------------------------------------------------
    very_happy     |
            gender |
           Female  |       0.07       0.02     3.47   0.001         0.03        0.11
                   |
             class |
    working class  |       0.33       0.06     5.82   0.000         0.22        0.44
     middle class  |       0.74       0.06    12.91   0.000         0.63        0.85
      upper class  |       1.18       0.08    15.25   0.000         1.03        1.33
                   |
              educ |      -0.00       0.00    -0.04   0.966        -0.01        0.01
             yrint |      -0.01       0.00    -6.59   0.000        -0.01       -0.00
             _cons |      11.27       1.88     5.99   0.000         7.58       14.96
    --------------------------------------------------------------------------------

I find it hard to interpret the model using the coefficients. I find **it far easier to interpret the results using the contrast, pwcompare, margins, and marginsplot commands.**

We can use the contrast command to obtain the overall effect of class. By default, this test is performed for the first equation (that is, for outcome 1, not too happy). This test is significant.

```
  contrast class
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df        chi2     P>chi2
        -------------+----------------------------------
        not_too_ha~y |
               class |          3      558.71     0.0000
        ------------------------------------------------

Adding the equation(3) option performs the contrast with respect to the third outcome (that is, very happy). This test is also significant.

```
  contrast class,equation(3) 
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df        chi2     P>chi2
        -------------+----------------------------------
        very_happy   |
               class |          3      564.26     0.0000
        ------------------------------------------------


The atequations option can be used to apply the contrast command with respect to all the equations. **The output of this command matches what we saw in the previous two contrast commands.**

```
  contrast class,atequations
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df        chi2     P>chi2
        -------------+----------------------------------
        not_too_ha~y |
               class |          3      558.71     0.0000
        -------------+----------------------------------
        pretty_happy |
               class |  (omitted)
        -------------+----------------------------------
        very_happy   |
               class |          3      564.26     0.0000
        ------------------------------------------------


We can use contrast operators with the contrast command to make specific comparisons among groups. In the example below, the ar. contrast operator is used to compare adjacent levels of class for the third equation. I also included **the rrr option to interpret the results in terms of relative-risk ratios.** Each of these contrasts is significant.

```
  contrast ar.class,equation(3) nowald pveffects rrr  
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -------------------------------------------------------------------------
                                         |        RRR   Std. err.      z    P>|z|
        ---------------------------------+---------------------------------------
        very_happy                       |
                                   class |
         (working class vs lower class)  |       1.39       0.08     5.82   0.000
        (middle class vs working class)  |       1.50       0.03    18.72   0.000
          (upper class vs middle class)  |       1.55       0.09     7.94   0.000
        -------------------------------------------------------------------------
        
**The pwcompare command can be used to obtain pairwise comparisons among the four class groups. The equation(3) option specifies that these pairwise comparisons should be made for the third equation, and the rrr option requests the results in terms of relative-risk ratios.** 

Focusing on the third contrast, the odds of rating oneself as very happy is 3.25 times greater for those who self-identify as upper class versus lower class.

```
  pwcompare class,equation(3) pveffects
```

        Pairwise comparisons of marginal linear predictions
        
        Margins: asbalanced
        
        -----------------------------------------------------------------------
                                       |                            Unadjusted
                                       |   Contrast   Std. err.      z    P>|z|
        -------------------------------+---------------------------------------
        very_happy                     |
                                 class |
         working class vs lower class  |       0.33       0.06     5.82   0.000
          middle class vs lower class  |       0.74       0.06    12.91   0.000
           upper class vs lower class  |       1.18       0.08    15.25   0.000
        middle class vs working class  |       0.41       0.02    18.72   0.000
         upper class vs working class  |       0.85       0.06    15.12   0.000
          upper class vs middle class  |       0.44       0.06     7.94   0.000
        -----------------------------------------------------------------------


When using the margins command, the default is to compute the predicted probability for every equation. (For more information, see [R] margins.) The margins command below computes, by class, the predictive margin of the probability of being not too happy (that is, outcome 1), being pretty happy (that is, outcome 2), and being very happy (that is, outcome 3). For example, the predictive margin of the probability of being very happy (outcome 3) for those who are upper class is 0.461, the last line of the output below

```
  margins class,nopvalues
```

        Predictive margins                                      Number of obs = 48,409
        Model VCE: OIM
        
        1._predict: Pr(haprate==not_too_happy), predict(pr outcome(1))
        2._predict: Pr(haprate==pretty_happy), predict(pr outcome(2))
        3._predict: Pr(haprate==very_happy), predict(pr outcome(3))
        
        ------------------------------------------------------------------
                         |            Delta-method
                         |     Margin   std. err.     [95% conf. interval]
        -----------------+------------------------------------------------
          _predict#class |
          1#lower class  |       0.30       0.01          0.28        0.32
        1#working class  |       0.13       0.00          0.12        0.13
         1#middle class  |       0.09       0.00          0.09        0.10
          1#upper class  |       0.10       0.01          0.09        0.12
          2#lower class  |       0.53       0.01          0.51        0.55
        2#working class  |       0.60       0.00          0.59        0.61
         2#middle class  |       0.54       0.00          0.53        0.54
          2#upper class  |       0.44       0.01          0.41        0.46
          3#lower class  |       0.17       0.01          0.16        0.19
        3#working class  |       0.27       0.00          0.27        0.28
         3#middle class  |       0.37       0.00          0.36        0.37
          3#upper class  |       0.46       0.01          0.44        0.49
        ------------------------------------------------------------------


The marginsplot command is used below to graph the results of the previous margins command.

Using the s1mono scheme, different line patterns are used to graph each of the outcomes. The dotted line shows how the rating of being very happy increases as a function of self-identified class.

```
  marginsplot,plotdim(,labels("Not too happy" "pretty happy" "very happy"))xlabel(,angle(45)) scheme(s1mono) 
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.13.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>The predictive marginal probability of being not too happy, pretty happy, and very happy by self-identified social class</strong></figcaption>
</figure>

The margins command below is used to compute the predictive margin of the probability of being not too happy (the first outcome) for those with 5 to 18 years of education. 

```
  margins,predict(outcome(1))at(educ=(5(1)18))
  marginsplot
```
        
        Predictive margins                                      Number of obs = 48,409
        Model VCE: OIM
        
        Expression: Pr(haprate==not_too_happy), predict(outcome(1))
        1._at:  educ =  5
        2._at:  educ =  6
        3._at:  educ =  7
        4._at:  educ =  8
        5._at:  educ =  9
        6._at:  educ = 10
        7._at:  educ = 11
        8._at:  educ = 12
        9._at:  educ = 13
        10._at: educ = 14
        11._at: educ = 15
        12._at: educ = 16
        13._at: educ = 17
        14._at: educ = 18
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 _at |
                  1  |       0.20       0.01    35.88   0.000         0.18        0.21
                  2  |       0.18       0.00    40.10   0.000         0.17        0.19
                  3  |       0.17       0.00    45.44   0.000         0.17        0.18
                  4  |       0.16       0.00    52.22   0.000         0.16        0.17
                  5  |       0.15       0.00    60.67   0.000         0.15        0.16
                  6  |       0.14       0.00    70.43   0.000         0.14        0.15
                  7  |       0.13       0.00    79.37   0.000         0.13        0.14
                  8  |       0.13       0.00    82.97   0.000         0.12        0.13
                  9  |       0.12       0.00    78.30   0.000         0.11        0.12
                 10  |       0.11       0.00    68.33   0.000         0.11        0.11
                 11  |       0.10       0.00    57.73   0.000         0.10        0.11
                 12  |       0.10       0.00    48.65   0.000         0.09        0.10
                 13  |       0.09       0.00    41.43   0.000         0.09        0.09
                 14  |       0.08       0.00    35.76   0.000         0.08        0.09
        ------------------------------------------------------------------------------



<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.14.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>The predictive marginal probability of being not too happy by education</strong></figcaption>
</figure>

## 3 Ordinal logistic regression

Let’s use the variable haprate from the previous section as the outcome but now model it using an ordinal logistic regression. Let’s use the ologit command to model the three-level variable haprate (1 = not too happy, 2 = pretty happy, and 3 = very happy) as a function of gender, class, education, and year of interview.

```
  use gss_ivrm.dta
  ologit haprate i.gender i.class educ yrint,nolog
```

    Ordered logistic regression                            Number of obs =  48,409
                                                           LR chi2(6)    = 1799.46
                                                           Prob > chi2   =  0.0000
    Log likelihood = -44937.445                            Pseudo R2     =  0.0196
    
    --------------------------------------------------------------------------------
           haprate | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    ---------------+----------------------------------------------------------------
            gender |
           Female  |       0.06       0.02     3.34   0.001         0.02        0.10
                   |
             class |
    working class  |       0.96       0.04    23.15   0.000         0.88        1.04
     middle class  |       1.36       0.04    32.12   0.000         1.28        1.44
      upper class  |       1.66       0.06    25.75   0.000         1.54        1.79
                   |
              educ |       0.03       0.00    10.55   0.000         0.03        0.04
             yrint |      -0.01       0.00    -7.84   0.000        -0.01       -0.00
    ---------------+----------------------------------------------------------------
             /cut1 |     -13.41       1.65                        -16.64      -10.19
             /cut2 |     -10.60       1.65                        -13.82       -7.37
    --------------------------------------------------------------------------------
    

I will bypass interpreting the coefficients and briefly illustrate the use of the contrast, pwcompare, margins, and marginsplot commands.

First, let’s consider the contrast command. The contrast command below tests the overall effect of class.

```
  contrast class  //test the overall effect of class
```

    Contrasts of marginal linear predictions
    
    Margins: asbalanced
    
    ------------------------------------------------
                 |         df        chi2     P>chi2
    -------------+----------------------------------
    haprate      |
           class |          3     1286.31     0.0000
    ------------------------------------------------


We can further dissect the overall effect of class through the use of contrast operators. The contrast command below uses the ar. contrast operator to compare each level of class with the previous level.

```
  contrast ar.class,nowald pveffects eform //Compare each level of class with the previous level.
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -------------------------------------------------------------------------
                                         |     exp(b)   Std. err.      z    P>|z|
        ---------------------------------+---------------------------------------
        haprate                          |
                                   class |
         (working class vs lower class)  |       2.61       0.11    23.15   0.000
        (middle class vs working class)  |       1.50       0.03    20.95   0.000
          (upper class vs middle class)  |       1.35       0.07     5.86   0.000
        -------------------------------------------------------------------------

The exponentiated coefficients can be very abstract. Instead, let’s compute the predictive marginal probability of being very happy (the third response) as a function of self-identified social class. The predictive marginal probability of rating oneself as very happy was 43.7% for those identifying themselves as upper class.

```
  margins class,predict(pr outcome(3)) 
```

        Predictive margins                                      Number of obs = 48,409
        Model VCE: OIM
        
        Expression: Pr(haprate==3), predict(pr outcome(3))
        
        --------------------------------------------------------------------------------
                       |            Delta-method
                       |     Margin   std. err.      z    P>|z|     [95% conf. interval]
        ---------------+----------------------------------------------------------------
                 class |
          lower class  |       0.13       0.00    28.33   0.000         0.12        0.14
        working class  |       0.28       0.00    97.08   0.000         0.27        0.28
         middle class  |       0.37       0.00   115.21   0.000         0.36        0.37
          upper class  |       0.44       0.01    35.61   0.000         0.41        0.46
        --------------------------------------------------------------------------------


By applying the ar. contrast operator, we can obtain comparisons among the **adjacent levels of social class.**

```
  margins ar.class,predict(pr outcome(3)) contrast(pveffects nowald)  //Comparisons among the adjacent levels of social class.
```
    Contrasts of predictive margins                         Number of obs = 48,409
    Model VCE: OIM
    
    Expression: Pr(haprate==3), predict(pr outcome(3))
    
    -------------------------------------------------------------------------
                                     |            Delta-method
                                     |   Contrast   std. err.      z    P>|z|
    ---------------------------------+---------------------------------------
                               class |
     (working class vs lower class)  |       0.15       0.01    29.32   0.000
    (middle class vs working class)  |       0.09       0.00    21.00   0.000
      (upper class vs middle class)  |       0.07       0.01     5.73   0.000
    -------------------------------------------------------------------------
    
The margins command below is used to compute the predictive margin of the probability of each of the three happiness ratings for those with 5 to 18 years of education. 

```
  margins,at(educ=(5(1)18)) 
  marginsplot,plotdim(,label("Not too happy""Pretty happy""Very happy"))
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.15.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong> Probability of being very unhappy by education</strong></figcaption>
</figure>

## 4 Poisson regression

Let’s now briefly consider a Poisson model, showing the use of the contrast, pwcompare, margins, and marginsplot commands following the use of the poisson command. 

Let’s fit a model predicting the number of children a person has from gender, class, education, and year of interview

```
  use gss_ivrm.dta
  poisson children i.gender i.class educ yrint
```

Iteration 0:  Log likelihood =  -95914.71  
Iteration 1:  Log likelihood = -95914.709  

        Poisson regression                                     Number of obs =  51,417
                                                               LR chi2(6)    = 5773.21
                                                               Prob > chi2   =  0.0000
        Log likelihood = -95914.709                            Pseudo R2     =  0.0292
        
        --------------------------------------------------------------------------------
              children | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        ---------------+----------------------------------------------------------------
                gender |
               Female  |       0.13       0.01    20.47   0.000         0.12        0.14
                       |
                 class |
        working class  |      -0.09       0.01    -7.18   0.000        -0.12       -0.07
         middle class  |      -0.05       0.01    -3.58   0.000        -0.07       -0.02
          upper class  |       0.06       0.02     2.87   0.004         0.02        0.10
                       |
                  educ |      -0.07       0.00   -68.73   0.000        -0.07       -0.07
                 yrint |      -0.00       0.00    -3.75   0.000        -0.00       -0.00
                 _cons |       3.68       0.58     6.38   0.000         2.55        4.82
        --------------------------------------------------------------------------------


As we have seen before, the contrast command can be used to test the overall effect of class. 
        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df        chi2     P>chi2
        -------------+----------------------------------
               class |          3      129.31     0.0000
        ------------------------------------------------

The ar. contrast operator is used to compare adjacent levels of class, comparing each class with the previous class.
```
  contrast ar.class,nowald pveffects  //compare the adjacent levels of class,comparing each class with the previous class
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -------------------------------------------------------------------------
                                         |   Contrast   Std. err.      z    P>|z|
        ---------------------------------+---------------------------------------
                                   class |
         (working class vs lower class)  |      -0.09       0.01    -7.18   0.000
        (middle class vs working class)  |       0.05       0.01     6.69   0.000
          (upper class vs middle class)  |       0.11       0.02     6.00   0.000
        -------------------------------------------------------------------------


We can use the pwcompare command to form pairwise comparisons among the four class groups.

```
  pwcompare class,pveffects
```

        Pairwise comparisons of marginal linear predictions
        
        Margins: asbalanced
        
        -----------------------------------------------------------------------
                                       |                            Unadjusted
                                       |   Contrast   Std. err.      z    P>|z|
        -------------------------------+---------------------------------------
        children                       |
                                 class |
         working class vs lower class  |      -0.09       0.01    -7.18   0.000
          middle class vs lower class  |      -0.05       0.01    -3.58   0.000
           upper class vs lower class  |       0.06       0.02     2.87   0.004
        middle class vs working class  |       0.05       0.01     6.69   0.000
         upper class vs working class  |       0.15       0.02     8.48   0.000
          upper class vs middle class  |       0.11       0.02     6.00   0.000
        -----------------------------------------------------------------------


**When using the margins command, the default is to compute the predicted number of events.** The margins command below computes the predicted number of children by class.

```
  margins class,nopvalues   //compute the predicted number of children class
```

        Predictive margins                                      Number of obs = 51,417
        Model VCE: OIM
        
        Expression: Predicted number of events, predict()
        
        ----------------------------------------------------------------
                       |            Delta-method
                       |     Margin   std. err.     [95% conf. interval]
        ---------------+------------------------------------------------
                 class |
          lower class  |       2.08       0.03          2.03        2.12
        working class  |       1.89       0.01          1.87        1.91
         middle class  |       1.98       0.01          1.96        2.00
          upper class  |       2.21       0.04          2.13        2.28
        ----------------------------------------------------------------

The margins command is used to compute the predicted number of children for those with 5 to 18 years of education.

```
  margins,at(educ=(5(1)18))
  marginsplot
```

        Predictive margins                                      Number of obs = 51,417
        Model VCE: OIM
        
        Expression: Predicted number of events, predict()
        1._at:  educ =  5
        2._at:  educ =  6
        3._at:  educ =  7
        4._at:  educ =  8
        5._at:  educ =  9
        6._at:  educ = 10
        7._at:  educ = 11
        8._at:  educ = 12
        9._at:  educ = 13
        10._at: educ = 14
        11._at: educ = 15
        12._at: educ = 16
        13._at: educ = 17
        14._at: educ = 18
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 _at |
                  1  |       3.26       0.03   127.61   0.000         3.21        3.31
                  2  |       3.04       0.02   144.40   0.000         3.00        3.08
                  3  |       2.83       0.02   165.41   0.000         2.80        2.87
                  4  |       2.64       0.01   191.84   0.000         2.62        2.67
                  5  |       2.47       0.01   224.54   0.000         2.44        2.49
                  6  |       2.30       0.01   262.31   0.000         2.28        2.32
                  7  |       2.15       0.01   297.88   0.000         2.13        2.16
                  8  |       2.00       0.01   315.54   0.000         1.99        2.02
                  9  |       1.87       0.01   303.52   0.000         1.86        1.88
                 10  |       1.74       0.01   270.14   0.000         1.73        1.76
                 11  |       1.63       0.01   231.94   0.000         1.61        1.64
                 12  |       1.52       0.01   197.99   0.000         1.50        1.53
                 13  |       1.42       0.01   170.32   0.000         1.40        1.43
                 14  |       1.32       0.01   148.30   0.000         1.30        1.34
        ------------------------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.16.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong> Predicted number of children by education</strong></figcaption>
</figure>

## 5 More applications of nonlinear models

### 5.1 Categorical by categorical interaction

This section illustrates a categorical by categorical interaction using a logistic regression. 

For this example, let’s use the variable fepol as the outcome variable. The respondent was asked if they believed that women are not suited for politics. The variable fepol is coded: 1 = yes and 0 = no. Thus using fepol as our outcome, we will model endorsement of the statement as a function of two categorical predictors: gender (gender) and a three-level measure of education (educ3). This analysis is restricted to interviews conducted between 1972 and 1980.

```
  use gss_ivrm
  keep if yrint <= 1980
  logit fepol i.educ3##gender age,nolog
```

    Logistic regression                                     Number of obs =  5,014
                                                            LR chi2(6)    = 310.52
                                                            Prob > chi2   = 0.0000
    Log likelihood = -3313.7435                             Pseudo R2     = 0.0448
    
    ------------------------------------------------------------------------------
           fepol | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           educ3 |
             HS  |      -0.23       0.10    -2.26   0.024        -0.43       -0.03
           Coll  |      -0.65       0.13    -4.99   0.000        -0.91       -0.40
                 |
          gender |
         Female  |       0.19       0.10     1.94   0.052        -0.00        0.39
                 |
    educ3#gender |
      HS#Female  |      -0.14       0.13    -1.06   0.287        -0.39        0.12
    Coll#Female  |      -0.57       0.19    -2.97   0.003        -0.95       -0.20
                 |
             age |       0.02       0.00    11.48   0.000         0.02        0.02
           _cons |      -0.84       0.12    -6.99   0.000        -1.07       -0.60
    ------------------------------------------------------------------------------


The contrast command is used to test the overall interaction of educ3 by gender.

```
  contrast educ3#gender  //test the overall interaction of educ3 by gender
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df        chi2     P>chi2
        -------------+----------------------------------
        educ3#gender |          2        8.85     0.0120
        ------------------------------------------------


To help understand this interaction, let’s use the margins command to estimate the log odds of believing that women are not suited for politics by educ3 and gender. Then, let’s make a graph of these predicted logits using the marginsplot command.

```
  margins educ3#gender,nopvalues predict(xb)
```

        Predictive margins                                       Number of obs = 5,014
        Model VCE: OIM
        
        Expression: Linear prediction (log odds), predict(xb)
        
        ----------------------------------------------------------------
                       |            Delta-method
                       |     Margin   std. err.     [95% conf. interval]
        ---------------+------------------------------------------------
          educ3#gender |
          not hs#Male  |       0.07       0.08         -0.08        0.22
        not hs#Female  |       0.27       0.07          0.14        0.40
              HS#Male  |      -0.16       0.06         -0.28       -0.03
            HS#Female  |      -0.10       0.05         -0.20        0.00
            Coll#Male  |      -0.58       0.11         -0.79       -0.37
          Coll#Female  |      -0.96       0.13         -1.21       -0.71
        ----------------------------------------------------------------

```
  marginsplot,legend(subtitle(Gender))
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.17.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Predicted log odds of believing women are not suited for politics by gender and education</strong></figcaption>
</figure>

Although the log-odds metric is not easy to interpret, we can still glean the trends implied by the gender by education interaction. The graph suggests that the log odds of agreeing with this statement declines with increasing education, and that this decline appears to be stronger for females than for males.

test by interaction gender with comparisons of adjacent education levels，test partial interaction by applying the ar.contrast operator to educ3 and interacting that with gender.

```
  contrast ar.educ3#gender  
```

    Contrasts of marginal linear predictions
    
    Margins: asbalanced
    
    -----------------------------------------------------------
                            |         df        chi2     P>chi2
    ------------------------+----------------------------------
               educ3#gender |
    (HS vs not hs) (joint)  |          1        1.13     0.2874
      (Coll vs HS) (joint)  |          1        5.60     0.0180
                     Joint  |          2        8.85     0.0120
    -----------------------------------------------------------
    
Let’s now assess the gender difference at each level of education. We can do this by testing the simple effect of gender at each level of education using the contrast command below.

```
  contrast gender@educ3
```

    Contrasts of marginal linear predictions
    
    Margins: asbalanced
    
    ------------------------------------------------
                 |         df        chi2     P>chi2
    -------------+----------------------------------
    gender@educ3 |
         not hs  |          1        3.78     0.0520
             HS  |          1        0.48     0.4878
           Coll  |          1        5.28     0.0216
          Joint  |          3        9.54     0.0230
    ------------------------------------------------


To get a better sense of the magnitude of these effects in a metric that we understand, we can use the margins command to estimate the predictive margin of the probability of believing that women are not suited for politics by education and gender.

```
  margins educ3#gender,nopvalues 
```

        Predictive margins                                       Number of obs = 5,014
        Model VCE: OIM
        
        Expression: Pr(fepol), predict()
        
        ----------------------------------------------------------------
                       |            Delta-method
                       |     Margin   std. err.     [95% conf. interval]
        ---------------+------------------------------------------------
          educ3#gender |
          not hs#Male  |       0.52       0.02          0.48        0.55
        not hs#Female  |       0.56       0.02          0.53        0.60
              HS#Male  |       0.46       0.02          0.43        0.49
            HS#Female  |       0.48       0.01          0.45        0.50
            Coll#Male  |       0.36       0.02          0.32        0.41
          Coll#Female  |       0.28       0.03          0.23        0.33
        ----------------------------------------------------------------

```
  marginsplot,legend(subtitle(Gender))
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.18.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Predicted probability of believing women are not suited for politics by gender and education</strong></figcaption>
</figure>

Focusing on college graduates, the predictive margins of agreeing with this statementwas 36.2% for males and 28.2% for females.

The pattern of the interaction in the probability metric could vary as a function of the other covariates contained in the model. In this case, we have only one covariate, age. Let’s make two graphs showing the pattern of this interaction in the probability metric, **one where age is held constant at 30 and another where age is held constant at 50. The margins command below computes the predictive margins by education and gender for 30-year-olds and 50-year-olds.**

```
  margins educ3#gender,nopvalues at(age=(30 50)) 
```

        Adjusted predictions                                     Number of obs = 5,014
        Model VCE: OIM
        
        Expression: Pr(fepol), predict()
        1._at: age = 30
        2._at: age = 50
        
        ------------------------------------------------------------------
                         |            Delta-method
                         |     Margin   std. err.     [95% conf. interval]
        -----------------+------------------------------------------------
        _at#educ3#gender |
          1#not hs#Male  |       0.45       0.02          0.40        0.49
        1#not hs#Female  |       0.49       0.02          0.46        0.53
              1#HS#Male  |       0.39       0.02          0.36        0.42
            1#HS#Female  |       0.40       0.01          0.38        0.43
            1#Coll#Male  |       0.29       0.02          0.25        0.34
          1#Coll#Female  |       0.22       0.02          0.18        0.27
          2#not hs#Male  |       0.55       0.02          0.51        0.58
        2#not hs#Female  |       0.60       0.02          0.56        0.63
              2#HS#Male  |       0.49       0.02          0.46        0.52
            2#HS#Female  |       0.51       0.01          0.48        0.53
            2#Coll#Male  |       0.39       0.03          0.34        0.44
          2#Coll#Female  |       0.30       0.03          0.25        0.35
        ------------------------------------------------------------------
        
```
  marginsplot,bydimension(age)legend(subtitle(Gender))  
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.19.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Predicted probability of believing women are not suited for politics by gender and education with age held constant at 30 and 50</strong></figcaption>
</figure>

You could also assess the partial interaction of ar.educ3#gender, in the probability metric, for those at different ages. 

```
  margins ar.educ3#gender,at(age=(30 50)) contrast(nowald pveffects) 
```

    Contrasts of adjusted predictions                        Number of obs = 5,014
    Model VCE: OIM
    
    Expression: Pr(fepol), predict()
    1._at: age = 30
    2._at: age = 50
    
    ---------------------------------------------------------------------------
                                       |            Delta-method
                                       |   Contrast   std. err.      z    P>|z|
    -----------------------------------+---------------------------------------
                      educ3#gender@_at |
    (HS vs not hs) (Female vs base) 1  |      -0.03       0.03    -1.10   0.272
    (HS vs not hs) (Female vs base) 2  |      -0.03       0.03    -1.04   0.296
      (Coll vs HS) (Female vs base) 1  |      -0.09       0.04    -2.34   0.019
      (Coll vs HS) (Female vs base) 2  |      -0.10       0.04    -2.37   0.018
    ---------------------------------------------------------------------------

The interaction of gender by education (HS vs not hs) is not significant whether age is held constant at 30 or 50. The interaction of gender by education (Coll vs HS) is significant whether age is held constant at 30 or 50.

### 5.2 Categorical by continuous interaction

This section illustrates a categorical by continuous interaction using a binary logistic regression. 

The outcome for this example is the variable fepres, which is coded: 1 = would vote for a woman president and 0 = would not vote for a woman president. Let’s model this as a function of time (that is, year of interview) and education to see if the linear change in this attitude over time differed by education level. With respect to the year of interview, this question was asked in 17 different years ranging from 1972 to 1998, then it was asked again in 2008 and 2010. Because of the large 10-year gap between 1998 and 2008, we will omit the data for 2008 onward. With respect to education, let’s use the three-level categorical variable educ3, which is coded: 1 = non–high school graduate, 2 = high school graduate, and 3 = college graduate.

```
  use gss_ivrm.dta
  drop if yrint>=2008

  logit fepres i.yrint72##educ3 age gender
```

Let’s begin by assessing the trend in the log odds of the outcome across years.we first fit a model using fepres as the outcome predicted by i.yrint72##educ3 as well as age and gender.

```
  logit fepres i.yrint72##educ3 age gender
```

        Iteration 0:  Log likelihood = -9680.7297  
        Iteration 1:  Log likelihood =  -8870.722  
        Iteration 2:  Log likelihood = -8766.7906  
        Iteration 3:  Log likelihood = -8765.9168  
        Iteration 4:  Log likelihood = -8765.9147  
        Iteration 5:  Log likelihood = -8765.9147  
        
        Logistic regression                                    Number of obs =  23,926
                                                               LR chi2(52)   = 1829.63
                                                               Prob > chi2   =  0.0000
        Log likelihood = -8765.9147                            Pseudo R2     =  0.0945
        
        -------------------------------------------------------------------------------
               fepres | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        --------------+----------------------------------------------------------------
              yrint72 |
                   2  |       0.17       0.14     1.25   0.211        -0.10        0.43
                   3  |       0.13       0.13     0.95   0.340        -0.13        0.39
                   5  |       0.09       0.13     0.70   0.486        -0.17        0.35
                   6  |       0.13       0.14     0.96   0.339        -0.14        0.40
                  10  |       0.54       0.14     3.99   0.000         0.28        0.81
                  11  |       0.72       0.15     4.67   0.000         0.42        1.02
                  13  |       0.23       0.14     1.58   0.114        -0.05        0.51
                  14  |       0.60       0.15     3.94   0.000         0.30        0.89
                  16  |       0.68       0.18     3.75   0.000         0.33        1.04
                  17  |       0.31       0.18     1.70   0.089        -0.05        0.67
                  18  |       0.51       0.20     2.50   0.012         0.11        0.91
                  19  |       0.89       0.20     4.35   0.000         0.49        1.29
                  21  |       0.98       0.21     4.58   0.000         0.56        1.39
                  22  |       1.17       0.18     6.36   0.000         0.81        1.52
                  24  |       1.10       0.19     5.92   0.000         0.73        1.46
                  26  |       1.16       0.19     6.02   0.000         0.78        1.54
                      |
                educ3 |
                  HS  |       0.05       0.13     0.43   0.666        -0.19        0.30
                Coll  |       0.85       0.24     3.60   0.000         0.39        1.31
                      |
        yrint72#educ3 |
                2#HS  |       0.44       0.19     2.27   0.023         0.06        0.81
              2#Coll  |      -0.05       0.33    -0.15   0.884        -0.70        0.60
                3#HS  |       0.46       0.19     2.41   0.016         0.08        0.83
              3#Coll  |       0.22       0.35     0.63   0.529        -0.47        0.91
                5#HS  |       0.36       0.19     1.95   0.051        -0.00        0.73
              5#Coll  |       0.53       0.36     1.47   0.142        -0.18        1.23
                6#HS  |       0.59       0.19     3.08   0.002         0.22        0.97
              6#Coll  |       0.19       0.34     0.55   0.582        -0.48        0.86
               10#HS  |       0.61       0.19     3.12   0.002         0.23        0.99
             10#Coll  |       0.06       0.35     0.16   0.870        -0.62        0.74
               11#HS  |       0.20       0.21     0.97   0.330        -0.20        0.61
             11#Coll  |      -0.16       0.34    -0.47   0.641        -0.83        0.51
               13#HS  |       0.48       0.20     2.45   0.014         0.10        0.86
             13#Coll  |       0.31       0.34     0.91   0.365        -0.36        0.99
               14#HS  |       0.34       0.21     1.63   0.104        -0.07        0.75
             14#Coll  |       0.54       0.39     1.39   0.163        -0.22        1.31
               16#HS  |       0.45       0.25     1.78   0.075        -0.04        0.93
             16#Coll  |       0.39       0.43     0.89   0.372        -0.46        1.23
               17#HS  |       0.83       0.25     3.36   0.001         0.34        1.31
             17#Coll  |       0.41       0.41     1.02   0.309        -0.38        1.21
               18#HS  |       0.88       0.27     3.20   0.001         0.34        1.41
             18#Coll  |       0.78       0.44     1.76   0.078        -0.09        1.64
               19#HS  |       0.54       0.27     2.00   0.045         0.01        1.08
             19#Coll  |       0.51       0.47     1.09   0.274        -0.41        1.43
               21#HS  |       0.39       0.27     1.42   0.156        -0.15        0.92
             21#Coll  |       0.31       0.43     0.72   0.470        -0.54        1.16
               22#HS  |       0.40       0.23     1.70   0.089        -0.06        0.86
             22#Coll  |       0.04       0.35     0.11   0.909        -0.65        0.73
               24#HS  |       0.61       0.24     2.53   0.011         0.14        1.08
             24#Coll  |       0.58       0.39     1.49   0.137        -0.18        1.34
               26#HS  |       0.70       0.25     2.77   0.006         0.21        1.19
             26#Coll  |       0.53       0.40     1.32   0.186        -0.25        1.31
                      |
                  age |      -0.03       0.00   -23.33   0.000        -0.03       -0.02
               gender |      -0.02       0.04    -0.50   0.617        -0.10        0.06
                _cons |       2.19       0.12    17.85   0.000         1.95        2.43
        -------------------------------------------------------------------------------


We then use the margins command to compute the predicted outcome in the logodds metric followed by the marginsplot command to graph the results. 


```
  margins yrint72#educ3,predict(xb)
  marginsplot,noci
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.20.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Predicted log odds of voting for a woman president by year of interview and education</strong></figcaption>
</figure>

As we might expect, there are some bumps across time, but the trends seem reasonably linear for each group. We can test for differences in the linear effect of year by education using the contrast command below. The p1 contrast operator indicates to test for the linear effect of yrint72. 

```
  contrast p1.yrint72
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df        chi2     P>chi2
        -------------+----------------------------------
             yrint72 |          1      286.09     0.0000
        ------------------------------------------------
        
        --------------------------------------------------------------
                     |   Contrast   Std. err.     [95% conf. interval]
        -------------+------------------------------------------------
             yrint72 |
           (linear)  |       0.42       0.03          0.38        0.47
        --------------------------------------------------------------

We can test for a quadratic effect of yrint72 using the contrast command below.

```
  contrast p2.yrint72  //test the quadratic effect of yrint72
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df        chi2     P>chi2
        -------------+----------------------------------
             yrint72 |          1        1.71     0.1909
        ------------------------------------------------
        
        --------------------------------------------------------------
                     |   Contrast   Std. err.     [95% conf. interval]
        -------------+------------------------------------------------
             yrint72 |
        (quadratic)  |       0.03       0.03         -0.02        0.08
        --------------------------------------------------------------


Using the contrast command below, we can jointly test for quadratic, cubic, quartic, and quintic trends over time. The joint test of these four nonlinear trends is not significant ($p=0.2346$)

```
  contrast p(2 3 4 5).yrint72
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df        chi2     P>chi2
        -------------+----------------------------------
             yrint72 |
        (quadratic)  |          1        1.71     0.1909
            (cubic)  |          1        1.65     0.1994
          (quartic)  |          1        2.09     0.1481
          (quintic)  |          1        0.06     0.8125
              Joint  |          4        5.54     0.2364
        ------------------------------------------------
        
        --------------------------------------------------------------
                     |   Contrast   Std. err.     [95% conf. interval]
        -------------+------------------------------------------------
             yrint72 |
        (quadratic)  |       0.03       0.03         -0.02        0.08
            (cubic)  |       0.03       0.03         -0.02        0.08
          (quartic)  |      -0.04       0.02         -0.08        0.01
          (quintic)  |      -0.01       0.03         -0.06        0.04
        --------------------------------------------------------------


We could further analyze the nature of the relationship between time searching for nonlinearities analytically or graphically, but I think these tests give us a sufficient basis to proceed forward with fitting a model where we treat yrint72 as a continuous variable and interacting it with educ3. In this model, let’s add age and gender as covariates.

```
  logit fepres c.yrint72##educ3 age gender,nolog
```

        Logistic regression                                    Number of obs =  23,926
                                                               LR chi2(7)    = 1754.77
                                                               Prob > chi2   =  0.0000
        Log likelihood = -8803.3423                            Pseudo R2     =  0.0906
        
        ---------------------------------------------------------------------------------
                 fepres | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        ----------------+----------------------------------------------------------------
                yrint72 |       0.04       0.00    10.96   0.000         0.04        0.05
                        |
                  educ3 |
                    HS  |       0.34       0.07     4.80   0.000         0.20        0.48
                  Coll  |       0.91       0.12     7.51   0.000         0.67        1.14
                        |
        educ3#c.yrint72 |
                    HS  |       0.01       0.01     2.76   0.006         0.00        0.02
                  Coll  |       0.02       0.01     2.00   0.045         0.00        0.03
                        |
                    age |      -0.03       0.00   -23.39   0.000        -0.03       -0.02
                 gender |      -0.02       0.04    -0.52   0.604        -0.10        0.06
                  _cons |       2.16       0.10    22.18   0.000         1.97        2.36
        ---------------------------------------------------------------------------------

We can test the overall interaction of c.yrint72#educ3 using the contrast command. This test is significant.

The linear trend in the relationship between the log odds of the outcome and the year of interview differs by a three-level measure of education.

```
  contrast c.yrint72#educ3
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ---------------------------------------------------
                        |         df        chi2     P>chi2
        ----------------+----------------------------------
        educ3#c.yrint72 |          2        8.83     0.0121
        ---------------------------------------------------

Let’s use the pwcompare command to form **pairwise comparisons** of the effect of yrint72 among the three levels of education.

```
  pwcompare c.yrint72#educ3,pveffects 
```

    Pairwise comparisons of marginal linear predictions
    
    Margins: asbalanced
    
    --------------------------------------------------------
                    |                            Unadjusted
                    |   Contrast   Std. err.      z    P>|z|
    ----------------+---------------------------------------
    fepres          |
    educ3#c.yrint72 |
      HS vs not hs  |       0.01       0.01     2.76   0.006
    Coll vs not hs  |       0.02       0.01     2.00   0.045
        Coll vs HS  |       0.00       0.01     0.29   0.774
    --------------------------------------------------------

The test comparing the effect of the year of interview for high school graduates versus non–high school graduates is significant, and the test comparing the effect of the year of interview for college graduates versus non–high school graduates is also significant. 

We can visualize the predicted log odds of the willingness to vote for a woman president by year and education using the margins and marginsplot commands shown below. 

```
  margins educ3,at(yrint72=(0(1)26))predict(xb)
  marginsplot,noci
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.21.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Predicted log odds of willingness to vote for a woman president by year of interview and education</strong></figcaption>
</figure>

If we prefer, we can estimate and graph these results in the predicted probability metric using the margins and marginsplot commands below. 

```
  margins educ3,at(yrint72=(0(1)26)) 
  marginsplot,noci 
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.22.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Predictive margin of the probability of being willing to vote for a woman president by year of interview and education</strong></figcaption>
</figure>

### 5.3 Piecewise modeling
This section illustrates modeling a continuous predictor fit using piecewise modeling in the context of a logistic regression model. 

Let’s begin by inspecting the nature of the relationship between education and smoking status.

we first fit a model predicting smoking status from education, treating education as a categorical variable. Age is also included in the model as a covariate. The output of the logit command is omitted to save space.

```
  use gss_ivrm.dta
  logit smoke i.educ age  // treating education as a categorical variable.
```

        Iteration 0:  Log likelihood =   -10539.7  
        Iteration 1:  Log likelihood =  -10121.92  
        Iteration 2:  Log likelihood = -10117.862  
        Iteration 3:  Log likelihood = -10117.858  
        Iteration 4:  Log likelihood = -10117.858  
        
        Logistic regression                                     Number of obs = 16,274
                                                                LR chi2(21)   = 843.69
                                                                Prob > chi2   = 0.0000
        Log likelihood = -10117.858                             Pseudo R2     = 0.0400
        
        ------------------------------------------------------------------------------
               smoke | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                educ |
                  1  |       0.05       0.66     0.08   0.940        -1.24        1.34
                  2  |      -0.33       0.57    -0.58   0.563        -1.44        0.78
                  3  |       0.63       0.42     1.49   0.136        -0.20        1.46
                  4  |      -0.05       0.41    -0.11   0.912        -0.85        0.76
                  5  |       0.16       0.40     0.39   0.694        -0.63        0.94
                  6  |      -0.08       0.39    -0.22   0.828        -0.84        0.67
                  7  |       0.05       0.38     0.12   0.901        -0.69        0.79
                  8  |      -0.01       0.36    -0.03   0.975        -0.73        0.70
                  9  |       0.35       0.37     0.95   0.342        -0.37        1.07
                 10  |       0.44       0.37     1.21   0.227        -0.27        1.16
                 11  |       0.35       0.36     0.97   0.333        -0.36        1.07
                 12  |      -0.17       0.36    -0.47   0.639        -0.88        0.54
                 13  |      -0.36       0.36    -0.97   0.330        -1.07        0.36
                 14  |      -0.40       0.36    -1.09   0.276        -1.11        0.32
                 15  |      -0.43       0.37    -1.15   0.248        -1.15        0.30
                 16  |      -0.84       0.36    -2.31   0.021        -1.56       -0.13
                 17  |      -1.16       0.38    -3.06   0.002        -1.91       -0.42
                 18  |      -1.14       0.38    -3.00   0.003        -1.88       -0.39
                 19  |      -0.86       0.40    -2.15   0.032        -1.65       -0.08
                 20  |      -0.93       0.39    -2.36   0.018        -1.70       -0.16
                     |
                 age |      -0.02       0.00   -18.58   0.000        -0.02       -0.02
               _cons |       0.49       0.37     1.33   0.185        -0.23        1.20
        ------------------------------------------------------------------------------

Let’s now make a graph that shows the predicted logit of smoking as a function of education, adjusting for age. 

```
  margins educ,predict(xb)
  marginsplot,xline(12 16)
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.23.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Log odds of smoking by education</strong></figcaption>
</figure>

The graph in figure above includes vertical lines at 12 and 16 years of education. These junctures seem like excellent candidates for the placement of knots where there is a change in slope and a change in intercept. 

Let’s fit such a model below

```
  mkspline edprehsm 12 edhsm 16 edcom = educ,marginal
  logit smoke c.edprehsm c.edhsm c.edcom hsgrad cograd age,nolog
```

        Logistic regression                                     Number of obs = 16,274
                                                                LR chi2(6)    = 806.95
                                                                Prob > chi2   = 0.0000
        Log likelihood = -10136.225                             Pseudo R2     = 0.0383
        
        ------------------------------------------------------------------------------
               smoke | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
            edprehsm |       0.05       0.01     3.68   0.000         0.03        0.08
               edhsm |      -0.16       0.03    -5.83   0.000        -0.21       -0.10
               edcom |       0.06       0.04     1.40   0.161        -0.02        0.14
              hsgrad |      -0.60       0.06    -9.60   0.000        -0.72       -0.48
              cograd |      -0.30       0.10    -3.18   0.001        -0.49       -0.12
                 age |      -0.02       0.00   -19.10   0.000        -0.02       -0.02
               _cons |       0.27       0.15     1.76   0.078        -0.03        0.57
        ------------------------------------------------------------------------------

Before interpreting the results, let’s create a graph of the predicted log odds of smoking as a function of education. 

```
  margins,at(edprehsm=0 edhsm=0 edcom=0 hsgrad=0 cograd=0) ///  
          at(edprehsm=12 edhsm=0 edcom=0 hsgrad=0 cograd=0) ///
		      at(edprehsm=12 edhsm=0 edcom=0 hsgrad=1 cograd=0) ///
		      at(edprehsm=16 edhsm=4 edcom=0 hsgrad=1 cograd=0) ///
		      at(edprehsm=16 edhsm=4 edcom=0 hsgrad=1 cograd=1) ///
		      at(edprehsm=20 edhsm=8 edcom=4 hsgrad=1 cograd=1) ///
		      predict(xb) noatlegend

  mat yhat = r(b)'
  mat educ = (0 \ 12 \ 12 \ 16 \ 16\ 20)
  svmat yhat
  svmat educ
  graph twoway line yhat1 educ1,xline(12 16) title("Piecewise Model")
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.24.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Predicted log odds of smoking from education fit using a piecewise model with two knots</strong></figcaption>
</figure>

To assess how well the piecewise model accommodates the relationship between the log odds of smoking and education, let’s visualize figures above side byside.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.25.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Log odds of smoking treating education as a categorical variable (left panel) and fitting education using a piecewise model (right panel)</strong></figcaption>
</figure>

Let’s use the margins command to create the predictive margin of the probability of smoking as a function of education, averaging across the covariate age.

```
  margins,at(edprehsm=5 edhsm=0 edcom=0 hsgrad=0 cograd=0) ///  
          at(edprehsm=12 edhsm=0 edcom=0 hsgrad=0 cograd=0) ///
		      at(edprehsm=12 edhsm=0 edcom=0 hsgrad=1 cograd=0) ///
		      at(edprehsm=16 edhsm=4 edcom=0 hsgrad=1 cograd=0) ///
		      at(edprehsm=16 edhsm=4 edcom=0 hsgrad=1 cograd=1) ///
		      at(edprehsm=20 edhsm=8 edcom=4 hsgrad=1 cograd=1) noatlegend

  mat prsmoke = r(b)'
  mat xeduc = (5\12\12\16\16\20)
  svmat prsmoke
  svmat xeduc
  graph twoway line prsmoke1 xeduc1,xline(12 16) name(g3,replace)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.26.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Probability of smoking treating by education (fit using a piecewise model)</strong></figcaption>
</figure>

however we need to remember that the pattern using probabilities could change as a function of the covariates. We could further explore this by creating graphs at different levels of the covariate, age

