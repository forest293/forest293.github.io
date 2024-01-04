# Chapter5 ：Continuous by continuous interactions


**This chapter illustrates how to interpret interactions of two continuous predictors.**

## 1 Linear by linear interactions

**A linear by linear interaction implies that the slope of the relationship between one of the predictors and the outcome changes as a linear function of the other predictor.**

Let’s begin with a hypothetical example in which we predict income (realrinc) from age (age) and education (educ) without an interaction. 

$$ \widehat{realrin}=-41300 + 600age + 3000educ $$   



<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/linear%20interaciton.png" style="width:450px;height:350px;"alt="图片描述">
  <figcaption><strong>Three-dimensional graph of fitted values from model without aninteraction</strong></figcaption>
</figure>


Let’s now consider a second hypothetical regression model that contains an interaction of age (age) and education (educ).

$$ \widehat{realrin}=2400 + -1100age - 1600educ + 120age*educ $$   

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/three-demensional%20graph.png" style="width:450px;height:350px;"alt="图片描述">
  <figcaption><strong>Three-dimensional graph of fitted values from model with an interaction</strong></figcaption>
</figure>


Let’s transition to the use of two-dimensional graphs for illustrating the precise role of linear by linear interactions. 



<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/two-dimensional%20graph.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Two-dimensional graph of fitted values from model without an interaction (left panel) and with an interaction (right panel)</strong></figcaption>
</figure>

The left panel shows a two-dimensional representation of the model without an interaction, and the right panel shows a two-dimensional representation of the model with an interaction.



### 1.1 Example using GSS data

```
  use gss_ivrm.dta
  keep if (age>=22 & age <=55) & (educ>=12)
```
Let’s fit a model where we predict realrinc from educ, age, and the interaction of these two variables.

```
   reg realrinc c.educ##c.age female, vce(robust)
```

     Linear regression                               Number of obs     =     22,367
                                                     F(4, 22362)       =     664.04
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.1712
                                                     Root MSE          =      24677

------------------------------------------------------------------------------
                  |               Robust
         realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
  
             educ |   -1487.58     339.60    -4.38   0.000     -2153.21     -821.94
              age |   -1036.79     126.23    -8.21   0.000     -1284.21     -789.38
                  |
     c.educ#c.age |     114.91       9.38    12.25   0.000        96.52      133.30
                  |
           female |  -12553.79     328.28   -38.24   0.000    -13197.25   -11910.33
            _cons |   28738.43    4550.48     6.32   0.000     19819.18    37657.69
------------------------------------------------------------------------------

### 1.2 Interpreting the interaction in terms of age

We can use the margins and marginsplot commands to visualize this interaction. Let’s visualize this interaction by graphing the adjusted means with age on the X axis and with separate lines for each level of educ. The margins command is used with the **at()** option to compute the adjusted means for ages 22 and 55 and educations ranging from 12 to 20 in two-year increments.

```
   margins, at(age=(22 55) educ=(12(2)20))
```

Then, the marginsplot command is used to graph the adjusted means with age on the axis and with separate lines for each level of educ.**The legend() option is included to customize the display of the graph legend.**

```
   marginsplot,noci legend(rows(3) title(Education))
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/two-demensional%20graph%20of%20an%20interaction1.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Adjusted means for a linear by linear interaction with age on the X axis</strong></figcaption>
</figure>

we can use the margins command combined with the **dydx(age) option** to estimate the slope for each of the lines displayed in figure 


```
   margins, at(educ=(12(2)20)) dydx(age) vsquish
```

     Average marginal effects                                Number of obs = 22,367
     Model VCE: Robust

     Expression: Linear prediction, predict()
     dy/dx wrt:  age
     1._at: educ = 12
     2._at: educ = 14
     3._at: educ = 16
     4._at: educ = 18
     5._at: educ = 20

------------------------------------------------------------------------------
             |            Delta-method
             |      dy/dx   std. err.      t    P>|t|     [95% conf. interval]
 
     age          |
         _at |
          1  |     342.16      19.78    17.30   0.000       303.39      380.93
          2  |     571.99      16.30    35.09   0.000       540.03      603.94
          3  |     801.81      29.06    27.59   0.000       744.85      858.77
          4  |    1031.64      46.12    22.37   0.000       941.23     1122.04
          5  |    1261.46      64.14    19.67   0.000      1135.73     1387.19
------------------------------------------------------------------------------

The estimates of the age slope increase as a function of educ. For example, at 12 years of education, the age slope is 342.16, and at 14 years of education, the age slope is 571.99. For a two-unit increase in education, the age slope increases by 229.83 ($571.99-342.16$). **We can relate this to the coefficient for the c.educ#c.age interaction, which is the amount by which the age slope changes for every one-year increase in educ.** For every one-year increase in educ, the age slope increases by 114.91($229.83÷2=114.91$).


### 1.3 Interpreting the interaction in terms of education

Now, let’s explore the meaning of the interaction by focusing on the educ slope. Let’s visualize this by creating a graph of the adjusted means showing educ on the $x$ axis and separate lines for age. 

First, the margins command is used to create adjusted means for 12 and 20 years of education and ages ranging from 25 to 55 in 10-year increments.

```
   margins, at(educ=(12(2)20)) dydx(age) vsquish
```
     Predictive margins                                      Number of obs = 22,367
     Model VCE: Robust

     Expression: Linear prediction, predict()
     1._at: educ = 12
            age  = 25
     2._at: educ = 12
            age  = 35
     3._at: educ = 12
            age  = 45
     4._at: educ = 12
            age  = 55
     5._at: educ = 20
            age  = 25
     6._at: educ = 20
            age  = 35
     7._at: educ = 20
            age  = 45
     8._at: educ = 20
            age  = 55

------------------------------------------------------------------------------
             |            Delta-method
             |     Margin   std. err.      t    P>|t|     [95% conf. interval]
 
         _at |
          1  |   13091.92     236.14    55.44   0.000     12629.07    13554.76
          2  |   16513.52     172.78    95.57   0.000     16174.85    16852.19
          3  |   19935.12     286.72    69.53   0.000     19373.12    20497.11
          4  |   23356.72     461.33    50.63   0.000     22452.47    24260.96
          5  |   24173.82     876.85    27.57   0.000     22455.13    25892.50
          6  |   36788.43     619.20    59.41   0.000     35574.75    38002.11
          7  |   49403.04     906.00    54.53   0.000     47627.21    51178.86
          8  |   62017.65    1442.62    42.99   0.000     59190.01    64845.28
------------------------------------------------------------------------------

```
   marginsplot,noci legend(title(Age))
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/linear%20interaction.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Adjusted means for a linear by linear interaction with education on the axis</strong></figcaption>
</figure>

we can estimate the slope of each line (that is, the educ slope) using the margins command, as shown below.

```
   margins,at(age=(25(10)55)) dydx(educ)vsquish
```
     Average marginal effects                                Number of obs = 22,367
     Model VCE: Robust

     Expression: Linear prediction, predict()
     dy/dx wrt:  educ
     1._at: age = 25
     2._at: age = 35
     3._at: age = 45
     4._at: age = 55

------------------------------------------------------------------------------
                  |            Delta-method
                  |      dy/dx   std. err.      t    P>|t|     [95% conf. interval]

     educ         |
              _at |
               1  |    1385.24     129.78    10.67   0.000      1130.87     1639.61
               2  |    2534.36      90.87    27.89   0.000      2356.24     2712.48
               3  |    3683.49     131.45    28.02   0.000      3425.83     3941.15
               4  |    4832.62     209.54    23.06   0.000      4421.90     5243.33
------------------------------------------------------------------------------

As age increases, so does the educ slope. For every one-unitincrease in age, the educ slope increases by 114.91, the estimate of the age#educ interaction. For a 10-unit increase in age, the educ slope would be expected to increase by 1,149.12. 

### 1.4 Interpreting the interaction in terms of age slope

We can visualize the age by educ interaction by illustrating the way that **the age slope changes as a function of education.** The margins command below includes the dydx(age) option to estimate the age slope at each level of educ.

```
   margins,dydx(age) at(educ=(12(1)20))vsquish
```


     Average marginal effects                                Number of obs = 22,367
     Model VCE: Robust

     Expression: Linear prediction, predict()
     dy/dx wrt:  age
     1._at: educ = 12
     2._at: educ = 13
     3._at: educ = 14
     4._at: educ = 15
     5._at: educ = 16
     6._at: educ = 17
     7._at: educ = 18
     8._at: educ = 19
     9._at: educ = 20

------------------------------------------------------------------------------
                  |            Delta-method
                  |      dy/dx   std. err.      t    P>|t|     [95% conf. interval]

     age          |
              _at |
               1  |     342.16      19.78    17.30   0.000       303.39      380.93
               2  |     457.07      15.51    29.47   0.000       426.68      487.47
               3  |     571.99      16.30    35.09   0.000       540.03      603.94
               4  |     686.90      21.61    31.78   0.000       644.54      729.26
               5  |     801.81      29.06    27.59   0.000       744.85      858.77
               6  |     916.72      37.39    24.52   0.000       843.44      990.01
               7  |    1031.64      46.12    22.37   0.000       941.23     1122.04
               8  |    1146.55      55.07    20.82   0.000      1038.61     1254.49
               9  |    1261.46      64.14    19.67   0.000      1135.73     1387.19
------------------------------------------------------------------------------

This shows that the age slope increases as a function of education. In fact, the age slope increases by 114.91 units for every one-unit increase in educ. We can visualize these age slopes as a function of education using the marginsplot command (shown below). 

```
   marginsplot
```

![age slope as a function of education](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.3marginsplot.png)

### 1.5 Interpreting the interaction in terms of the educ slope

We can visualize the age by educ interaction by focusing on the way that the educ slope changes as a function of age. The margins command below estimates the educ slope for ages ranging from 25 to 55 in five-year increments.

```
   margins,dydx(educ)at(age=(25(5)55))vsquish
```
     Average marginal effects                                Number of obs = 22,367
     Model VCE: Robust

     Expression: Linear prediction, predict()
     dy/dx wrt:  educ
     1._at: age = 25
     2._at: age = 30
     3._at: age = 35
     4._at: age = 40
     5._at: age = 45
     6._at: age = 50
     7._at: age = 55

------------------------------------------------------------------------------
                  |            Delta-method
                  |      dy/dx   std. err.      t    P>|t|     [95% conf. interval]

     educ         |
              _at |
               1  |    1385.24     129.78    10.67   0.000      1130.87     1639.61
               2  |    1959.80     101.73    19.26   0.000      1760.40     2159.20
               3  |    2534.36      90.87    27.89   0.000      2356.24     2712.48
               4  |    3108.93     102.80    30.24   0.000      2907.43     3310.43
               5  |    3683.49     131.45    28.02   0.000      3425.83     3941.15
               6  |    4258.05     168.50    25.27   0.000      3927.78     4588.33
               7  |    4832.62     209.54    23.06   0.000      4421.90     5243.33
------------------------------------------------------------------------------

This shows that the educ slope increases as a function of age. For each five-year increase in age, the educ slope increases by 574.56 (that is, the age#educ coefficient multiplied by five, $114.91 × 5$).

```
marginsplot,noci
```
![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.3marginsplot2.png)


## 2 Linear by quadratic interactions

**This section considers models with an interaction of two continuous variables, where one of the variables is fit linearly and the other is fit using a quadratic term.**

A quadratic term introduces curvature in the fitted relationship between the predictor and outcome. 

Let’s first illustrate a model that contains a quadratic predictor and a linear predictor **but no interaction between these predictors.** This hypothetical example uses realrinc as the outcome variable, age as the quadratic predictor, and educ as the linear predictor. 

$$ \widehat{realrin}=-7000 + 2100age + - age^2 + 3000educ $$   


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.3three%20dimentional.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Three-dimensional graph of fitted values for linear and quadratic models without an interaction</strong></figcaption>
</figure>

Let’s now consider a model that includes an interaction between age (as a quadratic term) and educ. This regression equation is shown below

$$ \widehat{realrin}=165000 + -8600age + 95age^2 +-14000educ + 780age*educ + - 8age^2 * educ  $$   

The key term is the age squared by education interaction. This governs the degree of the curvature in age as a function of education. 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.3three%20dimentional2.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Three-dimensional graph of fitted values for linear and quadratic models with an interaction</strong></figcaption>
</figure>

As education increases from 12 to 20years, the degree of the inverted U-shape grows as a function of education. This is due to the interaction of education with age squared. When quadratic terms become more negative, the inverted U-shape becomes more pronounced. Thus for every one-unit increase in education, the relationship between income and age shows more of an inverted U-shape.


 We can clearly see how the degree of the curvature in relationship between income and age is the same across the levels of education. The right panel of figure shows a two-dimensional representation of figure above, where there was an interaction of education with the quadratic term for age. 



 <figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/two-dimensional%20graph2.png" style="width:600px;height:350px;" alt="图片描述">
  <figcaption><strong>Two-dimensional graph of fitted values for linear and quadratic models without an interaction (left panel) and with linear by quadratic interaction (right panel)</strong></figcaption>
</figure>

### 2.1 Example using GSS data
Let’s use the GSS dataset to fit a model that predicts realrinc from educ (treated as a continuous linear variable) interacted with age (treated as a continuous variable fit using a **quadratic term**).

``` code
  use gss_ivrm.dta
  keep if (age>=22 & age<=80) & (educ>=12)
  reg realrinc c.educ c.age##c.age female,vce(robust)
```
Let’s begin by fitting a model that predicts realrinc using educ as a linear term and age as a quadratic term **but no interaction** between education and age. 


     Linear regression                               Number of obs     =     25,964
                                                     F(4, 25959)       =     785.49
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.1658
                                                     Root MSE          =      26078

------------------------------------------------------------------------------
                  |               Robust
         realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
          
             educ |    3071.27      93.46    32.86   0.000      2888.08     3254.45
              age |    2245.62      79.05    28.41   0.000      2090.67     2400.57
                  |
      c.age#c.age |     -21.77       0.94   -23.09   0.000       -23.62      -19.92
                  |
           female |  -13344.78     319.66   -41.75   0.000    -13971.33   -12718.23
            _cons |  -64839.08    2060.09   -31.47   0.000    -68876.96   -60801.20
------------------------------------------------------------------------------

The quadratic term is significant and is negative. This negative coefficient indicates that the relationship between age and income has an **inverted U-shape**. 

Let’s use the margins and marginsplot commands to visualize the adjusted means of income as a function of age while holding education constant at 12 to 20 years of education (in two-year increments).

We first compute the adjusted means as a function of age and education using the margins command.
Then, the marginsplot command graphs the adjusted means on the $y$ axis and age on the $x$ axis, with separate lines for each level of education. 


``` code
   margins,at(age=(22(1)80) educ=(12(2)20))
   marginsplot,noci legend(rows(2))recast(line) scheme(s1mono)
```



<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.4.1.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Adjusted means at 12, 14, 16, 18, and 20 years of education</strong></figcaption>
</figure>



Let’s now fit a model that **includes an interaction** of educ with the quadratic term for age. 

``` code
   reg realrinc c.educ##c.age##c.age female,vce(robust) noci
```

     Linear regression                               Number of obs     =     25,964
                                                     F(6, 25957)       =     558.27
                                                     Prob > F          =     0.0000
                                                     R-squared         =     0.1757
                                                     Root MSE          =      25924

-----------------------------------------------------------
                        |               Robust
               realrinc | Coefficient  std. err.      t    P>|t|
     
                   educ |   -9317.78     817.82   -11.39   0.000
                    age |   -5009.26     551.97    -9.08   0.000
                        |
           c.educ#c.age |     517.95      41.56    12.46   0.000
                        |
            c.age#c.age |      46.88       6.39     7.34   0.000
                        |
     c.educ#c.age#c.age |      -4.91       0.48   -10.18   0.000
                        |
                 female |  -13160.33     317.92   -41.40   0.000
                  _cons |  108559.85   10897.04     9.96   0.000
-----------------------------------------------------------

Let’s interpret this as a function of age by graphing the adjusted means on the axis and age on the axis, with separate lines to indicate the levels of education. 

``` code
   margins,at(age=(22 25(5)80) educ=(12(2)20))
   marginsplot,plotdimension(,allsimple)legend(subtitle(Education)rows(2))noci recast(line) scheme(s1mono)
```


![]()

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.4.2.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Adjusted means from linear by quadratic model</strong></figcaption>
</figure>


the c.educ#c.age#c.age coefficient describes the degree to which the inverted U-shape changes as a function of education. This coefficient, which is $-4.9$, represents the change in the quadratic term for age for a one-unit increase in educ. As education increases by one unit, the quadratic term for age decreases by 4.9, creating a more inverted U-shape.

For those with lower educations, the relationship between age and income tends to be more linear, and for those with higher levels of education, the relationship between age and income is more curved, showing a greater rise and fall across ages.
