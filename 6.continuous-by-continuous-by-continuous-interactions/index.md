# Chapter6 ：Continuous by continuous by continuous interactions


**This chapter explores models that involve the interaction of three continuous linear predictors.**

## 1 Overview

Let’s first consider a hypothetical example looking at income (adjusted for inflation) as a function of age, education, and year born.

To illustrate the meaning of an interaction of these three continuous predictor, let’s first consider a model that **does not include a three-way interaction.**

$$ \widehat{realrin}=3365394 + — 55747age + -66053educ + -1712yrborn + 109age * educ + 28age * yrborn + 33.2educ*yrborn$$   




The variable age is plotted on the $x$(horizontal) axis, educ is on the $z$axis (representing depth), and the predicted value of realrinc is on the $y$(vertical) axis. Separate graphs are used to represent yrborn.The lines with respect to age are drawn thicker to help accentuate changes in the slope of the relationship between age and the outcome as a function of educ and yrborn.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.4.3.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Three-dimensional graph of predicted values from model without the three-way interaction</strong></figcaption>
</figure>

let’s now consider a model that includes a three-way interaction.This regression model predicts realrinc from age, educ, yrborn, the two-way interactions of these predictors (age*educ, age*yrborn, and educ*yrborn), and the three-way interaction (age*educ*yrborn).




\begin{align}
\widehat{realrin} &= -2004225 + 66407age + 313720educ + 1042yrborn +- 8524age\\
\cdot educ \notag\\
\end{align}

\begin{align}
+- 34.7age \cdot yrborn +- 161.62educ \cdot yrborn + 4.43age \\
\cdot educ \cdot yrborn \notag
\end{align}


    
<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.4.4.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Three-dimensional graph of predicted values from model with the three-way interaction</strong></figcaption>
</figure>

age on the $x$axis, educ on the $z$axis, the predicted value of realrinc on the $y$axis. Separate graphs represent yrborn for the years of birth 1930, 1940, 1950, and 1960 shown from left to right and then top to bottom

* For those born in 1930 (the top left panel), the age slope is mildly negative for those with 12 years of education and remains mildly negative across the different levels of education. 
* Skipping forward to those born in 1960 (the bottom right panel), the age slope is mildly positive for those with 12 years of education and is sharply positive for those with 20 years of education. 
* Looking across the panels from those born in 1930 (top left) to those born in 1960 (bottom right), we can see how the increase in the age slope due to higher levels of education grows as the year of birthincreases from 1930 to 1960. 
* For those born in 1930, the age slope remains largely the same for all education levels. By contrast, for those born in 1960, the age slope increases considerably with increasing levels of educ. In other words, the size of the age*educ interaction changes as a function of yrborn. This is a result of, and a way to describe, the interaction of age*educ*yrborn.


Let’s see how these models can be visualized using two-dimensional graphs. 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.5.1.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Two-dimensional graph of adjusted means for model with the threeway interaction</strong></figcaption>
</figure>

In 1960, the age slope increases considerably with increasing education. We can compare how the age slope changes as a function of education for those born in 1930 with those born in 1960. 
For those born in 1930, the age slope remains mildly negative for all levels of education. By contrast, for those born in 1960, the age slope grows increasingly positive with increasing levels of education. 
As we saw in the three-dimensional graph, this two-dimensional graph illustrates how the age×educ interaction changes as a function of yrborn, which is another way of saying that there is an age × educ × yrborn interaction.

## 1.1 Example using GSS data

Let’s now illustrate a model with an interaction of three continuous variables using the GSS dataset. This example predicts realrinc from age, educ, and yrborn.


```
  use gss_ivrm.dta
  keep if (age>=30 & age<=55) & (educ>=12) & (yrborn>=1930 & yrborn<=1960)
```

### 1.1.1 A model without a three-way interaction

     Linear regression                               Number of obs     =     11,765
                                                     F(8, 11756)       =     138.87
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.1142
                                                     Root MSE          =      24595

--------------------------------------------------------
                     |               Robust
            realrinc | Coefficient  std. err.      t    P>|t|
     
                 age |  -48960.89    8316.82    -5.89   0.000
                educ |  -61659.03   34481.49    -1.79   0.074
              yrborn |   -1529.75     290.82    -5.26   0.000
                     |
        c.age#c.educ |     109.44      18.74     5.84   0.000
                     |
      c.age#c.yrborn |      24.53       4.28     5.73   0.000
                     |
     c.educ#c.yrborn |      30.92      17.50     1.77   0.077
                     |
                race |
              black  |   -5274.18     464.22   -11.36   0.000
              other  |   -4616.74    1145.59    -4.03   0.000
                     |
               _cons |   3.01e+06  570438.47     5.28   0.000
--------------------------------------------------------

We can visualize these results using the margins and the marginsplot commands. The margins command is used to obtain the fitted value for ages 30 and 55, for educations of 12 to 20 years (in two-year increments), and for the years of birth 1930, 1940, 1950, and 1960. The marginsplot command is then used to graph these values, placing age on the $x$axis, using separate panels for yrborn, and using separate lines for educ


```
  margins, at(age=(30 55) educ=(12(2)20) yrborn=(1930(10)1960))
  marginsplot,xdimension(age) bydimension(yrborn) ///
              plotdimension(educ,allsimple) legend(row(2)subtitle(Education)) ///
              recast(line) scheme(s1mono) noci ylabel(,angle(0)) 
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.5.2.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Adjusted means from model without the three-way interaction, showing age on the $x$ axis, separate panels for year of birth, and separate lines for education</strong></figcaption>
</figure>

Although the people born in different years might show a different age slope at 12 years of education, the increase in the age slope as a function of education is the same across the years of birth. In fact, **the coefficient for c.age#c.educ describes the exact degree of this increase.** For every unit increase in educ, the age slope increases by 109.44. This is true for all years of birth.

Let’s see this for ourselves using the margins command. The margins command below estimates the age slope for those with 14 and 15 years of education who were born in 1930.

result compute: -88.59 + 109.44 = 20.85

```
margins,dydx(age) at(educ=(14 15) yrborn=1930) vsquish
```
     Average marginal effects                                Number of obs = 11,765
     Model VCE: Robust
     
     Expression: Linear prediction, predict()
     dy/dx wrt:  age
     1._at: educ   =   14
            yrborn = 1930
     2._at: educ   =   15
            yrborn = 1930

     ------------------------------------------------------------------------------
                  |            Delta-method
                  |      dy/dx   std. err.      t    P>|t|     [95% conf. interval]
      
     age          |
              _at |
               1  |     -88.59      81.21    -1.09   0.275      -247.78       70.59
               2  |      20.85      86.72     0.24   0.810      -149.14      190.84
     ------------------------------------------------------------------------------

Let’s make the same comparison, except for those born in 1940.
```
  margins,dydx(age)at(educ=(14 15) yrborn=1940) vsquish
```
result compute: 266.1371 - 156.6922 = 109.44



     Average marginal effects                                Number of obs = 11,765
     Model VCE: Robust

     Expression: Linear prediction, predict()
     dy/dx wrt:  age
     1._at: educ   =   14
            yrborn = 1940
     2._at: educ   =   15
            yrborn = 1940

     ------------------------------------------------------------------------------
                  |            Delta-method
                  |      dy/dx   std. err.      t    P>|t|     [95% conf. interval]
     -------------+----------------------------------------------------------------
     age          |
              _at |
               1  |     156.69      46.04     3.40   0.001        66.45      246.93
               2  |     266.14      53.97     4.93   0.000       160.35      371.92
     ------------------------------------------------------------------------------

     
### 1.1.2 A three-way interaction model

Let’s now consider a model that includes a three-way interaction of age, educ, and yrborn when predicting realrinc. Such models can **have a high degree of multicollinearity that can lead to numerical instability of the estimates and inflated standard errors.**

To avoid these problems, Aiken and West (1991) recommend **centering the predictors prior to the analysis.**

Let’s adopt this advice and center age, educ, and yrborn before attempting an analysis that includes the interaction of these three variables. The following generate commands create centered versions of these variables, centering them around values that I chose to ease the interpretation of the centered values.

```
  generate yrborn30 = yrborn - 1930
  generate age40 = age - 40
  generate educ16 = educ - 16
```

The variable yrborn is centered by subtracting 1930, creating a new variable called yrborn30. This centered variable will range from 0 to 30, corresponding to the years of birth ranging from 1930 to 1960. 

The centered version of age is called age40 and contains the value of age minus 40. An age of 30 would be represented as using the centered version of age (age40). 

Finally, the centered version of educ is called educ16, which contains educ minus 16. We could refer to 20 years of education via the centered variable by specifying that educ16 equals 4. The rest of this section will use these centered variables for the analysis.

Let’s now form a model that predicts realrinc from age40, educ16, and yrborn30, the two-way interactions among these variables, and the three-way interaction of these variables. 

```
  reg realrinc c.age40##c.educ16##c.yrborn30 i.race,vce(robust) noci
```

     Linear regression                               Number of obs     =     11,765
                                                     F(9, 11755)       =     126.24
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.1145
                                                     Root MSE          =      24591

     --------------------------------------------------------------------
                                 |               Robust
                        realrinc | Coefficient  std. err.      t    P>|t|
     ----------------------------+---------------------------------------
                           age40 |     -27.89     135.31    -0.21   0.837
                          educ16 |    2800.94     366.46     7.64   0.000
                                 |
                c.age40#c.educ16 |      26.63      42.54     0.63   0.531
                                 |
                        yrborn30 |     -88.92      55.93    -1.59   0.112
                                 |
              c.age40#c.yrborn30 |      32.56       6.69     4.87   0.000
                                 |
             c.educ16#c.yrborn30 |      12.89      17.90     0.72   0.471
                                 |
     c.age40#c.educ16#c.yrborn30 |       4.32       2.19     1.97   0.049
                                 |
                            race |
                          black  |   -5279.07     463.47   -11.39   0.000
                          other  |   -4648.00    1142.81    -4.07   0.000
                                 |
                           _cons |   33441.15    1161.80    28.78   0.000
     --------------------------------------------------------------------

We can visualize the c.age40#c.educ16#c.yrborn30 interaction in a variety of ways. We can focus on the age40 slope, the educ16 slope, or the yrborn30 slope. As we have done throughout this chapter, let’s focus our attention on the age slope (via the centered variable age40).


>Note! Retaining main effects and two-way interactions
>>Looking at the estimates from the regress command, you might notice that some of the main effects and two-way interaction terms are not significant. **Even though they are not significant, it is important to retain these effects to preserve the interpretation of the three-way interaction term.**

#### 1.1.2.1 Visualizing the three-way interaction

The margins command is used to compute the adjusted means at different values of the centered variable using the at() option. 
In terms of the uncentered variables, the at() option specifies ages 30 and 55, educations from 12 to 20 in 2-unit increments, and years of birth from 1930 to 1960 in 10-unit increments.

Then, the marginsplot command is used to graph the fitted values, placing age40 on the $x$ axis, with separate lines for educ16 and with separate panels for yrborn30. 


```
  margins, at(age40=(-10 15) educ16=(-4(2)4) yrborn30=(0(10)30))

  marginsplot,xdimension(age40) bydimension(yrborn30) ///
              plotdimension(educ16,allsimple) legend(row(2) subtitle(Education)) ///
              recast(line) scheme(s1mono) noci ylabel(, angle(0))
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.5.3.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Adjusted means from model with the three-way interaction as a function of age ( $x$ axis), year of birth (separate panels), and education (separate lines)</strong></figcaption>
</figure>

Let’s focus on the age40 slope, beginning with those born in 1930 (that is, yrborn30 = 0). The age40 slope is mildly negative for those born in 1930, across all levels of education. 
Jumping forward to 1960 (that is, yrborn30 = 30), the age40 slope is mildly positive for those with 12 years of education and is sharply positive for those with 20 years of education. Looking across the years of birth, we can see that increasing education is more highly related to an increase in the age40 slope as the year of birth increases. 

Let’s begin by estimating the age40 slope when yrborn30 is 0 and educ16 is and . This corresponds to those born in 1930 with 14 and 15 years of education.

```
  margins,dydx(age40) at(yrborn30=0 educ16=(-2 -1))vsquish
```

     Average marginal effects                                Number of obs = 11,765
     Model VCE: Robust

     Expression: Linear prediction, predict()
     dy/dx wrt:  age40
     1._at: educ16   = -2
            yrborn30 =  0
     2._at: educ16   = -1
            yrborn30 =  0

     ------------------------------------------------------------------------------
                  |            Delta-method
                  |      dy/dx   std. err.      t    P>|t|     [95% conf. interval]
     -------------+----------------------------------------------------------------
     age40        |
              _at |
               1  |     -81.16      80.00    -1.01   0.310      -237.98       75.66
               2  |     -54.53     102.69    -0.53   0.595      -255.81      146.76
     ------------------------------------------------------------------------------

The age40 slope is $-81.16$ for those with 14 years of education compared with $-54.53$ for those with 15 years of education, a gain of 26.63 units. This illustrates that a one-unit increase in education leads to a 26.63-unit increase in the age40 slope for those born in 1930.

Let’s now obtain the same estimates for those born one year later, born in 1931. This will allow us to assess the impact of a one-unit increase in yrborn30.


```
  margins,dydx(age40) at(yrborn30=1 educ16=(-2 -1))vsquish
```

     Average marginal effects                                Number of obs = 11,765
     Model VCE: Robust

     Expression: Linear prediction, predict()
     dy/dx wrt:  age40
     1._at: educ16   = -2
            yrborn30 =  1
     2._at: educ16   = -1
            yrborn30 =  1

     ------------------------------------------------------------------------------
                  |            Delta-method
                  |      dy/dx   std. err.      t    P>|t|     [95% conf. interval]
     -------------+----------------------------------------------------------------
     age40        |
              _at |
               1  |     -57.24      76.23    -0.75   0.453      -206.67       92.19
               2  |     -26.29      98.05    -0.27   0.789      -218.49      165.91
     ------------------------------------------------------------------------------

For those born in 1931, the change in the age40 slope due to a one-unit increase in education is 30.95 ($-26.29- -57.24$). This is larger than the value we found for 1930. A one-unit increase in educ16 yields a larger increase in the age40 slope for those born in 1931 (30.95) than for those born in 1930 (26.63). This is due to the inclusion of the three-way interaction.

Consider the value we obtain if we take 30.95 minus 26.63. We obtain 4.32, which corresponds to the coefficient for the c.age40#c.educ16#c.yrborn30 interaction. This interaction can be interpreted as the degree to which the age40 slope increases as a function of educ16 for every one-unit increase in yrborn30. For every one-unit increase in yrborn30, the age40 slope increases by 4.32 units for every oneunit increase in educ16.

#### 1.1.2.2 Visualizing the age slope
We can visualize the contribution that educ16 and yrborn30 make with respect to the age40 slope using the margins and marginsplot commands. 
First, the margins command is used with the dydx(age40) option to obtain estimates of the age40 slope for those with 12 to 20 years of education (in one-year increments) and those born in 1930 to 1960 (in 10-year increments).


```
  margins,dydx(age40) at(educ16=(-4(1)4) yrborn30=(0(10)30))
  marginsplot,noci
```
<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.5.4.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>age40 slope as a function of education ($x$ axis) and year of birth (separate lines)</strong></figcaption>
</figure>

>**Centering means to subtract a constant from the variable. One form of centering is called mean centering, in which the mean is subtracted from the variable.**

>You might wonder if the results of the following analysis would change if these variables were centered around different values. For example, suppose age was centered around 45 instead of 40. **The estimates regarding the three-way interaction would remain the same, whether age was centered around 40 or 45. The estimates of the adjusted means would remain the same as well.** However, there would be differences in the main effects and two-way interactions. These differences are not consequential because these lower order effects are not important in the presence of the three-way interaction term.

