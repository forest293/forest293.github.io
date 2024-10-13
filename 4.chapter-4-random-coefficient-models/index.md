# [Multilevel  Longitudinal Models] Chapter 4 - Random-coefficient models


## 1 Introduction

In the previous chapter, we considered linear random-intercept models where the overall level of the response was allowed to vary between clusters after controlling for covariates. In this chapter, we include random coefficients or random slopes in addition to random intercepts, thus also allowing the effects of covariates to vary between clusters. **Such models involving both random intercepts and random slopes are often called random-coefficient models.** In longitudinal settings, where the level-1 units are occasions and the clusters are typically subjects, models with a random-coefficient of time are also referred to as growth-curve models. Such models are the topic of chapter 7.

## How effective are different schools?

Here we analyze a dataset on inner-London schools that accompanies the MLwiN software (Rasbash et al. 2019) and is part of the data analyzed by Goldstein et al. (1993).

At age 16, students took their Graduate Certificate of Secondary Education (GCSE) exams in a number of subjects. A score was derived from the individual exam results. Such scores often form the basis for school comparisons, for instance, to allow parents to choose the best school for their child. However, schools can differ considerably in their intake achievement levels. It may be argued that what should be compared is the “value added”; that is, the difference in mean GCSE score between schools after controlling for the students’ achievement before entering the school. One such measure of prior achievement is the London Reading Test (LRT) taken by these students at age 11.

The dataset gcse.dta has the following variables:

- school: school identifier
- student: student identifier
- gcse: Graduate Certificate of Secondary Education (GCSE) score (score, multiplied by 10)
- lrt: London Reading Test (LRT) score ( score, multiplied by 10)
- girl: dummy variable for student being a girl (1: girl; 0: boy)
- schgend: type of school (1: mixed gender; 2: boys only; 3: girls only)

One purpose of the analysis is to **investigate the relationship between GCSE and LRT and how this relationship varies between schools.** The model can then be used to address the question of **which schools appear to be most effective, taking prior achievement into account**.

## 3 Separate linear regressions for each school

Before developing a model for all 65 schools combined, we consider a separate model for each school. For each school, an obvious model for the relationship between GCSE and LRT is a simple regression model.

$$y_{ij} = \beta_{1j}+\beta_{2j}x_{ij}+\epsilon_{ij}$$

where $y_{ij}$ is the GCSE score for the _th student in school _j, $x_{ij}$ is the corresponding LRT score, $\beta_{1 j}$ is the school-specific intercept, $\beta_{2 j}$ is the school-specific slope, and $\varepsilon_{i j}$ is a residual error term with school-specific variance $\theta_j$. 

For school 1, OLS estimates of the intercept $\hat{\beta}_{11}$ and the slope $\hat{\beta}_{21}$ can be obtained using `regress` ,

```
regress gcse lrt if school==1
```
    
          Source |       SS           df       MS      Number of obs   =        73
    -------------+----------------------------------   F(1, 71)        =     59.44
           Model |  4084.89189         1  4084.89189   Prob > F        =    0.0000
        Residual |  4879.35759        71  68.7233463   R-squared       =    0.4557
    -------------+----------------------------------   Adj R-squared   =    0.4480
           Total |  8964.24948        72  124.503465   Root MSE        =      8.29
    
    ------------------------------------------------------------------------------
            gcse | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             lrt |       0.71       0.09     7.71   0.000         0.53        0.89
           _cons |       3.83       0.98     3.90   0.000         1.87        5.79
    ------------------------------------------------------------------------------

where we have selected school 1 by specifying the condition `if school==1`.

To assess whether this is a reasonable model for school 1, we can obtain the predicted (ordinary least squares) regression line for this school (with  $j = 1$  ).

$$\widehat y_{i1}=\widehat \beta_{11}+\widehat \beta_{21}x_{i1}$$

by using the **`predict`** command with the **`xb`** option:

```
predict p_gcse, xb 
```

We superimpose this line on the scatterplot of the data for the school, as shown in figure 4.1.

```
twoway (scatter gcse lrt) (line p_gcse lrt, sort) if school==1, ///
  xtitle(LRT) ytitle(GCSE)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.4.png" style="width:540px;height:330px;" alt="图片描述">
  <figcaption><strong>Figure 4.1: Scatterplot of gcse versus lrt for school 1 with ordinary least-squares regression line</strong></figcaption>
</figure>

We can also produce a *trellis graph* containing such plots for all 65 schools by using

```
twoway (scatter gcse lrt) (lfit gcse lrt, sort lpatt(solid)), ///
  by(school, compact legend(off) cols(5)) ///
  xtitle(LRT) ytitle(GCSE) ysize(3) xsize(2)
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.5.png" style="width:540px;height:730px;" alt="图片描述">
  <figcaption><strong>Figure 4.2: Trellis of scatterplots of gcse versus lrt with fitted regression lines for all 65 schools</strong></figcaption>
</figure>

We will now fit a simple linear regression model for each school, which is easily done using Stata’s prefix command **``statsby``**. Then we will examine the variability in the estimated intercepts and slopes.

First, calculate the number of students per school by using **``egen``** with the **``count()``** function to preclude fitting lines to schools with fewer than five students below:

```
egen num = count(gcse), by(school)
```

Then, use **``statsby``** to create a new dataset, **``ols.dta``**, in the working directory with the variables **``inter``** and **``slope``** containing OLS estimates of the intercepts (**``_b[_cons]``**) and slopes (**``_b[lrt]``**) from the command **``regress gcse lrt if num>4``** applied to each school:

```
statsby inter=_b[_cons] slope=_b[lrt], by(school) saving(ols): ///
  regress gcse lrt if num>4 
```
    
    (running regress on estimation sample)
    
          Command: regress gcse lrt if num>4
            inter: _b[_cons]
            slope: _b[lrt]
               By: school
    
    Statsby groups:
    ..................................................    50
    ..............

The new dataset also contains the variable **``school``** and is sorted by **``school``**, making it easy to merge it into the original dataset (the “master data”) after sorting the latter by **``school``**:

```
sort school
merge m:1 school using ols
drop _merge
```
    
        Result                      Number of obs
        -----------------------------------------
        Not matched                             2
            from master                         2  (_merge==1)
            from using                          0  (_merge==2)
    
        Matched                             4,057  (_merge==3)
        -----------------------------------------

**Here we have specified **``m:1``** in the **``merge``** command, which stands for “many-to-one merging” (observations for several students per school in the master data, but only one observation per school in the “using data”).** We see that two of the students in the master data did not have matches in the using data (because their school, school 48, had fewer than 5 students in the data, so we did not compute OLS estimates for that school). **We have deleted the variable **``_merge``** produced by the **``merge``** command to avoid error messages when we run the **``merge``** command in the future.**

A scatterplot of the OLS estimates of the intercept and slope is produced using the following command and given in figure 4.3:

```
twoway scatter slope inter, xtitle(Intercept) ytitle(Slope)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.6.png" style="width:540px;height:330px;" alt="图片描述">
  <figcaption><strong>Figure 4.3: Scatterplot of estimated intercepts and slopes for all schools with at least five students</strong></figcaption>
</figure>

We see that there is considerable variability between the estimated intercepts and slopes of different schools. To investigate this further, we first create a dummy variable to pick out one observation per school.

```
egen pickone = tag(school)
```

and then produce summary statistics for the schools by using the **``summarize``** command:

```
summarize inter slope if pickone==1
```
    
    
        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
           inter |         64   -.1805974    3.291357  -8.519253   6.838716
           slope |         64    .5390514    .1766135   .0380965   1.076979

To allow comparison with the parameter estimates obtained from the random-coefficient model considered later on, we also obtain the covariance matrix of the estimated intercepts and slopes:

```
correlate inter slope if pickone==1, covariance
```
        
                     |    inter    slope
        -------------+------------------
               inter |   10.833
               slope |  .208622  .031192

The diagonal elements are the sample variances of the intercepts and slopes, respectively. The off-diagonal element is the sample covariance between the intercepts and slopes, equal to the correlation times the product of the intercept and slope standard deviations.

We can also obtain a spaghetti plot of the predicted school-specific regression lines for all schools. We first calculate the fitted values $\hat{y}_{i j}=\hat{\beta}_{1 j}+\hat{\beta}_{2 j} x_{i j}$,

```
generate pred = inter + slope*lrt
```

and sort the data so that `lrt` increases within a given school and then jumps to its lowest value for the next school in the dataset:

```
sort school lrt
```

We then produce the plot by typing

```
twoway (line pred lrt, connect(ascending)), xtitle(LRT) ///
   ytitle(Fitted regression lines)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.7.png" style="width:540px;height:400px;" alt="图片描述">
  <figcaption><strong>Figure 4.4: Spaghetti plot of ordinary least-squares regression lines for all schools with at least five students</strong></figcaption>
</figure>

## 4 Specification and interpretation of a random-coefficient model

### 4.1 pecification of a random-coefficient model

How can we develop a joint model for the relationships between gcse and lrt in all schools that allows intercepts and slopes to differ between schools?

One way would be to use dummy variables for all schools (omitting the overall intercept) and interactions between these dummy variables and lrt (omitting the overall slope of lrt). The school-specific intercepts are then the coefficients of the dummy variables and the school-specific slopes are the interaction coefficients. The only difference between the resulting model and separate regressions is that a common residual error variance $\theta_j=\theta$ is assumed. However, this model has 130 regression coefficients! Furthermore, if the schools are viewed as a (random) sample of schools from a population of schools, we are not interested in the individual coefficients characterizing each school's regression line. Rather, we would like to estimate the mean intercept and slope as well as the (co)variability of the intercepts and slopes in the population of schools.

A parsimonious model for the relationships between gcse and lrt can be obtained by specifying a school-specific random intercept $\zeta_{1j}$ and a school-specific random slope $\zeta_{2j}$ for lrt $(x_{ij})$:


$$\begin{aligned}y_{ij}&=\quad\beta_1+\beta_2x_{ij}+\zeta_{1j}+\zeta_{2j}x_{ij}+\epsilon_{ij}\\&=\quad(\beta_1+\zeta_{1j})+(\beta_2+\zeta_{2j})x_{ij}+\epsilon_{ij}\end{aligned}$$

Here $\zeta_{1j}$ represents the deviation of school $j$ 's intercept from the mean intercept $\beta_1$, and $\zeta_{2j}$ represents the deviation of school $j$ 's slope from the mean slope $\beta_2$.  

Given all covariates $X_j$ in cluster $j$, it is assumed that the random effects $\zeta_{1j}$ and $\zeta_{2j}$ have zero expectations:

$$E(\zeta_{1j}|\mathbf{X}_j)=0$$

$$E(\zeta_{2j}|\mathbf{X}_j)=0$$

It is also assumed that the level-1 residual $\varepsilon_{i j}$ has zero expectation, given the covariates and the random effects:

$$E(\epsilon_{ij}|\mathbf X_j,\zeta_{1j},\zeta_{2j})=0$$

It follows from these mean-independence assumptions that the random terms $\zeta_{1 j}, \zeta_{2 j},$ and $\varepsilon_{i j}$ are all uncorrelated with the covariate $x_{i j}$ and with $\overline x_{\cdot j}$ and that $\varepsilon_{i j}$ is uncorrelated with both $\zeta_{1 j}$ and $\zeta_{2 j}$. Both the intercepts $\zeta_{1 j}$ and slopes $\zeta_{2 j}$ are assumed to be uncorrelated across schools, and the level-1 residuals $\varepsilon_{i j}$ are assumed to be uncorrelated across schools and students.

An illustration of this random-coefficient model with one covariate $x_{i j}$ for one cluster $j$ is shown in the bottom panel of figure 4.5. A random-intercept model is shown for comparison in the top panel.



#### 问题背景

我们想要研究GCSE（英国普通中等教育证书）成绩和LRT（可能是某种学习资源或时间）之间的关系，并考虑这种关系在不同学校之间可能存在差异。
#### 初步方法

1. **使用哑变量**：我们可以为每个学校创建一个哑变量（除了总体截距），并考虑这些哑变量与LRT的交互作用（除了LRT的总体斜率）。这样，每个学校的特定截距就是哑变量的系数，特定斜率就是交互作用的系数。
   **优点**：这种方法可以为每个学校提供一个单独的模型。
   **缺点**：如果学校数量很多（比如130个），那么模型会有130个回归系数，这会导致模型过于复杂。

2. **考虑学校作为样本**：如果我们把学校看作是从学校总体中随机抽取的样本，那么我们可能更关心的是学校总体中的平均截距和斜率，以及截距和斜率的变异性。

#### 解决方案：混合效应模型

为了解决上述问题，我们可以使用混合效应模型（也称为多层次模型或随机效应模型）。这种模型允许我们同时估计总体的平均截距和斜率，以及截距和斜率在不同学校之间的变异性。

#### 模型公式
模型可以表示为：
$$ y_{ij} = \beta_1 + \beta_2x_{ij} + \zeta_{1j} + \zeta_{2j}x_{ij} + \epsilon_{ij} $$
这里：
- $ y_{ij} $ 是学生 $ i $ 在学校 $ j $ 的GCSE成绩。
- $ x_{ij} $ 是学生 $ i $ 在学校 $ j $ 的LRT。
- $ \beta_1 $ 是所有学校的平均截距。
- $ \beta_2 $ 是所有学校的平均斜率。
- $ \zeta_{1j} $ 是学校 $ j $ 的截距的随机效应，表示学校 $ j $ 的截距与总体平均截距 $ \beta_1 $ 的偏差。
- $ \zeta_{2j} $ 是学校 $ j $ 的斜率的随机效应，表示学校 $ j $ 的斜率与总体平均斜率 $ \beta_2 $ 的偏差。
- $ \epsilon_{ij} $ 是随机误差项，表示模型未能解释的变异。

#### 模型的假设

1. **随机效应的期望为零**：
$$ E(\zeta_{1j}|\mathbf X_j) = 0 $$
$$ E(\zeta_{2j}|\mathbf X_j) = 0 $$
这意味着随机效应 $ \zeta_{1j} $ 和 $ \zeta_{2j} $ 在给定学校 $ j $ 的所有协变量 $ \mathbf{X}_j $ 的条件下，期望值为零。
2. **残差的期望为零**：
$$ E(\epsilon_{ij}|\mathbf X_j, \zeta_{1j}, \zeta_{2j}) = 0 $$
这意味着在给定学校 $ j $ 的所有协变量 $ \mathbf X_j $，以及随机效应 $ \zeta_{1j} $ 和 $ \zeta_{2j} $ 的条件下，残差 $ \epsilon_{ij} $ 的期望值为零。
3. **无相关性**：
- 随机效应 $ \zeta_{1j} $，$ \zeta_{2j} $，和 $ \epsilon_{ij} $ 与协变量 $ x_{ij} $ 以及 $ \overline x_{\cdot j} $ 无相关性。
- $ \epsilon_{ij} $ 与 $ \zeta_{1j} $ 和 $ \zeta_{2j} $ 无相关性。
- 截距 $ \zeta_{1j} $ 和斜率 $ \zeta_{2j} $ 在不同学校之间无相关性。
- 残差 $ \epsilon_{ij} $ 在不同学校和学生之间无相关性。

#### 例子

假设我们有3所学校，每所学校有5名学生。我们想要研究LRT对学生GCSE成绩的影响，并考虑这种影响在不同学校之间可能存在差异。
1. **数据结构**：
   | 学校 | 学生 | LRT | GCSE |
   |------|------|-----|------|
   | 1    | 1    | 1   | 70   |
   | 1    | 2    | 2   | 75   |
   | 1    | 3    | 3   | 80   |
   | 1    | 4    | 4   | 85   |
   | 1    | 5    | 5   | 90   |
   | 2    | 1    | 1   | 60   |
   | 2    | 2    | 2   | 65   |
   | ...  | ...  | ... | ...  |
2. **模型应用**：
   对于学校1，我们有5个数据点，可以估计出学校1的平均截距 $ \beta_1 + \zeta_{1j} $ 和平均斜率 $ \beta_2 + \zeta_{2j} $。
   对于学校2和学校3，我们也可以分别估计它们的平均截距和平均斜率。
3. **模型估计**：
   通过混合效应模型，我们可以估计出所有学校的平均截距 $ \beta_1 $ 和平均斜率 $ \beta_2 $，以及截距和斜率在不同学校之间的变异性。

#### 总结
通过使用混合效应模型，我们可以在考虑学校间差异的同时，估计出GCSE成绩和LRT之间的关系。这种方法既考虑了学校间的异质性，又能够估计出总体的平均效应，是一种既灵活又有效的方法。

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.8.png" style="width:470px;height:680px;" alt="图片描述">
  <figcaption><strong>Figure 4.5: Illustration of random-intercept and random-coefficient models</strong></figcaption>
</figure>

In each panel, the lower bold and solid line represents the population-averaged or marginal regression line across all clusters. The higher and thinner solid line represents the cluster-specific regression line for cluster $j$. The arrows from the cluster-specific regression lines to the responses $y_{ij}$ are the within-cluster residual error terms $\varepsilon_{ij}$ (with variance $\theta$).

$$E(y_{ij}|x_{ij})=\beta_1+\beta_2x_{ij}$$

For the random-intercept model, the cluster-specific line is

$$E(y_{ij}|x_{ij},\zeta_{1j})=(\beta_1+\zeta_{1j})+\beta_2x_{ij}$$

which is parallel to the population-averaged line with vertical displacement given by the random intercept $\zeta_{1j}$. In contrast, in the random-coefficient model, the cluster-specific or conditional regression line

$$E(y_{ij}|x_{ij},\zeta_{1j},\zeta_{2j})=(\beta_1+\zeta_{1j})+(\beta_2+\zeta_{2j})x_{ij}$$

is not parallel to the population-averaged line but has a greater slope because the random slope $\zeta_{2 j}$ is positive in the illustration. Here the dashed line is parallel to the population-averaged regression line and has the same intercept as cluster $j$. The vertical deviation between this dashed line and the line for cluster $j$ is $\zeta_{2 j} x_{i j},$ as shown in the diagram for \(x_{i j}=1\). The bottom panel illustrates that the total intercept for cluster $j$ is $\beta_{1}+\zeta_{1 j}$ and the total slope is $\beta_{2}+\zeta_{2 j}$. It is clear that \(\zeta_{2 j} x_{i j}\) represents an interaction between the clusters, treated as random, and the covariate \(x_{i j} .\)

Given X$_{j},$ the random intercept and random slope have a bivariate distribution assumed to have 0 means and covariance matrix $\Psi:

<!--
$$\boldsymbol{\Psi}=\left[\begin{array}{cc}\psi_{11}&\psi_{12}\\\psi_{21}&\psi_{22}\end{array}\right]\equiv\left[\begin{array}{cc}\mathrm{Var}(\zeta_{1j}|\mathbf X_j)&\mathrm{Cov}(\zeta_{1j},\zeta_{2j}|\mathbf X_j)\\\mathrm{Cov}(\zeta_{2j},\zeta_{1j}|\mathbf X_j)&\mathrm{Var}(\zeta_{2j}|\mathbf X_j)\end{array}\right],\quad\psi_{21}=\psi_{12}$$
-->


![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.9.png)

Hence, given the covariates, the variance of the random intercept is $\psi_{11}$, the variance of the random slope is $\psi_{22}$, and the covariance between the random intercept and the random slope is $\psi_{21}$. The correlation between the random intercept and random slope given the covariates becomes

<!--
$$\rho_{21}\equiv\mathrm{Cor}(\zeta_{1j},\zeta_{2j}|\mathbf{X}_j) = \frac{\psi_{21}}{\sqrt{\psi_{11}\psi_{22}}}$$
-->

![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.10.png)

For maximum likelihood (ML) and restricted maximum likelihood (REML) estimation a normal distribution is specified for the level-1 error $\boldsymbol \epsilon_{i j}$ and a bivariate normal distribution for the random intercept and random slope, given $X_{j}$. An example of a bivariate normal distribution with $\psi_{11}=\psi_{22}=4 \text { and } \psi_{21}=\psi_{12}=1$ is shown as a perspective plot in figure 4.6. Specifying a bivariate normal distribution implies that the (marginal) univariate distributions of the intercept and slope are also normal.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.11.png" style="width:500px;height:350px;" alt="图片描述">
  <figcaption><strong>Figure 4.6: Perspective plot of bivariate normal distribution</strong></figcaption>
</figure>



1. **基本概念**
首先，我们有一个数据集，其中包含了多个集群（clusters），每个集群有自己的数据点。我们想要研究某个变量 $ y $ 如何随着另一个变量 $ x $ 的变化而变化。在统计学中，我们通常使用回归线来描述这种关系。

2. **回归线**
- **总体回归线**：这是不考虑集群差异的情况下，所有数据点的平均回归线。
- 它的方程是 $ E(y_{ij}|x_{ij}) = \beta_1 + \beta_2x_{ij} $，其中 $ \beta_1 $ 是截距，$ \beta_2 $ 是斜率。

3. **集群特定的回归线**
- **随机截距模型**：在这个模型中，每个集群都有自己的回归线，但是这些线都是平行的，只是垂直位移不同。方程是 $ E(y_{ij}|x_{ij},\zeta_{1j}) = (\beta_1 + \zeta_{1j}) + \beta_2x_{ij} $，其中 $ \zeta_{1j} $ 是随机截距，表示每个集群的垂直位移。
- **随机斜率模型**：在这个模型中，每个集群的回归线不仅垂直位移不同，而且斜率也不同。方程是 $ E(y_{ij}|x_{ij},\zeta_{1j},\zeta_{2j}) = (\beta_1 + \zeta_{1j}) + (\beta_2 + \zeta_{2j})x_{ij} $，其中 $ \zeta_{2j} $ 是随机斜率，表示每个集群的斜率变化。

4. **随机效应的分布**

- **协方差矩阵 $ \boldsymbol{\Psi} $ 详细解释**

![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.12.png)

协方差矩阵 $ \boldsymbol{\Psi} $ 是一个2x2的矩阵，它用来描述两个随机变量之间的关系。在我们的例子中，这两个随机变量是随机截距 $ \zeta_{1j} $ 和随机斜率 $ \zeta_{2j} $。

- **矩阵的元素：**

- **$ \psi_{11} $**：这是随机截距 $ \zeta_{1j} $ 的方差。方差衡量的是随机截距在不同集群中的分散程度。如果 $ \psi_{11} $ 较大，意味着不同集群的截距差异较大。
- **$ \psi_{22} $**：这是随机斜率 $ \zeta_{2j} $ 的方差。同样，方差衡量的是随机斜率在不同集群中的分散程度。如果 $ \psi_{22} $ 较大，意味着不同集群的斜率差异较大。
- **$ \psi_{12} $ 和 $ \psi_{21} $**：这两个元素是协方差，衡量的是随机截距和随机斜率之间的线性关系。协方差的值可以是正的或负的：
  - 如果 $ \psi_{12} $（或 $ \psi_{21} $）是正数，意味着当一个集群的截距增加时，斜率也倾向于增加，它们之间存在正相关。
  - 如果 $ \psi_{12} $（或 $ \psi_{21} $）是负数，意味着当一个集群的截距增加时，斜率倾向于减少，它们之间存在负相关。
  - 如果 $ \psi_{12} $（或 $ \psi_{21} $）接近0，意味着截距和斜率的变化是相互独立的。

- **为什么协方差矩阵是对称的？**

因为协方差是度量两个变量之间线性关系的方式，所以 $ \zeta_{1j} $ 对 $ \zeta_{2j} $ 的影响和 $ \zeta_{2j} $ 对 $ \zeta_{1j} $ 的影响是相同的。这就是为什么 $ \psi_{12} $ 等于 $ \psi_{21} $。

- **相关系数 $ \rho_{21} $ 详细解释**
相关系数 $ \rho_{21} $ 是一个无量纲的值，用来衡量两个变量之间的线性关系强度。它的值介于 -1 和 1 之间。

- **计算方法：**
$$
\rho_{21} = \frac{\psi_{21}}{\sqrt{\psi_{11} \psi_{22}}}
$$
- **分子** $ \psi_{21} $：这是随机截距和随机斜率之间的协方差，它直接衡量了两个变量之间的线性关系。
- **分母** $ \sqrt{\psi_{11} \psi_{22}} $：这是随机截距的方差和随机斜率的方差的平方根的乘积。它实际上是两个变量的标准差的乘积。这个值的作用是将协方差标准化，确保相关系数不受变量单位或量级的影响。

- **相关系数的解释：**
- **$ \rho_{21} = 1 $**：完全正相关。当一个变量增加时，另一个变量也完全按照固定比例增加。
- **$ \rho_{21} = -1 $**：完全负相关。当一个变量增加时，另一个变量完全按照固定比例减少。
- **$ \rho_{21} = 0 $**：没有线性相关。变量的变化是相互独立的。

- **实际例子**
假设我们有以下协方差矩阵：

![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.13.png)
<!--
$$
\boldsymbol{\Psi} = \begin{bmatrix}
4 & 1 \\
1 & 4
\end{bmatrix}
$$
-->
- **计算相关系数**：
$$
\rho_{21} = \frac{1}{\sqrt{4 \times 4}} = \frac{1}{4} = 0.25
$$
这意味着随机截距和随机斜率之间存在较弱的正相关。

- **结论**
通过协方差矩阵和相关系数，我们可以量化随机截距和随机斜率之间的关系。这有助于我们理解不同集群的回归线如何相对于总体平均线变化，以及它们是如何相互依赖的。

5. **最大似然估计**
- **误差项**：在最大似然估计（MLE）和限制最大似然估计（REML）中，我们假设第一层误差 $ \boldsymbol{\epsilon_{ij}} $ 是正态分布的，随机截距和随机斜率给定 $ X_j $ 也是二元正态分布的。

6. **二元正态分布的例子**
- **二元正态分布图**：如果我们有一个二元正态分布，其中 $ \psi_{11} = \psi_{22} = 4 $ 并且 $ \psi_{21} = \psi_{12} = 1 $，那么这个分布可以被可视化为一个透视图。


### 4.2 Interpretation of the random-effects variances and covariances

Interpreting the covariance matrix $\Psi$ of the random effects (given the covariates $X_j$) is not completely straightforward.  

First, the random-slope variance $\psi_{22}$ and the covariance between random slope and intercept $\psi_{21}$ depend not just on the scale of the response variable but also on the scale of the covariate, here lrt. Let the units of the response and covariate be denoted as $u_y$ and $u_x$, respectively. For instance, in an application in chapter 7 that considers children's increase in weight over time, $u_y$ is kilograms and $u_x$ is years. The units of $\psi_{11}$ are $u_y^2$, the units of $\psi_{21}$ are $u_y^2/u_x$, and the units of $\psi_{22}$ are $u_y^2/u_x^2$. It therefore does not make sense to compare the magnitude of random-intercept and random-slope variances.

Another issue is that the total residual variance is no longer constant as in random-intercept models. The total residual is now

$$\xi_{ij} \equiv \zeta_{1j}+\zeta_{2j}x_{ij}+\epsilon_{ij}$$

and the conditional variance of the responses given the covariate, or the conditional variance of the total residual, is

$$\mathrm Var(y_{ij}|\mathbf X_j) = \mathrm Var(\xi_{ij}|\mathbf X_j) = \psi_{11}+2\psi_{21}x_{ij}+\psi_{22}x_{ij}^2+\theta\quad(4.2)$$

This variance is a (quadratic) function of the covariate $x_{ij}$, and the total residual is therefore heteroskedastic. The conditional covariance for two students $i$ and $i'$ with covariate values $x_{ij}$ and $x_{i'j}$ in the same school $j$ is


$$\mathrm Cov(y_{ij},y_{i^{\prime}j}|\mathbf X_j)\quad=\quad\mathrm Cov(\xi_{ij},\xi_{i^{\prime}j}|\mathbf X_j)$$

$$=\quad\psi_{11}+\psi_{21}x_{ij}+\psi_{21}x_{i'j}+\psi_{22}x_{ij}x_{i'j}$$

and the conditional intraclass correlation becomes

$$\mathrm Cor(y_{ij},y_{i^{\prime}j}|\mathbf X_j) = \frac{\mathrm Cov(\xi_{ij},\xi_{i^{\prime}j}|\mathbf X_j)}{\sqrt{\mathrm Var(\xi_{ij}|\mathbf X_j)\mathrm Var(\xi_{i^{\prime}j}|\mathbf X_j)}}$$

where we can plug in the covariance from (4.3) and the variances from (4.2). When $x_{ij}=x_{i'j}=0$, the expression for the intraclass correlation is the same as for the random-intercept model and represents the correlation of the total residuals (from the overall mean regression line) for two students in the same school who both have lrt scores equal to 0 (the mean in this case). However, for pairs of students $i$ and $i'$ in the same school $j$ with other values of lrt, the intraclass correlation is a complicated function of `lrt` ($x_{ij}$ and $x_{i'j}$).

Due to the heteroskedastic total residual variance, it is not straightforward to define coefficients of determination—such as $R^2$, $R^2_2$, and $R^2_Y$, discussed in section 3.5—for random-coefficient models. Snijders and Bosker (2012, 114) suggest removing the random coefficient(s) for the purpose of calculating the coefficient of determination because this will usually yield values that are close to correct (see their section 7.2.2 for how to obtain the correct version).

Finally, interpreting the parameters $\psi_{11}$ and $\psi_{21}$ can be difficult because their values depend on the translation of the covariate or, in other

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.14.png" style="width:500px;height:650px;" alt="图片描述">
  <figcaption><strong>Figure 4.7: Cluster-specific regression lines for random-coefficient model, illustrating lack of invariance under translation of covariate (Source: Skrondal and Rabe-Hesketh 2004)</strong></figcaption>
</figure>

To make $\psi_{11}$ and $\psi_{21}$ interpretable, it makes sense to translate $x_{ij}$ so that the value $x_{ij} = 0$ is a useful reference point in some way. Typical choices are either mean centering (as for lrt) or, if $x_{ij}$ is time, as in growth-curve models, defining 0 to be the initial time in some sense. Because the magnitude and interpretation of $\psi_{21}$ depend on the location (or translation) of $x_{ij}$, which is often arbitrary, it generally does not make sense to set $\psi_{21}$ to 0 by specifying uncorrelated intercepts and slopes.

A useful way of interpreting the magnitudes of the estimated variances $\hat \psi_{11}$ and $\hat \psi_{22}$ is by constructing intervals that contain the intercepts and slopes of 95% of clusters in the population (treating estimates as known parameters). Assuming that the intercepts and slopes are normally distributed with means $\hat \beta_1$ and $\hat \beta_2$ and variances $\hat\psi_{11}$ and $\hat \psi_{22}$, these intervals are $\hat \beta_1 \pm 1.96\sqrt{\hat \psi_{11}}$ and $\hat \beta_2 \pm 1.96\sqrt{\hat \psi_{22}}$. To aid interpretation of the random part of the model, it is also useful to produce plots of predicted school-specific regression lines, as discussed in section 4.8.3.


#### 第一部分：理解协方差矩阵 $ \Psi $ 的元素
##### 4.2.1 协方差矩阵 $ \Psi $ 的元素

在随机效应模型中，我们通常有随机截距 $ \zeta_{1j} $ 和随机斜率 $ \zeta_{2j} $，它们的关系可以通过协方差矩阵 $ \Psi $ 来描述：

![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.15.png)
<!--
$$
\Psi = \begin{bmatrix}
\psi_{11} & \psi_{12} \\
\psi_{21} & \psi_{22}
\end{bmatrix}
$$
-->

- **$ \psi_{11} $**：随机截距的方差，表示不同集群截距的变异程度。
- **$ \psi_{22} $**：随机斜率的方差，表示不同集群斜率的变异程度。
- **$ \psi_{12} $ 和 $ \psi_{21} $**：随机截距和随机斜率之间的协方差，表示它们之间的线性关系。

##### 4.2.2 单位的影响
这些参数的单位取决于响应变量和协变量的单位。例如，如果响应变量是孩子的体重（单位为千克），协变量是时间（单位为年），那么：
- **$ \psi_{11} $** 的单位是 $ \text{千克}^2 $。
- **$ \psi_{21} $** 和 **$ \psi_{12} $** 的单位是 $ \text{千克}^2 / \text{年} $。
- **$ \psi_{22} $** 的单位是 $ \text{千克}^2 / \text{年}^2 $。
这意味着你不能直接比较随机截距和随机斜率的方差，因为它们的单位可能不同。

#### 第二部分：总残差和条件方差
##### 4.2.3 总残差

在随机斜率模型中，总残差 $ \xi_{ij} $ 现在包括随机截距、随机斜率和随机误差：

$$
\xi_{ij} \equiv \zeta_{1j} + \zeta_{2j}x_{ij} + \epsilon_{ij}
$$

##### 4.2.4 条件方差

条件方差公式是这样的：
$$
\mathrm Var(y_{ij}|\mathbf X_j) = \mathrm Var(\xi_{ij}|\mathbf X_j) = \psi_{11} + 2\psi_{21}x_{ij} + \psi_{22}x_{ij}^2 + \theta
$$
这个公式描述了在给定协变量 $\mathbf X_j$ 的情况下，响应变量 $y_{ij}$ 的方差。这里 $\xi_{ij}$ 是总残差，包括随机截距、随机斜率和随机误差。
- **$\psi_{11}$**：是随机截距的方差，无论 $x_{ij}$ 的值是多少，它都是方差的一部分。
- **$2\psi_{21}x_{ij}$**：是与协变量 $x_{ij}$ 相乘的项，表示随机斜率与随机截距的协方差对总方差的贡献。因为协方差 $\psi_{21}$ 考虑了截距和斜率之间的关系，所以当 $x_{ij}$ 增加时，这种关系对总方差的贡献也会增加。
- **$\psi_{22}x_{ij}^2$**：是随机斜率方差部分，随着 $x_{ij}$ 的平方增加而增加，表示如果 $x_{ij}$ 很大，那么响应变量 $y_{ij}$ 的变异也会很大。
- **$\theta$**：是固定效应的方差，它表示了模型中未被随机效应解释的变异部分。

- **例子：**
假设我们研究的是孩子们的体重增长，其中：
- $ y_{ij} $ 是孩子的体重，
- $ x_{ij} $ 是时间（年），
并且我们知道：
- $ \psi_{11} = 4 $（随机截距的方差），
- $ \psi_{21} = 1 $（随机截距和斜率之间的协方差），
- $ \psi_{22} = 2 $（随机斜率的方差），
- $ \theta = 1 $（固定效应的方差）。
如果一个孩子随时间的协变量 $ x_{ij} = 3 $ 年，那么这个孩子体重的预期方差将是：
$$
\mathrm Var(y_{ij}|\mathbf X_j) = 4 + 2 \times 1 \times 3 + 2 \times 3^2 + 1 = 4 + 6 + 18 + 1 = 29
$$
这意味着，给定3年的时间，这个孩子体重的预期方差是29千克的平方。

##### 4.2.5 条件协方差
条件协方差公式是这样的：

$$
\mathrm Cov(y_{ij},y_{i'j}|\mathbf X_j) = \mathrm Cov(\xi_{ij},\xi_{i'j}|\mathbf X_j) = \psi_{11} + \psi_{21}x_{ij} + \psi_{21}x_{i'j} + \psi_{22}x_{ij}x_{i'j}
$$

这个公式描述了在给定协变量 $\mathbf X_j$ 的情况下，两个响应变量 $y_{ij}$ 和 $y_{i'j}$ 之间的协方差。
- **$\psi_{11}$**：与上面相同，表示随机截距的方差。
- **$\psi_{21}x_{ij}$** 和 **$\psi_{21}x_{i'j}$**：分别表示 $y_{ij}$ 和 $y_{i'j}$ 与随机斜率的关系。这两项表示随机斜率对协方差的贡献，与每个观测值的 $x$ 值有关。
- **$\psi_{22}x_{ij}x_{i'j}$**：表示随机斜率方差对协方差的贡献，与两个观测值的 $x$ 值的乘积有关。

- **例子：**
假设两个孩子 $ i $ 和 $ i' $ 在同一个班级，他们的时间协变量分别是 $ x_{ij} = 3 $ 年和 $ x_{i'j} = 5 $ 年。那么他们体重增长的预期协方差是：
$$
\mathrm Cov(y_{ij},y_{i'j}|\mathbf X_j) = 4 + 1 \times 3 + 1 \times 5 + 2 \times 3 \times 5 = 4 + 3 + 5 + 30 = 42
$$
这意味着，这两个孩子的体重增长之间的预期协方差是42千克的平方。

##### 4.2.6 条件内类相关
条件内类相关公式是这样的：
$$
\mathrm Cor(y_{ij},y_{i'j}|\mathbf X_j) = \frac{\mathrm Cov(\xi_{ij},\xi_{i'j}|\mathbf X_j)}{\sqrt{\mathrm Var(\xi_{ij}|\mathbf X_j)\mathrm Var(\xi_{i'j}|\mathbf X_j)}}
$$
这个公式描述了在给定协变量 $\mathbf X_j$ 的情况下，两个响应变量 $y_{ij}$ 和 $y_{i'j}$ 之间的相关性。
- **分子**：是两个观测值的协方差，表示它们之间的线性关系。
- **分母**：是两个观测值的方差的乘积的平方根，用于标准化协方差，使其成为一个相关系数。

当 $ x_{ij} = x_{i'j} = 0 $ 时，内类相关与随机截距模型相同，表示两个在同一学校且 lrt 得分为 0（平均值）的学生的总残差的相关性。


- **例子：**
使用之前的例子，两个孩子 $i$ 和 $i'$ 在同一个班级，他们的时间协变量分别是 $x_{ij} = 3$ 年和 $x_{i'j} = 5$ 年。

我们计算他们的体重增长的预期协方差和方差：
- **协方差** $\mathrm Cov(y_{ij},y_{i'j}|\mathbf X_j)$：
$$
\mathrm Cov(y_{ij},y_{i'j}|\mathbf X_j) = \psi_{11} + \psi_{21}x_{ij} + \psi_{21}x_{i'j} + \psi_{22}x_{ij}x_{i'j}
$$

$$
= 4 + 1 \times 3 + 1 \times 5 + 2 \times 3 \times 5 = 4 + 3 + 5 + 30 = 42
$$
- **方差** $\mathrm Var(y_{ij}|\mathbf X_j)$ 和 $\mathrm Var(y_{i'j}|\mathbf X_j)$：

$$
\mathrm Var(y_{ij}|\mathbf{X}_j) = 4 + 2 \times 1 \times 3 + 2 \times 3^2 + 1 = 4 + 6 + 18 + 1 = 29
$$

$$
\mathrm Var(y_{i'j}|\mathbf{X}_j) = 4 + 2 \times 1 \times 5 + 2 \times 5^2 + 1 = 4 + 10 + 50 + 1 = 65
$$

现在，我们可以计算内类相关：

$$
\mathrm Cor(y_{ij},y_{i'j}|\mathbf X_j) = \frac{42}{\sqrt{29} \times \sqrt{65}} = \frac{42}{\sqrt{1885}} \approx \frac{42}{43.42} \approx 0.97
$$
这意味着两个孩子的体重增长之间的预期相关性大约是0.97，表明他们的体重增长趋势非常相似。


#### 第三部分：解释参数 $ \psi_{11} $ 和 $ \psi_{21} $ 的挑战
##### 4.2.7 参数解释的挑战
解释参数 $ \psi_{11} $ 和 $ \psi_{21} $ 可能会比较困难，因为它们的值依赖于协变量 $ x_{ij} $ 的转换。例如，如果我们将协变量 $ x_{ij} $ 进行中心化处理，使得 $ x_{ij} = 0 $ 成为一个有用的参考点，这将有助于解释这些参数。

##### 4.2.8 协变量的转换
通常，我们会将协变量进行中心化处理，比如将时间变量设置为0表示开始时间。这样做的目的是为了让 $ x_{ij} = 0 $ 成为一个有意义的参考点。因为 $ \psi_{21} $ 的大小和解释依赖于 $ x_{ij} $ 的位置（或转换），这通常是任意的，所以通过指定不相关的截距和斜率来将 $ \psi_{21} $ 设置为0通常没有意义。

#### 第四部分：估计方差的解释
##### 4.2.9 估计方差的解释
估计的方差 $ \hat\psi_{11} $ 和 $ \hat\psi_{22} $ 的大小可以通过构建包含95%集群截距和斜率的区间来解释（将估计值视为已知参数）。假设截距和斜率呈正态分布，均值为 $ \hat\beta_1 $ 和 $ \hat\beta_2 $，方差为 $ \hat\psi_{11} $ 和 $ \hat\psi_{22} $，则这些区间为：

$$
\hat\beta_1 \pm 1.96\sqrt{\hat\psi_{11}}
$$

$$
\hat\beta_2 \pm 1.96\sqrt{\hat\psi_{22}}
$$
这些区间表示95%的集群的截距和斜率落在这些范围内。


- **$\hat \beta_1$** 和 **$\hat \beta_2$**：分别是截距和斜率的估计值。
- **$1.96\sqrt{\hat \psi_{11}}$** 和 **$1.96\sqrt{\hat \psi_{22}}$**：分别是截距和斜率的标准误差。1.96 是基于正态分布的性质，95% 的数据落在均值的 +/- 1.96



##### 4.2.10 预测集群特定的回归线
为了帮助解释模型的随机部分，可以绘制预测的集群特定的回归线。这可以帮助我们直观地看到不同集群的截距和斜率是如何变化的。
- **例子：**
假设我们估计：
- $\hat \beta_1 = 50$（平均体重的截距），
- $\hat\beta_2 = 2$（平均体重增长的斜率），
- $\hat\psi_{11} = 4$（随机截距的方差），
- $\hat\psi_{22} = 2$（随机斜率的方差）。
那么95%的集群的截距和斜率的区间将是：
$$
\hat\beta_1 \pm 1.96\sqrt{\hat\psi_{11}} = 50 \pm 1.96 \times \sqrt{4} = 50 \pm 1.96 \times 2 = 50 \pm 3.92
$$
$$
\hat\beta_2 \pm 1.96\sqrt{\hat\psi_{22}} = 2 \pm 1.96 \times \sqrt{2} = 2 \pm 1.96 \times 1.41 = 2 \pm 2.76
$$
这意味着我们可以预期95%的集群的截距将在46.08到53.92千克之间，斜率将在-0.76到4.76千克/年之间。

#### 总结
- **协方差矩阵 $\Psi$** 描述了随机截距和随机斜率之间的关系。
- **单位的影响** 使得我们不能直接比较随机截距和随机斜率的方差。
- **总残差** 现在是异方差的，因为它是协变量的二次函数。
- **条件协方差** 和 **内类相关** 描述了同一集群内不同观测值之间的相关性。
- **参数解释的挑战** 可以通过将协变量转换为以0为参考点来克服。
- **估计方差的解释** 可以通过构建包含大部分集群的截距和斜率的区间来实现。

## 5 Estimation using mixed

The **`mixed`** command can be used to fit linear random-coefficient models by ML or REML. (**`xtreg`** can only fit two-level random-intercept models.)

### 5.1 Random-intercept model

We first consider a random-intercept model discussed in the previous chapter:

$$\begin{matrix}y_{ij}&=&(\beta_1+\zeta_{1j})+\beta_2x_{ij}+\epsilon_{ij}\end{matrix}$$


This model is a special case of the random-coefficient model in $(4.1)$ with $\zeta_{2j} =0 $ or, equivalently, with zero random-slope variance and zero random-intercept and random-slope covariance, $\psi_{22}=\psi_{21}=0$.

ML estimates for the random-intercept model can be obtained using **`mixed`** with the **`mle`** option (the default), and we also use the **`vce(robust)`** option for robust standard errors.

```
mixed gcse lrt || school:, mle stddeviations vce(robust)
```
    
    Performing gradient-based optimization: 
    Iteration 0:  Log pseudolikelihood = -14024.799  
    Iteration 1:  Log pseudolikelihood = -14024.799  
    
    Computing standard errors ...
    
    Mixed-effects regression                             Number of obs    =  4,059
    Group variable: school                               Number of groups =     65
                                                         Obs per group:
                                                                      min =      2
                                                                      avg =   62.4
                                                                      max =    198
                                                         Wald chi2(1)     = 852.73
    Log pseudolikelihood = -14024.799                    Prob > chi2      = 0.0000
    
                                    (Std. err. adjusted for 65 clusters in school)
    ------------------------------------------------------------------------------
                 |               Robust
            gcse | Coefficient  std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             lrt |       0.56       0.02    29.20   0.000         0.53        0.60
           _cons |       0.02       0.41     0.06   0.953        -0.77        0.82
    ------------------------------------------------------------------------------
    
    ------------------------------------------------------------------------------
                                 |               Robust           
      Random-effects parameters  |   Estimate   std. err.     [95% conf. interval]
    -----------------------------+------------------------------------------------
    school: Identity             |
                       sd(_cons) |       3.04       0.32          2.48        3.72
    -----------------------------+------------------------------------------------
                    sd(Residual) |       7.52       0.13          7.27        7.78
    ------------------------------------------------------------------------------


To allow later comparison with random-coefficient models via likelihood-ratio tests, we store these estimates by using

```
estimates store ri
```

The random-intercept model assumes that the school-specific regression lines are parallel. The common coefficient or slope $\beta_2$ of **lrt**, shared by all schools, is estimated as 0.56 and the mean intercept as 0.02. Schools vary in their intercepts with an estimated standard deviation of 3.04. Within the schools, the estimated residual standard deviation around the school-specific regression lines is 7.52. The within-school correlation, after controlling for **lrt**, is therefore estimated as

We could obtain this within-school correlation by typing **`estat icc`**.

The ML estimates for the random-intercept model are also given under “Random intercept” in table 4.1.

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.16.png" style="width:600px;height:450px;" alt="图片描述">
  <figcaption><strong>Table 4.1: Maximum likelihood estimates for inner-London-schools data with robust standard errors</strong></figcaption>
</figure>

### 5.2 Random-coefficient model

We now relax the assumption that the school-specific regression lines are parallel by introducing random school-specific slopes $β_{j} (j = 1, ..., J)$ and $u_{ij}$.

$$y_{ij} = (\beta_1+\zeta_{1j})+(\beta_2+\zeta_{2j})x_{ij}+\epsilon_{ij}$$

- $ y_{ij} $ 是第 $ j $ 所学校的第 $ i $ 个学生的 GCSE（普通中等教育证书）成绩。
- $ \beta_1 $ 和 $ \beta_2 $ 是固定效应（fixed effects）的截距和斜率。
- $ \zeta_{1j} $ 和 $ \zeta_{2j} $ 是随机效应的截距和斜率，它们是学校 $ j $ 特有的。
- $ x_{ij} $ 是解释变量，比如学生的某种测试成绩。
- $ \epsilon_{ij} $ 是误差项。

To introduce a random slope for **lrt** using **``mixed``**, we simply add that variable name in the specification of the random part, replacing **``school:``** with **``school: lrt``**. We must also specify the **``covariance(unstructured)``** option because **``mixed``** will otherwise set the covariance $\psi_{21}$(and the corresponding correlation) to 0 by default. ML estimates for the random-coefficient model are then obtained using

在Stata中，我们使用 `mixed` 命令来估计这个模型。如果我们想要为 `lrt`（可能是某种测试成绩）引入随机斜率，我们需要在随机部分的规范中添加变量名，将 `school:` 替换为 `school: lrt`。同时，我们需要指定 `covariance(unstructured)` 选项，因为默认情况下，`mixed` 命令会将协方差 $ \psi_{21} $（以及相应的相关性）设置为0。

```
mixed gcse lrt || school: lrt, covariance(unstructured) mle stddeviations ///
 vce(robust)
```

这个命令做了以下几件事：
- `gcse lrt`：指定了固定效应模型，其中 `gcse` 是因变量，`lrt` 是解释变量。
- `|| school: lrt`：指定了随机效应模型，其中 `lrt` 的斜率在不同学校之间是随机的。
- `covariance(unstructured)`：允许随机效应的协方差矩阵是无结构的，即每个随机效应都可以有自己的方差和它们之间的协方差。
- `mle`：使用最大似然估计方法。
- `stddeviations`：输出随机效应的标准差而不是方差。
- `vce(robust)`：使用稳健标准误。

    
        Performing EM optimization ...
        
        Performing gradient-based optimization: 
        Iteration 0:  Log pseudolikelihood = -14004.613  
        Iteration 1:  Log pseudolikelihood = -14004.613  
        
        Computing standard errors ...
        
        Mixed-effects regression                             Number of obs    =  4,059
        Group variable: school                               Number of groups =     65
                                                             Obs per group:
                                                                          min =      2
                                                                          avg =   62.4
                                                                          max =    198
                                                             Wald chi2(1)     = 767.80
        Log pseudolikelihood = -14004.613                    Prob > chi2      = 0.0000
        
                                           (Std. err. adjusted for 65 clusters in school)
        ---------------------------------------------------------------------------------
                        |               Robust
                   gcse | Coefficient  std. err.      z    P>|z|     [95% conf. interval]
        ----------------+----------------------------------------------------------------
                    lrt |       0.56       0.02    27.71   0.000         0.52        0.60
                  _cons |      -0.12       0.40    -0.29   0.774        -0.90        0.67
        ---------------------------------------------------------------------------------
        
        ------------------------------------------------------------------------------
                                     |               Robust           
          Random-effects parameters  |   Estimate   std. err.     [95% conf. interval]
        -----------------------------+------------------------------------------------
        school: Unstructured         |
                             sd(lrt) |       0.12       0.02          0.08        0.18
                           sd(_cons) |       3.01       0.31          2.45        3.69
                     corr(lrt,_cons) |       0.50       0.18          0.09        0.76
        -----------------------------+------------------------------------------------
                        sd(Residual) |       7.44       0.13          7.20        7.69
        ------------------------------------------------------------------------------

输出结果提供了固定效应和随机效应的估计值。
- **固定效应**：
  - `lrt` 的系数是 0.56，意味着在控制其他因素的情况下，`lrt` 每增加一个单位，`gcse` 成绩预期增加 0.56 单位。
  - `_cons` 是截距项，但在这个模型中它不显著。
- **随机效应**：
  - `sd(lrt)` 是 `lrt` 斜率的标准差，即不同学校 `lrt` 斜率的变异程度。
  - `sd(_cons)` 是截距项的标准差，即不同学校截距的变异程度。
  - `corr(lrt,_cons)` 是 `lrt` 斜率和截距之间的相关性。


Because the **``stddeviations``** option was used, the output shows the standard deviations, **``sd(lrt)``**, of the slope and **``sd(_cons)``** of the intercept instead of variances. It also shows the correlation between intercepts and slopes, **``corr(lrt,_cons)``**, instead of the covariance. We can obtain the estimated covariance matrix either by replaying the estimation results without the **``stddeviations``** option (or with the **``variance``** option).


1. 使用 `stddeviations` 选项

在混合效应模型中，我们通常对随机效应的方差和协方差感兴趣，因为这些参数描述了随机效应的变异程度和它们之间的关联性。在Stata中，使用 `stddeviations` 选项可以让我们直接看到随机效应的标准差，而不是方差。
- **标准差**：衡量随机效应的变异程度。例如，`sd(lrt)` 表示不同学校 `lrt` 斜率的标准差，即学校之间 `lrt` 斜率的变异程度。
- **`sd(_cons)`**：表示不同学校截距的标准差，即学校之间截距的变异程度。
2. 相关性与协方差

- **相关性**：衡量两个随机效应之间的线性关联程度。例如，`corr(lrt,_cons)` 表示 `lrt` 斜率和截距之间的相关性。如果这个值接近1或-1，表示它们之间有很强的正相关或负相关关系；如果接近0，则表示它们之间几乎没有线性关系。
- **协方差**：衡量两个随机效应共同变化的程度。协方差和相关性不同，协方差受到变量单位和量级的影响，而相关性是一个无量纲的度量。
3. 获取协方差矩阵

虽然 `stddeviations` 选项提供了标准差和相关性，但有时我们可能需要原始的协方差矩阵。

我们可以通过以下两种方式之一来获取：

- **不使用 `stddeviations` 选项**：重新运行模型命令，不使用 `stddeviations` 选项，这样输出将直接显示方差和协方差。

```
mixed gcse lrt || school: lrt, covariance(unstructured) mle ///
vce(robust)
```
- **使用 `variance` 选项**：这个选项与 `stddeviations` 选项相反，它将显示方差而不是标准差。

```
mixed gcse lrt || school: lrt, covariance(unstructured) mle variance ///
vce(robust)
```
- **使用 `estat recovariance` 命令**：这个命令可以在模型估计后用来直接获取随机效应的协方差矩阵。

```
estat recovariance
```

通过使用 `stddeviations` 选项，我们可以更直观地理解随机效应的变异程度和它们之间的关联性。然而，如果我们需要进行更复杂的统计分析，比如构建预测模型或进行假设检验，我们可能需要原始的协方差矩阵。通过重新运行模型命令或使用 `estat recovariance` 命令，我们可以获取这些信息。这些工具为我们提供了灵活性，以适应不同的分析需求。



```
mixed, variance
```

or by using the postestimation command **``estat recovariance``**.


```
estat recovariance
```
    
    Random-effects covariance matrix for level school
    
                 |       lrt      _cons 
    -------------+----------------------
             lrt |  .0145358            
           _cons |  .1804042    9.04472 

The ML estimates for the random-coefficient model were also given under “Random coefficient” in table 4.1. We store the estimates under the name **rc** for later use:

```
estimates store rc
```

We can also obtain the model-implied residual standard deviations and correlations among the GCSE scores for students in a particular school by using the **``estat wcorrelation``** command. For schools that have many students in the data, the correlation matrix is too large to display without wrapping, so we choose school 54, which has 8 students in the data, for illustration. First, we sort the data in ascending order of **lrt** within school and list the values of **lrt** because they will affect both the standard deviations and correlations, as shown in equations (4.3) and (4.2).


1. 使用 `estat wcorrelation` 命令

`estat wcorrelation` 是 Stata 中的一个命令，用于计算模型预测的残差标准差和相关性。这有助于我们理解在特定学校内，学生的GCSE成绩之间的相互关系。

2. 选择特定学校

由于数据中有些学校的学生数量很多，相关性矩阵会非常大，难以在不换行的情况下显示。因此，我们选择了学校54作为示例，这所学校在数据中有8名学生。

3. 数据排序和列表

首先，我们按照学校和 `lrt` 的升序对学生数据进行排序，并列出学校54的 **lrt** 值。这是因为 **lrt** 值会影响残差标准差和相关性。

```
sort school lrt
list school lrt if school==54, clean noobs
```
    
        school       lrt  
            54   -5.3806  
            54     2.058  
            54    2.8845  
            54     3.711  
            54    9.4967  
            54    10.323  
            54    11.976  
            54    11.976  

Now, we obtain the estimated residual standard deviations and correlations for school 54:

使用 `estat wcorrelation` 命令，我们可以计算学校54的学生GCSE成绩的残差标准差和相关性。


```
estat wcorrelation, at(school=54)
```
    
             obs |      1       2       3       4       5       6       7       8 
    -------------+---------------------------------------------------------------
              sd |  7.930   8.076   8.098   8.121   8.315   8.348   8.415   8.415 
    
    Correlations:
    
             obs |      1       2       3       4       5       6       7       8 
    -------------+---------------------------------------------------------------
               1 |  1.000                                                         
               2 |  0.129   1.000                                                 
               3 |  0.130   0.153   1.000                                         
               4 |  0.131   0.155   0.158   1.000                                 
               5 |  0.137   0.170   0.173   0.177   1.000                         
               6 |  0.138   0.172   0.175   0.179   0.202   1.000                 
               7 |  0.139   0.176   0.179   0.183   0.208   0.212   1.000         
               8 |  0.139   0.176   0.179   0.183   0.208   0.212   0.218   1.000 
    
The standard deviations increase with increasing **lrt**. To interpret the pattern of the correlations, we can look down the columns, which corresponds to holding the **lrt** for one student constant and looking at the correlations as the **lrt** of the other student increases. We see that the corresponding correlations increase.

- **残差标准差**：显示了每个学生GCSE成绩的预测残差标准差。随着 **lrt** 的增加，残差标准差也在增加，这表明 **lrt** 值较高的学生的成绩变异性更大。
- **相关性**：显示了学生GCSE成绩之间的相关性。我们可以通过查看列来解释相关性模式，这相当于保持一个学生的 `lrt` 值不变，观察另一个学生的 **lrt** 值增加时相关性的变化。我们可以看到，随着 **lrt** 的增加，相应的相关性也在增加。

Here we used ML estimation. REML estimation should be used instead when the number of clusters is small ($J - q < 42$,see display 2.1), and this method is requested by specifying the **``reml``** option.

在模型估计中，我们使用了最大似然估计（MLE）。但是，当簇的数量较少时（例如，学校数量减去随机效应的数量小于42），我们应该使用限制性最大似然估计（REML）。REML 方法通过指定 `reml` 选项来请求。

```
mixed, reml
```
REML 方法在簇的数量较少时更为准确，因为它对小样本的方差估计进行了调整。

## 6 Testing the slope variance

Before interpreting the parameter estimates, we may want to test whether the random slope is needed in addition to the random intercept. Specifically, we test the null hypothesis.

$$H_0: \psi_{22}=0\quad\mathrm{against}\quad H_a:\psi_{22}>0$$

Note that $H_0$ is equivalent to the hypothesis that the random slopes $\xi_{2j}$ are all 0. The null hypothesis also implies that $\psi_{21}=0$, because a variable that does not vary also does not covary with other variables. Setting $\psi_{22}=0$ and $\psi_{21}=0$ gives the random-intercept model.

A naïve likelihood-ratio test can be performed using the **``lrtest``** command.

```
lrtest ri rc, force
```
    
    Likelihood-ratio test
    Assumption: ri nested within rc
    
     LR chi2(2) =  40.37
    Prob > chi2 = 0.0000

The **``force``** option was used here because without it, Stata will not perform likelihood-ratio tests when robust standard errors have been specified. This is because inferences based on robust standard errors do not require the likelihood to be correct (that is, to correspond to the data-generating mechanism), which is why Stata calls it a pseudolikelihood in the output. Because likelihood-ratio tests require correct likelihoods, Stata will not perform such tests unless forced to do so. Here we accept that, unlike inferences for the regression coefficients based on robust standard errors, likelihood-ratio tests for variance and covariance parameters will not be robust to misspecification of the residual covariance structure. Remember that point estimators of variance and covariance parameters are inconsistent if the residual covariance structure is misspecified (which is also unlike regression coefficients).

在Stata中使用`force`选项的原因与似然比检验（Likelihood-Ratio Test, LRT）的执行条件有关。似然比检验是一种统计方法，用于比较两个嵌套模型的拟合度，即一个模型是另一个模型的特殊情况。在执行似然比检验时，Stata要求两个模型都必须使用最大似然法（Maximum Likelihood, ML）进行估计，而且通常要求模型的残差协方差结构被正确指定。

当我们在模型中指定`vce(robust)`选项时，我们告诉Stata我们担心模型的规范可能不正确，因此我们使用稳健标准误（Robust Standard Errors）来得到系数的估计。这些稳健标准误不需要似然函数是正确的，也就是说，它们不需要对应于数据生成机制。因此，当使用稳健标准误时，Stata不会执行似然比检验，因为似然比检验需要正确的似然函数。

`force`选项在这里的作用是强制Stata执行似然比检验，即使在已经指定了稳健标准误的情况下。使用`force`选项时，Stata会执行检验，但不会保证检验结果的有效性或可解释性，因为稳健标准误的使用意味着我们可能已经偏离了似然比检验的有效假设。

此外，似然比检验的零假设是模型参数的某些限制是成立的，例如在这种情况下，零假设是随机斜率的方差 $ \psi_{22}$ 等于0。如果这个零假设是真的，那么模型的拟合度不应该因为这些限制而受到太大影响。似然比检验通过比较有限制和无限制模型的对数似然值来工作，如果两个模型的对数似然值差异显著，那么我们可以拒绝零假设，认为更复杂的模型（无限制模型）提供了更好的拟合度。

在实际操作中，如果模型的残差协方差结构被错误指定，那么基于稳健标准误的回归系数推断将不受影响，但似然比检验的结果可能会受到影响。因此，在执行似然比检验时，我们需要确保模型的规范是正确的，或者使用`force`选项来强制执行检验，但要意识到这可能会影响检验结果的解释。

总结来说，`force`选项允许我们在不满足似然比检验所有假设的情况下执行检验，但这样做时要谨慎，因为结果可能不可靠。在实际应用中，如果模型的残差协方差结构被正确指定，那么通常不需要使用`force`选项。如果使用稳健标准误，那么似然比检验的结果可能不准确，这时可以使用`force`选项来执行检验，但要清楚这只是一个近似的检验。

This likelihood-ratio test is naive because the variance $\psi_{22}$ must be nonnegative so that the null hypothesis is on the boundary of the parameter space. As discussed in section 2.6.2 for random-intercept models, the asymptotic null distribution of the likelihood-ratio statistic $L$ is therefore no longer a simple $\chi^2$ distribution as assumed by the **`lrtest`** command.

这段话讨论的是一种统计检验方法，叫做**似然比检验（likelihood-ratio test）**。这种方法用于比较两个统计模型的拟合优度。

1. **方差 $\psi_{22}$ 必须是非负的**：
   - 方差是衡量数据分散程度的一个指标。在统计学中，方差不能为负数，因为它代表的是平方后的数值。
   - 这里提到的 $\psi_{22}$ 是一个特定的方差参数，它必须是非负的。

2. **零假设在参数空间的边界上**：
   - 零假设（null hypothesis）是指在统计检验中假设没有显著差异或效应。
   - 参数空间是指所有可能的参数值的集合。当零假设在参数空间的边界上时，意味着某些参数（如方差）可能接近于零。

3. **渐近零分布不再是简单的 $\chi^2$ 分布**：
   - 渐近分布是指当样本量趋于无穷大时，统计量的分布。
   - $\chi^2$ 分布是一种常见的统计分布，用于许多检验中。
   - 由于零假设在参数空间的边界上，似然比统计量 $L$ 的分布不再是简单的 $\chi^2$ 分布，而是更复杂的分布。

4. **`lrtest` 命令的假设**：
   - **`lrtest`** 是一个用于执行似然比检验的命令。
   - 该命令假设似然比统计量 $L$ 服从 $\chi^2$ 分布，但在这种情况下，这个假设不成立。

总结一下，这段话的核心意思是：由于方差参数的限制，零假设在参数空间的边界上，导致似然比统计量的分布变得复杂，不再符合常规的 $\chi^2$ 分布，因此使用 **`lrtest`** 命令时需要注意这一点。


In `mixed`, the default estimation metric (transformation used during estimation) for the covariance matrix of the random effects is the square root or Cholesky decomposition (which is requested by the `matsqrt` option). This parameterization forces the covariance matrix to be positive semidefinite (estimates on the boundary of parameter space, for example, 0 variance or perfect correlations, are allowed). It can be shown that the asymptotic null distribution for testing the null hypothesis that the variance of the r + 1th random effect is 0 becomes 0.5$\chi^2(r)$ + 0.5$\chi^2(r+1)$. For our case of testing the random slope variance in a model with a random intercept and a random slope, r = 1; it follows that the asymptotic null distribution is 0.5$\chi^2(1)$ + 0.5$\chi^2(2)$. The correct p-value can be obtained as

```
display 0.5*chi2tail(1,40.37) + 0.5*chi2tail(2,40.37)   
```

在Stata中使用`mixed`命令进行混合效应模型分析时，我们经常需要处理随机效应的协方差矩阵。这个矩阵描述了模型中随机效应的变异性和它们之间的关联性。默认情况下，`mixed`命令使用平方根或Cholesky分解作为随机效应协方差矩阵的估计度量（变换）。这种参数化方式允许协方差矩阵是正半定的（Positive Semi-definite），这意味着它可以包含边界值，比如0方差或完全相关的估计。

1. 理解Cholesky分解

Cholesky分解是一种将协方差矩阵分解为一个下三角矩阵和其转置的上三角矩阵的乘积的方法。这种分解确保了协方差矩阵是正半定的，从而保证了模型的数学性质和估计的稳定性。

2. 边界值的处理

在模型中，我们可能会遇到边界值，比如某个随机效应的方差估计为0（即该效应没有随机性），或者两个随机效应完全相关。Cholesky分解允许这些边界值的存在，因为它不会强制所有的随机效应都必须有非零的方差。

3. 似然比检验的修正

当我们使用Cholesky分解时，如果我们要进行似然比检验来测试某个随机效应的方差是否为0，我们需要对检验的分布进行修正。这是因为在边界值的情况下，标准的$ \chi^2 $分布不再适用。
对于一个模型，如果它包含一个随机截距和一个随机斜率（$ r=1 $），那么测试随机斜率方差是否为0的检验的渐近分布变为$ 0.5\chi^2(1) + 0.5\chi^2(2) $。这意味着，我们不能简单地使用标准的$ \chi^2 $分布来计算p值。

4. 计算修正的p值

在Stata中，我们可以使用以下命令来计算修正的p值：
```stata
display 0.5*chi2tail(1,40.37) + 0.5*chi2tail(2,40.37)
```

这里，`chi2tail(k, chi2)`函数计算的是自由度为k的$ \chi^2 $分布的右尾概率，对应于检验统计量的值`chi2`。这个命令将计算两个$ \chi^2 $分布的加权和，得到修正的p值。

5. 使用`matlog`选项

如果我们在`mixed`命令中使用`matlog`选项，那么估计度量将使用矩阵对数，这要求协方差矩阵是正定的（Positive Definite），不允许边界值的存在。在这种情况下，如果模型的ML估计位于参数空间的边界上，可能会导致模型无法收敛。
如果使用`matlog`选项，那么渐近分布变为$ 0.5\chi^2(0) + 0.5\chi^2(r+1) $。对于我们的案例，渐近分布是$ 0.5\chi^2(0) + 0.5\chi^2(2) $，所以修正的p值可以通过将基于$ \chi^2(2) $的朴素p值除以2来获得。
6. 总结

在混合效应模型中，处理随机效应的协方差矩阵时，我们需要考虑边界值和正确的统计检验方法。Cholesky分解提供了一种灵活的处理方式，但需要我们对似然比检验的分布进行修正。这些修正确保了我们在模型估计和假设检验时能够得到准确的结果。


We see that the conclusion remains the same as for the naïve approachfor this application.

If the matlog option is used, the estimation metric for the covariance matrix of the random effects is matrix logarithms, which forces the covariance matrix to be positive definite (estimates on the boundary of the parameter space are not allowed). Consequently, convergence is not achieved if the ML estimates are on the boundary of the parameter space. If this leads to reverting to the model under the null hypothesis, giving a likelihood-ratio statistic equal to 0, then the asymptotic null distribution for testing the null hypothesis that the variance of the r + 1th random effect is 0 becomes $0.5\chi^2(0)+0.5\chi^2(r+1)$ , where $\chi^2(0)$ has a probability mass of 1 at 0. For testing the random slope variance in a model with a random intercept and a random slope, $r=1$ and the distribution becomes $0.5\chi^2(0)+0.5\chi^2(2)$ so that the correct p-value can simply be obtained by dividing the naive p-value based on the $\chi^2(2)$ by 2.

Keep in mind that the naïve likelihood-ratio test for testing the slope variance is conservative. Hence, if the null hypothesis of a zero slope variance is rejected by the naïve approach, it is also rejected by the correct approach.

Unfortunately, there is no straightforward procedure available for testing several variances simultaneously, unless the random effects are independent (see section 8.8), and simulations (for example, parametric bootstrapping) must be used in this case to obtain the empirical null distribution.

1. `matlog`选项的影响

当使用`matlog`选项时，随机效应协方差矩阵的估计度量变为矩阵对数。这种度量要求协方差矩阵是正定的，不允许参数估计值位于参数空间的边界上。这意味着，如果最大似然估计（MLE）的值位于边界上，比如方差为零，模型可能无法收敛。
如果模型无法收敛，并且回到了零假设下的模型，那么似然比统计量可能等于0。在这种情况下，测试零假设（即第$ r+1 $个随机效应的方差为0）的渐近分布变为$ 0.5\chi^2(0) + 0.5\chi^2(r+1) $。这里的$ \chi^2(0) $在0处有1的概率质量，这意味着它总是0。
对于测试随机斜率方差的模型，其中$ r=1 $（一个随机截距和一个随机斜率），渐近分布变为$ 0.5\chi^2(0) + 0.5\chi^2(2) $。因此，正确的p值可以通过将基于$ \chi^2(2) $的朴素p值除以2来获得。

2. 朴素似然比检验的保守性

朴素的似然比检验是保守的，这意味着它可能过于严格，不太可能拒绝零假设。因此，如果朴素方法拒绝了零假设（即认为斜率方差不为零），那么更准确的检验方法也可能会拒绝零假设。

3. 同时测试多个方差

不幸的是，没有简单的方法可以同时测试多个方差，除非随机效应是独立的。在这种情况下，可能需要使用模拟方法（例如参数自举）来获得经验上的零分布。
参数自举是一种统计技术，通过从估计的分布中抽样来近似复杂的统计量的分布。这可以用来评估标准检验方法在特定情况下的适用性，尤其是在零假设位于参数空间边界时。

4. 总结

在混合效应模型中，我们可以使用不同的估计度量来处理随机效应的协方差矩阵。Cholesky分解允许边界值的存在，而`matlog`选项要求协方差矩阵是正定的。在测试随机效应的方差时，我们需要对似然比检验的分布进行修正，以获得准确的p值。此外，如果模型无法收敛，我们可能需要使用模拟方法来评估检验的有效性。
这个过程涉及到复杂的统计概念，但希望这个详细的解释能帮助你理解这个过程。如果你有任何问题或需要进一步的解释，请随时告诉我。




**在建立统计模型时，我们经常面临选择哪些变量应该包括在模型中的决策。在这个特定的例子中，我们想要确定是否需要在模型中包括一个随机斜率项，而不仅仅是一个随机截距项。**


#### 1. 为什么要进行假设检验
在建立统计模型时，我们经常面临选择哪些变量应该包括在模型中的决策。在这个特定的例子中，我们想要确定是否需要在模型中包括一个随机斜率项，而不仅仅是一个随机截距项。
#### 2. 零假设和备择假设
- **零假设** $ H_0 $：随机斜率的方差 $ \psi_{22} $ 等于0。这意味着所有学校的学生成绩与 **lrt** 的关系都是一样的，不存在学校之间的差异。
- **备择假设** $ H_a $：随机斜率的方差 $ \psi_{22} $ 大于0。这意味着不同学校的学生成绩与 **lrt** 的关系可能存在差异。
#### 3. 似然比检验
似然比检验是一种统计方法，用于比较两个模型：限制性模型（零假设下的模型）和一般模型（备择假设下的模型）。通过比较两个模型的拟合度，我们可以决定哪个模型更合适。
- **限制性模型**：只有随机截距，没有随机斜率。
- **一般模型**：既有随机截距，也有随机斜率。
在Stata中，我们使用 `lrtest` 命令来进行这个检验：
```stata
lrtest ri rc, force
```
这里，`ri` 代表只有随机截距的模型，而 `rc` 代表包含随机斜率的模型。`force` 选项用于强制执行检验。
#### 4. 似然比检验的问题
似然比检验假设检验统计量的渐近分布是 $ \chi^2 $ 分布。但是，当零假设位于参数空间的边界上时（比如方差等于0），这个假设不成立。在这种情况下，检验统计量的分布不再是标准的 $ \chi^2 $ 分布。
#### 5. 正确的p值计算
为了解决这个问题，我们需要使用一个修正的p值计算方法。这个方法基于这样一个事实：在零假设下，检验统计量的渐近分布是 $ 0.5\chi^2(r) + 0.5\chi^2(r+1) $ 的混合分布。
- **对于我们的情况**，$ r = 1 $（因为模型中有一个随机截距和一个随机斜率），所以渐近分布是 $ 0.5\chi^2(1) + 0.5\chi^2(2) $。
在Stata中，计算修正p值的命令如下：
```stata
display 0.5*chi2tail(1,40.37) + 0.5*chi2tail(2,40.37)
```
这个命令计算了修正的p值。
#### 6. `matlog` 选项
如果我们使用 `matlog` 选项，那么随机效应协方差矩阵的估计度量是矩阵对数。这要求协方差矩阵是正定的，不允许在参数空间的边界上估计。
- **如果ML估计位于边界上**，可能导致模型回到零假设下的模型，这时似然比统计量等于0。
在这种情况下，渐近分布变为 $ 0.5\chi^2(0) + 0.5\chi^2(r+1) $。对于我们的案例，渐近分布是 $ 0.5\chi^2(0) + 0.5\chi^2(2) $，所以修正的p值可以通过将基于 $ \chi^2(2) $ 的朴素p值除以2来获得。
#### 7. 保守性
朴素的似然比检验是保守的，这意味着如果它拒绝零假设，那么更准确的检验也会拒绝零假设。
#### 8. 同时测试多个方差
不幸的是，没有简单的方法可以同时测试多个方差，除非随机效应是独立的。在这种情况下，必须使用模拟（例如参数自举）来获得经验零分布。
#### 总结
通过似然比检验，我们可以测试随机斜率是否需要在模型中。但是，我们需要小心处理零假设位于参数空间边界上的情况。通过使用修正的p值计算方法，我们可以得到更准确的结论。如果使用 `matlog` 选项，我们还需要考虑另一种修正方法。这些方法帮助我们更准确地评估模型中的随机效应。

## 7 Interpretation of estimates

The population-mean intercept and slope are estimated as $-0.12$ and $0.56$, respectively. These estimates are similar to those for the random intercept model (see table 4.1) and are also close to the means of the school-specific intercept and slope estimates given in section 4.3.

The estimated random-intercept standard deviation and level-1 residual standard deviation are somewhat lower than for the random intercept model. The latter is because of a better fit of the school-specific regression lines for the random-coefficient model, which relaxes the restriction of parallel regression lines. The estimated covariance matrix of the intercepts and slopes is similar to the sample covariance matrix of the ordinary least-squares estimates reported in section 4.3.

As discussed in section 4.4.2, the easiest way to interpret the estimated standard deviations of the random intercept and random slope (conditional on the covariates) is to form intervals within which 95% of the schools' random intercepts and slopes are expected to lie assuming normality. Remember that these intervals represent ranges within which 95% of the realizations of a random variable are expected to lie, a concept different from confidence intervals, which are ranges within which an unknown parameter is believed to lie.

For the intercepts, we obtain $-0.115 \pm 1.96\times3.007$, so 95% of schools have their intercept in the range -6.0 to 5.8. In other words, the school mean GCSE scores for children with average LRT scores ($\text{lrt}=0$) vary between -6.0 and 5.8. For the slopes, we obtain $0.557 \pm 1.96\times0.121$, giving an interval from 0.32 to 0.80. Thus, 95% of schools have slopes between 0.32 and 0.80.

This exercise of forming intervals is particularly important for slopes because it is useful to know whether the slopes have different signs for different schools (which would be odd in the current example). The range from 0.32 to 0.80 is fairly wide and the regression lines for schools may cross: one school could add more value (produce higher mean GCSE scores for given LRT scores) than another school for students with low LRT scores and add less value than the other school for students with high LRT scores.

The estimated correlation $\widehat{\rho}_{21}=0.50$ between random intercepts and slopes (given the covariates) means that schools with larger mean GCSE scores for students with average LRT scores than other schools also tend to have larger slopes than those other schools. This correlation, combined with the random-intercept and slope variances and the range of LRT scores, determines how much the lines cross, something that is best explored by plotting the predicted regression lines for the schools, as demonstrated in section 4.8.3.

The variance of the total residual $\xi_{ij}$ (equal to the conditional variance of the responses $y_{ij}$ given the covariates $X_{ij}$ ) was given in (4.2). We can estimate the corresponding standard deviation by plugging in the ML estimates:

$$\sqrt{\widehat{\mathrm Var}(\xi_{ij}|\mathbf X_{j})} = \sqrt{\widehat\psi_{11}+2\widehat\psi_{21}x_{ij}+\widehat\psi_{22}x_{ij}^{2}+\widehat\theta}$$

$$\begin{array}{rcl}=&\sqrt{9.0447+2\times0.1804\times x_{ij}+0.0145\times x_{ij}^2+55.3653}\end{array}$$

A graph of the estimated standard deviation of the total residual against the covariate **lrt** ($X_{ij}$) can be obtained using the following **``twoway function``** command, which is graphed in figure 4.8:

```
twoway function sqrt(9.0447+2*0.1804*x+0.0145*x^2+55.3653), range(-30 30) ///
 xtitle(LRT) ytitle(Estimated standard deviation of total residual)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.17.png" style="width:600px;height:450px;" alt="图片描述">
  <figcaption><strong>Figure 4.8: Heteroskedasticity of total residual $\xi_{ij}$ as function of lrt</strong></figcaption>
</figure>

The estimated standard deviation of the total residual varies between just under 8 and just under 9.5 for the range of lrt in the data.



### 1. 固定效应的估计

首先，我们有一个模型，它估计了人口平均截距（intercept）和斜率（slope）分别为 $-0.12$ 和 $0.56$。这些估计值与随机截距模型（random intercept model）的估计值相似，并且也接近于第4.3节给出的学校特定的截距和斜率估计值的平均值。

### 2. 随机效应的估计

接下来，我们估计了随机截距的标准差和第一层残差（level-1 residual）的标准差。这些估计值比随机截距模型的估计值要低一些。这是因为随机系数模型（random-coefficient model）的拟合效果更好，它放宽了平行回归线的限制。

### 3. 随机效应的协方差矩阵

我们还估计了截距和斜率的协方差矩阵，这个矩阵与第4.3节报告的普通最小二乘估计的样本协方差矩阵相似。

### 4. 随机效应的解释

解释随机截距和斜率的标准差的一种方法是形成区间，在这个区间内，假设正态性，95%的学校随机截距和斜率预期会落在这个区间内。这些区间代表了随机变量的实现预期落在的范围内，这与置信区间的概念不同，置信区间是未知参数预期落在的区间。


#### 例子：

- 对于截距，我们得到 $-0.115 \pm 1.96 \times 3.007$，所以95%的学校截距在 $-6.0$ 到 $5.8$ 的范围内。
- 对于斜率，我们得到 $0.557 \pm 1.96 \times 0.121$，给出的区间从 $0.32$ 到 $0.80$。

### 5. 斜率的解释
形成这些区间对于斜率特别重要，因为知道不同学校的斜率是否有不同的符号（这在当前例子中会很奇怪）是有用的。从 $0.32$ 到 $0.80$ 的范围相当宽，学校的回归线可能会交叉：一所学校可能在低LRT分数的学生中比其他学校增加更多的价值（产生更高的平均GCSE分数），而在高LRT分数的学生中增加的价值比其他学校少。

### 6. 随机效应之间的相关性
估计的随机截距和斜率之间的相关性（给定协变量）为 $0.50$，这意味着对于平均LRT分数的学生，平均GCSE分数比其他学校大的学校也倾向于比其他学校有更大的斜率。这种相关性，结合随机截距和斜率的方差以及LRT分数的范围，决定了线条交叉的程度，这最好通过绘制学校的预测回归线来探索，如第4.8.3节所示。


### 7. 总残差的方差估计
总残差 $\xi_{ij}$（等于给定协变量 $X_{ij}$ 的响应 $y_{ij}$ 的条件方差）的方差在公式 (4.2) 中给出。我们可以通过插入最大似然估计（ML estimates）来估计相应的标准差：

$$
\sqrt{\widehat{\mathrm Var}(\xi_{ij}|\mathbf X_{j})} = \sqrt{\widehat\psi_{11}+2\widehat\psi_{21}x_{ij}+\widehat\psi_{22}x_{ij}^{2}+\widehat\theta}
$$

$$
=\sqrt{9.0447+2\times0.1804\times x_{ij}+0.0145\times x_{ij}^2+55.3653}
$$

### 8. 残差标准差的图形表示
最后，我们可以使用 `twoway function` 命令绘制残差标准差与协变量 LRT 的图形，如图4.8所示。
现在，让我们来详细解释一下最后一个公式：

$$
\sqrt{\widehat{\mathrm Var}(\xi_{ij}|\mathbf X_{j})} = \sqrt{9.0447+2\times0.1804\times x_{ij}+0.0145\times x_{ij}^2+55.3653}
$$
这个公式是用来估计给定协变量 $X_{ij}$ 的条件下，响应 $y_{ij}$ 的条件方差的平方根。这里的 $\widehat{\mathrm Var}(\xi_{ij}|\mathbf X_{j})$ 表示的是条件方差，而 $\sqrt{\cdot}$ 表示的是取平方根。
- $\widehat\psi_{11}$、$\widehat\psi_{21}$、$\widehat\psi_{22}$ 和 $\widehat\theta$ 是模型参数的估计值。
- $x_{ij}$ 是协变量的值。
公式中的每个部分都是一个平方项或线性项，它们相加得到总的方差估计。然后我们取平方根得到标准差。
现在，让我们计算一下这个公式：
假设 $x_{ij} = 0$（这是一个特定的协变量值），我们可以将这个值代入公式：

$$
\sqrt{9.0447+2\times0.1804\times 0+0.0145\times 0^2+55.3653} = \sqrt{9.0447 + 0 + 0 + 55.3653} = \sqrt{64.41}
$$
计算平方根得到：

$$
\sqrt{64.41} \approx 8.02
$$
这意味着当 $x_{ij} = 0$ 时，估计的标准差大约是 8.02。
当然可以，让我们更详细地分解一下最后提到的公式：

$$
\sqrt{\widehat{\mathrm Var}(\xi_{ij}|\mathbf X_{j})} = \sqrt{9.0447+2\times0.1804\times x_{ij}+0.0145\times x_{ij}^2+55.3653}
$$
这个公式是用来估计给定协变量 $X_{ij}$ 的条件下，响应 $y_{ij}$ 的条件方差的平方根。这里的 $\widehat{\mathrm Var}(\xi_{ij}|\mathbf X_{j})$ 表示的是条件方差，而 $\sqrt{\cdot}$ 表示的是取平方根。
#### 公式分解
1. **$9.0447$**: 这是方差估计中的常数项，表示当 $x_{ij} = 0$ 时的方差基础值。
2. **$2 \times 0.1804 \times x_{ij}$**: 这是方差估计中的线性项，表示 $x_{ij}$ 的值每增加一个单位，方差会增加 $0.1804$ 的两倍。
3. **$0.0145 \times x_{ij}^2$**: 这是方差估计中的二次项，表示 $x_{ij}$ 的值的平方每增加一个单位，方差会增加 $0.0145$。
4. **$55.3653$**: 这是方差估计中的另一个常数项，通常与模型的其他部分有关，可能是与模型的误差项有关。


#### 计算步骤
假设 $x_{ij} = 10$（这是一个示例协变量值），我们可以将这个值代入公式：
$$
\sqrt{9.0447 + 2 \times 0.1804 \times 10 + 0.0145 \times 10^2 + 55.3653}
$$
1. **计算线性项**:
   $$
   2 \times 0.1804 \times 10 = 3.608
   $$
2. **计算二次项**:
   $$
   0.0145 \times 10^2 = 1.45
   $$
3. **将所有项相加**:
   $$
   9.0447 + 3.608 + 1.45 + 55.3653 = 69.468
   $$
4. **取平方根**:
   $$
   \sqrt{69.468} \approx 8.33
   $$
这意味着当 $x_{ij} = 10$ 时，估计的标准差大约是 8.33。

#### 图形表示
公式中的 $x_{ij}$ 可以取不同的值，我们可以通过绘制图形来观察标准差如何随 $x_{ij}$ 的变化而变化。在给定的代码中：
```
twoway function sqrt(9.0447+2*0.1804*x+0.0145*x^2+55.3653), range(-30 30) ///
 xtitle(LRT) ytitle(Estimated standard deviation of total residual)
```
这行代码是在统计软件中使用的，用于绘制 $x_{ij}$ 从 -30 到 30 范围内的标准差估计值的图形。图形可以帮助我们直观地看到标准差如何随 $x_{ij}$ 的变化而变化。


## 8 Assigning values to the random intercepts and slopes

Having obtained estimated model parameters $\hat\beta_1$, $\hat\beta_2$, $\hat\psi_{11}$, $\hat\psi_{22}$, $\hat\psi_{21}$, and $\hat\theta$, we now assign values to the random intercepts and slopes (see also section 2.11). This is useful for model visualization, residual diagnostics, and inference for individual clusters, as will be demonstrated. Until section 4.8.5, the estimated parameters will be treated as known. In section 4.8.5, we will use REML estimation to obtain standard errors for empirical Bayes predictions that take uncertainty in estimating $\hat\beta_1$ and $\hat\beta_2$ into account.

### 8.1 Maximum "likelihood" estimation

Maximum "likelihood" estimates of the random intercepts and slopes can be obtained by first predicting the total residuals $\xi_{ij} = y_{ij} - (\hat\beta_1 + \hat\beta_2 x_{ij})$ and then fitting individual regressions of $\xi_{ij}$ on $x_{ij}$ for each school by OLS. As explained in section 2.11.1, we put "likelihood" in quotes in the section heading because it differs from the marginal likelihood that is used to estimate the model parameters.

We can fit the individual regression models by using the **``statsby``** prefix command as shown in section 4.3. We first retrieve the **`mixed`** estimates stored under rc,


```
estimates restore rc
```

and obtain the predicted total residuals,

```
predict fixed, xb
generate totres = gcse - fixed
```

We can then use **`statsby`** to produce the variables **mli** and **mls**, which contain the ML estimates $\hat{\zeta}_{1j}$  and $\hat{\zeta}_{2j}$ of the random intercepts and slopes, respectively:

```
statsby mli=_b[_cons] mls=_b[lrt], by(school) saving(ols, replace): ///
  regress totres lrt
```

    
    (running regress on estimation sample)
    
          Command: regress totres lrt
              mli: _b[_cons]
              mls: _b[lrt]
               By: school
    
    Statsby groups:
    ..................................................    50
    ...............

```
sort school
merge m:1 school using ols
drop _merge
```
    
        Result                      Number of obs
        -----------------------------------------
        Not matched                             0
        Matched                             4,059  (_merge==3)
        -----------------------------------------

Maximum likelihood estimates will not be available for schools with only one observation or for schools within which $x_{ij}$ does not vary. There are no such schools in the dataset, but school 48 has only two observations, and the ML estimates of the intercept and slope look odd:

```
list lrt gcse mli mls if school==48, clean noobs
```
    
            lrt      gcse       mli         mls  
        -3.7276   -6.9951   -32.607   -7.458484  
        -4.5541   -1.2908   -32.607   -7.458484  

Because there are only two students, the fitted line connects the points perfectly. The school’s intercept and slope are determined by $ϵ_{1j}$ and $ϵ_{2j}$ roughly as much as they are by the true intercept and slope. The intercept and slope estimates are therefore imprecise and can be extreme; the so-called “bouncing beta” phenomenon often encountered when using ML estimation of random effects for clusters that provide little information. In general, we therefore do not recommend using this method and suggest using empirical Bayes prediction instead.

### 8.2 Empirical Bayes prediction

As discussed for random-intercept models in section 2.11.2, empirical Bayes (EB) predictions have a smaller prediction error variance (for given model parameters) than ML estimates because of shrinkage toward the mean. Furthermore, EB predictions are available for schools with only one observation or only one unique value of $x_{ij}$, for which ML estimates cannot be obtained.

Empirical Bayes predictions $\tilde{\zeta}_{1j}$ and $\tilde{\zeta}_{2j}$ of the random intercepts $\zeta_{1j}$ and slopes $\zeta_{2j}$, respectively, can be obtained using the **``predict``** command with the **``reffects``** option after estimation with **``mixed``**:

```
estimates restore rc
predict ebs ebi, reffects
```

Here we specified the variable names **ebs** and **ebi** for the EB predictions $\tilde{\zeta}_{2j}$  and $\tilde{\zeta}_{1j}$ of the random slopes and intercepts. The intercept variable comes last because **`mixed`** treats the intercept as the last random effect, as reflected by the output. This order is consistent with Stata's convention of treating the fixed intercept as the last regression parameter in estimation commands.

To compare the EB predictions with the ML estimates, we list one observation per school for schools 1–9 and school 48:

```
list school mli ebi mls ebs if pickone==1 & (school<10 | school==48), noobs
```

![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.18.png)


Most of the time, the EB predictions are closer to 0 than the ML estimates because of shrinkage, as discussed for random-intercept models in section 2.11.2. However, for models with several random effects, the relationship between EB predictions and ML estimates is somewhat more complex than for random-intercept models. The benefit of shrinkage is apparent for school 48, where the EB predictions appear more reasonable than the ML estimates.

We can see shrinkage more clearly by plotting the EB predictions against the ML estimates and superimposing a $y=x$. For the random intercept, the command is

```
twoway (scatter ebi mli if pickone==1 & school!=48, mlabel(school)) ///
  (function y=x, range(-10 10)), legend(off) xtitle(ML estimates of intercepts) ///
  ytitle(EB predictions of intercepts) legend(off) xline(0)
```

and for the random slope, it is

```
twoway (scatter ebs mls if pickone==1 & school!=48, mlabel(school)) ///
  (function y=x, range(-0.6 0.6)), xtitle(ML estimates of slopes)    ///
  ytitle(EB predictions of slopes) legend(off) xline(0)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.20.png" style="width:600px;height:400px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.19.png" style="width:600px;height:400px;" alt="图片描述">
  <figcaption><strong>Figure 4.9: Scatterplots of empirical Bayes (EB) predictions versus maximum likelihood (ML) estimates of school-specific intercepts (top) and slopes (bottom); equality of EB and ML shown as dashed reference lines and ML estimates of 0 shown as solid reference lines.</strong></figcaption>
</figure>

For ML estimates above 0, the EB prediction tends to be smaller than the ML estimate; the reverse is true for ML estimates below 0. There is more shrinkage for slopes than for intercepts.

### 8.3 Model visualization

To better understand the estimates obtained for random-intercept models and random-coefficient models—and in particular, the variability implied by the random part—it is useful to produce graphs of predicted model-implied regression lines for the individual schools.

This can be achieved using the **`predict`** command with the **`fitted`** option to obtain school-specific fitted regression lines, with ML estimates substituted for the regression parameters ($\beta_1$ and $\beta_2$) and EB predictions substituted for the random effects ($\zeta_{1j}$ for the random-intercept model, and $\zeta_{1j}$ and $\zeta_{2j}$ for the random-coefficient model). For instance, for the random-coefficient model, the predicted regression line for school $j$ is

$$\widehat{y}_{ij} = \widehat{\beta}_1+\widehat{\beta}_2x_{ij}+\widetilde{\zeta}_{1j}+\widetilde{\zeta}_{2j}x_{ij}$$

These predictions are obtained by typing

```
mixed gcse lrt || school: lrt, covariance(unstructured) mle stddeviations ///
 vce(robust)
mixed, variance
* mixed, variance
estat recovariance
estimates store rc

predict murc, fitted
```

and a spaghetti plot is produced as follows:

```
sort school lrt
twoway (line murc lrt, connect(ascending)), xtitle(LRT) ///
  ytitle(Empirical Bayes regression lines for model 2)
```

To obtain predictions for the random-intercept model, we must first restore the estimates stored under the name **ri**:

```
mixed gcse lrt || school:, mle stddeviations vce(robust)


estimates restore ri
predict muri, fitted
sort school lrt
twoway (line muri lrt, connect(ascending)), xtitle(LRT) ///
  ytitle(Empirical Bayes regression lines for model 1)
```

The resulting spaghetti plots of the school-specific regression lines for both the random-intercept model and the random-coefficient model are given in figure 4.10


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.22.png" style="width:600px;height:400px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.21.png" style="width:600px;height:400px;" alt="图片描述">
  <figcaption><strong>Figure 4.10: Spaghetti plots of empirical Bayes (EB) predictions of school-specific regression lines for the random-intercept model (top) and the random-coefficient model (bottom)</strong></figcaption>
</figure>

### 8.4 Residual diagnostics

If normality is assumed for the random intercepts $ζ_{1j}$, random slopes $ζ_{2j}$, and level-1 residuals $ε_{ij}$, the corresponding EB predictions should also have normal distributions.

如果在多层次模型中，我们假定随机截距（random intercepts）、随机斜率（random slopes）以及第一层残差（level-1 residuals）都服从正态分布，那么由此产生的EB预测也应该服从正态分布。

To plot the distributions of the predicted random effects, we must pick one prediction per school, and we can accomplish this by using the pickone variable created with the command **``egen pickone = tag(school)``** in section 4.3. We can now plot the distributions by using

```
histogram ebi if pickone==1, normal xtitle(Predicted random intercepts)

histogram ebs if pickone==1, normal xtitle(Predicted random slopes)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.23.png" style="width:600px;height:400px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.24.png" style="width:600px;height:400px;" alt="图片描述">
  <figcaption><strong>Figure 4.11: Histograms of predicted random intercepts and slopes</strong></figcaption>
</figure>

The histograms in figure 4.11 look approximately normal although the one for the slopes is perhaps a little positively skewed. It should be noted, however, that moderate nonnormality of random effects can easily be missed because EB predictions tend to be closer to normal than the true random effects.

It is also useful to look at the bivariate distribution of the predicted random intercepts and slopes by using a scatterplot, or to display such a scatterplot together with the two histograms:

```
scatter ebs ebi if pickone==1, saving(yx, replace)    ///
   xtitle("Random intercept") ytitle("Random slope")  ///
   ylabel(, nogrid)

histogram ebs if pickone==1, freq horizontal saving(hy, replace) normal ///
    yscale(alt) ytitle(" ") fxsize(35) ylabel(, nogrid)

histogram ebi if pickone==1, freq saving(hx, replace) normal ///
    xscale(alt) xtitle(" ") fysize(35) ylabel(, nogrid)

graph combine hx.gph yx.gph hy.gph, hole(2) imargin(0 0 0 0)
```

Here the scatterplot and histograms are first plotted separately and then combined using the **``graph combine``** command. In the first histogram command, the **``horizontal``** option is used to produce a rotated histogram of the random slopes. In the histogram commands, the **``yscale(alt)``** and **``xscale(alt)``** options are used to put the corresponding axes on the other side, and the **``normal``** option is used to overlay normal density curves. The **``fysize(35)``** and **``fxsize(35)``** options change the aspect ratios of the histograms, making them more flat so that they use up a smaller portion of the combined graph. Finally, in the **``graph combine``** command, the graphs are listed in lexicographic order, the **``hole(2)``** option denotes that there should be a hole in the second position—that is, the top-right corner—and the **``imargin(0 0 0 0)``** option reduces the space between the graphs. The resulting graph is shown in figure 4.12.

After estimation with `mixed`, we obtain the predicted level-1 residuals,

$$\widetilde \epsilon_{ij} = y_{ij}-(\widehat\beta_1+\widehat\beta_2x_{ij}+\widetilde\zeta_{1j}+\widetilde\zeta_{2j}x_{ij})$$

by using

```
predict res1, residuals
```

We plot the residuals by using the following command, which produces the graph in figure 4.13:

```
histogram res1, normal xtitle(Predicted level-1 residuals)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.26.png" style="width:600px;height:400px;" alt="图片描述">
  <figcaption><strong>Figure 4.13: Histogram of predicted level-1 residuals</strong></figcaption>
</figure>

To obtain standardized level-1 residuals, use the **``rstandard``** option in the **``predict``** command after estimation using **``mixed``**.

### 8.5 Inferences for individual schools

Random-intercept predictions $\tilde{\gamma}_{1 j}$ are sometimes viewed as measures of institutional performance—in the present context, how much value the schools add for children with LRT scores equal to 0 (the mean). However, we may not have adequately controlled for covariates correlated with achievement that are outside the control of the school, such as student SES. Furthermore, the model assumes that the random intercepts are uncorrelated with the LRT scores, so if schools with higher mean LRT scores tend to add more value, their value added would be underestimated. Nevertheless, predicted random intercepts shed some light on the research question: Which schools are most effective for children with $LRT=0$ ?

It does not matter whether we add the predicted fixed part of the model because the ranking of schools is not affected by this.

Returning to the question of comparing the schools’ effectiveness for children with LRT scores equal to 0, we can plot the predicted random intercepts with approximate 95% confidence intervals based on comparative standard errors (see section $2.11.3)$ . We recommend fitting the model by REML before using `predict` in order to obtain estimated comparative standard errors that take uncertainty in the estimated regression coefficients into account:

```
quietly mixed gcse lrt || school: lrt, covariance(unstructured) reml
predict slope1 inter1, reffects reses(slope_se inter_se)
```

Here we only need **inter_se**. We first produce ranks for the schools in ascending order of the random-intercept predictions **inter1**:

```
gsort + inter1 -pickone
generate rank = sum(pickone)
```

Here the **``gsort``** command is used to sort in ascending order of **``inter1``** (indicated by **``+ inter1``**) and, within **``inter1``**, in descending order of **``pickone``** (indicated by **``- pickone``**). The **``sum()``** function forms the cumulative sum, so the variable **``rank``** increases by 1 every time a new school with a higher value of **``inter1``** is encountered. Before producing the graph, we generate a variable, **``labpos``**, for the vertical positions in the graph where the school identifiers should go:

```
generate labpos = inter1 + 1.96*inter_se + .5
```

We are now ready to produce a so-called *caterpillar plot* :

```
serrbar inter1 inter_se rank if pickone==1, addplot(scatter labpos rank,   ///
  mlabel(school) msymbol(none) mlabpos(0)) scale(1.96) xtitle(Rank)        ///
  ytitle(Prediction) legend(off)
```

The school labels were added to the graph by superimposing a scatterplot onto the error bar plot with the **``addplot()``** option, where the vertical positions of the labels are given by the variable **``labpos``**. The resulting caterpillar plot is shown in figure 4.14.

1. **随机截距预测（Random-intercept predictions）**

随机截距预测（$\tilde{\gamma}_{1j}$）是一种统计模型预测，用来衡量学校对孩子们的“增值”。在这个上下文中，我们关心的是学校对LRT成绩为0（即平均值）的孩子们的增值。

* **公式分解**

随机截距预测的公式通常是：

$ \tilde \gamma_{1j} = \beta_0 + b_{0j} $

- $\beta_0$ 是固定效应，表示所有学校的平均增值。
- $b_{0j}$ 是随机效应，表示第j所学校相对于平均值的增值。

2. **控制变量**

在实际应用中，我们可能需要控制一些与学生成绩相关的变量，比如学生的社会经济地位（SES）。如果不控制这些变量，那么我们的预测可能会受到这些未控制变量的影响。

3. **模型假设**

模型假设随机截距与LRT成绩是独立的。如果这个假设不成立，比如如果成绩较高的学校倾向于增加更多的价值，那么这些学校的预测值可能会被低估。

4. **预测随机截距**

尽管存在上述问题，预测的随机截距仍然可以为我们提供一些关于哪些学校对LRT=0的学生最有效。

5. **固定部分的影响**

在比较学校的有效性时，我们不需要考虑模型的固定部分，因为它不会影响学校的排名。

6. **预测随机截距的可视化**

我们可以通过绘制预测的随机截距和95%置信区间来可视化学校的表现。这里使用的是混合效应模型（mixed model），并且使用限制性最大似然估计（REML）来拟合模型。

7. **Stata命令**

接下来是Stata命令的解释：

- `mixed gcse lrt || school: lrt, covariance(unstructured) reml`：这是一个混合效应模型的命令，其中`gcse`是因变量，`lrt`是固定效应，`school`是随机效应，`covariance(unstructured)`表示随机效应的协方差结构是无结构的，`reml`表示使用限制性最大似然估计。

- `predict slope1 inter1, reffects reses(slope_se inter_se)`：这个命令用来预测随机斜率（slope1）和随机截距（inter1），以及它们的标准误差（slope_se和inter_se）。

- `gsort + inter1 -pickone`：这个命令用于根据`inter1`的值对数据进行排序，`+`表示升序，`-`表示降序。

- `generate rank = sum(pickone)`：这个命令生成一个新变量`rank`，它是根据`pickone`变量的累计和来计算的。

- `generate labpos = inter1 + 1.96*inter_se + .5`：这个命令生成一个新变量`labpos`，用于确定学校标识符在图表中的垂直位置。

8. **绘制图表**

最后，使用`serrbar`命令绘制误差棒图，`scatter`命令绘制散点图，并且使用`mlabel(school)`来标记学校名称。

* **计算置信区间**

置信区间是通过以下公式计算的：

$ \text{置信区间} = \text{预测值} \pm Z \times \text{标准误差} $

其中 `Z` 是正态分布的临界值，对于95%置信区间，`Z` 通常是1.96。

在我们的例子中，预测值是 `inter1`，标准误差是 `inter_se`。所以我们计算 `labpos` 如下：

```stata
generate labpos = inter1 + 1.96 * inter_se + .5
```

这里我们加上1.96倍的标准误差来得到置信区间的上界，然后再加上0.5以确保标签位于误差棒的上方。

* **8.2 绘制毛毛虫图**

最后，我们使用 `serrbar` 命令绘制毛毛虫图，它将显示每个学校的预测值和置信区间，并且学校的名字会显示在置信区间的顶部。

* **例子**

假设我们有以下数据：
    
  | 学校 | 随机截距预测值（inter1） | 标准误差（inter_se） | 排名（rank） |
  |------|-------------------------|---------------------|--------------|
  | A    | 0.5                     | 0.1                 | 3            |
  | B    | 0.3                     | 0.2                 | 5            |
  | C    | 0.7                     | 0.15                | 1            |
  | D    | 0.4                     | 0.05                | 4            |
  | E    | 0.6                     | 0.2                 | 2            |
    
我们首先计算每个学校的置信区间上界：

- 学校A：`0.5 + 1.96 * 0.1 = 0.756`
- 学校B：`0.3 + 1.96 * 0.2 = 0.756`
- 学校C：`0.7 + 1.96 * 0.15 = 1.094`
- 学校D：`0.4 + 1.96 * 0.05 = 0.478`
- 学校E：`0.6 + 1.96 * 0.2 = 0.956`

然后我们绘制毛毛虫图，每个学校的预测值由一个条形表示，条形的两端是置信区间，学校的名字显示在置信区间的顶部。

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.27.png" style="width:600px;height:400px;" alt="图片描述">
  <figcaption><strong>Figure 4.14: Caterpillar plot of random-intercept predictions and approximate 95% confidence intervals versus ranking (school identifiers shown on top of confidence intervals)</strong></figcaption>
</figure>

The interval for school 48 is particularly wide because there are only two students from this school in the dataset. It is clear from the large confidence intervals that the rankings are not precise and that perhaps only a coarse classification into poor, medium, and good schools can be justified.

An alternative method for producing a caterpillar plot is to first generate the confidence limits **``lower``** and **``upper``**,

```
generate lower = inter1 - 1.96*inter_se
generate upper = inter1 + 1.96*inter_se
```

and then use the **`rcap`** plot type to produce the intervals:

```
twoway (rcap lower upper rank, blpatt(solid) lcol(black))       ///
  (scatter inter1 rank)                                         ///
  (scatter labpos rank, mlabel(school) msymbol(none) mlabpos(0) ///
           mlabcol(black) mlabsiz(medium)),                     ///
  xtitle(Rank) ytitle(Prediction) legend(off)                   ///
  xscale(range(1 65)) xlabel(1/65) ysize(1)
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.29.png" style="width:600px;height:400px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

We could also produce similar plots for children with different values $x^0$ of the LRT scores:

$$\widehat\beta_1+\widehat\beta_2x^0+\widetilde\zeta_{1j}+\widetilde\zeta_{2j}x^0$$

For instance, in a similar application, Goldstein et al. (2000) substitute the 10th percentile of the intake measure to compare school effectiveness for poorly performing children. (To obtain confidence intervals for different values of $x^0$ requires posterior correlations that can be obtained by **``gllamm``**; see the gllamm companion.)

## 9 Two-stage model formulation

In this section, we describe an alternative way of specifying random-coefficient models that is popular in some areas such as education (for example, Raudenbush and Bryk 2002). As shown below, models are specified in two stages (for levels 1 and 2), necessitating a distinction between level-1 and level-2 covariates. Many people find this formulation helpful for interpreting and specifying models. Identical models can be formulated using either the approach discussed up to this point or the two-stage formulation.

To express the random-coefficient model by using a two-stage formulation, Raudenbush and Bryk (2002) specify a level-1 model:

$$y_{ij} = \beta_{oj} + \beta_{1j}x_{ij} + r_{ij}$$

where the intercept $\beta_{oj}$  and slope $\beta_{1j}$ are school-specific coefficients. Their level-2 models have these coefficients as responses:
<!--
$$\mathbf{T} = \left[\begin{array}{cc}\tau_{00}&\tau_{01}\\\tau_{10}&\tau_{11}\end{array}\right],\quad\tau_{10}=\tau_{01}$$
-->
$$\beta_{oj} = \gamma_{00} + u_{oj}$$  

$$\beta_{1j} = \gamma_{10} + u_{1j}$$   

Sometimes the first of these level-2 models is referred to as a "means as outcomes" or "intercepts as outcomes" model, and the second as a "slopes as outcomes" model. It is typically assumed that given the covariate(s), the residuals or disturbances $u_{oj}$  and $u_{1j}$ in the level-2 model have a bivariate normal distribution with 0 mean and covariance matrix 

$$T = [ \tau_{00} \quad \tau_{01} ] , τ_{10} = τ_{01}  $$

The level-2 models cannot be fit on their own because the school-specific coefficients $\beta_{oj}$  and $\beta_{1j}$ are not observed. Instead, we must substitute the level-2 models into the level-1 model to obtain the reduced-form model for the observed responses, $y_{ij}$:

$$\begin{array}{rcl}y_{ij}&=&\underbrace{\gamma_{00}+u_{0j}}_{\beta_{0j}}+\underbrace{(\gamma_{10}+u_{1j})}_{\beta_{1j}}x_{ij}+r_{ij}\end{array}$$

$$\begin{array}{rcl}=&\underbrace{\gamma_{00}+\gamma_{10}x_{ij}}_{\text{fixed}}+\underbrace{u_{0j}+u_{1j}x_{ij}+r_{ij}}_{\text{random}}\end{array}$$

$$\equiv\quad\beta_1+\beta_2x_{ij}\quad+\zeta_{1j}+\zeta_{2j}x_{ij}+\epsilon_{ij}$$

In the reduced form, the fixed part is usually written first, followed by the random part. As shown in the last line of the equation above, we can return to our previous notation by defining $\beta_1 = \gamma_{00},\beta_2 = \gamma_{10},\zeta_{1j} = u_{0j}$, $\zeta_{2j} = u_{1j}$, and $\varepsilon_{ij} = r_{ij}$. The above model is thus equivalent to the model in $(4.1)$.

The level-1 model contains only level-1 covariates (that vary between units within clusters). Any level-2 covariates (that do not vary within clusters) are included in the level-2 models. For instance, we could include dummy variables for type of school: $w_{1j}$ for boys-only schools and $w_{2j}$ for girls-only schools, with mixed schools as the reference category. If we include these dummy variables in the model for the random intercept,

$$\begin{matrix}\beta_{0j}&=&\gamma_{00}+\gamma_{01}w_{1j}+\gamma_{02}w_{2j}+u_{0j}\end{matrix}$$

the reduced form becomes

$$\begin{array}{rcl}y_{ij}&=&\underbrace{\gamma_{00}+\gamma_{01}w_{1j}+\gamma_{02}w_{2j}+u_{0j}}_{\beta_{0j}}+\underbrace{(\gamma_{10}+u_{1j})}_{\beta_{1j}}x_{ij}+r_{ij}\\&=&\underbrace{\gamma_{00}+\gamma_{01}w_{1j}+\gamma_{02}w_{2j}+\gamma_{10}x_{ij}}_{\text{fixed}}+\underbrace{u_{0j}+u_{1j}x_{ij}+r_{ij}}_{\text{random}}\end{array}$$

If we also include the dummy variables for type of school in the model for the random slope,

$$\begin{matrix}\beta_{1j}&=&\gamma_{10}+\gamma_{11}w_{1j}+\gamma_{12}w_{2j}+u_{1j}\end{matrix}$$

we obtain so-called cross-level interactions between covariates varying at different levels—w_1j by x_ij as well as w_2j by x_ij—in the reduced form

<!--
$$\begin{array}{rcl}y_{ij}&=&\underbrace{\gamma_{00}+\gamma_{01}w_{1j}+\gamma_{02}w_{2j}+u_{0j}}_{\beta_{0j}}+\underbrace{(\gamma_{21}+\gamma_{22}w_{2j}+\gamma_{23}w_{3j}+u_{1j})}_{\beta_{1j}} x_{ij}+r_{ij}\\&=&\underbrace{\gamma_{00}+\gamma_{01}w_{1j}+\gamma_{02}w_{2j}+\gamma_{10}x_{ij}+\gamma_{11}w_{1j}x_{ij}+\gamma_{12}w_{2j}x_{ij}}_{\mathrm{fixed}}+\underbrace{u_{0j}+u_{1j}x_{ij}+r_{ij}}_{\mathrm{random}}\end{array}$$
-->

The effect of lrt now depends on the type of school, with $\gamma_{11}$ representing the additional effect of **lrt** on **gcse** for boys-only schools compared with mixed schools and representing the additional effect for girls-only schools compared with mixed schools.




### 第一部分：理解两阶段模型的基本结构
#### Level-1模型（个体层面）
首先，我们来看Level-1模型，也就是个体层面的模型。这个模型描述的是个体（比如学生）的结果（比如考试成绩）如何受到个体层面变量（比如学习时间）的影响。

公式是这样的：
$$ y_{ij} = \beta_{oj} + \beta_{1j}x_{ij} + r_{ij} $$
这里：
- $ y_{ij} $ 表示第 $ i $ 个学生在第 $ j $ 所学校的考试成绩。
- $ \beta_{oj} $ 是第 $ j $ 所学校的截距，可以理解为如果 $ x_{ij} $ 为0时，学生的平均成绩。
- $ \beta_{1j} $ 是第 $ j $ 所学校的斜率，表示学习时间每增加一个单位，学生成绩的变化量。
- $ x_{ij} $ 是学生层面的变量，比如学习时间。
- $ r_{ij} $ 是随机误差项，表示除了模型中考虑的因素外，其他因素对学生成绩的影响。
#### Level-2模型（学校层面）
然后，我们来看Level-2模型，也就是学校层面的模型。这个模型描述的是学校特有的截距和斜率是如何受到学校层面变量（比如学校类型）的影响。

公式是这样的：

$$ \beta_{oj} = \gamma_{00} + u_{oj} $$
$$ \beta_{1j} = \gamma_{10} + u_{1j} $$

这里：
- $ \beta_{oj} $ 和 $ \beta_{1j} $ 是从Level-1模型中来的，现在被视为响应变量，也就是我们想要解释的变量。
- $ \gamma_{00} $ 和 $ \gamma_{10} $ 是固定效应，它们是所有学校共有的效应，可以理解为所有学校的平均截距和斜率。
- $ u_{oj} $ 和 $ u_{1j} $ 是随机效应，表示每个学校特有的截距和斜率的偏离。
#### 随机效应的分布
我们假设随机效应 $ u_{oj} $ 和 $ u_{1j} $ 服从二元正态分布，均值为0，协方差矩阵为 $ T $：
$ T = [ \tau_{00} \quad \tau_{01} ] , \tau_{10} = \tau_{01} $
这意味着 $ u_{oj} $ 和 $ u_{1j} $ 之间的相关性是 $ \tau_{01} $。
### 第二部分：将Level-2模型代入Level-1模型
由于我们不能直接观察到学校特有的系数 $ \beta_{oj} $ 和 $ \beta_{1j} $，我们需要将Level-2模型代入Level-1模型中，以得到观察到的响应 $ y_{ij} $ 的简化形式模型。

公式是这样的：
$$ y_{ij} = \gamma_{00} + u_{0j} + (\gamma_{10} + u_{1j})x_{ij} + r_{ij} $$
这可以进一步分解为固定部分和随机部分：
$$ y_{ij} = (\gamma_{00} + \gamma_{10}x_{ij}) + (u_{0j} + u_{1j}x_{ij} + r_{ij}) $$
这里：
- $ \gamma_{00} + \gamma_{10}x_{ij} $ 是固定部分，表示所有学校共有的效应。
- $ u_{0j} + u_{1j}x_{ij} + r_{ij} $ 是随机部分，表示每个学校特有的效应和其他随机因素。
#### 引入Level-2协变量
如果我们在模型中引入学校类型的虚拟变量（比如 $ w_{1j} $ 表示男校，$ w_{2j} $ 表示女校），那么Level-2模型会变得更加复杂，但基本思想是相同的。我们会增加一些项来表示不同类型学校的影响。

公式是这样的：
$$ \beta_{0j} = \gamma_{00} + \gamma_{01}w_{1j} + \gamma_{02}w_{2j} + u_{0j} $$

代入Level-1模型后，我们得到：
$$ y_{ij} = (\gamma_{00} + \gamma_{01}w_{1j} + \gamma_{02}w_{2j} + \gamma_{10}x_{ij}) + (u_{0j} + u_{1j}x_{ij} + r_{ij}) $$

这里：
- $ \gamma_{00} + \gamma_{01}w_{1j} + \gamma_{02}w_{2j} + \gamma_{10}x_{ij} $ 是固定部分，表示不同类型学校的平均效应。
- $ u_{0j} + u_{1j}x_{ij} + r_{ij} $ 是随机，表示每个学校特有的效应和其他随机因素。


### 第三部分：模型的实际应用
#### 模型的含义

我们先来回顾一下，我们的模型现在看起来是这样的：

$$ y_{ij} = (\gamma_{00} + \gamma_{01}w_{1j} + \gamma_{02}w_{2j} + \gamma_{10}x_{ij} + \gamma_{11}w_{1j}x_{ij} + \gamma_{12}w_{2j}x_{ij}) + (u_{0j} + u_{1j}x_{ij} + r_{ij}) $$
这个模型告诉我们，学生的考试成绩 $ y_{ij} $ 不仅受到个体层面变量 $ x_{ij} $（比如学习时间）的影响，还受到学校类型的影响。更具体地说：
- $ \gamma_{00} $ 是混合学校（作为参考类别）的平均截距。
- $ \gamma_{01} $ 和 $ \gamma_{02} $ 分别表示男校和女校相比于混合学校在截距上的差异。
- $ \gamma_{10} $ 是学习时间对所有学校学生成绩的平均影响。
- $ \gamma_{11} $ 和 $ \gamma_{12} $ 表示男校和女校学生学习时间对成绩影响的额外增加量，与混合学校相比。
#### 随机效应
- $ u_{0j} $ 表示第 $ j $ 所学校的截距与平均截距的偏差。
- $ u_{1j} $ 表示第 $ j $ 所学校的学习时间对成绩影响的斜率与平均斜率的偏差。
- $ r_{ij} $ 是其他未观察到的因素对第 $ i $ 个学生在第 $ j $ 所学校的成绩的影响。

### 第四部分：模型的解释和应用
#### 解释固定效应
固定效应部分告诉我们，平均来看，不同类型的学校和学习时间如何影响学生的考试成绩。例如，如果 $ \gamma_{11} $ 是正的，这意味着在控制了其他因素后，男校学生的学习时间对成绩的正向影响大于混合学校。同样，$ \gamma_{12} $ 告诉我们女校学生的情况。
#### 解释随机效应
随机效应部分告诉我们，即使在考虑了固定效应之后，不同学校的学生成绩仍然会有所差异。这些差异可能由于未观察到的学校特定因素（如学校资源、教师质量等）或随机误差（如测量误差、未包括在模型中的其他变量）造成。
#### 模型的应用
这种模型在教育研究中非常有用，因为它允许我们探索不同学校环境如何影响学生的学习成果，并且可以考虑到学校之间的差异。例如，我们可以用这个模型来比较不同类型学校（男校、女校、混合学校）的教育效果，或者研究学校资源如何影响学生的学习时间与成绩之间的关系。
#### 进一步的分析
在实际应用中，我们可能会对模型进行进一步的细化，比如考虑更多的学校层面或学生层面的变量，或者探索不同变量之间的交互作用。此外，我们还可以对模型的假设进行检验，比如随机效应的分布假设，以及模型是否满足其他统计假设。
希望这个详细的解释能帮助你更好地理解随机系数模型的两阶段表述方法。

The effect of **lrt** now depends on the type of school, with $\gamma_{11}$ representing the additional effect of **lrt** on **gcse** for boys-only schools compared with mixed schools and representing the additional effect for girls-only schools compared with mixed schools.

For estimation in `mixed`, it is necessary to convert the two-stage formulation to the reduced form because the fixed part of the model is specified first, followed by the random part of the model. Using factor variables in mixed, the command is **`mixed`**.

```

mixed gcse i.schgend##c.lrt || school: lrt, covariance(unstructured) mle ///
   stddeviations vce(robust)
```
    
    Performing EM optimization ...
    
    Performing gradient-based optimization: 
    Iteration 0:  Log pseudolikelihood = -13998.825  
    Iteration 1:  Log pseudolikelihood = -13998.825  
    
    Computing standard errors ...
    
    Mixed-effects regression                             Number of obs    =  4,059
    Group variable: school                               Number of groups =     65
                                                         Obs per group:
                                                                      min =      2
                                                                      avg =   62.4
                                                                      max =    198
                                                         Wald chi2(5)     = 930.12
    Log pseudolikelihood = -13998.825                    Prob > chi2      = 0.0000
    
                                       (Std. err. adjusted for 65 clusters in school)
    ---------------------------------------------------------------------------------
                    |               Robust
               gcse | Coefficient  std. err.      z    P>|z|     [95% conf. interval]
    ----------------+----------------------------------------------------------------
            schgend |
              boys  |       0.85       0.96     0.89   0.376        -1.04        2.75
             girls  |       2.43       0.84     2.91   0.004         0.79        4.07
                    |
                lrt |       0.57       0.02    24.24   0.000         0.53        0.62
                    |
      schgend#c.lrt |
              boys  |      -0.02       0.05    -0.43   0.671        -0.13        0.08
             girls  |      -0.03       0.05    -0.60   0.550        -0.13        0.07
                    |
              _cons |      -1.00       0.55    -1.80   0.072        -2.08        0.09
    ---------------------------------------------------------------------------------
    
    ------------------------------------------------------------------------------
                                 |               Robust           
      Random-effects parameters  |   Estimate   std. err.     [95% conf. interval]
    -----------------------------+------------------------------------------------
    school: Unstructured         |
                         sd(lrt) |       0.12       0.02          0.08        0.18
                       sd(_cons) |       2.80       0.30          2.26        3.46
                 corr(lrt,_cons) |       0.60       0.14          0.26        0.80
    -----------------------------+------------------------------------------------
                    sd(Residual) |       7.44       0.13          7.20        7.69
    ------------------------------------------------------------------------------

Here mixed schools are the reference category for **schgend** to which boys-only schools and girls-only schools are compared. We see that, when **lrt** is 0, students from girls-only schools perform significantly better at the 5% level than students from mixed schools, whereas students from boys-only schools do not perform significantly better than students from mixed schools. The effect of lrt does not differ significantly between boys-only schools and mixed schools or between girls-only schools and mixed schools. The estimates and the corresponding parameters in the two-stage formulation are given under “Rand. coefficient & level-2 covariates” in the last three columns of table 4.1.

Although equivalent models can be specified using either the reduced form (used by `mixed`) or the two-stage (used by the HLM software of Raudenbush et al. [2019]) formulation, in practice, model specification to some extent depends on the approach adopted. For instance, cross-level interactions are easily included using the two-stage specification in HLM, whereas same-level interactions must be created outside the program. Papers using HLM therefore tend to include more cross-level interactions and fewer same-level interactions. They also tend to include more random coefficients than papers using, for instance, Stata because the level-2 models look odd without residuals.

## Some warnings about random-coefficient models

### 10.1 Meaningful specification

It rarely makes sense to include a random slope if there is no random intercept, just like interactions between two covariates usually do not make sense without including the covariates themselves in standard regression models. Similarly, it is seldom sensible to include a random slope without including the corresponding fixed slope because it is usually strange to allow the slope to vary randomly but constrain its population mean to 0.

It is generally not a good idea to include a random coefficient for a covariate that does not vary at a lower level than the random coefficient itself. For example, in the inner-London-schools data, it does not make sense to include a school-level random slope for type of school because type of school does not vary within schools. Because we cannot estimate the effect of type of school for individual schools, it also appears impossible to estimate the variability of the effect of type of school between schools. However, level-2 random coefficients of level-2 covariates can be used to construct heteroskedastic random intercepts (see section 7.5.2).



* **第一部分：为什么需要随机截距**

在随机系数模型中，我们经常想要了解个体层面的变量（比如学生的学习时间）是如何影响结果的（比如考试成绩）。但是，这种影响可能在不同的群体（比如不同的学校）之间有所不同。因此，我们引入了随机斜率来捕捉这种差异。

然而，随机斜率通常需要与随机截距一起使用。原因如下：

1. **互动效应**：如果我们只包含随机斜率而不包含随机截距，那么我们实际上是在允许斜率随机变化，但假设所有群体的截距都是相同的。这就像我们说，每个学校的学生学习时间对成绩的影响可能不同，但所有学校的平均成绩都是相同的，这通常没有意义。

2. **模型的完整性**：包含随机截距可以让模型更完整。随机截距可以捕捉到每个群体（比如学校）的平均效应，而随机斜率则可以捕捉到群体间斜率的变化。这样，我们就可以更全面地了解不同群体的特性。

* **第二部分：为什么需要固定斜率**

同样，当我们在模型中包含随机斜率时，通常也需要包含相应的固定斜率。原因如下：

1. **群体平均效应**：固定斜率代表了所有群体的平均效应。如果我们只允许斜率随机变化，而不设定一个固定的平均值，那么我们就没有一个基准来比较不同群体的效应。

2. **模型的可解释性**：固定斜率的存在使得模型更容易解释。我们可以清楚地看到，平均来看，个体层面的变量是如何影响结果的。

* **第三部分：何时不包含随机系数**

有时候，我们不应该在模型中包含某个变量的随机系数。这通常发生在以下情况：

1. **变量不变化**：如果一个变量在随机系数所定义的层次上不变化，那么包含它的随机系数就没有意义。比如，在学校层面的模型中包含学校的类型作为随机斜率就没有意义，因为学校类型在每个学校内是固定的。

2. **无法估计效应**：如果一个变量在个体层面不变化，那么我们无法估计它对个体的影响，因此也就无法估计它在群体间的变异性。

* **第四部分：如何构建异方差随机截距**

尽管我们不能在学校层面为学校类型这样的变量包含随机斜率，但我们可以使用学校层面的变量（比如学校类型）来构建异方差随机截距。这意味着不同学校的平均成绩可以有不同的方差，从而允许模型捕捉到更多学校间的差异。

* **总结**

- 随机斜率通常需要与随机截距一起使用，以捕捉群体间的平均效应和斜率的变化。
- 固定斜率提供了一个基准，使得模型更易于解释。
- 如果一个变量在个体层面不变化，那么包含它的随机系数是没有意义的。
- 我们可以使用群体层面的变量来构建异方差随机截距，以捕捉更多群体间的差异。


### 10.2 Many random coefficients

It may be tempting to allow many different covariates to have random slopes. However, the number of parameters for the random part of the model increases rapidly with the number of random slopes because there is a variance parameter for each random effect (intercept or slope) and a covariance parameter for each pair of random effects. If there are $k$ random slopes plus one random intercept, then there are $(k+1)(k+2)/2+1$ parameters in the random part (for example, $k=3$ gives 11 parameters).

Another problem is that clusters may not provide much information on cluster-specific slopes and hence on the slope variance either if the clusters are small, or if it does not vary much within clusters or varies only in a small number of clusters. Perhaps a useful rule is to consider the random part of the model (ignoring the fixed part) and replace the random effects with fixed regression coefficients. It should be possible (even if not very sensible) to fit the resulting model to a good number of clusters (say, 20 or more), with some error degrees of freedom. Note, however, that it does not matter if some of the clusters have insufficient data as long as there are an adequate number of clusters that do have sufficient data. It is never a good idea to discard clusters merely because they provide little information on some of the parameters of the model.

In general, it makes sense to allow for more flexibility in the fixed part of the model than in the random part. For instance, the fixed part of the model may include a dummy variable for each occasion in longitudinal data, but in the random part of the model it may be sufficient to allow for a random intercept and a random slope of time, keeping in mind that in this case it is only assumed that the deviation from the population-average curve is linear in time, not that the relationship itself is linear. See section 7.3 for examples of modeling a nonlinear relationship in the fixed part of the model but not in the random part.

The overall message is that random slopes should be included only if strongly suggested by the subject-matter theory related to the application and if the data provide sufficient information.

好的，我们来一步步理解为什么在随机效应模型中不能随意增加太多的随机斜率，以及如何决定是否应该包含随机斜率。我们会分两部分来讲解，首先从参数数量和数据信息量的角度来理解，然后讨论模型的灵活性和随机斜率的适用条件。

* **第一部分：参数数量和数据信息量**

#### 1. 随机斜率的参数数量

当我们在模型中为不同的协变量引入随机斜率时，我们实际上是在允许这些协变量的影响在不同的群体（如不同的学校或班级）之间有所变化。但是，这样做会增加模型中随机部分的参数数量。

假设我们有 $ k $ 个随机斜率和一个随机截距，那么随机部分的参数数量将是 \((k+1)(k+2)/2 + 1\) 个。这里的参数包括每个随机效应的方差参数和每对随机效应之间的协方差参数。

**例子**：
如果我们有3个随机斜率（$ k=3 $），那么随机部分的参数数量将是：
$ \frac{(3+1)(3+2)}{2} + 1 = \frac{4 \times 5}{2} + 1 = 11 $
这意味着我们需要估计11个参数，包括每个随机斜率的方差、每对随机斜率之间的协方差，以及随机截距的方差。

#### 2. 数据对随机斜率的支撑

另一个问题是，如果群体（如学校或班级）数量不多，或者在群体内部协变量的变化不大，或者只在少数群体中变化，那么我们可能没有足够的信息来估计群体特定的斜率，以及斜率的方差。

一个有用的经验法则是，考虑模型的随机部分（忽略固定部分），并将随机效应替换为固定回归系数。即使不是很合理，也应该能够将得到的模型拟合到足够多的群体上（比如说20个或更多），并且有一定的误差自由度。

**例子**：
假设我们有30所学校的数据，我们想要估计每个学校学生学习时间对成绩的影响。如果大多数学校的学生学习时间对成绩的影响变化不大，那么我们可能没有足够的信息来估计每个学校的随机斜率。在这种情况下，我们可能需要考虑只使用固定斜率，或者使用较少的随机斜率。

* **第二部分：模型的灵活性和随机斜率的适用条件**

#### 1. 模型的灵活性

通常，我们在模型的固定部分比在随机部分允许更多的灵活性。例如，在纵向数据中，固定部分可能包括每个时间点的虚拟变量，但在随机部分，可能只需要允许一个随机截距和一个随时间变化的随机斜率。

**例子**：
假设我们有学生在不同时间点的考试成绩数据。在固定部分，我们可能包括每个时间点的虚拟变量来捕捉不同时间点的平均成绩变化。但在随机部分，我们可能只需要允许一个随机截距和一个随时间变化的随机斜率，假设从总体平均曲线的偏差随时间线性变化。

#### 2. 随机斜率的适用条件

总的来说，只有在以下情况下才应该包含随机斜率：
1. **理论支持**：与应用相关的主题理论强烈建议包含随机斜率。
2. **数据支持**：数据提供了足够的信息来估计这些随机斜率。

**例子**：
如果我们研究的是不同学校的教学方法对学生成绩的影响，并且理论或先前的研究建议教学方法的影响在不同学校之间可能有所不同，那么我们可能会考虑在模型中包含教学方法的随机斜率。同时，如果数据中不同学校的教学方法确实有显著变化，那么我们就有理由包含随机斜率。

* **总结**

- 在模型中增加随机斜率会增加需要估计的参数数量。
- 如果群体数量不多或者群体内部协变量变化不大，我们可能没有足够的信息来估计随机斜率。
- 模型的固定部分可以比随机部分更灵活。
- 只有在理论和数据都支持的情况下，才应该包含随机斜率。

我们已经讨论了为什么在随机效应模型中不能随意增加太多的随机斜率，以及如何决定是否应该包含随机斜率。现在，我们继续深入探讨这个主题，特别是关于如何在实际中应用这些原则，并提供一些实际的指导。

* **第三部分：实际应用中的考虑**

#### 1. 模型的复杂性与解释性

当我们在模型中引入更多的随机斜率时，模型的复杂性会增加。这不仅使得模型更难以估计，也可能使得结果更难以解释。因此，我们需要在模型的复杂性和解释性之间找到平衡。

**例子**：
假设我们正在研究工作压力对员工健康的影响，并考虑引入部门作为随机效应。如果我们引入太多的随机斜率（比如每个部门的不同工作压力斜率），模型可能会变得过于复杂，难以向非专业的决策者解释。

#### 2. 数据的质量和数量

在决定是否包含随机斜率时，我们需要考虑数据的质量和数量。如果数据量不足或者数据质量不高，那么估计过多的随机斜率可能会导致模型估计不准确。

**例子**：
如果我们只有少数几个部门的数据，并且每个部门的数据量都很小，那么我们可能没有足够的信息来估计每个部门的随机斜率。在这种情况下，使用固定效应模型可能更为合适。

#### 3. 理论指导

在决定是否包含随机斜率时，理论指导是非常重要的。我们需要有理论基础来支持不同群体之间斜率的变化。

**例子**：
如果我们研究的是不同教学方法对学生成绩的影响，并且教育理论表明不同的教学方法对不同学生群体的效果可能不同，那么我们就有理由在模型中包含随机斜率。

* **第四部分：模型选择的指导原则**

#### 1. 从简单到复杂

在构建模型时，通常建议从简单模型开始，逐步增加复杂性。这样可以确保每个增加的随机斜率都有充分的理论和数据支持。

**例子**：
我们可以先从一个只包含随机截距的模型开始，然后逐步引入随机斜率，每次增加后都要检查模型的拟合度和参数的显著性。

#### 2. 信息准则

在模型选择过程中，我们可以使用信息准则（如赤池信息准则AIC或贝叶斯信息准则BIC）来帮助决定是否包含随机斜率。这些准则可以帮助我们在模型的拟合度和复杂性之间找到平衡。

**例子**：
如果我们发现包含某个随机斜率后，模型的AIC或BIC显著降低，那么这可能表明包含这个随机斜率是合理的。

#### 3. 交叉验证

在可能的情况下，使用交叉验证来评估模型的预测能力。如果包含某个随机斜率显著提高了模型的预测能力，那么这可能表明包含这个随机斜率是有益的。

**例子**：
我们可以将数据分为训练集和测试集，然后在训练集上拟合模型，并在测试集上评估模型的预测能力。如果包含某个随机斜率提高了模型在测试集上的预测准确度，那么这可能表明包含这个随机斜率是有益的。

* **总结**

在随机效应模型中引入随机斜率是一个需要谨慎考虑的决策。我们需要考虑参数数量、数据信息量、模型的复杂性与解释性、数据的质量和数量，以及理论指导。在实际应用中，我们应该从简单模型开始，逐步增加复杂性，并使用信息准则和交叉验证来指导模型选择。

希望这个详细的解释能帮助你更好地理解如何在实际中应用随机效应模型，并做出明智的模型选择。如果有任何不清楚的地方，或者你想要更多的例子，请随时告诉我。


### 10.3 Convergence problems

Convergence problems can manifest themselves in different ways. Either estimates are never produced, or standard errors are missing, or mixed produces messages such as “nonconcave”, “backed-up”, or “standard error calculation has failed”. Sometimes none of these things happen, but the confidence intervals for some of the correlations cover the full permissible range from to 1 (see sections **7.3** and **8.13.2** for examples).

Convergence problems can occur because the estimated covariance matrix “tries” to become negative definite, meaning, for instance, that variances try to become negative or correlations try to be greater than $1$ or less than $-1$ All the commands in Stata force the covariance matrix to be positive (semi)definite, and when parameters approach nonpermissible values, convergence can be slow or even fail. It may help to translate and rescale $x_{ij}$because variances and covariances are not invariant to these transformations. Often a better remedy is to simplify the model by removing some random slopes. Convergence problems can also occur because of lack of identification, and again, a remedy is to simplify the model.

However, before giving up on a model, it is worth attempting to achieve convergence by trying both the **`mle`** and the **`reml`** options, specifying the **`difficult`** option, trying the **`matlog`** option (which parameterizes the random part differently during maximization), or increasing the number of EM iterations by using either the **`emiterate()`** option or even the **`emonly`** option. It can also be helpful to monitor the iterations more closely by using **`trace`**, which displays the parameter estimates at the end of each iteration (unfortunately, not for the EM iterations). Lack of identification of a parameter might be recognized by that parameter changing wildly between iterations without much of a change in the log likelihood. Problems with a variance approaching 0 can be detected by noticing that the log-standard deviation takes on very large negative values.


#### 收敛问题的详细解释
#### a. 什么是协方差矩阵？
协方差矩阵是一个方阵，用于表示多个随机变量之间的协方差。对于两个随机变量 $X$ 和 $Y$，它们的协方差矩阵可能看起来像这样：

![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.30.png)

<!--
$$
\begin{bmatrix}
\text{Var}(X) & \text{Cov}(X, Y) \\
\text{Cov}(Y, X) & \text{Var}(Y)
\end{bmatrix}
$$
-->

其中，$\text{Var}(X)$ 是 $X$ 的方差，$\text{Var}(Y)$ 是 $Y$ 的方差，$\text{Cov}(X, Y)$ 是 $X$ 和 $Y$ 之间的协方差。
#### b. 为什么协方差矩阵不能是负定的？
在数学上，协方差矩阵必须是半正定的，这意味着它的所有特征值都必须是非负的。这是因为方差（协方差矩阵的对角元素）总是非负的，而协方差（协方差矩阵的非对角元素）表示变量之间的线性关系，它们的值被限制在 -1 和 1 之间。如果协方差矩阵试图变成负定的，那么它就会违反这些基本的统计属性。
#### c. 收敛问题的表现
收敛问题可能表现为：
- 无法产生估计值。
- 标准误差缺失。
- 出现错误信息，如“非凹形”、“阻塞”或“标准误差计算失败”。
- 相关性的置信区间覆盖了从 -1 到 1 的全部范围。
### 解决收敛问题的方法
#### a. 转换和重新缩放变量
有时候，变量的尺度和分布会影响模型的收敛。例如，如果一个变量的尺度非常大，它可能会主导模型的估计过程，导致其他变量的估计不稳定。通过转换（如对数转换）和重新缩放（如标准化或归一化）变量，我们可以改善模型的收敛性。
#### b. 简化模型
如果模型太复杂，可能会导致收敛问题。通过移除一些不必要的随机斜率或固定效应，我们可以简化模型，使其更容易收敛。
#### c. 使用不同的估计方法
- **`mle`**（最大似然估计）：这是一种常用的参数估计方法，它试图找到最大化似然函数的参数值。
- **`reml`**（限制性最大似然估计）：这种方法在估计模型参数时，对某些参数施加了限制，有时可以改善收敛性。
- **`difficult`** 选项：这个选项通常用于处理难以收敛的问题，它可能会改变算法的一些参数，使其更适合处理困难的问题。
- **`matlog`** 选项：这个选项在最大化过程中以不同的方式参数化随机部分，有时可以改善收敛性。
- **`emiterate()`** 和 **`emonly`** 选项：这些选项用于增加EM（期望最大化）算法的迭代次数，有时可以改善收敛性。
#### d. 监控迭代过程
使用 **`trace`** 选项可以监控迭代过程，它会在每次迭代结束时显示参数估计值。这有助于我们了解参数是如何随着迭代过程而变化的，从而判断模型是否正在收敛。
### 识别参数识别问题
- **参数变化大**：如果一个参数在迭代过程中变化很大，而对数似然值变化不大，这可能表明该参数无法被识别。
- **方差接近0**：如果一个方差接近0，可以通过观察对数标准差是否取非常大的负值来检测。
希望这个更详细的解释能帮助你更好地理解收敛问题及其解决方法。如果你还有任何疑问，或者需要进一步的解释，请随时告诉我。

好的，我们继续深入探讨收敛问题及其解决方法。

### 收敛问题的数学原理

#### a. 协方差矩阵的正定性

协方差矩阵的正定性是一个重要的数学属性。如果一个矩阵的所有特征值都是正的，那么这个矩阵就是正定的。如果所有特征值都是非负的，那么这个矩阵就是半正定的。在统计学中，协方差矩阵必须是半正定的，因为它代表了数据的方差和协方差，这些都是非负的。

#### b. 协方差矩阵的非正定性问题

当协方差矩阵试图变成负定的，这意味着模型的参数估计可能导致方差变成负数，或者相关系数超出[-1, 1]的范围。这在数学上是不可能的，因为：
- 方差是数据分散程度的度量，它必须是非负的。
- 相关系数是两个变量之间线性关系的度量，它的取值范围是[-1, 1]。

如果模型的参数估计导致协方差矩阵变成非正定的，那么模型的估计就是不稳定的，这就会导致收敛问题。

### 解决收敛问题的策略

#### a. 转换和重新缩放变量

转换和重新缩放变量是解决收敛问题的常用方法。例如：
- **对数转换**：如果变量的尺度非常大，对其进行对数转换可以减少尺度的影响。
- **标准化**：将变量转换为均值为0，标准差为1的分布，这有助于稳定模型的估计。
- **归一化**：将变量缩放到[0, 1]的范围内，这有助于处理不同尺度的变量。

#### b. 简化模型

如果模型太复杂，可能会导致收敛问题。简化模型的方法可能包括：
- **移除不必要的变量**：如果某些变量对模型的贡献不大，可以考虑移除它们。
- **减少随机效应**：在混合效应模型中，减少随机效应的数量可以简化模型。
- **固定效应**：将某些随机效应固定为特定的值，以简化模型。

#### c. 使用不同的估计方法

不同的估计方法可能对收敛问题有不同的敏感性。例如：
- **`mle`**：最大似然估计是一种常用的估计方法，但它可能对初始值和模型的复杂性敏感。
- **`reml`**：限制性最大似然估计对某些模型可能更稳定，特别是当模型包含不可估计的参数时。
- **`difficult`** 选项：这个选项可以改变算法的行为，使其更适合处理难以收敛的问题。

#### d. 增加迭代次数

增加迭代次数可以给算法更多的时间来寻找最优解。例如：
- **`emiterate()`** 选项：这个选项可以增加EM算法的迭代次数。
- **`emonly`** 选项：这个选项可以让算法只使用EM算法，而不使用其他方法。

#### e. 监控迭代过程

使用 **`trace`** 选项可以监控迭代过程，这有助于我们了解模型的收敛情况。例如：
- **参数的变化**：如果参数在迭代过程中变化很大，这可能表明模型还没有收敛。
- **对数似然值的变化**：如果对数似然值在迭代过程中变化不大，这可能表明模型已经收敛。

### 实际操作中的注意事项

在实际操作中，解决收敛问题可能需要尝试多种方法。例如，你可以尝试以下步骤：
1. **检查数据**：确保数据没有错误或异常值。
2. **转换和重新缩放变量**：尝试不同的转换和缩放方法。
3. **简化模型**：尝试移除不必要的变量或随机效应。
4. **尝试不同的估计方法**：使用 `mle`、`reml` 或其他估计方法。
5. **增加迭代次数**：使用 `emiterate()` 或 `emonly` 选项。
6. **监控迭代过程**：使用 `trace` 选项来监控模型的收敛情况。


### 10.4 Lack of identification

Sometimes random-coefficient models are simply not identified (or in other words, underidentified). As an important example, consider balanced data with clusters of size $n_{j}=2$ and with a covariate $x_{ij}$ taking the same two values $t_{1}=0$ and $t_{2}=1$ for each cluster (an example would be the peak-expiratory-flow data from chapter 2). A model with a random intercept, a random slope of $x_{ij}$, and a level-1 residual, all of which are normally distributed, is not identified in this case. This can be seen by considering the two distinct variances (for $i=1$ and $i=2$) and one covariance of the total residuals when $t_{1}=0$ and $t_{2}=1$:

$$\begin{matrix}\mathrm{Var}(\xi_{1j})&=&\psi_{11}+\theta\end{matrix}$$

$$\begin{matrix}\mathrm{Var}(\xi_{2j})&=&\psi_{11}+2\psi_{21}+\psi_{22}+\theta\end{matrix}$$

$$\begin{matrix}\mathrm{Cov}(\xi_{1j},\xi_{2j})&=&\psi_{11}+\psi_{21}\end{matrix}$$

The marginal distribution of $y_{ij}$ given the covariates is normal and therefore completely characterized by the fixed part of the model and these three model-implied moments (two variances and a covariance). However, the three moments are determined by four parameters of the random part ($\psi_{11}, \psi_{22}, \psi_{21}$, and $\theta$), so fitting the model-implied moments to the data would effectively involve solving three equations for four unknowns. The model is therefore not identified. We could identify the model by setting $\theta = 0$, which does not impose any restrictions on the covariance matrix (however, such a constraint is not allowed in mixed). The original model becomes identified if the covariate $x_{ij}$, which has a random slope, varies also between clusters because the model-implied covariance matrix of the total residuals then differs between clusters, yielding more equations to solve for the four parameters.

Still assuming that the random effects and level-1 residual are normally distributed, consider now the case of balanced data with clusters of size $n_j=3$ and with a covariate $x_{ij}$ taking the same three values $t_1, t_2,$ and $t_3$ for each cluster. An example would be longitudinal data with three occasions at times $t_1, t_2,$ and $t_3$. Instead of including a random intercept and a random slope of time, it may be tempting to specify a random-coefficient model with a random intercept and two random coefficients for the dummy variables for occasions two and three. In total, such a model would contain seven (co)variance parameters: six for the three random effects and one for the level-1 residual variance. Because the covariance matrix of the responses for the three occasions given the covariates only has six elements, it is impossible to solve for all unknowns. The same problem would occur when attempting to fit this kind of model for more than three occasions.

### 第一部分：模型识别的基本概念和例子
#### 1. 模型识别的含义
模型识别是指我们是否能够通过观测数据来唯一确定模型中的所有参数。如果参数不能被唯一确定，模型就是不可识别的。
#### 2. 模型不可识别的原因
模型不可识别通常是因为模型中的参数数量超过了数据所能提供的信息量。这就像我们有四个未知数，但只有三个方程，所以我们无法解出这四个未知数。
#### 3. 一个简单的例子
假设我们有一个模型，它包含随机截距、随机斜率，以及一级残差，它们都是正态分布的。我们有以下两个方差和一个协方差：
- $ \mathrm{Var}(\xi_{1j}) = \psi_{11} + \theta $
- $ \mathrm{Var}(\xi_{2j}) = \psi_{11} + 2\psi_{21} + \psi_{22} + \theta $
- $ \mathrm{Cov}(\xi_{1j}, \xi_{2j}) = \psi_{11} + \psi_{21} $

这里，$ \psi_{11} $、$ \psi_{21} $、$ \psi_{22} $ 和 $ \theta $ 是模型的随机部分的参数。我们有四个参数，但只有三个方程（两个方差和一个协方差）。这意味着我们不能唯一地解出这四个参数，因此这个模型是不可识别的。

为了帮助理解，我们可以将这些公式看作是关于这些参数的方程：
假设我们有四个未知数 $ a $、$ b $、$ c $ 和 $ d $，但我们只有三个方程：
1. $ 3a + 2b = 5 $
2. $ a + b + c = 3 $
3. $ 2a - b + 3c + d = 1 $

我们无法解出 $ a $、$ b $、$ c $ 和 $ d $ 的唯一值，因为我们的方程不够。
#### 4. 如何解决模型识别问题
解决模型识别问题的一种方法是减少模型的参数数量。例如，我们可以设定 $ \theta = 0 $，这样就减少了参数数量，使得模型变得可识别。但这种方法可能会对模型的协方差矩阵施加限制，有时候这是不可接受的。
### 第二部分：更复杂情况下的模型识别问题
#### 1. 更复杂的情况
现在，我们考虑一个更复杂的情况。假设我们有每个簇大小为3的平衡数据，以及一个在每个簇中取三个值 $ t_1, t_2, t_3 $ 的协变量 $ x_{ij} $。例如，这可以是三个不同时间点的纵向数据。
#### 2. 尝试建模
我们可能会尝试使用一个包含随机截距和两个随机系数（对应于后两个时间点的虚拟变量）的随机系数模型。这样的模型总共包含七个（协）方差参数：六个用于三个随机效应，一个用于一级残差方差。
#### 3. 模型不可识别的原因
问题在于，给定协变量的三个时间点的响应的协方差矩阵只有六个元素。这意味着我们没有足够的信息来解出所有的未知数。因此，这个模型也是不可识别的。
为了帮助理解，我们可以将这七个参数看作是七个未知数，但我们只有六个方程，因此无法解出这七个未知数的唯一值。
#### 4. 解决方案
为了解决这个问题，我们可以尝试改变模型的结构，例如，不包括随机斜率，而是包括随机截距和时间的随机系数。这样，模型的协方差矩阵就会在不同的簇之间有所不同，从而提供了更多的方程来解出参数。
例如，如果我们有四个时间点而不是三个，我们可能需要包括随机截距和三个随机系数（对应于后三个时间点的虚拟变量）。这样，我们就有了更多的方程来解出参数，从而使模型变得可识别。


让我们通过一个具体的数据分析案例来说明模型识别问题是如何影响模型解释的。
### 案例背景
假设我们有一个关于学生考试成绩的数据集，我们想要研究学生的学习时间（一个协变量 $x_{ij}$）如何影响他们的考试成绩（因变量 $y_{ij}$）。我们收集了多个学校（称为“簇”）的数据，每个学校有多个学生的学习时间和成绩。
### 模型设定
我们首先设定一个包含随机截距和随机斜率的混合效应模型，这个模型允许每个学校有不同的截距和斜率。模型可以表示为：

$$
 y_{ij} = (\beta_0 + u_{0j}) + (\beta_1 + u_{1j})x_{ij} + e_{ij} 
$$

其中：
- $y_{ij}$ 是第 $i$ 个学生在第 $j$ 所学校的考试成绩。
- $x_{ij}$ 是第 $i$ 个学生在第 $j$ 所学校的学习时间。
- $\beta_0$ 和 $\beta_1$ 是总体的截距和斜率。
- $u_{0j}$ 和 $u_{1j}$ 是第 $j$ 所学校的随机截距和随机斜率。
- $e_{ij}$ 是误差项。
### 模型识别问题
在实际分析中，我们可能会遇到模型识别问题。例如，如果我们的数据集中每个学校只有两个学生，且学习时间 $x_{ij}$ 只取两个值（0和1），那么我们的模型可能无法识别。这是因为模型的参数数量超过了数据所能提供的信息量。
具体来说，我们有四个参数（$\psi_{11}$、$\psi_{21}$、$\psi_{22}$ 和 $\theta$）需要估计，但我们只有三个方程（两个方差和一个协方差）：
- $\mathrm{Var}(\xi_{1j}) = \psi_{11} + \theta$
- $\mathrm{Var}(\xi_{2j}) = \psi_{11} + 2\psi_{21} + \psi_{22} + \theta$
- $\mathrm{Cov}(\xi_{1j}, \xi_{2j}) = \psi_{11} + \psi_{21}$

由于方程数量少于参数数量，我们无法唯一确定这些参数的值，这就是模型识别问题。
### 模型识别问题的影响
模型识别问题会影响我们对模型的解释。在上述案例中，如果我们不能识别模型，我们就无法准确地估计学习时间对考试成绩的影响，也无法确定不同学校之间的差异。这可能导致我们对教育政策或教学方法的效果做出错误的推断。
### 解决方案
为了解决这个问题，我们可以采取以下几种方法：
1. **增加数据**：增加每个学校的学生数量或学校数量，以提供更多的信息来估计模型参数。
2. **简化模型**：减少模型的复杂性，例如，只估计随机截距而不是随机斜率。
3. **使用不同的估计方法**：尝试不同的估计方法，如最大似然估计（MLE）或限制性最大似然估计（REML）。
通过这些方法，我们可以提高模型的识别能力，从而更准确地解释数据。在实际应用中，这可能意味着我们需要重新设计数据收集方案，或者在分析时做出一些妥协。
