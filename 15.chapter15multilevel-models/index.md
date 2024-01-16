# Chapter15 ：Multilevel models


One of the unique features of multilevel models is the ability to study cross-level interactions—the interactions of a level-1 variable with a level-2 variable. Such interactions allow you to explore the extent to which the effect of a level-1 variable is moderated by a level-2 variable. 

## 1 Chapter overview
This chapter contains four examples, all illustrating multilevel models where students are nested within schools.

These four examples provide the opportunity to explore four kinds of crosslevel interactions: 

* continuous by continuous (example 1) 
* continuous by categorical (example 2)
* categorical by continuous (example 3) 
* categorical by categorical (example 4)

All of these examples are completely hypothetical and have been constructed to simplify the interpretation and visualization of the results.

## 2  Example 1: Continuous by continuous interaction

Consider a two-level multilevel model where students are nested within schools. One hundred schools were randomly sampled from a population of schools, and students were randomly sampled from each of the schools. 

Two student-level variables were measured: socioeconomic status (ses) and a standardized writing test score (write). Furthermore, a school-level variable was measured: the number students per computer within the school, stucomp


``` 
  use school_write.dta
  sum
  list in 1/5,abbreviate(30)
``` 
        
            Variable |        Obs        Mean    Std. dev.       Min        Max
        -------------+---------------------------------------------------------
            schoolid |      3,026    50.71481    29.01184          1        100
               stuid |      3,026    17.08824    10.81501          1         52
               write |      3,026    542.1325    191.3663          0       1200
                 ses |      3,026    49.78352    10.18169    14.1897   85.06909
             stucomp |      3,026    5.857066    3.533295   1.149443   16.50701

             +------------------------------------------------+
             | schoolid   stuid   write        ses    stucomp |
             |------------------------------------------------|
          1. |        1       1     553   55.38129   3.350059 |
          2. |        1       2     530   61.13125   3.350059 |
          3. |        1       3     604   47.61407   3.350059 |
          4. |        1       4     433   48.26278   3.350059 |
          5. |        1       5     370    47.9762   3.350059 |
             +------------------------------------------------+

The aim of this hypothetical study is to determine if the greater availability of computers at a school reduces the strength of the relationship between socioeconomic status and writing test scores. In other words, the goal is to determine if there is a cross-level interaction of ses and stucomp in the prediction of write.

The mixed command predicts write from c.ses, c.stucomp, and the interaction c.stucomp#c.ses. The random-effects portion of the model indicates that ses is a random effect across levels of schoolid. The covariance(un) (un is short for unstructured) option permits the random intercept and ses slope to be correlated. (In this and all subsequent examples in this chapter, the nolog and noheader options are used to save space.These options suppress the iteration log and the header information.)

``` 
  mixed write c.stucomp##c.ses || schoolid:ses,covariance(un)nolog noheader
``` 

        ---------------------------------------------------------------------------------
                  write | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        ----------------+----------------------------------------------------------------
                stucomp |     -26.75       2.69    -9.95   0.000       -32.01      -21.48
                    ses |       0.36       0.68     0.53   0.597        -0.97        1.69
                        |
        c.stucomp#c.ses |       0.24       0.10     2.28   0.023         0.03        0.44
                        |
                  _cons |     602.29      18.18    33.12   0.000       566.65      637.93
        ---------------------------------------------------------------------------------
        
        ------------------------------------------------------------------------------
          Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
        -----------------------------+------------------------------------------------
        schoolid: Unstructured       |
                            var(ses) |       8.92       1.73          6.09       13.05
                          var(_cons) |     372.35    1229.73          0.58   241085.03
                      cov(ses,_cons) |      19.20      35.82        -51.01       89.40
        -----------------------------+------------------------------------------------
                       var(Residual) |    9820.89     261.13       9322.20    10346.25
        ------------------------------------------------------------------------------
        LR test vs. linear model: chi2(3) = 3253.82               Prob > chi2 = 0.0000
        
        Note: LR test is conservative and provided only for reference.

>Note! Fixed and random effects
>>The fixed effects are specified after the dependent variable and before the ||. The random effects are specified after the ||. 

As expected, the c.stucomp#c.ses interaction is significant. We can use themargins command below with the dydx(ses) option to compute the ses slope for schools that have between one and eight students per computer.

``` 
  margins,dydx(ses) at(stucomp=(1(1)8)) vsquish 
``` 

        Average marginal effects                                 Number of obs = 3,026
        
        Expression: Linear prediction, fixed portion, predict()
        dy/dx wrt:  ses
        1._at: stucomp = 1
        2._at: stucomp = 2
        3._at: stucomp = 3
        4._at: stucomp = 4
        5._at: stucomp = 5
        6._at: stucomp = 6
        7._at: stucomp = 7
        8._at: stucomp = 8
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |      dy/dx   std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
        ses          |
                 _at |
                  1  |       0.59       0.59     1.01   0.315        -0.56        1.75
                  2  |       0.83       0.51     1.62   0.104        -0.17        1.83
                  3  |       1.07       0.44     2.42   0.016         0.20        1.93
                  4  |       1.30       0.39     3.37   0.001         0.54        2.06
                  5  |       1.54       0.35     4.34   0.000         0.84        2.23
                  6  |       1.77       0.35     5.05   0.000         1.08        2.46
                  7  |       2.01       0.38     5.32   0.000         1.27        2.75
                  8  |       2.24       0.43     5.24   0.000         1.40        3.08
        ------------------------------------------------------------------------------

Writing score by socioeconomic status and students per computer


``` 
  margins,at(stucomp=(1(1)8) ses=(20(5)80))
  marginsplot,noci xdimension(ses) plotdimension(stucomp, allsimple)legend(subtitle(Students per computer) rows(2)) recast(line) scheme(s1mono) 
``` 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.13.6.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>Writing score by socioeconomic status and students per computer</strong></figcaption>
</figure>

We can see that as the number of students per computer increases (that is, as the availability of computers decreases), the ses slope increases. 

## 3  Example 2: Continuous by categorical interaction

In this study, standardized reading scores were measured as well as the socioeconomic status of the student. Of the 100 schools, 35 were private (non-Catholic), 35 were public, and 30 were Catholic schools

The goal of this hypothetical study is to examine the relationship between socioeconomic status (ses) and reading scores (read), and to determine if the strength of that relationship varies as a function of the type of school (private, public, or Catholic). This involves examining the cross-level interaction of ses and schtype.

The mixed command for performing this analysis is shown below. The variable read is predicted from ses, schtype, and the interaction of these two variables. The variable ses is specified as a random coefficient that varies across schools.


``` 
  use school_read.dta
  sum
  list in 1/5,abbreviate(30)
  mixed read i.schtype##c.ses || schoolid:ses,covariance(un) nolog noheader 
``` 

        --------------------------------------------------------------------------------
                  read | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        ---------------+----------------------------------------------------------------
               schtype |
               Public  |      41.97      23.27     1.80   0.071        -3.64       87.57
             Catholic  |     152.48      23.79     6.41   0.000       105.86      199.10
                       |
                   ses |       4.04       0.67     6.00   0.000         2.72        5.36
                       |
         schtype#c.ses |
               Public  |      -1.24       0.95    -1.30   0.192        -3.11        0.62
             Catholic  |      -3.37       0.99    -3.41   0.001        -5.30       -1.44
                       |
                 _cons |     519.28      16.57    31.33   0.000       486.80      551.77
        --------------------------------------------------------------------------------
        
        ------------------------------------------------------------------------------
          Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
        -----------------------------+------------------------------------------------
        schoolid: Unstructured       |
                            var(ses) |      12.13       2.12          8.61       17.10
                          var(_cons) |       4.90      82.77          0.00    1.14e+15
                      cov(ses,_cons) |       6.75      31.24        -54.47       67.97
        -----------------------------+------------------------------------------------
                       var(Residual) |   10015.68     264.26       9510.90    10547.24
        ------------------------------------------------------------------------------
        LR test vs. linear model: chi2(3) = 4052.81               Prob > chi2 = 0.0000
        
        Note: LR test is conservative and provided only for reference.

The contrast command is used below to test the overall schtype#c.ses interaction. This tests the following null hypothesis:

$H\\tiny 0$:$\beta\\tiny 1$ = $\beta\\tiny 2$ = $\beta\\tiny 3$  

$\beta\\tiny 1$ is the average ses slope for private schools,$\beta\\tiny 2$ is the average ses slope for public schools, and $\beta\\tiny 3$ is the average ses slope for Catholic schools. This test is significant, indicating that the ses slopes differ by schtype.

``` 
  contrast schtype#c.ses
``` 
        Margins: asbalanced
        
        -------------------------------------------------
                      |         df        chi2     P>chi2
        --------------+----------------------------------
        read          |
        schtype#c.ses |          2       11.82     0.0027
        -------------------------------------------------


Let’s create a graph that illustrates the ses slopes by schtype. We do this using the margins command to compute the adjusted means of reading scores as a function of ses and schtype, and then graphing these adjusted means using the marginsplot.

``` 
  margins schtype, at(ses=(20(5)80))
  marginsplot,noci
``` 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.13.7.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Reading score by socioeconomic status and school type</strong></figcaption>
</figure>

Let’s now use the margins command combined with the dydx(ses) option to estimate the ses slope for each of the three different types of schools. 

``` 
  margins,dydx(ses) over(schtype) vsquish 
``` 

        Average marginal effects                                 Number of obs = 2,973
        
        Expression: Linear prediction, fixed portion, predict()
        dy/dx wrt:  ses
        Over:       schtype
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |      dy/dx   std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
        ses          |
             schtype |
            Private  |       4.04       0.67     6.00   0.000         2.72        5.36
             Public  |       2.80       0.67     4.17   0.000         1.48        4.12
           Catholic  |       0.67       0.72     0.93   0.350        -0.74        2.09
        ------------------------------------------------------------------------------


The ses slope is 4.04 for private schools, and this slope is significantly different from 0. The ses slope is 2.80 for public schools, and this slope is also significantly different from 0. The ses slope is 0.67 for Catholic schools, but this slope is not significantly different from 0.

Let’s now use the contrast command to form contrasts among the ses slopes for the three different school types. Let’s compare the ses slope for each group with group 2 (public schools)—that is, comparing private schools with public schools; and comparing Catholic schools with public schools. 

Note that the rb2. contrast operator compares each group with group 2 (that is, public schools).


``` 
  contrast rb2.schtype#c.ses,pveffects nowald
``` 
        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        --------------------------------------------------------------
                              |   Contrast   Std. err.      z    P>|z|
        ----------------------+---------------------------------------
        read                  |
                schtype#c.ses |
         (Private vs Public)  |       1.24       0.95     1.30   0.192
        (Catholic vs Public)  |      -2.13       0.99    -2.16   0.031
        --------------------------------------------------------------

## 4 Example 3: Categorical by continuous interaction

The aim of this study is to look at gender differences in math performance and to determine if smaller class sizes are associated with smaller gender differences in math scores. In other words, the aim is to determine if there is a cross-level interaction between gender (a level-1 predictor) and class size (a level-2 predictor).

``` 
  use school_math.dta
  summarize
  list in 1/5
``` 

The mixed command is used to predict math from gender, clsize, and the interaction of these variables. The variable gender is specified as a random coefficient at the school level.


``` 
  mixed math i.gender##c.clsize ||schoolid:gender,covariance(un) nolog
``` 

        Mixed-effects ML regression                          Number of obs    =  2,926
        Group variable: schoolid                             Number of groups =    100
                                                             Obs per group:
                                                                          min =      8
                                                                          avg =   29.3
                                                                          max =     52
                                                             Wald chi2(3)     =  59.38
        Log likelihood = -17691.732                          Prob > chi2      = 0.0000
        
        -----------------------------------------------------------------------------------
                     math | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        ------------------+----------------------------------------------------------------
                   gender |
                  Female  |      13.83      12.41     1.11   0.265       -10.49       38.16
                   clsize |      -1.34       0.32    -4.15   0.000        -1.97       -0.71
                          |
          gender#c.clsize |
                  Female  |      -1.10       0.48    -2.30   0.021        -2.04       -0.16
                          |
                    _cons |     451.94       8.34    54.18   0.000       435.59      468.29
        -----------------------------------------------------------------------------------
        
        ------------------------------------------------------------------------------
          Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
        -----------------------------+------------------------------------------------
        schoolid: Unstructured       |
                         var(gender) |     244.79     231.45         38.37     1561.80
                          var(_cons) |      35.46      96.33          0.17     7273.92
                   cov(gender,_cons) |      78.98     115.00       -146.42      304.37
        -----------------------------+------------------------------------------------
                       var(Residual) |   10273.41     277.07       9744.46    10831.07
        ------------------------------------------------------------------------------
        LR test vs. linear model: chi2(3) = 15.08                 Prob > chi2 = 0.0018
        
        Note: LR test is conservative and provided only for reference.

To help interpret this effect, we can graph the results using the margins and marginsplot commands 

``` 
  margins gender, at(clsize=(15(5)40))
  marginsplot,noci
``` 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.13.8.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong> Math scores by gender and average class size</strong></figcaption>
</figure>

Let’s assess the significance of the gender difference for class sizes ranging from 15 to 40 in five-student increments using the margins command below.

``` 
  margins r.gender,at(clsize=(15(5)40)) contrast(pveffects nowald) vsquish
``` 

        Contrasts of adjusted predictions                        Number of obs = 2,926
        
        Expression: Linear prediction, fixed portion, predict()
        1._at: clsize = 15
        2._at: clsize = 20
        3._at: clsize = 25
        4._at: clsize = 30
        5._at: clsize = 35
        6._at: clsize = 40
        
        ------------------------------------------------------------
                            |            Delta-method
                            |   Contrast   std. err.      z    P>|z|
        --------------------+---------------------------------------
                 gender@_at |
        (Female vs Male) 1  |      -2.72       6.10    -0.45   0.656
        (Female vs Male) 2  |      -8.24       4.61    -1.79   0.074
        (Female vs Male) 3  |     -13.76       4.10    -3.35   0.001
        (Female vs Male) 4  |     -19.27       4.88    -3.95   0.000
        (Female vs Male) 5  |     -24.79       6.51    -3.81   0.000
        (Female vs Male) 6  |     -30.31       8.51    -3.56   0.000
        ------------------------------------------------------------

The margins command is repeated below for 15 to 40 students per class in onestudent increments (the output is omitted to save space). The marginsplot command is then used to visualize the gender differences (with a confidence interval) across the entire spectrum of class sizes 

``` 
  margins r.gender,at(clsize=(15(1)40)) contrast(effects)
  marginsplot,recastci(rarea) ciopts(fcolor(%20)) yline(0)
``` 

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.13.9.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Gender difference in reading score by average class size</strong></figcaption>
</figure>

## 5 Example 4: Categorical by continuous interaction

This study focuses on gender differences in standardized science scores, and whether such differences vary by school size. In this study, each school is classified into one of three sizes: small, medium, or large. Thus, the focus of this study is on the cross-level interaction of gender by school size, where both gender and school size are categorical variables.

``` 
  use school_science.dta
  summarize
  list in 1/5
  mixed science i.gender##i.schsize || schoolid: gender,covariance(un) nolog noheader
``` 

        -----------------------------------------------------------------------------------
                  science | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
        ------------------+----------------------------------------------------------------
                   gender |
                  Female  |     -19.65       6.65    -2.96   0.003       -32.68       -6.62
                          |
                  schsize |
                  Medium  |      18.56       7.36     2.52   0.012         4.13       32.99
                   Large  |      40.16       7.20     5.58   0.000        26.06       54.27
                          |
           gender#schsize |
           Female#Medium  |       1.47       9.54     0.15   0.878       -17.23       20.17
            Female#Large  |      22.24       9.29     2.39   0.017         4.03       40.45
                          |
                    _cons |     394.97       5.14    76.89   0.000       384.90      405.04
        -----------------------------------------------------------------------------------
        
        ------------------------------------------------------------------------------
          Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
        -----------------------------+------------------------------------------------
        schoolid: Unstructured       |
                         var(gender) |      38.52     205.24          0.00    1.32e+06
                          var(_cons) |     155.78     115.88         36.25      669.43
                   cov(gender,_cons) |      17.52     128.51       -234.35      269.38
        -----------------------------+------------------------------------------------
                       var(Residual) |    9772.30     271.33       9254.71    10318.83
        ------------------------------------------------------------------------------
        LR test vs. linear model: chi2(3) = 12.15                 Prob > chi2 = 0.0069
        
        Note: LR test is conservative and provided only for reference.

The contrast command is used to test the overall gender#schsize interaction. This test is significant.

``` 
  contrast gender#schsize 
``` 

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        --------------------------------------------------
                       |         df        chi2     P>chi2
        ---------------+----------------------------------
        science        |
        gender#schsize |          2        7.17     0.0278
        --------------------------------------------------
        

``` 
  contrast gender#schsize 
``` 

To help interpret this interaction, we can use the margins command to display the adjusted mean of science scores as a function of gender and schsize


``` 
  margins schsize#gender,nopvalues vsquish  
  marginsplot,noci
``` 

        Adjusted predictions                                     Number of obs = 2,764
        
        Expression: Linear prediction, fixed portion, predict()
        
        ----------------------------------------------------------------
                       |            Delta-method
                       |     Margin   std. err.     [95% conf. interval]
        ---------------+------------------------------------------------
        schsize#gender |
           Small#Male  |     394.97       5.14        384.90      405.04
         Small#Female  |     375.32       5.46        364.62      386.03
          Medium#Male  |     413.53       5.27        403.20      423.86
        Medium#Female  |     395.36       5.56        384.45      406.26
           Large#Male  |     435.14       5.04        425.26      445.01
         Large#Female  |     437.73       5.36        427.23      448.23
        ----------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.13.10.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Science scores by gender and school size</strong></figcaption>
</figure>

One way to further understand this interaction is by testing the simple effect of gender for each school size. This is performed using the contrast command, as shown below. 

``` 
  contrast gender@schsize,nowald pveffects
``` 

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -----------------------------------------------------------------
                                 |   Contrast   Std. err.      z    P>|z|
        -------------------------+---------------------------------------
        science                  |
                  gender@schsize |
         (Female vs base) Small  |     -19.65       6.65    -2.96   0.003
        (Female vs base) Medium  |     -18.18       6.84    -2.66   0.008
         (Female vs base) Large  |       2.59       6.49     0.40   0.690
        -----------------------------------------------------------------

We can also further probe the interaction by using partial interaction tests. Let’s apply the ar. contrast operator to school size (comparing each group with the previous group) and interact that with gender.


``` 
  contrast gender#ar.schsize,nowald pveffects
``` 

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------------------------------
                                            |   Contrast   Std. err.      z    P>|z|
        ------------------------------------+---------------------------------------
        science                             |
                             gender#schsize |
        (Female vs base) (Medium vs Small)  |       1.47       9.54     0.15   0.878
        (Female vs base) (Large vs Medium)  |      20.77       9.43     2.20   0.028
        ----------------------------------------------------------------------------

