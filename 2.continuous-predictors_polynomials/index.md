# Chapter3 ：Continuous predictors: Polynomials


***This Chapter focuses on how to interpret the coefficient of a continuous predictor in a linear regression model.***

## 1 Quadratic (squared) terms

A quadratic (**squared**) term can be used to model curved relationships, accounting for **one bend** in the relationship between the predictor and outcome.

Let’s relate the nature of the curve to the regression equation predicting income (realrinc) from age (age), shown below.

$$ \widehat{realrinc}=-30000 + 2500age + (-25age^{2}) $$ 
![Quadratic regression with quadratic coefficient of -25](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/ushape.png)

For an inverted U-shaped curve, we can compute the value of x that corresponds to the **maximum value of y**. 

If we call the linear coefficient of age and the quadratic coefficient of age b2 , then the value of age that yields the maximum income is given by -b1/(2×b2). Substituting 2,500 for b1 and -25 for b2 yields a value of 50;therefore,the maximum income occurs when someone is 50 years old. 


> The degree of curvature (U-shape) would increase as the quadratic coefficient increases. The linear coefficient would still determine the slope when the predictor is at zero. Because the curve is **U-shaped**, the minimum of that curve would be represented by **-b1/(2×b2)**, where is the linear coefficient and is the quadratic coefficient.

### 1.1 Examples
``` code
      use gss_ivrm.dta
      keep if (age<=80)
```
Before fitting a quadratic model relating income to age, let’s assess the shape of the relationship between these variables using a **locally weighted smoother**. The lowess command is used to create the variable yhatlowess, which is the **predicted value based on the locally weighted regression predicting income from age**. 

Checking for nonlinearity

``` code
      lowess realrinc age,nograph gen(yhatlowess)
      line yhatlowess age,sort
```
![ Lowess-smoothed values of income by age](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/lowess.png)

Let’s fit a model with a quadratic (squared) term to account for the bend in the relationship between age and income. 
We do this using the interaction operator ## (as shown below), which creates a term that multiplies age by age. Specifying c.age indicates to Stata that age should be treated as a **continuous variable** (instead of treating it as factor variable).
#### 1.1.1 Interpreting the relationship between age and income
``` code
  reg realrinc c.age##c.age female,vce(robust)
```
>Note! Why not square age?
>>You might be wondering why we do not instead use the generate command to create a new variable, say, age2, that contains the age squared. If we do this, the results of the regress command would be the same, but this would **confuse the margins command**. The margins command would think that age and age2 **are two completely different variables**.
   
     Linear regression                              Number of obs     =     32,100
                                                    F(3, 32096)       =    1252.67
                                                    Prob > F          =     0.0000
                                                    R-squared         =     0.1089
                                                    Root MSE          =      25407

------------------------------------------------------------------------------
                 |               Robust
        realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
     
             age |   2412.339     58.058    41.55   0.000     2298.544    2526.135
                 |
     c.age#c.age |    -24.202      0.696   -34.78   0.000      -25.566     -22.838
                 |
          female |  -1.24e+04    280.475   -44.28   0.000    -1.30e+04   -1.19e+04
           _cons |  -2.57e+04   1038.412   -24.73   0.000    -2.77e+04   -2.36e+04
------------------------------------------------------------------------------

We can use the formula -b1/(2×b2) to compute the age at which income is at its maximum. This yields -2412.34/(2×-24.20) , which equals 49.84. The adjusted mean of income is highest for those who are 49.84 years old.


Suppose we wanted to estimate the age slope for any given value of age. We can do so using the margins command combined with the **dydx(age)** option.Below,we obtain the age slope for ages ranging from 30 to 70 in 10-year increments.

``` code
  margins,at(age=(30(10)70))dydx(age)vsquish
```
    Average marginal effects                                Number of obs = 32,100
    Model VCE: Robust

    Expression: Linear prediction, predict()
    dy/dx wrt:  age
    1._at: age = 30
    2._at: age = 40
    3._at: age = 50
    4._at: age = 60
    5._at: age = 70

------------------------------------------------------------------------------
                  |            Delta-method
                  |      dy/dx   std. err.      t    P>|t|     [95% conf. interval]
                                                                              
     age          |
              _at |
               1  |    960.222     18.274    52.55   0.000      924.404     996.039
               2  |    476.183      9.823    48.47   0.000      456.928     495.437
               3  |     -7.856     15.700    -0.50   0.617      -38.628      22.915
               4  |   -491.896     27.998   -17.57   0.000     -546.772    -437.019
               5  |   -975.935     41.336   -23.61   0.000    -1056.955    -894.915
------------------------------------------------------------------------------

#### 1.1.2 Graphing adjusted means with confidence intervals

The marginsplot command can be used to create a graph of the adjusted means with a shaded region showing the confidence interval for the adjusted means. 
We can then run the marginsplot command, adding the recast(line) and recastci(rarea) options to display the adjusted means as a line and the confidence interval as a shaded region. The resulting graph is shown in below.

``` code
     margins,at(age=(18(1)80))  
     marginsplot,recast(line) recastci(rarea)
```
![Adjusted means with shaded confidence region from quadratic regression](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/quaduatic.png)


## 2 Cubic (third power)terms

Let’s examine the relationship between the year of birth and number of children a woman has using the gss_ivrm.dta dataset, focusing on women aged 45 to 55 born between 1920 and 1960.

```code
      use gss_ivrm.dta
      keep if (age>=45 & age<=55) & (yrborn>=1920 & yrborn<=1960) & female==1
      lowess children yrborn,gen(yhatlowess) nograph
      graph twoway line yhatlowess yrborn,sort
```

![ Lowess-smoothed fit of number of children by year of birth](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/cubic.png)

Below,we fit a model predicting children from yrborn fit using a cubic term.The model specifies c.yrborn##c.yrborn##c.yrborn, which includes the cubic term for yrborn, the quadratic term for yrborn, and the linear term for yrborn.

``` code
    reg children c.yrborn##c.yrborn##c.yrborn,noci
```
note: c.yrborn#c.yrborn#c.yrborn omitted because of collinearity.

      Source |       SS           df       MS      Number of obs   =     5,049
                                                   F(2, 5046)      =    193.19
       Model |  1163.13728         2   581.56864   Prob > F        =    0.0000
    Residual |   15189.893     5,046  3.01028399   R-squared       =    0.0711
                                                   Adj R-squared   =    0.0708
       Total |  16353.0303     5,048   3.2395068   Root MSE        =     1.735

-------------------------------------------------------------------
                       children | Coefficient  Std. err.      t    P>|t|
                                                                 
                         yrborn |      1.431      0.816     1.75   0.079
                                |
              c.yrborn#c.yrborn |     -0.000      0.000    -1.81   0.071
                                |
     c.yrborn#c.yrborn#c.yrborn |      0.000  (omitted)
                                |
                          _cons |  -1344.881    791.474    -1.70   0.089
-------------------------------------------------------------------

There was a problem running this model. There is a note saying that the cubic term was omitted because of **collinearity.** This is a common problem when entering cubic terms, which can be solved by **centering yrborn.**

The dataset includes a variable called yrborn40, which is the variable yrborn centered around the year 1940 (that is, **1940 is subtracted from each value of yrborn**). Let’s try fitting the above model again but instead using the variable yrborn40.

``` code
      reg children c.yrborn40##c.yrborn40##c.yrborn40,noci
```
.   reg children c.yrborn40##c.yrborn40##c.yrborn40,noci

      Source |       SS           df       MS      Number of obs   =     5,049
                                                   F(3, 5045)      =    166.84
       Model |  1475.96126         3  491.987087   Prob > F        =    0.0000
    Residual |   14877.069     5,045  2.94887394   R-squared       =    0.0903
                                                   Adj R-squared   =    0.0897
       Total |  16353.0303     5,048   3.2395068   Root MSE        =    1.7172

-------------------------------------------------------------------------
                             children | Coefficient  Std. err.      t    P>|t|
                                                                         
                             yrborn40 |     -0.091      0.005   -17.34   0.000
                                      |
                c.yrborn40#c.yrborn40 |     -0.001      0.000    -3.66   0.000
                                      |
     c.yrborn40#c.yrborn40#c.yrborn40 |      0.000      0.000    10.30   0.000
                                      |
                                _cons |      2.741      0.037    73.67   0.000
-------------------------------------------------------------------------

>Note! Including linear, quadratic, and cubic terms
>>When fitting this kind of a cubic model, you might find that the linear or quadratic coefficients are not significant. For the sake of parsimony, you might be tempted to omit those variables because they are not significant. However, **it is essential that these terms be included in the model (even if not significant) to preserve the interpretation of the cubic term.**



Specifying at(yrborn40=(-20(1)20)) yields predicted means for years of birth ranging from 1920 to 1960 in one-year increments.
``` code
      reg children c.yrborn40##c.yrborn40##c.yrborn40,noci
```
The marginsplot command is then used to create the graph shown in figure. The recast(line) and recastci(rarea) options display the predicted means as a **solid line** and **the confidence interval** as a shaded area. 

![Predicted means from cubic regression with shaded confidence](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/cubic2.png)

You can further explore how the yrborn40 slope varies as a function of year of birth by specifying multiple values within the at() option, as shown below.


``` code
      margins,at(yrborn40=(-20(5)20)) dydx(yrborn40) vsquish
```
     Conditional marginal effects                             Number of obs = 5,049
     Model VCE: OLS

     Expression: Linear prediction, predict()
     dy/dx wrt:  yrborn40
     1._at: yrborn40 = -20
     2._at: yrborn40 = -15
     3._at: yrborn40 = -10
     4._at: yrborn40 =  -5
     5._at: yrborn40 =   0
     6._at: yrborn40 =   5
     7._at: yrborn40 =  10
     8._at: yrborn40 =  15
     9._at: yrborn40 =  20

------------------------------------------------------------------------------
                  |            Delta-method
                  |      dy/dx   std. err.      t    P>|t|     [95% conf. interval]

     yrborn40     |
              _at |
               1  |      0.191      0.023     8.34   0.000        0.146       0.236
               2  |      0.074      0.012     6.02   0.000        0.050       0.097
               3  |     -0.013      0.005    -2.35   0.019       -0.023      -0.002
               4  |     -0.067      0.004   -15.70   0.000       -0.076      -0.059
               5  |     -0.091      0.005   -17.34   0.000       -0.101      -0.081
               6  |     -0.083      0.005   -18.12   0.000       -0.092      -0.074
               7  |     -0.044      0.004    -9.82   0.000       -0.052      -0.035
               8  |      0.027      0.010     2.70   0.007        0.007       0.047
               9  |      0.129      0.020     6.50   0.000        0.090       0.168
------------------------------------------------------------------------------

### 2.1 Fractional polynomial regression

Fractional polynomial regression is more **flexible** by considering other kinds of power terms as well, including negative powers and fractional powers.
The **fp** prefix automates the process of selecting the best fitting fractional
polynomial model.It fits a variety of polynomial terms (and combinations of polynomial terms) and shows you the best fitting model. The default set of power terms the fp prefix will try includes -2,-1,-0.5, 0, 0.5, 1, 2, and 3 (where 0 indicates that the natural log of the predictor is used). 

Let’s look at this canvas of shapes, beginning with the negative powers (-2,-1,and -0.5). 
These models take the form of y=b*Xpower, where could be positive or negative, and could be -2,-1, or -0.5. 

Figure  shows some possible shapes of these models, showing the powers -2,-1,-0.5 and in columns 1, 2, and 3.

![ Fractional polynomials, powers =-2,-1, and -0.5(columns) for b= 0.3(top row) and b=-0.3 (bottom row)](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/fraction.png)

The graphs in the first row (with the positive coefficients) are all typified by a steep descent and then reaching a floor. **The more strongly negative the power term, the stronger the descent.** The second row is a vertical mirror image of the first. When the coefficient is negative, there is a sharp ascent and then a ceiling is reached. **The more negative power terms are associated with a sharper ascent.**

The shapes of the relationship between and for the powers 1, 2, and 3 are shown in figure
![Fractional polynomials, powers = 1, 2, and 3 (columns) for b=0.3 (top row) and b=-0.3 (bottom row)](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/fraction2.png)

The first column shows a linear relationship between and . The second and third columns show the second and third power, with the top row showing a U-shaped bend and the bottom row showing an inverted U-shaped bend. **The higher power terms are associated with a more rapid change in the outcome for a unit change in the predictor.**

the fp prefix (by default) will not only fit each of these eight powers alone, but also includes **all two-way combinations of these powers.**

![Combined fractional polynomials](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/fraction3.png)

The right panel shows the formula combining these two curves, Note how this curve combines the rapid rise of the left panel with the gradual inverted U-shape of the middle panel. As you can imagine, being able to combine two different fractional polynomials can yield a flexible set of possible curve shapes for modeling your data.

#### 2.1.1 Example using fractional polynomial regression
First, we will run a regression predicting educ fromage, treating age as a categorical variable
``` code
  use gss_ivrm.dta
  keep if (age<=80)
  reg educ i.age
  predict yhatmean
  graph twoway line yhatmean age,sort xlabel(20(5)80)
```
![Average education at each level of age](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/yhatmean.png)

Note how there is a curvilinear aspect to the relationship between age and education, suggesting that we might try including a quadratic term for educ. 

``` code
 reg educ c.age##c.age
```


      Source |       SS           df       MS      Number of obs   =    53,070
                                                   F(2, 53067)     =   1616.60
       Model |  29981.1031         2  14990.5515   Prob > F        =    0.0000
    Residual |  492083.501    53,067  9.27287205   R-squared       =    0.0574
                                                   Adj R-squared   =    0.0574
       Total |  522064.604    53,069  9.83746828   Root MSE        =    3.0451

------------------------------------------------------------------------------
             educ | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
                                                                             
              age |      0.138      0.005    28.22   0.000        0.129       0.148
                  |
      c.age#c.age |     -0.002      0.000   -36.02   0.000       -0.002      -0.002
                  |
            _cons |     10.763      0.107   100.14   0.000       10.553      10.974
------------------------------------------------------------------------------

Let’s use the predict command to compute the fitted values from the quadratic model, naming the variable yhatq. Then, let’s graph the fitted values from the quadratic model and the mean of education by age, as shown below. 
``` code
  predict yhatq
  graph twoway line yhatmean yhatq age,sort xlabel(20(5)80)
```
![Fitted values of quadratic model compared with observed means](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/yhatq.png)

Note how the quadratic fit line yields predicted values that are **too high in the younger years** and **too low in the older years.** Another way of putting this is that the quadratic line does not account for the rapid rise in education in the late teens and early 20s, nor does it account for the slow decline of education in later years.

**A fractional polynomial model, with its increased flexibility, could provide a more appropriate fit.** We fit such a model **by adding the fp prefix to the regress command**, as shown below.

``` code
  fp <age>:reg educ <age>
```
    Fractional polynomial comparisons:
------------------------------------------------------------------------------
             | Test              Residual   Deviance
         age |   df   Deviance   std. dev.      diff.       P   Powers
                                                                              
     omitted |    4  271933.68      3.136   3507.042    0.000               
      linear |    3  270076.53      3.082   1649.885    0.000   1           
       m = 1 |    2  269297.58      3.060    870.937    0.000   3           
       m = 2 |    0  268426.64      3.035      0.000       --   -2 1        
------------------------------------------------------------------------------
       Note: Test df is degrees of freedom, and P = P > F is sig. level for tests
       comparing models vs. model with m = 2 based on deviance difference,
       F(df, 53065).

      Source |       SS           df       MS      Number of obs   =    53,070
                                                   F(2, 53067)     =   1812.66
       Model |  33384.5311         2  16692.2655   Prob > F        =    0.0000
    Residual |  488680.073    53,067   9.2087375   R-squared       =    0.0639
                                                   Adj R-squared   =    0.0639
       Total |  522064.604    53,069  9.83746828   Root MSE        =    3.0346

------------------------------------------------------------------------------
        educ | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
                                                                              
       age_1 |  -1655.009     40.430   -40.94   0.000    -1734.252   -1575.766
       age_2 |     -0.090      0.002   -57.93   0.000       -0.093      -0.087
       _cons |     18.090      0.098   183.82   0.000       17.897      18.282
------------------------------------------------------------------------------
The fp prefix tried 44 different models involving the polynomials -2,-1,-0.5, 0, 0.5, 1, 2, and 3 selected alone and in pairs (where 0 indicates that the natural log of the predictor). **The fp results show that it selected the model using the powers -2 and 1 as the best fitting combination of polynomials.**

The variable age_1 reflects the term associated with age , and the coefficient for this variable is negative. As X increases, there is a sharp rise in Y followed by a plateau.

The variable age_2 reflects the term associated with age, and the coefficient for this variable is negative. This shows a linear decrease in Y with increasing values of X.

 Let’s generate the predicted values based on this model and see if they conform to these expectations. The predict command is used to create the predicted values from the fractional polynomial model, calling the variable yhatfp.

``` code
  predict yhatfp
  graph twoway line yhatmean yhatq yhatfp age,sort xlabel(20(5)85) ///
  legend(label(1 "mean at each age") label(2 "Quadratic fit")) ///
  legend(label(3 "Fractional polynomial fit"))
```
![Fitted values of quadratic and fractional polynomial models compared with observed means](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/fraction4.png)


Suppose that we wanted to compute the adjusted mean for multiple ages, say, for the ages 20 to 80 in 10-year increments. We can use the forvalues command to loop across such a range of ages, as shown below. Within the loop, the local command is used to compute the local macros age1 and age2, using the powers associated with age_1 and age_2. The margins command is then used to compute the adjusted mean based on the values of age1 and age2. The matrix and local commands extract the adjusted mean, resulting in the local macro named adjmean. Finally, the display command displays the adjusted mean.


``` code
  forvalues age = 20(10)80 {
  local age1 = `age'^-2
  local age2 = `age'
  margins, at(age_1=`age1' age_2=`age2')
  matrix am = r(b)
  local adjmean = am[1,1]
  display "Adjsted mean for age = `age' is " `adjmean'  
  }
```

* Adjsted mean for age = 20 is 12.147077
* Adjsted mean for age = 30 is 13.543231
* Adjsted mean for age = 40 is 13.44528 
* Adjsted mean for age = 50 is 12.915188
* Adjsted mean for age = 60 is 12.214998
* Adjsted mean for age = 70 is 11.434496
* Adjsted mean for age = 80 is 10.611189

This forvalues loop helps to **automate the process of computing the adjusted means as a function of age.**

To avoid the process of typing the values into a dataset, we can automate the process of saving the adjusted means to a dataset using the postfile and post commands, as illustrated below.


``` code
  *New: open up a postfile named -adjmeans- that will contain the variables age and yhat
  postutil clear  //Close any open postfiles
  postfile adjmeans age yhat using adjmeans,replace
  forvalues age = 18(1)80 {
     local age1 = `age'^-2
     local age2 = `age'
     margins, at(age_1=`age1' age_2=`age2')
     matrix mm = r(b)
     local yhat = mm[1,1]
  *New save local macros `age' and `yhat' to the postfile named adjmeans post adjmeans (`age')(`yhat')
  *New close the postfile named -adjmeans-
     post adjmeans (`age') (`yhat')
  }

  postclose adjmeans
  use "D:\1\Stata\ado\personal\adjmeans.dta",clear
  list in 1/10
  graph twoway line yhat age
```
                      
             | age       yhat |
       1.    |  18   11.35704 |
       2.    |  19   11.79033 |
       3.    |  20   12.14708 |
       4.    |  21    12.4415 |
       5.    |  22   12.68467 |
       6.    |  23    12.8853 |
       7.    |  24   13.05033 |
       8.    |  25   13.18535 |
       9.    |  26   13.29488 |
       10.   |  27   13.38263 |
 

![Adjusted means from fractional polynomial model, ages 18 to 80 incrementing by 1](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/fraction5.png)


### 2.2 Main effects with polynomial terms

**The meaning of main effects changes when polynomial terms are included in the model.** In fact, the inclusion of polynomial terms can substantially change the coefficient for the main effect when compared with the main effect–only model. 

``` code
  use gss_ivrm.dta
  keep if (age<=80)
  reg realrinc age female,vce(robust) 
```
      Linear regression                               Number of obs     =     32,100
                                                      F(2, 32097)       =    1170.51
                                                      Prob > F          =     0.0000
                                                      R-squared         =     0.0787
                                                      Root MSE          =      25833
------------------------------------------------------------------------------
             |               Robust
    realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
                                                                              
         age |     320.07      10.70    29.93   0.000       299.10      341.03
      female |  -12373.25     285.03   -43.41   0.000    -12931.91   -11814.59
       _cons |   15106.17     403.04    37.48   0.000     14316.19    15896.16
------------------------------------------------------------------------------
However, there is a problem. As we saw in section 1.1, the relationship between income and age is **not linear.** As we did in that section, let’s add a quadratic term for age, as shown below.

``` code
  reg realrinc c.age##c.age female,vce(robust)
```
     Linear regression                               Number of obs     =     32,100
                                                     F(3, 32096)       =    1252.67
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.1089
                                                     Root MSE          =      25407

------------------------------------------------------------------------------
                 |               Robust
        realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
                                                                              
             age |    2412.34      58.06    41.55   0.000      2298.54     2526.13
                 |
     c.age#c.age |     -24.20       0.70   -34.78   0.000       -25.57      -22.84
                 |
          female |  -12419.24     280.47   -44.28   0.000    -12968.98   -11869.49
           _cons |  -25679.77    1038.41   -24.73   0.000    -27715.10   -23644.44
------------------------------------------------------------------------------

The quadratic term is significant, but we might suddenly become concerned that the main effect of age has skyrocketed from 320.07 in the linear model to 2,412.34 in the quadratic model. Why did the main effect change so much? Did we do something wrong? **The key is that the term “main effect” is really a misnomer, because we expect this term to describe the general trend of the relationship between income and age.**

This value is meaningless for two reasons. First, **nobody has income when they are zero years old.** Second, **this term no longer reflects the general trend.** As we saw in section 1.1, the age slope changes for every level of age, so there is no such thing as a measure of general trend in this kind of model.
