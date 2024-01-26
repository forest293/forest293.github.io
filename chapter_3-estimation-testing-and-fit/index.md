# [Categorical Dependent Var] Chapter_3 ：Estimation, testing, and fit


**Reviews Stata commands for fitting models,testing hypotheses,and computing measures of model fit***

Our book deals with what we think are the most fundamental and useful cross-sectional regression models for categorical and count outcomes: binary logit and probit, ordinal logit and probit, multinomial logit, Poisson regression, and negative binomial regression. We also explore several less common models, such as the stereotype logistic regression model and the zero-inflated and zero-truncated count models. Although these models differ in many respects, they generally share common features:

1. **Each model is fit by maximum likelihood, and many can be fit when data is collected using a complex sample survey design.**
2. **Hypotheses about the parameters can be tested with Wald and likelihood-ratio tests.**
3. **Measures of fit can be computed.**
4. **The models can be interpreted by examining predicted values of the outcomes, a topic that is considered in chapter 4.**

Because of these similarities, the same principles and many of the same commands can be applied to each model. 

## 1 Estimation
Each of the models we consider is fit using maximum likelihood (ML). **ML estimates are the values of the parameters that have the greatest likelihood of generating the observed sample of data if the assumptions of the model are true.** 

**To obtain the ML estimates, a likelihood function calculates how likely it is that w e would observe the set of outcome values we actually observed if a given set of parameter estimates were the true parameters.** 

If we imagine a surface in which the range of possible values of a makes up one axis and the range of  $\beta$ makes up another axis, the resulting graph of the likelihood function would look like a hill; the ML estimates would be the parameter values corresponding to the top of this hill. The variance of the estim ates corresponds roughly to how quickly the slope is changing near the top of the hill.

### 1.1 Stata’s output for ML estimation

**The process of iteration is reflected in the initial lines of Stata’s output.** Below are the first lines of the output from the logit model of labor force participation th at we use as an example in chapters 5 and 6:

```
  use binlfp4, clear
  logit lfp k5 k618 agecat wc hc lwg inc
```

    Iteration 0:  Log likelihood =  -514.8732  
    Iteration 1:  Log likelihood = -453.09301  
    Iteration 2:  Log likelihood = -452.72688  
    Iteration 3:  Log likelihood = -452.72649  
    Iteration 4:  Log likelihood = -452.72649  
    
    Logistic regression                                     Number of obs =    753
                                                            LR chi2(7)    = 124.29
                                                            Prob > chi2   = 0.0000
    Log likelihood = -452.72649                             Pseudo R2     = 0.1207
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              k5 |      -1.39       0.19    -7.39   0.000        -1.76       -1.02
            k618 |      -0.07       0.07    -0.96   0.338        -0.20        0.07
          agecat |      -0.64       0.13    -4.92   0.000        -0.89       -0.38
              wc |       0.80       0.23     3.48   0.001         0.35        1.25
              hc |       0.14       0.21     0.66   0.510        -0.27        0.54
             lwg |       0.61       0.15     4.06   0.000         0.32        0.91
             inc |      -0.03       0.01    -4.25   0.000        -0.05       -0.02
           _cons |       1.66       0.37     4.45   0.000         0.93        2.39
    ------------------------------------------------------------------------------

**The results begin with the iteration log, where the first, line, iteration 0, reports the value of the log likelihood at the start value.** Whereas earlier we talked about maximizing the likelihood function, in practice, programs maximize the log of the likelihood, which simplifies the computations and yields the same result. Here the log likelihood at the start is —514.8732. The next four lines show the progress in maximizing the log likelihood, converging to the value of —452.72649. The rest of the output, which is omitted here, is discussed later in this section.


### 1.2 ML and sample size

Under the usual assumptions, the ML estimator is consistent, efficient, and asymptotically normal. These properties hold as the sample size approaches infinity (see Cameron and Trivedi [2005]; Cramer [1986]; and Eliason [1993] for details). Although ML estimators are not necessarily bad estimators in small samples, the small-sample behavior of ML estim ators for the models we consider is largely unknown. Except for the logit and Poisson regression, which can be fit using exact perm utation methods with exlogistic or expoisson, alternative estimators with known small-sample properties are generally not available. With this in mind, Long (1997, 54) proposed the following guidelines for the use of ML in small samples:

**It is risky to use ML with samples smaller than 100, while samples over 500 seem adequate. These values should be raised depending on characteristics of the model and the data.** 

First, if there are many parameters, more observations are needed A rule of at least 10 observations per parameter seems reasonable This does not imply that a minimum of 100 is not needed if you have only two parameters. 

Second, if the data are ill-conditioned (for example, independent variables are highly collinear) or if there is little variation in the dependent variable (for example, nearly all the outcomes are 1), a larger sample is required. 

Third, some models seem to require more observations (such as the ordinal regression model or the zero-inflated count models).

### 1.3 Problems in obtaining ML estimates

Although the numerical methods used by S tata to fit models with ML are highly refined and generally work extremely well, you can encounter problems. If your sample size is adequate, but you cannot get a solution or you get estimates th at appear to not make substantive sense, one common cause is th at **the data have not been properly “cleaned”.** 

In addition to mistakes in constructing variables and selecting observations, the scaling of variables can cause problems. **The larger the ratio between the largest and smallest standard deviations among variables in the model, the more problems you are likely to encounter with numerical m ethods due to rounding.** For example, if income is measured in units of $1$, income is likely to have a very large standard deviation relative to other variables. Recoding income to units of $1,000$ can solve the problem. For a detailed technical discussion of maximum likelihood estim ation in Stata, see Gould, Pitblado, and Poi (2010)

**Overall, however, numerical methods for ML estimation work well when your model is appropriate for your data.** Still, Cramer’s (1986, 10) advice about the need for care in estimation should be taken seriously:

**Check the data, check their transfer into the computer, check the actual computations (preferably by repeating at least a sample by a rival program), and always remain suspicious of the results, regardless of the appeal.**

### 1.4 Syntax of estimation commands

All single-equation estimation commands that we consider in this book have the same syntax:

command depvar [ indepvars ] [if] [ in ] [ weight ] [ , options ]


Here are a few examples for a lo g it model with lfp as the dependent variable:

```
logit lfp k5 k618 age wc lwg
logit lfp k5 k618 age wc lwg if hc == 1
logit lfp k5 k618 age wc lwg if hc == 1, level(90)
```

The syntax diagram here uses 1) variable lists, 2) i f and in conditions, 3) weights, and 4) options.

### 1.5 Variable lists

Stata automatically corrects some mistakes you may make when specifying independent variables. For example, if you include wc as an independent variable when th e sample is restricted to a single value of wc, such as logit lfp k5 age he wc if wc==l. then Stata drops wc from the model. Or **suppose that you recode a k-category variable into a set of k indicator variables. Recall that one of the indicator variables must be excluded to avoid perfect collinearity. If you included all k indicator variables in indepvars, Stata automatically excludes one of them.**

**Using factor-variable notation in the variable list**

In Stata 11 and later, you can specify a k-category variable as a set of indicator variables using Stata’s factor-variable notation. Prefixing a variable name with i.tells Stata to do this. In our previous example, suppose th at instead of age being measured in years, it was measured using three age groups with the variable agecat:

```
tabulate agecat, missing
```

     Wife's age |
          group |      Freq.     Percent        Cum.
    ------------+-----------------------------------
          30-39 |        298       39.58       39.58
          40-49 |        290       38.51       78.09
            50+ |        165       21.91      100.00
    ------------+-----------------------------------
          Total |        753      100.00

Variable agecat equals 1 for ages 30- 39, 2 for 40 49, and 3 for 50 or older. If we were not using factor variables, we could recode the three categories of ag ecat to generate three dummy variables:

```
generate age3039 = (agecat==1) if agecat < .
label var age3039 "Age 30 to 39?"
generate age4049 = (agecat==2) if agecat < .
label var age4049 "Age 40 to 49?"
generate age50plus = (agecat==3) & agecat < .
label var age50plus "Age 50 or older?"
```

Next, we fit a model using these variables, where age3039 is the excluded base category:

```
logit lfp k5 k618 age4049 age50plus wc hc lwg inc, nolog
```

Using factor-variable notation, we can fit the exact same model but let S tata automatically create the indicator variables:

```
logit lfp k5 k618 i.agecat wc hc lwg inc, nolog
```

By default, Stata uses the lowest value of the source variable as the base or omitted category in the model. Accordingly,

```
logit lfp k5 k618 i.agecat wc hc lwg inc, nolog
```

If you want a different base category, specify the base with the prefix ib#. , where # is the value of the base category. In our example, to treat women ages 50 or older as our base category instead of women ages 30-39, we would specify the model as follows:

```
logit lfp k5 k618 ib3.agecat wc hc lwg inc, nolog
```

By default, any variable not specified with i. is treated as a continuous variable. For example, in the specification

```
logit lfp k5 k618 agecat wc hc lwg inc, nolog
```

**More on factor-variable notation**

sometimes factor variables can be confusing. First, the name of the variables automatically generated by Stata when fitting a model using factor variables might not be obvious. This is important because some postestimation commands, such as test and lincom, require the exact, symbolic name of the variable associated with a coefficient. 

To obtain the names associated with each coefficient, referred to as symbolic names, one can simply type the name of the last estimation command with the option coeflegend, such as logit, coefleg end.The model is not fit again, but the names associated with the estimates are listed. For example, fitting a model where factor-variable notation creates indicator variables and interactions produces output like this:

```
logit lfp i.agecat c.age##c.age, nolog
```
        Logistic regression                                     Number of obs =    753
                                                                LR chi2(4)    =  11.17
                                                                Prob > chi2   = 0.0248
        Log likelihood = -509.29034                             Pseudo R2     = 0.0108
        
        ------------------------------------------------------------------------------
                 lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
              agecat |
              40-49  |      -0.37       0.35    -1.05   0.296        -1.05        0.32
                50+  |      -0.56       0.56    -1.00   0.319        -1.66        0.54
                     |
                 age |       0.26       0.14     1.77   0.077        -0.03        0.54
                     |
         c.age#c.age |      -0.00       0.00    -1.74   0.082        -0.01        0.00
                     |
               _cons |      -4.90       3.03    -1.62   0.106       -10.85        1.04
        ------------------------------------------------------------------------------


```
logit, coeflegend
```

    Logistic regression                                     Number of obs =    753
                                                            LR chi2(4)    =  11.17
                                                            Prob > chi2   = 0.0248
    Log likelihood = -509.29034                             Pseudo R2     = 0.0108
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Legend
    -------------+----------------------------------------------------------------
          agecat |
          40-49  |      -0.37  _b[2.agecat]
            50+  |      -0.56  _b[3.agecat]
                 |
             age |       0.26  _b[age]
                 |
     c.age#c.age |      -0.00  _b[c.age#c.age]
                 |
           _cons |      -4.90  _b[_cons]
    ------------------------------------------------------------------------------


Sometimes you might not be sure if your specification of the model using factor variable notation produces the model you want. **One trick that we find useful——especially with large datasets, when fitting a model can take a long time—— is to use summarize to decode your variable list.**

In this example, summarize decodes the factor-variable notation i.agec at into two indicator variables and c.age##c.age into age and age-squared:

```
summarize i.agecat c.inc##c.inc
```

            Variable |        Obs        Mean    Std. dev.       Min        Max
        -------------+---------------------------------------------------------
              agecat |
              30-39  |        753    .3957503    .4893363          0          1
              40-49  |        753    .3851262    .4869486          0          1
                50+  |        753    .2191235    .4139274          0          1
                     |
                 inc |        753    20.12897     11.6348  -.0290001         96
                     |
         c.inc#c.inc |        753     540.364    830.3907    .000841       9216


value labels are used to label categories of an indicator variable, such as 40-49 above. To see the values rather than the labels, you can add the **nofvlabel** option:

```
summarize i.agecat c.inc##c.inc, nofvlabel
```

            Variable |        Obs        Mean    Std. dev.       Min        Max
        -------------+---------------------------------------------------------
              agecat |
                  1  |        753    .3957503    .4893363          0          1
                  2  |        753    .3851262    .4869486          0          1
                  3  |        753    .2191235    .4139274          0          1
                     |
                 inc |        753    20.12897     11.6348  -.0290001         96
                     |
         c.inc#c.inc |        753     540.364    830.3907    .000841       9216
        

### 1.6 Specifying the estimation sample

if and in restrictions can be used to define the sample of observations used to fit the model, referred to as the estimation sample, where the syntax for if and in conditions follows the guidelines in chapter 2, page 45.

#### 1.6.1 Missing data

Estimation commands use **listwise deletion to exclude cases in which there are missing values for any of the variables in the model.** Accordingly, if two models are fit using the same dataset but have different sets of independent variables, it is possible to have different samples. 

Suppose th at among the 753 cases in the sample, 23 have missing data for at least one variable. If we fit a model using all variables, we would obtain

```
use binlfp4-missing, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
```

    Logistic regression                                     Number of obs =    730
                                                            LR chi2(8)    = 113.28
                                                            Prob > chi2   = 0.0000
    Log likelihood = -441.33862                             Pseudo R2     = 0.1137
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              k5 |      -1.40       0.19    -7.22   0.000        -1.78       -1.02
            k618 |      -0.07       0.07    -0.95   0.340        -0.20        0.07
                 |
          agecat |
          40-49  |      -0.66       0.21    -3.11   0.002        -1.07       -0.24
            50+  |      -1.26       0.26    -4.76   0.000        -1.77       -0.74
                 |
              wc |
        college  |       0.74       0.23     3.23   0.001         0.29        1.20
                 |
              hc |
        college  |       0.16       0.21     0.77   0.439        -0.25        0.57
             lwg |       0.59       0.15     3.88   0.000         0.29        0.88
             inc |      -0.03       0.01    -3.82   0.000        -0.05       -0.02
           _cons |       1.02       0.29     3.51   0.000         0.45        1.60
    ------------------------------------------------------------------------------

Suppose that seven of the missing cases were missing only for k618 and that we fit a second model excluding k618:

```
logit lfp k5 i.agecat i.wc i.hc lwg inc, nolog
```

    Logistic regression                                     Number of obs =    737
                                                            LR chi2(7)    = 116.53
                                                            Prob > chi2   = 0.0000
    Log likelihood = -444.78964                             Pseudo R2     = 0.1158
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              k5 |      -1.38       0.19    -7.23   0.000        -1.75       -1.01
                 |
          agecat |
          40-49  |      -0.60       0.21    -2.92   0.003        -1.00       -0.20
            50+  |      -1.15       0.24    -4.87   0.000        -1.62       -0.69
                 |
              wc |
        college  |       0.78       0.23     3.38   0.001         0.33        1.23
                 |
              hc |
        college  |       0.14       0.21     0.67   0.505        -0.27        0.54
             lwg |       0.61       0.15     4.03   0.000         0.31        0.90
             inc |      -0.03       0.01    -3.89   0.000        -0.05       -0.02
           _cons |       0.87       0.25     3.48   0.000         0.38        1.36
    ------------------------------------------------------------------------------

Thus we cannot, use a likelihood ratio test or information criteria to compare the two models (see sections 3.2 and 3.3 for details), because **changes in the estimates could be due either to changes in the model specification or to the use of different samples to fit the models. When you compare coefficients across models, you want the samples to be the same.**

Although Stata uses listwise deletion when fitting models, this is rarely the best way to handle missing data. **We recommend that you make explicit decisions about which cases to include in your analyses rather than let cases be dropped implicitly.** Indeed, we would prefer th at Stata issue an error rather than automatically drop cases.

**The mark and markout commands make it simple to explicitly exclude missing data,** mark markvar generates the new variable markvar that equals 1 for all cases, markout mnrkvar varlist changes the values of markvar to 0 for any cases in which values of any of the variables in varlist are missing. The following example, where we have artificially created the missing data, illustrates how this works:

```
use binlfp4-missing, clear
mark nomiss
markout nomiss lfp k5 k618 agecat wc hc lwg inc
tab nomiss
```

             nomiss |      Freq.     Percent        Cum.
        ------------+-----------------------------------
                  0 |         23        3.05        3.05
                  1 |        730       96.95      100.00
        ------------+-----------------------------------
              Total |        753      100.00

Because nomiss is 1 for cases where none of the variables in our models is missing, to use the same sample when fitting both models, we add the condition if nomiss==l:

logit lfp k5 k618 i.agecat i.wc i.hc lwg inc if nomiss==1, nolog

        Logistic regression                                     Number of obs =    730
                                                                LR chi2(8)    = 113.28
                                                                Prob > chi2   = 0.0000
        Log likelihood = -441.33862                             Pseudo R2     = 0.1137
        
        ------------------------------------------------------------------------------
                 lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                  k5 |      -1.40       0.19    -7.22   0.000        -1.78       -1.02
                k618 |      -0.07       0.07    -0.95   0.340        -0.20        0.07
                     |
              agecat |
              40-49  |      -0.66       0.21    -3.11   0.002        -1.07       -0.24
                50+  |      -1.26       0.26    -4.76   0.000        -1.77       -0.74
                     |
                  wc |
            college  |       0.74       0.23     3.23   0.001         0.29        1.20
                     |
                  hc |
            college  |       0.16       0.21     0.77   0.439        -0.25        0.57
                 lwg |       0.59       0.15     3.88   0.000         0.29        0.88
                 inc |      -0.03       0.01    -3.82   0.000        -0.05       -0.02
               _cons |       1.02       0.29     3.51   0.000         0.45        1.60
        ------------------------------------------------------------------------------

```
logit lfp k5 i.agecat i.wc i.hc lwg inc if nomiss==1, nolog
```

    Logistic regression                                     Number of obs =    730
                                                            LR chi2(7)    = 112.37
                                                            Prob > chi2   = 0.0000
    Log likelihood = -441.79452                             Pseudo R2     = 0.1128
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              k5 |      -1.38       0.19    -7.19   0.000        -1.76       -1.00
                 |
          agecat |
          40-49  |      -0.62       0.21    -2.98   0.003        -1.02       -0.21
            50+  |      -1.15       0.24    -4.84   0.000        -1.61       -0.68
                 |
              wc |
        college  |       0.76       0.23     3.28   0.001         0.30        1.21
                 |
              hc |
        college  |       0.16       0.21     0.76   0.447        -0.25        0.56
             lwg |       0.60       0.15     3.96   0.000         0.30        0.89
             inc |      -0.03       0.01    -3.87   0.000        -0.05       -0.02
           _cons |       0.88       0.25     3.53   0.000         0.39        1.38
    ------------------------------------------------------------------------------

**Instead of selecting cases in each model with if, we could have used drop if nomiss==0 to delete observations with missing values before fitting the models**

>Aside: Analysis with missing data
>>We do want to make two brief points.

>>First, **a sophisticated but increasingly common approach to missing data involves multiple imputations, and S tata has an excellent suite of commands to make working with multiply imputed data simpler than it would be without it.**What Stata does not have, at this writing, is a way of using margins with its multiple imputation suite. Because our prim ary methods of interpretation use the margins command, these cannot be used with multiply imputed data.

>>Second, **one way that d ata may be missing in a dataset is called missing at random (MAR.). The name can be misleading: it does not mean missing completely at random but instead means that data are missing in a way that unbiased predictions of missing values can be made from other variables in the dataset itself.** Even in the situation where missing at random data does not bias estimates (a matter outside the scope of our discussion), the techniques of interpretation that comprise much of our book involve using either the mean of a variable or the m ean of an estimated effect size calculated over all observations. In either case, missing data can cause problems for computation of these averages even in cases in which the estimation of coefficients is unbiased


#### 1.6.2 Information about missing values

Although mark and markout work well for determining which observations have missing values for a set of variables, these commands do not provide information on the patterns of missing data among these variables. There are three types of questions that we might ask:

1. How many observations have no missing values? How many have missing data for one variable? For two variables? And so on.
2. What percentage of cases are missing for each variable?
3. What patterns of missing data are there among the variables? For example, do missing values on one variable tend to occur when there are missing values on some other variable?


We will consider each of these questions in turn. First, to determine the number of observations with a given number of missing values on a set of variables, the easiest way is to use the **egen command with the row miss(varlist) function.This creates a new variable that contains the number of missing values for each observation, for which we can use tabulate to view the frequency distribution.** For example,

```
use gsskidvalue4, clear
egen missing = rowmiss(age anykids black degree female kidvalue othrrace year income91 income)
```

            missing |      Freq.     Percent        Cum.
        ------------+-----------------------------------
                  0 |      2,684       58.37       58.37
                  1 |      1,709       37.17       95.54
                  2 |        195        4.24       99.78
                  3 |          7        0.15       99.93
                  4 |          3        0.07      100.00
        ------------+-----------------------------------
              Total |      4,598      100.00


Next, we could generate a variable,**nomiss**, that equals 1 if an observation has no missing values and 0 if an observation has any missing values: **gen nomiss = (missing==0)**. This is an alternative to using mark and markout to create an indicator variable for whether an observation has missing values for any variable in a list.

```
misstable summarize age anykids black degree female kidvalue othrrace ///
                    year income91 income, all showzero
```

                   |                                | Unique
          Variable |     Obs=.     Obs>.     Obs<.  | values        Min         Max
      -------------+--------------------------------+------------------------------
               age |         0         0     4,598  |     73         18          99
           anykids |        14         0     4,584  |      2          0           1
             black |         0         0     4,598  |      2          0           1
            degree |        14         0     4,584  |      5          0           4
            female |         0         0     4,598  |      2          0           1
          kidvalue |     1,609         0     2,989  |      4          1           4
          othrrace |         0         0     4,598  |      2          0           1
              year |         0         0     4,598  |      2       1993        1994
          income91 |         0         0     4,598  |     24          1          99
            income |       495         0     4,103  |     21       1000       75000
      -----------------------------------------------------------------------------

The **all** option prints information for all the variables in the variable list. Otherwise, only those variables with at least one missing value are displayed. The **showzero** option displays each 0 in the table as 0 rather than as a blank space. These options make the output clearer for didactic purposes, although you may prefer to omit them in practice.

**The output for misstable summarize distinguishes between missing values that are coded with the system missing value (.) and those coded with extended missing values (.a through .z).** Some datasets use extended missing values to distinguish why different cases have missing values, like coding people who respond “don’t know” to a survey question as .d and coding those for whom the question is inapplicable as .i. **In the misstable summarize output, the first column of numbers is the number of cases with missing values coded as the system missing value (.), the second is the number of cases with extended missing values, and the third is the number of cases with no missing values.**

To understand the labels for these columns, you need to know that Stata considers missing values to be numerically larger than nonmissing values and th at extended m issing values are considered larger than the system missing value. Accordingly, Obs<. indicates nonmissing values and Obs>. indicates extended missing values that are larger than ., the system-missing value. In this example, we see that only four variables have missing values: anykids (14 cases), degree (14), kid value (1609), and income (495).

Third, to obtain information on the patterns of missing data, we use **misstable** patterns :

```
misstable patterns age anykids black degree female kidvalue othrrace ///
                   year income91 income, freq
```

       Missing-value patterns
         (1 means complete)
    
                  |   Pattern
        Frequency |  1  2  3  4
      ------------+-------------
            2,684 |  1  1  1  1
                  |
            1,410 |  1  1  1  0
              294 |  1  1  0  1
              185 |  1  1  0  0
                6 |  0  1  0  0
                5 |  1  0  0  1
                4 |  1  0  1  1
                3 |  0  0  0  0
                3 |  0  1  1  0
                1 |  0  1  0  1
                1 |  0  1  1  1
                1 |  1  0  0  0
                1 |  1  0  1  0
      ------------+-------------
            4,598 |
    
      Variables are  (1) anykids  (2) degree  (3) income  (4) kidvalue

The **freq** option displays **results as frequencies rather than as percentages.** Each row of the output represents a unique pattern of missing values, **where 0 indicates a missing value and 1 indicates a nonmissing value.** 

* In the top row, the pattern is all 1s, showing th at 2,684 cases had no missing values for any variable. 
* In the second row, we see the most common pattern of missing values was where there were no missing values for variables 1, 2, and 3, with missing values for variable 4. 
* At the bottom of the table, we see which number corresponds to which variable; for example, variable 1 is anykids. Only 4 variables are included even though 10 variables were in the variable list. This is because only variables with some missing values are included in the table.

The misstable summarize command has a generate **(stubname)** option **to generate new variables that indicate whether observations are missing for a particular variable. For each variable that has missing data, a new variable is created whose name is stubname followed by the name of the variable with missing data.** The new variable is assigned a value of 1 if the variable is missing for an observation and 0 if it is not missing

These new variables could be used, for example, in a binary logit model of the sort we describe in chapters 5 and 6 using basic demographics as predictors of whether observations are missing. First, we create the indicator variables:

```
misstable summarize age anykids black degree female kidvalue othrrace ///
                    year income91 income, gen(m_)
```

    
                                                                   Obs<.
                                                    +------------------------------
                   |                                | Unique
          Variable |     Obs=.     Obs>.     Obs<.  | values        Min         Max
      -------------+--------------------------------+------------------------------
           anykids |        14               4,584  |      2          0           1
            degree |        14               4,584  |      5          0           4
          kidvalue |     1,609               2,989  |      4          1           4
            income |       495               4,103  |     21       1000       75000
      -----------------------------------------------------------------------------

Then, we fit a logit model:

```
logit m_income female black othrrace age, nolog
```

        Logistic regression                                     Number of obs =  4,598
                                                                LR chi2(4)    = 100.88
                                                                Prob > chi2   = 0.0000
        Log likelihood = -1520.1691                             Pseudo R2     = 0.0321
        
        ------------------------------------------------------------------------------
            m_income | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
              female |       0.48       0.10     4.67   0.000         0.28        0.68
               black |       0.51       0.13     3.93   0.000         0.26        0.77
            othrrace |       0.25       0.24     1.07   0.285        -0.21        0.71
                 age |       0.02       0.00     7.92   0.000         0.02        0.03
               _cons |      -3.52       0.16   -21.83   0.000        -3.84       -3.21
        ------------------------------------------------------------------------------


Although this is only an informal assessment of the missing data, it suggests th at missing values on income are associated with being female, black, and older.

#### 1.6.3 Postestimation commands and the estimation sample

Excepting predict, postestimation commands for testing, assessing fit, and making predictions use the observations from the estimation sample, unless you specify otherwise.Accordingly, with these commands you do not need to worry about dropping cases that were deleted because of missing data during estimation. **Stata automatically selects cases from the estimation sample by using a special variable named e (sam ple) that is created by every estimation command. This variable equals 1 for cases used to fit the model and equals 0 otherwise.** You can use this variable to select cases with if conditions, as in **sum female if e(sample).** But you cannot otherwise use this variable as if it was an ordinary variable in your dataset. For example, you cannot use a command like **tabulate female e(sample).** Instead, you need to generate an ordinary variable equal to **e(sample)**, which then allows you to tabulate the variable or do anything else with it.

```
logit m_income female black othrrace age i.degree, nolog
```

        Logistic regression                                     Number of obs =  4,584
                                                                LR chi2(8)    = 102.94
                                                                Prob > chi2   = 0.0000
        Log likelihood = -1498.4489                             Pseudo R2     = 0.0332
        
        ---------------------------------------------------------------------------------
               m_income | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        ----------------+----------------------------------------------------------------
                 female |       0.48       0.10     4.60   0.000         0.27        0.68
                  black |       0.46       0.13     3.44   0.001         0.20        0.73
               othrrace |       0.27       0.24     1.16   0.247        -0.19        0.74
                    age |       0.02       0.00     6.92   0.000         0.01        0.02
                        |
                 degree |
           high school  |      -0.10       0.13    -0.79   0.430        -0.35        0.15
        junior college  |      -0.65       0.28    -2.35   0.019        -1.19       -0.11
              bachelor  |      -0.23       0.17    -1.34   0.182        -0.56        0.11
              graduate  |      -0.31       0.23    -1.37   0.171        -0.76        0.13
                        |
                  _cons |      -3.30       0.21   -15.72   0.000        -3.71       -2.89
        ---------------------------------------------------------------------------------

```
gen included = e(sample)
label var included "Cases included in logit on m_income"
tab included
```

          Cases |
    included in |
       logit on |
       m_income |      Freq.     Percent        Cum.
    ------------+-----------------------------------
              0 |         14        0.30        0.30
              1 |      4,584       99.70      100.00
    ------------+-----------------------------------
          Total |      4,598      100.00
    
The generated variable included has 4,584 cases equal to 1, which is the same as the number of cases used to fit our model. The remaining 14 cases are 0, which corresponds with the 14 cases missing on the degree variable th at we showed in the output from m isstab le above.

### 1.7 Weights and survey data

**Weights indicate that some observations should be given more weight than others when computing estimates.** The syntax for specifying weights is *[type=vamame]* , where the square brackets are part of the command, type is the type of weight to be used, and vamame is the variable containing the weights. Stata recognizes four types of weights:

1. fweights (frequency weights) indicate that an observation represents multiple observations with identical values. For example, if an observation has an fweight of 5, this is equivalent to having five identical, duplicate observations. If you do not include a weight modifier in your estim ation command, this is equivalent to specifying [fweight= l].
2. pweights (sampling weights) denote the inverse of the probability that the observation is included because of the sampling design. For example, if a case has a pweight of 1,200, that case had a 1 in 1,200 chance of being selected into the sample and in th at sense represents 1,200 observations in the population.
3. aweights (analytic weights) are inversely proportional to the variance of an observation. The variance of the *j*th observation is assumed to be $\frac{\sigma^2}{\omega_{j}}$, where $\omega_{j}$ is the analytic weight. Analytic weights are used most often when observations are averages and the weights are the num ber of elements that gave rise to the average. For example, if each observation is the cell mean from a larger dataset, the data are heteroskedastic because the variance of the means decreases as the number of observations used to calculate them increases.
4. iw eights (importance weights) have no formal statistical definition. They are used by programmers to facilitate certain types of computations

Frequency weights differ notably from the other types because a dataset th at includes an fw eight variable can be used to create a new dataset that yields equivalent results without frequency weights by simply repeating observations with duplicate values. As a result, frequency weights pose no issues for various techniques we consider in this book

The use of weights is a complex topic, and it is easy to apply weights incorrectly. If you need to use weights, we encourage you to read the discussions in [u] 11.1.6 weight and [u] 20.23 Weighted estimation . Winship and Radbill (1994) have an accessible introduction to weights in the linear regression model. Heeringa, West, and Berglund (2010) provide an in-depth treatment along with examples using Stata in their excellent book on complex survey design, a topic we consider next.

#### 1.7.1 complex survey desighs

Complex survey designs have three major features. 

First, **samples can be divided into strata within which observations are selected separately.** For example, a sample might be stratified by region of the country so th at the researchers can achieve precisely the number of respondents they want from each region.

Second, **samples can use clustering in which higher levels of aggregation, called primary sampling units, are selected first and then individuals are sampled from within these clusters.** A survey of adolescents might use schools as its prim ary sampling unit and then sample students from within each school. Observations within clusters often share similarities leading to violations of the assumption of independent observations. Accordingly, when there is clustering, the usual standard errors will be incorrect because they do not adjust for the lack of independence.

Third, **individuals can have different probabilities of selection.** For example, the design might oversample minority populations. Such oversampling allows more precise estimates of subgroup characteristics, but probability weights must be used to obtain accurate estimates for the population as a whole.

**Stata’s svy commands for samples with complex survey designs (see the Stata Survey Data Manual for details) provide estimates where the standard errors are adjusted for stratification, clustering, and weights.** If the sample design involves weights or clustering, but not stratification, then models can be fit using standard regression commands with the [pweight = weight-variable] and vce (cluster cluster-variable) options. The results will be identical to those from **svy**. If the sample design involves stratification, then svy commands must be used to get the correct standard errors.

The first step in using commands for complex samples is **to specify the key features of the survey design by using the svyset command.** To illustrate this, we use an example from Heeringa, West, and Berglund (2010) based on the Health and Retirement Study, a representative sample of American adults over age 50. The primary sampling units (PSUs). another name for clusters, are defined by the variable **secu**, the strata by the variable **stratum** , and the probability weights by the variable **kwgtr**. We use svyset as follows:

```
use svyhrs4, clear
svyset secu [pweight=kwgtr], strata(stratum)
```

        Sampling weights: kwgtr
                     VCE: linearized
             Single unit: missing
                Strata 1: stratum
         Sampling unit 1: secu
                   FPC 1: <zero>


**Stata will use this information automatically when a subsequent command that supports survey estimation is prefixed by **svy** :.** In our example, the outcome is whether th e respondent has arthritis, and the independent variables are gender, education, and age. To fit a logit model, discussed in detail in chapter 5, we begin the command with the prefix svy: and afterward specify the logit command as we otherwise would.

```
svy: logit arthritis male i.ed3cat age
```

Sampling weights: kwgtr
             VCE: linearized
     Single unit: missing
        Strata 1: stratum
 Sampling unit 1: secu
           FPC 1: <zero>

. 
end of do-file

. do "C:\Users\lenovo\AppData\Local\Temp\STD4f8_000000.tmp"

. svy: logit arthritis male i.ed3cat age
(running logit on estimation sample)

    Survey: Logistic regression
    
    Number of strata =  56                            Number of obs   =     18,375
    Number of PSUs   = 112                            Population size = 76,085,117
                                                      Design df       =         56
                                                      F(4, 53)        =     204.28
                                                      Prob > F        =     0.0000
    
    ------------------------------------------------------------------------------
                 |             Linearized
       arthritis | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            male |      -0.58       0.04   -13.05   0.000        -0.67       -0.49
                 |
          ed3cat |
    12-15 years  |      -0.21       0.06    -3.81   0.000        -0.33       -0.10
      16+ years  |      -0.64       0.07    -9.78   0.000        -0.77       -0.51
                 |
             age |       0.05       0.00    21.96   0.000         0.04        0.05
           _cons |      -2.32       0.15   -15.03   0.000        -2.63       -2.01
    ------------------------------------------------------------------------------

Two differences merit noting between the information provided in the above results and those provided when svy: is not used. 

First, the svy results indicate both the number of strata and the number of PSUs in the sample. 

Second, in addition to the sample size, the results present the population size implied by the probability weights. In this example, the size of the target population is over 76 million.

Models fit using complex survey methods pose two problems for the postestimation techniques used in this book. 

First, measures of fit are often based on the model’s likelihood. With survey estimation, the “likelihood” on which model estimates are based is not a true likelihood (Sribney 1997), so any technique that requires a value for the likelihood is not available.6 

Second, some methods of interpretation that we use require estimates of the standard deviation of one or more variables in the model, which are not always available with survey estimation


Stata supports survey estimation for nearly all the models we discuss in this book. If the model you are using does not work with the svy: prefix, remember that **nearly all regression commands in Stata allow weights and clustering;** although not ideal, this is a reasonable way to proceed if the regression command you are using does not support the svy: prefix.

### 1.8 Options for regression models

The following options apply to most regression models. Unique options for specific models are considered in later chapters.

* **noconstant** constrains the intercept to equal 0. For example, in a linear regression, the command **regress y xl x2, noconstant** would fit the model $y = \beta_{1} X_{1} + \beta_{2} X_{2} + \varepsilon$
* **nolog** suppresses the iteration history, which shortens the output. If you use this option, which we often do, and have problems obtaining estimates, it is a good idea to refit the model w ithout this option and with the trace option.

**trace** lets you see the values of the parameters for each step of th e iteration. This can be useful for determining which variables may be causing a problem if your model has difficulty converging.

**level(#)** specifies the level of the confidence interval. By default, Stata provides 95% confidence intervals for estimated coefficients, meaning that the interval around the estimated $\hat{\beta}$
would capture the true value of $\beta$ 95% of the time if repeated samples were drawn, level () allows you to specify other intervals. For example, level (90) specifies a 90% interval. You can also change the default level with the command set level. For example, set level 90 specifies 90% confidence intervals.

**vce (cluster cluster-variable)** specifies that the observations are independent across the clusters th at are defined by unique values of cluster-variable but are not necessarily independent within clusters. Specifying this option leads to robust standard errors, as discussed below, with additional corrections for the effects of clustered data. See Hosmer, Lemeshow, and Sturdivant (2013, chap. 9) for a detailed discussion of logit models with clustered data. **Using vce ( cluster cluster-variable) does not affect the coefficient estimates but can have a large impact on the standard errors.**

**vce(vcetype)** specifies the type of standard errors th at are reported, vce (robust) replaces traditional standard errors with robust standard errors, which are also known as Huber, White, or sandwich standard errors. These are discussed further next, in section 3.1.9. Gould, Pitblado, and Poi (2010) provide details on how robust standard errors are computed in Stata. **Robust standard errors are automatically used if the vce ( cluster cluster-variable) option is specified, if probability weights are used, or if a model is fit using svy.** In earlier versions of Stata, this option was simply robust. 

**Option vce (bootstrap) estimates the variance-covariance matrix by bootstrap, which involves repeated reestimation on samples drawn with replacement from the original estimation sample.**

**Option vce(jackknife) uses the jackknife method, which involves refitting the model N times, each time leaving out a single observation.** Type help vce option for further details.

**vsquish** eliminates the blank lines in output that are inserted when factor-variable notation is used. We sometimes use nolog and vsquish in this book to save space.


### 1.9 Robust standard errors

Robust standard errors, which are computed by Stata when the robust option is specified, go by a variety of names, including Huber-Eicker-White, clustered, White, heteroskedasticity-consistent, HCCM, and sandwich standard errors. In the last decade, their use has become increasingly common. For example, King and Roberts (2014) conducted a survey of articles in the American Political Science Review and found that 66% of the articles using regression models reported robust standard errors.

**Robust standard errors are considered robust in the sense that they are correct in the presence of some types of violations of the assumptions of the model.** For example, if the correct model is a binary logit model but a binary probit model is fit, the model has been misspecified. The estimates obtained by fitting a logit model cannot be maximum likelihood estimates because an incorrect likelihood function is being used (that is, a logistic probability density is used instead of the correct normal density). When a model is misspecified in this way, the usual standard errors are incorrect (White 1982). **For this reason, Arminger (1995) argues that robust standard errors should be broadly used. He writes: “If one keeps in mind that most researchers misspecify the model ..., it is obvious that their estimated parameters can usually be interpreted only as minimum ignorance estimators and that the standard errors and test statistics may be far away from the correct asymptotic values, depending on the discrepancy between the assumed density and the actual density that generated the data.”**

In some cases, robust standard errors are likely to work quite well. **If violations of the underlying model are minor, as we would argue is the case if the true model is logit and you fit a probit model, then the robust standard errors are preferred, but the differences are likely to be quite small.** In our informal simulations, they are trivially different. On the other hand, **if you fit a Poisson regression model (see chapter 9) in the presence of overdispersion, Cameron and Trivedi (2013, 72 80) provide convincing evidence that robust standard errors provide a more accurate assessment of statistical significance.**  If there is clustering in the data, robust standard errors should be used, ideally by specifying vce(cluster cluster-variablc) or by using svy estimation.

Arguments for robust standard errors are compelling. Some argue they should be used nearly always in practice. At the same time, robust standard errors are not a general solution to problems of misspecification, and they have important limitations. Kauermann and Carroll (2001) show that even when the model is correct, robust standard errors have more sampling variability, and sometimes far more, than the usual standard errors. This is “the price that one pays to obtain consistency”. These theoretical results are consistent with simulations by Long and Ervin (2000), who found that **in the linear regression model robust standard errors often did worse than the usual standard errors in samples smaller than 500.** They recommended using small-sample versions that can be computed in Stata for regress with the options hc2 or hc3. Among nonlinear models, Kauermann and Carroll (2001) consider the Poisson regression model and the logit model. They showed that the loss of efficiency when using robust standard errors can be worse than th at occurring in normal models. However, we are unaware of small-sample versions of robust standard errors for nonlinear models

There is a second and potentially very serious problem. **If robust standard errors are used because a model is misspecified, it is im portant to consider what other implications misspecification may have.** Freedman (2006) is dismissive of robust standard errors for many of the models discussed in this book for this reason, writing pointedly: "It remains unclear why applied workers should care about the variance of an estimator for the wrong parameter.” Cameron and Trivedi (2010, 334) note that if a model is misspecified, the inconsistency of the parameter estimate is a far more serious problem than the consistency of the standard error.

King and Roberts (2014) argue that differences between robust and classical errors are “like canaries in the coal mine, providing clear indications th at your model is misspecified and your inferences are likely biased”. They suggest that a comparison of robust and nonrobust standard errors should be used as an informal test of model misspecification. **Researchers should try to address the specification problems that cause robust and classical standard errors to differ, instead of considering robust standard errors to have solved the problematic implications of misspecification.**

### 1.10 Reading the estimation output

Because we have already discussed the iteration log, in the following example we suppress it with the nolog option and consider other parts of the output from estimation commands. Although the sample output is from logit, our discussion applies generally to other regression models fit by maximum likelihood. We comment briefly below on changes to the estimation output for svy estimation. The following output from logit illustrates **how Stata displays results from regression commands**:

```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
```

        Logistic regression                                     Number of obs =    753
                                                                LR chi2(8)    = 124.30
                                                                Prob > chi2   = 0.0000
        Log likelihood = -452.72367                             Pseudo R2     = 0.1207
        
        ------------------------------------------------------------------------------
                 lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                  k5 |      -1.39       0.19    -7.25   0.000        -1.77       -1.02
                k618 |      -0.07       0.07    -0.96   0.336        -0.20        0.07
                     |
              agecat |
              40-49  |      -0.63       0.21    -3.00   0.003        -1.04       -0.22
                50+  |      -1.28       0.26    -4.92   0.000        -1.79       -0.77
                     |
                  wc |
            college  |       0.80       0.23     3.48   0.001         0.35        1.25
                     |
                  hc |
            college  |       0.14       0.21     0.66   0.508        -0.27        0.54
                 lwg |       0.61       0.15     4.04   0.000         0.31        0.91
                 inc |      -0.04       0.01    -4.24   0.000        -0.05       -0.02
               _cons |       1.01       0.29     3.54   0.000         0.45        1.57
        ------------------------------------------------------------------------------

#### 1.10.1 Header

1. **Log likelihood = -452.72367** is the value of the log likelihood at convergence.
2. **Number of obs** is the number of observations, excluding those with missing values and those excluded with if and in conditions.
3. **LR chi2(8)** is the value of a likelihood-ratio chi-squared for the test of the null hypothesis that all the coefficients associated with independent variables are simultaneously equal to 0 (see page 119 for details). The number in parentheses is the degrees of freedom for the test. When robust standard errors or probability weights are used, results from a Wald test of the same null hypothesis are shown instead.
4. **Prob > chi2** indicates the p-value.
5. **Pseudo R2** is the measure of fit also known as McFadden’s (1974) R^2. Details on how this measure is computed are given on page 126.


#### 1.10.2 Estimates and standard errors

1. The leftmost column lists the variables in the model, with the dependent variable at the top. The independent variables are in the same order as they were typed on the command line. The constant, labeled **_cons**, is last. With Stata 13 and later, factor variables are labeled with their value labels. For example, the indicator variable for **agecat==2** is labeled as **agecat** followed by 40-49, which is the value label for category 2. In Stata 11 and 12, or with the nofvlabel option in Stata 13 and later, the indicator variable for agecat==2 is labeled as agecat followed on the next line by 2. Retyping the estim ation command followed by coef legend will list the symbolic names of each regression parameter.
2. Column Coef . contains estimates of the regression coefficients
3. Column Std. Err . contains the standard errors of the estimates. With the vce(robust) option, these are labeled Robust Std . Err.
4. Column z contains the $z$ test equal to the estimate divided by its standard error.
5. Column **P>|z|** is the two-tailed significance level. A significance level listed as 0.000 means that p < 0.001. For example, p = 0.00049 is rounded to 0.000.
6. Column [95% Conf. Interval] contains the confidence interval for each estimate. Instead of testing a specific hypothesis (for example, $H_0: (\beta = 0$), we can use a confidence interval that contains the true parameter with a chosen probability, known as the confidence level. For a given confidence level, the estimated upper and lower bounds define the confidence interval.
   
#### 1.10.3 Differences in output for svy estimation

With svy estimation, the output differs, reflecting that the estimates are no longer ML estimates.
1. In addition to the sample size, an estim ate of the population size is shown.
2. The likelihood-ratio test that all coefficients are 0 is replaced by an $F$ test.
3.  The pseudo-$R^2$ is not shown because it is based on the log likelihood.
4.  $t$-values are shown instead of $z$-values
   
### 1.11 Storing estimation results

Stata considers the results of a model that has just been fit to be the active estimates.After fitting a model, you can typee return list to see a summary of the informationth at Stata stores about the active estimates. Postestimation commands are based onth e active estimates. When a new model is fit, its results become the active estimates,replacing the previous model’s estimates.

The **estimates store** and **estimates save** commands preserve the active estimation results so that they can be retrieved and used even after a new model is fit.**estimates store saves the active estimates to memory, while estimates save savesthem to a file. Storing estimation results is extremely useful for several reasons.** For one,commands like lrtest and estimates table use results from more than one model.Because only one set of estimates can be active at a time, stored estimates are the way we can refer to multiple sets of estimates. Additionally, the margins command, usedextensively in later chapters, makes predictions based on estimates from a model thathas already been fit, meaning the active estimates. For some applications, however, we will need to overwrite the active estimates from our regression model with estimatesfrom margins by using the post option. Once this is done, the estimates from the regression model are no longer active and need to be restored (discussed below) as theactive estimates if you want to do additional postestimation analysis of the model.

After running any estimation command, the syntax is


**est**imates **sto**re *name*

For example, to store the estimation results with the name logit 1. type

```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store logit1
```

After running estimates restore , the estimation results in memory are the same as if we had just fit the model, even though we may have fit other models in the interim. Of course, we need to be careful about changes made to the data after fitting the model, but the same caveat about not changing the data between fitting a model and postestimation analysis applies even when estimates store and estimates restore
are not used.

#### 1.11.1 (Advanced) Saving estimates to a file

if you are fitting a model that takes hours to estimate, you may want to save the result to a file so you can use them later without refitting the model.

Because **estimates store** holds the estimates in memory, estimates stored in one Statasession are not available in the next. Even within a S tata session, the command **clear all** erases stored estimates. To use estimates in a later session or after clearing memory,you can use **estimates save** to save results to a disk file:

**est**imates save filename, replace

For example, estimates save model1, replace will create the file model1.ster.

We can load previously saved estimation results with estim ates use:

estimates use filename

estimates use restores the estimates almost as if we had just fit the model, and the“almost” here is very important. **As described earlier, when we fit a model, Stata creates the variable e(sample) to indicate which observations were used when fitting the model. Some postestimation commands need e (sample) to produce proper results.However, estimates use does not require that the data in memory are the data used to estimate the saved results. You can even run estimates use without data in memory.**Accordingly, estima tes use does not restore the e(sample) variable. Although this prevents some postestimation commands from working, this is better than having them give wrong answers because the wrong dataset is in memory.

To deal with this issue, you can reset **e(sample)**. When doing this, you are responsible for making sure that the data loaded to memory are the same as the data when the model was originally fit. Assuming the proper data are in memory, you use the **estimates esample** command to respecify the outcome and independent variables, thei **if** and **in** conditions, and the weights that were used when the model was originally fit. e(sample) is then set accordingly. The syntax is

estimates esample varlist [if] [ in ] [ weight ]

To show how this works, we present an example that uses a command we have notyet introduced, **estat summarize**, which provides summary statistics on the estimation sample that was used to compute the active estimates. First, we fit a model and show the summary statistics for the estimation sample. Then, we save the estimates as model1:

```
use binlfp4, clear
logit lfp k5 if wc==1, nolog
```

        Logistic regression                                     Number of obs =    212
                                                                LR chi2(1)    =  17.91
                                                                Prob > chi2   = 0.0000
        Log likelihood = -124.06386                             Pseudo R2     = 0.0673
        
        ------------------------------------------------------------------------------
                 lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                  k5 |      -0.93       0.23    -3.98   0.000        -1.40       -0.47
               _cons |       1.09       0.18     6.19   0.000         0.75        1.44
        ------------------------------------------------------------------------------

```
estimates save model1, replace
```

(Because of the i f wc==l condition specified with logit, the estimates are based on 212 cases, not the 753 cases in the entire sample.) Next, we simulate restarting Statawith the **clear all** command. We then bring the estimates we saved to a file back asthe active estimates with estim ates use, and we replay the estimates:

```
clear all
estimates use model1
estimates replay
```

        Logistic regression                                     Number of obs =    212
                                                                LR chi2(1)    =  17.91
                                                                Prob > chi2   = 0.0000
        Log likelihood = -124.06386                             Pseudo R2     = 0.0673
        
        ------------------------------------------------------------------------------
                 lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                  k5 |      -0.93       0.23    -3.98   0.000        -1.40       -0.47
               _cons |       1.09       0.18     6.19   0.000         0.75        1.44
        ------------------------------------------------------------------------------

We can see that the old estimates are now active because the estimates **replay** command displayed the same results for our model that we had before. We also ran **estimates describe** , which displays the command used to fit the model. All of thisw orks even though we do not have any data in memory.

```
estimates describe
```  
. logit lfp k5 if wc==1, nolog

Even if there are data in memory when estimates use is run, Stata is cautious and does not presume they are the same data used to fit the model: if you load the data with use and type estat summarize, yon get an error. To avoid this error, after loading the same data, we run the estimates esample command:


```
use binlfp4, clear
estimates esample: lfp k5 if wc==1
estat summarize
```

        Estimation sample logit                  Number of obs =        212
        
          -------------------------------------------------------------------
              Variable |         Mean      Std. dev.         Min          Max
          -------------+-----------------------------------------------------
                   lfp |     .6792453      .4678714            0            1
                    k5 |     .3301887      .6634921            0            3
          -------------------------------------------------------------------
        
With **estimates esample**, we must specify the same variables and conditions as when we fit the model. Then **estat summarize** will produce the same results it did after we fit the model initially.

### 1.12 Reformatting output with estimates table

**estimates table reformats the results from an estimation command to look more like the tables that are seen in articles and books. estimates table also makes it easier to move estimation results into a word processor or spreadsheet to make presentation-quality tables.** We strongly recommend using this command or some other automated procedure rather than retyping results to make tables. **Not only is this less tedious, but it diminishes the possibility of errors. Also, if you revise your model and used estimates table in your do-file, then you automatically have the corrected tables.**

The syntax is

estimates table [ model-name1 [model-name2 ... ] ] [ , options ]

where model-name# is the name of a model whose results were stored using estim ates store . If model-name# is not specified, the estim ation results in memory are used.

Here is a simple example that lets us compare estimates from similarly specified logit and probit models, a topic considered in detail in chapter 5. We start by fitting the two models and using **estimates store** to save the estimates:

```
use binlfp4, clear
logit lfp k5 i.agecat i.wc, nolog
estimates store logit_model
```

        Logistic regression                                     Number of obs =    753
                                                                LR chi2(4)    =  85.93
                                                                Prob > chi2   = 0.0000
        Log likelihood = -471.9082                              Pseudo R2     = 0.0834
        
        ------------------------------------------------------------------------------
                 lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                  k5 |      -1.35       0.19    -7.27   0.000        -1.72       -0.99
                     |
              agecat |
              40-49  |      -0.62       0.20    -3.18   0.001        -1.01       -0.24
                50+  |      -1.19       0.23    -5.27   0.000        -1.63       -0.75
                     |
                  wc |
            college  |       0.83       0.18     4.53   0.000         0.47        1.19
               _cons |       0.89       0.16     5.40   0.000         0.57        1.21
        ------------------------------------------------------------------------------

```
probit lfp k5 i.agecat i.wc, nolog
estimates store probit_model
```

        Probit regression                                       Number of obs =    753
                                                                LR chi2(4)    =  85.57
                                                                Prob > chi2   = 0.0000
        Log likelihood = -472.08881                             Pseudo R2     = 0.0831
        
        ------------------------------------------------------------------------------
                 lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                  k5 |      -0.82       0.11    -7.56   0.000        -1.03       -0.61
                     |
              agecat |
              40-49  |      -0.37       0.12    -3.17   0.001        -0.61       -0.14
                50+  |      -0.72       0.14    -5.29   0.000        -0.99       -0.46
                     |
                  wc |
            college  |       0.50       0.11     4.57   0.000         0.29        0.71
               _cons |       0.54       0.10     5.50   0.000         0.35        0.73
        ------------------------------------------------------------------------------

We combine the estimates by using estimates table :

```
estimates table logit_model probit_model, b(%12.3f) t varlabel 
```

    --------------------------------------------------------
                    Variable | logit_model    probit_model  
    -------------------------+------------------------------
                  # kids < 6 |       -1.351         -0.820  
                             |        -7.27          -7.56  
                             |                              
            Wife's age group |                              
                      40-49  |       -0.624         -0.374  
                             |        -3.18          -3.17  
                        50+  |       -1.190         -0.723  
                             |        -5.27          -5.29  
                             |                              
      Wife attended college? |                              
                    college  |        0.832          0.500  
                             |         4.53           4.57  
                    Constant |        0.889          0.540  
                             |         5.40           5.50  
    --------------------------------------------------------
                                                 Legend: b/t

estimates table provides great flexibility for what you include in your table. Although you should check the Stata Base Reference Manual or type help estimates table for complete information, here are some of the most basic and helpful options:

1. b(format) specifies the format used to print the coefficients. For example, **b(%9.3f) indicates the estimates are to be in a column nine characters wide with three decimal places.** For more information on formats, see help format or the Stata User's Guide.
2. varwidth (#) specifies the width of the column th at includes variable names and labels on the left side of the table. This is often needed when variable labels are used.
3. keep (varlist) or drop (varlist) specify which of the independent variables to include in or exclude from the table.
4. se[(format)], t[(format)], and p[C(format)] request standard errors, t or z statistics, and p-values, respectively. By specifying the format, you can have a different number of decimal places for each statistic. For example, b(%9.3f) and t(%9.2f) indicate the desired formats for coefficients and t-statistics, respectively.
5. star tells Stata to print one star by a coefficient if the p-value is less than 0.05, two stars if less than 0.01, and three stars if less than 0.001. The star option cannot be used in conjunction with the se, t, or p options.
6. varlabel requests that variable labels be used instead of variable names in the rows of the table. Prior to Stata 13, this option was named label.
7. stats(statlist) indicates that the scalar statistics in statlist should be included in the base of the table. In statlist, N requests the sample size, aic requests Akaike's information criterion, and bic requests the Bayesian information criterion. To determine which other statistics can be included, after you fit a model, run **ereturn list**, for example.

```
ereturn list
```

## 2 Testing

If the assumptions of the model hold, ML estim ators are distributed asymptotically normally:

<math xmlns="http://www.w3.org/1998/Math/MathML" display="block">
  <msub>
    <mrow>
      <mover>
        <mi>β</mi>
        <mo>^</mo>
      </mover>
    </mrow>
    <mi>k</mi>
  </msub>
  <mrow data-mjx-texclass="REL">
    <mover>
      <mrow data-mjx-texclass="OP">
        <mo>∼</mo>
      </mrow>
      <mrow>
        <mi>a</mi>
      </mrow>
    </mover>
  </mrow>
  <mrow>
    <mi data-mjx-variant="-tex-calligraphic" mathvariant="script">N</mi>
  </mrow>
  <mrow data-mjx-texclass="INNER">
    <mo data-mjx-texclass="OPEN">(</mo>
    <msub>
      <mi>β</mi>
      <mi>k</mi>
    </msub>
    <mo>,</mo>
    <msubsup>
      <mi>σ</mi>
      <mrow>
        <msub>
          <mrow>
            <mover>
              <mi>β</mi>
              <mo stretchy="false">^</mo>
            </mover>
          </mrow>
          <mi>k</mi>
        </msub>
      </mrow>
      <mn>2</mn>
    </msubsup>
    <mo data-mjx-texclass="CLOSE">)</mo>
  </mrow>
</math>

The hypothesis $H_0 :{\beta}_k = \beta^*$ can be tested with the $ z $  statistic:

<math xmlns="http://www.w3.org/1998/Math/MathML" display="block">
  <mi>z</mi>
  <mo>=</mo>
  <mfrac>
    <mrow>
      <msub>
        <mrow>
          <mover>
            <mi>β</mi>
            <mo>^</mo>
          </mover>
        </mrow>
        <mi>k</mi>
      </msub>
      <mo>−</mo>
      <msup>
        <mi>β</mi>
        <mo>∗</mo>
      </msup>
    </mrow>
    <msub>
      <mrow>
        <mover>
          <mi>σ</mi>
          <mo>^</mo>
        </mover>
      </mrow>
      <mrow>
        <msub>
          <mrow>
            <mover>
              <mi>β</mi>
              <mo stretchy="false">^</mo>
            </mover>
          </mrow>
          <mi>k</mi>
        </msub>
      </mrow>
    </msub>
  </mfrac>
</math>

If Ho is true, then 2: is distributed approximately normally with a mean of 0 and a variance of 1 for large samples. The sampling distribution is shown in the following figure, where the shading shows the rejection region for a two-tailed test at the 0.05 level 

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.20.1.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

For some estimators, such as linear regression implemented by **regress** and with survey estimation, the estimators have at distribution rather than a normal distribution. The general principles of testing are, however, the same.

## 2.1 One-tailed and two-tailed tests

The probability levels in the Stata output for estim ation commands are for two-tailed tests. When past research or theory suggests the sign of the coefficient, a one-tailed test might be used, and $H_0$ is rejected only when $t$ or $z$ is in the expected tail.
You should divide P > |t| (or P>|z| ) by 2 only when the estimated coefficient is in the expected direction. 

Disciplines vary in their preferences for using one-tailed or two-tailed tests. Consequently, it is im portant to be explicit about whether p-values are for one-tailed or two-tailed tests. Unless stated otherwise, all the p-values we report in this book are for two-tailed tests.

## 2.2 Wald and likelihood-ratio tests

**For models fit by ML, hypotheses can be tested with Wald tests by using test and with likelihood-ratio (LR) tests by using lrtest. Only Wald tests are available for coefficients estimated using survey estimation.** For both types of tests, there is a null hypothesis $H_0$ that implies constraints on the model’s parameters. For example, $H_0: \beta_vc — \beta_hc = 0$ hypothesizes that two of the parameters are 0 in the population.

The Wald test assesses $H_0$ by considering two pieces of information. First, **all else being equal, the greater the distance between the estimated coefficients and the hypothesized values, the less support we have for $H_0$**. Second, **the greater the curvature of the log-likelihood function, the more certainty we have about our estimates. This means that smaller differences between the estimates and hypothesized values are required to reject $H_0$.**

The LR test assesses $H_0$ by comparing the log likelihood from th e full model that does not include the constraints implied by $H_0$ with a restricted model th at does impose those constraints. If the constraints significantly reduce the log likelihood, then $H_0$ is rejected. Thus the LR test requires fitting two models.

Although the LR and Wald tests are asymptotically equivalent, they have different values in finite samples, particularly in small samples. In general, it is unclear which test is to be preferred. Cameron and Trivedi (2005, 238) review the literature and conclude that neither test is uniformly superior. Nonetheless, many statisticians prefer the LR when both are suitable. We do recommend com puting only one test or the other; that is, we see no reason why you would want to compute or report both tests for a given hypothesis

## 2.3 Wald tests with test and testparm

test computes Wald tests for linear hypotheses about parameters from the last model that was fit. Here we consider the most useful features of this powerful command. Features for multiple-equation models, such as mlogit, zip, and zinb, are discussed in chapters 8 and 9. Use help test for more features and help testnl for Wald tests of nonlinear hypotheses."

```
test varlist [ , accumulate ]
```

The first syntax for test allows you to **test that one or more coefficients from the last model are simultaneously equal to 0**:

where varlist contains names of independent variables from the last estimation.

Some examples of test after fitting the model logit lfp k5 k618 i.agecat i.wc i.hclwg inc should make this first syntax clear. With one variable listed—here, **k5**—we are testing $H0: \beta_k5 = 0$.

```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
```

        Logistic regression                                     Number of obs =    753
                                                                LR chi2(8)    = 124.30
                                                                Prob > chi2   = 0.0000
        Log likelihood = -452.72367                             Pseudo R2     = 0.1207
        
        ------------------------------------------------------------------------------
                 lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                  k5 |      -1.39       0.19    -7.25   0.000        -1.77       -1.02
                k618 |      -0.07       0.07    -0.96   0.336        -0.20        0.07
                     |
              agecat |
              40-49  |      -0.63       0.21    -3.00   0.003        -1.04       -0.22
                50+  |      -1.28       0.26    -4.92   0.000        -1.79       -0.77
                     |
                  wc |
            college  |       0.80       0.23     3.48   0.001         0.35        1.25
                     |
                  hc |
            college  |       0.14       0.21     0.66   0.508        -0.27        0.54
                 lwg |       0.61       0.15     4.04   0.000         0.31        0.91
                 inc |      -0.04       0.01    -4.24   0.000        -0.05       -0.02
               _cons |       1.01       0.29     3.54   0.000         0.45        1.57
        ------------------------------------------------------------------------------


```
* Wald tests
test k5
```

     ( 1)  [lfp]k5 = 0
    
               chi2(  1) =   52.57
             Prob > chi2 =    0.0000

The resulting chi-squared test with 1 degree of freedom equals the square of the $z$ test statistic in the **logit** output. The results indicate that we can reject the null hypothesis.

If we list all the regressors in the model, we can test that all the coefficients except the constant are simultaneously equal to 0. **When factor-variable notation is used, variables must be specified with the value.variable-name syntax, such as 2.agecat. Recall that if you are not sure what name to use, you can replay the results by using the coef legend option (for example, logit, coef legend).**

```
test k5 k618 2.agecat 3.agecat 1.wc 1.hc lwg inc
```


         ( 1)  [lfp]k5 = 0
         ( 2)  [lfp]k618 = 0
         ( 3)  [lfp]2.agecat = 0
         ( 4)  [lfp]3.agecat = 0
         ( 5)  [lfp]1.wc = 0
         ( 6)  [lfp]1.hc = 0
         ( 7)  [lfp]lwg = 0
         ( 8)  [lfp]inc = 0
        
                   chi2(  8) =   95.90
                 Prob > chi2 =    0.0000
        
As noted above, an **LR** test of the same hypothesis is part of the standard output ol estim ation commands, labeled as **LR chi2** in the header ot the estimation output.

To test all the coefficients associated with a factor variable with more than two categories, you can use testparm . For example, to test that all the coefficients for agecat are 0, we can use test:

```
test 2.agecat 3.agecat
```

     ( 1)  [lfp]2.agecat = 0
     ( 2)  [lfp]3.agecat = 0
    
               chi2(  2) =   24.27
             Prob > chi2 =    0.0000
    
The same results are obtained with testparm :


     ( 1)  [lfp]2.agecat = 0
     ( 2)  [lfp]3.agecat = 0
    
               chi2(  2) =   24.27
             Prob > chi2 =    0.0000


Because agecat has only two categories, the advantage of testpaxm is not great. But when there are many categories, it is much simpler to use.

The second syntax for test allows you to test hypotheses about linear combinations of coefficients:

test  [exp = exp] [ , accumulate ]

For example, to test th at two coefficients are equal— say, $H_0:\beta = \beta_k618$:

```
test k5=k618
```

         ( 1)  [lfp]k5 - [lfp]k618 = 0
        
                   chi2(  1) =   45.07
                 Prob > chi2 =    0.0000

Because the test statistic is significant, we reject the null hypothesis that the effect of having young children on labor force participation is equal to the effect of having older children.

As before, testing hypotheses involving indicator variables requires us to specify both the value and the variable. For example, to test that the coefficients for ages 4049 versus 30-39 and for ages 50+ versus 30-39 are equal, we would use the following:

```
test 2.agecat=3.agecat
```

         ( 1)  [lfp]2.agecat - [lfp]3.agecat = 0
        
                   chi2(  1) =    8.86
                 Prob > chi2 =    0.0029

### 2.3.1 The accumulate option

The accumulate option allows you to build more complex hypotheses based 011 the prior test command. For example, we begin with a test of $H_0:\beta_k5 = \beta_k618$:


```
test k5=k618
```

     ( 1)  [lfp]k5 - [lfp]k618 = 0
    
               chi2(  1) =   45.07
             Prob > chi2 =    0.0000

Next, add the constraint that $\beta_wc = \beta_hc$

```
test 1.wc=1.hc, accumulate
```

     ( 1)  [lfp]k5 - [lfp]k618 = 0
     ( 2)  [lfp]1.wc - [lfp]1.hc = 0
    
               chi2(  2) =   47.63
             Prob > chi2 =    0.0000

This results in a test of $H_0:\beta_k5 = \beta_k618$, $\beta_wc = \beta_hc$. Instead of using the **accumulate** option, we could have used a single **test** command with multiple restrictions: test (k5=k618) ( 1.wc = 1.hc ).

## 2.4 LR tests with Irtest

lrtest compares **nested models** by using an LR test. The syntax is where model-one and model-two are the names of estimation results stored by estimates store. When model-two is not specified, the most recent estimation results are used in its place. Typically, we begin by fitting the full or unconstrained model, and then we store the results. For example,

**Irtest model-one [ model-two]**

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store full
```

    Logistic regression                                     Number of obs =    753
                                                            LR chi2(8)    = 124.30
                                                            Prob > chi2   = 0.0000
    Log likelihood = -452.72367                             Pseudo R2     = 0.1207
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              k5 |      -1.39       0.19    -7.25   0.000        -1.77       -1.02
            k618 |      -0.07       0.07    -0.96   0.336        -0.20        0.07
                 |
          agecat |
          40-49  |      -0.63       0.21    -3.00   0.003        -1.04       -0.22
            50+  |      -1.28       0.26    -4.92   0.000        -1.79       -0.77
                 |
              wc |
        college  |       0.80       0.23     3.48   0.001         0.35        1.25
                 |
              hc |
        college  |       0.14       0.21     0.66   0.508        -0.27        0.54
             lwg |       0.61       0.15     4.04   0.000         0.31        0.91
             inc |      -0.04       0.01    -4.24   0.000        -0.05       -0.02
           _cons |       1.01       0.29     3.54   0.000         0.45        1.57
    ------------------------------------------------------------------------------

where full is the name we chose for the estimation results from the full model.9 After we store the results, we fit a model that is nested in the full model. A nested model is one that can be created by imposing constraints on the coefficients in the prior model. Most commonly, some of the variables from the full model are excluded, which in effect constrains the coefficients for these variables to be 0. For example, if we drop k5 and k618 from the last model, this produces

```
logit lfp i.agecat i.wc i.hc lwg inc, nolog
estimates store nokidvars
```

    Logistic regression                                     Number of obs =    753
                                                            LR chi2(6)    =  61.59
                                                            Prob > chi2   = 0.0000
    Log likelihood = -484.07589                             Pseudo R2     = 0.0598
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
          agecat |
          40-49  |      -0.02       0.18    -0.10   0.922        -0.36        0.33
            50+  |      -0.48       0.21    -2.36   0.018        -0.89       -0.08
                 |
              wc |
        college  |       0.66       0.22     3.06   0.002         0.24        1.09
                 |
              hc |
        college  |       0.03       0.20     0.18   0.859        -0.35        0.42
             lwg |       0.61       0.15     4.18   0.000         0.32        0.89
             inc |      -0.03       0.01    -4.37   0.000        -0.05       -0.02
           _cons |       0.22       0.22     1.01   0.312        -0.21        0.65
    ------------------------------------------------------------------------------

We stored the results for the nested models as nokidvars. Next, we com pute the test:

```
lrtest full nokidvars
```

    Likelihood-ratio test
    Assumption: nokidvars nested within full
    
     LR chi2(2) =  62.70
    Prob > chi2 = 0.0000


The output indicates that the LR test assumes that **nokidvars** is nested in full. It is up to the user to ensure that the models are nested. Because our models are nested, the result is an LR test of the hypothesis Ho: $H_0:\beta_k5 = \beta_k618 = 0$. **The significant chi-squared statistic means that we reject the null hypothesis that these two coefficients are simultaneously equal to 0. Although we fit the full model first followed by the constrained model, lrtest allows the constrained model to be fit first followed by the full model.**

The output for all models fit by maximum likelihood includes an LR test that all the coefficients except the intercept(s) are 0. For our full model above, this is listed as LR chi2(8) = 124.30. The results can be computed with lrtest as follows:

```
logit lfp, nolog
```

    Logistic regression                                     Number of obs =    753
                                                            LR chi2(0)    =   0.00
                                                            Prob > chi2   =      .
    Log likelihood = -514.8732                              Pseudo R2     = 0.0000
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.28       0.07     3.74   0.000         0.13        0.42
    ------------------------------------------------------------------------------

```
lrtest full .
```

    Likelihood-ratio test
    Assumption: . nested within full
    
     LR chi2(8) = 124.30
    Prob > chi2 = 0.0000

### 2.4.1 Avoiding invalid LR tests

lrtest does not always prevent you from computing an invalid test. There are two things that you must check: that **the two models are nested and that the two models were fit using the same sample. In general, if either of these conditions is violated, the results of lrtest are meaningless.** Although lrtest exits with an error message if the number of observations differs in the two models, this check does not catch those cases in which the number of observations is the same but the samples are different. One exception to the requirement of equal sample sizes is when perfect prediction removes some observations. In such a case, the apparent sample sizes for nested models differ, but an LR test is still appropriate (see section 5.2.3 for details). When this occurs, the force option can be used to force lrtest to compute the seemingly invalid test. For details on ensuring the same sample size, see our discussion of mark and markout in section 3.1.6.


## 3 Measures of fit

Assessing fit involves both the analysis of the fit of individual observations and the evaluation of scalar measures of fit for the model as a whole.

Many scalar measures have been developed to summarize the overall goodness of fit of regression models. A scalar measure can in some cases be useful in comparing competing models and. ultimately, in selecting a final model. W ithin a substantive area, measures of fit might provide a rough index of whether a model is adequate. However, there is no convincing evidence that selecting a model th a t maximizes the value of a given measure results in a model th at is optimal in any sense other than the m odel’s having a larger (or, in some instances, smaller) value of th a t measure. Measures of fit provide some information, but it is partial information th at must be assessed within the context of the theory motivating the analysis, past research, and the estim ated parameters of the model being considered.

### 3.1 Syntax of fitstat

The SPost fitstat command calculates many fit statistics for the estimation commands in this book. We should mention again that **we often find these measures of limited utility in our own research, with the exception of the information criteria BIC and AIC.** When we do use these measures, we find it helpful to compare multiple measures, **fitstat** makes this simple. The options **diff, saving (), and using ()** facilitate the comparison of measures across two models. Although fitstat duplicates some measures computed by other Stata commands (for example, the pseudo-R² in standard Stata output and the information criteria from **estat ic**), fitstat adds many more measures and makes it convenient to compare measures across models.

The syntax is

fitstat[, saving(name) using(name) ic force diff]

fitstat terminates with an error if the last estimation command does not return a value for the log-likelihood function for a model with only an intercept (that is, if e(ll_0) is missing). This occurs, for example, if the noconstant option is used to fit a model. Although fitstat can be used when models are fit with weighted data, there are two limitations. 

First, some measures cannot be computed with some types of weights and none can be computed after svy estimation. 

Second, when pweights or robust standard errors are used to fit the model, fitstat uses the "pseudolikelihood" rather than the likelihood to compute measures of fit. Given the heuristic nature of the various measures of fit, we see no reason why the resulting measures would be inappropriate.

**Options**

* fitstat[, saving(name)] saves the computed measures in a matrix, **_fitstat_name**, for later comparisons. When the **saving()** option is not used, **fitstat** saves results to the matrix **_fitstat_0**.
* using (name) compares the measures for the model in memory, referred to in the output as the current model, with those of the model saved as name.
* **diff** compares the current model to the prior model.
* **ic** presents only the Bayesian information criterion (BIC) and Akaike’s information criterion (AlC). When comparing two models, fitstat reports Raftery’s (1995) guidelines for assessing the strength of one model over another with BIC.
* force is required to compare information criteria when the number of observations or the estimation m ethod varies between the two models, or to conduct a likelihood ratio test under circumstances in which Stata’s **lrtest** command would require the force option.
  


### 3.2 Methods and formulas used by fitstat

In this section, we provide brief descriptions of each measure computed by fitstat. Full details for most measures along with citations to original sources are in Long (1997). We begin with formulas for several quantities that are used in the computation of other measures. We then consider the information criteria BIC and AIC. Again, these are the measures that we find most useful in practice. We then review the coefficient of determination $R^2$ for the linear regression model followed by numerous pseudo-R²s.

#### 3.2.1 Quantities used in other measures

**Log-likelihood based measures**. Stata begins maximum likelihood iterations by computing the log likelihood of the model with all parameters but the intercept constrained to 0, referred to as $\ln L\left(M_{\text {Intercept }}\right)$ The log likelihood upon convergence, referred to as $\ln L\left(M_{\text {Full }}\right)$, is also listed. This information is presented in the iteration log and in the head er for the estim ation results.


**LR chi-square test of all coefficients**. An LR test of the hypothesis that all coefficients except the intercepts are 0 can be computed by comparing the log likelihoods:  $LR = 2 \ln L\left(M_{\text {Full }}\right)-2 \ln L\left(M_{\text {Intercept }}\right)$LR is reported by Stata as $ LR chi2 (df) = #$, where the degrees of freedom in parentheses are the number of constrained parameters. For the **zip** and **zinb** models discussed in chapter 9, LR tests that the coefficients in the count portion (not the binary portion) of the model are 0.

Deviance. The deviance compares the given model with a model that has one parameter for each observation so th at the model reproduces the observed d ata perfectly. The deviance is defined as $D=-2 \ln L\left(M_{\text {Full }}\right)$, where the degrees of freedom equals $N$ m inus the number of param eters. $D$ does not have a. chi-squared distribution.

#### 3.2.2 Information criteria

**Information measures can be used to compare both nested and nonnested models.**

AIC. The formula for Akaike’s information criterion (1973) used by fitstat and Stata’s **estat ic** command is

$$
\mathrm{AIC}=-2 \ln \widehat{L}\left(M_k\right)+2 P_k
$$

where $\widehat{L}\left(M_k\right)$ is the likelihood of model $ M_k$ and $P_k$ is the number of parameters in the model (for example, $K$ + 1 in the binary regression model, where $K$ is the number of regressors). All else being equal, the model with the smaller AIC is considered the better-fitting model. Another definition of AIC is equal to the value in (3.1) divided by $N$ . We include this quantity in the $fitstat$ output as **AIC divided by N**.


**BIC**. The Bayesian information criterion (BIC) was proposed by Raftery (1995) and others as a means to compare nested and nonnested models. **Because BIC imposes a greater penalty for the number of parameters in a model, it favors a simpler mode, compared with the AIC measure.**

The BIC statistic is defined in at least three ways. Although this can be confusing the choice of which version to use is not important, as we show after presenting thvarious definitions. Stata defines the BIC for model $M_k$ as

$$\mathrm{BlC_k} = -2\:\ln\hat{L}(M_{k})+\mathrm{df}_{k}\ \ln N$$

where $df_{k}$ is the number of param eters in $M_k$, including auxiliary parameters such as a in the negative binomial regression model. As with AIC, the smaller or more negativ the BIC, the better the fit. A second definition of BIC is computed using the deviance

$$\mathrm{BIC_k}^{D}=D(M_{k})+\mathrm{df}_{k}^{D}\ \ln N$$

where $df_{k}$ is the degrees of freedom associated with the deviance, **fitstat** labels this as BIC (based on deviance). The third version, sometimes denoted as BIC', uses the LR chi-squared with  $df_{k}  equal to the number of regressors (not parameters) in the model.

$$\mathrm{BIC_k}^{\prime}=-G^{2}(M_{k})+\mathrm{df}_{k}^{\prime}\ \ln N$$

The difference in the BICs from two models indicates which model is preferred. Because $\mathrm{BIC_1}-\mathrm{BIC_2}=\mathrm{BIC_1}^{\prime}-\mathrm{BIC_2}^{\prime}=\mathrm{BIC_1}^{D}-\mathrm{BIC_2}^{D}$, the choice of which version of BIC to use is a m atter of convenience. When$ BIC_1 < BIC_2$ , the first model is preferred, and accordingly, when $ BIC_1 > BIC_2$, the second model is preferred. Raftery (1995) suggested these guidelines for the strength of evidence favoring $M_2$ against $M_1$ base on a difference in BIC:

By default, **firststat** shows you $BIC_k$, which is also computed by Stata’s estat ic. If you specify **fiststat, ic**, then all versions of AIC and BIC are reported but non-1' measures of fit are not shown.

#### 3.2.2 Example of information criteria.

To compute information criteria for a single model, we fit the model and then run **fitstat** , saving our results with the name **basemodel**:

```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
fitstat, ic saving(basemodel)
```

    Logistic regression                                     Number of obs =    753
                                                            LR chi2(8)    = 124.30
                                                            Prob > chi2   = 0.0000
    Log likelihood = -452.72367                             Pseudo R2     = 0.1207
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              k5 |      -1.39       0.19    -7.25   0.000        -1.77       -1.02
            k618 |      -0.07       0.07    -0.96   0.336        -0.20        0.07
                 |
          agecat |
          40-49  |      -0.63       0.21    -3.00   0.003        -1.04       -0.22
            50+  |      -1.28       0.26    -4.92   0.000        -1.79       -0.77
                 |
              wc |
        college  |       0.80       0.23     3.48   0.001         0.35        1.25
                 |
              hc |
        college  |       0.14       0.21     0.66   0.508        -0.27        0.54
             lwg |       0.61       0.15     4.04   0.000         0.31        0.91
             inc |      -0.04       0.01    -4.24   0.000        -0.05       -0.02
           _cons |       1.01       0.29     3.54   0.000         0.45        1.57
    ------------------------------------------------------------------------------

                             |       logit 
    -------------------------+------------
    AIC                      |            
                         AIC |     923.447 
              (divided by N) |       1.226 
    -------------------------+------------
    BIC                      |            
                   BIC(df=9) |     965.064 
     BIC (based on deviance) |   -4022.857 
        BIC' (based on LRX2) |     -71.307 

Information criteria for a single model are not very useful. Their value comes when comparing models. Suppose we generate the variable kids, which is the sum of k5 and k618. Our new model drops k5 and k618 and adds kids. In other words, instead of a model in which the effect of an additional child age 5 or under is allowed to differ from the effect of an additional child age 6 to 18, we fit a model in which the effect of each additional child regardless of age is presumed equal. After fitting the new model, fitstat compares it with the saved model:

```
generate kids = k5 + k618
label var kids "Number of kids 18 or younger"
logit lfp kids i.agecat i.wc i.hc lwg inc, nolog
fitstat, ic using(basemodel)
```
    
                             |     Current        Saved   Difference 
    -------------------------+--------------------------------------
    AIC                      |                                      
                         AIC |     973.368      923.447       49.921 
              (divided by N) |       1.293        1.226        0.066 
    -------------------------+--------------------------------------
    BIC                      |                                      
              BIC(df=8/9/-1) |    1010.361      965.064       45.297 
     BIC (based on deviance) |   -3977.561    -4022.857       45.297 
        BIC' (based on LRX2) |     -26.010      -71.307       45.297 
    
    Difference of   45.297 in BIC provides very strong support for saved model.

All AIC and Bic measures are smaller for the base model (listed as Saved). At the bottom of the table, it indicates that based on Raftery’s criterion, there is very strong support for the saved model over the current model.

#### 3.2.3 $R^2$ in the linear regression model

For regress, fitstat reports the coefficient of determination, which can be defined variously as

![]()

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.20.2.png" style="width:750px;height:150px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The adjusted $R^2$ is defined as

$$\overline{R^2}=\left(R^{2}-\frac{K}{N-1}\right)\left(\frac{N-1}{N-K-1}\right)$$

#### 3.2.4 $Pseudo-R2^{\prime}s$

Although each definition of $R^2$ gives the same numeric value in the linear regression model, each gives different values and thus provides different measures of fit when applied to other models. **There are also other ways of computing measures th at have some resemblance to the $R^2$ in the LR model. These are known as $Pseudo-R2^{\prime}s$. Because different $Pseudo-R2$ measures can yield substantially different results and different software packages use different measures as their default $Pseudo-R2$, when presenting results it is important to report exactly which measure is being used rather than simply saying $Pseudo-R2^{\prime}s$”**

**Pseudo-R2 is a metric used to measure the goodness of fit of a statistical model. Unlike the traditional R2 (coefficient of determination) that represents the actual proportion of explained variance, Pseudo-R2 is an approximate method used in certain situations, especially in Generalized Linear Models (GLM) where the assumptions of normality and constant variance may not hold, making traditional R2 inapplicable.**

**In GLM, the response variable may not follow a normal distribution, and the variance might be complex, making the traditional R2 less straightforward. Therefore, Pseudo-R2 is introduced to gauge the model's fit to the data.**

**Different types of models may have different formulations for Pseudo-R2.**

**McFadden’s R2.**

McFadden’s (1974) R2, also known as the LR index, **compares a model with just the intercept to a model with all parameters.** It is defined as

$$\begin{aligned}R_{\mathrm{McF}}^2&=1-\frac{\ln\widehat{L}(M_{\mathrm{Full}})}{\ln\widehat{L}(M_{\mathrm{Intercept}})}\end{aligned}$$

If model $M_intercept$ = $M_Full$, then $\overline R_\mathrm{McF}^{2} = 0$, but $\overline R_\mathrm{McF}^{2}$ can never exactly equal 1. This measure is reported by Stata in the header of estimation results as Pseudo $R^2$ and is listed in the fitstat output as R2 McFadden. Because $\overline R_\mathrm{McF}^{2}$ always increases as variables are added to a model, an adjusted version is also available:

$$\overline R_\mathrm{McF}^{2}=1-\frac{\ln\widehat{L}(M_{\mathrm{Full}})-K^{*}}{\ln\widehat{L}(M_{\mathrm{Intercept}})}$$

where $K^*$ is the number of parameters, not independent variables

**Maximum likelihood R2** 

Another analogy to R2 was suggested by Maddala (1983):


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.20.3.png" style="width:450px;height:150px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

This R2 is also called the Cox-Snell (1989) R2.

**Cragg and Uhler’s R2.** 
Because $R_\mathrm{ML}^2$ reaches a maximum of $1-L\left(M_{\mathrm{Intercept}}\right)^{2/N}.$ , Cragg and Uhler (1970) suggested a normed measure:

$$R_{\mathrm{CkU}}^{2}=\frac{R_{\mathrm{ML}}^{2}}{\max R_{\mathrm{ML}}^{2}}=\frac{1-\{L(M_{\mathrm{Intercept}})/L(M_{\mathrm{Full}})\}^{2/N}}{1-L(M_{\mathrm{Interospt}})^{2/N}}$$

This R2 is also known as Nagelkerke’s (1991) R2


**Efron’s R2** 
For binary outcomes, Efron’s (1978) pseudo-R2 defines $\widehat\pi=\widehat\Pr(y=1\mid\mathbf{x})$ and equals 

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.20.4.png" style="width:450px;height:150px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

**Tjur’s coefficient of discrimination.**

For binary outcomes, Tjur (2009) motivates a goodness-of-fit measure ranging from 0 to 1 that he calls the coefficient of discrimination. D simply compares the average predicted probability when the outcome is observed as 1 to the average when the outcome is observed as 0:

$$D=\text{mean }\widehat{\Pr}(y=1|y=1)-\text{mean }\widehat{\Pr}(y=1|y=0)$$

The measure is a simple expression of the principle that as binary models fit better, the predicted probability of a positive outcome will increase for cases with a positive outcome and decrease for cases with a negative outcome.

**V$(y^*)$, V (e), and McKelvey and Zavoina's R2.** 

Some models can be defined in terms of a latent variable y*. These include the models for binary or ordinal outcomes, such as logit, probit, ologit, and oprobit, as well as some models with censoring, such as to bit and intreg . Each model is defined in terms of a regression on a latent variable y*

$$y^{*}=\mathbf{x}\boldsymbol{\beta}+\varepsilon $$

Using $\widehat{\mathrm{Var}}(\widehat{y}^{*})=\widehat{\hat{\boldsymbol{\beta}}}^{\prime}\widehat{\mathrm{Var}}(\mathbf{x})\widehat{\boldsymbol{\beta}}$,McKelvey and Zavoina (1975) proposed


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.20.5.png" style="width:450px;height:150px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

In models for categorical outcomes,$\operatorname{Var}(\varepsilon)$ is assumed to identify the model

**Count and adjusted count R2.** 

Observed and predicted values can be used in models with categorical outcomes to compute what is known as the count R2. Consider the binary case where the observed $y$ is 0 or 1 and $\widehat{\pi}_i=\widehat{\Pr}(y=1\mid\mathbf{x}_i)$. Define the expected outcome as


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.20.6.png" style="width:450px;height:150px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

This allows us to construct a table of observed and predicted values, such as that produced for the logit model by the command **estat classification**:

```
logit lfp kids i.agecat i.wc i.hc lwg inc, nolog
estat classification
```

                  -------- True --------
    Classified |         D            ~D  |      Total
    -----------+--------------------------+-----------
         +     |       334           182  |        516
         -     |        94           143  |        237
    -----------+--------------------------+-----------
       Total   |       428           325  |        753
    
    Classified + if predicted Pr(D) >= .5
    True D defined as lfp != 0
    --------------------------------------------------
    Sensitivity                     Pr( +| D)   78.04%
    Specificity                     Pr( -|~D)   44.00%
    Positive predictive value       Pr( D| +)   64.73%
    Negative predictive value       Pr(~D| -)   60.34%
    --------------------------------------------------
    False + rate for true ~D        Pr( +|~D)   56.00%
    False - rate for true D         Pr( -| D)   21.96%
    False + rate for classified +   Pr(~D| +)   35.27%
    False - rate for classified -   Pr( D| -)   39.66%
    --------------------------------------------------
    Correctly classified                        63.35%
    --------------------------------------------------

We see that positive responses were predicted for 516 observations, of which 334 were correctly classified because the observed response was positive (y = 1), whereas the other 182 were incorrectly classified because the observed response was negative (y = 0). Likewise, of the 237 observations for which a negative response was predicted, 143 were correctly classified and 94 were incorrectly classified

A seemingly appealing measure is the proportion of correct predictions, referred to as the count R2,
$$R_{\mathrm{Count}}^{2}=\frac{1}{N}\sum_{j}n_{jj}$$

where the $n_{jj}'\text{s}$ are the number of correct predictions for outcome $j$. The count R 2
can give a faulty impression that the model is predicting very well. In a binary model,
without knowledge about the independent variables, it is possible to correctly predict
at least 50% of the cases by choosing the outcome category with the largest percentage
of observed cases. To adjust for the largest row marginal,

$$R_{\mathrm{AdjCount}}^{2}=\frac{\left(\sum_{j}n_{jj}\right)-\max_{r}\left(n_{r+}\right)}{N-\max_{r}\left(n_{r+}\right)}$$

where $n_r+$ is the marginal for row r. The adjusted count $R2$ is the proportion of correct guesses beyond the number that would be correctly guessed by choosing the largest marginal.


### 3.3 Example of fitstat

To examine all the measures of fit, we repeat our example for information criteria, but this time we use **fitstat** without the **ic** option. We fit our base model and save the **fitstat** results with the name **basemodel**:

```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
fitstat, ic saving(basemodel)
```

Next, we fit a model that includes the variable kid s, which is the sum of k5 and k618. and drops k5 and k618. **fitstat** compares this model with the saved model:

```
generate kids = k5 + k618
label var kids "Number of kids 18 or younger"
logit lfp kids i.agecat i.wc i.hc lwg inc, nolog
fitstat,using(basemodel)
```

    
                             |     Current        Saved   Difference 
    -------------------------+--------------------------------------
    Log-likelihood           |                                      
                       Model |    -478.684     -452.724      -25.960 
              Intercept-only |    -514.873     -514.873        0.000 
    -------------------------+--------------------------------------
    Chi-square               |                                      
             D(df=745/744/1) |     957.368      905.447       51.921 
               LR(df=7/8/-1) |      72.378      124.299      -51.921 
                     p-value |       0.000        0.000        0.000 
    -------------------------+--------------------------------------
    R2                       |                                      
                    McFadden |       0.070        0.121       -0.050 
          McFadden(adjusted) |       0.055        0.103       -0.048 
          McKelvey & Zavoina |       0.125        0.215       -0.090 
                Cox-Snell/ML |       0.092        0.152       -0.061 
      Cragg-Uhler/Nagelkerke |       0.123        0.204       -0.081 
                       Efron |       0.090        0.153       -0.063 
                    Tjur's D |       0.091        0.153       -0.063 
                       Count |       0.633        0.676       -0.042 
             Count(adjusted) |       0.151        0.249       -0.098 
    -------------------------+--------------------------------------
    IC                       |                                      
                         AIC |     973.368      923.447       49.921 
            AIC divided by N |       1.293        1.226        0.066 
              BIC(df=8/9/-1) |    1010.361      965.064       45.297 
    -------------------------+--------------------------------------
    Variance of              |                                      
                           e |       3.290        3.290        0.000 
                      y-star |       3.761        4.192       -0.431 
    
    Note: Likelihood-ratio test assumes current model nested in saved model.
    
    Difference of   45.297 in BIC provides very strong support for saved model.

In this example, the two models are nested because the second model is in effect imposing
the constraint $\mathrm{t~}\beta_{\mathrm{k5}}=\beta_{\mathrm{k618}}$ on the first model.

## 4 estat postestimation commands

**estat** is a set of subcommands that provide different statistics about the model whose estimates are active. Each is invoked using **estat** subcommand. Here we provide an overview of some of the most useful subcommands, which we use in later chapters

**estat summarize**

estat summarize provides descriptive statistics for the variables in the model by using
the estimation sample. For example,
```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
```

    Logistic regression                                     Number of obs =    753
                                                            LR chi2(8)    = 124.30
                                                            Prob > chi2   = 0.0000
    Log likelihood = -452.72367                             Pseudo R2     = 0.1207
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              k5 |      -1.39       0.19    -7.25   0.000        -1.77       -1.02
            k618 |      -0.07       0.07    -0.96   0.336        -0.20        0.07
                 |
          agecat |
          40-49  |      -0.63       0.21    -3.00   0.003        -1.04       -0.22
            50+  |      -1.28       0.26    -4.92   0.000        -1.79       -0.77
                 |
              wc |
        college  |       0.80       0.23     3.48   0.001         0.35        1.25
                 |
              hc |
        college  |       0.14       0.21     0.66   0.508        -0.27        0.54
             lwg |       0.61       0.15     4.04   0.000         0.31        0.91
             inc |      -0.04       0.01    -4.24   0.000        -0.05       -0.02
           _cons |       1.01       0.29     3.54   0.000         0.45        1.57
    ------------------------------------------------------------------------------

```
estat summarize
```

      Estimation sample logit                  Number of obs =        753
    
      -------------------------------------------------------------------
          Variable |         Mean      Std. dev.         Min          Max
      -------------+-----------------------------------------------------
               lfp |     .5683931      .4956295            0            1
                k5 |     .2377158       .523959            0            3
              k618 |     1.353254      1.319874            0            8
                   |
            agecat |
            40-49  |     .3851262      .4869486            0            1
              50+  |     .2191235      .4139274            0            1
                   |
                wc |
          college  |     .2815405      .4500494            0            1
                   |
                hc |
          college  |     .3917663      .4884694            0            1
               lwg |     1.097115      .5875564    -2.054124     3.218876
               inc |     20.12897       11.6348    -.0290001           96
      -------------------------------------------------------------------

The output is equivalent to the results from **summarize** modelvars **if e(sample) == 1**, where modelvars is the list of variables in your model. Several options are useful:

* **labels** displays variable labels rather than the names of the variables.
* **noheader** suppresses the header.
* **noweights** ignores the weights if they have been used in estimation.

**estat ic**
**estat ic** lists the inform ation criteria AIC and BIC for the last model. See page 123 for details

**estat vce**
**estat vce** lists the variance-covariance matrix for the coefficient estimates. For further details, see  **help estat vce**
