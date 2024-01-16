# Chapter10 ：Linear by categorical interactions


This chapter illustrates models that involve interactions of categorical and continuous variables.

## 1 Linear and two-level categorical: No interaction

### 1.1 Overview

**This section introduces the concepts involving models that combine continuous and categorical predictors with no interaction.** Let’s begin by considering a hypothetical simple regression model predicting income from age, focusing on people who are aged 22 to 55. 

$$ \widehat{realrinc}=9000 + 400age $$   

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.10.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Simple linear regression predicting income from age</strong></figcaption>
</figure>

Let’s now expand upon this model and introduce a categorical variable with two levels reflecting whether the respondent graduated college (named cograd in the dataset gss_ivrm.dta). It is coded 0 if the respondent did not graduate college and 1 if the respondent did graduate college. 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.11.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>One continuous and one categorical predictor with labels for slopes and intercepts</strong></figcaption>
</figure>

the intercept for those who did not graduate college is 4,000 and for those who did graduate college is 21,000. The difference in theseintercepts is 17,000.

One equation is given for noncollege graduates and another for college graduates, as shown below.


$$ Noncollege - graduate:\widehat{realrinc}=4000 + 400age $$
$$ College - graduate:\widehat{realrinc}=21000 + 400age $$      

This regression model can also be expressed as one equation, as shown below.

$$ \widehat{realrinc}=4000 + 17000cograd + 400age $$      

The predicted values at 30, 40, and 50 years of age have been computed both for those who did and for those who did not graduate college and are graphed in figure below：


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.12.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>One continuous and one categorical predictor with labels for predicted values</strong></figcaption>
</figure>

### 1.2 Examples using the GSS

This section applies the model predicting realrinc from age and cograd using the GSS dataset. 

``` 
  use gss_ivrm.dta
  keep if age>=22 & age<=55
  reg realrinc age i.cograd female,vce(robust)
``` 

        Linear regression                               Number of obs     =     25,718
                                                        F(3, 25714)       =     906.12
                                                        Prob > F          =     0.0000
                                                        R-squared         =     0.1569
                                                        Root MSE          =      23938
        
        ------------------------------------------------------------------------------
                     |               Robust
            realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 age |     539.90      15.27    35.36   0.000       509.98      569.83
                     |
              cograd |
            CO Grad  |   14176.56     425.16    33.34   0.000     13343.22    15009.90
              female |  -12119.58     295.27   -41.05   0.000    -12698.33   -11540.83
               _cons |    4171.57     518.76     8.04   0.000      3154.76     5188.37
        ------------------------------------------------------------------------------
        
Before interpreting the coefficients for this model, let’s create a graph showing the adjusted means as a function of age and cograd.2 First, we use the margins command to compute the adjusted means at ages 22 and 55 for each level of cograd.

``` 
  margins cograd,nopvalues at(age=(22 55)) vsquish
  marginsplot,noci
``` 

        Predictive margins                                      Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: age = 22
        2._at: age = 55
        
        ----------------------------------------------------------------
                       |            Delta-method
                       |     Margin   std. err.     [95% conf. interval]
        ---------------+------------------------------------------------
            _at#cograd |
        1#Not CO Grad  |   10048.09     216.20       9624.33    10471.85
            1#CO Grad  |   24224.65     398.82      23442.95    25006.35
        2#Not CO Grad  |   27864.91     344.23      27190.20    28539.62
            2#CO Grad  |   42041.46     553.37      40956.82    43126.11
        ----------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.13.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong> Fitted values of continuous and categorical model without interaction</strong></figcaption>
</figure>

These parallel lines have the same slope, as represented by the coefficient for age. Regardless of whether you graduated college, the age slope is 539.90.

We can use the margins command to compute adjusted means based on this model. For example, the adjusted mean for someone who graduated college and was 40 years old, adjusting for gender, is 33,942.91.

``` 
  margins,nopvalues at(cograd=1 age=40)
``` 

        Predictive margins                                      Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: age    = 40
            cograd =  1
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
               _cons |   33942.91     419.99      33119.71    34766.11
        --------------------------------------------------------------
        
Let’s repeat this command, but this time obtain the adjusted means separately for those who did and for those who did not graduate college.

``` 
  margins cograd,nopvalues at(age=40)
``` 

        Predictive margins                                      Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: age = 40
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
              cograd |
        Not CO Grad  |   19766.35     151.46      19469.48    20063.23
            CO Grad  |   33942.91     419.99      33119.71    34766.11
        --------------------------------------------------------------

Note the difference between these two values corresponds to the main effect of cograd. Here $33942.91 - 19766.35 = 14176.56$ . You could repeat the above command for any given level of age and the difference in the adjusted means would remain the same.

The adjusted means are computed by setting all the covariates (that is, age and female) to the average value of the entire sample. In this example, adding the at((mean) age female) option specifies that age and female should be held constant at their mean.

``` 
  margins cograd,nopvalues at((mean) age female) 
``` 

        Adjusted predictions                                    Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: age    = 37.28214 (mean)
            female = .4951785 (mean)
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
              cograd |
        Not CO Grad  |   18298.97     129.82      18044.52    18553.42
            CO Grad  |   32475.53     405.08      31681.55    33269.50
        --------------------------------------------------------------


The adjusted mean for noncollege graduates is 18,298.97, compared with 32,475.53 for college graduates. **The difference in these means is sometimes called the marginal effect at the mean, because the effect is computed at the mean of the covariates.**

Based on this example, you might think that you would need to specify each of the covariates in the at() option to compute adjusted means. However, we can use the margins command below and obtain the same results.

``` 
  margins cograd,nopvalues
``` 
        Predictive margins                                      Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
              cograd |
        Not CO Grad  |   18298.97     129.82      18044.52    18553.42
            CO Grad  |   32475.53     405.08      31681.55    33269.50
        --------------------------------------------------------------

The difference in these means, although the same, is sometimes called the **average marginal effect.** In a linear model (for example, following the regress command), the marginal effect at the mean and average marginal effect methods provide the same results.

#### 1.2.1 Unadjusted means
computing the unadjusted mean of income separately for college graduates and noncollage graduates

``` 
  margins,nopvalues over(cograd) 
``` 

        Predictive margins                                      Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        Over:       cograd
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
              cograd |
        Not CO Grad  |   18157.94     128.42      17906.24    18409.65
            CO Grad  |   32821.99     408.30      32021.69    33622.28
        --------------------------------------------------------------

## 2 Linear by two-level categorical interactions

### 2.1 Overview
Let’s build upon the model that was illustrated by including age and cograd as predictors, as well as the interaction between age and cograd. 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.14.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Linear by two-level categorical predictor with labels for intercepts and slopes</strong></figcaption>
</figure>

This figure includes labels showing the intercept and slope for those who did not graduate college (labeled “Int0” and “Slope0”) and the intercept and slope for those who did graduate college (labeled “Int1” and “Slope1”).

The regression equation for this hypothetical example can be written as shown below.

$$ \widehat{realrinc}=9300 + - 1300cograd + 250age + 450cograd*age $$



The intercept in the regression equation corresponds to the intercept for those who did not graduate college (that is, 9,300). The coefficient for cograd is the difference in the intercepts (that is, ). Note how this represents the difference between a college graduate versus a noncollege graduate when age is held constant at zero **(which is implausible and completely absurd)**. This is often called the main effect of cograd, but that is misleading because in the presence of the interaction, this term represents the effect of cograd when age is held constant at zero.

The interaction term (cograd*age) is the difference in the slopes comparing those who graduated college with those who did not graduate college (that is,$700 - 250 = 450$ ). This interaction term compares the slope of college graduates with the slope of noncollege graduates.

It can be easier to understand this model when it is written as two equations

$$ Noncollege - graduate:\widehat{realrinc}=9300 + 250age $$
$$ College - graduate:\widehat{realrinc}=8000 + 700age $$ 

### 2.2 Examples using the GSS

``` 
  use gss_ivrm.dta
  keep if age>=22 & age<=55
  reg realrinc i.cograd age i.cograd#c.age female,vce(robust)
``` 

        Linear regression                               Number of obs     =     25,718
                                                        F(4, 25713)       =     710.76
                                                        Prob > F          =     0.0000
                                                        R-squared         =     0.1661
                                                        Root MSE          =      23807
        
        ------------------------------------------------------------------------------
                     |               Robust
            realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
              cograd |
            CO Grad  |   -8648.27    1426.59    -6.06   0.000    -11444.48    -5852.07
                 age |     375.15      14.68    25.56   0.000       346.39      403.92
                     |
        cograd#c.age |
            CO Grad  |     607.52      42.24    14.38   0.000       524.73      690.31
                     |
              female |  -12004.56     293.46   -40.91   0.000    -12579.76   -11429.37
               _cons |   10224.97     480.32    21.29   0.000      9283.52    11166.41
        ------------------------------------------------------------------------------

As a shorthand, we can specify i.cograd##c.age. **The ## operator includes both the main effects and interactions of the variables specified.**

``` 
  reg realrinc i.cograd##c.age female,vce(robust)
``` 
        Linear regression                               Number of obs     =     25,718
                                                        F(4, 25713)       =     710.76
                                                        Prob > F          =     0.0000
                                                        R-squared         =     0.1661
                                                        Root MSE          =      23807
        
        ------------------------------------------------------------------------------
                     |               Robust
            realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
              cograd |
            CO Grad  |   -8648.27    1426.59    -6.06   0.000    -11444.48    -5852.07
                 age |     375.15      14.68    25.56   0.000       346.39      403.92
                     |
        cograd#c.age |
            CO Grad  |     607.52      42.24    14.38   0.000       524.73      690.31
                     |
              female |  -12004.56     293.46   -40.91   0.000    -12579.76   -11429.37
               _cons |   10224.97     480.32    21.29   0.000      9283.52    11166.41
        ------------------------------------------------------------------------------

$$ \widehat{realrinc}=10224.97 + - 8648.27cograd + 375.15age + 607.5224cograd*age + - 12004.56female $$

Let’s create a graph to aid in the process of interpreting the results. compute the adjusted means for ages22 and 55 separately for each level of cograd

``` 
  margins cograd,nopvalues at(age=(22 55))  
  marginsplot,noci
``` 

        Predictive margins                                      Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: age = 22
        2._at: age = 55
        
        ----------------------------------------------------------------
                       |            Delta-method
                       |     Margin   std. err.     [95% conf. interval]
        ---------------+------------------------------------------------
            _at#cograd |
        1#Not CO Grad  |   12533.97     185.57      12170.25    12897.69
            1#CO Grad  |   17251.19     549.53      16174.07    18328.31
        2#Not CO Grad  |   24914.08     349.75      24228.55    25599.61
            2#CO Grad  |   49679.54     950.38      47816.74    51542.34
        ----------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.15.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Fitted values for linear by two-level categorical predictor model</strong></figcaption>
</figure>

The figure indicate the slope between cograd & notcograd are significant different.

#### 2.2.1 Estimates of slopes

Let’s use the margins command to **compute the age slope for college graduates and noncollege graduates.** The dydx(age) option is used with the over(cograd) option to compute the age slope at each level of cograd. 

``` 
  margins,dydx(age) over(cograd) 
``` 

        Average marginal effects                                Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        dy/dx wrt:  age
        Over:       cograd
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |      dy/dx   std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
        age          |
              cograd |
        Not CO Grad  |     375.15      14.68    25.56   0.000       346.39      403.92
            CO Grad  |     982.68      39.55    24.84   0.000       905.15     1060.20
        ------------------------------------------------------------------------------

#### 2.2.2 Estimates and contrasts on means

Let’s begin the investigation of the effect of cograd by computing the adjusted mean of income for each level of cograd, holding age constant at 30. 


``` 
  margins cograd, nopvalues at(age=30) 
``` 
        Predictive margins                                      Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: age = 30
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
              cograd |
        Not CO Grad  |   15535.21     112.55      15314.60    15755.82
            CO Grad  |   25112.61     367.24      24392.81    25832.41
        --------------------------------------------------------------


Now, let’s compare these two adjusted means by applying the r. contrast operator with cograd, which compares each group with the reference group.This compares group 1 (college graduates) with group 0 (noncollege graduates),  

``` 
  margins r.cograd,at(age=30) contrast(nowald pveffect)
```

        Contrasts of predictive margins                         Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: age = 30
        
        ------------------------------------------------------------------
                                  |            Delta-method
                                  |   Contrast   std. err.      t    P>|t|
        --------------------------+---------------------------------------
                           cograd |
        (CO Grad vs Not CO Grad)  |    9577.40     383.87    24.95   0.000
        ------------------------------------------------------------------
        
Let’s repeat this margins command, but this time specify that the comparisons should be made at three ages—30, 40, and 50.


``` 
  margins r.cograd,at(age=(30 40 50)) contrast(nowald pveffect) vsquish 
```

        Contrasts of predictive margins                         Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: age = 30
        2._at: age = 40
        3._at: age = 50
        
        --------------------------------------------------------------------
                                    |            Delta-method
                                    |   Contrast   std. err.      t    P>|t|
        ----------------------------+---------------------------------------
                         cograd@_at |
        (CO Grad vs Not CO Grad) 1  |    9577.40     383.87    24.95   0.000
        (CO Grad vs Not CO Grad) 2  |   15652.62     481.42    32.51   0.000
        (CO Grad vs Not CO Grad) 3  |   21727.85     820.37    26.49   0.000
        --------------------------------------------------------------------


Let’s now create a graph that visualizes the differences in the adjusted means for college graduates versus noncollege graduates for each year of age from 22 to 55. 


``` 
  margins r.cograd,at(age=(22(1)55))
  marginsplot
```
Then, we use the marginsplot command to graph these differences and the confidence interval for the difference, as shown in figure below. **If the confidence interval does not include zero, then the difference is significant.** 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.16.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Contrasts of fitted values by age with confidence intervals</strong></figcaption>
</figure>

## 3 Linear by three-level categorical interactions

Let’s now explore a model in which a three-level categorical variable is interacted with a continuous variable. 

Let’s extend the previous example by considering three educational groups: 1) non–high school graduates, 2) high school graduates, and 3) college graduates. 
Although not shown in the graph, the intercept is 3,000 for group 1, 3,700 for group 2, and for group 3.

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.17.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Linear by three-level categorical predictor with labels for slopes</strong></figcaption>
</figure>

$$ \widehat{realrinc}=3000 + 700hsgrad + -800cograd + 300age + 100hsgrad*age + 700cograd*age $$


The value of 3,000 is the intercept for those who did not graduate college. The coefficient for hsgrad is the difference in the intercepts between high school graduates and non–high school graduates (that is,$3700 - 3000 = 700$ ). The coefficient for cograd is the difference in the intercepts between college graduates and non–high school graduates (that is,$- 5000 - 3000 = -8000$). 

$$ Non-hsgrad:\widehat{realrinc}=3000 + 300age $$
$$ Hsgrad:\widehat{realrinc}=3700 + 400age $$ 
$$ College-grad:\widehat{realrinc}=-5000 + 1000age $$ 

If we do not reject this null hypothesis, then the education by interaction terms may no longer be needed and could be omitted from the model. If we do reject this null hypothesis, we might be further interested in forming specific contrasts among the slopes.

We might be further interested in forming specific contrasts among the slopes, for example, We might be interested in comparing the different educational groups given different levels of age. As an illustration, the predicted mean of income for those aged 30, 40, and 50 for eacheducational group have been computed and are plotted in figure below.

### 3.1 Examples using the GSS

Let’s continue to use age as the continuous predictor and realrinc as the outcome, but now we will use a three-category education variable, educ3. 


``` 
  use gss_ivrm.dta
  keep if age>=22 & age<=55
  reg realrinc i.educ3##c.age female,vce(robust)
```

        Linear regression                               Number of obs     =     25,718
                                                        F(6, 25711)       =     563.58
                                                        Prob > F          =     0.0000
                                                        R-squared         =     0.1728
                                                        Root MSE          =      23712
        
        ------------------------------------------------------------------------------
                     |               Robust
            realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
               educ3 |
                 HS  |    1906.98    1098.01     1.74   0.082      -245.18     4059.14
               Coll  |   -6287.54    1643.87    -3.82   0.000     -9509.63    -3065.46
                     |
                 age |     299.15      28.35    10.55   0.000       243.58      354.72
                     |
         educ3#c.age |
                 HS  |     120.00      32.96     3.64   0.000        55.40      184.60
               Coll  |     682.88      48.71    14.02   0.000       587.42      778.35
                     |
              female |  -12258.80     293.44   -41.78   0.000    -12833.96   -11683.63
               _cons |    8012.50     944.86     8.48   0.000      6160.51     9864.49
        ------------------------------------------------------------------------------

Before interpreting these results, let’s make a graph showing the adjusted means by age and educ3. 
compute the adjusted means at ages22 and 55 for each level of educ3

``` 
  margins educ3,nopvalues at(age=(22 55))   
  marginsplot,noci
```

        Predictive margins                                      Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: age = 22
        2._at: age = 55
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
           _at#educ3 |
           1#not hs  |    8523.52     361.18       7815.59     9231.45
               1#HS  |   13070.41     208.67      12661.40    13479.43
             1#Coll  |   17259.39     549.76      16181.83    18336.94
           2#not hs  |   18395.49     655.31      17111.04    19679.94
               2#HS  |   26902.25     405.94      26106.58    27697.93
             2#Coll  |   49666.47     950.09      47804.25    51528.69
        --------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.1.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Fitted values for linear by three-level categorical predictor model</strong></figcaption>
</figure>

The dydx(age) is combined with the over(educ3) option to compute the age slope separately for each level of educ3.

#### 3.1.1 Estimates and contrasts on slopes

``` 
  margins,dydx(age) over(educ3) 
```

        Average marginal effects                                Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        dy/dx wrt:  age
        Over:       educ3
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |      dy/dx   std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
        age          |
               educ3 |
             not hs  |     299.15      28.35    10.55   0.000       243.58      354.72
                 HS  |     419.15      16.85    24.87   0.000       386.12      452.18
               Coll  |     982.03      39.55    24.83   0.000       904.51     1059.55
        ------------------------------------------------------------------------------

The output not only shows the age slope at each level of educ3 but also includes the standard error, confidence interval, and a test of whether the slope is significantly different from 0.

Let’s test whether these slopes are equal to each other. In other words,let’s test the null hypothesis：

$$ H_{0} = \beta{1} = \beta{2} = \beta{3} $$  

``` 
  contrast educ3#c.age
```
        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df           F        P>F
        -------------+----------------------------------
         educ3#c.age |          2      105.89     0.0000
                     |
         Denominator |      25711
        ------------------------------------------------

This interaction is significant, so we can reject the null hypothesis that these slopes are all equal.

$$ H_{0} = \beta{1} = \beta{3}  $$  
$$ H_{0} = \beta{2} = \beta{3}  $$ 

The following contrast command tests these two null hypotheses.Specifying rb3.educ3 uses reference group comparisons with group 3 as the baseline (comparison) group. We can reject both null hypotheses.

``` 
  contrast rb3.educ3#c.age,nowald pveffect
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------------
                          |   Contrast   Std. err.      t    P>|t|
        ------------------+---------------------------------------
              educ3#c.age |
        (not hs vs Coll)  |    -682.88      48.71   -14.02   0.000
            (HS vs Coll)  |    -562.89      43.03   -13.08   0.000
        ----------------------------------------------------------

Let’s try using the j. contrast operator that compares each group with the mean of the previous levels, as shown below.

``` 
  contrast j.educ3#c.age,nowald pveffect
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ---------------------------------------------------------
                         |   Contrast   Std. err.      t    P>|t|
        -----------------+---------------------------------------
             educ3#c.age |
        (HS vs  not hs)  |     120.00      32.96     3.64   0.000
        (Coll vs <Coll)  |     622.88      42.90    14.52   0.000
        ---------------------------------------------------------


#### 3.1.2 Estimates and contrasts on means

Another way we can understand this interaction is to examine the differences in the adjusted means among the levels of educ3 for a specified level (or levels) of age

``` 
  margins educ3,nopvalues at(age=30)
```

        Predictive margins                                      Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: age = 30
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
               educ3 |
             not hs  |   10916.72     205.36      10514.21    11319.23
                 HS  |   16423.59     127.05      16174.56    16672.61
               Coll  |   25115.65     367.37      24395.58    25835.72
        --------------------------------------------------------------

Let’s test the equality of these means by adding the contrast(overall) option. This tests the equality of the adjusted means across the levels of educ3 when age is held constant at 30. This test is significant.

``` 
  margins educ3,at(age=30) contrast(overall)
```
        Contrasts of predictive margins                         Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: age = 30
        
        ------------------------------------------------
                     |         df           F        P>F
        -------------+----------------------------------
               educ3 |          2      593.45     0.0000
                     |
         Denominator |      25711
        ------------------------------------------------

Let’s use the ar. contrast operator to perform adjacent group contrasts on educ3 while holding age constant at 30 40 and 50. 

``` 
  margins ar.educ3,at(age=(30 40 50)) contrast(nowald pveffect)
```
        Contrasts of predictive margins                         Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: age = 30
        2._at: age = 40
        3._at: age = 50
        
        ----------------------------------------------------------
                          |            Delta-method
                          |   Contrast   std. err.      t    P>|t|
        ------------------+---------------------------------------
                educ3@_at |
        (HS vs not hs) 1  |    5506.86     242.88    22.67   0.000
        (HS vs not hs) 2  |    6706.82     333.60    20.10   0.000
        (HS vs not hs) 3  |    7906.78     617.12    12.81   0.000
          (Coll vs HS) 1  |    8692.06     387.21    22.45   0.000
          (Coll vs HS) 2  |   14320.93     488.59    29.31   0.000
          (Coll vs HS) 3  |   19949.79     835.38    23.88   0.000
        ----------------------------------------------------------


Say that we wanted to form the same comparisons on education (that is, specifying ar.educ3) but spanning the ages ranging from 22 to 55. We can create a graph that illustrates such comparisons using the margins and marginsplot commands. 

First, the margins command is used, applying the ar. contrast operator to educ combined with the at() option. This performs the contrasts of education groups 2 versus 1 (high school graduates versus non–high school graduates) and education group 3 versus 2 (college graduates versus high school graduates) holding age constant at every value ranging from 22 to 55 years of age.

``` 
  margins ar.educ3,at(age=(22(1)55))
  marginsplot,recast(line)recastci(rarea) bydimension(educ3)
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.2.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Adjacent contrasts on education by age with confidence intervals, as two graph panels</strong></figcaption>
</figure>
