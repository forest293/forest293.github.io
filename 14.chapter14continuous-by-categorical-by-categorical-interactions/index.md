# Chapter14 ：Continuous by continuous by categorical interactions


This chapter considers models that involve the interaction of two categorical predictors with a linear continuous predictor. 

## 1 Chapter overview

This chapter blends these two modeling techniques by exploring how the slope of the continuous variable varies as a function of the interaction of the two categorical variables.

Let’s consider a hypothetical example of a model with income as the outcome variable. The predictors include gender (a two-level categorical variable), education (treated as a three-level categorical variable), and age (a continuous variable). Income can be modeled as a function of each of the predictors, as well as the interactions of all the predictors. 

A three-way interaction of age by gender by education would imply that the effect of age interacts with gender by education. One way to visualize such an interaction would be to graph age on the $x$ axis, with separate lines for the levels of education and separate graphs for gender. 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.13.1.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Fitted values of income as a function of age, education, and gender</strong></figcaption>
</figure>


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.13.2.png" style="width:450px;height:150px;" alt="图片描述">
  <figcaption><strong>The age slope by level of education and gender</strong></figcaption>
</figure>

But consider the differences in the age slopes between females and males at each level of education. This difference is $-250 (150-400)$ for non–high school graduates, whereas this difference is $-350 (250-600)$ for high school graduates, and the difference is $-700 (600-1300)$ for college graduates. The difference in the age slopes between females and males seems to be much larger for college graduates than for high school graduates and non–high school graduates. 

Let’s explore this in more detail with an example using the GSS dataset. To focus on the linear effect of age, we will keep those who are 22 to 55 years old.


``` 
  use gss_ivrm.dta
  keep if (age>=22 & age<=55)
  reg realrinc i.gender##i.educ3##c.age i.race,vce(robust) noci
``` 

        Linear regression                               Number of obs     =     25,718
                                                        F(13, 25704)      =     411.30
                                                        Prob > F          =     0.0000
                                                        R-squared         =     0.1839
                                                        Root MSE          =      23556
        
        -----------------------------------------------------------
                           |               Robust
                  realrinc | Coefficient  std. err.      t    P>|t|
        -------------------+---------------------------------------
                    gender |
                   Female  |    1337.13    1693.69     0.79   0.430
                           |
                     educ3 |
                       HS  |     550.48    1782.19     0.31   0.757
                     Coll  |  -11156.10    2618.98    -4.26   0.000
                           |
              gender#educ3 |
                Female#HS  |     783.10    2021.65     0.39   0.698
              Female#Coll  |    7657.91    3164.30     2.42   0.016
                           |
                       age |     413.87      45.62     9.07   0.000
                           |
              gender#c.age |
                   Female  |    -264.98      50.66    -5.23   0.000
                           |
               educ3#c.age |
                       HS  |     175.85      54.75     3.21   0.001
                     Coll  |     897.33      77.47    11.58   0.000
                           |
        gender#educ3#c.age |
                Female#HS  |     -80.31      60.95    -1.32   0.188
              Female#Coll  |    -414.66      93.27    -4.45   0.000
                           |
                      race |
                    black  |   -2935.14     273.33   -10.74   0.000
                    other  |     185.40     956.34     0.19   0.846
                           |
                     _cons |    2691.23    1495.78     1.80   0.072
        -----------------------------------------------------------

Let’s test the interaction of gender, education, and age using the contrast command below. The three-way interaction is significant.

``` 
  contrast i.gender#i.educ3#c.age
``` 
        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------------
                           |         df           F        P>F
        -------------------+----------------------------------
        gender#educ3#c.age |          2       10.17     0.0000
                           |
               Denominator |      25704
        ------------------------------------------------------

To begin the process of interpreting the three-way interaction, let’s create a graph of the adjusted means as a function of age, education, and gender.

**compute the adjusted means by gender and education for age 22 and 55**

``` 
  margins gender#educ3,at(age=(22 55)) 
``` 

        Predictive margins                                      Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        1._at: age = 22
        2._at: age = 55
        
        ----------------------------------------------------------------------------------
                         |            Delta-method
                         |     Margin   std. err.      t    P>|t|     [95% conf. interval]
        -----------------+----------------------------------------------------------------
        _at#gender#educ3 |
          1#Male#not hs  |   11404.29     542.03    21.04   0.000     10341.88    12466.70
              1#Male#HS  |   15823.46     349.34    45.29   0.000     15138.72    16508.20
            1#Male#Coll  |   19989.51     916.25    21.82   0.000     18193.61    21785.41
        1#Female#not hs  |    6911.76     353.90    19.53   0.000      6218.10     7605.42
            1#Female#HS  |   10347.31     219.71    47.10   0.000      9916.67    10777.95
          1#Female#Coll  |   14032.45     620.38    22.62   0.000     12816.47    15248.44
          2#Male#not hs  |   25061.99    1080.61    23.19   0.000     22943.94    27180.03
              2#Male#HS  |   35284.20     738.38    47.79   0.000     33836.93    36731.46
            2#Male#Coll  |   63259.18    1510.70    41.87   0.000     60298.13    66220.24
        2#Female#not hs  |   11824.98     475.99    24.84   0.000     10892.02    12757.94
            2#Female#HS  |   18413.49     363.95    50.59   0.000     17700.13    19126.85
          2#Female#Coll  |   34873.99    1089.16    32.02   0.000     32739.18    37008.81
        ----------------------------------------------------------------------------------


**the figure illustrates how the age slope varies as a function of gender and education.**
``` 
  marginsplot,bydimension(gender)noci 
``` 

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.13.3.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Fitted values of income as a function of age, education, and gender</strong></figcaption>
</figure>

The graph in figure illustrates how the age slope varies as a function of gender and education. 

Let’s compute the age slope for each of the lines shown in this graph. The margins command is used with the dydx(age) and over() options to compute the age slopes separately for each combination of gender and education.

``` 
  margins,noci dydx(age) over(gender educ3)
```

        Average marginal effects                                Number of obs = 25,718
        Model VCE: Robust
        
        Expression: Linear prediction, predict()
        dy/dx wrt:  age
        Over:       gender educ3
        
        -------------------------------------------------------
                       |            Delta-method
                       |      dy/dx   std. err.      t    P>|t|
        ---------------+---------------------------------------
        age            |
          gender#educ3 |
          Male#not hs  |     413.87      45.62     9.07   0.000
              Male#HS  |     589.72      30.38    19.41   0.000
            Male#Coll  |    1311.20      62.88    20.85   0.000
        Female#not hs  |     148.89      22.09     6.74   0.000
            Female#HS  |     244.43      15.25    16.02   0.000
          Female#Coll  |     631.56      46.91    13.46   0.000
        -------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.13.4.png" style="width:750px;height:250px;" alt="图片描述">
  <figcaption><strong>The age slope by level of education and gender</strong></figcaption>
</figure>

## 2 Simple effects of gender on the age slope
We can use the contrast command to test the simple effect of gender on the age slope. This is illustrated below.

``` 
  contrast gender#c.age@educ3,nowald pveffects
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -----------------------------------------------------------------
                                 |   Contrast   Std. err.      t    P>|t|
        -------------------------+---------------------------------------
              gender@educ3#c.age |
        (Female vs base) not hs  |    -264.98      50.66    -5.23   0.000
            (Female vs base) HS  |    -345.29      33.99   -10.16   0.000
          (Female vs base) Coll  |    -679.64      78.45    -8.66   0.000
        -----------------------------------------------------------------

The first test compares the age slope for females versus males among non–high school graduates. Referring to table 14.2, this test compares $\beta\\tiny 1F$with$\beta\\tiny 1M$ . The difference in these age slopes is $-264.98(148.89 - 413.87)$, and this difference is significant. The age slope for females who did not graduate high school is 264.98 units smaller than the age slope for males who did not graduate high school.

The second test is similar to the first, except the comparison is made among high school graduates, comparing $\beta\\tiny 2F$ with $\beta\\tiny 2M$ from table 14.2. This test is also significant. 

The third test compares the age slope between females and males among college graduates (that is, comparing $\beta\\tiny 3F$ with $\beta\\tiny 3M$ ). This test is also significant. 

## 3 Simple effects of education on the age slope

We can also look at the simple effects of education on the age slope at each level of gender.

``` 
  contrast educ3#c.age@gender
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------------
                           |         df           F        P>F
        -------------------+----------------------------------
        educ3@gender#c.age |
                     Male  |          2       70.96     0.0000
                   Female  |          2       43.37     0.0000
                    Joint  |          4       57.21     0.0000
                           |
               Denominator |      25704
        ------------------------------------------------------

The first test compares the age slope among the three levels of education for males. 

$H\\tiny 0$:$\beta\\tiny 1M$ = $\beta\\tiny 2M$ = $\beta\\tiny 3M$      

This test is significant. The age slope significantly differs as a function of education among males.

The second test is like the first test, except that the comparisons are made for females. 

$H\\tiny 0$:$\beta\\tiny 1F$ = $\beta\\tiny 2F$ = $\beta\\tiny 3F$  

This test is also significant. Among females, the age slope significantly differs among the three levels of education.

## 4 Simple contrasts on education for the age slope

We can further dissect the simple effects tested above by applying contrast coefficients to the education factor.For example, say that we used the ar. contrast operator to form reverse adjacent group comparisons. 

``` 
  contrast ar.educ3#c.age@gender,nowald pveffects
```


        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ---------------------------------------------------------------
                               |   Contrast   Std. err.      t    P>|t|
        -----------------------+---------------------------------------
            educ3@gender#c.age |
          (HS vs not hs) Male  |     175.85      54.75     3.21   0.001
        (HS vs not hs) Female  |      95.54      26.84     3.56   0.000
            (Coll vs HS) Male  |     721.48      69.75    10.34   0.000
          (Coll vs HS) Female  |     387.13      49.39     7.84   0.000
        ---------------------------------------------------------------


## 5 Partial interaction on education for the age slope

The three-way interaction can be dissected by forming contrasts on the three-level categorical variable. Say that we use reverse adjacent group comparisons on education, which compares high school graduates with non–high school graduates and college graduates with high school graduates. 

``` 
  contrast ar.educ3#r.gender#c.age,nowald pveffects
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -------------------------------------------------------------------------
                                         |   Contrast   Std. err.      t    P>|t|
        ---------------------------------+---------------------------------------
                      educ3#gender#c.age |
        (HS vs not hs) (Female vs Male)  |     -80.31      60.95    -1.32   0.188
          (Coll vs HS) (Female vs Male)  |    -334.35      85.49    -3.91   0.000
        -------------------------------------------------------------------------

The first comparison tests the interaction of the contrast of high school graduates versus non–high school graduates by gender by age. 

The difference in the age slope between high school graduates and non–high school graduates for females is 244.43 minus 148.89 (95.54). For males, this difference is 589.72 minus 413.87 (175.85). The difference in these differences is $-80.31$, which is not significant (see the first comparison from the margins command).The difference in the age slope comparing high school graduates with non–high school graduates is not significantly different for males and females.

The second test forms the same kind of comparison, but compares college graduates with high school graduates. 

The difference in the age slope comparing female college graduates with female high school graduates is 631.56 minus 244.43 (387.13). This difference for males is 1,311.20 minus 589.72 (721.48). The difference of these differences is and is statistically significant (see the second comparison from the margins output). The increase in the age slope comparing college graduates with high school graduates is greater for males than it is for females.




