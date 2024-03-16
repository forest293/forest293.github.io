# [Categorical Dependent Var] Chapter_6 ：Models for binary outcomes——Interpretation


**The challenge of interpreting results, then, is to find a summary of how changes in the independent variables are associated with changes in the outcome that best reflects critical substantive processes without overwhelming yourself or your readers with distracting detail.**

**Using odds ratios to interpret the logit model is very common but rarely is it sufficient for understanding the results of the model. Nonetheless, it is important to understand what odds ratios mean for several reasons.**For one, odds ratios are used a lot, and you need to understand what they can and cannot tell you. Also, odds ratios are useful for understanding the structure of the ordinal regression model in chapter 7 and the multinomial logit model in chapter 8. **Interpretation based only on \(y^*\) parallels interpretation in the linear regression model, but it is not often used for binary outcomes. It is, however, sometimes useful for models for ordinal outcomes, considered in chapter 7.**

1. We begin in section 6.2 with marginal effects, which we find more informative than the more commonly used odds ratios as scalar measures to assess the magnitude of a variable’s effect. 
2. In section 6.3, we consider computing predictions based on substantively motivated profiles of values for the independent variables, also referred to as ideal types. Thinking about the types of individuals represented in your sample is a valuable way to gain an intuitive sense of which configurations of variables are substantively important. 
3. Tables of predictions, which are discussed in section 6.4, can effectively highlight the impact of categorical independent variables. 
4. We end our discussion of interpretation in section 6.6 by considering graphical methods to show how probabilities change as a continuous independent variable changes.

When using logit or probit, or any nonlinear model, we suggest that you try a variety of methods of interpretation with the goal of finding an elegant way to present the results that does justice to the complexities of the nonlinear model and the substantive application. **No one method works in all situations, and often the only way to determine which method is most effective is to try them all.** Fortunately, the methods we consider in this chapter can be readily extended to models for ordinal, nominal, and count outcomes, which are considered in chapters 7-9.

## 1 Interpretation using regression coefficients
Interpretation of regression models involves examining how a change in an independent variable is associated with a change in the outcome.

In the nonlinear binary regression model (BRM), a regression coefficient indicates the direction of a variable’s effect. In our model of labor force participation, the coefficients for \(k5\) and \(k618\) are both negative, which implies that higher numbers of children are associated with a lower probability of being in the labor force. What is harder to interpret from the coefficient is the magnitude of the effect. The logit model, for example, can be written as

$$\ln\Omega\left(\mathbf{x}\right)=\mathbf{x}\beta $$


**The ($\beta$) coefficients indicate the effect of the independent variable on the log odds of the outcome, where the log odds is also known as the logit.** We can interpret the ($\beta$)’s as follows:

For a unit change in ($x_k$), we expect the log of the odds of the outcome to change by ($\beta_k$) units, holding all other variables constant.

This interpretation does not depend on the level of ($X_k$) or the levels of the other variables in the model. In this regard, it is just like the linear regression model. The problem is that a change of ($\beta_k$) in the log odds has little substantive meaning for most people. **Consequently, tables of logit coefficients typically have little value for conveying the magnitude of effects. As an alternative, odds ratios can be used to explain the effects of independent variables on the odds, which we consider in the next section.**

### 1.1 Interpretation using odds ratios

Effects for the logit model (but not the probit model) can be interpreted in terms of changes in the odds. For binary outcomes, we typically consider the odds of observing a positive outcome, coded 1, versus a negative outcome, coded 0:

$$\Omega\left(\mathbf{x}\right)=\frac{\Pr\left(y=1\mid\mathbf{x}\right)}{\Pr\left(y=0\mid\mathbf{x}\right)}=\frac{\Pr\left(y=1\mid\mathbf{x}\right)}{1-\Pr\left(y=1\mid\mathbf{x}\right)}$$

In the logit model, the log odds are a linear combination of the \(x\)'s and $beta's$. For example, consider a model with three independent variables:

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.1.png" style="width:450px;height:100px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


**The problem with interpreting these \( \beta \)'s directly is that changes in log odds are not substantively meaningful to most audiences.**

To make the interpretation more meaningful, **we can transform the log odds to the odds by taking the exponential of both sides of the equation.** This leads to a model that is multiplicative instead of linear but in which the outcome is the odds:

$$\Omega\left(\mathbf{x},x_{3}\right)=e^{\beta_{0}}e^{\beta_{1}x_{1}}e^{\beta_{2}x_{2}}e^{\beta_{3}x_{3}}$$

Our notation emphasizes the value of \(X_3\), which we want to increase by 1:



\begin{align*}
\Omega\left(\mathbf{x},x_3+1\right) &= e^{\beta_0}e^{\beta_1x_1}e^{\beta_2x_2}e^{\beta_3\left(x_3+1\right)} \\
\end{align*}


\begin{align*}
\qquad\qquad\qquad= e^{\beta_0}e^{\beta_0}e^{\beta_1x_1}e^{\beta_2x_2}e^{\beta_3x_3}e^{\beta_3}
\end{align*}


This leads to the odds ratio

$$\frac{\Omega\left(\mathbf{x},x_{3}+1\right)}{\Omega\left(\mathbf{x},x_{3}\right)}=\frac{e^{\beta_{0}}e^{\beta_{1}x_{1}}e^{\beta_{2}x_{2}}e^{\beta_{3}x_{3}}e^{\beta_{3}}}{e^{\beta_{0}}e^{\beta_{1}x_{1}}e^{\beta_{2}x_{2}}e^{\beta_{3}x_{3}}}=e^{\beta_{3}}$$

Accordingly, we can interpret the exponential of the logit coefficient as follows:

For a unit change in $x_k$, the odds are expected to change by a factor of $\exp(\beta_k)$, holding other variables constant.

For $\exp(\beta_k) > 1$, you could say that the odds are "exp($\beta_k$) times larger," and for $\exp(\beta_k) < 1$, you could say that the odds are $\\exp(\beta_k)$ times smaller. If $\exp(\beta_k) = 1$, then $X_k$ does not affect the odds. We can evaluate the effect of a standard deviation change in $x_k$ instead of a unit change:

**The odds ratio is computed by changing one variable, while holding all other variables constant. This means that the formula in (6.1) cannot be used when the variable being changed is mathematically linked to another variable. For example, if $x_1$ is age and $x_2$ is age-squared, you cannot increase $x_1$ by 1 while holding $x_2$ constant.**In such cases, the odds ratio computed as $\exp(\beta_k)$ should not be interpreted.

Although odds ratios are a common method of interpretation for logit models, it is essential to understand their limitations. **Most importantly, they do not indicate the magnitude of the change in the probability of the outcome.** We begin with an example from our model of labor force participation, followed by a few words of caution.

The output from **`logit`** with the **`or`** option shows the odds ratios instead of the estimated $\beta$’s:

```
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

Here are some examples of interpretations:
* For each additional young child, the odds of being in the labor force decrease by a factor of 0.25, holding all other variables constant (p < 0.01).
* If a woman attended college, her odds of labor force participation are 2.22 times larger than a woman who did not attend college, holding all other variables constant (p < 0.01).

**Odds ratios for categorical variables.** Multiple odds ratios are associated with a multiple-category independent variable. For example, the two coefficients for agecat are relative to the base category of being 30 to 39. Accordingly, we can interpret the coefficient labeled 40-49 as follows:

Being age 40 to 49 compared with being 30 to 39 decreases the odds of being in the labor force by a factor of 0.53, holding all other variables constant (p < 0.01).

And similarly for the coefficient for 50+. If you are interested in the coefficient for the effects of being 40 to 49 compared with being 50 or older, you can use the **`pwcompare`** command, where option **`eform`** requests the exponential of the coefficients, which are the odds ratios. The option **`effects`** requests p-values and confidence intervals.

```
pwcompare agecat, effects eform
```

    Pairwise comparisons of marginal linear predictions
    
    Margins: asbalanced
    
    ---------------------------------------------------------------------------------
                    |                            Unadjusted           Unadjusted
                    |     exp(b)   Std. err.      z    P>|z|     [95% conf. interval]
    ----------------+----------------------------------------------------------------
    lfp             |
             agecat |
    40-49 vs 30-39  |       0.53       0.11    -3.00   0.003         0.35        0.80
      50+ vs 30-39  |       0.28       0.07    -4.92   0.000         0.17        0.46
      50+ vs 40-49  |       0.52       0.11    -2.98   0.003         0.34        0.80
    ---------------------------------------------------------------------------------

We conclude the following:

Being 50 or older compared with being 40 to 49 decreases the odds of being in the labor force by a factor of 0.52, holding all other variables constant (p < 0.01).

Although any two of the pairwise coefficients imply the third (for example, $0.521 \times 0.534 = 0.278$), it is often useful to see all the coefficients and report those that are most useful for the substantive application.

**Confidence intervals for odds ratios.**

**If you report the odds ratios instead of the untransformed $\beta$ coefficients, then the 95% confidence interval of the odds ratio is often reported instead of the standard error. The reason is that the odds ratio is a nonlinear transformation of the logit coefficient, so the confidence interval is asymmetric**. For example, if the logit coefficient is 0.75 with a standard error of 0.25, the 95% interval around the logit coefficient is approximately [0.26, 1.24], but the confidence interval around the odds ratio $\exp(0.75) = 2.12$ is $[\exp(0.26), \exp(1.24)] = [1.30, 3.46]$. The **`or`** option for the **`logit`** command reports odds ratios and includes confidence intervals.

**Odds ratios for changes other than one.** 

You can compute the odds ratio for changes in $X_k$ other than 1 with the formula

$$\frac{\Omega\left(\mathbf{x},x_k+\delta\right)}{\Omega\left(\mathbf{x},x_k\right)}=e^{\delta\beta_k}=e^\delta e^{\beta_k}$$

For example,

Increasing income by $10,000$ decreases the odds by a factor of 0.70 $(= $e^{(-0.035 \times 10)}$) holding all other variables constant.

Accordingly, the odds ratio for a standard deviation change of an independent variable equals $\exp(\beta_k \cdot S_k)$, where $S_k$ is the standard deviation of $x_k$. The odds ratios for both a unit and a standard deviation change of the independent variables can be obtained with `listcoef`:

```
listcoef, help
```

    logit (N=753): Factor change in odds 
    
      Odds of: in LF vs not in LF
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|       e^b   e^bStdX     SDofX
    -------------+----------------------------------------------------------
              k5 |   -1.3916   -7.250    0.000     0.249     0.482     0.524
            k618 |   -0.0657   -0.961    0.336     0.936     0.917     1.320
                 |
          agecat |
          40-49  |   -0.6268   -3.003    0.003     0.534     0.737     0.487
            50+  |   -1.2791   -4.924    0.000     0.278     0.589     0.414
                 |
              wc |
        college  |    0.7977    3.481    0.001     2.220     1.432     0.450
                 |
              hc |
        college  |    0.1359    0.661    0.508     1.146     1.069     0.488
             lwg |    0.6099    4.045    0.000     1.840     1.431     0.588
             inc |   -0.0351   -4.238    0.000     0.966     0.665    11.635
        constant |    1.0140    3.545    0.000         .         .         .
    ------------------------------------------------------------------------
           b = raw coefficient
           z = z-score for test of b=0
       P>|z| = p-value for z-test
         e^b = exp(b) = factor change in odds for unit increase in X
     e^bStdX = exp(b*SD of X) = change in odds for SD increase in X
       SDofX = standard deviation of X
    
By using the coefficients for **lwg** in the column **e^bStdX**. we can say

For a standard deviation increase in the log of the wife’s expected wages, the odds of being in the labor force are 1.43 times greater, holding all other variables constant.

**Odds ratios act as multiplicative coefficients.** 

When interpreting them, keep in mind that they are multiplicative. A value of 1 indicates no effect, positive effects are greater than 1, and negative effects are between 0 and 1. To compare magnitudes, invert the negative effect or the reciprocal of the positive effect. For instance, an odds ratio of 2 is equivalent to an odds ratio of 0.5 (1/2). Therefore, an odds ratio of 0.1 (1/10) is much "larger" than the odds ratio 2 (1/0.5). The multiplicative scale also simplifies determining the effect on the odds of the event not occurring—just take the inverse of the effect on the odds of the event occurring. **`Listcoef`** automatically calculates this if you specify the **`reverse`** option.

```
listcoef, reverse
```

    logit (N=753): Factor change in odds 
    
      Odds of: not in LF vs in LF
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|       e^b   e^bStdX     SDofX
    -------------+----------------------------------------------------------
              k5 |   -1.3916   -7.250    0.000     4.021     2.073     0.524
            k618 |   -0.0657   -0.961    0.336     1.068     1.091     1.320
                 |
          agecat |
          40-49  |   -0.6268   -3.003    0.003     1.872     1.357     0.487
            50+  |   -1.2791   -4.924    0.000     3.593     1.698     0.414
                 |
              wc |
        college  |    0.7977    3.481    0.001     0.450     0.698     0.450
                 |
              hc |
        college  |    0.1359    0.661    0.508     0.873     0.936     0.488
             lwg |    0.6099    4.045    0.000     0.543     0.699     0.588
             inc |   -0.0351   -4.238    0.000     1.036     1.504    11.635
        constant |    1.0140    3.545    0.000         .         .         .
    ------------------------------------------------------------------------

The header indicates that columns **e^b** and **e^bStdX** now contain the factor changes in the odds of the outcome not in LF versus the outcome in LF, whereas before we computed the factor change in the odds of in LF versus not in LF. We can interpret the result for $k_5$ as follows:

For each additional child, the odds of not being in the labor force are increased by a factor of 4.02 (=1/0.48$), holding all other variables constant.

**Percentage change in the odds.** 

Instead of a multiplicative or factor change in the outcome, some people prefer the percentage change:

percentage change in odds = 

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.2.png" style="width:250px;height:70px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

This is shown by **`listcoef`** with the **`percent`** option

```
listcoef, help percent
```

    logit (N=753): Percentage change in odds 
    
      Odds of: in LF vs not in LF
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|         %     %StdX     SDofX
    -------------+----------------------------------------------------------
              k5 |   -1.3916   -7.250    0.000     -75.1     -51.8     0.524
            k618 |   -0.0657   -0.961    0.336      -6.4      -8.3     1.320
                 |
          agecat |
          40-49  |   -0.6268   -3.003    0.003     -46.6     -26.3     0.487
            50+  |   -1.2791   -4.924    0.000     -72.2     -41.1     0.414
                 |
              wc |
        college  |    0.7977    3.481    0.001     122.0      43.2     0.450
                 |
              hc |
        college  |    0.1359    0.661    0.508      14.6       6.9     0.488
             lwg |    0.6099    4.045    0.000      84.0      43.1     0.588
             inc |   -0.0351   -4.238    0.000      -3.4     -33.5    11.635
        constant |    1.0140    3.545    0.000         .         .         .
    ------------------------------------------------------------------------
           b = raw coefficient
           z = z-score for test of b=0
       P>|z| = p-value for z-test
           % = percent change in odds for unit increase in X
       %StdX = percent change in odds for SD increase in X
       SDofX = standard deviation of X
    
Some interpretations are as follows:

For each additional young child, the odds of being in the labor force decrease by 75%, holding all other variables constant.

A standard deviation increase in the log of a wife’s expected wages increases the odds of being in the labor force by 43%, holding all other variables constant.

Percentage and factor change provide the same information, so which you use is a matter of preference. Although we tend to prefer percentage change, methods for the graphical interpretation of the multinomial logit model (chapter 8) work only with factor change coefficients.

**Limitations of the odds ratio**

Certainly:

The interpretation of the odds ratio assumes that the other variables are held constant, but it does not require that they be held at specific values. This might seem to resolve the problem of nonlinearity, but in practice, it does not. A constant factor change in the odds does not imply a constant change in the probability, and probabilities provide a more meaningful metric for interpretation than do odds.

For example, if the odds are $\frac{1}{50}$, the corresponding probability is $0.020$ because $p = \frac{1}{1 + \text{odds}}$. If the odds double to $\frac{2}{50}$, the probability increases to $0.038$ for a change of $0.019$. Depending on your substantive purposes, this small change may be trivial or may be quite important (such as when you identify a risk factor that makes it twice as likely that a subject will contract a fatal disease).

Meanwhile, if the odds are $\frac{1}{1}$ and double to $\frac{2}{1}$, the probability increases from $0.500$ to $0.667$ for a change of $0.167$. The odds ratio is the same, but the change in the probability is much larger.

The substantive meaning of a given odds ratio depends on the specific value of the odds before they change. Those odds in turn depend on the predicted probability, which in turn depends on the specific values of all independent variables in the model. For describing results in terms of probabilities, there is no way around the nonlinearity of the model.

Odds ratios in case-control studies

Odds ratios may provide the best alternative for contexts in which the probabilities are determined by the sample design and accordingly are not obviously of substantive interest. The key example is case-control studies, which are especially common in epidemiology. **Case-control studies recruit cases with a disease separately from the controls without the disease, and commonly studies will recruit equal proportions of cases and controls even though cases are relatively rare in the population and controls are abundant.**In other words, the proportion of people with a disease in a case-control study has nothing to do with the proportion who actually have the disease in a population.

Logit models are typically used in such studies because, if assumptions are satisfied, odds ratios estimated from a case-control study can be extrapolated to a population (Hosmer, Lemeshow, and Sturdivant 2013). However, because the baseline probability in the population is much lower than the proportion in the sample (and might not even be known), interpreting the effects of independent variables in terms of the effects on the probability of being a case versus being a control in one’s sample typically has no substantive pertinence.

### 1.2 (Advanced) Interpretation using y*

>Binary logit and probit models are rarely interpreted in terms of the latent variable $y^*$. Accordingly, this section is primarily useful to provide a deeper understanding of identification and why logit coefficients are generally larger than probit coefficients.

As discussed in section 5.1.1, the logit and probit models can be derived from regression of a latent variable $y^*$.

$$y^{*}=\mathbf{x}\beta+\varepsilon $$

where $\varepsilon$ is a random error. For the probit model, we assume $\varepsilon$ is normal with $\operatorname{Var}(\varepsilon)=1.$ For logit, we assume $\varepsilon$ is distributed logistically with $\mathrm{Var}(\varepsilon)=\pi^2/3$ As with the linear regression model, the marginal change in $y^*$ with respect to $xk$ is

$$\frac{\partial y^*}{\partial x_k}=\beta_k$$

However, because $y^*$ is latent, its true metric is unknown and depends on the identification assumption we make about the variance of the errors


As we saw in section 5.2.2, the coefficients produced by **`logit`** and **`probit`** cannot be directly compared with one another. The logit coefficients will typically be about 1.7 times larger than the probit coefficients, simply as a result of the arbitrary assumption about the variance of the error. Consequently, the marginal change in y* cannot be interpreted without standardizing by the estimated standard deviation of y*, which is computed as

$$\widehat{\sigma}_{y^{*}}^{2}=\widehat{\boldsymbol{\beta}}^{\prime}\widehat{\mathrm{Var}}\left(\mathbf{x}\right)\widehat{\boldsymbol{\beta}}+\mathrm{Var}\left(\varepsilon\right)$$

where $\operatorname{Var}\left(\mathbf{x}\right)$ is the covariance matrix for the observed $x’s$,$\widehat\beta$ contains maximum likelihood estimates, and $\operatorname{Var}(\varepsilon)=1$ for probit and $\mathrm{Var}(\varepsilon)=\pi^2/3$ for logit. Then the $y^*$-standardized coefficient for $xk$ is


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.3.png" style="width:250px;height:100px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

which can be interpreted as follows:

For a unit increase in $x_k$, $y*$ is expected to increase by $\beta_{k}^{S}y^{*}$ standard deviations,holding all other variables constant.

The fully standardized coefficient is

$$\beta_{k}^{S}=\frac{\sigma_{k}\beta_{k}}{\sigma_{y^{*}}}$$

which can be interpreted as follows:

For each standard deviation increase in $x_k$ , $y*$ is expected to increase by $\beta_{k}^{S}$ standard deviations, holding all other variables constant.

These coefficients are computed by **`listcoef`** with the **`std`** option:

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
listcoef, std help
```

    logit (N=753): Unstandardized and standardized estimates 
    
      Observed SD:  0.4956
        Latent SD:  2.0474
    
    -------------------------------------------------------------------------------
                 |         b        z    P>|z|    bStdX    bStdY   bStdXY     SDofX
    -------------+-----------------------------------------------------------------
              k5 |   -1.3916   -7.250    0.000   -0.729   -0.680   -0.356     0.524
            k618 |   -0.0657   -0.961    0.336   -0.087   -0.032   -0.042     1.320
                 |
          agecat |
          40-49  |   -0.6268   -3.003    0.003   -0.305   -0.306   -0.149     0.487
            50+  |   -1.2791   -4.924    0.000   -0.529   -0.625   -0.259     0.414
                 |
              wc |
        college  |    0.7977    3.481    0.001    0.359    0.390    0.175     0.450
                 |
              hc |
        college  |    0.1359    0.661    0.508    0.066    0.066    0.032     0.488
             lwg |    0.6099    4.045    0.000    0.358    0.298    0.175     0.588
             inc |   -0.0351   -4.238    0.000   -0.408   -0.017   -0.199    11.635
        constant |    1.0140    3.545    0.000        .        .        .         .
    -------------------------------------------------------------------------------
           b = raw coefficient
           z = z-score for test of b=0
       P>|z| = p-value for z-test
       bStdX = x-standardized coefficient
       bStdY = y-standardized coefficient
      bStdXY = fully standardized coefficient
       SDofX = standard deviation of X

The y*-standardized* coefficients are in the column labeled bStdY*, and the fully standardized coefficients are in the column bStdXY. We could interpret these coefficients as follows:

For each additional young child, the propensity of a woman to join the labor force decreases by 0.68 standard deviations, holding all other variables constant.

For every standard deviation increase in family income, a woman’s propensity to join the labor force is expected to decrease by 0.199 standard deviations, holding all other variables constant.

Next, we compute the y*-standardized coefficients for probit:

```
probit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
listcoef, std
```

    probit (N=753): Unstandardized and standardized estimates 
    
      Observed SD:  0.4956
        Latent SD:  1.1530
    
    -------------------------------------------------------------------------------
                 |         b        z    P>|z|    bStdX    bStdY   bStdXY     SDofX
    -------------+-----------------------------------------------------------------
              k5 |   -0.8396   -7.503    0.000   -0.440   -0.728   -0.382     0.524
            k618 |   -0.0412   -1.011    0.312   -0.054   -0.036   -0.047     1.320
                 |
          agecat |
          40-49  |   -0.3816   -3.058    0.002   -0.186   -0.331   -0.161     0.487
            50+  |   -0.7801   -5.000    0.000   -0.323   -0.677   -0.280     0.414
                 |
              wc |
        college  |    0.4821    3.555    0.000    0.217    0.418    0.188     0.450
                 |
              hc |
        college  |    0.0738    0.596    0.551    0.036    0.064    0.031     0.488
             lwg |    0.3710    4.211    0.000    0.218    0.322    0.189     0.588
             inc |   -0.0211   -4.368    0.000   -0.245   -0.018   -0.212    11.635
        constant |    0.6222    3.692    0.000        .        .        .         .
    -------------------------------------------------------------------------------

Although the estimates of $\beta$ in column b are uniformly smaller than those from **`logit`**, the β-standardized and fully standardized coefficients in columns bStdY and **bStdY** are very similar, which demonstrates that the differences in the magnitude of coefficients in logit and probit are due to differences in scale.

An issue related to y*-standardized coefficients arises when researchers compare coefficients across models. In the linear regression model, mediating variables are often added to a model, and the change in coefficients is interpreted as indicating how much of the effect of an independent variable on the dependent variable is due to the indirect effect of the mediating variable (see, for example, Breen, Karlson, and Holm [2013, 166—167]). For example, if the coefficient estimating the effect of childhood socioeconomic status (SES) on adult earnings is reduced when educational attainment is added to the model, one might say that half the effect of SES on earnings is explained by educational attainment.

This interpretation of a change in unstandardized logit or probit coefficients is problematic (Winship and Mare 1984). In linear regression, when independent variables are added to a model,$\widehat{\mathrm{Var}}(\mathbf{x}\widehat{\boldsymbol{\beta}})$ increase and $\widehat{\operatorname{Var}(\varepsilon)}$ decrease accordingly,because the observed variance of y must remain the same. In the logit and probit models, when independent variables are added to a model, $\widehat{\mathrm{Var}}(\mathbf{x}\widehat{\beta})$ increases but $\operatorname{Var}(\varepsilon)$ does not change because its value is assumed.Consequently, $\operatorname{Var}(y^{*})$ Must increase. For the BRM, the indirect effects interpretation across models with different independent variables no longer holds because as the model specification changes, the scale of the latent dependent variable changes.

One can think about y*-standardized coefficients as the coefficients we would observe if Var(y*) was rescaled to be fixed to 1. If so, then in terms of ^-standardized coefficients, adding new independent variables to the model does not increase this rescaled Var(y*). For indirect effects, interpreting changes in y-standardized logit and probit coefficients seems clearly preferable to doing so for unstandardized coefficients. Alternatives to y-standardization have also been proposed (Karlson, Holm, and Breen 2012; Breen and Karlson 2013).

## 2 Marginal effects: Changes in probabilities

**A marginal effect measures the change in the probability of an outcome for a change in Xk, holding all other independent variables constant at specific values.** The critical idea is that **one variable is changing while the other variables are not.** There are two varieties of marginal effects. **A marginal change computes the effect of an instantaneous or infinitely small change in Xk. A discrete change computes the effect of a discrete or finite change in Xk.** (See section 4.5 for an introductory discussion of marginal effects.)


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.4.png" style="width:650px;height:400px;" alt="图片描述">
  <figcaption><strong> Marginal change and discrete change in the BRM</strong></figcaption>
</figure>

A marginal change, shown by the tangent to the probability curve at x = 1 in figure 6.1, is the rate of change in the probability for an infinitely small change in $x_k$ holding other variables at specific values:

$$\frac{\partial\Pr(y=1\mid\mathbf{x}=\mathbf{x}^*)}{\partial x_k}$$

Because the effect is computed with a partial derivative, some authors refer to this as the partial change or partial effect. In this formula, x* contains specific values of the independent variables. For example, x* could equal x7 with the observed values for the ith observation, it could equal the means x of all variables, or it could equal any other values. When the meaning is clear, we will refer to x without specifying x*. The important thing is that the value of the marginal effect depends on the specific values of the xk s where the change is computed.

In the BRM , the marginal change has the simple formula
$$\frac{\partial\Pr\left(y_i=1\mid\mathbf{x}\right)}{\partial x_k}=f\left(\mathbf{x}\beta\right)\beta_k$$

where $f$ is the normal probability distribution function (PDF) for probit and the logistic PDF for logit. In logit models, the marginal change has a particularly convenient form:

$$\frac{\partial\Pr\left(y_i=1\mid\mathbf{x}\right)}{\partial x_k}=\Pr\left(y_i=1\mid\mathbf{x}\right)\left[1-\Pr\left(y_i=1\mid\mathbf{x}\right)\right]\beta_k$$

From this formula, we see that the change must be greatest when $\Pr\left(y=1\mid\mathbf{x}\right)=0.5$,where the marginal change is $(0.5)(0.5)\beta_{k}=\beta_{k}/4$.Accordingly, dividing a binary logit coefficient by 4 indicates the maximum marginal change in the probability (Cramer 1991, 8).

As long as the model does not include power or interaction terms, the marginal change for xk has the same sign as $\beita_k$ for all values of x because the PDF is always positive. (Computing marginal effects when powers and interactions are in the model is discussed in section 6.2.1.) The formula also shows that marginal changes for different independent variables differ by a scale factor. For example, the ratio of the marginal effect of $x_j$ to the effect of $x_k$ is

$$\frac{\partial\Pr\left(y_i=1\mid\mathbf{x}\right)/\partial x_j}{\partial\Pr\left(y_i=1\mid\mathbf{x}\right)/\partial x_k}=\frac{f\left(\mathbf{x}\boldsymbol{\beta}\right)\beta_j}{f\left(\mathbf{x}\boldsymbol{\beta}\right)\beta_k}=\frac{\beta_j}{\beta_k}$$

for all values of x. Consequently, while does not tell you the magnitude of $x_k's$ effect, it can tell you how much larger or smaller it is than the effects of other variables.

A discrete change, sometimes called a first difference, is the actual change in the predicted probability for a given change in $x_k$, holding other variables at specific values. For example, the discrete change for an increase in age from 30 to 40 is the change in the probability of being in the labor force as age increases from 30 to 40, holding other variables at specified values. Defining $x_k^{\mathrm{start}}$ as the starting value of $x_k$ and $x_k^{\mathrm{end}}$ as the ending value,the discrete change equals 

$$\frac{\Delta\Pr\left(y=1\mid\mathbf{x}\right)}{\Delta x_k\left(x_k^{\mathrm{start}}\to x_k^{\mathrm{ond}}\right)}=\Pr\left(y=1\mid\mathbf{x},x_k=x_k^{\mathrm{end}}\right)-\Pr\left(y=1\mid\mathbf{x},x_k=x_k^{\mathrm{start}}\right)$$

For binary variables, such as having attended college, the obvious choice is a change from 0 to 1:

$$\frac{\Delta\Pr\left(y_i=1\mid\mathbf{x}\right)}{\Delta x_k\left(0\rightarrow1\right)}=\Pr\left(y_i=1\mid x_k^{\mathrm{end}}=1,\mathbf{x}\right)-\Pr\left(y_i=1\mid x_k^{\mathrm{start}}=0,\mathbf{x}\right)$$

We often are interested in the discrete changes when a variable increases by some amount δ from its observed value. Defining $Pr(y = 1 | x, x_k)$ as the probability at $x$, noting in particular the value of $δ$, the discrete change for a change of $δ$ in Xk equals

$$\frac{\Delta\Pr\left(y=1\mid\mathbf{x}\right)}{\Delta x_k\left(x_k\rightarrow x_k+\delta\right)}=\Pr\left(y=1\mid\mathbf{x},x_k+\delta\right)-\Pr\left(y=1\mid\mathbf{x},x_k\right)$$

We might want to examine a discrete change of one unit, a standard deviation, 15 points for IQ, 4 years for education, the range of income, or 10 years for age.

The discrete change tells you how much the probability actually changes for a given change in a variable. To the degree that the probability curve is linear in the region where the change occurs, the marginal change for $X_k$ approximates the discrete change for a unit increase in $X_k$. The more nonlinear the curve in the region where $X_k$ increases, the greater the difference between the marginal change and the discrete change. Because in general $\partial\Pr(y=1\mid\mathbf{x})/\partial x_k$ dose not equal $\Delta\Pr\left(y=1\mid\mathbf{x}\right)/\Delta x_{k}.$,we prefer the discrete change that indicates the actual amount of change in the probability for a specific change in $x_k$. For example, we find it more meaningful to say that "for a standard deviation increase in income, about 11,000, the probability of labor force participation decreases on average by 0.09" than to say that "the average rate of change in the probability of labor force participation with respect to income is -0.007". However, some fields, such as economics, have a strong preference for marginal change over discrete change for continuous independent variables.

1. **边际变化 (Marginal Change):**
- **概念：** 边际变化是我们微小地改变一个变量时，对概率的影响有多大。
- **例子：** 想象一下，你微调你的年龄，看看这微小的年龄变化对某事件发生的概率有多大影响。

 边际变化的数学表示：
- $\frac{\partial\Pr(y=1\mid\mathbf{x}=\mathbf{x}^*)}{\partial x_k}$
- 解释：表示对于变量 $x_k$ 的微小改变，概率的变化率。

2. **边际变化在BRM中的表达:**
- **概念：** 在二元回归模型中，边际变化的计算方式。
- **例子：** 想象一下，你正在研究某事件的概率，而这个事件的发生可能受到不同因素的影响，比如年龄、收入等。

 边际变化在BRM中的数学表示：
- $\frac{\partial\Pr\left(y_i=1\mid\mathbf{x}\right)}{\partial x_k}=f\left(\mathbf{x}\beta\right)\beta_k$
- 解释：表示变量 $x_k$ 的微小变化对事件概率的影响，其中 $f$ 是概率分布函数，$\beta_k$ 是模型系数。

3. **离散变化 (Discrete Change):**
- **概念：** 离散变化是我们实际改变一个变量时，概率的实际变化。
- **例子：** 想象一下，你从30岁变成40岁，看看这个年龄变化对参与劳动力市场的概率有多大影响。

 离散变化的数学表示：
- $\frac{\Delta\Pr\left(y=1\mid\mathbf{x}\right)}{\Delta x_k\left(x_k^{\mathrm{start}}\to x_k^{\mathrm{end}}\right)}$
- 解释：表示变量 $x_k$ 从起始值 $x_k^{\mathrm{start}}$ 变化到结束值 $x_k^{\mathrm{end}}$ 时，事件概率的实际变化。

4. **为什么有两种变化方式？**
- **边际变化：** 主要用于微小变化，研究概率的敏感度。
- **离散变化：** 更注重实际情况，研究实际变化对概率的影响。



### 2.1 Linked variables

Fundamental to the concept of a marginal effect is the idea that only one variable changes while holding all other variables at specified values. An exception must be made for variables that are linked mathematically. For example, if $x_{\text{age}}$ is age and $X_{\text{agesq}} = x_{\text{age}} \times x_{\text{age}}$, you cannot change $x_{\text{age}}$ while holding $x_{\text{agesq}}$ constant. The change in $x_{\text{age}}$ must be matched by a corresponding change in $x_{\text{agesq}}$. This is easy to illustrate with a discrete change in age from 20 to 30:

$$\begin{aligned}\frac{\Delta\operatorname{Pr}\left(y=1\mid\mathbf{x}\right)}{\Delta\operatorname{gre}\left(20\to30\right)}&=&\operatorname{Pr}\left(y=1\mid\mathbf{x},x_{\mathrm{age}}=30,x_{\mathrm{agesq}}=30^2\right)\\end{aligned}$$


$$\begin{aligned}\quad\quad\qquad\qquad\qquad\&-\operatorname{Pr}\left(y=1\mid\mathbf{x},x_{\mathrm{age}}=20,x_{\mathrm{agesq}}=20^2\right)\\end{aligned}$$


Linked variables must also be considered for the variables being held constant. For example, if we are computing the marginal effect of $x_k$ while holding age at its mean, we need to hold $x_{\text{age}}$ at $\text{mean}(x_{\text{age}})$ and $x_{\text{agesq}}$ at $[\text{mean}(x_{\text{age}}) × \text{mean}(x_{\text{age}})]$ not at $\text{mean}(x_{\text{agesq}})$. Similarly, if your model includes $X_{\text{female}}$, $x_{\text{age}}$, and the interaction $$x_{\mathrm{female}\times\mathrm{age}}=x_{\mathrm{female}}\times x_{\mathrm{age}},$, you cannot change $x_{\mathrm{age}}$ while holding $x_{\mathrm{female}\times\mathrm{age}}$ constant.

Categorical regressors that enter a model as a set of indicators are also linked. Suppose that education has three categories: no high school degree, high school diploma as the highest degree, and college diploma as the highest degree. Let $x_{\text{hs}} = 1$ if high school is the highest degree and equal 0 otherwise; and let $x_{\text{College}} = 1$ if college is the highest degree and equal 0 otherwise. If $x_{\text{hs}} = 1$, then $x_{\text{College}} = 0$. You cannot increase $x_{\text{College}}$ from 0 to 1 while holding $x_{\text{hs}}$ at 1. Computing the effect of having college as the highest degree ($x_{\text{hs}} = 0$, $x_{\text{College}} = 1$) compared with high school as the highest degree ($x_{\text{hs}} = 1$, $x_{\text{College}} = 0$) involves changing two variables:



<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.5.png" style="width:550px;height:150px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

When discussing marginal effects with linked variables, we will say "holding other variables constant" with the implicit understanding that appropriate adjustments for linked variables are being made. A major benefit of using factor-variable notation when specifying a regression model is that **`margins`**, **`mchange`**, **`mtable`**, and **`mgen`** keep track of which variables are linked, and compute predictions and marginal effects correctly.

1. **边际效应概念：** 边际效应是指当我们微小地改变一个变量时，观察这个变化对概率的影响有多大。这里强调了在计算边际效应时，其他变量都被保持在特定的值上，保证我们只观察一个变量的变化对概率的影响。

2. **相关变量的例外情况：** 文中提到，如果有数学上相关的变量，需要特别注意。比如，如果 $x_{\text{age}}$ 表示年龄，而 $X_{\text{agesq}} = x_{\text{age}} \times x_{\text{age}}$ 表示年龄的平方，那么在改变 $x_{\text{age}}$ 的同时，需要相应地调整 $x_{\text{agesq}}$。这是因为它们是数学上相关的。

3. **具体例子：** 通过公式 $\frac{\Delta\operatorname{Pr}\left(y=1\mid\mathbf{x}\right)}{\Delta\operatorname{gre}\left(20\to30\right)}$ 的计算，我们可以看到对于年龄从20到30的变化，事件 $y=1$ 的概率变化是通过两个部分的减法来计算的。第一部分是在年龄为30岁和 $x_{\text{agesq}}$ 为 $30^2$ 的情况下，事件发生的概率；第二部分是在年龄为20岁和 $x_{\text{agesq}}$ 为 $20^2$ 的情况下，事件发生的概率。两者之差即为由于年龄变化引起的边际效应。

4. **变量的数学关系和离散变化：** 文中还提到，对于有数学关系的变量，需要适当调整。比如，如果模型中包含了交互项 $x_{\text{female}\times\text{age}}$，在改变 $x_{\text{age}}$ 时需要注意。类别型变量也需要特殊处理，确保在改变某一个变量时，与之相关的其他变量也得到相应调整。


### 2.2 Summary measures of change

The marginal effect of a variable depends on the specific values of all independent variables. Because the effect of $x_k$ differs for each observation (unless, of course, multiple observations have identical values), there is a distribution of marginal effects in the sample. For interpretation, we seek a simple, informative summary of this distribution of effects. There are three basic approaches:

* **Marginal effect at the mean (MEM)**. Compute the marginal effect of $x_k$ with all variables held at their means.
* **Marginal effect at representative values (MER)**. Compute the marginal effect of $X_k$ with variables held at specific values that are selected for being especially instructive for the substantive questions being considered. The MEM is a special case of the MER.
* **Average marginal effect (AME)**. Compute the marginal effect of $X_k$ for each observation at its observed values $x_i$, and then compute the average of these effects.

We consider each measure before discussing how to decide which measure is appropriate for your application.

**MEMs and MERs**

The MEM is computed with all variables held at their means. For a marginal change, this is

$$\frac{\partial\Pr\left(y=1\mid\overline{\mathbf{x}},x_k=\overline{x}_k\right)}{\partial x_k}$$

which can be interpreted as follows:

For someone who is average on all characteristics, the marginal change of $x_k$ is

The discrete change equals

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.6.png" style="width:350px;height:80px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


For someone who is average on all characteristics, increasing $x_k$ by 8 changes the probability by ...

The MER would replace "who is average" with a description of the values of the covariates.

**AMEs**

The AME is the mean of the marginal effect computed at the observed values for all observations in the estimation sample. For a marginal change, this is

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.7.png" style="width:380px;height:80px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

which can be interpreted as follows:

The average marginal effect of $x_k$ is ...

The average discrete change equals


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.8.png" style="width:380px;height:80px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

which is interpreted as follows:

On average, increasing $x_k$ by $\delta $ increases the probability by ...

For factor variables or changes from one fixed value to another (for example, from the maximum to the maximum), we say

On average, increasing $X_k$ from start-value to end-value increases the probability by...

Standard errors of marginal effects

For each of these measures of change, standard errors can be computed using the delta method (Agresti 2013, 72-75; Wooldridge 2010, 576-577; Xu and Long 2005; [r] margins). The standard errors allow you to test whether a marginal effect is 0, to add a confidence interval to the estimated effect, and to test such things as whether marginal effects are equal at different values of the independent variables.

### 2.3 Should you use the AME, the MEM , or the MER?

The popularity of the MEM is probably because of the ease of computation. Computing an AME, in principle, involves N times more computation than the corresponding MEM. With the rapid growth in computing power, this is a trivial issue compared with having readily available software that easily computes the AME. For example, with our **prchange** command in **SPost9**, computing MEMs was trivially easy. Although you could compute the AME with prchange, you needed to write your own program to collect and summarize the computations for each observation. Few people, ourselves included, bothered to do that. With Stata's `margins` and our `mchange`, it is as easy to compute AMEs as MEMs. These computational advances do not, however, imply that the AME is always the best way to assess the effect of a variable. There are several issues to consider when deciding which measure of change to use.

Does the marginal effect computed at the mean of all variables provide useful information about the overall effect of that variable? This is relevant not only in deciding what to do in current analyses but when evaluating past research that used the MEM. A common criticism of the MEM is that typically there is no actual case in the dataset for which all variables equal the mean. Most obviously, with binary independent variables, the mean does not correspond to a possible value of an observation. For example, a variable like "pregnant" is measured as 0 and 1 without it being possible to observe someone with a value equal to a sample mean of intermediate value. This issue alone leads some to disfavor the MEM (Hanmer and Kalkan 2013). We are not ourselves as concerned about this point because holding a binary variable at its mean is, roughly speaking, taking a weighted mean of effects for each group. If the groups are a focus of the analysis, you can compute MERs for each group by using group-specific means. Alternatively, effects can be computed at the modal values of the binary variables, but this ignores everyone who is in a less well-represented group.

Sometimes, it is argued that the MEM is a reasonable approximation to the AME. Although Greene and Hensher (2010, 36) correctly observed that the AME and MEM are often similar, they incorrectly suggest that this is especially true in large samples. Although the two measures will often be similar, they can differ in substantively meaningful ways, and whether this is the case has little to do with whether a sample is bigger or smaller.

Bartus (2005) and Verlinda (2006) explain more precisely when $MEM$ and $AME$ differ and which is larger. For the binary logit and probit models, the difference between the $AME$ and $MEM$ for depends on three things: the probability that $y = 1$ when all $\beta$'s are held to their means, the variance of $x_k$, and the size of $\beta_k$ (Bartus 2005; Hanmer and Kalkan 2013, SI). The sign of the difference between the $AME$ and $MEM$ depends on $Pr(y = 1 | x)$, with the $AME$ being larger at lower and higher probabilities. In the middle, the $MEM$ is larger, with the largest difference occurring when $Pr(y = 1 | x) = 0.5$. The $AME$ and $MEM$ will be equal when the probability is about 0.21 and 0.79 for the binary logit model, and about 0.15 and 0.85 for the binary probit model.

The $AME, MEM,$ and $MER$ are each summary measures, and no single summary of effects is ideal for all situations. Broadly speaking, we believe that the $AME$ is the best summary of the effect of a variable. Because it averages the effects across all cases in the sample, it can be interpreted as the average size of the effect in the sample. The $MEM$ is computed at values of the independent variables that might not be representative of anyone in the sample.

However, both $AME$ and $MEM$ are limited because they are based on averages. If the average value of each regressor is a substantively interesting location in the data, the $MEM$ is useful because it tells you the magnitude of the effect for someone with those or similar characteristics. If the average of the independent variables is not an interesting location, it is not useful. If you are interested in the average effect in the sample, the $AME$ is appropriate. However, it is possible that nobody in the sample has a marginal effect that is close to the $AME$. If, for example, you are interested in the effect of a treatment for young minority women, knowing the effect for someone who is average is not helpful. Similarly, the average effect of the treatment for the entire sample does not tell you the effect for the group of young minority women you are interested in. In general, the $AME$ is not necessarily more informative than a set of $MERs$ computed at substantively interesting places. Or, as shown in section 6.2.5, you can examine the distribution of effects for all observations.

No single number is a substitute for understanding how predictions vary over the range of one’s data and for conveying the fact of that variation when it is substantively meaningful. Thus the best measure is the one that addresses the goals of your research. Although examining $AMEs$ of your independent variables is an important step in data analysis, this should be followed by a more detailed analysis of predictions in tables or graphs.

这一大段主要探讨了为什么一些研究者更倾向于使用 \(MEM\)（在均值处计算边际效应）而不是 \(AME\)（平均边际效应）。其中提到了计算 \(AME\) 相对于 \(MEM\) 更加复杂，尤其是在数据量较大时，所需计算量更大。然而，随着计算能力的增强，这变得不那么重要。作者还提到了一些关于 \(MEM\) 的批评，特别是当样本中不存在所有变量均值的实际案例时。对此，作者认为这个问题并不是很严重，因为将二进制变量保持在均值处大致相当于对每个组计算效应的加权平均。如果对组是分析的焦点，可以通过使用组特定均值计算每组的 \(MER\)。或者，可以在二进制变量的众数值处计算效应，但这会忽略那些在较少表示的组中的个体。

有时人们认为 \(MEM\) 是对 \(AME\) 的合理近似。虽然 Greene 和 Hensher（2010, 36）正确观察到 \(AME\) 和 \(MEM\) 通常相似，但错误地认为这在大样本中尤为真实。尽管这两个测度通常会相似，但它们在实质上有可能存在差异，而这与样本大小的大小关系不大。

Bartus（2005）和 Verlinda（2006）更详细地解释了 \(MEM\) 和 \(AME\) 何时不同以及哪个更大。对于二元 logit 和 probit 模型，\(AME\) 和 \(MEM\) 之间的差异取决于三个因素：当所有 $\beta$ 保持在均值时， $y = 1$ 的概率， $x_k$ 的方差以及 $\beta_k$ 的大小（Bartus 2005; Hanmer 和 Kalkan 2013, SI）。 \(AME\) 和 \(MEM\) 之间的差异的符号取决于 $Pr(y = 1 | x)$， \(AME\) 在较低和较高概率时更大。在中间， \(MEM\) 更大，最大的差异发生在 $Pr(y = 1 | x) = 0.5$ 时。对于二元 logit 模型，当概率约为 0.21 和 0.79 时， \(AME\) 和 \(MEM\) 将相等；对于二元 probit 模型，当概率约为 0.15 和 0.85 时，它们将相等。

\(AME\)，\(MEM\) 和 \(MER\) 都是对效应的汇总测量，没有单一的效应摘要适用于所有情况。广义来说，作者认为 \(AME\) 是对变量效应的最佳摘要。因为它对样本中的所有案例效应进行了平均，所以它可以被解释为样本中效应的平均大小。 \(MEM\) 是计算在样本中任何人都可能不具代表性的自变量的值。

然而，\(AME\) 和 \(MEM\) 都有局限性，因为它们基于平均值。如果每个回归变量的平均值在数据中是一个实质上有趣的位置，则 \(MEM\) 是有用的，因为它告诉你在具有这些或类似特征的人身上效应的大小。如果独立变量的平均值不是一个有趣的位置，那么它就没有用。如果你对样本中的平均效应感兴趣，那么 \(AME\) 是合适的。然而，有可能在样本中没有人的边际效应接近 \(AME\)。例如，如果你对年轻的少数族裔女性的治疗效应感兴趣，知道对于一个平均值的人来说效应是没有帮助的。同样，对于整个样本的治疗的平均效应并不告诉你你感兴趣的年轻少数族裔女性群体的效应。总的来说，\(AME\) 不一定比在实质上有趣的地方计算的一组 \(MER\) 更具信息量。或者，如第 6.2.5 节所示，你可以检查所有观察的效应分布。

没有一个数字可以替代对预测如何在数据范围内变化以及在实质上有意义时传达这种变化事实的理解。因此，最好的度量是符合你研究目标的度量。虽然检查独立变量的 \(AMEs\) 是数据分析中的一项重要步骤，但这应该在表格或图形中更详细地分析预测之后进行。

### 2.4 Examples of marginal effects

In this section, we use our model of labor force participation to illustrate the computation and interpretation of marginal effects with **`mchange`**. **The **`mchange`** command makes it simple to compute marginal effects for different amounts of changes, either averaging effects over the sample or computing them at fixed values.** **`mchange`** uses **`margins`** to compute the effects, which are then collected into a compact table. For example, running **`mchange`** after fitting our baseline model creates a 30-line table that summarizes 500 lines of output from a dozen **`margins`** commands. If you want to learn more about **`margins`**, you can add the option **`details`** to **`mchange`** to see how to use **`margins`** output. Information on using **`margins`** to compute marginal effects is given in section 6.2.6.


We begin by fitting our model and storing the estimates so that they can be restored later:

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
estimates store base
```

```
estat summarize, labels
```

    Estimation sample logit                                      Number of obs =        753
    
    ---------------------------------------------------------------------------------------
        Variable |      Mean   Std. dev.     Min      Max   Label
    -------------+-------------------------------------------------------------------------
             lfp |  .5683931   .4956295        0        1   In paid labor force?
              k5 |  .2377158    .523959        0        3   # kids < 6
            k618 |  1.353254   1.319874        0        8   # kids 6-18
          agecat |                                          Wife's age group
          40-49  |  .3851262   .4869486        0        1
            50+  |  .2191235   .4139274        0        1
              wc |                                          Wife attended college?
        college  |  .2815405   .4500494        0        1
              hc |                                          Husband attended college?
        college  |  .3917663   .4884694        0        1
             lwg |  1.097115   .5875564 -2.05412  3.21888   Log of wife's estimated wages
             inc |  20.12897    11.6348    -.029       96   Family income excluding wife's
    ---------------------------------------------------------------------------------------


We will next show how to compute and interpret AMEs for continuous and factor variables, before examining the corresponding MEMs. Marginal effects in models with powers and interactions are then considered. Finally, we show how to compute the distribution of effects for observations in the estimation sample.

#### 2.4.1 AMEs for continuous variables
For continuous independent variables, **`mchange`** computes the **average marginal change** and **average discrete change** of 1 and a standard deviation. To assess the effects of income and wages, type:

```
mchange inc lwg
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                 |    Change    p-value 
    -------------+---------------------
    inc          |                     
              +1 |    -0.007      0.000 
             +SD |    -0.086      0.000 
        Marginal |    -0.007      0.000 
    lwg          |                     
              +1 |     0.120      0.000 
             +SD |     0.072      0.000 
        Marginal |     0.127      0.000 
    
    Average predictions
    
                 | not in LF      in LF 
    -------------+---------------------
      Pr(y|base) |     0.432      0.568 
    
The average predictions, listed below the table of changes, show that in the sample the average predicted probability of being in the labor force is 0.432. This is the same value you would obtain by first running **`predict`** and then computing the mean of the predictions. In later examples, we often suppress this result by adding the **`brief`** option. Summarizing the AMEs for a standard deviation change, we can say

>Holding other variables at their observed values, increasing income by one standard deviation, roughly $12,000$, decreases the probability of labor force participation on average by 0.09. An increase of one standard deviation in the log of anticipated wages, about O.G., increases the probability by 0.07. Both effects are significant at the 0.001 level.

There are two points of interest here. First, the marginal and unit discrete changes are similar, which reflects that the probability curve is nearly linear for a change of 1 in either variable. Second, for **`lwg`**, the marginal change and unit discrete change are potentially misleading because a unit change in **`lwg`** corresponds to a change of nearly two standard deviations.

The **`delta(#)`** option allows us to compute the effect of a change of any amount # to replace the default change of one standard deviation. For example, to compute the effect of a $5,000$ change in income, we use **`delta(5)`**:

```
mchange inc, delta(5) brief
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                 |    Change    p-value 
    -------------+---------------------
    inc          |                     
              +1 |    -0.007      0.000 
          +delta |    -0.037      0.000 
        Marginal |    -0.007      0.000 

This can be interpreted as follows:

>On average, an increase of $5,000$ in income decreases the probability of labor force participation by 0.04 (p < 0.001).

Note that our reporting of results has become shorter by no longer making explicit that other variables are kept at their observed values.

For the number of young children, **k5**, the most reasonable effect is for an increase of one child, so we specify **`amount(one)`**:

```
mchange k5, amount(one) brief
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                 |    Change    p-value 
    -------------+---------------------
    k5           |                     
              +1 |    -0.281      0.000 

We conclude the following:

>On average, holding other variables at their observed values, increasing the number of young children in a family by one is expected to decrease the probability of labor force participation by 0.28 (p < 0.001, two-tailed).

A quick way to assess the maximum potential impact of continuous variables (that is, nonfactor variables) is to compute the AME over the range. Changes over the range tell us how much you would expect the outcome probability to change in the unlikely event of massive changes in a variable, holding other variables constant. For example, what would happen if a person increased her family income from $0$ to $104,000$, without other variables changing? Although this type of massive change is not likely to occur in the real world, it provides a bound for the largest effect you could find. If the change is small and nonsignificant, the lack of effect might be substantively interesting, but you are unlikely to learn anything more about the variable by analyzing it further.

Consider the effects of changing from 0 to the maximum number of young or older children in the sample:

```
mchange k5 k618, amount(range) brief
```

        logit: Changes in Pr(y) | Number of obs = 753
        
        Expression: Pr(lfp), predict(pr)
        
                     |    Change    p-value 
        -------------+---------------------
        k5           |                     
               Range |    -0.599      0.000 
        k618         |                     
               Range |    -0.110      0.336 
        
Using information on the range from the summary statistics shown above, we see that changing from 0 to 3 young children has a very large and significant AME of -0.60. Changing from 0 to 8 older children, on the other hand, has a nonsignificant effect. Accordingly, **k618** will not be considered further in our examples of interpretation.

Because a variable’s range can be influenced by even a single extreme observation (for example, one person with an unusually high income), we suggest using a trimmed range for some variables. For example, **`mchange inc lwg, amount (range) trim(5)`** computes the AME for a change in income and anticipated wages from the 5th percentile to the 95th percentile:

```
mchange inc lwg, amount(range) trim(5) brief
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                 |    Change    p-value 
    -------------+---------------------
    inc          |                     
       5% to 95% |    -0.249      0.000 
    lwg          |                     
       5% to 95% |     0.239      0.000 

We can interpret the discrete change for inc as follows:

>On average the probability of labor force participation will decrease by 0.25 if respondents changed from the 5th percentile of income to the 95th percentile (p < 0.001, two-tailed).

We could interpret lwg similarly

To fully understand the meaning of a discrete change over the range, we need to know the range over which a variable varies. We obtain this information by using **``**centile varlist, centile(0 5 95 100)**``** to request the 5th and 95th percentiles along with the minimum and maximum. Here we request information for **inc** and **lwg**:

```
centile inc lwg, centile(0 5 95 100)
```

                                                              Binom. interp.   
        Variable |       Obs  Percentile    Centile        [95% conf. interval]
    -------------+-------------------------------------------------------------
             inc |       753          0   -.0290001       -.0290001   -.0290001*
                 |                    5      7.0428        6.334469    7.789306
                 |                   95       41.19        37.50825    45.02423
                 |                  100          96              96          96*
             lwg |       753          0   -2.054124       -2.054124   -2.054124*
                 |                    5    .2065675        .1047049    .3321936
                 |                   95    2.084091        1.953158    2.153738
                 |                  100    3.218876        3.218876    3.218876*
    
    * Lower (upper) confidence limit held at minimum (maximum) of sample

Comparing the 95th with the 100th percentile shows that for both variables, the trimmed range excludes extreme observations.


"We can obtain more information about the discrete changes by using the option **`statistics0`** to request the starting and ending probabilities. To show this, we compute the changes for income and log of wages:"

```
mchange inc lwg, amount(range) trim(5) ///
    statistics(change from to pvalue) brief
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                 |    Change       From         To    p-value 
    -------------+-------------------------------------------
    inc          |                                           
       5% to 95% |    -0.249      0.660      0.411      0.000 
    lwg          |                                           
       5% to 95% |     0.239      0.454      0.693      0.000 

Using the new information, wre can elaborate our interpretation of the effect of income:

>Changing family income from its 5th percentile of $7,000$ to the 95th percentile of $41,000$ on average decreases the probability of a woman being in the labor force from 0.66 to 0.41, a decrease of 0.25 (p < 0.001, two-tailed).

The effect for **lwg** could be interpreted similarly.

#### 2.4.2 AMEs for factor variables

**For binary independent variables, the only reasonable change is from 0 to 1, which is the default when factor-variable notation is used.** Because he and wc were included in the logit specification as i.h c and i.w c, mchange automatically computes a discrete change from 0 to 1:

```
mchange hc wc, stat(change from to pvalue) brief
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                   |    Change       From         To    p-value 
    ---------------+-------------------------------------------
    hc             |                                           
     college vs no |     0.028      0.558      0.586      0.508 
    wc             |                                           
     college vs no |     0.162      0.525      0.688      0.000 

We interpret these results as follows:

>On average, having attended college increases a woman’s probability of labor force participation from 0.53 to 0.69, a change of 0.16 (p < 0.001), while the effect of the husband having attended college is not significant.

The **`mchange`** command also works with factor variables th at have more than two categories. Because **agecat** has three categories and was entered into our model as **i.agecat**, **`mchange`** computes effects of changes between all categories, referred to as contrasts:

```
mchange agecat, stat(change from to pvalue) brief
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                    |    Change       From         To    p-value 
    ----------------+-------------------------------------------
    agecat          |                                           
     40-49 vs 30-39 |    -0.124      0.676      0.552      0.002 
       50+ vs 30-39 |    -0.262      0.676      0.414      0.000 
       50+ vs 40-49 |    -0.138      0.552      0.414      0.002 

Each contrast can be interpreted exactly as the interpretation of a binary independent variable. The discrete change labeled 40-49 vs 30-39 is the effect of being in the age group 40-49 compared with being in the age group 30-39, and so on for other comparisons.

>On average, being 40 to 49 compared with being 30 to 39 decreases the probability of labor force participation by 0.12 (p < 0.01). Being 50 or older compared with being 30 to 39 decreases the probability by 0.26 (p < 0.001). Being 50 or older compared with being 40 to 49 decreases the probability by 0.14 (p < 0.01).

Notice that knowing two of the contrasts implies the third: 0.124 + 0.138 = 0.262.

#### 2.4.3 Summary table of AMEs

Computing AMEs is often the next step after examining predictions with **`predict`**. AMEs quickly provide a general sense of the effects of each variable, much like regression coefficients in linear regression. After fitting your model, **`mchange`** with the default options provides a quick summary:

```
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

```
mchange, amount(one sd) decimals(2) brief
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                    |    Change    p-value 
    ----------------+---------------------
    k5              |                     
                 +1 |     -0.28       0.00 
                +SD |     -0.15       0.00 
    k618            |                     
                 +1 |     -0.01       0.34 
                +SD |     -0.02       0.34 
    agecat          |                     
     40-49 vs 30-39 |     -0.12       0.00 
       50+ vs 30-39 |     -0.26       0.00 
       50+ vs 40-49 |     -0.14       0.00 
    wc              |                     
      college vs no |      0.16       0.00 
    hc              |                     
      college vs no |      0.03       0.51 
    lwg             |                     
                 +1 |      0.12       0.00 
                +SD |      0.07       0.00 
    inc             |                     
                 +1 |     -0.01       0.00 
                +SD |     -0.09       0.00 

If you prefer marginal changes instead of discrete changes, you could use **`mchange, amount (marginal)`** instead. With marginal changes, you will probably need at least three decimal places. If you prefer discrete changes that are centered, use the **`centered`** option.

We find AMEs so much more useful than the estimated ft's or odds ratios that we wish the standard output from logit or probit would present AMEs along with estimated coefficients, perhaps as an option (like the **`or`** option to **`logit`** presents odds ratios instead of untransformed coefficients).

#### 2.4.4 Marginal effects for subgroups

We might be interested in comparing marginal effects for subgroups in our sample. For example, we might be interested in the effect of an additional child on labor force participation for women who have attended college. In this case, all the same arguments that we made earlier for why we broadly prefer AMEs to MEMs apply, only now we are applying these arguments to a subgroup of our sample instead of to the whole sample. As a result, we want the average change only over members of this subgroup. We can obtain this by using an if condition:

```
mchange k5 if wc == 1, amount(one)
```

    logit: Changes in Pr(y) | Number of obs = 212
    
    Expression: Pr(lfp), predict(pr)
    
                 |    Change    p-value 
    -------------+---------------------
    k5           |                     
              +1 |    -0.276      0.000 
    
    Average predictions
    
                 | not in LF      in LF 
    -------------+---------------------
      Pr(y|base) |     0.321      0.679 
    
    1: Sample selection: if wc == 1 & e(sample)==1

We can interpret this as follows:

For women who have attended college, an additional child decreases the probability of being in the labor force by an average of 0.28.

In section 6.5, after we learn more about the **`mtable`**, we show how to compute AMEs for different groups and how to test whether the marginal effects are equal across groups.

#### 2.4.5 MEM s and MERs

Although we tend to prefer AMEs as a summary measure of change, marginal effects are often computed at the mean or at other values. With the **`atmeans`** option, **`mchange`** computes the MEMs. We will use **`atmeans`** extensively in section 6.3, when we discuss generating predictions based upon the hypothetical observations we call ideal types. A hypothetical observation implies specific values for all values of the independent variables, each of which we can either specify directly with **`at()`** or specify by using **`atmeans`**. Talking about a hypothetical observation can be contrasted with the example of subgroup analysis immediately above, in which we were making statements about a group of observations and wanted to compute the average effect over that group.

Here we compute marginal effects by using **`atmeans`**, adding the **`statistics(ci)`** option to request the confidence intervals rather than the p-values.

```
mchange, statistics(ci) atmeans
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                    |    Change         LL         UL 
    ----------------+--------------------------------
    k5              |                                
                 +1 |    -0.324     -0.396     -0.252 
                +SD |    -0.180     -0.227     -0.133 
           Marginal |    -0.339     -0.431     -0.247 
    k618            |                                
                 +1 |    -0.016     -0.049      0.017 
                +SD |    -0.021     -0.065      0.022 
           Marginal |    -0.016     -0.049      0.017 
    agecat          |                                
     40-49 vs 30-39 |    -0.146     -0.238     -0.053 
       50+ vs 30-39 |    -0.307     -0.422     -0.191 
       50+ vs 40-49 |    -0.161     -0.265     -0.058 
    wc              |                                
      college vs no |     0.186      0.088      0.284 
    hc              |                                
      college vs no |     0.033     -0.065      0.131 
    lwg             |                                
                 +1 |     0.138      0.078      0.198 
                +SD |     0.084      0.045      0.123 
           Marginal |     0.149      0.077      0.221 
    inc             |                                
                 +1 |    -0.009     -0.013     -0.005 
                +SD |    -0.101     -0.148     -0.054 
           Marginal |    -0.009     -0.013     -0.005 
    
    Predictions at base value
    
                 | not in LF      in LF 
    -------------+---------------------
      Pr(y|base) |     0.422      0.578 
    
    Base values of regressors
    
                 |                               2.         3.         1.         1.                      
                 |        k5       k618     agecat     agecat         wc         hc        lwg        inc 
    -------------+---------------------------------------------------------------------------------------
              at |      .238       1.35       .385       .219       .282       .392        1.1       20.1 
    
    1: Estimates with margins option atmeans.


The values at which variables are being held constant are listed in the table **Base values of regressors**.

Here are examples of interpreting each type of effect:

* **Change of 1 at the mean.** For a woman who is average on all characteristics, an additional young child decreases the probability of being in the labor force by 0.32 (95% CI: [0.25, 0.40]).
* **Change of standard deviation at the mean.** A standard deviation increase in income, roughly $11,000$, decreases the probability of being in the labor force by 0.10 (95% CI: [0.05, 0.15]), holding other variables at their means.
* **Change from 0 to 1 at the mean.** If a woman attended college, her probability of being in the labor force is 0.19 greater than a woman who did not attend college, holding other variables at their means (95% CI: [0.09, 0.28]).
* **Change of categorical variables at the mean.** For an average woman, being 40 to 49 compared with being 30 to 39 decreases the probability of being in the labor force by 0.15 (95% CI: [0.05, 0.24]). Being 50 or older compared with being 30 to 39 decreases the probability by 0.31 (95% CI: [0.19, 0.42]). Being 50 or older compared with being 40 to 49 decreases the probability by 0.16 (95% CI: [0.06, 0.27]).

Marginal effects can be computed at other values by using the **`at()`** option. For example, to compute the marginal effect of **k5** for a hypothetical family in which the husband and wife both attended college, holding other variables at their means:

```
mchange k5, at(wc=1 hc=1) amount(1) atmeans
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                 |    Change    p-value 
    -------------+---------------------
    k5           |                     
              +1 |    -0.329      0.000 
    
    Predictions at base value
    
                 | not in LF      in LF 
    -------------+---------------------
      Pr(y|base) |     0.275      0.725 
    
    Base values of regressors
    
                 |                               2.         3.                                            
                 |        k5       k618     agecat     agecat         wc         hc        lwg        inc 
    -------------+---------------------------------------------------------------------------------------
              at |      .238       1.35       .385       .219          1          1        1.1       20.1 
    
    1: Estimates with margins option atmeans.

Notice that **hc** and **wc** are listed as 1 in the table of base values. We conclude the following:

>For an otherwise average family in which the husband and wife both attended college, an additional young child decreases the probability of being in the labor force by 0.33 (p < 0.001).

Or, we might want to estimate the effect of changing income from $0$ to $5,000$ for a family with two young children and in which neither parent went to college, holding other variables at their means. We set the values of the variables by using **`at(inc=0 k5=2 wc=0 hc=0)`** **`atmeans`**, and we indicate that we want a change of five by using **`delta(5)`**.

```
mchange inc, delta(5) amount(delta) at(inc=0 k5=2 wc=0 hc=0) atmeans
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                 |    Change    p-value 
    -------------+---------------------
    inc          |                     
          +delta |    -0.021      0.012 
    
    Predictions at base value
    
                 | not in LF      in LF 
    -------------+---------------------
      Pr(y|base) |     0.847      0.153 
    
    Base values of regressors
    
                 |                               2.         3.                                            
                 |        k5       k618     agecat     agecat         wc         hc        lwg        inc 
    -------------+---------------------------------------------------------------------------------------
              at |         2       1.35       .385       .219          0          0        1.1          0 
    
    1: Estimates with margins option atmeans.
    2: Delta equals 5.

We conclude the following:

>For an otherwise average family with two young children and parents who did not attend college, increasing income from $0$ to $5,000$ is expected to decrease the probability of labor force participation by 0.02, which is significant at the 0.05 level but not at the 0.01 level.

If you prefer discrete changes that are **centered around the mean**, you can add the **`centered`** option to **`mchange`**:

```
mchange inc lwg,atmeans centered
```

    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                 |    Change    p-value 
    -------------+---------------------
    inc          |                     
     +1 centered |    -0.009      0.000 
    +SD centered |    -0.099      0.000 
        Marginal |    -0.009      0.000 
    lwg          |                     
     +1 centered |     0.148      0.000 
    +SD centered |     0.087      0.000 
        Marginal |     0.149      0.000 
    
    Predictions at base value
    
                 | not in LF      in LF 
    -------------+---------------------
      Pr(y|base) |     0.422      0.578 
    
    Base values of regressors
    
                 |                               2.         3.         1.         1.                      
                 |        k5       k618     agecat     agecat         wc         hc        lwg        inc 
    -------------+---------------------------------------------------------------------------------------
              at |      .238       1.35       .385       .219       .282       .392        1.1       20.1 
    
    1: Estimates with margins option atmeans.

This can be interpreted as follows:

For an average family, a standard deviation change in income, roughly $12,000$, centered around the mean is expected to decrease the probability of labor force participation by 0.10 (p < 0.001).

Because the change is centered, we are computing the effect of changing income from 1/2 standard deviation below the mean of $20,100$ to 1/2 standard deviation above the mean, that is, a change from roughly $14,300$ to $25,900$. In this example, the centered change is nearly identical to the uncentered change with a value of -0.101. Centered and uncentered changes are similar when the change in the independent variable occurs in a region where the probability curve is approximately linear. When the probability curve is changing shape over the region of change, centered and uncentered changes can differ noticeably.

#### 2.4.6 Marginal effects with powers and interactions

As discussed in section 6.2.1, **when computing marginal effects for a variable that is linked with other variables, you must ensure that all linked variables change appropriately. You cannot simply change one of the linked variables and assume you can hold the others constant.** Fortunately, this is taken care of automatically when linked variables are specified to your model with factor-variable notation.


To demonstrate how linked variables are properly handled by **`mchange`**, we compute MEMs for a standard deviation increase in **inc** and **lwg**. **Although we are using MEMs because they make the levels of other variables explicit, which is didactically useful, things work the same way with AMEs.** We include income and income-squared in the model by including **c.inc** **c.inc#c.inc**in the command (equivalently, we could have used **c.inc#c.inc**).

```
logit lfp c.inc c.inc#c.inc lwg k5 k618 i.agecat i.wc i.hc , nolog
```
    
    Logistic regression                                     Number of obs =    753
                                                            LR chi2(9)    = 128.00
                                                            Prob > chi2   = 0.0000
    Log likelihood = -450.87545                             Pseudo R2     = 0.1243
    
    ------------------------------------------------------------------------------
             lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             inc |      -0.07       0.02    -3.54   0.000        -0.11       -0.03
                 |
     c.inc#c.inc |       0.00       0.00     1.99   0.046         0.00        0.00
                 |
             lwg |       0.62       0.15     4.10   0.000         0.32        0.92
              k5 |      -1.41       0.19    -7.32   0.000        -1.79       -1.03
            k618 |      -0.06       0.07    -0.92   0.360        -0.20        0.07
                 |
          agecat |
          40-49  |      -0.60       0.21    -2.88   0.004        -1.01       -0.19
            50+  |      -1.26       0.26    -4.84   0.000        -1.77       -0.75
                 |
              wc |
        college  |       0.80       0.23     3.48   0.000         0.35        1.25
                 |
              hc |
        college  |       0.20       0.21     0.96   0.336        -0.21        0.61
           _cons |       1.38       0.35     3.99   0.000         0.70        2.06
    ------------------------------------------------------------------------------


Estimates of the coefficient for income and income-squared, labeled **c.inc#c.inc**, are shown. Using **`mchange`**, we compute MEMs for **inc** and **lwg**:

```
mchange inc lwg, atmeans amount(sd)
```
    
    logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                 |    Change    p-value 
    -------------+---------------------
    inc          |                     
             +SD |    -0.123      0.000 
    lwg          |                     
             +SD |     0.087      0.000 
    
    Predictions at base value
    
                 | not in LF      in LF 
    -------------+---------------------
      Pr(y|base) |     0.438      0.562 
    
    Base values of regressors
    
                 |                                                     2.         3.         1.         1.
                 |       inc        lwg         k5       k618     agecat     agecat         wc         hc 
    -------------+---------------------------------------------------------------------------------------
              at |      20.1        1.1       .238       1.35       .385       .219       .282       .392 
    
    1: Estimates with margins option atmeans.

The base values include the mean of inc but do not mention c.inc#c.inc. This is because the mean of the variable income-squared is not computed, but instead, the mean of income is squared. This is what we want. If we had added income-squared to our model by creating a new variable (for example, gen incsq = inc*inc) instead of using c.inc#c.inc, the **`atmeans`** option would have incorrectly held incsq at its mean. Again, one of the great advantages of using factor-variable notation is that Stata will automatically handle this correctly.

The discrete change for income in the model is -0.123. We can interpret the discrete change just as we would in a model that did not include income-squared:

>For someone who is average on all characteristics, an increase of one standard deviation in family income is predicted to decrease the probability of being in the labor force by 0.12.

**Although we do not provide an example here with interaction terms, the same principle applies. If factor-variable notation is used, margins and our m* commands will handle the computations of marginal and discrete changes correctly. If you do not use factor-variable notation, you can still get `margins` to produce the right answers, but it requires you to do work that Stata can handle automatically.**


### 2.5 The distribution of marginal effects
The value of a marginal effect depends on the level of all variables in the model. Because each observation can have different values of the independent variables, there is a distribution of marginal effects within the sample where the AME is the mean of this distribution. **Although the mean tells you where the center of the distribution is, it does not reflect variation within the distribution. Just as the means of the independent variables used to compute the MEM might not correspond even approximately to anyone in the sample, the AME might not correspond to the magnitude of the marginal effect for anyone in the sample.** For this reason, we believe that examining the distribution of marginal effects provides valuable substantive insights.

We consider two approaches for learning about the distribution of marginal effects. First, we compute effects for each observation and create a histogram of the effects. Although there is no Stata command for this, we provide simple programs that you can adapt to your needs. Second, we compute marginal effects at strategic locations in the data space by using MERs. This approach is presented in section 6.3.

>A third approach that we do not consider here estimates the quantiles of the effects in the population; see Firpo (2007) and Cattaneo (2010) for seminal papers, and see Cattaneo, Drukker, and Holland (2013) and Drukker (2014) for intuition, Stata commands, and extensions to survival data. For example, a training program that boosts the income of low-income participants and has no effect on higher-income participants could have the 0.25 quantile effect be significant and the 0.75 quantile effect be insignificant. These quantiles of effects provide the researcher with a more nuanced picture of the effect of a treatment than the one provided by the mean effect.

The marginal change for the BRM, assuming no interactions or power terms, equals:

$$\frac{\partial\Pr\left(y_i=1\mid\mathbf{x}_i\right)}{\partial x_k}=f\left(\mathbf{x}_i\boldsymbol{\beta}\right)\beta_k$$

The shape of the distribution of marginal changes for each observation is determined by $ f(x_i\beta) $, where $ \beta_k $ simply rescales $ f(x_i\beta) $ to create the distribution of effects for $ x_k $. The distribution is more spread out if $ \beta_k $ is larger in absolute value and is more condensed if $ \beta_k $ is smaller. Although the shape of the distribution of discrete changes will be similar for different variables, they are not a simple rescaling of each other.

当我们谈论计算每个观察值的边际变化时，我们想知道当某个自变量微小变化时，因变量的变化量是多少。在 logistic 回归模型中，我们可以使用一个简单的公式来计算这个边际变化。

1. 首先，我们有一个 logistic 回归模型，其预测某个事件发生的概率。假设我们有一个自变量 $ x_i $，我们想要知道当 $ x_i $ 发生微小变化时，事件发生的概率会如何变化。

2. 公式中的 $ f(x_i\boldsymbol{\beta}) $ 是 logistic 函数，它描述了自变量 $ x_i $ 和事件发生的概率之间的关系。这个函数会给出一个在 0 到 1 之间的值，表示事件发生的概率。

3. $ (1 - f(x_i\boldsymbol{\beta})) $ 则表示事件不发生的概率，因为 logistic 函数的性质保证了事件发生和不发生的概率之和为 1。

4. 因此，$ f(x_i\boldsymbol{\beta}) \cdot (1 - f(x_i\boldsymbol{\beta})) $ 就是事件发生和不发生的联合概率密度函数，也可以看作是事件发生的概率密度函数（Probability Density Function, PDF）。

5. 当我们计算出每个观察值的 $ \text{Pr}(y_i = 1 | x_i) $（即事件发生的概率）后，我们可以将其代入上述公式中，得到对应的边际变化。这个边际变化告诉我们，当 $ x_i $ 发生微小变化时，事件发生的概率会如何变化。

举个例子来说明这个过程：假设我们有一个 logistic 回归模型，想要预测一个学生是否通过了考试，而自变量包括学习时间 $ x_1 $ 和学习资料数量 $ x_2 $。我们可以使用上述公式来计算每个学生通过考试的概率，并进一步分析当学习时间或学习资料数量微小变化时，通过考试的概率会如何变化。这样，我们就可以评估这些因素对考试结果的影响程度。

There are several ways to compute the marginal changes for each observation. For the logit model, the simplest approach is to use the formula


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.9.png" style="width:380px;height:70px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


where $ \text{Pr}(y_i = 1 | x_i)  1 - \text{Pr}(y_i = 1 | x_i) $ is the PDF for the logistic distribution. After predict computes $ \text{Pr}(y_i = 1 | x_i) $ for each observation, it is easy to create a variable containing the marginal effects:

给定一个具有多个自变量 $ x_1, x_2, \ldots, x_n $ 的模型，我们想要计算当某个自变量 $ x_k $ 微小变化时，因变量 $ y $ 的变化量。这个变化量称为边际变化（marginal change）。

边际变化可以使用以下公式计算：

$$ \frac{\partial\Pr(y_i=1|\mathbf{x}_i)}{\partial x_k} = f(\mathbf{x}_i\boldsymbol{\beta}) \cdot \beta_k $$

这里的 $ \frac{\partial\Pr(y_i=1|\mathbf{x}_i)}{\partial x_k} $ 表示因变量为 $ y_i=1 $ 的条件概率对 $ x_k $ 的偏导数。$ f(\mathbf{x}_i\boldsymbol{\beta}) $ 是一个函数，描述了自变量和因变量之间的关系。$ \boldsymbol{\beta} $ 是模型的参数向量，而 $ \beta_k $ 是其中的一个参数，它简单地调整了 $ f(\mathbf{x}_i\boldsymbol{\beta}) $ 的尺度，以创建 $ x_k $ 的效应分布。

现在，让我们来用一个具体的例子来说明：

假设我们有一个 logistic 回归模型，想要预测一个学生是否通过了考试，而自变量包括学习时间 $ x_1 $ 和学习资料数量 $ x_2 $。

我们的模型可以表示为：

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.12.png" style="width:380px;height:100px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


其中，$ \beta_0, \beta_1, $ 和 $ \beta_2 $ 是模型的参数。

假设我们想要计算学习时间 $ x_1 $ 对通过考试概率的边际效应。我们可以使用上面的公式：

$$\frac{\partial\Pr(y_i=1|\mathbf{x}_i)}{\partial x_1}=f(\mathbf{x}_i\boldsymbol{\beta})\cdot\beta_1$$
在这个例子中，$ f(\mathbf{x}_i\boldsymbol{\beta}) $ 是 logistic 函数，$ \beta_1 $ 是学习时间的参数。

如果我们想要比较不同学生之间学习时间对通过考试概率的影响，我们可以调整 $ \beta_1 $ 的值。如果 $ \beta_1 $ 的绝对值较大，那么学习时间对通过考试概率的影响就更为显著。







```
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
predict double prhat if e(sample)
gen double mcinc = prhat * (1-prhat) * _b[inc]
label var mcinc "Marginal change of inc on Pr(LFP)"
```

where **_b[inc]** is the estimated regression coefficient for inc. For a probit model, we compute the PDF by using the **`normalden()`** function:

```
probit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
predict double pbtxb, xb
label var pbtxb "xb"
gen double pbtpdf = normalden(pbtxb)
label var pbtpdf "normal pdf at xb"
gen double pbtmcinc = pbtpdf * _b[inc]
label var pbtmcinc "Marginal change of inc from probit"
```

Next, we plot the distribution of marginal changes with the **`histogram`** command. To annotate the graph with the values of the AME, we use local macros to hold estimates from **`mchange`**. After running **`mchange`** in Stata, the AME is saved in the third row and first column of the return matrix r(table). To see this, you can enter the command **`matlist r(table)`** after **`mchange`**. We place the estimate in the local macro named **ame** by using the **`el()`** function, which retrieves the value of a single element from a matrix. We do the same thing for the MEM.

```
pwcorr pbtmcinc mcinc

quietly mchange inc
local ame = el(r(table),3,1)
quietly mchange inc, atmeans
local mem = el(r(table),3,1)
```

                 | pbtmcinc    mcinc
    -------------+------------------
        pbtmcinc |   1.0000 
           mcinc |   0.9966   1.0000 

The histogram of marginal changes is created with the following command, using **`text()`** to add the AME and MEM:

```
histogram mcinc, xlab(-.008(.002)0) ylab(0(.1).4,grid) ///
    fraction bin(25) col(gs10) fcol(gs12) ///
    text(.015 `ame' "AME", place(center)) ///
    text(.000 `ame' "|", place(center)) ///
    text(.015 `mem' "MEM", place(center)) ///
    text(.000 `mem' "|", place(center))
graph export rm3ch6-marginal-dist-inc.emf,  replace

```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.13.png" style="width:680px;height:470px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The distribution of marginal changes for income is highly skewed, ranging from -0.10 to less than -0.005. Because of the skew, the MEM is a better indicator of what we would expect for most respondents than is the AME. The graph shows that over 30% of the sample have effects similar to that of an "average" person. On the other hand, 37% of the sample have effects that are smaller (to the right in the histogram) than the AME.

To compute the distribution of discrete changes for wc, we use **counterfactual predictions**. **The same approach could also be used to compute discrete changes for a continuous variable.** Before proceeding, a word of caution: This approach to computing effects involves changing the original data that were used to fit the model. It is essential that you do not save the changed data.


* Step 1: The original variable wc is copied to variable wc.orig so that wc can be changed.
* Step 2: All cases are assigned wc=0 to create the counterfactual condition that no women went to college
* Step 3: Predicted probabilities are computed assuming that no women went to college
* Step 4: All cases are assigned wc=1 to create the counterfactual condition that all women went to college.
* Step 5: Predicted probabilities are computed assuming that all women went to college
* Step 6: The variable wc is restored to the original values that were saved in wc_orig in Step 1.
* Step 7: The difference between the predictions from Step 5 where wc=1 and Step 3 where wc=0 is the discrete change for each case; the average of the differences is the average discrete change.

```
gen wc_orig = wc // step 1
replace wc = 0 // step 2
predict double prhat_wc0 // step 3
replace wc = 1 // step 4
predict double prhat_wc1 // step 5
replace wc = wc_orig // step 6
gen double dc_wc = prhat_wc1 - prhat_wc0 // step 7
label var dc_wc "Discrete change of wc on Pr(LFP)"
```

The command to draw the histogram is nearly identical to that above, so we do not repeat it. The following graph for the discrete change for **wc** is produced:

```
* get AME and MEM
quietly mchange wc
local ame = el(r(table),1,1)
quietly mchange wc, atmeans
local mem = el(r(table),1,1)
```

```
* histogram
histogram dc_wc, xlab(0(.05).20) ylab(0(.1).3,grid) ///
    fraction bin(25) col(gs10) fcol(gs12) ///
    text(.015 `ame' "AME", place(center)) ///
    text(.000 `ame' "|", place(center)) ///
    text(.015 `mem' "MEM", place(center)) ///
    text(.000 `mem' "|", place(center))
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.14.png" style="width:680px;height:470px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

After plotting the distribution of effects, a useful next step is to determine the marginal effects for ideal types that represent distinct characteristics in the sample. This topic is considered in section 6.3.


### 2.6 (Advanced) Algorithm for computing the distribution of effects

>In this section, we use margins and slightly more advanced programming techniques to create a general algorithm for plotting the distribution of effects. **Although the programming is more complicated, the code works with any model that is compatible with margins, even if your model includes interactions and product terms.** We suggest you read this section after you have mastered other materials in this chapter.

Instead of using **`generate`** to compute marginal effects based on the formula for a specific model, this algorithm uses **`margins`** to compute the effect for each observation. Although this is computationally slow, it works very generally for creating a histogram of any marginal effect that can be computed by **`margins`** or by predictions made by **`margins`**. We begin with a review of using **`margins`** to compute marginal effects (see section 4.5 for related information)

#### 2.6.1 Using margins to compute marginal effects
The option **`dydx (varname)`** tells **`margins`** to compute marginal effects. If **varname** is a factor variable, such as **i.wc** in our example, **`margins`** computes the discrete change as **varname** changes from 0 to 1. If **varname** is not a factor variable, **`margins`** computes the marginal change (that is, partial derivative) for **varname**.

We begin by fitting the model and storing the results. We must store them because we will use the **`post`** option with **`margins`**, which replaces the regression estimates in memory with the results from **`margins`**.

```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store mymodel
```

Next, we compute the marginal change with **`margins,dydx (inc)`**, leaving in the return **`r(b)`**.

```
margins, dydx(wc)
matlist r(b)
```

    Average marginal effects                                   Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    dy/dx wrt:  1.wc
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |      dy/dx   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              wc |
        college  |       0.16       0.04     3.69   0.000         0.08        0.25
    ------------------------------------------------------------------------------
    Note: dy/dx for factor levels is the discrete change from the base level.

**We can also use **`margins`** to compute discrete changes for continuous variables, but this takes two steps. First, we make two predictions and post the results. Second, we use **`lincom`** or **`mlincom`** to compute the discrete change.** For example, suppose that we want to compute the change in the probability of labor force participation as the number of young children increases from 0 to 3. We compute predictions with two **atspecs**, one for **k5=0** and the other for **k5=3**:

    
                 |        0b.         1.
                 |        wc         wc 
    -------------+---------------------
              y1 |         0   .1624037 



```
margins, at(k5=0) at(k5=3) post
```

    Predictive margins                                         Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    1._at: k5 = 0
    2._at: k5 = 3
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             _at |
              1  |       0.64       0.02    34.97   0.000         0.60        0.67
              2  |       0.04       0.02     2.09   0.036         0.00        0.07
    ------------------------------------------------------------------------------

Because we used the **`post`** option, the predictions are saved to **`e(b)`**, which allows us to use **`mlincom`** (or **`lincom`**) to compute the average discrete change:

```
mlincom 2 - 1
```


                 |   lincom    pvalue        ll        ul 
    -------------+---------------------------------------
               1 |   -0.599     0.000    -0.656    -0.541 

The linear combination in the column **`lincom`** is returned to **`r(est)`**.

We can also compute a change of a fixed amount from the observed values, for example, the average change as inc increases by 1 from its observed values. To do this, we will want to use a single **`margins`** command to produce two different predictions: one in which predictions are computed at the observed values and one in which inc is increased by 1. To get predictions at the observed values, we simply want to specify **`at()`** with an empty **`atspec`**. To get predictions in which inc is increased by 1, we must use the **`gen()`** option, added in Stata 13, when specifying the **`atspec`**. The specification **`at(inc = gen(in c + 1))`** tells **`margins`** to increase **inc** by 1 from its observed values before computing the prediction. We use these two **`atspecs`** after we restore the logit results from our base model.

```
estimates restore mymodel
margins, at() at(inc=gen(inc+1)) post
```

    Predictive margins                                         Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    1._at: (asobserved)        
    2._at: inc = inc+1
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             _at |
              1  |       0.57       0.02    34.24   0.000         0.54        0.60
              2  |       0.56       0.02    33.51   0.000         0.53        0.59
    ------------------------------------------------------------------------------

Because we used the **`post`** option, we can use **`mlincom`** to compute the change:

```
mlincom 2 - 1
```

                 |   lincom    pvalue        ll        ul 
    -------------+---------------------------------------
               1 |   -0.007     0.000    -0.011    -0.004 

The results are identical to those produced by **`mchange`** in Stata, with the command **`mchange inc, amount(one)`**.

#### 2.6.2 Algorithm for computing the distribution of marginal effects
In this section, we explain how to write a do-file that computes marginal effects for each observation in the estimation sample. The effects are saved in a variable that is plotted. We begin with an overview of the steps involved.

**Prepare to compute effects:**

* Step 1: Fit the regression model.
* Step 2: Create the temporary variable **`insample`** from **`e(sample)`**, indicating which cases are in the estimation sample. (Temporary variables are variables that will automatically be erased when your do-file ends. For more information, type **`help tempvar`** or see [p] macro.)
* Step 3: Create the temporary variable **`effect`** to hold marginal effects. This variable is graphed with **`histogram`** to show the distribution of marginal effects.

**Loop through observations and compute effects:**

* Step 4: Determine whether a case is in the estimation sample by using the temporary variable **`insample`**.
* Step 5: Use **`margins`** to compute the marginal effect for the current case. Any of the methods of computing effects from the prior section can be used.
* Step 6: Save the effect for the current case in the corresponding row of the variable **`effect`**.

**Verify results and plot effects:**

* Step 7: Compute the mean of **`effect`** and compare it with the AME computed by **`margins`**. If these are not the same, there is an error in your program.
* Step 8: Plot the distribution of effects by creating a histogram of **`effect`**.
* Step 9: If you want to save the effects for each observation, generate a variable equal to the temporary variable **`effect`**.

Using these steps, we compute the marginal effects for **wc**.

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
// step 2: create a variable containing e(sample)
tempvar insample
gen `insample' = e(sample)
label var `insample' "In estimation sample?"

// step 3: create a variable to hold effects
tempvar effect
gen `effect' = .
label var `effect' "Marginal effect for each observation"
```

**loop through all observations**

```
local nobs = _N
forvalues i = 1/`nobs' {
    if `insample'[`i']==1 { // step 4: only cases in estimation sample

        // step 5: use margins to compute effect for current case
        qui margins in `i', dydx(wc) nose

        // step 6: save marginal effect in variable
        qui replace `effect' = el(r(b),1,2) in `i'
    }
}
```

当程序运行到循环部分时，它会逐个检查每个观测。让我们逐行解释循环的部分：

1. **设定循环次数**：**`local nobs = _N`** 这一行将数据集中的观测总数存储在本地宏（local macro）**`nobs`** 中。这个宏在后面的循环中用来确定要循环多少次。

2. **开始循环**：**`forvalues i = 1/nobs {`** ：这一行启动了一个循环，从第一个观测到最后一个观测。循环中的`i`是一个循环变量，它会从1开始递增直到数据集中的总观测数。

3. **检查观测是否在估计样本中**：**``if `insample' [`i']==1 {``**：这行代码检查当前循环中的观测是否在估计样本中。`insample`变量是在前面创建的，它指示了每个观测是否在估计样本中。如果当前观测在估计样本中，循环继续执行下面的代码；否则，跳过这个循环步骤，继续下一个观测。

4. **计算边际效应**：**``qui margins in `i', dydx(wc) nose``**：这行代码使用 **`margins`** 命令计算当前观测的边际效应。**``in `i'``** 选项指示 **`margins`** 只对当前循环中的观测进行计算。**`dydx(wc)`** 选项告诉 **`margins`** 计算 **wc** 变量的边际效应。**`nose`** 选项阻止 **`margins`** 显示结果。

5. **保存边际效应**：**``qui replace `effect' = el(r(b),1,2) in `i'``**：这行代码将 **`margins`** 命令计算的边际效应保存到名为 **`effect`** 的临时变量中。**`el(r(b),1,2)`** 是一个命令，它从 **`margins`** 的结果中提取出边际效应的值。

总体来说，这个循环遍历了数据集中的每个观测，并对在估计样本中的每个观测计算了 **`wc`** 变量的边际效应，并将这些效应保存到一个临时变量中。

```
// step 7: compare average of effect variable to AME from margins
sum `effect'
margins, dydx(wc)
```

    
        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
        __000001 |        753    .1624037    .0344572   .0074083   .1968259
    
    Average marginal effects                                   Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    dy/dx wrt:  1.wc
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |      dy/dx   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              wc |
        college  |       0.16       0.04     3.69   0.000         0.08        0.25
    ------------------------------------------------------------------------------
    Note: dy/dx for factor levels is the discrete change from the base level.

In this example, **`margins`** computed the marginal effect by using **`dydx()`**. The code can be modified so that **`margins`** computes two predictions (for example, with **wc=0** and with **wc=1**) and the discrete change can be computed with **`mlincom`** or **`lincom`**. For models with multiple outcomes, such as **`mlogit`** or **`oprobit`**, the option **`predict(outcome())`** can be used. Although this algorithm is very general, it is also slow because **`margins`** is run once for each observation; every time **`margins`** is run, it computes effects for every observation.

## 3 Ideal types

An ideal type is a hypothetical observation with substantively illustrative values. A table of probabilities for ideal types of people, countries, cows, or whatever you are studying can quickly summarize the effects of key variables. In our example of labor force participation, we want to examine four ideal types of respondents:

* A young family with lower income, no college education, and young children.
* A young family with college education and young children.
* A middle-aged family with college education and teenage children.
* An older family with college education and adult children.

We find ideal types to be particularly illustrative for interpretation when independent variables are substantially correlated. In the above example, we first consider the contrast between lower income and no college education and higher income and college education, because these indicators of SES covary strongly enough that it is easy to envision them as low- and high-SES prototypes. Across the latter three examples, we construct ideal types reflecting that the age of parents and their children change together.

**We use **``mtable``** to estimate the probabilities for each of these ideal types.** To introduce the command and explain some options, we begin with an example that combines two sets of predictions. (See section 4.4 for an introduction to mtable.) We then illustrate two approaches for creating a table of ideal types.

For our first ideal type, we define a young, lower-class family as having the values specified as at (agecat=1 k5=2 k618=0 inc=10 lwg=.75 hc=0 wc=0). lwg equals the log of the federal minimum wage for 1975, the year the data were collected. **We use **``mtable``** to make predictions, using the **``rowname()``** option to label the results. The option **``ci``**, a synonym for statistics (**``ci``**), requests confidence intervals along with the predicted probability.** Because this is the first step in constructing a table of predictions, we use the **``clear``** option to remove from memory any prior predictions saved by **``mtable``**.

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
mtable, rowname(1 Young low SES young kids) ci clear ///
    at(agecat=1 k5=2 k618=0 inc=10 lwg=.75 hc=0 wc=0)
```

    Expression: Pr(lfp), predict()
    
                                 |    Pr(y)        ll        ul
     ----------------------------+-----------------------------
      1 Young low SES young kids |    0.159     0.068     0.251
    
    Specified values of covariates
    
               |       k5      k618    agecat        wc        hc       lwg       inc
     ----------+---------------------------------------------------------------------
       Current |        2         0         1         0         0       .75        10

We conclude the following:

>For a young, lower SES family with two young children, the estimated probability of being in the labor force is 0.16 with a 95% confidence interval from 0.07 to 0.25.

For our next ideal type, we define a young, college-educated family with young children by using **``at(agecat=1 k5=2 k618=0 wc=1 hc=1)``**, which specifies the values for all the independent variables except lwg and inc. Because we used the **``atmeans``** option, these variables are set to the means in the estimation sample. **To place the new prediction below the prediction from the last **``mtable``** command, we use the **``below``** option.**

Below the table of predictions is a table showing the levels of the covariates when the predictions were made. **Although you can suppress its display with the **``brief``** option, we find it useful for knowing exactly how the ideal types were defined. Set 1 refers to the first predictions in the table, which we numbered as 1. The row Current contains values of the **``at()``** variables from the current or most recent **``mtable``** command.**

We add two more ideal types that show what happens to the probability of being in the labor force as women and children get older. We use **``quietly``** to suppress output until the last **``mtable``** command, which displays the complete table.

```
quietly mtable, rowname(3 Midage college with teens) ci below ///
    at(agecat==2 k5==0 k618==2 wc==1 hc==1) atmeans
mtable, rowname(4 Older college with adult kids) ci below ///
    at(agecat==3 k5==0 k618==0 wc==1 hc==1) atmeans
```

    Expression: Pr(lfp), predict()
    
                                 |    Pr(y)        ll        ul
     ----------------------------+-----------------------------
      1 Young low SES young kids |    0.159     0.068     0.251
      2 Young college young kids |    0.295     0.156     0.433
      3 Midage college with teen |    0.760     0.680     0.840
      4 Older college with adult |    0.653     0.548     0.758
    
    Specified values of covariates
    
               |       k5      k618    agecat        wc        hc       lwg       inc
     ----------+---------------------------------------------------------------------
         Set 1 |        2         0         1         0         0       .75        10
         Set 2 |        2         0         1         1         1       1.1      20.1
         Set 3 |        0         2         2         1         1       1.1      20.1
       Current |        0         0         3         1         1       1.1      20.1
    
The first two rows allow us to see the big difference in the labor force between the lower and higher SES families that have young children. The mother from the higher SES family has about twice the probability of being in the labor force. The probability for the higher SES mother increases dramatically as her children are no longer young: the chance of labor force participation goes from about 30% to 76%.

**It is important to emphasize that predictions we make about ideal types are predictions about a hypothetical observation, not predictions about a subgroup. When we use ideal types, we will specify a particular value for each of the independent variables, either directly or by using **``atmeans``** to compute global or, as we will show next, local means.** This keeps our understanding of what is meant by an ideal type simpler and the interpretations that use them clearer. What we want to avoid in particular is defining an ideal type by specifying values for some independent variables, but then computing average predictions over a set of observations with **``asobserved``**. That mixes together the concepts of MERs and AMEs and makes interpreting results very confusing. Again, **you can think of an ideal type as a hypothetical observation with one prediction and think of a subgroup as a set of observations with a distribution of predictions that may be averaged or plotted.**

### 3.1 Using local means with ideal types

The last three rows of the table were constructed using the **``atmeans``** option to specify the values of **``inc``** and **``lwg``** to the sample means. We refer to means based on the entire estimation sample as global means. **Although using global means for each ideal type is simple, it often is not realistic. For example, it is reasonable to assume that levels of income and wages would be higher for college-educated respondents than for those who have not attended college and that they would change with age, which is not reflected in the global means.**

**To address this problem, we can use local means that are defined based on the characteristics specified in the **``at()``** statements.** To do this, we create a selection variable that equals 1 if an observation is part of the group defined by the conditions of an **``atspec``** and equals 0 otherwise. In other words, **a selection variable indicates whether an observation is part of the group defined for the ideal type.** To create these variables, we use the **``generate``** command with **``if``** conditions that correspond to the **``atspecs``** used for an ideal type:

```
capture drop _sel*
gen _selYC = agecat==1 & k5==2 & k618==0 & wc==1 & hc==1
label var _selYC "Select Young college young kids"
gen _selMC = agecat==2 & k5==0 & k618==2 & wc==1 & hc==1
label var _selMC "Select Midage college with teens"
gen _selOC = agecat==3 & k5==0 & k618==0 & wc==1 & hc==1
label var _selOC "Select Older college with adult kids"
```

Once these variables are created, we can make a table of predictions containing local means for variables not explicitly set by the **``atspec``**. The first row of the table is unchanged from before because all variables for that ideal type were explicitly specified in the **``at()``** option:

```
quietly mtable, rowname(1 Young low SES young kids) ci clear ///
                at(agecat=1 k5=2 k618=0 inc=10 lwg=.75 hc=0 wc=0)
```

In the next command, we add **``if _selYC==1``** to the **``mtable``** command so that predictions are based only on observations defined by **``_selYC``**:

```
quietly mtable if _selYC==1, rowname(2 Young college young kids) ///
               atmeans ci below
```

The **``if``** condition selects observations where **``agecat==1``** & **``k5==2``** & **``k618==0``** & **``wc==1``** & **``hc==1``**, which define our ideal type. The means of these variables will equal their specified values (for example, **``agecat``** will equal 1 and **``k5``** will equal 2), while those variables not used to define the selection variable will equal the local mean defined by selection variables. For example, **``lwg``** will equal the average log of wages for young families with college education. Accordingly, the **``if``** condition makes it easy to specify the values we wanted to define our ideal type. In the same way, we add the last two ideal types to the table:


```
quietly mtable if _selMC==1, rowname(3 Midage college with teens) ///
        atmeans ci below
mtable if _selOC==1, rowname(4 Older college with adult kids) ///
       atmeans ci below
```

    Expression: Pr(lfp), predict()
    
                                 |    Pr(y)        ll        ul
     ----------------------------+-----------------------------
      1 Young low SES young kids |    0.159     0.068     0.251
      2 Young college young kids |    0.394     0.234     0.554
      3 Midage college with teen |    0.739     0.659     0.820
      4 Older college with adult |    0.631     0.528     0.734
    
    Specified values of covariates
    
               |       k5      k618    agecat        wc        hc       lwg       inc
     ----------+---------------------------------------------------------------------
         Set 1 |        2         0         1         0         0       .75        10
         Set 2 |        2         0         1         1         1      1.62      16.6
         Set 3 |        0         2         2         1         1      1.16      24.4
       Current |        0         0         3         1         1      1.38      27.9

An advantage of using local means with ideal types is that the values of variables not specified in the type are held to values more consistent with what is actually observed, so the ideal type more accurately resembles the actual cases in our dataset that share the key features of the ideal type.

### 3.2 Comparing ideal types with statistical tests

The predicted probabilities of labor force participation vary among the four ideal types. Before concluding, for example, that the probability of being in the labor force is greater for a young, college-educated family with children than for a family with no college education, we need to test whether the predictions are significantly different. Essentially, this involves testing whether a discrete change is 0 when the starting values and ending values vary on multiple variables. **To show how this is done, we compute two ideal types in the same **``mtable``** command and post the results so that we can evaluate them with **``mlincom``**. Because we are posting the results, we begin with **``estimates store``** so that we can later restore the estimation results from **``logit``** after they have been replaced by the posted predictions.**

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store base
mtable, atmeans post ///
    at(agecat=1 k5=2 k618=0 wc=0 hc=0 lwg=.75 inc=10) /// ideal type 1
    at(agecat=1 k5=2 k618=0 wc=1 hc=1 lwg=1.62 inc=16.64) // ideal type 2

```
    Expression: Pr(lfp), predict()
    
               |       wc        hc       lwg       inc     Pr(y)
     ----------+-------------------------------------------------
             1 |        0         0       .75        10     0.159
             2 |        1         1      1.62      16.6     0.394
    
    Specified values of covariates
    
               |       k5      k618    agecat
     ----------+-----------------------------
       Current |        2         0         1

Now, we estimate the difference in the predictions and end by restoring the estimation results from **``logit``**:

```
mlincom 1 - 2
```

                 |   lincom    pvalue        ll        ul 
    -------------+---------------------------------------
               1 |   -0.234     0.000    -0.340    -0.129 
    
We conclude the following:
>A wife from a young, lower SES family with young children is significantly less likely to be in the labor force than a wife from a young family with college education (p < 0.001).

### 3.3 (Advanced) Using macros to test differences between ideal types

>In this section, we discuss using local macros and returns to automate the process of computing predictions at multiple fixed values of the **``at()``** variables. If you rarely test the equality of predictions, the methods from the last section should meet your needs. If you often test the equality of predictions, this section can save you time.

It is tedious and error-prone to specify the **``atspecs``** for multiple ideal types to test the equality of predictions. To automate this process, we can use the returned results from **``mtable``**. When **``mtable``** is run with a single **``at()``**, it returns the local **``r(atspec)``** as a string that contains the specified values of the covariates. This is easiest to understand with an example:

```
mtable, atmeans at(agecat=1 k5=2 k618=0 wc=0 hc=0 lwg=.75 inc=10)
```

    Expression: Pr(lfp), predict()
    
       Pr(y)
    --------
       0.159
    
    Specified values of covariates
    
               |       k5      k618    agecat        wc        hc       lwg       inc
     ----------+---------------------------------------------------------------------
       Current |        2         0         1         0         0       .75        10

The values shown in the **Specified values of covariates** table are saved in the return **``r(atspec)``**:

```
display "`r(atspec)'"
```

    . display "`r(atspec)'"
    k5=2 k618=0 1b.agecat=1 2.agecat=0 3.agecat=0 0b.wc=1 1.wc=0 0b.hc=1 1.hc=0 lwg=.75 inc=10 

We create a local macro that is used to specify the atspec for **`mtable`**:

```
local myatspec `r(atspec)'
mtable, atmeans at(`myatspec')
```

    Expression: Pr(lfp), predict()
    
       Pr(y)
    --------
       0.159
    
    Specified values of covariates
    
               |       k5      k618    agecat        wc        hc       lwg       inc
     ----------+---------------------------------------------------------------------
       Current |        2         0         1         0         0       .75        10

The results match those we obtained earlier.

Using this strategy and the selection variables created before (see page 273), we create local macros with the **``atspecs``** for our four ideal types:

```
quietly mtable, atmeans at(agecat=1 k5=2 k618=0 inc=10 lwg=.75 hc=0 wc=0)
local YngLow `r(atspec)'
quietly mtable if _selYC == 1, atmeans
local YngCol `r(atspec)'
quietly mtable if _selMC == 1, atmeans
local MidCol `r(atspec)'
quietly mtable if _selOC == 1, atmeans
local OldCol `r(atspec)'
```

We use these locals to compute four predictions with a single **`mtable`**:

```
mtable, at(`YngLow') at(`YngCol') at(`MidCol') at(`OldCol') post
```

    Expression: Pr(lfp), predict()
    
               |       k5      k618    agecat        wc        hc       lwg       inc     Pr(y)
     ----------+-------------------------------------------------------------------------------
             1 |        2         0         1         0         0       .75        10     0.159
             2 |        2         0         1         1         1      1.62      16.6     0.394
             3 |        0         2         2         1         1      1.16      24.4     0.739
             4 |        0         0         3         1         1      1.38      27.9     0.631
    
    Specified values where .n indicates no values specified with at()
    
               |  No at()
     ----------+---------
       Current |       .n

Because the values of all independent variables were specified for each prediction, their values appear in the table of predictions rather than in a table of values of covariates below the predictions. Because there are no values to place in the table, **``.n``** is shown. Because the predictions were posted, we can use **``mlincom``** for each comparison:

```
mlincom 1 - 2
mlincom 1 - 3
mlincom 1 - 4
```

    . mlincom 1 - 2
    
                 |   lincom    pvalue        ll        ul 
    -------------+---------------------------------------
               1 |   -0.235     0.000    -0.340    -0.129 
    
    . mlincom 1 - 3
    
                 |   lincom    pvalue        ll        ul 
    -------------+---------------------------------------
               1 |   -0.580     0.000    -0.720    -0.440 
    
    . mlincom 1 - 4
    
                 |   lincom    pvalue        ll        ul 
    -------------+---------------------------------------
               1 |   -0.471     0.000    -0.622    -0.320 

Alternatively, we can take advantage of the **``pwcompare()``** option in **``margins``**, which is not available with the **``mtable``** command. We specify the values at which we want to make predictions, just like we did with **``mtable``**. We suppress the lengthy listing of the titlegend and request pairwise comparisons of the estimates:

```
estimates restore base
margins, at(`YngLow') at(`YngCol') at(`MidCol') at(`OldCol') ///
noatlegend pwcompare(effects)
```

    Pairwise comparisons of adjusted predictions               Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    
    ------------------------------------------------------------------------------
                 |            Delta-method    Unadjusted           Unadjusted
                 |   Contrast   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             _at |
         2 vs 1  |       0.23       0.05     4.37   0.000         0.13        0.34
         3 vs 1  |       0.58       0.07     8.10   0.000         0.44        0.72
         4 vs 1  |       0.47       0.08     6.11   0.000         0.32        0.62
         3 vs 2  |       0.35       0.09     3.94   0.000         0.17        0.52
         4 vs 2  |       0.24       0.09     2.71   0.007         0.07        0.41
         4 vs 3  |      -0.11       0.05    -2.23   0.026        -0.20       -0.01
    ------------------------------------------------------------------------------

### 3.4 Marginal effects for ideal types

Given our cautions about relying on a single value to summarize marginal effects, ideal types are an excellent way to examine variation in the size of effects at different locations in the data space. Here we are taking different hypothetical observations and describing how the predicted probability changes as the value of one of the independent variables for that observation changes. To do this, we use local macros to specify the values at which the change is to be computed, just as we did with **`mtable`**.

First, we com pute discrete changes for wc and k5 for a young, low SES family:

```
mchange wc k5, atmeans amount(one) at(`YngLow')
```

logit: Changes in Pr(y) | Number of obs = 753
    
    Expression: Pr(lfp), predict(pr)
    
                   |    Change    p-value 
    ---------------+---------------------
    wc             |                     
     college vs no |     0.137      0.008 
    k5             |                     
                +1 |    -0.114      0.000 
    
    Predictions at base value
    
                 | not in LF      in LF 
    -------------+---------------------
      Pr(y|base) |     0.841      0.159 
    
    Base values of regressors
    
                 |        k5       k618     agecat         wc         hc        lwg        inc 
    -------------+----------------------------------------------------------------------------
              at |         2          0          1          0          0        .75         10 
    
    1: Estimates with margins option atmeans.


```
matrix YngLow = r(table)
```

**``mchange``** leaves the marginal effects in the **``r (table)``** matrix, which we copy to the matrix **YngLow** so that we can combine it with estimates of effects for other ideal types.

```
mchange wc k5, atmeans amount(one) at(`YngCol')
matrix YngCol = r(table)
mchange wc k5, atmeans amount(one) at(`MidCol')
matrix MidCol = r(table)
mchange wc k5, atmeans amount(one) at(`OldCol')
matrix OldCol = r(table)
```

Next, we select the first column of each matrix, which contains the effects, and concatenate them into a single matrix we name **me**:

```
matrix me = YngLow[1...,1], YngCol[1...,1], MidCol[1...,1], OldCol[1...,1]
matrix colnames me = YngLow YngCol MidCol OldCol
matlist me, format(%9.2f) twidth(15)
```

                    |    YngLow     YngCol     MidCol     OldCol 
    ----------------+-------------------------------------------
    wc              |                                           
      college vs no |      0.14       0.19       0.19       0.19 
    ----------------+-------------------------------------------
    k5              |                                           
                 +1 |     -0.11      -0.32      -0.32      -0.32 

The effects of the wife going to college are within 0.06 across the four ideal types. The effects of having one more young child in the family, however, increase in magnitude from -0.11 for young families without college education to -0.33 for older families that attended college. The differences in discrete changes for k5 reflect the variation in the size of effects within the sample.

## 4 Tables of predicted probabilities

When you are interested in the effects of one or more categorical independent variables, a table of predictions can be very effective. For example, our analysis thus far highlights the importance of attending college and having young children. To see how these variables jointly affect the probability of being in the labor force, we can use a simple **``mtable``** command:

```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store base

mtable, at(wc=(0 1) k5=(0 1 2 3)) atmeans
```

    Expression: Pr(lfp), predict()
    
               |       k5        wc     Pr(y)
     ----------+-----------------------------
             1 |        0         0     0.604
             2 |        0         1     0.772
             3 |        1         0     0.275
             4 |        1         1     0.457
             5 |        2         0     0.086
             6 |        2         1     0.173
             7 |        3         0     0.023
             8 |        3         1     0.049
    
    Specified values of covariates
    
               |                    2.        3.        1.                    
               |     k618      agecat    agecat        hc       lwg       inc
     ----------+-------------------------------------------------------------
       Current |     1.35        .385      .219      .392       1.1      20.1

Although this is the information we want, it is not an effective table. We can improve it by using two **``at()``**'s along with **``atvars(wc k5)``** to list values of wc in the first column followed by values of k5. The option **``names(columns)``** removes the row numbers (see help **``matlist``** for details on the **``names()``** option). 

```
mtable, at(wc=0 k5=(0 1 2 3)) at(wc=1 k5=(0 1 2 3)) atmeans ///
        atvars(wc k5) names(columns)
```

    Expression: Pr(lfp), predict()
    
           1.                    
          wc        k5     Pr(y)
    ----------------------------
           0         0     0.604
           0         1     0.275
           0         2     0.086
           0         3     0.023
           1         0     0.772
           1         1     0.457
           1         2     0.173
           1         3     0.049
    
    Specified values of covariates
    
               |                    2.        3.        1.                    
               |     k618      agecat    agecat        hc       lwg       inc
     ----------+-------------------------------------------------------------
       Current |     1.35        .385      .219      .392       1.1      20.1

The table shows the strong effect of education and how the size of the effects differ by the number of young children, but the information still is not presented well.

Our next step is to compute the discrete change for college education conditional on the number of young children:

$$\frac{\Delta\Pr\left(y=1\mid\mathbf{x},\mathbf{k}5\right)}{\Delta\text{wc}\left(0\to1\right)}$$

Because **``wc``** was entered into the model as a factor variable, we can compute the discrete change by using **``dydx(wc)``**. In the process, let’s create an even more effective table that gets close to what we might include in a paper. First, we compute the predictions for **``wc=0``**:

```
quietly mtable, estname(NoCol) at(wc=0 k5=(0 1 2 3)) atmeans brief
```

Next, we make predictions for **wc=1**. We use the **``right``** option to place the predictions to the right of those from the prior **``mtable``** command, and we use **``atvars(_none)``** because we do not want the column with **k5** included again.

```
quietly mtable, estname(College) at(wc=1 k5=(0 1 2 3)) atmeans ///
        atvars(_none) right
```

Now, we use the **``dydx(wc)``** option to compute discrete changes. We place these along with the **p-value** for testing whether the change is 0 to the right:

```
mtable, estname(Change) dydx(wc) at(k5=(0 1 2 3)) atmeans ///
    atvars(_none) right stats(estimate p) names(columns) brief
```

    Expression: Pr(lfp), predict()
    
          k5     NoCol     NoCol   College    Change         p
    ----------------------------------------------------------
           0     0.772     0.604     0.772     0.168     0.000
           1     0.457     0.275     0.457     0.182     0.001
           2     0.173     0.086     0.173     0.087     0.013
           3     0.049     0.023     0.049     0.027     0.085

We can summarize these findings:
>For someone who is average on all characteristics and has no young children, having attended college significantly increases the predicted probability of being in the labor force by 0.17. The size of the effect decreases with the number of young children. For example, for someone with two young children, the increase is only 0.09, which is significant at the 0.01 level.

Although this table shows clearly how education and children affect labor force participation, it assumes that it is reasonable to change **``wc``** and **``k5``** while holding other variables at their global means. This is unrealistic. For example, it is likely that women with three young children will be in the youngest age group, while few people with three young children will be over 50. Each cell in the table represents a different ideal type, but some of the ideal types are substantively unusual, limiting their usefulness as a point of comparison.

We could approach the problem in at least a couple of different ways that we regard as preferable to using global means. Here we consider two approaches. First, we define ideal types in terms of someone from the youngest age category, ages 30-39, for whom having three young children is not substantively unrealistic. Means of the control variables are based on sample members in the youngest age group. Second, we use local means defined by levels of **k5** when making predictions. Thus the means for those with no children differ from those with one child.

Our first approach is to focus on those in the youngest age category. We could do this by adding **agecat=1** to the **``atspecs``** used above, but, substantively, we think it makes more sense to use the mean values of the other independent variables for someone in this age group. To do this, we specify **if agecat==1** when we use **``atmeans``** to make our predictions:

```
mtable if agecat == 1, estname(Change) dydx(wc) at(k5=(0 1 2 3)) atmeans ///
    atvars(k5) stats(estimate p) names(columns)
```

    Expression: Pr(lfp), predict()
    
          k5    Change         p
    ----------------------------
           0     0.131     0.000
           1     0.197     0.000
           2     0.124     0.005
           3     0.043     0.058
    
    Specified values of covariates
    
               |                            1.        1.                    
               |     k618    agecat        wc        hc       lwg       inc
     ----------+-----------------------------------------------------------
       Current |     1.85         1      .312      .463      1.06      18.8

Notice that **``agecat``** is 1 in the table of specified values because we are restricting the analyses to those in this age group. Adapting the commands used above, we create a new table illustrating the effects of children and education on labor force participation:

```
quietly mtable if agecat==1, estname(NoCol) at(wc=0 k5=(0 1 2 3)) atmeans
quietly mtable if agecat==1, estname(College) at(wc=1 k5=(0 1 2 3)) atmeans ///
    atvars(_none) right
mtable if agecat==1, estname(Change) dydx(wc) at(k5=(0 1 2 3)) atmeans ///
    atvars(_none) right stats(estimate p) names(columns) brief
```

    Expression: Pr(lfp), predict()
    
          k5     NoCol   College    Change         p
    ------------------------------------------------
           0     0.720     0.851     0.131     0.000
           1     0.390     0.586     0.197     0.000
           2     0.137     0.261     0.124     0.005
           3     0.038     0.081     0.043     0.058

The predicted probabilities and discrete changes using local means for the youngest members of the sample are different from when we used global means for the sample as a whole. **Using global means, having a college education increased the predicted probability for a woman with no children by 0.168, while using means conditional on the woman being age 30-39, the increase is 0.131.**

Our second approach uses local means defined by levels of **k5** when making predictions. **That is, when making predictions for women with no young children, we will hold other variables at the mean for those with no young children, which would be equivalent to computing the means by using **``summarize ... if k5==0``**.** We can do this with the **``over(overvar)``** option, which makes predictions at each value of **``overvar``**, where this variable must have nonnegative integer values. For each value of **k5**, predictions are made based only on cases with the given value of **``k5``**. That is, predictions are made first with cases that meet the condition **k5==0**, then with cases that meet **k5==1**, and so on. Accordingly, the **``atmeans``** option will hold other variables at the means conditional on the value of **k5**. The output shows how the means vary:

```
mtable, at(wc=(0 1)) atmeans over(k5) brief
```

    Expression: Pr(lfp), predict()
    
               |                            2.        3.                  1.                              
               |       k5      k618    agecat    agecat        wc        hc       lwg       inc     Pr(y)
     ----------+-----------------------------------------------------------------------------------------
             1 |        0      1.28      .436      .269         0      .358      1.11        20     0.583
             2 |        1      1.75      .212     .0169         0      .517      1.03      20.8     0.337
             3 |        2      1.31     .0385         0         0      .538      1.18      17.6     0.154
             4 |        3      1.33         0         0         0         1      1.08      46.1     0.017
             5 |        0      1.28      .436      .269         1      .358      1.11        20     0.757
             6 |        1      1.75      .212     .0169         1      .517      1.03      20.8     0.530
             7 |        2      1.31     .0385         0         1      .538      1.18      17.6     0.288
             8 |        3      1.33         0         0         1         1      1.08      46.1     0.037

In row 1 where **wc==0** and row 5 where **wc==1**, the values for **k618**, **agecat**, **wc**, **hc**, **lwg**, and **inc** are the means in the subsample defined by **k5==0**. And so on for other values of **k5**. Using **``over()``** here is equivalent to running a series of **``mtable``** commands of the form **``mtable if k5==0, at(wc=(0 1 )) atmeans``**.

Does using local means affect the results? In this example, the results using global means do differ somewhat from those using local means, especially at **k5==2**. 


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.15.png" style="width:750px;height:170px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Tables of predictions allow readers to see how predictions vary over values of substantively important independent variables. We do not want a change in an independent variable to result in our presenting as illustrative some ideal types that are actually unlikely or even impossible. As we have shown above, one way to do this is to restrict our sample or otherwise choose representative values so that the changes presented in the table remain substantively realistic. Another is to use local means that change as the key independent variable(s) of the table changes.

## 5 Second differences comparing marginal effects

We can compute AMEs based on a subset of observations. For example, suppose that we are interested in ways in which the wife’s and the husband’s educations **interact** to affect labor force participation. Because we are focusing on the joint effects of these two variables, we fit a new model that includes the interaction between **wc** and **hc**:


```
logit lfp k5 k618 i.agecat wc##hc lwg inc, nolog
estimates store base
```

    Logistic regression                                     Number of obs =    753
                                                            LR chi2(9)    = 125.57
                                                            Prob > chi2   = 0.0000
    Log likelihood = -452.08908                             Pseudo R2     = 0.1219
    
    ----------------------------------------------------------------------------------
                 lfp | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -----------------+----------------------------------------------------------------
                  k5 |      -1.40       0.19    -7.27   0.000        -1.78       -1.02
                k618 |      -0.07       0.07    -0.96   0.337        -0.20        0.07
                     |
              agecat |
              40-49  |      -0.62       0.21    -2.97   0.003        -1.03       -0.21
                50+  |      -1.27       0.26    -4.88   0.000        -1.78       -0.76
                     |
                  wc |
            college  |       1.19       0.43     2.75   0.006         0.34        2.05
                     |
                  hc |
            college  |       0.25       0.23     1.08   0.281        -0.20        0.69
                     |
               wc#hc |
    college#college  |      -0.56       0.51    -1.11   0.269        -1.55        0.43
                     |
                 lwg |       0.61       0.15     4.05   0.000         0.31        0.90
                 inc |      -0.03       0.01    -4.19   0.000        -0.05       -0.02
               _cons |       0.98       0.29     3.41   0.001         0.42        1.54
    ----------------------------------------------------------------------------------

We want to know whether the effect of a women going to college is the same for a women whose husband did go to college as for a woman whose husband did not go to college:

$$H_0{:\frac{\Delta\Pr\left(y=1\mid\mathrm{x,hc}=0\right)}{\Delta\mathrm{wc}}}=\frac{\Delta\Pr\left(y=1\mid\mathrm{x,hc}=1\right)}{\Delta\mathrm{wc}}$$

To test this hypothesis, we compute the AME of wc averaging over only those cases where hc is 0 and compare it with the AME for those cases where hc is 1. Although we could compute these discrete changes by using **`mchange wc if hc==1`** and **`mchange wc if hc==0`**, this will not allow us to test whether the effects are equal because the estimates cannot be posted for testing with **`mlincom`**. To test the hypothesis, we use **`mtable`** with the **`dydx(wc)`** option to compute the discrete change for wc and the **`over(hc)`** option to request the changes be computed with the subgroups defined by hc:

```
mtable, dydx(wc) over(hc) post
```

    Expression: Pr(lfp), predict()
    
               |  d Pr(y)
     ----------+---------
            no |    0.233
       college |    0.128
    
    Specified values where .n indicates no values specified with at()
    
               |  No at()
     ----------+---------
       Current |       .n

The row labeled no contains the discrete change of wc for those women whose husbands did not attend college (no is the value label for hc= 0), and the row college contains the change for those whose husbands attended college. The **`post`** option saves the estimates to **`e(b)`**, which allows us to use **`mlincom`** to test whether the marginal effects are equal:

```
test _b[1.wc:0.hc]=_b[1.wc:1.hc]
mlincom 1 - 2
```

     ( 1)  [1.wc]0bn.hc - [1.wc]1.hc = 0
    
               chi2(  1) =    1.42
             Prob > chi2 =    0.2329
    
    . mlincom 1 - 2
    
                 |   lincom    pvalue        ll        ul 
    -------------+---------------------------------------
               1 |    0.105     0.233    -0.068     0.279 

We conclude the following:

>Although the average effect of the wife going to college is 0.10 larger when the husband did not go to college than when he did, this difference is not significant (p > 0.10).

## 6 Graphing predicted probabilities

With a continuous independent variable, you can plot the predicted probabilities over the range of the variable. For example, to examine the effects of inc, we might plot the predicted probability of labor force participation as income changes, holding other variables at fixed values. We offer two approaches for making such graphs. First, Stata's **`marginsplot`** command uses predictions from margins to create plots. As you will see, it quickly produces effective graphs. The second approach uses our **`mgen`** command to generate variables with the values to be plotted, which are then plotted with **`graph`**. This is essentially what **`marginsplot`** does behind the scenes. Although **`marginsplot`** is simpler, **`mgen`** is more flexible in ways that often justify the greater effort that it requires. The advantage will be particularly apparent in subsequent chapters when we create plots for multiple outcomes that cannot be created with **`marginsplot`**.

We begin by showing you how to create plots where one variable changes while all other variables are held constant.

### 6.1 Using marginsplot

The first step is to use **`margins`** to compute predicted probabilities as income increases from 0 to 100, while holding other variables at their means:

```
use binlfp4, clear
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
estimates store base
margins, at(inc=(0(10)100)) atmeans
```

    Adjusted predictions                                       Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    1._at:  k5       = .2377158 (mean)
            k618     = 1.353254 (mean)
            1.agecat = .3957503 (mean)
            2.agecat = .3851262 (mean)
            3.agecat = .2191235 (mean)
            0.wc     = .7184595 (mean)
            1.wc     = .2815405 (mean)
            0.hc     = .6082337 (mean)
            1.hc     = .3917663 (mean)
            lwg      = 1.097115 (mean)
            inc      =        0
    11._at: k5       = .2377158 (mean)
            k618     = 1.353254 (mean)
            1.agecat = .3957503 (mean)
            2.agecat = .3851262 (mean)
            3.agecat = .2191235 (mean)
            0.wc     = .7184595 (mean)
            1.wc     = .2815405 (mean)
            0.hc     = .6082337 (mean)
            1.hc     = .3917663 (mean)
            lwg      = 1.097115 (mean)
            inc      =      100
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             _at |
              1  |       0.73       0.04    20.36   0.000         0.66        0.81
              2  |       0.66       0.03    25.32   0.000         0.61        0.71
              3  |       0.58       0.02    29.40   0.000         0.54        0.62
              4  |       0.49       0.03    17.17   0.000         0.44        0.55
              5  |       0.41       0.04     9.20   0.000         0.32        0.49
              6  |       0.32       0.06     5.70   0.000         0.21        0.44
              7  |       0.25       0.06     3.94   0.000         0.13        0.38
              8  |       0.19       0.07     2.95   0.003         0.06        0.32
              9  |       0.14       0.06     2.33   0.020         0.02        0.26
             10  |       0.11       0.06     1.92   0.055        -0.00        0.21
             11  |       0.08       0.05     1.63   0.103        -0.02        0.17
    ------------------------------------------------------------------------------

The **atlegend** shows the values of the independent variables for each of the 11 predictions in the table, which are automatically saved in the matrix **`r(b)`**. Because the **atlegend** can be quite long, we often use **`noatlegend`** to suppress it. Then, we use **`margins`** for a more compact summary.

```
margins, at(inc=(0(10)100)) atmeans noatlegend
```

    Adjusted predictions                                       Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             _at |
              1  |       0.73       0.04    20.36   0.000         0.66        0.81
              2  |       0.66       0.03    25.32   0.000         0.61        0.71
              3  |       0.58       0.02    29.40   0.000         0.54        0.62
              4  |       0.49       0.03    17.17   0.000         0.44        0.55
              5  |       0.41       0.04     9.20   0.000         0.32        0.49
              6  |       0.32       0.06     5.70   0.000         0.21        0.44
              7  |       0.25       0.06     3.94   0.000         0.13        0.38
              8  |       0.19       0.07     2.95   0.003         0.06        0.32
              9  |       0.14       0.06     2.33   0.020         0.02        0.26
             10  |       0.11       0.06     1.92   0.055        -0.00        0.21
             11  |       0.08       0.05     1.63   0.103        -0.02        0.17
    ------------------------------------------------------------------------------

```
mlistat
```

    at() values held constant
    
                               2.        3.        1.        1.          
          k5      k618    agecat    agecat        wc        hc       lwg 
    --------------------------------------------------------------------
        .238      1.35      .385      .219      .282      .392       1.1 
    
    at() values vary
    
       _at |      inc 
    -------+---------
         1 |        0 
         2 |       10 
         3 |       20 
         4 |       30 
         5 |       40 
         6 |       50 
         7 |       60 
         8 |       70 
         9 |       80 
        10 |       90 
        11 |      100 

Either way, **`marginsplot`** uses the predictions in **`r(b)`** along with other returns from **`margins`**, and it graphs the predictions including the 95% confidence intervals.

```
marginsplot
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.16.png" style="width:750px;height:470px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The graph shows how the probability of being in the labor force decreases with family income. It also shows that the confidence intervals are smaller near the center of the data (the mean of inc is 20.1) and increase as we move to the extremes.

Although **`marginsplot`** does an excellent job of creating the graph without requiring options, you can fully customize the graph. Use **`help marginsplot`** for full details. For example, to suppress the confidence interval, type **`marginsplot, noci`**. To use shading to show the confidence interval (illustrated below), type **`marginsplot, recast(line) recastci(rarea)`**.

If you are only interested in plotting a single type of prediction from one model, there is little reason to use anything but **`marginsplot`**. But, if you want to plot multiple outcomes, such as for multinomial logit, or predictions for a single outcome from multiple models, it is worth learning about **`mgen`**.

### 6.2 Using mgen with the graph command
To create the same graph as above by using **`mgen`**, our first step is to generate variables for plotting:

```
mgen, atmeans at(inc=(0(10)100)) stub(PLT) predlabel(Pr(LFP))
```

    Predictions from: margins, atmeans at(inc=(0(10)100)) predict(pr)
    
    Variable   Obs Unique      Mean        Min       Max  Label
    -------------------------------------------------------------------------------------------
    PLTpr1      11     11  .3608011   .0768617  .7349035  Pr(LFP)
    PLTll1      11     11  .2708139  -.0156624  .6641427  95% lower limit
    PLTul1      11     11  .4507883   .1693859  .8056643  95% upper limit
    PLTinc      11     11        50          0       100  Family income excluding wife's
    -------------------------------------------------------------------------------------------
    
    Specified values of covariates
    
                                  1.         3.         1.         1.           
           k5       k618     agecat     agecat         wc         hc        lwg 
    ---------------------------------------------------------------------------
     .2377158   1.353254   .3851262   .2191235   .2815405   .3917663   1.097115 


The option **`stub()`** specifies the prefix for variables that are generated. If **`stub()`** is not specified, the default **`stub(_)`** is used. If you want to replace existing plot variables (perhaps while debugging your do-file), add the option **`replace`**. The option **`predlabel()`** customizes the variable label for **`PLTprl`**, which is handy because by default graph uses this label for the y axis.

If we list the values for the first 13 observations, we see the variables created by **`mgen`**:

```
list PLTinc PLTpr PLTll PLTul lfp in 1/13, clean
```

           PLTinc     PLTpr1      PLTll1     PLTul1         lfp  
      1.        0   .7349035    .6641427   .8056643   not in LF  
      2.       10   .6613024    .6101217   .7124832   not in LF  
      3.       20   .5789738    .5403737   .6175739   not in LF  
      4.       30   .4920058    .4358374   .5481742   not in LF  
      5.       40    .405519    .3191012   .4919367   not in LF  
      6.       50    .324523    .2129492   .4360968   not in LF  
      7.       60   .2528245    .1272066   .3784425   not in LF  
      8.       70   .1924535    .0644926   .3204144   not in LF  
      9.       80   .1437253    .0227563   .2646942   not in LF  
     10.       90   .1057196   -.0023663   .2138055   not in LF  
     11.      100   .0768617   -.0156624   .1693859   not in LF  
     12.        .          .           .          .   not in LF  
     13.        .          .           .          .   not in LF  

Column 1 contains the 11 values of income from variable **`PLTinc`** that will define the x coordinates. The next column contains predicted probabilities computed at the values of income with other variables held at their means. The negative effect of income is shown by the increasingly small probabilities. The next two columns contain the upper and lower bounds of the confidence intervals for the predictions. The first four variables have missing values beginning in rows 12 and 13 because our **`atspec`** requested only 11 predictions. The last column shows the observed variable **`lfp`**, which does not have missing values. This is being shown to remind you that the variables created for graphing are added to the dataset used for estimation.

You can also create a basic graph w ithout confidence intervals:

```
scatter PLTpr PLTinc
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.17.png" style="width:750px;height:470px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Next, we want to add the 95% confidence interval around the predictions. This requires more complicated graph options. To explain these, let’s start by looking at the graph we want to create:

```
twoway ///
    (rarea PLTul PLTll PLTinc, color(gs12)) ///
    (connected PLTpr PLTinc, msymbol(i)) ///
    , title("Adjusted Predictions") ///
    caption("Other variables held at their means") ///
    ytitle(Pr(LFP)) ylabel(0(.25)1, grid gmin gmax) legend(off)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.18.png" style="width:750px;height:510px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Here is the twoway command that we will explain:

The first thing to realize is that the **`twoway`** command includes two plots: a plot and a connected plot. These are overlaid to make a single graph. First, the shaded confidence intervals are created with a **`rarea`** plot where the area on the y axis is shaded between the values of **`PLTul`** for the upper level or bound and **`PLTll`** for the lower bound. We chose **`color(gs12)`** to make the shading grayscale level 12, a matter of personal preference. Second, the line with predicted probabilities is created with a connected plot, where **`msymbol(i)`** specifies that the symbols (shown as solid circles in our prior graph) that are connected should be invisible—that is, draw the line without symbols. We defined the **`rarea`** plot before the connected plot because Stata draws overlaid plots in the order specified; we want the line indicating the predicted probabilities to appear on top of the shading.

After the **`,`** in the fourth line of the command are options that apply to the overall graph rather than the individual plots. **`ylabel()`** defines the y-axis labels, with **`grid`** requesting grid lines. Suboptions **`gmin`** and **`gmax`** place grid lines at the maximum and minimum values of the axis. By default, when you are plotting multiple outcomes (in this case **`PLTul`**, **`PLTll`**, and **`PLTpr`**), graph adds a legend describing each outcome. To turn this off, we use **`legend(off)`**. See section 2.17 for more information on graphing.

### 6.3 Graphing multiple predictions

An effective way to show the effects of two variables is to graph predictions at various levels of one variable as the other variable changes. This can be done with either **`marginsplot`** or **`mgen`**.

#### 6.3.1 Using marginsplot
We can plot the effects of income for each of the age groups. First, we compute the predictions with **`margins`**, where **`margins agecat`** indicates that we want predictions for each level of the factor variable **`agecat`**. **`at(inc = (0(10)100))atmeans`** specifies predictions as income increases from 0 to 100 by 10s, with all variables except **`agecat`** at their means:

```
margins agecat, at(inc=(0(10)100)) atmeans noatlegend
```

    Adjusted predictions                                       Number of obs = 753
    Model VCE: OIM
    
    Expression: Pr(lfp), predict()
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
      _at#agecat |
        1#30-39  |       0.82       0.03    26.36   0.000         0.76        0.88
        1#40-49  |       0.71       0.05    15.79   0.000         0.63        0.80
          1#50+  |       0.57       0.06     9.20   0.000         0.44        0.69
        2#30-39  |       0.77       0.03    25.73   0.000         0.71        0.83
        2#40-49  |       0.64       0.04    17.04   0.000         0.56        0.71
          2#50+  |       0.48       0.05     9.42   0.000         0.38        0.58
        3#30-39  |       0.70       0.03    21.88   0.000         0.64        0.76
        3#40-49  |       0.55       0.03    17.13   0.000         0.49        0.62
          3#50+  |       0.39       0.04     8.95   0.000         0.31        0.48
        4#30-39  |       0.62       0.04    14.75   0.000         0.54        0.70
        4#40-49  |       0.47       0.04    12.72   0.000         0.39        0.54
          4#50+  |       0.31       0.04     7.27   0.000         0.23        0.40
        5#30-39  |       0.53       0.06     9.22   0.000         0.42        0.65
        5#40-49  |       0.38       0.05     8.08   0.000         0.29        0.47
          5#50+  |       0.24       0.04     5.40   0.000         0.15        0.33
        6#30-39  |       0.45       0.07     6.01   0.000         0.30        0.59
        6#40-49  |       0.30       0.06     5.34   0.000         0.19        0.41
          6#50+  |       0.18       0.05     4.01   0.000         0.09        0.27
        7#30-39  |       0.36       0.09     4.19   0.000         0.19        0.53
        7#40-49  |       0.23       0.06     3.81   0.000         0.11        0.35
          7#50+  |       0.14       0.04     3.09   0.002         0.05        0.22
        8#30-39  |       0.29       0.09     3.10   0.002         0.11        0.47
        8#40-49  |       0.18       0.06     2.89   0.004         0.06        0.30
          8#50+  |       0.10       0.04     2.48   0.013         0.02        0.18
        9#30-39  |       0.22       0.09     2.42   0.016         0.04        0.40
        9#40-49  |       0.13       0.06     2.30   0.021         0.02        0.24
          9#50+  |       0.07       0.04     2.05   0.040         0.00        0.14
       10#30-39  |       0.17       0.08     1.96   0.049         0.00        0.33
       10#40-49  |       0.10       0.05     1.91   0.056        -0.00        0.20
         10#50+  |       0.05       0.03     1.75   0.080        -0.01        0.11
       11#30-39  |       0.12       0.07     1.65   0.099        -0.02        0.27
       11#40-49  |       0.07       0.04     1.63   0.104        -0.01        0.15
         11#50+  |       0.04       0.02     1.52   0.128        -0.01        0.09
    ------------------------------------------------------------------------------

```
mlistat
```

          k5      k618    agecat    agecat        wc        hc       lwg 
    --------------------------------------------------------------------
        .238      1.35      .385      .219      .282      .392       1.1 
    
    at() values vary
    
       _at |      inc 
    -------+---------
         1 |        0 
         2 |       10 
         3 |       20 
         4 |       30 
         5 |       40 
         6 |       50 
         7 |       60 
         8 |       70 
         9 |       80 
        10 |       90 
        11 |      100 

The labeling of the predictions from **`margins`** can be confusing. The left column of the prediction table is labeled **`_at#agecat`**, which indicates that the information in this column begins with a number corresponding to the 11 values of inc used for making predictions; these are referred to as the **`_at`** values. For example, 1 is the prediction with inc=0 while 11 is the prediction with inc=100. After that, the value or value label for **`agecat`** is listed. For example, the row labeled **`l#30-39`** contains the predictions when all variables except **`agecat`** are held at the first **`_at`** value, with **`agecat=1`** as indicated by the value label **`30-39`**. The command **`marginsplot, noci`** automatically understands what these predictions are and creates the plot we want.

```
marginsplot, noci legend(cols(3))
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.19.png" style="width:750px;height:410px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

This example shows that **`marginsplot`** can plot multiple curves for the same outcome from the same model. Unfortunately, it cannot plot curves for multiple outcomes (for example, the probability of categories 1, 2, and 3 in an ordinal model) or predictions from different models (for example, showing how the predictions differ from two specifications of the model). For this, you need to use **`mgen`**.

#### 6.3.2 Using mgen with graph

We can create the same graph as in the previous section by running **`mgen`** once for each level of **`agecat`**:

```
mgen, atmeans at(inc=(0(10)100) agecat=1) stub(PLT1) predlab(30 to 39)
```

    Predictions from: margins, atmeans at(inc=(0(10)100) agecat=1) predict(pr)
    
    Variable   Obs Unique      Mean        Min       Max  Label
    -----------------------------------------------------------------------------------------
    PLT1pr1     11     11  .4591184   .1230226  .8236541  30 to 39
    PLT1ll1     11     11  .3349719  -.0230338  .7624032  95% lower limit
    PLT1ul1     11     11   .583265    .269079  .8849049  95% upper limit
    PLT1inc     11     11        50          0       100  Family income excluding wife's
    ------------------------------------------------------------------------------------------
    
    Specified values of covariates
    
                                             1.         1.           
           k5       k618     agecat         wc         hc        lwg 
    ----------------------------------------------------------------
     .2377158   1.353254          1   .2815405   .3917663   1.097115 

```
mgen, atmeans at(inc=(0(10)100) agecat=2) stub(PLT2) predlab(40 to 49)
mgen, atmeans at(inc=(0(10)100) agecat=3) stub(PLT3) predlab(50 plus)
```

Then , we use a more complex graph command to obtain the following graph:

```
graph twoway connected PLT1pr PLT2pr PLT3pr PLT1inc, ///
    title("Adjusted predictions by age group") ///
    caption("Other variables at their means") ///
    msym(Oh Dh Sh) msiz(*1.4 *1.1 *1.2) mcol(black black black) ///
    lpat(solid solid solid) ///
    ytitle("Pr(In Labor Force)") ylab(0(.25)1, grid gmin gmax) ///
    legend(position(3))
```

When there are multiple sets of lines and symbols to be drawn—in this case, three sets—you need to provide options for each set. The option **`msym(Oh Dh Sh)`** indicates that we want large, hollow circles, diamonds, and squares for the symbols. We find that **`Oh`** by default is a smaller symbol than **`Dh`** or **`Sh`**. The **`msize()`** option lets you specify the size for each symbol. Although you can use names for the sizes, such as **`msize(large medium med small)`**, we find relative sizing to be easier. Our option **`msize(*1.4 *1.1 *1.2)`** tells graph to make the first symbol 1.4 times larger than normal, and so on.

### 6.3 Overlapping confidence intervals

We find that researchers sometimes conclude that estimates are significantly different only if confidence intervals for two estimates do not overlap. That is, if the confidence intervals overlap, the hypothesis that the estimates are equal is accepted. Although this might have been a useful approximation when computation was very expensive, it often leads to incorrect conclusions because it ignores the covariances of the estimators that need to be taken into account when testing equality.

To illustrate the problem, as well as show how discrete changes and marginal changes can be graphed, we use the techniques above to plot the probability of labor force participation by income for women who attended college and those who did not. We start with the graph before showing how we created it.

We use one mgen command for each level of wc:

```
mgen, atmeans at(inc=(0(5)100) wc=0) stub(PLTWC0) predlab(NoCollege)
```

    Predictions from: margins, atmeans at(inc=(0(5)100) wc=0) predict(pr)
    
    Variable   Obs Unique      Mean        Min       Max  Label
    -------------------------------------------------------------------------------------
    PLTWC0pr1   21     21  .3177494   .0623648  .6889161  NoCollege
    PLTWC0ll1   21     21  .2309727  -.0151898  .6107005  95% lower limit
    PLTWC0ul1   21     21  .4045261   .1399194  .7671317  95% upper limit
    PLTWC0inc   21     21        50          0       100  Family income excluding wife's
    -------------------------------------------------------------------------------------
    
    Specified values of covariates
    
                                  1.         3.                    1.           
           k5       k618     agecat     agecat         wc         hc        lwg 
    ---------------------------------------------------------------------------
     .2377158   1.353254   .3851262   .2191235          0   .3917663   1.097115 

```
mgen, atmeans at(inc=(0(5)100) wc=1) stub(PLTWC1) predlab(College)
```

```
twoway ///
    (rarea PLTWC0ul PLTWC0ll PLTWC0inc, col(gs12)) ///
    (rarea PLTWC1ul PLTWC1ll PLTWC0inc, col(gs12)) ///
    (connected PLTWC0pr PLTWC1pr PLTWC1inc, msym(i i)) ///
    , ytitle(Pr(In Labor Force)) legend(order(4 3))
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.20.png" style="width:750px;height:410px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Judging by the overlap of confidence intervals, we might mistakenly conclude that the probability of labor force participation was significantly higher for women who attended college when family income was between $5,000$ and $40,000$ but not at other incomes.

To see how poorly this "approximation" works, we compute the discrete change conditional on income with **`mgen`**. The option **`dydx(wc)`** specifies that we want to predict the marginal effect of wc. Because wc was entered into the model as the factor variable **`i.wc`**, **`mgen`** computes a discrete change.

```
mgen, dydx(wc) atmeans at(inc=(0(5)100)) stub(PLTWCDC) ///
    predlab(Discrete change in LFP by attending college)
```

    Predictions from: margins, dydx(wc) atmeans at(inc=(0(5)100)) predict(pr)
    
    Variable      Obs Unique      Mean        Min       Max  Label
    ------------------------------------------------------------------------------------------------------
    PLTWCDCd_pr1   21     21  .1507267   .0663191  .1967745  Discrete change in LFP by attending college
    PLTWCDCll1     21     21  .0556941  -.0111785  .0895455  95% lower limit
    PLTWCDCul1     21     21  .2457593   .1438166  .3049388  95% upper limit
    PLTWCDCinc     21     21        50          0       100  Family income excluding wife's
    ------------------------------------------------------------------------------------------------------
    
    Specified values of covariates
    
                                  1.         3.         1.         1.           
           k5       k618     agecat     agecat         wc         hc        lwg 
    ---------------------------------------------------------------------------
     .2377158   1.353254   .3851262   .2191235   .2815405   .3917663   1.097115 

Plotting the results along with those for the probabilities leads to figure 6.2, which shows that women who attended college have significantly higher probabilities of labor force participation over almost the entire income distribution, excepting only incomes above $95,000$ (where there are very few cases). What is remarkable about **`margins`** is that it allows you to test just about anything you might want to say about your predictions!

```
twoway ///
    (rarea PLTWC0ul PLTWC0ll PLTWC0inc, col(gs12)) ///
    (rarea PLTWC1ul PLTWC1ll PLTWC0inc, col(gs12)) ///
    (connected PLTWC0pr PLTWC1pr PLTWC1inc, msym(i i)) ///
    , ytitle(Pr(LFP)) ///
    xlin(2 39) ///
    legend(order(4 3)) saving(temppr, replace)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.22.png" style="width:550px;height:810px;" alt="图片描述">
  <figcaption><strong>Overlapping confidence intervals compared with discrete change</strong></figcaption>
</figure>

### 6.5 Adding power terms and plotting predictions

As shown in section 6.2.1, squared terms can be included in models by using factor-variable notation. For example, income and income-squared can be included in the model by adding the term **`c.inc##c.inc`**. Although you can obtain the same parameter estimates by generating a new variable for income-squared, **`margins`** or our **`m*`** commands will not compute predictions correctly. With factor-variable notation, however, power terms and interaction terms do not pose any special problems. When **`mgen`** makes predictions, it automatically increases income-squared appropriately as income changes.

To illustrate how this works, we compare predictions from a model that is linear in income with a model that adds the squared term **`c.inc#c.inc`**. First, we fit the model that includes income (but not income-squared) and make predictions:

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg inc, nolog
mgen, predlabel(linear) atmeans at(inc=(0(10)100)) stub(_lin)
```

    Predictions from: margins, atmeans at(inc=(0(10)100)) predict(pr)
    
    Variable   Obs Unique      Mean        Min       Max  Label
    ------------------------------------------------------------------------------------------
    _linpr1     11     11  .3608011   .0768617  .7349035  linear
    _linll1     11     11  .2708139  -.0156624  .6641427  95% lower limit
    _linul1     11     11  .4507883   .1693859  .8056643  95% upper limit
    _lininc     11     11        50          0       100  Family income excluding wife's
    ------------------------------------------------------------------------------------------
    
    Specified values of covariates
    
                                  1.         3.         1.         1.           
           k5       k618     agecat     agecat         wc         hc        lwg 
    ---------------------------------------------------------------------------
     .2377158   1.353254   .3851262   .2191235   .2815405   .3917663   1.097115 

Next, we fit th e model that adds income-squared and make predictions:

```
logit lfp k5 k618 i.agecat i.wc i.hc lwg c.inc##c.inc, nolog
mgen, predlabel(quadratic) atmeans at(inc=(0(10)100)) stub(_quad)
```

    Predictions from: margins, atmeans at(inc=(0(10)100)) predict(pr)
    
    Variable   Obs Unique      Mean        Min       Max  Label
    -------------------------------------------------------------------------------------
    _quadpr1    11     11  .4410442   .2887324  .8078035  quadratic
    _quadll1    11     11  .2613509  -.1501807  .7207593  95% lower limit
    _quadul1    11     11  .6207375   .4265932  .9690264  95% upper limit
    _quadinc    11     11        50          0       100  Family income excluding wife's
    -------------------------------------------------------------------------------------
    
    Specified values of covariates
    
                                  1.         3.         1.         1.           
           k5       k618     agecat     agecat         wc         hc        lwg 
    ---------------------------------------------------------------------------
     .2377158   1.353254   .3851262   .2191235   .2815405   .3917663   1.097115 

Then, we plot the predictions:

```
graph twoway connected _linpr _quadpr _lininc, ///
    title("Comparing income specifications") ///
    caption("Other variables at their means") ///
    msym(Oh Dh) msiz(*1.5 *1) mcol(black black) lpat(solid dash) ///
    ytitle("Pr(In Labor Force)") ///
    ylabel(0(.25)1, grid gmin gmax)
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.23.png" style="width:650px;height:440px;" alt="图片描述">
  <figcaption><strong>Overlapping confidence intervals compared with discrete change</strong></figcaption>
</figure>

Although the differences at higher incomes are suggestive and dramatic, the evidence for preferring the quadratic model is mixed. BIC provides positive support for the linear model, while AIC supports the quadratic model. The coefficient for income-squared is significant at the 0.046 level. The confidence intervals around the predictions at high income levels (not shown) are wide. Based on these results, we are not convinced to abandon our baseline model.

### 6.6 (Advanced) Graphs with local means

>When plotting predictions over the range of a variable, you must decide where to hold the values of other variables. With the **`atmeans`** option in **`mgen`**, as the plotted variable changes, the other variables stay at the same global means. **Following our previous discussion of local means, in this section we show you how to allow the values of the other variables to change as the variable being plotted changes.** This requires using **`mtable`** with the **`over()`** option and moving predictions from the matrix that **`mtable`** returns. These steps require more data management than other parts of the book, but they can provide valuable insights into how robust your plot and conclusions are to assumptions about the levels of other variables.

When demonstrating tables of predictions, we suggested caution before holding other variables at their global means because changing one variable while holding all other variables at the same values might not be realistic. For example, suppose that we included age in our model as a continuous variable ranging from 20 to 90. Plotting predictions as age changes while holding the number of young children constant is unrealistic because older respondents are unlikely to have any young children in the family. Note that we have the same problem if we used a **`subscribed`** instead of **`atmeans`** here; in that case, we would be including in our average predictions those cases for which the hypothetical value of age is implausible given the observed numbers of children. One alternative approach, which we will not explore further here, is to forgo using global means in favor of a set of representative values that are substantively plausible for all values of age (that is, a family with no children).

**In any event, if you are plotting predictions in regions of your data where it is impossible or very unlikely that observations will exist, the predictions might be misleading. You can determine whether global means are reasonable by exploring how other values affect the results.** We will consider what happens when we use local means instead of global in generating the plot. To illustrate how this is done, we start with the example used above, where we plotted labor force participation by income.

```
mgen, at(inc=(0(10)100)) atmeans stub(GLOBAL) predlabel(Global means)
```

    Predictions from: margins, at(inc=(0(10)100)) atmeans predict(pr)
    
    Variable   Obs Unique      Mean        Min       Max  Label
    -------------------------------------------------------------------------------------
    GLOBALpr1   11     11  .4410442   .2887324  .8078035  Global means
    GLOBALll1   11     11  .2613509  -.1501807  .7207593  95% lower limit
    GLOBALul1   11     11  .6207375   .4265932  .9690264  95% upper limit
    GLOBALinc   11     11        50          0       100  Family income excluding wife's
    -------------------------------------------------------------------------------------
    
    Specified values of covariates
    
                                  1.         3.         1.         1.           
           k5       k618     agecat     agecat         wc         hc        lwg 
    ---------------------------------------------------------------------------
     .2377158   1.353254   .3851262   .2191235   .2815405   .3917663   1.097115 

Plotting the predictions produces th e following plot:


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.24.png" style="width:650px;height:410px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

These predictions were made by increasing family incomes from $0$ to $100,000$, holding wc, he, lwg, and other variables at their global means. This implies that those with no income have the same education and wages as those with $100,000$. As noted, before accepting this graph as a reasonable summary of the effect of family income on labor force participation, we want to determine how sensitive the predictions are to the values at which we held the other variables. In particular, what would happen if we held the other variables at levels more typical of those with a given income?

Because inc is continuous, we cannot compute means for the non-income variables conditional on a single value of income, because these means might be based on very few observations. Instead, we begin by generating the variable **`inc10k`**, which divides income into groups of $10,000$:

```
gen inc10k = trunc(inc/10)
label var inc10k "income in 10K categories"
tabulate inc10k, miss
```

      income in |
            10K |
     categories |      Freq.     Percent        Cum.
    ------------+-----------------------------------
              0 |         99       13.15       13.15
              1 |        353       46.88       60.03
              2 |        198       26.29       86.32
              3 |         61        8.10       94.42
              4 |         22        2.92       97.34
              5 |         10        1.33       98.67
              6 |          3        0.40       99.07
              7 |          4        0.53       99.60
              8 |          1        0.13       99.73
              9 |          2        0.27      100.00
    ------------+-----------------------------------
          Total |        753      100.00

Because there are very few cases in some of the higher income groupings, we might want to use larger groups. But for the experiment we have in mind, this is a reasonable place to begin. Next, we use **`mtable, over(inc10k) atmeans ci`** to compute predictions by selecting observations at each value of **`inc10k`**. This is equivalent to running **`mtable`** repeatedly for subsamples defined by **`inc10k`**.

The results show how the values of other variables vary as income changes:

```
mtable, over(inc10k) atmeans ci
```

    Expression: Pr(lfp), predict()
    
               |                            2.        3.        1.        1.                              
               |       k5      k618    agecat    agecat        wc        hc       lwg       inc     Pr(y) 
     ----------+------------------------------------------------------------------------------------------
             1 |     .202      1.43      .303      .222      .121     .0808      .922      7.25     0.680 
             2 |     .261      1.29      .363      .215      .212      .312      1.08      15.1     0.602 
             3 |     .192      1.37      .455      .192      .369       .52      1.17      24.1     0.564 
             4 |     .213      1.54      .361      .311      .443      .689      1.16      33.7     0.459 
             5 |     .318      1.55      .409      .273        .5      .727      1.05      43.4     0.354 
             6 |       .4        .6        .6        .2        .8        .8      1.48      53.8     0.411 
             7 |        0         1      .333      .667      .333      .667      1.07      64.9     0.270 
             8 |      .75      1.25       .75         0       .75         1      1.41      75.3     0.300 
             9 |        0         2         1         0         0         0      1.07        88     0.307 
            10 |        1       2.5         0         0         1         1      1.33      93.5     0.411 
    
               |                    
               |       ll        ul
     ----------+-------------------
             1 |    0.613     0.746
             2 |    0.561     0.644
             3 |    0.515     0.613
             4 |    0.386     0.531
             5 |    0.267     0.441
             6 |    0.287     0.534
             7 |    0.122     0.418
             8 |    0.077     0.524
             9 |   -0.045     0.660
            10 |   -0.056     0.878
    
    Specified values where .n indicates no values specified with at()
    
               |  No at()
     ----------+---------
       Current |       .n

To plot these predictions, we need to move them into variables, something that is ordinarily done automatically by **`mgen`**. Unfortunately, **`mgen`** does not allow local means when making predictions. So we must manually move the predictions that **`mtable`** saves in the return matrix **`r(table)`**.

To do this, we first create a matrix, **`localpred`**, equal to **`r(table)`**. This is necessary because some of the commands we want to use will not work on an **`r()`** matrix.

```
matrix localpred = r(table)
matlist localpred, format(%8.2g)
```

             |                            2.        3.        1.        1.                              
             |       k5      k618    agecat    agecat        wc        hc       lwg       inc     Pr(y) 
    ---------+------------------------------------------------------------------------------------------
           1 |       .2       1.4        .3       .22       .12      .081       .92       7.2       .68 
           2 |      .26       1.3       .36       .22       .21       .31       1.1        15        .6 
           3 |      .19       1.4       .45       .19       .37       .52       1.2        24       .56 
           4 |      .21       1.5       .36       .31       .44       .69       1.2        34       .46 
           5 |      .32       1.5       .41       .27        .5       .73         1        43       .35 
           6 |       .4        .6        .6        .2        .8        .8       1.5        54       .41 
           7 |        0         1       .33       .67       .33       .67       1.1        65       .27 
           8 |      .75       1.3       .75         0       .75         1       1.4        75        .3 
           9 |        0         2         1         0         0         0       1.1        88       .31 
          10 |        1       2.5         0         0         1         1       1.3        94       .41 

             |                    
             |       ll        ul 
    ---------+-------------------
           1 |      .61       .75 
           2 |      .56       .64 
           3 |      .51       .61 
           4 |      .39       .53 
           5 |      .27       .44 
           6 |      .29       .53 
           7 |      .12       .42 
           8 |     .077       .52 
           9 |    -.045       .66 
          10 |    -.056       .88 

Next, we select all rows of the matrix (designated as 1 ...) and the set of columns starting with column **`inc`** and ending with column **`ul`**. We use **`matrix colnames`** to give the columns the names we want to use for the new variables created in the next step.

```
matrix localpred = r(table)
matrix localpred = localpred[1...,"inc".."ul"]
matrix colnames localpred = LOCALinc LOCALpr LOCALll LOCALul
```

The command **`svmat`** generates variables from the columns of a matrix. The **`(col)`** option specifies that the new variables should have names corresponding to the columns of the matrix.

```
svmat localpred, names(col)
label var LOCALpr "Local means"
```

We gave variable **LOCALpr** a label that will be used in the graph we now create:

```
twoway ///
    (connected GLOBALpr GLOBALinc, ///
    clcol(black) clpat(solid) msym(i)) ///
    (connected LOCALpr LOCALinc, ///
    clcol(black) clpat(dash) msym(i)) ///
    , ytitle("Pr(In Labor Force)") ylab(0(.25)1, grid gmin gmax)
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.2.1.25.png" style="width:650px;height:410px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

In this example, there are no major discrepancies, suggesting that using the global means is appropriate for making the predictions.
