# Chapter19 ：Complex survey data


**The chapter briefly illustrates the mechanics of using these commands in the context of a complex survey**

The example dataset used in this chapter is the nhanes2.dta dataset. This is one of the Stata example datasets and is used via the Internet with the webuse command, shown below.


```
 webuse nhanes2
 svyset
```
The svyset command has already been used to declare the design for this survey, naming the primary sampling unit, the person weight, and the strata.


        Sampling weights: finalwgt
                     VCE: linearized
             Single unit: missing
                Strata 1: strata
         Sampling unit 1: psu
                   FPC 1: <zero>

Let’s now perform a regression analysis using this dataset. Let’s predict systolic blood pressure from the person’s age (in six age groups), sex, and weight. **We use the svy prefix before the regress command to account for the survey design as specified by the svyset command.**

```
 svy:regress bpsystol i.agegrp i.sex c.weight
```

        Survey: Linear regression
        
        Number of strata = 31                            Number of obs   =      10,351
        Number of PSUs   = 62                            Population size = 117,157,513
                                                         Design df       =          31
                                                         F(7, 25)        =      328.16
                                                         Prob > F        =      0.0000
                                                         R-squared       =      0.3087
        
        ------------------------------------------------------------------------------
                     |             Linearized
            bpsystol | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
        -------------+----------------------------------------------------------------
              agegrp |
              30–39  |       1.20       0.57     2.11   0.043         0.04        2.37
              40–49  |       6.88       0.72     9.57   0.000         5.41        8.35
              50–59  |      16.04       0.71    22.44   0.000        14.58       17.49
              60–69  |      23.38       0.77    30.26   0.000        21.81       24.96
                70+  |      30.28       0.90    33.83   0.000        28.46       32.11
                     |
                 sex |
             Female  |      -0.65       0.54    -1.20   0.239        -1.75        0.45
              weight |       0.43       0.02    24.85   0.000         0.39        0.46
               _cons |      88.07       1.32    66.49   0.000        85.36       90.77
        ------------------------------------------------------------------------------
        
We can use the contrast, pwcompare, margins, and marginsplot commands to interpret these results. The use of these commands is briefly illustrated below.

**The contrast command can be used to make comparisons among the groups formed by a factor variable.** The contrast command below tests the equality of the adjusted means for the six age groups. The test shows that the average systolic blood pressure is not equal among the six age groups.

```
 contrast agegrp
```

        Contrasts of marginal linear predictions
        
                                                                        Design df = 31
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df           F        P>F
        -------------+----------------------------------
              agegrp |          5      297.86     0.0000
              Design |         31
        ------------------------------------------------
        Note: F statistics are adjusted for the survey
              design.

The output of the contrast command indicates the $F$ test is adjusted for the survey design. **If you wanted to omit the adjustment for the design degrees of freedom, youcould add the nosvyadjust option**, as shown below. (See [R] contrast for more details about this option.)

```
 contrast agegrp,nosvyadjust
```

        Contrasts of marginal linear predictions
        
                                                                        Design df = 31
        
        Margins: asbalanced
        
        ------------------------------------------------
                     |         df           F        P>F
        -------------+----------------------------------
              agegrp |          5      341.99     0.0000
                     |
              Design |         31
        ------------------------------------------------


The pwcompare command can also be used to form pairwise comparisons among the different age groups. In the example below, **the mcompare(sidak) option is included to adjust for multiple comparisons.**

```
 pwcompare agegrp,pveffects mcompare(sidak)
```

        Pairwise comparisons of marginal linear predictions
        
                                                                        Design df = 31
        
        Margins: asbalanced
        
        ---------------------------
                     |    Number of
                     |  comparisons
        -------------+-------------
              agegrp |           15
        ---------------------------
        
        --------------------------------------------------------
                        |                              Sidak
                        |   Contrast   Std. err.      t    P>|t|
        ----------------+---------------------------------------
                 agegrp |
        30–39 vs 20–29  |       1.20       0.57     2.11   0.482
        40–49 vs 20–29  |       6.88       0.72     9.57   0.000
        50–59 vs 20–29  |      16.04       0.71    22.44   0.000
        60–69 vs 20–29  |      23.38       0.77    30.26   0.000
          70+ vs 20–29  |      30.28       0.90    33.83   0.000
        40–49 vs 30–39  |       5.68       0.65     8.76   0.000
        50–59 vs 30–39  |      14.83       0.76    19.51   0.000
        60–69 vs 30–39  |      22.18       0.82    27.13   0.000
          70+ vs 30–39  |      29.08       1.00    29.06   0.000
        50–59 vs 40–49  |       9.16       0.89    10.30   0.000
        60–69 vs 40–49  |      16.50       0.95    17.33   0.000
          70+ vs 40–49  |      23.40       0.96    24.44   0.000
        60–69 vs 50–59  |       7.35       0.85     8.60   0.000
          70+ vs 50–59  |      14.24       0.75    19.06   0.000
          70+ vs 60–69  |       6.90       1.02     6.76   0.000
        --------------------------------------------------------
        
We can use the margins command to estimate the adjusted means by agegrp, as shown below. **Note that I include the vce(unconditional) option to account for the sampling of covariates not fixed with the at() option. The estimates of the adjusted means are not altered by including this option, but the standard errors account for the fact that the values of the covariates were randomly sampled.** (See [R] margins for more details about this option.)

```
 margins agegrp,nopvalues vce(unconditional)
```

        Predictive margins
        
        Number of strata = 31                            Number of obs   =      10,351
        Number of PSUs   = 62                            Population size = 117,157,513
                                                         Design df       =          31
        
        Expression: Linear prediction, predict()
        
        --------------------------------------------------------------
                     |             Linearized
                     |     Margin   std. err.     [95% conf. interval]
        -------------+------------------------------------------------
              agegrp |
              20–29  |     118.34       0.61        117.10      119.58
              30–39  |     119.54       0.59        118.34      120.75
              40–49  |     125.22       0.77        123.64      126.80
              50–59  |     134.38       0.91        132.52      136.23
              60–69  |     141.72       0.88        139.93      143.52
                70+  |     148.62       1.09        146.40      150.84
        --------------------------------------------------------------


The marginsplot command can be used to graph the adjusted means and confidence intervals computed by the margins command.

```
 marginsplot
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.14.27.png" style="width:750px;height:450px;" alt="图片描述">
  <figcaption><strong>Adjusted means of systolic blood pressure by age group</strong></figcaption>
</figure>

When you use the svy prefix with an estimation command, the contrast, pwcompare, and margins commands compute appropriate estimates and standard errors based on the survey design. For more information, see [SVY] svy.
