# Chapter9 ：Categorical by categorical by categorical interactions


This chapter illustrates models involving interactions of three categorical variables, with an emphasis on how to interpret the interaction of the three categorical variables.

This chapter focuses on three types of interactions: 
1. **two by two by two model** 
2. **two by two by three model**
3. **three by three by four model**

## 1 Two by two by two models

a new study using a two by two by two model in which treatment has two levels (control group versus happiness therapy), depression status has two levels (nondepressed versus mildly depressed), and season has two levels (winter and summer).

```
  opt-2by2by2.dta
  anova opt depstat##treat##season
```
    

                         Number of obs =        240    R-squared     =  0.5677
                         Root MSE      =    8.01794    Adj R-squared =  0.5546

                  Source | Partial SS         df         MS        F    Prob>F
    ---------------------+----------------------------------------------------
                   Model |  19584.517          7   2797.7881     43.52  0.0000
                         |
                 depstat |  1601.6667          1   1601.6667     24.91  0.0000
                   treat |  13470.017          1   13470.017    209.53  0.0000
           depstat#treat |  35.266667          1   35.266667      0.55  0.4596
                  season |  3713.0667          1   3713.0667     57.76  0.0000
          depstat#season |  220.41667          1   220.41667      3.43  0.0653
            treat#season |  112.06667          1   112.06667      1.74  0.1880
    depstat#treat#season |  432.01667          1   432.01667      6.72  0.0101
                         |
                Residual |  14914.667        232   64.287356  
    ---------------------+----------------------------------------------------
                   Total |  34499.183        239   144.34805  

>Note! Three-way interaction shortcut
>>**Specifying depstat##treat##season is a shortcut for specifying all main effects, two-way interactions, and the three-way interaction of depstat, treat, and season.** This both saves time and helps ensure that you include all lower order effects. Even if not significant, these lower order effects should be included in the model.

The depstat#treat#season interaction is significant ($F = 6.72$,$p = 0.0101$). Let’s use the margins command to show the mean of optimism broken down by these three factors.
     
```
  margins depstat#treat#season,nopvalues
```
        Adjusted predictions                                       Number of obs = 240
        
        Expression: Linear prediction, predict()
        
        ----------------------------------------------------------------------
                             |            Delta-method
                             |     Margin   std. err.     [95% conf. interval]
        ---------------------+------------------------------------------------
        depstat#treat#season |
             Non#Con#Winter  |      44.40       1.46         41.52       47.28
             Non#Con#Summer  |      51.67       1.46         48.78       54.55
              Non#HT#Winter  |      59.93       1.46         57.05       62.82
              Non#HT#Summer  |      64.57       1.46         61.68       67.45
            Mild#Con#Winter  |      39.23       1.46         36.35       42.12
            Mild#Con#Summer  |      44.97       1.46         42.08       47.85
             Mild#HT#Winter  |      50.93       1.46         48.05       53.82
             Mild#HT#Summer  |      64.77       1.46         61.88       67.65
        ----------------------------------------------------------------------


Let’s then use the marginsplot command to make a graph of the means, showing treat on the $x$ axis and the different seasons in separate panels.

```
  marginsplot,xdimension(treat) bydimension(season)noci
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.8.1.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Optimism by treatment, depression status, and season</strong></figcaption>
</figure>

### 1.1 Simple interactions by season

One way that we can dissect the three-way interaction is by looking at the simple interactions of treatment by depression status at each season. 

Looking at figure，it appears that the interaction is not significant during the winter and is significant during the summer. We test this using the contrast command, which tests the treat#depstat interaction at each level of season.

```
  contrast treat#depstat@season 
```
        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        --------------------------------------------------------
                             |         df           F        P>F
        ---------------------+----------------------------------
        treat#depstat@season |
                     Winter  |          1        1.71     0.1917
                     Summer  |          1        5.55     0.0193
                      Joint  |          2        3.63     0.0279
                             |
                 Denominator |        232
        --------------------------------------------------------

### 1.2 Simple interactions by depression status

Another way to dissect this three-way interaction is by looking at the simple interaction of treatment by season at each level of depression status.

To visualize this, let’s rerun the margins command and then use the marginsplot command to graph the means showing treat on the $x$ axis and separate panels for those who are nondepressed and mildly depressed. 

```
  margins depstat#treat#season
  marginsplot,xdimension(treat)bydimension(depstat) noci 
```

        Adjusted predictions                                       Number of obs = 240
        
        Expression: Linear prediction, predict()
        
        --------------------------------------------------------------------------------------
                             |            Delta-method
                             |     Margin   std. err.      t    P>|t|     [95% conf. interval]
        ---------------------+----------------------------------------------------------------
        depstat#treat#season |
             Non#Con#Winter  |      44.40       1.46    30.33   0.000        41.52       47.28
             Non#Con#Summer  |      51.67       1.46    35.29   0.000        48.78       54.55
              Non#HT#Winter  |      59.93       1.46    40.94   0.000        57.05       62.82
              Non#HT#Summer  |      64.57       1.46    44.11   0.000        61.68       67.45
            Mild#Con#Winter  |      39.23       1.46    26.80   0.000        36.35       42.12
            Mild#Con#Summer  |      44.97       1.46    30.72   0.000        42.08       47.85
             Mild#HT#Winter  |      50.93       1.46    34.79   0.000        48.05       53.82
             Mild#HT#Summer  |      64.77       1.46    44.24   0.000        61.88       67.65
        --------------------------------------------------------------------------------------
        

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.8.2.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Optimism by treatment, season, and depression status</strong></figcaption>
</figure>

There is an interaction of treatment by season for those who are mildly depressed, but no such interaction for those who are not depressed. We can test the interaction of treatment by season at each level of depression status using the following contrast command:


```
  contrast treat#season@depstat
```
        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        --------------------------------------------------------
                             |         df           F        P>F
        ---------------------+----------------------------------
        treat#season@depstat |
                        Non  |          1        0.81     0.3693
                       Mild  |          1        7.65     0.0061
                      Joint  |          2        4.23     0.0157
                             |
                 Denominator |        232
        --------------------------------------------------------

### 1.3 Simple effects

We might want to know whether the effect of happiness therapy is significant for each combination of season and depression status. 


```
  contrast treat@season#depstat,nowald pveffects
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------------------------
                                  |   Contrast   Std. err.      t    P>|t|
        --------------------------+---------------------------------------
             treat@season#depstat |
         (HT vs base) Winter#Non  |      15.53       2.07     7.50   0.000
        (HT vs base) Winter#Mild  |      11.70       2.07     5.65   0.000
         (HT vs base) Summer#Non  |      12.90       2.07     6.23   0.000
        (HT vs base) Summer#Mild  |      19.80       2.07     9.56   0.000
        ------------------------------------------------------------------
        
The mean optimism for those in the happiness therapy group is always greater than the control group at each level of season and at each level of depression status. 


## 2 Two by two by three models

Let’s now consider an example with three factors, two of which have two levels and one of which has three levels. In this example, the treatment variable now has three levels: 1.control group, 2. traditional therapy, and 3. happiness therapy.


```
  use opt-3by2by2.dta
```

Let’s now use the anova command to predict opt from depstat, treat, season, all two-way interactions of these variables, and the three-way interaction.

```
  anova opt depstat##treat##season
```

        
                                 Number of obs =        360    R-squared     =  0.4912
                                 Root MSE      =     7.9879    Adj R-squared =  0.4751
        
                          Source | Partial SS         df         MS        F    Prob>F
            ---------------------+----------------------------------------------------
                           Model |  21435.233         11   1948.6576     30.54  0.0000
                                 |
                         depstat |  2423.2111          1   2423.2111     37.98  0.0000
                           treat |  13811.217          2   6905.6083    108.23  0.0000
                   depstat#treat |  3.9055556          2   1.9527778      0.03  0.9699
                          season |     3960.1          1      3960.1     62.06  0.0000
                  depstat#season |  253.34444          1   253.34444      3.97  0.0471
                    treat#season |  469.11667          2   234.55833      3.68  0.0263
            depstat#treat#season |  514.33889          2   257.16944      4.03  0.0186
                                 |
                        Residual |  22204.667        348   63.806513  
            ---------------------+----------------------------------------------------
                           Total |    43639.9        359   121.55961  
        
The three-way interaction is significant ( $F = 4.03$ , $p = 0.0186$ ). Let’s use the margins and marginsplot commands to display and graph the means by each of these categorical variables. 

```
  margins depstat#treat#season,nopvalues
  marginsplot,bydimension(depstat) noci
```

        Adjusted predictions                                       Number of obs = 360
        
        Expression: Linear prediction, predict()
        
        -----------------------------------------------------------------------
                              |            Delta-method
                              |     Margin   std. err.     [95% conf. interval]
        ----------------------+------------------------------------------------
         depstat#treat#season |
         Non#Con#Winter (S1)  |      44.70       1.46         41.83       47.57
         Non#Con#Summer (S2)  |      49.70       1.46         46.83       52.57
          Non#TT#Winter (S1)  |      54.33       1.46         51.46       57.20
          Non#TT#Summer (S2)  |      59.40       1.46         56.53       62.27
          Non#HT#Winter (S1)  |      59.77       1.46         56.90       62.64
          Non#HT#Summer (S2)  |      64.57       1.46         61.70       67.44
        Mild#Con#Winter (S1)  |      39.63       1.46         36.76       42.50
        Mild#Con#Summer (S2)  |      44.20       1.46         41.33       47.07
         Mild#TT#Winter (S1)  |      49.23       1.46         46.36       52.10
         Mild#TT#Summer (S2)  |      54.70       1.46         51.83       57.57
         Mild#HT#Winter (S1)  |      49.33       1.46         46.46       52.20
         Mild#HT#Summer (S2)  |      64.23       1.46         61.36       67.10
        -----------------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.1.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Optimism by treatment, season, and depression status</strong></figcaption>
</figure>


### 2.1 Simple interactions by depression status

It appears that the treat#season interaction might not be significant for those who are not depressed (see the left panel of figure) but might be significant for those who are mildly depressed (see the right panel of figure ). We can explore this by assessing the treat#season interaction at each level of depstat using the contrastcommand below.

```
  contrast treat#season@depstat
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        --------------------------------------------------------
                             |         df           F        P>F
        ---------------------+----------------------------------
        treat#season@depstat |
                        Non  |          2        0.00     0.9955
                       Mild  |          2        7.70     0.0005
                      Joint  |          4        3.85     0.0044
                             |
                 Denominator |        348
        --------------------------------------------------------
        

Let’s further dissect this simple interaction by applying contrasts to the treatment
factor through the use of simple partial interactions.

### 2.2 Simple partial interaction by depression status

Say that we want to form two comparisons with respect to treat, comparing group 2 versus 1 (traditional therapy versus control group) and comparing group 3 versus 2 (happiness therapy versus traditional therapy). 

The interaction of treatment (traditional therapy versus control group) by season for those who are mildly depressed is visualized in the left panel of figure below, and the interaction of treatment (happiness therapy versus traditional therapy) by season for those who are mildly depressed is visualized in the right panel of figure below.



<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.2.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Simple partial interactions</strong></figcaption>
</figure>

To understand this, let’s break it into two parts. The first part, ar.treat#season, creates the interactions of treatment (traditional therapy versus control group) by season, and treatment (happiness therapy versus traditional therapy) by season. The second part, @2.depstat indicates the contrasts will be performed only for level 2 of depstat (the mildly depressed group)

```
  contrast ar.treat#season@2.depstat
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -------------------------------------------------------------
                                  |         df           F        P>F
        --------------------------+----------------------------------
             treat#season@depstat |
        (TT vs Con) (joint) Mild  |          1        0.10     0.7578
         (HT vs TT) (joint) Mild  |          1       10.46     0.0013
                           Joint  |          2        7.70     0.0005
                                  |
                      Denominator |        348
        -------------------------------------------------------------


Let’s now further understand this simple partial interaction through the use of
simple contrasts.

### 2.3 Simple contrasts

In particular, let’s ask whether there is a difference between happiness therapy and traditional therapy separately for each season focusing only on those who are mildly depressed. We can perform this test by specifying ar3.treat@season#2.depstat on the contrast command.

Let’s break this into two parts. The first part, ar3.treat, requests the comparison of treatment group 3 versus 2 (happiness therapy versus traditional therapy). 

The second part, @season#2.depstat, requests that the contrasts be performed at each level of season and at level 2 of depression status (mildly depressed)

```
  contrast ar3.treat@season#2.depstat,nowald pveffects
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ---------------------------------------------------------------------
                                     |   Contrast   Std. err.      t    P>|t|
        -----------------------------+---------------------------------------
                treat@season#depstat |
        (HT vs TT) Winter (S1)#Mild  |       0.10       2.06     0.05   0.961
        (HT vs TT) Summer (S2)#Mild  |       9.53       2.06     4.62   0.000
        ---------------------------------------------------------------------


### 2.4 Partial interactions

Say that we wanted to further understand this interaction by applying adjacent group contrasts to the treatment factor. These contrasts compare group 2 versus 1 (traditional therapy versus control group) and group 3 versus 2 (happiness therapy versus traditional therapy). We could interact these contrasts with season and depression status. 

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.3.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong> Simple partial interaction: T2 vs. T1 by season by depstat</strong></figcaption>
</figure>


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.4.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Simple partial interaction: T3 vs. T2 by season by depstat</strong></figcaption>
</figure>

We can test each of these partial interactions using the contrast command below.


```
  contrast ar.treat#season#depstat
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ----------------------------------------------------------------
                                     |         df           F        P>F
        -----------------------------+----------------------------------
                treat#season#depstat |
        (TT vs Con) (joint) (joint)  |          1        0.04     0.8400
         (HT vs TT) (joint) (joint)  |          1        5.53     0.0193
                              Joint  |          2        4.03     0.0186
                                     |
                         Denominator |        348
        ----------------------------------------------------------------

## 3  Three by three by three models and beyond

Let’s consider an extension of the example involving the factors treat, depstat, and season, except that these factors have three, three and four levels, respectively. 

The three levels of treat are 1) control group, 2) traditional therapy, and 3) happiness therapy. 

The three levels of depstat are 1) nondepressed, 2) mildly depressed, and 3) severely depressed. 

The four levels of season are 1) winter, 2) spring, 3) summer, and 4)fall.

```
  use opt-3by3by4.dta
  anova opt depstat##treat##season
```

                                 Number of obs =      1,080    R-squared     =  0.6172
                                 Root MSE      =    6.00663    Adj R-squared =  0.6043
        
                          Source | Partial SS         df         MS        F    Prob>F
            ---------------------+----------------------------------------------------
                           Model |    60718.9         35   1734.8257     48.08  0.0000
                                 |
                         depstat |  18782.039          2   9391.0194    260.29  0.0000
                           treat |  34636.039          2   17318.019    480.00  0.0000
                   depstat#treat |  1702.0056          4   425.50139     11.79  0.0000
                          season |  3522.8333          3   1174.2778     32.55  0.0000
                  depstat#season |  1004.2722          6    167.3787      4.64  0.0001
                    treat#season |  185.11667          6   30.852778      0.86  0.5275
            depstat#treat#season |  886.59444         12    73.88287      2.05  0.0179
                                 |
                        Residual |  37667.067      1,044   36.079566  
            ---------------------+----------------------------------------------------
                           Total |  98385.967      1,079   91.182546  

The three-way interaction of depstat#treat#season is significant ( $F = 2.05$ , $p=0.0179$ ). 

To begin to understand the nature of this interaction, let’s first graph the interaction using the margins and marginsplot commands. 

```
  margins depstat#treat#season
  marginsplot,xdimension(treat)bydimension(season) noci legend(rows(1))
```

        Adjusted predictions                                     Number of obs = 1,080
        
        Expression: Linear prediction, predict()
        
        --------------------------------------------------------------------------------------
                             |            Delta-method
                             |     Margin   std. err.      t    P>|t|     [95% conf. interval]
        ---------------------+----------------------------------------------------------------
        depstat#treat#season |
             Non#Con#Winter  |      44.63       1.10    40.70   0.000        42.48       46.79
             Non#Con#Spring  |      47.53       1.10    43.34   0.000        45.38       49.69
             Non#Con#Summer  |      49.70       1.10    45.32   0.000        47.55       51.85
               Non#Con#Fall  |      47.83       1.10    43.62   0.000        45.68       49.99
              Non#TT#Winter  |      54.30       1.10    49.51   0.000        52.15       56.45
              Non#TT#Spring  |      57.33       1.10    52.28   0.000        55.18       59.49
              Non#TT#Summer  |      60.37       1.10    55.05   0.000        58.21       62.52
                Non#TT#Fall  |      57.03       1.10    52.01   0.000        54.88       59.19
              Non#HT#Winter  |      61.30       1.10    55.90   0.000        59.15       63.45
              Non#HT#Spring  |      62.70       1.10    57.17   0.000        60.55       64.85
              Non#HT#Summer  |      64.70       1.10    59.00   0.000        62.55       66.85
                Non#HT#Fall  |      62.87       1.10    57.33   0.000        60.71       65.02
            Mild#Con#Winter  |      39.60       1.10    36.11   0.000        37.45       41.75
            Mild#Con#Spring  |      42.73       1.10    38.97   0.000        40.58       44.89
            Mild#Con#Summer  |      44.50       1.10    40.58   0.000        42.35       46.65
              Mild#Con#Fall  |      42.73       1.10    38.97   0.000        40.58       44.89
             Mild#TT#Winter  |      49.17       1.10    44.83   0.000        47.01       51.32
             Mild#TT#Spring  |      52.20       1.10    47.60   0.000        50.05       54.35
             Mild#TT#Summer  |      54.10       1.10    49.33   0.000        51.95       56.25
               Mild#TT#Fall  |      52.03       1.10    47.45   0.000        49.88       54.19
             Mild#HT#Winter  |      51.20       1.10    46.69   0.000        49.05       53.35
             Mild#HT#Spring  |      56.37       1.10    51.40   0.000        54.21       58.52
             Mild#HT#Summer  |      65.33       1.10    59.58   0.000        63.18       67.49
               Mild#HT#Fall  |      56.17       1.10    51.22   0.000        54.01       58.32
          Severe#Con#Winter  |      36.67       1.10    33.44   0.000        34.51       38.82
          Severe#Con#Spring  |      39.67       1.10    36.17   0.000        37.51       41.82
          Severe#Con#Summer  |      39.60       1.10    36.11   0.000        37.45       41.75
            Severe#Con#Fall  |      39.97       1.10    36.44   0.000        37.81       42.12
           Severe#TT#Winter  |      47.20       1.10    43.04   0.000        45.05       49.35
           Severe#TT#Spring  |      50.23       1.10    45.81   0.000        48.08       52.39
           Severe#TT#Summer  |      49.23       1.10    44.89   0.000        47.08       51.39
             Severe#TT#Fall  |      50.00       1.10    45.59   0.000        47.85       52.15
           Severe#HT#Winter  |      46.67       1.10    42.55   0.000        44.51       48.82
           Severe#HT#Spring  |      49.77       1.10    45.38   0.000        47.61       51.92
           Severe#HT#Summer  |      48.57       1.10    44.29   0.000        46.41       50.72
             Severe#HT#Fall  |      50.20       1.10    45.78   0.000        48.05       52.35
        --------------------------------------------------------------------------------------
        


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.5.png" style="width:650px;height:350px;" alt="图片描述">
  <figcaption><strong>Optimism by treatment, season, and depression status</strong></figcaption>
</figure>

### 3.1 Partial interactions and interaction contrasts

Let’s explore ways to dissect the three-way interaction of depstat#treat#season by applying contrasts to one or more of the factors. 

Let’s begin by testing whether the interaction of treat#depstat is the same for each season compared with winter (season 1). This is performed by applying the **r. contrast** operator to season and interacting that with treat and depstat.

```
  contrast r.season#treat#depstat
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -----------------------------------------------------------------------
                                            |         df           F        P>F
        ------------------------------------+----------------------------------
                       season#treat#depstat |
        (Spring vs Winter) (joint) (joint)  |          4        0.46     0.7619
        (Summer vs Winter) (joint) (joint)  |          4        5.36     0.0003
          (Fall vs Winter) (joint) (joint)  |          4        0.40     0.8115
                                     Joint  |         12        2.05     0.0179
                                            |
                                Denominator |       1044
        -----------------------------------------------------------------------
        
>Note! Fall versus spring
>>A custom contrast is applied to season to obtain the comparison of fall versus spring (season 4 versus 2).[ contrast {season 0 -1 0 1}#treat#depstat ]

The contrast command below performs this comparison by applying the r3. contrast to season to compare summer with winter (season 3 versus 1) and the r2. contrast on depstat to compare those who are mildly depressed with those who are nondepressed (levels 2 versus 1). These terms are all interacted (that is, r3.season#r2.depstat#treat) yielding a test of summer versus winter (season 3 versus 1) by mildly depressed versus nondepressed (levels 2 versus 1) by treatment.

```
  *Season(winter vs. summer) by depstat(nondepressed vs. mildly depressed)by treat
  contrast r3.season#r2.depstat#treat
```
        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        --------------------------------------------------------
                             |         df           F        P>F
        ---------------------+----------------------------------
        season#depstat#treat |          2        9.03     0.0001
                             |
                 Denominator |       1044
        --------------------------------------------------------
        


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.6.png" style="width:650px;height:350px;" alt="图片描述">
  <figcaption><strong>Interaction contrast of season (winter versus summer) by depression status (mildly depressed versus nondepressed) by treatment</strong></figcaption>
</figure>


This significant test indicates that the two-way interaction formed by interacting depression status (nondepressed versus mildly depressed) by treatment differs by season (winter versus summer)

Say that we wanted to take this test and focus on the contrast of happiness therapy versus traditional therapy (group 3 versus 2).1 This yields an interaction of season (winter versus summer) by depression status (nondepressed versus mildly depressed) by treatment (happiness therapy versus traditional therapy).

```
  *season(winter vs. summer) by depstat (nondepressed vs mildly depressed) by treat(HT vs. TT)
  contrast r3.season#r2.depstat#r3b2.treat,noeffects
```
        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        --------------------------------------------------------
                             |         df           F        P>F
        ---------------------+----------------------------------
        season#depstat#treat |          1       14.64     0.0001
                             |
                 Denominator |       1044
        --------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.7.png" style="width:650px;height:350px;" alt="图片描述">
  <figcaption><strong>Interaction contrast of season (winter versus summer) by depression status (mildly depressed versus nondepressed) by treatment (HT versus TT)</strong></figcaption>
</figure>

### 3.2 Simple interactions

Let’s now explore a different way to dissect the three-way interaction through the use of simple interaction tests. 


```
  contrast depstat#treat@season
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        --------------------------------------------------------
                             |         df           F        P>F
        ---------------------+----------------------------------
        depstat#treat@season |
                     Winter  |          4        3.75     0.0049
                     Spring  |          4        2.29     0.0575
                     Summer  |          4        9.88     0.0000
                       Fall  |          4        2.01     0.0904
                      Joint  |         16        4.48     0.0000
                             |
                 Denominator |       1044
        --------------------------------------------------------
        
The treat#depstat interaction is significant for winter (season 1) and summer (season 3). The treat#depstat interaction is not significant in the spring (season 2) or fall (season 4)

Let’s perform this same contrast command, but apply the r2. contrast to depstat that compares those who are nondepressed versus mildly depressed. This yields four partial interactions of depression status (nondepressed versus mildly depressed) by treatment at each level of season. 


```
  contrast r2.depstat#treat@season
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        -----------------------------------------------------------------
                                      |         df           F        P>F
        ------------------------------+----------------------------------
                 depstat#treat@season |
        (Mild vs Non) (joint) Winter  |          2        3.49     0.0309
        (Mild vs Non) (joint) Spring  |          2        0.27     0.7631
        (Mild vs Non) (joint) Summer  |          2        5.74     0.0033
          (Mild vs Non) (joint) Fall  |          2        0.38     0.6851
                               Joint  |          8        2.47     0.0119
                                      |
                          Denominator |       1044
        -----------------------------------------------------------------
        

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.8.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Optimism by treatment and season focusing on mildly depressed versus nondepressed</strong></figcaption>
</figure>

Let’s also apply the r3b2. contrast to treat, comparing group 3 with group 2 (happiness therapy to traditional therapy). This yields an interaction contrast of depression status (mildly depressed versus nondepressed) by treatment (happiness therapy versus traditional therapy) performed at each of the four seasons. 



```
  contrast r2.depstat#r3b2.treat@season,noeffects  //r2 means non-dep vs mild-dep
```

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        --------------------------------------------------------------------
                                         |         df           F        P>F
        ---------------------------------+----------------------------------
                    depstat#treat@season |
        (Mild vs Non) (HT vs TT) Winter  |          1        5.13     0.0238
        (Mild vs Non) (HT vs TT) Spring  |          1        0.30     0.5844
        (Mild vs Non) (HT vs TT) Summer  |          1        9.90     0.0017
          (Mild vs Non) (HT vs TT) Fall  |          1        0.60     0.4385
                                  Joint  |          4        3.98     0.0033
                                         |
                             Denominator |       1044
        --------------------------------------------------------------------

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.9.9.png" style="width:450px;height:350px;" alt="图片描述">
  <figcaption><strong>Simple interaction contrast of depression status by treatment at each season</strong></figcaption>
</figure>

### 3.3 Simple effects and simple comparisons

We might be interested in focusing on the simple effects of treatment across the levels of season and depression status. 


```
  contrast treat@season#depstat
```
We could further refine this test by focusing on the comparison of happiness therapy versus traditional therapy (group 3 versus 2) by applying the r3b2. contrast operator to treat, as shown below.


```
  contrast r3b2.treat@season#depstat,nowald pveffects
```

>this contrast is specified as r3b2.treat,which indicates to compare group 3 with group2.  This could have also been specified as a custom contrast,{treat 0 -1 1}

        Contrasts of marginal linear predictions
        
        Margins: asbalanced
        
        ------------------------------------------------------------------
                                  |   Contrast   Std. err.      t    P>|t|
        --------------------------+---------------------------------------
             treat@season#depstat |
           (HT vs TT) Winter#Non  |       7.00       1.55     4.51   0.000
          (HT vs TT) Winter#Mild  |       2.03       1.55     1.31   0.190
        (HT vs TT) Winter#Severe  |      -0.53       1.55    -0.34   0.731
           (HT vs TT) Spring#Non  |       5.37       1.55     3.46   0.001
          (HT vs TT) Spring#Mild  |       4.17       1.55     2.69   0.007
        (HT vs TT) Spring#Severe  |      -0.47       1.55    -0.30   0.764
           (HT vs TT) Summer#Non  |       4.33       1.55     2.79   0.005
          (HT vs TT) Summer#Mild  |      11.23       1.55     7.24   0.000
        (HT vs TT) Summer#Severe  |      -0.67       1.55    -0.43   0.667
             (HT vs TT) Fall#Non  |       5.83       1.55     3.76   0.000
            (HT vs TT) Fall#Mild  |       4.13       1.55     2.67   0.008
          (HT vs TT) Fall#Severe  |       0.20       1.55     0.13   0.897
        ------------------------------------------------------------------

This shows that for some combinations of season and depstat, the difference between happiness and traditional therapy is significant. 

For example, in season 1 (winter) and depression status 1 (nondepressed), the difference between happiness and traditional therapy is significant, with happiness therapy yielding optimism scores that are 7 points greater than traditional therapy
