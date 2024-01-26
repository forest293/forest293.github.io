# [Categorical Dependent Var] Chapter_4 ：Methods of interpretation


**introduce methods of interpretation that will be used throughout the rest of the book.**

Models for categorical outcomes are nonlinear, and this nonlinearity is th e fundamental challenge th at must be addressed for effective interpretation. Most simply, this means th at you cannot effectively interpret your model by presenting a list of the estimated parameters. Instead, **we believe that the most effective way to interpret these models is by first fitting the model and then computing and examining postestimation predictions of the outcomes.**


## 1 Comparing linear and nonlinear models

### 1.1 linear models

Consider a linear regression model where $y$ is the dependent variable, $x$ is a continuous independent variable, and $d$ is a binary independent variable. The model is

$$y=\alpha+\beta x+\delta d+\varepsilon $$

Given the usual assumption th at $E(\varepsilon\mid x,d)=0$, it follows that

$$E(y|x,d)=\alpha+\beta x+\delta d$$
    
which is graphed in figure 4.1. The solid line plots $E(\varepsilon\mid x,d)$ as $x$ changes, holding $d$ = 0;that is, $E(y|x,d)=\alpha+\beta x$  ,The dashed line plots $E(\varepsilon\mid x,d)$ as $x$ changes when $d$ = 1, which has the effect of changing the intercept :$E(y|x,d)=\alpha+\beta x+\delta1=(\alpha+\delta)+\beta x.$


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.20.7.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>A simple linear model</strong></figcaption>
</figure>

The effect of $x$ on $y$ can be computed as the partial derivative of $E(\varepsilon\mid x,d)$ with respect to $x$. This is sometimes called the marginal change:

$$\frac{\partial E(y|x,d)}{\partial x}=\frac{\partial\left(\alpha+\beta x+\delta d\right)}{\partial x}=\beta $$

The marginal change is the ratio of the change in the expected value of y to the change in x , when the change in x is infinitely small, holding d constant. In linear models, the marginal change equals the discrete change in  $E(\varepsilon\mid x,d)$ as x changes by one unit, holding other variables constant. In our notation, we indicate that x is changing by a discrete amount with $\Delta x$ using $(x\rightarrow x+1)$ to indicate that x changes from its current value to be 1 larger (for example, from 10 to 11 or from 9.3 to 10.3):

$$\frac{\Delta E(y|x,d)}{\Delta x(x\to x+1)}=\{\alpha+\beta(x+1)+\delta d\}-(\alpha+\beta x+\delta d)=\beta $$

When x increases by 1, $E(\varepsilon\mid x,d)$ increases by $\beta$ regardless of the values for $x$ and $d$ at the point where change is measured. This is shown by the four small triangles in figure above with bases of length 1 and heights of $\beta$.

The effect of d cannot be computed as a partial derivative because $d$ is discrete. Instead, we measure the change in $E(y\mid x,d)$ with a discrete change from 0 to 1 indicated as $\Delta d\left(0\rightarrow1\right)$:

$$\frac{\Delta E(y\mid x,d)}{\Delta d(0\rightarrow1)}=(\begin{array}{c}\alpha+\beta x+\delta1\end{array})-(\begin{array}{c}\alpha+\beta x+\delta0\end{array})=\delta $$

When d changes from 0 to 1,$E(\varepsilon\mid x,d)$ changes by $\delta$ units regardless of the level of $x$. This is shown by the two arrows labeled $\delta$ in figure above marking the distance between the solid and dashed lines.

The distinguishing feature for interpretation in linear models is th at the effect of a given change in an independent variable is the same regardless of the value of that variable at the start of its change and regardless of the level of the other variables in the model. Interpretation only needs to specify which variable is changing, by how much, and that other variables are being held constant. Given the simple structure of linear models, such as regress, most interpretations require only reporting the estimates. There are, however, important exceptions. In our discussion, we assumed that the model does not include polynomial terms such as $x^2$ or interactions such as $xd$. When such terms are included, the linear model becomes nonlinear in the sense we consider in the next section.

### 1.2 Nonlinear models

We use a logit model to illustrate the idea of nonlinearity. Let $y = 1$ if the outcome occurs, say, if a person is in the labor force, and otherwise y = 0. T he curves are from the logit equation

$$\Pr(y=1|x,d)=\frac{\exp{(\alpha+\beta x+\delta d)}}{1+\exp{(\alpha+\beta x+\delta d)}}$$

where the $\alpha$,$\beta$, and $\delta$ parameters in this equation are unrelated to those for the linear model. Once again, $x$ is continuous and $d$ is binary. The model is shown in figure


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.20.8.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>A simple nonlinear model</strong></figcaption>
</figure>


The nonlinearity of the model makes it more difficult to interpret the effects of x and d on the probability of y occurring. For example, neither the marginal change $\partial\Pr\left(y=1|x,d\right)/\partial x$ nor the discrete change $\Delta\Pr\left(y=1\mid x,d\right)/\Delta d(0\rightarrow1)$ are constant, but instead depend on the values of $ x$ and $d$ . Consider the effect of changing d from 0 to 1 for a given value of $x$.  
This effect is the distance between the solid curve for d = 0 and the dashed curve for $d$ = 1. Because the curves are not parallel, the magnitude of the difference in the predicted probability at d = 1 compared with d = 0 depends on the value of x where the difference is computed. Accordingly, $\Delta_{d1}\neq\Delta_{d2}$ Similarly, the magnitude of the effect of $ x$ depends on the values of $x$ and d where the effect is evaluated so that $\Delta_{x1}\neq\Delta_{x2}\neq\Delta_{x3}\neq\Delta_{x4}$ . **In nonlinear models, the effect of a change in a variable depends on the values of all variables in the model and is no longer simply equal to a parameter of the model.** Accordingly, the methods of interpretation that we recommend for nonlinear models are largely based on the use of predictions, which we consider in the next section.


## 2 Approaches to interpretation

The primary methods of interpretation presented in this book are based on predictions from the model. **The model is fit and the estimated parameters are used to make predictions at values of the independent variables that are (hopefully) useful for understanding the substantive implications of the nonlinear model.** These methods depend critically on Stata’s predict and margins commands, which are the foundation for the SPost commands m table, mchange, and mgen (referred to collectively as the m* commands). Although the basic use of these commands is straightforward, they have many—sometimes subtle—features that are valuable for fully interpreting your model. **This chapter provides an overview of general principles and syntax for these commands.** Details on why you would use each feature are explained fully when the commands are used in later chapters to interpret specific models.

### 2.1 Method of interpretation based on predictions
We use predictions in four basic ways

* **Predictions for each observation.** Most fundamentally, predictions can be computed for each observation by using **predict**. Predictions include the probabilities of outcomes as well as rates for count models. We often start our analysis by examining the distribution of predictions in the estimation sample.
* **Predictions at specified values.** Predicted values at specific values of the independent variables can be computed using the commands **margins** and **mtable**. These commands can compute predictions at substantively interesting combinations of values of the independent variables, which we refer to as profiles or ideal types. In some cases, tables of predictions are arranged by the level of one or more explanatory variables and can succinctly summarize processes affecting the outcomes
* **Marginal effects.** An important way to examine the effects of a variable is to compute how changes in the variable are associated with changes in the outcomes, holding other variables constant. These changes, known as marginal effects, can be computed as a marginal change when a regressor changes by an infinitely small amount or as a discrete change when a regressor changes by a fixed amount. Marginal effects are computed by **margins**, **mtable**. and **mchange**. which can easily **compute average marginal effects and marginal effects at the mean.**
* **Graphs of predictions.**For continuous independent variables, graphs often effectively summarize effects. Stata’s m arginsplot elegantly plots a single outcome category based on predictions from margins. Just as m argins can only compute predictions for one outcome at a time, **marginsplot** does not allow you to plot multiple outcomes. Because this is essential for models with nominal and ordinal outcomes, we wrote **mgen** to generate variables with predictions for all outcomes. These variables containing predictions can be plotted using Stata’s **graph** command

### 2.2 Method of interpretation using parameters

Although the predictions used for each of these methods are computed using the model’s estimated param eters, in some cases the parameters themselves can be used for interpretation. Examples include odds ratios for binary models, standardized coefficients for latent outcomes, and factor changes in rates for count models. These are considered in detail in later chapters.

### 2.3 Stata and SPost commands for interpretation

The most fundamentally important command for sophisticated interpretation using predictions is Stata’s 'margins' command. This command is incredibly powerful, flexible, and general. As a consequence, it can be rather intimidating to use. To make 'margins' simpler to use, we wrote a series of “wrappers” that use 'margins' for their computations; they simplify the process of specifying the predictions you want and produce output that is easier to interpret. Nonetheless, there are times when you might need to use 'margins,' either because our commands did not anticipate something you want to do or because we encountered technical issues that made using 'margins' the only option. Accordingly, even if our 'm*' commands seem to do everything you want, you should have some familiarity with what 'margins' does and how it works. This will also give you a better understanding of what our commands are doing.

## 3 Predictions for each observation

The 'predict' command computes predicted values for each observation in the current dataset. 'predict' has many options that depend on the model that was fit. Here we consider only the options that provide information we use regularly in later chapters. If you type 'help estimation-command' (for example, 'help logit'), you can click on the 'Also See' tab in the upper-right corner of the window and then select the postestimation entry for the command (for example, '[R] logit postestimation'); the postestimation entry includes details on how 'predict' works for that estimation command.

The simplest syntax for predict is 

**predict newvarlist**

where newvarlist contains the name or names of the variables that are generated to hold the predictions. How many variables and what is predicted depends on the model. The defaults for estimation commands used in this book are listed in the following table.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.20.9.png" style="width:650px;height:250px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

As an example, **we compute predicted probabilities for a logit model of women’s labor force participation.** Below, 'predict' generates the variable 'prob' (a name we chose) containing the probabilities of a woman being in the labor force.

```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog

```

    Logistic regression                                     Number of obs =    753
                                                            LR chi2(8)    = 124.30
                                                            Prob > chi2   = 0.0000
    Log likelihood = -452.72367                             Pseudo R2     = 0.1207
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              k5 |      -1.39       0.19    -7.25   0.000        -1.77       -1.02
            k618 |      -0.07       0.07    -0.96   0.336        -0.20        0.07
                 |
          agecat |
          40-49  |      -0.63       0.21    -3.00   0.003        -1.04       -0.22
            50+  |      -1.28       0.26    -4.92   0.000        -1.79       -0.77
                 |
              wc |
        college  |       0.80       0.23     3.48   0.001         0.35        1.25
                 |
              hc |
        college  |       0.14       0.21     0.66   0.508        -0.27        0.54
             lwg |       0.61       0.15     4.04   0.000         0.31        0.91
             inc |      -0.04       0.01    -4.24   0.000        -0.05       -0.02
           _cons |       1.01       0.29     3.54   0.000         0.45        1.57
    ------------------------------------------------------------------------------


```
predict prob
summarize prob
```



        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
            prob |        753    .5683931    .1945282   .0135618   .9512301

The summary statistics show that in the sample of 753, the probabilities range from 0.014 to 0.951 with an average of 0.568. A detailed discussion of predicted probabilities for binary models is provided in chapter 6

For models with ordinal or nominal outcomes (chapters 7 and 8), 'predict' computes the predicted probability of an observation falling into each of the outcome categories. So, **instead of providing a single variable name for predictions, you specify as many names as there are categories. For example, after fitting a model for a nominal dependent variable with four categories, you can use 'predict prob1 prob2 prob3 prob4'. The new variables contain the predicted probabilities of being in the first, second, third, and fourth categories.**

For count models, by default **predict** computes the rate or expected count. Alternatively, predict newvarname, **pr(#)** computes the predicted probabilities of the specified counts. For example, predict prob0, pr(0) generates the variable 'prob0' containing estimates of Pr(y = 0 | x). Also, 'predict pr(#iow > #high)' computes probabilities of contiguous counts. For example, **predict prob1to3, pr(1,3)** generates the variable '**prob1to3**' containing estimates of $Pr(1 < y < 3 | x).$

## 4 Predictions at specified values

Interpreting predictions at substantively interesting values of the regressors is an essential method of interpretation. Such predictions can be made with m argins and with the m* commands we have written that are based upon margins. We focus on several aspects of these commands:

1. Specifying values of the independent variables.
2. Explaining how factor variables are handled.
3. Using a $numlist$ for predictions at multiple values.
4. Making predictions by the levels of a variable defining groups.
5. Predicting quantities th at are not the default for **margins**

We will explain how to use the **margins** command to make predictions, and then we will show how the same things (and more) can be done using the **m*** command **mtable**.

### 4.1 Why use the m* commands instead of margins?

Our m* commands **mtable**, **mchange**, and **mgen** are “wrappers” for margins. By wrapper, we mean that **the m* commands translate your specification into a series of margins commands that actually do the computations.** 

Although we think margins is an extraordinary command, it can be difficult to use, and the output can be difficult to interpret. It does difficult things with amazing ease and also makes you work hard to do some simple things. In some ways, frankly, **margins** is more suited for a programmer than for a data analyst. For example, if you are fitting models with ordinal, nominal, or count outcomes, you have to run margins once for each outcome. Then, you face the tedious and error-prone task of combining the output from several margins commands. The learning curve for margins can also be steep. Our commands make it easier—sometimes much easier. The output is more compact, and if you want to plot the predictions, variables are automatically generated.

### 4.2 Using margins for predictions

The margins command allows you to predict many quantities and compute summary measures of your predictions. To begin, it is helpful to see how margins is related to predict. Consider the example in section 4.3, where predict computed the probability of labor force participation for each observation in the sample. Using summarize to analyze the variable generated by predict, we found that the mean probability was 0.568. We can obtain exactly the same mean prediction with margins:

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
margins
```

    Logistic regression                                     Number of obs =    753
                                                            LR chi2(8)    = 124.30
                                                            Prob > chi2   = 0.0000
    Log likelihood = -452.72367                             Pseudo R2     = 0.1207
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              k5 |      -1.39       0.19    -7.25   0.000        -1.77       -1.02
            k618 |      -0.07       0.07    -0.96   0.336        -0.20        0.07
                 |
          agecat |
          40-49  |      -0.63       0.21    -3.00   0.003        -1.04       -0.22
            50+  |      -1.28       0.26    -4.92   0.000        -1.79       -0.77
                 |
              wc |
        college  |       0.80       0.23     3.48   0.001         0.35        1.25
                 |
              hc |
        college  |       0.14       0.21     0.66   0.508        -0.27        0.54
             lwg |       0.61       0.15     4.04   0.000         0.31        0.91
             inc |      -0.04       0.01    -4.24   0.000        -0.05       -0.02
           _cons |       1.01       0.29     3.54   0.000         0.45        1.57
    ------------------------------------------------------------------------------


    Predictive margins                                         Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.57       0.02    34.24   0.000         0.54        0.60
    ------------------------------------------------------------------------------


When no options are specified, margins calculates the mean of the default quantity computed by **predict** for the estimation command. Earlier, we used **predict** to generate a variable with the probabilities of $y = 1$ for each observation, and we used summarize to compute the mean probability. Behind the scenes, this is what **margins** does.

An advantage of using **margins** to compute the average predicted probability is that it provides the 95% confidence interval along with a test of the null hypothesis that the average prediction is 0. Stata does this using the delta method to compute standard errors (see [R] margins or Agresti [2013, 72]).

In this example, testing that the mean prediction is 0 is not useful; but, when we later use margins to compute marginal effects, testing whether estimates differ from 0 is very useful.

In addition to computing average predictions over the sample, margins allows us to compute predictions at specified values of the independent variables, whether those values occur in the sample or not. The most common example of this is computing the prediction with all variables at their mean by using the **atmeans** option:

```
margins, atmeans
```

    Adjusted predictions                                       Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    At: k5       = .2377158 (mean)
        k618     = 1.353254 (mean)
        1.agecat = .3957503 (mean)
        2.agecat = .3851262 (mean)
        3.agecat = .2191235 (mean)
        0.wc     = .7184595 (mean)
        1.wc     = .2815405 (mean)
        0.hc     = .6082337 (mean)
        1.hc     = .3917663 (mean)
        lwg      = 1.097115 (mean)
        inc      = 20.12897 (mean)
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.58       0.02    29.33   0.000         0.54        0.62
    ------------------------------------------------------------------------------


The output, begins by listing the values of the independent variables at which the prediction was calculated, called the *atlegend*, where **(mean)** lets you know that these values are the means.

#### 4.2.1 The at() option for specifying values

The **at()** option allows us to set specific values of the independent variables at which predictions are calculated. Stata refers to the specification of values within **at()** as the *atspec*. As an example, we can compute the probability of labor force participation for a young woman with one young child, one older child, and so on:


```
margins, at(k5=1 k618=1 agecat=1 wc=0 hc=0 lwg=1 inc=20)
```

    Adjusted predictions                                       Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    At: k5     =  1
        k618   =  1
        agecat =  1
        wc     =  0
        hc     =  0
        lwg    =  1
        inc    = 20
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.37       0.05     7.92   0.000         0.28        0.46
    ------------------------------------------------------------------------------

In th is example, we are computing predictions for a hypothetical observation that has th e values of the independent variables specified with **at()** . The output shows these values in the *atlegend* before displaying the prediction.

If we want some of the variables to be held at their means, say, **inc and lwg**, we could remove them from the **atspec** and include the **atmeans** option:

```
margins, at(k5=1 k618=1 agecat=1 wc=0 hc=0) atmeans
```

    Adjusted predictions                                       Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    At: k5     =        1
        k618   =        1
        agecat =        1
        wc     =        0
        hc     =        0
        lwg    = 1.097115 (mean)
        inc    = 20.12897 (mean)
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.38       0.05     8.05   0.000         0.29        0.48
    ------------------------------------------------------------------------------

With **atmeans**. all variables not in the **atspec** are set equal to their means.

There are two other ways we could have done the same thing. (With **margins**, most things can be done multiple ways!) First, we could enter the values for the means in the *atspec*:

```
margins, at(k5=1 k618=1 agecat=1 wc=0 hc=0 lwg=1.097 inc=20.13)
```

This is not exactly the same because the specified values of means were rounded. Second, we can use the **(atstat)** suboption within **at ( )** :

```
margins, at(k5=1 k618=1 agecat=1 wc=0 hc=0 (mean) lwg inc)
```

We can also specify other statistics. For example, we can calculate the predicted probability with **lwg** held at its mean by using **(mean)** and inc held at its median by using **(median)**

```
margins, at(k5=1 k618=1 agecat=1 wc=0 hc=0 (mean) lwg (median) inc)
```

For continuous predictors, atstat, can be **mean**, **median**, **p#** for percentiles from 1 to 99, min for the minimum, and max for the maximum. If you try to use these options for variables specified as factor variables using **i.** , an error is generated.

#### 4.2.2 asobserved for average predictions

When we do not specify values for the independent variables, either using atmeans or at(), the margins command computes the mean of the predictions across observations. **Average predictions, which are sometimes called as-observed predictions, are the default.** You can make the default explicit with the command **margins**, **asobserved**. In linear models, **atmeans** and **asobserved** predictions are identical, but because they differ in nonlinear models, it is important to understand why they differ. **The substantive implications of this difference are particularly important when computing marginal effects**, discussed briefly in section 4.5 and in detail in section 6.2.

If you do not set the value for an independent variable in the atspec or **with atmeans**, the variable is treated as-observed. For example, in the command

```
margins, at(k5=1 k618=1 agecat=1 wc=0 hc=0 lwg=1.097)
```
the variable inc is treated as-observed because it is not included in the atspec. We can make this explicit by using (asobserved) inc:

    Predictive margins                                         Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    At: k5     =     1
        k618   =     1
        agecat =     1
        wc     =     0
        hc     =     0
        lwg    = 1.097
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.39       0.05     8.48   0.000         0.30        0.48
    ------------------------------------------------------------------------------



```
margins, at(k5=1 k618=1 agecat=1 wc=0 hc=0 lwg=1.097 (asobserved) inc)
```
    Predictive margins                                         Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    At: k5     =     1
        k618   =     1
        agecat =     1
        wc     =     0
        hc     =     0
        lwg    = 1.097
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.39       0.05     8.48   0.000         0.30        0.48
    ------------------------------------------------------------------------------

Values for as-observed variables are not listed in the output because they vary across observations. For example, here inc is an as-observed variable, so it is not shown in the atlegend:

```
margins, at(k5=1 k618=1 agecat=1 wc=0 hc=0 lwg=1)
```

    Predictive margins                                         Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    At: k5     = 1
        k618   = 1
        agecat = 1
        wc     = 0
        hc     = 0
        lwg    = 1
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.37       0.04     8.33   0.000         0.29        0.46
    ------------------------------------------------------------------------------

To understand what happens with the as-observed variable inc, we show how to use a series of Stata commands to compute the same prediction. First, for each variable specified in at( ) , we replace the observed value with the specified value:

```
replace k5 = 1
replace k618 = 1
replace agecat = 1
replace wc = 0
replace hc = 0
replace lwg = 1
```

The observed values of all variables except inc have been replaced. For every observation in the dataset, k5 is 1, k618 is 1, and so on. Variable inc has been left “as observed”. Next, we make predictions with the observed values of inc and the changed values of other variables:

```
capture drop prob
predict prob
label var prob "predict with fixing values of all but inc"
sum prob
```

        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
            prob |        753     .374379    .0804292   .0392212   .5418247

we obtain the same value as margins when inc was treated as-observed. Nontrivially, although the mean prediction is the same, we have not computed the standard error of the prediction, which margins provides.

#### 4.2.3 Predictions using interaction and polynomial terms

When factor-variable notation is used in the atspec, margins handles these terms properly. For example, let’s say your specification includes i.wc##c.age. In this case, when margins, at(age=30 wc=l) makes predictions, it automatically computes the value of the interaction wc x age to equal 30. as it should. Likewise, if your model includes the term c.age##c.age. specifying margins, at(age=30) makes predictions with age held at 30 and age-squared held at 900. This powerful feature of factor-variable notation greatly simplifies the way in which you can specify and interpret models with interactions and polynomials.

#### 4.2.4 Making multiple predictions
Making multiple predictions with a single margins command is critical if you want to test hypotheses about those predictions, such as whether the probability of voting Republican is the same for men and for women. In this section, we consider a variety of ways to make multiple predictions.

**margins allows you to compute multiple predictions with a single at () specification.** For example, here we make predictions at two values of wc, with other variables held at their means:

```
use binlfp4, clear
logit lfp k5 k618 age i.wc i.hc lwg inc, nolog
```

    Logistic regression                                     Number of obs =    753
                                                            LR chi2(7)    = 124.48
                                                            Prob > chi2   = 0.0000
    Log likelihood = -452.63296                             Pseudo R2     = 0.1209
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              k5 |      -1.46       0.20    -7.43   0.000        -1.85       -1.08
            k618 |      -0.06       0.07    -0.95   0.342        -0.20        0.07
             age |      -0.06       0.01    -4.92   0.000        -0.09       -0.04
                 |
              wc |
        college  |       0.81       0.23     3.51   0.000         0.36        1.26
                 |
              hc |
        college  |       0.11       0.21     0.54   0.588        -0.29        0.52
             lwg |       0.60       0.15     4.01   0.000         0.31        0.90
             inc |      -0.03       0.01    -4.20   0.000        -0.05       -0.02
           _cons |       3.18       0.64     4.94   0.000         1.92        4.45
    ------------------------------------------------------------------------------

```
margins, at(wc=0 wc=1) atmeans
```

    Adjusted predictions                                       Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    1._at: k5   = .2377158 (mean)
           k618 = 1.353254 (mean)
           age  = 42.53785 (mean)
           wc   =        0
           0.hc = .6082337 (mean)
           1.hc = .3917663 (mean)
           lwg  = 1.097115 (mean)
           inc  = 20.12897 (mean)
    2._at: k5   = .2377158 (mean)
           k618 = 1.353254 (mean)
           age  = 42.53785 (mean)
           wc   =        1
           0.hc = .6082337 (mean)
           1.hc = .3917663 (mean)
           lwg  = 1.097115 (mean)
           inc  = 20.12897 (mean)
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             _at |
              1  |       0.52       0.02    21.08   0.000         0.47        0.57
              2  |       0.71       0.04    18.13   0.000         0.63        0.79
    ------------------------------------------------------------------------------


The legend labeled 1. _at is for wc=0 with other variables held at the mean. The second legend, labeled 2._at, is for wc=l.

There are two other ways to specify these two predictions with at() in a single margins command. We could include two at() options in the same margins command:

```
margins, at(wc=0 wc=1) atmeans
```

Or we could use a numlist, Sta ta’s name for a list of numerical values:

```
margins, at(wc=(0 1)) atmeans
```

In th is specification, (0 1) is the numlist, which must be **enclosed in parentheses**. For example, at(wc=0 1) will generate an error.

The *atspec* can use any Stata numlist to specify multiple predictions (see help numlist for more details). **One of the most useful forms allows us to specify every #th value over a range of values of an independent variable**. For instance, say that we want predictions at every 10 years of age from 30 to 60:

```
margins, at(age=(30(10)60)) atmeans
```

    Adjusted predictions                                       Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    1._at: k5   = .2377158 (mean)
           k618 = 1.353254 (mean)
           age  =       30
           0.wc = .7184595 (mean)
           1.wc = .2815405 (mean)
           0.hc = .6082337 (mean)
           1.hc = .3917663 (mean)
           lwg  = 1.097115 (mean)
           inc  = 20.12897 (mean)
    2._at: k5   = .2377158 (mean)
           k618 = 1.353254 (mean)
           age  =       40
           0.wc = .7184595 (mean)
           1.wc = .2815405 (mean)
           0.hc = .6082337 (mean)
           1.hc = .3917663 (mean)
           lwg  = 1.097115 (mean)
           inc  = 20.12897 (mean)
    3._at: k5   = .2377158 (mean)
           k618 = 1.353254 (mean)
           age  =       50
           0.wc = .7184595 (mean)
           1.wc = .2815405 (mean)
           0.hc = .6082337 (mean)
           1.hc = .3917663 (mean)
           lwg  = 1.097115 (mean)
           inc  = 20.12897 (mean)
    4._at: k5   = .2377158 (mean)
           k618 = 1.353254 (mean)
           age  =       60
           0.wc = .7184595 (mean)
           1.wc = .2815405 (mean)
           0.hc = .6082337 (mean)
           1.hc = .3917663 (mean)
           lwg  = 1.097115 (mean)
           inc  = 20.12897 (mean)
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             _at |
              1  |       0.75       0.03    21.74   0.000         0.68        0.82
              2  |       0.62       0.02    29.23   0.000         0.57        0.66
              3  |       0.46       0.03    15.39   0.000         0.40        0.52
              4  |       0.31       0.05     6.28   0.000         0.22        0.41
    ------------------------------------------------------------------------------


If we specify *numlists* for multiple independent variables, we get predictions for all combinations of those variables. For example, to make predictions at every 10 years of age when wc = 0 and when wc = 1, holding other variables to their means, type

```
logit lfp k5 k618 age i.wc i.hc lwg inc, nolog
margins, at(age=(30(10)60) wc=(0 1)) atmeans
```


    Adjusted predictions                                       Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    1._at: k5   = .2377158 (mean)
           k618 = 1.353254 (mean)
           age  =       30
           wc   =        0
           0.hc = .6082337 (mean)
           1.hc = .3917663 (mean)
           lwg  = 1.097115 (mean)
           inc  = 20.12897 (mean)
    8._at: k5   = .2377158 (mean)
           k618 = 1.353254 (mean)
           age  =       60
           wc   =        1
           0.hc = .6082337 (mean)
           1.hc = .3917663 (mean)
           lwg  = 1.097115 (mean)
           inc  = 20.12897 (mean)


    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             _at |
              1  |       0.71       0.04    17.80   0.000         0.63        0.78
              2  |       0.84       0.03    24.84   0.000         0.78        0.91
              3  |       0.56       0.03    21.66   0.000         0.51        0.61
              4  |       0.74       0.04    19.84   0.000         0.67        0.81
              5  |       0.41       0.03    12.41   0.000         0.34        0.47
              6  |       0.60       0.05    12.23   0.000         0.51        0.70
              7  |       0.27       0.05     5.64   0.000         0.17        0.36
              8  |       0.45       0.07     6.41   0.000         0.31        0.59
    ------------------------------------------------------------------------------
    
Eight predictions are made for the four values of age by two values of **hc** . In the atlegend. the values of the independent variables for each prediction are labeled as #. _at . which correspond to the prediction numbers listed under _at.

When many predictions are calculated, the atlegend can take hundreds of lines. The **noatlegend** option suppresses the listing; however, although the output is shorter, it is easy to lose track of which prediction corresponds to which values of the independent variables. To address this issue, our **mlistat** command lists the **atlegend** in a more compact form:

```
logit lfp k5 k618 age i.wc i.hc lwg inc, nolog
margins, at(age=(30(10)60) wc=(0 1)) atmeans noatlegend
```

    Adjusted predictions                                       Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             _at |
              1  |       0.71       0.04    17.80   0.000         0.63        0.78
              2  |       0.84       0.03    24.84   0.000         0.78        0.91
              3  |       0.56       0.03    21.66   0.000         0.51        0.61
              4  |       0.74       0.04    19.84   0.000         0.67        0.81
              5  |       0.41       0.03    12.41   0.000         0.34        0.47
              6  |       0.60       0.05    12.23   0.000         0.51        0.70
              7  |       0.27       0.05     5.64   0.000         0.17        0.36
              8  |       0.45       0.07     6.41   0.000         0.31        0.59
    ------------------------------------------------------------------------------
    

```
mlistat
```

    at() values held constant
    
                               1.                    
          k5      k618        hc       lwg       inc 
    ------------------------------------------------
        .238      1.35      .392       1.1      20.1 
    
    at() values vary
    
       _at |      age        wc 
    -------+-------------------
         1 |       30         0 
         2 |       30         1 
         3 |       40         0 
         4 |       40         1 
         5 |       50         0 
         6 |       50         1 
         7 |       60         0 
         8 |       60         1 

mlistat divides the independent variables into those that are constant, which are listed only once, and those that vary across predictions. If values of a variable vary, mlistat lists their values along with the prediction number in the _at column. If you do not want the output to be divided in this way, you can specify the nosplit option to list all values for all variables. The noconstant option prints only variables whose values vary.

We mention this because **it is important to produce predictions that make it as easy as possible to interpret results**. This is considered further when we discuss the mtable command below.

#### 4.2.5 Predictions for groups defined by levels of categorical variables

If multiple factor variables are specified in *varlist*, then **margins** computes all combinations, just as it does when a num.list specifies multiple variables within at() .

With the at() specification, we can use combinations of continuous variables and factor variables, whereas only factor variables can be included in the varlist. For instance, earlier we computed predictions over age by using **at(age=30(10)60)**, but typing margins **age** produces an error because **age** is not a factor variable. The atlegend is also more compact, and perhaps more confusing, when a varlist is used because it shows the means for the factor variables 0.wc and 1.wc even though the predictions are made with wc=0 and wc=1, not at their means.

We can also make predictions at different levels of a categorical variable with the **over()** option. Like many Stata commands, **margins supports the over() option to obtain separate estimates for different groups, where the variable defining the groups does not need to be in the model.** There is a subtle but important difference in how overO makes group predictions compared with the methods considered earlier. An example is the easiest way to understand how overO works. For the logit model fit earlier, we make predictions over the binary variable wc:

```
margins,over(wc) atmeans
```

        Adjusted predictions                                       Number of obs = 753
        Model VCE: OIM
        
        Expression: Pr(lfp), predict()
        Over:       wc
        At: 0.wc
                k5   = .2014787 (mean)
                k618 = 1.395564 (mean)
                age  = 42.85952 (mean)
                wc   =        0
                0.hc = .7707948 (mean)
                1.hc = .2292052 (mean)
                lwg  = .9827096 (mean)
                inc  = 18.10913 (mean)
            1.wc
                k5   = .3301887 (mean)
                k618 = 1.245283 (mean)
                age  = 41.71698 (mean)
                wc   =        1
                0.hc = .1933962 (mean)
                1.hc = .8066038 (mean)
                lwg  = 1.389064 (mean)
                inc  = 25.28335 (mean)
        
        ------------------------------------------------------------------------------
                     |            Delta-method
                     |     Margin   std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
                  wc |
                 no  |       0.52       0.02    22.92   0.000         0.48        0.57
            college  |       0.70       0.03    20.38   0.000         0.64        0.77
        ------------------------------------------------------------------------------
    
    Predictions for wc = 0 are computed with other variables held a t the mean for the subsample defined by if wc==0. Similarly, means for wc = 1 are computed with if wc==l.
    
    To see this, we run margins using an if condition. When an if or in condition is used with margins, the sample is restricted to those cases when computing means, medians, and other values for the at( ) variables. 
    
    ```
    margins if wc==0, atmeans
    margins if wc==1, atmeans
    ```
    
    Adjusted predictions                                       Number of obs = 541
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    At: k5   = .2014787 (mean)
        k618 = 1.395564 (mean)
        age  = 42.85952 (mean)
        wc   =        0
        0.hc = .7707948 (mean)
        1.hc = .2292052 (mean)
        lwg  = .9827096 (mean)
        inc  = 18.10913 (mean)
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.52       0.02    22.92   0.000         0.48        0.57
    ------------------------------------------------------------------------------
    
    . margins if wc==1, atmeans
    
    Adjusted predictions                                       Number of obs = 212
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    At: k5   = .3301887 (mean)
        k618 = 1.245283 (mean)
        age  = 41.71698 (mean)
        wc   =        1
        0.hc = .1933962 (mean)
        1.hc = .8066038 (mean)
        lwg  = 1.389064 (mean)
        inc  = 25.28335 (mean)
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.70       0.03    20.38   0.000         0.64        0.77
    ------------------------------------------------------------------------------

### 4.3 (Advanced) Nondefault predictions using margins

Although the section heading seems esoteric, this is an important topic. The default predictions computed by margins or the m* commands are often the predictions you want, which is why they are the default. But you might want to predict some other quantity. Using the options described in this section, you can predict arbitrarily complex functions of any quantity computed by predict. Several useful applications of this powerful feature of margins are illustrated in later chapters.

By default, **margins** predicts whatever predict would **predict** by default for the last estimation command. For instance, the default prediction for regress is the predicted value $\widehat{E(y|\mathbf{x})}$, whereas for logit the default prediction is $\widehat{\mathrm{Pr}}(y=1|\mathbf{x})$. For most estimation commands, you can predict other quantities by adding an option to **predict**. For example, after **logit**, the command **predict myxb, xb** generates the variable **myxb** with the linear combination of the log-odds. To determine the default prediction and what other types of predictions are available for a given estimation command, type **help** estimation-command **postestimation** (for example, **help logit postestimation**). The margins command can estimate any of the quantities computed by predict, as well as arbitrarily complex functions of these quantities with the **predict()** and **expression()** options.

#### 4.3.1 The predict() option

With the **predict**(*statistic*) option, the margins command makes predictions for any *statistic* that can be computed by **predict**. For example, in the ordered logit model considered in chapter 7, the default prediction is the probability of the first outcome. Suppose that our outcome categories are numbered 1, 2, 3, and 4. Running margins without predict() computes the average of $\widehat{\mathrm{Pr}}(y_{i}=1|\mathbf{x}_{i})$. Because **predict prob2**, **outcome(2)** generates the variable prob2 containing $\widehat{\Pr}(y_i=2|\mathbf{x}_i)$, to estimate the average probability that y equals 2, we use margins, **predict(outcome(2))**:

```
use gssclass4, clear
ologit class i.fem i.white i.year i.ed age inc, nolog
margins, predict(outcome(2))
```

    Predictive margins                                       Number of obs = 5,620
    Model VCE: OIM
    
    Expression: Pr(class==2), predict(outcome(2))
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.46       0.01    73.93   0.000         0.45        0.47
    ------------------------------------------------------------------------------

In the output, Expression: Pr(class==2), predict(outcome(2)) indicates the quantity being estimated. Because margins can only predict one outcome at a time, we must either run margins once for each outcome or use mtable to automate this process, as we describe shortly.


#### 4.3.2 The expression() option

The **expression()** option lets you estimate transformations of what is computed by **predict()**. To show how this works, imagine that after fitting an ordered logit model on an outcome with four categories, we want the predicted probability that y is 2, 3, or 4. That is, we want to compute $\begin{aligned}\Pr(y\ne1|\mathbf{x})=1-\Pr(y=1|\mathbf{x})\end{aligned}$. The option is expression(1 - predict(outcome(1))):

```
margins, expression(1-predict(outcome(1)))
```

    Predictive margins                                       Number of obs = 5,620
    Model VCE: OIM
    
    Expression: 1-predict(outcome(1))
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.93       0.00   274.89   0.000         0.92        0.94
    ------------------------------------------------------------------------------


A similar application is computing the probability of a 0 after fitting a binary model. We cannot obtain this prediction with the **predict()** option because the predict command does not have an option to compute the probability of a 0. The default option **pr** computes the probability of a 1. To compute the probability of a 0, type:

```
use binlfp4, clear
logit lfp k5 k618 age i.wc i.hc lwg inc, nolog
margins, expression(1-predict(pr))
```

    Predictive margins                                         Number of obs = 753
    Model VCE: OIM
    
    Expression: 1-predict(pr)
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.43       0.02    26.00   0.000         0.40        0.46
    ------------------------------------------------------------------------------


The **expression()** option is incredibly powerful, allowing you to routinely test things that would otherwise be difficult. To test whether the expected probability is 0.5 after **logit**, use **margins, expression(predict(pr) - 0.5)**. In this expression, **predict(pr)** computes the probability that y equals 1. By subtracting 0.5, we are computing deviations from 0.5. We can use the z statistic from margins to test whether the average deviation is 0, which is equivalent to testing whether the average probability is 0.5. As another example, after **logit**, we can test whether the probability of a respondent identifying as lower class (y = 1) equals the probability of identifying as upper class (y = 4) by using:

```
margins, expression( predict(outcome(1)) - predict(outcome(#4)) )
```

    Predictive margins                                       Number of obs = 5,620
    Model VCE: OIM
    
    Expression: predict(outcome(1)) - predict(outcome(#4))
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.04       0.00     9.00   0.000         0.03        0.04
    ------------------------------------------------------------------------------


### 4.4 Tables of predictions using mtable
mtable makes tables from the predictions computed by margins. You do not need to run margins because mtable does this for you, using most of the options for margins that we just considered. In addition, mtable has options to customize how the results appear by adding labels, selecting statistics, and combining results from multiple mtable commands. There are, however, some features in margins that will not work with mtable. Most notably, perhaps, **margins allows a varlist with factor variables, but mtable does not. But as we showed on page 151, results that can be computed with a varlist can be computed using at(), so this does not limit what you can do with mtable.**

To explain how m table works, we start by creating a table of predicted probabilities that vary by wc and he from a logit model. We will talk at length about how to interpret these predictions in chapter 6

```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
mtable, at(wc=(0 1) hc=(0 1)) atmeans
```

    Expression: Pr(lfp), predict()
    
               |       wc        hc     Pr(y)
     ----------+-----------------------------
             1 |        0         0     0.509
             2 |        0         1     0.543
             3 |        1         0     0.697
             4 |        1         1     0.725
    
    Specified values of covariates
    
               |                              2.        3.                    
               |       k5      k618      agecat    agecat       lwg       inc
     ----------+-------------------------------------------------------------
       Current |     .238      1.35        .385      .219       1.1      20.1
    

In the header, **Expression** echoes the description that margins uses to describe the predictions it is making. The column **Pr(y)** contains predicted probabilities that **lfp** is 1. The first row of the prediction table, numbered 1, shows that the probability of being in the labor force is 0.509 for a woman who did not go to college (wc=0) and whose husband did not go to college (hc=0), holding other variables at their means as specified with the atmeans option. Rows 2, 3, and 4 show predictions for other combinations of hc and wc. Values of the independent variables that are held constant are displayed below the predictions.

To convince you (we hope) of the advantages of **mtable**. let’s look at the corresponding output from **margins**. We show all the output because if you use **noatlegend**, you risk not knowing which predictions correspond to which values of the variables that vary.

```
margins, at(wc=(0 1) hc=(0 1)) atmeans
```

    Adjusted predictions                                       Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    1._at: k5       = .2377158 (mean)
           k618     = 1.353254 (mean)
           1.agecat = .3957503 (mean)
           2.agecat = .3851262 (mean)
           3.agecat = .2191235 (mean)
           wc       =        0
           hc       =        0
           lwg      = 1.097115 (mean)
           inc      = 20.12897 (mean)
    2._at: k5       = .2377158 (mean)
           k618     = 1.353254 (mean)
           1.agecat = .3957503 (mean)
           2.agecat = .3851262 (mean)
           3.agecat = .2191235 (mean)
           wc       =        0
           hc       =        1
           lwg      = 1.097115 (mean)
           inc      = 20.12897 (mean)
    3._at: k5       = .2377158 (mean)
           k618     = 1.353254 (mean)
           1.agecat = .3957503 (mean)
           2.agecat = .3851262 (mean)
           3.agecat = .2191235 (mean)
           wc       =        1
           hc       =        0
           lwg      = 1.097115 (mean)
           inc      = 20.12897 (mean)
    4._at: k5       = .2377158 (mean)
           k618     = 1.353254 (mean)
           1.agecat = .3957503 (mean)
           2.agecat = .3851262 (mean)
           3.agecat = .2191235 (mean)
           wc       =        1
           hc       =        1
           lwg      = 1.097115 (mean)
           inc      = 20.12897 (mean)
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             _at |
              1  |       0.51       0.03    18.54   0.000         0.46        0.56
              2  |       0.54       0.04    12.21   0.000         0.46        0.63
              3  |       0.70       0.05    14.23   0.000         0.60        0.79
              4  |       0.73       0.04    19.92   0.000         0.65        0.80
    ------------------------------------------------------------------------------

The margins output has additional information about the predictions, such as the **confidence interval**, that was missing from the **mtable** output.

We can include the confidence interval in the mtable output by adding the options statistics(ci). At the same time, we show how to customize the label for predictions by using estname():

```
mtable, at(wc=(0 1) hc=(0 1)) atmeans estname(Pr_LFP) statistics(ci)
```

    Expression: Pr(lfp), predict()
    
               |       wc        hc    Pr_LFP        ll        ul
     ----------+-------------------------------------------------
             1 |        0         0     0.509     0.455     0.563
             2 |        0         1     0.543     0.456     0.630
             3 |        1         0     0.697     0.601     0.793
             4 |        1         1     0.725     0.654     0.796
    
    Specified values of covariates
    
               |                              2.        3.                    
               |       k5      k618      agecat    agecat       lwg       inc
     ----------+-------------------------------------------------------------
       Current |     .238      1.35        .385      .219       1.1      20.1

The **statistics**(*statlist*) option allows you to add other statistics as well. The potential elements of *statlist* are shown in the following table.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.20.10.png" style="width:650px;height:350px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


By default, mtable includes columns with the values of the **at()** variables that are changing. If you do not want these columns displayed, specify atvars(_none). You can use the **atvars(varlist)** option to select which variables will appear, even if their values are not changing. This is useful when building tables (discussed soon) or when you want your table to show the level of a variable that is not varying across the predictions. In our example, wc varies but k5 and k618 do not. To include them in the table, we add the option **atvars(k5 k618 wc)**. We also use the **brief** option so that the table of values for covariates is not shown:

```
mtable, at(k5=0 k618=0 wc=(0 1)) atmeans atvars(k5 k618 wc) brief
```

    Expression: Pr(lfp), predict()
    
               |                            1.          
               |       k5      k618        wc     Pr(y)
     ----------+---------------------------------------
             1 |        0         0         0     0.625
             2 |        0         0         1     0.787
    
#### 4.4.1 mtable with categorical and count outcomes

With categorical and count outcomes, a separate margins command must be run for each value of the outcome variable. For example, to compute predictions for each outcome category in an ordinal logit, you would need a series of commands such as margins, predict(outcome(1)) atmeans, then margins, predict(outcome(2)) atmeans, and so on. **In contrast, mtable automatically calculates predicted probabilities for all categories. Indeed, automatically computing predictions for multiple outcomes and combining the predictions into a single table is what initially motivated the creation of mtable.**

In one of our running examples in chapter 7, the outcome is subjective class identification, with categories ranging from 1 for lower class to 4 for upper class. To examine how attitudes are related to a respondent’s race and gender, we compute the following table, where the **dec(2)** option indicates that 2 decimal places should be used to display the estimates:

```
use gssclass4, clear
ologit class i.fem i.white i.year i.ed age inc, nolog
mtable, at(fem=(0 1) white=(0 1)) atmeans stat(ci) dec(2)
```

    Expression: Pr(class), predict(outcome())
    
               |   female     white     lower   working    middle     upper
     ----------+-----------------------------------------------------------
         Pr(y) |        0         0      0.06      0.52      0.41      0.01
            ll |        0         0      0.05      0.49      0.38      0.01
            ul |        0         0      0.07      0.54      0.44      0.02
         Pr(y) |        0         1      0.05      0.46      0.47      0.02
            ll |        0         1      0.04      0.44      0.45      0.01
            ul |        0         1      0.05      0.48      0.49      0.02
         Pr(y) |        1         0      0.06      0.51      0.42      0.01
            ll |        1         0      0.05      0.48      0.38      0.01
            ul |        1         0      0.07      0.54      0.45      0.02
         Pr(y) |        1         1      0.05      0.46      0.48      0.02
            ll |        1         1      0.04      0.44      0.46      0.01
            ul |        1         1      0.05      0.48      0.50      0.02
    
    Specified values of covariates
    
               |        2.        3.        2.        3.                    
               |     year      year      educ      educ       age    income
     ----------+-----------------------------------------------------------
       Current |      .45       .31       .58       .24        45        68


Holding other variables at their means, the predicted probability of identifying as working class is 0.52 for nonwhite men (fem=0, white=0). With categorical outcomes, additional statistics are placed below the estimates, in this case showing the lower and upper bounds for the confidence interval.

By default, all outcome categories are included in the table. **The options pr(numlist) and outcome(numlist') let you select which outcomes to include in the table. For estimation commands that support the outcome() option in predict (for example, logit), mtable uses the outcome() option to select which predictions to present.** For commands such as logit and poisson that support the pr option with predict, mtable uses pr() to select which outcomes to present. For example, if we want to display only results for the 1=lower class and 4=upper class categories, we type:

```
mtable, at(fem=(0 1) white=(0 1)) outcome(1 4) atmeans brief
```

    Expression: Pr(class), predict(outcome())
    
               |   female     white     lower     upper
     ----------+---------------------------------------
             1 |        0         0     0.061     0.014
             2 |        0         1     0.048     0.018
             3 |        1         0     0.060     0.014
             4 |        1         1     0.047     0.018

where we suppress the values of the covariates with brief

For count models discussed in chapter 9, the default prediction for **mtable** is the rate because it is the default for predict in count models. To display predicted probabilities for a specific count—say, 0— we would use the option **pr(0)**. To compute predicted probabilities for counts from 0 to 5, we would use **pr(0/5)**.

    
#### 4.4.1 (Advanced) Combining and formatting tables using mtable

>We mark this section as advanced because it does not consider new ways of making predictions using mtable, but instead considers how to create tables that combine predictions from running multiple mtable commands. If you are only making a few predictions, the time it takes to learn these features might not be worth it. But if you often create tables of predictions, these features will save you time, make it easier to see key results, and prevent the inevitable errors that occur when constructing tables by hand.

**mtable** allows you to combine results from multiple **mtable** commands. The best way to understand how this works is with an example. Suppose that we want to compare the average predicted probability of labor force participation with the predicted probability holding all variables at their mean. We begin by fitting the model:

```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
```

Using the results from this model, we next estimate the average probability of labor force participation for values of k5 from 0 to 3. Because our table is going to include multiple predictions, we add labels to identify each set of predictions. The option coleqnmO adds a header row to our predictions. The name for this option might seem odd, but it reflects that mtable saves results as matrices that refer to this header as the “column equation name”. We use the coleqnm(lst) option to label our first set of predictions:

```
mtable, at(k5=(0 1 2 3)) coleqnm(1st)
```
Expression: Pr(lfp), predict()

               | 1st               
               |       k5     Pr(y)
     ----------+-------------------
             1 |        0     0.637
             2 |        1     0.342
             3 |        2     0.129
             4 |        3     0.038
    
    Specified values where .n indicates no values specified with at()
    
               |  No at()
     ----------+---------
       Current |       .n


Next, we compute predictions at the means by adding the **atmeans** option, using the **right** option to append the new predictions to the right of those above. To simplify our example, we exclude the **atlegend** with the brief option:

```
mtable, at(k5=(0 1 2 3)) atmeans right brief coleqnm(2nd)
```

    Expression: Pr(lfp), predict()
    
               | 1st                           2nd               
               |       k5     Pr(y)     Pr(y)        k5     Pr(y)
     ----------+-------------------------------------------------
             1 |        0     0.656     0.637         0     0.656
             2 |        1     0.322     0.342         1     0.322
             3 |        2     0.105     0.129         2     0.105
             4 |        3     0.028     0.038         3     0.028

The results on the left labeled 1st are from the first time we ran mtable to compute the average predicted probability. The next two columns, labeled 2nd, are predictions at the mean.

Soon we will show how to make the output more effective by removing the repetition of the **k5** column and using better labels. First, however, we need to explain how the levels of covariates are displayed when combining tables. Here is the output we would obtain if **we had not used the brief option**:

```
mtable, at(k5=(0 1 2 3)) atmeans right coleqnm(2nd)
```

    Expression: Pr(lfp), predict()
    
               | 1st                 2nd               
               |       k5     Pr(y)        k5     Pr(y)
     ----------+---------------------------------------
             1 |        0     0.637         0     0.656
             2 |        1     0.342         1     0.322
             3 |        2     0.129         2     0.105
             4 |        3     0.038         3     0.028
    
    Specified values where .n indicates no values specified with at()
    
               |                              2.        3.        1.        1.                    
               |  No at()      k618      agecat    agecat        wc        hc       lwg       inc
     ----------+---------------------------------------------------------------------------------
         Set 1 |       .n         .           .         .         .         .         .         .
       Current |        .      1.35        .385      .219      .282      .392       1.1      20.1


The row labeled Set 1 contains values from the first use of mtable whose predictions are labeled 1st. **The .n in the column labeled Noat() indicates that the predictions were made without an at() specification; .n stands for “no covariates specified”. The second row, labeled Current, lists the mean values of each variable from the most recent (that is, current) use of mtable**; these correspond to the predictions in the columns labeled 2nd.


We can make the same table more clear by using additional options. First, there is no reason for the values of k5 to appear twice. **Specifying atvars(_none) in the second call of mtable removes this redundancy**. Second,** the two columns of predicted probabilities should have different labels, which is accomplished with the estname() option.** We will call them asobserved and atmeans:

```
quietly mtable, at(k5=(0 1 2 3)) estname(asobserved)
mtable, at(k5=(0 1 2 3)) atmeans atvars(_none) estname(atmeans) right brief
```

        Expression: Pr(lfp), predict()
        
                   |       k5  asobserved   atmeans
         ----------+-------------------------------
                 1 |        0       0.637     0.656
                 2 |        1       0.342     0.322
                 3 |        2       0.129     0.105
                 4 |        3       0.038     0.028


Next, we add a title to the table with the option title(Predicted probability of labor force participation). Finally, the numbers in the left column are not needed because the content of the rows is clear from the levels of k5. We can remove these with the norownum option. Combining these, we get a table that is close to what we might include in a paper:

```
quietly mtable, at(k5=(0 1 2 3)) estname(asobserved)
mtable, at(k5=(0 1 2 3)) atmeans atvars(_none) estname(atmeans) ///
    title(Predicted probability of labor force participation) ///
    right brief norownum
```

Expression: Pr(lfp), predict()

           |       k5  asobserved   atmeans
 ----------+-------------------------------
         1 |        0       0.637     0.656
         2 |        1       0.342     0.322
         3 |        2       0.129     0.105
         4 |        3       0.038     0.028

. 
end of do-file

. do "C:\Users\lenovo\AppData\Local\Temp\STD3d18_000000.tmp"

. quietly mtable, at(k5=(0 1 2 3)) estname(asobserved)

. mtable, at(k5=(0 1 2 3)) atmeans atvars(_none) estname(atmeans) ///
>     title(Predicted probability of labor force participation) ///
>     right brief norownum

        Predicted probability of labor force participation
        
        Expression: Pr(lfp), predict()
        
              k5  asobserved   atmeans
        ------------------------------
               0       0.637     0.656
               1       0.342     0.322
               2       0.129     0.105
               3       0.038     0.028

### 4.5 Marginal effects: Changes in predictions

**Marginal effects are estimates of the change in an outcome for a change in one independent variable, holding all other variables constant.** Here we provide an overview of the commands and basic concepts for computing marginal effects. A detailed discussion of marginal effects, along with substantive applications of alternative measures of change, is given in later chapters, especially chapter 6. **We begin by discussing margins, which computes marginal effects with the dydx() option, and we then show how mtable can do the same thing. Because marginal effects are such a useful summary of effects in nonlinear models, we created mchange to easily compute many types of changes and present them in a compact table.**

#### 4.5.1 Marginal effects using margins

margins can calculate the change in a predicted quantity as an independent variable changes, holding other variables constant. The prediction can be anything that margins can estimate. **The variables for which changes are calculated are specified using the dydx(varlist) option, where dydx(*) indicates that changes for all independent variables are to be computed.**For example,

```
use binlfp4, clear
logit lfp k5 i.agecat i.wc inc, nolog
margins, dydx(*)
```

    Average marginal effects                                   Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    dy/dx wrt:  k5 2.agecat 3.agecat 1.wc inc
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |      dy/dx   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              k5 |      -0.29       0.03    -8.35   0.000        -0.36       -0.22
                 |
          agecat |
          40-49  |      -0.11       0.04    -2.72   0.007        -0.19       -0.03
            50+  |      -0.24       0.05    -5.30   0.000        -0.33       -0.15
                 |
              wc |
        college  |       0.22       0.04     6.16   0.000         0.15        0.29
             inc |      -0.01       0.00    -4.30   0.000        -0.01       -0.00
    ------------------------------------------------------------------------------
    Note: dy/dx for factor levels is the discrete change from the base level.


The amount of change in a regressor that is used to calculate the change in the prediction depends on whether the variable is a continuous or a factor variable, where Stata assumes variables are continuous unless specified as factor variables with the i. notation.

In our example, k5 and inc are continuous while agecat and wc are factor variables. 

* **For a continuous variable, margins estimates the marginal change, which is the partial derivative or instantaneous rate of change in the estimated quantity with respect to a given variable, holding other variables constant.** 
* **For factor variables, margins calculates the discrete change, which is the difference in the prediction when the factor variable is 1 compared with the prediction when the variable is 0.**
* For the binary variable wc, this is the change in the probability of being in the labor force if the wife attended college compared with if she did not attend college. 
* For multiple-category factor variables, the change is from the base category to the value listed in column dy/dx. For i.agecat in this example, the row labeled 40-49 is the change in the probability for a change in agecat from the excluded base category 30-39 to the category 40-49.

**It bears repeating that margins only calculates the discrete change for variables specified with the i. factor-variable notation.** For example (using underlining to highlight the differences between the two commands), although logit lfp k5 i.agecat wc inc and logit lfp k5 i.agecat i.wc inc yield the same estimates of the regression coefficients, the values of dydx() computed by margins will differ. 

In the first specification, wc is not a factor variable, so margins computes the partial derivative with respect to wc; 

in the second specification, i.wc is a factor variable, so margins computes the discrete change. Almost certainly in this context, you want the discrete change, and so factor-variable notation must be used when fitting the model.

#### 4.5.2 Marginal effects using mtable

**Showing how mtable can compute the same results as margins provides an opportunity to illustrate the mechanics of how discrete changes are computed and to extend our discussion of how m table can display different estimates in a single table.** We use a pair of mtable commands to compute predicted probabilities, first, when wc is 1 and then when wc is 0. Using two mtable commands rather than a single command with at(wc=(0 1)) allows us to have different row labels for each prediction by adding the rownameO option. **The option below indicates that the results from the second mtable command should be stacked below those from the first mtable:**

```
logit lfp k5 i.agecat i.wc inc, nolog
quietly mtable, at(wc=1) rowname(wc=1) statistics(ci) estname(Pr_LFP)
mtable, at(wc=0) rowname(wc=0) statistics(ci) estname(Pr_LFP) below
```

    Expression: Pr(lfp), predict()
    
               |   Pr_LFP        ll        ul
     ----------+-----------------------------
          wc=1 |    0.728     0.671     0.784
          wc=0 |    0.506     0.466     0.546
    
    Specified values of covariates
    
               |       wc
     ----------+---------
         Set 1 |        1
       Current |        0


The first row contains the average predicted probability of labor force participation under the assumption that all women went to college, while the second row contains predictions assuming that none of the women went to college. Next, we compute the discrete change by using the **dydx(wc)** option:

```
mtable, dydx(wc) rowname(wc=1 - wc=0) statistics(ci) estname(Pr_LFP) below brief
```

    Expression: Pr(lfp), predict()
    
                  |   Pr LFP        ll        ul
     -------------+-----------------------------
             wc=1 |    0.728     0.671     0.784
             wc=0 |    0.506     0.466     0.546
      wc=1 - wc=0 |    0.222     0.151     0.292
    

#### 4.5.3 Posting predictions and using mlincom
Computing change is also an ideal venue for introducing the idea of posting estimates. To explain this powerful feature of **margins**, we need to review how estimation commands work. **After a regression model is fit, Stata saves the results in memory as what are called ereturns. The coefficient estimates are saved in the matrix e(b) and the variance-covariance of the estimates in e(V). Commands may subsequently use ereturns to compute additional quantities.** For example, the test command uses **e(b)** and **e(V)** to compute Wald tests of linear hypotheses, and **lincom** uses these matrices to estimate linear combinations of the estimates. We could also test nonlinear hypotheses with testnl or compute nonlinear functions of estimates with **nlcom**.

Just like regression estimation commands, **margins** computes estimates and their variance-covariance matrix. By default, these are saved in the return matrices **r(b)** and **r(V)** so that **margins** does not overwrite **e(b)** and **e(V)** from the regression model. Because the results of the regression model are not disturbed, we can run multiple margins commands without refitting the regression model. The **post** option for **margins** replaces the matrices **e(b)** and **e(V)** from the regression model with the estimates from **margins**. Once this is done, test can be used to test linear hypotheses about the predictions from **margins**, and **lincom** can be used to estimate linear combinations of the predictions. Adding the **post** option to mtable does the same thing.

To illustrate posting, we compute the discrete change for wc from the last example. We start by saving the estimation results from **logit** so that we can restore them after we finish analyzing the estimates with **mtable**.

```
use binlfp4, clear
logit lfp k5 i.agecat i.wc inc, nolog
estimates store model1
```

With **mtable**, we compute predicted probabilities at two levels of wc and use the **post** option to save the estimated predictions and their covariance matrix to **e(b)** and **e(V)**:

```
mtable, at(wc=(1 0)) post
```

    Expression: Pr(lfp), predict()
    
               |       wc     Pr(y)
     ----------+-------------------
             1 |        1     0.728
             2 |        0     0.506
    
    Specified values where .n indicates no values specified with at()
    
               |  No at()
     ----------+---------
       Current |       .n

To see the posted predictions, we list e(b)

```
matlist e(b)
```

    
                 |         1.         2.
                 |       _at        _at 
    -------------+---------------------
              y1 |  .7276954    .505965 

Next, we estimate the differences between these predictions by using lincom. **The lincom command requires us to specify the difference with the symbolic names of the estimates, which are shown as the column names of e(b)**:

```
lincom _b[1._at] - _b[2._at]
```

     ( 1)  1bn._at - 2._at = 0
    
    ------------------------------------------------------------------------------
                 | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             (1) |       0.22       0.04     6.16   0.000         0.15        0.29
    ------------------------------------------------------------------------------

The estimated discrete change of 0.2217 matches the earlier results from dydx(wc).

Personally, we find names like **_b[l._at]**and such to be cumbersome, so we created the **mlincom** command, which **allows you to refer to an estimate by its position rather than by its name.** Here we compute the difference between the first and second predictions:

```
mlincom 1 - 2
```

                 |   lincom    pvalue        ll        ul 
    -------------+---------------------------------------
               1 |    0.222     0.000     0.151     0.292 

Options for **mlincom** (type help mlincom for details) allow you to select which statistics you want to see, add labels, and combine results from multiple **mlincom** commands.

Regardless of whether we used mlincom or lincom, the estimation results from logit are no longer active. To run additional margins or m* commands, or to use test or lrtest on the regression estimates, we must restore the logit estimates:

```
estimates restore model1
```

**In this simple example, there is no advantage to using post and mlincom because mtable, dydx(wc) is much simpler.**


#### 4.5.4 Marginal effects using mchange

**Marginal effects are so fundamental for interpreting nonlinear models that we created mchange to make it simple to create compact tables containing many types of marginal effects.** By default, changes are computed as observed. **That is, the change is computed for each observation in the estimation sample and then averaged.** These are sometimes referred to as average marginal effects. If we want the marginal effects at the mean instead, we can use the **atmeans** option, or we can set specific values of the independent variables at which changes are computed by using the **at()** option.

To see how this command differs from m argins, dydx(*), let’s first consider what m change provides by default:


```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
mchange
```
    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                    |    Change    p-value 
    ----------------+---------------------
    k5              |                     
                 +1 |    -0.281      0.000 
                +SD |    -0.153      0.000 
           Marginal |    -0.289      0.000 
    k618            |                     
                 +1 |    -0.014      0.337 
                +SD |    -0.018      0.337 
           Marginal |    -0.014      0.335 
    agecat          |                     
     40-49 vs 30-39 |    -0.124      0.002 
       50+ vs 30-39 |    -0.262      0.000 
       50+ vs 40-49 |    -0.138      0.002 
    wc              |                     
      college vs no |     0.162      0.000 
    hc              |                     
      college vs no |     0.028      0.508 
    lwg             |                     
                 +1 |     0.120      0.000 
                +SD |     0.072      0.000 
           Marginal |     0.127      0.000 
    inc             |                     
                 +1 |    -0.007      0.000 
                +SD |    -0.086      0.000 
           Marginal |    -0.007      0.000 
    
    Average predictions
    
                 | not in LF      in LF 
    -------------+---------------------
      Pr(y|base) |     0.432      0.568 

**For continuous variables (that is, those not specified as i.variable_name), the rows labeled Marginal contain the average marginal changes and are identical to what is computed using margins, dydx(*).** 

**For binary factor variables, such as i.wc, mchange computes the average discrete change as the variable changes from 0 to 1.** In Stata 13 and later, value labels are used to label the change if available (for example, college vs no); otherwise, values are used (1 vs 0). 

**For categorical factor variables, such as i.agecat, mchange computes differences between all pairs of categories (referred to as contrasts).**In Stata 13 and later, these contrasts are labeled with the value labels associated with the categories. The contrasts 40-49 vs 30-39 and 50+ vs 30-39 are the same as shown in the output from margins, dydx(*). The comparison 50+ vs 40-49 was computed by mchange with margins, pwcompare.

**By default, for continuous variables, mchange also computes two types of discrete changes. The unit change, labeled +1, is the change in the prediction as a variable changes from its observed value to its observed value plus 1. The standard deviation change, labeled +SD, is the change in the prediction as a variable changes one standard deviation from its observed value.**

mchange has many options, a few of which we discuss here. A full description of mchange’s functionality is provided with help mchange, and many examples are provided in later chapters.

The amount (*amount-types*) option specifies the amount of change for continuous variables. The following are available:


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.20.11.png" style="width:650px;height:250px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The default amounts are amount (one sd marginal), which were described above. In Stata 11 and 12, changes of 1 or 1 standard deviation cannot be computed and will appear as .m in the results. **By default, changes of 1 and 1 standard deviation are uncentered. The centered option requests changes that are centered; a centered unit change is the change from $x - (1/2) to x + (1/2)$ rather than from $x$ to $x + 1$.**

**The amount (range) option computes the discrete changes as x changes from its minimum to maximum, but it can also be used with the trim(#) option to compute the change between other percentiles.** For example, we could estimate the average change in the probability of labor force participation if family income changes from the 25th percentile of income to the 75th percentile by using **trim(25)** with **amount (range)**:

```
mchange inc, amount(range) trim(25)
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                 |    Change    p-value 
    -------------+---------------------
    inc          |                     
      25% to 75% |    -0.084      0.000 
    
    Average predictions
    
                 | not in LF      in LF 
    -------------+---------------------
      Pr(y|base) |     0.432      0.568 


**The delta(#) option computes a change of # units instead of a standard deviation change.** For example, if income is measured in thousands of dollars. To estimate the average change in labor force participation if income increased by $5,000, we use delta(5):

```
mchange inc, amount(sd) delta(5)
```
    
    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                 |    Change    p-value 
    -------------+---------------------
    inc          |                     
          +delta |    -0.037      0.000 
    
    Average predictions
    
                 | not in LF      in LF 
    -------------+---------------------
      Pr(y|base) |     0.432      0.568 
    
    1: Delta equals 5.

**The statistics (statistics-types) option allows you to select which statistics related to the marginal effect to display.** By default, mchange provides the estimated change and the p-value from a test of the hypothesis that the effect is 0. The following statistics are available:

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.1.png" style="width:650px;height:350px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

We can use mchange, amount (all) statistics (all) to compute all the statistics for all types of changes, which is a lot of information. For just one variable, here is what we get:

```
mchange inc, amount(all) statistics(all)
```
    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                 |    Change    p-value         LL         UL    z-value    Std Err       From         To 
    -------------+---------------------------------------------------------------------------------------
    inc          |                                                                                       
          0 to 1 |    -0.006      0.000     -0.009     -0.004     -5.454      0.001      0.706      0.700 
              +1 |    -0.007      0.000     -0.011     -0.004     -4.419      0.002      0.568      0.561 
             +SD |    -0.086      0.000     -0.124     -0.048     -4.404      0.019      0.568      0.483 
           Range |    -0.593      0.000     -0.761     -0.424     -6.883      0.086      0.706      0.114 
        Marginal |    -0.007      0.000     -0.010     -0.004     -4.427      0.002         .z         .z 
    
    Average predictions
    
                 | not in LF      in LF 
    -------------+---------------------
      Pr(y|base) |     0.432      0.568 


By selecting which statistics we want to show, mchange can easily replicate what took several steps with mtable earlier. We use a varlist to select variable wc and option statistics (start end est pvalue) to display the start and end values leading to the change that is shown along with its p-value:

```
mchange wc, statistics(start end est pvalue)
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                   |      From         To     Change    p-value 
    ---------------+-------------------------------------------
    wc             |                                           
     college vs no |     0.525      0.688      0.162      0.000 
    
    Average predictions
    
                 | not in LF      in LF 
    -------------+---------------------
      Pr(y|base) |     0.432      0.568 


By default, mchange computes average marginal effects (see section 6.2 for a detailed discussion). 

* You can compute marginal effects at the mean by adding the atmeans option. 
* Or you can use at() to compute changes at specific values of the independent variables. 
* Finally, if you use factor-variable notation to specify interactions or polynomial terms, mchange will compute marginal effects by making the appropriate changes among linked variables. For example, if your model includes c.age##c.age, then mchange age will change both age and age-squared. 
* The mchange command does a lot of work behind the scenes and can take a long time to run in models such as ologit or mlogit when there are many outcome categories. For example, after running our baseline model for ologit with four categories and eight variables, mchange, amount (all) runs 40 margins commands, 32 lincom commands, and summarizes 1,123 lines of output in a 46-line table.

### 4.6 Plotting predictions

For continuous variables, graphs can effectively summarize effects. The Stata command **marginsplot** plots the predictions from the most recently run margins. Our **mgen** command can also be used to plot results from margins. **The major difference between the commands is that marginsplot creates plots, while mgen generates variables that can be used with Stata’s graphing commands. The former approach is convenient, but ultimately limited because it allows you to plot only a single outcome category from a single model in a graph.**

#### 4.6.1 Plotting predictions with marginsplot
marginsplot uses results from the preceding margins command. For example, here we plot the predicted probabilities of labor force participation over the ages 20 to 80 for women who attended college and those who did not:

```
use binlfp4, clear
logit lfp k5 k618 age i.wc i.hc lwg inc, nolog
margins, at(age=(20(10)80) wc=(0 1)) atmeans
marginsplot
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.2.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


#### 4.6.2 Plotting predictions with marginsplot
The mgen command generates variables that can be plotted using Stata’s graph commands. Like mtable and mchange, mgen runs margins for you and accepts most of the options that can be used with margins. **The most important options for graphing are at(), which is used to specify the range of the variable on the x-axis and the levels of other variables, and atmeans, if you want to hold other variables at the mean.**

Here is a simple example that uses mgen to create a variable containing predictions as income increases from $0$ to $100,000$ in increments of $10,000$:

```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
mgen, at(inc=(0(10)100)) stub(A) atmeans
```

    Predictions from: margins, at(inc=(0(10)100)) atmeans predict(pr)
    
    Variable   Obs Unique      Mean        Min       Max  Label
    -----------------------------------------------------------------------------------------------------
    Apr1        11     11  .3608011   .0768617  .7349035  pr(y=in LF) from margins
    All1        11     11  .2708139  -.0156624  .6641427  95% lower limit
    Aul1        11     11  .4507883   .1693859  .8056643  95% upper limit
    Ainc        11     11        50          0       100  Family income excluding wife's
    -----------------------------------------------------------------------------------------------------
    
    Specified values of covariates
    
                                  1.         3.         1.         1.           
           k5       k618     agecat     agecat         wc         hc        lwg 
    ---------------------------------------------------------------------------
     .2377158   1.353254   .3851262   .2191235   .2815405   .3917663   1.097115 

**The option stub(stubname) provides the first letters to be used in the names of the variables that are generated.** We recommend a stub that differs from the starting letters of variables in the dataset; then, afterward, the variables can be easily deleted by typing drop stubname*. **If the variable names in your dataset are all lowercase, an uppercase stub works well for this purpose. If you do not use the stub() option, the default stub is an underscore, leading to variable names such as _pr1. If you want to overwrite existing variables, perhaps while debugging the command, you can include the option replace.**

In our example, mgen generated four variables: Aprl with the predicted probabilities, Alll and Aull with the lower and upper bounds of the confidence interval for the prediction, and Ainc with values of inc for each prediction. The values of Ainc are determined by the at() option. The summary statistics for generated variables show that inc ranges from 0 to 100, with predicted probabilities ranging from 0.08 to 0.73. We can list these values:

```
list Apr Ainc in 1/12, clean
graph twoway connected Apr Ainc 
```


               Apr1   Ainc  
      1.   .7349035      0  
      2.   .6613024     10  
      3.   .5789738     20  
      4.   .4920058     30  
      5.    .405519     40  
      6.    .324523     50  
      7.   .2528245     60  
      8.   .1924535     70  
      9.   .1437253     80  
     10.   .1057196     90  
     11.   .0768617    100  
     12.          .      .  
    

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.3.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

We can run mgen multiple times to generate variables with predictions at different levels of variables that are not varying. Here we use quietly to suppress the output from mgen, and we create variables with predictions at each level of agecat. The results are then plotted using a single graph command:

```
quietly mgen, at(inc=(0(10)100) agecat=1) atmeans stub(A30) predlabel(Age 30-39)
quietly mgen, at(inc=(0(10)100) agecat=2) atmeans stub(A40) predlabel(40-49)
quietly mgen, at(inc=(0(10)100) agecat=3) atmeans stub(A50) predlabel(50+)
graph twoway connected A30pr A40pr A50pr A50inc, ytitle("Pr(In Labor Force)") ///
  xtitle("Income") legend(cols(3))
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.4.png" style="width:650px;height:350px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

#### 4.6.3 Predictions over multiple outcome values

**The greatest advantage of mgen over marginsplot occurs when you want to plot predictions for multiple outcomes (not multiple lines for the same outcome but different outcomes) or to combine predictions from different models (for example, plot the predicted probabilities with different sets of control variables).** 

**Because a single margins command computes predictions for a single outcome category, predictions for multiple outcomes require running margins multiple times. marginsplot can only do plots based on running margins once; mgen does this automatically and produces variables named in a consistent fashion that makes plotting simple.**As an example, we use a model from our chapter on ordinal outcomes:

```
use gssclass4, clear
ologit class i.fem i.white i.year i.ed age inc, nolog
mgen, at(age=(20(10)80)) stub(B) atmeans
```

    Predictions from: margins, at(age=(20(10)80)) atmeans predict(outcome())
    
    Variable   Obs Unique      Mean       Min       Max  Label
    -----------------------------------------------------------------------------------------------------------
    Bpr1         7      7  .0481717  .0247463  .0799486  pr(y=lower) from margins
    Bll1         7      7  .0425263  .0206772  .0705518  95% lower limit
    Bul1         7      7   .053817  .0288155  .0893454  95% upper limit
    Bage         7      7        50        20        80  age of respondent
    BCpr1        7      7  .0481717  .0247463  .0799486  pr(y<=lower)
    Bpr2         7      7  .4471604   .321645  .5647999  pr(y=working) from margins
    Bll2         7      7  .4283944  .2954391   .544966  95% lower limit
    Bul2         7      7  .4659264  .3478508  .5846338  95% upper limit
    BCpr2        7      7  .4953321  .3463913  .6447485  pr(y<=working)
    Bpr3         7      7   .484427  .3450458  .6195026  pr(y=middle) from margins
    Bll3         7      7  .4645732  .3225423  .5927632  95% lower limit
    Bul3         7      7  .5042807  .3675494   .646242  95% upper limit
    BCpr3        7      7  .9797591  .9658939  .9897943  pr(y<=middle)
    Bpr4         7      7   .020241  .0102057  .0341061  pr(y=upper) from margins
    Bll4         7      7  .0166152  .0082046  .0276169  95% lower limit
    Bul4         7      7  .0238667  .0122068  .0405953  95% upper limit
    BCpr4        7      7         1         1         1  pr(y<=upper)
    -----------------------------------------------------------------------------------------------------------
    
    Specified values of covariates
    
            1.         1.         2.         3.         2.         3.           
       female      white       year       year       educ       educ     income 
    ---------------------------------------------------------------------------
     .5491103   .8140569   .4510676   .3099644   .5818505   .2414591   68.07737 
    

To understand what mgen has done, we list some of the variables that were generated:

```
list Bage Bpr1 Bpr2 Bpr3 Bpr4 in 1/8, clean
```

           Bage       Bpr1       Bpr2       Bpr3       Bpr4  
      1.     20   .0799486   .5647999   .3450458   .0102057  
      2.     30   .0660995   .5303931   .3910067   .0125007  
      3.     40   .0545074   .4917871   .4384017   .0153039  
      4.     50   .0448505   .4502865   .4861394   .0187237  
      5.     60   .0368379   .4072433    .533029   .0228899  
      6.     70   .0302114   .3639681   .5778638   .0279568  
      7.     80   .0247463    .321645   .6195026   .0341061  
      8.      .          .          .          .          .  

Variables **BPrl** through **BPr4** contain predicted probabilities for the four categories of class as they change by **age**. Most simply, we could graph these with **twoway line Bpr1 Bpr2 Bpr3 Bpr4 Bage**. Interpreting such graphs and making them more effective are topics discussed in subsequent chapters.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.5.png" style="width:650px;height:350px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

**mgen’s defaults for handling multiple outcome values depend on model type. The way mgen distinguishes model types is by what options predict supports for a model. The following table summarizes the major types of models.**


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.6.png" style="width:650px;height:250px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

For categorical models, mgen automatically computes predictions for all outcomes. The predicted probabilities for outcome # are named stubpr#. The cumulative probabilities Pr(y < #|x) are also computed and named stubCpr#.


#### 4.6.4 Observed and average predicted proportion using mgen, meanpred

By default, mgen generates variables where each row is a prediction from margins based on specified values of the independent variables. When option **meanpred** is used, mgen also generates variables in which the rows correspond to values of the outcome. These variables allow you to compare observed proportions versus average predicted probabilities, an important tool for count models in chapter 9. Variables generated by mgen, meanpred are as follows:

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.7.png" style="width:650px;height:350px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

**Other mgen options**

* atvars(varlist) specifies the independent variables for which variables should be generated. _none indicates no variables. The default is to generate variables for all independent variables whose values vary over at().
* noci and allstats modify which variables are generated. When noci is used, no variables for confidence intervals are generated. When allstats is used, variables for the p-value (stubpval), z statistic (stubz), and standard error (stubse) are generated along with the prediction and confidence interval.
* level(#) sets the level of the confidence interval from 10 to 99.99.
* predname(predname) specifies the name of the variable (plus the stub) used for predictions generated by margins. By default, this is pr for probabilities and is margins otherwise.
* nolabel indicates that value labels are not to be used in labeling generated variables.
* valuelength() changes the length at which labels are truncated.

### 4.7 Interpretation of parameters
Although the primary methods of interpretation in this book are based on predictions from the model, some methods of interpretation involve simple transformations of the model's parameters. For some estimation commands, there are options to list transformations of the estimates, such as the or option to list odds ratios for logit or the beta option to list standardized coefficients for regress. **Although Stata is commendably clear in explaining the meaning of the estimated parameters, in some models it is easy to be confused about proper interpretations.** For example, the zip model (discussed in chapter 9) simultaneously fits a binary and a count model, and it is easy to be confused regarding the direction of the effects.

For the estimation commands considered in this book, plus some not considered here, **our listcoef command lists estimated coefficients in ways that facilitate interpretation. You can list coefficients selected by name or by significance level, list transformations of the coefficients, and request help on interpretation.** In fact, often you will not need the normal output from the estimation. You could suppress this output with the prefix quietly (for example, quietly logit lf pk5 wche) and then use the listcoef command.

#### 4.7.1 The listcoef command
The listcoef command

listcoef \[varlist\] [, [factor|percent|std] adjacent gt lt negative positive pvalue (#) nolabel constant off help\]

**where varlist indicates that coefficients for only these variables are to be listed. If no varlist is given, then coefficients for all variables are listed. The varlist should not use factor-variable notation.**For example, for the model logit lfp i.age cat i.wc lwg, the command listcoef agecat will show the coefficients for 2.agecat and 3.agecat. If agecat##c.lw g was in the model, estimates for all coefficients that include agecat would be listed.

##### 4.7.1.1 Options for types of coefficients

Depending on the model and the specified options, **listcoef computes standardized coefficients, factor changes in the odds or expected counts, or percentage changes in the odds or expected counts.**More information on these types of coefficients is provided below, as well as in the chapters that deal with specific types of outcomes.

* **factor** requests factor change coefficients indicating how many times larger or smaller the outcome is. In some cases, these coefficients are odds ratios.
* **percent** requests percentage change coefficients indicating the percentage change in the outcome.
* **std** requests that coefficients be standardized to a unit variance for the independent variables or the dependent variable. For models that can be derived from a latent-dependent variable (for example, the binary logit model), the variance of the latent outcome is estimated.

The following options (details on these options are given below) are available for each estimation command. If an option is the default, it does not need to be specified.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.8.png" style="width:650px;height:250px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

##### 4.7.1.2 Options for mlogit, m probit, and slogit

For the **mlogit**, **mprobit**, and **slogit** commands discussed in chapter 8, **listcoef** can show the coefficients for each pair of outcome categories. When these models are used with ordered outcomes, it is helpful to look at a subset of these coefficients. The following options are for this purpose:

* **adjacent** specifies that only the coefficients from comparisons in which the two category values are adjacent will be printed (for example, comparing outcome 1 versus 2, and 2 versus 1, but not 1 versus 3). This option can be combined with **gt** or **lt**.
* **gt** specifies that only the coefficients from comparisons in which the first category has a larger value than the second will be printed (for example, comparing outcome 2 versus 1, but not 1 versus 2).
* **lt** specifies that only the coefficients from comparisons in which the first category has a smaller value than the second will be printed (for example, comparing outcome 1 versus 2, but not 2 versus 1).
* **negative** specifies that only negative coefficients be shown. **This option cannot be combined with adjacent, gt, or lt.**
* **positive** specifies that only positive coefficients be shown. **This option cannot be combined with adjacent, gt, or lt.**

##### 4.7.1.3 Other options
* **pvalue(#)** specifies that only coefficients significant at the # significance level or smaller will be printed. For example, pvalue(0.05) specifies that only coefficients significant at the 0.05 level should be listed. If pvalue is not given, all coefficients are listed.
* **nolabel** requests th at category numbers rather than value labels be used in the output
* **constantoff** specifies to not include the constant(s) in the output. By default, they are listed. In Stata 10 and earlier, the default is constantoff, and you must use the option constant to list the constants.
* **help** gives details for interpreting each coefficient.


#### 4.7.2 Standardized coefficients

**std** requests coefficients after variables have been standardized to a unit variance. Standardized coefficients are computed as follows.

**x-standardized coefficients.** The linear regression model can be expressed as

$$y=\beta_{0}+\beta_{1}x_{1}+\beta_{2}x_{2}+\varepsilon $$

Let. $\sigma_{k}$ be the standard deviation of $x_k$ Then dividing each $x_k$ by $\sigma_{k}$ and multiplying the corresponding $\beta_{k}$ by $x_k$ becomes

$$y=\beta_{0}+\left(\sigma_{1}\beta_{1}\right)\frac{x_{1}}{\sigma_{1}}+\left(\sigma_{2}\beta_{2}\right)\frac{x_{2}}{\sigma_{2}}+\varepsilon $$

$\beta_{k}^{S_{z}}=\sigma_{k}\beta_{k}$is an x-standardized coefficient. For a continuous variable, $\beta_{k}^{S_{z}}$ can be interpreted as follows:

For a standard deviation increase in $x_k$, $y$ is expected to change by$\beta_{k}^{S_{z}}$ units, holding other variables constant.

The same method of x-standardization can be used in all the other models we consider in this book.

**$y-$ and $y*$-standardized coefficients.** To standardize the dependent variable, let ay be the standard deviation of $y$. We standardize $y$ by dividing (4.2) by $\sigma_{k}$:

$$\frac{y}{\sigma_y}=\frac{\beta_0}{\sigma_y}+\frac{\beta_1}{\sigma_y}x_1+\frac{\beta_2}{\sigma_y}x_2+\frac{\varepsilon}{\sigma_y}$$

Then $\beta_{k}^{S_{y}}=\beta_{k}/\sigma_{y}$ is a $y$-standardized coefficient th a t can be interpreted as follows:

For a unit increase in $x_k$,$y$ is expected to change by $\beta_{k}^{S_{y}}$ standard deviations, holding other variables constant.

For a binary independent variable,Having characteristic $x_k$ as opposed to not having the characteristic results in an expected change in $y$ of $\beta_{k}^{S_{y}}$ standard deviations, holding other variables constant.

Or more simply,
Having characteristic $x_k$ results in an expected change in $y$ of $\beta_{k}^{S_{y}}$ standard deviations, holding other variables constant

In models with a latent dependent variable, the equation $y^*=\beta_0+\beta_1x_1+\beta_2x_2+\varepsilon $can be divided by $\widehat{\sigma}_y$ To estimate the variance of the latent variable, the quadratic form is used:

$$\widehat{\mathrm{Var}}(y^{*})=\widehat{\boldsymbol{\beta}}^{\prime}\widehat{\mathrm{Var}}(\mathbf{x})\widehat{\boldsymbol{\beta}}+\mathrm{Var}(\varepsilon)$$

where $\widehat\beta$ is a vector of estimated coefficients and $\widehat{\mathrm{Var}(\mathbf{x})}$ is the covariance matrix for the $x’s$ computed from the observed data. By assumption, $\mathrm{Var}(\varepsilon)=1$ in probit models and $\mathrm{Var}(\varepsilon)=\pi^2/3$ in logit models.

**Fully standardized coefficients.** In the linear regression model, it is possible to standardize both $y$ and the $x's$:

$$\frac{y}{\sigma_y}=\frac{\beta_0}{\sigma_y}+\left(\frac{\sigma_1\beta_1}{\sigma_y}\right)\frac{x_1}{\sigma_1}+\left(\frac{\sigma_2\beta_2}{\sigma_y}\right)\frac{x_2}{\sigma_2}+\frac{\varepsilon}{\sigma_y}$$

Then $\beta_{k}^{S}=(\sigma_{k}\beta_{k})/\sigma_{y}$ is a fully standardized coefficient that can be interpreted as follows:

For a standard deviation increase in $x_k$,$y$ is expected to change $\beta_{k}^{S}$ standard deviations, holding other variables constant.

The same approach can be used in models with a latent-dependent variable $y^*$

##### 4.7.1.3 Example of listcoef for standardized coefficients

Here we illustrate the computation of standardized coefficients for the regression model. Examples for other models are given in later chapters. The standard output from regress is:

```
use science4, clear
regress job i.female i.phdclass mentcit3yr fellow pub1 cit1
```
    
              Source |       SS           df       MS      Number of obs   =       161
    -------------+----------------------------------   F(8, 152)       =      6.18
           Model |  30.5973982         8  3.82467477   Prob > F        =    0.0000
        Residual |  94.0515545       152  .618760227   R-squared       =    0.2455
    -------------+----------------------------------   Adj R-squared   =    0.2058
           Total |  124.648953       160  .779055954   Root MSE        =    .78661
    
    ------------------------------------------------------------------------------
             job | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
          female |
            yes  |      -0.09       0.17    -0.54   0.593        -0.42        0.24
                 |
        phdclass |
           good  |       0.40       0.22     1.82   0.071        -0.03        0.84
         strong  |       0.81       0.23     3.52   0.001         0.35        1.26
          elite  |       0.89       0.24     3.75   0.000         0.42        1.35
                 |
      mentcit3yr |       0.00       0.00     1.00   0.317        -0.00        0.01
          fellow |       0.19       0.13     1.47   0.145        -0.07        0.46
            pub1 |       0.00       0.03     0.02   0.987        -0.05        0.05
            cit1 |       0.01       0.00     1.87   0.064        -0.00        0.02
           _cons |       2.06       0.21     9.64   0.000         1.64        2.49
    ------------------------------------------------------------------------------



if we use listcoef,we get

```
listcoef, help
```

    regress (N=161): Unstandardized and standardized estimates 
    
      Observed SD:  0.8826
      SD of error:  0.7866
    
    -------------------------------------------------------------------------------
                 |         b        t    P>|t|    bStdX    bStdY   bStdXY     SDofX
    -------------+-----------------------------------------------------------------
          female |
            yes  |   -0.0886   -0.535    0.593   -0.038   -0.100   -0.043     0.430
                 |
        phdclass |
           good  |    0.4003    1.817    0.071    0.181    0.454    0.206     0.453
         strong  |    0.8090    3.520    0.001    0.362    0.917    0.410     0.447
          elite  |    0.8871    3.750    0.000    0.418    1.005    0.474     0.471
                 |
      mentcit3yr |    0.0024    1.004    0.317    0.075    0.003    0.085    31.644
          fellow |    0.1947    1.465    0.145    0.098    0.221    0.111     0.501
            pub1 |    0.0004    0.016    0.987    0.001    0.000    0.001     3.228
            cit1 |    0.0077    1.866    0.064    0.163    0.009    0.185    21.242
        constant |    2.0630    9.640    0.000        .        .        .         .
    -------------------------------------------------------------------------------
           b = raw coefficient
           t = t-score for test of b=0
       P>|t| = p-value for t-test
       bStdX = x-standardized coefficient
       bStdY = y-standardized coefficient
      bStdXY = fully standardized coefficient
       SDofX = standard deviation of X

By default for **regress**, **listcoef** lists standardized coefficients for all variables. If we are interested in listing coefficients for a subset of variables, we can specify a varlist after **listcoef**. **For factor variables, you should specify only the source name, not the factor-variable notation** (for example, **i.female**) or the name of the variable that is constructed (for example, **2.phdclass**). Here is an example:

```
listcoef female phdclass pub1
```

    regress (N=161): Unstandardized and standardized estimates 
    
      Observed SD:  0.8826
      SD of error:  0.7866
    
    -------------------------------------------------------------------------------
                 |         b        t    P>|t|    bStdX    bStdY   bStdXY     SDofX
    -------------+-----------------------------------------------------------------
          female |
            yes  |   -0.0886   -0.535    0.593   -0.038   -0.100   -0.043     0.430
                 |
        phdclass |
           good  |    0.4003    1.817    0.071    0.181    0.454    0.206     0.453
         strong  |    0.8090    3.520    0.001    0.362    0.917    0.410     0.447
          elite  |    0.8871    3.750    0.000    0.418    1.005    0.474     0.471
                 |
            pub1 |    0.0004    0.016    0.987    0.001    0.000    0.001     3.228
    -------------------------------------------------------------------------------

#### 4.7.3 Factor and percentage change coefficients

In logit models and count models, coefficients can be expressed in two ways:

1. Coefficients can indicate the factor or multiplicative change in the odds, relative risks, or expected count. These are the default for some models or can be requested with the factor option with **listcoef**.
2. Percent changes in these quantities can be requested with the **percent** option.

Details on these coefficients are given in later chapters for each specific model
