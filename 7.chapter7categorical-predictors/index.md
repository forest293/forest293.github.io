# Chapter7 ：Categorical predictors


**This chapter covers models that involve categorical predictors. The emphasis is on how to make contrasts among levels of the categorical predictor to answer interesting questions regarding the differences among the categories.**

## 1 Comparing two groups using a t test
The simplest kind of categorical predictor has two levels. Examples of such twolevel predictors include gender (male versus female), treatment assignment (treatment group versus control group), or whether one is married (married versus not married).


The variable happy7 indicates the happiness rating of the respondent on a 1 to 7 scale, where 7 is completely happy and 1 is completely unhappy. To compare the average happiness between those who are married and unmarried, we can perform an independent groups $t$test, as shown below.


```
  use gss_ivrm.dta
  ttest happy7, by(married)
```
The variable happy7 indicates the happiness rating of the respondent on a 1 to 7 scale, where 7 is completely happy and 1 is completely unhappy. 

To compare the average happiness between those who are married and unmarried, we can perform an independent groups $t$ test,

     Two-sample t test with equal variances
     ------------------------------------------------------------------------------
        Group |     Obs        Mean    Std. err.   Std. dev.   [95% conf. interval]
     ---------+--------------------------------------------------------------------
     Unmarrie |     604     5.35596    .0425197    1.044982    5.272456    5.439465
      Married |     556    5.705036    .0368763    .8695302    5.632602     5.77747
     ---------+--------------------------------------------------------------------
     Combined |   1,160    5.523276    .0287773    .9801179    5.466815    5.579737
     ---------+--------------------------------------------------------------------
         diff |           -.3490757    .0567084               -.4603384    -.237813
     ------------------------------------------------------------------------------
              diff = mean(Unmarrie) - mean(Married)                         t =  -6.1556
          H0: diff = 0                                     Degrees of freedom =     1158
     
         Ha: diff < 0                 Ha: diff != 0                 Ha: diff > 0
      Pr(T < t) = 0.0000         Pr(|T| > |t|) = 0.0000          Pr(T > t) = 1.0000

The t-test command shows that the average happiness is 5.356 for those who are unmarried and 5.705 for those who are married. The difference between these means is $-0.349$, and that difference is significantly different from 0 (with a two-tailed value of 0.0000). 

The difference between these means is negative. We can interpret this result to say that those who are unmarried are significantly less happy than those who are married. We could also say that those who are married are significantly happier than those who are unmarried


## 2 More groups and more predictors

We are seldom interested in simply comparing two groups in the absence of any additional predictors (covariates). Let’s extend the previous example in two ways. 

First, let’s use a five-level measure of marital status, which is coded: 1 = married, 2 = widowed, 3 = divorced, 4 = separated, and 5 = never married. 

Second, let’s include additional predictors (covariates): gender,2 race, and age. We begin by testing the overall null hypothesis that **the average happiness is equal among the five marital status groups**:

$$ H_{0} = \mu_{2} = \mu_{3} = \mu_{4} = \mu_{5}$$  

I included the i. prefix before each of the categorical variables and the c. prefix in front of age to specify that it is a continuous variable.


```
  use gss_ivrm.dta
  anova happy7 i.marital i.gender i.race c.age
```

                          Number of obs =      1,156    R-squared     =  0.0488
                          Root MSE      =    .957826    Adj R-squared =  0.0422
 
                   Source | Partial SS         df         MS        F    Prob>F
                   -----------+----------------------------------------------------
                    Model |  53.979104          8    6.747388      7.35  0.0000
                          |
                  marital |  47.156057          4   11.789014     12.85  0.0000
                   gender |  3.8954776          1   3.8954776      4.25  0.0396
                     race |  .92449374          2   .46224687      0.50  0.6043
                      age |  5.3200497          1   5.3200497      5.80  0.0162
                          |
                 Residual |  1052.2934      1,147   .91743103  
                   -----------+----------------------------------------------------
                   Total |  1106.2725      1,155   .95781168 

>Note! The anova and regress commands
>> In this chapter (as well as chapters 8 and 9), my focus will be on starting the analysis with an assessment of the main effects (and interactions, if any) using the omnibus $F$-tests provided by the anova command. I would emphasize, however, that these same results could be obtained via the regress command.

>Note! Omnibus $F$-tests
>>Omnibus F-tests are statistical tests used to assess the overall significance or goodness-of-fit of a regression model. These tests evaluate the joint significance of multiple coefficients in the model or the overall explanatory power of the regression equation.  
>>In regression analysis, the Omnibus F-test is typically used to determine whether there is a significant relationship between the predictor variables and the dependent variable. It examines the overall fit of a model by comparing the variance explained by the model to the unexplained variance (residuals) around the regression line.

>>The steps involved in performing an Omnibus F-test include:
>>1. Fit the regression model using the given predictor variables.
>>2. Calculate the overall F-statistic using the explained and unexplained variance from the model.
>>3. Obtain the degrees of freedom for the F-distribution based on the number of predictors and the sample size.
>>4. Compare the calculated F-statistic with the critical value from the F-distribution at a specified significance level (commonly 0.05 or 0.01).
>>5. If the calculated F-statistic exceeds the critical value, the null hypothesis is rejected, indicating that the overall model is statistically significant.

The overall test of marital is significant ( , ). After adjusting for gender, race, and age, we can reject the null hypothesis that the average happiness is equal among the five marital status groups.

Let’s probe this finding in more detail. Suppose that our research hypothesis (prior to even seeing the data) was that those who are married will be happier than each of the four other marital status groups. We can frame this as four separate null hypotheses, shown below


$$H_{0}: \mu_{2} = \mu_{1} $$
$$H_{0}: \mu_{3} = \mu_{1} $$
$$H_{0}: \mu_{4} = \mu_{1} $$
$$H_{0}: \mu_{5} = \mu_{1} $$

Let’s begin the exploration of these tests by using the margins command to compute the adjusted mean of happiness by marital status. 

```
  margins marital
  marginsplot
```
     Predictive margins                                       Number of obs = 1,156

     Expression: Linear prediction, predict()

     --------------------------------------------------------------------------------
                    |            Delta-method
                    |     Margin   std. err.      t    P>|t|     [95% conf. interval]
     ---------------+----------------------------------------------------------------
            marital |
           married  |       5.70       0.04   139.63   0.000         5.62        5.78
           widowed  |       5.25       0.12    44.51   0.000         5.01        5.48
          divorced  |       5.20       0.07    75.92   0.000         5.06        5.33
         separated  |       5.15       0.16    31.25   0.000         4.83        5.47
     never married  |       5.54       0.06    87.86   0.000         5.42        5.67
     --------------------------------------------------------------------------------

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.5.5.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Adjusted mean of happiness by marital status</strong></figcaption>
</figure>

The results so far have given some indirect support for our research hypotheses. The overall test of marital is significant, and the pattern of means is consistent with our hypothesis—the adjusted means of those who are married appear greater than the means for the other groups. 

But let’s directly test the four null hypotheses based on our research question—the happiness ratings for each group compared with those who are married to determine if these differences are statistically significant. We can perform such tests using the **contrast command**, as shown below.
```
  contrast r.marital
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     ---------------------------------------------------------------
                                 |         df           F        P>F
     ----------------------------+----------------------------------
                         marital |
           (widowed vs married)  |          1       13.44     0.0003
          (divorced vs married)  |          1       39.38     0.0000
         (separated vs married)  |          1       10.46     0.0013
     (never married vs married)  |          1        4.18     0.0411
                          Joint  |          4       12.85     0.0000
                                 |
                     Denominator |       1147
     ---------------------------------------------------------------

     -----------------------------------------------------------------------------
                                 |   Contrast   Std. err.     [95% conf. interval]
     ----------------------------+------------------------------------------------
                         marital |
           (widowed vs married)  |      -0.45       0.12         -0.70       -0.21
          (divorced vs married)  |      -0.50       0.08         -0.66       -0.34
         (separated vs married)  |      -0.55       0.17         -0.88       -0.22
     (never married vs married)  |      -0.16       0.08         -0.31       -0.01
     -----------------------------------------------------------------------------


This contrast command compares each marital status group with the first group (those who are married), adjusting for gender, race, and age. 

```
  margins r.marital
```
We can use margins and marginsplot to visually depicts these contrast,this graph illustrates the differences in the adjusted means (comparing each group with those who are unmarried) along with the 95% confidence interval for each difference. 

**This confidence interval excludes 0, indicating that this difference is significant at the 5% level.**

     Contrasts of predictive margins                          Number of obs = 1,156

     Expression: Linear prediction, predict()

     ---------------------------------------------------------------
                                 |         df           F        P>F
     ----------------------------+----------------------------------
                         marital |
           (widowed vs married)  |          1       13.44     0.0003
          (divorced vs married)  |          1       39.38     0.0000
         (separated vs married)  |          1       10.46     0.0013
     (never married vs married)  |          1        4.18     0.0411
                          Joint  |          4       12.85     0.0000
                                 |
                     Denominator |       1147
     ---------------------------------------------------------------

     -----------------------------------------------------------------------------
                                 |            Delta-method
                                 |   Contrast   std. err.     [95% conf. interval]
     ----------------------------+------------------------------------------------
                         marital |
           (widowed vs married)  |      -0.45       0.12         -0.70       -0.21
          (divorced vs married)  |      -0.50       0.08         -0.66       -0.34
         (separated vs married)  |      -0.55       0.17         -0.88       -0.22
     (never married vs married)  |      -0.16       0.08         -0.31       -0.01
     -----------------------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.5.6.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Contrasts of adjusted means to those who are married</strong></figcaption>
</figure>

## 3 Overview of contrast operators

the contrast operators table provides a brief description of each contrast operator and shows the section of this chapter in which each contrast operator is covered.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.6.1.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Summary of contrast operators</strong></figcaption>
</figure>

## 4 Compare each group against a reference group

This section provides further examples illustrating the r. contrast operator for making reference group contrasts.

Let’s continue with the example that predicting happiness from marital status, adjusting for gender, race, and age. 

```
  use gss_ivrm.dta
  anova happy7 i.marital i.gender i.race c.age
```

                              Number of obs =      1,156    R-squared     =  0.0488
                              Root MSE      =    .957826    Adj R-squared =  0.0422
     
                       Source | Partial SS         df         MS        F    Prob>F
                   -----------+----------------------------------------------------
                        Model |  53.979104          8    6.747388      7.35  0.0000
                              |
                      marital |  47.156057          4   11.789014     12.85  0.0000
                       gender |  3.8954776          1   3.8954776      4.25  0.0396
                         race |  .92449374          2   .46224687      0.50  0.6043
                          age |  5.3200497          1   5.3200497      5.80  0.0162
                              |
                     Residual |  1052.2934      1,147   .91743103  
                   -----------+----------------------------------------------------
                        Total |  1106.2725      1,155   .95781168  



### 4.1  Selecting a specific contrast

Suppose you wanted to focus on the contrast of group 3 to group 1 (divorced versus married) and group 5 to group 1 (never married versus married). **You can perform those two contrasts by specifying the r(3 5). contrast operator.** This compares each of the groups within the parentheses with the reference group (group 1).

```
  contrast r(3 5).marital,pveffects
```
     Contrasts of marginal linear predictions

     Margins: asbalanced

     ---------------------------------------------------------------
                                 |         df           F        P>F
     ----------------------------+----------------------------------
                         marital |
          (divorced vs married)  |          1       39.38     0.0000
     (never married vs married)  |          1        4.18     0.0411
                          Joint  |          2       19.78     0.0000
                                 |
                     Denominator |       1147
     ---------------------------------------------------------------

     --------------------------------------------------------------------
                                 |   Contrast   Std. err.      t    P>|t|
     ----------------------------+---------------------------------------
                         marital |
          (divorced vs married)  |      -0.50       0.08    -6.28   0.000
     (never married vs married)  |      -0.16       0.08    -2.04   0.041
     --------------------------------------------------------------------

     


>Note! Options on the contrast command
>>The previous contrast command included two options: nowald and pveffects. This yields concise output that fits well on the pages of this book. When you run such analyses yourself, I recommend specifying the options nowald and effects, which will display both significance tests and confidence intervals associated with each comparison.


### 4.2 Selecting a different reference group

Suppose that instead we wanted to compare each group with a different reference group. We can **specify the rb5. contrast operator,** which requests reference group contrasts using group 5 (never married) as the baseline (reference) group.

```
    contrast rb5.marital,nowald pveffects
```
     Contrasts of marginal linear predictions

     Margins: asbalanced

     ----------------------------------------------------------------------
                                   |   Contrast   Std. err.      t    P>|t|
     ------------------------------+---------------------------------------
                           marital |
       (married vs never married)  |       0.16       0.08     2.04   0.041
       (widowed vs never married)  |      -0.30       0.14    -2.06   0.040
      (divorced vs never married)  |      -0.34       0.09    -3.62   0.000
     (separated vs never married)  |      -0.39       0.18    -2.24   0.026
     ----------------------------------------------------------------------

### 4.3 Selecting a contrast and reference group
You can both specify the reference group and specify the contrasts to be made at one time. 


```
  contrast r(1 3)b5.marital,nowald pveffects //(1 versus 5)(3 versus 5)
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     ---------------------------------------------------------------------
                                  |   Contrast   Std. err.      t    P>|t|
     -----------------------------+---------------------------------------
                          marital |
      (married vs never married)  |       0.16       0.08     2.04   0.041
     (divorced vs never married)  |      -0.34       0.09    -3.62   0.000
     ---------------------------------------------------------------------


## 5 Compare each group against the grand mean

This section illustrates the g. contrast operator that compares each group with the grand mean of all groups. 

a researcher might be interested in comparing the mean happiness of each marital status group versus the grand mean of all groups.


```
  use gss_ivrm.dta
  anova happy7 i.marital i.gender i.race c.age
  margins g.marital,contrast(nowald pveffects)
```

the prefix "g" means the mean happiness of each marital status group with the grand mean

     Contrasts of predictive margins                          Number of obs = 1,156

     Expression: Linear prediction, predict()

     -----------------------------------------------------------------
                              |            Delta-method
                              |   Contrast   std. err.      t    P>|t|
     -------------------------+---------------------------------------
                      marital |
           (married vs mean)  |       0.33       0.05     6.12   0.000
           (widowed vs mean)  |      -0.12       0.10    -1.18   0.240
          (divorced vs mean)  |      -0.17       0.07    -2.44   0.015
         (separated vs mean)  |      -0.22       0.14    -1.61   0.107
     (never married vs mean)  |       0.18       0.07     2.48   0.013
     -----------------------------------------------------------------

```
  marginsplot,yline(0)
```

**When the confidence interval for a contrast excludes zero, the difference is significant at the 5% level.**

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.6.2.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Contrasts comparing each group with the grand mean</strong></figcaption>
</figure>

## 6 Compare adjacent means

This section illustrates contrasts that compare the means of adjacent groups, for example, group 1 versus 2, group 2 versus 3, group 3 versus 4. 

These kinds of contrasts are especially useful for studies where you expect a **nonlinear relationship between an ordinal or interval predictor and outcome.**

For example, consider a hypothetical study about the dosage of a new pain medication where the researchers expect that at a certain dosage level the effects of the medication will kick in and lead to a statistically significant reduction in pain. The medication dosages range from 0 mg to 250 mg incrementing by 50 mg, yielding six dosage groups.

```
  use pain
  codebook dosegrp
```

                       Type: Numeric (float)
                      Label: dosegrp

                      Range: [1,6]                         Units: 1
              Unique values: 6                         Missing .: 0/180

                 Tabulation: Freq.   Numeric  Label
                                30         1  0mg
                                30         2  50mg
                                30         3  100mg
                                30         4  150mg
                                30         5  200mg
                                30         6  250mg
     
Let’s begin the analysis relating pain to medication dosage by testing the most general null hypothesis that could be tested—that the average pain is equal across all six dosage groups:

$$ H_{0} = \mu_{2} = \mu_{3} = \mu_{4} = \mu_{5}$$  

This null hypothesis is tested using the anova command shown below.

```
  anova pain i.dosegrp 
```

                              Number of obs =        180    R-squared     =  0.4602
                              Root MSE      =    10.4724    Adj R-squared =  0.4447

                       Source | Partial SS         df         MS        F    Prob>F
                   -----------+----------------------------------------------------
                        Model |  16271.694          5   3254.3389     29.67  0.0000
                              |
                      dosegrp |  16271.694          5   3254.3389     29.67  0.0000
                              |
                     Residual |  19082.633        174   109.67031  
                   -----------+----------------------------------------------------
                        Total |  35354.328        179   197.51021  

he $F$ value is 29.67 and is significant. We can reject the overall null hypothesis. Let’s use the margins command and the marginsplot command to display and graph the predicted mean of pain by dosegrp.

```
  margins dosegrp   //the predicted mean of pain by dosegrp
  marginsplot
```
     Adjusted predictions                                       Number of obs = 180

     Expression: Linear prediction, predict()

     ------------------------------------------------------------------------------
                  |            Delta-method
                  |     Margin   std. err.      t    P>|t|     [95% conf. interval]
     -------------+----------------------------------------------------------------
          dosegrp |
             0mg  |      71.83       1.91    37.57   0.000        68.06       75.61
            50mg  |      70.60       1.91    36.93   0.000        66.83       74.37
           100mg  |      72.13       1.91    37.73   0.000        68.36       75.91
           150mg  |      70.40       1.91    36.82   0.000        66.63       74.17
           200mg  |      54.70       1.91    28.61   0.000        50.93       58.47
           250mg  |      48.30       1.91    25.26   0.000        44.53       52.07
     ------------------------------------------------------------------------------



<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.6.3.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Mean pain rating by dosage group</strong></figcaption>
</figure>

For this study, the research question of interest focuses on the test of each dosage against the previous dosage to determine the dosage that leads to a statistically significant decrease in pain. This leads us to five specific null hypotheses.

$$H_{0}: \mu_{1} = \mu_{2} $$
$$H_{0}: \mu_{2} = \mu_{3} $$
$$H_{0}: \mu_{3} = \mu_{4} $$
$$H_{0}: \mu_{4} = \mu_{5} $$
$$H_{0}: \mu_{5} = \mu_{6} $$

Let’s now test each of the hypotheses using the contrast command with the a. contrast operator to compare each dosage with the adjacent (subsequent) dosage.


```
  margins a.dosegrp,contrast(nowald pveffects)
  margins a.dosegrp,contrast(nowald cieffects)  //specify cieffects in lieu of pveffects
  marginsplot,yline(0)
```

     Contrasts of adjusted predictions                          Number of obs = 180

     Expression: Linear prediction, predict()

     ----------------------------------------------------------
                       |            Delta-method
                       |   Contrast   std. err.      t    P>|t|
     ------------------+---------------------------------------
               dosegrp |
        (0mg vs 50mg)  |       1.23       2.70     0.46   0.649
      (50mg vs 100mg)  |      -1.53       2.70    -0.57   0.571
     (100mg vs 150mg)  |       1.73       2.70     0.64   0.522
     (150mg vs 200mg)  |      15.70       2.70     5.81   0.000
     (200mg vs 250mg)  |       6.40       2.70     2.37   0.019
     ----------------------------------------------------------

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.6.4.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Contrasts of each dosage to the previous dosage</strong></figcaption>
</figure>

### 6.1 Reverse adjacent contrasts

The ar. contrast operator, shown below, provides adjacent group contrasts in reverse order.

```
  contrast ar.dosegrp,nowald pveffects
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     ----------------------------------------------------------
                       |   Contrast   Std. err.      t    P>|t|
     ------------------+---------------------------------------
               dosegrp |
        (50mg vs 0mg)  |      -1.23       2.70    -0.46   0.649
      (100mg vs 50mg)  |       1.53       2.70     0.57   0.571
     (150mg vs 100mg)  |      -1.73       2.70    -0.64   0.522
     (200mg vs 150mg)  |     -15.70       2.70    -5.81   0.000
     (250mg vs 200mg)  |      -6.40       2.70    -2.37   0.019
     ----------------------------------------------------------

### 6.2 Selecting a specific contrast

When making adjacent group contrasts, you can select a specific contrast. 

```
  contrast a1.dosegrp,nowald pveffects
```
     Contrasts of marginal linear predictions

     Margins: asbalanced

     -------------------------------------------------------
                    |   Contrast   Std. err.      t    P>|t|
     ---------------+---------------------------------------
            dosegrp |
     (0mg vs 50mg)  |       1.23       2.70     0.46   0.649
     -------------------------------------------------------

We can also select contrasts using the ar. contrast operator. The ar. contrast operator forms contrasts with the previous group, so specifying ar3. (as shown below) contrasts group 3 (100 mg) with the previous group (group 2, 50 mg).

```
  contrast ar3.dosegrp,nowald pveffects
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     ---------------------------------------------------------
                      |   Contrast   Std. err.      t    P>|t|
     -----------------+---------------------------------------
              dosegrp |
     (100mg vs 50mg)  |       1.53       2.70     0.57   0.571
     ---------------------------------------------------------

We can combine selected contrasts as well. Suppose we wanted to test the equality of the mean pain ratings for the first four groups. 


```
 contrast a(1 2 3).dosegrp
```
     Contrasts of marginal linear predictions

     Margins: asbalanced

     -----------------------------------------------------
                       |         df           F        P>F
     ------------------+----------------------------------
               dosegrp |
        (0mg vs 50mg)  |          1        0.21     0.6489
      (50mg vs 100mg)  |          1        0.32     0.5714
     (100mg vs 150mg)  |          1        0.41     0.5223
                Joint  |          3        0.21     0.8918
                       |
           Denominator |        174
     -----------------------------------------------------

     -------------------------------------------------------------------
                       |   Contrast   Std. err.     [95% conf. interval]
     ------------------+------------------------------------------------
               dosegrp |
        (0mg vs 50mg)  |       1.23       2.70         -4.10        6.57
      (50mg vs 100mg)  |      -1.53       2.70         -6.87        3.80
     (100mg vs 150mg)  |       1.73       2.70         -3.60        7.07
     -------------------------------------------------------------------

## 7 Comparing the mean of subsequent or previous levels

This section describes contrasts that compare **each group mean with the mean of the subsequent groups** (also known as Helmert contrasts). 


```
 use pain2
 tab dosage
```
      Medication |
       dosage in |
              mg |      Freq.     Percent        Cum.
     ------------+-----------------------------------
             300 |         30       16.67       16.67
             400 |         30       16.67       33.33
             500 |         30       16.67       50.00
             600 |         30       16.67       66.67
             800 |         30       16.67       83.33
            1000 |         30       16.67      100.00
     ------------+-----------------------------------
           Total |        180      100.00

Let’s begin by testing this overall null hypothesis using the anova command below.

$$ H_{0} : \mu_{300} = \mu_{400} = \mu_{500} = \mu_{600}= \mu_{800}= \mu_{1000}$$  

```
  anova pain i.dosage
```

                              Number of obs =        180    R-squared     =  0.2052
                              Root MSE      =    10.5056    Adj R-squared =  0.1824
     
                       Source | Partial SS         df         MS        F    Prob>F
                   -----------+----------------------------------------------------
                        Model |  4958.8667          5   991.77333      8.99  0.0000
                              |
                       dosage |  4958.8667          5   991.77333      8.99  0.0000
                              |
                     Residual |  19204.133        174   110.36858  
                   -----------+----------------------------------------------------
                        Total |      24163        179   134.98883  

Let’s use the margins and marginsplot commands to show and graph the means by the six levels of dosage.

```
  margins dosage 
  marginsplot
```
     Adjusted predictions                                       Number of obs = 180

     Expression: Linear prediction, predict()

     ------------------------------------------------------------------------------
                  |            Delta-method
                  |     Margin   std. err.      t    P>|t|     [95% conf. interval]
     -------------+----------------------------------------------------------------
           dosage |
             300  |      43.83       1.92    22.85   0.000        40.05       47.62
             400  |      37.60       1.92    19.60   0.000        33.81       41.39
             500  |      31.87       1.92    16.61   0.000        28.08       35.65
             600  |      29.63       1.92    15.45   0.000        25.85       33.42
             800  |      30.63       1.92    15.97   0.000        26.85       34.42
            1000  |      29.43       1.92    15.35   0.000        25.65       33.22
     ------------------------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.6.5.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Mean pain rating by dosage</strong></figcaption>
</figure>



$$H_{0}: \mu_{300} = \mu > _{\text{300}}$$

$$H_{0}: \mu_{400} = \mu > _{\text{400}}$$

$$H_{0}: \mu_{500} = \mu > _{\text{500}}$$

$$H_{0}: \mu_{600} = \mu > _{\text{600}}$$

$$H_{0}: \mu_{800} = \mu > _{\text{1000}}$$

Let’s now test each of the null hypotheses below using the margins command combined with the h. contrast operator.



```
  margins h.dosage,contrast(nowald pveffects)
```

     Contrasts of adjusted predictions                          Number of obs = 180

     Expression: Linear prediction, predict()

     --------------------------------------------------------
                     |            Delta-method
                     |   Contrast   std. err.      t    P>|t|
     ----------------+---------------------------------------
              dosage |
     (300 vs > 300)  |      12.00       2.10     5.71   0.000
     (400 vs > 400)  |       7.21       2.14     3.36   0.001
     (500 vs > 500)  |       1.97       2.21     0.89   0.376
     (600 vs > 600)  |      -0.40       2.35    -0.17   0.865
     (800 vs  1000)  |       1.20       2.71     0.44   0.659
     --------------------------------------------------------

```
  marginsplot,yline(0) xlabel(, angle(45))
```



<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.6.6.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Mean pain rating by dosage</strong></figcaption>
</figure>

When the confidence interval for the contrast excludes zero,the difference is significant at the 5% level.

### 7.1 Comparing the mean of previous levels

```
  contrast j.dosage,nowald pveffects
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     ---------------------------------------------------------
                      |   Contrast   Std. err.      t    P>|t|
     -----------------+---------------------------------------
               dosage |
      (400 vs   300)  |      -6.23       2.71    -2.30   0.023
      (500 vs < 500)  |      -8.85       2.35    -3.77   0.000
      (600 vs < 600)  |      -8.13       2.21    -3.67   0.000
      (800 vs < 800)  |      -5.10       2.14    -2.38   0.018
     (1000 vs <1000)  |      -5.28       2.10    -2.51   0.013
     ---------------------------------------------------------

### 7.2 Selecting a specific contrast

```
  contrast h400.dosage,nowald pveffects
```
we wanted to focus only on the contrast of those whose value of dosage was 400 to those who have higher values of dosage.

     Contrasts of marginal linear predictions

     Margins: asbalanced

     -------------------------------------------------------
                    |   Contrast   Std. err.      t    P>|t|
     ---------------+---------------------------------------
             dosage |
     (400 vs >400)  |       7.21       2.14     3.36   0.001
     -------------------------------------------------------

Alternatively, we might want to focus only on the contrasts of 400 mg versus the subsequent groups and 500 mg versus the subsequent groups. 

```
  contrast h(400 500).dosage,nowald pveffects
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     -------------------------------------------------------
                    |   Contrast   Std. err.      t    P>|t|
     ---------------+---------------------------------------
             dosage |
     (400 vs >400)  |       7.21       2.14     3.36   0.001
     (500 vs >500)  |       1.97       2.21     0.89   0.376
     -------------------------------------------------------

select the contrast of 500mg versus the mean of the previous groups

```
  contrast j500.dosage,nowald pveffects
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     -------------------------------------------------------
                    |   Contrast   Std. err.      t    P>|t|
     ---------------+---------------------------------------
             dosage |
     (500 vs <500)  |      -8.85       2.35    -3.77   0.000
     -------------------------------------------------------


## Polynomial contrasts

Let’s consider the use of polynomial contrasts for assessing nonlinear trends (for example, quadratic, cubic, or quartic). 


We can specify q.dosegrp on the contrast command to compute tests of polynomial trend with respect to dosegrp. (The noeffects option is used to save space and focus on the results of the Wald tests.)

```
  use pain
  contrast q.dosegrp,noeffects
```
compute tests of polynomial trend with respect to dosegrp.


     Margins: asbalanced

     ------------------------------------------------
                  |         df           F        P>F
     -------------+----------------------------------
          dosegrp |
        (linear)  |          1      109.12     0.0000
     (quadratic)  |          1       29.25     0.0000
         (cubic)  |          1        0.00     0.9824
       (quartic)  |          1        8.39     0.0043
       (quintic)  |          1        1.62     0.2048
           Joint  |          5       29.67     0.0000
                  |
      Denominator |        174
     ------------------------------------------------

**Suppose you wanted to fit the relationship between a predictor and outcome using a linear term and wanted to assess whether there are significant nonlinear trends in the relationship between the predictor and outcome.** You can use the contrast command to test only the nonlinear terms, as shown below. 

```
  contrast q(2/6).dosegrp,noeffects
```
     Contrasts of marginal linear predictions

     Margins: asbalanced

     ------------------------------------------------
                  |         df           F        P>F
     -------------+----------------------------------
          dosegrp |
     (quadratic)  |          1       29.25     0.0000
         (cubic)  |          1        0.00     0.9824
       (quartic)  |          1        8.39     0.0043
       (quintic)  |          1        1.62     0.2048
           Joint  |          4        9.81     0.0000
                  |
      Denominator |        174
     ------------------------------------------------

The joint test of all the nonlinear terms (powers 2 through 6) is significant.In such a case, **it would be inadvisable to fit the relationship between the predictor and outcome using only a linear fit.**

The q.contrast operator assumes that the levels of dosegrp are **equidistant** from each other. In next example the level of dosage are not equidistant and we would have obtained different results by specifying q.dosage compared with specifying p.dosage.


```
  contrast p.dosage,noeffects
```

the p.contrast operator to dosage.this tests the polynomial trends based on the actual dosage,accounting for the differing gaps among the levels of dosage.

     Contrasts of marginal linear predictions

     Margins: asbalanced

     ------------------------------------------------
                  |         df           F        P>F
     -------------+----------------------------------
           dosage |
        (linear)  |          1       28.37     0.0000
     (quadratic)  |          1       13.44     0.0003
         (cubic)  |          1        2.60     0.1084
       (quartic)  |          1        0.47     0.4956
       (quintic)  |          1        0.05     0.8202
           Joint  |          5        8.99     0.0000
                  |
      Denominator |        174
     ------------------------------------------------

specific on cubic,quartic,and quintic

```
  contrast p(3/6).dosage,noeffects 
```

## 9 Custom contrasts

For those times when you want to make another kind of contrast, you can specify a custom contrast.

Let’s begin by illustrating how to perform custom contrasts using simple examples that compare one group with another group. 

For the first example, let’s compare the mean of group 1 (married) with group 5 (not married). The custom contrast is **enclosed within curly braces by specifying the variable name followed by the contrast coefficients.** The contrast coefficients map to the levels (groups) of the variable. In this example, the contrast coefficient of 1 is applied to group 1, and is applied to group 5. (A contrast coefficient of 0 is applied to groups 2, 3, and 4.) The result is a contrast of group 1 minus group 5. 

```
  contrast {marital 1 0 0 0 -1} 
```
the contrast coef of 1 is applied to group 1,and -1 is applied to group 5(a contrast coefficient of 0 is applied to group 2 3 and 4)

     Contrasts of marginal linear predictions

     Margins: asbalanced

     ------------------------------------------------
                  |         df           F        P>F
     -------------+----------------------------------
          marital |          1        4.18     0.0411
                  |
      Denominator |       1147
     ------------------------------------------------

     --------------------------------------------------------------
                  |   Contrast   Std. err.     [95% conf. interval]
     -------------+------------------------------------------------
          marital |
             (1)  |       0.16       0.08          0.01        0.31
     --------------------------------------------------------------

Let’s switch the above contrast. Let’s compare group 5 (not married) with group 1 (married), as shown below. 

```
  contrast {marital -1 0 0 0 1},nowald pveffects
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     -----------------------------------------------------
                  |   Contrast   Std. err.      t    P>|t|
     -------------+---------------------------------------
          marital |
             (1)  |      -0.16       0.08    -2.04   0.041
     -----------------------------------------------------

Say that we want to compare those who are married (group 1) with the average of those who are separated and divorced (groups 3 and 4). We can form that contrast as shown below. 

```
  contrast{marital 1 0 -.5 -.5 0},nowald pveffects
```
     Contrasts of marginal linear predictions

     Margins: asbalanced

     -----------------------------------------------------
                  |   Contrast   Std. err.      t    P>|t|
     -------------+---------------------------------------
          marital |
             (1)  |       0.52       0.10     5.34   0.000
     -----------------------------------------------------

Suppose we want to compare those who are married (group 1) with the average of those who are widowed, divorced, and separated (groups 2, 3, and 4). 


```
  contrast{marital 1 -.33333333 -.33333333 -.33333333 0},nowald pveffects
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     -----------------------------------------------------
                  |   Contrast   Std. err.      t    P>|t|
     -------------+---------------------------------------
          marital |
             (1)  |       0.50       0.08     6.10   0.000
     -----------------------------------------------------

>Note! Contrasts must sum to zero
>>The contrast coefficients that we specify in a custom contrast must sum to zero. In the previous example, the contrast coefficients for groups 2, 3, and 4 are expressed as -.33333333, using eight digits after the decimal point. Although the sum of the coefficients for that custom contrast is not exactly zero, it is close enough to zero to satisfy the margins command. 

>>You can use inline expansions to directly specify the fraction , as shown in the contrast command below.
>>contrast {marital 1 ‘=-1/3’ ‘=-1/3’ ‘=-1/3’ 0}, nowald pveffects


Let’s form a contrast of the average of those who are married (group 1) and separated (group 4) to the average of those who are widowed (group 2) and divorced (group 3). Note how the coefficients for groups 1 and 4 are specified as 0.5 and the coefficients for groups 2 and 3 are specified as .

```
  contrast{marital .5 -.5 -.5 .5 0},nowald pveffects
```
     Margins: asbalanced

     -----------------------------------------------------
                  |   Contrast   Std. err.      t    P>|t|
     -------------+---------------------------------------
          marital |
             (1)  |       0.20       0.11     1.84   0.065
     -----------------------------------------------------

To formulate the contrast coefficients, it can be helpful to write out the null hypothesis that you want to test. For example, let’s write the null hypothesis corresponding to the contrast above:

$$H_{0}: (\mu_{1} +\mu_{4})/2 = (\mu_{2} +\mu_{3})/2$$

$$H_{0}: (1/2) * \mu_{1} +  (1/2) * \mu_{4} = (1/2) * \mu_{2} +  (1/2) * \mu_{3}$$

$$H_{0}: (1/2) * \mu_{1} +  (1/2) * \mu_{4} + - (1/2) * \mu_{2} + -  (1/2) * \mu_{3} = 0$$

$$H_{0}: (1/2) * \mu_{1} + -   (1/2) * \mu_{2} + - (1/2) * \mu_{3} + -  (1/2) * \mu_{4} = 0$$


## 10 Weighted contrasts

So far, the examples I have shown estimate the mean for groups 2 and 3 combined by obtaining the mean for group 2 and the mean for group 3, and then averaging those means. Stata calls this the **as-balanced approach, because it gives equal weights to the groups even if their sample sizes are different.** 

We could, instead, weight the means for groups 2 and 3 proportionate to their sample size. Stata calls this the **as-observed approach, because the means are weighted in proportion to their observed sample size.**

```
  anova happy7 i.marital3
  margins h.marital3,contrast(nowald pveffects) 
```

                              Number of obs =      1,160    R-squared     =  0.0369
                              Root MSE      =    .962698    Adj R-squared =  0.0352
     
                       Source | Partial SS         df         MS        F    Prob>F
                   -----------+----------------------------------------------------
                        Model |   41.07881          2   20.539405     22.16  0.0000
                              |
                     marital3 |   41.07881          2   20.539405     22.16  0.0000
                              |
                     Residual |  1072.2927      1,157   .92678716  
                   -----------+----------------------------------------------------
                        Total |  1113.3716      1,159   .96063119  


Let’s use the **h. contrast operator to compare each group with the mean of the subsequent groups,** using the as-balanced approach. 

Let’s focus our attention on the first contrast, Married vs >Married, which compares those who are married (group 1) versus those who are previously married and never married (groups 2 and 3).

Compare each group with the mean of the subsequent groups,using the as-balanced approach




     Contrasts of adjusted predictions                        Number of obs = 1,160

     Expression: Linear prediction, predict()

     -------------------------------------------------------------------------
                                      |            Delta-method
                                      |   Contrast   std. err.      t    P>|t|
     ---------------------------------+---------------------------------------
                             marital3 |
               (Married vs >Married)  |       0.34       0.06     6.07   0.000
     (Prevmarried vs  Never married)  |      -0.20       0.08    -2.50   0.012
     -------------------------------------------------------------------------

We can manually compute this estimate:

$5.705036 - (5.263323 + 5.459649)/2 = 0.34355$

Let’s compare this estimate with the **as-observed approach,** which weights the mean of groups 2 and 3 (previously married and never married) by their sample size. **The hw. contrast operator is used to obtain the as-observed estimate.**


```
  margins h.marital3,contrast(nowald pveffects)
```
     Contrasts of adjusted predictions                        Number of obs = 1,160

     Expression: Linear prediction, predict()

     -------------------------------------------------------------------------
                                      |            Delta-method
                                      |   Contrast   std. err.      t    P>|t|
     ---------------------------------+---------------------------------------
                             marital3 |
               (Married vs >Married)  |       0.34       0.06     6.07   0.000
     (Prevmarried vs  Never married)  |      -0.20       0.08    -2.50   0.012
     -------------------------------------------------------------------------

Instead of weighting groups 2 and 3 equally—by (1/2)—groups 2 and 3 are weighted by their individual sample size divided by the combined sample size. The $N$ for group 2 (previously married) is 319, and the $N$ for group 3 (never married) is 285, and the combined for the two groups is 604. 

We can manually compute this estimate:

$5.705036 - (5.263323*(319/604)+ 5.459649*(285/604)) = 0.34907573$


Thus, if you want estimates that are weighted as a function of the proportion of observations in each group, then the as-observed estimates will provide you the kind of estimates that you desire.

## 11 Pairwise comparisons

Sometimes, you want to test all pairwise comparisons that can be formed for a factor variable. The pwcompare command can be used to form such comparisons. 

```
  use gss_ivrm,clear
  anova happy7 i.marital i.gender i.race c.age
```
We can use the pwcompare command to form all pairwise comparisons among the marital status groups. 

Because of the many comparisons, we might want to make adjustments to the values to account for the multiple comparisons. For example, let’s use **Šidák’s method for adjusting for multiple comparisons by adding the mcompare(sidak) option.**

```
  pwcompare marital,pveffects mcompare(sidak)  
```

     Pairwise comparisons of marginal linear predictions

     Margins: asbalanced

     ---------------------------
                  |    Number of
                  |  comparisons
     -------------+-------------
         marital3 |            3
     ---------------------------

     ----------------------------------------------------------------------
                                   |                              Sidak
                                   |   Contrast   Std. err.      t    P>|t|
     ------------------------------+---------------------------------------
                          marital3 |
           Prevmarried vs Married  |      -0.44       0.07    -6.53   0.000
         Never married vs Married  |      -0.25       0.07    -3.50   0.001
     Never married vs Prevmarried  |       0.20       0.08     2.50   0.037
     ----------------------------------------------------------------------

The bonferroni or scheffe method could have alternatively been specified within the mcompare() option. When you have **balanced data**, the tukey, snk, duncan, or dunnett method can be specified within mcompare() (see [R] pwcompare for more details)


## 12 Interpreting confidence intervals

The marginsplot command displays margins and confidence intervals that were computed from the most recent margins command. Sometimes, these confidence intervals **might tempt you into falsely believing that they tell us about differences among groups.**


```
  use gss_ivrm,clear
  anova happy7 i.marital i.gender i.race c.age
```

The margins command is used to estimate the adjusted means of happiness by marital. The output also includes the 95% confidence interval for each adjusted mean.

```
  margins marital
  marginsplot
```
     Predictive margins                                       Number of obs = 1,156

     Expression: Linear prediction, predict()

     --------------------------------------------------------------------------------
                    |            Delta-method
                    |     Margin   std. err.      t    P>|t|     [95% conf. interval]
     ---------------+----------------------------------------------------------------
            marital |
           married  |       5.70       0.04   139.63   0.000         5.62        5.78
           widowed  |       5.25       0.12    44.51   0.000         5.01        5.48
          divorced  |       5.20       0.07    75.92   0.000         5.06        5.33
         separated  |       5.15       0.16    31.25   0.000         4.83        5.47
     never married  |       5.54       0.06    87.86   0.000         5.42        5.67
     --------------------------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.7.1.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Adjusted means of happiness by marital status</strong></figcaption>
</figure>

Our eye might be tempted to use the overlap (or lack of overlap) of confidence intervals between groups to draw conclusions about the significance of the differences between groups. **However, such conclusions would not be appropriate.** For example, although the confidence intervals for those who are separated and never married overlap, the output from the contrast command below shows that the difference in these means is statistically significant ($P = 0.026$)

```
  contrast ar.marital,nowald pveffects
```
     Contrasts of marginal linear predictions

     Margins: asbalanced

     ----------------------------------------------------------------------
                                   |   Contrast   Std. err.      t    P>|t|
     ------------------------------+---------------------------------------
                           marital |
             (widowed vs married)  |      -0.45       0.12    -3.67   0.000
            (divorced vs widowed)  |      -0.05       0.13    -0.34   0.732
          (separated vs divorced)  |      -0.05       0.18    -0.28   0.777
     (never married vs separated)  |       0.39       0.18     2.24   0.026
     ----------------------------------------------------------------------

## 13 Testing categorical variables using regression

The analyses in this chapter have been conducted using the anova command because it produces concise printed output. However, this is not to imply that we cannot perform such tests using the regress command. 


```
  use gss_ivrm,clear
  anova happy7 i.marital i.gender i.race c.age
  reg happy7 i.marital i.gender i.race c.age
```

           Source |       SS           df       MS      Number of obs   =     1,156
     -------------+----------------------------------   F(8, 1147)      =      7.35
            Model |  53.9791041         8  6.74738801   Prob > F        =    0.0000
         Residual |  1052.29339     1,147  .917431026   R-squared       =    0.0488
     -------------+----------------------------------   Adj R-squared   =    0.0422
            Total |  1106.27249     1,155  .957811681   Root MSE        =    .95783

     --------------------------------------------------------------------------------
             happy7 | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
     ---------------+----------------------------------------------------------------
            marital |
           widowed  |      -0.45       0.12    -3.67   0.000        -0.70       -0.21
          divorced  |      -0.50       0.08    -6.28   0.000        -0.66       -0.34
         separated  |      -0.55       0.17    -3.23   0.001        -0.88       -0.22
     never married  |      -0.16       0.08    -2.04   0.041        -0.31       -0.01
                    |
             gender |
            Female  |       0.12       0.06     2.06   0.040         0.01        0.23
                    |
               race |
             black  |      -0.04       0.09    -0.42   0.676        -0.20        0.13
             other  |      -0.11       0.12    -0.95   0.341        -0.34        0.12
                    |
                age |       0.01       0.00     2.41   0.016         0.00        0.01
              _cons |       5.42       0.11    47.90   0.000         5.19        5.64
     --------------------------------------------------------------------------------


The output of the regress command is lengthier because it uses dummy coding for each of the categorical variables and shows the effect for each of the dummy variables. **To obtain the test of the overall effect of marital and the test of the overall effect of race, we can use the contrast command, as shown below.**


```
 contrast marital race
```

Contrasts of marginal linear predictions

Margins: asbalanced

     ------------------------------------------------
                  |         df           F        P>F
     -------------+----------------------------------
          marital |          4       12.85     0.0000
                  |
             race |          2        0.50     0.6043
                  |
      Denominator |       1147
     ------------------------------------------------


**The contrast, margins, marginsplot, and pwcompare commands work the same way after the regress command as they do after the anova command.**For example, wecan use the margins command to obtain the adjusted means broken down by marital.

```
  contrast r.marital,nowald pveffects 
```

     Predictive margins                                       Number of obs = 1,156
     Model VCE: OLS

     Expression: Linear prediction, predict()

     --------------------------------------------------------------------------------
                    |            Delta-method
                    |     Margin   std. err.      t    P>|t|     [95% conf. interval]
     ---------------+----------------------------------------------------------------
            marital |
           married  |       5.70       0.04   139.63   0.000         5.62        5.78
           widowed  |       5.25       0.12    44.51   0.000         5.01        5.48
          divorced  |       5.20       0.07    75.92   0.000         5.06        5.33
         separated  |       5.15       0.16    31.25   0.000         4.83        5.47
     never married  |       5.54       0.06    87.86   0.000         5.42        5.67
     --------------------------------------------------------------------------------


