# Chapter12 ：Piecewise by categorical interactions


This chapter illustrates how to fit models in which a continuous variable, fit in a piecewise manner, is interacted with a categorical variable. 

## 1 One knot and one jump
For example, education was modeled in a piecewise manner including one knot at 12 years of education. This knot signifies both a change of slope and change in intercept (jump). The model also includes gender as a categorical variable and estimates separate slopes and intercepts for each level of gender


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.12.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Piecewise regression with one knot and one jump, labeled with estimated slopes</strong></figcaption>
</figure>


Figure shows the adjusted means of income as a function of education with labels for each of the slopes. For men, the slope for non–high school graduates is labeled $\beta\\tiny M1$ and the slope for high school graduates is labeled $\beta\\tiny M2$ . For women, the slope for non–high school graduates is labeled $\beta\\tiny F1$ and the slope for high school graduates is labeled $\beta\\tiny F2$. The income jump at 12 years of education for men is labeled as $\alpha\\tiny M1$. The corresponding jump at 12 years of education for women is labeled as $\alpha\\tiny F1$. Note how an arrow head points to a sudden jump in income at 12 years of education

To fit this model, we will use separate slope and separate intercept coding with respect to the gender groups. This means that we will estimate separate intercept terms for men and women (this includes separate jumps,$\alpha\\tiny M1$ and $\alpha\\tiny F1$ ). It will also estimate separate slope terms for men (that is, $\beta\\tiny M1$ and $\beta\\tiny M2$ ) and separate slope terms for women (that is,$\beta\\tiny F1$ and $\beta\\tiny F2$).

We first use the mkspline command to create the variables ed1 and ed2 with a knot at 12 years of education.
the variables ed1 and ed2 will reflect the slopes of the individual line segments before and after the knot. 

``` 
  mkspline ed1 12 ed2 = educ 
``` 

We are now ready to run the piecewise regression model, shown in the regress command below. The regress command includes ibn.gender used in combination with the noconstant option to yield separate estimates of the intercept for each gender.

``` 
  reg realrinc ibn.gender ibn.gender#i.hsgrad ///
               ibn.gender#c.ed1 ibn.gender#c.ed2 i.race, vce(robust) noconstant noci
``` 

        Linear regression                               Number of obs     =      3,126
                                                        F(6, 3120)        =     614.01
                                                        Prob > F          =     0.0000
                                                        R-squared         =     0.5132
                                                        Root MSE          =      20860
        
        --------------------------------------------------------
                        |               Robust
               realrinc | Coefficient  std. err.      t    P>|t|
        ----------------+---------------------------------------
                 gender |
                Female  |   10502.44    1991.84     5.27   0.000
                        |
          gender#hsgrad |
        Female#HS Grad  |    2891.28     893.57     3.24   0.001
                        |
           gender#c.ed1 |
                Female  |      95.54     211.64     0.45   0.652
                        |
           gender#c.ed2 |
                Female  |    3260.42     247.08    13.20   0.000
                        |
                   race |
                 black  |   -1174.41     902.16    -1.30   0.193
                 other  |   -4732.20    1169.14    -4.05   0.000
        --------------------------------------------------------

>Note! Model shortcut
>>Stata expands the expression ibn.gender#(i.hsgrad c.ed1 c.ed2) to become ibn.gender#i.hsgrad ibn.gender#c.ed1 ibn.gender#c.ed2, yielding the same model we saw before.

1. The first two columns of the table repeat the name and estimate of the coefficient from the regress output. 
2. The third column shows the symbol used to represent the coefficient in figure above, providing a cross reference between the output of the regression model and figure.
3. The last column shows the symbolic name of the regression coefficient that we can use with the lincom command for making comparisons among the coefficients.

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.13.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Summary of piecewise regression results with one knot</strong></figcaption>
</figure>

Let’s begin by interpreting the change in intercept (jump) terms. The jump in the adjusted mean of income for men at 12 years of education is 3,382.01, and the corresponding jump for women is 1,748.10. Each of these jumps is statistically significant.

Let’s now interpret the slopes. For men, the educ slope is 86.71 for non–high school graduates and is 4,057.64 for high school graduates. For women, the slope is 241.61 for non–high school graduates and is 2,529.55 for high school graduates. Aside from the educ slope for male non–high school graduates (86.71), all of these slopes are significantly different from 0.

The following four sections will illustrate how to make a number of comparisons, showing how to:
1. **compare slopes between women and men**
2. **compare slopes across the levels of education, comparing high school graduates with non–high school graduates**
3. **compare the changes in slope between men and women due to graduating high school**
4. **compare the jump in income due to graduating high school between men and women
  
### 1.1 Comparing slopes across gender
Let’s begin by testing the equality of the educ slopes of women and men who have not graduated high school. In other words, is $\beta\\tiny F1$= $\beta\\tiny M1$ ? 

``` 
  contrast gender#c.ed1,pveffects nowald
``` 

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------------
                          |   Contrast   Std. err.      t    P>|t|
        ------------------+---------------------------------------
             gender#c.ed1 |
        (Female vs base)  |     154.90     179.32     0.86   0.388
        ----------------------------------------------------------

Let’s compare the educ slopes of men and women for those who graduated high school by testing whether $\beta\\tiny F2$ = $\beta\\tiny M2$ . 

``` 
  contrast gender#c.ed2,pveffects nowald
``` 
        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------------
                          |   Contrast   Std. err.      t    P>|t|
        ------------------+---------------------------------------
             gender#c.ed2 |
        (Female vs base)  |   -1528.09     187.09    -8.17   0.000
        ----------------------------------------------------------

The difference in the slopes (women versus men) is $-1,528.09$. For every year of education beyond the 12th year, the income for men increases by $1,528.09$ more than for women.

### 1.2 Comparing slopes across education

let’s examine the change in slope between high school graduates and non–high school graduates for men. is $\beta\\tiny M2$ = $\beta\\tiny M1$ ?


As shown in table , the symbolic names for these coefficients are 1.gender#ed2 and 1.gender#ed1. We can compare these coefficients using the **lincom command**, as shown below.


``` 
  lincom 1.gender#c.ed2 - 1.gender#c.ed1 
```   

         ( 1)  - 1bn.gender#c.ed1 + 1bn.gender#c.ed2 = 0
        
        ------------------------------------------------------------------------------
            realrinc | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 (1) |    3970.93     212.70    18.67   0.000      3554.04     4387.83
        ------------------------------------------------------------------------------

Men show a significantly higher slope after graduating high school than men who have not graduated high school. The difference (comparing high school graduates with non–high school graduates) is 3,970.93.

We can formulate the same kind of test for women, comparing the educ slope for high school graduates with that for non–high school graduates. 

``` 
  lincom 2.gender#c.ed2 - 2.gender#c.ed1
``` 
Is $\beta\\tiny F2$ = $\beta\\tiny F1$ ?

         ( 1)  - 2.gender#c.ed1 + 2.gender#c.ed2 = 0
        
        ------------------------------------------------------------------------------
            realrinc | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 (1) |    2287.94     147.26    15.54   0.000      1999.30     2576.58
        ------------------------------------------------------------------------------
        
### 1.3 Difference in differences of slopes（gain in slope）
The previous section showed that, for men, the educ slope after graduating high school minus the slope before graduating high school equals 3,970.93. Let’s call this the gain in slope due to graduating high school. For women, the gain in slope due to graduating high school is 2,287.94. 

We might ask if the gain in slope due to graduating high school differs by gender. The lincom command below tests the gain in slope for men compared with the gain in slope for women.

``` 
  lincom (1.gender#c.ed2 - 1.gender#c.ed1) - (2.gender#c.ed2 - 2.gender#c.ed1)
``` 

         ( 1)  - 1bn.gender#c.ed1 + 2.gender#c.ed1 + 1bn.gender#c.ed2 - 2.gender#c.ed2 = 0
        
        ------------------------------------------------------------------------------
            realrinc | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 (1) |    1682.99     259.29     6.49   0.000      1174.78     2191.20
        ------------------------------------------------------------------------------


### 1.4 Comparing changes in intercepts

Let’s now ask whether the jump in income at 12 years of education is equal for men and women. 
the income for men jumps by $3,382.01$ at 12 years of education, whereas the corresponding jump for women is $1,748.10$. Are these jumps equal? 

Is $\alpha\\tiny F1$ = $\alpha\\tiny M1$ ?

``` 
  contrast gender#hsgrad,pveffects nowald 
``` 

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------------------------------
                                            |   Contrast   Std. err.      t    P>|t|
        ------------------------------------+---------------------------------------
                              gender#hsgrad |
        (Female vs base) (HS Grad vs base)  |   -1633.91     766.98    -2.13   0.033
        ----------------------------------------------------------------------------

Some people might find that this test is more intuitive if performed using the following lincom command below. 


``` 
  lincom 2.gender#1.hsgrad - 1.gender#1.hsgrad
``` 

         ( 1)  - 1bn.gender#1.hsgrad + 2.gender#1.hsgrad = 0
        
        ------------------------------------------------------------------------------
            realrinc | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 (1) |   -1633.91     766.98    -2.13   0.033     -3137.23     -130.59
        ------------------------------------------------------------------------------

### 1.5 Computing and comparing adjusted means

Let’s now turn our attention to how we can compute adjusted means for this regression model. 

Before we can compute adjusted means with respect to education (that is, educ), we need to know how to express the level of education in terms of hsgrad, ed1, and ed2. 

``` 
  showcoding educ hsgrad ed1 ed2  //the command is not fit for stata18
``` 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.14.png" style="width:250px;height:350px;" alt="图片描述">
  <figcaption><strong> </strong></figcaption>
</figure>

Let’s now estimate the adjusted mean for a female (that is, gender=2) with 15 years of education, as shown below. To indicate 15 years of education, we specify that hsgrad equals 1, ed1 equals 12, and ed2 equals 3.


``` 
  margins,nopvalues at(gender=2 hsgrad=1 ed1=12 ed2=3)  
``` 

        Predictive margins                                      Number of obs = 32,183
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: gender =  2
            hsgrad =  1
            ed1    = 12
            ed2    =  3
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
               _cons |   18993.22     234.17      18534.24    19452.20
        --------------------------------------------------------------

We can estimate the adjusted mean of income for men and women with 15 years of education using the margins command below.

``` 
  margins gender,nopvalues at(hsgrad=1 ed1=12 ed2=3)  
``` 

        Predictive margins                                      Number of obs = 32,183
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: hsgrad =  1
            ed1    = 12
            ed2    =  3
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
              gender |
               Male  |   33171.77     351.09      32483.62    33859.93
             Female  |   18993.22     234.17      18534.24    19452.20
        --------------------------------------------------------------


Specifying the r. contrast operator indicates we want to use reference group comparisons, comparing the adjusted mean of income for women versus men.

``` 
  margins r.gender,at(hsgrad=1 ed1=12 ed2=3) contrast(nowald pveffects)
``` 
        Contrasts of predictive margins                         Number of obs = 32,183
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: hsgrad =  1
            ed1    = 12
            ed2    =  3
        
        ----------------------------------------------------------
                          |            Delta-method
                          |   Contrast   std. err.      t    P>|t|
        ------------------+---------------------------------------
                   gender |
        (Female vs Male)  |  -14178.55     422.06   -33.59   0.000
        ----------------------------------------------------------

### 1.6 Graphing adjusted means

Let’s graph the adjusted means as a function of education and gender. To make this graph, we need to compute the adjusted means separately for men and women with 0 years of education, 12 years of education (assuming the absence and presence of a high school diploma), and 20 years of education.

``` 
  margins gender,at(hsgrad=0 ed1=0 ed2=0) ///
                 at(hsgrad=0 ed1=12 ed2=0) ///
				         at(hsgrad=1 ed1=12 ed2=0) ///
				         at(hsgrad=1 ed1=12 ed2=8) nopvalues
``` 

        Predictive margins                                      Number of obs = 32,183
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: hsgrad =  0
               ed1    =  0
               ed2    =  0
        2._at: hsgrad =  0
               ed1    = 12
               ed2    =  0
        3._at: hsgrad =  1
               ed1    = 12
               ed2    =  0
        4._at: hsgrad =  1
               ed1    = 12
               ed2    =  8
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
          _at#gender |
             1#Male  |   16576.36    1384.62      13862.46    19290.26
           1#Female  |    6757.19     910.63       4972.32     8542.05
             2#Male  |   17616.85     590.78      16458.91    18774.80
           2#Female  |    9656.48     345.83       8978.65    10334.32
             3#Male  |   20998.86     297.50      20415.75    21581.96
           3#Female  |   11404.58     179.77      11052.22    11756.94
             4#Male  |   53459.97    1042.02      51417.56    55502.37
           4#Female  |   31640.96     761.69      30148.02    33133.90
        --------------------------------------------------------------
``` 
  preserve
  clear
  input educ yhatm yhatf

  0 16576.36 6757.187
  12 17616.85 9656.485
  12 20998.86 11404.58
  20 53459.97 31640.96
end

  graph twoway line yhatm yhatf educ,xline(12)legend(label(1 "Men")label(2 "Women")) ///
               xtitle(Education)ytitle(Adjusted mean)
``` 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.15.png" style="width:250px;height:350px;" alt="图片描述">
  <figcaption><strong>Fitted values from piecewise model with one knot and one jump at educ = 12</strong></figcaption>
</figure>

**We can automate the creation of this graph by extending the strategy**

1. First, we use the matrix command to create a matrix named yhat that contains the adjusted means computed by the margins command.

``` 
**First,rerun the -margins- command from above
  quietly margins gender, at(hsgrad=0 ed1=0 ed2=0) ///
                          at(hsgrad=0 ed1=12 ed2=0) ///
				                  at(hsgrad=1 ed1=12 ed2=0) ///
				                  at(hsgrad=1 ed1=12 ed2=8) 

** store the adjusted means in a matrix named -yhat-
  matrix yhat = r(b)'  //NOTE: We must use transpose here	
``` 

Now, we encounter a twist because the adjusted means are computed as a function two variables (education and gender).

Looking at the output of the margins command, let’s focus on the order in which **the adjusted means are displayed with respect to education and gender.** Eight adjusted means are shown, corresponding to the following levels of educ, hsgrad, and gender:

* educ=0, hsgrad=0, gender=1
* educ=0, hsgrad=0, gender=2
* educ=12, hsgrad=0, gender=1
* educ=12, hsgrad=0, gender=2 
* educ=12, hsgrad=1, gender=1
* educ=12, hsgrad=1, gender=2
* educ=20, hsgrad=1, gender=1
* educ=20, hsgrad=1, gender=2

The matrix command is used to create a matrix named educ that reflects the levels of education shown in the bulleted list above. The matrix command is used again, this time to create a matrix called gender that contains the levels of gender shown in the bulleted list above.

``` 
* store levels of education in a matrix named -educ-
  matrix educ = (0\0\12\12\12\12\20\20)
* store levels of gender in a matrix named -gender-
  matrix gender = (1\2\1\2\1\2\1\2)
``` 

The svmat command is then used three times, to save the matrices named yhat, educ, and gender to the current dataset. 

The list command is then used to show the variables yhat1, educ1, and gender1 for the first 10 observations of the dataset. 


``` 
  svmat yhat  //save the matrix -yhat- to the current dataset
  svmat educ  //save the matrix -educ- to the current dataset  
  svmat gender  //save the matrix -gender- to the current dataset
  list yhat1 educ1 gender1 in 1/10,sep(2)
``` 


             +----------------------------+
             |    yhat1   educ1   gender1 |
             |----------------------------|
          1. | 16576.36       0         1 |
          2. | 6757.188       0         2 |
             |----------------------------|
          3. | 17616.85      12         1 |
          4. | 9656.484      12         2 |
             |----------------------------|
          5. | 20998.86      12         1 |
          6. | 11404.58      12         2 |
             |----------------------------|
          7. | 53459.96      20         1 |
          8. | 31640.96      20         2 |
             |----------------------------|
          9. |        .       .         . |
         10. |        .       .         . |
             +----------------------------+


We are now ready to use the graph command to graph the adjusted means as afunction of education and gender. 

``` 
  graph twoway (line yhat1 educ1 if gender1==1) ///
               (line yhat1 educ1 if gender1==2), ///
			          xline(12) legend(label(1 "Men") label(2 "Women")) ///
			          xtitle(Education) ytitle(Adjusted mean)
``` 

## 2 Two knots and two jumps

**This section covers piecewise regression models with two knots and two jumps interacted with a categorical variable.**

For example,income was predicted from education modeled in a piecewise fashion with two knots and two jumps at 12 and 16 years of education.Furthermore, these piecewise terms were interacted with gender.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.16.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Piecewise regression with two knots and two jumps, labeled with estimated slopes</strong></figcaption>
</figure>

The jump at 12 years of education for men is labeled as $\alpha\\tiny M1$. The jump at 16 years of education for men is labeled as $\alpha\\tiny M2$. These corresponding jumps for women are labeled $\alpha\\tiny F1$and $\alpha\\tiny F2$.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.17.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Piecewise regression with two knots and two jumps, labeled with estimated intercepts</strong></figcaption>
</figure>

Let’s now illustrate how to perform this analysis. First, the mkspline command is used to create the variables ed1, ed2, and ed3 based on the knots that are specified at 12 and 16 years of education

``` 
  use gss_ivrm.dta
  mkspline ed1 12 ed2 16 ed3 = educ
``` 

The variables are now ready to run the piecewise regression model. The regress command, shown below, 

includes ibn.gender used in combination with the noconstant option. This models separate intercepts for men and women. The model also includes the interaction of ibn.gender with i.hsgrad and i.cograd. 

This models the jump in income due to graduating high school and graduating college separately for men and women. 

Finally, the model includes the interaction of ibn.gender with c.ed1, c.ed2, and c.ed3. This models the educ slope for non–high school graduates, high school graduates, and college graduates, estimating these slopes separately for men and women. The variable i.race is included as a covariate.


``` 
  reg realrinc ibn.gender ibn.gender#(hsgrad cograd c.ed1 c.ed2 c.ed3) i.race,vce(robust) noconstant noci
``` 

        Linear regression                               Number of obs     =     32,183
                                                        F(14, 32169)      =    2602.08
                                                        Prob > F          =     0.0000
                                                        R-squared         =     0.4885
                                                        Root MSE          =      24897
        
        --------------------------------------------------------
                        |               Robust
               realrinc | Coefficient  std. err.      t    P>|t|
        ----------------+---------------------------------------
                 gender |
                  Male  |   17084.37    1389.70    12.29   0.000
                Female  |    7262.50     921.90     7.88   0.000
                        |
          gender#hsgrad |
          Male#HS Grad  |    4927.53     655.33     7.52   0.000
        Female#HS Grad  |    2400.89     372.21     6.45   0.000
                        |
          gender#cograd |
          Male#CO Grad  |    9230.72    1224.50     7.54   0.000
        Female#CO Grad  |    1615.19     789.62     2.05   0.041
                        |
           gender#c.ed1 |
                  Male  |      89.40     151.40     0.59   0.555
                Female  |     243.64      98.84     2.46   0.014
                        |
           gender#c.ed2 |
                  Male  |    1592.64     255.81     6.23   0.000
                Female  |    1724.74     182.70     9.44   0.000
                        |
           gender#c.ed3 |
                  Male  |    4279.36     521.83     8.20   0.000
                Female  |    3608.45     496.99     7.26   0.000
                        |
                   race |
                 black  |   -3361.24     246.84   -13.62   0.000
                 other  |   -1813.49     840.47    -2.16   0.031
        --------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.18.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Let’s now see how to form comparisons among these coefficients. Specifically, let’s learn how to

* compare slopes between men and women 
* compare slopes across the levels of education, comparing college graduates, high school graduates, and non–high school graduates
* compare changes in slope between men and women due to graduating high school and due to graduating college
* compare changes in intercept (the jumps in income due to graduating high school and college) by gender
* compare changes in intercept (the jumps in income due to graduating high school and college) across levels of education

### 2.1 Comparing slopes across gender (Cross Section)
Let’s begin by testing the equality of the slopes between women and men who have not graduated high school—testing whether $\beta\\tiny F1$= $\beta\\tiny M1$.

``` 
  contrast gender#c.ed1,pveffects nowald //slope between Men and Women (not-grad)
``` 

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------------
                          |   Contrast   Std. err.      t    P>|t|
        ------------------+---------------------------------------
             gender#c.ed1 |
        (Female vs base)  |     154.25     179.30     0.86   0.390
        ----------------------------------------------------------


the difference in the educ slope between women and men before graduating high school is 154.25. However, this difference is not statistically significant. In other words, prior to graduating high school, the educ slope is not significantly different for men and women.


``` 
  contrast gender#c.ed2,pveffects nowald  //slope between Men and Women (grad)
``` 

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------------
                          |   Contrast   Std. err.      t    P>|t|
        ------------------+---------------------------------------
             gender#c.ed2 |
        (Female vs base)  |     132.11     314.04     0.42   0.674
        ----------------------------------------------------------
        


``` 
  contrast gender#c.ed3,pveffects nowald   //slope between Men and Women (grad-col)  
``` 

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------------
                          |   Contrast   Std. err.      t    P>|t|
        ------------------+---------------------------------------
             gender#c.ed3 |
        (Female vs base)  |    -670.91     721.07    -0.93   0.352
        ----------------------------------------------------------

### 2.2 Comparing slopes across education

Let’s begin by comparing the slopes for high school graduates with non–high school graduates, starting with men. testing whether $\beta\\tiny M2$ = $\beta\\tiny M1$  . 

**comparing the slopes for high school graduates with non-high school graduates (Men).**
``` 
  lincom 1.gender#c.ed2 - 1.gender#c.ed1   
``` 
         ( 1)  - 1bn.gender#c.ed1 + 1bn.gender#c.ed2 = 0
        
        ------------------------------------------------------------------------------
            realrinc | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 (1) |    1503.24     297.13     5.06   0.000       920.85     2085.63
        ------------------------------------------------------------------------------

**comparing the slopes for high school graduates with non-high school graduates (Women).**
``` 
  lincom 2.gender#c.ed2 - 2.gender#c.ed1
``` 
         ( 1)  - 2.gender#c.ed1 + 2.gender#c.ed2 = 0
        
        ------------------------------------------------------------------------------
            realrinc | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 (1) |    1481.10     208.81     7.09   0.000      1071.82     1890.38
        ------------------------------------------------------------------------------

**Men : cograd vs. hsgrad**
``` 
  lincom 1.gender#c.ed3 - 1.gender#c.ed2
``` 
         ( 1)  - 1bn.gender#c.ed2 + 1bn.gender#c.ed3 = 0
        
        ------------------------------------------------------------------------------
            realrinc | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 (1) |    2686.72     581.27     4.62   0.000      1547.41     3826.03
        ------------------------------------------------------------------------------

**Women : cograd vs. hsgrad**

``` 
  lincom 2.gender#c.ed3 - 2.gender#c.ed2
``` 

         ( 1)  - 2.gender#c.ed2 + 2.gender#c.ed3 = 0
        
        ------------------------------------------------------------------------------
            realrinc | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 (1) |    1883.71     530.20     3.55   0.000       844.49     2922.93
        ------------------------------------------------------------------------------

### 2.3 Difference in differences of slopes

Let’s now test the gain in slope due to graduating high school for men compared with the gain in slope due to graduating high school for women, which is ($\beta\\tiny M2$ -  $\beta\\tiny M1$) -($\beta\\tiny F2$ -  $\beta\\tiny F1$) 

``` 
  lincom (1.gender#c.ed2 - 1.gender#c.ed1) - (2.gender#c.ed2 - 2.gender#c.ed1)
``` 
         ( 1)  - 1bn.gender#c.ed1 + 2.gender#c.ed1 + 1bn.gender#c.ed2 - 2.gender#c.ed2 = 0
        
        ------------------------------------------------------------------------------
            realrinc | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 (1) |      22.14     361.97     0.06   0.951      -687.33      731.61
        ------------------------------------------------------------------------------

``` 
  lincom (1.gender#c.ed3 - 1.gender#c.ed2) - (2.gender#c.ed3 - 2.gender#c.ed2)
``` 
         ( 1)  - 1bn.gender#c.ed2 + 2.gender#c.ed2 + 1bn.gender#c.ed3 - 2.gender#c.ed3 = 0
        
        ------------------------------------------------------------------------------
            realrinc | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 (1) |     803.01     786.64     1.02   0.307      -738.84     2344.87
        ------------------------------------------------------------------------------
        

### 2.4 Comparing changes in intercepts by gender
Let’s now ask whether the jump in income at 12 years of education is the same for men and women. 

Let’s test whether these jumps are equal (whether $\alpha\\tiny F1$= $\alpha\\tiny M1$ ).

``` 
  contrast gender#hsgrad,pveffects nowald 
``` 
        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------------------------------
                                            |   Contrast   Std. err.      t    P>|t|
        ------------------------------------+---------------------------------------
                              gender#hsgrad |
        (Female vs base) (HS Grad vs base)  |   -2526.64     752.53    -3.36   0.001
        ----------------------------------------------------------------------------

**test whether these jump are equal(αF2=αM2？)**
``` 
  contrast gender#cograd,pveffects nowald 
``` 

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------------------------------
                                            |   Contrast   Std. err.      t    P>|t|
        ------------------------------------+---------------------------------------
                              gender#cograd |
        (Female vs base) (CO Grad vs base)  |   -7615.52    1455.05    -5.23   0.000
        ----------------------------------------------------------------------------

### 2.5 Comparing changes in intercepts by education

We might ask whether the jump due to graduating college ($9,230.72$) is equal to the jump due to graduating high school ($4,927.53$). 

**whether the jump due to graduating college is equal to the jump due to graduating high school(Men)**

``` 
  lincom 1.gender#1.cograd - 1.gender#1.hsgrad
``` 

         ( 1)  - 1bn.gender#1.hsgrad + 1bn.gender#1.cograd = 0
        
        ------------------------------------------------------------------------------
            realrinc | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 (1) |    4303.19    1327.65     3.24   0.001      1700.93     6905.44
        ------------------------------------------------------------------------------

  
``` 
  lincom 2.gender#1.cograd - 2.gender#1.hsgrad
``` 
         ( 1)  - 2.gender#1.hsgrad + 2.gender#1.cograd = 0
        
        ------------------------------------------------------------------------------
            realrinc | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 (1) |    -785.70     831.22    -0.95   0.345     -2414.92      843.52
        ------------------------------------------------------------------------------

### 2.6 Computing and comparing adjusted means

Let’s now see how to compute adjusted means in the context of this model. To compute adjusted means with respect to education, we need to express education in terms of hsgrad, cograd, ed1, ed2, and ed3. 

``` 
  showcoding educ hsgrad cograd ed1 ed2 ed3
``` 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.19.png" style="width:350px;height:450px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

We can estimate the adjusted mean for men and women (separately) in one margins command. 

**hoding education constant at 15-years of education**

``` 
  margins gender,nopvalues at(hsgrad=1 cograd=0 ed1=12 ed2=3 ed3=0)
``` 

        Predictive margins                                      Number of obs = 32,183
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: hsgrad =  1
            cograd =  0
            ed1    = 12
            ed2    =  3
            ed3    =  0
        
        --------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
              gender |
               Male  |   27322.43     652.72      26043.07    28601.78
             Female  |   17221.21     488.53      16263.68    18178.75
        --------------------------------------------------------------


Let’s now use the margins command to compare the adjusted mean of income for women versus men among those with 15 years of education. Specifying the r. contrast operator indicates we want to use reference group comparisons. 

``` 
  margins r.gender,at(hsgrad=1 cograd=0 ed1=12 ed2=3 ed3=0)
``` 

        Contrasts of predictive margins                         Number of obs = 32,183
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: hsgrad =  1
            cograd =  0
            ed1    = 12
            ed2    =  3
            ed3    =  0
        
        ------------------------------------------------
                     |         df           F        P>F
        -------------+----------------------------------
              gender |          1      154.10     0.0000
                     |
         Denominator |      32169
        ------------------------------------------------
        
        -------------------------------------------------------------------
                          |            Delta-method
                          |   Contrast   std. err.     [95% conf. interval]
        ------------------+------------------------------------------------
                   gender |
        (Female vs Male)  |  -10101.21     813.72     -11696.14    -8506.28
        -------------------------------------------------------------------

at 15 years of education, the adjusted mean of income for men is $10,101.21$ higher than for women.

### 2.7 Graphing adjusted means

``` 
  margins gender,at(hsgrad=0 cograd=0 ed1=0 ed2=0 ed3=0) ///
                 at(hsgrad=0 cograd=0 ed1=12 ed2=0 ed3=0) ///
				         at(hsgrad=1 cograd=0 ed1=12 ed2=0 ed3=0) ///
				         at(hsgrad=1 cograd=0 ed1=12 ed2=4 ed3=0) ///
				         at(hsgrad=1 cograd=1 ed1=12 ed2=4 ed3=0) ///
				         at(hsgrad=1 cograd=1 ed1=12 ed2=4 ed3=4) 
``` 

        Predictive margins                                      Number of obs = 32,183
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: hsgrad =  0
               cograd =  0
               ed1    =  0
               ed2    =  0
               ed3    =  0
        2._at: hsgrad =  0
               cograd =  0
               ed1    = 12
               ed2    =  0
               ed3    =  0
        3._at: hsgrad =  1
               cograd =  0
               ed1    = 12
               ed2    =  0
               ed3    =  0
        4._at: hsgrad =  1
               cograd =  0
               ed1    = 12
               ed2    =  4
               ed3    =  0
        5._at: hsgrad =  1
               cograd =  1
               ed1    = 12
               ed2    =  4
               ed3    =  0
        6._at: hsgrad =  1
               cograd =  1
               ed1    = 12
               ed2    =  4
               ed3    =  4
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
          _at#gender |
             1#Male  |   16544.24    1384.77    11.95   0.000     13830.04    19258.45
           1#Female  |    6722.38     909.92     7.39   0.000      4938.90     8505.85
             2#Male  |   17616.99     590.89    29.81   0.000     16458.82    18775.15
           2#Female  |    9646.09     345.63    27.91   0.000      8968.64    10323.54
             3#Male  |   22544.52     282.66    79.76   0.000     21990.49    23098.54
           3#Female  |   12046.99     142.20    84.72   0.000     11768.27    12325.71
             4#Male  |   28915.06     896.20    32.26   0.000     27158.47    30671.66
           4#Female  |   18945.96     667.08    28.40   0.000     17638.46    20253.45
             5#Male  |   38145.78     830.91    45.91   0.000     36517.16    39774.40
           5#Female  |   20561.15     422.35    48.68   0.000     19733.33    21388.97
             6#Male  |   55263.21    1736.33    31.83   0.000     51859.94    58666.48
           6#Female  |   34994.95    1760.66    19.88   0.000     31543.99    38445.92
        ------------------------------------------------------------------------------

``` 
  preserve 
  clear
  input educ yhatm yhatf
   0 16544.24 6722.377
  12 17616.99 9646.094
  12 22544.52 12046.99
  16 28915.06 18945.96
  16 38145.78 20561.15
  20 55263.21 34994.95
  end

  graph twoway line yhatm yhatf educ,xlabel(0(4)20) xline(12 16)
  restore
``` 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.11.20.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Fitted values from piecewise model with two knots and two jumps</strong></figcaption>
</figure>

**Automate the creation of the graph**

``` 
  *First,rerun the -margins- command from above
  quietly margins gender,at(hsgrad=0 cograd=0 ed1=0 ed2=0 ed3=0) ///
                         at(hsgrad=0 cograd=0 ed1=12 ed2=0 ed3=0) ///
				                 at(hsgrad=1 cograd=0 ed1=12 ed2=0 ed3=0) ///
				                 at(hsgrad=1 cograd=0 ed1=12 ed2=4 ed3=0) ///
				                 at(hsgrad=1 cograd=1 ed1=12 ed2=4 ed3=0) ///
				                 at(hsgrad=1 cograd=1 ed1=12 ed2=4 ed3=4) ///
  
  *Store the adjusted means(from the -margins- command) in a matrix named -yhat-
  matrix yhat = r(b)' //note: we must transpose r(b) here
  
  *Store levels of education in a matrix named -educ-
  matrix educ = (0\0\12\12\12\12\16\16\16\16\20\20) 
  
  *Store levels of gender in a matrix named -gender-
  matrix gender = (1\2\1\2\1\2\1\2\1\2\1\2)
  
  svmat yhat  //save the matrix -yhat- to the current dataset
  svmat educ  //save the matrix -educ- to the current dataset 
  svmat gender //save the matrix -gender- to the current dataset
  
  graph twoway (line yhat1 educ1 if gender1==1) ///
               (line yhat1 educ1 if gender1==2), ///
               xline(12 16) legend(label(1 "Men") label(2 "Women")) ///
			         xtitle(Education) ytitle(Adjusted mean)
``` 

## 3 Comparing coding schemes

This chapter has focused on one coding scheme for fitting models that interact a categorical variable and a continuous variable fit in a piecewise manner.Depending on your research question, another coding scheme might be more useful. 
### 3.1 Coding scheme #1
This coding scheme will be called coding scheme #1. The noheader option is used in this and subsequent examples to save space.

``` 
  use gss_ivrm.dta
  mkspline ed1 12 ed2 = educ
  reg realrinc ibn.gender ibn.gender#(i.hsgrad c.ed1 c.ed2) i.race,vce(robust) noconstant noheader noci
``` 
        --------------------------------------------------------
                        |               Robust
               realrinc | Coefficient  std. err.      t    P>|t|
        ----------------+---------------------------------------
                 gender |
                  Male  |   17144.66    1389.51    12.34   0.000
                Female  |    7325.48     922.52     7.94   0.000
                        |
          gender#hsgrad |
          Male#HS Grad  |    3382.00     661.55     5.11   0.000
        Female#HS Grad  |    1748.10     389.36     4.49   0.000
                        |
           gender#c.ed1 |
                  Male  |      86.71     151.38     0.57   0.567
                Female  |     241.61      98.92     2.44   0.015
                        |
           gender#c.ed2 |
                  Male  |    4057.64     150.38    26.98   0.000
                Female  |    2529.55     110.43    22.91   0.000
                        |
                   race |
                 black  |   -3521.34     246.66   -14.28   0.000
                 other  |   -1946.16     839.73    -2.32   0.020
        --------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.12.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Intercept and slope coefficients from piecewise regression fit using coding scheme #1</strong></figcaption>
</figure>

### 3.2 Coding scheme #2
Let’s now fit a model using what I call coding scheme #2. This coding scheme is the same as coding scheme #1, except that the marginal option is used on the mkspline command.

``` 
  use gss_ivrm.dta
  mkspline ed1m 12 ed2m = educ,marginal
  reg realrinc ibn.gender ibn.gender#(i.hsgrad c.ed1m c.ed2m) i.race,vce(robust) noconstant noheader noci
``` 
        --------------------------------------------------------
                        |               Robust
               realrinc | Coefficient  std. err.      t    P>|t|
        ----------------+---------------------------------------
                 gender |
                  Male  |   17144.66    1389.51    12.34   0.000
                Female  |    7325.48     922.52     7.94   0.000
                        |
          gender#hsgrad |
          Male#HS Grad  |    3382.00     661.55     5.11   0.000
        Female#HS Grad  |    1748.10     389.36     4.49   0.000
                        |
          gender#c.ed1m |
                  Male  |      86.71     151.38     0.57   0.567
                Female  |     241.61      98.92     2.44   0.015
                        |
          gender#c.ed2m |
                  Male  |    3970.93     212.70    18.67   0.000
                Female  |    2287.94     147.26    15.54   0.000
                        |
                   race |
                 black  |   -3521.34     246.66   -14.28   0.000
                 other  |   -1946.16     839.73    -2.32   0.020
        --------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.12.1.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Summary of regression results and meaning of coefficients for coding schemes #1 and #2</strong></figcaption>
</figure>

You can see that the only difference is in the final row of the table. Coding scheme #1 estimates $\beta\\tiny M2$ and $\beta\\tiny F2$, whereas coding scheme #2 estimates ($\beta\\tiny M2$-$\beta\\tiny M1$)and ($\beta\\tiny F2$-$\beta\\tiny F1$).

### 3.3 Coding scheme #3
This coding scheme is like coding scheme #1, in that the marginal option is omitted from the mkspline command. Unlike coding scheme #1, coding scheme #3 specifies i.gender (instead of ibn.gender) and omits the noconstant option

``` 
  use gss_ivrm.dta
  mkspline ed1 12 ed2 = educ
  reg realrinc i.gender##(i.hsgrad c.ed1 c.ed2) i.race,vce(robust) noheader noci
``` 
        --------------------------------------------------------
                        |               Robust
               realrinc | Coefficient  std. err.      t    P>|t|
        ----------------+---------------------------------------
                 gender |
                Female  |   -9819.17    1638.56    -5.99   0.000
                        |
                 hsgrad |
               HS Grad  |    3382.00     661.55     5.11   0.000
                    ed1 |      86.71     151.38     0.57   0.567
                    ed2 |    4057.64     150.38    26.98   0.000
                        |
          gender#hsgrad |
        Female#HS Grad  |   -1633.91     766.98    -2.13   0.033
                        |
           gender#c.ed1 |
                Female  |     154.90     179.32     0.86   0.388
                        |
           gender#c.ed2 |
                Female  |   -1528.09     187.09    -8.17   0.000
                        |
                   race |
                 black  |   -3521.34     246.66   -14.28   0.000
                 other  |   -1946.16     839.73    -2.32   0.020
                        |
                  _cons |   17144.66    1389.51    12.34   0.000
        --------------------------------------------------------


### 3.4 Coding scheme #4

Finally, let’s consider a fourth coding scheme. This coding scheme is like coding scheme #3, except that the marginal option is included on the mkspline command.


``` 
  use gss_ivrm.dta
  mkspline ed1m 12 ed2m = educ,marginal
  reg realrinc i.gender##(i.hsgrad c.ed1m c.ed2m) i.race,vce(robust) noheader noci
``` 
        --------------------------------------------------------
                        |               Robust
               realrinc | Coefficient  std. err.      t    P>|t|
        ----------------+---------------------------------------
                 gender |
                Female  |   -9819.17    1638.56    -5.99   0.000
                        |
                 hsgrad |
               HS Grad  |    3382.00     661.55     5.11   0.000
                   ed1m |      86.71     151.38     0.57   0.567
                   ed2m |    3970.93     212.70    18.67   0.000
                        |
          gender#hsgrad |
        Female#HS Grad  |   -1633.91     766.98    -2.13   0.033
                        |
          gender#c.ed1m |
                Female  |     154.90     179.32     0.86   0.388
                        |
          gender#c.ed2m |
                Female  |   -1682.99     259.29    -6.49   0.000
                        |
                   race |
                 black  |   -3521.34     246.66   -14.28   0.000
                 other  |   -1946.16     839.73    -2.32   0.020
                        |
                  _cons |   17144.66    1389.51    12.34   0.000
        --------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.12.2.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Summary of regression results and meaning of coefficients for coding schemes #3 and #4</strong></figcaption>
</figure>

In comparing coding schemes #3 and #4, the only difference is in the final row of the table. Coding scheme #3 estimates $\beta\\tiny MM2$ and ($\beta\\tiny F2$-$\beta\\tiny M2$). By comparison, coding scheme #4 estimates ($\beta\\tiny M2$-$\beta\\tiny M1$) and($\beta\\tiny F2$-$\beta\\tiny F1$)-($\beta\\tiny M2$-$\beta\\tiny M1$) .

### 3.5 Choosing coding schemes
We can deliberately choose the coding scheme that might make the most sense given our research question.

Say that the emphasis of your research study was to test gender differences in the educ slope among high school graduates (that is,$\beta\\tiny F2$-$\beta\\tiny M2$). In that case, coding scheme #3 might be the most useful, **because the coefficient associated with gender#ed2 directly estimates this difference ($\beta\\tiny F2$-$\beta\\tiny M2$).** Had you chosen coding scheme #1, you could still estimate this difference, but would need to also use the contrast gender#c.ed1 command to compute this difference.

Instead, imagine that your research question focused on gender differences in the educ slope for high school graduates versus non–high school graduates. In that case, coding system #4 might be the most useful because the coefficient associated with gender#ed2m directly estimates this difference ($\beta\\tiny F2$-$\beta\\tiny F1$)-($\beta\\tiny M2$-$\beta\\tiny M1$).
