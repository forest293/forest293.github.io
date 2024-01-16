# Chapter13 ：Continuous by continuous by categorical interactions


This chapter explores models that include a categorical variable interacted with two continuous variables. 

## 1 Linear by linear by categorical interactions

In this section, we will explore whether the size of the c.age##c.educ interaction depends on gender. 

### 1.1 Fitting separate models for males and females
To get a general sense of the size of the c.age#c.educ interaction separately for males and females, let’s fit separate models for males and females.

Doing so separately for each level of gender by including the by gender, sort: prefix. (The vsquish and noheader options are used to save space.) The first set of results is restricted to analyzing only males, and the second set is restricted to analyzing only females. 


``` 
  use gss_ivrm.dta
  keep if (age>=22 & age<=55) & (educ>=12)
  by gender,sort:regress realrinc c.age##c.educ,vce(robust)vsquish noheader
``` 

        --------------------------------------------------------------------------------------------------------------
        -> gender = Male
        ------------------------------------------------------------------------------
                     |               Robust
            realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 age |   -1092.29     195.71    -5.58   0.000     -1475.92     -708.65
                educ |   -1831.07     526.01    -3.48   0.001     -2862.14     -799.99
        c.age#c.educ |     134.46      14.35     9.37   0.000       106.33      162.60
               _cons |   25275.91    7128.77     3.55   0.000     11302.24    39249.58
        ------------------------------------------------------------------------------
        
        --------------------------------------------------------------------------------------------------------------
        -> gender = Female
        ------------------------------------------------------------------------------
                     |               Robust
            realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                 age |    -876.56     158.24    -5.54   0.000     -1186.74     -566.39
                educ |    -966.11     426.78    -2.26   0.024     -1802.69     -129.54
        c.age#c.educ |      87.95      11.97     7.34   0.000        64.47      111.42
               _cons |   17020.70    5661.95     3.01   0.003      5922.30    28119.10
        ------------------------------------------------------------------------------

We can visualize this in two different ways, by focusing on the slope in the direction of age or by focusing on the slope in the direction of educ. Let’s begin by making a graph that focuses on the slope in the direction of age by placing age on the $x$ axis, with separate lines for educ. 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.12.3.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Fitted values for age by education interaction for males (left) and females (right)</strong></figcaption>
</figure>

Among males, the age slope increases by 134.46 units for every oneyear increase in educ. Among females, the age slope increases by 87.95 units for every one-year increase in educ.

We can visualize the c.age#c.educ interaction another way, focusing on the educ slope by placing educ on the $x$ axis, with separate lines for age. 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.12.4.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong> Fitted values for age by education interaction for males (left) and females (right) with education on the $x$ axis</strong></figcaption>
</figure>

For males, the educ slope increases by 134.46 units for every one-year increase in age. For females, the educ slope increases by 87.95 units for every oneyear increase in age.

To summarize, the coefficient for c.age#c.educ is 134.46 for males and is 87.95 for females. This suggests that the size of the c.age#c.educ coefficient may be significantly greater for males than for females. Let’s test this by analyzing males and females together in a single model.

### 1.2 Fitting a combined model for males and females

We now create a regression model that includes both males and females and predicts realrinc from age, educ, and the interaction of these two continuous variables. 

By specifying ibn.gender in conjunction with the noconstant option, the model fits separate intercepts by gender. By specifying the interaction of ibn.gender with c.age, with c.educ, and with c.age#c.educ, the model fits separate estimates of age, educ, and age#educ by gender. 

``` 
  reg realrinc ibn.gender ibn.gender#(c.age c.educ c.age#c.educ)i.race,vce(robust) noconstant noci
``` 
        Linear regression                               Number of obs     =     22,367
                                                        F(10, 22357)      =    3126.19
                                                        Prob > F          =     0.0000
                                                        R-squared         =     0.5339
                                                        Root MSE          =      24543
        
        ------------------------------------------------------------
                            |               Robust
                   realrinc | Coefficient  std. err.      t    P>|t|
        --------------------+---------------------------------------
                     gender |
                      Male  |   27044.09    7125.42     3.80   0.000
                    Female  |   19020.31    5695.44     3.34   0.001
                            |
               gender#c.age |
                      Male  |   -1116.24     195.43    -5.71   0.000
                    Female  |    -903.50     158.76    -5.69   0.000
                            |
              gender#c.educ |
                      Male  |   -1929.04     526.08    -3.67   0.000
                    Female  |   -1063.55     428.25    -2.48   0.013
                            |
        gender#c.age#c.educ |
                      Male  |     136.00      14.34     9.48   0.000
                    Female  |      89.52      12.01     7.46   0.000
                            |
                       race |
                     black  |   -3067.11     305.07   -10.05   0.000
                     other  |     493.01    1192.16     0.41   0.679
        ------------------------------------------------------------


We can express these results as two though there are two regression equations, one for males and one for females.      

\begin{align}
Males:\widehat{realrinc} = 27044.09 + - 1116.24age + - 1929.04educ  \notag\\
\end{align}

\begin{align}
\quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad  \\
 +136.00age*educ + -3067.11black + 493.01other  \notag \\
\end{align}


\begin{align}
Females:\widehat{realrinc} = 19020.31 + - 903.50age + - 1063.55educ  \notag\\
\end{align}

\begin{align}
\quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad   \\
 +89.52age*educ + -3067.11black + 493.01other  \notag \\
\end{align}

Let’s now test the difference of the c.age#c.educ interaction for females versus males using the contrast command below.

```
  contrast gender#c.age#c.educ,nowald pveffects
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------------------
                            |   Contrast   Std. err.      t    P>|t|
        --------------------+---------------------------------------
        gender#c.age#c.educ |
          (Female vs base)  |     -46.48      18.71    -2.48   0.013
        ------------------------------------------------------------


This test is significant,This means that the c.age#c.educ interaction is significantly lower for females than for males. Let’s explore how to interpret this interaction.

>Note! What about the lower order effects?
>>We have been focusing on the gender#c.age#c.educ interaction, but you might wonder about the lower order effects, such as gender#c.age or gender#c.educ. It is important to include these effects in the model to preserve the interpretation of the gender#c.age#c.educ interaction. However, there is little to gain by trying to interpret these effects.

### 1.3 Interpreting the interaction focusing in the age slope

To help interpret this interaction, let’s visualize it by making a graph that focuses on the age slope. 

```
  margins gender, at(age=(22 55) educ=(12(2)20))
  marginsplot,bydimension(gender) plotdimension(educ,allsimple)legend(subtitle(Education) rows(2)) noci 
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.12.5.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong> Fitted values by age ( axis), education (separate lines), and gender (separate panels)</strong></figcaption>
</figure>

For males, the age slope increases by 136.00 units for every one-unit increase in education. For females, the age slope increases by 89.52 units for every one-unit increase in education. The test of the gender#c.age#c.educ effect represents the difference in these interaction terms and this graph shows one way to visualize this.

The margins command can be used to show the size of the age slope by specifying the dydx(age) option. Let’s use the margins command to estimate the age slope for each of the levels of education expressed as a separate line in figure 


```
  margins gender,at(educ=(12(2)20)) dydx(age)  
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
        -------------+----------------------------------------------------------------
        age          |
          _at#gender |
             1#Male  |     515.76      34.61    14.90   0.000       447.93      583.60
           1#Female  |     170.70      19.56     8.73   0.000       132.36      209.03
             2#Male  |     787.76      28.15    27.98   0.000       732.58      842.94
           2#Female  |     349.73      16.77    20.85   0.000       316.85      382.61
             3#Male  |    1059.76      45.09    23.50   0.000       971.38     1148.14
           3#Female  |     528.76      36.51    14.48   0.000       457.19      600.33
             4#Male  |    1331.76      70.14    18.99   0.000      1194.28     1469.24
           4#Female  |     707.80      59.48    11.90   0.000       591.20      824.39
             5#Male  |    1603.76      97.22    16.50   0.000      1413.20     1794.32
           5#Female  |     886.83      83.05    10.68   0.000       724.06     1049.60
        ------------------------------------------------------------------------------

### 1.4 Interpreting the interaction focusing on the educ slope

Now, let’s visualize this interaction by making a graph that focuses on the educ slope.


```
  margins gender, at(age=(25(10)55) educ=(12 20)) 
  marginsplot, bydimension(gender) xdimension(educ) noci legend(rows(2)subtitle(Age))
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.12.6.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Fitted values by education ( axis), age (separate lines), and gender (separate panels)</strong></figcaption>
</figure>

This graph illustrates the gender#c.age#c.educ interaction by showing how the educ slope increases more rapidly as a function of age for males than for females.

The dydx(educ) option can be used with the margins command to compute the educ slope for each of the lines displayed in figure 

```
  margins gender,at(age=(25(10)55)) dydx(educ) 
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
        -------------+----------------------------------------------------------------
        educ         |
          _at#gender |
             1#Male  |    1470.96     206.19     7.13   0.000      1066.82     1875.10
           1#Female  |    1174.36     155.32     7.56   0.000       869.91     1478.81
             2#Male  |    2830.96     144.29    19.62   0.000      2548.14     3113.78
           2#Female  |    2069.52     104.23    19.85   0.000      1865.22     2273.83
             3#Male  |    4190.97     200.68    20.88   0.000      3797.61     4584.32
           3#Female  |    2964.69     162.58    18.24   0.000      2646.02     3283.35
             4#Male  |    5550.97     317.61    17.48   0.000      4928.43     6173.50
           4#Female  |    3859.85     266.13    14.50   0.000      3338.21     4381.49
        ------------------------------------------------------------------------------


### 1.5 Estimating and comparing adjusted means by gender

The difference in the adjusted means by gender varies as a function of age and educ due to the interaction of gender with c.age#c.educ. Thus, any comparisons by gender should be performed by specifying a particular value of age and educ. 

The size of the difference (as well as the significance of the difference) between males and females depends on both educ and age. You could repeat the margins commands above to obtain comparisons between males and females for a variety ofvalues of age and educ. Or you can specify multiple values at once within the margins command. 

or example, the margins command below estimates the adjusted mean for males and females for the combinations of 12, 15, and 20 years of education and 30, 40, and 50 years of age 

```
  margins gender,at(educ=(12 16 20)age=(30 40 50))
```

        Predictive margins                                      Number of obs = 22,367
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: age  = 30
               educ = 12
        2._at: age  = 30
               educ = 16
        3._at: age  = 30
               educ = 20
        4._at: age  = 40
               educ = 12
        5._at: age  = 40
               educ = 16
        6._at: age  = 40
               educ = 20
        7._at: age  = 50
               educ = 12
        8._at: age  = 50
               educ = 16
        9._at: age  = 50
               educ = 20
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
          _at#gender |
             1#Male  |   18995.85     290.94    65.29   0.000     18425.60    19566.10
           1#Female  |   11006.03     210.47    52.29   0.000     10593.50    11418.57
             2#Male  |   27599.71     480.25    57.47   0.000     26658.37    28541.04
           2#Female  |   17493.80     332.35    52.64   0.000     16842.37    18145.22
             3#Male  |   36203.56    1106.97    32.71   0.000     34033.83    38373.29
           3#Female  |   23981.56     788.13    30.43   0.000     22436.77    25526.36
             4#Male  |   24153.48     377.11    64.05   0.000     23414.31    24892.64
           4#Female  |   12713.02     213.38    59.58   0.000     12294.77    13131.26
             5#Male  |   38197.33     507.34    75.29   0.000     37202.92    39191.75
           5#Female  |   22781.44     373.68    60.96   0.000     22048.99    23513.88
             6#Male  |   52241.19    1088.80    47.98   0.000     50107.08    54375.31
           6#Female  |   32849.85     845.68    38.84   0.000     31192.26    34507.45
             7#Male  |   29311.10     662.83    44.22   0.000     28011.91    30610.30
           7#Female  |   14420.00     351.09    41.07   0.000     13731.84    15108.17
             8#Male  |   48794.96     831.12    58.71   0.000     47165.91    50424.02
           8#Female  |   28069.07     659.91    42.53   0.000     26775.61    29362.54
             9#Male  |   68278.83    1742.40    39.19   0.000     64863.60    71694.06
           9#Female  |   41718.15    1479.36    28.20   0.000     38818.50    44617.79
        ------------------------------------------------------------------------------

```
  margins r.gender,at(educ=(12 16 20) age=(30 40 50))
```

        Contrasts of predictive margins                         Number of obs = 22,367
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: age  = 30
               educ = 12
        2._at: age  = 30
               educ = 16
        3._at: age  = 30
               educ = 20
        4._at: age  = 40
               educ = 12
        5._at: age  = 40
               educ = 16
        6._at: age  = 40
               educ = 20
        7._at: age  = 50
               educ = 12
        8._at: age  = 50
               educ = 16
        9._at: age  = 50
               educ = 20
        
        -------------------------------------------------------
                            |         df           F        P>F
        --------------------+----------------------------------
                 gender@_at |
        (Female vs Male) 1  |          1      499.54     0.0000
        (Female vs Male) 2  |          1      296.57     0.0000
        (Female vs Male) 3  |          1       80.27     0.0000
        (Female vs Male) 4  |          1      701.62     0.0000
        (Female vs Male) 5  |          1      595.57     0.0000
        (Female vs Male) 6  |          1      196.14     0.0000
        (Female vs Male) 7  |          1      395.11     0.0000
        (Female vs Male) 8  |          1      381.32     0.0000
        (Female vs Male) 9  |          1      134.57     0.0000
                     Joint  |          4      483.36     0.0000
                            |
                Denominator |      22357
        -------------------------------------------------------
        
        ---------------------------------------------------------------------
                            |            Delta-method
                            |   Contrast   std. err.     [95% conf. interval]
        --------------------+------------------------------------------------
                 gender@_at |
        (Female vs Male) 1  |   -7989.82     357.48      -8690.50    -7289.14
        (Female vs Male) 2  |  -10105.91     586.83     -11256.13    -8955.68
        (Female vs Male) 3  |  -12222.00    1364.16     -14895.84    -9548.16
        (Female vs Male) 4  |  -11440.46     431.91     -12287.03   -10593.89
        (Female vs Male) 5  |  -15415.90     631.69     -16654.05   -14177.75
        (Female vs Male) 6  |  -19391.34    1384.60     -22105.24   -16677.44
        (Female vs Male) 7  |  -14891.10     749.15     -16359.48   -13422.72
        (Female vs Male) 8  |  -20725.89    1061.37     -22806.25   -18645.53
        (Female vs Male) 9  |  -26560.68    2289.64     -31048.54   -22072.82
        ---------------------------------------------------------------------


## 2 Linear by quadratic by categorical interactions

In this section, we will explore whether the size of the c.educ#c.age#c.age interaction depends on gender. 

### 2.1 Fitting separate models for males and females

Let’s begin by fitting a model that estimates the c.educ#c.age#c.age interaction in two separate models: one fit for males and another fit for females. This is performed by using the by gender, sort: prefix before the regress command that predicts realrinc from c.educ#c.age#c.age. 

```
  use gss_ivrm.dta
  keep if (age>=22 & age<=80) & (educ>=12)
  by gender,sort:reg realrinc c.educ##c.age##c.age,vce(robust)vsquish noheader
```

        ------------------------------------------------------------------------------------
        -> gender = Male
        ------------------------------------------------------------------------------------
                           |               Robust
                  realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
        -------------------+----------------------------------------------------------------
                      educ |  -12420.54    1305.49    -9.51   0.000    -14979.50    -9861.58
                       age |   -6160.36     876.56    -7.03   0.000     -7878.55    -4442.17
              c.educ#c.age |     661.99      65.00    10.18   0.000       534.57      789.41
               c.age#c.age |      56.96       9.96     5.72   0.000        37.43       76.48
        c.educ#c.age#c.age |      -6.21       0.74    -8.43   0.000        -7.66       -4.77
                     _cons |  131388.59   17570.69     7.48   0.000     96947.42   165829.75
        ------------------------------------------------------------------------------------
        
        ------------------------------------------------------------------------------------
        -> gender = Female
        ------------------------------------------------------------------------------------
                           |               Robust
                  realrinc | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
        -------------------+----------------------------------------------------------------
                      educ |   -6103.40     867.43    -7.04   0.000     -7803.68    -4403.12
                       age |   -3703.10     599.49    -6.18   0.000     -4878.18    -2528.02
              c.educ#c.age |     366.29      45.81     8.00   0.000       276.49      456.08
               c.age#c.age |      35.83       7.23     4.95   0.000        21.65       50.00
        c.educ#c.age#c.age |      -3.57       0.56    -6.43   0.000        -4.66       -2.48
                     _cons |   69959.52   11406.57     6.13   0.000     47600.98    92318.06
        ------------------------------------------------------------------------------------

This suggests that the size of this interaction might be more negative for males than for females. Before pursuing whether this difference is significant, let’s first visualize the c.educ#c.age#c.age interaction by gender to gain a further understanding of what this interaction means.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.12.7.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Fitted values for education by age-squared interaction for males (left) and females (right)</strong></figcaption>
</figure>

the male exhibit a greater increase in the curvature in the relationship between age and income as a function of education than do females.

Let’s test whether the c.educ#c.age#c.age interaction is significantly different for males versus females. To do this, we fit a combined model that includes both males and females to permit a statistical test of c.educ#c.age#c.age by gender.

### 2.2 Fitting a common model for males and females

Let’s fit one model for males and females together using a separate intercept and separate slopes coding system that provides separate intercept and slope estimates for males and females. Separate intercepts by gender are obtained by specifying ibn.gender in conjunction with the noconstant option. Then, the model uses the shortcut notation to interact ibn.gender with each term created by c.educ#c.age#c.age.

```
  reg realrinc ibn.gender ibn.gender#(c.educ##c.age##c.age) i.race,vce(robust)noconstant noci
```

        Linear regression                               Number of obs     =     25,964
                                                        F(14, 25950)      =    2544.34
                                                        Prob > F          =     0.0000
                                                        R-squared         =     0.5263
                                                        Root MSE          =      25748
        
        ------------------------------------------------------------------
                                  |               Robust
                         realrinc | Coefficient  std. err.      t    P>|t|
        --------------------------+---------------------------------------
                           gender |
                            Male  |  133604.98   17548.76     7.61   0.000
                          Female  |   73711.44   11412.10     6.46   0.000
                                  |
                    gender#c.educ |
                            Male  |  -12550.64    1305.36    -9.61   0.000
                          Female  |   -6329.01     867.97    -7.29   0.000
                                  |
                     gender#c.age |
                            Male  |   -6202.10     875.06    -7.09   0.000
                          Female  |   -3809.82     598.99    -6.36   0.000
                                  |
              gender#c.educ#c.age |
                            Male  |     665.21      64.93    10.24   0.000
                          Female  |     374.24      45.78     8.17   0.000
                                  |
               gender#c.age#c.age |
                            Male  |      57.18       9.95     5.75   0.000
                          Female  |      36.76       7.22     5.09   0.000
                                  |
        gender#c.educ#c.age#c.age |
                            Male  |      -6.23       0.74    -8.47   0.000
                          Female  |      -3.65       0.55    -6.58   0.000
                                  |
                             race |
                           black  |   -3474.03     291.35   -11.92   0.000
                           other  |    -299.12    1110.20    -0.27   0.788
        ------------------------------------------------------------------

We can express results as separate regression equations for males and females, as shown below

\begin{align}
Males:\widehat{realrinc} = 133605 + -12550.64educ + -6202.10age  \notag\\
\end{align}

\begin{align}
\quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad  \\
 +665.21educ × age + 57.18age^2+-6.23educ × age^2 \notag \\
\end{align}

\begin{align}
\quad \quad \quad \quad   \\
 +-3474.03black + -299.12other  \notag \\
\end{align}





\begin{align}
Females:\widehat{realrinc} = 73711.44 + -6329.01educ + -3809.82age  \notag\\
\end{align}

\begin{align}
\quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad  \\
 +665.21educ × age + 57.18age^2+-6.23educ × age^2 \notag \\
\end{align}

\begin{align}
\quad \quad \quad \quad   \\
 +-3474.03black + -299.12other  \notag \\
\end{align}

The coefficient for the c.educ#c.age#c.age interaction is $-6.23$ for males and is $-3.65$ for females. We can compare these coefficients using the contrast command, as shown below.

### 2.3 Interpreting the interaction

To help us understand this interaction, let’s visualize it by making a graph showing the c.educ#c.age#c.age interaction separately for males and females. 

compute adjusted means for age 22 to 90(in one-year increments) and for 12 to 20 years of education (in two-year increments),separately for males and females.

```
  margins gender,at(age=(22(1)80) educ=(12(2)20))
  marginsplot,bydimension(gender) xdimension(age)plotdimension(educ,allsimp) noci recast(line) scheme(s1mono)legend(subtitle(Education)rows(1))
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.12.8.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Fitted values by age ( axis), education (separate lines), and gender (separate panels)</strong></figcaption>
</figure>

We see a similar pattern for both males and females; as educ increases, the inverted U-shape for the relationship between income and age becomes more pronounced. However, this effect appears stronger for males than for females. This is confirmed by the significant gender#c.educ#c.age#c.age interaction. In other words, this interaction shows that the degree to which the quadratic effect of age changes as a function of educ is stronger for males than it is for females.

### 2.4 Estimating and comparing adjusted means by gender

The difference in income for males and females depends on both age and gender. The margins command can be used to compute estimates of, and differences in, the adjusted means by gender. 

```
  margins gender,at(educ=16 age=30)
```

        Predictive margins                                      Number of obs = 25,964
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: educ = 16
            age  = 30
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
              gender |
               Male  |   27288.24     430.84    63.34   0.000     26443.78    28132.71
             Female  |   17867.20     303.52    58.87   0.000     17272.28    18462.12
        ------------------------------------------------------------------------------

By adding the r. contrast operator to gender, we estimate the difference in these adjusted means, comparing females with males. This difference is significant.

```
  margins r.gender,at(educ=16 age=30) contrast(nowald pveffects)  
```

        Contrasts of predictive margins                         Number of obs = 25,964
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        At: educ = 16
            age  = 30
        
        ----------------------------------------------------------
                          |            Delta-method
                          |   Contrast   std. err.      t    P>|t|
        ------------------+---------------------------------------
                   gender |
        (Female vs Male)  |   -9421.04     529.92   -17.78   0.000
        ----------------------------------------------------------

We can specify multiple values in the at() option to estimate the mean for males and females for various combinations of age and educ. The margins command below estimates the mean for males and females for the combinations of 12, 16, and 20 years of education and 30, 40, and 50 years of age.


```
  margins gender,at(educ=(12 16 20) age=(30 40 50)) 
```

        Predictive margins                                      Number of obs = 25,964
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: educ = 12
               age  = 30
        2._at: educ = 12
               age  = 40
        3._at: educ = 12
               age  = 50
        4._at: educ = 16
               age  = 30
        5._at: educ = 16
               age  = 40
        6._at: educ = 16
               age  = 50
        7._at: educ = 20
               age  = 30
        8._at: educ = 20
               age  = 40
        9._at: educ = 20
               age  = 50
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
          _at#gender |
             1#Male  |   20106.88     284.18    70.75   0.000     19549.88    20663.89
           1#Female  |   11414.41     198.42    57.53   0.000     11025.50    11803.33
             2#Male  |   25574.42     439.87    58.14   0.000     24712.25    26436.58
           2#Female  |   13294.97     249.62    53.26   0.000     12805.70    13784.24
             3#Male  |   27517.24     502.91    54.72   0.000     26531.51    28502.98
           3#Female  |   13767.08     273.95    50.25   0.000     13230.11    14304.04
             4#Male  |   27288.24     430.84    63.34   0.000     26443.78    28132.71
           4#Female  |   17867.20     303.52    58.87   0.000     17272.28    18462.12
             5#Male  |   41909.91     621.06    67.48   0.000     40692.60    43127.22
           5#Female  |   24497.60     435.14    56.30   0.000     23644.71    25350.50
             6#Male  |   48019.96     712.27    67.42   0.000     46623.87    49416.06
           6#Female  |   26799.67     508.67    52.69   0.000     25802.64    27796.70
             7#Male  |   34469.61     988.60    34.87   0.000     32531.90    36407.31
           7#Female  |   24319.99     710.54    34.23   0.000     22927.30    25712.68
             8#Male  |   58245.41    1334.69    43.64   0.000     55629.34    60861.49
           8#Female  |   35700.24     981.01    36.39   0.000     33777.40    37623.07
             9#Male  |   68522.68    1525.11    44.93   0.000     65533.38    71511.98
           9#Female  |   39832.27    1144.65    34.80   0.000     37588.69    42075.85
        ------------------------------------------------------------------------------


```
  margins r.gender,at(educ=(12 16 20)age=(30 40 50))
```

        Contrasts of predictive margins                         Number of obs = 25,964
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: educ = 12
               age  = 30
        2._at: educ = 12
               age  = 40
        3._at: educ = 12
               age  = 50
        4._at: educ = 16
               age  = 30
        5._at: educ = 16
               age  = 40
        6._at: educ = 16
               age  = 50
        7._at: educ = 20
               age  = 30
        8._at: educ = 20
               age  = 40
        9._at: educ = 20
               age  = 50
        
        -------------------------------------------------------
                            |         df           F        P>F
        --------------------+----------------------------------
                 gender@_at |
        (Female vs Male) 1  |          1      637.88     0.0000
        (Female vs Male) 2  |          1      592.48     0.0000
        (Female vs Male) 3  |          1      577.96     0.0000
        (Female vs Male) 4  |          1      316.07     0.0000
        (Female vs Male) 5  |          1      523.91     0.0000
        (Female vs Male) 6  |          1      587.04     0.0000
        (Female vs Male) 7  |          1       68.93     0.0000
        (Female vs Male) 8  |          1      183.57     0.0000
        (Female vs Male) 9  |          1      225.32     0.0000
                     Joint  |          6      375.91     0.0000
                            |
                Denominator |      25950
        -------------------------------------------------------
        
        ---------------------------------------------------------------------
                            |            Delta-method
                            |   Contrast   std. err.     [95% conf. interval]
        --------------------+------------------------------------------------
                 gender@_at |
        (Female vs Male) 1  |   -8692.47     344.17      -9367.07    -8017.88
        (Female vs Male) 2  |  -12279.45     504.48     -13268.25   -11290.65
        (Female vs Male) 3  |  -13750.17     571.95     -14871.23   -12629.11
        (Female vs Male) 4  |   -9421.04     529.92     -10459.72    -8382.37
        (Female vs Male) 5  |  -17412.31     760.72     -18903.37   -15921.25
        (Female vs Male) 6  |  -21220.29     875.82     -22936.95   -19503.62
        (Female vs Male) 7  |  -10149.62    1222.46     -12545.71    -7753.52
        (Female vs Male) 8  |  -22545.17    1664.01     -25806.73   -19283.61
        (Female vs Male) 9  |  -28690.41    1911.35     -32436.76   -24944.05
        ---------------------------------------------------------------------

