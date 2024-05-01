# [Categorical Dependent Var] Chapter_9 ：Models for count outcomes



**Count variables record how many times something has happened.** Examples include the number of patients, hospitalizations, daily homicides, theater visits, international conflicts, beverages consumed, industrial injuries, soccer goals scored, new companies, and arrests by police. **Although the linear regression model has often been applied to count outcomes, these estimates can be inconsistent or inefficient. In some cases, the linear regression model can provide reasonable results; however, it is much safer to use models specifically designed for count outcomes.**

In this chapter, we consider seven regression models for count outcomes, all based on the Poisson distribution. We begin with the Poisson regression model (PRM), which is the foundation for other count models. We then consider the negative binomial regression model (NBRM), which adds unobserved, continuous heterogeneity to the PRM and often provides a much better fit to the data. To deal with outcomes where observations with zero counts are missing, we consider the zero-truncated Poisson and zero-truncated negative binomial models. By combining a zero-truncated model with a binary model, we develop the hurdle regression model, which models zero and nonzero counts in separate equations. Finally, we consider the zero-inflated Poisson and the zero-inflated negative binomial models, which assume that there are two sources of zero counts.

As with earlier chapters, we review the statistical models, consider issues of testing and fit, and then discuss methods of interpretation. These discussions are intended as a review for those who are familiar with the models. See Long (1997) for a more technical introduction to count models and Cameron and Trivedi (2013) for a definitive review. You can obtain sample do-files and datasets as explained in chapter 2.

## 1 The Poisson distribution

Because the univariate Poisson distribution is fundamental to understanding regression models for counts, we start by exploring this distribution. **Let $\mu$ be the rate of occurrence or the expected number of times an event will occur during a given period of time. Let $y$ be a random variable indicating the actual number of times an event did occur. Sometimes the event will occur fewer times than expected, even not at all, and other times it will occur more often.**

The relationship between the expected count $\mu$ and the probability of observing a given count $y$ is specified by the Poisson distribution

$$\Pr(y\mid\mu)=\frac{e^{-\mu}\mu^{y}}{y!}\quad\mathrm{~for~}y=0,1,2,\ldots $$


where $ \mu > 0 $ is the sole parameter defining the distribution. $ y! $ is the factorial operator: for example, $ 4! = 4 \times 3 \times 2 \times 1 $. The easiest way to get a sense of the Poisson distribution is to compare plots of predicted probabilities for different values of the rate $ \mu $, as shown in Figure 9.1.

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.1.4.png" style="width:550px;height:370px;" alt="图片描述">
  <figcaption><strong>The Poisson probability density function (PDF) for different rates</strong></figcaption>
</figure>

The figure illustrates four characteristics of the Poisson distribution that are important for understanding regression models for counts:

1. As the mean of the distribution $\mu$ increases, the mass of the distribution shifts to the right.
2. The mean ($ \mu $) is also the variance. Thus $ \text{Var}(y) = \mu $, which is known as equidispersion. In real data, count variables often have a variance greater than the mean, which is called overdispersion. It is possible for counts to be underdispersed, but this is rarer.
3. As $ \mu $ increases, the probability of a zero count decreases rapidly. For many count variables, there are more observed 0s than predicted by the Poisson distribution.
4. as $\mu$ increases, the Poisson distribution approximates a normal distribution. This is shown by the distribution for $ \mu = 10.5 $.


5. **泊松分布的定义**：
   - 假设你经营一家呼叫中心，你想知道在一天中接到特定数量电话的概率。你预计平均每天会接到10个电话（$\mu = 10$）。
   - 泊松分布可以帮你计算实际接到5个、10个或15个电话的概率。它不关心具体哪个电话是在哪个时间接到的，只关心一天结束时总共接到了多少电话。
6. **概率计算公式**：
   - 泊松分布的概率公式是 $\Pr(y\mid\mu)=\frac{e^{-\mu}\mu^{y}}{y!}$。以 $\mu = 10$ 为例，计算接到恰好5个电话的概率是 $\Pr(y=5\mid\mu=10)=\frac{e^{-10} \cdot 10^5}{5!}$。
   - $e^{-10}$ 是一个小于1的数，$10^5$ 是10的5次方，$5!$ 是5的阶乘，即 $5 \times 4 \times 3 \times 2 \times 1$。这个公式的结果会给出接到5个电话的概率。
7. **泊松分布的特性**：
   - **分布的移动**：如果平均电话数量从10增加到20，那么接到更多电话的概率会增加。比如，接到15个电话的概率在 $\mu = 10$ 时可能很低，但在 $\mu = 20$ 时会显著增加。
   - **方差与平均数的关系**：在泊松分布中，方差（即数据波动的程度）等于平均数。这意味着如果事件平均发生次数是10，那么它的波动程度也是10。但在实际数据中，我们经常看到方差大于平均数，这被称为过度分散。比如，虽然平均每天接到10个电话，但实际的电话数量可能会在很大的范围内波动。
   - **零事件概率的降低**：随着平均数的增加，事件完全没有发生的概率迅速降低。例如，如果预计每天接到10个电话，那么完全没有接到电话的概率会很低。
   - **泊松分布向正态分布的近似**：当平均数 $\mu$ 很大时，泊松分布看起来越来越像正态分布，即钟形曲线。这意味着事件发生次数的分布变得更加对称和集中。

These ideas are used as we develop regression models for count outcomes in the rest of the chapter.

>Aside: Plotting the Poisson PDF.
>>The commands below were used to create Figure 9.1. The first `generate` creates variable $ k $, which contains the values 0 to 20 that are the counts for which we want to compute probabilities. This is done by subtracting 1 from \_n, where \_n is how Stata refers to the row number of an observation. The probability of outcome $ k $ from a Poisson distribution with mean $ \mu $ is computed with the function `poissonp(mu, k)` for each of four values of $ \mu $.

```
clear all
set obs 21
gen k = _n - 1
label var k "y = # of events"
gen psn1 = poissonp(0.8, k)
label var psn1 "&mu = 0.8"
gen psn2 = poissonp(1.5, k)
label var psn2 "&mu = 1.5"
gen psn3 = poissonp(2.9, k)
label var psn3 "&mu = 2.9"
gen psn4 = poissonp(10.5, k)
label var psn4 "&mu = 10.5"
graph twoway connected psn1 psn2 psn3 psn4 k, ///
    ytitle("Probability") ylabel(0(.1).5) xlabel(0(2)20) ///
    lwidth(thin thin thin thin) msymbol(O D S T)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.1.5.png" style="width:550px;height:370px;" alt="图片描述">
  <figcaption><strong>The Poisson probability density function (PDF) for different rates</strong></figcaption>
</figure>

### 1.1 Fitting the Poisson distribution with the poisson command

To illustrate count models, we use data from Long (1990) on the number of articles written by biochemists in the 3 years prior to receiving their doctorate. The variables considered are:

```
use couart4, clear
codebook art female married kid5 mentor phd, compact
```

    Variable   Obs Unique      Mean   Min   Max  Label
    -------------------------------------------------------------------------------
    art        915     15  1.692896     0    19  Articles in last 3 yrs of PhD
    female     915      2  .4601093     0     1  Gender: 1=female 0=male
    married    915      2  .6622951     0     1  Married: 1=yes 0=no
    kid5       915      4   .495082     0     3  # of kids < 6
    mentor     915     49  8.767213     0    77  Mentor's # of articles
    phd        915     83  3.103109  .755  4.62  PhD prestige
    -------------------------------------------------------------------------------

```
tabulate art, missing
```

    Articles in |
     last 3 yrs |
         of PhD |      Freq.     Percent        Cum.
    ------------+-----------------------------------
              0 |        275       30.05       30.05
              1 |        246       26.89       56.94
              2 |        178       19.45       76.39
              3 |         84        9.18       85.57
              4 |         67        7.32       92.90
              5 |         27        2.95       95.85
              6 |         17        1.86       97.70
              7 |         12        1.31       99.02
              8 |          1        0.11       99.13
              9 |          2        0.22       99.34
             10 |          1        0.11       99.45
             11 |          1        0.11       99.56
             12 |          2        0.22       99.78
             16 |          1        0.11       99.89
             19 |          1        0.11      100.00
    ------------+-----------------------------------
          Total |        915      100.00

Often, the first step in analyzing a count variable is to compare the mean with the variance to determine whether there is overdispersion. By default, **``summarize``** does not report the variance, so we use the **``detail``** option:

```
sum art, detail
```


                    Articles in last 3 yrs of PhD
    -------------------------------------------------------------
          Percentiles      Smallest
     1%            0              0
     5%            0              0
    10%            0              0       Obs                 915
    25%            0              0       Sum of wgt.         915
    
    50%            1                      Mean           1.692896
                            Largest       Std. dev.      1.926069
    75%            2             12
    90%            4             12       Variance       3.709742
    95%            5             16       Skewness        2.51892
    99%            7             19       Kurtosis       15.66293

The variance is more than twice as large as the mean, providing clear evidence of overdispersion.

### 1.2 Comparing observed and predicted counts with mgen

We can visually inspect the overdispersion in a rt by comparing the observed probabilities with those predicted from the Poisson distribution. Later, we will use the same method as a first assessment of the specification of count regression models (section 9.2.5). We begin by using the **`poisson`** command to fit a model with a constant but no independent variables. **When there are no independent variables, **`poisson`** fits a univariate Poisson distribution, where $ \exp(\beta_0) $ equals the mean $ \mu $.** Using **art** as the outcome,

```
poisson art, nolog
```

    Poisson regression                                      Number of obs =    915
                                                            LR chi2(0)    =   0.00
                                                            Prob > chi2   =      .
    Log likelihood = -1742.5735                             Pseudo R2     = 0.0000
    
    ------------------------------------------------------------------------------
             art | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.53       0.03    20.72   0.000         0.48        0.58
    ------------------------------------------------------------------------------

Because $ \hat{\beta}_0 = 0.526 $, the estimated rate is $ \hat{\mu} = \exp(0.526) = 1.693 $, which matches the mean of art obtained with summarize earlier.

In earlier chapters, we used `mgen` to compute predictions as an independent variable changed, holding other variables constant. Although this can be done with count models, as illustrated below, **the `mgen` option `meanpred` creates variables with observed and average predicted probabilities, where the rows correspond to values of the outcome.** The syntax for `mgen` used in this way is:

* **`mgen, meanpred stub(stub) pr(min/max) [options]`**

**where option `pr (min/max)` specifies that we want to create variables with predictions for each of the counts from min to max.** The new variables are:


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.1.6.png" style="width:690px;height:300px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

In a regression model with no independent variables, the predicted probability of $ y = k $ is the same for all observations. Accordingly, mgen is simply computing $ Pr(y = k) $ from a Poisson distribution with a mean equal to the mean of the outcome. In our example,

```
poisson art, nolog
mgen, pr(0/9) meanpred stub(psn)
```

    Predictions from: 
    
    Variable   Obs Unique       Mean        Min       Max  Label
    ---------------------------------------------------------------------------------------
    psnval      10     10        4.5          0         9  Articles in last 3 yrs of PhD
    psnobeq     10     10   .0993443   .0010929  .3005464  Observed proportion
    psnoble     10     10   .8328962   .3005464  .9934426  Observed cum. proportion
    psnpreq     10     10   .0999988   .0000579   .311469  Avg predicted Pr(y=#)
    psnprle     10     10   .8307106   .1839859  .9999884  Avg predicted cum. Pr(y=#)
    psnob_pr    10     10  -.0006546  -.0691068  .1165606  Observed - Avg Pr(y=#)
    ---------------------------------------------------------------------------------------

**`mgen`** created six variables with 10 observations that correspond to the counts 0-9. In the list below, $ p\_sn\_val $ contains the count values, $ psnobeq $ contains observed proportions or probabilities, and $ psnpreq $ has predicted probabilities from a Poisson distribution with mean 1.69:

**`mgen`** 创建了六个变量，每个变量有10个观测值，分别对应计数0-9。在下面的列表中，$ psnval $ 包含计数值，$ psnobeq $ 包含观测到的比例或概率，而 $ psnpreq $ 则有来自均值为1.69的泊松分布的预测概率。
这段话是在描述一个统计模拟过程，其中 **`mgen`** 可能是一个用于生成模拟数据的软件或程序。这里的关键点解释如下：
1. **六个变量**：这意味着 **`mgen`** 生成了六个不同的变量。这些变量可能代表不同的实验组或条件。
2. **每个变量有10个观测值**：每个变量都包含10个观测值，这些观测值可能代表某种事件发生的次数。
3. **计数0-9**：观测值代表事件发生的次数，范围从0（事件没有发生）到9（事件发生了9次）。
4. **`psnval`**：这个变量包含了具体的计数值。例如，如果事件发生了3次，那么 $ psnval $ 中的相应条目就是3。
5. **`psnobeq`**：这个变量包含了观测到的比例或概率。例如，如果有10个观测值中有3个是3，那么 $ psnobeq $ 中对应的概率就是 $ \frac{3}{10} $。
6. **`psnpreq`**：这个变量包含了根据泊松分布预测的概率。泊松分布是一种统计学上用来描述特定时间内发生某事件的次数的概率分布。在这里，泊松分布的均值（即事件的平均发生次数）被设定为1.69。因此，$ psnpreq $ 中的每个条目都是根据泊松公式计算得出的，对应于每个计数值的预测概率。


```
list psnval psnobeq psnpreq in 1/10
```

         +--------------------------------+
         | psnval     psnobeq     psnpreq |
         |--------------------------------|
      1. |      0   .30054645    .1839859 |
      2. |      1   .26885246   .31146902 |
      3. |      2   .19453552   .26364236 |
      4. |      3   .09180328   .14877305 |
      5. |      4   .07322404   .06296433 |
         |--------------------------------|
      6. |      5    .0295082   .02131841 |
      7. |      6   .01857923   .00601498 |
      8. |      7   .01311475   .00145468 |
      9. |      8    .0010929   .00030783 |
     10. |      9   .00218579    .0000579 |
         +--------------------------------+

The values of **psnobeq** match those from **tabulate art** above, except that tabulate shows percentages while **mgen** generates probabilities, which equal the percentages divided by 100. The first row shows that the observed probability of no publications is $0.301$, while the predicted probability from the Poisson distribution is only $0.184$.

Using these variables, we can create a graph that compares the observed probabilities with the predicted probabilities from the Poisson distribution:

```
label var psnobeq "Observed Proportion"
label var psnpreq "Poisson Prediction"
label var psnval "# of Articles"
graph twoway connected psnobeq psnpreq psnval, ///
    ytitle("Probability") ylabel(0(.1).4) xlabel(0/9) msym(O Th) ///
    lwid(thin thin) msize(*1.4 *1.2) mcol(gs1 gs10) // tweaks to remove from text
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.1.7.png" style="width:550px;height:370px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The graph clearly shows that the fitted Poisson distribution underpredicts Os; overpredicts counts 1, 2, and 3; and has smaller underpredictions of larger counts. This pattern of overprediction and underprediction is characteristic of count models that do not adequately account for heterogeneity among observations in their rate $mu$. Because the univariate Poisson distribution assumes that all scientists have exactly the same rate of productivity, which is clearly unrealistic, our next step is to allow heterogeneity in $mu$, based on observed characteristics of the scientists.

## 2 The Poisson regression model

The PRM extends the Poisson distribution by allowing each observation i to have a different rate $ \mu_i $. More formally, the PRM assumes that the observed count for observation i is drawn from a Poisson distribution with mean $ \mu_i $ where $ \mu_i $ is estimated from the independent variables in the model. This is sometimes referred to as incorporating observed heterogeneity and leads to the structural equation

$$\mu_{i}=E\left(y_{i}\mid\mathbf{x_i}\right)=\exp\left(\mathbf{x}_{i}\boldsymbol{\beta}\right)$$

Taking the exponential of $x_i\beta$ forces $\mu_i$ to be positive, which is necessary because counts can be only 0 or positive.

To see how this works, consider the PRM with one independent variable:


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.1.8.png" style="width:550px;height:370px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

the mean $\begin{array}{rcl}\mu&=&\exp\left(\alpha+\beta x\right)\end{array}$ shown by the solid, curved line that increases as x increases. For each value of $\mu$,the Poisson distribution around the mean is shown by the dots, which should be thought of as coming out of the page to represent the probability of each count. Interpretation of the model involves assessing how changes in the independent variables affect the conditional mean and the probabilities of each count. Details on interpretation are given after we consider estimation.

首先，我们知道泊松分布是用来描述在一段时间或空间内发生某事件的次数。在基本的泊松分布中，我们假设这个事件发生的平均速率（mean rate）是固定的，我们用符号 $ \mu $ 来表示它。但是，在现实生活中，这个平均速率可能会因为不同的条件或因素而变化。
这就是泊松回归模型（PRM）的作用。**它扩展了基本的泊松分布，允许每个观测值有自己的平均速率 $ \mu_i $。这意味着，对于不同的观测值，事件发生的平均次数可以是不同的。**
在泊松回归模型中，我们假设每个观测值的计数（比如事件发生的次数）是从一个平均速率为 $ \mu_i $ 的泊松分布中抽取的。这个 $ \mu_i $ 是通过模型中的自变量来估计的。这就是说，我们可以根据不同的条件或因素来预测事件发生的平均次数。
模型的数学方程是 $ \mu_{i} = E(y_{i} | \mathbf{x_i}) = \exp(\mathbf{x}_{i} \boldsymbol{\beta}) $。这里的 $ \exp $ 表示自然指数函数，它的作用是保证 $ \mu_i $ 是正数。这是必要的，因为事件的次数只能是0或正数。
当我们只有一个自变量时，模型可以表示为 $ \mu = \exp(\alpha + \beta x) $。这个方程告诉我们，事件发生的平均次数是随着自变量 $ x $ 的增加而增加的。
图表展示了这个模型是如何工作的。实线表示事件发生的平均次数 $ \mu $，它随着 $ x $ 的增加而上升。围绕着这个平均值的点表示泊松分布，这些点可以被想象成从页面跳出来，代表每个计数的概率。
解释这个模型涉及到评估自变量的变化如何影响事件的平均次数和每个计数的概率。

### 2.1 Estimation using poisson

The PRM is fit with the command

* **``poisson depvar [ indepvars ] [if] [ m ] [ weight ] i , noconstant exposure(.varnarne) vce(vcetype) irr ]``**

In our experience, poisson converges quickly and difficulties are rare.

* **Variable lists**

**``depvar``** is the dependent variable, **``poisson``** does not require this to be an integer;
however, if you have noninteger values, you obtain the following warning:
>**note:** you are responsible for interpretation of noncount dep. variable.

`indepvars` is a list of independent variables. If `indepvars` is not included, a model with only an intercept is estimated that fits a univariate Poisson distribution, as shown in the previous section.

* **Specifying the estimation sample**

**`if and in qualifiers.`** if and in qualifiers can be used to restrict the estimation sample. For example, if you want to fit a model for only women, you could specify `poisson depvar indepvars if female == 1`.

**Listwise deletion**: **Stata excludes observations with missing values for any of the variables in the model.** Accordingly, if two models are estimated using the same data but have different independent variables, it is possible to have different samples. As discussed in chapter 3, we recommend that you explicitly remove observations with missing data.

* **Weights and complex samples**
`poisson` can be used with `fweights`, `pweights`, and `iweights`. Survey estimation for complex samples is possible using svy. See chapter 3 for details.

* **Option**

**`noconstant`** suppresses the constant term or intercept in the model

**`exposure(varname)`** specifies a variable indicating **the amount of time during which an observation was "at risk" of the event occurring.** Details are given in section 9.2.6.

**`vce(vcetype)`** specifies the type of standard errors to be computed, **`vce(robust)`** requests that robust variance estimates be used. See sections 3.1.9 and 9.3.5 for details.

**`irr`** reports estimated coefficients that are transformed to incidence-rate ratios defined as $\exp(\beta_k)$. These are discussed in section 9.2.2.

* **Example of the PRM**

If scientists who differ in their rates of productivity are combined, the univariate distribution of articles will be overdispersed, with the variance greater than the mean. Differences among scientists in their rates of productivity could be due to factors such as quality of their graduate program, gender, marital status, number of young children, and the number of articles written by a scientist’s mentor. To account for these differences, we add these variables as independent variables:

```
use couart4, clear
poisson art i.female i.married kid5 phd mentor, nolog
```

    Poisson regression                                      Number of obs =    915
                                                            LR chi2(5)    = 183.03
                                                            Prob > chi2   = 0.0000
    Log likelihood = -1651.0563                             Pseudo R2     = 0.0525
    
    ------------------------------------------------------------------------------
             art | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
          female |
         Female  |      -0.22       0.05    -4.11   0.000        -0.33       -0.12
                 |
         married |
        Married  |       0.16       0.06     2.53   0.011         0.03        0.28
            kid5 |      -0.18       0.04    -4.61   0.000        -0.26       -0.11
             phd |       0.01       0.03     0.49   0.627        -0.04        0.06
          mentor |       0.03       0.00    12.73   0.000         0.02        0.03
           _cons |       0.30       0.10     2.96   0.003         0.10        0.51
    ------------------------------------------------------------------------------

How you interpret a count model depends on whether you are interested in 
1) the expected value or rate of the count outcome  
2) the distribution of counts. 
* If your interest is in the rate of occurrence, several methods can be used to compute the change in the rate for a change in an independent variable, holding other variables constant. 

* If your interest is in the distribution of counts or perhaps the probability of a specific count, such as not publishing, the probability of a count for a given level of the independent variables can be computed. We begin with interpretation using rates.

### 2.2 Factor and percentage changes in E(y|x)

In th e PRM,

$$\mu=E\left(y\mid\mathbf{x}\right)=\exp\left(\mathbf{x}\beta\right)$$

The changes in mu as an independent variable changes can be presented in several ways. Factor change and percentage change coefficients are counterparts to odds ratios that were discussed in previous chapters. In those chapters, we expressed reservations about the usefulness of interpreting coefficients that indicated changes in the odds. As we will explain shortly, the interpretation of factor and percentage change coefficients in count models is much clearer and more useful.

Perhaps the most common method of interpretation is the factor change in the rate.Let $E\left(y\mid\mathbf{x},x_{k}\right)$ be the expected count for a given x, where we explicitly note the value of $x_k$,and let $E\left(y\mid\mathbf{x},x_{k}+1\right)$ be the expected count after increasing $x_k$ by 1.Simple algebra shows that the ratio is

$$\frac{E\left(y\mid\mathbf{x},x_k+1\right)}{E\left(y\mid\mathbf{x},x_k\right)}=e^{\beta_k}$$

Therefore,

>Factor change: For a unit change in $x_k$, the expected count changes by a factor of $\exp(\beta_k)$, holding other variables constant.

In some discussions of count models, $\mu$ is referred to as the incidence rate and Equation (9.1) is called the incidence-rate ratio. These coefficients are shown by `poisson` by adding the option `irr` or by using the `listcoef` command, which is illustrated below. We can easily generalize Equation (9.1) to changes in $X_k$ of any amount $\delta$:

$$\frac{E\left(y\mid\mathbf{x},x_k+\delta\right)}{E\left(y\mid\mathbf{x},x_k\right)}=e^{\beta_k\delta}$$

This leads to interpretations such as the following:

>Factor change for $\delta$: For a change of $\delta$ in $X_k$, the expected count changes by a factor of $\exp(\beta_k \delta)$, holding other variables constant.

Standardized factor change: For a standard deviation change in $x_k$, the expected rate changes by a factor of $\exp(\beta_k s_k)$, holding other variables constant.

Alternatively, we can compute the percentage change in the expected count for a $\delta$-unit change in $x_k$, holding other variables constant:

$$100\times\frac{E\left(y\mid\mathbf{x},x_k+\delta\right)-E\left(y\mid\mathbf{x},x_k\right)}{E\left(y\mid\mathbf{x},x_k\right)}=100\times\left(e^{\beta_k\delta}-1\right)$$

which can be interpreted as follows:
>Percentage change for $\delta$: For a change of $\delta$ in $x_k$, the expected count changes by $100 \times \left(e^{\beta_k \delta} - 1\right)\%$, holding other variables constant.

Whether you use percentage or factor change is a matter of taste and convention in your area of research.


首先，原文提到：
在泊松回归模型（PRM）中，
$$\mu=E\left(y\mid\mathbf{x}\right)=\exp\left(\mathbf{x}\beta\right)$$
这里，$\mu$ 表示给定自变量 $\mathbf{x}$ 时因变量 $y$ 的期望值，它通过向量 $\mathbf{x}$ 和参数向量 $\beta$ 的乘积的指数函数来计算。
接下来，文中讨论了期望计数随独立变量的变化而变化的方式，提到了因子变化和百分比变化系数，这与之前章节讨论过的几率比相对应。在泊松回归模型中，解释因子和百分比变化系数要比解释表示概率变化的优势比系数更加清晰和有用。
现在让我们分解一个关键公式：
$$\frac{E\left(y\mid\mathbf{x},x_k+1\right)}{E\left(y\mid\mathbf{x},x_k\right)}=e^{\beta_k}$$
这个公式表示，当我们把变量 $x_k$ 的值增加1时，期望计数 $E(y|\mathbf{x},x_k)$ 的变化比率是 $e^{\beta_k}$。因此，如果 $\beta_k$ 是2，那么 $e^{\beta_k}$ 就是 $e^2$，大约是7.39。这意味着，保持其他变量不变的情况下，$x_k$ 增加1个单位，期望计数会增加大约7.39倍。
文中还提到了一个更一般的情况，即变量 $x_k$ 的变化量为 $\delta$：
$$\frac{E\left(y\mid\mathbf{x},x_k+\delta\right)}{E\left(y\mid\mathbf{x},x_k\right)}=e^{\beta_k\delta}$$
这个公式告诉我们，对于 $x_k$ 的任意变化量 $\delta$，期望计数的变化比率是 $e^{\beta_k\delta}$。
接下来，文中提到了如何计算 $x_k$ 变化 $\delta$ 单位时的期望计数的百分比变化：
$$100\times\frac{E\left(y\mid\mathbf{x},x_k+\delta\right)-E\left(y\mid\mathbf{x},x_k\right)}{E\left(y\mid\mathbf{x},x_k\right)}=100\times\left(e^{\beta_k\delta}-1\right)$$
这个公式表示，对于 $x_k$ 的 $\delta$ 单位变化，期望计数的百分比变化是 $100 \times (e^{\beta_k\delta} - 1)\%$。
最后，文中指出，使用百分比变化还是因子变化取决于你所在研究领域内的习惯和偏好。

让我们通过一个具体的例子来解释这些概念。假设你正在研究某个城市中餐馆的日客流量，你想了解不同因素如何影响客流量。你收集了数据，包括餐馆的位置（城市中心或郊区）、餐馆的大小（小型、中型或大型）以及是否提供外卖服务。你的泊松回归模型可能如下所示：
$$\log(\mu) = \beta_0 + \beta_1 \times \text{城市中心} + \beta_2 \times \text{餐馆大小} + \beta_3 \times \text{提供外卖}$$
这里，$\mu$ 是日客流量的期望值，$\beta_0, \beta_1, \beta_2, \beta_3$ 是模型参数，"城市中心"、"餐馆大小"和"提供外卖"是二进制变量（0或1）。
现在，假设你的模型估计得到 $\beta_3 = 0.5$，这意味着如果餐馆提供外卖服务，其日客流量期望值会乘以 $e^{0.5}$。我们可以计算这个值：
$$e^{0.5} \approx 1.6487$$
所以，如果其他因素保持不变，提供外卖服务的餐馆日客流量期望值会增加大约64.87%。
接下来，我们考虑百分比变化。如果餐馆开始提供外卖服务，我们想要计算日客流量的百分比变化。根据上面的公式，我们有：
$$100 \times \left(e^{\beta_3} - 1\right) = 100 \times (1.6487 - 1) = 64.87\%$$
这意味着，如果餐馆开始提供外卖服务，我们期望其日客流量会增加大约64.87%。
最后，如果我们想知道餐馆大小对客流量的影响，我们可以考虑一个标准差的变化。假设餐馆大小的标准差是 $s_2$，那么 $\beta_2 s_2$ 会给出因子变化。如果 $\beta_2 = 0.3$ 并且 $s_2 = 1$（例如，餐馆大小是从小型到中型的一个标准差变化），那么因子变化是：
$$e^{\beta_2 s_2} = e^{0.3 \times 1} \approx 1.3499$$
这意味着，如果餐馆大小增加一个标准差（例如，从小型到中型），我们期望日客流量会增加大约34.99%。
这些例子展示了如何使用泊松回归模型来解释不同因素对日客流量的影响。通过这种方式，你可以清楚地看到每个因素如何以因子或百分比变化的形式影响结果。



**让我们再举一个例子，这次我们将使用一个更具体的场景来解释因子变化和百分比变化。**

假设你正在研究影响医院急诊室患者数量的因素。你收集的数据包括一天中的时间（白天或晚上）、天气状况（晴朗或多云）以及是否是周末。你的泊松回归模型可能如下所示：
$$\log(\mu) = \beta_0 + \beta_1 \times \text{白天} + \beta_2 \times \text{多云} + \beta_3 \times \text{周末}$$
这里，$\mu$ 是患者数量的期望值，$\beta_0, \beta_1, \beta_2, \beta_3$ 是模型参数，"白天"、"多云"和"周末"是二进制变量（0或1）。
假设你的模型估计得到 $\beta_1 = -0.5$，这意味着白天相对于晚上，急诊室患者数量的期望值会乘以 $e^{-0.5}$。我们可以计算这个值：
$$e^{-0.5} \approx 0.6065$$
所以，如果其他因素保持不变，白天的急诊室患者数量期望值会减少到晚上的约60.65%。
接下来，我们考虑百分比变化。如果我们要计算从晚上到白天患者数量的百分比变化，根据上面的公式，我们有：
$$100 \times \left(e^{\beta_1} - 1\right) = 100 \times (0.6065 - 1) = -39.35\%$$
这意味着，如果其他因素保持不变，白天的急诊室患者数量期望值会减少大约39.35%。
现在，让我们考虑天气状况的影响。假设 $\beta_2 = 0.3$，这意味着多云相对于晴朗，急诊室患者数量的期望值会乘以 $e^{0.3}$。我们可以计算这个值：
$$e^{0.3} \approx 1.3499$$
所以，如果其他因素保持不变，多云的天气下急诊室患者数量的期望值会增加大约34.99%。
最后，我们考虑周末的影响。假设 $\beta_3 = 0.2$，这意味着周末相对于工作日，急诊室患者数量的期望值会乘以 $e^{0.2}$。我们可以计算这个值：
$$e^{0.2} \approx 1.2214$$
所以，如果其他因素保持不变，周末的急诊室患者数量的期望值会增加大约22.14%。
这些例子展示了如何使用泊松回归模型来解释不同因素对急诊室患者数量的影响。通过这种方式，你可以清楚地看到每个因素如何以因子或百分比变化的形式影响结果。

* **Example of factor and percentage change**
Factor change coefficients can be com puted using **`listcoef`** :

```
listcoef female mentor, help
```

    poisson (N=915): Factor change in expected count 
    
      Observed SD:  1.9261
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|       e^b   e^bStdX     SDofX
    -------------+----------------------------------------------------------
          female |
         Female  |   -0.2246   -4.112    0.000     0.799     0.894     0.499
          mentor |    0.0255   12.733    0.000     1.026     1.274     9.484
    ------------------------------------------------------------------------
           b = raw coefficient
           z = z-score for test of b=0
       P>|z| = p-value for z-test
         e^b = exp(b) = factor change in expected count for unit increase in X
     e^bStdX = exp(b*SD of X) = change in expected count for SD increase in X
       SDofX = standard deviation of X

By default, **`listcoef`** will show all coefficients, including the constant. We typed the command **`listcoef female mentor`** to select coefficients for only these variables. The coefficients can be interpreted as follows:

>Being a female scientist decreases the expected number of articles by a factor of 0.80, holding other variables constant.

>For a standard deviation increase in the mentor’s productivity, roughly 9.5 articles, a scientist’s expected productivity increases by a factor of 1.27, holding other variables constant.

To compute the percentage change, we add the option **`percent`**:

```
listcoef female mentor, percent help
```


    poisson (N=915): Percentage change in expected count 
    
      Observed SD:  1.9261
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|         %     %StdX     SDofX
    -------------+----------------------------------------------------------
          female |
         Female  |   -0.2246   -4.112    0.000     -20.1     -10.6     0.499
          mentor |    0.0255   12.733    0.000       2.6      27.4     9.484
    ------------------------------------------------------------------------
           b = raw coefficient
           z = z-score for test of b=0
       P>|z| = p-value for z-test
           % = percent change in expected count for unit increase in X
       %StdX = percent change in expected count for SD increase in X
       SDofX = standard deviation of X

These can be interpreted as follows:

>Being a female scientist decreases the expected number of articles by 20%, holding other variables constant.

>For every additional article by the mentor, a scientist’s expected productivity increases by 2.6%, holding other variables constant.

The standardized percentage change coefficient can be interpreted as follows:

>For a standard deviation increase in the mentor’s productivity, a scientist’s expected productivity increases by 27.4%, holding other variables constant.

Factor or percentage change coefficients are quite effective for explaining the effect of variables in the PRM. **Because the model is nonlinear, the specific amount of change in mu depends on the levels of all variables in the model, just like the odds in earlier chapters. However, because a multiplicative change in the rate is substantively much clearer than a multiplicative change in the odds, using factor change coefficients to interpret count models is more effective than the odds ratios we used in previous chapters.** To give an example, suppose that $ \exp(\beta_x) = 2 $. In the PRM, we would say that for a unit increase in $ x $, the expected number of publications doubles, holding other variables constant. If given a scientist’s characteristics, her rate was $ \mu = 1 $, the rate becomes $ \mu = 2 $. If her rate was 2, it becomes 4, and so on. It is easy to understand the effect of $ x $. If, however, we say the odds were 1 and become 2, it is not immediately obvious (for most of us) how the probability changes.

Because factor and percentage changes in the rate are an effective method for interpreting count models, we find it less important to use marginal effects. Still, they can be useful in applications when you are interested in providing a sense of the absolute amount of change in the rate. Accordingly, we consider marginal effects next.

### 2.3 Marginal effects on E(y | x)

**Marginal effects indicate the change in the rate for a given change in one independent variable, holding all other variables constant.** As with the models in earlier chapters, we can compute the marginal change that indicates the rate of change in $ E(y | x) $ for an infinitely small change in $ x_k $ or a discrete change that indicates the amount of change in $ E(y | x) $ for a discrete change in $ x_k $.

For the PRM, the marginal change in $ E(y | x) $ = $\mu$ equals

$$\frac{\partial E\left(y\mid\mathbf{x}\right)}{\partial x_{k}}=E\left(y\mid\mathbf{x}\right)\beta_{k}$$

The marginal change depends on both $\beta_k$ and $E(y|x)$. For $\beta_k > 0$, the larger the current value of $E(y | x)$, the larger the rate of change; for $\beta_k < 0$, the smaller the rate of change.Because $E(y | x)$ depends on the levels of all variables in the model, the size of the marginal change also depends on the levels of all variables. The marginal change can be computed at the mean or at other representative values. Alternatively, the average marginal change over all the observations in the sample can be computed.

A discrete change is the change in the rate as $ x_k $ changes from $ x_k^{\text{start}} $ to $ x_k^{\text{end}} $, while holding other variables constant:

$$\frac{\Delta E\left(y\mid\mathbf{x}\right)}{\Delta x_k\left(x_k^{\mathrm{start}}\to x_k^{\mathrm{end}}\right)}=E\left(y\mid\mathbf{x},x_k=x_k^{\mathrm{end}}\right)-E\left(y=1\mid\mathbf{x},x_k=x_k^{\mathrm{start}}\right)$$

Different am ounts of change can be computed depending on your purpose:

* The effect of a binary variable $ x_k $ is computed by letting $ x_k $ change from 0 to 1.
* The effect of an uncentered change of 1 in $ x_k $ is computed by changing from the observed $ x_{ik} $ to $ x_{ik} + 1 $. A centered change is computed by changing from $ x_{ik} - \left(1/2\right) $ to $ x_{ik} + \left(1/2\right) $. Change can also be computed from other values of $ x_k $, such as the mean. Then, the uncentered unit change in $ x_k $ is from $\overline{x}_k$ to $\overline{x}_k + 1$ The centered discrete change is the result of the change from $\overline{x}_k-\left(1/2\right)$ to $\overline{x}_k+\left(1/2\right)$ 
* The effect of an uncentered change of $ \delta $, where $ \delta $ might be the standard deviation of $ x_k $, is computed by changing from $ x_{ik} $ to $ x_{ik} + \delta $. The centered change is computed by changing from $ x_{ik} - \left(\frac{\delta}{2}\right) $ to $ x_{ik} + \left(\frac{\delta}{2}\right) $. Change can also be computed from values of $ x_k $ other than the observed value, such as the mean. Then, the uncentered change of $\delta$ in $ x_k $ is from $ \overline{x}_k $ to $ \overline{x}_k + \delta $ and the centered change is from $ \overline{x}_k - \left(\frac{\delta}{2}\right) $ to $ \overline{x}_k + \left(\frac{\delta}{2}\right) $. Changes of a standard deviation or some other value may be particularly useful when the scale of $ x_k $ is very large or very small. For example, when the independent variable is a proportion, a unit change would be at least as large as the entire range of the variable. When the range of $ x_k $ is large, the effect of a unit change can be quite small.
* The total possible effect of $ x_k $ is found by letting $ x_k $ change from its minimum to its maximum. Trimmed ranges can also be used and may be particularly useful when the distribution of $ x_k $ is highly skewed or contains outliers.

在统计学中，特别是在回归分析中，边际效应是指一个自变量（解释变量）发生单位变化时，因变量（响应变量）期望值的平均变化量。在泊松回归模型中，我们关注的是计数数据，比如一天内发生的事件次数。
* 边际效应的连续变化

在泊松回归模型中，期望计数 $\mu$ 的表达式是：
$$\mu=E\left(y\mid\mathbf{x}\right)=\exp\left(\mathbf{x}\beta\right)$$
这里的 $\mathbf{x}$ 是自变量的向量，$\beta$ 是相应的参数向量。如果我们想知道 $\mathbf{x}$ 中某个特定变量 $x_k$ 的变化对 $\mu$ 的影响，我们可以计算 $\mu$ 对 $x_k$ 的导数，这就是边际效应：
$$\frac{\partial E\left(y\mid\mathbf{x}\right)}{\partial x_{k}}=E\left(y\mid\mathbf{x}\right)\beta_{k}$$
这个公式的意思是，当 $x_k$ 发生微小变化时，$\mu$ 的变化量是 $E(y|\mathbf{x})$ 乘以 $\beta_k$。这里的 $\beta_k$ 是 $x_k$ 的系数，它告诉我们 $x_k$ 每变化一个单位，$\mu$ 会变化多少倍。如果 $\beta_k$ 是正数，那么 $x_k$ 增加1个单位，$\mu$ 就会增加；如果 $\beta_k$ 是负数，$\mu$ 就会减少。
* 边际效应的离散变化

有时候，我们更关心当 $x_k$ 发生一个离散的变化时，比如从1变成2，$\mu$ 会发生多大的变化。这种情况下，我们可以直接计算 $\mu$ 在 $x_k$ 变化前后的差值：
$$\Delta E\left(y\mid\mathbf{x}\right) = E\left(y\mid\mathbf{x},x_k=x_k^{\text{end}}\right) - E\left(y\mid\mathbf{x},x_k=x_k^{\text{start}}\right)$$
这个公式的意思是，当 $x_k$ 从一个值 $x_k^{\text{start}}$ 变化到另一个值 $x_k^{\text{end}}$ 时，$\mu$ 的变化量是两个期望值之差。
* 例子

让我们用一个简单的例子来说明。假设你在研究影响人们每周购买咖啡次数的因素。你的泊松回归模型可能包括年龄、收入和是否有咖啡机等变量。假设你有以下模型：
$$\log(\mu) = \beta_0 + \beta_1 \times \text{年龄} + \beta_2 \times \text{收入} + \beta_3 \times \text{有咖啡机}$$
假设 $\beta_1 = 0.1$，这意味着年龄每增加1岁，购买咖啡的期望次数会增加 $e^{0.1}$ 倍。我们可以计算这个值：
$$e^{0.1} \approx 1.1052$$
这意味着，如果其他因素保持不变，年龄增加1岁，购买咖啡的期望次数会增加大约10.52%。
现在，假设我们想要计算年龄从30岁增加到31岁时，购买咖啡的期望次数的具体变化。我们可以使用离散边际变化的公式：
$$\Delta E(y|\mathbf{x}) = E(y|\mathbf{x}, \text{年龄}=31) - E(y|\mathbf{x}, \text{年龄}=30)$$
如果 $\beta_1 = 0.1$，那么期望购买咖啡次数的变化率将是 $e^{0.1} \approx 1.1052$ 倍，即年龄每增加1岁，期望购买咖啡次数增加约10.52%。
这个例子展示了如何使用泊松回归模型来解释年龄的变化如何影响购买咖啡的期望次数，同时保持其他变量不变。通过这种方式，你可以清楚地看到每个变量如何以边际效应的形式影响结果。

文中提到的四种计算方式，分别适用于不同的研究目的和场景。
1. **计算二进制变量的效应**：
   当一个自变量是二进制的（比如，是否有某个特征），我们通常感兴趣的是这个特征从无到有的变化对因变量的影响。例如，在研究消费者购买行为的模型中，我们可能想知道拥有会员卡（0表示没有，1表示有）是否影响购买频率。
   - **例子**：假设我们的模型中，拥有会员卡的系数 $\beta_{\text{会员卡}} = 0.3$。那么，从没有会员卡到有会员卡，购买频率的期望变化是 $e^{0.3} \approx 1.3499$ 倍，即拥有会员卡会使购买频率增加大约34.99%。
2. **计算未中心化的单位变化效应**：
   这适用于自变量是连续的情况，我们想知道当这个变量增加1个单位时，因变量如何变化。例如，研究工资增长对消费水平的影响时，我们可能想知道工资每增加1单位，消费水平会如何变化。
   - **例子**：假设工资的系数 $\beta_{\text{工资}} = 0.2$。如果一个人的工资增加1单位，那么消费水平的期望变化是 $e^{0.2} \approx 1.2214$ 倍，即工资每增加1单位，消费水平增加大约22.14%。
3. **计算已中心化的变化效应**：
   有时候，我们想要计算的是自变量相对于其平均值的变化效应。这通常通过将变化量分成两部分来实现，每部分是变化量的一半，分别在平均值以下和以上进行计算。这种方法可以减少异常值的影响。
   - **例子**：假设我们研究的是年龄对购买健康保险的影响，年龄的系数 $\beta_{\text{年龄}} = 0.05$，平均年龄是40岁。我们想要计算的是年龄从40岁增加到41岁的影响。我们可以先计算从39.5岁增加到40岁的影响，然后计算从40岁增加到40.5岁的影响，最后将这两个影响合并。
4. **计算总可能效应**：
   这种方法考虑的是自变量在其可能范围内的最大变化。例如，如果我们研究的是教育水平对收入的影响，我们可能想要知道从最低教育水平到最高教育水平的影响。
   - **例子**：假设教育年限的系数 $\beta_{\text{教育}} = 0.8$。如果我们想要计算从小学教育（6年）到博士教育（20年）的影响，我们可以计算这个范围内的总变化，或者只考虑教育年限的一个标准差的变化。

这些不同的计算方法可以根据你的研究目的和数据的特性来选择。它们都提供了不同的视角来理解自变量对因变量的影响。


* **Examples of marginal effects**
Marginal effects can be computed using mchange. For example, the average margina effects are

```
poisson art i.female i.married kid5 phd mentor, nolog
mchange
```

    poisson: Changes in mu | Number of obs = 915
    
    Expression: Predicted number of art, predict()
    
                       |    Change    p-value 
    -------------------+---------------------
    female             |                     
        Female vs Male |    -0.375      0.000 
    married            |                     
     Married vs Single |     0.256      0.010 
    kid5               |                     
                    +1 |    -0.286      0.000 
                   +SD |    -0.223      0.000 
              Marginal |    -0.313      0.000 
    phd                |                     
                    +1 |     0.022      0.629 
                   +SD |     0.022      0.629 
              Marginal |     0.022      0.627 
    mentor             |                     
                    +1 |     0.044      0.000 
                   +SD |     0.464      0.000 
              Marginal |     0.043      0.000 
    
    Average prediction
    
        1.693 

Because **female** and **married** were entered using the factor-variable notation **i.female** and **i.married**, the discrete change from 0 to 1 was computed and can be interpreted as follows:

>On average, for scientists similar on other characteristics, being female decreases the expected number of publications by 0.38 articles. Being married, on the other hand, increases the expected number of articles by 0.26 on average. Both effects are significant at the 0.001 level.

The most reasonable effect for the number of young children is an increase of 1 from the actual number of children a scientist has, which can be interpreted as follows:

```
mchange kid5, amount(one) brief
```

    poisson: Changes in mu | Number of obs = 915
    
    Expression: Predicted number of art, predict()
    
                 |    Change    p-value 
    -------------+---------------------
    kid5         |                     
              +1 |    -0.286      0.000 
    
>On average, increasing the number of young children in the family by 1 decreases the expected rate of productivity by 0.29, holding other variables at their observed values.

Because the effect of doctoral prestige is not significant (given that our model includes the prestige of the mentor that is associated with the prestige of the department), we do not consider this variable further. The productivity of the mentor can be interpreted as a continuous variable. Consider the discrete change of 1:

>On average, increasing the mentor's number of papers by 1 is expected to increase a scientist's productivity by 0.04 papers.

The effect is small because an increase of one paper is tiny relative to the range of 77 for mentor's productivity. The effect of a standard deviation change gives a better sense of the effect of the mentor:

>A standard deviation change in the mentor's productivity, roughly 10 papers, on average increases a scientist's expected rate of productivity by 0.46, holding other variables at their observed values.

The average marginal change could also be used to interpret the effect of the mentor's productivity. For example:

>The average rate of change for the productivity of the mentor is 0.04 (p < 0.001). The effect of departmental prestige is not significant.

In this example, the marginal change and the discrete change of 1 for the mentor are nearly identical, reflecting that the curve for the expected number of publications is approximately linear within a 1-unit change around the observed values.

### 2.4 Interpretation using predicted probabilities

The estimated parameters can also be used to compute predicted probabilities with the formula

$$\widehat{\Pr}(y=k\mid\mathbf{x})=\frac{e^{-\mathbf{x}\widehat{\beta}}\left(\mathbf{x}\widehat{\beta}\right)^k}{k!}$$

Predictions at the observed values for all observations can be made using **`predict`**. Probabilities at specified values or average predicted probabilities can be computed using **`margins`** or **`mtable`**. Changes in the probabilities can be computed with **`mchange`**, and plots can be made using **`mgen`**.

* **Predicted probabilities using mtable and mchange**

```
mtable, at(married=(0 1) female=1 kid5=0) atmeans pr(0/5) width(7)
```

    Expression: Pr(art), predict(pr())
    
               | married        0        1        2        3        4        5
     ----------+--------------------------------------------------------------
             1 |       0    0.244    0.344    0.243    0.114    0.040    0.011
             2 |       1    0.193    0.317    0.261    0.143    0.059    0.019
    
    Specified values of covariates
    
               |  female     kid5      phd   mentor
     ----------+-----------------------------------
       Current |       1        0      3.1     8.77

Row 1 has predictions for those who are not married (that is, married=0), while row 2 has predictions for those who are married. Individuals who are not married have higher probabilities for zero and one publications, while married women have higher probabilities for two or more publications. The values of variables that are held constant are shown below the predictions.

We can use `mchange` to compute and test differences between women who are single and those who are married, using the option **`stat(from to change p)`** to request the starting prediction, the ending prediction, the change, and the p-value for the test that the effect is 0:

```
mchange married, at(female=1 kid5=0) atmeans pr(0/5) ///
    stat(from to change p) width(7) brief
```

    poisson: Changes in Pr(y) | Number of obs = 915
    
    Expression: Pr(art), predict(pr())
    
                       |       0        1        2        3        4        5 
    -------------------+-----------------------------------------------------
    married            |                                                     
                  From |   0.244    0.344    0.243    0.114    0.040    0.011 
                    To |   0.193    0.317    0.261    0.143    0.059    0.019 
     Married vs Single |  -0.051   -0.027    0.019    0.029    0.019    0.008 
               p-value |   0.011    0.014    0.014    0.011    0.013    0.017 

The rows **From** and **To** correspond exactly to the predictions for unmarried and married women in the earlier `mtable` output, while the row **Married vs Single** contains the discrete changes. The results show that the differences in the predicted probabilities are all statistically significant at the 0.05 level but not the 0.01 level.

In the prior example, we specified the values of all the independent variables and examined how predictions differed when the variable **married** was changed from 0 to 1. The result was a discrete change at a representative value. In our next example, we consider female scientists who are married, and we compute the average rate of productivity and average predicted probabilities if we assume these women all have no children, all have one child, all have two children, and so on. Because we are focusing on married women, we average the predictions over the subsample of married women rather than the full estimation sample. We do this by adding the condition **if married==1 & female==1** to `mtable`. We begin by computing average rates assuming different numbers of children:

```
quietly mtable if married==1 & female==1, at(kid5=(0/3)) long
```

By default, **`mtable`** uses the wide format when reporting rates. Here we use the **long** option so that we can combine the rates with predicted probabilities that are by default shown in **long** format. The **norownumbers** option suppresses row numbers for the predictions.

```
mtable if married==1 & female==1, pr(0/5) ///
    at(kid5=(0/3)) atvars(_none) right norownumbers brief
```

    Expression: Pr(art), predict(pr())
    
        kid5        mu         0         1         2         3         4         5
    ------------------------------------------------------------------------------
           0     1.656     0.205     0.315     0.249     0.136     0.059     0.022
           1     1.376     0.266     0.343     0.227     0.105     0.039     0.013
           2     1.144     0.331     0.357     0.199     0.078     0.025     0.007
           3     0.951     0.398     0.358     0.168     0.056     0.015     0.004

The column **mu** shows that if all married women were assumed to have no children, their average rate of productivity is 1.60 papers. Assuming all of these women have one child, the rate drops to 1.38, then 1.14 with two children, and 0.95 with three. Overall, as the number of young children increases, the rate of publication decreases by about 0.7, while the average probability of no publications increases from 0.21 to 0.40. We also see that having more young children increases the chances of having one publication and decreases the probabilities of more publications. These results are consistent with the negative effect of **kid5** in the model.

* **Treating a count independent variable as a factor variable**

The number of young children is itself a count variable, although it is an independent variable rather than the outcome. In the PRM that we fit, the estimated coefficient for **kid5** was $β_{kid5} = -0.185$, leading to the factor change in the rate of $exp(β_{kid5}) = 0.83$. This means the following:

>For a unit increase in the number of children, the rate of productivity is expected to decline by a factor of 0.83, holding other variables constant.

The same rate of decline applies for the change from zero children to one child, from one child to two children, or from two to three children. It is possible, however, that the rate of change is not constant. For example, the impact of the first child could be greater than that of later children. To allow for this possibility, we can include **kid5** as a set of indicator variables by entering the variable with the factor-variable notation **i.kid5**. Fitting this model,

```
poisson art i.kid5 i.female i.married phd mentor, nolog
```

    Poisson regression                                      Number of obs =    915
                                                            LR chi2(7)    = 184.31
                                                            Prob > chi2   = 0.0000
    Log likelihood = -1650.4198                             Pseudo R2     = 0.0529
    
    ------------------------------------------------------------------------------
             art | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            kid5 |
              1  |      -0.18       0.07    -2.53   0.011        -0.32       -0.04
              2  |      -0.33       0.09    -3.61   0.000        -0.51       -0.15
              3  |      -0.82       0.28    -2.92   0.004        -1.37       -0.27
                 |
          female |
         Female  |      -0.23       0.05    -4.12   0.000        -0.33       -0.12
                 |
         married |
        Married  |       0.15       0.06     2.39   0.017         0.03        0.27
             phd |       0.01       0.03     0.48   0.630        -0.04        0.06
          mentor |       0.03       0.00    12.53   0.000         0.02        0.03
           _cons |       0.31       0.10     2.98   0.003         0.11        0.51
    ------------------------------------------------------------------------------

There are three indicator variables for the number of children, with zero children being the excluded category. We use the `mtable` commands from the last section to compute our predictions:

```
quietly mtable if married==1 & female==1, at(kid5=(0/3)) long
mtable if married==1 & female==1, pr(0/5) at(kid5=(0/3)) ///
    atvars(_none) right norownumbers brief
```

    Expression: Pr(art), predict(pr())
    
        kid5        mu         0         1         2         3         4         5
    ------------------------------------------------------------------------------
           0     1.648     0.207     0.316     0.249     0.135     0.059     0.022
           1     1.379     0.266     0.342     0.228     0.106     0.039     0.013
           2     1.187     0.318     0.355     0.205     0.083     0.027     0.008
           3     0.725     0.493     0.342     0.124     0.032     0.007     0.001

There is a noticeable difference between the two models in predictions for those with three children; however, the Bayesian information criterion (BIC) statistics for the two models show very strong support for the model where **kid5** is treated as a continuous variable (ΔBIC = 12.36).

In a similar vein, we might hypothesize that the effect of the mentor’s productivity on a scientist’s productivity decreases as the mentor’s productivity increases (that is, there are diminishing returns to each additional article by the mentor). We could either use a squared term to capture this (`c.mentor##c.mentor`) or log the mentor’s number of publications before including it in the model. The larger point is that while count models are nonlinear, we still need to work at specifying this nonlinearity correctly, which requires thinking not just about the left-hand side of the model but also about the relationships implied by how we specify variables on the right-hand side of the model.

在分析科学家的生产力时，如何处理导师的生产力对科学家生产力的影响。作者提出了一个假设，即随着导师生产力的增加，其对科学家生产力的影响可能会减小（即存在边际效益递减的现象）。为了在模型中捕捉这种递减效应，有两种常见的方法：

1. **使用平方项**：在回归模型中包含导师生产力的平方项（`c.mentor##c.mentor`）。这样做可以捕捉到生产力对生产力影响的非线性关系，即生产力增加到一定程度后，其对科学家生产力的影响增长速度会放缓。
   
2. **对导师的出版物数量取对数**：在将导师的出版物数量纳入模型之前，先对其数量取对数。对数变换是一种常用的方法，可以将非线性关系转换为线性关系，使得模型能够更好地捕捉边际效益递减的现象。

作者强调，虽然计数模型本身是非线性的，但我们仍然需要努力正确地指定这种非线性关系。这不仅仅涉及到模型左侧的因变量，还涉及到模型右侧自变量的关系设定。这意味着，在构建模型时，我们需要仔细考虑自变量之间的相互作用以及它们如何共同影响因变量。简而言之，模型的形式和自变量的选择对于解释因变量的变化至关重要。

* **Predicted probabilities using mgen**

The **`mgen`** command computes a series of predictions by holding all variables but one constant (unless there are linked variables such as age and age-squared). The resulting predictions can then be plotted. To provide an example, we plot the predicted probability of not publishing for married men and married women with different numbers of children. First, we compute predictions for women by using the stub **`Fprm`** to indicate predictions for female scientists from the PRM:

```
mgen if married==1 & female==1, atmeans at(kid5=(0/3)) ///
    stub(Fprm) pr(0) predlabel(Married women)
```

    Predictions from: margins if married==1 & female==1, atmeans at(kid5=(0/3)) predict(pr(0))
    
    Sample selection: if married==1 & female==1 
    
    Variable   Obs Unique      Mean       Min       Max  Label
    ---------------------------------------------------------------------------
    Fprmpr0      4      4  .3179806  .2011927  .4940723  Married women
    Fprmll0      4      4  .2333646  .1677741  .3013507  95% lower limit
    Fprmul0      4      4  .4025967  .2346112  .6867938  95% upper limit
    Fprmkid5     4      4       1.5         0         3  # of kids < 6
    FprmCpr0     4      4  .3179806  .2011927  .4940723  pr(y<=0)
    ----------------------------------------------------------------------------
    
    Specified values of covariates
    
       female    married        phd     mentor 
    ------------------------------------------
            1          1   3.092822       7.88 

The **`predlabel()`** option adds a variable label that will be used in the legend when we graph the results. Next, we compute predictions for men, using the stub **`Mprm`**:

```
mgen if married==1 & female==0, atmeans at(kid5=(0/3)) ///
    stub(Mprm) pr(0) predlabel(Married men)
```

    Predictions from: margins if married==1 & female==0, atmeans at(kid5=(0/3)) predict(pr(0))
    
    Sample selection: if married==1 & female==0 
    
    Variable   Obs Unique      Mean       Min       Max  Label
    ---------------------------------------------------------------------------
    Mprmpr0      4      4  .2309351  .1247218   .400383  Married men
    Mprmll0      4      4  .1531598  .0995559  .2010656  95% lower limit
    Mprmul0      4      4  .3087103  .1498877  .5997005  95% upper limit
    Mprmkid5     4      4       1.5         0         3  # of kids < 6
    MprmCpr0     4      4  .2309351  .1247218   .400383  pr(y<=0)
    ---------------------------------------------------------------------------
    
    Specified values of covariates
    
       female    married        phd     mentor 
    ------------------------------------------
            0          1   3.017231   9.330709 

We can then plot the predictions:

```
label var Fprmpr0 "Married women"
label var Mprmpr0 "Married men"
label var Mprmkid5  "Number of children"

graph twoway connected Fprmpr0 Mprmpr0 Mprmkid5, ///
    msiz(*1.6 *1.265) lwid(thin thin) mcol(black gs8) ///
    ylabel(0(.1).4) yline(.1 .2 .3) xlabel(0/3) msym(O D) ///
    ytitle("Probability of No Articles")
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.1.9.png" style="width:550px;height:370px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

If you compare the values plotted for women with those computed with `ratable` in the prior section, you will see that they are the same—just presented differently.

### 2.5 Comparing observed and predicted counts to evaluate model specification

Does the PRM fit the data in our example well? In this section, we present an informal way to evaluate the fit of the PRM by comparing the predicted distribution of counts with the observed distribution, extending the ideas from section 9.1.2. After fitting our model, we can compute the predicted probabilities for specific counts by using **`predict`**. For example, for counts of 0, 1, and 2, we type:

```
poisson art i.female i.married kid5 phd mentor, nolog
predict prob0, pr(0)
predict prob1, pr(1)
predict prob2, pr(2)
sum prob0 prob1 prob2
```

        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
           prob0 |        915    .2092071    .0794247   .0000659   .4113403
           prob1 |        915    .3098447    .0634931   .0006345   .3678775
           prob2 |        915     .242096    .0311473   .0030544   .2706704

The means are the average predicted probabilities defined as

$$\overline{\mathrm{Pr}}(y=k)=\frac{1}{N}\sum_{i=1}^{N}\widehat{\mathrm{Pr}}(y_{i}=k\mid\mathbf{x}_{i})$$

The average predictions from **`predict`** are identical to the average predictions computed by **`mtable`**:

```
mtable, pr(0/2) brief
```

    Expression: Pr(art), predict(pr())
    
           0         1         2
    ----------------------------
       0.209     0.310     0.242
    

The **`mgen, meanpred`** command computes the same average predicted probabilities but saves the predictions in variables that can be graphed:

```
mgen, stub(PR) pr(0/9) meanpred
```

    Predictions from: 
    
    Variable   Obs Unique       Mean        Min       Max  Label
    ----------------------------------------------------------------------------------------
    PRval       10     10        4.5          0         9  Articles in last 3 yrs of PhD
    PRobeq      10     10   .0993443   .0010929  .3005464  Observed proportion
    PRoble      10     10   .8328962   .3005464  .9934426  Observed cum. proportion
    PRpreq      10     10   .0998819   .0009304  .3098447  Avg predicted Pr(y=#)
    PRprle      10     10   .8308733   .2092071  .9988187  Avg predicted cum. Pr(y=#)
    PRob_pr     10     10  -.0005376  -.0475604  .0913393  Observed - Avg Pr(y=#)
    ----------------------------------------------------------------------------------------

```
list PRval PRpreq PRobeq in 1/3, clean
```
           PRval      PRpreq      PRobeq  
      1.       0    .2092071   .30054645  
      2.       1   .30984468   .26885246  
      3.       2   .24209596   .19453552  


The listed values of **PRpreq** match those from **`mtable`**, and the values of **PRobeq** are the observed proportions for each value of **art**.

To show how `mgen, meanpred` is used to compare predictions from different models, we begin by fitting a model with no independent variables, which is simply fitting the Poisson PDF:

```
poisson art, nolog
```

    Poisson regression                                      Number of obs =    915
                                                            LR chi2(0)    =   0.00
                                                            Prob > chi2   =      .
    Log likelihood = -1742.5735                             Pseudo R2     = 0.0000
    
    ------------------------------------------------------------------------------
             art | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |       0.53       0.03    20.72   0.000         0.48        0.58
    ------------------------------------------------------------------------------

Next, we compute predictions for counts 0-9:

```
mgen, stub(PDF) pr(0/9) meanpred
```

    Variable   Obs Unique       Mean        Min       Max  Label
    --------------------------------------------------------------------------------------
    PDFval      10     10        4.5          0         9  Articles in last 3 yrs of PhD
    PDFobeq     10     10   .0993443   .0010929  .3005464  Observed proportion
    PDFoble     10     10   .8328962   .3005464  .9934426  Observed cum. proportion
    PDFpreq     10     10   .0999988   .0000579   .311469  Avg predicted Pr(y=#)
    PDFprle     10     10   .8307106   .1839859  .9999884  Avg predicted cum. Pr(y=#)
    PDFob_pr    10     10  -.0006546  -.0691068  .1165606  Observed - Avg Pr(y=#)
    --------------------------------------------------------------------------------------

Because we specified the stub **PDF**, **`mgen`** created a new variable called **PDFpreq** with the average predicted probabilities for counts 0-9 from a univariate Poisson distribution. Variable **PDFobeq** contains the corresponding observed probability, while **PDFval** contains the values of the count itself (for example, 1 for the row that contains information about the observed and predicted counts of y = 1).

Next, we fit the PRM with the independent variables used above,

```
poisson art i.female i.married kid5 phd mentor, nolog
```

and compute the average predictions:

```
mgen, stub(PRM) pr(0/9) meanpred
```
    Predictions from: 
    
    Variable   Obs Unique       Mean        Min       Max  Label
    ------------------------------------------------------------------------------------------
    PRMval      10     10        4.5          0         9  Articles in last 3 yrs of PhD
    PRMobeq     10     10   .0993443   .0010929  .3005464  Observed proportion
    PRMoble     10     10   .8328962   .3005464  .9934426  Observed cum. proportion
    PRMpreq     10     10   .0999988   .0000579   .311469  Avg predicted Pr(y=#)
    PRMprle     10     10   .8307106   .1839859  .9999884  Avg predicted cum. Pr(y=#)
    PRMob_pr    10     10  -.0006546  -.0691068  .1165606  Observed - Avg Pr(y=#)
    ------------------------------------------------------------------------------------------

Variable **PRMpreq** contains the average predictions based on the PRM we just fit.

We plot **PDFobeq**, **PDFpreq**, and **PRMpreq** against the count values in **PDFval** (which has the same values as **PRMval**) on the x-axis:

```
label variable PRMobeq  "Observed"
label variable PRMpreq  "PRM"

graph twoway connected PDFobeq PDFpreq PRMpreq PRMval, ///
    ytitle("Probability") ylabel(0(.1).4, gmax) xlabel(0/9) msym(O Th Sh)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.1.10.png" style="width:550px;height:370px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The graph shows that, even though many of the independent variables have significant effects on the number of articles, there is only a modest improvement in the predictions made by the PRM over the univariate Poisson distribution. **This suggests the need for an alternative model. Although an incorrect model might reproduce the observed distribution of counts reasonably well, systematic discrepancies between the predicted and observed distributions suggest that an alternative model should be considered.** In section 9.7, we discuss other ways to compare the fit of the PRM model and alternative models.

这段话讨论的是泊松回归模型（PRM）的预测能力以及模型选择的重要性。图表显示，尽管许多自变量对文章数量有显著影响，但PRM模型相较于单变量泊松分布的预测能力只是略有提高。这表明可能需要考虑替代模型。

这里的核心观点是，即使一个模型能够合理地再现观察到的计数分布，但如果预测分布和观察分布之间存在系统性差异，那么这个模型可能不是最佳选择。换句话说，模型的预测能力不仅仅是复制数据的能力，而是能够准确地捕捉数据背后的生成过程。

在统计学中，我们经常使用不同的指标来比较模型的拟合度，例如赤池信息准则（AIC）、贝叶斯信息准则（BIC）或者似然比检验。这些方法可以帮助我们判断模型是否能够很好地捕捉数据的特征，以及是否有足够的证据支持模型中的假设。

因此，作者提到，在9.7节中会讨论比较PRM模型和其他替代模型拟合度的其他方法。这意味着，研究人员应该考虑多种模型，并使用适当的统计工具来选择最能准确描述数据生成过程的模型。


### 2.6 (Advanced) Exposure time

>This section is marked as advanced because it may be more useful to read if you are working with an application in which observations vary in terms of exposure time—that is, how long they have been at risk of the event being counted. **If you have previous experience with survival models or event history models, this section may also help you better understand how modeling event counts outcomes is related to modeling whether events have happened.**

So far, we have implicitly assumed that each observation was at risk of an event occurring for the same amount of time. For our example, for each person in the sample, we counted their articles over a 3-year period. Often, when collecting data, however, different observations have different exposure times. For example, the sample of scientists might have received their degrees in different years, and our outcome could have been the total publications from the PhD to the date of the survey. The amount of time in the career would clearly affect the number of publications, and scientists vary in the length of their careers. The same issue arises if we use data in which the counts are collected from regions that have different sizes or populations. For example, if the outcome variable was the number of homicides reported in a city, the counts would be affected by the population size of the city. The methods we explain in this section could be applied in the same way.

To illustrate how to adjust for exposure time and the problems that occur if you do not make the appropriate adjustment, we have artificially constructed a variable named **profage** measuring a scientist’s professional age. This is the amount of time that a scientist has been “exposed” to the possibility of publishing. The variable **totalarts** is the total number of articles during the scientist’s career. Fitting a PRM, we obtain the following results:

```
use couexposure4, clear
poisson totalarts kid5 mentor, nolog irr
```

    Poisson regression                                      Number of obs =    915
                                                            LR chi2(2)    = 277.18
                                                            Prob > chi2   = 0.0000
    Log likelihood = -2551.3379                             Pseudo R2     = 0.0515
    
    ------------------------------------------------------------------------------
       totalarts |        IRR   Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            kid5 |       1.16       0.03     6.42   0.000         1.11        1.22
          mentor |       1.02       0.00    16.65   0.000         1.02        1.03
           _cons |       2.13       0.06    26.33   0.000         2.02        2.26
    ------------------------------------------------------------------------------
    Note: _cons estimates baseline incidence rate.


As expected, the mentor’s productivity has a strong and significant effect on the student’s productivity. Surprisingly, having young children increases scientific productivity; most research in this area finds that having young children decreases productivity. One paper, however, found that having young children increases productivity. This result was an artifact from a sample where scientists with more children were older and the dependent variable was the total number of publications. Essentially, the number of children was a proxy for professional age, which has a positive effect on total publications. The same is true in our simulated data. Now, let’s consider how to adjust for the artifact.

导师的指导质量、研究水平等对学生的学术成就有重要影响。

然而，更令人惊讶的发现是有年幼孩子的科学家其生产力更高。这与大多数研究结果相悖，后者认为有年幼孩子会分散科学家的精力，从而降低其生产力。但有一篇论文却发现有年幼孩子实际上能增加科学家的生产力。这一看似矛盾的结果实际上是由于研究所选取的样本和数据导致的：样本中拥有更多孩子的科学家年龄较大，而论文总数作为因变量，实际上与科学家的专业年龄正相关。也就是说，随着科学家专业年龄的增长，其论文总数也会增加，因此孩子的数量成为了专业年龄的代理变量。

同样的情况也出现在模拟数据中。这说明在进行数据分析时，需要考虑到各种可能的因素，以避免出现误导性的结果。因此，作者提出需要考虑如何调整这一人为现象，以获得更准确的研究结果。
听明白了，请看下文：

Exposure time can be easily incorporated into count models. Let $t_i$ be the amount of time that observation $i$ is at risk. If $\mu_i$ is the expected number of observations for one unit of time for case $i$, then $t_i \cdot \mu_i$ is the rate over a period of length $t_i$. Assuming only two independent variables for simplicity, our count equation becomes:

$$\mu_{i}t_{i}=\{\exp\left(\beta_{0}+\beta_{1}x_{1}+\beta_{2}x_{2}\right)\}\times t_{i}$$

Because $t=\exp{(\ln t)}$，the equation can be rewritten as

$$\mu_{i}t_{i}=\exp\left(\beta_{0}+\beta_{1}x_{1}+\beta_{2}x_{2}+\ln t_{i}\right)$$

This shows that the effect of different exposure times among observations can be included in the model as the log of the exposure time with its regression coefficient constrained to 1. This is exactly what Stata does with the **`exposure()`** option:


在计数模型中，我们通常关注的是事件发生的次数，例如一个人在一定时间内打电话的次数、一个地区在一定时间内发生犯罪事件的次数等。然而，在实际应用中，我们可能需要考虑时间因素，即观察单位在风险中的持续时间。这就是暴露时间（exposure time）的概念。

在泊松回归模型中，我们假设事件发生的次数服从泊松分布，其期望值（平均值）为 $\mu$。如果 $\mu_i$ 是观察单位 $i$ 在单位时间内预期发生的事件数，那么在暴露时间为 $t_i$ 的情况下，观察单位 $i$ 在时间长度为 $t_i$ 的期间内的事件发生率可以表示为 $t_i \cdot \mu_i$。

现在，让我们分解公式来更清楚地理解这个过程。假设我们有两个独立变量 $x_1$ 和 $x_2$，那么计数方程可以表示为：
$$\mu_{i}t_{i}=\{\exp\left(\beta_{0}+\beta_{1}x_{1}+\beta_{2}x_{2}\right)\}\times t_{i}$$
这里，$\beta_0, \beta_1, \beta_2$ 是模型参数，表示不同变量对事件发生率的影响。
接下来，我们使用自然对数的性质，即 $e^{\ln t} = t$，将方程重写为：
$$\mu_{i}t_{i}=\exp\left(\beta_{0}+\beta_{1}x_{1}+\beta_{2}x_{2}+\ln t_{i}\right)$$
这个方程表明，不同观察单位之间的暴露时间差异可以通过将其自然对数包含在模型中，并将其回归系数设置为1来反映。这样做的好处是，我们可以在模型中包含暴露时间的影响，而不会增加额外的参数。
* 例子（1）

假设我们正在研究某种药物对患者症状缓解的时间。我们收集了患者使用药物的时间（暴露时间）和症状缓解的次数（事件发生率）。我们的泊松回归模型可能如下所示：
$$\mu_{i}t_{i}=\exp\left(\beta_{0}+\beta_{1}x_{1}+\beta_{2}x_{2}+\ln t_{i}\right)$$
其中，$\beta_0, \beta_1, \beta_2$ 是模型参数，分别代表常数项、药物剂量和患者年龄对症状缓解次数的影响。$\ln t_{i}$ 是暴露时间的自然对数，其回归系数被设置为1，表示暴露时间的影响已经被包含在模型中了。

通过这个模型，我们可以分析药物剂量和患者年龄对症状缓解次数的影响，同时考虑到每个患者暴露时间的差异。例如，如果药物剂量增加，我们期望症状缓解次数会增加；如果患者年龄增加，我们可能期望症状缓解次数会减少。同时，我们还可以分析不同暴露时间对症状缓解次数的影响，即使药物剂量和患者年龄保持不变。

* 例子（2）

假设我们有一个城市一天内发生的交通事故数据，我们将一天分为三个时间段：上午（6小时）、下午（8小时）和晚上（12小时）。我们的泊松回归模型可能包括一个常数项 $\beta_0$、上午时间段的影响 $\beta_1$、下午时间段的影响 $\beta_2$ 和晚上时间段的影响 $\beta_3$。

首先，我们需要定义每个时间段的长度作为暴露时间 $t_i$。例如，上午的暴露时间是6小时，下午的暴露时间是8小时，晚上的暴露时间是12小时。

接下来，我们需要定义每个时间段内交通事故的期望值 $\mu_i$。这个值可以通过观察到的交通事故次数除以暴露时间来估计。例如，如果上午发生了3次交通事故，那么上午的期望值 $\mu_i$ 是 $3 \div 6 = 0.5$ 交通事故/小时。

现在，我们可以将这些值代入泊松回归模型的公式中：
$$\mu_{i}t_{i}=\exp\left(\beta_{0}+\beta_{1}x_{1}+\beta_{2}x_{2}+\ln t_{i}\right)$$
对于上午：
$$0.5 \times 6 = \exp\left(\beta_{0}+\beta_{1} \times 1+\beta_{2} \times 0+\beta_{3} \times 0+\ln(6)\right)$$
对于下午：
$$0.5 \times 8 = \exp\left(\beta_{0}+\beta_{1} \times 0+\beta_{2} \times 1+\beta_{3} \times 0+\ln(8)\right)$$
对于晚上：
$$0.5 \times 12 = \exp\left(\beta_{0}+\beta_{1} \times 0+\beta_{2} \times 0+\beta_{3} \times 1+\ln(12)\right)$$
通过这些方程，我们可以估计参数 $\beta_0, \beta_1, \beta_2, \beta_3$，这些参数将告诉我们每个时间段对交通事故发生率的影响。例如，如果 $\beta_2$ 是正数，那么下午的时间段可能会增加交通事故的发生率；如果 $\beta_3$ 是负数，那么晚上的时间段可能会减少交通事故的发生率。

* 例子（3）

假设我们有一个咖啡店的顾客流量数据，我们将一天分为三个时间段：上午（6小时）、下午（8小时）和晚上（12小时）。我们还收集了不同天气条件下的数据，包括晴天、多云和雨天。我们的泊松回归模型可能包括一个常数项 $\beta_0$、上午时间段的影响 $\beta_1$、下午时间段的影响 $\beta_2$、晴天的影响 $\beta_3$、多云的影响 $\beta_4$ 和雨天的影响 $\beta_5$。
首先，我们需要定义每个时间段的长度作为暴露时间 $t_i$。例如，上午的暴露时间是6小时，下午的暴露时间是8小时，晚上的暴露时间是12小时。

接下来，我们需要定义每个时间段和天气条件下顾客的期望流量 $\mu_i$。这个值可以通过观察到的顾客流量除以暴露时间来估计。例如，如果上午晴天的顾客流量是60人，那么上午晴天的期望流量 $\mu_i$ 是 $60 \div 6 = 10$ 人/小时。

现在，我们可以将这些值代入泊松回归模型的公式中：
$$\mu_{i}t_{i}=\exp\left(\beta_{0}+\beta_{1}x_{1}+\beta_{2}x_{2}+\beta_{3}x_{3}+\beta_{4}x_{4}+\beta_{5}x_{5}+\ln t_{i}\right)$$
对于上午晴天：
$$10 \times 6 = \exp\left(\beta_{0}+\beta_{1} \times 1+\beta_{2} \times 0+\beta_{3} \times 1+\beta_{4} \times 0+\beta_{5} \times 0+\ln(6)\right)$$
对于下午晴天：
$$10 \times 8 = \exp\left(\beta_{0}+\beta_{1} \times 0+\beta_{2} \times 1+\beta_{3} \times 1+\beta_{4} \times 0+\beta_{5} \times 0+\ln(8)\right)$$
对于上午多云：
$$10 \times 6 = \exp\left(\beta_{0}+\beta_{1} \times 1+\beta_{2} \times 0+\beta_{3} \times 0+\beta_{4} \times 1+\beta_{5} \times 0+\ln(6)\right)$$
对于下午多云：
$$10 \times 8 = \exp\left(\beta_{0}+\beta_{1} \times 0+\beta_{2} \times 1+\beta_{3} \times 0+\beta_{4} \times 1+\beta_{5} \times 0+\ln(8)\right)$$
对于上午雨天：
$$10 \times 6 = \exp\left(\beta_{0}+\beta_{1} \times 1+\beta_{2} \times 0+\beta_{3} \times 0+\beta_{4} \times 0+\beta_{5} \times 1+\ln(6)\right)$$
对于下午雨天：
$$10 \times 8 = \exp\left(\beta_{0}+\beta_{1} \times 0+\beta_{2} \times 1+\beta_{3} \times 0+\beta_{4} \times 0+\beta_{5} \times 1+\ln(8)\right)$$

通过这些方程，我们可以估计参数 $\beta_0, \beta_1, \beta_2, \beta_3, \beta_4, \beta_5$，这些参数将告诉我们每个时间段和天气条件对顾客流量的影响。例如，如果 $\beta_3$ 是正数，那么晴天的顾客流量可能会增加；如果 $\beta_4$ 是负数，那么多云的顾客流量可能会减少。

这个例子展示了如何在泊松回归模型中包含暴露时间，并如何通过代入实际数据来估计模型参数。通过这种方式，我们可以分析不同时间段和天气条件对顾客流量的影响，并做出更准确的预测和分析。

```
poisson totalarts kid5 mentor, nolog irr exposure(profage)
```

    Poisson regression                                      Number of obs =    915
                                                            LR chi2(2)    = 245.43
                                                            Prob > chi2   = 0.0000
    Log likelihood = -2416.7457                             Pseudo R2     = 0.0483
    
    ------------------------------------------------------------------------------
       totalarts |        IRR   Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            kid5 |       0.90       0.02    -4.47   0.000         0.86        0.94
          mentor |       1.02       0.00    16.98   0.000         1.02        1.03
           _cons |       1.41       0.04    12.08   0.000         1.33        1.49
     ln(profage) |       1.00  (exposure)
    ------------------------------------------------------------------------------
    Note: _cons estimates baseline incidence rate.

Variable **In (profage)** appears just as other variables, but the coefficient is shown as 1. There is no standard error or $t$ statistic, because the coefficient was not estimated but was assumed to equal 1. The coefficients for **kid5** and **mentor** can be interpreted using the same methods discussed earlier. The effect of the mentor’s productivity is nearly identical to our earlier results that did not adjust for exposure, but having young children now decreases scientific productivity, consistent with other studies.

We can obtain the same result by using the **`offset()`** option, where we specify a variable containing the log of the exposure time instead of the exposure time. For example,

```
poisson totalarts kid5 mentor, nolog irr offset(lnprofage)
```

    Poisson regression                                      Number of obs =    915
                                                            LR chi2(2)    = 245.43
                                                            Prob > chi2   = 0.0000
    Log likelihood = -2416.7457                             Pseudo R2     = 0.0483
    
    ------------------------------------------------------------------------------
       totalarts |        IRR   Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            kid5 |       0.90       0.02    -4.47   0.000         0.86        0.94
          mentor |       1.02       0.00    16.98   0.000         1.02        1.03
           _cons |       1.41       0.04    12.08   0.000         1.33        1.49
       lnprofage |       1.00  (offset)
    ------------------------------------------------------------------------------
    Note: _cons estimates baseline incidence rate.

The effect of exposure time is constant over time. For those familiar with survival models, this is the same as saying the hazard of publishing is constant. To relax this assumption, we could estimate a parameter for the log of exposure time instead of constraining it to 1. This would require including **lnprofage** as an independent variable and estimating its effect just as we would any other independent variable. We could also use a different functional form to address the possibility of nonlinear effects of time on productivity.

Although the **``exposureQ``** and **``offset()``** options are not considered further in this chapter, they can be used with the other models we discuss.

## 3  The negative binomial regression model

The PRM accounts for observed heterogeneity by stating that the rate $ \mu_i $ depends on the observed $ x_k $'s. In practice, the PRM often fails due to overdispersion. In other words, the model does not adequately capture the level of variability in the outcome. The negative binomial regression model (NBRM) addresses this issue by introducing the parameter $ \alpha $, which represents unobserved heterogeneity among observations3. For instance, when considering three independent variables, the PRM is

$$\mu_{i}=\exp\left(\beta_{0}+\beta_{1}x_{i1}+\beta_{2}x_{i2}+\beta_{3}x_{i3}\right)$$

>3.The NBRM can also be derived through a process of contagion where the occurrence of an event changes the probability of further events—an approach not considered further here.

The NBRM adds the error $\varepsilon $ that is assumed to be uncorrelated with the $ x's $.

$$\widetilde\mu_{i}=\exp\left(\beta_{0}+\beta_{1}x_{i1}+\beta_{2}x_{i2}+\beta_{3}x_{i3}+\varepsilon_{i}\right)$$

With basic algebra and defining $\delta$ as $ \exp(\varepsilon) $, the model becomes

\begin{align*}
\quad \quad\widetilde\mu_{i}=\exp\left(\beta_{0}+\beta_{1}x_{i1}+\beta_{2}x_{i2}+\beta_{3}x_{i3}\right)\exp\left(\varepsilon_{i}\right)\\
\end{align*}

\begin{align*}
 =\exp\left(\beta_0+\beta_1x_{i1}+\beta_2x_{i2}+\beta_3x_{i3}\right)\delta_i
\end{align*}

To identify the model, we assume that $E\left(\delta\right)=1$,which corresponds to the assumption $E\left(\varepsilon\right)=0$ in the linear regression model. With this assumption, it follows that $E\left(\widetilde{\mu}\right)=\mu E\left(\delta\right)=\mu.$  Thus the PRM and the NBRM have the same mean structure. Accordingly, if the assumptions of the NBRM are correct, the expected rate for a given level of the independent variables will be the same in both models. However, the standard errors in the PRM are biased downward, resulting in spuriously large $ z-values $and spuriously small $p-values$ (Cameron and Trivedi 2013)4.

>As we discuss in section 9.3.5, robust standard errors would be consistent and would produce p-values that yield nominal coverage, as discussed by Cameron and Trivedi (2013). Instead of following the robust approach, we focus on using a more efficient estimator whose standard errors are consistently estimated.

To better understand the link between the PRM and the NBRM, as well as their differences, suppose that the error term $ \varepsilon $ in (9.3) is an observed variable with a regression coefficient constrained to equal 1:

$$\widetilde\mu_{i}=\exp\left(\beta_{0}+\beta_{1}x_{i1}+\beta_{2}x_{i2}+\beta_{3}x_{i3}+1\varepsilon_{i}\right)$$

The distribution of observations conditional on the values of both the $ x_i $ and the $ \varepsilon_i $ has a Poisson distribution, just as it did for the PRM. Accordingly

$$\mathrm{Pr}(y_{i}\mid\mathbf{x_i},\varepsilon_{i})=\frac{e^{-\widetilde\mu_{i}}\widetilde\mu_{i}^{y_{i}}}{y_{i}!}$$

However, because e is unknown, we cannot compute $\operatorname{Pr}\left(y\mid\mathbf{x},\varepsilon\right)$ This limitation is resolved by assuming that exp$(\varepsilon)$ has a gamma distribution (see Long [1997, 231-232] or Cameron and Trivedi [2013, 80-89]).

The probability of $ y $ conditional on $ x $, but not conditional on $ \varepsilon $, is computed as a weighted combination of $ \text{Pr} (y | x , \varepsilon) $ for all values of $ \varepsilon $, where the weights are determined by $ \text{Pr}(\exp(\varepsilon)) $ from the gamma distribution. The mathematics for this mixing are complex and not particularly helpful for understanding the interpretation of the model, leading to the negative binomial distribution for $ y $ given $ x $.

$$\Pr\left(y\mid\mathbf{x}\right)=\frac{\Gamma(y+\alpha^{-1})}{y!\Gamma(\alpha^{-1})}\left(\frac{\alpha^{-1}}{\alpha^{-1}+\mu}\right)^{\alpha-1}\left(\frac{\mu}{\alpha^{-1}+\mu}\right)^{y}$$

where $\Gamma(\cdot)$ The gamma function is the gamma function. The parameter $ a $ determines the degree of dispersion in the predictions, as illustrated by the following figure:

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.1.11.png" style="width:450px;height:630px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

In both panels, the dispersion of predicted counts for a given value of $ x $ is larger than in the PRM (compare with the figure on page 488). This is most easily seen in the greater probability of zero counts. Further, the larger value of $ a $ in panel B results in greater spread in the data. If $ a = 0 $, the NBRM reduces to the PRM, which turns out to be the key to testing for overdispersion. This is discussed in section 9.3.3.

---
**泊松回归模型（PRM）和负二项回归模型（NBRM）在处理观测到的异质性时的差异**
* 泊松回归模型（PRM）是一个用于分析计数数据的统计模型。它假设事件发生的次数遵循泊松分布，并且这些次数的期望值（即速率）依赖于观察到的自变量 $ x_k $。PRM 的公式是：
$$\mu_{i}=\exp\left(\beta_{0}+\beta_{1}x_{i1}+\beta_{2}x_{i2}+\beta_{3}x_{i3}\right)$$
这里，$\mu_{i}$ 是第 $i$ 个观察单位的期望事件发生次数，$\beta_{0}, \beta_{1}, \beta_{2}, \beta_{3}$ 是模型参数，$x_{i1}, x_{i2}, x_{i3}$ 是第 $i$ 个观察单位的自变量值。
为了帮助你理解这个公式，我们可以用一个例子来解释。假设我们研究的是一个城市中每天发生的交通事故次数。我们的自变量可能包括一天中的时间、天气情况和是否是周末。如果我们假设参数 $\beta_0, \beta_1, \beta_2, \beta_3$ 分别为1, 0.5, -0.2, 0.3，这意味着一天中的时间、天气情况和是否是周末都会影响每天发生的交通事故次数。根据这个模型，如果一天中的时间是上午，天气情况是晴天，并且是周末，那么每天发生的交通事故次数的期望值将是：
$$\mu_{i}=\exp(1 + 0.5 \times 1 + (-0.2) \times 1 + 0.3 \times 1) = \exp(1.6) \approx 4.89$$
这个例子展示了如何使用PRM来分析不同自变量对事件发生次数的影响。通过这种方式，我们可以更准确地理解数据背后的生成过程，并做出更可靠的预测和分析。

---

* 接下来，我们来解释负二项回归模型（NBRM）中的关键概念。NBRM是为了解决PRM中的过度分散问题而提出的。在实际应用中，PRM往往因为过度分散而失败，即模型不能很好地捕捉结果的变异性。NBRM通过引入参数 $ \alpha $ 来解决这个问题，这个参数代表观察之间未观察到的异质性。NBRM的公式是：
$$\widetilde\mu_{i}=\exp\left(\beta_{0}+\beta_{1}x_{i1}+\beta_{2}x_{i2}+\beta_{3}x_{i3}+\varepsilon_{i}\right)$$
其中，$\varepsilon_{i}$ 是误差项。为了识别模型，我们假设 $E\left(\delta\right)=1$，其中 $\delta = \exp(\varepsilon)$。这样，PRM 和 NBRM 都有相同的意思结构。
NBRM的预测比PRM更准确，因为它考虑了观察之间的异质性。然而，PRM的标准误差存在偏差，导致虚假的 $ z $ 值和虚假的 $ p $ 值。为了克服这个问题，可以使用稳健的标准误差估计，或者使用更有效的估计器，其标准误差是一致的。这个例子展示了如何在NBRM中使用 $\alpha$ 参数来处理过度分散，并帮助我们更好地理解数据。
$$\widetilde\mu_{i}=\exp\left(\beta_{0}+\beta_{1}x_{i1}+\beta_{2}x_{i2}+\beta_{3}x_{i3}\right)\exp\left(\varepsilon_{i}\right)$$
这个公式是负二项回归模型（NBRM）中的一个步骤，其中我们使用了基本代数来表达 $\widetilde\mu_{i}$，即 $ \exp(\varepsilon_{i}) $。这里，$\varepsilon_{i}$ 是误差项，而 $\exp(\varepsilon_{i})$ 被称为 $\delta_i$。接下来，我们来看这个公式：
$$\exp\left(\beta_0+\beta_1x_{i1}+\beta_2x_{i2}+\beta_3x_{i3}\right)\delta_i$$
这个公式进一步简化了 $\widetilde\mu_{i}$ 的表达式，将 $\exp(\varepsilon_{i})$ 替换为 $\delta_i$。

---

* 现在，我们来看这个假设：
$$E\left(\delta\right)=1$$

这个假设对应于线性回归模型中的假设 $E\left(\varepsilon\right)=0$。这意味着误差项的平均值是 0。由于 $E\left(\delta\right)=1$，我们可以得出 $E\left(\widetilde{\mu}\right)=\mu E\left(\delta\right)=\mu$。这意味着 PRM 和 NBRM 具有相同的意思结构，即它们预测的期望值相同。

为了帮助您更好地理解这些概念，我们可以用一个例子来解释。假设我们研究的是一个城市中每天发生的交通事故次数。我们的自变量可能包括一天中的时间、天气情况和是否是周末。

如果我们使用 PRM 来分析这些数据，我们可能会得到一个公式，类似于第一个公式：
$$\mu_{i}=\exp\left(\beta_{0}+\beta_{1}x_{i1}+\beta_{2}x_{i2}+\beta_{3}x_{i3}\right)$$
在这个公式中，$\mu_{i}$ 是第 $i$ 个观察单位的期望交通事故发生次数。
然而，如果我们的数据表现出过度分散，即实际观察到的方差大于 PRM 预测的方差，我们可以使用 NBRM 来分析这些数据。在这种情况下，我们可能会得到一个类似于第二个公式的概率分布：
$$\widetilde\mu_{i}=\exp\left(\beta_{0}+\beta_{1}x_{i1}+\beta_{2}x_{i2}+\beta_{3}x_{i3}\right)\exp\left(\varepsilon_{i}\right)$$
这个公式表示的是调整后的期望交通事故发生次数，它考虑了过度分散性。

通过这个例子，我们可以看到，NBRM 比 PRM 更能处理过度分散的数据，因为它允许模型中的误差项有更大的波动性。这样，NBRM 可以更准确地捕捉到观测数据的变异性。

* 为了更好的理解PRM和NBRM之间的联系和区别，我们列举了如下公式：
首先，让我们来看第一个公式：
$$\widetilde\mu_{i}=\exp\left(\beta_{0}+\beta_{1}x_{i1}+\beta_{2}x_{i2}+\beta_{3}x_{i3}+1\varepsilon_{i}\right)$$
这个公式表示的是负二项回归模型（NBRM）中的一个变体。在这个公式中，$\varepsilon_{i}$ 是误差项，其回归系数被约束为等于 1。这意味着 $\varepsilon_{i}$ 是一个观测变量，而不是一个未知的随机变量。这个约束允许我们更好地理解 PRM 和 NBRM 之间的关系。
接下来，我们来看第二个公式：
$$\mathrm{Pr}(y_{i}\mid\mathbf{x_i},\varepsilon_{i})=\frac{e^{-\widetilde\mu_{i}}\widetilde\mu_{i}^{y_{i}}}{y_{i}!}$$
这个公式表示的是在给定 $ x_i $ 和 $ \varepsilon_i $ 的条件下，观察到的 $ y_i $ 的概率分布。这个概率分布仍然是泊松分布，这与 PRM 中的假设相同。

* 为了帮助您更好地理解这些公式，我们可以用一个例子来解释。假设我们研究的是一个城市中每天发生的交通事故次数。我们的自变量可能包括一天中的时间、天气情况和是否是周末。
如果我们使用 PRM 来分析这些数据，我们可能会得到一个公式，类似于第一个公式：
$$\mu_{i}=\exp\left(\beta_{0}+\beta_{1}x_{i1}+\beta_{2}x_{i2}+\beta_{3}x_{i3}\right)$$
在这个公式中，$\mu_{i}$ 是第 $i$ 个观察单位的期望交通事故发生次数。
然而，如果我们的数据表现出过度分散，即实际观察到的方差大于 PRM 预测的方差，我们可以使用 NBRM 来分析这些数据。在这种情况下，我们可能会得到一个类似于第二个公式的概率分布：
$$\mathrm{Pr}(y_{i}\mid\mathbf{x_i},\varepsilon_{i})=\frac{e^{-\widetilde\mu_{i}}\widetilde\mu_{i}^{y_{i}}}{y_{i}!}$$
这个公式表示的是在给定 $ x_i $ 和 $ \varepsilon_i $ 的条件下，观察到的 $ y_i $ 的概率分布。在这个例子中，$\widetilde\mu_{i}$ 是调整后的期望交通事故发生次数，它考虑了过度分散性。
通过这个例子，我们可以看到，NBRM 比 PRM 更能处理过度分散的数据，因为它允许模型中的误差项有更大的波动性。这样，NBRM 可以更准确地捕捉到观测数据的变异性。

---

* 我们之前提到了负二项回归模型（NBRM）的公式，它考虑了泊松分布的过度分散性。NBRM 的公式是
$$\Pr\left(y\mid\mathbf{x}\right)=\frac{\Gamma(y+\alpha^{-1})}{y!\Gamma(\alpha^{-1})}\left(\frac{\alpha^{-1}}{\alpha^{-1}+\mu}\right)^{\alpha-1}\left(\frac{\mu}{\alpha^{-1}+\mu}\right)^{y}$$
其中，$\Gamma(\cdot)$ 是伽马函数，$\mu$ 是期望事件发生次数，$\alpha$ 是模型参数，它决定了预测的分散程度。

* 这个公式可能看起来很复杂，但我们可以通过一个例子来简化它。假设我们研究的是一个医院中每天感染新冠病毒的患者数量。我们的自变量可能包括医院的床位数、护士的数量和医院的政策。

如果我们假设 $\alpha=0.5$，这意味着我们预期医院中每天感染新冠病毒的患者数量会有一定的波动，但这种波动是有限的。如果我们增加医院的床位数、护士的数量或改变医院的政策，我们预计感染人数会有所增加，但增加的幅度不会非常大。

如果我们假设 $\alpha=1.0$，这意味着我们预期医院中每天感染新冠病毒的患者数量会有更大的波动。如果我们增加医院的床位数、护士的数量或改变医院的政策，我们预计感染人数会有更大的增加，但这种增加也是有限的。
通过这个例子，我们可以看到，$\alpha$ 参数允许我们捕捉观测数据的变异性，并更好地理解不同自变量对感染人数的影响。
这个例子展示了如何在NBRM中使用 $\alpha$ 参数来处理过度分散，并帮助我们更好地理解数据。通过这种方式，我们可以更准确地理解数据背后的生成过程，并做出更可靠的预测和分析。

---

通过这个完整的解释，我们可以看到，负二项回归模型（NBRM）通过引入参数 $\alpha$ 来处理泊松回归模型（PRM）中的过度分散问题。NBRM 允许模型中的误差项有更大的波动性，从而更准确地捕捉到观测数据的变异性。通过这个模型，我们可以更好地理解数据，并做出更准确的预测和分析。

让我们用一个具体的例子来解释负二项回归模型（NBRM）的应用。
假设我们研究的是一个城市中每天的交通事故发生次数。我们的数据包括不同街道的交通事故发生次数，并且我们知道这些街道的宽度、人口密度、交通流量和天气状况。
我们的模型可能如下所示：
$$\Pr\left(y\mid\mathbf{x}\right)=\frac{\Gamma(y+\alpha^{-1})}{y!\Gamma(\alpha^{-1})}\left(\frac{\alpha^{-1}}{\alpha^{-1}+\mu}\right)^{\alpha-1}\left(\frac{\mu}{\alpha^{-1}+\mu}\right)^{y}$$
这里，$\alpha$ 是模型参数，它决定了预测的分散程度。如果 $\alpha$ 值较大，那么预测的分散程度也会较大，这意味着模型认为事故发生次数会有更大的波动性。

假设我们收集了以下数据：

| 街道 | 宽度 | 人口密度 | 交通流量 | 天气状况 | 事故发生次数 |
|------|------|---------|---------|---------|------------|
| 1    | 5    | 10000   | 10000   | 晴天    | 2         |
| 2    | 7    | 15000   | 12000   | 阴天    | 3         |
| 3    | 4    | 8000    | 8000    | 雨天    | 4         |

我们可以使用这些数据来估计模型参数 $\beta_0, \beta_1, \beta_2, \beta_3$ 和 $\alpha$。
首先，我们需要估计 $\alpha$。这可以通过观察数据的分散程度来完成。如果我们的数据显示出过度分散，我们可以使用更大的 $\alpha$ 值。
接下来，我们估计 $\beta_0, \beta_1, \beta_2, \beta_3$。这些参数将告诉我们不同自变量对事故发生次数的影响。例如，如果 $\beta_1$ 是正数，那么我们可以预期宽度较大的街道发生事故的次数会更多。
一旦我们估计了这些参数，我们就可以使用模型来预测不同条件下的事故发生次数。例如，如果一条街道的宽度是7，人口密度是15000，交通流量是12000，并且天气状况是阴天，我们可以使用模型来预测该街道的事故发生次数。
通过这个例子，我们可以看到如何使用负二项回归模型来处理计数数据，并理解模型中的不同参数如何影响预测结果。

让我们将案例数据代入负二项回归模型（NBRM）的公式中，并计算预测的事故发生次数。
首先，我们需要估计模型参数 $\alpha$ 和 $\beta_0, \beta_1, \beta_2, \beta_3$。由于我们没有实际的数据，我们将使用一些假设的值来进行演示。在实际应用中，这些参数通常通过统计软件（如R、Stata或Python的statsmodels库）来估计。
假设我们估计得到的参数如下：

- $\alpha = 1.5$
- $\beta_0 = 0$
- $\beta_1 = 0.5$（表示街道宽度的影响）
- $\beta_2 = 0.3$（表示人口密度的影响）
- $\beta_3 = 0.2$（表示交通流量的影响）
- 
现在，我们可以使用这些参数来计算给定条件下的事故发生次数。假设我们想要预测的是街道2的事故发生次数，其特征值是宽度7、人口密度15000、交通流量12000和阴天。
负二项回归模型的公式是：
$$\Pr\left(y\mid\mathbf{x}\right)=\frac{\Gamma(y+\alpha^{-1})}{y!\Gamma(\alpha^{-1})}\left(\frac{\alpha^{-1}}{\alpha^{-1}+\mu}\right)^{\alpha-1}\left(\frac{\mu}{\alpha^{-1}+\mu}\right)^{y}$$
首先，我们需要计算 $\mu$，即期望事故发生次数：
$$\mu = \exp\left(\beta_{0}+\beta_{1}x_{i1}+\beta_{2}x_{i2}+\beta_{3}x_{i3}\right)$$
对于街道2，我们有：
$$\mu = \exp\left(0 + 0.5 \times 7 + 0.3 \times 15000 + 0.2 \times 12000\right)$$
计算 $\mu$ 的值，然后我们可以使用这个值来计算 $\Pr\left(y\mid\mathbf{x}\right)$。由于我们没有实际的数据，我们将使用 $\Pr\left(y\mid\mathbf{x}\right)$ 的形式来表示这个概率，而不是计算具体的数值。
希望这个例子能帮助你更好地理解如何将实际数据代入负二项回归模型的公式中。在实际应用中，你需要使用统计软件来估计模型参数，并计算具体的预测值。

现在，让我们继续计算街道2的期望事故发生次数 $\mu$，然后使用这个值来表示 $\Pr\left(y\mid\mathbf{x}\right)$ 的形式。
首先，我们计算 $\mu$：
$$\mu = \exp\left(0 + 0.5 \times 7 + 0.3 \times 15000 + 0.2 \times 12000\right)$$
接下来，我们使用 $\mu$ 来表示 $\Pr\left(y\mid\mathbf{x}\right)$ 的形式。由于我们没有具体的数值，我们将使用 $\mu$ 来表示这个概率。
$$\Pr\left(y\mid\mathbf{x}\right) = \frac{\Gamma(y+\alpha^{-1})}{y!\Gamma(\alpha^{-1})}\left(\frac{\alpha^{-1}}{\alpha^{-1}+\mu}\right)^{\alpha-1}\left(\frac{\mu}{\alpha^{-1}+\mu}\right)^{y}$$
对于街道2，我们可以将 $y$ 替换为实际的事故发生次数，即3。
$$\Pr\left(3\mid\mathbf{x}\right) = \frac{\Gamma(3+\alpha^{-1})}{3!\Gamma(\alpha^{-1})}\left(\frac{\alpha^{-1}}{\alpha^{-1}+\mu}\right)^{\alpha-1}\left(\frac{\mu}{\alpha^{-1}+\mu}\right)^{3}$$
由于我们没有具体的数值，我们将保留 $\mu$ 和 $\alpha$ 的形式，以便展示如何使用这些参数来表示 $\Pr\left(y\mid\mathbf{x}\right)$。
在实际应用中，你需要使用统计软件来估计 $\alpha$ 和 $\beta_0, \beta_1, \beta_2, \beta_3$，然后使用这些估计值来计算 $\mu$ 和 $\Pr\left(y\mid\mathbf{x}\right)$ 的具体数值。
希望这个例子能帮助你更好地理解如何将实际数据代入负二项回归模型的公式中。如果你有任何问题或需要进一步的例子来帮助理解，请随时告诉我。


具体的数据分析场景是否适合使用负二项回归模型（NBRM）取决于数据的特点和分析的目的。负二项回归模型通常用于处理泊松回归模型（PRM）中出现的过度分散问题。在泊松回归模型中，如果数据的方差大于模型预测的方差，即出现过度分散，那么使用负二项回归模型可以更准确地拟合数据。
以下是一些判断是否适合使用负二项回归模型的考虑因素：
1. **过度分散**：如果观察到的方差大于模型预测的方差，这通常表明数据存在过度分散。在这种情况下，负二项回归模型可以提供更准确的拟合。
2. **计数数据**：负二项回归模型主要用于分析计数数据，即每个观测单位的事件发生次数。如果你的数据是计数数据，并且存在过度分散，那么负二项回归模型可能是合适的。
3. **异质性**：负二项回归模型允许模型中的误差项有更大的波动性，从而更好地捕捉观测之间的异质性。如果你的数据存在观测之间的异质性，负二项回归模型可以提供更好的解释。
4. **数据量**：负二项回归模型的估计比泊松回归模型更复杂，因此在数据量较小的情况下，可能需要更多的谨慎。在大样本量下，负二项回归模型的估计通常更加稳定。
5. **参数解释**：负二项回归模型中的参数，如 $\alpha$，允许你解释观测数据的变异性。如果你的分析需要解释这种变异性，负二项回归模型可能是合适的。
6. **研究目的**：负二项回归模型通常用于预测和分析，如果你的研究目的需要更准确的预测或对数据的变异性有更深的理解，那么负二项回归模型可能是合适的选择。
总之，如果数据存在过度分散，并且你的分析目的需要处理这种异质性，那么负二项回归模型可能是合适的选择。在实际应用中，通常需要使用统计软件（如R、Stata或Python的statsmodels库）来估计模型参数，并评估模型的拟合度。


* 关于负二项回归模型的案例分析，以下是一些详细的案例分析，这些案例可以帮助你更好地理解如何应用负二项回归模型进行数据分析：
  
1. **学生缺课天数案例**：这个案例研究了146名学生的缺课天数，并收集了他们的种族、年龄、性别和学习状况等信息。通过负二项回归分析，研究人员可以探究这些因素对学生缺课天数的影响。在这个案例中，因变量是计数变量（即缺课天数），自变量包括种族、性别、年龄和学习状况等分类变量。这个案例展示了如何使用负二项回归来分析计数数据，并解释了这些变量如何影响缺课天数。
2. **专利数量影响关系研究**：这个案例研究了政府对企业的支持力度、是否为一线城市等因素对专利数量的影响。通过负二项回归分析，研究人员可以更准确地理解这些因素如何影响专利数量。在这个案例中，因变量是计数数据（即专利数量），自变量包括政府支持力度、城市类型等分类变量。这个案例展示了如何使用负二项回归来分析具有过度分散特性的计数数据。

这些案例都强调了负二项回归在处理计数数据时的适用性，尤其是在数据表现出过度分散时。通过这些案例，你可以了解到如何设定模型、解释结果，以及如何将负二项回归应用于实际的数据分析中。希望这些案例能够帮助你更好地理解负二项回归模型的应用。

### 3.1 Estimation using nbreg

The NBRM is fit with the following command

**``nbreg depvar[indepvars] [if] [in] [weight] [, noconstant dispersion([mean | constant]) exposure(vamame) vce(vcetype) irr]``**

Most options are the same as those for poisson with the notable exception of the option **``dispersion()``**, which is discussed in the next section. Because of differences in how poisson and nbreg are implemented in Stata, models fit with nbreg take longer to converge.

* **NB1 and NB2 variance functions**

The **``dispersion()``** option specifies the function for the variance of $y$ given $x$, referred to as the dispersion function. To understand what this means, consider the dispersion function from the PRM. In that model,

$$\mathrm{Var}\left(y_i\mid\mathbf{x}_i\right)=E\left(y_i\mid\mathbf{x}_i\right)=\mu_i$$

which is referred to as equidispersion. In real-world data, counts are usually overdispersed, meaning that the conditional variance is larger than the conditional mean.

The NBRM addresses this problem by allowing overdispersion through the $\alpha$ parameter.Cameron and Trivedi (2013) show that a variety of variance functions are possible. The most commonly used function, the default in Stata, is

$$\mathrm{Var}\left(y_i\mid\mathbf{x}\right)=\mu_i+\alpha\mu_i^2$$

which Cameron and Trivedi refer to as the NB2 model, in reference to the squared term $\mu^2$. The **``dispersion(constant)``** option specifies the NB1 model in which

$$\mathrm{Var}\left(y_i\mid\mathbf{x}\right)=\mu_i+\alpha\mu_i$$

NBin refers to the power of 1 in the $\alpha \mu$ term. Because the NB2 model is used most often in applied research, we use this form of the model throughout the book.


在 NBRM 中，我们使用一个额外的参数 $\alpha$ 来处理过度分散问题。过度分散是指数据的真实方差大于模型预测的方差，这会导致泊松回归模型（PRM）中的标准误差估计偏低，从而导致 $ p $ 值偏小，这在统计学中称为过度分散。
NBRM 通过引入参数 $\alpha$ 来解决这个问题，这个参数代表观测之间未观察到的异质性。在 NBRM 中，我们假设 $\exp(\varepsilon)$ 服从伽马分布。这样，我们可以计算出条件概率 $\Pr\left(y\mid\mathbf{x}\right)$，它遵循负二项分布。

负二项分布的公式是：
$$\Pr\left(y\mid\mathbf{x}\right)=\frac{\Gamma(y+\alpha^{-1})}{y!\Gamma(\alpha^{-1})}\left(\frac{\alpha^{-1}}{\alpha^{-1}+\mu}\right)^{\alpha-1}\left(\frac{\mu}{\alpha^{-1}+\mu}\right)^{y}$$
其中，$\Gamma(\cdot)$ 是伽马函数，$\mu$ 是期望事件发生次数，$\alpha$ 是模型参数，它决定了预测的分散程度。
现在，让我们回到提到的 **``dispersion()``** 选项。这个选项指定的是 $y$ 给定 $x$ 的方差函数，也就是我们之前提到的负二项分布中的 $\alpha$ 参数。
在泊松回归模型（PRM）中，假设数据的方差等于其期望值，即 $\mathrm{Var}\left(y_i\mid\mathbf{x}_i\right)=E\left(y_i\mid\mathbf{x}_i\right)=\mu_i$，这被称为等方差性。

**但在实际数据中，事件发生的次数通常表现出过度分散，这意味着条件方差大于条件均值。**

为了处理过度分散，NBRM 允许使用不同的方差函数。Cameron 和 Trivedi (2013) 展示了多种可能的方差函数。最常用的函数，也是 Stata 的默认设置，是：
$$\mathrm{Var}\left(y_i\mid\mathbf{x}\right)=\mu_i+\alpha\mu_i^2$$
这个函数被称为 NB2 模型，因为它包含了 $\mu_i^2$ 的平方项。NB2 模型在实际研究中使用得最为频繁。

另一种可能的方差函数是：
$$\mathrm{Var}\left(y_i\mid\mathbf{x}\right)=\mu_i+\alpha\mu_i$$
这个函数被称为 NB1 模型。NBin 指的是 $\alpha \mu$ 项的指数。

让我们分别举例子来解释 **``dispersion()``** 选项中的两种模型：NB1 和 NB2。
* NB1 模型

NB1 模型是 **``dispersion(constant)``** 选项指定的模型，其方差函数为：
$$\mathrm{Var}\left(y_i\mid\mathbf{x}\right)=\mu_i+\alpha\mu_i$$
这个模型假设条件方差是条件均值的一个常数倍。让我们通过一个例子来解释这个模型。
假设我们研究的是一个城市中每天发生的交通事故次数。我们的数据包括一天中的时间、天气情况和是否是周末。如果我们假设 $\alpha=1$，那么方差函数变为：
$$\mathrm{Var}\left(y_i\mid\mathbf{x}\right)=\mu_i+1\mu_i$$
这意味着无论时间、天气或周末如何变化，每天发生的交通事故次数的方差始终是均值的1倍。**这显然是一个简化的模型，它忽略了实际数据中可能存在的过度分散。**
* NB2 模型

NB2 模型是最常用的模型，其方差函数为：
$$\mathrm{Var}\left(y_i\mid\mathbf{x}\right)=\mu_i+\alpha\mu_i^2$$
这个模型假设条件方差是条件均值的线性函数。让我们通过一个例子来解释这个模型。
假设我们研究的是一个城市中每天发生的交通事故次数。我们的数据包括一天中的时间、天气情况和是否是周末。如果我们假设 $\alpha=1$，那么方差函数变为：
$$\mathrm{Var}\left(y_i\mid\mathbf{x}\right)=\mu_i+1\mu_i^2$$
这意味着如果每天发生的交通事故次数增加，其方差也会增加，但增加的幅度会小于均值的增加幅度。这更符合实际情况，因为我们通常观察到数据中的过度分散。

通过这两个例子，我们可以看到，NB1 模型假设条件方差是条件均值的一个常数倍，而 NB2 模型则假设条件方差是条件均值的线性函数。在实际应用中，NB2 模型更常用于处理过度分散的数据。

### 3.2 Example of NBRM

Here we use the same example as for the PRM above:

```
use couart4, clear
nbreg art i.female i.married kid5 phd mentor
```

    Fitting Poisson model:
    
    Iteration 0:  Log likelihood = -1651.4574  
    Iteration 1:  Log likelihood = -1651.0567  
    Iteration 2:  Log likelihood = -1651.0563  
    Iteration 3:  Log likelihood = -1651.0563  
    
    Fitting constant-only model:
    
    Iteration 0:  Log likelihood = -1625.4242  
    Iteration 1:  Log likelihood = -1609.9746  
    Iteration 2:  Log likelihood = -1609.9368  
    Iteration 3:  Log likelihood = -1609.9367  
    
    Fitting full model:
    
    Iteration 0:  Log likelihood = -1565.6652  
    Iteration 1:  Log likelihood = -1561.0095  
    Iteration 2:  Log likelihood = -1560.9583  
    Iteration 3:  Log likelihood = -1560.9583  
    
    Negative binomial regression                            Number of obs =    915
                                                            LR chi2(5)    =  97.96
    Dispersion: mean                                        Prob > chi2   = 0.0000
    Log likelihood = -1560.9583                             Pseudo R2     = 0.0304
    
    ------------------------------------------------------------------------------
             art | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
          female |
         Female  |      -0.22       0.07    -2.98   0.003        -0.36       -0.07
                 |
         married |
        Married  |       0.15       0.08     1.83   0.067        -0.01        0.31
            kid5 |      -0.18       0.05    -3.32   0.001        -0.28       -0.07
             phd |       0.02       0.04     0.42   0.672        -0.06        0.09
          mentor |       0.03       0.00     8.38   0.000         0.02        0.04
           _cons |       0.26       0.14     1.85   0.065        -0.02        0.53
    -------------+----------------------------------------------------------------
        /lnalpha |      -0.82       0.12                         -1.05       -0.58
    -------------+----------------------------------------------------------------
           alpha |       0.44       0.05                          0.35        0.56
    ------------------------------------------------------------------------------
    LR test of alpha=0: chibar2(01) = 180.20               Prob >= chibar2 = 0.000

The output is similar to that of poisson except for the results at the bottom of the output, which initially can be confusing. Although the model is defined in terms of the parameter $\alpha$, nbreg estimates $\ln(\alpha)$, which forces the estimate of $a$ to be positive as required for the gamma distribution.The estimate $\widehat{\ln(\alpha)}$ is reported as **`/lnalpha`**, with the value of $a$ shown on the next line. Test statistics are not given for ln($\alpha$) or $\alpha$ because they require special treatment, as discussed next.

这个输出与泊松回归模型的输出相似，除了输出底部的一些结果，这些结果最初可能会让人感到困惑。尽管模型是通过参数 $\alpha$ 来定义的，但是 nbreg 估计的是 $\ln(\alpha)$，这使得 $\alpha$ 的估计值必须为正，这是伽马分布所要求的。估计值 $\widehat{\ln(\alpha)}$ 被报告为 **`/lnalpha`**，而 $\alpha$ 的值则在下一行显示。对于 $\ln(\alpha)$ 或 $\alpha$ 的检验统计量没有给出，因为它们需要特殊的处理，如接下来所讨论的。

在负二项回归模型中，$\alpha$ 是一个重要的参数，它控制着数据的过度分散程度。然而，由于 $\alpha$ 必须大于零（因为伽马分布的参数必须为正），所以 nbreg 提供了 $\ln(\alpha)$ 的估计值。这个估计值 $\widehat{\ln(\alpha)}$ 是模型输出中的一个组成部分，通常以 **`/lnalpha`** 表示。然后，$\alpha$ 的值（即 $\exp(\widehat{\ln(\alpha)})$）在下一行给出。

由于 $\ln(\alpha)$ 和 $\alpha$ 本身没有直接的统计检验，因此 nbreg 没有提供这些检验的统计量。这些检验需要通过 $\ln(\alpha)$ 和 $\alpha$ 的估计值来进行特殊处理，这通常涉及到对 $\alpha$ 的似然比检验或相关统计量。这些特殊处理的方法在统计文献中有详细的讨论，但通常超出了初学者的范围。

总的来说，负二项回归模型的输出提供了 $\ln(\alpha)$ 的估计值和 $\alpha$ 的值，但并没有直接提供这些值的统计检验。这些检验需要通过特殊的方法来进行，通常超出了初学者的理解范围。

### 3.3 Testing for overdispersion

Because the NBRM reduces to the PRM when $\alpha = 0$, we can test for overdispersion by testing $H_0: \alpha = 0$. There are two points to remember:

1. The nbreg command estimates $\ln(\alpha)$ rather than $a$. A test of $H_0: \ln(\alpha) = 0$ corresponds to testing $H_0: \alpha = 1$, which is not the hypothesis we want to test.
2. Because $\alpha$ must be greater than or equal to 0, the sampling distribution of $\hat{\alpha}$ when $\alpha = 0$ is only half of a normal distribution because values less than 0 have a probability of 0. This requires an adjustment to the usual significance level of the test, which is done automatically by Stata.

Stata provides a likelihood-ratio (LR) test of $H_0: \alpha = 0$ that is listed after the estimates of the parameters:

Likelihood-ratio test of alpha=0: chibar2(01) = 180.20, Prob>=chibar2 = 0.000

The test statistic chibar2(01) is computed as



\begin{align*}
\begin{array}{rcl}G^{2}&=&2\left(\operatorname{ln}L_{\mathrm{NBRM}}-\operatorname{ln}L_{\mathrm{PRM}}\right)\end{array}
\end{align*}

\begin{align*}
\quad\quad\quad\quad\quad\quad\quad\quad=\quad2\left(-1560.9583--1651.0563\right)=180.20
\end{align*}

The log likelihood for the PRM is shown in the iteration log for nbreg under the heading **Fitting Poisson model**, with the log likelihood for the NBRM shown last in the log. The significance level of the test is adjusted for the truncated sampling distribution for $\alpha$. For details, you can click on the blue linked chibar2(01) in the Results window. In our example, the test provides strong evidence of overdispersion. You can summarize this by saying the following:

>Because there is significant evidence of overdispersion  $(G^2=180.2,p<0.001)$ , the NBRM is preferred over the PRM.

As with other LR tests, this test is not available if robust standard errors are used, including when probability weights, survey estimation, or the **``cluster()``** option is used. We talk more about robust standard errors shortly. Given that, in our experience, the test usually indicates overdispersion in cases where the LR test is available, we suggest that investigators using robust standard errors begin by fitting the NBRM - simply assume there is overdispersion.


首先，我们需要理解负二项回归模型（NBRM）和泊松回归模型（PRM）之间的关系。当 $\alpha = 0$ 时，NBRM 简化为 PRM。这意味着如果数据不是过度分散的，即数据的真实方差等于模型的预测方差，那么 NBRM 就会退化为 PRM。

为了测试数据是否过度分散，我们可以测试 $H_0: \alpha = 0$。如果数据是过度分散的，那么 $H_0$ 就不会成立，我们需要一个更大的 $\alpha$ 值来拟合数据的过度分散特性。
1. **估计 $\ln(\alpha)$ 而不是 $\alpha$**：在 Stata 中的 nbreg 命令中，我们估计的是 $\ln(\alpha)$ 而不是 $\alpha$。这是因为直接估计 $\alpha$ 会导致估计值可能为负，而伽马分布的参数必须为正。因此，我们通过估计 $\ln(\alpha)$ 来确保估计值是正的。当我们说 "$\ln(\alpha) = 0$" 时，我们实际上是在说 "$\alpha = 1$"。但这并不是我们想要测试的假设，因为我们真正想要知道的是 $\alpha = 0$ 是否成立。
2. **调整显著性水平**：由于 $\alpha$ 必须大于或等于 0，当 $\alpha = 0$ 时，$\hat{\alpha}$ 的抽样分布只有正态分布的一半。这意味着在 $\alpha = 0$ 时，$\hat{\alpha}$ 变小的概率只有 50%。因此，我们需要调整显著性水平，以适应这种不对称的分布。Stata 会自动完成这个调整。

在实际应用中，Stata 提供了对 $\alpha = 0$ 的似然比检验，称为 chibar2(01)。如果这个检验的 p 值小于 0.05，我们就可以说模型存在过度分散，并选择负二项回归模型而不是泊松回归模型。

### 3.4 Comparing the PRM and NBRM using estimates table

To understand how the PRM and NBRM differ, it is useful to compare the estimates from poisson and **``nbreg``** side by side:

```
qui poisson art i.female i.married kid5 phd mentor
estimates store PRM
qui nbreg art i.female i.married kid5 phd mentor
estimates store NBRM
estimates table PRM NBRM, b(%9.3f) t p(%9.3f) varlabel ///
    drop(lnalpha) stats(alpha N) eform vsquish
```

    --------------------------------------------------
                    Variable |    PRM        NBRM     
    -------------------------+------------------------
     Gender: 1=female 0=male |                        
                     Female  |     0.799       0.805  
                             |     -4.11       -2.98  
                             |     0.000       0.003  
         Married: 1=yes 0=no |                        
                    Married  |     1.168       1.162  
                             |      2.53        1.83  
                             |     0.011       0.067  
               # of kids < 6 |     0.831       0.838  
                             |     -4.61       -3.32  
                             |     0.000       0.001  
                PhD prestige |     1.013       1.015  
                             |      0.49        0.42  
                             |     0.627       0.672  
      Mentor's # of articles |     1.026       1.030  
                             |     12.73        8.38  
                             |     0.000       0.000  
                    Constant |     1.356       1.292  
                             |      2.96        1.85  
                             |     0.003       0.065  
    -------------------------+------------------------
                       alpha |                 0.442  
                           N |       915         915  
    --------------------------------------------------
                                         Legend: b/t/p


The estimated parameters from the PRM and the NBRM are close, but the $z$-values for the NBRM are consistently smaller than those for the PRM. This is the expected consequence of overdispersion. If there is overdispersion, estimates from the PRM are inefficient, with standard errors that are biased downward (meaning that the $z$-values are inflated), even if the model includes the correct variables. As a consequence, if the PRM is used when there is overdispersion, the risk is that a variable will mistakenly be considered significant when it is not, as is the case for "married" in our example. Accordingly, it is important to test for overdispersion before using the PRM.

### 3.5 Robust standard errors

In the presence of overdispersion, the standard errors in the PRM are downwardly biased. Accordingly, **Cameron and Trivedi (2013, 85) recommend that robust standard errors be used—not only with the PRM but also with the NBRM—in case the variance specification of the model is misspecified.** To illustrate how robust standard errors can affect the statistical significance of regression coefficients, the following table compares estimates from the PRM and NBRM both with and without using robust standard errors:

```
quietly poisson art i.female i.married kid5 phd mentor
estimates store PRM
quietly poisson art i.female i.married kid5 phd mentor, vce(robust)
estimates store PRMrobust
quietly nbreg art i.female i.married kid5 phd mentor
estimates store NBRM
quietly nbreg art i.female i.married kid5 phd mentor, vce(robust)
estimates store NBRMrobust
estimates table PRM PRMrobust NBRM NBRMrobust, b(%9.3f) se(%9.4f) p(%9.3f) ///
    varlabel drop(lnalpha) stats(alpha N) eform vsquish modelwidth(10)
```

    ------------------------------------------------------------------------------
                    Variable |    PRM       PRMrobust       NBRM      NBRMrobust  
    -------------------------+----------------------------------------------------
     Gender: 1=female 0=male |                                                    
                     Female  |      0.799        0.799        0.805        0.805  
                             |     0.0436       0.0573       0.0585       0.0568  
                             |      0.000        0.002        0.003        0.002  
         Married: 1=yes 0=no |                                                    
                    Married  |      1.168        1.168        1.162        1.162  
                             |     0.0717       0.0957       0.0954       0.0936  
                             |      0.011        0.058        0.067        0.062  
               # of kids < 6 |      0.831        0.831        0.838        0.838  
                             |     0.0334       0.0465       0.0445       0.0445  
                             |      0.000        0.001        0.001        0.001  
                PhD prestige |      1.013        1.013        1.015        1.015  
                             |     0.0267       0.0425       0.0366       0.0381  
                             |      0.627        0.760        0.672        0.684  
      Mentor's # of articles |      1.026        1.026        1.030        1.030  
                             |     0.0021       0.0039       0.0036       0.0040  
                             |      0.000        0.000        0.000        0.000  
                    Constant |      1.356        1.356        1.292        1.292  
                             |     0.1397       0.1988       0.1790       0.1812  
                             |      0.003        0.038        0.065        0.068  
    -------------------------+----------------------------------------------------
                       alpha |                                0.442        0.442  
                           N |        915          915          915          915  
    ------------------------------------------------------------------------------
                                                                    Legend: b/se/p

There are several things to note. 

First, parameter estimates are not affected by using robust standard errors. For example, the coefficient for "female" is 0.799 for both PRM and PRM robust. 

Second, for the PRM, the robust standard errors are substantially smaller than the nonrobust standard errors. The most noticeable difference is with "married", where the effect is significant at the 0.01 level when robust standard errors are not used but p = 0.06 with robust standard errors. For the NBRM, the two types of standard errors are similar. 

Third, the robust standard errors in the PRM are similar to the standard errors in the NBRM, illustrating that robust standard errors for the PRM correct for the downward bias in the nonrobust standard errors. However, **even if the coefficients and standard errors for the PRM estimated with robust standard errors and those for the NBRM are similar, predicted probabilities from the two models can be quite different because these probabilities depend on the dispersion parameter for the NBRM.** In other words, **in the presence of overdispersion, the NBRM is preferred for accurate estimation of predicted probabilities.** Cameron and Trivedi (2013, sec. 3.3) show that the NBRM is also robust to distributional misspecification when robust standard errors are used.

### 3.6 Interpretation using E(y | x)

Because the mean structure for the NBRM is identical to that for the PRM, the same methods of interpretation with rates can be used. As before, the factor change for an increase of $\delta$ in $x_k$ equals

$$\frac{E\left(y\mid\mathbf{x},x_k+\delta\right)}{E\left(y\mid\mathbf{x},x_k\right)}=e^{\beta_k\delta}$$

and the corresponding percentage change equals

$$100\times\frac{E\left(y\mid\mathbf{x},x_{k}+\delta\right)-E\left(y\mid\mathbf{x},x_{k}\right)}{E\left(y\mid\mathbf{x},x_{k}\right)}=100\times\left(e^{\beta_{k}\delta}-1\right)$$


* 因子变化和百分比变化的概念
在负二项回归模型（NBRM）中，因子变化和百分比变化是用来解释自变量变化对因变量期望值的影响。这些变化可以通过对模型中的参数 $\beta_k$ 进行计算得出。
1. **因子变化**：当自变量 $x_k$ 增加一个单位时，因变量 $y$ 的期望值 $E(y|\mathbf{x}, x_k)$ 会发生变化。这个变化可以通过因子变化来衡量，即新旧期望值之比。
2. **百分比变化**：百分比变化是因子变化的一个百分比表示，它告诉我们自变量变化导致的客流量期望值变化的百分比。
* 公式分解
现在，让我们分解公式，并用一个例子来解释。
1. **因子变化**：

$$
\frac{E\left(y\mid\mathbf{x},x_k+\delta\right)}{E\left(y\mid\mathbf{x},x_k\right)}=e^{\beta_k\delta}
$$
   这里，$E\left(y\mid\mathbf{x},x_k+\delta\right)$ 是新的期望值，$E\left(y\mid\mathbf{x},x_k\right)$ 是旧的期望值，$\delta$ 是自变量 $x_k$ 的变化量，$\beta_k$ 是 $x_k$ 的系数。
   例如，假设我们研究的是餐馆的客流量，我们的自变量包括餐馆的位置、菜单价格和促销活动。如果餐馆的位置从市中心移动到郊区，根据我们的模型，客流量会相应地减少。我们可以计算这个变化，即新旧客流量之比，来了解移动对客流量的影响。
1. **百分比变化**：
$$
100\times\frac{E\left(y\mid\mathbf{x},x_{k}+\delta\right)-E\left(y\mid\mathbf{x},x_{k}\right)}{E\left(y\mid\mathbf{x},x_{k}\right)}=100\times\left(e^{\beta_{k}\delta}-1\right)
$$
这个公式告诉我们，自变量 $x_k$ 的变化导致的客流量期望值变化的百分比。
例如，如果新的客流量是旧的客流量的一半，那么这个百分比变化将是 -50%。

通过这个例子，我们可以看到如何使用因子变化和百分比变化来解释自变量对因变量的影响。这两个概念在实际应用中非常有用，可以帮助我们理解不同因素如何影响我们的结果。希望这个解释能帮助你更好地理解这些概念。

Factor and percentage change coefficients can be obtained using **``listcoef``**. For example, the factor change coefficients for **female** and **mentor** are

```
nbreg art i.female i.married kid5 phd mentor, nolog
listcoef female mentor
```

    nbreg (N=915): Factor change in expected count 
    
      Observed SD:  1.9261
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|       e^b   e^bStdX     SDofX
    -------------+----------------------------------------------------------
          female |
         Female  |   -0.2164   -2.978    0.003     0.805     0.898     0.499
          mentor |    0.0291    8.381    0.000     1.030     1.318     9.484
    ------------------------------------------------------------------------

and the percentage change coefficients are

```
listcoef female mentor, percent
```

    nbreg (N=915): Percentage change in expected count 
    
      Observed SD:  1.9261
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|         %     %StdX     SDofX
    -------------+----------------------------------------------------------
          female |
         Female  |   -0.2164   -2.978    0.003     -19.5     -10.2     0.499
          mentor |    0.0291    8.381    0.000       3.0      31.8     9.484
    ------------------------------------------------------------------------

These coefficients can be interpreted as follows:

>Being a female scientist decreases the expected number of articles by a factor of 0.805, holding other variables constant. Equivalently, being a female scientist decreases the expected number of articles by 19.5%, holding other variables constant.
>For every additional article by the mentor, a scientist’s expected rate of productivity increases by 3.0%, holding other variables constant.
>For a standard deviation increase in the mentor’s productivity, roughly 10 articles, a scientist’s expected productivity increases by 32%, holding other variables constant.

As noted earlier, factor and percentage change coefficients for count models are often quite effective for interpretation in contrast to models in previous chapters for which we think factor and percentage changes in odds are not as readily understood.

The similarity in magnitude of coefficients in the PRM and the NBRM, as well as the difference in standard errors when there is overdispersion, also affects predictions. To see this, we compute the rate of productivity for women from elite programs who have mentors with different levels of productivity:

```
poisson art i.female i.married kid5 phd mentor, nolog
quietly mtable, estname(PRM_mu) ci at(mentor=(0(5)20) female=1 phd=4) ///
    atmeans clear dec(2)
nbreg art i.female i.married kid5 phd mentor, nolog
mtable, estname(NBRM_mu) ci at(mentor=(0(5)20) female=1 phd=4) ///
    atmeans right dec(2) noatvars norownumbers brief
```

    Expression: Predicted number of art, predict()
    
      mentor    PRM_mu        ll        ul   NBRM_mu        ll        ul
    --------------------------------------------------------------------
           0      1.15      1.03      1.27      1.12      0.96      1.28
           5      1.31      1.18      1.44      1.30      1.13      1.46
          10      1.49      1.35      1.63      1.50      1.32      1.68
          15      1.69      1.53      1.85      1.73      1.52      1.95
          20      1.92      1.74      2.11      2.00      1.73      2.28

The predicted rates, in the columns **PRM_mu** and **NBRM_mu**, are similar, but the confidence intervals for the PRM are narrower than those for the NBRM, reflecting the underestimation of standard errors in the PRM when there is overdispersion. In the next section, we will show how predicted probabilities differ between the two models even when the rates are similar.

Marginal effects for the NBRM can be computed and interpreted exactly as they were for the PRM. For example, we can compute the average discrete change for increasing the number of young children in a scientist's family by 1:

```
nbreg art i.female i.married kid5 phd mentor, nolog
mchange kid5, amount(one) stats(ci) brief
```

    nbreg: Changes in mu | Number of obs = 915
    
    Expression: Predicted number of art, predict()
    
                 |    Change         LL         UL 
    -------------+--------------------------------
    kid5         |                                
              +1 |    -0.276     -0.426     -0.125 
    
### 3.7 Interpretation using predicted probabilities

In the presence of overdispersion, predicted probabilities from the NBRM can differ substantially from those for the PRM, even though the predicted rates are similar. The methods used to interpret predicted probabilities, however, are the same with probabilities for the NBRM computed with

$$\Pr\left(y\mid\mathbf{x}\right)=\frac{\Gamma(y+\alpha^{-1})}{y!\Gamma(\alpha^{-1})}\left(\frac{\alpha^{-1}}{\alpha^{-1}+\mu}\right)^{\alpha^{-1}}\left(\frac{\mu}{\alpha^{-1}+\mu}\right)^{y}$$

where $\mu=\exp(\mathbf{x}\boldsymbol{\beta})$ Predicted probabilities for observed values of the independent variables can be computed using **``predict``**. Average predicted probabilities or predicted probabilities at specific values can be calculated using **``mtable``**, **``mgen``**, **``mchange``**, or **``margins``**. Because there is nothing new in how to use these commands with the NBRM, we provide only a few examples that are designed to illustrate key differences and similarities between the PRM and the NBRM.

In this example, we use **``mtable``** to compare predicted probabilities for counts 0 to 7, specified with the option **``pr(0/7)``**, for the two models for a hypothetical case of someone who is average on all characteristics.

```
poisson art i.female i.married kid5 phd mentor, nolog
estimates store PRM
quietly mtable, rowname(PRM) pr(0/7) atmeans
nbreg art i.female i.married kid5 phd mentor, nolog
estimates store NBRM
mtable, rowname(NBRM) pr(0/7) atmeans below brief decimals(3) width(6)
```

    Expression: Pr(art), predict(pr())
    
               |      0       1       2       3       4       5       6       7
     ----------+---------------------------------------------------------------
           PRM |  0.200   0.322   0.259   0.139   0.056   0.018   0.005   0.001
          NBRM |  0.298   0.279   0.189   0.111   0.061   0.031   0.016   0.008


The predicted probability of a 0 is 0.20 for the PRM and 0.30 for the NBRM, a substantial difference. This difference is offset by higher probabilities for the PRM for counts 1-3. At 4 and above, the probabilities are larger for the NBRM. The larger probabilities at the higher and lower counts reflect the greater dispersion in the NBRM compared with the PRM. Differences between predictions in the NBRM and the PRM are generally most noticeable for 0s. You can think of it this way. With overdispersion, the variance in the predicted counts increases. Because counts cannot be smaller than 0, the increased variation below the mean leads to predictions “stacking up” at 0. Above the mean, the greater variation affects all values.

To highlight the greater probability of a 0 in the NBRM, we plot the probability of 0s as mentor productivity increases from 0 to 50, holding other variables at the mean. Using **``mgen``**, we make predictions for the probability of 0 at different levels of the mentor’s productivity for the PRM:

```
poisson art i.female i.married kid5 phd mentor, nolog
estimates restore PRM
mgen, atmeans at(mentor=(0(5)50)) stub(PRM) pr(0)
```

    Predictions from: margins, atmeans at(mentor=(0(5)50)) predict(pr(0))
    
    Variable   Obs Unique      Mean       Min       Max  Label
    -------------------------------------------------------------------------------
    PRMpr0      11     11   .110384   .009894  .2760837  pr(y=0) from margins
    PRMll0      11     11  .0954709  .0026402  .2517684  95% lower limit
    PRMul0      11     11   .125297  .0171479  .3003991  95% upper limit
    PRMmentor   11     11        25         0        50  Mentor's # of articles
    PRMCpr0     11     11   .110384   .009894  .2760837  pr(y<=0)
    -------------------------------------------------------------------------------
    
    Specified values of covariates
    
            1.         1.                      
       female    married       kid5        phd 
    ------------------------------------------
     .4601093   .6622951    .495082   3.103109 

We make corresponding predictions for the NBRM:

```
nbreg art i.female i.married kid5 phd mentor, nolog
estimates restore NBRM
mgen, atmeans at(mentor=(0(5)50)) stub(NBRM) pr(0)
```

    Predictions from: margins, atmeans at(mentor=(0(5)50)) predict(pr(0))
    
    Variable    Obs Unique      Mean       Min       Max  Label
    -----------------------------------------------------------------------------
    NBRMpr0      11     11  .1954706  .0648641   .371642  pr(y=0) from margins
    NBRMll0      11     11  .1629587  .0318453  .3382501  95% lower limit
    NBRMul0      11     11  .2279824  .0978828  .4050339  95% upper limit
    NBRMmentor   11     11        25         0        50  Mentor's # of articles
    NBRMCpr0     11     11  .1954706  .0648641   .371642  pr(y<=0)
    -----------------------------------------------------------------------------
    
    Specified values of covariates
    
            1.         1.                      
       female    married       kid5        phd 
    ------------------------------------------
     .4601093   .6622951    .495082   3.103109 

```
label var PRMpr0 "PRM"
label var NBRMpr0 "NBRM"
```

```
graph twoway ///
    (rarea PRMll0 PRMul0 NBRMmentor, color(gs14)) ///
    (rarea NBRMll0 NBRMul0 NBRMmentor, color(gs14)) ///
    (connected PRMpr0 NBRMmentor, lpattern(dash) msize(zero)) ///
    (connected NBRMpr0 NBRMmentor, lpattern(solid) msize(zero)), ///
    legend(on order(3 4)) ylabel(0(.1).4, gmax) ///
    ytitle("Probability of a zero count")
```

The **`rarea`** graphs add the shaded confidence intervals, with the **`connected`** graphs drawing the lines for predicted probabilities. The **``order()``** suboption of the **``legend``** option indicates to order the lines in the legend to start with the third graph (that is, the first connected graph), followed by the fourth. Because graphs 1 and 2 are not included in **``order()``**, no entries are included in the legend for the rarea graphs, which is what we want. The following graph is created:

For both models, the probability of a 0 decreases as the mentor’s productivity increases, but the predicted probability of a scientist not publishing is substantially larger for the NBRM. Because both models have roughly the same expected number of publications, the higher proportion of 0s for the NBRM is being offset by the higher proportion of larger counts that are predicted by this model. The smaller confidence interval for the predictions from the PRM reflects the underestimation of standard errors when there is overdispersion. In this example, the PRM substantially underestimates the probability of a scientist not publishing, but it does this with excessive precision!

## 4 Models for truncated counts

Modeling zeros often poses special problems in count models. One type of problem arises when observations with outcomes equal to 0 are missing from the sample because of the way the data were collected. For example, suppose that we are gathering data to study scientific productivity among chemists but that we do not have a sampling roster of all chemists with PhDs. One solution is to take a sample from those scientists who published at least one article that was listed in Chemical Abstracts, which excludes all chemists who have not published. Other examples of truncation are easy to find. A study of how many times people visit national parks could be based on a survey given to people entering the park. Or, when you fill out a customer satisfaction survey after buying a TV, you might be asked how many TVs are in your household, leading to a dataset in which everyone has at least one TV.

Zero-truncated count models are designed for instances like these, where observations with an outcome of 0 exist in the population but have been excluded from the sample. The zero-truncated Poisson model (ZTP) begins with the PRM from section 9.2:

$$\Pr\left(y_{i}=k\mid\mathbf{x}\right)=\frac{\exp\left(-\mu_{i}\right)\mu_{i}^{y_{i}}}{y_{i}!}$$

where $\mu_i=\exp\left(\mathbf{x}_i\boldsymbol{\beta}\right)$  For a given the $x_i$,the probability of observing a 0 is 

$$\Pr\left(y_i=0\mid\mathbf{x}_i\right)=\exp\left(-\mu_i\right)$$

and the probability of a positive (that is, nonzero) count is

$$\Pr\left(y_{i}>0\mid\mathbf{x}_{i}\right)=1-\exp\left(-\mu_{i}\right)$$

Because our counts are truncated at 0, we want to compute the probability for each positive count given that we know the count must be greater than 0 for a case to be observed. By the law of conditional probability,

$$\Pr\left(A\mid B\right)=\frac{\Pr\left(A\And B\right)}{\Pr\left(B\right)}$$

Thus the conditional probability of observing a specific value $y = k$ given that we know the count is not 0 is

$$\Pr(y_i=k|y_i>0,\mathbf{x}_i) = \frac{\Pr(y_i=k \And y_i>0|\mathbf{x}_i)}{\Pr(y_i>0|\mathbf{x}_i)}$$

Given the probability that $y = k$ and $y > 0$ is simply the probability that $y = k$, and substituting $(9.6)$ leads to the conditional probability

$$\Pr\left(y_{i}=k\mid y_{i}>0,\mathbf{x_i}\right)=\frac{\Pr\left(y_{i}=k\mid\mathbf{x_i}\right)}{1-\exp\left(-\mu_{i}\right)}$$

This formula increases each unconditional probability by the factor $\{1-\exp\left(-\mu\right)\}^{-1}$ forcing the probability mass of the truncated distribution to sum to 1.

好的，我们来一步一步地理解零截断计数模型（Zero-truncated count models），以及它是如何应用于实际情况中的。

首先，我们需要理解什么是截断（truncation）。在统计学中，截断是指数据集中缺失了某些值，通常是因为数据收集的方式导致了某些结果的出现。例如，如果我们在研究化学家的科学生产力，但我们没有所有拥有博士学位的化学家的名单，我们可能会选择从那些在《化学文摘》上至少发表了一篇文章的科学家中抽取样本。这样，那些从未发表过文章的化学家就不会出现在样本中，这就是一个截断的例子。

接下来，我们来看零截断泊松模型（Zero-truncated Poisson model, ZTP）。这个模型是为了处理那些在总体中存在但因为样本收集方式而被排除的零观测值的情况。

我们首先有一个基本的概率公式，它是泊松回归模型（Poisson regression model, PRM）的一部分：

$$\Pr\left(y_{i}=k\mid\mathbf{x}\right)=\frac{\exp\left(-\mu_{i}\right)\mu_{i}^{y_{i}}}{y_{i}!}$$
在这个公式中：

- $\Pr(y_i = k|\mathbf{x})$ 表示在给定 $\mathbf{x}$ 的条件下，观测值 $y_i$ 等于 $k$ 的概率。
- $\mu_i$ 是随机变量 $y_i$ 的期望值，也就是在给定 $\mathbf{x}$ 的条件下，$y_i$ 的平均值。
- $e$ 是自然对数的底数，大约等于 2.71828。
- $\mathbf{x}$ 是解释变量的向量。
- $\boldsymbol{\beta}$ 是模型参数的向量。
- $y_i!$ 是 $y_i$ 的阶乘，即 $y_i$ 乘以所有小于它的正整数的乘积。

接下来，我们来看在给定 $\mathbf{x}_i$ 的条件下，观测值为 0 的概率：

$$\Pr(y_i=0|\mathbf{x}_i) = e^{-\mu_i}$$

这里的 $e^{-\mu_i}$ 表示 $\mu_i$ 取负值后，再求 $e$ 的幂。

然后，我们来看在给定 $\mathbf{x}_i$ 的条件下，观测值大于 0 的概率：

$$\Pr(y_i>0|\mathbf{x}_i) = 1 - e^{-\mu_i}$$

这个概率是 1 减去观测值为 0 的概率，因为一个计数要么为 0，要么大于 0。

现在，我们想要计算的是，在我们知道观测值已经大于 0 的情况下，观测值为某个正数 $k$ 的条件概率。这就是条件概率的概念：

$$\Pr(A|B) = \frac{\Pr(A \And B)}{\Pr(B)}$$

在这里，$A$ 是观测值 $y = k$ 的事件，$B$ 是观测值 $y > 0$ 的事件。根据条件概率的定义，我们可以写出：

$$\Pr(y_i=k|y_i>0,\mathbf{x}_i) = \frac{\Pr(y_i=k \And y_i>0|\mathbf{x}_i)}{\Pr(y_i>0|\mathbf{x}_i)}$$

由于 $y = k$ 且 $y > 0$ 的概率就是 $y = k$ 的概率，所以我们可以将 $\Pr(y_{i}=k|\mathbf{x}_i)$ 直接代入，得到：

$$\Pr\left(y_{i}=k\mid y_{i}>0,\mathbf{x_i}\right)=\frac{\Pr\left(y_{i}=k\mid\mathbf{x_i}\right)}{1-\exp\left(-\mu_{i}\right)}$$

这个公式通过乘以一个因子 $\{1 - e^{-\mu}\}^{-1}$ 来调整原本的概率，使得所有正数的计数概率之和为 1。这样，我们就得到了在考虑了截断的情况下，观测值为 $k$ 的条件概率。

让我们通过一个例子来进一步理解这个概念。假设我们想要研究人们一年内访问国家公园的次数。如果我们在公园入口处进行调查，那么我们只会得到那些至少访问过一次公园的人的数据。这意味着，那些从未访问过公园的人的数据被截断了。
假设我们有一个简单的泊松模型，其中 $\mu_i$ 是一个人访问公园的平均次数，取决于他们居住的距离（解释变量 $x_i$）和一些其他因素（模型参数 $\boldsymbol{\beta}$）。
- 如果一个人居住地距离公园很远，那么 $\mu_i$ 可能很小，因此 $e^{-\mu_i}$ 会接近 1，这意味着观测值为 0 的概率很高。但是，因为我们只在公园入口处进行调查，所以我们知道这个人至少访问了一次公园。因此，我们需要调整这个概率，使其不为 0。
- 如果一个人居住地距离公园很近，那么 $\mu_i$ 可能较大，因此 $e^{-\mu_i}$ 会小于 1，这意味着观测值为 0 的概率较低。在这种情况下，调整后的概率会更接近原始的泊松模型概率，因为我们更有可能观察到至少一次访问。

通过这种方式，零截断泊松模型可以帮助我们估计在考虑了数据收集方式导致的截断后的概率分布。这对于理解和解释实际数据非常重要，因为它允许我们考虑到那些可能因为数据收集方法而被遗漏的信息。

当然可以。让我们通过几个生活中的例子来进一步理解零截断计数模型的概念。

* 例子1：图书馆借阅记录

假设你想研究一个学期内学生借阅图书的平均次数。你从图书馆获取了一份数据，包含了所有在该学期至少借阅过一本书的学生的借阅记录。这个数据集就存在零截断问题，因为那些整个学期一本书都没借的学生并没有出现在你的数据中。

使用零截断泊松模型，你可以调整这种偏差。模型会考虑学生的年级、专业、课程负担等因素（这些是解释变量 $\mathbf{x}$），来预测一个学生在一个学期内借阅图书的次数（这是随机变量 $y_i$）。然后，你可以计算在已知至少借阅一本书的情况下，一个学生借阅 $k$ 本书的条件概率。

* 例子2：交通事故统计

一个交通部门想要分析某地区交通事故的发生频率。他们从警察局获取了一份报告，记录了所有报告给警察局的交通事故。然而，许多小事故可能没有被报告，这就造成了数据的零截断。

通过零截断计数模型，交通部门可以调整这种偏差，考虑事故发生的时间、地点、路况等因素（解释变量 $\mathbf{x}$），来预测事故发生的频率（随机变量 $y_i$）。这样，即使在数据中没有记录的零事故日，也能估计出事故发生的概率。

* 例子3：生物学研究中的物种计数

生物学家可能对一个特定区域内某种鸟类的出现次数感兴趣。他们通过观察和记录所有被观察到的鸟类来收集数据。然而，有些日子可能一只鸟也没有观察到，这些零计数就被忽略了，因为只有在鸟类出现的日子才会有记录。

零截断计数模型可以帮助生物学家调整这种偏差，通过考虑季节、气候、观察时间和地点等因素（解释变量 $\mathbf{x}$），来预测在特定条件下观察到鸟类出现的次数（随机变量 $y_i$）。这样，即使在没有观察到鸟类的日子里，也能估计出鸟类出现的概率。

通过这些例子，我们可以看到零截断计数模型如何帮助我们处理那些因为数据收集方式而导致的零观测值的问题。这种模型通过调整概率分布，使得我们可以更准确地估计在实际情况下，事件发生的频率和可能性。

With a truncated model, there are two types of expected counts that might be of interest. First, we can compute the expected rate without truncation. For example, we might want to estimate the expected number of publications for scientists in the entire population, not just among those who have at least one article. We will refer to this as the unconditional prediction. Importantly, we use “unconditional” to indicate that the prediction applies to the whole population; unconditional predictions are still, of course, conditional on $x$. In the Z TP, the unconditional rate has the same formula as for the standard PRM:

$$E(y_i\mid\mathbf{x}_i)=\exp\left(\mathbf{x}_i\boldsymbol{\beta}\right)$$

Second, we can compute the expected rate given that the count is positive, written as $E\left(y\mid y>0,\mathbf{x}\right)$

For example, among those who publish, what is the expected number of publications? Or, among those who see the doctor at least once a year, what is the average number of visits with a doctor? The conditional rate must be larger than the unconditional rate because we are excluding 0s. As with the conditional probability in (9.7), the rate increases proportionally to the probability of a positive count:

$$E\left(y_i\mid y_i>0,\mathbf{x}_i\right)=\frac{\mu_i}{\Pr\left(y_i>0\mid\mathbf{x}_i\right)}=\frac{\mu_i}{1-\exp(-\mu_i)}$$

The same idea applies to the NBRM , where

$$\Pr\left(y_i\mid\mathbf{x}_i\right)=\frac{\Gamma(y_i+\alpha^{-1})}{y_i!\Gamma(\alpha^{-1})}\left(\frac{\alpha^{-1}}{\alpha^{-1}+\mu_i}\right)^{\alpha^{-1}}\left(\frac{\mu_i}{\alpha^{-1}+\mu_i}\right)^{y_i}$$

Accordingly, $\Pr\left(y=0\mid\mathbf{x}\right)=\left(1+\alpha\mu\right)^{-1/\alpha}$  and  $\Pr\left(y>0\mid\mathbf{x}\right)=1-\left(1+\alpha\mu\right)^{-1/\alpha}$ . The conditional probability in the zero-truncated negative binomial model (ZTNB) is

$$\Pr\left(y_i\mid y_i>0,\mathbf{x}_i\right)=\frac{\Pr\left(y_i\mid\mathbf{x}_i\right)}{1-\left(1+\alpha\mu_i\right)^{-1/\alpha}}$$

and the conditional mean equals 

$$E(y_i\mid y_i>0,\mathbf{x}_i)=\frac{\mu_i}{1-(1+\alpha\mu_i)^{-1/\alpha}}$$

The adverse effects of overdispersion are worse with truncated models. When the sample is not truncated, using the PRM in the presence of overdispersion underestimates the standard errors but does not bias the estimated $\beta$'s. With the ZTP, overdispersion results in biased and inconsistent estimates of the $\beta$'s and, consequently, in biased estimates of rates and probabilities (Grogger and Carson 1991). Accordingly, before using the ZTP, you should check for overdispersion by fitting the ZTNB. As with the NBRM, overdispersion in the ZTNB is based on an LR test of $\alpha = 0$, which is included in the output for the ZTNB (see page 511).



* 无截断条件下的期望计数（Unconditional Prediction）

这个概念就像是我们想要估计整个人群中某个事件发生的平均次数，而不仅仅是那些至少发生了一次的人群。例如，假设我们想要估计所有科学家的平均发表论文数量。我们不仅对那些至少发表过一篇文章的科学家感兴趣，我们也想包括那些可能一篇论文都没发表的科学家。这种预测被称为“无条件预测”，因为它适用于整个人群，而不仅仅是样本中的一部分。

在零截断泊松模型（ZTP）中，我们可以使用下面的公式来计算这个无条件期望计数：

$$E(y_i|\mathbf{x}_i) = \exp(\mathbf{x}_i\boldsymbol{\beta})$$

这里的 $\exp$ 是指数函数，它将解释变量 $\mathbf{x}_i$ 通过模型参数 $\boldsymbol{\beta}$ 转换后的结果转换为一个正数，这个正数就是预测的发表论文数量。

* 给定计数大于0的期望计数（Conditional Prediction）

这个概念是当我们只关注那些至少有一次计数的个体时，事件发生的平均次数。例如，我们可能想要知道那些至少发表过一篇文章的科学家平均发表论文的数量。或者，我们可能想要知道那些至少一年看一次医生的人平均看医生的次数。这种条件下的期望计数必须大于无条件下的期望计数，因为我们排除了计数为零的情况。

在零截断泊松模型中，我们可以使用下面的公式来计算这个条件期望计数：

$$E(y_i|y_i>0,\mathbf{x}_i) = \frac{\mu_i}{1-\exp(-\mu_i)}$$

这里的 $\mu_i$ 是根据解释变量 $\mathbf{x}_i$ 和模型参数计算出的每个科学家的平均发表论文数量。公式中的 $\exp(-\mu_i)$ 表示没有发表论文的概率，所以我们用 $1-\exp(-\mu_i)$ 来表示至少发表一篇论文的概率。然后，我们将平均发表论文数量 $\mu_i$ 除以这个概率，得到在至少发表一篇论文的情况下，科学家平均发表论文的数量。

* 零截断负二项模型（ZTNB）

零截断负二项模型（ZTNB）是另一种处理过度离散数据的模型。过度离散是指实际观测到的方差大于模型预测的方差。在ZTNB模型中，我们使用下面的公式来计算随机变量 $y_i$ 的概率分布：

$$\Pr(y_i|\mathbf{x}_i) = \frac{\Gamma(y_i+\alpha^{-1})}{y_i!\Gamma(\alpha^{-1})}\left(\frac{\alpha^{-1}}{\alpha^{-1}+\mu_i}\right)^{\alpha^{-1}}\left(\frac{\mu_i}{\alpha^{-1}+\mu_i}\right)^{y_i}$$

这里的 $\Gamma$ 是伽马函数，$\alpha$ 是模型中的一个参数，用于调整计数的变异性。$\mu_i$ 是平均计数，它依赖于解释变量 $\mathbf{x}_i$。

对于零计数的概率，我们有：

$$\Pr(y=0|\mathbf{x}) = \left(1+\alpha\mu\right)^{-1/\alpha}$$

而在计数大于0的条件下的概率则是：

$$\Pr(y>0|\mathbf{x}) = 1 - \left(1+\alpha\mu\right)^{-1/\alpha}$$

在ZTNB中，条件概率的计算方式如下：

$$\Pr(y_i|y_i>0,\mathbf{x}_i) = \frac{\Pr(y_i|\mathbf{x}_i)}{1-\left(1+\alpha\mu_i\right)^{-1/\alpha}}$$

条件均值（conditional mean）则是：

$$E(y_i|y_i>0,\mathbf{x}_i) = \frac{\mu_i}{1-(1+\alpha\mu_i)^{-1/\alpha}}$$

* 过度离散的影响

过度离散在截断模型中的影响尤其严重。如果样本没有截断，使用泊松回归模型（PRM）时，过度离散可能会低估标准误差，但不会影响估计的 $\beta$ 参数的偏差。然而，在ZTP模型中，过度离散会导致 $\beta$ 参数的估计值有偏差且不一致，从而导致计数和概率的估计也有偏差。

因此，在实际应用ZTP模型之前，我们应该通过拟合ZTNB模型来检查是否存在过度离散。如果ZTNB模型的输出结果中，对 $\alpha = 0$ 的似然比检验（LR test）显著，那么我们可以认为存在过度离散。

通过这些解释和例子，我们可以看到零截断模型中的期望计数概念是如何应用在实际情况中的，以及过度离散对模型估计的影响。希望这些解释能够帮助你更好地理解这些概念。如果还有不清楚的地方，请随时告诉我。

在实际应用中，选择合适的模型来处理过度离散数据通常涉及以下几个步骤：

1. **识别过度离散**：首先，需要确定数据是否表现出过度离散。过度离散通常意味着计数数据的方差大于均值，这违反了泊松回归模型的关键假设，即方差等于均值。可以通过计算残差与拟合值的比率、使用卡方检验或其他统计测试来识别过度离散。

2. **模型选择**：如果数据表现出过度离散，泊松回归模型可能不再是最佳选择。此时，可以考虑使用负二项回归模型（Negative Binomial Regression Model）。负二项回归模型在泊松回归模型的基础上增加了一个参数来捕捉额外的变异性，允许方差大于均值，从而更好地拟合数据。

3. **模型比较**：在选择了可能适用的模型后，可以使用信息准则（如赤池信息准则AIC）来比较不同模型的拟合优度。AIC较低的模型通常被认为是更好的模型，因为它平衡了模型复杂度和拟合数据的能力。

4. **模型诊断**：拟合模型后，进行模型诊断是至关重要的。检查残差图、拟合优度测试和参数的显著性，以确保模型假设得到满足。如果模型仍然表现出过度离散，可能需要考虑更复杂的模型，如零膨胀模型（Zero-inflated Models）或零截断模型（Zero-truncated Models）。

5. **考虑其他因素**：在选择模型时，还需要考虑数据的其他特性，如是否存在零计数的膨胀（Zero Inflation）或数据是否被截断。如果数据中存在大量的零计数，可能需要使用零膨胀或零截断模型来更准确地反映数据的生成过程。

6. **使用软件工具**：在实践中，可以使用统计软件（如R、Stata或SAS）中的函数和包来实现上述模型。这些软件提供了丰富的工具来拟合模型、进行诊断和比较不同模型的效果。

通过上述步骤，研究者可以更系统地选择和验证适合处理过度离散数据的模型，从而得到更准确和可靠的分析结果。

要判断一个模型是否适合处理过度离散的数据，可以遵循以下步骤：

1. **识别过度离散的迹象**：首先，需要确定数据是否表现出过度离散。过度离散通常意味着计数数据的方差大于均值，这违反了泊松回归模型的关键假设，即方差等于均值。可以通过计算残差与拟合值的比率、使用卡方检验或其他统计测试来识别过度离散。

2. **拟合泊松回归模型**：作为起点，先拟合一个泊松回归模型。如果数据的离散程度比泊松模型所预期的要大，那么这些数据就不符合泊松分布。

3. **检查模型的偏差比率**：模型的偏差比率是偏差（一种衡量模型错误的方法）除以自由度（一种衡量模型复杂性的方法）。一个良好拟合的模型应该有一个接近1.0的偏差比率。如果值偏高，就表示存在过度离散或离散过小。

4. **使用负二项回归模型**：如果数据表现出过度离散，泊松回归模型可能不再是最佳选择。此时，可以考虑使用负二项回归模型，它在泊松回归模型的基础上增加了一个参数来捕捉额外的变异性，允许方差大于均值。

5. **比较模型的拟合优度**：可以使用信息准则，如赤池信息准则（AIC），来比较不同模型的拟合优度。AIC较低的模型通常被认为是更好的模型，因为它平衡了模型复杂度和拟合数据的能力。

6. **进行模型诊断**：拟合模型后，进行模型诊断是至关重要的。检查残差图、拟合优度测试和参数的显著性，以确保模型假设得到满足。如果模型仍然表现出过度离散，可能需要考虑更复杂的模型，如零膨胀模型或零截断模型。

7. **使用统计软件工具**：在实践中，可以使用统计软件（如R、Stata或SAS）中的函数和包来实现上述模型。这些软件提供了丰富的工具来拟合模型、进行诊断和比较不同模型的效果。

通过上述步骤，研究者可以更系统地选择和验证适合处理过度离散数据的模型，从而得到更准确和可靠的分析结果。


### 4.1 Estimation using tpoisson and tnbreg

The ZTP is fit with the command **`tpoisson`**, and the ZTNB is fit with the command **`tnbreg`**.

* **`tpoisson depvar [indepvars] [if] [in] [weight] [, options]`**

* **`tnbreg depvar [indepvars] [if] [in] [weight] [, options]`**

where options are largely the same as those for **`poisson`** and **`nbreg`**. **These commands can also be used when the sample is truncated at a value greater than 0.** Imagine a study in which the outcome is number of criminal offenses with data that are collected only on people with multiple offenses, so only people with a count of at least two are included in the dataset. **A model for this outcome can be fit by adding the **`ll(#)`** option to either **`tpoisson`** or **`tnbreg`**, where # is the value of the largest nonobserved count.** In the example where only individuals with a count of at least two are included, this option should be specified as **`ll(1)`**. **The default for both **`tpoisson`** and **`tnbreg`** is a zero-truncated model (that is, **`ll(0)`**), so you do not have to specify this option for zero-truncated models.** 

* Example of zero-truncated model

To illustrate zero-truncated count models, we continue our example of scientific productivity with the outcome variable **`artrunc`**, which artificially recodes values of 0 in **`art`** to missing. We begin by truncating the sample to exclude scientists who have no publications.

```
use couart4, clear

quietly nbreg art i.female i.married kid5 phd mentor, nolog vce(robust)
estimates store NBRM

sum art arttrunc
drop if art==0
```

        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
             art |        915    1.692896    1.926069          0         19
        arttrunc |        640    2.420313    1.882269          1         19

After dropping those 275 observations, we have a truncated sample of 640 scientists.

Next, we fit the ZTNB with the truncated sample, where we used the **`irr`** option so that the parameters are shown as factor change coefficients:

```
tnbreg arttrunc i.female i.married kid5 phd mentor, nolog irr
estimates store ZTNBRM
```
    
    
        Truncated negative binomial regression                  Number of obs =    640
        Truncation point = 0                                    LR chi2(5)    =  44.58
        Dispersion: mean                                        Prob > chi2   = 0.0000
        Log likelihood = -1027.3185                             Pseudo R2     = 0.0212
        
        ------------------------------------------------------------------------------
            arttrunc |        IRR   Std. err.      z    P>|z|     [95% conf. interval]
        -------------+----------------------------------------------------------------
              female |
             Female  |       0.78       0.08    -2.52   0.012         0.65        0.95
                     |
             married |
            Married  |       1.11       0.12     0.95   0.345         0.89        1.37
                kid5 |       0.86       0.06    -2.12   0.034         0.74        0.99
                 phd |       1.00       0.05    -0.06   0.951         0.91        1.10
              mentor |       1.02       0.00     5.54   0.000         1.02        1.03
               _cons |       1.43       0.28     1.80   0.071         0.97        2.10
        -------------+----------------------------------------------------------------
            /lnalpha |      -0.60       0.22                         -1.04       -0.16
        -------------+----------------------------------------------------------------
               alpha |       0.55       0.12                          0.35        0.85
        ------------------------------------------------------------------------------
        Note: Estimates are transformed only in the first equation to incidence-rate ratios.
        Note: _cons estimates baseline incidence rate.
        LR test of alpha=0: chibar2(01) = 105.43               Prob >= chibar2 = 0.000

The output looks like that from **`nbreg`** except that the title now says “Truncated negative binomial regression” and the truncation point is listed. The LR test provides clear evidence of overdispersion, as was the case with the NBRM. Indeed, the ZTNB is estimating the same parameters as the NBRM but with more limited data. The effects of estimating the parameters with the truncated sample can be seen by comparing the estimates from the two models:

```
estimates table NBRM ZTNBRM, b(%9.3f) stats(N) t eform
```

    --------------------------------------
        Variable |   NBRM       ZTNBRM    
    -------------+------------------------
                 |   art        arttrunc
          female |
         Female  |     0.805     0.783          
                 |    -3.07     -2.52         
                 |
         married |
        Married  |     1.162     1.109         
                 |      1.87     0.95         
                 |
            kid5 |     0.838     0.858         
                 |    -3.32     -2.12         
             phd |     1.015     0.997         
                 |     0.41     -0.06         
          mentor |     1.030     1.024         
                 |     7.49      5.54         
           _cons |     1.292     1.426         
                 |     1.83      1.80         
    -------------+------------------------
        /lnalpha |     0.442       0.547  
                 |     -6.54       -2.68  
    -------------+------------------------
    Statistics   |                        
               N |      915         640  
    --------------------------------------
                               Legend: b/t

Although the estimates are similar, there is more sampling variability in the ZTNBRM (indicated by the smaller z-values), which is expected given that estimation uses more limited data.

### 4.2 Interpretation using E(y | x)

If counts of zero are missing from your sample because of the way the data were collected, the parameters can be interpreted in the same way as those for the PRM and the NBRM. **Essentially, the model fills in the data that were lost when the data were collected. Accordingly, $exp(\beta_k)$ is the factor increase in the rate for a unit increase in $x_k$, holding other variables constant. Keep in mind that we are referring to the factor change in the unconditional rate $E(y|x)$ not the conditional rate $E(y|y > 0, x)$.**

As with the NBRM and the PRM, you can use the **`irr`** option (as shown in the output above) or use **`listcoef`** to compute factor change coefficients after running **`tpoisson`** or **`tnbreg`**.

```
listcoef female mentor
```

    tnbreg (N=640): Factor change in expected count 
    
      Observed SD:  1.8823
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|       e^b   e^bStdX     SDofX
    -------------+----------------------------------------------------------
          female |
         Female  |   -0.2447   -2.517    0.012     0.783     0.886     0.497
          mentor |    0.0237    5.538    0.000     1.024     1.278    10.329
    ------------------------------------------------------------------------

Here are some examples of interpreting these results:

* Being a female scientist decreases the expected number of papers by a factor of 0.78, holding other variables constant.
* Each additional publication by the mentor increases the predicted number of publications by 2.4%, holding other variables constant.
* A standard deviation increase in publication by the mentor increases the predicted number of publications by a factor of 1.28, holding other variables constant.

### 4.3 Predictions in the estimation sample

Running **`predict`** after fitting a truncated count model will generate a new variable with predictions based on the values of the independent variable for each observation. The predictions can be conditional or unconditional. Consider our hypothetical example in which scientists are in the sample only if they have published at least once. Unconditional predictions are predictions about the entire population of scientists, regardless of whether they have published. Conditional predictions are predictions conditional on the count being greater than the truncation point. If we want to make predictions about those who have published (that is, the count is greater than 0), we would use conditional predictions. As noted before, both unconditional and conditional predictions are still conditional on the values of the independent variables.

By default, **`predict`** computes the unconditional rate. We can obtain the unconditional predicted probability of a specific count $Pr(y = k)$ with the option **`pr(fc)`** and the predicted probability of a count within a range $Pr(m < y < n)$ with the option **`pr(m,n)`**. We can also compute conditional predictions that are conditional on the count being greater than the truncation point. We obtain conditional predictions about the rate with the option **`cm`** and conditional predicted probabilities with the option **`cpr()`** instead of **`pr()`**.

In our example, we use **`predict`** followed by **`summarize`** to show the average unconditional and conditional rates and to compare those with the mean number of articles in the sample.

```
predict rate
label var rate "Unconditional rate of productivity"
predict crate, cm
label var rate "Conditional rate of productivity given at least 1 article"
summarize rate crate arttrunc
```

        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
            rate |        640    1.687704     .641613   .9468752   8.397242
           crate |        640    2.425643    .5871667   1.774038   8.774148
        arttrunc |        640    2.420313    1.882269          1         19

### 4.4 Interpretation using predicted rates and probabilities

**`mtable`**, **`mgen`**, and **`mchange`** can compute conditional and unconditional rates and predicted probabilities. Unconditional rates are computed by default. Other types of predictions can be made using the **`predict()`** option. For example, to compute the average conditional rate, you could use the command **`mtable, predict(cm)`**. To compute unconditional probabilities for counts from m to n, we use the option **`predict(pr(m/n))`**. To compute conditional probabilities, we use **`predict(cpr(m/n))`**.

To illustrate this, we compare the conditional and unconditional rates of productivity for married (**married=1**), female (**female=1**) scientists without young children (kid5=0), who are average on other characteristics:

```
qui mtable, rowname(Unconditional) at(female=1 married=1 kid5=0) atmeans ci

mtable, rowname(Conditional) at(female=1 married=1 kid5=0) atmeans ci ///
    predict(cm) below brief
```

    Expression: Conditional mean of arttrunc > ll(0), predict(cm)
    
                    |       mu        ll        ul
     ---------------+-----------------------------
      Unconditional |    1.561     1.243     1.880
        Conditional |    2.308     2.062     2.553

An otherwise average married, female scientist without young children is predicted to publish 1.56 papers. However, if we know she has published at least one paper, this prediction increases to 2.31 articles. The conditional rate is higher than the unconditional rate, as it must be, because the conditional rate includes only those with at least one paper. To compute unconditional probabilities from 0 to 5 articles for a married female scientist without children, we type

```
mtable, at(female=1 married=1 kid5=0) atmeans pr(0/5) ci brief
```

    Expression: Pr(arttrunc), predict(pr())
    
               |        0         1         2         3         4         5
     ----------+-----------------------------------------------------------
         Pr(y) |    0.323     0.272     0.177     0.104     0.058     0.031
            ll |    0.240     0.253     0.150     0.081     0.042     0.020
            ul |    0.407     0.292     0.205     0.127     0.074     0.042

The probability of a zero count is 0.32 with a 95% confidence interval from 0.24 to 0.41, indicating that we expect that an otherwise average married female scientist with no children would have a 32% chance of having no publications. Other probabilities can be interpreted similarly.

We cannot compute the conditional probability of a zero count because we are conditioning on having at least one publication. Accordingly, we compare unconditional and conditional probabilities of counts greater than 1:

```
qui mtable, rowname(Unconditional) at(female=1 married=1 kid5=0) atmeans ///
    pr(1/5)
mtable, rowname(Conditional) at(female=1 married=1 kid5=0) atmeans ///
cpr(1/5) below brief
```

    Expression: Pr(arttrunc | arttrunc>0), cpr()
    
                    |        1         2         3         4         5
     ---------------+-------------------------------------------------
      Unconditional |    0.272     0.177     0.104     0.058     0.031
        Conditional |    0.403     0.262     0.154     0.086     0.046

As expected, the conditional probabilities are larger than the unconditional probabilities. This is because the unconditional probability of a 0 is 0.32, so the conditional predictions equal $\Pr\left(y=k\mid\mathbf{x},y>0\right)=\Pr\left(y=k\mid\mathbf{x}\right)/\{1-\Pr\left(y=0\mid\mathbf{x}\right)\}.$Accordingly, each unconditional probability is 32.3% lower than the corresponding conditional probability.

Although we do not illustrate them, other methods of interpretation that were used for the PRM and NBRM can also be used for count models with truncation.

## 5 (Advanced) The hurdle regression model

>This section is marked as advanced because it involves fitting a model that is not built-in to Stata. Obtaining estimates is, consequently, more complicated. The section is useful if you have an application of the hurdle model, and it also provides an opportunity to learn about working with matrices and using the **``suest``** command to combine results from different models.

这段话是在讨论如何在Stata统计软件中进行一些高级操作，特别是涉及到拟合一个非内置的模型，即障碍模型（hurdle model）。障碍模型是一种用于处理零膨胀数据的模型，通常用于计数数据或存在大量零值的数据集。由于这个模型不是Stata内置的，因此使用它来获取模型估计的过程比使用内置命令更为复杂。

这一部分对于那些需要在实际应用中使用障碍模型的用户来说是有帮助的。同时，它也提供了一个学习如何处理矩阵和如何使用`suest`命令的机会。`suest`是Stata中的一个命令，用于对多个模型进行联合估计，特别适用于当需要对不同子样本或不同模型估计的参数进行合并检验时。

总的来说，这段话强调了障碍模型在Stata中的拟合是一个高级操作，需要用户具备一定的统计知识和Stata操作能力，同时指出了这一过程对于学习和理解更复杂的统计技术和Stata命令是有益的。

The hurdle regression model (HRM) combines a binary model to predict Os and a ZTP or ZTNB to predict nonzero counts (Mullahy 1986; Cameron and Trivedi [2013, 136-139]). Let y be a count outcome that is not truncated at 0. Suppose that zero counts are generated by a binary process. Here we use a logit to model the binary outcome $y = 0$ versus $y > 0$, but other binary models could be used:

$$\Pr(y_i=0\mid\mathbf{x}_i)=\frac{\exp\left(\mathbf{x}_i\boldsymbol{\gamma}\right)}{1+\exp\left(\mathbf{x}_i\boldsymbol{\gamma}\right)}=\pi_i$$

In this two-equation model, 0 is viewed as a hurdle that you have to get past before reaching positive counts. Positive counts are generated either by the ZTP or the ZTNB models from section 9.4. Because there are separate equations to predict zero counts and positive counts, this allows the process that predicts zeros to be different from the process that predicts positive counts.

The name "hurdle" might suggest that the process of moving from 0 to 1 with the count outcome is more difficult than subsequent increases. This makes sense in many count processes, including the case of scientific publishing: we might imagine that getting one’s first publication is especially difficult, and publishing is easier after that. In such cases, the number of zero counts would be greater than we would predict using the PRM or NORM. Unlike the zero-inflated models considered in section 9.6, however, the hurdle model can also be applied to cases in which there are fewer Os than expected. In such cases, getting over the hurdle is easier to achieve than subsequent counts.

The predicted rates and probabilities for the HRM are computed by mixing the results of the binary model and the zero-truncated model. The probability of a 0 is

$$\Pr(y_i=0\mid\mathbf{x}_i)=\pi_i$$

as estimated by a binary regression model. Because positive counts can occur only if you get past the 0 hurdle, which occurs with probability $1 - \pi_i$, we rescale the conditional probability from the zero-truncated model to compute the unconditional probability.

$$\Pr\left(y_i\mid\mathbf{x}_i\right)=\left(1-\pi_i\right)\Pr\left(y_i\mid y_i>0,\mathbf{x}_i\right)\quad\mathrm{for~}y>0$$

The unconditional rate is computed by combining the mean rate for those with $y = 0$ (which, of course, is 0) and the mean rate for those with positive counts:


$$E(y_i|x_i)=(\pi_i\times0)+{(1-\pi_i)}\times E(y_i|y_i>0,x_i)$$

\begin{align*}
= (1-\pi_{i})\times E(y_{i}\mid y_{i}>0,\mathbf{x}_{i}) 
\end{align*}

where $E(y_i\mid y_i>0,\mathbf{x}_i)$ is defined by (9.8) for the ZTP and (9.9) for the ZTNB.

Although Stata does not include the hurdle model as a command, it can be fit by combining results from **``logit``** with those from either **``tnb reg``** or **``tpoisson``**. Using these estimation commands along with commands for working with predictions, we can compute predictions for the hurdle model that correspond to those for other count models. This process involves a few more steps than the other examples in this book, but these are straightforward and necessary for using this model. The process also provides an example of accomplishing postestimation analyses “by hand”.

A bigger problem is that the standard errors for the parameter estimates and predictions obtained using this method will be incorrect. We will show you how to obtain correct standard errors for the parameters by using Stata’s **``suest``** command. Unfortunately, these estimates cannot be used with **``predict, margins, mtable, mgen,``** or **``mchange``** to obtain predicted probabilities. Accordingly, we show how to make these predictions with estimates from **``logit``** and **``tnbreg``**. The predictions will be correct because they do not depend on the standard errors; but the standard errors will be incorrect, so you cannot construct confidence intervals around these predictions or do hypothesis testing.



* Hurdle模型的详细步骤

1. 步骤1：理解数据

假设我们有一组数据，记录了10位科学家的以下特征：

- 年龄（Age）
- 经验（Experience）
- 发表的论文数量（Publications）

我们的目标是预测每位科学家未来一年内可能发表的论文数量。

2. 步骤2：构建二元模型

我们首先使用逻辑回归模型来预测科学家是否发表任何论文（即零值和非零值的区分）。

逻辑回归模型的公式是：

$$
\Pr(y_i=0\mid\mathbf{x}_i) = \frac{\exp(\mathbf{x}_i\boldsymbol{\gamma})}{1+\exp(\mathbf{x}_i\boldsymbol{\gamma})} = \pi_i
$$

其中：

- $ y_i $ 是第 $ i $ 位科学家发表的论文数量。
- $ \mathbf{x}_i $ 是第 $i $ 位科学家的特征向量，例如 $ \mathbf{x}_i = [Age_i, Experience_i] $$。
- $ \boldsymbol{\gamma} $ 是回归系数向量，它告诉我们每个特征对发表概率的影响。
- $ \pi_i $ 是第 $ i $ 位科学家没有发表任何论文的概率。

3. 步骤3：构建零截断模型

对于已经发表至少一篇论文的科学家，我们使用零截断泊松（ZTP）或零截断负二项（ZTNB）模型来预测他们发表的具体论文数量。

假设我们使用ZTP模型，其期望函数通常为：

$$
E(y_i\mid y_i>0, \mathbf{x}_i) = \exp(\mathbf{x}_i\boldsymbol{\beta})
$$

其中：

- $ E(y_i\mid y_i>0, \mathbf{x}_i) $ 是在给定 $ y_i > 0 $ 和 $ \mathbf{x}_i $ 的条件下，第 $ i $ 位科学家发表的论文数量的期望。
- $ \boldsymbol{\beta} $ 是回归系数向量，与 $ \boldsymbol{\gamma} $ 不同，它专门用于预测非零计数。

4. 步骤4：计算无条件概率

结合二元模型和零截断模型的结果，我们可以计算无条件概率。对于非零计数，无条件概率的公式是：

$$
\Pr(y_i\mid\mathbf{x}_i) = (1-\pi_i) \cdot \Pr(y_i\mid y_i>0,\mathbf{x}_i) \quad \text{for } y > 0
$$

这意味着，如果科学家有 $ 1 - \pi_i $ 的概率发表了至少一篇论文，那么他发表的论文数量的概率就是这个概率乘以零截断模型给出的条件概率。

5. 步骤5：计算期望值

期望值 $ E(y_i\mid\mathbf{x}_i) $ 是在给定特征 $ \mathbf{x}_i $ 的条件下，科学家 $ i $ 发表的论文数量的期望。它由两部分组成：

- 当 $ y_i = 0 $ 时，期望值是0。
- 当 $ y_i > 0 $ 时，期望值是 $ (1-\pi_i) $ 乘以非零值的条件期望 $ E(y_i\mid y_i>0,\mathbf{x}_i) $。

期望值的计算公式是：

$$
E(y_i\mid\mathbf{x}_i) = (1-\pi_i) \times E(y_i\mid y_i>0,\mathbf{x}_i)
$$

* 实际例子

假设我们有以下数据：

- 科学家A：年龄30岁，经验5年，没有发表任何论文。
- 科学家B：年龄40岁，经验10年，发表了3篇论文。

* 二元模型预测零值

假设逻辑回归模型的系数为：

- $ \gamma_{Age} = -0.05 $
- $ \gamma_{Experience} = 0.1 $

对于科学家A：

$$
\pi_A = \frac{\exp(30 \times -0.05 + 5 \times 0.1)}{1+\exp(30 \times -0.05 + 5 \times 0.1)} = 0.7
$$

这意味着科学家A有70%的概率没有发表任何论文。

* 零截断模型预测非零值

假设ZTP模型的系数为：

- $ \beta_{Age} = 0.1 $
- $ \beta_{Experience} = 0.2 $

对于科学家B：

$$
E(y_B\mid y_B>0, \mathbf{x}_B) = \exp(40 \times 0.1 + 10 \times 0.2) = 2.94
$$

这意味着，给定科学家B的特征，他发表的论文数量的期望是2.94篇。

* 无条件概率和期望值

对于科学家B，我们计算无条件概率和期望值：

1. 首先，我们假设 $ \pi_B = 0.5 $（这个值是示例性的，实际中需要通过二元模型计算）。
2. 然后，我们计算无条件概率：

$$
\Pr(y_B\mid\mathbf{x}_B) = (1-0.5) \cdot 2.94 = 1.47
$$

这意味着，根据模型，科学家B发表的论文数量的概率是1.47篇。

3. 最后，我们计算期望值，由于 $ y_B = 3 $ 是一个已知的非零计数，我们不需要重新计算期望值。

* 结论

通过这个详细的例子，我们可以看到Hurdle模型是如何分两步来预测计数数据的。**首先，它使用一个二元模型来预测观测值为零的概率；然后，对于非零的观测值，它使用一个零截断模型来预测具体的计数。最后，将这两个模型的结果结合起来，得到每个观测值的无条件概率和期望值。**


### 5.1 Fitting the hurdle model

With binary models (see chapter 5), Stata defines the two outcome categories as 0 or any nonmissing value that is not 0. For the hurdle model, this is very convenient. If we use **``logit``** or **``probit``** for a count outcome, the resulting estimates are for a binary model of any positive count versus 0, which is precisely what we want for the first step in the hurdle model. Here we fit the logit model and store the estimates:

```
use couart4, clear
logit art i.female i.married kid5 phd mentor, nolog or
predict prlogit
est store Hlogit
```

Then, we fit a ZTNB by using **``if art > 0``** to restrict our sample to scientists with at least one article:

```
tnbreg art i.female i.married kid5 phd mentor if art>0, nolog irr
est store Hztnb
```

This correctly estimates the coefficients, but the standard errors are incorrect. Accordingly, in the following table, the estimated coefficients are correct but the p-values are not (we only show them for comparison with the correct standard errors, computed with **``suest``** below):

```
est table Hlogit Hztnb, b(%9.3f) eform t
```

    --------------------------------------
        Variable |  Hlogit       Hztnb    
    -------------+------------------------
    art          |
          female |
         Female  |     0.778       0.783  
                 |     -1.58       -2.52  
                 |
         married |
        Married  |     1.386       1.109  
                 |      1.80        0.95  
                 |
            kid5 |     0.752       0.858  
                 |     -2.57       -2.12  
             phd |     1.022       0.997  
                 |      0.28       -0.06  
          mentor |     1.083       1.024  
                 |      6.15        5.54  
           _cons |     1.267       1.426  
                 |      0.80        1.80  
    -------------+------------------------
        /lnalpha |                 0.547  
                 |                 -2.68  
    --------------------------------------
                               Legend: b/t

To obtain the correct standard errors, we use the **``suest``** command (see [r] su est), which takes into account that even though the two models were independently estimated, they are dependent on one another.

```
suest Hlogit Hztnb, vce(robust) eform(expB)
```

    Simultaneous results for Hlogit, Hztnb                     Number of obs = 915
    
    ------------------------------------------------------------------------------
                 |               Robust
                 |       expB   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
    Hlogit_art   |
          female |
         Female  |       0.78       0.12    -1.61   0.108         0.57        1.06
                 |
         married |
        Married  |       1.39       0.25     1.83   0.068         0.98        1.97
            kid5 |       0.75       0.08    -2.58   0.010         0.61        0.93
             phd |       1.02       0.08     0.28   0.782         0.87        1.20
          mentor |       1.08       0.02     5.61   0.000         1.05        1.11
           _cons |       1.27       0.37     0.81   0.417         0.72        2.24
    -------------+----------------------------------------------------------------
    Hztnb_art    |
          female |
         Female  |       0.78       0.07    -2.64   0.008         0.65        0.94
                 |
         married |
        Married  |       1.11       0.12     0.98   0.327         0.90        1.36
            kid5 |       0.86       0.06    -2.10   0.036         0.74        0.99
             phd |       1.00       0.05    -0.06   0.954         0.90        1.10
          mentor |       1.02       0.01     4.79   0.000         1.01        1.03
           _cons |       1.43       0.27     1.84   0.065         0.98        2.08
    -------------+----------------------------------------------------------------
    /Hztnb       |
         lnalpha |       0.55       0.13                          0.34        0.87
    ------------------------------------------------------------------------------

You can confirm that the parameter estimates are the same as those shown with **``estimates table``** earlier but that the $z-values$ differ.

With the hurdle model, a variable can be significant in one part of the model but not in the other part. For example, women are not significantly different from men "getting over the hurdle", but they have a significantly lower rate of publication once over the hurdle. For that matter, coefficients for the same variable can be in opposite directions for the two parts of the model, and there is no need for both parts to include the same independent variables.

The logit coefficients can be interpreted as discussed in chapter 6. If the coefficient for $X_k$ is positive, it means that a unit increase in $X_k$ increases the odds of publishing one or more papers by a factor of $exp(\hat{\beta}_k)$, holding other variables constant. For example, consider the effect that young children have on publishing:

>For an additional young child, the odds of publishing at least one article decrease by a factor of 0.75, holding other variables constant.

The ZTNB part of the model estimates how independent variables affect the rate of publication for those who have gotten "over the hurdle" of publishing. The coefficients from the ZTP or ZTNB do not have a direct substantive interpretation because we are not using the truncated model to overcome limitations of a truncated sample. If there were no 0s in our sample because data were only collected if the event occurred at least once, we could interpret these parameters. Here, however, we assume that the process generating zeros is different from the process generating nonzeros. Recall from our discussion of the zero-truncated model that the exponentiated coefficients estimate effects on the unconditional rate of the outcome. In this case, however, we use the logit equation to predict zero counts, so the exponentiated coefficients from the zero-truncated model no longer correspond directly to changes in the unconditional rate.

### 5.2 Predictions in the sample

Using **``predict``** with the estimates from our logit model, we can compute the predicted probability of observing a positive count for each observation:

```
estimates restore Hlogit
predict Hprobgt0
label var Hprobgt0 "Pr(y>0)"
```

If we subtract this probability from 1, we get the predicted probability of a zero count:

```
gen Hprob0 = 1 - Hprobgt0
label var Hprob0 "Pr(y=0)"
```

```
summarize Hprob0 Hprobgt0
```

        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
          Hprob0 |        915    .3005464    .1180336   .0015213   .5612055
        Hprobgt0 |        915    .6994536    .1180336   .4387945   .9984787

The average predicted probability of a 0 is 0.3005, which is exactly the same as the proportion of the 0s in the sample, as shown with **``tabulate art``** earlier. When **``logit``** is used for the binary portion of the model, the average probability of a 0 will always exactly match the observed proportion of 0s. Accordingly, in the hurdle model, the average predicted probability of a 0 equals the observed proportion of 0s.

To compute predictions of positive counts, we restore the results from the ZTNB and use **``predict``** with the **``cm``** option to generate conditional predictions:

```
estimates restore Hztnb
predict Hcrate, cm
label var Hcrate "Conditional rate"
```

The conditional rate is the expected number of publications for those who have made it over the hurdle of publishing at least one article. To compute the unconditional rate, we multiply the conditional rate by the probability of having published at least one article, which we estimated using the logit portion of the model.

```
gen Hrate = Hcrate * Hprobgt0
label var Hrate "Unconditional rate"
```

We use **``summarize``** to compare the average conditional and unconditional rates:

```
sum Hcrate Hrate
```

        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
          Hcrate |        915    2.356661      .52643    1.73671   8.774148
           Hrate |        915    1.697686    .7016245   .7705519     8.7608

We follow the same idea to compute unconditional probabilities for nonzero counts. Because we want to compute predictions for multiple counts, we use a **``foreach``** loop. Within the loop, we first compute the conditional predicted probabilities by using **``predict``**. Next, we multiply the conditional probabilities by the probability from our logit model of having published at least one article:

```
forvalues icount = 1/8 {
    predict Hcprob`icount', cpr(`icount')
    label variable Hcprob`icount' "Pr(y=`icount'|y>0)"
    gen Hprob`icount' = Hcprob`icount' * Hprobgt0
    label variable Hprob`icount' "Pr(y=`icount')"
}
```
The loop executes the code within braces eight times, successively substituting the values 1-8 for the macro **``icount``**. The **``predict``** command uses the **``pr()``** option to compute conditional probabilities. The unconditional probabilities are obtained by multiplying conditional probabilities by the probability of a positive count. We use **``summarize``** to obtain the average unconditional probabilities:

```
sum Hprob*
```

        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
        Hprobgt0 |        915    .6994536    .1180336   .4387945   .9984787
          Hprob0 |        915    .3005464    .1180336   .0015213   .5612055
          Hprob1 |        915    .2772253    .0264024   .0672922   .3398655
          Hprob2 |        915    .1783374    .0225025     .07815   .2233984
          Hprob3 |        915    .1053164    .0246465   .0489526   .1597355
    -------------+---------------------------------------------------------
          Hprob4 |        915    .0599405     .022171   .0196098   .1201168
          Hprob5 |        915    .0336258    .0178631   .0075859   .0961457
          Hprob6 |        915    .0188354    .0136543    .002865   .0806172
          Hprob7 |        915    .0106266    .0102113   .0010633   .0676752
          Hprob8 |        915    .0060773    .0075988   .0003894   .0604464

The mean predicted probabilities can be compared with the observed predictions, as illustrated for the PRM and NBRM.

### 5.3 Predictions at user-specified values

We can use **``mtable``** or **``margins``** to compute predictions at specific values of the independent variables. You should not compute confidence intervals for these predictions: they will be incorrect because they do not use the correct standard errors from **``suest``**.

For our example, we make predictions for the ideal type of a married, female scientist (**``female=l married=l``**) from an elite PhD program (**``phd=4.5``**) who studied with a mentor with average productivity. We begin by making predictions from the logit portion of the model:

```
est restore Hlogit
mtable, at(female=1 married=1 kid5=0 phd=4.5) atmeans
```

    Expression: Pr(art), predict()
    
       Pr(y)
    --------
       0.753
    
    Specified values of covariates
    
               |   female   married      kid5       phd    mentor
     ----------+-------------------------------------------------
       Current |        1         1         0       4.5      8.77

Recall, that $Pr(y)$ is the probability of a 1 in the logit model, which corresponds to having one or more publications. Accordingly, the predicted probability of publishing one or more papers for a scientist with these characteristics is 0.753.

Next, we need the probability of a positive count to compute unconditional predictions with (9.10) and (9.12). Although we could simply type 0.753 in our do-file, a better way is to save the result as a local macro. Not only does this prevent typing errors, but our code will still work correctly if we change the model or sample and the predicted probability is no longer 0.753. To do this, we use information in the matrix `r (\text{table})` that is returned by **``mtable``**. If you type **``matlist r(table)``**, you will see that the prediction we want is in row 1 and column 1 of the matrix. To store this value in the local macro **``prgtO``**, we use:

```
local prgt0 = el(r(table),1,1)
```

where the **``el()``** function extracts the element in row 1 and column 1 of matrix \(r (\text{table})\). After we compute conditional probabilities, we will use the macro when computing unconditional probabilities.

Next, we compute conditional probabilities from the truncated portion of the model:

```
est restore Hztnb
mtable, at(female=1 married=1 kid5=0 phd=4.5) atmeans noesample cpr(1/8)
```

    Expression: Pr(art | art>0), cpr()
    
           1         2         3         4         5         6         7         8
    ------------------------------------------------------------------------------
       0.413     0.264     0.152     0.083     0.044     0.022     0.011     0.006
    
    Specified values of covariates
    
               |   female   married      kid5       phd    mentor
     ----------+-------------------------------------------------
       Current |        1         1         0       4.5      8.77
    


The **``noesample``** option is essential; we explain it briefly here, with further discussion below. The predictions for **``mtable``** above use estimates from **``tnbreg``**, which was fit using only observations where **art** is nonzero. By default, **``atmeans``** would use this sample to compute the mean for **mentor**. We want to compute predictions with the mean for mentor based on the entire sample not just the estimation sample. The **``noesample``** option tells **``mtable``** (and other commands based on **``margins``**) to use the entire sample.

The conditional probabilities computed by **``mtable``** were stored in the matrix **``r(table)``**. To compute unconditional probabilities, we multiply these conditional probabilities by the probability of a positive count, which we saved in the local macro **``prgtO``**. This can be done simply with a **``matrix``** command:

```
matrix Huncond = `prgt0' * r(table)
matlist Huncond, format(%8.3f) title(Unconditional probabilties) names(col)
```

    Unconditional probabilties
    
           1         2         3         4         5         6         7         8 
    ------------------------------------------------------------------------------
       0.251     0.155     0.086     0.045     0.023     0.011     0.006     0.003 
    
These predictions are for the ideal type of a married, female scientist with no children, who studied with an average mentor at an elite graduate program. The predicted probabilities of publishing one article is 0.31, two articles is 0.20, and so on. We could extend these analyses to compare these predictions with those for scientists with other characteristics.

### 5.4 Warning regarding sample specification

The two parts of the hurdle model are fit using different samples. The full sample was used to fit the binary model, while the truncated sample was used to fit the zero-truncated model. When computing predictions at specified values of the independent variables—say, the mean—you want the mean for the full sample, not the smaller, truncated sample. Or, if we want average predictions, we want averages for the full sample. By default, **``margins``**, **``mtable``**, **``mgen``**, and **``mchange``** use the estimation sample. For example, after fitting the ZTNB, **``mtable``**,**``atmeans``** computes predictions by using the means for cases with nonzero outcomes. What we want, however, are the means for the sample used for the binary portion of the model. To avoid problems, we suggest the following steps:

1. Before fitting the binary portion of the model, use the **``drop``** command to drop cases with missing data or that you would otherwise drop by using an **``if``** or **``in``** condition. This was not necessary in our example above because we wanted to use all the cases to fit the model.
2. Fit the truncated model with an **``if``** condition to drop cases that are 0 on the outcome (for example, **``tnbreg ... if art > 0``**).
3. When using **``margins``**, **``mtable``**, or other margins-based commands, use the option **``noesample``**, which specifies that all cases in memory be used to compute averages and other statistics, rather than using only the estimation sample.


## 6 Zero-inflated count models

The NBRM improves upon the underprediction of Os in the PRM by increasing the conditional variance without changing the conditional mean. The hurdle model addresses the underprediction of Os by using two equations: a binary model to predict Os and a zero-truncated model for the remaining counts. We can think of this as allowing the process that generates the first count to be distinct from the process that generates subsequent counts. Zero-inflated count models, introduced by Mullahy (1986) and Lambert (1992), change the mean structure to allow Os to be generated by two distinct processes.

Consider our example of scientific productivity. The PRM, NBRM, and HRM assume that every scientist has a nonzero rate of publishing. This implies a chance that any given scientist would have no publications, but it also implies a positive probability for all positive counts. The rates and predicted probabilities differ across individuals according to their characteristics, but all scientists could publish even if that probability is quite small. Substantively, this would be unrealistic if some scientists have no opportunity for publishing. For example, scientists might be employed in an industry where publishing is not allowed, or they might hold jobs that do not involve research. As a result, we would observe more scientists with no publications because zero counts reflect a combination of scientists with no probability of publishing and scientists for whom no publications is the result of a probabilistic process.

Zero-inflated models allow for this possibility. A zero-inflated model assumes that there are two latent (that is, unobserved) groups. An individual in the "always 0 group" (group ~A) has outcome 0 with a probability of 1, whereas an individual in the "not always 0" group (group ~A) might have outcome 0, but there is a nonzero probability that the individual has a positive count. For someone with no publications, we cannot determine whether he or she is in group A or group ~A, but we can model the individual’s probability of being in one of the groups based on observed characteristics. This process is developed in three steps.

* Step 1. Model membership into the latent groups A and ~A.

* Step 2. Model counts for those in group ~A who can publish

* Step 3. Compute probabilities for each count as a mixture of the probabilities from the
two groups.

抱歉刚才的回复中出现了误解。现在我将按照您的要求，将文中的数学公式用美元符号 $包裹起来，以符合Markdown的公式渲染格式。
* 零膨胀模型的详细解释
1. 第1步：模型潜在群体的成员资格
在这一步中，我们使用一个二元逻辑回归模型来预测个体是否属于“总是零”的群体（群体A）。逻辑回归模型的形式通常如下：
$$
 \log\left(\frac{p}{1-p}\right) = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \ldots + \beta_n X_n 
$$
这里：
- $ p $ 是个体属于群体A的概率。
- $ \log\left(\frac{p}{1-p}\right) $ 是对数几率（logit），它是将概率转换为一个可以被线性模型预测的值。
- $ \beta_0, \beta_1, \ldots, \beta_n $ 是模型参数，它们表示不同特征对个体属于群体A概率的影响。
- $ X_1, X_2, \ldots, X_n $ 是个体的特征，比如年龄、性别、教育水平等。
2. 第2步：模型计数
对于群体~A中的个体，我们使用一个计数模型来预测他们的出版物数量。泊松回归模型是常用的计数模型之一，其形式如下：
$$
 \log(\lambda) = \alpha_0 + \alpha_1 Y_1 + \alpha_2 Y_2 + \ldots + \alpha_m Y_m 
$$
这里：
- $ \lambda $ 是个体预期的出版物数量（即泊松分布的参数）。
- $ \alpha_0, \alpha_1, \ldots, \alpha_m $ 是模型参数。
- $ Y_1, Y_2, \ldots, Y_m $ 是影响个体出版物数量的特征，比如研究资金、工作时间等。
3. 第3步：计算每个计数的概率
在这一步中，我们将前两步的结果结合起来，计算每个可能计数的概率。对于任何给定的计数 $ k $，最终的概率是：
$$
 P(X = k) = (1 - P(\text{在群体A})) \cdot P(X = k | \text{在群体~A}) + P(\text{在群体A}) \cdot P(X = k | \text{在群体A}) 
$$
由于群体A中的个体永远没有出版物，所以 $ P(X = k | \text{在群体A}) $ 只有在 $ k = 0 $ 时才不为零。因此，上述公式可以简化为：
$$
 P(X = k) = 
  \begin{cases} 
   P(\text{在群体A}) & \text{if } k = 0 \\
   (1 - P(\text{在群体A})) \cdot P(X = k | \text{在群体~A}) & \text{if } k > 0 
  \end{cases} 
$$
这意味着：
- 当 $ k = 0 $ 时，概率仅仅是个体属于群体A的概率，因为只有群体A中的个体才总是有零出版物。
- 当 $ k > 0 $ 时，概率是个体属于群体~A的概率乘以在群体~A中得到 $ k $ 个出版物的概率。
* 例子
假设我们有两个科学家，Alice和Bob，他们的特征如下：
- Alice：工业界工作，没有研究资金。
- Bob：大学教授，有研究资金。
1. 第1步：预测Alice和Bob属于群体A的概率
假设逻辑回归模型的参数估计结果如下：
- Alice：$ P(\text{在群体A}) = 0.9 $（很高的概率属于群体A）
- Bob：$ P(\text{在群体A}) = 0.1 $（较低的概率属于群体A）
2. 第2步：预测Bob的出版物数量
假设泊松回归模型预测Bob有1篇、2篇、3篇出版物的概率分别为：
- $ P(X = 1 | \text{在群体~A}) = 0.2 $
- $ P(X = 2 | \text{在群体~A}) = 0.3 $
- $ P(X = 3 | \text{在群体~A}) = 0.25 $
3. 第3步：计算Alice和Bob的最终出版物数量概率
对于Alice：
- $ P(X = 0) = P(\text{在群体A}) = 0.9 $
- $ P(X = 1) = (1 - P(\text{在群体A})) \cdot P(X = 1 | \text{在群体~A}) = 0.1 \cdot 0.2 = 0.02 $
- 同理，我们可以计算出Alice有2篇和3篇出版物的

We consider each step in turn, followed by an example.

* **Step 1: Membership in group A**

Let $A_i = 1$ if someone is in group A, or let $A_i = 0$ otherwise. Group membership is a binary outcome that can be modeled using the logit or probit model from chapters 5 and 6:

$$\Pr\left(A_i=1\mid\mathbf{z}_i\right)=F\left(\mathbf{z}_i\boldsymbol{\gamma}\right)=\psi_i$$

This is a binary regression model, where the outcome $A_i$ is unobserved and $\psi_i$ is the probability of being in group A for individual $i$. The two variables are referred to as inflation variables because they inflate (that is, increase) the number of Os, as shown below. To illustrate (9.13), assume that two variables affect the probability that an individual is in group A and that we model this with the logit equation

$$\Pr\left(A_{i}=1\mid z_{i1},z_{i2}\right)=\psi_{i}=\frac{\exp\left(\gamma_{0}+\gamma_{1}z_{i1}+\gamma_{2}z_{i2}\right)}{1+\exp\left(\gamma_{0}+\gamma_{1}z_{i1}+\gamma_{2}z_{i2}\right)}$$

If we had an observed variable indicating group membership, this would be a standard, binary logit model. But because group membership is a latent variable, we do not know whether an individual is in group A or group ~A.



* **潜在群体成员资格的二元模型**
在零膨胀模型中，我们首先要确定个体是否属于特定的潜在群体。这里，我们关注的是群体A，即那些可能永远不会产生计数事件（例如，永远不会发表文章）的个体。
1. **模型设定**

我们用 $ A_i $ 来表示个体 $ i $ 是否属于群体A的二元（0或1）结果：
- 如果 $ A_i = 1 $，表示个体属于群体A。
- 如果 $ A_i = 0 $，表示个体不属于群体A。
我们使用条件概率 $ \Pr(A_i=1 \mid \mathbf{z}_i) $ 来表示给定向量 $ \mathbf{z}_i $ 的条件下，个体 $ i $ 属于群体A的概率。这里，$ \mathbf{z}_i $ 包含了影响个体是否属于群体A的变量，而 $ \gamma $ 是模型参数向量。
2. **逻辑回归模型**

我们使用逻辑回归模型来估计这个概率，模型的形式如下：
$$
 \Pr(A_i=1 \mid \mathbf{z}_i) = F(\mathbf{z}_i\boldsymbol{\gamma}) = \psi_i 
$$
其中：
- $ F(\cdot) $ 是逻辑函数（logit function），它将线性组合 $ \mathbf{z}_i\boldsymbol{\gamma} $ 映射到概率值上。
- $ \psi_i $ 是个体 $ i $ 属于群体A的概率。
3. **逻辑函数的具体形式**

逻辑函数（logit function）的具体形式是：
$$
 \log\left(\frac{\psi_i}{1-\psi_i}\right) = \mathbf{z}_i\boldsymbol{\gamma} 
$$
利用指数函数 $ \exp(\cdot) $ 的性质，我们可以将其转换为更常见的形式：
$$
 \psi_i = \frac{\exp(\mathbf{z}_i\boldsymbol{\gamma})}{1 + \exp(\mathbf{z}_i\boldsymbol{\gamma})} 
$$

* **模型参数的解释**

现在，让我们通过一个具体的例子来解释模型参数：
1. 例子

假设有两个变量 $ z_{i1} $ 和 $ z_{i2} $ 影响个体属于群体A的概率，我们可以使用逻辑回归模型来表示：
$$
 \Pr(A_{i}=1 \mid z_{i1}, z_{i2}) = \psi_{i} = \frac{\exp(\gamma_{0} + \gamma_{1}z_{i1} + \gamma_{2}z_{i2})}{1 + \exp(\gamma_{0} + \gamma_{1}z_{i1} + \gamma_{2}z_{i2})} 
$$
在这个模型中：
- $ \gamma_{0} $ 是截距项，表示当所有 $ z $ 变量为0时，个体属于群体A的基础概率。
- $ \gamma_{1} $ 和 $ \gamma_{2} $ 是系数，它们分别表示 $ z_{i1} $ 和 $ z_{i2} $ 对个体属于群体A概率的影响。
- $ z_{i1} $ 和 $ z_{i2} $ 是个体的特征，比如教育水平、工作经验等。
* **潜在变量的挑战**

由于群体成员资格 $ A_i $ 是一个潜在变量，我们无法直接观察到。这意味着，尽管我们可以估计个体属于群体A的概率 $ \psi_i $，但我们无法确定每个个体实际上属于哪个群体。
* **总结**

通过逻辑回归模型，我们可以估计每个个体属于潜在群体A的概率，尽管我们无法直接观察到群体成员资格。这个概率是通过考虑个体特征 $ z_{i1} $, $ z_{i2} $ ... $ z_{in} $ 以及相应的模型参数 $ \gamma_0 $, $ \gamma_1 $, ..., $ \gamma_n $ 来计算的。

* **Step 2: Counts for those in group ~A**

Among those who are not always 0, the probability of each count, including 0, is determined by either a PRM or an NBRM. In the equations that follow, we are conditioning both on the observed x's and on A being equal to 0. Although the x's can be the same as the 2's in the first step, they could be different. Defining mu_i=exp(x,beta), for the zero-inflated Poisson (ZIP) model, we have

$$\Pr(y_i\mid\mathbf{x}_i,A_i=0)=\frac{e^{-\mu_i}\mu_i^{y_i}}{y_i!}$$

and for the zero-inflated negative binomial (ZINB) model, we have

$$\Pr\left(y_i\mid\mathbf{x}_i,A_i=0\right)=\frac{\Gamma(y_i+\alpha^{-1})}{y_i!\Gamma(\alpha^{-1})}\left(\frac{\alpha^{-1}}{\alpha^{-1}+\mu_i}\right)^{\alpha^{-1}}\left(\frac{\mu_i}{\alpha^{-1}+\mu_i}\right)^{y_i}$$

If we knew which observations were in group ~A, these equations would define the PRM and the NBRM. Here the equations apply only to those observations from group ~A, but we do not have an observed variable indicating group membership.


1. **零膨胀模型的计数部分**

在零膨胀模型中，我们已经确定了哪些个体属于“不总是零”的群体（群体~A）。对于这些个体，我们需要一个计数模型来预测他们的计数结果，包括零计数。这里有两种模型可以选择：
- **零膨胀泊松（Zero-Inflated Poisson, ZIP）模型**
- **零膨胀负二项（Zero-Inflated Negative Binomial, ZINB）模型**
2. **零膨胀泊松（ZIP）模型**

对于ZIP模型，计数结果的概率质量函数（probability mass function）是标准的泊松分布，但增加了一个额外的零膨胀概率。对于个体 \( i \)，计数结果 \( y_i \) 的概率由以下公式给出：
$$
 \Pr(y_i \mid \mathbf{x}_i, A_i=0) = \frac{e^{-\mu_i} \mu_i^{y_i}}{y_i!} 
$$
其中：
- $ y_i $ 是个体 \( i \) 的计数结果。
- $ \mathbf{x}_i $ 是影响计数结果的特征向量。
- $ A_i=0 $ 表示个体属于群体~A。
- $ \mu_i $ 是泊松分布的参数，定义为 $ \mu_i = \exp(\mathbf{x}_i \boldsymbol{\beta}) $，其中 $ \boldsymbol{\beta} $ 是模型参数向量。
- $ e^{-\mu_i} $ 是泊松分布中每一项的概率的基数部分。
- $ \mu_i^{y_i} $ 是泊松分布中每一项的概率的计数部分。
- $ y_i! $ 是 \( y_i \) 的阶乘。
3. **零膨胀负二项（ZINB）模型**

ZINB模型是负二项分布的一个变体，它允许过度离散（overdispersion），即计数结果的方差大于均值。对于个体 \( i \)，计数结果 \( y_i \) 的概率由以下公式给出：
$$
 \Pr(y_i \mid \mathbf{x}_i, A_i=0) = \frac{\Gamma(y_i + \alpha^{-1})}{y_i! \Gamma(\alpha^{-1})} \left( \frac{\alpha^{-1}}{\alpha^{-1} + \mu_i} \right)^{\alpha^{-1}} \left( \frac{\mu_i}{\alpha^{-1} + \mu_i} \right)^{y_i} 
$$
其中：
- $ \Gamma(\cdot) $ 是伽玛函数（Gamma function），它是阶乘在实数和复数上的推广。
- $ \alpha^{-1} $ 是负二项分布的分散参数，控制计数结果的离散程度。
- 其余符号与ZIP模型中的相同。
4. **模型应用的挑战**

如果我们能够观察到个体的群体成员资格，那么上述方程将定义标准的泊松回归模型（PRM）和负二项回归模型（NBRM）。然而，在零膨胀模型中，我们只能将这些方程应用于群体~A的观测值，而我们并没有一个观察变量来指示群体成员资格。
5. **总结**

在零膨胀模型的计数部分，我们使用ZIP或ZINB模型来预测那些属于群体~A的个体的计数结果。这些模型考虑了计数数据中的过度离散，并允许我们更准确地预测零计数和非零计数。


* **Step 3: Mixing groups A and ~A**

Predicted probabilities of observed counts are computed by mixing the probabilities from the binary and count models. The simplest way to understand mixing is with an example. Suppose that retirement status is indicated by $r = 1$ for retired folks and $r = 0$ for those not retired, where

$$\Pr\left(r=1\right)=0.2$$
$$\Pr\left(r=0\right)=1-\Pr\left(r=1\right)=0.8$$

Let y indicate living in a warm climate, with $y = 1$ for yes and $y = 0$ for no. Suppose that the conditional probabilities are

$$\Pr\left(y=1\mid r=1\right)=0.5$$
$$\Pr\left(y=1\mid r=0\right)=0.3$$

so people are more likely to live in a warm climate if they are retired. What is the probability of living in a warm climate for the population as a whole? The answer is a mixture of the probabilities for the two groups weighted by the proportion in each group:

\begin{align*}
\Pr\left(y=1\right)=\left\{\Pr\left(r=1\right)\times\Pr\left(y=1\mid r=1\right)\right\} 
\end{align*}

\begin{align*}
+\left\{\Pr\left(r=0\right)\times\Pr\left(y=1\mid r=0\right)\right\} \\
\end{align*}

\begin{align*}
=(0.2\times0.5)+(0.8\times0.3)=0.34
\end{align*}

In other words, the two groups are mixed according to their proportions in the population to determine the overall rate. The same thing is done for the zero-inflated models that mix predictions from groups A and ~A

The proportion in each group equals

\begin{align*}
\Pr\left(A_{i}=1\right)=\psi_{i}
\end{align*}

\begin{align*}
\Pr\left(A_i=0\right)=1-\psi_i
\end{align*}

from the binary portion of the model. The probabilities of a 0 within each group are

$$\Pr\left(y_i=0\mid A_i=1,\mathbf{x}_i,\mathbf{z}_i\right)=1\text{ by definition of the }A\text{ group}$$
$$\Pr\left(y_i=0\mid A_i=0,\mathbf{x}_i,\mathbf{z}_i\right)=\text{prediction from PRM or NBRM portion of model}$$

The overall probability of a 0 mixes the two types of 0s:

$$
 \Pr(y_i=0 \mid \mathbf{x}_i, \mathbf{z}_i) = \psi_i \times 1 + (1 - \psi_i) \times \Pr(y_i=0 \mid A_i=0, \mathbf{x}_i, \mathbf{z}_i) 
$$
   
简化后为：

$$
 \Pr(y_i=0 \mid \mathbf{x}_i, \mathbf{z}_i) = \psi_i + (1 - \psi_i) \times \Pr(y_i=0 \mid A_i=0, \mathbf{x}_i, \mathbf{z}_i) 
$$

For count outcomes other than 0,

$$\Pr\left(y_i=k\mid\mathbf{x}_i,\mathbf{z}_i\right)=(\psi_i\times0)+\{(1-\psi_i)\times\Pr\left(y_i=k\mid\mathbf{x}_i,A_i=0\right)\}$$

$$=(1-\psi_i)\times\Pr(y_i=k\mid\mathbf{x}_i,A_i=0)$$

where we use the assumption that the probability of a positive count in group A is by definition 0.

Expected counts are computed similarly:

$$E(y_i\mid\mathbf{x}_i,\mathbf{z}_i)=(0\times\psi_i)+\{\mu_i\times(1-\psi_i)\}$$

$$
\quad \quad = \mu_i \times (1 - \psi_i)
$$


Because $0\leq\psi\leq1$, the expected value must be smaller than $/mu$, which shows that the mean structure in zero-inflated models differs from that in the PRM or NBRM.

The ZIP and ZINB differ in their assumption about the distribution of the count outcome for members of group ~A. The ZIP assumes a conditional Poisson distribution with a mean indicated by the count equation, while the ZINB assumes a conditional negative binomial distribution that also includes a dispersion parameter that is fit along with the other parameters of the model.

* **零膨胀模型的概率混合解释**
零膨胀模型是一种用于处理计数数据中零结果过多现象的统计模型。这种模型特别适用于某些个体或对象根本没有产生计数事件的情况。在零膨胀模型中，我们通常有两个潜在的群体：
**群体A**：这个群体的个体总是有零计数。

**群体~A**：这个群体的个体可能有非零计数。

我们使用一个二元模型来预测个体属于哪个群体，然后使用一个计数模型来预测群体~A中个体的计数结果。

1. **二元模型预测群体成员资格**

二元模型的输出是一个概率 
$ \psi_i $，表示个体 

$ i $ 属于群体A的概率。

相应的，个体属于群体~A的概率是 $ 1 - \psi_i $。
* **公式分解：**
$$
 \Pr(A_i=1) = \psi_i 
$$
$$
 \Pr(A_i=0) = 1 - \psi_i 
$$
这里：
$ A_i=1 $ 表示个体属于群体A。

$ A_i=0 $ 表示个体属于群体~A。

$ \psi_i $ 是通过二元模型计算得到的，表示属于群体A的概率。

* **理解混合概率**

我们从一个简单的例子开始，即退休状态和居住在温暖气候的概率。
* 1.1 退休状态的概率
假设退休状态由 $ r $ 表示：
$ r = 1 $ 表示退休。

$ r = 0 $ 表示未退休。
根据模型，退休的概率是 $ \Pr(r=1) = 0.2 $，那么未退休的概率就是 $ \Pr(r=0) = 1 - \Pr(r=1) = 0.8 $。

* 1.2 居住气候的概率
再假设 $ y $ 表示是否居住在温暖气候：
$ y = 1 $ 表示居住在温暖气候。

$ y = 0 $ 表示不居住在温暖气候。

给定退休状态 $ r $，居住气候的概率是条件概率：

$ \Pr(y=1 \mid r=1) = 0.5 $ 表示如果退休了，居住在温暖气候的概率是0.5。

$ \Pr(y=1 \mid r=0) = 0.3 $ 表示如果没退休，居住在温暖气候的概率是0.3。

* 1.3 整体居住气候的概率
要计算整个人口居住在温暖气候的概率，我们混合两个群体的概率，权重是每个群体在总体中的比例：
$$
 \Pr(y=1) = \left(\Pr(r=1) \times \Pr(y=1 \mid r=1)\right) + \left(\Pr(r=0) \times \Pr(y=1 \mid r=0)\right) 
$$
代入已知数值：
$$
 \Pr(y=1) = (0.2 \times 0.5) + (0.8 \times 0.3) = 0.1 + 0.24 = 0.34 
$$
这意味着整个人口居住在温暖气候的概率是0.34。

2. **计数模型预测非零计数**
我们使用一个二元模型来预测个体属于哪个群体，然后使用一个计数模型来预测群体~A中个体的计数结果。
二元模型预测群体成员资格
二元模型的输出是一个概率 $ \psi_i $，表示个体 $ i $ 属于群体A的概率。相应的，个体属于群体~A的概率是 $ 1 - \psi_i $。
* **公式分解：**
$$
 \Pr(A_i=1) = \psi_i 
$$
$$
 \Pr(A_i=0) = 1 - \psi_i 
$$
这里：

$ A_i=1 $ 表示个体属于群体A。

$ A_i=0 $ 表示个体属于群体~A。

$ \psi_i $ 是通过二元模型计算得到的，表示属于群体A的概率。


3. **计算整体计数结果的概率**
我们通过混合两个群体的概率，按它们在总体中的比例，来计算整体计数结果的概率。
* **公式分解：**

$$
 \Pr(y_i=0 \mid \mathbf{x}_i, \mathbf{z}_i) = \psi_i + (1 - \psi_i) \times \Pr(y_i=0 \mid A_i=0, \mathbf{x}_i, \mathbf{z}_i) 
$$
$$
 \Pr(y_i=k \mid \mathbf{x}_i, \mathbf{z}_i) = (1 - \psi_i) \times \Pr(y_i=k \mid A_i=0, \mathbf{x}_i, \mathbf{z}_i) \quad \text{for } k > 0 
$$
这里：

第一个公式计算的是零结果的整体概率。

第二个公式计算的是非零结果 $ k $ 的整体概率。


* **零值概率的计算**

整体零值概率的计算

整体零值概率的计算是零膨胀模型中的一个重要部分，因为它考虑了所有观测值（无论是群体A还是群体非A）产生零计数的概率。以下是整体零值概率计算的详细解释和公式：
1. **群体A的零值概率**：

根据零膨胀模型的定义，群体A中的观测值只产生零计数，因此，对于群体A中的任何观测值，其零值概率是1。
$$
 \Pr(y_i=0 \mid A_i=1, \mathbf{x}_i, \mathbf{z}_i) = 1 
$$
2. **群体非A的零值概率**：

群体非A中的观测值可能产生零计数或其他正整数计数。零值概率是由计数部分的模型（例如泊松回归模型PRM或负二项回归模型NBRM）来预测的。
$$
 \Pr(y_i=0 \mid A_i=0, \mathbf{x}_i, \mathbf{z}_i) = \text{prediction from PRM or NBRM} 
$$
3. **整体零值概率**：

整体零值概率是将群体A和群体非A的零值概率按它们在总人口中的比例混合得出的。这意味着我们需要将群体A的零值概率（1）乘以观测值属于群体A的概率 $ \psi_i $，以及将群体非A的零值概率乘以观测值属于群体非A的概率 $ 1 - \psi_i $。

整体零值概率的公式为：
$$
 \Pr(y_i=0 \mid \mathbf{x}_i, \mathbf{z}_i) = \psi_i \times 1 + (1 - \psi_i) \times \Pr(y_i=0 \mid A_i=0, \mathbf{x}_i, \mathbf{z}_i) 
$$
   简化后为：
$$
 \Pr(y_i=0 \mid \mathbf{x}_i, \mathbf{z}_i) = \psi_i + (1 - \psi_i) \times \Pr(y_i=0 \mid A_i=0, \mathbf{x}_i, \mathbf{z}_i) 
$$
* 例子
为了更好地理解，让我们通过一个具体的例子来说明整体零值概率的计算。
假设我们有以下信息：
- $ \psi_i = 0.7 $：表示观测值属于群体A的概率为70%。
- $ \Pr(y_i=0 \mid A_i=0, \mathbf{x}_i, \mathbf{z}_i) = 0.1 $：表示给定观测值属于群体非A，其计数结果为0的概率为10%。
根据上述信息，我们可以计算整体零值概率：
$$
 \Pr(y_i=0 \mid \mathbf{x}_i, \mathbf{z}_i) = 0.7 \times 1 + (1 - 0.7) \times 0.1 
$$
$$
 \Pr(y_i=0 \mid \mathbf{x}_i, \mathbf{z}_i) = 0.7 + 0.3 \times 0.1 
$$
$$
 \Pr(y_i=0 \mid \mathbf{x}_i, \mathbf{z}_i) = 0.7 + 0.03 
$$
$$
 \Pr(y_i=0 \mid \mathbf{x}_i, \mathbf{z}_i) = 0.73 
$$
这意味着，根据模型，观测值的零值概率为73%。

整体零值概率的计算结合了群体A的确定性（只产生零计数）和群体非A的概率性（可能产生零计数或其他正整数计数）。这种方法允许模型捕捉数据中过量零值的现象，并提供一个综合的概率估计，这对于分析和预测计数数据特别有用。


* **非零计数概率的计算**

对于计数结果 $ y_i $ 不等于零的情况，我们只考虑群体非A中的观测值，因为群体A中的观测值不可能产生正计数。
2. **群体非A中的非零计数概率**：

计数结果 $ y_i = k $ 的概率（对于 $ k > 0 $）是由计数部分的模型给出的，不考虑群体A。
$$
 \Pr(y_i=k \mid A_i=0, \mathbf{x}_i, \mathbf{z}_i) = \text{probability from PRM or NBRM for } k > 0 
$$
3. **整体非零计数概率**：

在零膨胀模型（Zero-Inflated Models，ZIM）中，非零计数的概率只与群体非A相关，因为群体A的观测值仅产生零计数。

以下两个公式描述的是零膨胀模型中非零计数 $ y_i = k $（$ k > 0 $）的条件概率。我们来逐一解释它们。

对于计数结果 $ y_i = k $（其中 $ k > 0 $），计算非零计数概率的公式如下：

* 第一个公式
$$\Pr\left(y_i=k\mid\mathbf{x}_i,\mathbf{z}_i\right)=(\psi_i\times0)+\{(1-\psi_i)\times\Pr\left(y_i=k\mid\mathbf{x}_i,A_i=0\right)\}$$

这个公式是用来计算在给定自变量 $ \mathbf{x}_i $ 和其他变量 $ \mathbf{z}_i $ 的条件下，观测值 $ y_i $ 等于某个非零正整数 $ k $ 的概率。这个概率由两部分组成：
1. $ \psi_i \times 0 $：这部分表示如果观测值属于群体A（即 $ A_i = 1 $），那么它产生非零计数 $ k $ 的概率是0，因为群体A只产生零计数。
2. $(1-\psi_i)\times\Pr\left(y_i=k\mid\mathbf{x}_i,A_i=0\right)$：这部分表示如果观测值属于群体非A（即 $ A_i = 0 $），那么它产生非零计数 $ k $ 的概率是由计数模型（例如泊松回归模型PRM或负二项回归模型NBRM）给出的，乘以观测值属于群体非A的概率 $ 1 - \psi_i $。
* 第二个公式
$$=(1-\psi_i)\times\Pr(y_i=k\mid\mathbf{x}_i,A_i=0)$$
这个公式是第一个公式的简化形式。由于 $ \psi_i \times 0 = 0 $，第一项对概率没有贡献，因此可以忽略。这样，我们只需要考虑观测值属于群体非A的情况，即：
$ 1 - \psi_i $：观测值属于群体非A的概率。

$\Pr(y_i=k\mid\mathbf{x}_i,A_i=0)$
在给定观测值属于群体非A的情况下，计数结果为 $k$ 的条件概率。

* 解释
在零膨胀模型中，我们假设数据生成过程由两个部分组成：一个二元过程决定观测值是否属于群体A（零计数群体），和一个计数过程决定不属于群体A的观测值的计数。由于群体A中的观测值总是产生零计数，我们只对群体非A中的非零计数感兴趣。
因此，非零计数 $ y_i = k $ 的整体概率完全由群体非A中的计数模型决定，并且乘以观测值属于群体非A的概率。这就是为什么公式中的第一部分消失，我们只剩下与群体非A相关的部分。
* 例子
假设我们有以下信息：

$ \psi_i = 0.6 $：观测值属于群体A的概率为60%。

$\Pr(y_i=k\mid\mathbf{x}_i,A_i=0)=0.05$：在给定观测值属于群体非A的情况下，计数结果为 $ k $ 的条件概率为5%。
根据公式：

$\Pr(y_i=k\mid\mathbf{x}_i,\mathbf{z}_i)=(1-0.6)\times0.05=0.05\times0.4=0.02$
这意味着，在考虑了群体A的影响后，观测值 $ y_i $ 等于 $ k $ 的整体概率是2%。


4. **计算期望计数**

预期计数 $ E(y_i \mid \mathbf{x}_i, \mathbf{z}_i) $ 是通过考虑群体A和群体非A的预期计数，按它们在总人口中的比例混合得出的。即考虑了两个群体的概率后，预测的个体 $ i $ 的平均计数结果。


* **公式分解：**
$$
 E(y_i \mid \mathbf{x}_i, \mathbf{z}_i) = \mu_i \times (1 - \psi_i) 
$$
这里：
- $ E(y_i \mid \mathbf{x}_i, \mathbf{z}_i) $ 是给定特征 $ \mathbf{x}_i $ 和 $ \mathbf{z}_i $ 的条件下，个体 $ i $ 的期望计数结果。
- $ \mu_i $ 是计数模型预测的期望计数，不考虑零膨胀部分。


* **零膨胀模型的预期计数计算**
这个公式描述的是零膨胀模型（Zero-Inflated Models，ZIM）中预期计数 $ E(y_i \mid \mathbf{x}_i, \mathbf{z}_i) $ 的计算方法。预期计数是指在给定自变量 $ \mathbf{x}_i $ 和其他变量 $ \mathbf{z}_i $ 的条件下，观测值 $ y_i $ 的期望值。这里的 $ \psi_i $ 表示观测值属于产生过量零值的群体A的概率，而 $ \mu_i $ 是群体非A的预期计数。
让我们逐步分解这个公式：
1. **群体A的预期计数**：由于群体A中的观测值只产生零计数，所以其预期计数为 $ 0 \times \psi_i $。这是因为任何数乘以0都等于0。
2. **群体非A的预期计数**：群体非A的预期计数由计数模型（如泊松回归模型PRM或负二项回归模型NBRM）给出，表示为 $ \mu_i $。
3. **整体预期计数**：整体预期计数是将群体A的预期计数和群体非A的预期计数按比例混合得出的。由于群体A只产生零计数，我们只考虑群体非A的预期计数，并乘以观测值属于群体非A的概率 $ 1 - \psi_i $。
整体预期计数的公式为：


$$E(y_i\mid\mathbf{x}_i,\mathbf{z}_i)=(0\times\psi_i)+\{\mu_i\times(1-\psi_i)\}$$

简化后得到：
$$
E(y_i \mid \mathbf{x}_i, \mathbf{z}_i) = \mu_i \times (1 - \psi_i)
$$
这个公式表明，整体预期计数 $ E(y_i \mid \mathbf{x}_i, \mathbf{z}_i) $ 是群体非A的预期计数 $ \mu_i $ 乘以观测值不属于群体A的概率 $ 1 - \psi_i $。
* **进一步解释**
由于 $ \psi_i $ 的取值范围是 $ 0 \leq \psi_i \leq 1 $，这意味着 $ 0 \leq (1 - \psi_i) < 1 $。因此，$ \mu_i \times (1 - \psi_i) $ 必然小于 $ \mu_i $，因为乘以一个小于1的数会减小 $ \mu_i $ 的值。
这表明，在零膨胀模型中，由于存在产生过量零值的群体A，整体预期计数 $ E(y_i \mid \mathbf{x}_i, \mathbf{z}_i) $ 会小于没有零膨胀时的预期计数 $ \mu_i $。这也是零膨胀模型与普通计数模型（如PRM或NBRM）的一个主要区别，即零膨胀模型考虑了数据中过量零值的影响，从而提供了不同的均值结构。
* **例子**
假设我们有以下信息：
- $ \psi_i = 0.4 $：观测值属于群体A的概率为40%。
- $ \mu_i = 5 $：群体非A的预期计数为5。
根据上述公式，我们可以计算整体预期计数：
$$
E(y_i \mid \mathbf{x}_i, \mathbf{z}_i) = 5 \times (1 - 0.4) = 5 \times 0.6 = 3
$$
这意味着，在考虑了群体A的影响后，我们预期的计数结果是3，而不是群体非A的预期计数5。


1. **ZIP和ZINB模型的区别**

- **ZIP模型** 假设群体~A中的计数结果遵循泊松分布，其均值由计数模型确定。
- **ZINB模型** 假设群体~A中的计数结果遵循负二项分布，除了均值外，还有一个描述数据离散程度的参数。
这两种模型都考虑了数据中零结果过多的现象，但它们在处理非零结果分布时采用了不同的统计分布。


6. **总结**

零膨胀模型通过结合二元模型（预测个体属于哪个潜在群体）和计数模型（预测群体~A中个体的计数结果）来计算观察到的计数结果的概率。这种方法特别适用于那些数据中存在大量零结果的情况，例如科学研究中的发表记录。ZIP和ZINB模型提供了两种不同的方法来处理这种类型的数据。


1. **零膨胀泊松模型（ZIP）**

**ZIP模型假设**：
- **条件泊松分布**：对于不属于群体A的个体（即 $ A_i = 0 $），其计数结果 $ y_i $ 假设服从泊松分布，其均值（lambda，$ \lambda_i $）由模型的计数部分决定。
- **零值的生成**：群体A中的个体（即 $ A_i = 1 $）只产生零计数，而群体非A中的个体可能产生零计数或正计数。
**ZIP模型的应用场景**：
- 当数据中存在大量零计数，且这些零计数被认为是由某个未观测到的二元过程（即群体A和非A的区分）产生时。
- 当计数结果的变异性可以用泊松分布来合理描述时。
2. **零膨胀负二项模型（ZINB）**

**ZINB模型假设**：
- **条件负二项分布**：对于不属于群体A的个体，计数结果 $ y_i $ 假设服从负二项分布，其均值和方差由模型的计数部分决定。负二项分布通过引入一个离散参数（通常表示为 $ \alpha $ 或 $ \theta $）来允许方差大于均值，从而解决泊松分布的等方差问题。
- **零值的生成**：与ZIP模型类似，群体A中的个体只产生零计数，而群体非A中的个体可能产生零计数或正计数。
**ZINB模型的应用场景**：
- 当数据中存在大量零计数，且计数结果的变异性大于泊松分布所能描述的范围时，即存在过度离散性（overdispersion）。
- 当负二项分布的特性更适合描述数据的变异性时。

3. **ZIP与ZINB的比较**
- **离散性**：ZIP模型假设泊松分布，方差等于均值；而ZINB模型通过引入离散参数允许方差大于均值，更适合描述过度离散的数据。
- **参数数量**：ZINB模型比ZIP模型多一个参数，即离散参数，这使得ZINB模型更加灵活，但同时也需要更多的数据来准确估计参数。
- **计算复杂性**：由于ZINB模型需要估计额外的离散参数，其计算过程通常比ZIP模型更复杂。
4. **模型选择**

在实际应用中，选择ZIP模型还是ZINB模型通常取决于数据的特性和研究目的：
- 如果数据的变异性可以用泊松分布合理描述，且计算资源有限，可以选择ZIP模型。
- 如果数据表现出明显的过度离散性，或者研究者希望更灵活地描述数据的变异性，可以选择ZINB模型。
5. **实例分析**

假设我们正在研究一个城市的交通事故数据，数据中包含大量的零事故天数。我们想要建立一个模型来预测未来某一天的事故数量。
- **数据探索**：我们首先检查数据的分布特性，发现事故数量的方差大于均值，这表明泊松分布可能不足以描述数据的变异性。
- **模型选择**：基于上述发现，我们选择ZINB模型来分析数据，因为它可以更好地处理过度离散性。
- **参数估计**：我们使用最大似然估计或其他统计方法来估计ZINB模型的参数，包括事故数量的均值、离散参数以及决定零事故概率的二元过程参数。
- **预测与解释**：通过ZINB模型，我们可以得到未来某一天发生事故数量的预测，并对影响事故数量的因素进行解释。

希望这个进一步的解释能帮助你更深入地理解零膨胀模型，特别是ZIP和ZINB模型的应用和区别。

### Estimation using zinb and zip

The ZIP and ZINB models are fit with the zip and zinb commands, respectively, listed here with their basic options:

* **``zip depvar [indepvars] [if] [m] [weight], inflat e(indepvars2) [noconstant probit vce(vcetype) irr vuong]``**

* **``zinb depvar [indepvars] [if] [in] [weight], inflate(indepvars2) [noconstant probit vce(vcetypc) irr vuong]``**

**Variable lists**

* **``depvar``** is the dependent variable, which must be a count with no negative values or non-integers.

* **``indepvars``** is a list of independent variables that determine counts among those who are not always Os. If **``indepvars``** is not included, a model with only an intercept is fit.

* **``indepvars2``** is a list of inflation variables that determine whether one is in the “always 0” group (group A) or the “not always 0” group (group ~A).

* indepvars and indepvars2 can be the same variables but do not have to be.

**Options**

Here we consider only those options that differ from the options for earlier models in this chapter.

**``probit``** specifies that the model determining the probability of being in group A versus group -A be a binary probit model. By default, a binary logit model is used.

**``vuong``** requests a Vuong (1989) test of the ZIP versus the PRM or of the ZINB versus the NBRM. Details are given in section 9.7.2. The **``vuong``** option is not available if robust standard errors are used.


### Example of zero-inflated models

The output from **``zip``** and **``zinb``** is similar, so here we show only the output for **``zinb``**:

```
zinb art i.female i.married kid5 phd mentor, ///
    inflate(i.female i.married kid5 phd mentor) nolog vce(robust)
estimates store zinb
```

    Zero-inflated negative binomial regression              Number of obs =    915
    Inflation model: logit                                  Nonzero obs   =    640
                                                            Zero obs      =    275
                                                            Wald chi2(5)  =  52.34
    Log pseudolikelihood = -1549.991                        Prob > chi2   = 0.0000
    
    ------------------------------------------------------------------------------
                 |               Robust
             art | Coefficient  std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
    art          |
          female |
         Female  |      -0.20       0.08    -2.58   0.010        -0.34       -0.05
                 |
         married |
        Married  |       0.10       0.09     1.15   0.251        -0.07        0.26
            kid5 |      -0.15       0.06    -2.71   0.007        -0.26       -0.04
             phd |      -0.00       0.04    -0.02   0.986        -0.08        0.08
          mentor |       0.02       0.00     5.99   0.000         0.02        0.03
           _cons |       0.42       0.15     2.85   0.004         0.13        0.70
    -------------+----------------------------------------------------------------
    inflate      |
          female |
         Female  |       0.64       1.00     0.63   0.526        -1.33        2.60
                 |
         married |
        Married  |      -1.50       1.13    -1.33   0.184        -3.71        0.71
            kid5 |       0.63       0.45     1.41   0.160        -0.25        1.50
             phd |      -0.04       0.31    -0.12   0.904        -0.65        0.58
          mentor |      -0.88       0.30    -2.96   0.003        -1.47       -0.30
           _cons |      -0.19       1.56    -0.12   0.902        -3.25        2.87
    -------------+----------------------------------------------------------------
        /lnalpha |      -0.98       0.15    -6.73   0.000        -1.26       -0.69
    -------------+----------------------------------------------------------------
           alpha |       0.38       0.05                          0.28        0.50
    ------------------------------------------------------------------------------


The top set of coefficients, labeled **``art``** in the left margin, corresponds to the NBRM for those in group A. The lower set of coefficients, labeled **``inflate``**, corresponds to the binary model predicting group membership.

### 6.3 Interpretation of coefficients

When interpreting zero-inflated models, it is easy to be confused by the direction of the coefficients. **``listcoef``** makes interpretation simpler. For example, consider the results for the ZINB:

```
listcoef, help
```

    zinb (N=915): Factor change in expected count 
    
      Observed SD:  1.9261
    
    Count equation: Factor change in expected count for those not always 0
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|       e^b   e^bStdX     SDofX
    -------------+----------------------------------------------------------
          female |
         Female  |   -0.1955   -2.580    0.010     0.822     0.907     0.499
                 |
         married |
        Married  |    0.0976    1.148    0.251     1.103     1.047     0.473
            kid5 |   -0.1517   -2.707    0.007     0.859     0.890     0.765
             phd |   -0.0007   -0.018    0.986     0.999     0.999     0.984
          mentor |    0.0248    5.989    0.000     1.025     1.265     9.484
        constant |    0.4167    2.846    0.004         .         .         .
    -------------+----------------------------------------------------------
    alpha        |
         lnalpha |   -0.9764        .        .         .         .         .
           alpha |    0.3767        .        .         .         .         .
    ------------------------------------------------------------------------
           b = raw coefficient
           z = z-score for test of b=0
       P>|z| = p-value for z-test
         e^b = exp(b) = factor change in expected count for unit increase in X
     e^bStdX = exp(b*SD of X) = change in expected count for SD increase in X
       SDofX = standard deviation of X
    
    Binary equation: factor change in odds of always 0
    ------------------------------------------------------------------------
                 |         b        z    P>|z|       e^b   e^bStdX     SDofX
    -------------+----------------------------------------------------------
          female |
         Female  |    0.6359    0.634    0.526     1.889     1.373     0.499
                 |
         married |
        Married  |   -1.4995   -1.329    0.184     0.223     0.492     0.473
            kid5 |    0.6284    1.406    0.160     1.875     1.617     0.765
             phd |   -0.0377   -0.121    0.904     0.963     0.964     0.984
          mentor |   -0.8823   -2.963    0.003     0.414     0.000     9.484
        constant |   -0.1917   -0.123    0.902         .         .         .
    ------------------------------------------------------------------------
           b = raw coefficient
           z = z-score for test of b=0
       P>|z  | = p-value for z-test
         e^b = exp(b) = factor change in odds for unit increase in X
     e^bStdX = exp(b*SD of X) = change in odds for SD increase in X
       SDofX = standard deviation of X

The top half of the output, labeled **Count equation**, contains coefficients for the factor change in the expected count for those who have the opportunity to publish (that is, group ~A). The coefficients can be interpreted in the same way as coefficients from the PRM or the NBRM. For example,

>Among those who have the opportunity to publish, being a female scientist decreases the expected rate of publication by a factor of 0.82, holding other variables constant.

The bottom half of the output, labeled **Binary equation**, contains coefficients for the factor change in the odds of being in group A compared with being group -A. These can be interpreted just as the coefficients for a binary logit model. For example,

>Being a female scientist increases the odds of not having the opportunity to publish by a factor of 1.89, holding other variables constant.

As found in this example, when the same variables are included in both equations, the signs of the corresponding coefficients from the binary equation are often in the opposite direction of those from the count equation. This makes substantive sense. The count equation predicts the number of publications, so a positive coefficient indicates higher productivity. In contrast, the binary equation is predicting membership in the group that always has zero counts, so a positive coefficient implies lower productivity. This is not, however, required by the model.

### 6.4 Interpretation of predicted probabilities

For th e ZIP,

$$\widehat{\Pr}\left(y=0\mid\mathbf{x},\mathbf{z}\right)=\widehat{\psi}+\left(1-\widehat{\psi}\right)e^{-\widehat{\mu}}$$

where $\widehat{\mu}=\exp\left(\mathbf{x}\widehat{\boldsymbol{\beta}}\right)\mathrm{~and~}\widehat{\psi}=F(\mathbf{z}\widehat{\boldsymbol{\gamma}}).$ The predicted probability of a positive count applies only to the $1-\widehat{\psi}$ observations in group ~A:

$$\widehat{\Pr}(y\mid\mathbf{x})=\left(1-\widehat{\psi}\right)\frac{e^{-\widehat{\mu}_{t}}\widehat{\mu}^{y}}{y!}$$

Similarly, for th e ZINB,

$$\widehat{\Pr}\left(y=0\mid\mathbf{x},\mathbf{z}\right)=\widehat{\psi}+\left(1-\widehat{\psi}\right)\left(\frac{\widehat{\alpha}^{-1}}{\widehat{\alpha}^{-1}+\widehat{\mu}_{i}}\right)^{\widehat{\alpha}^{-1}}$$

and the predicted probability for a positive count is

$$\widehat{\Pr}(y\mid\mathbf{x})=\left(1-\widehat{\psi}\right)\frac{\Gamma\left(y+\widehat{\alpha}^{-1}\right)}{y!\Gamma(\widehat{\alpha}^{-1})}\left(\frac{\widehat{\alpha}^{-1}}{\widehat{\alpha}^{-1}+\widehat{\mu}}\right)^{\widehat{\alpha}^{-1}}\left(\frac{\widehat{\mu}}{\widehat{\alpha}^{-1}+\widehat{\mu}}\right)^{y}$$

Predicted probabilities can be computed with **``margins``** and with our SPost commands **``mtable``**, **``mgen``**, and **``mchange``**.

* **Predicted probabilities with mtable**

Suppose that we want to compare the predicted probabilities for a married female scientist with young children who came from a weak graduate program with those for a married male from a strong department who had a productive mentor. We can use the **``mtable``** command to display these two predictions in one table.

First, we use a **``vars``** option to include in the table the variables on which we are focusing. By default, these would not be included because they do not vary within either of the **``mtable``** commands. Second, because the **``at()``** variables in the table make it clear what each row contains, we use **``norownumbers``** so that only column labels are shown. The **``width(7)``** option makes the results fit without wrapping.

```
quietly mtable, at(female=0 married=1 kid5=3 phd=3 mentor=10) ///
    atvars(female phd mentor) pr(0/5)
mtable, at(female=1 married=1 kid5=3 phd=1 mentor=0) ///
    atvars(female phd mentor) pr(0/5) below width(7) norownumbers
```

    Expression: Pr(art), predict(pr())
    
           1.                                                                        
      female      phd   mentor        0        1        2        3        4        5
    --------------------------------------------------------------------------------
           0        3       10    0.334    0.300    0.185    0.097    0.047    0.021
           1        1        0    0.835    0.096    0.043    0.017    0.006    0.002
    
    Specified values of covariates
    
               |  female  married     kid5      phd   mentor
     ----------+--------------------------------------------
         Set 1 |       0        1        3        3       10
       Current |       1        1        3        1        0

The predicted probabilities of a 0 include both scientists from group A and scientists from group ~A who by chance did not publish.

To compute the probability of being in group A , we use the **`predict(pr)`** option:9

```
quietly mtable, at(female=0 married=1 kid5=3 phd=3 mentor=10) ///
    atvars(female phd mentor) predict(pr)
mtable, at(female=1 married=1 kid5=3 phd=1 mentor=0) ///
    atvars(female phd mentor) predict(pr) below norownumbers decimals(4)
```

    Expression: Pr(art = always 0), predict(pr)
    
           1.                              
      female       phd    mentor    PrAll0
    --------------------------------------
           0         3        10    0.0002
           1         1         0    0.6883
    
    Specified values of covariates
    
               |   female   married      kid5       phd    mentor
     ----------+-------------------------------------------------
         Set 1 |        0         1         3         3        10
       Current |        1         1         3         1         0


We used the option **``decimals(4)``** to show that the probability of being in group A for the men is small but is not 0.

* **Plotting predicted probabilities with mgen**

In this section, we explore the two sources of 0s and how they each contribute to the predicted proportion of 0s as the number of publications by a scientist’s mentor changes. First, we use **``mgen``** to compute the predicted probability of a 0 of either type as mentor’s publications range from 0 to 6, holding other variables to their means:

```
mgen, at(mentor=(0/6)) atmeans pr(0) stub(ZINB) replace
```

Next, we compute the probability of being in group A by specifying the **``predict(pr)``** option.

```
mgen, at(mentor=(0/6)) atmeans predict(pr) stub(ZINB) replace
```

    Predictions from: margins, at(mentor=(0/6)) atmeans predict(pr)
    
    Variable    Obs Unique      Mean        Min       Max  Label
    ----------------------------------------------------------------------------------------------------------
    ZINBprall0    7      7  .0912045   .0024928  .3322358  Pr(art=always0) from margins
    ZINBll        7      7  .0144599  -.0286754  .1578251  95% lower limit
    ZINBul        7      7  .1679491   .0113839  .5066465  95% upper limit
    ZINBmentor    7      7         3          0         6  Mentor's # of articles
    ----------------------------------------------------------------------------------------------------------
    
    Specified values of covariates
    
            1.         1.                      
       female    married       kid5        phd 
    ------------------------------------------
     .4601093   .6622951    .495082   3.103109 


Variable **ZINBprallO** contains the probability of a 0 from being in group A. If we subtract this from the overall probability of a 0 from any source, contained in the variable **ZINBpranyO**, we obtain the probability of a 0 for those in group -A; these scientists could have published but by chance did not. After we compute this difference, we label the variables so that the legend of our plot will be clear.

```
gen ZINBprcount0 = ZINBprany0 - ZINBprall0
label var ZINBprall0 "Always Zero from Binary Equation"
label var ZINBprcount0 "Sometimes Zero from Count Equation"
label var ZINBprany0 "Zeroes from Both Equations"
label var ZINBmentor "Mentor's Publications"
```

These variables can then be plotted:

```
graph twoway connected ZINBprall0 ZINBprcount0 ZINBprany0 ZINBmentor, ///
    xlabel(0/6) ylabel(0(.1).5, gmax) ///
    ytitle(Probability of Zero) msymbol(Sh Dh O) legend(rows(3))
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.28.1.png" style="width:550px;height:370px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The curve marked with **□'s** is a probability curve like those in chapters 5 and 6 for binary models. It indicates the probability of being in the group that never publishes, where each point corresponds to different rates $\mu$ determined by the level of the mentor’s publications. The curve marked with **◇’s** shows the probability of 0s from a negative binomial distribution. The overall probability of a zero count is the sum of the two curves, which is shown by the curve with **●’s**. We see that the probability of being in group A drops off quickly as the mentor’s number of publications increases, so for scientists whose mentors have more than three publications, virtually all the zero counts are due to membership in group ~A.


## 7 Comparisons among count models
There are two approaches that can be used to compare count models. First, we can compare the mean predicted probabilities and the observed proportions for each count. This was done when we compared predictions from the PRM and NBRM earlier. Second, we can use various tests and measures of fit to compare models, such as the LR test of overdispersion or the BIC statistic.

We begin by showing you how to make these computations using official Stata commands. Then, we demonstrate the SPost command **``countfit``**, which automates this process. Although **``countfit``** is the simplest way to compare models, it is useful to understand how these computations are made to more fully understand the output of **``countfit``**.

### 7.1 Comparing mean probabilities

One way to compare count models is to compute average predicted probabilities and compare their fit to the observed data across models. First, we compute the mean predicted probability. For example, in the PRM,

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.28.5.png" style="width:450px;height:100px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

This is simply the average across all observations of the probability of each count. $\widehat{\mathrm{Pr}}_{\mathrm{Observed}}(y=k)$ is the observed probability or proportion of observations with the count equal to $ k $. The difference between the observed probability and the mean probability is

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.28.6.png" style="width:450px;height:90px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.28.2.png" style="width:650px;height:470px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.28.4.png" style="width:650px;height:190px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.28.3.png" style="width:650px;height:500px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


To compute these measures, we first fit each of the four models and then use **``mgen``**, **``meanpred``** to create variables containing average predictions:

```
poisson art i.female i.married kid5 phd mentor, nolog
mgen, stub(PRM) pr(0/9) meanpred
```


    Poisson regression                                      Number of obs =    915
                                                            LR chi2(5)    = 183.03
                                                            Prob > chi2   = 0.0000
    Log likelihood = -1651.0563                             Pseudo R2     = 0.0525
    
    ------------------------------------------------------------------------------
             art | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
          female |
         Female  |      -0.22       0.05    -4.11   0.000        -0.33       -0.12
                 |
         married |
        Married  |       0.16       0.06     2.53   0.011         0.03        0.28
            kid5 |      -0.18       0.04    -4.61   0.000        -0.26       -0.11
             phd |       0.01       0.03     0.49   0.627        -0.04        0.06
          mentor |       0.03       0.00    12.73   0.000         0.02        0.03
           _cons |       0.30       0.10     2.96   0.003         0.10        0.51
    ------------------------------------------------------------------------------
    
    . mgen, stub(PRM) pr(0/9) meanpred
    
    Predictions from: 
    
    Variable   Obs Unique       Mean        Min       Max  Label
    ---------------------------------------------------------------------------------------
    PRMval      10     10        4.5          0         9  Articles in last 3 yrs of PhD
    PRMobeq     10     10   .0993443   .0010929  .3005464  Observed proportion
    PRMoble     10     10   .8328962   .3005464  .9934426  Observed cum. proportion
    PRMpreq     10     10   .0998819   .0009304  .3098447  Avg predicted Pr(y=#)
    PRMprle     10     10   .8308733   .2092071  .9988187  Avg predicted cum. Pr(y=#)
    PRMob_pr    10     10  -.0005376  -.0475604  .0913393  Observed - Avg Pr(y=#)
    ---------------------------------------------------------------------------------------


```
nbreg art i.female i.married kid5 phd mentor, nolog
mgen, stub(NBRM) pr(0/9) meanpred
```

    Negative binomial regression                            Number of obs =    915
                                                            LR chi2(5)    =  97.96
    Dispersion: mean                                        Prob > chi2   = 0.0000
    Log likelihood = -1560.9583                             Pseudo R2     = 0.0304
    
    ------------------------------------------------------------------------------
             art | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
          female |
         Female  |      -0.22       0.07    -2.98   0.003        -0.36       -0.07
                 |
         married |
        Married  |       0.15       0.08     1.83   0.067        -0.01        0.31
            kid5 |      -0.18       0.05    -3.32   0.001        -0.28       -0.07
             phd |       0.02       0.04     0.42   0.672        -0.06        0.09
          mentor |       0.03       0.00     8.38   0.000         0.02        0.04
           _cons |       0.26       0.14     1.85   0.065        -0.02        0.53
    -------------+----------------------------------------------------------------
        /lnalpha |      -0.82       0.12                         -1.05       -0.58
    -------------+----------------------------------------------------------------
           alpha |       0.44       0.05                          0.35        0.56
    ------------------------------------------------------------------------------
    LR test of alpha=0: chibar2(01) = 180.20               Prob >= chibar2 = 0.000
    
    . mgen, stub(NBRM) pr(0/9) meanpred
    
    Predictions from: 
    
    Variable   Obs Unique      Mean        Min       Max  Label
    -------------------------------------------------------------------------------------------
    NBRMval     10     10       4.5          0         9  Articles in last 3 yrs of PhD
    NBRMobeq    10     10  .0993443   .0010929  .3005464  Observed proportion
    NBRMoble    10     10  .8328962   .3005464  .9934426  Observed cum. proportion
    NBRMpreq    10     10  .0993102   .0035078  .3035957  Avg predicted Pr(y=#)
    NBRMprle    10     10  .8314587   .3035957  .9931023  Avg predicted cum. Pr(y=#)
    NBRMob_pr   10     10   .000034  -.0145355  .0144863  Observed - Avg Pr(y=#)
    -------------------------------------------------------------------------------------------


```
zip art i.female i.married kid5 phd mentor, ///
    inflate(i.female i.married kid5 phd mentor) nolog
mgen, stub(ZIP) pr(0/9) meanpred
```

    Zero-inflated Poisson regression                        Number of obs =    915
    Inflation model: logit                                  Nonzero obs   =    640
                                                            Zero obs      =    275
                                                            LR chi2(5)    =  78.56
    Log likelihood = -1604.773                              Prob > chi2   = 0.0000
    
    ------------------------------------------------------------------------------
             art | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
    art          |
          female |
         Female  |      -0.21       0.06    -3.30   0.001        -0.33       -0.08
                 |
         married |
        Married  |       0.10       0.07     1.46   0.145        -0.04        0.24
            kid5 |      -0.14       0.05    -3.02   0.003        -0.24       -0.05
             phd |      -0.01       0.03    -0.20   0.842        -0.07        0.05
          mentor |       0.02       0.00     7.89   0.000         0.01        0.02
           _cons |       0.64       0.12     5.28   0.000         0.40        0.88
    -------------+----------------------------------------------------------------
    inflate      |
          female |
         Female  |       0.11       0.28     0.39   0.695        -0.44        0.66
                 |
         married |
        Married  |      -0.35       0.32    -1.11   0.265        -0.98        0.27
            kid5 |       0.22       0.20     1.10   0.269        -0.17        0.60
             phd |       0.00       0.15     0.01   0.993        -0.28        0.29
          mentor |      -0.13       0.05    -2.96   0.003        -0.22       -0.05
           _cons |      -0.58       0.51    -1.13   0.257        -1.58        0.42
    ------------------------------------------------------------------------------
    
    . mgen, stub(ZIP) pr(0/9) meanpred
    
    Predictions from: 
    
    Variable   Obs Unique      Mean        Min       Max  Label
    -----------------------------------------------------------------------------------------
    ZIPval      10     10       4.5          0         9  Articles in last 3 yrs of PhD
    ZIPobeq     10     10  .0993443   .0010929  .3005464  Observed proportion
    ZIPoble     10     10  .8328962   .3005464  .9934426  Observed cum. proportion
    ZIPpreq     10     10  .0999393   .0008223  .2985684  Avg predicted Pr(y=#)
    ZIPprle     10     10  .8307272   .2985684  .9993931  Avg predicted cum. Pr(y=#)
    ZIPob_pr    10     10  -.000595  -.0508832  .0539793  Observed - Avg Pr(y=#)
    -----------------------------------------------------------------------------------------


    Zero-inflated negative binomial regression              Number of obs =    915
    Inflation model: logit                                  Nonzero obs   =    640
                                                            Zero obs      =    275
                                                            LR chi2(5)    =  67.97
    Log likelihood = -1549.991                              Prob > chi2   = 0.0000
    
    ------------------------------------------------------------------------------
             art | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
    art          |
          female |
         Female  |      -0.20       0.08    -2.59   0.010        -0.34       -0.05
                 |
         married |
        Married  |       0.10       0.08     1.16   0.248        -0.07        0.26
            kid5 |      -0.15       0.05    -2.80   0.005        -0.26       -0.05
             phd |      -0.00       0.04    -0.02   0.985        -0.07        0.07
          mentor |       0.02       0.00     7.10   0.000         0.02        0.03
           _cons |       0.42       0.14     2.90   0.004         0.14        0.70
    -------------+----------------------------------------------------------------
    inflate      |
          female |
         Female  |       0.64       0.85     0.75   0.454        -1.03        2.30
                 |
         married |
        Married  |      -1.50       0.94    -1.60   0.110        -3.34        0.34
            kid5 |       0.63       0.44     1.42   0.156        -0.24        1.50
             phd |      -0.04       0.31    -0.12   0.903        -0.64        0.57
          mentor |      -0.88       0.32    -2.79   0.005        -1.50       -0.26
           _cons |      -0.19       1.32    -0.14   0.885        -2.78        2.40
    -------------+----------------------------------------------------------------
        /lnalpha |      -0.98       0.14    -7.21   0.000        -1.24       -0.71
    -------------+----------------------------------------------------------------
           alpha |       0.38       0.05                          0.29        0.49
    ------------------------------------------------------------------------------
    
    . mgen, stub(ZINB) pr(0/9) meanpred
    
    Predictions from: 
    
    Variable   Obs Unique       Mean        Min       Max  Label
    ------------------------------------------------------------------------------------------
    ZINBval     10     10        4.5          0         9  Articles in last 3 yrs of PhD
    ZINBobeq    10     10   .0993443   .0010929  .3005464  Observed proportion
    ZINBoble    10     10   .8328962   .3005464  .9934426  Observed cum. proportion
    ZINBpreq    10     10   .0994811   .0032089  .3119487  Avg predicted Pr(y=#)
    ZINBprle    10     10   .8313521   .3119487  .9948109  Avg predicted cum. Pr(y=#)
    ZINBob_pr   10     10  -.0001368  -.0188056  .0138316  Observed - Avg Pr(y=#)
    ------------------------------------------------------------------------------------------

After each model, mgen generates the variable stubpreq that contains the average predicted probability $\overline{\Pr}(y=k)$ for counts 0-9 (specified with the `pr(0/9)` option) and the variable *stubobeq* that contains the observed proportion $\widehat{Pr}_{\mathrm{Observed}}\left(y=k\right)$. The value of the count itself is stored in variable stubval. Using these variables, we create a plot that compares the four models with the observed data:

```
label variable PRMobeq  "Observed"
label variable PRMpreq  "PRM"
label variable NBRMpreq "NBRM"
label variable ZIPpreq  "ZIP"
label variable ZINBpreq "ZINB"

graph twoway connected PRMobeq PRMpreq NBRMpreq ZIPpreq ZINBpreq PRMval, ///
    ytitle(Average Predicted Probability) ///
    xlabel(0/9) msymbol(Th Oh Sh O S) ///
    legend(col(3) holes(4)) ///
    msiz(*1.2 *1.4 *1.1 *1.4 *1.1) lwid(thin thin thin thin thin)
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.28.7.png" style="width:650px;height:470px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The graph is not very effective because the lines are difficult to distinguish from each other. The information is much clearer if we can instead plot the differences between the observed and predicted probabilities:


```
gen obs = PRMobeq
gen dPRM = obs - PRMpreq
label var dPRM "PRM"
gen dNBRM = obs - NBRMpreq
label var dNBRM "NBRM"
gen dZIP = obs - ZIPpreq
label var dZIP "ZIP"
gen dZINB = obs - ZINBpreq
label var dZINB "ZINB"

graph twoway connected dPRM dNBRM dZIP dZINB PRMval, ///
    ytitle(Observed-Predicted) ylabel(-.10(.05).10, grid gmin gmax) ///
    xlabel(0/9) msymbol(Oh Sh O S) ///
    subtitle(Positive deviations indicate more observed counts than predicted, ///
    pos(11)) ///
    msiz(*1.4 *1.1 *1.4 *1.1) lwid(thin thin thin thin thin)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.28.8.png" style="width:650px;height:470px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


Points above 0 on the y axis indicate more observed counts than predicted on average; those below 0 indicate fewer observed counts than predicted

The graph shows that only the PRM has a problem predicting the average number of Os. The ZIP does less well, predicting too many 1s and too few 2s and 3s. The NBRM and ZINB do about equally well. From these results, we might prefer the NBRM because it is simpler. Section 9.7.3 shows how to automate the creation of this type of graph with the **`countfit`** command.

### 7.2 Tests to compare count models

Plotting predictions is only an informal method of assessing the fit of a count model. More formal testing can be done with an LR test of overdispersion and a Vuong test to compare two models.

* **LR tests of α**

Because the NBRM reduces to the PRM when α = 0, the PRM and NBRM can be compared by testing $H_{0}:\alpha=0$ As shown in section 9.3.3, we find that

    Likelihood-ratio test of alpha=0: chibar2(01) = 180.20 prob>=chibar2 = 0.000


```
quietly zip art i.female i.married kid5 phd mentor, ///
    inflate(i.female i.married kid5 phd mentor)
estimates store zip
quietly zinb art i.female i.married kid5 phd mentor, ///
    inflate(i.female i.married kid5 phd mentor)
estimates store zinb
lrtest zip zinb, force
```
    
    Likelihood-ratio test
    Assumption: zip nested within zinb
    
     LR chi2(1) = 109.56
    Prob > chi2 = 0.0000

Given the significant LR test statistics, we conclude that the ZINB significantly improves the fit over the ZIP.

* **Vuong test of nonnested models**

Greene (1994) points out that the PRM and the ZIP are not nested.11 For the ZIP model to reduce to the PRM, $\psi $ must equal 0, but this does not occur when $\gamma=0$ because $\psi=F\left(\mathbf{z0}\right)=0.5$ Similarly, the NBRM and the ZINB are not nested. Greene proposes using a test by Vuong (1989, 319) for nonnested models. This test considers two models, where $\widehat{Pr_1}\left(y_{i}\mid\mathbf{x}_{i}\right)$ is the predicted probability of observing $y_i$ in the first model and $\widehat{\mathrm{Pr}}_2\left(y_i\mid\mathbf{x}_i\right)$ is the predicted probability for the second model. If there are inflation variables, we are assuming they are part of x. Define

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.28.9.png" style="width:250px;height:110px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>



and let $\overline{m}$ be the mean and $s_m$ be the standard deviation of $m_i$.The Vuong statistic to
test the hypothesis that $E\left(m\right)=0$ is 

$$V=\frac{\sqrt{N}\overline{m}}{s_m}$$

$V$ has an asymptotic normal distribution. If $V > 1.96$, the first model is favored; if $V < -1.96$, the second model is favored.
 
For **zip**. the **`vuong`** option computes the Vuong statistic comparing the ZIP with the PRM; for **zinb** , the **`vuong`** option compares the ZINB with the NBRM . For example,


* 基本概念
1. **PRM (Poisson Regression Model)** 和 **ZIP (Zero-Inflated Poisson)** 是两种统计模型，用于处理计数数据，特别是当数据中存在大量零值时。
2. **NBRM (Negative Binomial Regression Model)** 和 **ZINB (Zero-Inflated Negative Binomial)** 也是用于计数数据的模型，特别是当数据的方差大于均值时。
3. **Nested Models** 指的是一种模型可以简化为另一种模型的特殊情况。如果模型A在某些参数条件下可以变成模型B，那么我们说模型A嵌套于模型B。
4. **非嵌套模型 (Non-nested models)** 指的是两种模型之间不存在简化关系。

* 符号解释
 $ \psi $：某个参数，当它等于0时，ZIP模型可以简化为PRM模型。

 $ \gamma $：另一个参数，当它等于0时，$ \psi $ 不等于0。

 $ F(\mathbf{z0}) $：一个函数，这里用来说明 $ \psi $ 的值。

 $ \widehat{Pr_1} $ 和 $ \widehat{\mathrm{Pr}}_2 $：分别是两个模型预测的观察到 $ y_i $ 的概率。

* Vuong检验
Vuong检验是一种用于比较两个非嵌套模型拟合优度的统计方法。它通过计算Vuong统计量 $ V $ 来决定哪个模型更优。

1. 步骤1：定义 $ m_i $ 和它的统计量
   
 $ m_i $ 是对于每个观测值 $ i $，第一个模型预测概率与第二个模型预测概率的比值的自然对数。

 $ \overline{m} $ 是 $ m_i $ 的均值。

 $ s_m $ 是 $ m_i $ 的标准差。
2. 步骤2：计算Vuong统计量 $ V $

 $ V $ 的计算公式是 $ \frac{\sqrt{N}\overline{m}}{s_m} $，其中 $ N $ 是样本大小。
3. 步骤3：判断模型

 $ V $ 服从渐近正态分布。
 如果 $ V > 1.96 $，则偏好第一个模型。
 如果 $ V < -1.96 $，则偏好第二个模型。
4. 示例

在统计软件中，对于ZIP模型和PRM模型，或者ZINB模型和NBRM模型，可以通过设置 `vuong` 选项来计算Vuong统计量，以比较这两个模型。
5. 总结

Vuong检验为我们提供了一种在两个非嵌套模型之间做出选择的方法。通过计算每个模型的预测概率，然后使用这些预测概率来计算Vuong统计量，我们可以判断哪个模型更适合我们的数据。这种方法特别适用于存在大量零值或者方差大于均值的计数数据。



```
* Vuong test of non-nested models

zip art i.female i.married kid5 phd mentor, ///
    inflate(i.female i.married kid5 phd mentor) forcevuong nolog
listcoef, help
```

    zip (N=915): Factor change in expected count 
    
      Observed SD:  1.9261
    
    Count equation: Factor change in expected count for those not always 0
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|       e^b   e^bStdX     SDofX
    -------------+----------------------------------------------------------
          female |
         Female  |   -0.2091   -3.299    0.001     0.811     0.901     0.499
                 |
         married |
        Married  |    0.1038    1.459    0.145     1.109     1.050     0.473
            kid5 |   -0.1433   -3.022    0.003     0.866     0.896     0.765
             phd |   -0.0062   -0.199    0.842     0.994     0.994     0.984
          mentor |    0.0181    7.886    0.000     1.018     1.187     9.484
        constant |    0.6408    5.283    0.000         .         .         .
    ------------------------------------------------------------------------
           b = raw coefficient
           z = z-score for test of b=0
       P>|z| = p-value for z-test
         e^b = exp(b) = factor change in expected count for unit increase in X
     e^bStdX = exp(b*SD of X) = change in expected count for SD increase in X
       SDofX = standard deviation of X
    
    Binary equation: factor change in odds of always 0
    ------------------------------------------------------------------------
                 |         b        z    P>|z|       e^b   e^bStdX     SDofX
    -------------+----------------------------------------------------------
          female |
         Female  |    0.1097    0.392    0.695     1.116     1.056     0.499
                 |
         married |
        Married  |   -0.3540   -1.115    0.265     0.702     0.846     0.473
            kid5 |    0.2171    1.105    0.269     1.242     1.181     0.765
             phd |    0.0013    0.009    0.993     1.001     1.001     0.984
          mentor |   -0.1341   -2.964    0.003     0.874     0.280     9.484
        constant |   -0.5771   -1.133    0.257         .         .         .
    ------------------------------------------------------------------------
    Vuong Test =  4.18 (p=0.000) favoring ZIP over PRM.
           b = raw coefficient
           z = z-score for test of b=0
       P>|z  | = p-value for z-test
         e^b = exp(b) = factor change in odds for unit increase in X
     e^bStdX = exp(b*SD of X) = change in odds for SD increase in X
       SDofX = standard deviation of X
    
The significant, positive value of V supports the ZIP over the PRM. If you use **`listcoef`**, you get more guidance in interpreting the result:

```
zinb art i.female i.married kid5 phd mentor, ///
    inflate(i.female i.married kid5 phd mentor) forcevuong nolog
listcoef, help
```

    zinb (N=915): Factor change in expected count 
    
      Observed SD:  1.9261
    
    Count equation: Factor change in expected count for those not always 0
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|       e^b   e^bStdX     SDofX
    -------------+----------------------------------------------------------
          female |
         Female  |   -0.1955   -2.586    0.010     0.822     0.907     0.499
                 |
         married |
        Married  |    0.0976    1.155    0.248     1.103     1.047     0.473
            kid5 |   -0.1517   -2.799    0.005     0.859     0.890     0.765
             phd |   -0.0007   -0.019    0.985     0.999     0.999     0.984
          mentor |    0.0248    7.097    0.000     1.025     1.265     9.484
        constant |    0.4167    2.902    0.004         .         .         .
    -------------+----------------------------------------------------------
    alpha        |
         lnalpha |   -0.9764        .        .         .         .         .
           alpha |    0.3767        .        .         .         .         .
    ------------------------------------------------------------------------
           b = raw coefficient
           z = z-score for test of b=0
       P>|z| = p-value for z-test
         e^b = exp(b) = factor change in expected count for unit increase in X
     e^bStdX = exp(b*SD of X) = change in expected count for SD increase in X
       SDofX = standard deviation of X
    
    Binary equation: factor change in odds of always 0
    ------------------------------------------------------------------------
                 |         b        z    P>|z|       e^b   e^bStdX     SDofX
    -------------+----------------------------------------------------------
          female |
         Female  |    0.6359    0.749    0.454     1.889     1.373     0.499
                 |
         married |
        Married  |   -1.4995   -1.597    0.110     0.223     0.492     0.473
            kid5 |    0.6284    1.419    0.156     1.875     1.617     0.765
             phd |   -0.0377   -0.122    0.903     0.963     0.964     0.984
          mentor |   -0.8823   -2.790    0.005     0.414     0.000     9.484
        constant |   -0.1917   -0.145    0.885         .         .         .
    ------------------------------------------------------------------------
    Vuong Test =  2.24 (p=0.012) favoring ZINB over NBRM.
           b = raw coefficient
           z = z-score for test of b=0
       P>|z  | = p-value for z-test
         e^b = exp(b) = factor change in odds for unit increase in X
     e^bStdX = exp(b*SD of X) = change in odds for SD increase in X
       SDofX = standard deviation of X

Although it is possible to compute a Vuong statistic to compare other pairs of models, such as ZIP and NBRM, these are not available in Stata. This does not mean they cannot be computed, and in the next section, we show how it can be done with the more complicated case of a comparison against the hurdle model. The Vuong test is not appropriate when robust standard errors are used.

Overall, these tests provide evidence that the ZINB fits the data best. However, when fitting a series of models with no theoretical rationale, it is easy to overfit the data, and the ZINB adds many more parameters to the NBRM. Here the most compelling evidence for the ZINB is that it makes substantive sense. Within science, there are some scientists who for structural reasons cannot publish, but for other scientists, the failure to publish in a given period of time is a matter of chance. This is the basis of the zero-inflated models. The NBRM seems preferable to the PRM, because there are probably unobserved sources of heterogeneity that differentiate the scientists. In sum, the ZINB makes substantive sense and fits the data well. At the same time, the simplicity of the NBRM is also compelling.

* (Advanced) Computing the Vuong test with a hurdle model
  
>We mark this section as advanced because it involves computing a test that is not built into Stata. The section is only directly useful if you are working with hurdle models. However, computing the test also provides another illustration of working with predicted values postestimation, as well as of the value of mastering how to use loops in Stata.

You might be interested in computing a Vuong test to compare the ZINB to the HRM we fit earlier. This test is not built into Stata, but we can use what we have explained so far to compute it. Because we are computing the test “by hand”, Stata will not stop us from calculating the test statistic even when it is inappropriate; in particular, the Vuong test should not be computed if robust standard errors have been used.

The Vuong test is based on comparing the predicted probabilities of the count values that were actually observed. In other words, for cases in which $ y = k $ we compare $\widehat{\Pr}(y=k)$ across models for all observed values of the count outcome. The first step, then, is to generate the predicted probabilities for every observation for all observed counts. In our data, $ y $ ranges from 0 to 19. For the ZINB, computing the predicted probabilities of each count is a relatively straightforward application of a **`foreach`** loop:

```
quietly zinb art i.female i.married kid5 phd mentor, ///
    inflate(i.female i.married kid5 phd mentor)
forvalues icount = 0/19 {
    predict ZINB`icount', pr(`icount')
    }
```

For the HRM, this step is more complicated. We first fit the HRM by separately fitting logit and zero-truncated models and store the results:

```
quietly logit art i.female i.married kid5 phd mentor
estimates store Hlogit
quietly tnbreg art i.female i.married kid5 phd mentor if art > 0
estimates store Hztnb
```

We need the logit results to compute the predicted probability of observing a positive count, which we save in the variable **HRMnotO**. Subtracting from 1 gives us the probability of a 0:

```
estimates restore Hlogit
predict HRMnot0
label var HRMnot0 "HRM prob of non-zero count"
gen HRM0 = 1 - HRMnot0
label var HRM0 "HRM prob of zero count"
```

To compute the predicted probabilities of counts 1-19, we calculate the conditional probability of each count and then multiply by HRMnotO to compute the unconditional probabilities:

```
estimates restore Hztnb
forvalues icount = 1/19 {
    predict HRM`icount', cpr(`icount')
    quietly replace HRM`icount' = HRM`icount' * HRMnot0
    label var HRM`icount' "HRM unconditional prob(`icount')"
    }
```

At this point, we have two sets of predicted probabilities: **ZINB0-ZINB19** and **HRM0-HRM19**. For each model, we need to generate a single variable that contains the predicted probability of the count that was actually observed. Every observation has a probability for all possible counts, but only one count is the one that actually occurred for each scientist. We save the probability of the observed count by first creating an empty variable and then using a loop to assign the predicted probability of the observed count to this variable:

```
gen ZINBprobs = .
label var ZINBprobs "ZINB prob of count that was observed"
gen HRMprobs = .
label var HRMprobs "HRM prob of count that was observed"
forvalues icount = 0/19 {
    quietly replace ZINBprobs = ZINB`icount' if art == `icount'
    qui replace HRMprobs = HRM`icount' if art == `icount'
    }
```

We then **`summarize`** these variables:

```
sum ZINBprob HRMprobs
```

        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
       ZINBprobs |        915    .2346309    .1369695   .0001224   .7454953
        HRMprobs |        915      .23238    .1221957   .0002254   .5574288

The means are similar, though the average probability of the observed count is slightly higher for the ZINB, meaning that the ZINB has a higher likelihood than the HRM. The Vuong test from (9.14) formalizes this comparison:

```
gen mZINB_HRM = ln(ZINBprobs/HRMprobs)
sum mZINB_HRM
```

        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
       mZINB_HRM |        915    .0028478    .1551608  -.7172125   .9502286


```
display (r(mean)*sqrt(r(N)))/r(sd)
```

  .55517845    

### 7.3 Using countfit to compare count models

The **`countfit`** command automates the analyses described in the last two sections for the PRM, NBRM, ZIP, and ZINB. The command can provide a table of estimates, a table of differences between observed and average estimated probabilities, a graph of these differences, and various tests and measures of fit used to compare count models. The syntax is

* **`countfit` `varlist [if] [in] [, inflate(varlist2) noconst prm nbreg zip zinb stub(prefix) replace note(string) nograph nodifferences noptable noestimates nofit nodash maxcount(#) noisily]`**

* **Options for specifying the model**
  
**`varlist`** is the variable list for the model, beginning with the count outcome variable

**`if`** and **`in`** specify the sample used for fitting the models.

**`inflate(varlist2)`** specifies the inflation variables for ZIP and ZINB.

**`noconstant`** specifies that no constant be included in the model.

* **Options to select the models to fit**
  
By default, PRM, NBRM, ZIP, and ZINB are all fit. If you want only some of these models, specify the models you want.

**`prm`** fits the PRM.

**`nbreg`** fits the NBRM.

**`zip`** fits the ZIP.

**`zinb`** fits the ZINB.

* **Options to label and save results**

**`stub(prefix)`** can be up to five letters to prefix the variables that are created and to label the models in the output. This name is placed in front of the type of model (for example, namePRM). These labels help keep track of results from multiple specifications of models.

**`replace`** replaces variables created by **`stub()`** if they already exist.

**`note(string)`** adds a label to the graph.

* **Options to control what is printed**

**`nograph`** suppresses the graph of differences from observed counts.

**`nodifferences`** suppresses the table of differences from observed counts.

**`noptable`** suppresses the table of predictions for each model.

**`noestimates`** suppresses the table of estimated coefficients.

**`nofit`** suppresses the table of fit statistics and tests of fit.

**`nodash`** suppresses dashed lines between measures of fit.

**`maxcount(#)`** specifies the number of counts to evaluate.

**`noisily`** includes output from Stata estimation commands; without this option, the results are shown only in the estimates table output.

To illustrate what **`countfit`** does, we use **`countfit`** with our publication example and discuss the output that is generated. **`countfit`** estimates each of the models, so our command includes the specification of the outcome, the $x$ variables, and the $z$ variables for zero-inflated models. We do not use any of the options that limit the output that is generated.


```
use couart4, clear
countfit art i.female i.married kid5 phd mentor, inflate(i.mentor i.female)
```

    
    --------------------------------------------------------------------------------
                          Variable |    PRM        NBRM         ZIP        ZINB     
    -------------------------------+------------------------------------------------
    art                            |
           Gender: 1=female 0=male |                                                
                           Female  |     0.799       0.805       0.802       0.814  
                                   |     -4.11       -2.98       -3.48       -2.59  
                                   |                                                
               Married: 1=yes 0=no |                                                
                          Married  |     1.168       1.162       1.131       1.146  
                                   |      2.53        1.83        1.85        1.68  
                     # of kids < 6 |     0.831       0.838       0.849       0.843  
                                   |     -4.61       -3.32       -3.75       -3.27  
                      PhD prestige |     1.013       1.015       0.994       1.002  
                                   |      0.49        0.42       -0.22        0.05  
            Mentor's # of articles |     1.026       1.030       1.020       1.025  
                                   |     12.73        8.38        8.78        7.04  
                          Constant |     1.356       1.292       1.891       1.557  
                                   |      2.96        1.85        5.62        3.10  
    -------------------------------+------------------------------------------------
    lnalpha                        |
                          Constant |                 0.442                   0.309  
                                   |                 -6.81                   -6.71  
    -------------------------------+------------------------------------------------
    inflate                        |
                                   |                                                
            Mentor's # of articles |                                                
                                1  |                             1.121       1.153  
                                   |                              0.26        0.24  
                                2  |                             0.678       0.565  
                                   |                             -0.93       -0.89  
                                3  |                             0.351       0.129  
                                   |                             -2.00       -1.17  
                                4  |                             0.139       0.000  
                                   |                             -2.40       -0.02  
                                5  |                             0.341       0.119  
                                   |                             -2.13       -1.13  
                                6  |                             0.343       0.159  
                                   |                             -1.88       -1.21  
                                7  |                             0.369       0.203  
                                   |                             -1.68       -1.19  
                                8  |                             0.380       0.223  
                                   |                             -1.75       -1.21  
                                9  |                             0.276       0.089  
                                   |                             -1.68       -0.81  
                               10  |                             0.066       0.000  
                                   |                             -1.46       -0.00  
                               11  |                             0.097       0.000  
                                   |                             -1.57       -0.00  
                               12  |                             0.096       0.000  
                                   |                             -1.49       -0.00  
                               13  |                             0.647       0.605  
                                   |                             -0.68       -0.54  
                               14  |                             0.172       0.006  
                                   |                             -1.59       -0.11  
                               15  |                             0.146       0.000  
                                   |                             -1.38       -0.02  
                               16  |                             0.033       0.000  
                                   |                             -0.76       -0.00  
                               17  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               18  |                             0.075       0.000  
                                   |                             -1.12       -0.00  
                               19  |                             0.500       0.466  
                                   |                             -0.93       -0.66  
                               20  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               21  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               22  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               23  |                             0.249       0.064  
                                   |                             -0.87       -0.31  
                               24  |                             0.047       0.000  
                                   |                             -0.67       -0.00  
                               25  |                             0.201       0.107  
                                   |                             -1.15       -0.56  
                               26  |                             0.342       0.335  
                                   |                             -0.67       -0.47  
                               27  |                             0.434       0.460  
                                   |                             -0.60       -0.38  
                               29  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               30  |                             0.264       0.196  
                                   |                             -0.82       -0.48  
                               31  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               32  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               34  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               35  |                             2.921       4.246  
                                   |                              0.84        1.07  
                               36  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               37  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               38  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               39  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               42  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               45  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               46  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               47  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               48  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               49  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               53  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               55  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               57  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               66  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                               77  |                             0.000       0.000  
                                   |                             -0.00       -0.00  
                                   |                                                
           Gender: 1=female 0=male |                                                
                           Female  |                             1.126       1.387  
                                   |                              0.44        0.56  
                          Constant |                             0.620       0.363  
                                   |                             -1.63       -2.21  
    -------------------------------+------------------------------------------------
    Statistics                     |                                                
                             alpha |                 0.442                          
                                 N |       915         915         915         915  
                                ll | -1651.056   -1560.958   -1588.203   -1545.411  
                               bic |  3343.026    3169.649    3558.266    3479.500  
                               aic |  3314.113    3135.917    3288.406    3204.821  
    --------------------------------------------------------------------------------
                                                                         Legend: b/t

Next, **`countfit`** lists the count for which the deviation between the observed and average predicted probability is the greatest. For the PRM, the biggest problem is the prediction of zero counts, with a difference that is much larger than the maximum for the other models. The average difference between observed and predicted is largest for the PRM (0.026) and smallest for the NORM (0.006) and ZINB (0.008).

    Comparison of Mean Observed and Predicted Count
    
                Maximum       At      Mean
    Model     Difference    Value    |Diff|
    ---------------------------------------------
    PRM         0.091         0      0.026
    NBRM       -0.015         3      0.006
    ZIP         0.055         1      0.014
    ZINB        0.024         1      0.010

This summary information is expanded with detailed comparisons of observed and predicted probabilities for each model.
    
    PRM: Predicted and actual probabilities
    
    Count   Actual    Predicted    |Diff|   Pearson
    ------------------------------------------------
    0        0.301       0.209      0.091    36.489
    1        0.269       0.310      0.041     4.962
    2        0.195       0.242      0.048     8.549
    3        0.092       0.135      0.043    12.483
    4        0.073       0.061      0.012     2.174
    5        0.030       0.025      0.005     0.760
    6        0.019       0.010      0.009     6.883
    7        0.013       0.004      0.009    17.815
    8        0.001       0.002      0.001     0.300
    9        0.002       0.001      0.001     1.550
    ------------------------------------------------
    Sum      0.993       0.999      0.259    91.964
    
    NBRM: Predicted and actual probabilities
    
    Count   Actual    Predicted    |Diff|   Pearson
    ------------------------------------------------
    0        0.301       0.304      0.003     0.028
    1        0.269       0.272      0.003     0.039
    2        0.195       0.180      0.014     1.066
    3        0.092       0.106      0.015     1.818
    4        0.073       0.060      0.013     2.753
    5        0.030       0.033      0.004     0.348
    6        0.019       0.018      0.000     0.004
    7        0.013       0.010      0.003     0.719
    8        0.001       0.006      0.005     3.593
    9        0.002       0.004      0.001     0.456
    ------------------------------------------------
    Sum      0.993       0.993      0.062    10.824
    
    
    ZIP: Predicted and actual probabilities
    
    Count   Actual    Predicted    |Diff|   Pearson
    ------------------------------------------------
    0        0.301       0.303      0.003     0.021
    1        0.269       0.213      0.055    13.177
    2        0.195       0.208      0.013     0.782
    3        0.092       0.141      0.049    15.692
    4        0.073       0.076      0.002     0.067
    5        0.030       0.035      0.005     0.694
    6        0.019       0.014      0.004     1.092
    7        0.013       0.006      0.007     8.536
    8        0.001       0.002      0.001     0.601
    9        0.002       0.001      0.001     1.354
    ------------------------------------------------
    Sum      0.993       0.999      0.142    42.016
    
    ZINB: Predicted and actual probabilities
    
    Count   Actual    Predicted    |Diff|   Pearson
    ------------------------------------------------
    0        0.301       0.319      0.018     0.949
    1        0.269       0.245      0.024     2.170
    2        0.195       0.181      0.014     0.927
    3        0.092       0.113      0.021     3.609
    4        0.073       0.065      0.009     1.057
    5        0.030       0.035      0.006     0.880
    6        0.019       0.019      0.000     0.008
    7        0.013       0.010      0.003     0.770
    8        0.001       0.006      0.004     3.269
    9        0.002       0.003      0.001     0.242
    ------------------------------------------------
    Sum      0.993       0.995      0.100    13.881

The **`|Diff|`** columns show the absolute value of the difference between the observed and predicted counts. A plot of these differences is also provided：


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.4.28.10.png" style="width:650px;height:470px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Finally, **`countfit`** compares the fit of the four models by several standard criteria and tests, including AIC and BIC. For each statistic comparing models, the last three columns indicate which model is preferred.

    
    Tests and Fit Statistics
    
    PRM            BIC=  3343.026  AIC=  3314.113  Prefer  Over  Evidence
    -------------------------------------------------------------------------
      vs NBRM      BIC=  3169.649  dif=   173.377  NBRM    PRM   Very strong
                   AIC=  3135.917  dif=   178.196  NBRM    PRM
                   LRX2=  180.196  prob=    0.000  NBRM    PRM   p=0.000    
    -------------------------------------------------------------------------
      vs ZIP       BIC=  3558.266  dif=  -215.240  PRM     ZIP   Very strong
                   AIC=  3288.406  dif=    25.706  ZIP     PRM
                   Vuong=       .  prob=        .  ZIP     PRM   p=.        
    -------------------------------------------------------------------------
      vs ZINB      BIC=  3479.500  dif=  -136.474  PRM     ZINB  Very strong
                   AIC=  3204.821  dif=   109.291  ZINB    PRM
    -------------------------------------------------------------------------
    NBRM           BIC=  3169.649  AIC=  3135.917  Prefer  Over  Evidence
    -------------------------------------------------------------------------
      vs ZIP       BIC=  3558.266  dif=  -388.617  NBRM    ZIP   Very strong
                   AIC=  3288.406  dif=  -152.490  NBRM    ZIP
    -------------------------------------------------------------------------
      vs ZINB      BIC=  3479.500  dif=  -309.851  NBRM    ZINB  Very strong
                   AIC=  3204.821  dif=   -68.905  NBRM    ZINB
                   Vuong=       .  prob=        .  ZINB    NBRM  p=.        
    -------------------------------------------------------------------------
    ZIP            BIC=  3558.266  AIC=  3288.406  Prefer  Over  Evidence
    -------------------------------------------------------------------------
      vs ZINB      BIC=  3479.500  dif=    78.766  ZINB    ZIP   Very strong
                   AIC=  3204.821  dif=    83.585  ZINB    ZIP
                   LRX2=   85.585  prob=    0.000  ZINB    ZIP   p=0.000    
    -------------------------------------------------------------------------
    
    Vuong test is not appropriate for testing zero-inflation. To force the
    the computation of the test, use option -forcevuong-.


Both the NBRM and the ZINB consistently fit better than either the PRM or the ZIP. This also provides a good example of how BIC penalizes extra parameters more severely than does AIC. The more parsimonious NBRM is preferred over the ZINB according to the BIC statistic but not according to the AIC statistic. The Vuong statistic prefers the ZINB over the NBRM. In terms of fit, there is little to distinguish these two models. If the two-part structure of the ZINB was substantively compelling, we would choose this model. Otherwise, the simplicity of the NBRM would make this the model of choice.

## 8 Conclusion

Count outcomes are not categorical variables in the sense that binary, ordinal, and nominal variables are. Although they are discrete, there is no sense in which the values assigned to a count variable are arbitrary. Indeed, count outcomes support both additive and multiplicative operations, and the number of potential outcome values is not limited.

Even though count variables are thus not categorical, we hope that it is clear how the study of count outcomes can benefit from the same strategies of modeling and interpretation that we introduced in earlier chapters. Instead of only offering interpretations in terms of expected counts, we offered interpretations based on the probabilities of observing a specific count or range of counts. Also, as we showed, the outcomes of 0 can be conceptualized and even modeled as though they are categorically different from the process by which positive counts accumulate. Consequently, as with other types of outcomes in this book, we can learn much by thinking about and testing our ideas about how outcome values are generated. The combination of Stata with the extra tools we provide in this book makes it easy to interpret the relationship between independent variables and count outcomes in ways that are much more effective than vast tables of untransformed coefficients.
