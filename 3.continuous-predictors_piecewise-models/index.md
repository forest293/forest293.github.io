# Chapter4 ：Continuous predictors: Piecewise models


**This chapter illustrates the use of piecewise regression. This involves fitting separate line segments, demarcated by knots, that account for the nonlinearity between the predictor and outcome.**

## 1 Introduction to piecewise regression models

A piecewise regression goes by several names, including **spline regression**,**broken line regression**, **broken stick regression**, and even **hockey stick models**.

Consider the example, predicting annual income from education, shown in the figure below


<div align=center>
<img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/piecewise.png"title="Piecewise regression with one knot (left) and two knots (right)"  />
</div>

**A knot can signify a change of slope and a change of intercept, yielding an increase (or decrease) in the outcome upon attaining a particular milestone.**


<div align=center>
<img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/picewise1.png"title="Piecewise regression with one knot (left) and two knots (right)"  />
</div>


>Note! Instantaneous jumps?
>>As a thought experiment, imagine someone being one day short of graduating high school and the wages they would obtain as they seek a job. Compare this person with an identical job seeker who has one more day of education (that is, they graduated high school). These two people are identical except that one crossed the threshold of getting a diploma. It is indeed plausible that the second person would be offered an annual income $2,200 more than the first person$. 

Suppose you have a predictor that shows a nonlinear relationship with the outcome, and you believe that a piecewise model with one knot signifying a change in slope would fit your data well. However, unlike the previous examples, you do not have a theoretical or practical basis for selecting the placement of the knot. You could haphazardly try a variety of placements for the knot, trying to find a placement that results in the best fitting model. Alternatively, **you can let Stata do the work for you by using a least-squares procedure for selecting a location for the knot that produces the lowest residual sum of squares.**

## 2 Piecewise with one known knot
This section illustrates piecewise regression with one knot.

### 2.1 Examples using the GSS

Let’s use the GSS dataset to illustrate a piecewise model with one knot, focusing on **the relationship between income and education.**

``` code
  use gss_ivrm.dta
  reg realrinc i.educ,vce(robust)
  margins educ
  marginsplot,noci
```

![Mean of income at each level of education](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/piecewise2.png)

It looks like the relationship between education and income could be fit well with a piecewise model with a knot at 12 (corresponding to graduating high school). The following subsections illustrate two different ways to fit this kind of piecewise model: **using individual slope coding** and **using change in slope coding.**

#### 2.1.1 Individual slope coding

The individual slope coding scheme **estimates the slope of each line segment of the piecewise model.**

The first step is to **create two new variables that are coded to represent the educ slope before and after graduating high school.** The **mkspline** command creates the variables ed1 and ed2 based on the original variable educ. The value of 12 is inserted between ed1 and ed2, indicating that this is the knot.

``` code
  mkspline ed1 12 ed2 = educ
  showcoding educ ed1 ed2
``` 

<div align=center>
<img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/showcoding.png"title="showcoding educ ed1 ed2" width = "300" height = "400" />
</div>

The next step is to use the regress command to predict realrinc from ed1 and ed2. This will yield a piecewise model with 12 years of education as the knot.

``` code
  reg realrinc ed1 ed2 female,vce(robust)
``` 



     Linear regression                               Number of obs     =     32,183
                                                     F(3, 32179)       =    1030.24
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.1420
                                                     Root MSE          =      25045

------------------------------------------------------------------------------
             |               Robust
    realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
                                                                              
         ed1 |     832.27      72.38    11.50   0.000       690.40      974.14
         ed2 |    3441.33      93.42    36.84   0.000      3258.21     3624.44
      female |  -12372.38     276.35   -44.77   0.000    -12914.05   -11830.72
       _cons |   12052.18     793.42    15.19   0.000     10497.04    13607.31
------------------------------------------------------------------------------

Among non–high school graduates(ed1), each additional year of education is predicted to increase income by $832.27$.The coefficient for ed2 is the slope for those with 12 or more years of education (high school graduates). Income increases by $3,441.33$ for each additional year of education beyond 12 years of education(ed2). Each of these slopes is significantly different from 0.

Say that we want to compute the adjusted mean given that a person has eight years of education. We can compute this adjusted mean using the **margins command**;

To graph the adjusted means as a function of education, we need to compute the adjusted means when education is 0, 12, and 20. The margins command below computes these three adjusted means by using the at() option three times.


``` code
  margins,at(ed1=0 ed2=0) at(ed1=12 ed2=0) at(ed1=12 ed2=8) vsquish
``` 
     Predictive margins                                      Number of obs = 32,183
     Model VCE: Robust

     Expression: Linear prediction, predict()
     1._at: ed1 =  0
            ed2 =  0
     2._at: ed1 = 12
            ed2 =  0
     3._at: ed1 = 12
            ed2 =  8

------------------------------------------------------------------------------
             |            Delta-method
             |     Margin   std. err.      t    P>|t|     [95% conf. interval]
                                                                              
         _at |
          1  |    5964.98     794.24     7.51   0.000      4408.23     7521.72
          2  |   15952.22     161.46    98.80   0.000     15635.75    16268.69
          3  |   43482.83     657.66    66.12   0.000     42193.79    44771.86
------------------------------------------------------------------------------

``` code
  preserve
  clear
  input educ yhat
  0  5964.977
  12 15952.22
  20 43482.83
  end

  graph twoway line yhat educ,xlabel(0(4)20) xline(12)
  restore
``` 
![Adjusted means for income from a piecewise model with one knot at educ = 12](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/one%20knot.png)


#### 2.1.2 Change in slope coding

This coding scheme estimates the slope for the line segment before the first knot (for example, for non–high school graduates) and then estimates the difference in the slopes of adjacent line segments (for example, for high school graduates versus non–high school graduates). This strategy emphasizes the change in slope that occurs at each knot.

The key difference is that **we add the marginal option to the mkspline command,** as shown below. I name the variables ed1m and ed2m, adding the m to emphasize that **these variables were created using the marginal option.**

``` code
  mkspline ed1m 12 ed2m = educ,marginal
``` 

<div align=center>
<img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/showcoding1.png"title="showcoding" width = "300" height = "400" />
</div>

The next step is to enter ed1m and ed2m as predictors of income, as shown below.

``` code
  reg realrinc ed1m ed2m female,vce(robust)
```

     Linear regression                               Number of obs     =     32,183
                                                     F(3, 32179)       =    1030.24
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.1420
                                                     Root MSE          =      25045

------------------------------------------------------------------------------
             |               Robust
    realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
                                                                              
        ed1m |     832.27      72.38    11.50   0.000       690.40      974.14
        ed2m |    2609.06     134.40    19.41   0.000      2345.63     2872.48
      female |  -12372.38     276.35   -44.77   0.000    -12914.05   -11830.72
       _cons |   12052.18     793.42    15.19   0.000     10497.04    13607.31
------------------------------------------------------------------------------
#### 2.1.3 Summary

The individual slope coding method **omitted the marginal option on the mkspline command and yielded estimates of the educ slope before and after the knot.** We also saw the change in slope coding method, which **included the marginal option on the mkspline command, and yielded an estimate of the educ slope before the knot and the change in the educ slope before and after the knot.**


## 3 Piecewise with two known knots
### 3.1 Examples using the GSS

``` code
  use gss_ivrm.dta
  mkspline ed1m 12 ed2m 16 ed3m = educ,marginal
  showcoding educ ed1m ed2m ed3m
```

<div align=center>
<img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/showcoding2.png"title="showcoding" width = "300" height = "400" />
</div>

The coefficient for ed2m will represent the change in slope for **high school graduates versus non–high school graduates.** The coefficient for ed3m will represent the change in slope **comparing college graduates with high school graduates.**

``` code
  reg realrinc ed1m ed2m ed3m female,vce(robust)
```
     Linear regression                               Number of obs     =     32,183
                                                     F(4, 32178)       =     784.42
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.1432
                                                     Root MSE          =      25029
  
------------------------------------------------------------------------------
             |               Robust
    realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
                                                                              
        ed1m |     939.67      70.55    13.32   0.000       801.40     1077.95
        ed2m |    2022.42     144.24    14.02   0.000      1739.71     2305.13
        ed3m |    1657.45     412.15     4.02   0.000       849.62     2465.28
      female |  -12336.74     277.09   -44.52   0.000    -12879.84   -11793.64
       _cons |   11215.32     787.53    14.24   0.000      9671.73    12758.92
------------------------------------------------------------------------------

We can use the margins command to compute adjusted means for any given value of education by expressing education in terms of the variables ed1m, ed2m, and ed3m.

To graph the entire range of education values, we need to compute the adjusted means for the minimum of education (0), for each of the knots (12 and 16), and for the maximum of education (20). **The margins command below computes these adjusted means using the at() option once for each of these four levels of education.**

``` code
  margins,at(ed1m=0 ed2m=0 ed3m=0) ///
          at(ed1m=12 ed2m=0 ed3m=0) ///
          at(ed1m=16 ed2m=4 ed3m=0) ///
          at(ed1m=20 ed2m=8 ed3m=4) vsquish   
```

     Predictive margins                                      Number of obs = 32,183
     Model VCE: Robust

     Expression: Linear prediction, predict()
     1._at: ed1m =  0
            ed2m =  0
            ed3m =  0
     2._at: ed1m = 12
            ed2m =  0
            ed3m =  0
     3._at: ed1m = 16
            ed2m =  4
            ed3m =  0
     4._at: ed1m = 20
            ed2m =  8
            ed3m =  4

------------------------------------------------------------------------------
             |            Delta-method
             |     Margin   std. err.      t    P>|t|     [95% conf. interval]

         _at |
          1  |    5145.66     785.16     6.55   0.000      3606.72     6684.60
          2  |   16421.73     145.60   112.79   0.000     16136.36    16707.11
          3  |   28270.11     383.13    73.79   0.000     27519.16    29021.06
          4  |   46748.30    1242.79    37.62   0.000     44312.38    49184.22
------------------------------------------------------------------------------
We can then manually input the adjusted means from the margins command into a dataset as shown below.
The graph command is then used to graph the relationship between education and income. 
``` code
  preserve
  clear
  input educ yhat
  0 5154.66
  12 16421.73
  16 28270.11
  20 46748.3
  end
  
  graph twoway line yhat educ,xlabel(0(4)20) xline(12 16) xtitle(Education) ytitle(Adjusted mean)
  restore
```

![Adjusted means from piecewise model with knots at educ = 12 and educ = 16](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/two%20knot.png)

## 4 Piecewise with one knot and one jump
### 4.1 Examples using the GSS



``` code
  use gss_ivrm.dta
  mkspline ed1m 12 ed2m = educ,marginal
  reg realrinc ed1m ed2m hsgrad female,vce(robust)
```
The variable **hsgrad** is coded 1 if someone has 12 or more years of education, and 0 otherwise.


     Linear regression                               Number of obs     =     32,183
                                                     F(4, 32178)       =     803.67
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.1425
                                                     Root MSE          =      25039

------------------------------------------------------------------------------
             |               Robust
    realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]

        ed1m |     273.10     105.88     2.58   0.010        65.56      480.64
        ed2m |    3102.95     141.83    21.88   0.000      2824.97     3380.94
      hsgrad |    2721.54     412.82     6.59   0.000      1912.40     3530.67
      female |  -12391.31     276.30   -44.85   0.000    -12932.87   -11849.74
       _cons |   16345.24     988.38    16.54   0.000     14407.98    18282.49
------------------------------------------------------------------------------

At 12 years of education, the adjusted mean is **computed twice**, once assuming the absence of a high school degree and once **assuming a high school degree**, illustrating the jump in income due to graduating high school.


<div align=center>
<img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/one%20jump1.png"title="Adjusted means from piecewise model with one knot and one jump at educ = 12"  />
</div>

**How tow interpret the regression coefficient?**
* For each additional year of education (up to 12), income increases by $273.10.$figure shows the adjusted means given zero and one year of education. This difference in these adjusted means equals 273.10 $(10521.83-10248.73)$
* The coefficient of ed2m is 3,102.95, which is the change (increase) in slope for high school graduates compared with non–high school graduates. 
* Finally, the hsgrad coefficient (2,721.54) represents the predicted jump (increase in income) due to graduating high school. 

``` code
  showcoding educ hsgrad ed1m ed2m
  margins,at(ed1m=0 ed2m=0 hsgrad=0) ///
          at(ed1m=12 ed2m=0 hsgrad=0) ///
          at(ed1m=12 ed2m=0 hsgrad=1) ///
          at(ed1m=20 ed2m=8 hsgrad=1) vsquish 

```

<div align=center>
<img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/12.31.png"title="showcoding" width = "300" height = "400" />
</div>



     Predictive margins                                      Number of obs = 32,183
     Model VCE: Robust

     Expression: Linear prediction, predict()
     1._at: ed1m   =  0
            ed2m   =  0
            hsgrad =  0
     2._at: ed1m   = 12
            ed2m   =  0
            hsgrad =  0
     3._at: ed1m   = 12
            ed2m   =  0
            hsgrad =  1
     4._at: ed1m   = 20
            ed2m   =  8
            hsgrad =  1

------------------------------------------------------------------------------
             |            Delta-method
             |     Margin   std. err.      t    P>|t|     [95% conf. interval]

          1  |   10248.73     987.42    10.38   0.000      8313.35    12184.10
          2  |   13525.93     374.97    36.07   0.000     12790.97    14260.89
          3  |   16247.46     174.79    92.95   0.000     15904.86    16590.07
          4  |   43255.90     664.00    65.14   0.000     41954.43    44557.37
------------------------------------------------------------------------------

![Adjusted means from piecewise model with one knot and one jump at educ = 12](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/one%20jump2.png)


```
  preserve
  clear
  input educ yhat
  0  10248.73
  12  13525.93
  12  16247.46
  20  43255.9
  end
  graph twoway line yhat educ,xlabel(0(4)20) xline(12)
  restore
```

> Note! Individual slope coding
>>This model was fit using the change in slope coding method (that is, with the marginal option on the mkspline command). If you wished, you could fit this model using individual slope coding by omitting the marginal option on the mkspline command. 

## 5 Piecewise with two knots and two jumps

**This section will illustrate a model with two knots signifying a change of slope and intercept.**

### 5.1 Examples using the GSS


``` code
  use gss_ivrm.dta
  mkspline ed1m 12 ed2m 16 ed3m = educ,marginal
  reg realrinc ed1m ed2m ed3m hsgrad cograd female,vce(robust)
```
     Linear regression                               Number of obs     =     32,183
                                                     F(6, 32176)       =     544.02
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.1458
                                                     Root MSE          =      24991

------------------------------------------------------------------------------
             |               Robust
    realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
 
        ed1m |     272.27     105.87     2.57   0.010        64.76      479.79
        ed2m |    1329.81     189.31     7.02   0.000       958.75     1700.88
        ed3m |    2540.17     398.71     6.37   0.000      1758.69     3321.65
      hsgrad |    3925.58     405.81     9.67   0.000      3130.18     4720.98
      cograd |    5740.76     733.17     7.83   0.000      4303.73     7177.80
      female |  -12358.72     276.69   -44.67   0.000    -12901.04   -11816.40
       _cons |   16339.10     988.28    16.53   0.000     14402.04    18276.16
------------------------------------------------------------------------------

<div align=center>
<img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/two%20jump.png"title="Adjusted means from piecewise model with knots and jumps at educ = 12 and educ = 16"  />
</div>


``` code
  showcoding educ ed1m ed2m ed3m hsgrad cograd
  margins,at (ed1m=0 ed2m=0 ed3m=0 hsgrad=0 cograd=0) ///
          at(ed1m=12 ed2m=0 ed3m=0 hsgrad=0 cograd=0) ///
          at(ed1m=12 ed2m=0 ed3m=0 hsgrad=1 cograd=0) ///
          at(ed1m=16 ed2m=4 ed3m=0 hsgrad=1 cograd=0) ///
          at(ed1m=16 ed2m=4 ed3m=0 hsgrad=1 cograd=1) ///
          at(ed1m=20 ed2m=8 ed3m=4 hsgrad=1 cograd=1) vsquish noatlegend
```

<div align=center>
<img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/two%20jump1.png"title="showcoding" width = "300" height = "400" />
</div>

     Predictive margins                                      Number of obs = 32,183
     Model VCE: Robust

     Expression: Linear prediction, predict()

------------------------------------------------------------------------------
             |            Delta-method
             |     Margin   std. err.      t    P>|t|     [95% conf. interval]

         _at |
          1  |   10258.62     987.32    10.39   0.000      8323.44    12193.81
          2  |   13525.88     374.93    36.08   0.000     12791.01    14260.75
          3  |   17451.46     157.37   110.90   0.000     17143.02    17759.91
          4  |   23859.81     558.53    42.72   0.000     22765.07    24954.55
          5  |   29600.57     475.61    62.24   0.000     28668.35    30532.79
          6  |   46169.58    1255.73    36.77   0.000     43708.30    48630.87
------------------------------------------------------------------------------


``` code
  preserve
  clear
  input educ yhat
  0 10258.62
  12 13525.88
  12 17451.46
  16 23858.81
  16 29600.57
  20 46169.58
  end
  
  graph twoway line yhat educ,xlabel(0(4)20) xline(12 16) xtitle(Education) ytitle(Adjusted means)
  restore
```
![Adjusted means from piecewise model with knots and jumps at educ = 12 and educ = 16](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/two%20jump2.png)


## 6 Piecewise with an unknown knot
This section illustrates a method for fitting a piecewise model with one knot where the location of the knot is uncertain.

Consider the relationship between year of birth and level of education. In this dataset, the year of birth is recorded in the variable yrborn and education level is recorded in the variable educ. To visualize the relationship between year of birth and education, let’s make a graph showing the mean of educ at each level of yrborn.

``` code
  use gss_ivrm.dta
  keep if (yrborn>=1905 & yrborn<=1985) & !missing(educ)
  reg educ i.yrborn
  margins yrborn
  marginsplot,noci
```

![Average education at each level of year of birth](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/unkoownjump.png)

We could haphazardly select different years for the placement of the knot and select the knot that yields the best fitting model. Rather than manually doing this selection process, we can use the **nl command** to ***automate the process of selecting the optimal knot, by selecting a knot location that yields the lowest residual sum of squares.*** The code for fitting such a model is shown below.

``` code
  nl (educ = ({cons} + {b1}*yrborn)*(yrborn<{knot}) + ///
  ({cons} + {b1}*{knot} + {b2}*(yrborn-{knot}))*(yrborn>={knot})), ///
  initial(knot 1945 b1 .1 b2 -.0125 cons -181)
```
     Iteration 0:  Residual SS = 464140
     Iteration 1:  Residual SS = 464092.3
     Iteration 2:  Residual SS = 464092.3


      Source |      SS            df       MS
                                                    Number of obs =     52,873
       Model |  48574.186          3  16191.3952    R-squared     =     0.0947
    Residual |  464092.28      52869  8.77815515    Adj R-squared =     0.0947
                                                    Root MSE      =   2.962795
       Total |  512666.47      52872  9.69636992    Res. dev.     =   264897.3

------------------------------------------------------------------------------
        educ | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
 
       /cons |    -152.07       3.24   -46.88   0.000      -158.43     -145.71
         /b1 |       0.09       0.00    50.59   0.000         0.08        0.09
       /knot |    1946.97       0.51  3800.38   0.000      1945.96     1947.97
         /b2 |      -0.01       0.00    -2.98   0.003        -0.01       -0.00
------------------------------------------------------------------------------
Note: Parameter cons is used as a constant term during estimation.

* The coefficient labeled cons is the constant for the model (the predicted value of educ when yrborn is 0). This value is generally uninteresting. 
* The coefficient labeled knot is the location of the knot, selected as 1946.97 (which we can round to 1947). 
* The coefficient labeled b1 is the slope of the relationship between education and year of birth for those born before the knot (before 1947). 
* The coefficient labeled b2 is the slope for those born in or after 1947. 
* The key is that 1947 was selected as the optimal location for the knot.

**How to compute the coef respectively and interpret the graph ?**

>In place of educ, you would insert your outcome variable, and in place of yrborn, you would place your predictor variable. Then for the initial() option, you would insert plausible values for knot, b1, b2, and cons. These correspond to **the placement of the knot**, **the slope before the knot**, **the slope after the knot**, and **the constant**, respectively.


<div align=center>
<img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/unkownjump1.png"title="Average education with hand-drawn fitted lines"  />
</div>

* In this example, I used 1945 as the knot. To estimate b1, I looked at the graph and saw that the mean education rose about **4 units** from 1905 to 1945. **Taking a change of 4 units divided by 40 gave me an estimate of 0.1 for b1.**
* Likewise, I estimated that education declined by about 0.5 units from 1945 to 1985 and divided $-0.5$ units by 40 to yield $-0.0125$ as an estimate of b2. 
* Finally, to estimate cons I estimated the average education to be 9.5 units at 1905 and then used the estimated slope of 0.1 to estimate the education at year 0 would be $9.5-1905×0.1=-181.$

## 7 Piecewise model with multiple unknown knots

**This section illustrates the use of piecewise regression models where there could be multiple knots in the relationship between the predictor and outcome, and you have no basis for the placement of the knots.**

Consider the relationship between age and income. I would suspect that income would initially rise rapidly with increasing age, hit a peak, and then decline with increasing age. 
In such a case, we can intentionally fit a model with too many knots and then progressively remove the superfluous knots. 


``` code
  use gss_ivrm.dta
  keep if age<=80
  * Model 0
  reg realrinc i.age,vce(robust)
```
* The test of these indicators is significant, and they have an $R^2$  value of 0.0581. This $R^2$ value is the highest amount of variance we could hope to explain using age as a predictor. **This is because this set of indicators accounts for every tiny bump and drop in the relationship between age and income.** 
* Any simpler model (for example, linear, quadratic, or piecewise) will not account for all the bumps and drops and thus will have a lower $R^2$ . 
* However, a good model will account for the major bumps and drops and have an $R^2$ that is not too much smaller than the $R^2$ from the indicator model. This is our goal in fitting the piecewise model with multiple knots.


**Let’s visualize the relationship between age and income. We can do this by using the predict command to create predicted values based on the indicator model, in this case named yhatind.**


``` code
  predict yhatind
  graph twoway line yhatind age,sort xlabel(18 20(5)80)
  graph twoway line yhatind age,xlabel(25 30 35 45 55 65) xline(25 30 35 45 55 65)sort
```
![Income predicted from age using indicator model with lines at ages 25, 30, 35, 45, 55, and 65](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/multiple%20unknown%20knots.png)

**Let’s first fit a model specified by the knots at ages 25, 30, 35, 45, 55, and 65, which we will call model 1.** we will use the **mkspline** command to create knots at each of the ages, and this creates the variables age18to24m to age65to80m.

``` code
  model1 : full model*
  mkspline age18to24m 25 age25to29m 30 ///
           age30to34m 35 age35to44m 45 ///
		   age45to54m 55 age55to64m 65 ///
           age65to80m=age,marginal
  reg realrinc age18to24m-age65to80m,vce(robust)
```

     Linear regression                               Number of obs     =     32,100
                                                     F(7, 32092)       =     712.84
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.0559
                                                     Root MSE          =      26153

------------------------------------------------------------------------------
                |               Robust
       realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
                                                                                   
     age18to24m |    1559.14      66.34    23.50   0.000      1429.10     1689.18
     age25to29m |    -582.00     131.65    -4.42   0.000      -840.04     -323.96
     age30to34m |     -45.67     188.34    -0.24   0.808      -414.82      323.48
     age35to44m |    -494.46     184.93    -2.67   0.008      -856.93     -131.99
     age45to54m |    -310.18     154.30    -2.01   0.044      -612.61       -7.76
     age55to64m |    -827.83     190.35    -4.35   0.000     -1200.93     -454.73
     age65to80m |      -4.33     214.92    -0.02   0.984      -425.59      416.93
          _cons |  -25134.60    1502.49   -16.73   0.000    -28079.54   -22189.66
------------------------------------------------------------------------------

When a coefficient regarding the change in the slope in the line segments is not significant, **it indicates a knot that can be removed because the slopes before and after the knot are not significantly different. We can use this as a guide for removing superfluous knots.**

we can see that the slope for those who are 55 to 64 years old is similar to the slope for those who are 65 years old and older. Thus, the knot at age 65 is not really needed and we can assume one slope from ages 55 to 80. 

``` code
  *model 2 :drop knot at age 65
  drop age18to24m-age65to80m
  mkspline age18to24m 25 age25to29m 30 ///
           age30to34m 35 age35to44m 45 ///
		   age45to54m 55 age55to80m=age,marginal
  reg realrinc age18to24m-age55to80m,vce(robust)
```
     Linear regression                               Number of obs     =     32,100
                                                     F(6, 32093)       =     830.57
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.0559
                                                     Root MSE          =      26152

------------------------------------------------------------------------------
                |               Robust
       realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]

     age18to24m |    1559.15      66.34    23.50   0.000      1429.11     1689.18
     age25to29m |    -582.03     131.64    -4.42   0.000      -840.05     -324.01
     age30to34m |     -45.57     188.23    -0.24   0.809      -414.50      323.36
     age35to44m |    -494.68     184.31    -2.68   0.007      -855.94     -133.42
     age45to54m |    -309.49     146.24    -2.12   0.034      -596.12      -22.87
     age55to80m |    -829.99     133.15    -6.23   0.000     -1090.96     -569.01
          _cons |  -25134.71    1502.46   -16.73   0.000    -28079.59   -22189.83
------------------------------------------------------------------------------

In model 2, the coefficient for age30to34m is not significant. This suggests that the slope for those aged 30 to 34 are not different from those aged 25 to 29. Let’s consolidate the slope for the ages 25 to 35 and refit the model, calling it model 3.

``` code
  *model 3 :drop knot at age 30
  drop age18to24m-age55to80m
  mkspline age18to24m 25 age25to34m 35 ///
           age35to44m 45 age45to54m 55 ///
		   age55to80m=age,marginal
  reg realrinc age18to24m-age55to80m,vce(robust)
```
     Linear regression                               Number of obs     =     32,100
                                                     F(5, 32094)       =     961.72
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.0559
                                                     Root MSE          =      26152

------------------------------------------------------------------------------
                  |               Robust
         realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]

       age18to24m |    1570.77      66.99    23.45   0.000      1439.47     1702.07
       age25to34m |    -617.88     105.51    -5.86   0.000      -824.69     -411.08
       age35to44m |    -520.35     108.28    -4.81   0.000      -732.58     -308.11
       age45to54m |    -303.91     139.22    -2.18   0.029      -576.79      -31.03
       age55to80m |    -831.48     132.55    -6.27   0.000     -1091.27     -571.68
            _cons |  -25377.50    1512.81   -16.78   0.000    -28342.66   -22412.33
------------------------------------------------------------------------------
we should be mindful that we have a **very large-sample size with considerable statistical power to detect minor changes in slopes.** If we focus solely on statistical significance as our guide for removing knots,we could **overfit the model.** Let’s visualize the fit of this model against the mean of income at each level of age to see how well the model fits.


``` code
  predict yhat
  line yhat yhatind age,sort xline(25 35 45 55) xlabel(25 35 45 55) ///
  legend(label(1 "piecewise model")label(2 "indicator model"))
```

![Income predicted from age using indicator model and piecewise model 3 with four knots at 25, 35, 45, and 55](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/unkownjump3.png)

For the sake of creating a parsimonious model, let’s try removing the knot at age 45. This fits one slope for those who are 35 to 54 years old. Let’s call this model 4.

``` code
  *model 4:drop knot at age 45
  drop age18to24m-age55to80m
  mkspline age18to24m 25 age25to34m 35 ///
           age35to54m 55 age55to80m=age,marginal
  reg realrinc age18to24m-age55to80m,vce(robust)
```

     Linear regression                               Number of obs     =     32,100
                                                     F(4, 32095)       =    1201.52
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.0557
                                                     Root MSE          =      26154

------------------------------------------------------------------------------
                |               Robust
       realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]

     age18to24m |    1526.97      66.45    22.98   0.000      1396.73     1657.21
     age25to34m |    -503.65     101.85    -4.95   0.000      -703.28     -304.02
     age35to54m |    -736.21      72.34   -10.18   0.000      -878.01     -594.42
     age55to80m |   -1047.58      78.90   -13.28   0.000     -1202.23     -892.94
          _cons |  -24462.77    1502.23   -16.28   0.000    -27407.21   -21518.34
------------------------------------------------------------------------------

## 8 Automating graphs of piecewise models

If you change your data (for example, fix incorrect values), the adjusted means will not be automatically updated to reflect the new data.**This section illustrates a more efficient, but trickier, way of creating such graphs that does not require retyping the data.**

``` code
  use gss_ivrm.dta
  mkspline ed1m 12 ed2m 16 ed3m = educ,marginal
  reg realrinc ed1m ed2m ed3m hsgrad cograd female,vce(robust)
  margins,at(ed1m=0 ed2m=0 ed3m=0 hsgrad=0 cograd=0) ///
          at(ed1m=1 ed2m=0 ed3m=0 hsgrad=0 cograd=0) ///
		      at(ed1m=12 ed2m=0 ed3m=0 hsgrad=0 cograd=0) ///
		      at(ed1m=12 ed2m=0 ed3m=0 hsgrad=1 cograd=0) ///
		      at(ed1m=13 ed2m=1 ed3m=0 hsgrad=1 cograd=0) ///
		      at(ed1m=16 ed2m=4 ed3m=0 hsgrad=1 cograd=0) ///
		      at(ed1m=16 ed2m=4 ed3m=0 hsgrad=1 cograd=1) ///
		      at(ed1m=17 ed2m=5 ed3m=1 hsgrad=1 cograd=1) ///
		      at(ed1m=20 ed2m=8 ed3m=4 hsgrad=1 cograd=1)
		  
```

The following steps **save the adjusted means from the margins command, as well as the corresponding values of education into the active dataset.** The graph command is then used to graph the adjusted means by education, creating the graph shown below

```
  matrix yhat = r(b)'
  svmat yhat
  matrix educ = ( 0 \ 1 \ 12 \ 12 \ 13 \ 16 \ 16 \ 17 \ 20 )
  svmat educ
  graph twoway line yhat1 educ1, xline(12 16) xtitle(Education) ytitle(Adjusted mean)
```

![Adjusted means from piecewise regression with two knots and two jumps](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/automating%20graph%20of%20piecewise%20model.png)

Let’s walk through this process again, but do so more slowly. 
* First, repeat the use gss_ivrm command, as well as the mkspline, regress, and margins commands from above. 
* Now, the adjusted means computed by the margins command are stored in a matrix named r(b) with one row and nine columns, corresponding to the nine values specified with the at() option. 

``` code
  use gss_ivrm.dta
  mkspline ed1m 12 ed2m 16 ed3m = educ,marginal
  reg realrinc ed1m ed2m ed3m hsgrad cograd female,vce(robust)
  margins,at(ed1m=0 ed2m=0 ed3m=0 hsgrad=0 cograd=0) ///
          at(ed1m=1 ed2m=0 ed3m=0 hsgrad=0 cograd=0) ///
		      at(ed1m=12 ed2m=0 ed3m=0 hsgrad=0 cograd=0) ///
		      at(ed1m=12 ed2m=0 ed3m=0 hsgrad=1 cograd=0) ///
		      at(ed1m=13 ed2m=1 ed3m=0 hsgrad=1 cograd=0) ///
		      at(ed1m=16 ed2m=4 ed3m=0 hsgrad=1 cograd=0) ///
		      at(ed1m=16 ed2m=4 ed3m=0 hsgrad=1 cograd=1) ///
		      at(ed1m=17 ed2m=5 ed3m=1 hsgrad=1 cograd=1) ///
		      at(ed1m=20 ed2m=8 ed3m=4 hsgrad=1 cograd=1)
	matrix list r(b)
```

     r(b)[1,9]
                 1.         2.         3.         4.         5.         6.         7.         8.         9.
               _at        _at        _at        _at        _at        _at        _at        _at        _at
     r1  10258.623  10530.894  13525.881  17451.464   19053.55   23859.81  29600.571  33742.824  46169.582

Let’s store these adjusted means as a matrix called yhat and in the process transpose the matrix (converting the columns to rows). 

```
  matrix yhat = r(b)'
  matrix list yhat
```
     yhat[9,1]
                   r1
     1._at  10258.623
     2._at  10530.894
     3._at  13525.881
     4._at  17451.464
     5._at  19053.55
     6._at  23859.81
     7._at  29600.571
     8._at  33742.824
     9._at  46169.582

We can then save yhat into the current dataset with the **svmat** command. 

```
  svmat yhat
  list yhat1 in 1/10
```

          +----------+
          |    yhat1 |
          |----------|
     1.   | 10258.62 |
     2.   | 10530.89 |
     3.   | 13525.88 |
     4.   | 17451.46 |
     5.   | 19053.55 |
     6.   | 23859.81 |
     7.   | 29600.57 |
     8.   | 33742.82 |
     9.   | 46169.58 |
     10.  |        . |
          +----------+

Now, let’s make a matrix containing the values of education. **This is stored in the matrix named educ. We then save this matrix into the dataset,** as shown below.         

```
  matrix educ = (0 \ 1 \ 12 \ 12 \ 13 \ 16 \ 16 \ 17 \ 20)
  svmat educ
  list yhat1 educ1 in 1/10
```

         +------------------+
         |    yhat1   educ1 |
         |------------------|
      1. | 10258.62       0 |
      2. | 10530.89       1 |
      3. | 13525.88      12 |
      4. | 17451.46      12 |
      5. | 19053.55      13 |
      6. | 23859.81      16 |
      7. | 29600.57      16 |
      8. | 33742.82      17 |
      9. | 46169.58      20 |
     10. |        .       . |
         +------------------+

Now, we can graph the adjusted means, called yhat1, by the levels of education, called educ1, as shown below.

```
  graph twoway line yhat1 educ1, xline(12 16)xtitle(Education) ytitle(Adjusted mean)
```

Although the process of creating this graph is more complicated, **the benefit is that it will automatically be updated if the dataset changes. This can be a little more work in the short run but saves us time in the long run.**
