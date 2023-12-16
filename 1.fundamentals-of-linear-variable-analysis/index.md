# Continuous predictors:Linear


***This Chapter focuses on how to interpret the coefficient of a continuous predictor in a linear regression model.***

## 1 Simple linear regression

**This section illustrates the use of a continuous predictor for predicting a continuous outcome using ordinary least-squares regression.**

> Terminology: Continuous and categorical variables
>> When I use the termcontinuous variable, I am referring to a variable that is measured on an
interval or ratio scale.
By contrast, when I speak of a categorical variable,I am referring to either a nominal variable or an ordinal/interval/ratio variable that we wish to treat as though it were a nominal variable. 


Let’s run a simple regression model in which we predict the education of the respondent from the education of the respondent’s father.

```Simple regression code
    regress educ paeduc
```

The result as shown below


      Source |       SS           df       MS      Number of obs   =       696
                                                   F(1, 694)       =    228.14
       Model |  1649.70181         1  1649.70181   Prob > F        =    0.0000
    Residual |  5018.43038       694  7.23116769   R-squared       =    0.2474
                                                   Adj R-squared   =    0.2463
       Total |  6668.13218       695   9.5944348   Root MSE        =    2.6891

------------------------------------------------------------------------------
        educ | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]

      paeduc |      0.359      0.024    15.10   0.000        0.313       0.406
       _cons |      9.740      0.286    34.08   0.000        9.179      10.301
------------------------------------------------------------------------------

The regression equation can be written as shown below

$$ \widehat{educ}=9.74 + 0.36paeduc $$   

The intercept is the predicted mean of the respondent’s education when the father’s education is **0**. For
**every one-year increase in the education of the father**, we would predict that the education of the respondent increases by 0.36 years.


### 1.1 Computing predicted means using the margins command

Suppose we wanted to compute the predicted mean of education for the respondent, assuming separately that the father had 8, 12, or 16 years of education.

```Simple regression code
    margins,at (paeduc=(8 12 16)) vsquish
```
> Note: The **vsquish** option
>> The vsquish option vertically **squishes the output by omitting extra blank lines.**

    Adjusted predictions                                       Number of obs = 696
    Model VCE: OLS

    Expression: Linear prediction, predict()
    1._at: paeduc =  8
    2._at: paeduc = 12
    3._at: paeduc = 16

             |            Delta-method
             |     Margin   std. err.      t    P>|t|     [95% conf. interval]
 
         _at |
          1  |     12.616      0.128    98.93   0.000       12.365      12.866
          2  |     14.053      0.104   135.64   0.000       13.850      14.257
          3  |     15.491      0.153   101.42   0.000       15.191      15.791
------------------------------------------------------------------------------


Sometimes, we might want to compute the predicted means given a range of values for a predictor. For example, we might want to compute the predicted means when father’s education is 0, 4, 8, 12, 16, and 20. 


```Simple regression code
      margins,at(paeduc=(0(4)20)) vsquish
```
Rather than typing all of these values, we can **specify 0(4)20**, which tells Stata that **we mean 0 to 20 in 4-unit increments**. 


    Adjusted predictions                                       Number of obs = 696
    Model VCE: OLS

    Expression: Linear prediction, predict()
    1._at: paeduc =  0
    2._at: paeduc =  4
    3._at: paeduc =  8
    4._at: paeduc = 12
    5._at: paeduc = 16
    6._at: paeduc = 20

------------------------------------------------------------------------------
             |            Delta-method
             |     Margin   std. err.      t    P>|t|     [95% conf. interval]

         _at |
          1  |      9.740      0.286    34.08   0.000        9.179      10.301
          2  |     11.178      0.200    55.95   0.000       10.786      11.570
          3  |     12.616      0.128    98.93   0.000       12.365      12.866
          4  |     14.053      0.104   135.64   0.000       13.850      14.257
          5  |     15.491      0.153   101.42   0.000       15.191      15.791
          6  |     16.929      0.232    72.82   0.000       16.472      17.385
------------------------------------------------------------------------------



### 1.2 Graphing predicted means using the marginsplot command
  
We can use the **marginsplot** command to create a graph showing the predicted means and confidence intervals based on the most recent margins command. 


```Simple regression code
    marginsplot
```
>Note：The margins and marginsplot commands work together as a team.

![marginsplot](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/marginsplot.png)

Let’s now create a graph that shows the **fitted line** with a **shaded confidence interval**.

```Simple regression code
    margins,at(paeduc=(0(1)20))
    marginsplot,recast(line) recastci(rarea)
```


The **recast()** option specifies that the fitted line should be displayed as a line graph (suppressing the markers). The **recastci()** option specifies that the confidence interval should be displayed as an rarea graph, displaying a shaded area for the confidence region. 



![marginsplot](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/marginsplot2.png)

## 2 Multiple regression
Let’s now turn to a multiple regression model that predicts the respondent’s education from the father’s
education (paeduc), the mother’s education (maeduc), and the age of the respondent (age). 

```Simple regression code
    reg educ paeduc maeduc age
```

      Source |       SS           df       MS      Number of obs   =       650
                                                   F(3, 646)       =     92.93
       Model |  1822.26082         3  607.420272   Prob > F        =    0.0000
    Residual |  4222.37918       646  6.53619069   R-squared       =    0.3015
                                                   Adj R-squared   =    0.2982
       Total |     6044.64       649  9.31377504   Root MSE        =    2.5566

------------------------------------------------------------------------------
        educ | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]

      paeduc |      0.258      0.033     7.82   0.000        0.193       0.323
      maeduc |      0.208      0.038     5.48   0.000        0.133       0.282
         age |      0.034      0.007     5.28   0.000        0.022       0.047
       _cons |      6.962      0.511    13.63   0.000        5.959       7.965
------------------------------------------------------------------------------

The equation as shown below

$$ \widehat{educ}=6.86 + 0.26paeduc + 0.21maeduc + 0.03age $$ 

The coefficients from this multiple regression model reflect the association between each predictor and the outcome after adjusting for all the other predictors.

### 2.1 Computing adjusted means using the margins command
The margins command allows us to hold more than one variable constant at a time. In the example below, we compute the adjusted means when the father’s education equals 8, 12, and 16, while holding the mother’s education constant at 14.

```Simple regression code
    reg educ paeduc maeduc age
```

     Predictive margins                                         Number of obs = 650
     Model VCE: OLS

     Expression: Linear prediction, predict()
     1._at: paeduc =  8
            maeduc = 14
     2._at: paeduc = 12
            maeduc = 14
     3._at: paeduc = 16
            maeduc = 14

------------------------------------------------------------------------------
             |            Delta-method
             |     Margin   std. err.      t    P>|t|     [95% conf. interval]
         _at |
          1  |     13.544      0.211    64.18   0.000       13.130      13.958
          2  |     14.576      0.128   113.76   0.000       14.325      14.828
          3  |     15.609      0.152   102.52   0.000       15.310      15.908
------------------------------------------------------------------------------


> Terminology: Adjusted means
>>For example, we can say that the predicted mean, given the father has 8 years of education, is 13.544 after adjusting for all other predictors. We could also call this an adjusted mean. 
>>>The term adjusted mean implies after adjusting for all other predictors in the model. When using nonlinear models (such as a logistic regression model), we will use a more general term, such as **predictive margin**.



## 3 Checking for nonlinearity graphically
This section illustrates **graphical approaches** for checking for **nonlinearity** in the relationship between a predictor and outcome variable. These approaches include 
1)  **examining scatterplots of the predictor and outcome.**
2)  **examining residual-versusfitted plots.**
3)  **creating plots based on locally weighted smoothers.**
4)  **plotting the mean of the outcome for each level of the predictor.**


### 3.1 Using scatterplots to check for nonlinearity

Let’s look at a scatterplot of the size of the engine (displacement) by length of the car (length) with a line showing the linear fit, as shown in figure

```Simple regression code
    use autosubset
    graph twoway (scatter displacement length) (lfit displacement length),ytitle("Engine displacement(cu in.) ") legend (off) 
```

![Scatterplot of engine displacement by car length with linear fit line](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/marginsplot3.png)


The relationship between these two variables looks fairly linear, but the addition of the linear fit line helps us to see the nonlinearity. Note how for short cars (whenlength is below 160) the fit line underpredicts and for longer cars (when length is above 210) the fit line also underpredicts.


Using a scatterplot like this can be a simple means of looking at the linearity of the simple relationship between a predictor and outcome variable. However, this **does not account for other predictors that you might want to include in a model**. To this end, let’s next look at how we can use the **residuals** for checking linearity


### 3.2 Checking for nonlinearity using residuals
For example, let’s run a regression predicting displacement from length, trunk, and weight, as shown below

```Simple regression code
      reg displacement length trunk weight
```
We can then look at the residuals versus the fitted values, as shown in figure
``` code
     rvfplot
```
![Residual-versus-fitted plot of engine displacement by car length, trunk size, and weight](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/residual.png)


Note the **U-shaped pattern** of the residuals. This pattern suggests that the relationship between the predictors and outcome is **not linear**.

There are many excellent resources that illustrate Stata’s regression diagnostic tools, including the manual entry for [R] regress postestimation. You can also see the help entries for avplot, rvfplot, and rvpplot.

### 3.3 Checking for nonlinearity using locally weighted smoother
With larger datasets, it can be harder to visualize nonlinearity using scatterplots or residual-versus-fitted plots. 
Suppose we want to determine the nature of the relationship between the year that the respondent was born (yrborn) and education level (educ). 

```Simple regression code
      use gss_ivrm.dta
      scatter educ yrborn,msymbol(oh)
```

![Scatterplot of education by year of birth](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/scatter.png)

It is hard to discern the nature of the relationship between year of birth and education using this scatterplot. With so many observations, the scatterplot is saturated with data points creating one big blotch that tells us little about the shape of the relationship between the predictor and outcome

 The **lowess command** below creates a graph showing the **locally weighted regression** of education on year of birth, as shown in figure

```Simple regression code
    lowess educ yrborn,msymbol(p)
```

![Locally weighted regression of education by year of birth](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/scatter2.png)

The lowess graph suggests that there is nonlinearity in the relationship between year of birth and education. Education increases with year of birth until the 1950s, at which point the smoothed education values level out and then start to decline. The graph produced by the lowess command is much more informative than the scatterplot alone.

### 3.4 Graphing outcome mean at each level of predictor

Another way to visualize the relationship between the predictor and outcome is to **create a graph showing the mean of the outcome at each level of the predictor**. 

Using the example predicting education from year of birth means creating a graph of the average of education at each level of year of birth. 

Although the variable yrborn can assume many values (from 1883 to 1990), the variable is composed of discrete integers with reasonably many observations (usually more than 100) for each value. In such a case, we can explore the nature of the relationship between the predictor and outcome by **creating a graph of the mean of the outcome variable (education level) for each level of the predictor (year of birth)**. This kind of graph imposes no structure on the shape of the relationship between year of birth and education and allows us to **observe the nature of the relationship between the predictor and the outcome**.

One simple way to create such a graph is to fit a regression model predicting the outcome **treating the predictor variable as a factor variable**. 
Following that, the margins command is used to **obtain the predicted mean of the outcome for each level of the predictor**. In the regress command below, **specifying i.yrborn indicates that the variable yrborn should be treated as a factor variable**. The following margins command computes the predicted mean of the outcome (education) at each year of birth


```Simple regression code
      reg educ i.yrborn
      margins yrborn
      marginsplot
```
![Mean education by year of birth](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/marginsplot4.png)

The predicted means vary erratically for the years before 1900 because of the **few observations per year during those years**. For these years, the confidence intervals are much wider compared with later years, reflecting **greater uncertainty of the estimates because of fewer observations**.


If all the years had such few observations, then the entire graph **might be dominated by wild swings in the means and show little about the nature of the relationship between the predictor and outcome.**

## 4 Checking for nonlinearity analytically
This section shows how to check for nonlinearity using analytic approaches, including adding power terms and using factor variables.

### 4.1 Adding power terms
Another way to check for nonlinearity of a continuous variable is to add power terms (for example, quadratic or cubic). 

Using the example with education as a function of year of birth.
Let’s introduce a quadratic term (in addition to the linear term) by adding c.yrborn#c.yrborn to the model. This introduces a quadratic effect that would account for **one bend** in the line relating year of birth to education. We would expect the quadratic term to be significant based on the graphs we saw in figures 
```Simple regression code
      use gss_ivrm.dta
```

      Source |       SS           df       MS      Number of obs   =    54,745
                                                   F(2, 54742)     =   3567.49
       Model |  63778.2779         2  31889.1389   Prob > F        =    0.0000
    Residual |  489328.985    54,742  8.93882184   R-squared       =    0.1153
                                                   Adj R-squared   =    0.1153
       Total |  553107.263    54,744   10.103523   Root MSE        =    2.9898

-----------------------------------------------------------------
                  educ | Coefficient  Std. err.      t    P>|t|

                yrborn |      4.218      0.102    41.46   0.000
                       |
     c.yrborn#c.yrborn |     -0.001      0.000   -41.00   0.000
                       |
                 _cons |  -4129.000     98.816   -41.78   0.000
-----------------------------------------------------------------

>Note! Using the noci option for **clearer output**
>>When models include interactions,those labels can get rather wide, and Stata is forced to display those labels across multiple lines, which can make the output confusing and hard to read. 
>>This omits the display of the confidence intervals, which makes enough room to display the label for every term in the model in a single line. **but!,the confidence interval are also important for our research.**

The quadratic term is significant in this model.Furthermore, the R2 for this model increased to 0.1153 compared with 0.0881 for the linear model. This provides analytic support for including a quadratic term for year of birth when predicting education.

A cubic term would imply that the line fitting year of birth and education has a tendency to have two bends in it.c.yrborn##c.yrborn##c.yrborn as a predictor is a shorthand for including the linear, quadratic, and cubic terms for year of birth (yrborn).

```code
    reg educ c.yrborn##c.yrborn##c.yrborn,noci
```
note: c.yrborn#c.yrborn#c.yrborn omitted because of **collinearity**.

      Source |       SS           df       MS      Number of obs   =       926
                                                   F(2, 923)       =     11.47
       Model |  219.843988         2  109.921994   Prob > F        =    0.0000
    Residual |  8846.14737       923    9.584125   R-squared       =    0.0242
                                                   Adj R-squared   =    0.0221
       Total |  9065.99136       925  9.80107174   Root MSE        =    3.0958

-------------------------------------------------------------------
                           educ | Coefficient  Std. err.      t    P>|t|
 
                         yrborn |      6.159      1.287     4.79   0.000
                                |
              c.yrborn#c.yrborn |     -0.002      0.000    -4.79   0.000
                                |
     c.yrborn#c.yrborn#c.yrborn |      0.000  (omitted)
                                |
                          _cons |  -6015.618   1259.800    -4.78   0.000
-------------------------------------------------------------------

There was a problem running this model. There is a note saying that the cubic term was omitted because of **collinearity**. This is a common problem when entering cubic terms, which can be solved by **centering yrborn**. The dataset includes a variable called yrborn40, which is **the variable yrborn centered around the year 1940 (that is, 1940 is subtracted from each value of yrborn).** 

```code
       reg educ c.yrborn40##c.yrborn40##c.yrborn40,noci
```
      Source |       SS           df       MS      Number of obs   =       926
                                                   F(3, 922)       =      7.72
       Model |  222.175819         3  74.0586064   Prob > F        =    0.0000
    Residual |  8843.81554       922  9.59199083   R-squared       =    0.0245
                                                   Adj R-squared   =    0.0213
       Total |  9065.99136       925  9.80107174   Root MSE        =    3.0971

-------------------------------------------------------------------------
                                 educ | Coefficient  Std. err.      t    P>|t|
 
                             yrborn40 |      0.058      0.014     4.20   0.000
                                      |
                c.yrborn40#c.yrborn40 |     -0.002      0.001    -2.14   0.033
                                      |
     c.yrborn40#c.yrborn40#c.yrborn40 |      0.000      0.000     0.49   0.622
                                      |
                                _cons |     13.427      0.201    66.87   0.000
-------------------------------------------------------------------------
The results show, as expected, that the cubic term is significant. However, this dataset has many observations, so the model has the statistical power to detect very small effects. Note how the is 0.1162 for the cubic model compared with 0.1153 for the quadratic model. This is a **trivial increase**, suggesting that the cubic trend is not really an important term to include in this model.
### 4.2 Using factor variables

The strategy of using factor variables to check for nonlinearity makes sense **only when you have a relatively limited number of levels of the predictor that are coded as whole numbers.**

let’s perform an analysis to **detect any kind of nonlinearity** in the relationship between decade of age and health status.

```Simple regression code
      use gss_ivrm.dta
      reg health c.agedec i.agedec
```
note: 8.agedec omitted because of collinearity.

      Source |       SS           df       MS      Number of obs   =    40,984
                                                   F(7, 40976)     =    451.59
       Model |  2111.10121         7  301.585887   Prob > F        =    0.0000
    Residual |  27364.9308    40,976  .667828261   R-squared       =    0.0716
                                                   Adj R-squared   =    0.0715
       Total |   29476.032    40,983  .719225826   Root MSE        =    .81721

------------------------------------------------------------------------------
      health | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
 
      agedec |     -0.098      0.005   -18.46   0.000       -0.108      -0.087
             |
      agedec |
        20s  |      0.110      0.028     3.97   0.000        0.055       0.164
        30s  |      0.165      0.024     6.85   0.000        0.118       0.212
        40s  |      0.135      0.022     6.24   0.000        0.093       0.178
        50s  |      0.065      0.021     3.15   0.002        0.025       0.106
        60s  |     -0.006      0.021    -0.28   0.783       -0.047       0.036
        70s  |     -0.038      0.024    -1.60   0.110       -0.084       0.009
        80s  |      0.000  (omitted)
             |
       _cons |      3.320      0.035    95.81   0.000        3.253       3.388
------------------------------------------------------------------------------
This unconventional-looking model divides the relationship between the age decade and the outcome into two pieces: 
1)  the linear relationship, which is accounted for by c.agedec
2)  any remaining nonlinear components, which are explained by the indicator variables specified by i.agedec. 

Let’s now use the **testparm** command to perform a test of the indicator variables, giving us an **overall test of the nonlinearity** in the relationship between age decade and health status.

```Simple regression code
     testparm i.agedec  
```
     (1)  2.agedec = 0
     (2)  3.agedec = 0
     (3)  4.agedec = 0
     (4)  5.agedec = 0
     (5)  6.agedec = 0
     (6)  7.agedec = 0

       F(  6, 40976) =   20.61
            Prob > F =    0.0000

This general strategy tells us that there is nonlinearity between the age decade and health status but does not pinpoint the exact nature of the nonlinearity. 

Let’s try another strategy that will **pinpoint the nature of the nonlinearity.** And use the contrast command with the p. contrast operator to obtain a detailed breakdown of possible nonlinear trends in the relationship between the age decade and health status

```Simple regression code
     reg health i.agedec
     contrast p.agedec
```
Contrasts of marginal linear predictions

Margins: asbalanced

------------------------------------------------
                      |         df           F        P>F

          agedec      |
         (linear)     |          1     1187.58     0.0000
         (quadratic)  |          1       26.65     0.0000
         (cubic)      |          1       52.99     0.0000
         (quartic)    |          1        1.18     0.2778
         (quintic)    |          1        1.00     0.3179
         (sextic)     |          1        0.15     0.6959
         (septic)     |          1        0.02     0.8748
          Joint       |          7      451.59     0.0000
                      |
          Denominator |      40976
------------------------------------------------

--------------------------------------------------------------
                 |    Contrast    Std. err.     [95% conf. interval]

        agedec   |
     (linear)    |     -0.260      0.008        -0.275      -0.245
     (quadratic) |     -0.038      0.007        -0.053      -0.024
     (cubic)     |      0.047      0.006         0.034       0.059
     (quartic)   |      0.006      0.005        -0.005       0.016
     (quintic)   |     -0.004      0.004        -0.013       0.004
     (sextic)    |      0.001      0.004        -0.006       0.009
     (septic)    |     -0.001      0.004        -0.008       0.006
--------------------------------------------------------------





