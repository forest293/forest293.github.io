# Chapter8 ：Categorical by categorical interactions


This chapter illustrates models that involve the interaction of two categorical variables.The emphasis of this chapter is **not only how to test for interactions between factor variables but also how to understand and dissect those interactions.**

This chapter focuses on three types of interactions: 
1. **two by two interactions.** 
2. **two by three interactions.**
3. **three by three interactions.**

## 1 Two by two models: Example 1

In this first hypothetical study, she seeks to determine the effectiveness of happiness therapy by comparing the optimism of people who have completed happiness therapy treatment with the optimism of people in a control group who received no treatment. 

The researcher is interested in **not only assessing the effectiveness of happiness therapy but also assessing whether its effectiveness depends on whether the person has been diagnosed as clinically depressed.** This yields a two by two research design, crossing treatment group assignment (control group versus happiness therapy) with depression status (nondepressed versus depressed).

* The variable treat indicates the treatment assignment, coded: 1 = control group (Con) and 2 = happiness therapy (HT). 
* The variable depstat reflects the person’s depression status at the beginning of the study and is coded: 1 = nondepressed and 2 = depressed. 
* The variable opt is the optimism score at the end of the study. In this dataset, opt has a mean of 44.5, a minimum of 16, and a maximum of 80.

Let’s now run an analysis that predicts opt based on treat, depstat, and the interaction of these two variables. 

This analysis uses the anova command (instead of the regress command) because **the anova command directly shows the significance tests for each of the main effects as well as the interaction.**

```
  use opt-2by2.dta
  anova opt depstat##treat
```

                              Number of obs =        120    R-squared     =  0.4889
                              Root MSE      =    10.0136    Adj R-squared =  0.4757

                       Source | Partial SS         df         MS        F    Prob>F
                --------------+----------------------------------------------------
                        Model |      11126          3   3708.6667     36.99  0.0000
                              |
                      depstat |  7426.1333          1   7426.1333     74.06  0.0000
                        treat |  2803.3333          1   2803.3333     27.96  0.0000
                depstat#treat |  896.53333          1   896.53333      8.94  0.0034
                              |
                     Residual |  11631.467        116   100.27126  
                --------------+----------------------------------------------------
                        Total |  22757.467        119   191.23922  



>Note! The anova and regress commands
>>if you want use regress command in stead of the anova command to fit the previous model, there are two caveats:

>>1. the regress command will require an extra step using the contrast command to test the overall interaction (for example, contrast depstat#treat). 
>>2. the tests of the main effects differ when using the regress command compared with the anova command.

The significant interaction indicates that the effect of happiness therapy for those who are nondepressed is significantly different from the effect of happiness therapy for those who are depressed.



```
  margins treat#depstat,nopvalues
  marginsplot
```

     Adjusted predictions                                       Number of obs = 120

     Expression: Linear prediction, predict()

     ---------------------------------------------------------------
                   |            Delta-method
                   |     Margin   std. err.     [95% conf. interval]
     --------------+------------------------------------------------
     treat#depstat |
          Con#Non  |      44.87       1.83         41.25       48.49
          Con#Dep  |      34.60       1.83         30.98       38.22
           HT#Non  |      60.00       1.83         56.38       63.62
           HT#Dep  |      38.80       1.83         35.18       42.42
     ---------------------------------------------------------------

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.7.2.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Graph of means</strong></figcaption>
</figure>

### 1.1 Simple effects
The significant interaction indicates that the effect of happiness therapy is different for those who are depressed versus nondepressed. Each of these effects is called a simple effect, because they reflect the effect of one variable while holding another variable constant. 

We can estimate and test these simple effects using the contrast command, as shown below. **Note the use of the @ symbol.** This requests the simple effect of treat at each level of depstat.

```
  contrast treat@depstat
  contrast treat@depstat,nowald pveffects
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     ----------------------------------------------------------
                       |   Contrast   Std. err.      t    P>|t|
     ------------------+---------------------------------------
         treat@depstat |
     (HT vs base) Non  |      15.13       2.59     5.85   0.000
     (HT vs base) Dep  |       4.20       2.59     1.62   0.107
     ----------------------------------------------------------

By adding the nowald and pveffects options, the contrast command displays a table with the estimate of the simple effect, the standard error, and a significance test of the simple effect. 

This shows the effect of treatment among those who are nondepressed equals 15.1, and this effect is significant ( $t = 5.85,p=0.000$ ). Among those who are depressed, the treatment effect is 4.2, and this difference is not significant ( $t = 1.62,p=0.107$ ).

>Note! Contrast options
>>**Combining the nowald and pveffects options provides a concise output that includes an estimate of the size of the contrast and a test of its significance.**

### 1.2 Estimating the size of the interaction
As we saw in the analysis of the simple effects, the simple effect of treatment is 4.2 for those who are depressed and is 15.1 for those who are nondepressed. Taking the difference in these simple effects ($4.2 - 15.1$) gives us an estimate of the size of the interaction, which is $-10.9$.
This is the same value that we obtain if we estimate the interaction using the contrast command below. 

     Contrasts of marginal linear predictions

     Margins: asbalanced

     --------------------------------------------------------------------
                                 |   Contrast   Std. err.      t    P>|t|
     ----------------------------+---------------------------------------
                   treat#depstat |
     (HT vs base) (Dep vs base)  |     -10.93       3.66    -2.99   0.003
     --------------------------------------------------------------------

the $p$-value for this test matches the $p$-value of the treat#depstat interaction from the original anova command.

### 1.3 More about interaction

let’s further explore what we mean by an interaction by considering the hypothetical pattern of results shown in below.



<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.7.3.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Two by two with no interaction</strong></figcaption>
</figure>

This is an example pattern in which there is no interaction between treatment and depression status. One way we can see the absence of an interaction is by seeing that the line for D1 is parallel to the line for D2. 

## 2 Two by three models
his section considers models where one of the categorical variables has two levels and the other categorical variable has three levels. 

### 2.1 Example 2
Referring to the example from the previous section, depression status had two levels, nondepressed and depressed. 

Suppose that we instead use three categories for depression status: nondepressed, mildly depressed, and severely depressed. 

Let’s now perform an analysis predicting optimism from treatment group, depression status, and the interaction of these two variables.

``` 
  use opt-2by3-ex1.dta
  anova opt depstat##treat
```

                              Number of obs =        180    R-squared     =  0.5402
                              Root MSE      =    10.0154    Adj R-squared =  0.5270

                       Source | Partial SS         df         MS        F    Prob>F
                --------------+----------------------------------------------------
                        Model |  20505.828          5   4101.1656     40.89  0.0000
                              |
                      depstat |  15432.844          2   7716.4222     76.93  0.0000
                        treat |  3183.6056          1   3183.6056     31.74  0.0000
                depstat#treat |  1889.3778          2   944.68889      9.42  0.0001
                              |
                     Residual |  17453.567        174   100.30785  
                --------------+----------------------------------------------------
                        Total |  37959.394        179   212.06366  


As expected, the depstat#treat interaction is significant. We can compute the mean optimism as a function of depression status and treatment group by using the margins command below. 

Had there been additional predictors in the model, **the margins command would have produced adjusted means, adjusting for the other predictors in the model.**

``` 
  margins treat#depstat,nopvalues
  marginsplot
```

     Adjusted predictions                                       Number of obs = 180

     Expression: Linear prediction, predict()

     ---------------------------------------------------------------
                   |            Delta-method
                   |     Margin   std. err.     [95% conf. interval]
     --------------+------------------------------------------------
     treat#depstat |
          Con#Non  |      44.23       1.83         40.62       47.84
         Con#Mild  |      39.63       1.83         36.02       43.24
          Con#Sev  |      29.80       1.83         26.19       33.41
           HT#Non  |      59.60       1.83         55.99       63.21
          HT#Mild  |      49.73       1.83         46.12       53.34
           HT#Sev  |      29.57       1.83         25.96       33.18
     ---------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.7.4.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Graph of means</strong></figcaption>
</figure>

#### 2.1.1 Simple effects

We can ask whether the effect of happiness therapy is significant at each level of depression status.  

``` 
  contrast treat@depstat,nowald pveffects
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     -----------------------------------------------------------
                        |   Contrast   Std. err.      t    P>|t|
     -------------------+---------------------------------------
          treat@depstat |
      (HT vs base) Non  |      15.37       2.59     5.94   0.000
     (HT vs base) Mild  |      10.10       2.59     3.91   0.000
      (HT vs base) Sev  |      -0.23       2.59    -0.09   0.928
     -----------------------------------------------------------


This test of simple effects tells us that happiness therapy is significantly better than the control group for those who are nondepressed and for those who are mildly depressed. For those who are severely depressed, happiness therapy is not significantly different from being in the control group

#### 2.1.2 Partial interactions

Another way to dissect a two by three interaction is through the use of partial interactions. In this example, a partial interaction is constructed by applying a contrast operator to depstat and interacting that with treat. 

For example, applying the **a.contrast** operator to depstat yields two contrasts: group 1 versus 2 (Non vs Mild depression); and group 2 versus 3 (Mild vs Sev depression). Interacting **a.depstat** with treat forms two partial interactions.

``` 
  contrast a.depstat#treat
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     ----------------------------------------------------------
                            |         df           F        P>F
     -----------------------+----------------------------------
              depstat#treat |
     (Non vs Mild) (joint)  |          1        2.07     0.1516
     (Mild vs Sev) (joint)  |          1        7.98     0.0053
                     Joint  |          2        9.42     0.0001
                            |
                Denominator |        174
     ----------------------------------------------------------

The first partial interaction is not significant ( $F = 2.07 , p = 0.1516$ ).The effect of happiness therapy (compared with the control group) is not significantly different for those who are nondepressed versus mildly depressed. 



The second partial interaction is significant ( $F = 7.98 , p = 0.0053$ ). The effect of happiness therapy (compared with the control group) is significantly different when comparing those who are mildly depressed versus severely depressed. 

We are not limited to using the **a. contrast** operator when forming such partial interactions. The **j. contrast** operator compares each group with the mean of the previous groups. 

``` 
  contrast j.depstat#treat
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     -----------------------------------------------------------
                             |         df           F        P>F
     ------------------------+----------------------------------
               depstat#treat |
     (Mild vs  Non) (joint)  |          1        2.07     0.1516
      (Sev vs <Sev) (joint)  |          1       16.76     0.0001
                      Joint  |          2        9.42     0.0001
                             |
                 Denominator |        174
     -----------------------------------------------------------


The first contrast shown by the contrast command is the same as the first test we saw using the a. contrast operator. However, the second contrast shows us the interaction of the comparison of those who are severely depressed versus those mildly depressed and nondepressed interacted with treatment. This partial interaction is significant. 

## 3 Three by three models: Example 4

Let’s now consider an example that illustrates a three by three design. 

there are three levels of treatment (control group, traditional therapy, and happiness therapy), and three depression groups (nondepressed, mildly depressed, and severely depressed)

``` 
  use opt-3by3.dta.dta
  anova opt depstat##treat  
```

                              Number of obs =        270    R-squared     =  0.4898
                              Root MSE      =     10.023    Adj R-squared =  0.4742

                       Source | Partial SS         df         MS        F    Prob>F
                --------------+----------------------------------------------------
                        Model |  25175.519          8   3146.9398     31.32  0.0000
                              |
                      depstat |  17664.096          2   8832.0481     87.92  0.0000
                        treat |   5250.363          2   2625.1815     26.13  0.0000
                depstat#treat |  2261.0593          4   565.26481      5.63  0.0002
                              |
                     Residual |  26220.367        261   100.46117  
                --------------+----------------------------------------------------
                        Total |  51395.885        269   191.06277  


``` 
  margins treat#depstat,nopvalues  
  marginsplot 
``` 

     Adjusted predictions                                       Number of obs = 270

     Expression: Linear prediction, predict()

     ---------------------------------------------------------------
                   |            Delta-method
                   |     Margin   std. err.     [95% conf. interval]
     --------------+------------------------------------------------
     treat#depstat |
          Con#Non  |      44.20       1.83         40.60       47.80
         Con#Mild  |      39.70       1.83         36.10       43.30
          Con#Sev  |      29.90       1.83         26.30       33.50
           TT#Non  |      54.53       1.83         50.93       58.14
          TT#Mild  |      49.53       1.83         45.93       53.14
           TT#Sev  |      39.80       1.83         36.20       43.40
           HT#Non  |      59.33       1.83         55.73       62.94
          HT#Mild  |      49.87       1.83         46.26       53.47
           HT#Sev  |      30.10       1.83         26.50       33.70
     ---------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.7.6.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Mean optimism by treatment group and depression status</strong></figcaption>
</figure>

We can statistically dissect this interaction in four different ways: using simple effects , simple contrasts , partial interactions , or interaction contrasts . Each of these techniques is illustrated below

### 3.1  Simple effects

One way to dissect the interaction is by looking at the effect of treatment at each level of depression status.


``` 
  contrast treat@depstat
``` 
     Contrasts of marginal linear predictions

     Margins: asbalanced

     -------------------------------------------------
                   |         df           F        P>F
     --------------+----------------------------------
     treat@depstat |
              Non  |          2       17.86     0.0000
             Mild  |          2        9.96     0.0001
              Sev  |          2        9.56     0.0001
            Joint  |          6       12.46     0.0000
                   |
       Denominator |        261
     -------------------------------------------------

These results show that the effect of treat is significant at each level of depstat.

### 3.2  Simple contrast

``` 
  contrast r.treat@1.depstat,nowald pveffects  
``` 
This yields a comparison of each treatment group with the reference group (that is, group 1, the control group) at each level of depression status. 

     Contrasts of marginal linear predictions

     Margins: asbalanced

     ---------------------------------------------------------
                      |   Contrast   Std. err.      t    P>|t|
     -----------------+---------------------------------------
        treat@depstat |
     (TT vs Con) Non  |      10.33       2.59     3.99   0.000
     (HT vs Con) Non  |      15.13       2.59     5.85   0.000
     ---------------------------------------------------------


Let’s now perform these simple contrasts for those who are mildly depressed.

``` 
  contrast r.treat@2.depstat,nowald pveffects  
``` 
     Contrasts of marginal linear predictions

     Margins: asbalanced

     ----------------------------------------------------------
                       |   Contrast   Std. err.      t    P>|t|
     ------------------+---------------------------------------
         treat@depstat |
     (TT vs Con) Mild  |       9.83       2.59     3.80   0.000
     (HT vs Con) Mild  |      10.17       2.59     3.93   0.000
     ----------------------------------------------------------

Finally, let’s perform these simple contrasts for those who are severely depressed.

``` 
  contrast r.treat@3.depstat,nowald pveffects
``` 
     Contrasts of marginal linear predictions

     Margins: asbalanced

     ---------------------------------------------------------
                      |   Contrast   Std. err.      t    P>|t|
     -----------------+---------------------------------------
        treat@depstat |
     (TT vs Con) Sev  |       9.90       2.59     3.83   0.000
     (HT vs Con) Sev  |       0.20       2.59     0.08   0.938
     ---------------------------------------------------------

If you prefer, you can obtain all six of these simple contrasts at once using the contrast command below

``` 
  contrast r.treat@depstat,nowald pveffects
``` 

     Contrasts of marginal linear predictions

     Margins: asbalanced

     ----------------------------------------------------------
                       |   Contrast   Std. err.      t    P>|t|
     ------------------+---------------------------------------
         treat@depstat |
      (TT vs Con) Non  |      10.33       2.59     3.99   0.000
     (TT vs Con) Mild  |       9.83       2.59     3.80   0.000
      (TT vs Con) Sev  |       9.90       2.59     3.83   0.000
      (HT vs Con) Non  |      15.13       2.59     5.85   0.000
     (HT vs Con) Mild  |      10.17       2.59     3.93   0.000
      (HT vs Con) Sev  |       0.20       2.59     0.08   0.938
     ----------------------------------------------------------

### 3.3 Partial interaction

I used the means from the margins command to create a visual depiction of these two partial interactions, shown in figure below. 

The left panel of figure illustrates the comparison of treatment group 2 versus 1 (traditional therapy versus control) interacted with depression status. 

The right panel illustrates the comparison of treatment group 3 versus 1 (happiness therapy versus control) interacted with depression status


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.7.7.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Partial interactions</strong></figcaption>
</figure>

``` 
  contrast r.treat#depstat
``` 
     Contrasts of marginal linear predictions

     Margins: asbalanced

     --------------------------------------------------------
                          |         df           F        P>F
     ---------------------+----------------------------------
            treat#depstat |
     (TT vs Con) (joint)  |          2        0.01     0.9891
     (HT vs Con) (joint)  |          2        8.64     0.0002
                   Joint  |          4        5.63     0.0002
                          |
              Denominator |        261
     --------------------------------------------------------

The first partial interaction is not significant.The difference in optimism between traditional therapy and the control group does not differ among the levels of depression status. This result represent the left panel:The effect of traditional therapy (versus the control group) is similar for all three lines (representing the three levels of depression).

The first partial interaction is not significant.The difference in optimism between happiness therapy and the control group depends on the level of depression. This result represent the right panel:the effect of happiness therapy (compared with the control group) may be similar for those who are nondepressed and mildly depressed but different for those who are severely depressed. 

### 3.4 Interaction contrasts

Suppose we applied the r. contrast operator to treatment group and the a. contrast operator to depression status. 
 
This would create contrasts of each treatment group against the control group (that is, group 2 versus 1 and group 3 versus 1) interacted with contrasts of adjacent levels of depression groups (that is, group 1 versus 2 and group 2 versus 3). 

``` 
  contrast a.depstat#r.treat,nowald pveffects 
``` 
     Contrasts of marginal linear predictions

     Margins: asbalanced

     -------------------------------------------------------------------
                                |   Contrast   Std. err.      t    P>|t|
     ---------------------------+---------------------------------------
                  depstat#treat |
     (Non vs Mild) (TT vs Con)  |       0.50       3.66     0.14   0.891
     (Non vs Mild) (HT vs Con)  |       4.97       3.66     1.36   0.176
     (Mild vs Sev) (TT vs Con)  |      -0.07       3.66    -0.02   0.985
     (Mild vs Sev) (HT vs Con)  |       9.97       3.66     2.72   0.007
     -------------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.7.8.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Interaction contrasts</strong></figcaption>
</figure>

Let’s begin by interpreting the fourth interaction contrast, the only one that was significant. 
The significance of this interaction contrast indicates that the effect of happiness therapy (compared with the control group) is different for those who are mildly depressed compared with those who are severely depressed.

## 4 Unbalanced designs

Even in the context of a randomized experiment, **it is unusual to have the same number of observations in each cell.** 

This section presents an example of an unbalanced design. This will allow us to consider two different strategies that can be used for estimating adjusted means, **the asobserved strategy** and **the as-balanced strategy.** As we will see, the asbalanced option can be used with the margins command to estimate margins as though the design were balanced, even if the actual design is not balanced.

```
  use gss_ivrm.dta
  tab married cograd if !missing(happy7),row
```

     +----------------+
     | Key            |
     |----------------|
     |   frequency    |
     | row percentage |
     +----------------+

       marital: |
     married=1, |
     unmarried= |   College graduate
              0 |     (1=yes, 0=no)
      (recoded) | Not CO Gr    CO Grad |     Total
     -----------+----------------------+----------
      Unmarried |       454        147 |       601 
                |     75.54      24.46 |    100.00 
     -----------+----------------------+----------
        Married |       403        153 |       556 
                |     72.48      27.52 |    100.00 
     -----------+----------------------+----------
          Total |       857        300 |     1,157 
                |     74.07      25.93 |    100.00 

Before performing the analysis, let’s compute the mean of happy7 by married and cograd using the tabulate command below

```
  tab married cograd,sum(happy7)
```
       marital: |
     married=1, |
     unmarried= |  College graduate
              0 |    (1=yes, 0=no)
      (recoded) | Not CO Gr    CO Grad |     Total
     -----------+----------------------+----------
      Unmarried | 5.3039648  5.5170068 | 5.3560732
                | 1.0590229  .99556373 | 1.0470596
                |       454        147 |       601
     -----------+----------------------+----------
        Married | 5.7121588  5.6862745 |  5.705036
                |  .8986037  .79032462 | .86953016
                |       403        153 |       556
     -----------+----------------------+----------
          Total |  5.495916  5.6033333 | 5.5237684
                | 1.0071217  .89926887 |  .9810472
                |       857        300 |      1157




Let’s now perform an analysis that predicts happy7 from married, cograd, and the interaction of these two variables. 

```
  anova happy7 married##cograd
```


                              Number of obs =      1,157    R-squared     =  0.0362
                              Root MSE      =    .964375    Adj R-squared =  0.0337

                       Source | Partial SS         df         MS        F    Prob>F
               ---------------+----------------------------------------------------
                        Model |  40.284425          3   13.428142     14.44  0.0000
                              |
                      married |  18.502336          1   18.502336     19.89  0.0000
                       cograd |    1.94355          1     1.94355      2.09  0.1486
               married#cograd |  3.1674385          1   3.1674385      3.41  0.0652
                              |
                     Residual |  1072.3119      1,153   .93001903  
               ---------------+----------------------------------------------------
                        Total |  1112.5964      1,156   .96245361  

We can see that the married#cograd interaction is not significant ($p = 0.0652$). Let’s assume that we want to retain this interaction. (We may want to retain it based on theoretical considerations or because its -value is close to 0.05.) Let’s now turn our attention to married, which is significant ($p<0.001$). 

To understand this significant result, let’s use the margins command to compute the adjusted means by the levels of married.


```
  margins married,nopvalues
```
     Predictive margins                                       Number of obs = 1,157

     Expression: Linear prediction, predict()

     --------------------------------------------------------------
                  |            Delta-method
                  |     Margin   std. err.     [95% conf. interval]
     -------------+------------------------------------------------
          married |
       Unmarried  |       5.36       0.04          5.28        5.44
         Married  |       5.71       0.04          5.63        5.79
     --------------------------------------------------------------

We can think of this adjusted mean as being computed by taking each cell mean of happy7 among those who are not married and weighing it by the corresponding proportion of those are college graduates or noncollege graduates, as illustrated below.

$ 454 num ÷ 601 = 0.74$

$ 147 num ÷ 601 = 0.26$

$ 5.3039648 * 0.7407 + 5.5170068 * 0.2593 = 5.3592066 $

We can likewise compute the adjusted mean for those who are married using the same strategy, as shown below.
  
$ 5.7121588 * 0.7407 + 5.6862745 * 0.2593 = 5.05447$

**The key point is that the adjusted means are computed by creating a weighted average of cell means that is weighted by the observed proportions of observations in the data (in this case, the observed proportions of cograd). Stata calls this the asobserved strategy.** 

This is the default strategy, unless we specify otherwise. We can explicitly request this strategy by **adding the as observed option to the margins command**, as shown below. This yields the same adjusted means we saw in the previous margins command.
  

```
  margins married,nopvalues asobserved
```

     Predictive margins                                       Number of obs = 1,157

     Expression: Linear prediction, predict()

     --------------------------------------------------------------
                  |            Delta-method
                  |     Margin   std. err.     [95% conf. interval]
     -------------+------------------------------------------------
          married |
       Unmarried  |       5.36       0.04          5.28        5.44
         Married  |       5.71       0.04          5.63        5.79
     --------------------------------------------------------------

In some cases, we might want the adjusted means to be computed using an equal weighting of the cell means, as though the design had been balanced. 

In the context of this example, it would mean weighing college graduates and noncollege graduates equally.8 This can be accomplished by using the asbalanced option. 

```
  margins married,nopvalues asbalanced
```

     Adjusted predictions                                     Number of obs = 1,157

     Expression: Linear prediction, predict()
     At: married   (asbalanced)
         cograd    (asbalanced)

     --------------------------------------------------------------
                  |            Delta-method
                  |     Margin   std. err.     [95% conf. interval]
     -------------+------------------------------------------------
          married |
       Unmarried  |       5.41       0.05          5.32        5.50
         Married  |       5.70       0.05          5.61        5.79
     --------------------------------------------------------------

$  dis 5.3039648 * 0.5 + 5.5170068 * 0.5 = 5.4104858$

$  dis 5.7121588 * 0.5 + 5.6862745 * 0.5 = 5.6992167$

## 5 Main effects with interactions: anova versus regress

This section considers the meaning of main effects in the presence of an interaction when using the regress command compared with the anova command. 

In the presence of interactions, this can lead to conflicting estimates of so-called main effects for the regress command versus the anova command.

Let’s use the dataset for this example and show the mean optimism by treatment group and depression status.


```
  use opt-2by2.dta
  anova opt treat##depstat
```

                              Number of obs =        120    R-squared     =  0.4889
                              Root MSE      =    10.0136    Adj R-squared =  0.4757
     
                       Source | Partial SS         df         MS        F    Prob>F
                --------------+----------------------------------------------------
                        Model |      11126          3   3708.6667     36.99  0.0000
                              |
                        treat |  2803.3333          1   2803.3333     27.96  0.0000
                      depstat |  7426.1333          1   7426.1333     74.06  0.0000
                treat#depstat |  896.53333          1   896.53333      8.94  0.0034
                              |
                     Residual |  11631.467        116   100.27126  
                --------------+----------------------------------------------------
                        Total |  22757.467        119   191.23922  


Let’s now perform this analysis but instead use the regress command.

```
  reg opt treat##depstat,vsquish
```

            Source |       SS           df       MS      Number of obs   =       120
      -------------+----------------------------------   F(3, 116)       =     36.99
             Model |       11126         3  3708.66667   Prob > F        =    0.0000
          Residual |  11631.4667       116  100.271264   R-squared       =    0.4889
      -------------+----------------------------------   Adj R-squared   =    0.4757
             Total |  22757.4667       119  191.239216   Root MSE        =    10.014

     -------------------------------------------------------------------------------
               opt | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
     --------------+----------------------------------------------------------------
             treat |
               HT  |      15.13       2.59     5.85   0.000        10.01       20.25
           depstat |
              Dep  |     -10.27       2.59    -3.97   0.000       -15.39       -5.15
     treat#depstat |
           HT#Dep  |     -10.93       3.66    -2.99   0.003       -18.18       -3.69
             _cons |      44.87       1.83    24.54   0.000        41.25       48.49
     -------------------------------------------------------------------------------

Let’s now compare the results of the anova command with the results of the regress command, focusing on the significance tests. 

These comparisons are a bit tricky, **because the anova command reports $F$ statistics, whereas the regress command reports $t$ statistics.** But we can square the value from the regress command to convert it into an equivalent of an statistic.

 If we square $t$ the value of $-2.99$ from the regress command, we obtain the 8.94, the same value as $F$ the statistic from the anova command.

 Using the contrast treat#depstat command following the regress command also yields the same results as the anova command. The value from the contrast command is the same as the $F$ value from the anova command, 8.94.

```
  contrast treat#depstat
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     -------------------------------------------------
                   |         df           F        P>F
     --------------+----------------------------------
     treat#depstat |          1        8.94     0.0034
                   |
       Denominator |        116
     -------------------------------------------------

Let’s now compare the test of treat from the anova command with the regress command. We square of the $t$ value for the treat effect from the regress command(5.85) and obtain 34.22. **This is different from the $F$ value for the treat effect from the anova command, 27.96.**

```
  contrast treat
```

     Contrasts of marginal linear predictions

     Margins: asbalanced

     ------------------------------------------------
                  |         df           F        P>F
     -------------+----------------------------------
            treat |          1       27.96     0.0000
                  |
      Denominator |        116
     ------------------------------------------------


This might seem perplexing, but there is a perfectly logical explanation for this. The reason for these discrepancies is because of differences in the coding used by the anova and regress commands. The regress command uses dummy (0/1) coding, whereas the anova command and the contrast command use effect ($-1/1$) coding.The interpretation of the interactions is the same whether you use effect coding or dummy coding, but the meaning of the main effects differ. 

The interpretation of the interactions is the same whether you use effect coding or dummy coding, but the meaning of the main effects differ. When using dummy coding, the coefficient for treat represents the effect of treat when depstat is held constant at 0 (that is, for those who are nondepressed, the reference group). 

In the dummy-coded model used by the regress command, the tests of treat and depstat might be called main effects, but this is really a misnomer. These are really simple effects. The test of treat from the regress output is the effect of treat when depstat is held constant at the reference group, and the coefficient for depstat is the effect of depstat when treat is held constant at the reference group.

The anova and contrast commands instead use effect coding, and the terms associated with the main effects represent the classic analysis of variance main effects. 

his begs the question of whether we should even be interpreting main effects in the presence of an interaction. Whether the main effects are coded using dummycoding or using effect coding, **main effects are not meaningful when they are part of a significant interaction term.**

## 6 Interpreting confidence intervals

```
  use gss_ivrm.dta
  anova happy7 i.marital3##i.gender c.health
```



                         Number of obs =        783    R-squared     =  0.1246
                         Root MSE      =    .941504    Adj R-squared =  0.1178

                  Source | Partial SS         df         MS        F    Prob>F
         ----------------+----------------------------------------------------
                   Model |  97.864391          6   16.310732     18.40  0.0000
                         |
                marital3 |   26.17214          2    13.08607     14.76  0.0000
                  gender |  3.8126465          1   3.8126465      4.30  0.0384
         marital3#gender |  2.4708203          2   1.2354101      1.39  0.2488
                  health |  51.985684          1   51.985684     58.65  0.0000
                         |
                Residual |  687.86996        776   .88643037  
         ----------------+----------------------------------------------------
                   Total |  785.73436        782   1.0047754  

Let’s compute the adjusted means of happiness by marital3 by gender using the margins command and graph them using the marginsplot command

```
  margins marital3#gender
  marginsplot
```

     Predictive margins                                         Number of obs = 783

     Expression: Linear prediction, predict()

     ---------------------------------------------------------------------------------------
                           |            Delta-method
                           |     Margin   std. err.      t    P>|t|     [95% conf. interval]
     ----------------------+----------------------------------------------------------------
           marital3#gender |
             Married#Male  |       5.68       0.09    59.93   0.000         5.50        5.87
           Married#Female  |       5.69       0.06    98.87   0.000         5.57        5.80
         Prevmarried#Male  |       5.13       0.11    44.95   0.000         4.90        5.35
       Prevmarried#Female  |       5.29       0.08    64.16   0.000         5.13        5.46
       Never married#Male  |       5.27       0.09    57.86   0.000         5.09        5.45
     Never married#Female  |       5.55       0.09    60.82   0.000         5.37        5.73
     ---------------------------------------------------------------------------------------



<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.8.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong> Adjusted means of happiness by marital status and gender</strong></figcaption>
</figure>

we would need to test the effect of gender at each level of marital status using the margins command, as shown below.

```
  margins gender@marital3,contrast(nowald pveffects)
```

     

     Contrasts of predictive margins                            Number of obs = 783

     Expression: Linear prediction, predict()

     ------------------------------------------------------------------------
                                     |            Delta-method
                                     |   Contrast   std. err.      t    P>|t|
     --------------------------------+---------------------------------------
                     gender@marital3 |
           (Female vs base) Married  |       0.01       0.11     0.05   0.961
       (Female vs base) Prevmarried  |       0.17       0.14     1.20   0.231
     (Female vs base) Never married  |       0.29       0.13     2.21   0.027
     ------------------------------------------------------------------------


In summary, the marginsplot command provides a graphical display of the results calculated by the margins command. Sometimes, the appearance of the confidence intervals of individual groups might tempt you to inappropriately make statistical inferences about the comparisons between the groups. To avoid this trap, **you can directly form comparisons among the groups of interest to ascertain the significance of the group differences.**
     








     
