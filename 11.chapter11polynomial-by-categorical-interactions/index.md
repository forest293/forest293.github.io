# Chapter11 ：Polynomial by categorical interactions


This chapter illustrates models that involve a continuous variable modeled using a polynomial term interacted with a categorical variable. This chapter covers two types of polynomial terms: quadratic and cubic.

## 1 Quadratic by categorical interactions

### 1.1 Quadratic by two-level categorical

Let’s now use the GSS dataset to fit a model predicting income from age (modeled using a quadratic term), whether one is a college graduate, as well as the interaction of these variables. 

``` 
  use gss_ivrm.dta
  keep if age>=22 & age<=80
  lowess realrinc age,generate(yhat_lowess) nograph
  graph twoway line yhat_lowess age,sort
``` 

Let’s begin by looking at the relationship between age and income using a lowess smoother. The lowess command is used to create the variable yhat_lowess that contains the lowess smoothed values of realrinc. The graph command creates a graph showing the lowess smoothed values across age. 

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.3.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong> Lowess smoothed values of income by age</strong></figcaption>
</figure>

Let’s create this same kind of graph but separating people based on the variable cograd, which is coded: 0 = noncollege graduate and 1 = college graduate. The lowess command is issued twice, each with an if specification. 

``` 
  lowess realrinc age if cograd == 0,generate(yhat_lowess0)nograph
  lowess realrinc age if cograd == 1,generate(yhat_lowess1)nograph
  
  graph twoway line yhat_lowess0 yhat_lowess1 age,sort
``` 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.4.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong> Lowess smoothed values of income predicted from age by college graduation status</strong></figcaption>
</figure>

Based on this visual inspection of the data, a regression model predicting realrinc from age and cograd would **not only need to account for the quadratic trend in age, but also the difference in the quadratic trend in age for college graduates versus noncollege graduates.**

Let’s fit a model that includes an intercept, age, and age squared for noncollege graduates, and a separate intercept, age, and age squared for college graduates. 

Specifying ibn.cograd with the noconstant option yields separate intercept estimates by college graduation status. Specifying ibn.cograd#c.age yields separate age estimates by college graduation status, and ibn.cograd#c.age#c.age yields separate age#age estimates by college graduation status.

``` 
  reg realrinc ibn.cograd ibn.cograd#c.age ibn.cograd#c.age#c.age female,noconstant vce(robust) 
``` 



        Linear regression                               Number of obs     =     30,576
                                                        F(7, 30569)       =    5127.56
                                                        Prob > F          =     0.0000
                                                        R-squared         =     0.5088
                                                        Root MSE          =      24865
        
        ------------------------------------------------------------------------------------
                           |               Robust
                  realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
        -------------------+----------------------------------------------------------------
                    cograd |
              Not CO Grad  |  -11096.99    1107.78   -10.02   0.000    -13268.29    -8925.69
                  CO Grad  |  -52730.93    3688.05   -14.30   0.000    -59959.66   -45502.21
                           |
              cograd#c.age |
              Not CO Grad  |    1594.87      57.26    27.85   0.000      1482.64     1707.10
                  CO Grad  |    3941.46     194.37    20.28   0.000      3560.49     4322.43
                           |
        cograd#c.age#c.age |
              Not CO Grad  |     -16.20       0.66   -24.48   0.000       -17.50      -14.90
                  CO Grad  |     -37.81       2.30   -16.46   0.000       -42.31      -33.30
                           |
                    female |  -12457.11     278.57   -44.72   0.000    -13003.12   -11911.11
        ------------------------------------------------------------------------------------
        

>Note! Model shortcut
>>Stata expands the expression ibn.cograd#(c.age c.age#c.age) to become ibn.cograd#c.age ibn.cograd#c.age#c.age, yielding the same model shown previously.

$$Non-college-grad: \widehat{realrinc}=-11096.99 + 1594.87age + - 16.20age^2 + - 12457.11female $$    
$$College-grad: \widehat{realrinc}=-52730.93 + 3941.46age + -37.81age^2 + -12457.11female  $$    

Let’s visualize the impact of the differences in these quadratic coefficients by graphing the adjusted means as a function of age and college graduation status (that is, cograd), adjusting for gender. 

``` 
  margins cograd, nopvalues at(age=(22(1)80))
  marginsplot,noci recast(line) scheme(simono)
``` 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.5.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Fitted values from quadratic by two-level categorical model</strong></figcaption>
</figure>

We can ask whether the degree of curvature between college graduates and non-college graduates is significantly different. The contrast command below tests whether the quadratic term for non-college graduates is equal to the quadratic term for college graduates. 

``` 
  contrast cograd#c.age#c.age
``` 

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------------
                           |         df           F        P>F
        -------------------+----------------------------------
        cograd#c.age#c.age |          1       81.75     0.0000
                           |
               Denominator |      30569
        ------------------------------------------------------
        
Let’s now see how the margins command can be used to estimate adjusted means from this model. The margins command below **computes the adjusted mean of income for a 30-year-old who did not graduate college, adjusting for gender.** 

``` 
  margins,nopvalues at(cograd=0 age=30)
``` 

        Predictive margins                                      Number of obs = 30,576
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: cograd =  0
            age    = 30
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
               _cons |   16044.57     112.39      15824.29    16264.86
        --------------------------------------------------------------
        
The margins command below estimates the adjusted mean for 40-year-olds, separately by the levels of cograd.
``` 
  margins cograd,nopvalues at(age=40)
``` 

        Predictive margins                                      Number of obs = 30,576
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: age = 40
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
              cograd |
        Not CO Grad  |   20653.96     175.10      20310.75    20997.17
            CO Grad  |   38311.44     568.59      37196.98    39425.90
        --------------------------------------------------------------
        
The at() option allows us to specify multiple ages at once, as shown in the example below. This example computes the adjusted means for ages 30 to 70 (in 10year increments) separately for each level of cograd.

``` 
  margins cograd,nopvalues at(age=(30(10)70)) vsquish
``` 

        Predictive margins                                      Number of obs = 30,576
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: age = 30
        2._at: age = 40
        3._at: age = 50
        4._at: age = 60
        5._at: age = 70
        
        ----------------------------------------------------------------
                       |            Delta-method
                       |     Margin   std. err.     [95% conf. interval]
        ---------------+------------------------------------------------
            _at#cograd |
        1#Not CO Grad  |   16044.57     112.39      15824.29    16264.86
            1#CO Grad  |   25361.47     340.99      24693.12    26029.82
        2#Not CO Grad  |   20653.96     175.10      20310.75    20997.17
            2#CO Grad  |   38311.44     568.59      37196.98    39425.90
        3#Not CO Grad  |   22023.54     206.43      21618.92    22428.16
            3#CO Grad  |   43700.08     638.66      42448.27    44951.89
        4#Not CO Grad  |   20153.30     269.91      19624.26    20682.33
            4#CO Grad  |   41527.39     877.70      39807.05    43247.72
        5#Not CO Grad  |   15043.25     500.82      14061.62    16024.87
            5#CO Grad  |   31793.36    1754.30      28354.86    35231.86
        ----------------------------------------------------------------


The margins command can be used to compare the adjusted means of those who did and those who did not graduate college at specified ages. 

``` 
  margins r.cograd,at(age=30) contrast(nowald pveffects)  
``` 
compare the adjusted means of those who did and those did not graduate at specified ages.

        Contrasts of predictive margins                         Number of obs = 30,576
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: age = 30
        
        ------------------------------------------------------------------
                                  |            Delta-method
                                  |   Contrast   std. err.      t    P>|t|
        --------------------------+---------------------------------------
                           cograd |
        (CO Grad vs Not CO Grad)  |    9316.90     358.48    25.99   0.000
        ------------------------------------------------------------------

Compares college graduates with noncollege graduates at 30 to 70 years of age in 10-year increments. The difference is significant at each of the specified ages.

``` 
  margins r.cograd,at(age=(30(10)70)) contrast(nowald pveffects) vsquish
``` 

        Contrasts of predictive margins                         Number of obs = 30,576
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: age = 30
        2._at: age = 40
        3._at: age = 50
        4._at: age = 60
        5._at: age = 70
        
        --------------------------------------------------------------------
                                    |            Delta-method
                                    |   Contrast   std. err.      t    P>|t|
        ----------------------------+---------------------------------------
                         cograd@_at |
        (CO Grad vs Not CO Grad) 1  |    9316.90     358.48    25.99   0.000
        (CO Grad vs Not CO Grad) 2  |   17657.48     594.36    29.71   0.000
        (CO Grad vs Not CO Grad) 3  |   21676.54     671.46    32.28   0.000
        (CO Grad vs Not CO Grad) 4  |   21374.09     919.46    23.25   0.000
        (CO Grad vs Not CO Grad) 5  |   16750.11    1825.29     9.18   0.000
        --------------------------------------------------------------------


Let’s use the margins command to compute the difference between college graduates and high school graduates for 22- to 80-year-olds in one-year increments so we can then graph the differences using the marginsplot command. 


``` 
  margins r.cograd,at(age=(22(1)80)) contrast(nowald effects)
  marginsplot,yline(0)recast(line)recastci(rarea)
``` 

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.6.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Contrasts on college graduation status by age</strong></figcaption>
</figure>

You might notice that the width of the confidence intervals grows with increasing age. This is because of both smaller sample sizes and increased variability in incomes for those who are older.

### 1.2 Quadratic by three-level categorical

Let’s begin our exploration by using the lowess command to generate variables that contain the smoothed relationship between realrinc and age, separately for each of the three levels of educ3. 

``` 
  use gss_ivrm.dta
  keep if age>=22 & age<=80
  lowess realrinc age if educ3 == 1,generate(yhat_lowess1) nograph
  lowess realrinc age if educ3 == 2,generate(yhat_lowess2) nograph  
  lowess realrinc age if educ3 == 3,generate(yhat_lowess3) nograph  
  graph twoway line yhat_lowess1 yhat_lowess2 yhat_lowess3 age,sort legend(order(1 "Non-HS grad" 2 "HS grad" 3 "CO grad"))
``` 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.7.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Lowess smoothed values of income by age, separated by three levels of education</strong></figcaption>
</figure>

Let’s now fit a model that includes a quadratic term for age as well as an interaction of educ3 and the quadratic term for age. 

``` 
  reg realrinc ibn.educ3 ibn.educ3#(c.age c.age#c.age) female,noconstant vce(robust)
``` 

        Linear regression                               Number of obs     =     30,576
                                                        F(10, 30566)      =    3691.50
                                                        Prob > F          =     0.0000
                                                        R-squared         =     0.5129
                                                        Root MSE          =      24761
        
        -----------------------------------------------------------------------------------
                          |               Robust
                 realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
        ------------------+----------------------------------------------------------------
                    educ3 |
                  not hs  |   -8609.97    1907.08    -4.51   0.000    -12347.92    -4872.01
                      HS  |  -11115.20    1375.93    -8.08   0.000    -13812.07    -8418.32
                    Coll  |  -52556.01    3687.82   -14.25   0.000    -59784.28   -45327.74
                          |
              educ3#c.age |
                  not hs  |    1245.11      93.00    13.39   0.000      1062.82     1427.40
                      HS  |    1625.58      72.45    22.44   0.000      1483.59     1767.58
                    Coll  |    3940.66     194.33    20.28   0.000      3559.76     4321.57
                          |
        educ3#c.age#c.age |
                  not hs  |     -12.46       1.00   -12.40   0.000       -14.43      -10.49
                      HS  |     -16.04       0.87   -18.54   0.000       -17.74      -14.34
                    Coll  |     -37.81       2.30   -16.46   0.000       -42.31      -33.31
                          |
                   female |  -12750.67     278.86   -45.72   0.000    -13297.24   -12204.10
        -----------------------------------------------------------------------------------

The regression equation is written in this fashion as shown below.

$$NonHis-grad:\widehat{realrinc}=-8609.67 + 1245.11age + - 12.46age^2 + - 12750.67female$$    
$$His-grad:\widehat{realrinc}=-11115.2 + 1625.59age + -16.04age^2 + -12750.67female$$ 
$$College-grad:\widehat{realrinc}=-52556.01 + 3940.66age + -37.81age^2 + -12750.67female$$ 

To help interpret the regression coefficients, let’s use the margins and marginsplot commands to visualize the adjusted means as a function of age and educ3. 

``` 
  margins educ3,at(age=(22(1)80))
  marginsplot,noci recast(line) scheme(simono)
```     


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.8.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Fitted values from age (quadratic) by education level</strong></figcaption>
</figure>

The contrast command can be used to compare the quadratic coefficients among the educational groups. The contrast command below tests the equality of the quadratic coefficient for all three levels of educ3.


``` 
  contrast educ3#c.age#c.age 
```  

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -----------------------------------------------------
                          |         df           F        P>F
        ------------------+----------------------------------
        educ3#c.age#c.age |          2       51.24     0.0000
                          |
              Denominator |      30566
        -----------------------------------------------------

This test is significant, indicating that the quadratic term for age is not equal
across all three groups. 

the a. contrast operator is used below to compare the quadratic terms for **adjacent** education groups.(Specific compare)


``` 
  contrast a.educ3#c.age#c.age,nowald pveffects 
```  

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------------
                          |   Contrast   Std. err.      t    P>|t|
        ------------------+---------------------------------------
        educ3#c.age#c.age |
          (not hs vs HS)  |       3.58       1.33     2.70   0.007
            (HS vs Coll)  |      21.77       2.45     8.87   0.000
        ----------------------------------------------------------

Let’s now see how to use the margins command to **compute adjusted means.**

The margins command below computes the adjusted mean holding age constant at
30, separately for each education group.


``` 
  margins educ3,nopvalues at(age=30)
```  

        Predictive margins                                      Number of obs = 30,576
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: age = 30
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
               educ3 |
             not hs  |   11260.07     201.45      10865.22    11654.91
                 HS  |   16946.40     129.01      16693.54    17199.26
               Coll  |   25366.86     341.21      24698.06    26035.65
        --------------------------------------------------------------

Let’s compare adjacent levels of educ3 when age is held constant at 30. 

``` 
  margins ar.educ3,at(age=30) contrast(nowald pveffects) 
```  

        Contrasts of predictive margins                         Number of obs = 30,576
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: age = 30
        
        --------------------------------------------------------
                        |            Delta-method
                        |   Contrast   std. err.      t    P>|t|
        ----------------+---------------------------------------
                  educ3 |
        (HS vs not hs)  |    5686.33     240.61    23.63   0.000
          (Coll vs HS)  |    8420.46     363.26    23.18   0.000
        --------------------------------------------------------


Let’s repeat the above contrasts at each age level ranging from 22 to 80 using the margins command. We can then use the marginsplot command to graph each of the contrasts, as shown below. 

Two graphs are created: one for the contrast of high school graduates with non–high school graduates (group 2 versus 1) and the other for the contrast of college graduates with high school graduates (group 3 versus 2)

**when the confidence interval excludes zero, the contrast is significant at the 5% level.**

``` 
  margins ar.educ3,at(age=(22(1)80))
  marginsplot,bydimension(educ3) yline(0) recast(line) recastci(rarea)
```  

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.9.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Contrasts on education by age, with confidence intervals</strong></figcaption>
</figure>

>Note
>>This command incorporates the recast(line) option to display each line without marker symbols and the scheme(s1mono) option to display the graph using the s1mono scheme, which displays each line using a different pattern.

>>Recall that the vce(robust) option was included when fitting the regression model, which accommodates heterogeneity of variance (that is, heteroskedasticity).


## 2 Cubic by categorical interactions

This section describes models that involve interactions of a categorical variable with a continuous variable where the continuous variable is fit using a cubic polynomial term. 

**A cubic by categorical interaction allows the groups formed by the categorical variable to differ in cubic trend.**

In that example, we saw a cubic relationship between year of birth and number of children. Suppose that we divided women into two groups: those who graduated college and those who did not graduate college. Those who graduated college might show a different kind of cubic trend across years of birth compared with non-college graduates.

``` 
  use gss_ivrm.dta
  keep if (age>=45 & age <=55) & (yrborn>=1920 & yrborn<=1960) & female==1
```  

Let’s begin by visualizing the nature of the relationship between year of birth and number of children separately for college graduates and non-college graduates. We can do this using a lowess smoothed regression relating year of birth to number of children. 

``` 
  lowess children yrborn if cograd==0,generate(yhatlowess0) nograph
  lowess children yrborn if cograd==1,generate(yhatlowess1) nograph  
  graph twoway line yhatlowess0 yhatlowess1 yrborn,sort
``` 

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.10.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong> Lowess smoothed values of number of children by year of birth, separated by college graduation status</strong></figcaption>
</figure>

The graph in figure suggests that the relationship between year of birth and number of children may show a cubic trend. Furthermore, the cubic trend may differ based on whether the woman graduated college, suggesting an interaction of the cubic trend for year of birth and whether the woman graduated college.

Let’s fit a model using a separate slope and separate intercept strategy that allows us to compare the cubic trend in year of birth for college graduates with non-college graduates. 

Such a model is fit using the regress command below. The first term in the model is ibn.cograd. When specified in combination with noconstant option, the model fits separate intercepts by college graduation status. 

The model also includes ibn.cograd interacted with the linear term for year of birth, the quadratic term for year of birth, and the cubic term for year of birth. This yields separate estimates of these terms by college graduation status.

``` 
  reg children ibn.cograd ibn.cograd#(c.yrborn40 c.yrborn40#c.yrborn40 c.yrborn40#c.yrborn40#c.yrborn40) i.race,noconstant noci
``` 
>Note：the centered variable, yrborn40, is used to represent year of birth to reduce collinearity. 


              Source |       SS           df       MS      Number of obs   =     5,037
        -------------+----------------------------------   F(10, 5027)     =   1223.60
               Model |  34370.3789        10  3437.03789   Prob > F        =    0.0000
            Residual |  14120.6211     5,027  2.80895585   R-squared       =    0.7088
        -------------+----------------------------------   Adj R-squared   =    0.7082
               Total |       48491     5,037  9.62696049   Root MSE        =     1.676
        
        --------------------------------------------------------------------------------
                                       children | Coefficient  Std. err.      t    P>|t|
        ----------------------------------------+---------------------------------------
                                         cograd |
                                   Not CO Grad  |       2.80       0.04    65.64   0.000
                                       CO Grad  |       1.96       0.09    22.43   0.000
                                                |
                              cograd#c.yrborn40 |
                                   Not CO Grad  |      -0.09       0.01   -15.21   0.000
                                       CO Grad  |      -0.06       0.01    -5.35   0.000
                                                |
                   cograd#c.yrborn40#c.yrborn40 |
                                   Not CO Grad  |      -0.00       0.00    -4.07   0.000
                                       CO Grad  |       0.00       0.00     0.41   0.685
                                                |
        cograd#c.yrborn40#c.yrborn40#c.yrborn40 |
                                   Not CO Grad  |       0.00       0.00     9.42   0.000
                                       CO Grad  |       0.00       0.00     1.88   0.060
                                                |
                                           race |
                                         black  |       0.57       0.07     8.49   0.000
                                         other  |       0.67       0.13     5.26   0.000
        --------------------------------------------------------------------------------

The regression equation are devided to two part as shown below:

\begin{align}
Non-college-grad:\widehat{children} = -2.8 - 0.088yrborn40 + -0.00093yrborn40^2  \notag\\
\end{align}

\begin{align}
\quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad  \\
 +0.00021yrborn40^3 + 0.57black + 0.67other \notag \\
\end{align}


\begin{align}
College-grad:\widehat{children} = 1.96 + -0.063yrborn + 0.00024yrborn40^2   \notag\\
\end{align}

\begin{align}
\quad \quad \quad \quad  \quad \quad \quad \quad \quad \quad \quad \quad \quad  \\
+0.00009yrborn40^3 + 0.57black + 0.67other \notag \\
\end{align}


let’s create a graph of the fitted values using the margins and marginsplot commands. The margins command is used to calculate the adjusted means for years of birth from 1920 to 1960 in one-year increments, separately for college graduates and noncollege graduates. Then, the marginsplot command is used to graph these adjusted means

``` 
  margins cograd,at(yrborn40=(-20(1)20))
  marginsplot,noci recast(line)scheme(s1mono)
``` 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.11.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Fitted values of cubic by three-level categorical model</strong></figcaption>
</figure>

We can test the cubic trend for the college graduates compared with the noncollege graduates. The contrast command below compares the cubic trend for the college graduates with the noncollege graduates. 

``` 
  contrast cograd#c.yrborn40#c.yrborn40#c.yrborn40
``` 

The cubic trend is significantly different comparing noncollege graduates with college graduates.

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ---------------------------------------------------------------------------
                                                |         df           F        P>F
        ----------------------------------------+----------------------------------
        cograd#c.yrborn40#c.yrborn40#c.yrborn40 |          1        4.86     0.0276
                                                |
                                    Denominator |       5027
        ---------------------------------------------------------------------------


