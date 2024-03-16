# [Categorical Dependent Var] Chapter_5 ：Models for binary outcomes Estimation, testing, and fit


**This chapter focuses on the two most often used models for binary outcomes, the binary logit and binary probit models, referred to jointly as the binary regression model (BRM). The BRM allows a researcher to explore how each explanatory variable affects the probability of the event occurring.**

The BRM is also the foundation from which more complex models for ordinal, nominal, and count models are derived. Ordinal and nominal regression models are equivalent to simultaneously fitting a set of BRMs. **Although the link is less direct in count models, the Poisson distribution can be derived as the outcome of many binary trials.** Consequently, the principles of fitting, testing, and interpreting binary models provide essential tools that are used in later chapters. Although each chapter of the book is largely self-contained, the two chapters on binary outcomes provide more detailed explanations than later chapters.

**We begin the chapter by reviewing the mathematical structure of the binary regression model. We then examine statistical testing and fit. These discussions are brief, and much of it is intended either as a simple overview or as a review for those who are familiar with the models.**For a complete discussion, see Agresti (2013), Hosmer, Lemeshow, and Sturdivant (2013), or Long (1997). **Although the material in this chapter is fundamental to working with these models, we anticipate that the more important contribution of this book will be in helping you interpret and present results. The issues involved in effective interpretation are extensive enough that we devote a chapter of its own to the topic, to which this chapter might be considered the prelude.**

## 1 The statistical model

There are three ways to derive the BRM, with each method leading to the same statistical model. 
1. First, a latent variable can be hypothesized along with a measurement model relating the latent variable to the observed binary outcome.
2. Second, the model can be constructed as a probability model.
3. Third, the model can be generated as a random utility or discrete choice model. This last approach is not considered in our review; see Long (1997, 155-156) for an introduction and Train (2009) for a detailed discussion.

### 1.1 A latent-variable model

Assume a latent or unobserved variable $y*$ ranging from $-\infty $ to $\infty $ that is related to the observed independent variables by the structural model

$$y_i^*=\mathbf{x}_i\boldsymbol{\beta}+\varepsilon_i$$

where $i$ indicates the observation and $\varepsilon$ is a random error. For a single independent variable, we can simplify the notation to

$$y_{i}^{*}=\alpha+\beta x_{i}+\varepsilon_{i}$$

These equations are identical to those for the linear regression model except —— and this is a big exception —— that the dependent variable is unobserved.

The observed binary dependent variable has two values, typically coded as 0 for a negative outcome (that is, the event did not occur) and 1 for a positive outcome (that is, the event did occur). A measurement equation defines the link between the binary observed variable $y$ and the continuous latent variable $y*$.

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.9.png" style="width:250px;height:100px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Cases with positive values of $y*$ are observed as $y$ = 1, while cases with negative or 0 values of $y*$ are observed as $y$ = 0.


To give a concrete example, imagine a survey item that asks respondents if they agree or disagree with the proposition that “a working mother can establish just as warm and secure a relationship with her children as a mother who does not work”. Obviously, respondents will vary greatly in their opinions. Some people adamantly agree with the proposition, some adamantly disagree, and still others have weak opinions one way or the other. **Imagine an underlying continuum $y*$ of feelings about this item, with each respondent having a specific value on the continuum.**Those respondents with positive values for y* will answer “agree” to the survey question (y = 1) and those with negative values will “disagree” (y = 0). A shift in a respondent’s opinion might move her from agreeing strongly with the position to agreeing weakly with the position, which would not change the response we observe. Or, the respondent might move from weakly agreeing to weakly disagreeing, in which case, we would observe a change from y = 1 to y = 0.


Consider a second example, which we use throughout this chapter. Let y = 1 if a woman is in the paid labor force and let y = 0 if she is not. The independent variables include age, number of children, education, family income, and expected wages. Not all women in the labor force (y = 1) are there with the same certainty. One woman might be close to leaving the labor force, whereas another woman could be firm in her decision.to work. In both cases, we observe y = 1. The idea of a latent y* is that an underlying propensity to work generates the observed state. Although we cannot directly observe the propensity, at some point a change in y* results in a change in what we observe, namely, whether the woman is in the labor force.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.10.png" style="width:550px;height:350px;" alt="图片描述">
  <figcaption><strong>Relationship between latent variable y* and P r(y = 1) for the BRM</strong></figcaption>
</figure>

The latent-variable model for a binary outcome with a single independent variable is shown in figure above. For a given value of $x$

$$\Pr(y=1\mid x)=\Pr(y^{*}>0\mid x)$$

Substituting the structural model and rearranging terms 

$$\Pr(y=1\mid x)=\Pr(\varepsilon>-[\alpha+\beta x]\mid x)$$

which shows how the probability depends on the distribution of the error $\varepsilon$.

Two distributions of $\varepsilon$ are commonly used, both with an assumed mean of 0. First, $\varepsilon$ is assumed to be normal with $\mathrm{Var}(\varepsilon)=1$. This leads to the binary probit model in which (5.1) becomes

$$\Pr(y=1\mid x)=\int_{-\infty}^{\alpha+\beta x}\frac{1}{\sqrt{2\pi}}\exp\left(-\frac{t^{2}}{2}\right)dt$$

Alternatively, $\varepsilon$ is assumed to be distributed logistically with $\mathrm{Var}(\varepsilon)=\pi^2/3$, leading to the binary logit model with the simpler equation

The peculiar value assumed for $\mathrm{Var}(\varepsilon)$ in the logit model illustrates a basic point about the identification of models with latent outcomes. In the linear regression model, $\mathrm{Var}(\varepsilon)$ can be estimated because y is observed. In the BRM, $\mathrm{Var}(\varepsilon)$ cannot be estimated because the dependent variable is unobserved. Accordingly, the model is unidentified unless an assumption is made about the variance of the errors. For probit, we assume $\mathrm{Var}(\varepsilon)$ = 1 because this leads to a simple form of the model. If a different value was assumed, this would simply change the values of the structural coefficients uniformly. In the logit model, the variance is set to \$(\pi^2/3\)$ because this leads to the simple form in (5.2). Although the value assumed for $\mathrm{Var}(\varepsilon)$ is arbitrary, the value chosen does not affect the computed value of the probability (see Long [1997, 49-50] for a simple proof). Changing the assumed variance affects the spread of the distribution and the magnitude of the regression coefficients, but it does not affect the proportion of the distribution above or below the threshold.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.11.png" style="width:550px;height:550px;" alt="图片描述">
  <figcaption><strong>Relationship between the linear model $y^*=\alpha+\beta x+\varepsilon$ and the nonlinear probability model $\mathrm{Pr}(y=1\mid x)=F(\alpha+\beta x)$</strong></figcaption>
</figure>

For both logit and probit, the probability of the event conditional on $x$ is the cumulative density function (CDF) of $\varepsilon$ evaluated at $x\beta$,

$$\Pr(y=1\mid\mathbf{x})=F\left(\mathbf{x}\boldsymbol{\beta}\right)$$

where $F$ is the normal CDF $\text{Φ}$ for the probit model and the logistic CDF A for the logit model. The relationship between the linear latent-variable model and the resulting nonlinear probability model is shown in figure 5.2 for a model with one independent variable. Panel A shows the error distribution for nine values of $x$. The area where $y>0$ corresponds to $\mathrm{Pr}(y=1\mid x)$ and has been shaded. Panel B plots $\mathrm{Pr}(y=1\mid x)$ corresponding to the shaded regions in panel A. As we move from 1 to 2, only a portion of r he thin tail crosses the threshold in panel A, resulting in a small change in $\mathrm{Pr}(y=1\mid x)$ in panel B. As we move from 2 to 3 to 4, thicker regions of the error distribution slide over the threshold, and the increase in $\mathrm{Pr}(y=1\mid x)$ becomes larger. The resulting curve is the well-known S-curve associated with the BRM.

### 1.2 A nonlinear probability model

Can all binary dependent variables be conceptualized as observed manifestations of some underlying latent propensity? Although philosophically interesting, perhaps, the question is of little practical importance, because the BRM can also be derived without appealing to a latent variable. This is done by specifying a nonlinear model relating the $x's$ to the probability of an event. Following Theil (1970), the logit model can be derived by constructing a model in which the predicted $\Pr\left(y=1\mid\mathbf{x}\right)$ is forced to be within the range 0 to 1. For example, in the linear probability model.

$$\Pr\left(y=1\mid\mathbf{x}\right)=\mathbf{x}\boldsymbol{\beta}+\varepsilon $$

the predicted probabilities can be greater than 1 and less than 0. To constrain the predictions to the range 0 to 1, we first transform the probability into the odds,

$$\Omega\left(\mathbf{x}\right)=\frac{\Pr\left(y=1\mid\mathbf{x}\right)}{\Pr\left(y=0\mid\mathbf{x}\right)}=\frac{\Pr\left(y=1\mid\mathbf{x}\right)}{1-\Pr\left(y=1\mid\mathbf{x}\right)}$$

which indicate how often something happens $(y = 1)$ relative to how often it does not happen $(y = 0)$. The odds range from 0 when $\mathrm{Pr}\left(y=1\mid\mathbf{x}\right)=0$ to ∞ when  $\mathrm{Pr}\left(y=1\mid\mathbf{x}\right)=1$. The log of the odds, often referred to as the logit, ranges from $-∞$ to $∞$. This range suggests a model that is linear in the logit:

$$\ln\Omega\left(\mathbf{x}\right)=\mathbf{x}\beta $$

This equation is equivalent to the logit model (5.2). Interpretation of this form of the model often focuses on factor changes in the odds, which are discussed below.

Other binary regression models are created by choosing functions of $x\beta$ that range from 0 to 1. Cumulative distribution functions (CDFs) have this property and readily provide several examples. For example, the CDF for the standard normal distribution results in the probit model.

## 2 Estimation using logit and probit commands

Logit and probit models can be fit with the following commands and their basic options:


>logit depvar [indepvars] [if] [in] [weight] [, noconstant asis or vce(vcetype)]

>probit depvar [indepvars] [if] [in] [weight] [, noconstant asis vce(vcetype)]

* **Variable lists**

depvar is the dependent variable, indepvars is a list of independent variables. If indepvars is not included, Stata fits a model with only an intercept.

>Warning about dependent variable.
>>In binary models, all nonmissing, nonzero values of depvar are classified as positive outcomes, traditionally referred to as successes. Only zero values are considered negative outcomes, which are referred to as failures. Because negative values are nonzero, they are considered to be positive outcomes. To avoid possible confusion, we recommend that you explicitly create a 0/1 variable for use as depvar.

* **Specifying the estimation sample**

**if and in qualifiers.** can be used to restrict the estimation sample. For example, if you want to fit a logit model for only women who went to college, as indicated by the variable wc, you could specify logit lfp k5 k618 age he lwg if wc==1.

**Listwise deletion.** Stata excludes cases in which there are missing values for any of the variables in the model. Accordingly, if two models are fit using the same dataset but have different independent variables, the models may have different samples. We recommend that you use **`mark`** and **`markout`** (discussed in section 3.1.6) to explicitly remove cases with missing data.


* **Weights and complex samples**
  
Both logit and probit can be used with **fweight**, **pweight**, and **iweight**. Survey estimation can be done using svy: **logit** or svy: **probit**. See section 3.1.7 for details.

* **Options**

**`noconstant`** specifies that the model not have a constant term

**`asis`** specifies that estimates for variables that have perfect prediction should be included in the results table. For details, see page 197.

**`or`** (for logit only) reports the odds ratios defined as $\mathrm{exp}(\widehat{\beta})$. Standard errors and confidence intervals are similarly transformed. Alternatively, our listcoef command can be used.

**`vce`**(vcetype) specifies the type of standard errors to be computed. See section 3.1.9 for details.

### 2.1 Example of logit model

Our example is from Mroz’s (1987) study of the labor force participation of women, using data from the 1976 Panel Study of Income Dynamics. The sample consists of 753 white, married women between the ages of 30 and 60 years. The dependent variable **lfp** equals 1 if a woman is in the labor force and equals 0 otherwise. We use **codebook**, compact to list information about the variables we plan to include in our model:

```
use binlfp4, clear
codebook lfp k5 k618 agecat wc hc lwg inc, compact
```

    Variable   Obs Unique      Mean        Min       Max  Label
    -------------------------------------------------------------------------------------------
    lfp        753      2  .5683931          0         1  In paid labor force?
    k5         753      4  .2377158          0         3  # kids < 6
    k618       753      9  1.353254          0         8  # kids 6-18
    agecat     753      3  1.823373          1         3  Wife's age group
    wc         753      2  .2815405          0         1  Wife attended college?
    hc         753      2  .3917663          0         1  Husband attended college?
    lwg        753    676  1.097115  -2.054124  3.218876  Log of wife's estimated wages
    inc        753    621  20.12897  -.0290001        96  Family income excluding wife's
    -------------------------------------------------------------------------------------------

Although the meaning of most of the variables is clear from the label, **lwg** is the log of an estimate of what the wife's wages would be if she was employed, given her other characteristics. Because the outcome is labor force participation, it is important to include what the wife might be expected to earn if she was employed. Following the same reasoning, **inc** is family income excluding whatever the wife earns; this is, therefore, a measure of what the family income would be if the wife was not employed. We consider interpretation later, but it may also help bearing in mind that the data are from 1976. In the United States, prices have risen by just over a factor of 4 between 1976 and 2014, so a change in income of $5,000$ in 1974 is similar to a change in income of $20,000$ in 2014.

Because **agecat** is ordinal, we use **`tabulate`** to examine the distribution among the age groups:
```
tabulate agecat, missing
```
Next, we want to fit the logit model. To be consistent with the naming practice Stata will use, we use **2.agecat** and **3.agecat** to refer to dummy variables indicating whether **agecat==2** and whether **agecat==3**, respectively. By fitting the logit model,

$$\begin{aligned}\Pr(1\mathbf{f}\mathbf{p}=1)&=F(\beta_0+\beta_\mathbf{kf}\mathbf{k}\mathbf{5}+\beta_\text{kG19}\mathbf{k}\text{618}+\beta_\text{2 agecat}2.\text{agecat}\\&+\beta_\text{3.agecat}3.\text{agecat}+\beta_\text{we}\mathbf{w}\mathbf{c}+\beta_\text{he}\mathbf{hc}+\beta_\text{lwg}1\text{wg}+\beta_\text{lac}\mathbf{inc})\end{aligned}$$

we obtain the following results:

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc
```

    Iteration 0:  Log likelihood =  -514.8732  
    Iteration 1:  Log likelihood = -453.10297  
    Iteration 2:  Log likelihood = -452.72408  
    Iteration 3:  Log likelihood = -452.72367  
    Iteration 4:  Log likelihood = -452.72367  
    
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

The information in the header and table of coefficients is in the same form as discussed in chapter 3. The iteration log begins with Iteration 0: log likelihood = -514.8732 and ends with Iteration 4: log likelihood = -452.72367, with the intermediate iterations showing the steps taken in the numerical maximization of the log-likelihood function. **Although this information can provide insights when the model does not converge, in our experience it is of little use in logit and probit, where we have never seen problems with convergence.** Accordingly, when fitting further models, we use the nolog option to suppress the log. **If a model does not converge or the estimates seem "off", we would rerun the model without the `nolog` option.**

Using factor-variable notation for binary variables in this case may seem unnecessary, because we get the same coefficients regardless; but for some of the interpretation techniques we demonstrate later, specifying i.wc instead of just wc in the estimation command is essential to obtain proper results (see section 6.2). As a result, we find it good practice to always enter binary variables into our models with the i. syntax.

By default, the reference or base category of a factor variable is not listed.Adding the **`allbase`** option to an estimation command will display the base reference category. For agecat in the above example, the value 1 is the base category because this is the first value; specifying **`ib3.agecat`** would have used **agecat==3** as the base category instead.

### 2.2 comparing logit and probit

Above, we fit the model with logit, but we could have used probit instead. **An easy way to show how the results would differ is to put them side by side in a single table. We can do this by using estimates table (see [R] estimates table), which is more generally useful for combining results from multiple models into one table.** After fitting the logit model, we use **estimates store** to save the estimates with the name **Mlogit**:

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store Mlogit
```
We then fit a probit model and store the results:

```
probit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store Mprobit
```

Next, we combine the results with estimates table. 
* **`Option b()`** sets the format for displaying the coefficients. 
* **`b(%9.3f)`** lists the estimates in nine columns with five decimal places. 
* Option **t** requests test statistics for individual coefficients—either z tests or f tests depending on the model that was fit. 
* **`Varlable`** labels uses variable labels rather than variable names to label coefficients (the option was named label before Stata 13), with 
* **`varwidth`** 0 indicating how many columns should be used for the labels. Variable names and value labels are used with factor variables.

```
estimates table Mlogit Mprobit, b(%9.3f) t varlabel varwidth(30)
```

> The estimates table output labels the test statistic as t regardless of whether z tests or t are used.

    --------------------------------------------------------
                          Variable |  Mlogit      Mprobit   
    -------------------------------+------------------------
                        # kids < 6 |    -1.392      -0.840  
                                   |     -7.25       -7.50  
                       # kids 6-18 |    -0.066      -0.041  
                                   |     -0.96       -1.01  
                                   |                        
                  Wife's age group |                        
                            40-49  |    -0.627      -0.382  
                                   |     -3.00       -3.06  
                              50+  |    -1.279      -0.780  
                                   |     -4.92       -5.00  
                                   |                        
            Wife attended college? |                        
                          college  |     0.798       0.482  
                                   |      3.48        3.55  
                                   |                        
         Husband attended college? |                        
                          college  |     0.136       0.074  
                                   |      0.66        0.60  
     Log of wife's estimated wages |     0.610       0.371  
                                   |      4.04        4.21  
    Family income excluding wife's |    -0.035      -0.021  
                                   |     -4.24       -4.37  
                          Constant |     1.014       0.622  
                                   |      3.54        3.69  
    --------------------------------------------------------
                                                 Legend: b/t

Comparing results, the estimated logit coefficients are about 1.7 times larger than the probit estimates. For example, the ratios for k5 and inc are 1.66. **This illustrates how the magnitudes of the coefficients are affected by the assumed $\operatorname{Var}(\varepsilon)$.** The ratio of estimates for **hc** is larger because of the large standard errors for these estimates. Values of the **z** tests for logit and probit are quite similar because they are not affected by the assumed $\operatorname{Var}(\varepsilon)$, **but they are not exactly the same because the models assume different distributions of the errors.**


### 2.3 (Advanced) Observations predicted perfectly
We mark this section as advanced because if you work with large samples where your outcome variable is not rare, you may never encounter perfect prediction. If you have smaller samples with binary predictors, you may encounter it regularly.

**Maximum likelihood estimation is not possible when the dependent variable does not vary within one of the categories of an independent variable. This is referred to as perfect prediction or quasiincomplete separation.** To illustrate this, suppose that we are treating **k5** as categorical rather than continuous in our model of labor force participation. To do this, we regress lfp on indicator variables for the number of children.'* Variable k5_l equals 1 if a person had one young child and equals 0 otherwise, and so on for **k5_2** and **k5_3**. Only three respondents had three young children, and none of these women were in the paid labor force:

```
tab lfp k5
```

       In paid |
         labor |                 # kids < 6
        force? |         0          1          2          3 |     Total
    -----------+--------------------------------------------+----------
     not in LF |       231         72         19          3 |       325 
         in LF |       375         46          7          0 |       428 
    -----------+--------------------------------------------+----------
         Total |       606        118         26          3 |       753 


We find that **lfp** is 0 every time k5_3 is 0. A logit model predicting lfp with the binary variables k5_l, k5_2, and k5_3 (with no children being the excluded category) cannot be estimated because the observed coefficient for k5_3 is effectively infinite. Think of it this way: The observed odds of being in the labor force for those with no children is 375/231 = 1.62, while the observed odds for those with three young children is 0/3 = 0. The odds ratio is 0/1.62 = 0. For the odds ratios to be 0, $\widehat{\beta}_{k5.3}$ must be negative infinity. As the likelihood is maximized, estimates of $\beta_{k5.3}$ get more and more negative until Stata realizes that the parameter cannot be estimated and reports the following:

```
logit lfp k5_1 k5_2 k5_3, or nolog
```

    note: k5_3 != 0 predicts failure perfectly;
          k5_3 omitted and 3 obs not used.
    
    
    Logistic regression                                     Number of obs =    750
                                                            LR chi2(2)    =  31.05
                                                            Prob > chi2   = 0.0000
    Log likelihood = -496.82164                             Pseudo R2     = 0.0303
    
    ------------------------------------------------------------------------------
             lfp | Odds ratio   Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            k5_1 |       0.39       0.08    -4.52   0.000         0.26        0.59
            k5_2 |       0.23       0.10    -3.30   0.001         0.09        0.55
            k5_3 |       1.00  (omitted)
           _cons |       1.62       0.14     5.79   0.000         1.38        1.91
    ------------------------------------------------------------------------------
    Note: _cons estimates baseline odds.

    note: k5_3 != 0 predicts failure perfectly;
          k5_3 omitted and 3 obs not used.

    
The message can be interpreted as follows. If someone in the sample has three young children (that is, if **k5_3** is not equal to 0), then she is never in the labor force (that is, **lfp** equals 0), which is considered a "failure" in the terminology of the model. At this point, Stata removes the three cases where **k5_3** is 0 and also removes **k5_3** from the model. In the output, the coefficient for **k5_3** is shown as 1 followed by (omitted). If you use the **asis** option, Stata keeps **k5_3** and the observations in the model and shows the estimate at convergence.

```
logit lfp k5_1 k5_2 k5_3, or asis nolog
```

    Logistic regression                                     Number of obs =    753
                                                            LR chi2(2)    =  36.10
                                                            Prob > chi2   = 0.0000
    Log likelihood = -496.82164                             Pseudo R2     = 0.0351
    
    ------------------------------------------------------------------------------
             lfp | Odds ratio   Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            k5_1 |       0.39       0.08    -4.52   0.000         0.26        0.59
            k5_2 |       0.23       0.10    -3.30   0.001         0.09        0.55
            k5_3 |       0.00          .        .       .            .           .
           _cons |       1.62       0.14     5.79   0.000         1.38        1.91
    ------------------------------------------------------------------------------
    Note: _cons estimates baseline odds.
    Note: 3 failures and 0 successes completely determined.

The estimated odds ratio of 4.43e-10 (that is, 0.000000000443) is Stata’s attempt to estimate an odds ratio that is close to 0. With perfect prediction, the estimates for the other variables can be used, but you do not learn anything useful about the variable that is dropped.

Just because, in our sample, the three women with children under age 5 were not in the labor force does not imply the probability is 0 in the population. With any nonzero probability in the population, there is some chance that every observation with a given value of an independent variable will have the same outcome. This is especially so when the sample is small.

Exact methods of estimation provide more accurate inference in small samples than standard maximum likelihood estimation. In Stata, **`exlogistic`** provides exact method estimation for the logit model. Mehta and Patel (1995) provide an accessible review of these methods.

**Exact estimation computes p-values by enumerating all possible outcomes, which can provide estimates and significance levels in small samples with perfect prediction.**However, computing all enumerations takes a long time. For example, fitting the logit model above with **exlogistic** took 750 times longer than fitting the model with **logit**. The results are:

```
* exact estimation: very slow so do not run each time
exlogistic lfp k5_1 k5_2 k5_3, memory(2gb)
```

By default, **`exlogistic`** provides the conditional maximum likelihood estimates of parameters. When those estimates are infinite, as is the case with perfect prediction, median unbiased estimates are given. Cytel Software Corporation (2005, 512) suggests that the median unbiased estimates should be interpreted with caution by using the confidence interval.

Although one bound of the confidence interval for the odds ratio will be 0 or positive infinity, the other bound is informative, and we can be 95% confident that the estimate is larger (or smaller) than this bound. This allows us to speak precisely about how much uncertainty we have. For example, in the output above, the confidence interval includes 1, so our data do not even permit us to reject the null hypothesis that women with three children are less likely to be in the labor force than are women with no children.

## 3 Hypothesis testing

Hypothesis tests of regression coefficients can be conducted with the $z$ statistics from the estimation output, using the **`test`** command for Wald tests of simple and complex hypotheses, and with the **`lrtest`** command for the corresponding likelihood-ratio (LR) tests. We discuss using each to test hypotheses involving a single coefficient and then show how **`test`** and **`lrtest`** can be used for hypotheses involving multiple coefficients. See section 3.2 for general information on hypothesis testing using Stata. While often in this book, we show how to conduct both Wald and LR tests of the same hypothesis, in practice, you would want to test a hypothesis with only one type of test.

### 3.1 Testing individual coefficients
Most often, we are interested in testing the hypothesis $H_{0}\colon\beta_{k}=0$, which corresponds to results in column z in the output from **`logit`** and **`probit`**. For example, consider the results for variables k5 and wc from the **`logit`** output generated in section 5.2.1:

```
logit lfp k5 i.wc i.hc k618 i.agecat lwg inc, nolog
```

    Logistic regression                                     Number of obs =    753
                                                            LR chi2(8)    = 124.30
                                                            Prob > chi2   = 0.0000
    Log likelihood = -452.72367                             Pseudo R2     = 0.1207
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              k5 |      -1.39       0.19    -7.25   0.000        -1.77       -1.02
                 |
              wc |
        college  |       0.80       0.23     3.48   0.001         0.35        1.25
                 |
              hc |
        college  |       0.14       0.21     0.66   0.508        -0.27        0.54
            k618 |      -0.07       0.07    -0.96   0.336        -0.20        0.07
                 |
          agecat |
          40-49  |      -0.63       0.21    -3.00   0.003        -1.04       -0.22
            50+  |      -1.28       0.26    -4.92   0.000        -1.79       -0.77
                 |
             lwg |       0.61       0.15     4.04   0.000         0.31        0.91
             inc |      -0.04       0.01    -4.24   0.000        -0.05       -0.02
           _cons |       1.01       0.29     3.54   0.000         0.45        1.57
    ------------------------------------------------------------------------------

We conclude the following:

Having young children has a significant effect on the probability of being in the labor force $z = -7.25, p < 0.01$ for a two-tailed test.

The effect of the wife attending college is significant at the 0.01 level

#### 3.1.1 Testing single coefficients using test

The $z$ test included in the output of estimation commands is a Wald test, which can also be computed as a chi-squared test by using **`test`**. For example, to test $H_{0}\colon\beta_{\mathrm{k5}}=0$

```
test k5
```

     ( 1)  [lfp]k5 = 0
    
               chi2(  1) =   52.57
             Prob > chi2 =    0.0000
Stata refers to the coefficient for k5 as [lfp ]k5 because the dependent variable is lfp . We conclude the following:

The effect of having young children on entering the labor force is significant at the 0.01 level $(\chi^{2}=52.57,\mathrm{~df}=1,p<0.01)$

The value of the $z$ test is identical to the square root of the corresponding chi-squared test with 1 degree of freedom. For example, using **`display`** as a calculator

```
display sqrt(52.57)
```

This corresponds to —7.25 from the logit output shown above.

>Aside: Using returns .
>>Using returned results is a better way to show this. When you use the **`test`** command, the chi-squared statistic is returned as the scalar **`r(chi2)`**. The command **`display sqrt(r(chi2))`** then provides the same result more elegantly and with slightly more accuracy.

#### 3.1.2 Testing single coefficients using Irtest

An LR test is computed by comparing the log likelihood from a full model with that of a restricted model. To test a single coefficient, we begin by fitting the full model and storing the results:

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store Mfull
```

Then, we fit the model without k5 and store the results:

```
logit lfp k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store Mnok5
```

Next, we run Irtest :

```
lrtest Mfull Mnok5
```

    Likelihood-ratio test
    Assumption: Mnok5 nested within Mfull
    
     LR chi2(1) =  62.55
    Prob > chi2 = 0.0000

The LR test shows the following:
The effect of having young children is significant at the 0.01 level $(\operatorname{LR}\chi^2=62.55,\mathrm{~df}=1,p<0.01)$.

**If you want to run an LR test comparing a model stored by using "estimates store" with the last model fit, you can use a single period to represent the last model.** For example, instead of "lrtest Mfull Mnok5," you could use "lrtest Mfull .," where "." represents the last model.

### 3.2 Testing mutiple coefficiencts

You might want to test complex hypotheses that involve more than one coefficient. For example, we have two variables that reflect education in the family: "he" and "wc." The conclusion that education has (or does not have) a significant effect on labor force participation cannot be based on separate tests of single coefficients. **A joint hypothesis can also be tested using either "test" or "`lrtest`." Similarly, to test the effect of "agecat," requires testing the coefficients of all indicator variables.**

#### 3.2.1 Testing multiple coefficients using test

To test that the effect of the wife attending college and of the husband attending college on labor force participation are simultaneously equal to 0 (that is, $H_{0}\colon\beta_{\mathbf{wc}}=\beta_{\mathbf{hc}}=0$), we fit the full model and test the two coefficients. We must use "i.he" and "i.wc," not "hc" and "wc."

```
estimates restore Mfull
test 1.hc 1.wc
```

     ( 1)  [lfp]1.hc = 0
     ( 2)  [lfp]1.wc = 0
    
               chi2(  2) =   17.83
             Prob > chi2 =    0.0001

We reject the hypothesis that the effects of the husband’s and the wife’s education are simultaneously equal to 0 $(\chi^{2}=17.83,\mathrm{df}=2,p<0.01)$.

**`test`** can also be used to test the equality of coefficients. For example, to test that the effect of the wife attending college on labor force participation is equal to the effect of the husband attending college (that is, Ha: βVC = βhc), we type:

```
test 1.hc = 1.wc
```

     ( 1)  - [lfp]1.wc + [lfp]1.hc = 0
    
               chi2(  1) =    3.24
             Prob > chi2 =    0.0719


Here, the test translated $\beta_{\mathbf{uc}}=\beta_{\mathbf{hc}}$ into the equivalent expression $-\beta_{\mathbf{wc}}+\beta_{\mathbf{hc}}=0$. The null hypothesis that the effects of husband’s and wife’s education are equal is marginally significant. We might conclude the following:

There is weak evidence that the effects of husband’s and wife’s education are equal $(\chi^{2}=3.24,\mathrm{df}=1,p=0.072)$

We can test that the effect of agecat is 0 by specifying the two indicator variables that were created from the factor variable **i.agecat**:

```
test 2.agecat 3.agecat
```


     ( 1)  [lfp]2.agecat = 0
     ( 2)  [lfp]3.agecat = 0
    
               chi2(  2) =   24.27
             Prob > chi2 =    0.0000


To avoid having to specify each of the automatically created indicators, we can use **`testparm`**:

```
testparm i.agecat
```
     ( 1)  [lfp]2.agecat = 0
     ( 2)  [lfp]3.agecat = 0
    
               chi2(  2) =   24.27
             Prob > chi2 =    0.0000

**The advantage of "testparm" is that it works no matter how many indicator variables have been created by "i.catvar."**

#### 3.2.2 Testing multiple coefficients using Irtest

To compute an LR test of multiple coefficients, we start by fitting the full model and saving the results with "**`estimates store`** estname." To test the hypothesis that the effect of the wife attending college and of the husband attending college on labor force participation are both equal to 0 (that is, $H_{0}\colon\beta_{\mathbf{vc}}=\beta_{\mathbf{hc}}=0)$, we fit the model that **`excludes`** these two variables and then run **lrtest**:

```
logit lfp k5 k618 i.agecat lwg inc, nolog
estimates store Mnowchc
lrtest Mfull Mnowchc
```

    Likelihood-ratio test
    Assumption: Mnowchc nested within Mfull
    
     LR chi2(2) =  18.68
    Prob > chi2 = 0.0001
    
We conclude the following:
The hypothesis that the effects of the husband’s and the wife’s education are simultaneously equal to 0 can be rejected at the 0.01 level. $(\mathrm{LR}\chi^{2}=18.68,\mathrm{df}=2,p<0.01)$

This logic can be extended to exclude other variables. Say that we wish to test the hypothesis that the effects of all the independent variables are simultaneously 0. We do not need to fit the full model again because the results are still saved from our use of "**`estimates store Mfull`**" above. We fit the model with no independent variables and then run "**`lrtest`**":

```
logit lfp, nolog
estimates store Mconstant
lrtest Mfull Mconstant
```
    Logistic regression                                     Number of obs =    753
                                                            LR chi2(0)    =   0.00
                                                            Prob > chi2   =      .
    Log likelihood = -514.8732                              Pseudo R2     = 0.0000
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.28       0.07     3.74   0.000         0.13        0.42
    ------------------------------------------------------------------------------


    Likelihood-ratio test
    Assumption: Mconstant nested within Mfull
    
     LR chi2(8) = 124.30
    Prob > chi2 = 0.0000
 
We reject the hypothesis that all coefficients except the intercept are 0 $\left(\operatorname{LR}\chi^{2}=124.30,\mathrm{df}=8,p<0.01\right)$

This test is identical to the test in the header of the logit output from the full model: LR chi2 (8) = 124.30.


### 3.3 Comparing LR and Wald tests

Although the LR and Wald tests are asymptotically equivalent, **their values differ in finite samples**. In our example,


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.12.png" style="width:550px;height:250px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Statistical theory is unclear on whether the LR or Wald test is to be preferred in models for categorical outcomes, although many statisticians, ourselves included, prefer the LR test. **The choice of which test to use is often determined by convenience, personal preference, and convention within an area of research. Recall from chapter 3 that if robust standard errors or svy estimation is used, only Wald tests are available. For Wald tests of a single coefficient, some disciplines prefer to use chi-squared tests, while others prefer the corresponding $z$ test.**

## 4 Predicted probabilities, residuals, and influential observations
For a given set of values of the independent variables, the predicted probability can be computed from the estimated coefficients.

$$\mathrm{Logit:~}\widehat{\Pr}\left(y=1\mid\mathbf{x}\right)=\Lambda\left(\mathbf{x}\widehat{\beta}\right)\quad\mathrm{Probit:~}\widehat{\Pr}\left(y=1\mid\mathbf{x}\right)=\Phi\left(\mathbf{x}\widehat{\beta}\right)$$

where $\Lambda$  is the CDF for the logistic distribution with variance $π^2/3$, and $\Phi$ is the CDF for the normal distribution with variance 1. **For any set of values of the independent variables, whether occurring in the sample or not, the predicted probability can be computed.** In this section, we consider predictions for each observation in the dataset along with residuals and measures of influence based on these predictions. In sections 6.2-6.6, we use predicted probabilities for interpretation.

### 4.1 Predicted probabilities using predict

After running logit or probit,

predict newvar [if] [in]

computes the predicted probability of a positive outcome for each observation, given the observed values on the independent variables, and saves them in the new variable newvar.

Predictions are computed for all cases in memory that **do not have missing values for any variables in the model, regardless of whether "if" and "in" were used to restrict the estimation sample.** For example, if you fit "**logit lfp k5 i.age catif wc==l**," only 212 cases are used when fitting the model. But "predict newvar" computes predictions for all 753 cases in the dataset. **If you want predictions only for the estimation sample, you can use the command "predict newvar if e(sample)==1," where "e(sample)" is the variable created by "logit" or "probit" to indicate whether a case was used when fitting the model.**

We can use predict to examine the range of predicted probabilities from our model. For example, we start by computing the predictions:

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store logit
predict prlogit
```

     predict prlogit
    (option pr assumed; Pr(lfp))

Because we did not specify which quantity to predict, the default option "**pr**" for the probability of a positive outcome was assumed, and the new variable "**prlogit**" was given the default variable label "**Pr(lfp)**." **In general, and especially when fitting multiple models, we suggest adding your own variable label to the prediction to avoid having multiple variables with the same label. Here we add a variable label and compute summary statistics**:    

```
label var prlogit "Logit: Pr(lfp | X)"
codebook prlogit, compact
```

    Variable   Obs Unique      Mean       Min       Max  Label
    ----------------------------------------------------------------------------
    prlogit    753    753  .5683931  .0135618  .9512301  Logit: Pr(lfp | X)
    ----------------------------------------------------------------------------

The predicted probabilities range from 0.014 to 0.951 with a mean of 0.568. We use "dotplot" to examine the distribution of predictions.

```
dotplot prlogit, ylabel(0(.2)1, grid gmin gmax)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.13.png" style="width:550px;height:400px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

>In this example of "dotplot," **the option "ylabel(0(.2)1, grid gmin gmax)" sets the range of the axis from 0 to 1 with grid lines in increments of 0.2, where the "gmin" and "gmax" suboptions add lines at the minimum and maximum values.** Even if the actual range of the predictions is smaller than 0 to 1, we find it useful to see the distribution relative to the entire potential range of probabilities.

**Examining the distribution of predictions is a valuable first step after fitting your model to get a general sense of your data and possibly detect problems.** Our plot shows that there are individuals with predicted probabilities that span almost the entire range from 0 to 1, with roughly two-thirds of the observations between 0.40 and 0.80. The large range reflects that our sample contains individuals with both very large and very small probabilities of labor force participation. Examining the characteristics of these individuals could be useful for guiding later analysis. **If the distribution was bimodal, it would suggest the importance of a binary independent variable or the possibility of two types of individuals, perhaps with shared characteristics on many variables.**

#### 4.1.1 Comparing logit to probit predictions

**predict can also be used to show that the predictions from logit and probit models are nearly identical.** Although the two models make different assumptions about the distribution of $\varepsilon,$, these differences are absorbed in the relative magnitudes of the estimated coefficients. To see this, we begin by fitting comparable logit and probit models and computing their predicted probabilities. First, we fit the logit model, store the estimates, and compute predictions:

```
use binlfp4, clear

logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store logit
predict prlogit
```

Next, we fit the probit model:

```
probit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store probit
predict prprobit
label var prprobit "Probit: Pr(lfp | X)"
```

ven though we showed earlier that logit coefficients are about 1.7 times larger than probit coefficients, the predicted probabilities are almost perfectly correlated:

```
pwcorr prlogit prprobit
```
                 |  prlogit prprobit
    -------------+------------------
         prlogit |   1.0000 
        prprobit |   0.9998   1.0000 

Their nearly identical magnitudes can be shown by plotting the predictions from the two models against one another:

```
scatter prlogit prprobit, xlabel(0(.25)1, grid) ylabel(0(.25)1, grid) ///
                          msymbol(Oh) aspect(1)
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.14.png" style="width:750px;height:400px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

### 4.2 Residuals and influential observations using predict

After you have fit your baseline model, we suggest that you examine residuals and look for influential observations before beginning postestimation analyses for interpretation. **Residuals and influential observations can help you discover problems with your data and sometimes suggest problems in your model specification.** Residuals are the difference between a model’s predicted and observed outcomes for each observation in the sample. Cases that have large residuals are known as outliers. **When an observation has a large effect on the estimated parameters, it is said to be influential. We illustrate these ideas with the linear regression model in figure below.**


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.15.png" style="width:450px;height:700px;" alt="图片描述">
  <figcaption><strong>The distinction between an outlier and an influential observation</strong></figcaption>
</figure>

Not all outliers are influential, as the figure shows by using simulated data for the linear regression of y on x. The residual for an observation is its vertical distance from the regression line. In the top panel, the observation highlighted with a solid circle has a large residual and is considered an outlier. Even so, it is not very influential on the slope of the regression line. That is, the slope of the regression line is very close to what it would be if the highlighted observation was dropped from the sample and the model was fit again. In the bottom panel, the only observation whose value has changed is the highlighted observation marked with a square. The residual for this observation is small, but the observation is very influential; its presence is entirely responsible for the slope of the new regression line being positive instead of negative.

Building on the analysis of residuals and influence in the linear regression model (see Fox [2008] and Weisberg [2005, chap. 5]), Pregibon (1981) extended these ideas to the BRM.

#### 4.2.1 Residuals

The predicted probability for a given set of independent variables is

$$\pi_{i}=\Pr\left(y_{i}=1\mid\mathbf{x}_{i}\right)$$

The deviations $y_i-\pi_i$ are heteroskedastic because the variance depends on the probability $\pi_i$ of a positive outcome:

$$\mathrm{Var}\left(y_{i}-\pi_{i}\mid\mathbf{x}_{i}\right)=\pi_{i}\left(1-\pi_{i}\right)$$

The variance is greatest when $π_i = 0.5$ and decreases as $π_i$ approaches 0 or 1. That is, a fair coin is the most unpredictable, with a variance of $0.5 (1 - 0.5) = 0.25$. A coin that has a very small probability of landing head up (or tail up) has a small variance, for example, $0.01(1 — 0.01) = 0.0099$. The Pearson residual divides the residual $y-\widehat{\pi}$ by its standard deviation:

$$r_i=\frac{y_i-\widehat{\pi}_i}{\sqrt{\widehat{\pi}_i\left(1-\widehat{\pi}_i\right)}}$$

Large values of $r$ suggest a failure of the model to fit a given observation

Pregibon (1981) showed that the variance of $r$ is not 1 because $\operatorname{Var}(y_{i}-\hat{\pi}_{i})$ is not exactly equal to the estimate $\widehat{\pi}_i\left(1-\widehat{\pi}_i\right)$ He proposed the standardized Pearson residual

$$r_{i}^{\mathrm{std}}=\frac{r_{i}}{\sqrt{1-h_{\mathrm{ii}}}}$$

where

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.16.png" style="width:350px;height:70px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Although $r^{\mathrm{std}}$ is preferred over $r$ because of its constant variance, we find that the two residuals are often similar in practice. However, because $r$ is simple to compute in Stata, you should use this measure.

#### 4.2.2 Example
**An index plot is an easy way to examine residuals by plotting them against the observation number.** Standardized residuals are computed by specifying the **rstandard** option with **predict**. For example,

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
predict rstd, rstandard
label var rstd "Standardized Residual"
sort inc, stable
generate index = _n
label var index "Observation Number"
```

After computing the standardized residuals that are saved in the new variable **`rstd`**, we sorted the cases by **`inc`** so that observations are ordered from lowest to highest income in the plot that follows. The next line creates the variable **`index`** equal to the observation’s row number in the dataset, where **`_n`** on the right side of **`generate`** inserts the observation number.

All that remains is to plot the residuals against the index with the following command:

```
graph twoway scatter rstd index, msymbol(Oh) mcolor(black) ///
    xlabel(0(200)800) ylabel(-4(2)4, grid gmin gmax) ///
    yline(0, lcolor(black))
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.25.17.png" style="width:750px;height:470px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

There is no absolute standard that defines a “large” residual. In their discussion of residuals and outliers in the BRM, Hosmcr, Lenieshow, and Sturdivant (2013, 193) sagely caution that **“in practice, an assessment of ‘large’ is, of necessity, a judgment call based on experience and the particular set of data being analyzed”**.

**One way to search for problematic residuals is to sort the residuals by the value of a variable that you think may be a problem for the model.**

**Here we sorted on inc before plotting. If this variable had been responsible for the lack of fit of some observations, the plot might show a disproportionate number of cases with large residuals among either the low-income or the high-income observations.** However, this does not appear to be the case for these data.

Still, several residuals stand out as being large relative to the others. In such cases, it is important to identify those specific observations for further inspection. We can do this by labeling the points with their index number from the variable **`index`**. We make the marker invisible with **`msymbol(none)`**, use **`mlabel(index)`** to specify that we want to label each point with the value contained in variable index, and use **`mlabposition(0)`** to place the label where the marker would have appeared.

```
graph twoway scatter rstd index, ///
    msymbol(none) mlabel(index) mlabposition(0) xlabel(0(200)800) ///
    ylabel(-4(2)4, grid gmin gmax) yline(0, lcolor(black))
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.30.1.png" style="width:750px;height:470px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Although the labels are unreadable when there are many points close together, the plot very effectively identifies the isolated cases that we are interested in. We can then list these specific cases, such as observation 142:

```
list lfp k5 k618 2.agecat 3.agecat wc hc lwg inc in 142, clean
```
    
                                    2.       3.                              
             lfp   k5   k618   agecat   agecat   wc   hc         lwg    inc  
    142.   in LF    1      2        0        0   no   no   -2.054124   11.2  


**We can sort the residuals and then list the cases that have residuals greater than 1.7 or smaller than -1.7, along with the values of the independent variables that were significant in the regression (recall that I means "or"):**

```
sort rstd
list rstd index k5 agecat wc lwg inc if rstd>1.7 | rstd<-1.7, clean
```

                 rstd   index   k5   agecat        wc         lwg      inc  
      1.   -2.7519698     555    0    30-39   college    1.493441       24  
      2.   -2.6057072     297    0    30-39   college    1.065162     15.9  
      3.   -2.4837925     511    0    30-39   college    1.150608       22  
      4.   -2.2581826     637    0    30-39   college    1.432793    28.63  
      5.   -2.1681963     622    0    30-39   college    1.156383       28  
      6.   -2.1292388     396    0    30-39   college    .9602645   18.214  
      7.   -2.1029786     507    0    30-39   college    1.013849    21.85  
      8.   -2.0320789     701    0    30-39   college    1.472049    37.25  
      9.   -2.0254221     522    0    40-49   college    1.526319     22.3  
     10.   -1.9960329      23    0    30-39        no    .9234089        6  
     11.   -1.9886893     475    0    40-49   college    1.491301   20.883  
     12.   -1.9490669     457    0    40-49   college    1.279755    20.22  
     13.   -1.8634706      19    0    30-39        no    .8895015     5.33  
     14.   -1.8574452     408    0    40-49   college    1.331652    18.45  
     15.   -1.8341958     384    0    30-39        no    1.117239       18  
     16.   -1.8336811     426    0    40-49   college    1.219497       19  
     17.   -1.8247899     257    0    30-39        no    .9097289   14.694  
     18.   -1.8184564     630    0    30-39   college    .8978252     28.2  
     19.    -1.784916     640    0    40-49   college    1.490523     28.9  
     20.   -1.7847618     558    0    40-49   college    1.214075   24.106  
     21.   -1.7807683      48    0      50+   college    1.436805      7.7  
     22.    -1.740685      37    0    30-39        no    .4213425     7.04  
     23.   -1.7295835     185    0      50+   college    1.646665       13  
     24.   -1.7220067      11    0    30-39        no    .6791221     4.35  
     25.   -1.7155769     136    0    30-39        no    .9448364       11  
    737.    1.7023424     716    0      50+   college    -.433636     41.1  
    738.    1.7158426     568    0      50+        no    .0896122     24.5  
    739.    1.7378807     613    0      50+        no    .2025245     27.2  
    740.    1.7656236     108    2    30-39        no    2.107018     10.2  
    741.    1.7812345     551    1    40-49        no    1.241269   23.709  
    742.    1.8124003     686    1    30-39        no    .9681486    33.97  
    743.    1.8135768     638    0    40-49        no   -.6931472     28.7  
    744.    1.8342929     480    1    40-49        no    .8783384   20.989  
    745.    1.8798914     309    1    30-39   college   -1.543298    16.12  
    746.    1.9882888     653    1    30-39        no     .114816   30.235  
    747.    2.0149417     722    1    30-39        no    .9162908       43  
    748.    2.0847391     214    2    30-39   college           0   13.665  
    749.    2.1387269     401    1      50+        no    1.290984   18.275  
    750.    2.1863983     721    0      50+        no    .6931472    42.91  
    751.    2.8146801     345    2    30-39        no    .5108258    17.09  
    752.    2.8212584     752    1    30-39   college    1.299283       91  
    753.    2.9689827     142    1    30-39        no   -2.054124     11.2  

We can sort the residuals and then list the cases that have residuals greater than 1.7 or smaller than -1.7, along with the values of the independent variables that were significant in the regression (recall that I means "or"):

All the cases with the most negative residuals have k5 equal to 0, and cases with positive residuals often have young children. We can also look at this information by modifying the index plot to show only large residuals and to use the option mlabel(k5) to label each point with the value of k5:

```
graph twoway scatter rstd index if (rstd>1.7) | (rstd<-1.7), ///
    msymbol(none) mlabel(k5) mlabposition(0) xlabel(0(200)800) ///
    ylabel(-4(2)4, grid gmin gmax) yline(0, lcolor(black)) ///
    caption("Values indicate # of young children")
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.30.2.png" style="width:750px;height:470px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Further analyses of the highlighted cases might reveal either incorrectly coded data or some inadequacy in the specification of the model. If data problems were found, we would correct them. **However, cases with large positive or negative residuals should not simply be discarded, because with a correctly specified model you would expect some observations to have large residuals. Instead, you should examine cases with large residuals to see if they suggest a problem with the model (for example, problems associated with the specification of one of the regressors) or errors in the data (for example, a value of 9999 that should have been coded as missing).** In our cases above, we found no problems, and coding k5 as a set of indicator variables did not improve the fit.

#### 4.2.3 Influential cases

As shown in figure 5.3, large residuals do not necessarily have a strong influence on the estimated parameters, and cases with small residuals can have a strong influence. Influential observations, sometimes called high-leverage points, are determined by examining the changes in the estimated $\beta's$ that occur when the $i$th observation is deleted. Although fitting a new logit after eliminating each case is often impractical, Pregibon (1981) derived an approximation that requires fitting the model only once. His deltabeta influence statistic summarizes the effect of removing the ith observation on the entire vector $\widehat{\beta}_{i}$, which is the counterpart to Cook’s distance for the linear regression model. The measure is

$$\Delta\widehat{\beta_{i}}=\frac{r_{i}^{2}h_{ii}}{\left(1-h_{ii}\right)^{2}}$$

where hu was defined in (5.3). Stata refers to $\Delta\widehat{\beta}_i$, as **dbeta**, which we compute as follows:

当我们谈论“影响观测”时，我们实际上是在研究在统计模型中，如果我们去掉某个观测（比如某个学生的数据），模型参数会发生多大的变化。这种观测通常被称为“高杠杆点”。

作者在文章中提到，我们可以通过检查在删除第i个观测时估计的参数$\beta$的变化来确定这些“高杠杆点”。然而，由于在每次删除观测后都重新拟合模型可能不切实际，Pregibon（1981）提出了一个近似方法，只需要拟合模型一次。

这里提到的$\Delta\widehat{\beta_{i}}$是一种度量，表示删除第i个观测对整个模型参数向量$\widehat{\beta}_{i}$的影响。在线性回归模型中，这类似于Cook's距离的概念。这个度量的计算公式是：

$$\Delta\widehat{\beta_{i}} = \frac{r_{i}^{2}h_{ii}}{(1 - h_{ii})^{2}}$$

现在，让我更详细地解释一下公式中的符号：

- $r_{i}$ 是模型对第i个观测的预测误差，它衡量了我们的模型对数据的拟合程度。
  
- $h_{ii}$ 是一个指标，用来表示第i个观测对整体模型的影响程度，有时被称为“杠杆值”。

**在这个公式中，分子部分$r_{i}^{2}h_{ii}$是预测误差平方与杠杆值的乘积，用来衡量第i个观测对模型参数的影响。分母部分$(1 - h_{ii})^{2}$是杠杆值的平方，用来调整影响的大小。**

**这个度量的核心思想是，如果一个观测的预测误差很大（$r_{i}^{2}$大），同时这个观测对整体模型影响也很大（$h_{ii}$大），那么$\Delta\widehat{\beta_{i}}$就会相对较大，表示这个观测在模型中有较大的影响。**

在Stata中，这个度量被称为**`dbeta`**，用于衡量删除某个观测后的模型影响。

```
predict deltabeta, dbeta
label var deltabeta "Pregibon's influence statistic"
graph twoway scatter deltabeta index, ///
    msymbol(none) mlabel(index) mlabposition(0) ///
    xlabel(0(200)800) xtitle("Observation Number") ///
    ylabel(0(.1).3, grid gmin gmax)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.1.30.3.png" style="width:750px;height:470px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Methods for plotting residuals and outliers can be extended in many ways, including plots of different diagnostics against one another. Details of these plots can be found in Cook and YYcisberg (1999), Hosmer, Lemeshow, and Sturdivant (2013), and Landwehr, Pregibon, and Shoemaker (1984).

### 4.3 Least likely observations

The command leastlikely (Freese 2002) will list the least likely observations. For example, for a binary model, leastlikely will list both the observations with the smallest $\widehat{\Pr}\left(y=0\mid\mathbf{x}\right)$ when y$ = 0$ and the observations with smallest $\widehat{\Pr}\left(y=1\mid\mathbf{x}\right)$ when $y = 1$.

>In addition to being used after **`logit`** and **`probit`**, **`leastlikely`** can be used after most binary models in which the option **`pr`** for predict generates the predicted probabilities of a positive outcome (for example, **`cloglog`**, **`scobit`**, and **`hetprobit`**) and after many models for ordinal or nominal outcomes in which the option **`outcome(#)`** for **`predict`** generates the predicted probability of outcome# (for example, **`ologit`**, **`oprobit`**, **`mlogit`**, **`mprobit`**, and **`slogit`**). **`leastlikely`** is not appropriate for models in which the probabilities produced by **`predict`** are probabilities within groups or panels (for example, such as clogit, nlogit, and asmprobit).

**Syntax**

The syntax for **`leastlikely`** is as follows:

**leastlikely** [varlist] [if] [in] [, **n**(#) **generate**(varname) [**nodisplay** | **display**] **nolabel** **noobs**]

where varlist contains any variables whose values are to be listed in addition to the observation numbers and probabilities.

**Options**

**`n(#)`** specifies the number of observations to be listed for each level of the outcome variable. The default is **`n(5)`**. For multiple observations with identical predicted probabilities, all observations will be listed.

**`generate`**(varname) specifies that the probabilities of observing the outcome that was observed be stored in varname.

**Options controlling the list of values**

**`[no]display`** forces the format into display or tabular (**`nodisplay`**) format. If you do not specify one of these options, Stata chooses the one it decides will be most readable.

**`nolabel`** causes numeric values rather than labels to be displayed

**`noobs`** suppresses printing of the observation numbers

**Example**
We can use **`leastlikely`** to identify the least likely observations from our model of labor force participation and to list the values of the variables **k5**, **k618**, and wc for these observations. Based on our model **logit lfp k5 k618 i.age cat i.wc i.hc lwg inc**,

```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
leastlikely k5 k618 wc agecat
```

    Outcome: 0 (not in LF)
    
         +------------------------------------------+
         |      Prob   k5   k618        wc   agecat |
         |------------------------------------------|
     60. | .14033509    0      1   college    30-39 |
    172. |  .1291429    0      2   college    30-39 |
    221. | .17652958    0      2   college    30-39 |
    252. | .11741232    0      0   college    30-39 |
    262. | .16508454    0      3   college    30-39 |
         +------------------------------------------+
    
    Outcome: 1 (in LF)
    
         +------------------------------------------+
         |      Prob   k5   k618        wc   agecat |
         |------------------------------------------|
    427. | .17668873    0      5        no      50+ |
    496. | .18095707    1      0        no      50+ |
    534. | .10392637    1      2        no    30-39 |
    635. | .11522446    1      3   college    30-39 |
    662. | .11338183    2      0        no    30-39 |
         +------------------------------------------+

Among women not in the labor force, we find that the lowest predicted probability of not being in the labor force occurs for those who have young children, attended college, and are younger. For women in the labor force with the lowest probabilities of being in the labor force, all but one individual have young children, most have more than one older child, and one attends college. This suggests further consideration of how labor force participation is affected by having children in the family.

## 5 Measures of fit

As discussed in chapter 3, a scalar measure of fit can be useful when comparing competing models. **Information criteria such as the Bayesian information criterion (BIC) and Akaike’s information criterion (AIC) can be used to select among models and are often very useful.** There are many pseudo-R^2 statistics that are inspired by the coefficient of determination R^2 in the linear regression model, but we find them less informative, even though they are often used. Finally, the Hosmer Lemeshow statistic is a popular way to assess the overall fit of the model, but we do not recommend it for reasons we explain below.

### 5.1 Information criteria

To illustrate scalar measures of fit and information criteria, we consider two models. Model 1 (*M1*) contains our original specification of independent variables k5, k618, agecat, wc, he, lwg, and inc. Model 2 (*M2*) drops variables k618, he, and lwg, and adds income-squared with c. in c##c. The models are fit, and estimates are stored.

```
quietly logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store model1
quietly logit lfp k5 i.agecat i.wc c.inc##c.inc, nolog
estimates store model2
```

We can list the estimates by using the option stats(N BIC AIC R2_p) to include the sample size, BIC, AIC, and pseudo-R^2 that are normally included in models fit with maximum likelihood. Recall that the formulas for these statistics are given in section 3.3.2.

```
estimates table model1 model2, b(%9.3f) p(%9.3f) stats(N bic aic r2_p)
```

    --------------------------------------
        Variable |  model1      model2    
    -------------+------------------------
              k5 |    -1.392      -1.369  
                 |     0.000       0.000  
            k618 |    -0.066              
                 |     0.336              
                 |
          agecat |
          40-49  |    -0.627      -0.512  
                 |     0.003       0.011  
            50+  |    -1.279      -1.137  
                 |     0.000       0.000  
                 |
              wc |
        college  |     0.798       1.119  
                 |     0.001       0.000  
                 |
              hc |
              1  |     0.136              
                 |     0.508              
                 |
             lwg |     0.610              
                 |     0.000              
             inc |    -0.035      -0.060  
                 |     0.000       0.001  
                 |
     c.inc#c.inc |                 0.000  
                 |                 0.083  
                 |
           _cons |     1.014       1.743  
                 |     0.000       0.000  
    -------------+------------------------
               N |       753         753  
             bic |   965.064     968.574  
             aic |   923.447     936.206  
            r2_p |     0.121       0.104  
    --------------------------------------
                               Legend: b/p

Model 2 (M2) modifies Model 1 (M1) by removing one statistically significant variable and two non-significant variables from M1, while adding a variable that is significant at the 0.10 level. **Since the models are not nested, they cannot be compared with a likelihood ratio (LR) test, but we can use the BIC and AIC statistics.** In this example, both the BIC and AIC statistics are smaller for M1, providing support for that model. **Following Raftery’s (1995) guidelines**, we can say that there is positive (neither weak nor strong) support for M1.

You can obtain information criteria in two other ways. You can use the **`ic`** option to **`fitstat`**, which shows multiple versions of the AIC and BIC measures (see section 3.3 for the formula for these measures):

```
estimates restore model1
quietly fitstat, save ic
estimates restore model2
fitstat, diff ic
```
    
                             |     Current        Saved   Difference 
    -------------------------+--------------------------------------
    AIC                      |                                      
                         AIC |     936.206      923.447       12.759 
              (divided by N) |       1.243        1.226        0.017 
    -------------------------+--------------------------------------
    BIC                      |                                      
              BIC(df=7/9/-2) |     968.574      965.064        3.511 
     BIC (based on deviance) |   -4019.347    -4022.857        3.511 
        BIC' (based on LRX2) |     -67.796      -71.307        3.511 
    
    Difference of    3.511 in BIC provides positive support for saved model.

The results match those from the estimates table output and even tell you the strength of support for the preferred model. You can also use the **`estat ic`** command:

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estat ic
```

    Akaike's information criterion and Bayesian information criterion
    
    -----------------------------------------------------------------------------
           Model |          N   ll(null)  ll(model)      df        AIC        BIC
    -------------+---------------------------------------------------------------
               . |        753  -514.8732  -452.7237       9   923.4473   965.0639
    -----------------------------------------------------------------------------
    Note: BIC uses N = number of observations. See [R] IC note.


```
logit lfp k5 i.agecat i.wc c.inc##c.inc, nolog
estat ic
```

    Akaike's information criterion and Bayesian information criterion
    
    -----------------------------------------------------------------------------
           Model |          N   ll(null)  ll(model)      df        AIC        BIC
    -------------+---------------------------------------------------------------
               . |        753  -514.8732   -461.103       7    936.206   968.5745
    -----------------------------------------------------------------------------
    Note: BIC uses N = number of observations. See [R] IC note.

These results match those from fitstat

### 5.2 $Pseudo-R^2’s$

Within a substantive area, $pseudo-R^2s$ might provide a rough index of whether a model is adequate. For example, if prior models of labor force participation routinely have values of 0.4 for a particular $pseudo-R^2$, you would expect that new analyses with a different sample or with revised measures of the variables would result in a similar value for that measure. But there is no convincing evidence that selecting a model that maximizes the value of a $pseudo-R^2$ results in a model that is optimal in any sense other than the model has a larger value of that measure.

We use the same models estimated in the last section and use **`fitstat`** to compute the scalar measures of fit (see section 3.3 for the formula for these measures):

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
quietly fitstat, save
logit lfp k5 i.agecat i.wc c.inc##c.inc, nolog
fitstat, diff
```
    
                             |     Current        Saved   Difference 
    -------------------------+--------------------------------------
    Log-likelihood           |                                      
                       Model |    -461.103     -452.724       -8.379 
              Intercept-only |    -514.873     -514.873        0.000 
    -------------------------+--------------------------------------
    Chi-square               |                                      
             D(df=746/744/2) |     922.206      905.447       16.759 
               LR(df=6/8/-2) |     107.540      124.299      -16.759 
                     p-value |       0.000        0.000        0.000 
    -------------------------+--------------------------------------
    R2                       |                                      
                    McFadden |       0.104        0.121       -0.016 
          McFadden(adjusted) |       0.091        0.103       -0.012 
          McKelvey & Zavoina |       0.183        0.215       -0.032 
                Cox-Snell/ML |       0.133        0.152       -0.019 
      Cragg-Uhler/Nagelkerke |       0.179        0.204       -0.026 
                       Efron |       0.135        0.153       -0.018 
                    Tjur's D |       0.135        0.153       -0.018 
                       Count |       0.672        0.676       -0.004 
             Count(adjusted) |       0.240        0.249       -0.009 
    -------------------------+--------------------------------------
    IC                       |                                      
                         AIC |     936.206      923.447       12.759 
            AIC divided by N |       1.243        1.226        0.017 
              BIC(df=7/9/-2) |     968.574      965.064        3.511 
    -------------------------+--------------------------------------
    Variance of              |                                      
                           e |       3.290        3.290        0.000 
                      y-star |       4.026        4.192       -0.165 
    
    Note: Likelihood-ratio test assumes current model nested in saved model.
    
    Difference of    3.511 in BIC provides positive support for saved model.


After fitting our first model with **`logit`**, we used **`quietly`** to suppress the output from fitstat with the save option to retain the results in memory. After fitting the second model, **`fitstat`**, **`diff`** displays the fit statistics for both models, **`fitstat`**, **`diff`** computes differences between all measures, shown in the column labeled Difference, even if the models are not nested. As with the **`lrtest`** command, you must determine if it makes sense to interpret the computed difference.

What do the fit statistics show? The values of the $pseudo-R^2s$ are slightly larger for M1, which is labeled "Saved logit" in the table. If you take the $pseudo-R^2s$ as evidence for the best model, which we do not, there is some evidence preferring M1.

### 5.3 (Advanced) Hosmer-Lemeshow statistic

>We only recommend reading this section if you are considering using the Hosmer-Lemeshow statistic. After reviewing how the measure is computed, we illustrate that **the statistic is highly dependent upon an arbitrary decision on the number of groups used.** As a result, we do not recommend this measure.

The idea of the Hosmer-Lemeshow (HL) test is to compare predicted probabilities with the observed data (Hosmer and Lemeshow 1980; Lemeshow and Hosmer 1982). This popular test can be computed using the **`estat gof`** command after fitting a logit or probit model. Unlike the measures presented above, this command also works with models fit by using complex survey data with the **`svy`** prefix.

To explain how the test works, we review the steps that are used to compute it.
1. Fit the regression model
2. Compute the predicted probabilities $\widehat{\pi}_i$
3. Sort the data from the smallest $\widehat{\pi}_i$ to the largest.
4. Divide the observations into G groups, where 10 groups are often used. Each group will have $n_{g}\approx\frac{N}{G}$ The first group will have the $n_1$ smallest values of $\widehat{\pi}_i$, and so on. If $G$ does not divide equally into $N$, the group sizes will differ slightly.
5. WTithin each group, compute the mean prediction:

$$\overline{\pi_g}=\sum_\text{i in group g}{ \widehat { \pi }_i/n_g}$$

Also compute th e mean number of observed cases where $y = 1$:

$$\overline{y_g}=\sum_{i\text{ in group g}} y _ i / n _ g$$

6. The test statistic is

$$HL=\sum_{g=1}^{G}\frac{\left(n_g\overline{y}_g-n_g\overline{\pi}_g\right)^2}{n_g\overline{\pi}_g\left(1-\overline{\pi}_g\right)}$$


Hosmer, Lemeshow, and Sturdivant (2013) ran simulations that suggest that HL is distributed approximately as $χ^2$ with $G – 2$ degrees of freedom if the model is correctly specified. If the p-value is large, the model is considered to fit the data.

To give an example, we fit the model we have used as a running example, and we use **`estat gof`** to compute the HL statistic:

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg c.inc, nolog
estat gof, group(10)
```

    note: obs collapsed on 10 quantiles of estimated probabilities.
    
    Goodness-of-fit test after logistic model
    Variable: lfp
    
     Number of observations =    753
           Number of groups =     10
    Hosmer–Lemeshow chi2(8) =  13.76
                Prob > chi2 = 0.0881

The p-value is greater than 0.05, indicating that the model fits based on the criterion provided for the HL.

Unfortunately, we do not find conclusions from the HL test to be convincing. First, as Hosmer and Lemeshow point out, the HL test is not a substitute for examining individual predictions and residuals as discussed in the last section. Second, Allison (2012b. 67) raised concerns that the test is not very powerful. In a simple simulation with 500 cases, the HL test failed to reject an incorrect model 75% of the time. Third, and most critically, the choice of the number of groups is arbitrary, even though 10 is most often used. The results of the Hosmer-Lemeshow test are sensitive to the arbitrary choice of the number of groups. In our experience, this is often the case, and for this reason, we do not recommend the test.

We can illustrate the sensitivity of the Hosmer-Lemeshow test by varying the number of groups used to compute the test from 5 to 15 in the model fit above:

```
quietly logit lfp k5 k618 i.agecat i.wc i.hc lwg c.inc, nolog
capture matrix drop hl
forvalues numgroups = 5(1)15 {
    quietly estat gof, group(`numgroups')
    local rm = r(m)
    matrix r =  r(chi2), r(df), chi2tail(r(df),r(chi2))
    matrix rownames r = "`rm' groups"
    matrix hl = nullmat(hl) \ r
}
matrix colnames hl = chi2 df prob
matlist hl, format(%8.3f)
```
    
                 |     chi2        df      prob 
    -------------+-----------------------------
        5 groups |    4.043     3.000     0.257 
        6 groups |    8.762     4.000     0.067 
        7 groups |   10.424     5.000     0.064 
        8 groups |   13.831     6.000     0.032 
        9 groups |   15.503     7.000     0.030 
       10 groups |   13.763     8.000     0.088 
       11 groups |   17.980     9.000     0.035 
       12 groups |   24.055    10.000     0.007 
       13 groups |   15.230    11.000     0.172 
       14 groups |   19.360    12.000     0.080 
       15 groups |   24.722    13.000     0.025 

The row labeled 10 groups corresponds to the result shown earlier. It is disconcerting that when 10 groups are used, the result is not significant, but if 9 or 11 groups had been used, the result would have been significant. Over the 11 groups listed, the p-values range from p = 0.007 to p = 0.257. Although the idea of the HL test is appealing, we are skeptical that it is an effective way to assess a model.

## 6 Other commands for binary outcomes

Logit and probit models are the most commonly used models for binary outcomes and are the only ones that we consider in this book, but other models exist that can be fit in Stata. Among them are the following:

* **`cloglog`** assumes a complementary log-log distribution for the errors instead of a logistic or normal distribution.
* **`scobit`** fits a logit model that relaxes the assumption that the marginal change in the probability is greatest when Pr($y$ = 1) = 0.5.
* **`hetprobit`** allows the assumed variance of the errors in the probit model to vary as a function of the independent variables, which is one approach to comparing logit and probit coefficients across groups (Williams 2009).
* ivprobit fits a probit model where one or more of the regressors are endogenously determined.
* **`biprobit`** simultaneously fits two binary probits and can be used when errors are correlated with each other, as in the estimation of seemingly unrelated regression models for continuous dependent variables. **`mvprobit`** (Cappellari and Jenkins 2003) extends this idea to more than two binary probits.


Binary outcomes that reflect an event that is expected to happen eventually for all cases are often handled using survival analysis, which is not covered in this book. Cleves et al. (2010) provides a detailed introduction to survival analysis in Stata focusing on the **`st*`** commands. Likewise, we do not consider Stata’s extensive commands for working with panel and multilevel data, including Stata’s **`xt*`** and **`me*`** commands, but these are discussed extensively in Rabe-Hesketh and Skrondal (2012) and Cameron and Trivedi (2010).
