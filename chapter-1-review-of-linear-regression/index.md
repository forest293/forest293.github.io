# [Multilevel  Longitudinal Models] Chapter 1 ：Review of linear regression



## 1 Introduction

The regression models considered here are essential building blocks for multilevel models. Although linear multilevel or mixed models for continuous responses are sometimes viewed from an ANOVA perspective, the regression perspective is beneficial because it is easily generalizable to binary and other types of responses. Furthermore, the Stata commands for multilevel modeling follow a regression syntax.

Even experienced regression modelers are likely to benefit from reading this chapter because it introduces our notation and terminology as well as Stata commands used in later chapters, including factor variables for specifying dummy variables and interactions within estimation commands instead of creating new variables.

## 2 Is there gender discrimination in faculty salaries? 

The primary purpose of these studies was to investigate whether evidence existed for gender inequity in faculty salaries at the university.

The data considered here are a subset of the data provided by DeMaris, comprising faculty members, excluding faculty from the Fireland campus, nonprofessors (instructors/lecturers), those not on the graduate faculty, and three professors hired as Ohio Board of Regents Eminent Scholars. We will use the following variables:


* **salary**: academic year (9-month) salary in U.S. dollars
* **male**: gender (1 male; 0 female)
* **market**: marketability of academic discipline, defined as the ratio of the national average salary paid in the discipline to the national average across all disciplines
* **yearsdg**: time since degree (in years)
* **rank**: academic rank (1 assistant professor; 2 associate professor; 3 full professor)

## 3 Independent-samples t test

If we have an interest in gender inequity, an obvious first step is to compare mean salaries between male and female professors at the university. We can use the tabstat command to produce a table of means, standard deviations, and sample sizes by gender:

```
tabstat salary, by(male) statistics(mean sd n)
```

      male |      Mean        SD         N
    -------+------------------------------
     Women |   42916.6   9161.61       128
       Men |  53499.24  12583.48       386
    -------+------------------------------
     Total |  50863.87  12672.77       514
    --------------------------------------

We see that the male faculty at the university earn, on average, over $10,000$ more than the female faculty. The standard deviation is also considerably larger for the men than for the women.

Due to chance or sampling variation, the large difference between the mean salary ${\overline{y_{1}}}$ of the $n_1$ men and the mean salary ${\overline{y}}_{0}$ of the $n_0$ women in the sample does not necessarily imply that the corresponding population means or *expectations* $\mu_1$ and $\mu_0$ for male and female faculty differ (the Greek letter $\mu$ is pronounced “mew”). Here *population* refers either to an imagined infinite population from which the data can be viewed as sampled or to the statistical model that is viewed as the data-generating mechanism for the observed data.

To define a statistical model, let $y_i$ and $x_i$ denote the salary and gender of the $i$th professor, respectively, where $x_i=1$ for men and $x_i=0$ for women.A standard model for the current problem can then be specified as

$$y_i|x_i\sim N(\mu_{x_i},\sigma_{x_i}^2)$$

Here $y_i|x_i\sim$ means $“y_i,\text{ for a given value of }x_i,\text{ is distributed as"}$ and $N(\mu_{x_i},\sigma_{x_i}^2)$ stands for a normal distribution with conditional mean parameter $\mu_{x_i}$ and conditional variance parameter $\sigma_{x_i}^2$ (the Greek letter $\sigma$ is pronounced “sigma”). The term conditional simply means that we are considering only the subset of the population for which some condition is satisfied—in this case, that $x_i$ takes on a particular value. In other words, we are considering the distribution of salaries for men $(x_{i}=1)$ separately from the distribution for women $(x_{i}=0)$

When conditioning on a categorical variable like gender, the expression “conditional on gender” can be replaced by “within gender” or “separately for each gender”. Because $x_i$ gender takes on only two values, we can be more explicit and write the statistical model as:

$$y_i|x_i = 0\sim N(\mu_{x_i},\sigma_{x_i}^2)$$
$$y_i|x_i = 1\sim N(\mu_{x_i},\sigma_{x_i}^2)$$

Each conditional distribution has its own conditional expectation, or conditional population mean,

$$\mu_{x_i}\equiv E(y_i|x_i)$$

denoted $\mu_1$ for men and $\mu_0$ for women ($\equiv$ stands for “defined as”). Each conditional distribution also has its own conditional variance

$$\sigma_{x_i}^2\equiv\mathrm{Var}(y_i|x_i)$$

denoted $\sigma_1^2$ for men and $\sigma_0^2$ for women. A final assumption is that $y_i$ is conditionally independent of $y_{i'}$ , given the values of $x_i$ and $x_{i'}$ , for different professors ${i}$ and ${i'}$ This means that knowing one professor’s salary does not help us predict another professor’s salary if we already know that other professor’s gender and the corresponding gender-specific mean salary.

By modeling $y_i$ conditional on $x_i$, we are treating $y_i$ as a response variable (sometimes also called dependent variable or criterion variable) and $x_i$ as an explanatory variable or covariate (sometimes referred to as independent variable, predictor, or regressor).


* **第一部分：总体均值和样本均值**

1. 总体均值（期望）

- **符号**: $\mu$ (发音 "mew") 表示总体均值或期望。
- **意义**: 对于男性教师，总体均值 $\mu_1$ 表示所有男性教师工资的平均数；对于女性教师，总体均值 $\mu_0$ 表示所有女性教师工资的平均数。

2. 样本均值
- **符号**: ${\overline{y_1}}$ 表示男性教师的样本均值，${\overline{y}}_{0}$ 表示女性教师的样本均值。
- **计算**: 样本均值是通过将样本中所有个体的工资相加，然后除以样本大小来计算的。例如，如果 $n_1$ 是男性教师的人数，那么 ${\overline{y_1}} = \frac{1}{n_1} \sum_{i=1}^{n_1} y_{i}$，其中 $y_{i}$ 是第 $i$ 个男性教师的工资。

* **第二部分：统计模型的建立**

1. **定义变量**

- **$y_i$**: 第 $i$ 个教授的工资。
- **$x_i$**: 第 $i$ 个教授的性别，男性为1，女性为0。

2. **条件分布**
- **表示法**: $y_i|x_i \sim N(\mu_{x_i}, \sigma_{x_i}^2)$
- **解释**:
  - “$\sim$” 表示“服从于”某种分布。
  - “$N(\mu_{x_i}, \sigma_{x_i}^2)$” 表示正态分布，其均值为 $\mu_{x_i}$，方差为 $\sigma_{x_i}^2$。
  - “条件”意味着 $y_i$ 的分布取决于 $x_i$ 的值。对于男性（$x_i=1$），我们考虑的是 $\mu_1$ 和 $\sigma_1^2$；对于女性（$x_i=0$），我们考虑的是 $\mu_0$ 和 $\sigma_0^2$。

3. **条件期望和条件方差**
- **条件期望**: $\mu_{x_i} \equiv E(y_i|x_i)$
  - 表示在已知性别 $x_i$ 的条件下，教授工资 $y_i$ 的期望值（或平均值）。
- **条件方差**: $\sigma_{x_i}^2 \equiv \mathrm{Var}(y_i|x_i)$
  - 表示在已知性别 $x_i$ 的条件下，教授工资 $y_i$ 的方差，即工资分布的离散程度。

4. **条件独立性**
- **假设**: $y_i$ 是条件独立的，给定 $x_i$ 和对于不同教授 $i$ 和 $i'$。
- **含义**: 这意味着一旦我们知道了教授的性别，一个教授的工资并不提供关于另一个教授工资的信息。

* **第三部分：解释变量和响应变量**

1. **解释变量（自变量）**
- **符号**: $x_i$
- **角色**: 解释或预测响应变量的变量。在这个例子中，性别 $x_i$ 被用来预测工资 $y_i$。

2. **响应变量（因变量）**
- **符号**: $y_i$
- **角色**: 被解释变量。在这个例子中，工资 $y_i$ 是我们想要预测或解释的变量。

* **第四部分：用例子进一步说明**

假设我们有一所大学，我们想要研究性别是否影响教授的工资。我们收集了以下数据：

- 男性教授（$x_i=1$）: 工资 $y_i$ 分布的均值为 $\mu_1 = 70000$，方差为 $\sigma_1^2 = 90000$。
- 女性教授（$x_i=0$）: 工资 $y_i$ 分布的均值为 $\mu_0 = 60000$，方差为 $\sigma_0^2 = 80000$。

这意味着，平均而言，男性教授的工资高于女性教授。然而，由于工资分布的离散程度（由方差表示），我们不能确定这种差异是否仅仅是由于随机抽样变异造成的。

通过建立统计模型，我们可以更深入地分析这些数据，并确定性别是否真的是影响工资的因素。我们可以使用统计检验来评估样本均值之间的差异是否具有统计学意义，从而推断总体均值之间是否存在差异。


The normality assumptions stated above are usually assessed by inspecting the conditional sample distributions for the men and women, using box plots.

```

graph box salary, over(male) ytitle(Academic salary) asyvars

generate lsalary = log10(salary)

graph box lsalary, over(male) ytitle(Log academic salary) asyvars
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.1.1.png" style="width:550px;height:370px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.1.2.png" style="width:550px;height:370px;" alt="图片描述">
  <figcaption><strong>Box plots of salary and log salary by gender</strong></figcaption>
</figure>



```
histogram salary, by(male, rows(2)) xtitle(Academic salary)

histogram lsalary, by(male, rows(2)) xtitle(Log academic salary)
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.1.3.png" style="width:550px;height:370px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.1.4.png" style="width:550px;height:370px;" alt="图片描述">
  <figcaption><strong>Histograms of salary and log salary by gender</strong></figcaption>
</figure>

A logarithmic transformation of salary makes the distributions more symmetric (like normal distributions).

We have specified a full statistical model for the data and will now turn to making inferences about the means $ \mu_0 $ and $ \mu_1 $ The methods of inference discussed in this chapter do not rely on the normality assumptions unless the sample is small.$

We use the **`ttest`** command to estimate the population means, produce confidence intervals, and test the null hypothesis that the two population means are equal,

$$H_0:\mu_0=\mu_1\quad\text{or}\quad H_0:\mu_0-\mu_1=0$$

against the two-sided alternative that they are different,

$$H_a:\mu_0\neq\mu_1\quad\text{or}\quad H_a:\mu_0-\mu_1\neq0$$

The most popular version of the test makes the additional assumption that the conditional variances are equal, $\sigma_0^2=\sigma_1^2$ , which we denote by dropping the subscript of $\sigma_{x_{i}}^2$ ,that is,

$$\mathrm{Var}(y_i|x_i)=\sigma^2$$

so that the model becomes

$$y_i|x_i\sim N(\mu_{x_i},\sigma_{x_i}^2)$$

The equal-variance assumption seems more reasonable for the log-transformed salaries than for the salaries on their original scale. However, salary in dollars is more interpretable than its log transformation, so for simplicity, we will work with the untransformed variable in this chapter.

* t检验

1. t检验的目的
t检验是一种统计方法，用来估计总体均值、产生置信区间，并检验两个总体均值是否相等的假设。

2. 使用t检验
在实际操作中，我们可以使用 `ttest` 命令来进行这些统计推断。

3. 等方差假设
t检验的一个常见版本假设两个总体的方差（即数据的离散程度）是相等的，这用 $\sigma_0^2 = \sigma_1^2$ 表示。这意味着对于给定的 $x_i$，条件方差 $\mathrm{Var}(y_i|x_i)$ 是常数 $\sigma^2$。

4. 正态分布模型
在等方差假设下，我们的模型可以表示为 $y_i | x_i \sim N(\mu_{x_i}, \sigma_{x_i}^2)$，即给定 $x_i$ 的条件下，$y_i$ 服从均值为 $\mu_{x_i}$，方差为 $\sigma_{x_i}^2$ 的正态分布。

5. 对数转换与原始尺度
文中提到，对数转换后的工资数据可能更适合等方差假设，但为了解释的简便，我们选择使用原始的工资数据，即使这可能意味着放弃等方差假设。

We can perform **`ttests**` with the assumption of equal variances by using

```
ttest salary, by(male)
```

    Two-sample t test with equal variances
    ------------------------------------------------------------------------------
       Group |     Obs        Mean    Std. err.   Std. dev.   [95% conf. interval]
    ---------+--------------------------------------------------------------------
       Women |     128     42916.6    809.7795     9161.61     41314.2    44519.01
         Men |     386    53499.24    640.4822    12583.48    52239.96    54758.52
    ---------+--------------------------------------------------------------------
    Combined |     514    50863.87     558.972    12672.77    49765.72    51962.03
    ---------+--------------------------------------------------------------------
        diff |           -10582.63    1206.345               -12952.63   -8212.636
    ------------------------------------------------------------------------------
        diff = mean(Women) - mean(Men)                                t =  -8.7725
    H0: diff = 0                                     Degrees of freedom =      512
    
        Ha: diff < 0                 Ha: diff != 0                 Ha: diff > 0
     Pr(T < t) = 0.0000         Pr(|T| > |t|) = 0.0000          Pr(T > t) = 1.0000


and without the assumption of equal variances by specifying the **`unequal`** option:

```
ttest salary, by(male) unequal
```

    Two-sample t test with unequal variances
    ------------------------------------------------------------------------------
       Group |     Obs        Mean    Std. err.   Std. dev.   [95% conf. interval]
    ---------+--------------------------------------------------------------------
       Women |     128     42916.6    809.7795     9161.61     41314.2    44519.01
         Men |     386    53499.24    640.4822    12583.48    52239.96    54758.52
    ---------+--------------------------------------------------------------------
    Combined |     514    50863.87     558.972    12672.77    49765.72    51962.03
    ---------+--------------------------------------------------------------------
        diff |           -10582.63    1032.454               -12614.48   -8550.787
    ------------------------------------------------------------------------------
        diff = mean(Women) - mean(Men)                                t = -10.2500
    H0: diff = 0                     Satterthwaite's degrees of freedom =  297.227
    
        Ha: diff < 0                 Ha: diff != 0                 Ha: diff > 0
     Pr(T < t) = 0.0000         Pr(|T| > |t|) = 0.0000          Pr(T > t) = 1.0000

For both versions of the $t$ test, the population means are estimated by the sample means,

$$\widehat{\mu}_0=\overline{y}_0,\quad\widehat{\mu}_1=\overline{y}_1$$

where the “hat” (^)denotes an estimator. The $t$ statistic is given by

$$t=\frac{\widehat{\mu}_0-\widehat{\mu}_1}{\widehat{\mathrm{SE}}(\widehat{\mu}_0-\widehat{\mu}_1)}$$

which is the estimated difference in population means (the difference in sample means) divided by the estimated standard error of the estimated difference in population means. Under the null hypothesis of equal population means, the statistic has a $t$ distribution with df degrees of freedom, where $df = n-2$ (sample size $n$ minus 2 for two estimated means $\widehat{\mu}_0$ and $\widehat{\mu}_1$) for the test assuming equal variances.

The 95% confidence interval for the difference in population means ${\mu_0} - {\mu_1}$ is 

$$\widehat{\mu_0}-\widehat{\mu_1} ± t_{0.975,\mathrm{df}}\widehat{\mathrm{SE}}(\widehat{\mu}_0-\widehat{\mu}_1)$$

where $t_{0.975,df}$ is the 97.5th percentile of the distribution with df degrees of freedom.

The standard error is estimated as $1,206.35$ under the equal-variance assumption $\sigma_0^2=\sigma_1^2$ and as $1,032.45$ without the equal-variance assumption; the degrees of freedom also differ. In both cases, the twotailed $p$-value (given under **Ha: diff != 0**) is less than (typically reported as $p<0.001$), leading to rejection of the null hypothesis at, say, the 5% level. For example, with the equal-variance assumption, $t=-8.77,\mathrm{df}=512,p<0.001.$ The 95% confidence intervals for the
difference in population mean salary for men and women are from $-12,953$ to $-8,213$ under the equal-variance assumption and from $-12,614$ to $-8,551$ without the equal-variance assumption. Repeating the analysis for log-salary (not shown) also gives $p<0.001$ and a smaller relative difference between the estimated standard errors for the two versions of the test

The normality assumption is not necessary for valid estimation of the model parameters (the population means and population standard deviations) and the standard error(s) used for inference. Normality ensures that the statistic has a distribution under the null hypothesis, but this null distribution is also approximately correct in large samples if normality is violated. Similar remarks apply to the rest of this volume.



* **第一部分：t检验的基本概念**

1. **估计总体均值**

在统计学中，我们经常使用样本均值来估计总体均值。这里的公式：

$$
\widehat{\mu}_0 = \overline{y}_0, \quad \widehat{\mu}_1 = \overline{y}_1
$$

表示我们用样本均值（上面的横线表示平均值，读作“y-bar”）来估计两个总体的均值（$\mu_0$和$\mu_1$）。这里的“帽子”符号（^）表示这是一个估计值。

**例子**：假设我们想要比较两个班级学生的数学成绩。班级A的平均成绩是85分（$\overline{y}_0 = 85$），班级B的平均成绩是90分（$\overline{y}_1 = 90$）。我们用这些样本均值来估计两个班级的总体均值。

2. **t统计量的计算**

t统计量是用来比较两个样本均值差异的统计量，其公式为：

$$
t = \frac{\widehat{\mu}_0 - \widehat{\mu}_1}{\widehat{\mathrm{SE}}(\widehat{\mu}_0 - \widehat{\mu}_1)}
$$

这里，分子是两个样本均值的差（在这个例子中，是85 - 90 = -5）。分母是这个差异的标准误差（SE）的估计值，它衡量了这个差异的可信程度。

**例子**：假设两个班级的成绩标准误差是10分。那么t统计量就是：

$$
t = \frac{-5}{10} = -0.5
$$

这个t值告诉我们两个班级成绩差异的大小相对于它们的标准误差。

3. **自由度的确定**

自由度（df）是一个重要的概念，它通常等于样本大小减去1（n-1）。但在比较两个总体均值的情况下，我们需要对每个总体都进行估计，所以自由度变为：

$$
df = n - 2
$$

这里的n是两个样本的总和。

**例子**：如果每个班级有50名学生，那么总的样本大小是100（n = 100）。因此，自由度是：

$$
df = 100 - 2 = 98
$$

4. **t分布和p值**

在零假设（两个总体均值相等）下，t统计量遵循t分布，其形状取决于自由度。如果t统计量的绝对值很大，或者对应的p值很小（通常小于0.05），我们就拒绝零假设，认为两个总体均值有显著差异。

**例子**：如果我们的t统计量是-0.5，自由度是98，我们可以查找t分布表或使用统计软件来确定对应的p值。如果p值大于0.05，我们不能拒绝零假设；如果p值小于0.05，我们可以拒绝零假设，认为两个班级的成绩有显著差异。

* **第二部分：置信区间和假设检验**

1. **置信区间的计算**

95%置信区间表示我们有95%的信心认为总体均值的差异在这个区间内。公式为：

$$
\widehat{\mu_0} - \widehat{\mu_1} ± t_{0.975, \mathrm{df}} \widehat{\mathrm{SE}}(\widehat{\mu}_0 - \widehat{\mu}_1)
$$

这里的$t_{0.975, \mathrm{df}}$是t分布的97.5百分位数，它告诉我们在95%的置信水平下，t统计量应该落在的区间。

**例子**：如果我们的t统计量是-0.5，自由度是98，97.5百分位数的t值是1.98（这个值会根据自由度的不同而变化）。如果我们的标准误差是10，那么95%置信区间将是：

$$
-5 ± 1.98 * 10 = -5 ± 19.8
$$

这给我们一个区间（-24.8到14.8），我们可以说，有95%的信心认为两个班级成绩的差异在这个区间内。

2. **标准误差的估计**

标准误差（SE）是衡量样本均值估计值的精确度的指标。在假设等方差的情况下，标准误差的估计值是1,206.35；如果不假设等方差，标准误差的估计值是1,032.45。

**例子**：如果我们使用等方差假设，那么标准误差是1,206.35。这意味着我们对两个班级成绩差异的估计值有很高的可信度。


* **方差的含义**

方差是衡量数据集中数值分布离散程度的一个统计量。简单来说，它告诉我们数据点偏离平均值的程度。如果数据点都紧密地围绕平均值，那么方差就会很小；如果数据点分散得很远，方差就会很大。

* **方差的计算步骤**

1. **计算平均值**：首先，我们需要计算数据集的平均值（均值），即所有数值加总后除以数值的个数。

   $$
   \mu = \frac{x_1 + x_2 + \ldots + x_n}{n}
   $$

   其中，$x_1, x_2, \ldots, x_n$ 是数据集中的数值，而 $n$ 是数值的总个数。

2. **计算偏差的平方**：对于数据集中的每一个数值，我们计算它与刚刚计算出的平均值的差（偏差），然后将这个差值平方。

   $$
   (x_i - \mu)^2
   $$

3. **求和**：将所有的平方偏差加在一起。

4. **计算方差**：将上一步得到的总和除以数据集的个数（对于总体方差）或个数减一（对于样本方差，这是为了得到一个无偏估计）。

   总体方差公式：
   $$
   \sigma^2 = \frac{1}{n}\sum_{i=1}^{n} (x_i - \mu)^2
   $$

   样本方差公式（无偏估计）：
   $$
   s^2 = \frac{1}{n-1}\sum_{i=1}^{n} (x_i - \overline{x})^2
   $$

   其中，$\sigma^2$ 是总体方差，$s^2$ 是样本方差，$x_i$ 是单个观测值，$\overline{x}$ 是样本均值。
* 例子

假设一个班级里有5个学生，他们的数学成绩分别是：70分、80分、85分、70分和90分。

1. **计算平均成绩**：
   $$
   \overline{x} = \frac{70 + 80 + 85 + 70 + 90}{5} = 70
   $$

2. **计算偏差的平方**：
   - 对于第一个学生：$(70 - 70)^2 = 0$
   - 对于第二个学生：$(80 - 70)^2 = 100$
   - 对于第三个学生：$(85 - 70)^2 = 225$
   - 对于第四个学生：$(70 - 70)^2 = 0$
   - 对于第五个学生：$(90 - 70)^2 = 400$

3. **求和**：将所有的平方偏差加在一起得到总和。
   $$
   0 + 100 + 225 + 0 + 400 = 725
   $$

4. **计算方差**：因为我们的数据是样本，所以我们使用样本方差的公式，并将总和除以个数减一（5-1=4）。
   $$
   s^2 = \frac{725}{4} = 181.25
   $$

这意味着样本中的成绩平均偏离均值181.25分的平方。方差告诉我们，这些成绩相对于平均成绩分散得相当远。

* **方差的作用**

方差帮助我们了解数据的稳定性。方差较小意味着数据点紧密围绕均值分布，而方差较大则意味着数据点分散在很大的范围内。在统计分析中，方差是衡量数据可靠性和预测模型准确性的关键指标之一。

* **标准误差与方差的关系：**

- 方差描述的是单个数据点与样本均值之间的差异。
- 标准误差描述的是样本均值与总体均值之间的差异。
- 当我们从总体中抽取样本时，样本均值会因为随机抽样而略有不同。标准误差提供了一个量化这种差异的方法。样本越大，标准误差越小，因为较大的样本通常更准确地代表总体。

* **标准误差的计算公式**

标准误差（SE）是用来衡量样本均值作为总体均值估计的可靠性的指标。对于两个独立样本的均值差异，标准误差的计算公式如下：

假设方差相等（Pooled Variance）时的公式：
$$
\widehat{\mathrm{SE}}(\overline{x}_1 - \overline{x}_2) = \sqrt{\frac{s_1^2}{n_1} + \frac{s_2^2}{n_2}}
$$

假设方差不相等（Unequal Variances）时的公式：
$$
\widehat{\mathrm{SE}}(\overline{x}_1 - \overline{x}_2) = \sqrt{\frac{s_1^2}{n_1} + \frac{s_2^2}{n_2}}
$$

注意：尽管公式看起来相同，但计算自由度（df）时会有所不同，这将影响t检验的进行。

* **计算步骤**

1. **计算每个样本的方差(s²)**:
   - 方差是各个数据点与样本均值差的平方和的平均值。

2. **确定每个样本的大小(n)**:
   - 样本大小是指样本中数据点的数量。

3. **根据方差是否相等，选择相应的标准误差公式**:
   - 如果假设方差相等，使用合并方差来计算SE。
   - 如果假设方差不相等，直接使用各自的方差来计算SE。

* **例子**

假设我们有两个班级的学生数学成绩数据：

- 班级A：20名学生，平均成绩（mean）85分，方差（variance）100。
- 班级B：25名学生，平均成绩80分，方差150。

* **假设方差相等**

1. **计算合并方差（Pooled Variance）**:
   $$
   s_p^2 = \frac{(20-1) \times 100 + (25-1) \times 150}{20 + 25 - 2}
   $$
   计算得：
   $$
   s_p^2 = \frac{19 \times 100 + 24 \times 150}{43} = \frac{1800 + 3600}{43} = \frac{5400}{43} \approx 125.58
   $$

2. **计算标准误差**:
   $$
   \widehat{\mathrm{SE}} = \sqrt{\frac{125.58}{20} + \frac{125.58}{25}} = \sqrt{6.279 + 5.0232} \approx \sqrt{11.3024} \approx 3.36
   $$

* **假设方差不相等**

1. **直接使用各自的方差计算标准误差**:
   $$
   \widehat{\mathrm{SE}} = \sqrt{\frac{100}{20} + \frac{150}{25}} = \sqrt{5 + 6} = \sqrt{11} \approx 3.32
   $$

* **自由度的计算**

在假设方差不相等的情况下，自由度的计算会稍有不同，使用以下公式：

$$
df = \frac{(\frac{s_1^2}{n_1} + \frac{s_2^2}{n_2})^2}{\frac{(\frac{s_1^2}{n_1})^2}{n_1 - 1} + \frac{(\frac{s_2^2}{n_2})^2}{n_2 - 1}}
$$

* **结论**

通过这个例子，我们可以看到，标准误差的计算涉及方差、样本大小以及方差是否相等的假设。标准误差越小，说明样本均值作为总体均值的估计越可靠。在进行假设检验时，标准误差是一个关键的计算步骤。希望这个更详细的解释和例子能帮助你更好地理解标准误差的计算。如果你还有任何疑问，请随时提问。


3. **p值的解释**

p值是检验统计显著性的概率指标。如果p值小于0.05，我们通常认为结果是统计显著的，可以拒绝零假设。

**例子**：如果我们的p值小于0.001，这意味着我们非常有信心拒绝零假设，认为两个班级的成绩有显著差异。

4. **例子：男女工资差异**

文中提到了一个具体的例子，比较男性和女性的工资差异。在等方差假设下，95%置信区间是从-12,953到-8,213；在不等方差假设下，区间是从-12,614到-8,551。这表明，无论是否假设等方差，我们都可以非常有信心地认为男性和女性的工资存在显著差异。

* 结论

t检验是一种强大的统计工具，用于比较两个总体均值是否有显著差异。通过理解t统计量的计算、自由度的选择、置信区间的确定以及p值的解释，我们可以更好地进行假设检验和置信区间的估计。记住，正态性假设对于参数的估计不是必需的，但在样本量较大时，即使违反了正态性假设，t分布仍然可以作为一个好的近似。

* **例子**

假设我们有两个样本，男性和女性的工资样本。我们想要检验男性和女性的工资是否有显著差异。
1. 我们计算两个样本的均值 $ \overline{y}_0 $ 和 $ \overline{y}_1 $。
2. 我们计算 $ t $ 统计量，即 $ \widehat{\mu}_0 - \widehat{\mu}_1 $ 除以它们的标准误差。
3. 我们查找 $ t $ 分布表，找到对应自由度的97.5百分位数 $ t_{0.975, df} $。
4. 我们计算95%置信区间，并得到 $ p $ 值。
5. 如果 $ p $ 值很小，我们拒绝零假设，认为男性和女性的工资有显著差异。
希望这个解释能帮助你更好地理解 $ t $ 检验的概念和计算方法。如果你有任何疑问或需要进一步的解释，请随时提问。



## 4 One-way analysis of variance

The model underlying the $t$-test with equal variances is also called a oneway analysis-of-variance (ANOVA) model.

ANOVA involves partitioning the *total sum of squares* (TSS), the sum of squared deviations of $y_i$ the from their overall mean,

$$\text{TSS}=\sum_{i=1}^n(y_i-\overline{y})^2$$

into the *model sum of squares* (MSS) and the *sum of squared errors* (SSE).

The MSS, also known as regression sum of squares, is


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.1.9.png" style="width:550px;height:71px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

the sum of squared deviations of the sample means from the overall mean, interpretable as the between-group sum of squares (here “ $i,x_i = 0$” and  “ $i,x_i = 1$” mean that the sums are taken over females and males, respectively)

The SSE, also known as residual sum of squares, is


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.1.8.png" style="width:550px;height:71px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


the sum of squared deviations of responses from their respective sample means, interpretable as the within-group sum of squares ( $s_0$ and $s_1$ are the within-group sample standard deviations)

 


The group-specific sample means can be viewed as predictions, 

<math xmlns="http://www.w3.org/1998/Math/MathML">
  <mrow>
    <mover accent="true" accentunder="false">
      <mrow>      
        <msub>
          <mi>y</mi>
          <mrow>
            <mi>i</mi>
          </mrow>
        </msub>      
      </mrow>      
      <mo>^</mo>
    </mover>
  </mrow>
  <mo>=</mo>
  <msub>
    <mrow>
      <mover accent="true" accentunder="false">
        <mrow>
          <mi>μ</mi>
        </mrow>
        <mo>^</mo>
      </mover>
    </mrow>
    <mrow>
      <msub>
        <mi>x</mi>
        <mrow>
          <mi>i</mi>
        </mrow>
      </msub>
    </mrow>
  </msub>
  <mo>=</mo>
  <msub>
    <mover accent="true" accentunder="false">
      <mrow>
        <mi>y</mi>
      </mrow>
      <mo accent="false">¯</mo>
    </mover>
    <mrow>
      <msub>
        <mi>x</mi>
        <mrow>
          <mi>i</mi>
        </mrow>
      </msub>
    </mrow>
  </msub>
</math> 
,representing the best guess of the salary when all that is known about the professor is the gender. These predictions, $\overline{y}_0$ and $\overline{y}_1$ minimize the SSE and are therefore referred to as ordinary least-squares OLS estimates of $\mu_0$ and $\mu_1$. When evaluating the quality of the predictions $\widehat{y}_{i}$ , the SSE is interpreted as the sum of the squared prediction errors $y_{i}-\widehat{y}_i$.

The total sum of squares equals the model sum of squares plus the sum of squared errors:

$$ TSS~=~MSS+SSE $$

The deviations contributing to each of these sums of squares are shown in figure 1.3 for an observation (shown as ·) in a hypothetical dataset. These deviations add up in the same way as the corresponding sums of squares. For example, for men,$y_{i}-\overline{y}=(\overline{y_1}-\overline{y})+(y_{i}-\overline{y}_{1}).$


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.1.5.png" style="width:550px;height:440px;" alt="图片描述">
  <figcaption><strong>Figure 1.3: Illustration of deviations contributing to total sum of squares (TSS), model sum of squares (MSS), and sum of squared errors (SSE)</strong></figcaption>
</figure>

The model mean square (MMS) and mean squared error (MSE) can be obtained from the corresponding sums of squares by dividing by the appropriate degrees of freedom as shown in table 1.1 for the general case of $g$ groups ( for comparing males and females,$g = 2$).


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.1.6.png" style="width:550px;height:170px;" alt="图片描述">
  <figcaption><strong>Table 1.1: Sums of squares (SS) and mean squares (MS) for one-way ANOVA</strong></figcaption>
</figure>

The MSE is the pooled within-group sample variance, which is an estimator for the population variance parameter $\sigma^{2}$:

$$\widehat{\sigma^2}=\mathrm{MSE}$$

The $F$statistic for the null hypothesis that the population means are the same (against the two-sided alternative) then is

$$F=\frac{\mathrm{MMS}}{\mathrm{MSE}}$$

Under the null hypothesis, this statistic has an $F$ distribution with $g-1$ and $n-g$ degree of freedom.When $g = 2$ as in our example, the statistic is the square of the $t$statistic from the independent-samples test under the equal-variance assumption, and the $p$-values from the two tests are identical.

* **ANOVA的基本概念和计算步骤（详细版）**

1. **总平方和（TSS）**

TSS是观测值与整体平均值之间差异的平方和，它反映了数据集中的总变异性。

**计算步骤**：

1. 计算所有观测值的平均值 $\overline{y}$。
2. 对每个观测值 $y_i$，计算其与 $\overline{y}$ 的差异 $y_i - \overline{y}$。
3. 将这些差异的平方求和，即 $\sum_{i=1}^n (y_i - \overline{y})^2$。

**数学表达式**：
$$
\text{TSS} = \sum_{i=1}^n (y_i - \overline{y})^2
$$

**举例**：
假设我们有两组数据，组1有3个观测值（1, 2, 3），组2有2个观测值（4, 5），则 $\overline{y} = \frac{1+2+3+4+5}{5} = 3$。

$$
\text{TSS} = (1-3)^2 + (2-3)^2 + (3-3)^2 + (4-3)^2 + (5-3)^2 = 4 + 1 + 0 + 1 + 4 = 10
$$

2. **模型平方和（MSS）**

MSS反映了不同组均值之间的差异，即模型解释的变异性。

**计算步骤**：

1. 计算每个组的均值。
2. 对每个组，计算其均值与全局均值之间的差异。
3. 将这些差异的平方乘以该组的观测值数量，然后求和。

**数学表达式**：
在两组的情况下，其数学表达式可以为：


$$\text{MSS} = n_1(\overline{y}_1 - \overline{y})^2 + n_2(\overline{y}_2 - \overline{y})^2$$

更一般的情况，如果有k个组，则表达式为：
$$
\text{MSS} = \sum_{j=1}^{k}n_j(\overline{y}_j - \overline{y})^2
$$
其中，$n_j$是第j组的观测值数量，$\overline{y}_j$是第j组的均值，而$\overline{y}$是所有观测值的总体均值。

**举例**：
假设我们有两组数据，组1有3个观测值（1, 2, 3），组2有2个观测值（4, 5）。
则组1的均值$\overline{y}_1 = 2$，组2的均值$\overline{y}_2 = 4.5$，全局均值$\overline{y} = 3$。
$$
\text{MSS} = 3 \times (2 - 3)^2 + 2 \times (4.5 - 3)^2 = 3 \times 1 + 2 \times 2.25 = 7.5
$$

1. **误差平方和（SSE）**

SSE衡量了组内变异性，即模型未解释的变异性。

**计算步骤**：

1. 对每个组，计算每个观测值与其组均值之间的差异。
2. 将这些差异的平方求和。

**数学表达式**：

$$
\text{SSE} = \sum_{j=1}^{k}\sum_{i=1}^{n_j}(y_{ij} - \overline{y}_j)^2
$$

其中，$y_{ij}$表示第j组的第i个观测值，$\overline{y}_j$表示第j组的均值，$n_j$是第j组的观测值数量，k是组的总数。

**举例**：
续上例，组1的SSE为$(1-2)^2 + (2-2)^2 + (3-2)^2 = 2$，组2的SSE为$(4-4.5)^2 + (5-4.5)^2 = 0.5$。
所以总的SSE为$2 + 0.5 = 2.5$。

4. **TSS、MSS和SSE的关系**

TSS、MSS和SSE之间有以下关系：
$$
\text{TSS} = \text{MSS} + \text{SSE}
$$

接上例：TSS(10) = MSS(7.5) + SSE(2.5)

这个公式表示总平方和可以分解为模型平方和与误差平方和之和。

5. **均方值（Mean Square）**

均方值是通过将平方和除以其自由度得到的。

**计算步骤**：

1. 计算MSS和SSE的自由度。MSS的自由度是组的数量减1（$k-1$），SSE的自由度是观测值的总数减组的数量（$N-k$），其中N是观测值的总数。

2. 将MSS和SSE分别除以其自由度，得到MMS（组间均方）和MSE（组内均方）。

**数学表达式**：
$$
\text{MMS} = \frac{\text{MSS}}{k - 1}
$$
$$
\text{MSE} = \frac{\text{SSE}}{N - k}
$$

$$\widehat{\sigma^2}=\mathrm{MSE}$$ 

均方误差（MSE）是总体方差的一个估计。让我来一步一步解释为什么它们是相等的。

首先，我们需要理解两个概念：
1. **总体方差（$\sigma^2$）**：这是描述一个整体数据集分布的统计量。它衡量的是数据点与总体均值的平均偏差的平方。总体方差越大，数据点之间的差异就越大。
2. **均方误差（MSE）**：MSE是线性回归模型中一个重要的统计量，它衡量的是模型预测值与实际观测值之间差异的平均大小。具体来说，它是所有单个预测误差（即每个观测值$y_i$与对应的预测值$\widehat{y}_i$之间的差异）的平方和的平均值。


现在，让我们来看看为什么MSE可以被视为总体方差$\sigma^2$的一个估计。在统计学中，总体方差$\sigma^2$是一个衡量数据分散程度的指标，它表示数据点与其均值之间的平均偏差的平方。然而，在现实中，我们通常无法观察到整个总体，只能观察到样本数据。因此，我们需要从样本中估计总体方差。


因此，当我们说$\widehat{\sigma^2}=\mathrm{MSE}$时，我们实际上是在说，MSE不仅是衡量模型预测精度的指标，而且也是对总体方差$\sigma^2$的一个无偏估计, 是我们能得到的最好的对总体方差的估计。这就是为什么它们被认为是相等的。简单来说，MSE告诉我们，在给定的模型下，我们的预测值与真实值之间的平均差异，这个差异也被认为是数据集总体方差的一个很好的估计。

简单来说，这个等式是在说：“我们的模型预测误差的平均值（MSE）是数据集总体方差（$\sigma^2$）的一个很好的估计。”这就是为什么它们被认为是相等的。

在线性回归中，当我们拟合一个模型时，我们希望模型的预测尽可能接近实际观测值。这意味着我们希望最小化预测误差，即每个观测值与模型预测值之间的差异。当我们成功拟合模型后，模型的MSE实际上就给出了一个关于数据点围绕回归线分散程度的度量。这种分散程度在统计上被认为是对总体方差$\sigma^2$的一个很好的估计。


**举例**（续上例）：
假设我们有两个组（$k=2$），总共有5个观测值（$N=5$）。
MSS的自由度是 $k-1=1$，SSE的自由度是 $N-k=3$。

$$\text{MMS} = \frac{7.5}{1} = 7.5$$
$$\text{MSE} = \frac{2.5}{3} \approx 0.8333$$

1. **F统计量的计算**

F统计量用于检验组间均值是否有显著差异。

**计算步骤**：

1. 计算MMS和MSE。
2. 将MMS除以MSE得到F统计量。

**数学表达式**：
$$
F = \frac{\text{MMS}}{\text{MSE}}
$$

**举例**（续上例）：
$$
F = \frac{7.5}{0.8333} \approx 9
$$


公式 $$F=\frac{\mathrm{MMS}}{\mathrm{MSE}}$$ 是在描述方差分析（ANOVA）中使用的F统计量。在这个公式中，MMS代表“均方均值”（Mean Square for Mean），而MSE代表“均方误差”（Mean Square Error），它们都是方差分析中计算出的统计量。
在方差分析中，我们比较不同组之间的均值是否有显著差异。为了做到这一点，我们计算两组之间的均值差异（MMS），然后除以组内差异（MSE）。这个比率就是F统计量，它告诉我们组间差异相对于组内差异的大小。

- **MMS（均方均值）**：这是组间均值差异的度量。我们计算不同组均值之间的差异，然后求这些差异的均方值。如果组间差异很大，MMS会很大。

- **MSE（均方误差）**：这是组内差异的度量。我们计算每个组内观测值与其组均值之间的差异，然后求这些差异的均方值。如果组内差异很小，MSE会很小。

F统计量的计算公式是将MMS除以MSE。如果组间差异（MMS）远大于组内差异（MSE），F统计量会很大，这表明不同组的均值可能有显著差异。

在零假设下，即假设所有组的均值都相同，F统计量服从F分布。F分布有两个参数，分别是组数减1（$g-1$）和样本总数减去组数（$n-g$）。这两个参数分别是F分布的两个自由度。

当组数$g=2$时，F统计量在等方差假设下是独立样本t检验的t统计量的平方。这是因为在这种情况下，我们只有两个组，F统计量就是比较这两个组均值差异的统计量。在等方差假设下，t检验和F检验的p值是相同的，因为它们都是基于相同的统计量进行的显著性检验。

简单来说，F统计量是一个衡量不同组均值差异相对于组内差异的大小的指标。如果F统计量很大，我们可能会拒绝零假设，认为不同组的均值存在显著差异。

1. **进行显著性检验**

根据计算得到的F统计量和对应的自由度（$k-1$和$N-k$），查找F分布表或使用统计软件获取临界值。如果F值大于临界值（即F值位于拒绝域内），则拒绝零假设（即组间均值无显著差异），认为组间均值存在显著差异。

* 注意事项

* ANOVA假设数据是独立且正态分布的，并且各组具有相同的方差（方差齐性）。如果不满足这些假设，可能需要使用非参数ANOVA或其他统计方法。
* ANOVA只能告诉我们组间是否存在差异，但不能告诉我们哪些组之间存在差异。如果需要进一步了解哪些组之间存在差异，可以使用后续检验，如t检验或Mann-Whitney U检验等。
* 在实际应用中，通常会使用统计软件（如SPSS、SAS、R或Python的statsmodels库等）来计算ANOVA，因为这些软件能自动处理计算细节，并提供详细的输出结果。

8. **方差齐性检验**

在进行ANOVA之前，通常需要检验各组之间的方差是否齐性（即各组的方差是否相等）。如果方差不齐，ANOVA的结果可能不准确。常用的方差齐性检验方法包括Levene检验、Bartlett检验等。

**Levene检验**：
Levene检验是一个常用的方差齐性检验方法，它通过计算每个观测值的绝对值与其组均值绝对偏差的乘积，并对这些乘积进行组间比较来检验方差是否齐性。如果Levene检验的结果不显著（即p值大于显著性水平），则认为各组方差齐性，可以进行ANOVA分析。

9. **后续分析（如果ANOVA结果显著）**

如果ANOVA的结果显著，即组间存在显著差异，我们可能希望进一步了解哪些组之间存在差异。此时，可以进行以下后续分析：

**t检验或Mann-Whitney U检验**：
对于只有两个组的情况，可以使用独立样本t检验（如果数据符合正态性和方差齐性假设）或Mann-Whitney U检验（如果数据不满足这些假设）来比较两组的均值是否存在差异。

**单因素方差分析的多重比较**：
当存在多个组时，可以使用多重比较方法（如Tukey's HSD、Scheffé's test、Bonferroni方法等）来比较各组之间的均值是否存在显著差异。这些方法可以提供关于哪些组之间存在差异的具体信息。

10. **效应大小（Effect Size）**

除了显著性检验外，了解效应大小也很重要。效应大小描述了组间差异的实际程度，而不仅仅是统计显著性。常用的效应大小指标包括η²（eta-squared）、ω²（omega-squared）和Cohen's d等。这些指标可以帮助我们理解组间差异的实际意义。

11. **解读ANOVA结果**

解读ANOVA结果时，需要注意以下几点：

* 检查ANOVA的显著性水平（p值），确定是否拒绝零假设。
* 检查方差齐性检验结果，确保ANOVA的有效性。
* 如果ANOVA结果显著，进行后续分析以了解哪些组之间存在差异。
* 报告效应大小指标，以描述组间差异的实际程度。

12. **假设检验的局限性和注意事项**

* 假设检验的结果是基于样本数据得出的，因此结果可能受到样本选择、样本大小等因素的影响。
* 显著性水平（如α=0.05）是人为设定的，不同的研究者可能会选择不同的显著性水平。因此，结果的解释需要谨慎。
* 假设检验只能告诉我们是否存在显著差异，但不能证明因果关系。要确定因果关系，需要进行更深入的研究。
* 方差分析假设数据是独立且正态分布的，并且各组具有相同的方差。如果数据不满足这些假设，可能需要使用其他统计方法或进行适当的数据转换。

13. **报告ANOVA结果**

在报告ANOVA结果时，应包括以下内容：

* 研究的样本大小、组数和观测值的描述性统计量（如均值、标准差等）。
* 方差齐性检验的结果（如Levene检验）。
* ANOVA的结果（包括F值、自由度、p值和效应大小指标）。
* 如果ANOVA结果显著，进行后续分析的结果（如t检验、多重比较等）。
* 研究的结论和解释，以及对结果的讨论和限制。

We can perform a one-way ANOVA in Stata by using

```
anova salary male
```

                             Number of obs =        514    R-squared     =  0.1307
                             Root MSE      =    11827.4    Adj R-squared =  0.1290
    
                      Source | Partial SS         df         MS        F    Prob>F
                  -----------+----------------------------------------------------
                       Model |  1.077e+10          1   1.077e+10     76.96  0.0000
                             |
                        male |  1.077e+10          1   1.077e+10     76.96  0.0000
                             |
                    Residual |  7.162e+10        512   1.399e+08  
                  -----------+----------------------------------------------------
                       Total |  8.239e+10        513   1.606e+08  
    
This gives a statistically significant difference in mean salaries for men and women as before $[F(1,512)=76.96,p<0.001]$. The estimate $\sqrt{\widehat{\sigma^2}}=\$11,827 is given as **Root MSE** in the second line of the output.

"Root MSE"是指均方误差（Mean Square Error, MSE）的平方根，通常用符号 $\sqrt{\mathrm{MSE}}$ 表示。MSE是预测值与实际值之间差异平方的平均值，而Root MSE则是这些差异的平均值。这个值提供了一个实际的数值，用来衡量模型预测的准确性。
例如，如果一个线性回归模型的MSE是0.25，那么Root MSE就是 $\sqrt{0.25} = 0.5$。这意味着模型预测值与实际值之间的平均差异是0.5个单位。

Root MSE通常用于报告回归分析的结果，因为它提供了一个更直观的误差度量，可以直接解释为预测值和实际值之间的平均距离。在统计模型中，Root MSE越小，模型的预测精度通常被认为越高。

Estimates of $\mu_0$ and $\mu_1$ are not shown but can be obtained using the postestimation command margins :

```
margins male
```

    Adjusted predictions                                       Number of obs = 514
    
    Expression: Linear prediction, predict()
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            male |
          Women  |   42916.60    1045.40    41.05   0.000     40862.80    44970.41
            Men  |   53499.24     602.00    88.87   0.000     52316.55    54681.93
    ------------------------------------------------------------------------------

## 5 Simple linear regression

Salaries can vary considerably between academic departments. Some disciplines are more marketable than others, perhaps because there are highly paid jobs in those disciplines outside academia or because there is a low supply of qualified graduates. The dataset contains a variable, **market**, for the marketability of the discipline, defined as the mean U.S. faculty salary in that discipline divided by the mean salary across all disciplines.

Let us now investigate the relationship between salaries and marketability of the discipline. Marketability ranges from 0.71 to 1.33 in this sample, taking on 46 different values. A one-way ANOVA model, allowing for a different mean salary $\mu_{xi}$for each value of marketability $x_i$ would have a large number of parameters and many groups containing only one individual and would hence be *overparameterized*. There are two popular ways of dealing with this problem: 
1) categorize the continuous explanatory variable into intervals, thus producing fewer and larger groups, or 
2) assume a parametric, typically linear, relationship between $\mu_{xi}$ and $x_i$. Before adopting the latter approach, we inspect the scatterplot in figure 1.4, with a smooth curve (called **LOWESS**) superimposed:

这段文字讨论的是学术部门之间薪资差异的问题，以及如何研究薪资与学科市场性之间的关系。首先，它定义了市场性（market）这个变量，它是通过计算某个学科在美国的平均教职员工薪资除以所有学科的平均薪资来得出的。接着，作者指出市场性在样本中的范围是从0.71到1.33，并且有46个不同的值。

接下来，作者提到了研究薪资与市场性之间关系时遇到的一个问题：如果使用单因素方差分析（one-way ANOVA）模型，允许每个市场性值 $x_i$ 有一个不同的平均薪资 $mu_{xi}$，那么模型将包含大量的参数，并且许多组可能只包含一个个体，从而导致模型过度参数化（overparameterized）。

为了解决这个问题，作者提出了两种流行的处理方法：

1. 将连续的解释变量（市场性）划分为区间，从而产生更少但更大的组。
2. 假设平均薪资 $mu_{xi}$ 和市场性 $x_i$ 之间存在一个参数化的，通常是线性的关系。



```
twoway (scatter salary market, msize(small)) ///
       (lowess salary market, lwidth(medthick) lpatt(solid))
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.1.10.png" style="width:650px;height:370px;" alt="图片描述">
  <figcaption><strong>Figure 1.4: Scatterplot with LOWESS curve</strong></figcaption>
</figure>


The LOWESS curve is almost perfectly linear, and we therefore specify a **simple linear regression model**

$$\begin{matrix}y_i|x_i&\sim&N(\mu_{x_i},\sigma^2)\end{matrix}$$

where

$$\mu_{x_i}\equiv E(y_i|x_i)=\beta_1+\beta_2x_i$$

Alternatively, the model can be written as

$$y_i=\beta_1+\beta_2x_i+\epsilon_i,\quad\epsilon_i|x_i\sim {N}(0,\sigma^2)$$

where $\epsilon_{i}$ is the residual or error term for the $i$th professor, assumed to be independent of the residuals for other professors. Here $\beta_1$(the Greek letter $\beta$ is pronounced “beta”) is called the intercept (often denoted $\beta_0$ or $\alpha$ ) and represents the conditional expectation of $y_i$ when $x_i = 0$ :

$$E(y_i|x_i=0)=\beta_1$$


$\beta_1$ is called the *slope* , or *regression coefficient* , of $x_i$ and represents the increase in conditional expectation when $x_i$ increases by one unit, from some value $\alpha$ to $\alpha + 1$:

$$E(y_i|x_i=a+1)-E(y_i|x_i=a)=[\beta_1+\beta_2(a+1)]-[\beta_1+\beta_2a]=\beta_2$$

We refer to $\beta_1+\beta_2{x_i}$ as the *fixed part* and $\epsilon_{i}$ as the *random part* of the model.


In addition to assuming that the conditional expectations fall on a straight line, the model assumes that the conditional variances, or residual variances, of the $y_i$ are equal for all $x_i$,

$$\mathrm{Var}(y_i|x_i)=\mathrm{Var}(\epsilon_i|x_i)=\sigma^2$$

which is known as the *homoskedasticity* assumption (in contrast to *heteroskedasticity* , where the conditional variances of the $y_i$ are not equal for all $x_i$).

A graphical illustration of the simple linear regression model is given in figure 1.5. Here the line represents the conditional expectation $E(y_{i}|x_{i})$ as a function of $x_i$, and the density curves represent the conditional distributions of $y_{i}|x_{i}$ shown only for some values of $x_i$.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.1.11.png" style="width:650px;height:370px;" alt="图片描述">
  <figcaption><strong>Figure 1.5: Illustration of simple linear regression model</strong></figcaption>
</figure>


* **第一部分：模型的组成部分**
1. **正态分布假设**
模型的起点是正态分布的假设：
$$
y_i|x_i \sim N(\mu_{x_i}, \sigma^2)
$$
这表示给定 $x_i$，$y_i$ 的条件分布是正态分布，均值为 $\mu_{x_i}$，方差为 $\sigma^2$。
2. **均值函数**
均值函数 $\mu_{x_i}$ 描述了 $x_i$ 和 $y_i$ 之间的关系：
$$
\mu_{x_i} \equiv E(y_i|x_i) = \beta_1 + \beta_2x_i
$$
这里：
- $E(y_i|x_i)$ 是在给定 $x_i$ 的条件下，$y_i$ 的期望值。
- $\beta_1$ 是截距，表示当 $x_i = 0$ 时 $y_i$ 的期望值。
- $\beta_2$ 是斜率，表示 $x_i$ 每增加一个单位，$y_i$ 的期望值增加的量。
3. **模型的另一种表达形式**
模型还可以写作：
$$
y_i = \beta_1 + \beta_2x_i + \epsilon_i, \quad \epsilon_i|x_i \sim N(0, \sigma^2)
$$
这里：
- $y_i$ 是实际观测到的值。
- $\beta_1 + \beta_2x_i$ 是由 $x_i$ 预测的 $y_i$ 的值。
- $\epsilon_i$ 是误差项，表示预测值和实际值之间的差异。
4. **误差项 $\epsilon_i$**
误差项 $\epsilon_i$ 假设是：
- 正态分布的，均值为0，方差为 $\sigma^2$。
- 对于不同的 $i$ 是独立的。
5. **斜率的解释**
斜率 $\beta_2$ 表示 $x_i$ 每增加一个单位，$y_i$ 的期望值增加的量：
$$
E(y_i|x_i=a+1) - E(y_i|x_i=a) = \beta_2
$$
这里：
- $a$ 是 $x_i$ 的某个值。
- $E(y_i|x_i=a+1)$ 是在 $x_i = a+1$ 时 $y_i$ 的期望值。
- $E(y_i|x_i=a)$ 是在 $x_i = a$ 时 $y_i$ 的期望值。
* **第二部分：模型的假设和图形表示**
1. **同方差性假设**
模型假设对于所有的 $x_i$，$y_i$ 的条件方差是相等的：
$$
\mathrm{Var}(y_i|x_i) = \mathrm{Var}(\epsilon_i|x_i) = \sigma^2
$$
这称为同方差性假设。如果条件方差不相等，则称为异方差性。
2. **图形表示**
简单线性回归模型在图形上表示为一条直线，这条直线的斜率是 $\beta_2$，截距是 $\beta_1$。围绕这条直线，每个 $x_i$ 值对应的 $y_i$ 会有一个正态分布的密度曲线，表示在该 $x_i$ 下 $y_i$ 的分布情况。
* **例子**
假设我们有一组数据，表示教授的年资 ($x_i$) 和他们的薪水 ($y_i$)：
- 教授A，年资 $x_i = 5$ 年，薪水 $y_i = 60,000$ 美元。
- 教授B，年资 $x_i = 10$ 年，薪水 $y_i = 70,000$ 美元。
我们假设：
- $\beta_1 = 40,000$（截距）
- $\beta_2 = 5,000$（斜率）
1. 计算均值函数
- 对于教授A：$\mu_{x_i} = 40,000 + 5 \times 5,000 = 60,000$。
- 对于教授B：$\mu_{x_i} = 40,000 + 10 \times 5,000 = 70,000$。
2. 误差项 $\epsilon_i$
- 对于教授A：误差项 $\epsilon_i = 60,000 - 60,000 = 0$。
- 对于教授B：误差项 $\epsilon_i = 70,000 - 70,000 = 0$。
在这个例子中，所有数据点都完美地落在了回归线上，所以误差项为0。在现实中，数据点通常不会完美地落在回归线上，误差项会捕捉这些差异。


We have described the regression coefficients as changes in conditional expectations without making any causal claims. Sometimes, especially in econometrics, regression coefficients are interpreted as causal effects; this necessitates further assumptions known as exogeneity assumptions, which are discussed in section 1.13. We will occasionally use the term *effect* in this book, but this does not necessarily mean that we are making causal claims.

For OLS estimates, we can partition the TSS into MSS and SSE exactly as in section 1.4, the only difference being the form of the predicted value of $y_i$:

$$\begin{array}{rcl}\widehat{y}_i&=&\widehat{\beta}_1+\widehat{\beta}_2x_i\end{array}$$

The contribution to each sum of squares is shown in figure 1.6 for an observation (shown as ·) in a hypothetical dataset. The OLS estimates of $\beta_1$ and $\beta_2$ are obtained by minimizing the SSE, and the estimate of $\sigma^{2}$ is again the MSE, where the error degrees of freedom is $n-2$ (number of observations $n$ minus 2 estimated regression coefficients $\widehat{y}_1$ and $\widehat{y}_2$). Maximum likelihood estimation, used for more complex models in later chapters, gives the same estimates of the regression coefficients as OLS but a smaller estimate of the residual variance because the latter is given by the SSE divided by $n$ instead of divided by $n-2$ as for OLS.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.1.12.png" style="width:650px;height:370px;" alt="图片描述">
  <figcaption><strong>Figure 1.5: Illustration of simple linear regression model</strong></figcaption>
</figure>

The coefficient $R^2$ of determination is defined as

$$R^2\equiv\frac{\mathrm M\mathrm S\mathrm S}{\mathrm T\mathrm S\mathrm S}=\frac{\mathrm T\mathrm S\mathrm S-\mathrm S\mathrm S\mathrm E}{\mathrm T\mathrm S\mathrm S}$$

and can be motivated as the proportion of the total variability (TSS) that is “explained” by the model that includes the covariate $x_i$ (MSS). Alternatively, if TSS is viewed as the sum of squared prediction errors when the predictions are simply $\widehat{y}_{i}=\overline{y}$ not using $x_i$ to make predictions whereas SSE is the sum of squared prediction errors taking into account $x_i$ as in (1.1), then $R^2$ can be interpreted as the proportional reduction in prediction error variability due to knowing $x_i$.

$R^2$ is not a measure of model fit,but rather,as the name *coefficient of determination* implies,a measure of how well $x_i$ predicts $y_i$ using a linear relationship.If the true model is a regression model with a large residual variance,then $R^2$ will tend to be small,and it would therefore be erroneous to interpret a small $R^2$ as indicating model misspecification.$R^2$ is also not a measure of the magnitude of the effect of $x_i$(or effect size),which is estimated by $\widehat{\beta}_2$.For a given estimated effect size $\widehat{\beta}_2$,$R^2$ will decrease as theestimated residual variance $\widehat{\sigma}^{2}$ increases (leading to a larger SSE)or the sample variance of $x_i$ decreases (leading to a larger MSS).


* **第一部分：回归系数和最小二乘法（OLS）**
1. **回归系数的解释**：回归系数通常被描述为条件期望的变化，而不是因果效应。在计量经济学中，回归系数有时被解释为因果效应，这需要额外的假设，称为外生性假设。
2. **最小二乘法（OLS）**：OLS是一种统计方法，用于估计回归模型中的参数。在简单线性回归中，我们有一个响应变量 $y$ 和一个解释变量 $x$。
3. **预测值 $\widehat{y}_i$ 的形式**：在OLS中，第 $i$ 个观测值的预测值由下面的公式给出：
   $$
   \widehat{y}_i = \widehat{\beta}_1 + \widehat{\beta}_2x_i
   $$
   这里，$\widehat{\beta}_1$ 是截距项，$\widehat{\beta}_2$ 是斜率，$x_i$ 是第 $i$ 个观测值的 $x$ 变量。
4. **总平方和（TSS）、回归平方和（MSS）和残差平方和（SSE）**：这些是衡量模型拟合优度的统计量。
   - TSS（总平方和）是所有观测值与其均值差值的平方和。
   - MSS（回归平方和）是模型预测值与观测均值差值的平方和。
   - SSE（残差平方和）是预测值与实际观测值差值的平方和。
5. **OLS 估计量**：通过最小化 SSE 来获得 $\beta_1$ 和 $\beta_2$ 的OLS估计量。误差项的自由度是 $n-2$，这里 $n$ 是观测值的数量，减去的2是因为估计了两个回归系数。
6. **最大似然估计（MLE）**：在更复杂的模型中使用，与OLS给出相同的回归系数估计，但残差方差估计较小，因为MLE使用 $SSE/n$ 而不是 $SSE/(n-2)$。

* **第二部分：决定系数 $R^2$**
1. **$R^2$ 的定义**：决定系数 $R^2$ 被定义为：
   $$
   R^2 \equiv \frac{\mathrm{MSS}}{\mathrm{TSS}} = \frac{\mathrm{TSS} - \mathrm{SSE}}{\mathrm{TSS}}
   $$
   这可以被解释为模型中包含的协变量 $x_i$（MSS）“解释”的总变异性（TSS）的比例。
2. **$R^2$ 的解释**：如果将TSS视为不使用 $x_i$ 进行预测时的预测误差平方和（即 $\widehat{y}_{i} = \overline{y}$），而SSE是考虑 $x_i$ 进行预测时的预测误差平方和，那么 $R^2$ 可以被解释为由于知道 $x_i$ 而减少的预测误差变异性的比例。
3. **$R^2$ 的局限性**：
   - $R^2$ 不是模型拟合优度的度量，而是衡量 $x_i$ 通过线性关系预测 $y_i$ 的效果的指标。
   - 如果真实模型是一个具有较大残差方差的回归模型，那么 $R^2$ 往往会较小，因此将小的 $R^2$ 解释为模型设定不当是错误的。
   - $R^2$ 也不是 $x_i$ 效应大小的度量，后者由 $\widehat{\beta}_2$ 估计。对于给定的估计效应大小 $\widehat{\beta}_2$，随着估计残差方差 $\widehat{\sigma}^{2}$ 增加（导致较大的 SSE）或 $x_i$ 的样本方差减少（导致较大的 MSS），$R^2$ 会减少。

* **例子**
假设我们有一个关于房屋价格（$y$）和房屋面积（$x$）的数据集。我们想要知道房屋面积对价格的影响。
1. **收集数据**：我们收集了一组房屋的面积和价格数据。
2. **建立模型**：我们使用简单线性回归模型 $y = \beta_1 + \beta_2x + \epsilon$。
3. **估计系数**：通过OLS，我们估计出 $\beta_1$（截距）和 $\beta_2$（斜率）。
4. **计算 $R^2$**：我们计算TSS、MSS和SSE，然后使用这些值来计算 $R^2$。
5. **解释 $R^2$**：如果 $R^2$ 为0.8，这意味着我们的模型解释了80%的房屋价格变异性。
6. **考虑局限性**：即使 $R^2$ 很高，我们也不能得出结论说房屋面积是导致价格变化的原因，因为这可能仅仅是相关性，而不是因果关系。


The Stata command for the simple linear regression model is

```
regress salary market
```


          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(1, 512)       =    101.77
           Model |  1.3661e+10         1  1.3661e+10   Prob > F        =    0.0000
        Residual |  6.8726e+10       512   134231433   R-squared       =    0.1658
    -------------+----------------------------------   Adj R-squared   =    0.1642
           Total |  8.2387e+10       513   160599133   Root MSE        =     11586
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
          market |   34545.22    3424.33    10.09   0.000     27817.75    41272.69
           _cons |   18096.99    3288.01     5.50   0.000     11637.35    24556.64
    ------------------------------------------------------------------------------

The estimated coefficient of marketability is given next to **market** in the regression table, and the estimated intercept is given next to **_cons**. The reason for the label **_cons** is that the intercept can be thought of as the coefficient of a variable that is equal to 1 for each observation, referred to as a constant (because it does not vary).

The estimated regression coefficients, their estimated standard errors, and the estimated residual standard deviations are given in table 1.2 for all models fit in this chapter, starting with the estimates for the model above in the first two columns under "Section 1.5".


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.1.png" style="width:650px;height:470px;" alt="图片描述">
  <figcaption><strong>Table 1.2: OLS estimates for salary data (in U.S. dollars)</strong></figcaption>
</figure>

We obtain the estimates $\widehat{\beta}_1$=18,097, $\widehat{\beta}_2$=34,545 and $\sqrt{\widehat{\sigma^2}}=11,586$ . The estimated intercept $\widehat{\beta}_1$ is the estimated population mean salary when marketability is 0, a value that does not occur in this sample and is meaningless. Before interpreting the estimates, we therefore refit the model after mean-centering **market**:


```
egen mn_market = mean(market)
generate marketc = market - mn_market

regress salary marketc
```

          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(1, 512)       =    101.77
           Model |  1.3661e+10         1  1.3661e+10   Prob > F        =    0.0000
        Residual |  6.8726e+10       512   134231433   R-squared       =    0.1658
    -------------+----------------------------------   Adj R-squared   =    0.1642
           Total |  8.2387e+10       513   160599133   Root MSE        =     11586
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
         marketc |   34545.22    3424.33    10.09   0.000     27817.75    41272.69
           _cons |   50863.87     511.03    99.53   0.000     49859.90    51867.85
    ------------------------------------------------------------------------------

Here **mn_market**, the sample mean of **market**, was subtracted from **market** so that **marketc** equals 0 when **market** is equal to its sample mean. The only estimate that is affected by the centering is the intercept that now represents the estimated population mean salary when marketability is equal to its sample mean (when **marketc** is 0). The estimated standard error of the intercept is considerably smaller after mean-centering because we are no longer extrapolating outside the range of the data.

For faculty working in departments with mean **marketability**, the population mean salary is estimated as $50,864$. For each unit increase in **marketability**, the population mean salary is estimated to increase by $34,545$ (with a 95% confidence interval from $27,818$ to $41,273$). Because **marketability** ranges from 0.71 to 1.33, with no two people differing by as much as one unit, we could consider the effect of a 0.1-point increase in **marketability**, which is associated with an estimated increase in population mean salary of $3,455$. Alternatively, we could standardize **marketability** (giving it a standard deviation of 1 in addition to a mean of 0), in which case the estimated regression parameter would be interpreted as the estimated increase in population mean salary when **marketability** increases by one sample standard deviation.

If we standardize both the response variable **salary** and the covariate **market**, the estimated regression coefficient becomes a standardized regression coefficient, interpreted as the estimated number of standard deviations that **salary** increases, on average, when **marketability** increases by one standard deviation. Standardized regression coefficients can also be obtained using the beta option in the regress command. However, they should be used with caution because they depend on sample-specific standard deviations, which invalidates comparisons across samples. See Greenland, Schlesselman, and Criqui (1986) for further discussion. A similar issue arises for mean-centering based on sample means. It might have been preferable to subtract (where the mean salary for the discipline equals the mean salary across disciplines) from **market** instead of subtracting the sample mean.

To test the null hypothesis that the regression coefficient of marketability is 0, $ H_0: \beta_2 = 0 $, against the two-sided alternative $ H_a: \beta_2 \neq 0 $, we again use a t statistic, now given by

$$t~=~\frac{\widehat{\beta_2}}{\widehat{\mathrm{SE}}(\widehat{\beta_2})}$$

If the null hypothesis is true, this statistic has a distribution with degrees of freedom given by the error degrees of freedom, denoted **Residual df** in the regression output. Here, $t = 10.09$, $df = 512$, and $p < 0.001$, so we can reject the null hypothesis at the 5% level of significance. The null hypothesis for the intercept $ H_0: \beta_1 = 0 $ is usually irrelevant, and p-values for intercepts are thus ignored.

To visualize the fitted model, we can calculate predicted values $\widehat {y}_i$ by using the postestimation command **`predict`** with the **`xb`** option (where **`xb`** is short for the vector multiplication “x times beta” and stands for $\widehat{\beta}_1$ + $\widehat{\beta}_2$ $x_i$ here):

```
predict yhat, xb
```

We can then produce a scatterplot of the data points $(y_i, x_i)$ together with a line connecting the predicted points $(\widehat{y}_i, x_i)$ with the following command:

```
twoway (scatter salary market, msize(small)) ///
       (line yhat market, sort lwidth(medthick) lpatt(solid)), ///
        ytitle(Academic salary) xtitle(Marketability)
```

The graph in figure 1.7 shows again that a straight line appears to fit well and that the constant-variance assumption does not appear to be violated.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.2.png" style="width:800px;height:470px;" alt="图片描述">
  <figcaption><strong>Figure 1.7: Scatterplot with predicted line from simple regression</strong></figcaption>
</figure>

There is considerable scatter around the regression line with only 16.6% (R-squared in regression output) of the variability in salaries being explained by marketability.

## 6 Dummy variables

Instead of using a test to compare the population mean salaries between men and women as we did in section 1.4, we can use simple linear regression. This becomes obvious by considering the diagram in figure 1.8, where we have simply used the variable $x_i$, equal to 1 for men and 0 for women, and connected the corresponding conditional expectations of $y_i$ by a straight line.

We are not making any assumption regarding the relationship between the conditional means and $x_i$ because any two means can be connected by a straight line. (In contrast, assuming in the previous section that the conditional means for the 46 values of marketability lay on a straight line was a strong but reasonable assumption in view of figure 1.7.) We are, however, assuming equal conditional variances for the two populations because of the homoskedasticity assumption discussed earlier.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.3.png" style="width:600px;height:470px;" alt="图片描述">
  <figcaption><strong>Figure 1.8: Illustration of simple linear regression with a dummy
variable</strong></figcaption>
</figure>

The model can be written as

$$\mu_{x_i}\equiv E(y_i|x_i)=\beta_1+\beta_2x_i,\quad y_i|x_i\ ～ N(\mu_{x_i},\sigma^2)$$

so that

$$\begin{array}{rcl}\mu_0&=&\beta_1+\beta_2\times0=\beta_1\end{array}$$
$$\begin{array}{rcl}\mu_{1}&=&\beta_{1}+\beta_{2}\times1=\beta_{1}+\beta_{2}\end{array}$$

The intercept can now be interpreted as the expectation for the group coded 0, the reference group (here women), whereas the slope $\beta_2$ represents the difference in expectations $\beta_2 = \mu_1 - \mu_0$ between the group coded 1 (here men) and the reference group.


**第一部分：理解背景和概念**
1. **简单线性回归**：这是一种统计方法，用于分析两个变量之间的关系，其中一个变量（自变量）用来预测另一个变量（因变量）。
2. **虚拟变量（Dummy Variable）**：这是一个特殊的变量，通常取值为0或1，用于表示分类数据。在这段文字中，$x_i$ 就是一个虚拟变量，用来区分性别：男性为1，女性为0。
3. **条件期望**：$E(y_i|x_i)$ 表示在给定自变量 $x_i$ 的条件下，因变量 $y_i$ 的期望值。
4. **同方差性（Homoskedasticity）**：这是一个假设，认为不同组的误差项具有相同的方差。
5. **图1.8**：它展示了如何使用虚拟变量来连接不同性别的薪资条件期望，并用一条直线表示。

**第二部分：数学模型和解释**
1. **模型公式**：
   $$
   \mu_{x_i} \equiv E(y_i|x_i) = \beta_1 + \beta_2 x_i
   $$
   这里，$\mu_{x_i}$ 是在给定 $x_i$ 的条件下，$y_i$ 的期望值。$\beta_1$ 是截距，$\beta_2$ 是斜率，它们是模型的参数。
2. **正态分布假设**：
   $$
   y_i|x_i \sim N(\mu_{x_i}, \sigma^2)
   $$
   这意味着在给定 $x_i$ 的条件下，$y_i$ 服从均值为 $\mu_{x_i}$，方差为 $\sigma^2$ 的正态分布。
3. **计算特定条件的期望**：
   - 当 $x_i = 0$（女性）时：
     $$
     \mu_0 = \beta_1 + \beta_2 \times 0 = \beta_1
     $$
     这表示女性的平均薪资期望是 $\beta_1$。
   - 当 $x_i = 1$（男性）时：
     $$
     \mu_1 = \beta_1 + \beta_2 \times 1 = \beta_1 + \beta_2
     $$
     这表示男性的平均薪资期望是 $\beta_1 + \beta_2$。
4. **参数解释**：
   - **截距 $\beta_1$**：代表参考组（这里是女性）的平均薪资期望。
   - **斜率 $\beta_2$**：代表男性与女性薪资期望的差异，即 $\beta_2 = \mu_1 - \mu_0$。

**例子**
假设我们有以下数据：
- $\beta_1 = 50000$（女性的平均薪资期望）
- $\beta_2 = 10000$（男性比女性的平均薪资期望高出的金额）
那么：
- 女性的薪资期望 $\mu_0 = 50000$
- 男性的薪资期望 $\mu_1 = 50000 + 10000 = 60000$
这意味着在这个模型中，男性的平均薪资期望比女性高出10000。


When a dichotomous variable, coded 0 and 1, is used in a regression model like this, it is referred to as a **dummy variable** or **indicator variable**. A useful convention is to give the dummy variable the name of the group for which it is 1 and to describe it as a dummy variable for being in that group; thus, here we have a dummy variable for being male.

The null hypothesis that $beta_2 = 0$ is equivalent to the null hypothesis that the population means are the same, $\mu_1 - \mu_0 = 0$. We can therefore use simple regression to obtain the same result as previously produced for the two-sided independent-samples test with the equal-variance assumption:

```
regress salary male
```

          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(1, 512)       =     76.96
           Model |  1.0765e+10         1  1.0765e+10   Prob > F        =    0.0000
        Residual |  7.1622e+10       512   139887048   R-squared       =    0.1307
    -------------+----------------------------------   Adj R-squared   =    0.1290
           Total |  8.2387e+10       513   160599133   Root MSE        =     11827
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            male |   10582.63    1206.35     8.77   0.000      8212.64    12952.63
           _cons |   42916.60    1045.40    41.05   0.000     40862.80    44970.41
    ------------------------------------------------------------------------------

The t statistic has the same value as that previously shown for the independent-samples $t$ test (with the equal-variance assumption) apart from the sign, which depends on whether $\mu_0 - \mu_1$ or $\mu_1 - \mu_0$ is estimated and is thus arbitrary.

We can also relax the homoskedasticity assumption in linear regression by replacing the conventional model-based estimator for the standard errors with the so-called sandwich estimator. Simply add the **`vce(robust)`** option (where vce stands for “variance–covariance matrix of estimates”) to the **`regress`** command.

这句话是在讨论如何在回归分析中放宽同方差性（homoskedasticity）的假设。同方差性假设指的是，不同自变量值所产生的因变量的误差（或残差）具有相同的方差。当这个假设不成立时，即存在异方差性（heteroskedasticity），使用传统的基于模型的估计器（如普通最小二乘法，OLS）来估计标准误差就不再有效。

为了解决这个问题，可以使用所谓的“三明治估计器”（sandwich estimator），也称为怀特修正（White's correction）或罗宾逊修正（Robinson's correction）。这种方法通过计算更稳健的标准误差来修正异方差性的影响，即使得估计更加稳健，不受异方差性的影响。

在Stata统计软件中，可以通过在回归命令（`regress`）后添加`vce(robust)`选项来应用这种修正。其中，`vce`代表“估计量的方差-协方差矩阵”（variance–covariance matrix of estimates）。这样，即使数据违反了同方差性假设，也能得到更为可靠的t统计量和置信区间。

```
regress salary male, vce(robust)
```

    Linear regression                               Number of obs     =        514
                                                    F(1, 512)         =     105.26
                                                    Prob > F          =     0.0000
                                                    R-squared         =     0.1307
                                                    Root MSE          =      11827
    
    ------------------------------------------------------------------------------
                 |               Robust
          salary | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            male |   10582.63    1031.46    10.26   0.000      8556.21    12609.05
           _cons |   42916.60     808.18    53.10   0.000     41328.84    44504.37
    ------------------------------------------------------------------------------

The resulting statistic is almost identical to that from the **`ttest`** command with the **`unequal`** option in section 1.3 (10.26 compared with 10.25). In the rest of this chapter, we will use model-based standard errors to facilitate comparisons between estimation methods. (Robust standard errors can perform worse than model-based standard errors in small samples, which is not a problem for the faculty-salary data.)

In this section, we entered a dummy variable into the model in the same way as we would enter a continuous covariate. However, remember that dummy variables are different from continuous variables. For example, it usually does not make sense to evaluate dummy variables at their mean or to report standardized regression coefficients for dummy variables, because a standard-deviation change in a dummy variable, such as male, is meaningless.

The resulting $t$ statistic is almost identical to that from the **`ttest`** command with the **`unequal`** option in section 1.3 (10.26 compared with 10.25). In the rest of this chapter, we will use model-based standard errors to facilitate comparisons between estimation methods. (Robust standard errors can perform worse than model-based standard errors in small samples, which is not a problem for the faculty-salary data.)

The utility of using a regression model with a dummy variable instead of the test to compare two groups becomes evident in the next section, where we want to control or adjust for other variables, which is straightforward in a multiple regression model

## 7 Multiple linear regression

An important question when investigating gender discrimination is whether the men and women being compared are similar in the variables that justifiably affect salaries. As we have seen, there is some variability in marketability, and marketability has an effect on salary. Could the lower mean salary for women be due to women tending to work in disciplines with lower marketability? This is a possible explanation only if women tend to work in disciplines with lower marketability than do men, which is indeed the case:

```
tabstat salary, by(male) statistics(mean sd n)
```
      male |      Mean        SD
    -------+--------------------
     Women | -.0469589  .1314393
       Men |  .0155719  .1518486
    -------+--------------------
     Total | -1.38e-17    .14938
    ----------------------------

A variable like marketability that is associated with both the covariate of interest (here gender) and the response variable is often called a confounder . The impact of ignoring one or more confounders on the estimated gender effect is called confounding.

We could render the comparison of salaries more fair by matching each woman to a man with the same value of marketability; however, this would be cumbersome, and we may not find matches for everyone. Instead, we can assume that marketability has the same, linear effect on salary for both genders and check whether gender has any additional effect after allowing for the effect of marketability.

This can be accomplished by specifying a multiple linear regression model,

$$y_i=\beta_1+\beta_2x_{2i}+\beta_3x_{3i}+\epsilon_i,\quad\epsilon_i|x_{2i},x_{3i} ～ N(0,\sigma^2)$$

where we have multiple covariates or explanatory variables: a dummy variable $x_2i$ for being a man and a continuous variable $x_3i$ representing mean-centered marketability. (We number covariates beginning with 2 to correspond to the regression coefficients.)

For disciplines with mean marketability ($x_3i = 0$), the model specifies that the expected salary is $\beta_1$ for women ($x_2i = 0$) and $\beta_1 + \beta_2$ for men ($x_2i = 1$). Therefore, $\beta_2$ can be interpreted as the difference in population mean salary between men and women in disciplines with mean marketability. Fortunately, $\beta_2$ has an even more general interpretation as the difference in population mean salary between men and women in disciplines with any level of marketability, as long as both genders have the same value for marketability, $x_3i = a$.

$$E(y_i|x_{2i}=1,x_{3i}=a)-E(y_i|x_{2i}=0,x_{3i}=a)=(\beta_1+\beta_2+\beta_3a)-(\beta_1+\beta_3a)=\beta_2$$

When comparing genders, we are now controlling for, adjusting for, partialing out, or keeping constant marketability.

By “controlling” or “keeping constant”, we mean that the comparison between males and females is made for a sub-population of faculty with a given value of marketability. It does not imply any experiment in which some factors are kept constant and others are manipulated. To estimate the gender difference, controlling for marketability, the model makes the strong assumptions that this difference is the same for each value of marketability and that the population mean salary changes linearly with marketability for each gender. Because of the lack of experimental manipulation and the strong model assumptions, it is more cautious to describe coefficients in multiple regression as “adjusted for” other covariates

Figure 1.9 shows model-implied regression lines for this model and how the lines depend on the coefficients. We see that determines the difference in means between men and women (vertical distance between the gender-specific regression lines) for any value of marketc.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.4.png" style="width:600px;height:470px;" alt="图片描述">
  <figcaption><strong>Figure 1.9: Illustration of multiple regression with a dummy variable for male ($x_{2i}$) and a continuous covariate, marketc($x_{3i}$)</strong></figcaption>
</figure>

The gender-specific regression lines both have slope, interpretable as the increase in population mean salary per unit increase in marketability, for a given gender or controlling for gender.

The Stata command for the model is

```
regress salary male marketc
```

          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(2, 511)       =     85.80
           Model |  2.0711e+10         2  1.0356e+10   Prob > F        =    0.0000
        Residual |  6.1676e+10       511   120696838   R-squared       =    0.2514
    -------------+----------------------------------   Adj R-squared   =    0.2485
           Total |  8.2387e+10       513   160599133   Root MSE        =     10986
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            male |    8708.42    1139.41     7.64   0.000      6469.92    10946.93
         marketc |   29972.60    3301.77     9.08   0.000     23485.89    36459.30
           _cons |   44324.08     983.35    45.07   0.000     42392.17    46256.00
    ------------------------------------------------------------------------------

The difference in population mean salaries between men and women, controlling for marketability, is estimated as $8,708$, considerably smaller than the unadjusted difference of $10,583$ that we estimated in the previous section. The estimated coefficient of **marketc** is also reduced and is now interpretable as the effect of marketability for a given gender or the within-gender effect of marketability. The coefficient of determination is now $r^2=0.251$ compared with $R^2 = 0.166$ for the model containing only marketc (R2 cannot decrease when more covariates are added).

We can obtain predicted salaries $\widehat y_i$ and plot them with the observed salaries $y_i$ against mean-centered marketability $x_{3i}$ separately for each gender $x_{2i}$ by using

```
predict yhat2, xb

twoway (scatter salary marketc if male==1, msymbol(o))    /// observed salaries if male == 1 
       (line yhat2 marketc if male==1, sort lpatt(dash)) ///  predict line  yhat2 if male == 1
       (scatter salary marketc if male==0, msymbol(oh))   ///
       (line yhat2 marketc if male==0, sort lpatt(solid)), ///
       ytitle(Academic salary) xtitle(Mean-centered marketability)    ///
       legend(order(1 " " 2 "Men" 3 " " 4 "Women"))
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.5.png" style="width:640px;height:470px;" alt="图片描述">
  <figcaption><strong>Figure 1.10: Scatterplot with predicted lines from multiple regression</strong></figcaption>
</figure>

In figure 1.10, the vertical distance between the regression lines is $\widehat \beta_2$, and the slope of both regression lines is $\widehat \beta_1$ (see also figure 1.9). The figure suggests that there is considerable overlap in the distributions of marketability for men and women. If there were little overlap (for instance, with men having higher values of marketability than women), the estimate of the coefficient of male would rely on extrapolating the regression line for males to low values of marketability and the regression line for females to high values of marketability. Such extrapolation beyond the range of data for each gender would hinge completely on the linearity assumption and would therefore be problematic. The degree of overlap, or common support, can be better assessed by plotting estimated probability density functions of marketc for both genders on the same graph.

```
twoway (kdensity marketc if male==0) (kdensity marketc if male==1),  ///
       legend(order(2 "Men" 1 "Women")) xtitle(Mean-centered marketability) ///
	     ytitle(Estimated density)
```

where **`kdensity`** uses a kernel density smoother to obtain a smooth version of a histogram. We see in figure 1.11 that there is very good overlap between the densities except beyond marketc equal to 0.3, which occurs only for a few men.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.6.png" style="width:640px;height:470px;" alt="图片描述">
  <figcaption><strong>Figure 1.11: Estimated densities of marketc for men and women</strong></figcaption>
</figure>

Figure 1.12 illustrates the ideas of confounding by using a hypothetical population model and simulated data. (To exaggerate the confounding, the data were simulated with little common support, but here we know that the relationships are linear.) There are two treatment groups, whose responses are represented by hollow and filled circles. The lines in the top panel represent the fixed part of the true regression model, which includes a dummy variable for treatment and a continuous covariate $x_i$ that is correlated with treatment. The coefficient of treatment is given by the vertical distance between the regression lines. The OLS estimator of the regression coefficient for treatment in the model including both treatment and the covariate (that is, the true model) is an unbiased estimator of this coefficient.

In the bottom panel, the vertical distance between the lines instead represents the difference in marginal population means for the treatments. This difference might be the parameter of interest. However, if we are interested in the regression coefficient of treatment in the true model, it is clear from the figure that $x_i$ is a confounder, because $x_i$ is associated with both the treatment (the mean of $x_i$ is larger for the treatment represented by hollow circles) and the response (the mean of $y_i$ is larger for larger values of $x_i$). Thus, the conditional difference in population means, given $x_i$, represented by the vertical distance between the population regression lines in the top panel, is different from the unconditional counterpart in the lower panel. If the regression lines in the top panel had coincided (with no vertical distance between them), then the association between the response variable and the treatment would be said to be spurious. For a further discussion of confounding—relating the idea to the concepts of causality and exogeneity, and giving an expression for omitted-variable bias—see section 1.13.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.7.png" style="width:640px;height:470px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.8.png" style="width:640px;height:470px;" alt="图片描述">
  <figcaption><strong>Figure 1.12: Illustration of confounding: Top panel shows conditional population means for the treatment groups, given $x$ (true, data-generating model); bottom panel shows population means for the treatment groups, not conditioning on $x$.</strong></figcaption>
</figure>


The model used in this section is sometimes called an analysis of covariance (ANCOVA) model because in addition to the categorical explanatory variable or factor (gender) used in one-way ANOVA, there is a continuous covariate marketability. Departing from the ANOVA/ANCOVA terminology, throughout this book we use the word covariate for any observed explanatory variable, including dummy variables.

Another covariate we should perhaps control for is time since the degree (in years), yearsdg, and we can do so by using

```
regress salary male marketc yearsdg
```

          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(3, 510)       =    367.56
           Model |  5.6333e+10         3  1.8778e+10   Prob > F        =    0.0000
        Residual |  2.6054e+10       510  51087083.4   R-squared       =    0.6838
    -------------+----------------------------------   Adj R-squared   =    0.6819
           Total |  8.2387e+10       513   160599133   Root MSE        =    7147.5
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            male |    2040.21     783.12     2.61   0.009       501.67     3578.75
         marketc |   38402.39    2171.69    17.68   0.000     34135.83    42668.95
         yearsdg |     949.26      35.95    26.41   0.000       878.63     1019.88
           _cons |   34834.30     733.79    47.47   0.000     33392.68    36275.93
    ------------------------------------------------------------------------------

For a given marketability and time since degree, the estimated population mean salary for men is $2,040$ greater than for women, a substantial reduction in the estimated gender gap due to controlling for yearsdg. For a given gender and time since degree, the estimated effect of marketability is $38,402$ extra mean salary per unit increase in marketability. Comparing professors of the same gender from disciplines with the same marketability, the estimated effect of time since degree is $949$ extra mean salary per year since degree. It is tempting to interpret this coefficient as an effect of experience, but those with, for instance, yearsdg differ from those with yearsdg not only in terms of experience but also because they were recruited in a different epoch. As we will discuss in Part III: Introduction to models for longitudinal and panel data, we cannot separate such cohort effects from age or experience effects by using cross-sectional data.

We will now use Stata’s **`margins`** command to obtain different kinds of predicted salaries for males and females based on the multiple regression model. To do this, we must first rerun the regression, this time declaring **male** as a categorical variable by using the factor-variable notation **i.male** (see section 1.8 for more on factor variables).

```
regress salary i.male marketc yearsdg
```

          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(3, 510)       =    367.56
           Model |  5.6333e+10         3  1.8778e+10   Prob > F        =    0.0000
        Residual |  2.6054e+10       510  51087083.4   R-squared       =    0.6838
    -------------+----------------------------------   Adj R-squared   =    0.6819
           Total |  8.2387e+10       513   160599133   Root MSE        =    7147.5
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            male |
            Men  |    2040.21     783.12     2.61   0.009       501.67     3578.75
         marketc |   38402.39    2171.69    17.68   0.000     34135.83    42668.95
         yearsdg |     949.26      35.95    26.41   0.000       878.63     1019.88
           _cons |   34834.30     733.79    47.47   0.000     33392.68    36275.93
    ------------------------------------------------------------------------------

We see that Stata is using the variable name, **male**, as the superheading for this categorical variable and then listing the value label(s), here just one, **Men**, for the group for which Stata created a dummy variable. The interpretation of this coefficient is identical to that when we use **male** directly as a dummy variable.

We obtain predicted mean estimates for the two genders for certain values of the other covariates in the model, for instance, **marketc** and **yearsdg**, by using

```
margins male, at(marketc=0 yearsdg=10)
```

    Adjusted predictions                                       Number of obs = 514
    Model VCE: OLS
    
    Expression: Linear prediction, predict()
    At: marketc =  0
        yearsdg = 10
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            male |
          Women  |   44326.89     639.76    69.29   0.000     43070.00    45583.78
            Men  |   46367.10     444.06   104.42   0.000     45494.69    47239.50
    ------------------------------------------------------------------------------

The difference in the means reported under **Margin** is equal to the estimated coefficient of the male dummy variable.

Alternatively, we can predict the mean salaries that males and females would have if both genders had the same distribution of the covariates **marketc** and **yearsdg**, namely, the distribution of the combined sample of males and females:

```
margins male
```

    Predictive margins                                         Number of obs = 514
    Model VCE: OLS
    
    Expression: Linear prediction, predict()
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            male |
          Women  |   49331.73     667.28    73.93   0.000     48020.78    50642.68
            Men  |   51371.94     370.71   138.58   0.000     50643.64    52100.24
    ------------------------------------------------------------------------------

One way of obtaining this predictive margin for females ourselves would be to set **male** to 0 for the entire sample, obtain the predicted value $\widehat y_i$, and average it over the sample; we would do similarly for males. Because the predictions are linear in the covariates, the same results would be obtained by substituting the mean of **marketc** and **yearsdg** into the prediction formula, which can be achieved using the command **`margins male, atmeans`**. Such predictions are also often referred to as adjusted means. We see that the difference between males and females is again equal to the estimated coefficient of the dummy variable for males.

## 8 Interactions

The models considered in the previous section assumed that the effects of different covariates were additive. For instance, if the dummy variable $x_{2i}$ changes from 0 (women) to 1 (men), the mean salary increases by an amount regardless of the values of the other covariates (marketc and yearsdg).

However, this is a strong assumption that can be violated. The gender difference may depend on yearsdg if, for instance, starting salaries are similar for men and women but men receive larger or more frequent increases. We can investigate this possibility by including an interaction between gender and time since degree. An interaction between two variables implies that the effect of each variable depends on the value of the other variable: in our case, the effect of gender depends on time since degree and the effect of time since degree depends on gender.

We can incorporate the interaction in the regression model (with the usual assumptions) by simply including the product of male ($x_{2i}$) and yearsdg ($x_{4i}$) as a further covariate with regression coefficient $\beta_5$:


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.9.png" style="width:640px;height:110px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

From (1.2), we see that the effect of male (also called the gender gap) is given by $\beta_2+\beta_5$ yearsdg and hence depends on time since degree if $\beta_5 \neq 0$. From (1.3), we see that the effect of yearsdg is given by $\beta_4+\beta_5$ male and hence depends on gender if $\beta_5 \neq 0$. We can describe time since degree as a moderator or an effect modifier of the effect of gender or vice versa.

When including an interaction between two variables, it is usually essential to keep both variables in the model. For instance, dropping male or setting $\beta_2 = 0$ would force the gender gap to be exactly 0 when time since degree is 0, which is a completely arbitrary constraint unless it corresponds to a specific research question.

An illustration of the model is given in figure 1.13 (with marketc set to $0$; $x_{3i} = 0$). If $\beta_5$ were $0$, we would obtain two parallel regression lines with vertical distance $\beta_2$. We see that $\beta_5$ represents the additional slope for men compared with women, or the additional gender gap when $yearsdg$ increases by one unit. We also see that $\beta_2$ is the gender gap when $yearsdg = 0$ and is the slope of $yearsdg$ when $male = 0$.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.10.png" style="width:540px;height:410px;" alt="图片描述">
  <figcaption><strong>Figure 1.13: Illustration of interaction between male ($x_{2i}$) and yearsdg ($x_{4i}$) for marketc ($x_{3i}$) equal to $0$ (not to scale)</strong></figcaption>
</figure>

To fit this model in Stata, we can generate the interaction as the product of the dummy variable for being male and years since degree:

```
generate male_years = male*yearsdg
```

We can then include the interaction in the regression:

```
regress salary male marketc yearsdg male_years
```

          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(4, 509)       =    279.95
           Model |  5.6641e+10         4  1.4160e+10   Prob > F        =    0.0000
        Residual |  2.5746e+10       509  50581607.4   R-squared       =    0.6875
    -------------+----------------------------------   Adj R-squared   =    0.6850
           Total |  8.2387e+10       513   160599133   Root MSE        =    7112.1
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            male |    -593.31    1320.91    -0.45   0.654     -3188.42     2001.80
         marketc |   38436.65    2160.96    17.79   0.000     34191.14    42682.15
         yearsdg |     763.19      83.42     9.15   0.000       599.31      927.07
      male_years |     227.15      92.00     2.47   0.014        46.41      407.89
           _cons |   36773.64    1072.39    34.29   0.000     34666.78    38880.51
    ------------------------------------------------------------------------------

Here it is natural to interpret the interaction in terms of the effect of gender. When time since degree is $0$ years, the population mean salary for men minus the population mean salary for women (after adjusting for marketability) is estimated as $-593$. For every additional year since completing the degree, we add $227$ to the difference, giving a difference of $0$ after a little over $2$ years; a difference of about $-593.31 + 227.15 \times 10 = 1678$ after $10$ years; a difference of $-593.31 + 227.15 \times 20 = 3959$ after $20$ years; and a difference of $-593.31 + 227.15 \times 30 = 6221$ after $30$ years. Although the estimated gender gap at $0$ years is not statistically significant at the $5\%$ level ($t = -0.45, df = 509, p = 0.65$), the change in gender gap with years since degree (or interaction) is significant ($t = 2.47, df = 509, p = 0.01$).

We might wonder if the gender gap is statistically significant for faculty with $10$ years of experience (adjusting for marketability), hence testing the null hypothesis $H_0: \beta_2 + \beta_5 \times 10 = 0$ against the two-sided alternative. This null hypothesis involves a linear combination of coefficients, and we can use the **`lincom`** command (which stands for "linear combination") to perform the test.

```
lincom male + male_years*10
```

     ( 1)  male + 10*male_years = 0
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             (1) |    1678.22     792.91     2.12   0.035       120.44     3236.00
    ------------------------------------------------------------------------------

giving $t = 2.12$, $df = 509$, and $p = 0.04$. We also obtain a $95\%$ confidence interval for the adjusted difference in population mean salaries at $10$ years since degree, which ranges from $120$ to $3,236$.

The regression model now includes three covariates (and an interaction), and it is difficult to represent it with a two-dimensional graph. However, as in figure 1.13, we can hold marketc constant and display the estimated population mean salary as a function of the other variables when marketc is $0$. To do this, we could first set marketc to $0$ and then use **`predict`**, or we can use Stata’s **`twoway function`** command to produce figure 1.14:

```
twoway (function Women = 36773 + 763.19*x, range(0 41) lpatt(dash)) ///
 (function Men = 36773 + -593.31 + (763.19 + 227.15)*x,             ///
 range(0 41) lpatt(solid)), xtitle(Time since degree (years))       ///
 ytitle(Mean salary)
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.11.png" style="width:540px;height:410px;" alt="图片描述">
  <figcaption><strong>Figure 1.14: Estimated effect of gender and time since degree on mean salary for disciplines with mean marketability</strong></figcaption>
</figure>

Here we have typed in the predicted regression lines for women and men as a function of **yearsdg** (here referred to as x), using (1.3) with male=0 for women, male=1 for men, and marketc=0 (that is, x_3i=0).

Because we have just fit the model in Stata, we can also refer to the regression coefficients as _b[_cons] for the intercept, _b[yearsdg] for the coefficient of yearsdg, etc., saving us from having to type in all the coefficients, which is error-prone. The Stata command then looks like this:


```
/*
twoway (function Women =_b[_cons] + _b[yearsdg]*x, range(0 41) lpatt(dash))  ///
 (function Men =_b[_cons] + _b[male] + (_b[yearsdg] + _b[male_years])*x,        ///
 range(0 41) lpatt(solid)), xtitle(Time since degree (years)) ytitle(Mean salary)
*/
```

Instead of creating the interaction variable **male_years** first and then including it as a covariate in the regression model, we can also use factor variables to specify the interactions within the regress command (see also help fvvarlist for more information). To introduce an interaction between two variables, simply bind them together with a hash, #, making sure to declare their type: use the prefix i. for categorical variables (where i. stands for indicators, another term for dummy variables), and use c. for continuous variables. The command becomes

```
regress salary male marketc yearsdg i.male#c.yearsdg
```

          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(4, 509)       =    279.95
           Model |  5.6641e+10         4  1.4160e+10   Prob > F        =    0.0000
        Residual |  2.5746e+10       509  50581607.4   R-squared       =    0.6875
    -------------+----------------------------------   Adj R-squared   =    0.6850
           Total |  8.2387e+10       513   160599133   Root MSE        =    7112.1
    
    --------------------------------------------------------------------------------
            salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    ---------------+----------------------------------------------------------------
              male |    -593.31    1320.91    -0.45   0.654     -3188.42     2001.80
           marketc |   38436.65    2160.96    17.79   0.000     34191.14    42682.15
           yearsdg |     763.19      83.42     9.15   0.000       599.31      927.07
                   |
    male#c.yearsdg |
              Men  |     227.15      92.00     2.47   0.014        46.41      407.89
                   |
             _cons |   36773.64    1072.39    34.29   0.000     34666.78    38880.51
    --------------------------------------------------------------------------------

The estimated interaction parameter $\widehat \beta_5$ is given under the heading male#c.yearsdg next to Men, the value label for male=1 (the label for male=0 is Female). The label Men helps us interpret the sign of the coefficient. The positive sign means that men have a greater slope of yearsdg than women, and not vice versa.

A more compact way to specify the model is to use two hashes for the interaction term, i.male##c.yearsdg, which stands for each variable i.male and c.yearsdg on its own, as well as the interaction:

```
regress salary marketc i.male##c.yearsdg
```

          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(4, 509)       =    279.95
           Model |  5.6641e+10         4  1.4160e+10   Prob > F        =    0.0000
        Residual |  2.5746e+10       509  50581607.4   R-squared       =    0.6875
    -------------+----------------------------------   Adj R-squared   =    0.6850
           Total |  8.2387e+10       513   160599133   Root MSE        =    7112.1
    
    --------------------------------------------------------------------------------
            salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    ---------------+----------------------------------------------------------------
           marketc |   38436.65    2160.96    17.79   0.000     34191.14    42682.15
                   |
              male |
              Men  |    -593.31    1320.91    -0.45   0.654     -3188.42     2001.80
           yearsdg |     763.19      83.42     9.15   0.000       599.31      927.07
                   |
    male#c.yearsdg |
              Men  |     227.15      92.00     2.47   0.014        46.41      407.89
                   |
             _cons |   36773.64    1072.39    34.29   0.000     34666.78    38880.51
    --------------------------------------------------------------------------------

The output for i.male now mimics the output for the interaction term by having a heading, here male, followed by the label Men.

In the lincom command, coefficients are generally referred to by the names shown in the regression output. The exception is for factor-variable terms that include i. in the syntax, here i.male and i.male#c.yearsdg. While the output drops the i., we must replace the i. by 1. to refer to the value 1 (labeled Men) for the variable male. (To see the numbers in the output instead of the value labels, use the nofvlabel option.) So, 10 years after the degree, the gender difference is estimated using

```
lincom 1.male + 1.male#c.yearsdg*10
```

     ( 1)  1.male + 10*1.male#c.yearsdg = 0
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             (1) |    1678.22     792.91     2.12   0.035       120.44     3236.00
    ------------------------------------------------------------------------------

Similarly, in the twoway command for producing figure 1.14, we refer to the coefficients as _b[1.male] and _b[1.male#c.yearsdg]. (To find out how to refer to the coefficients, use the coeflegend option in the regress command.) Because we used factor-variable notation, we can use the margins command with the r. contrast operator to obtain the result above, as follows:

```
margins r.male, at(yearsdg=10) contrast(nowald effects)
```

    Contrasts of predictive margins                            Number of obs = 514
    Model VCE: OLS
    
    Expression: Linear prediction, predict()
    At: yearsdg = 10
    
    ---------------------------------------------------------------------------------
                    |            Delta-method
                    |   Contrast   std. err.      t    P>|t|     [95% conf. interval]
    ----------------+----------------------------------------------------------------
               male |
    (Men vs Women)  |    1678.22     792.91     2.12   0.035       120.44     3236.00
    ---------------------------------------------------------------------------------
    
We could reproduce the graph in figure 1.14 with `marginsplot`. We first would use `margins` to obtain predicted values of salary across a range of yearsdg while setting marketc to 0.

```
quietly margins male, at(marketc==0 yearsdg=(0(10)40))
```

We would then plot the predictions by using `marginsplot`:

```
marginsplot, noci recast(line) plot1opts(lpatt(dash)) ///
	ytitle(Mean salary) xtitle(Time since degree (years)) title("")
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.12.png" style="width:540px;height:410px;" alt="图片描述">
  <figcaption><strong>Figure 1.14: Estimated effect of gender and time since degree on mean salary for disciplines with mean marketability</strong></figcaption>
</figure>

Here **`noci`** suppresses the display of confidence intervals, **`recast(line)`** shows lines only, instead of lines and dots, and **`plot1opts(lpatt(dash))`** changes the line style for the first line (for women) to dashed.

We could have included an interaction in our model between marketc and male and, as will be discussed in section 1.10.1, an interaction between the two continuous covariates marketc and yearsdg. In addition to these two-way interactions, we could consider the three-way interaction represented by the product of all three covariates. However, such higher-order interactions are rarely used because they are difficult to interpret.

## 9 Dummy variables for more than two groups

Another important explanatory variable for salary is academic rank, coded in the variable rank as 1 for assistant professor, 2 for associate professor, and 3 for full professor.

Using rank as a continuous covariate in a simple regression model would force a constraint on the population mean salaries for the three groups, namely, that the mean salary of associate professors is halfway between the mean salaries of assistant and full professors:


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.13.png" style="width:540px;height:90px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Such linearity is a strong assumption for an ordinal variable, such as rank, and a meaningless assumption for unordered categorical covariates, such as ethnicity, where the ordering of the values assigned to categories is arbitrary. It thus makes sense to estimate the mean of each rank freely by treating one of the ranks, for instance, assistant professor, as the reference category and by using dummy variables for the other two ranks.

We can create the dummy variables by typing

```
generate associate = rank==2 if !missing(rank)
generate full = rank==3 if !missing(rank)
```

Here the logical expression rank==2 evaluates to 1 if it is true and 0 otherwise. This expression yields a 0 when rank is 1, 3, or missing, but we do not want to interpret a missing value; therefore, the if condition is necessary to ensure that missing values in rank translate to missing values in the dummy variables.

Our preferred method for producing dummy variables is by using the `tabulate` command with the `generate()` option, as follows:

```
drop associate full
tabulate rank, generate(r)
rename r2 associate
rename r3 full
```

           rank |      Freq.     Percent        Cum.
    ------------+-----------------------------------
      Assistant |        143       27.82       27.82
      Associate |        160       31.13       58.95
           Full |        211       41.05      100.00
    ------------+-----------------------------------
          Total |        514      100.00
  
The **``generate(r)``** option produces dummy variables for each unique value of rank, here named r1, r2, and r3 for the values 1, 2, and 3. (The naming of the dummy variables would have been the same if the unique values had been 0, 1, and 4.) An advantage of using **``tabulate``** is that it places missing values into dummy variables whenever the original variable is missing—as shown above, this requires extra caution when using the **``generate``** command.

Denoting these dummy variables $x_{2i}$ and $x_{3i}$ , respectively, we specify the model


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.14.png" style="width:540px;height:90px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Showing that the intercept $\beta_1$ represents the population mean salary for the reference category (assistant professors), $\beta_2$ represents the difference in mean salaries between associate and assistant professors, and $\beta_3$ represents the difference in mean salaries between full and assistant professors. Hence, the coefficient of each dummy variable represents the population mean of the corresponding group minus the population mean of the reference group. Figure 1.15 illustrates how the model-implied means for the three ranks are determined by the regression coefficients.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.15.png" style="width:540px;height:410px;" alt="图片描述">
  <figcaption><strong>Figure 1.15: Illustration: Interpretations of coefficients of dummy variables $x_{2i}$ and $x_{3i}$ for associate and full professors, with assistant professors as the reference category</strong></figcaption>
</figure>

Estimates for the regression model with dummy variables for academic rank are obtained using

```
regress salary associate full
```
    
          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(2, 511)       =    262.54
           Model |  4.1753e+10         2  2.0877e+10   Prob > F        =    0.0000
        Residual |  4.0634e+10       511  79518710.1   R-squared       =    0.5068
    -------------+----------------------------------   Adj R-squared   =    0.5049
           Total |  8.2387e+10       513   160599133   Root MSE        =    8917.3
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
       associate |    7285.12    1026.19     7.10   0.000      5269.05     9301.19
            full |   21267.11     965.89    22.02   0.000     19369.51    23164.71
           _cons |   39865.86     745.70    53.46   0.000     38400.84    41330.88
    ------------------------------------------------------------------------------
    
We see that the estimated difference in population means between associate professors and assistant professors is $7,285$ and that the estimated difference in population means between full professors and assistant professors is $21,267$. The mean salary for assistant professors is estimated as $39,866$. We can obtain the estimated population mean salary for associate professors, $\hat{\beta}_1 + \hat{\beta}_2$, by using

```
lincom _cons + associate
```

     ( 1)  associate + _cons = 0
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             (1) |   47150.98     704.98    66.88   0.000     45765.97    48535.99
    ------------------------------------------------------------------------------

We can obtain the estimated difference in population mean salary between full and associate professors

```
lincom full-associate
```

     ( 1)  - associate + full = 0
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             (1) |   13981.99     934.80    14.96   0.000     12145.45    15818.52
    ------------------------------------------------------------------------------

```
lincom full- associate
```

     ( 1)  - associate + full = 0
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             (1) |   13981.99     934.80    14.96   0.000     12145.45    15818.52
    ------------------------------------------------------------------------------

The difference in estimated population mean salaries between full and associate professors ($13,982$) is considerably larger than the difference between associate and assistant professors ($7,285$), suggesting that the constraint imposed if academic rank were treated as a continuous covariate is unreasonable.

We can fit the same regression model without forming dummy variables ourselves by using factor variables, that is, by simply preceding the categorical covariate(s) with the `i.` prefix:

```
reg salary i.rank
```

The `i.` prefix treats the lowest value of a categorical variable as the reference category. For rank, the lowest value is 1 (assistant professor), and we wanted to treat assistant professors as the reference category. If we had instead wanted to treat the value 3 (full professors) as the reference category, we could have replaced `i.rank` by `ib3.rank` or `b3.rank`. Here the "b" stands for base level, another term for the reference category.

Another way to obtain the above `lincom` results when using factor variable notation is by using the `contrast` command as follows:

```
contrast r3b2.rank
```

Here `r3b2` is a contrast operator, where `r3` says that we want to compare category 3 to the reference (or base) category and `b2` says that we want to treat category 2 as the base.

Instead of using regression with dummy variables, we could use one-way ANOVA with groups (see table 1.1). The one-way ANOVA model with groups is sometimes written as

$$y_{ij}=\beta+\alpha_j+\epsilon_{ij},\quad\sum_{j=1}^g\alpha_j=0,\quad\epsilon_{ij}\sim N(0,\sigma^2)$$

The Stata command to fit this model is

```
anova salary rank
```

                             Number of obs =        514    R-squared     =  0.5068
                             Root MSE      =    8917.33    Adj R-squared =  0.5049
    
                      Source | Partial SS         df         MS        F    Prob>F
                  -----------+----------------------------------------------------
                       Model |  4.175e+10          2   2.088e+10    262.54  0.0000
                             |
                        rank |  4.175e+10          2   2.088e+10    262.54  0.0000
                             |
                    Residual |  4.063e+10        511    79518710  
                  -----------+----------------------------------------------------
                       Total |  8.239e+10        513   1.606e+08  

This command produces the same sums of squares, mean squares, and $F$ statistic as given at the top of the regression output but no estimates of population means or their differences. The test is a test of the null hypothesis that all three population means are the same, or in other words, that the coefficients $\beta_2$ and $\beta_3$ of the dummy variables are both 0. The alternative hypothesis is that at least one of the coefficients differs from 0. Such joint or simultaneous hypotheses can also be tested using **`testparm`** after fitting the regression model (see below).

Adding the dummy variables for academic rank to the regression model from the previous section, we obtain

```
regress salary i.male##c.yearsdg marketc i.rank
```
    
          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(6, 507)       =    242.32
           Model |  6.1086e+10         6  1.0181e+10   Prob > F        =    0.0000
        Residual |  2.1301e+10       507  42014709.8   R-squared       =    0.7414
    -------------+----------------------------------   Adj R-squared   =    0.7384
           Total |  8.2387e+10       513   160599133   Root MSE        =    6481.9
    
    --------------------------------------------------------------------------------
            salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    ---------------+----------------------------------------------------------------
              male |
              Men  |   -1043.39    1215.03    -0.86   0.391     -3430.52     1343.73
           yearsdg |     405.27      86.73     4.67   0.000       234.88      575.67
                   |
    male#c.yearsdg |
              Men  |     184.38      85.07     2.17   0.031        17.25      351.50
                   |
           marketc |   36987.08    1974.89    18.73   0.000     33107.11    40867.06
                   |
              rank |
        Associate  |    3349.01     871.62     3.84   0.000      1636.58     5061.43
             Full  |   11168.26    1167.81     9.56   0.000      8873.92    13462.60
                   |
             _cons |   37493.09     988.66    37.92   0.000     35550.72    39435.46
    --------------------------------------------------------------------------------

The estimated coefficients of Associate and Full are considerably lower than before because they now represent the estimated adjusted or partial differences in population means, holding the other covariates in the model constant. However, interpreting the effect of rank, adjusted for yearsdg, may be problematic because rank and years since degree are inherently strongly associated. Therefore, estimating the adjusted difference between full and assistant professors effectively requires extrapolation for full professors to unrealistically low values of yearsdg and for assistant professors to unrealistically high values (there is little common support).

We can test the null hypothesis that the coefficients of these dummy variables are both 0 by using the **`testparm`** command:

```
testparm i.rank
```

     ( 1)  2.rank = 0
     ( 2)  3.rank = 0
    
           F(  2,   507) =   52.89
                Prob > F =    0.0000

The $F$ statistic is equal to the difference in MSS between the models that do and do not contain the two dummy variables for academic rank (but contain all the other terms of the model), divided by the product of the difference in model degrees of freedom and the MSE of the larger model. (Had we used the dummy variables `associate` and `full` instead of the factor variable `i.rank`, the syntax for the above test would have been `testparm associate full`.)

After controlling for academic rank (and the other covariates), the difference in population mean salary between men and women is estimated as $-1043.39 + 184.38 \times yearsdg$, which is lower at every year since degree than the estimate of $-593.31 + 227.15 \times yearsdg$ before adjusting for academic rank. For example, at 10 years since degree, the difference in mean salary is now estimated as about $800$.

```
lincom 1.male + 1.male#c.yearsdg*10
```

     ( 1)  1.male + 10*1.male#c.yearsdg = 0
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             (1) |     800.37     727.97     1.10   0.272      -629.85     2230.59
    ------------------------------------------------------------------------------

The corresponding estimate not adjusting for academic rank was about $1,678$. Alternatively, use the command

```
margins r.male,at(yearsdg=10) contrast(nowald effects)
```

    Contrasts of predictive margins                            Number of obs = 514
    Model VCE: OLS
    
    Expression: Linear prediction, predict()
    At: yearsdg = 10
    
    ---------------------------------------------------------------------------------
                    |            Delta-method
                    |   Contrast   std. err.      t    P>|t|     [95% conf. interval]
    ----------------+----------------------------------------------------------------
               male |
    (Men vs Women)  |     800.37     727.97     1.10   0.272      -629.85     2230.59
    ---------------------------------------------------------------------------------

We see that the estimated gender effect is smaller when it is adjusted for academic rank. However, adjusting for academic rank could be problematic if rank is a mediating or intervening variable on the causal pathway from gender to salary, that is, if gender affects promotions, which in turn affect salary. After controlling for rank, we obtain an estimate of the direct effect of gender on salary, not mediated by rank (although other intervening variables may be involved). Here we must decide whether we are interested in the direct effect or the total effect of gender on salary (the sum of the direct effect and the indirect effect mediated by rank). If we are interested in the direct effect, we should control for rank, whereas we should not control for rank if we are interested in the total effect. Boudreau et al. (1997) discuss a study arguing that gender does not affect academic rank at Bowling Green State University, in which case the direct effect should be the same as the total effect.


## 10 Other types of interactions

### 10.1 Interaction between dummy variables

Could the salary difference between ranks be gender specific? Equivalently, could the gender gap in salaries depend on academic rank? These questions can be answered by including the two interaction terms $male \times associate (x_{2i} \cdot x_{5i})$ and $male \times full (x_{2i} \cdot x_{6i})$ in the model (We omit the male by yearsdg interaction here for simplicity).


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.5.21.16.png" style="width:600px;height:110px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

If the other terms denoted as "···" above are omitted, this model becomes a two-way ANOVA model with main effects and an interaction between academic rank and gender.

An interaction between dummy variables can be interpreted as a difference of a difference. For instance, we see from (1.4) that $\beta_7$ represents the difference between men and women of the difference between the mean salaries of associate and assistant professors.

We now construct the interactions,

```
generate male_assoc = male*associate
generate male_full = male*full
```

and fit the regression model including these interactions:

```
regress salary male marketc yearsdg associate full male_assoc male_full
```

          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(7, 506)       =    205.77
           Model |  6.0969e+10         7  8.7099e+09   Prob > F        =    0.0000
        Residual |  2.1418e+10       506  42328437.6   R-squared       =    0.7400
    -------------+----------------------------------   Adj R-squared   =    0.7364
           Total |  8.2387e+10       513   160599133   Root MSE        =      6506
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            male |     465.23    1118.95     0.42   0.678     -1733.13     2663.60
         marketc |   36950.82    1985.14    18.61   0.000     33050.69    40850.95
         yearsdg |     552.34      51.93    10.64   0.000       450.31      654.37
       associate |    3008.13    1306.74     2.30   0.022       440.81     5575.44
            full |    9098.93    1894.29     4.80   0.000      5377.28    12820.58
      male_assoc |     284.04    1574.62     0.18   0.857     -2809.56     3377.64
       male_full |    2539.39    1919.64     1.32   0.186     -1232.05     6310.83
           _cons |   36397.49     885.95    41.08   0.000     34656.90    38138.09
    ------------------------------------------------------------------------------

From (1.5), we see that the estimated difference in population mean salaries between men and women is $\widehat \beta_2 + \widehat \beta_7 associate + \widehat \beta_8 full$. In other words, the estimated coefficient $\widehat \beta_7$ of $male_{assoc}$ can be interpreted as the difference in estimated gender gap between associate and assistant professors, and similarly for $male_full$. Neither interaction coefficient is significant at the 5% level. However, it is not considered good practice to include only some of the interaction terms for a group of dummy variables representing one categorical variable; hence, we should test both coefficients simultaneously.

```
testparm male_assoc male_full
```
    
     ( 1)  male_assoc = 0
     ( 2)  male_full = 0
    
           F(  2,   506) =    0.95
                Prob > F =    0.3864

There is little evidence for an interaction between gender and academic rank [$F(2,506) = 0.95$, $p=0.39$].

Using factor variables instead of constructing interaction terms ourselves, the **`regress`** command becomes

```
regress salary marketc yearsdg i.male##i.rank
```
    
          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(7, 506)       =    205.77
           Model |  6.0969e+10         7  8.7099e+09   Prob > F        =    0.0000
        Residual |  2.1418e+10       506  42328437.6   R-squared       =    0.7400
    -------------+----------------------------------   Adj R-squared   =    0.7364
           Total |  8.2387e+10       513   160599133   Root MSE        =      6506
    
    --------------------------------------------------------------------------------
            salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    ---------------+----------------------------------------------------------------
           marketc |   36950.82    1985.14    18.61   0.000     33050.69    40850.95
           yearsdg |     552.34      51.93    10.64   0.000       450.31      654.37
                   |
              male |
              Men  |     465.23    1118.95     0.42   0.678     -1733.13     2663.60
                   |
              rank |
        Associate  |    3008.13    1306.74     2.30   0.022       440.81     5575.44
             Full  |    9098.93    1894.29     4.80   0.000      5377.28    12820.58
                   |
         male#rank |
    Men#Associate  |     284.04    1574.62     0.18   0.857     -2809.56     3377.64
         Men#Full  |    2539.39    1919.64     1.32   0.186     -1232.05     6310.83
                   |
             _cons |   36397.49     885.95    41.08   0.000     34656.90    38138.09
    --------------------------------------------------------------------------------

The estimated interaction coefficients are denoted Men#Associate and Men#Full. We could use the `nofvlabel` option with `regress` to display the terms as you would use them in a `lincom` command (for example, Men#Full would be replaced by 1.male#3.rank). The syntax for the `testparm` command for testing the gender by rank interaction becomes

```
testparm i.male#i.rank
```

     ( 1)  1.male#2.rank = 0
     ( 2)  1.male#3.rank = 0
    
           F(  2,   506) =    0.95
                Prob > F =    0.3864

The output above also shows how to refer to the two coefficients representing the interaction between gender and rank, namely, `_b[1.male#2.rank]` and `_b[1.male#3.rank]`.

### 10.2 Interaction between continuous covariates

The effect of marketability, **marketc** ($x_{3i}$), could increase or decrease with time since degree, **yearsdg** ($x_{4i}$). We can include an interaction between these two continuous covariates in a regression model:


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.5.png" style="width:600px;height:110px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


We can then fit this model in Stata by using the commands

```
generate market_yrs = marketc*yearsdg
regress salary male marketc yearsdg associate full market_yrs
```
    
          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(6, 507)       =    247.16
           Model |  6.1397e+10         6  1.0233e+10   Prob > F        =    0.0000
        Residual |  2.0990e+10       507    41401072   R-squared       =    0.7452
    -------------+----------------------------------   Adj R-squared   =    0.7422
           Total |  8.2387e+10       513   160599133   Root MSE        =    6434.4
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            male |     926.13     712.29     1.30   0.194      -473.27     2325.53
         marketc |   46905.65    3455.75    13.57   0.000     40116.31    53695.00
         yearsdg |     540.73      51.40    10.52   0.000       439.74      641.72
       associate |    3303.13     859.65     3.84   0.000      1614.23     4992.04
            full |   11573.46    1165.94     9.93   0.000      9282.80    13864.12
      market_yrs |    -750.42     214.13    -3.50   0.000     -1171.10     -329.73
           _cons |   36044.19     711.62    50.65   0.000     34646.10    37442.27
    ------------------------------------------------------------------------------

Using (1.6), we see that the estimated effect of marketability, $\widehat \beta_3 + \widehat \beta_7 yearsdg$,decreases from $46,906$ for faculty who have just completed their degree to $46906-750.42×30=24.394$ for faculty who completed their degree 30 years ago.

Using factor variables , the model can be fit like this:

```
regress salary i.male c.marketc##c.yearsdg i.rank
```
    
          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(6, 507)       =    247.16
           Model |  6.1397e+10         6  1.0233e+10   Prob > F        =    0.0000
        Residual |  2.0990e+10       507    41401072   R-squared       =    0.7452
    -------------+----------------------------------   Adj R-squared   =    0.7422
           Total |  8.2387e+10       513   160599133   Root MSE        =    6434.4
    
    -------------------------------------------------------------------------------------
                 salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    --------------------+----------------------------------------------------------------
                   male |
                   Men  |     926.13     712.29     1.30   0.194      -473.27     2325.53
                marketc |   46905.65    3455.75    13.57   0.000     40116.31    53695.00
                yearsdg |     540.73      51.40    10.52   0.000       439.74      641.72
                        |
    c.marketc#c.yearsdg |    -750.42     214.13    -3.50   0.000     -1171.10     -329.73
                        |
                   rank |
             Associate  |    3303.13     859.65     3.84   0.000      1614.23     4992.04
                  Full  |   11573.46    1165.94     9.93   0.000      9282.80    13864.12
                        |
                  _cons |   36044.19     711.62    50.65   0.000     34646.10    37442.27
    -------------------------------------------------------------------------------------

## 11 Nonlinear effects

We have assumed that the relationship between population mean salary and each of the continuous covariates marketc and yearsdg is linear after controlling for the other variables. However, the increase in population mean salary per year is likely to increase with time since degree (for instance, if percentage increases are constant over time).

Such a nonlinear relationship can be modeled by including the square of yearsdg in the model in addition to yearsdg itself (adding the interaction male_years back in):

```
generate yearsdg2 = yearsdg^2

regress salary male marketc yearsdg male_years associate full ///
   market_yrs yearsdg2
```
    
          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(8, 505)       =    192.04
           Model |  6.2005e+10         8  7.7507e+09   Prob > F        =    0.0000
        Residual |  2.0382e+10       505  40360475.1   R-squared       =    0.7526
    -------------+----------------------------------   Adj R-squared   =    0.7487
           Total |  8.2387e+10       513   160599133   Root MSE        =      6353
    
    ------------------------------------------------------------------------------
          salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            male |   -1181.82    1251.65    -0.94   0.346     -3640.90     1277.25
         marketc |   46578.20    3544.48    13.14   0.000     39614.45    53541.95
         yearsdg |      39.04     144.88     0.27   0.788      -245.59      323.67
      male_years |     177.71      89.36     1.99   0.047         2.14      353.28
       associate |    4811.53     967.94     4.97   0.000      2909.85     6713.21
            full |   12791.00    1230.26    10.40   0.000     10373.95    15208.05
      market_yrs |    -726.39     222.43    -3.27   0.001     -1163.38     -289.39
        yearsdg2 |      10.11       3.97     2.55   0.011         2.31       17.91
           _cons |   38837.69    1027.38    37.80   0.000     36819.23    40856.16
    ------------------------------------------------------------------------------

The estimated coefficient of **yearsdg2** is significantly different from 0 at, say, the 5% level ($t=2.55, df=505, p=0.01$), whereas the coefficient of yearsdg is no longer statistically significant. It should nevertheless be retained to form a flexible quadratic curve because the minimum of the curve is otherwise arbitrarily forced to occur when **yearsdg**=0.

We can visualize the relationship between salary and time since degree for male and female assistant professors (associate 0, full 0) in disciplines with mean marketability (marketc 0) by using the **twoway function** command, which produces figure 1.16:

```
twoway (function Women = _b[_cons] + _b[yearsdg]*x + _b[yearsdg2]*x^2,      ///
         range(0 41) lpatt(dash))                                           ///
       (function Men = _b[_cons] + _b[male] + (_b[yearsdg] + _b[male_years])*x ///
         + _b[yearsdg2]*x^2, range(0 41) lpatt(solid)),                     ///
        xtitle(Time since degree (years)) ytitle(Mean salary)

```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.6.png" style="width:600px;height:410px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The quadratic term for yearsdg can also be included in the regression by using factor variables, simply by using the expression `c.yearsdg#c.yearsdg`. We can therefore use the following command to fit the same model as before:

```
regress salary i.male##c.yearsdg c.marketc c.marketc#c.yearsdg i.rank 
    c.yearsdg#c.yearsdg
```

Here we use a double-hash, ##, in i.male##c.yearsdg to include i.male, c.yearsdg, and i.male#c.yearsdg. To avoid duplicating the term c.yearsdg, we use a single hash for c.marketc#c.yearsdg and then include the missing term c.marketc. In fact, it is not necessary to avoid duplication because Stata will drop any redundant terms. However, the terms are then listed in the output together with the label omitted. A preferred approach therefore is to factorize out all terms that interact with c.yearsdg by typing

```
regress salary (i.male c.marketc c.yearsdg)##c.yearsdg i.rank
```
    
          Source |       SS           df       MS      Number of obs   =       514
    -------------+----------------------------------   F(8, 505)       =    192.04
           Model |  6.2005e+10         8  7.7507e+09   Prob > F        =    0.0000
        Residual |  2.0382e+10       505  40360475.1   R-squared       =    0.7526
    -------------+----------------------------------   Adj R-squared   =    0.7487
           Total |  8.2387e+10       513   160599133   Root MSE        =      6353
    
    -------------------------------------------------------------------------------------
                 salary | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    --------------------+----------------------------------------------------------------
                   male |
                   Men  |   -1181.82    1251.65    -0.94   0.346     -3640.90     1277.25
                marketc |   46578.20    3544.48    13.14   0.000     39614.45    53541.95
                yearsdg |      39.04     144.88     0.27   0.788      -245.59      323.67
                        |
         male#c.yearsdg |
                   Men  |     177.71      89.36     1.99   0.047         2.14      353.28
                        |
    c.marketc#c.yearsdg |    -726.39     222.43    -3.27   0.001     -1163.38     -289.39
                        |
    c.yearsdg#c.yearsdg |      10.11       3.97     2.55   0.011         2.31       17.91
                        |
                   rank |
             Associate  |    4811.53     967.94     4.97   0.000      2909.85     6713.21
                  Full  |   12791.00    1230.26    10.40   0.000     10373.95    15208.05
                        |
                  _cons |   38837.69    1027.38    37.80   0.000     36819.23    40856.16
    -------------------------------------------------------------------------------------

Stata now makes sure not to duplicate any terms. The estimated regression coefficients can be referred to as _b[1.male], _b[1.male#c.yearsdg], _b[c.marketc#c.yearsdg], _b[c.yearsdg#c.yearsdg], _b[2.rank], and _b[3.rank].

A more flexible curve can be produced by using higher-order polynomials, also including yearsdg cubed, which can be expressed as c.yearsdg#c.yearsdg#c.yearsdg, and possibly higher powers. Unless there are specific hypotheses about the shape of the curve, the coefficients of lower powers should be kept in the model. See section 7.3 for a further discussion of polynomials and for an alternative approach to modeling nonlinearity based on linear splines.

## 12 Residual diagnostics

Predicted residuals are defined as the differences between the observed responses $y_i$ and the predicted responses $\widehat \y_i$:

$$\widehat{\epsilon_i}=y_i-\underbrace{(\widehat{\beta}_1+\widehat{\beta}_2x_{2i}+\cdots+\widehat{\beta}_px_{pi})}_{\widehat{y}_i}$$

Predicted standardized residuals are obtained as

$$r_i=\frac{\widehat{\epsilon_i}}{s_{r_i}}$$

where $s_{r_i}$ is the estimated standard error of the residual.

Predicted residuals or standardized residuals can be used to investigate whether model assumptions, such as homoskedasticity and normally distributed errors, are violated. **Predicted standardized residuals have the advantage that they have an approximate standard normal distribution if the model assumptions are true.** For instance, a value greater than 3 should occur only about 0.1% of the time and may therefore be an outlier.

The postestimation command **`predict`** with the **`residuals`** option provides predicted residuals for the last regression model that was fit.

```
predict res, residuals
```

Standardized residuals can be obtained by using the **`rstandard`** option of **`predict`**.

A histogram of the predicted residuals with an overlaid normal distribution is produced by the **`histogram`** command with the **`normal`** option.

```
histogram res, normal
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.5.1.png" style="width:600px;height:410px;" alt="图片描述">
  <figcaption><strong>Figure 1.17: Predicted residuals with overlaid normal distribution</strong></figcaption>
</figure>

The distribution is somewhat skewed, suggesting that salary should perhaps be log-transformed as discussed in section 1.3. Again, it may be advisable to use the **`vce(robust)`** option, which provides estimated standard errors for regression coefficients that are not just robust to heteroskedasticity but also to other violations of the distributional assumptions.


