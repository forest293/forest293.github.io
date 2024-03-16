# [Categorical Dependent Var] Chapter_7 ：Models for ordinal outcomes


**Ordinal variables are often coded as consecutive integers from 1 to the number of categories. Perhaps because of this coding, it is tempting to analyze ordinal outcomes with the linear regression model (LRM). However, an ordinal dependent variable violates the assumptions of the LRM, which can lead to incorrect conclusions**, as demonstrated strikingly by McKelvey and Zavoina (1975, 117) and Winship and Mare (1984, 521-523). With ordinal outcomes, it is much better to use models that avoid the assumption that the distances between categories are equal. Although many models have been designed for ordinal outcomes, **in this chapter we focus on the logit and probit versions of the ordinal regression model (ORM).** The model was introduced by McKelvey and Zavoina (1975) in terms of an underlying latent variable, and in biostatistics by McCullagh (1980), who referred to the logit version as the proportional-odds model. In section 7.16, we review several less commonly used models for ordinal outcomes.

As with the binary regression model (BRM), the ORM is nonlinear, and the magnitude of the change in the outcome probability for a given change in one of the independent variables depends on the levels of all the independent variables. As with the BRM, the challenge is to summarize the effects of the independent variables in a way that fully reflects key substantive processes without overwhelming and distracting detail. For ordinal outcomes, as well as for the models for nominal outcomes in chapter 8, the difficulty of this task is increased by having more than two outcomes to explain.

Before proceeding, we caution that researchers should think carefully before concluding that their outcome is indeed ordinal. Do not assume that a variable should be analyzed as ordinal simply because the values of the variable can be ordered. A variable that can be ordered when considered for one purpose could be unordered or ordered differently when used for another purpose. Miller and Volker (1985) show how different assumptions about the ordering of occupations result in different conclusions.

A variable might also reflect ordering on more than one dimension, such as attitude scales that reflect both the intensity and the direction of opinion. Moreover, surveys commonly include the category “don’t know”, which probably does not correspond to the middle category in a scale, even though analysts might be tempted to treat it this way. In general, ORMs restrict the nature of the relationship between the independent variables and the probabilities of outcome categories, as discussed in section 7.15. Even when an outcome seems clearly to be ordinal, such restrictions can be unrealistic, as illustrated in chapter 8. Indeed, we suggest that you always compare the results from ordinal models with those from a model that does not assume ordinality.

We begin by reviewing the statistical model, followed by an examination of testing, fit, and methods of interpretation. These discussions are intended as a review for those who are familiar with the models. For a complete discussion, see Agresti (2010), Long (1997), or Hosmer, Lemeshow, and Sturdivant (2013). As explained in chapter 1, you can obtain sample do-files and data files by installing the spostl3_do package.

## 1.1 The statistical model

The ORM can be developed in different ways, each of which leads to the same form of the model. These approaches to the model parallel those for the BRM. **Indeed, the BRM can be viewed as a special case of the ordinal model in which the ordinal outcome has only two categories.**

### 1.1.1 A latent-variable model

The ORM is commonly presented as a latent-variable model. Defining y* as a latent variable ranging from −∞ to ∞, the structural model is

$$y_{i}^{*}=\mathbf{x_i}\beta+\varepsilon_{i}$$

where $i$ is the observation and $\varepsilon$ is a random error, as discussed further below. For the case of one independent variable,

The measurement model for binary outcomes from chapter 5 is expanded to divide $y^*$ into $J$ ordinal categories,

$$y_i=m\quad\text{if }\tau_{m-1}\leq y_i^*<\tau_m\quad\text{for }m=1\text{to }J$$

where the cutpoints $\tau_1$ through $\tau_j-1$ are estimated.(Some authors refer to these as thresholds.)
We assume $t_0 = -\infty$ and $t_j = \infty$ for reasons that will be clear shortly.

To illustrate the measurement model, consider the example used in this chapter. People are asked to respond to the following statement:

>If you were asked to use one of four names for your social class, which would you say you belong in: the lower class, the working class, the middle class, or the upper class?

The underlying, continuous latent variable can be thought of as the propensity to identify oneself as having higher socioeconomic standing. The observed response categories are tied to the latent variable by the measurement model.

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.1.png" style="width:350px;height:110px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Thus when the latent $y^*$ crosses a cutpoint, the observed category changes. Anderson (1984) referred to ordinal variables created in this fashion as grouped continuous variables and referred to the ORM as the grouped continuous model.

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.2.png" style="width:650px;height:450px;" alt="图片描述">
  <figcaption><strong>7.1 Relationship between observed y and latent y* in ORM with one independent variable</strong></figcaption>
</figure>

For a single independent variable, the structural model is $y^* = \alpha + \beta x + \epsilon$, which is plotted in figure 7.1 along with the cutpoints for the measurement model. This figure is similar to that for the BRM, except that there are now three horizontal lines representing the cutpoints $\tau_1$, $\tau_2$, and $\tau_3$. The three cutpoints lead to four levels of $y$ that are labeled on the right-hand side of the graph.

The probability of an observed outcome $y$ for a given value of $x$, represented by the three vertical lines in the figure, is the area under the curve between a pair of cutpoints.

For example, the probability of observing $y = m$ for given values of the $x$'s corresponds to the region of the distribution where $y^*$ falls between $\tau_{m-1}$ and $\tau_m$:

$$\Pr(y=m\mid\mathbf{x})=\Pr\left(\tau_{m-1}\leq y^*<\tau_m\mid\mathbf{x}\right)$$

Substituting $x\beta + \epsilon$ for $y^*$ and using some algebra leads to the standard formula for the predicted probability in the ORM.

$$\mathrm{Pr}\left(y=m\mid\mathbf{x}\right)=F\left(\tau_{m}-\mathbf{x}\beta\right)-F\left(\tau_{m-1}-\mathbf{x}\beta\right)$$

Where $F$ is the cumulative distribution function for $\epsilon$. In the ordinal probit model, $F$ is normal with $Var(e) = 1$; in the ordinal logit model, $F$ is logistic with $Var(e) = \frac{\pi^2}{3}$. For $y = 1$, the second term on the right drops out because $F(-\infty - x\beta) = 0$, and for $y = J$, the first term equals $F(\infty - x\beta) = 1$.

假设我们想了解学生的考试成绩 $ y $ 如何受到学习时间 $ x $ 的影响。我们可以使用一个称为“有序回归模型（ORM）”的统计工具来分析这个关系。在这个模型中，我们假设学习时间 $ x $ 对考试成绩 $ y $ 有影响，但不是直接的影响，而是通过一个隐变量 $ y^* $ 来间接影响。这个隐变量 $ y^* $ 可以被理解为一个学生的潜在考试能力，即在没有任何其他影响因素的情况下，学生能够取得的最佳成绩。

1. **结构模型**：结构模型描述了潜在变量 $y^※$ 和自变量 $x$ 之间的关系。在这个例子中，我们假设学生的考试成绩 $y^※$ 受到学习时间 $x$ 的影响。这种影响被表示为一个线性关系：$y^※= \alpha + \beta x + \epsilon$。其中，$\alpha$和 $\beta$ 是模型的参数，表示了$x$对$y$的影响程度，$\epsilon$ 是一个随机误差项，代表了其他未考虑到的因素对 $y^*$ 的影响。让我们通过一个例子来解释这个结构模型：

   假设我们有一组学生，他们的学习时间 $x$ 分别为10小时、20小时和30小时。我们想知道他们的潜在考试能力 $y^*$ 是多少。根据结构模型，我们可以用下面的公式来计算：

   $$
   \begin{align*}
   \text{学生1的 } y^* &= \alpha + \beta \times 10 + \epsilon_1 
   \end{align*}
   $$
   $$
   \begin{align*}
   \text{学生2的 } y^* &= \alpha + \beta \times 20 + \epsilon_2
   \end{align*}
   $$
   $$
   \begin{align*}
   \text{学生3的 } y^* &= \alpha + \beta \times 30 + \epsilon_3
   \end{align*}
   $$



   其中，$\epsilon_1, \epsilon_2, \epsilon_3$ 分别代表了每个学生的随机误差。

2. **测量模型**：测量模型描述了潜在变量 $y^※$ 和观测到的变量 $y$ 之间的关系。在这个例子中，我们将 $y^※$ 划分为几个等级，每个等级对应一个观测到的成绩水平 $y$。这些等级由一些切分点 $\tau_1, \tau_2, \tau_3$ 确定。让我们通过一个例子来解释测量模型：

   假设 $y^※$ 表示学生的潜在考试能力，我们将它划分为四个等级：低、中低、中高和高。这些等级由以下切分点确定：$\tau_1 = 60, \tau_2 = 70, \tau_3 = 80$。这意味着潜在考试能力 $y^※$ 在60到70之间的学生被归类为低水平，70到80之间的学生被归类为中低水平，以此类推。

3. **预测概率**：我们想知道给定学习时间 $x$ 的情况下，学生考试成绩 $y$ 是多少的概率。我们可以通过计算 $y^*$ 落在某个区间内的概率来得到这个答案。具体来说，对于给定的 $x$ 值，$y$ 等于某个特定分数 $m$ 的概率可以表示为：

   $$ \Pr(y=m|\mathbf{x}) = \Pr(\tau_{m-1} \leq y^* < \tau_m|\mathbf{x}) $$

   这意味着我们想知道 $y^*$ 落在 $\tau_{m-1}$ 和 $\tau_m$ 之间的概率。通过一些数学运算，我们可以得到预测概率的标准公式：

   $$ \mathrm{Pr}(y=m|\mathbf{x}) = F(\tau_m - x\beta) - F(\tau_{m-1} - x\beta) $$

   其中 $F$ 是误差项 $\epsilon$ 的累积分布函数。根据模型的不同，$F$ 可以是正态分布函数或 logistic 分布函数。


Comparing these equations with those for the BRM shows that the ORM is identical to the BRM with one exception. To demonstrate this, we fit chapter 5’s binary model for labor force participation with both logit and ologit (the command for ordinal logit):

```
use binlfp4, clear
logit lfp c.k5 c.k618 i.agecat i.wc i.hc c.lwg c.inc, nolog
estimates store logit
ologit lfp c.k5 c.k618 i.agecat i.wc i.hc c.lwg c.inc, nolog
estimates store ologit
```

To compare the coefficients, we use **estimates table**:1

```
estimates table logit ologit, b(%9.3f) t varlabel varwidth(30) equations(1:1)
```

        --------------------------------------------------------
                              Variable |   logit      ologit    
        -------------------------------+------------------------
        #1                             |
                            # kids < 6 |    -1.392      -1.392  
                                       |     -7.25       -7.25  
                           # kids 6-18 |    -0.066      -0.066  
                                       |     -0.96       -0.96  
                                       |                        
                      Wife's age group |                        
                                40-49  |    -0.627      -0.627  
                                       |     -3.00       -3.00  
                                  50+  |    -1.279      -1.279  
                                       |     -4.92       -4.92  
                                       |                        
                Wife attended college? |                        
                              college  |     0.798       0.798  
                                       |      3.48        3.48  
                                       |                        
             Husband attended college? |                        
                              college  |     0.136       0.136  
                                       |      0.66        0.66  
         Log of wife's estimated wages |     0.610       0.610  
                                       |      4.04        4.04  
        Family income excluding wife's |    -0.035      -0.035  
                                       |     -4.24       -4.24  
                              Constant |     1.014              
                                       |      3.54              
        -------------------------------+------------------------
                                 /cut1 |                -1.014  
                                       |                 -3.54  
        --------------------------------------------------------
                                                     Legend: b/t

以下是修改后的原文，命令部分已用**``**包裹：

The slope coefficients and their $z$-values are identical. For **``logit``**, though, an intercept or constant is reported, whereas for **``ologit``**, the intercept is replaced by the cut point labeled **``cutl``**. The cut point has the same magnitude but opposite sign as the intercept from **``logit``**. This difference is due to how the two models are identified. As the ORM has been presented, there are “too many” free parameters; that is, you cannot estimate J — 1 thresholds and the constant too. For a unique set of maximum likelihood estimates to exist, an identifying assumption needs to be made about either the intercept or one of the cutpoints. Stata’s **``ologit``** and **``oprobit``** commands identify the ORM by assuming that the intercept is 0 and then estimating all cutpoints.

简单来说，就是在处理一些统计模型时，为了使得模型参数的估计结果是唯一的，我们需要对模型做一些假设。在这个例子中，**logit**模型和**ologit**模型虽然在大部分方面都是一样的，但在处理截距（模型中的一种参数）时存在差异，因为它们识别模型的方式不同。

Some other software packages that fit the ORM instead fix one of the cutpoints to 0 and estimate the intercept. **Although the different parameterizations can be confusing, keep in mind that the slope coefficients and predicted probabilities are the same under either parameterization** (see Long [1997, 122-123] for details). Section 7.5 shows how to fit the ORM using alternative parameterizations.

>Because **logit** has a constant and **ologit** has a cutpoint, by default **estimates table** will not line up the coefficients from the two models. Rather, each of the independent variables will be listed twice, **equations(1:1)** tells **estimates table** to line up the coefficients. This is easiest to understand if you try our command without the **equations(1:1)** option.

### 1.1.2 A nonlinear probability model
The ORM can also be developed as a nonlinear probability model without appealing to an underlying latent continuous variable. Here we show how this is done for the ordinal logit model. First, we define the odds that an outcome is less than or equal to $m$ versus greater than $m$ given $x$:

$$\Omega_{\leq m|>m}\left(\mathbf{x}\right)\equiv\frac{\mathrm{Pr}\left(y\leq m\mid\mathbf{x}\right)}{\mathrm{Pr}\left(y>m\mid\mathbf{x}\right)}\quad\mathrm{for}m=1,J-1$$

For example, we could compute the odds of lower- or working-class identification (that is, $rn < 2$) versus middle- or upper-class identification ($rn > 2$). The log of the odds is assumed to equal

$$\ln\Omega_{\leq m|>m}\left(\mathbf{x}\right)=\tau_m-\mathbf{x}\boldsymbol{\beta}$$

Critically, the $\beta$'s are the same for all values of $m$.

For a single independent variable and three categories, where we are fixing the intercept to equal 0 and estimating the $r$'s, the model is:

$$\ln\frac{\Pr\left(y\leq1\mid\mathbf{x}\right)}{\Pr\left(y>1\mid\mathbf{x}\right)}=\tau_{1}-\beta x$$
$$\ln\frac{\operatorname*{Pr}\left(y\leq2\mid\mathbf{x}\right)}{\operatorname*{Pr}\left(y>2\mid\mathbf{x}\right)}=\tau_{2}-\beta x$$

Although it may seem confusing that we subtract $r \cdot x$ rather than adding it, this is a consequence of computing the logit of $y < r_m$ versus $y > r_m$. We agree that it would be simpler to stick with $r_m + fix$, but this is not the way the model is normally presented.



在有序Logit模型中，我们想要研究一个有序分类变量 $y$，比如社会阶层。这个模型是如何描述和预测这个变量的呢？让我们一步步来看。

首先，我们有一个称为“几率比”的概念，它描述了一个结果小于或等于某个值 $m$ 的概率与大于 $m$ 的概率之间的关系。这个比率表示为 $\Omega_{\leq m|>m}(\mathbf{x})$，其中 $\mathbf{x}$ 是自变量的值。这个比率可以用以下公式表示：

$$
\Omega_{\leq m|>m}(\mathbf{x}) = \frac{\mathrm{Pr}(y \leq m \mid \mathbf{x})}{\mathrm{Pr}(y > m \mid \mathbf{x})} \quad \text{for } m=1,J-1
$$

这个比率告诉我们在给定自变量 $\mathbf{x}$ 的情况下，结果 $y$ 小于或等于 $m$ 的概率与大于 $m$ 的概率之间的关系。这对于理解结果的相对概率非常有帮助。

接下来，我们使用对数几率来描述这个比率。对数几率是对上面比率的对数，可以用以下公式表示：

$$
\ln\Omega_{\leq m|>m}(\mathbf{x}) = \tau_m - \mathbf{x}\boldsymbol{\beta}
$$

在这里，$\tau_m$ 是与结果 $m$ 相关的常数参数，$\boldsymbol{\beta}$ 是自变量 $\mathbf{x}$ 的参数。这个公式告诉我们，对数几率由两部分组成：一个与结果 $m$ 相关的常数部分 $\tau_m$，以及一个与自变量 $\mathbf{x}$ 相关的部分，通过参数 $\boldsymbol{\beta}$ 来表示。

最后，当我们只有一个自变量和三个结果时，模型可以简化为两个方程，它们描述了不同结果之间的对数几率。这些方程是：

$$
\ln\frac{\Pr(y \leq 1 \mid \mathbf{x})}{\Pr(y > 1 \mid \mathbf{x})} = \tau_{1} - \beta x
$$
$$
\ln\frac{\Pr(y \leq 2 \mid \mathbf{x})}{\Pr(y > 2 \mid \mathbf{x})} = \tau_{2} - \beta x
$$

在这里，参数 $r$ 是一个特殊的参数，表示不同结果之间的分界点。

困惑的一点是为什么在计算对数几率时要减去 $\beta x$ 而不是加上它。这其实是因为我们在计算 $y < r_m$ 与 $y > r_m$ 的Logit几率时所采取的方法不同。在Logit模型中，我们通常对一个事件发生的概率取对数几率，以便于建模和分析。在这种情况下，对于有序Logit模型，我们计算的是结果小于某个分界点 $r_m$ 的对数几率和结果大于 $r_m$ 的对数几率。因此，为了计算这两个对数几率，我们需要考虑 $\beta x$ 的影响。

作者提到了一种更简单的处理方法，即使用 $r_m$ + $\betax$，但这不是该模型通常被呈现的方式。这可能是因为 $\beta x$ 的形式更加直观，能够更清晰地表达出结果小于或大于分界点 $r_m$ 的对数几率之间的关系。尽管这种处理方法可能会导致一些困惑，但它在实践中是有效的，并且能够更好地解释模型的原理。

## 1.2 Estimation using ologit and oprobit

The ordered logit and probit models can be fit with the following commands and their basic options:

>ologit depvar [indepvars] [if] [m] [weight] [, vce(vcetype) or]
>oprobit depvar [indepvars] [if] [in] [weight] [, vce(vcetype)]

In our experience, these models take more steps to converge than either models for binary outcomes fit using **`logit`** or **`probit`** or models for nominal outcomes fit using **`mlogit`**.

>Variable lists
*depvar* is the dependent variable. The values assigned to the outcome categories are irrelevant, except that larger values are assumed to correspond to “higher” outcomes. For example, if you had three outcomes, you could use the values 1, 2, and 3, or —1.23, 2.3, and 999. **To avoid confusion, however, we recommend coding your dependent variable as consecutive integers beginning with 1.**

>*indepvars* is a list of independent variables. **If indepvars is not included, Stata fits a model with only cutpoints.**

>Specifying the estimation sample
**`if` and `in` qualifiers.** These can be used to restrict the estimation sample. For example, if you want to fit an ordered logit model for only those surveyed in 1980 (year = 1), you could specify **`ologit`** `class i.female i.white i.educ age inc` **`if year == 1`**.

**Listwise deletion.** Stata excludes cases in which there are missing values for any of the variables in the model. **Accordingly, if two models are fit using the same dataset but have different sets of independent variables, it is possible to have different samples. We recommend that you use `mark` and `markout` (discussed in section 3.1.G) to explicitly remove cases with missing data.**

>Weights and complex samples
Both **`ologit`** and **`oprobit`** can be used with **`fweights`**, **`pweights`**, and **`iweights`**. Survey estimation can be done using the **`svy`** prefix. See section 3.1.7 for details.

>Options
**`vce(vcetype)`** specifies the type of standard errors to be computed. See section 3.1.9 for details.

**`or`** reports odds ratios for the ordered logit model.

Additional options and information can be found in the Stata manual entries [R] ologit and [r] oprobit.

### 1.2.1 Example of ordinal logit model

Our example is based on a question asked in the 1980, 1996, and 2012 General Social Surveys. These are repeated cross-sectional data, not panel data. That is, in each wave the survey was administered to new respondents from a new nationally representative sample. The following variables are in the model:

```
use gssclass4, clear
codebook class female white year ed age income, compact
```

    Variable    Obs Unique      Mean     Min       Max  Label
    ------------------------------------------------------------------------------
    class      5620      4  2.437544       1         4  subjective class id
    female     5620      2  .5491103       0         1  respondent is female
    white      5620      2  .8140569       0         1  resondent is whte
    year       5620      3  2.070996       1         3  year of GSS survey
    educ       5620      3  2.064769       1         3  educational attainment
    age        5620     72  45.15712      18        89  age of respondent
    income     5620     62  68.07737  .51205  324.2425  household income
    ------------------------------------------------------------------------------

Respondents were asked to indicate the social class to which they think they most belong, using categories coded 1 = lower, 2 = working, 3 = middle, and 4 = upper. The resulting variable **`class`** has the distribution:

```
tab class
```

     subjective |
       class id |      Freq.     Percent        Cum.
    ------------+-----------------------------------
          lower |        394        7.01        7.01
        working |      2,567       45.68       52.69
         middle |      2,465       43.86       96.55
          upper |        194        3.45      100.00
    ------------+-----------------------------------
          Total |      5,620      100.00

The variable **`educ`** is a categorical variable in which the categories are less than a high school diploma, high school diploma, and college diploma. The variable **`income`** is measured in 2012 dollars for all years of the survey.

Using these data, we use **`ologit`** to fit the model

$$\mathrm{Pr}(\mathbf{c}1\mathbf{ass}=m\mid\mathbf{x_i})=F(\tau_{m}-\mathbf{x}\beta)-F(\tau_{m-1}-\mathbf{x}\beta)$$

where

$$\mathbf{x\beta}=\beta_\text{female}{\text{female}+\beta_\text{white}{ \mathrm{white}}}$$
$$\qquad \qquad \qquad \qquad \qquad \qquad+\beta_{\mathrm{year}\left[1996\right]}\text{(year==1996)}+\beta_{\mathrm{year}\left[2012\right]}\text{(year==2012)}$$
$$\qquad \qquad \qquad \qquad \qquad \quad +\beta_{\text{educ [ha only]}}\text{(educ==2)}+\beta_{\text{educ}[\text{college}]}\text{(educ==3)}$$
<math xmlns="http://www.w3.org/1998/Math/MathML">
  <mo>&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;+</mo>
  <msub>
    <mi>β</mi>
    <mrow>
      <mrow>
        <mi mathvariant="normal">c</mi>
        <mo>.</mo>
        <mi mathvariant="normal">a</mi>
        <mi mathvariant="normal">g</mi>
        <mi mathvariant="normal">e</mi>
      </mrow>
    </mrow>
  </msub>
  <mtext>c.age</mtext>
  <mo>+</mo>
  <msub>
    <mi>β</mi>
    <mrow>
      <mrow>
        <mi mathvariant="normal">c</mi>
        <mo>.</mo>
        <mi mathvariant="normal">a</mi>
        <mi mathvariant="normal">g</mi>
        <mi mathvariant="normal">e</mi>
        <mi mathvariant="normal">#</mi>
        <mi mathvariant="normal">c</mi>
        <mo>.</mo>
        <mi mathvariant="normal">a</mi>
        <mi mathvariant="normal">g</mi>
        <mi mathvariant="normal">e</mi>
      </mrow>
    </mrow>
  </msub>
  <mtext>c.age\#c.age</mtext>
  <mo>+</mo>
  <msub>
    <mi>β</mi>
    <mtext>income</mtext>
  </msub>
  <mrow>
    <mrow>
      <mi mathvariant="normal">i</mi>
      <mi mathvariant="normal">n</mi>
      <mi mathvariant="normal">c</mi>
      <mi mathvariant="normal">o</mi>
      <mi mathvariant="normal">m</mi>
      <mi mathvariant="normal">e</mi>
    </mrow>
  </mrow>
</math>

To specify the model, we use the factor-variable notation i.year to create indicators for the year of the survey, i.educ for education, and c.age##c.age to include age and age-squared, estimates store is used so that we can later make a table containing these results.

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
estimates store ologit
```
    Ordered logistic regression                            Number of obs =   5,620
                                                           LR chi2(9)    = 1453.95
                                                           Prob > chi2   =  0.0000
    Log likelihood = -5016.2107                            Pseudo R2     =  0.1266
    
    ------------------------------------------------------------------------------
           class | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
          female |
         female  |       0.02       0.05     0.30   0.765        -0.09        0.12
                 |
           white |
          white  |       0.24       0.07     3.28   0.001         0.09        0.38
                 |
            year |
           1996  |      -0.08       0.07    -1.16   0.247        -0.22        0.06
           2012  |      -0.50       0.08    -6.59   0.000        -0.65       -0.35
                 |
            educ |
        hs only  |       0.37       0.08     4.73   0.000         0.22        0.52
        college  |       1.57       0.10    15.99   0.000         1.37        1.76
                 |
             age |      -0.05       0.01    -5.31   0.000        -0.07       -0.03
                 |
     c.age#c.age |       0.00       0.00     7.65   0.000         0.00        0.00
                 |
          income |       0.01       0.00    22.20   0.000         0.01        0.01
    -------------+----------------------------------------------------------------
           /cut1 |      -2.14       0.23                         -2.59       -1.69
           /cut2 |       0.92       0.23                          0.47        1.36
           /cut3 |       4.93       0.24                          4.46        5.41
    ------------------------------------------------------------------------------



The information in the header and the table of coefficients is in the same form as discussed in chapters 3 and 5, with the addition of estimates for the cutpoints at the end.

Next, we fit the ordered probit model:

```
oprobit class i.female i.white i.year i.educ c.age##c.age income, nolog
estimates store oprobit
```
    Ordered probit regression                              Number of obs =   5,620
                                                           LR chi2(9)    = 1402.15
                                                           Prob > chi2   =  0.0000
    Log likelihood = -5042.1128                            Pseudo R2     =  0.1221
    
    ------------------------------------------------------------------------------
           class | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
          female |
         female  |       0.00       0.03     0.05   0.957        -0.06        0.06
                 |
           white |
          white  |       0.11       0.04     2.65   0.008         0.03        0.19
                 |
            year |
           1996  |      -0.06       0.04    -1.57   0.115        -0.14        0.02
           2012  |      -0.30       0.04    -6.98   0.000        -0.39       -0.22
                 |
            educ |
        hs only  |       0.20       0.04     4.49   0.000         0.11        0.28
        college  |       0.85       0.05    15.74   0.000         0.75        0.96
                 |
             age |      -0.03       0.01    -4.91   0.000        -0.04       -0.02
                 |
     c.age#c.age |       0.00       0.00     7.03   0.000         0.00        0.00
                 |
          income |       0.01       0.00    22.90   0.000         0.01        0.01
    -------------+----------------------------------------------------------------
           /cut1 |      -1.29       0.13                         -1.54       -1.03
           /cut2 |       0.47       0.13                          0.22        0.72
           /cut3 |       2.60       0.13                          2.34        2.86
    ------------------------------------------------------------------------------

Because we stored the results for both models, we can compare the results with the command estimates table:

```
estimates table ologit oprobit, b(%9.3f) t varlabel varwidth(30)
```

    --------------------------------------------------------
                          Variable |  ologit      oprobit   
    -------------------------------+------------------------
    class                          |
              respondent is female |                        
                           female  |     0.016       0.002  
                                   |      0.30        0.05  
                                   |                        
                 resondent is whte |                        
                            white  |     0.236       0.106  
                                   |      3.28        2.65  
                                   |                        
                year of GSS survey |                        
                             1996  |    -0.080      -0.062  
                                   |     -1.16       -1.57  
                             2012  |    -0.504      -0.302  
                                   |     -6.59       -6.98  
                                   |                        
            educational attainment |                        
                          hs only  |     0.370       0.195  
                                   |      4.73        4.49  
                          college  |     1.566       0.852  
                                   |     15.99       15.74  
                 age of respondent |    -0.049      -0.025  
                                   |     -5.31       -4.91  
                                   |
                       c.age#c.age |     0.001       0.000  
                                   |      7.65        7.03  
                  household income |     0.012       0.006  
                                   |     22.20       22.90  
    -------------------------------+------------------------
                             /cut1 |    -2.140      -1.285  
                                   |     -9.39      -10.05  
                             /cut2 |     0.915       0.471  
                                   |      4.07        3.72  
                             /cut3 |     4.935       2.599  
                                   |     20.26       19.58  
    --------------------------------------------------------
                                                 Legend: b/t

As with the BRM, the estimated coefficients differ from logit to probit by a factor of about 1.7, reflecting the different scaling of the ordered logit and ordered probit models that results from different assumptions about the variance of the errors. We also see scaling differences in the cutpoints, which are also larger in the ordered logit model. **Values of the $z$ tests are similar because they are not affected by the scaling, but they are not identical because of slight differences in the shape of the assumed distribution of the errors.**

### 1.2.2 Predicting perfectly
If either the highest or the lowest category of the dependent variable does not vary within one of the categories of an independent variable, there will be a problem with estimation. To see what happens, we created an artificial example with a dummy variable for whether respondents have a college degree. Tabulating **college** against **class** shows that in all cases where **college** is 1, respondents have values of **class** equal to 4, indicating upper-class identification.

```
capture drop college
gen college = (educ == 3 & class == 4)
label var college "Has college degree?"
label def college 0 no 1 yes
label val college college
tab class college
```

    subjective |  Has college degree?
      class id |        no        yes |     Total
    -----------+----------------------+----------
         lower |       394          0 |       394 
       working |     2,567          0 |     2,567 
        middle |     2,465          0 |     2,465 
         upper |        81        113 |       194 
    -----------+----------------------+----------
         Total |     5,507        113 |     5,620 

Accordingly, if you know **college** is 1, you can predict perfectly that **class** is 4. Although we purposely constructed **college** so this would happen, perfect prediction occurs in real data, especially when samples are small or one of the outcome values is infrequent.

When we fit the ordered logit model with **college** as a regressor, the perfectly predicted observations are retained in the estimation sample with a warning message appearing below the table of estimates.

```
ologit class i.female i.white i.year i.college c.age##c.age income, nolog
```

The note reflects that the standard error for **college** is enormous, indicating the problem that occurs when trying to estimate a coefficient that is effectively infinite. Another way of thinking about the large standard error is that the lack of variation in the outcome when **college** equals 1 means we do not have any information that would permit us to estimate the coefficient with precision. When this happens, our next step is to drop the 113 cases for which **college** equals 1 (you could use the command `drop if college==1` to do this) and refit the model without **college**. This is done automatically for binary models fit by `logit` and `probit` (see section 5.2.3).

There is no problem if an independent variable perfectly predicts one of the middle categories. For example, if all observations for which **college** is 1 reported being middle class, this would not cause problems for estimation.

## 1.3 Hypothesis testing

Hypothesis tests of regression coefficients can be evaluated with the $z$ statistics in the estimation output, with **`test`** and **`testparm`** for Wald tests of simple and complex hypotheses, and with **`lrttest`** for likelihood-ratio tests. We briefly review each. See section 3.2 for additional information on these commands.

### 1.3.1 Testing individual coefficients

If the assumptions of the model hold, the maximum likelihood estimators from **`ologit`** and **`oprobit`** are distributed asymptotically normally. The hypothesis $H_{0}:\beta_{k}=\beta^{*}$ can be tested with <math xmlns="http://www.w3.org/1998/Math/MathML">
  <mi>z</mi>
  <mo>=</mo>
  <mo stretchy="false">(</mo>
  <msub>
    <mrow>
      <mover accent="true" accentunder="false">
        <mrow>
          <mi>β</mi>
        </mrow>
        <mo>^</mo>
      </mover>
    </mrow>
    <mrow>
      <mi>k</mi>
    </mrow>
  </msub>
  <mo>−</mo>
  <msup>
    <mi>β</mi>
    <mrow>
      <mo>∗</mo>
    </mrow>
  </msup>
  <mo stretchy="false">)</mo>
  <mrow>
    <mo>/</mo>
  </mrow>
  <msub>
    <mrow>
      <mover accent="true" accentunder="false">
        <mrow>
          <mi>σ</mi>
        </mrow>
        <mo>^</mo>
      </mover>
    </mrow>
    <mrow>
      <msub>
        <mrow>
          <mover accent="true" accentunder="false">
            <mrow>
              <mi>β</mi>
            </mrow>
            <mo>^</mo>
          </mover>
        </mrow>
        <mrow>
          <mi>k</mi>
        </mrow>
      </msub>
    </mrow>
  </msub>
</math>Under the assumptions justifying maximum likelihood, if $H_0$ is true, then $z$ is distributed approximately normally with a mean of 0 and a variance of 1 for large samples. For example, consider the results for the variable **white** from the **`ologit`** output above. We are using the **`sformat`** option to show more decimal places for the $z$ statistic 2: 

当我们使用**`ologit`**和**`oprobit`**模型时，我们试图找到一组参数，这些参数可以最好地描述我们观察到的数据。其中一些参数代表我们感兴趣的变量对结果的影响。

假设我们对其中一个参数感兴趣，我们想知道这个参数的值是否等于某个特定值，比如说 $\beta^*$。我们可以进行一种假设检验来验证这个问题。这种检验告诉我们，如果我们的假设是正确的，那么我们从数据中计算的统计量（称为 $z$ 统计量）应该会遵循一个特定的分布，即正态分布。

这个 $z$ 统计量实际上是一个数字，它告诉我们观察到的参数值与我们假设的值之间有多大的差异，以及这个差异有多大可能是由随机因素引起的。如果这个差异很大，而且很少可能是由随机因素引起的，那么我们就会得到一个很大的 $z$ 值。

当我们对很多样本进行分析时，我们可以看到这个 $z$ 值的分布。如果它们聚集在某个特定的范围内，而不是分散在整个范围内，那么我们就可以相信我们的假设是正确的。

在这种情况下，如果我们的假设是正确的，那么这个 $z$ 统计量的平均值应该接近于零，而且不同样本的 $z$ 值的变化应该是一样的。这就是所谓的“均值为0，方差为1”的正态分布。

>We are displaying more decimal places to later demonstrate the equivalence of the **z test** and the corresponding **chi-squared test**. With any estimation command, the option **`format(fmt)`**(fmt) can be used to format the display of coefficients and standard errors. Likewise, option **`pformat`** formats the display of p-values and option **`sformat(fmt)`**  formats the display of the test. Alternatively, the **`set`** command can also be used to change these formats either permanently for the rest of the current Stata session. See [R] **`set format`** in the Stata manual for  details

```
ologit class i.female i.white i.year i.educ c.age##c.age income, ///
    nolog sformat(%8.3f)
```

    Ordered logistic regression                            Number of obs =   5,620
                                                           LR chi2(9)    = 1453.95
                                                           Prob > chi2   =  0.0000
    Log likelihood = -5016.2107                            Pseudo R2     =  0.1266
    
    ------------------------------------------------------------------------------
           class | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
          female |
         female  |       0.02       0.05    0.298   0.765        -0.09        0.12
                 |
           white |
          white  |       0.24       0.07    3.277   0.001         0.09        0.38
                 |
            year |
           1996  |      -0.08       0.07   -1.158   0.247        -0.22        0.06
           2012  |      -0.50       0.08   -6.594   0.000        -0.65       -0.35
                 |
            educ |
        hs only  |       0.37       0.08    4.730   0.000         0.22        0.52
        college  |       1.57       0.10   15.994   0.000         1.37        1.76
                 |
             age |      -0.05       0.01   -5.308   0.000        -0.07       -0.03
                 |
     c.age#c.age |       0.00       0.00    7.646   0.000         0.00        0.00
                 |
          income |       0.01       0.00   22.203   0.000         0.01        0.01
    -------------+----------------------------------------------------------------
           /cut1 |      -2.14       0.23                         -2.59       -1.69
           /cut2 |       0.92       0.23                          0.47        1.36
           /cut3 |       4.93       0.24                          4.46        5.41
    ------------------------------------------------------------------------------

We conclude the following:

>Whites and nonwhites significantly differ in their subjective social class identification (z = 3.28, p < 0.01, two-tailed)

Either a one-tailed or a two-tailed test can be used, as discussed in chapter 5

The z test in the output of estimation commands is a Wald test, which can also be computed using **`test`**. For example, to test $H_0{:}\beta_{\mathrm{white}}=0$,type

```
test 1.white
```

     ( 1)  [class]1.white = 0
    
               chi2(  1) =   10.74
             Prob > chi2 =    0.0011

We conclude the following:

>Whites and nonwhites significantly differ in their class identification $(x^{2}=10.74,df=1,p<0.01).$

The value of a chi-squared test with 1 degree of freedom is identical to the square of the corresponding z test, which can be demonstrated with the **`display`** command:

```
display "z*z=" 3.277*3.277
```

z*z=10.738729

A likelihood-ratio LR test is computed by comparing the log likelihood from a full model with that from a restricted model. To test a single coefficient, we begin by fitting the full model and storing the estimates:

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
estimates store fullmodel
```

Then, we fit a model that excludes the variable white th at we want to test

```
ologit class i.female i.year i.educ c.age##c.age income, nolog
estimates store dropwhite
```

The **`lrtest`** command computes the test:
```
lrtest fullmodel dropwhite
```

    Likelihood-ratio test
    Assumption: dropwhite nested within fullmodel
    
     LR chi2(1) =  10.75
    Prob > chi2 = 0.0010

The resulting LR test can be interpreted as follows:

>The effect of being white on class identification is significant (**`LR χ² = 10.75`**, **`df = 1`**, **`p < 0.01`**).

### 1.3.2 Testing multiple coefficients

**We can also test complex hypotheses that involve more than one coefficient.** For example, our model has the demographic variables white, female, and age. To test that the effects of these variables are simultaneously equal to 0—that is, $H_0{:}\beta_{\mathrm{white}}=\beta_{\mathrm{female}}=\beta_{\mathrm{age}}=\beta_{\mathrm{age*age}}=0$ We can use either a Wald or an LR test. For the Wald test, we fit the full model and then use the **`test`** command:

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
test 1.white 1.female age age#age
```

     ( 1)  [class]1.white = 0
     ( 2)  [class]1.female = 0
     ( 3)  [class]age = 0
     ( 4)  [class]c.age#c.age = 0
    
               chi2(  4) =  226.75
             Prob > chi2 =    0.0000

Before we interpret the results of the test, we want to clarify how coefficients are specified in the **`test`** command when factor-variable notation is used. The specification i.white added the variable 1.white to the model as shown in the output to **`ologit`** above. Accordingly, we are testing the coefficient associated with the variable 1.white, not i.white or white. The same rule applies for female. Age was entered into the model as c.age##c.age, which was expanded to estimate coefficients for c.age and c.age#c.age. When entering these coefficients into **`test`**, we do not need to include the c. prefix (although we could do so). Regardless of how we specify the **`test`** command, we conclude the following:

>The hypothesis that the demographic effects of age, race, and sex are simultaneously equal to 0 can be rejected at the 0.01 level (χ² = 226.8, df = 4, p < 0.01).

To compute an LR test of multiple coefficients, we first fit the full model and store the results with **`estimates store`**. Suppose we are interested in **whether demographic characteristics matter at all for subjective class identification or whether identification is only a function of socioeconomic status and changes over time.** To test $H_0{:}\beta_{\mathrm{white}}=\beta_{\mathrm{female}}=\beta_{\mathrm{age}}=\beta_{\mathrm{age*age}}=0$, we fit the model that excludes these four coefficients and run **`lrtest`**:

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
estimates store fullmodel
ologit class i.year i.educ income, nolog
estimates store dropdemog
lrtest fullmodel dropdemog
```

    Likelihood-ratio test
    Assumption: dropdemog nested within fullmodel
    
     LR chi2(4) = 236.53
    Prob > chi2 = 0.0000

We conclude the following:

>The hypothesis that the demographic effects of age, race, and sex are simultaneously equal to 0 can be rejected at the 0.01 level (**LR χ² = 236.5**, **df = 4**, **p < 0.01**).

We find that the Wald and LR tests usually lead to the same decisions, and there is no reason why you would typically want to compute both tests. When there are differences, they generally occur when the tests are near the cutoff for statistical significance. **Because the LR test is invariant to reparameterization, we prefer the LR test when both are available. However, only the Wald test can be used if robust standard errors, probability weights, or survey estimation are used.**

When a factor variable has more than two categories, such as year and educ in our model, you can specify each of the coefficients with **`test`** (for example, **test 2.educ 3.educ**) or you can use **`testparm`**:

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
testparm i.educ
```

     ( 1)  [class]2.educ = 0
     ( 2)  [class]3.educ = 0
    
               chi2(  2) =  329.48
             Prob > chi2 =    0.0000

**`test`** can also be used to test the equality of coefficients, as shown in section 5.3.2.


## 1.4 Measures of fit using fitstat

As we discussed in greater detail in chapter 3, scalar measures of fit can be used when comparing competing models (also see Long [1997, 85-113]). Several measures can be computed after either **`ologit`** or **`oprobit`** by using the **SPost** command **`fits`**. In this example, we compare a model for class identification that includes age but not age-squared with the model we have been using that includes age-squared:

```
ologit class i.female i.white i.year i.educ age income, nolog
quietly fitstat, save
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
fitstat, diff
```


                             |     Current        Saved   Difference 
    -------------------------+--------------------------------------
    Log-likelihood           |                                      
                       Model |   -5016.211    -5045.903       29.692 
              Intercept-only |   -5743.186    -5743.186        0.000 
    -------------------------+--------------------------------------
    Chi-square               |                                      
          D(df=5608/5609/-1) |   10032.421    10091.806      -59.385 
                LR(df=9/8/1) |    1453.951     1394.566       59.385 
                     p-value |       0.000        0.000        0.000 
    -------------------------+--------------------------------------
    R2                       |                                      
                    McFadden |       0.127        0.121        0.005 
          McFadden(adjusted) |       0.124        0.119        0.005 
          McKelvey & Zavoina |       0.284        0.274        0.011 
                Cox-Snell/ML |       0.228        0.220        0.008 
      Cragg-Uhler/Nagelkerke |       0.262        0.252        0.009 
                       Count |       0.605        0.600        0.005 
             Count(adjusted) |       0.273        0.264        0.009 
    -------------------------+--------------------------------------
    IC                       |                                      
                         AIC |   10056.421    10113.806      -57.385 
            AIC divided by N |       1.789        1.800       -0.010 
             BIC(df=12/11/1) |   10136.030    10186.781      -50.751 
    -------------------------+--------------------------------------
    Variance of              |                                      
                           e |       3.290        3.290        0.000 
                      y-star |       4.596        4.528        0.067 
    
    Note: Likelihood-ratio test assumes saved model nested in current model.
    
    Difference of   50.751 in BIC provides very strong support for current model.

The Bayesian information criterion (BIC), Akaike’s information criterion (AIC), and the LR test each provide evidence supporting the inclusion of age-squared in the model.

Using simulations, Hagle and Mitchell (1992) and Windmeijer (1995) found that with ordinal outcomes, McKelvey and Zavoina’s pseudo-R² most closely approximates the R² obtained by fitting the LRM on the underlying latent variable. If you are using y*-standardized coefficients to interpret the ORM (see section 7.8.1), McKelvey and Zavoina’s R² could be used as a counterpart to the R² from the LRM.


## 1.5 (Advanced) Converting to a different parameterization

>We mark this section as advanced because the conversion we show is likely only pertinent to readers who also work with other statistics packages that fit the model by using the alternative parameterization. **The section may still be useful to strengthen your understanding of how the intercept and thresholds of these models are related, as well as how the **`lincom`** command works.**

Earlier, we noted that the model can be identified by fixing either the intercept or one of the thresholds to equal 0. Stata sets $\beta_{0}=0$ and estimates $\tau_{1}$,whereas some programs fix $\tau_{1}=0$ and estimate $\beta_{0}$ Although all quantities of interest for interpretation (for example, predicted probabilities) are the same under both parameterizations, it is useful to see how Stata can fit the model with either parameterization. We can understand how this is done with the following equation, where we are simply adding $0=\delta-\delta $ and rearranging terms:

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.3.png" style="width:550px;height:70px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Without further constraints, it is possible to estimate the differences $\tau_{m}-\delta $ and $\beta_{0}-\delta $, but not the parameters $\tau_m$ and $\beta_{0}$. To identify the model, Stata assumes $\delta=\beta_{0}$ which forces the estimate of  $\beta_{0}$ to be 0, Some programs assume $\delta=\tau_{1}$, which forces the estimate of $\tau_{1}$ to be 0. The following table shows the differences in the parameterizations:


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.4.png" style="width:550px;height:200px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Although you would only need to estimate the alternative parameterization if you wanted to compare your results with those produced by another statistics package,Seeing how this is done illustrates why the intercept and thresholds are arbitrary. To estimate the alternative parameterization, we use **`lincom`** to compute the difference between Stata’s estimates and the estimated value of the first cutpoint. We begin with the alternative parameterization of the intercept:

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
lincom 0 - _b[/cut1] // intercept
```

     ( 1)  - [/]cut1 = 0
    
    ------------------------------------------------------------------------------
           class | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             (1) |       2.14       0.23     9.39   0.000         1.69        2.59
    ------------------------------------------------------------------------------

To understand the **`lincom`** command, you need to know that **`_b[/cut1]`** is how Stata refers to the estimate of the first cutpoint. Accordingly, **`0 - _b[/cut1]`** is the difference between 0 and the estimate of the first cutpoint, which simply reverses the sign of the first estimated cutpoint.

For the other cutpoints, we are estimating $\tau_2-\tau_1$ and $\tau_3-\tau_1$, which correspond to **`_b[/cut2] - _b[/cut1]`** and **`_b[/cut3] - _b[/cut1]`**:

```
lincom _b[/cut2] - _b[/cut1] // cutpoint 2
```

     ( 1)  - [/]cut1 + [/]cut2 = 0
    
    ------------------------------------------------------------------------------
           class | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             (1) |       3.06       0.06    53.29   0.000         2.94        3.17
    ------------------------------------------------------------------------------

```
lincom _b[/cut3] - _b[/cut1] // cutpoint 3
```

     ( 1)  - [/]cut1 + [/]cut3 = 0
    
    ------------------------------------------------------------------------------
           class | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             (1) |       7.08       0.11    64.44   0.000         6.86        7.29
    ------------------------------------------------------------------------------

The estimate of $\tau_3-\tau_1$ is, of course, 0. These estimates would match those from a program using the alternative parameterization.

## 1.6 The parallel regression assumption
平行回归假设指的是在有序回归模型中的一个重要假设，即每个自变量对于每个结果类别的影响是平行的。简单来说，就是当自变量的取值变化时，不同结果类别之间的关系是保持一致的，只是存在一定的偏移或平移。

具体来说，在有序回归模型中，我们考虑多个有序的结果类别，比如低、中、高三个阶段。平行回归假设表明，自变量对于每个阶段的影响是一致的，只是在不同的阶段之间存在一定的平移或偏移。换句话说，自变量的影响随着结果类别的增加是保持不变的。

这个假设在统计建模中起着重要作用，因为它简化了模型的解释和推断过程。然而，在实际应用中，这个假设并不总是成立，因此需要进行相应的检验和处理。如果平行回归假设被拒绝，就需要考虑使用其他不需要这种假设的模型。

假设我们对一种药物治疗心脏病的效果进行研究，我们将患者的病情分为轻、中、重三个等级。我们想知道药物治疗时间对于病情的改善有何影响。

根据平行回归假设，药物治疗时间对于不同等级病情的改善效果应该是一致的，只是存在一定的平移。举个例子，假设我们发现增加药物治疗时间可以显著降低每个等级病情的严重程度，但是降低的幅度可能不同。比如，增加一周的药物治疗时间可能对于轻病情的改善效果是30%，对于中等病情是25%，对于重病情是20%。

这就是平行回归假设的含义，即自变量（药物治疗时间）对于不同等级的病情改善效果是平行的，只是存在一定的偏移。也就是说，药物治疗时间对于病情的改善效果随着病情等级的增加是保持一致的，只是改善的幅度有所不同。

然而，如果平行回归假设不成立，就意味着药物治疗时间对于不同等级的病情改善效果并不是平行的，可能存在交叉效应或者其他的差异。在这种情况下，我们需要考虑使用其他模型来更准确地描述药物治疗时间与病情之间的关系。
Before discussing interpretation, it is important to understand an assumption that is implicit in the ORM, known both as the parallel regression assumption and, for the ordinal logit model, the proportional-odds assumption. Using Equation (7.1), the ORM with $J$ outcome categories can be written as:

$$\Pr\left(y=1\mid\mathbf{x}\right)=F\left(\tau_1-\mathbf{x}\boldsymbol{\beta}\right)$$
$$\Pr\left(y=m\mid\mathbf{x}\right)=F\left(\tau_{m}-\mathbf{x}\boldsymbol{\beta}\right)-F\left(\tau_{m-1}-\mathbf{x}\boldsymbol{\beta}\right)\mathrm{~for~}m=2\mathrm{~to~}J-1$$
$$\Pr\left(y=J\mid\mathbf{x}\right)=1-F\left(\tau_{J-1}-\mathbf{x}\boldsymbol{\beta}\right)$$

Using these equations, the cumulative probabilities have the simple form

$$\Pr(y\leq m\mid\mathbf{x})=F(\tau_m-\mathbf{x}\boldsymbol{\beta})\quad\mathrm{for~}m=1\mathrm{~to~}J-1$$

Notice that $\beta$ does not have a subscript $m$. Accordingly, this equation shows that the ORM is equivalent to $J - 1$ binary regressions with the critical assumption that the slope coefficients are identical in each binary regression.

For example, with four outcomes and one independent variable, the cumulative probability equations are:

$$\begin{aligned}\Pr\left(y\leq1\mid\mathbf{x}\right)&=F\left(\tau_1-\beta x\right)\\\Pr\left(y\leq2\mid\mathbf{x}\right)&=F\left(\tau_2-\beta x\right)\\\Pr\left(y\leq3\mid\mathbf{x}\right)&=F\left(\tau_3-\beta x\right)\end{aligned}$$

Recall that the intercept $a$ is not in the equation because it was assumed to equal 0 to identify the model. These equations lead to the following figure:

**第一部分：介绍**

在这段文字中，作者讨论了有序回归模型（ORM）的一个重要假设，即“平行回归假设”或者在有序logit模型中叫做“比例几率假设”。

这个假设告诉我们，在这个模型中，无论有多少个结果类别，每一次增加一个类别，自变量对结果的影响都是一致的。换句话说，无论是第一个结果还是最后一个结果，自变量的影响都是一样的。

**第二部分：方程表示**

具体来说，我们用一系列的方程来表示每个结果类别的发生概率。比如，对于第一个结果类别，概率是根据一个函数 $ F $，结合一些参数 $ \tau_1 $ 和 $ \beta $ 计算得到的。这个方程是：

$ \Pr(y=1|\mathbf{x}) = F(\tau_1 - \mathbf{x}\boldsymbol{\beta}) $

**第三部分：解释阈值参数**

而对于其它类别，概率的计算与前面的类别有关，通过减去前一个类别的概率来得到。这些方程中包含了一些参数 $ \tau_1, \tau_2, ..., \tau_{J-1} $，它们是用来决定在哪些点概率发生变化的。

这些阈值参数告诉我们，在自变量的不同取值下，从一个结果类别跳到另一个结果类别的临界点在哪里。

**第四部分：斜率参数的解释**

还有一个参数 $ \boldsymbol{\beta} $，表示自变量对结果的影响。在这些方程中，参数 $ \boldsymbol{\beta} $ 是相同的，不管是哪个结果类别。这就是为什么我们说这个模型遵循“平行回归假设”，因为每个类别之间的关系都是平行的，斜率参数都相同。

这意味着，自变量对于每个结果类别的影响是一致的，只是在不同结果类别间有一定的偏移。这就是为什么我们称之为“平行回归假设”。

**第五部分：方程的简化形式**

在这个模型中，我们可以进一步简化方程，得到一种更简单的形式来表示累积概率。这种形式告诉我们，在给定自变量 $ \mathbf{x} $ 的情况下，结果小于等于某个特定类别的累积概率是多少。这个形式如下：

$ \Pr(y \leq m|\mathbf{x}) = F(\tau_m - \mathbf{x}\boldsymbol{\beta}) \quad \text{for } m=1 \text{ to } J-1 $

**第六部分：参数的一致性**

最后，需要注意的是，尽管方程中的参数 $ \boldsymbol{\beta} $ 在不同的结果类别中是相同的，但这并不意味着这个参数在整个模型中是固定的。事实上，在模型估计过程中，我们会通过拟合数据来估计参数的值，以使模型尽可能地拟合数据。

因此，即使在这个假设下，参数 $ \boldsymbol{\beta} $ 是相同的，但在实际应用中，我们仍然需要对它进行估计，以获得最优的模型拟合效果。


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.5.png" style="width:480px;height:380px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

Each probability curve differs only in being shifted to the left or right. The curves are parallel as a consequence of the assumption that the $\beta's$ are equal for each equation.

This figure suggests that the parallel regression assumption can be tested by comparing the estimates from $J-1$ binary regressions.

$$\mathrm{Pr}(y\leq m\mid\mathbf{x})=F(\tau_m-\mathbf{x}\boldsymbol{\beta}_m)\quad\mathrm{for}m=1\mathrm{to}J-1$$

where the $\beta's$ are allowed to differ across the equations. The model in (7.4), called the generalized ORM is discussed further in section 7.16.2. The parallel regression assumption implies that $\beta_1=\beta_2=\cdots=\beta_{J-1}.$ To the degree that the parallel regression assumption holds, the estimates <math xmlns="http://www.w3.org/1998/Math/MathML">
  <msub>
    <mrow>
      <mover accent="true" accentunder="false">
        <mrow>
          <mi>β</mi>
        </mrow>
        <mo>^</mo>
      </mover>
    </mrow>
    <mn>1</mn>
  </msub>
  <mo>,</mo>
  <msub>
    <mrow>
      <mover accent="true" accentunder="false">
        <mrow>
          <mi>β</mi>
        </mrow>
        <mo>^</mo>
      </mover>
    </mrow>
    <mn>2</mn>
  </msub>
  <mo>,</mo>
  <mo>…</mo>
  <mo>,</mo>
  <msub>
    <mrow>
      <mover accent="true" accentunder="false">
        <mrow>
          <mi>β</mi>
        </mrow>
        <mo>^</mo>
      </mover>
    </mrow>
    <mrow>
      <mi>J</mi>
      <mo>−</mo>
      <mn>1</mn>
    </mrow>
  </msub>
</math> should be close.

There are several ways of testing the parallel regression assumption, although none of them can be done using commands in official Stata. Instead, the user-written command **`oparallel`** (Buis 2013), **`gologit2`** (Williams 2005), or **`brant`** (part of our SPost package) is required. To install any of these, while in Stata and connected to the Internet, type **`net search command-name`** and follow the instructions for installation. We begin by briefly describing the tests, and then we show how to compute them in Stata.

Under maximum likelihood theory, there are three types of tests: Wald tests, LR tests, and score tests (also called Lagrange multiplier tests). To understand how these tests are used to test the parallel regression assumption, let the generalized ORM in Equation (7.4) be the unconstrained model and the ORM in Equation (7.3) be the constrained model. We want to test the hypothesis $H_0:\beta_1=\beta_2=\ldots=\beta_k.$ That is, we want to test the restrictions on the unconstrained model that lead to the constrained model. A Wald test estimates the unconstrained model and tests the restrictions in the null hypothesis. The LR test estimates both the unconstrained and the constrained models and examines the change of the log likelihood. The score test estimates the constrained model and (oversimplifying some) estimates how much the log likelihood would change if the constraints were relaxed.

The command **`oparallel`** computes each type of test for the ordered logit model but not for the ordered probit model. The Wald test is computed by fitting a generalized ordered logit model with **`gologit2`** and then testing the constraints implied by parallel regressions with the **`test`** command. The LR test is computed by fitting a generalized ordered logit model with **`gologit2`** and the ordered logit model with **`ologit`** and comparing the log likelihoods. **`oparallel`** can also compute approximations of the LR and Wald tests. The approximate LR test is computed by comparing the log likelihood from **`ologit`** or **`oprobit`** with the likelihoods obtained by pooling $J - 1$ binary models fit with **`logit`** or **`probit`** and making an adjustment for the correlation between the binary outcomes defined by $y < m$ (Wolfe and Gould 1998). The approximate Wald test, known as the Brant test, compares the estimates from binary logit models. Details on how this test is computed are found in Brant (1990) and Long (1997, 143-144). The **`oparallel`** command does not test for violations of parallel regressions for individual variables, so below we discuss the **`brant`** command, which does.

While the null hypothesis might be rejected because the $\beta_m's$ differ by $m$, Brant (1990) notes that the null hypothesis could be rejected because of other departures from the specified model. Reiterating this point, Greene and Hensher (2010) suggest that tests of the parallel assumption are only useful for "supporting or casting doubt on the basic model", but the tests do not indicate what the appropriate model might be. This issue is considered further in chapter 8.

这段话的意思是：

1. 每个概率曲线之间唯一的区别在于左右移动。这是因为假设斜率参数 $ \beta $ 对每个方程都是相等的，所以曲线是平行的。

2. 作者提到，通过比较 $ J-1 $ 个二元回归的估计结果，可以测试平行回归假设。这些二元回归的方程如下所示：

$$ \mathrm{Pr}(y\leq m\mid\mathbf{x})=F(\tau_m-\mathbf{x}\boldsymbol{\beta}_m) \quad \mathrm{for} \ m=1 \ \mathrm{to} \ J-1 $$

在这些方程中，斜率参数 $ \boldsymbol{\beta} $ 可以在不同的方程中是不同的。作者提到，这个模型被称为广义ORM，并在第7.16.2节进一步讨论。

1. 平行回归假设意味着 $ \beta_1 = \beta_2 = \ldots = \beta_{J-1} $。如果平行回归假设成立，那么估计出的斜率参数 $ \hatβ_1 ,  \hatβ_2 ,  \ldots, \hat{\beta}_{J-1}$ 应该是接近的。

2. 要测试平行回归假设，可以使用几种方法，其中包括 Wald 检验、LR 检验和 score 检验。这些检验需要使用特定的统计命令，如 **`oparallel`**、**`gologit2`** 和 **`brant`**。这些命令的安装方法可以通过在 Stata 中连接到互联网后使用 **`net search command-name`** 命令来完成。

3. 作者简要介绍了这些检验的原理，然后说明了在 Stata 中如何执行这些检验。Wald 检验通过拟合未约束模型并测试空假设中的约束来进行。LR 检验则同时估计未约束模型和约束模型，并检查对数似然的变化情况。score 检验估计约束模型并估计对数似然如何随着约束放松而变化。

4. **`oparallel`** 命令可以计算有序 logit 模型的这三种检验中的每一种，但不适用于有序 probit 模型。具体方法包括使用 **`gologit2`** 拟合广义有序 logit 模型，并使用 **`test`** 命令进行 Wald 检验；使用 **`gologit2`** 拟合广义有序 logit 模型和 **`ologit`** 拟合有序 logit 模型，然后比较对数似然值进行 LR 检验；使用 **`ologit`** 或 **`oprobit`** 拟合有序 logit 或 probit 模型，然后使用二元 logit 或 probit 模型估计二元模型，计算近似 LR 检验；使用 **`brant`** 命令进行近似 Wald 检验。

### 1.6.1 Testing the parallel regression assumption using oparallel

**`oparallel`** can be used after **`ologit`** to compute the omnibus tests described above. The **`ic`** option provides the statistics AIC and BIC comparing the generalized ordered logit model with the ordered logit model.

```
ssc install oparallel
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
oparallel, ic
```

    Tests of the parallel regression assumption
    
                     |   Chi2     df  P>Chi2
    -----------------+----------------------
         Wolfe Gould |  127.1     16   0.000
               Brant |    137     16   0.000
               score |  144.9     16   0.000
    likelihood ratio |  126.3     16   0.000
                Wald |  141.2     16   0.000
    
    Information criteria
    
          |     ologit     gologit  difference 
    ------+-----------------------------------
      AIC |   10609.85    10515.55       94.29 
      BIC |   10682.82    10694.67      -11.85 
    
The results labeled "likelihood ratio" and "Wald" are the LR and Wald tests based on the generalized ordered logit model. The line "Wolfe Gould" contains the approximate LR test, "Brant" refers to the Brant test, and "score" is the score test. All tests reject the null hypothesis with $p < 0.001$. The score and Wald tests have similar values, while the two LR tests are larger. We find that these tests are often, perhaps usually, significant.

The AIC and BIC statistics can be used to evaluate the trade-off between the better fit of the generalized model and the loss of parsimony from having $J - 1$ coefficients for each independent variable instead of just one. In this example, the smaller values of both the AIC and BIC statistics for **`gologit`** compared with **`ologit`** provide evidence against the **`ologit`** model compared with the model in which the parallel regression assumption is relaxed. It is common for the BIC statistic to prefer the **`ologit`** model even when the significance tests reject the parallel regression assumption, and this sometimes happens with AIC as well.

Although **`oparallel`** can be used with **`ologit`** but not with **`oprobit`**, the approximate LR test presented as "Wolfe Gould" can be performed with **`oprobit`** by using the user-written command **`omodel`**. **`omodel`**, however, does not support factor-variable notation.

### 1.6.2 Testing the parallel regression assumption using brant

The SPost command **`brant`** also computes the Brant test for the ORM. The advantage of our command, which is used by **`oparallel`** to make its computations, is that it provides separate tests for each of the independent variables in the model. After running **`ologit`**, you run **`brant`**, which has the following syntax:

>brant [,detail]

The **`detail`** option provides a table of coefficients from each of the binary models. For example,

```
brant, detail
```

    Estimated coefficients from binary logits
    
    -----------------------------------------------
        Variable |  y_gt_1     y_gt_2     y_gt_3   
    -------------+---------------------------------
          female |
         female  |   -0.382     -0.027     -0.135  
                 |    -3.43      -0.46      -0.90  
                 |
           white |
          white  |    0.323      0.396      0.088  
                 |     2.60       5.11       0.42  
                 |
            year |
           1996  |   -0.328     -0.141     -0.110  
                 |    -2.10      -1.91      -0.57  
           2012  |   -1.075     -0.406     -0.309  
                 |    -6.94      -4.98      -1.45  
                 |
            educ |
        hs only  |    0.841      0.497     -0.151  
                 |     6.89       5.96      -0.59  
        college  |    2.668      2.092      1.456  
                 |    10.26      20.70       5.96  
                 |
             age |   -0.006     -0.015      0.051  
                 |    -0.36      -1.54       1.88  
                 |
     c.age#c.age |    0.000      0.000     -0.000  
                 |     0.37       3.85      -1.28  
                 |
           _cons |    2.377     -1.210     -5.286  
                 |     5.60      -5.02      -7.34  
    -----------------------------------------------
                                        Legend: b/t
    
    Brant test of parallel regression assumption
    
                  |       chi2     p>chi2      df
     -------------+------------------------------
              All |     137.04      0.000      16
     -------------+------------------------------
         1.female |      10.02      0.007       2
          1.white |       2.34      0.310       2
           2.year |       1.45      0.483       2
           3.year |      18.38      0.000       2
           2.educ |      14.41      0.001       2
           3.educ |      12.05      0.002       2
              age |       5.87      0.053       2
      c.age#c.age |      10.13      0.006       2
    
    A significant test statistic provides evidence that the parallel
    regression assumption has been violated.

The largest violation is for income, which may indicate particular problems with the parallel regression assumption for this variable. Looking at the coefficients from the binary logits, we see that for income the estimates from the binary logit of lower class versus working/middle/upper class differ from the other two binary logits. This suggests that income differences matter more for whether people report themselves as lower class than it does for either of the other thresholds. If the focus of our project was the relationship between income and subjective class identification, this would serve as a substantively interesting finding that we would have missed had we not used **`brant`**.    
这段话介绍了使用 **`brant`** 命令进行 Brant 测试的方法和结果解释：

1. **Brant 测试**：Brant 测试用于测试平行回归假设是否成立。这个假设是有序回归模型中的一个重要假设，即每个自变量对于每个结果类别的影响是平行的。

2. **使用方法**：首先，使用 **`ologit`** 命令拟合有序 logit 模型。然后，运行 **`brant`** 命令来进行 Brant 测试。该命令的语法是 **`brant [,detail]`**。使用 **`detail`** 选项可以得到每个二元模型的系数表格。

3. **结果解释**：在 Brant 测试的结果中，对每个自变量进行了单独的平行回归假设检验。如果检验统计量显著，则意味着平行回归假设被违反。

4. **系数表格**：系数表格展示了每个自变量在不同结果类别下的系数估计值，以及对应的 t 值。此外，还提供了对平行回归假设检验的结果。如果检验统计量显著，则表示对应的自变量违反了平行回归假设。

5. **解释**：如果某个自变量的平行回归假设检验显著，说明该自变量在不同结果类别下的影响不是平行的，即不同结果类别之间的效应存在差异。在本例中，发现收入（income）变量的违反程度最大，这可能意味着该变量对于结果类别的影响存在特定的问题。系数表格也显示了收入变量在不同二元模型中的估计值存在差异，这表明了收入对于被调查者报告自己为下层阶级的影响与其他两个阈值相比更加显著。如果研究项目的重点是收入与主观阶级认同之间的关系，那么这将作为一个具有实质性意义的发现。
### 1.6.3 Caveat regarding the parallel regression assumption

In the majority of the real-world applications of the ORM that we have seen, the hypothesis of parallel regressions is rejected. Keep in mind, however, that the tests of the parallel regression assumption are sensitive to other types of misspecification. Further, we have seen examples where the parallel regression assumption is violated but the predictions from **`ologit`** are very similar to those from the generalized ordered logit model or the multinomial logit model. When the hypothesis is rejected, consider alternative models that do not impose the constraint of parallel regressions. As illustrated in chapter 8, fitting the multinomial logit model on a seemingly ordinal outcome can lead to quite different conclusions. The generalized ordered logit model is another alternative to consider. Violation of the parallel regression assumption is not, however, a rationale for using the LRM. The assumptions implied by the application of the LRM to ordinal data are even stronger.
这段话讲述了在实际应用中，我们经常会看到平行回归假设被拒绝的情况。然而，需要记住的是，对平行回归假设的检验对于其他类型的错误规范也很敏感。此外，我们也看到过违反平行回归假设的例子，但是来自 **`ologit`** 的预测结果与广义有序 logit 模型或多项式 logit 模型的预测结果非常相似。当假设被拒绝时，应考虑不需要平行回归约束的备选模型。正如在第 8 章中所示，对看似有序的结果进行多项式 logit 模型拟合可能会得出完全不同的结论。广义有序 logit 模型是另一个要考虑的备选方案。然而，违反平行回归假设并不是使用多项式回归模型的理由。将多项式回归模型应用于有序数据所隐含的假设甚至更加严格。

## 1.7 Overview of interpretation

Most of the rest of the chapter focuses on interpreting results from the ORM. First, we consider methods of interpretation that are based on transforming the coefficients. If the idea of a latent variable makes substantive sense, or if you are tempted to run a linear regression on an ordinal outcome, interpretations based on rescaling $y^*$ to compute standardized coefficients can be used just like coefficients for the LRM. Coefficients can also be exponentiated and interpreted as odds ratios in the ordered logit model. We examine these strategies of interpretation first because they follow most straightforwardly from the methods of interpretation many readers are already familiar with from linear regression, but we regard them also as having important limitations that we discuss.

We then consider approaches to interpretation that use predicted probabilities, extending each of the methods for the LRM to multiple outcomes. We typically find these approaches far more informative. Because the ORM is nonlinear in the outcome probabilities, no approach can fully describe the relationship between a variable and the outcome probabilities. Consequently, you should consider each of these methods before deciding which approach is most effective in your application. As with models for binary outcomes, the basic command for interpretations based on predictions is **`margins`**, although our **`mtable`**, **`mchange`**, and **`mgen`** commands make things much simpler. Not only do these commands have the advantages illustrated for binary models in chapter 6, but when there are multiple outcome categories, **`margins`** can only compute predictions for one outcome at a time. Our commands will compute predictions for all categories and combine the results.

## 1.8 Interpreting transformed coefficients

As with the binary response model (BRM), coefficients for the ordered logit model are about 1.7 times larger than those for the ordered probit model because of the arbitrary assumption about the variance of the error term. For this reason, neither ordered logit nor ordered probit coefficients offer a direct interpretation that is readily meaningful. **There are two ways we can transform the coefficients into more meaningful quantities: standardization and odds ratios. In both cases, these interpretations are permissible only when the independent variable is not specified using polynomial or interaction terms.**

### 1.8.1 Marginal change in y*
In the ORM, $y*=\mathbf{x}\boldsymbol{\beta}+\varepsilon $ and the marginal change in $y^*$ with respect to $x_k$ is 

$$\frac{\partial y^*}{\partial x_k}=\beta_k$$

Because $y*$ is latent, its true metric is unknown. The value of $y*$depends on the identification assumption we make about the variance of the errors. As a result, the marginal change in $y*$cannot be interpreted without standardizing by the estimated standard deviation of $y*$,which is computed as

$$\widehat{\sigma}_{y^{*}}^{2}=\widehat{\beta}^{\prime}\widehat{\mathrm{Var}}\left(\mathbf{x}\right)\widehat{\beta}+\mathrm{Var}\left(\varepsilon\right)$$

where ${\widehat{\mathrm{Var}}}\left(\mathbf{x}\right)$ is the covariance matrix for the observed $x's,\widehat{\beta}$ contains maximum likelihood estimates, and $\mathrm{Var}(\varepsilon)=1$ for ordered probit and $\pi^{2}/3$ for ordered logit. Then the $y^*$ standardized coefficient for $x_k$ is

$$\beta_{k}^{Sy^* }=\frac{\beta_{k}}{\sigma_{y^*}}$$

which can be interpreted as follows:

>For a unit increase in $x_k$,$y^* $ expected to increase by $\beta_{k}^{\mathbf{S}y^{*}}$ standard deviations, holding all other variables constant.

The fully standardized coefficient is

$$\beta_{k}^{S}=\frac{\sigma_{k}\beta_{k}}{\sigma_{y^{*}}}$$

which can be interpreted as follows:

>For a standard deviation increase in $x_k$,$y^* $ is expected to inciease by $\beta_{k}^{S}$ standard deviations, holding all other variables constant.





1. **$y^*$的定义和边际变化**：
   - 在有序反应模型（ORM）中，$y^*$表示一个未观测到的变量，可以用以下方程表示：
     
     $$ y^* = \mathbf{x}\boldsymbol{\beta} + \varepsilon $$
     
   - 这里，$\mathbf{x}$是自变量向量，$\boldsymbol{\beta}$是参数向量，$\varepsilon$是误差项。
   - 对于特定自变量$x_k$，$y^*$关于$x_k$的边际变化等于与该变量相关的系数$\beta_k$：

     $$ \frac{\partial y^*}{\partial x_k} = \beta_k $$

2. **$y^*$的标准化和解释**：
   - 由于$y^*$的真实尺度未知，需要标准化才能解释其含义。标准化后的系数表示单位变化引起的标准偏差变化。
   - 对于自变量$x_k$，其标准化系数为：
$$\beta_{k}^{Sy^* }=\frac{\beta_{k}}{\sigma_{y^*}}$$
     
   - 其中，$\sigma_{y^* }$是$y^*$的估计标准差。

1. **完全标准化系数**：
   - 完全标准化系数考虑了自变量 $x_k$ 和 $y^* $ 的标准差，用于衡量单位标准差变化对$y^* $的影响。
   - 对于自变量$x_k$，其完全标准化系数为：

$$\beta_{k}^{S}=\frac{\sigma_{k}\beta_{k}}{\sigma_{y^{*}}}$$  

4. **使用listcoef命令计算**：
   - 这些系数是通过listcoef命令和std选项计算得出的。


    让我们考虑一个学生的考试成绩预测模型的例子：


5. **考试成绩模型**：
   - 我们正在研究一个模型，用于预测学生的考试成绩$y^*$。我们假设考试成绩受到学习时间和课外活动的影响。
   - 考试成绩模型是一个线性模型，可以表示为：
     $$ y^* = \beta_0 + \beta_1 \times \text{学习时间} + \beta_2 \times \text{课外活动} + \varepsilon $$
   - 在这个方程中，$\beta_0$是截距，$\beta_1$和$\beta_2$分别是学习时间和课外活动的系数，$\varepsilon$是未观测到的因素对考试成绩的影响。

6. **标准化系数的解释**：
   - 标准化系数用于比较不同变量对$y^* $的影响。假设我们估计出的$\beta_1$为5，表示每增加一个单位的学习时间，$y^* $预期会增加5个单位。
   - 但是，学习时间的单位可能与考试成绩的单位不同，因此我们需要将系数标准化。
   - 标准化系数$\beta_{1}^{Sy^* }$是系数$\beta_1$除以$y^* $的标准差$\sigma_{y^* }$。如果$\beta_1$为5，而$\sigma_{y^* }$为10，那么：
     $$ \beta_{1}^{Sy^* } = \frac{5}{10} = 0.5 $$
   - 这意味着每增加一个标准差的学习时间，$y^* $预期会增加0.5个标准差。

7. **完全标准化系数的解释**：
   - 完全标准化系数考虑了自变量和$y^* $的标准差。假设我们还知道课外活动的标准差为3，而$y^* $的标准差仍然为10。
   - 完全标准化系数$\beta_{1}^{S}$是系数$\beta_1$乘以自变量$x$的标准差，再除以$y^* $的标准差。如果$\beta_1$为5，学习时间的标准差为2，那么：
     $$ \beta_{1}^{S} = \frac{2 \times 5}{10} = 1 $$
   - 这意味着每增加一个标准差的学习时间，$y^*$预期会增加1个标准差。

These coefficients are computed using **``listcoef``** with the **``std``** option. For example, after fitting the ordered logit model,

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
listcoef, std help
```

    ologit (N=5620): Unstandardized and standardized estimates 
    
      Observed SD:  0.6749
        Latent SD:  2.1437
    
    -------------------------------------------------------------------------------
                 |         b        z    P>|z|    bStdX    bStdY   bStdXY     SDofX
    -------------+-----------------------------------------------------------------
          female |
         female  |    0.0162    0.298    0.765    0.008    0.008    0.004     0.498
                 |
           white |
          white  |    0.2363    3.277    0.001    0.092    0.110    0.043     0.389
                 |
            year |
           1996  |   -0.0799   -1.158    0.247   -0.040   -0.037   -0.019     0.498
           2012  |   -0.5039   -6.594    0.000   -0.233   -0.235   -0.109     0.463
                 |
            educ |
        hs only  |    0.3705    4.730    0.000    0.183    0.173    0.085     0.493
        college  |    1.5656   15.994    0.000    0.670    0.730    0.313     0.428
                 |
             age |   -0.0488   -5.308    0.000   -0.825   -0.023   -0.385    16.897
                 |
     c.age#c.age |    0.0007    7.646    0.000    1.202    0.000    0.561  1695.148
                 |
          income |    0.0116   22.203    0.000    0.770    0.005    0.359    66.258
    -------------------------------------------------------------------------------
           b = raw coefficient
           z = z-score for test of b=0
       P>|z| = p-value for z-test
       bStdX = x-standardized coefficient
       bStdY = y-standardized coefficient
      bStdXY = fully standardized coefficient
       SDofX = standard deviation of X

In our example, we can think of the dependent variable as measuring subjective social standing. Consequently, examples of interpretation are as follows:

>The subjective social standing of those with a high school degree as their highest degree is 0.17 standard deviations higher than that of those who do not have a high school diploma, holding all other variables constant.

>Each standard deviation increase in household income increases support by 0.36 standard deviations, holding all other variables constant.

Because the coefficients in the columns **``b``** and **``bStdX``** are not based on standardizing $y^*$, they should not be interpreted. And while **``listcoef``** presents coefficients for age, these should not be interpreted because you cannot change age while holding age-squared constant. An advantage of the methods of interpretation using probabilities that we discuss later in the chapter is that they can be used more simply when polynomials or interactions are in the model.

Although we do not often use coefficients for the marginal change in $y^*$ to interpret the ORM, we believe that this is a much better approach than fitting the LRM with an ordinal dependent variable and interpreting the LRM coefficients.

### 1.8.2 Odds ratios

The ordinal logit model (but not the ordinal probit model) can also be interpreted using odds ratios. Equation (7.2) defined the ordered logit model as

$$\Omega_{\leq m|>m}\left(\mathbf{x}\right)=\exp\left(\tau_m-\mathbf{x}\boldsymbol{\beta}\right)$$

For example, with four outcomes we would simultaneously estimate the three equations

$$\Omega_{\leq1|>1}\left(\mathbf{x}\right)=\exp\left(\tau_1-\mathbf{x}\boldsymbol{\beta}\right)$$
$$\Omega_{\leq2|>2}\left(\mathbf{x}\right)=\exp\left(\tau_{2}-\mathbf{x\beta}\right)$$
$$\Omega_{\leq3|>3}\left(\mathbf{x}\right)=\exp\left(\tau_{3}-\mathbf{x\beta}\right)$$

Using the same approach as shown for binary logit, the effect of a unit change in $x_k$ equals
$$\frac{\Omega_{\leq m|>m}\left(\mathbf{x},x_k+1\right)}{\Omega_{\leq m|>m}\left(\mathbf{x},x_k\right)}=e^{-\beta_k}=\frac{1}{e^{\beta_k}}$$

The value of the odds ratio does not depend on the value of $m$, which is why the parallel regression assumption is also known as the proportional-odds assumption. We could interpret the odds ratio as follows:

>For a unit increase in $x_k$, the odds of a lower outcome compared with a higher outcome are changed by the factor $\exp(-\beta_k)$, holding all other variables constant.

For a change in $x_k$ of $\delta$,

$$\frac{\Omega_{\leq m|>m}\left(\mathrm{x},x_k+\delta\right)}{\Omega_{\leq m|>m}\left(\mathrm{x},x_k\right)}=\exp\left(-\delta\times\beta_k\right)=\frac{1}{\exp\left(\delta\times\beta_k\right)}$$

which we interpret as follows:

>For an increase of $\delta$ in $x_k$， the odds of a lower outcome compared with a higher outcome change by a factor of exp$\left(-\delta\times\beta_{k}\right)$ , holding all other variables constant.

Notice that the odds ratio is derived by changing one variable, $x_k$, while holding all other variables constant. Accordingly, you do not want to compute the odds ratio for a variable that is included as a polynomial (for example, age and age-squared) or is included in an interaction term.


1. **有序logit模型方程式（7.2）**：

$$
\Omega_{\leq m|>m}(\mathbf{x}) = \exp(\tau_m - \mathbf{x}\boldsymbol{\beta})
$$

这个方程描述了一种统计模型，用于预测一系列有序结果的概率。$\Omega_{\leq m|>m}(\mathbf{x})$表示在给定一组特征$\mathbf{x}$的情况下，得到结果小于或等于$m$的概率与得到结果大于$m$的概率之比。$\tau_m$是模型中的一个参数，$\mathbf{x}$是包含特征值的向量，$\boldsymbol{\beta}$是与特征相关的参数向量。

2. **各个等式的解释**：

对于有四个结果的情况，我们需要同时估计三个方程：

- 第一个方程表示等于或小于1的结果的概率与大于1的结果的概率之比。
$$\Omega_{\leq1|>1}\left(\mathbf{x}\right)=\exp\left(\tau_1-\mathbf{x}\boldsymbol{\beta}\right)$$
- 第二个方程表示等于或小于2的结果的概率与大于2的结果的概率之比。
$$\Omega_{\leq2|>2}\left(\mathbf{x}\right)=\exp\left(\tau_{2}-\mathbf{x\beta}\right)$$
- 第三个方程表示等于或小于3的结果的概率与大于3的结果的概率之比。
$$\Omega_{\leq3|>3}\left(\mathbf{x}\right)=\exp\left(\tau_{3}-\mathbf{x\beta}\right)$$
1. **赔率比的计算公式**：

$$
\frac{\Omega_{\leq m|>m}(\mathbf{x},x_k+1)}{\Omega_{\leq m|>m}(\mathbf{x},x_k)} = e^{-\beta_k} = \frac{1}{e^{\beta_k}}
$$

这个公式告诉我们，当一个特征值$x_k$增加一个单位时，结果小于或等于$m$的概率与结果大于$m$的概率之比会以$e^{-\beta_k}$的因子改变，而其他特征保持不变。$\beta_k$是与特征$x_k$相关的参数。

4. **赔率比的解释**：

赔率比的值不依赖于结果的具体取值，因此被称为平行回归假设或比例赔率假设。对赔率比的解释是，对于$x_k$的单位增加，结果较低与结果较高之间的赔率会以$\exp(-\beta_k)$的因子改变，而其他变量保持不变。

当然，让我们通过一个简单的例子来说明这些概念。

假设我们正在研究一个考试成绩的有序结果：不及格、及格、良好、优秀。我们想知道学生的学习时间（特征$x_k$）对于各个考试成绩的影响。

1. **有序logit模型方程式**：

$$
\Omega_{\leq m|>m}(\mathbf{x}) = \exp(\tau_m - \mathbf{x}\boldsymbol{\beta})
$$

在我们的例子中，$\Omega_{\leq m|>m}(\mathbf{x})$表示在给定学习时间$\mathbf{x}$的情况下，得到结果小于或等于某个等级$m$（比如“及格”）的概率与得到结果大于$m$的概率之比。$\tau_m$是模型中的一个参数，表示不同等级之间的分界点，$\mathbf{x}$是学习时间的值，$\boldsymbol{\beta}$是与学习时间相关的参数。

2. **赔率比的计算公式**：

$$
\frac{\Omega_{\leq m|>m}(\mathbf{x},x_k+1)}{\Omega_{\leq m|>m}(\mathbf{x},x_k)} = e^{-\beta_k} = \frac{1}{e^{\beta_k}}
$$

这个公式告诉我们，当学习时间增加一个单位时，结果小于或等于某个等级$m$（比如“及格”）的概率与结果大于$m$的概率之比会以$e^{-\beta_k}$的因子改变，而其他特征保持不变。$\beta_k$是与学习时间$x_k$相关的参数。

假设我们的模型告诉我们，$\beta_k = 0.5$。这意味着当学习时间增加一个单位时，结果小于或等于某个等级$m$的概率与结果大于$m$的概率之比会减少一半。

例如，如果学生平均每天学习3小时（$x_k = 3$），那么结果小于或等于“及格”的概率与结果大于“及格”的概率之比为$e^{-0.5} \approx 0.606$。这意味着考试成绩达到及格的概率是考试成绩高于及格的概率的0.606倍。

当然，让我用更具体的数值来展示计算过程。

假设我们的模型给出的参数是 $\beta_k = 0.5$，而学生平均每天学习3小时（$x_k = 3$）。现在我们来计算结果小于或等于“及格”的概率与结果大于“及格”的概率之比。

根据赔率比的计算公式：

$$
\frac{\Omega_{\leq m|>m}(\mathbf{x},x_k+1)}{\Omega_{\leq m|>m}(\mathbf{x},x_k)} = e^{-\beta_k}
$$

将参数代入：

$$
\frac{\Omega_{\leq m|>m}(\mathbf{x},4)}{\Omega_{\leq m|>m}(\mathbf{x},3)} = e^{-0.5}
$$

因此，我们需要计算 $e^{-0.5}$ 的值，这里 $e$ 是自然对数的底，约为2.718。

$$
e^{-0.5} ≈ \frac{1}{e^{0.5}} ≈ \frac{1}{2.718^{0.5}} ≈ \frac{1}{1.648} ≈ 0.606
$$

所以，结果小于或等于“及格”的概率与结果大于“及格”的概率之比约为 0.606。这意味着考试成绩达到及格的概率是考试成绩高于及格的概率的约0.606倍。

The odds ratios for a unit and a standard deviation change of the independent variables can be computed with **``listcoef``**, which lists the factor changes in the odds of higher versus lower outcomes. You could also obtain odds ratios by using the **``or``** option with the **``ologit``** command. Here we request odds ratios for only white, year, income, and age:

```
listcoef white year income age, help
```

    ologit (N=5620): Factor change in odds 
    
      Odds of: >m vs <=m
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|       e^b   e^bStdX     SDofX
    -------------+----------------------------------------------------------
           white |
          white  |    0.2363    3.277    0.001     1.267     1.096     0.389
                 |
            year |
           1996  |   -0.0799   -1.158    0.247     0.923     0.961     0.498
           2012  |   -0.5039   -6.594    0.000     0.604     0.792     0.463
                 |
             age |   -0.0488   -5.308    0.000     0.952     0.438    16.897
                 |
     c.age#c.age |    0.0007    7.646    0.000     1.001     3.328  1695.148
                 |
          income |    0.0116   22.203    0.000     1.012     2.160    66.258
    ------------------------------------------------------------------------
           b = raw coefficient
           z = z-score for test of b=0
       P>|z| = p-value for z-test
         e^b = exp(b) = factor change in odds for unit increase in X
     e^bStdX = exp(b*SD of X) = change in odds for SD increase in X
       SDofX = standard deviation of X
    
Here are some interpretations:

>The odds of reporting higher subjective class standing are 0.60 times smaller in 2012 than they were in 1980, holding all other variables constant.

>For a standard deviation increase in income, the odds of indicating higher social standing increase by a factor of 2.16, holding all other variables constant.

Although odds ratios for age are shown, these should not be interpreted because you cannot change age while holding age-squared constant. If you prefer, you can compute coefficients for the percentage change in the odds by adding the **``percent``** option:

```
listcoef white year income, percent
```

    ologit (N=5620): Percentage change in odds 
    
      Odds of: >m vs <=m
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|         %     %StdX     SDofX
    -------------+----------------------------------------------------------
           white |
          white  |    0.2363    3.277    0.001      26.7       9.6     0.389
                 |
            year |
           1996  |   -0.0799   -1.158    0.247      -7.7      -3.9     0.498
           2012  |   -0.5039   -6.594    0.000     -39.6     -20.8     0.463
                 |
          income |    0.0116   22.203    0.000       1.2     116.0    66.258
    ------------------------------------------------------------------------

These results can be interpreted as follows:

>The odds of reporting higher subjective class standing are 40% smaller in 2012 than they were in 1980, holding all other variables constant.

>For a standard deviation increase in income, the odds of indicating higher social standing increase by 116%, holding all other variables constant.

So far, we interpreted the factor changes in the odds of lower outcomes compared with higher outcomes. This is done because the model is traditionally written in terms of the odds of lower versus higher outcomes,$\Omega_{\leq m|>m}\left(\mathbf{x}\right)$ leading to the factor change coefficient of $\exp\left(-\beta_{k}\right)$.We could ju st as well consider the factor change in the odds of higher versus lower values; that is, changes in the odds $\Omega_{>m|\leq m}\left(x\right)$ , which equals $\exp\left(\beta_{k}\right)$ . These odds ratios can be obtained by adding the option reverse:

```
listcoef year, reverse
```

    ologit (N=5620): Factor change in odds 
    
      Odds of: <=m vs >m
    
    ------------------------------------------------------------------------
                 |         b        z    P>|z|       e^b   e^bStdX     SDofX
    -------------+----------------------------------------------------------
            year |
           1996  |   -0.0799   -1.158    0.247     1.083     1.041     0.498
           2012  |   -0.5039   -6.594    0.000     1.655     1.262     0.463
    ------------------------------------------------------------------------

Notice that the output now says "Odds of: <=m vs >m" instead of "Odds of: >m vs <=m," as it did earlier. This factor change of 1.66 for 2012 is the inverse of the earlier value 0.60. Our interpretation is the following:

>The odds of reporting lower social standing are about 1.66 times larger in 2012 than they were in 1980, holding all other variables constant.

When presenting odds ratios, some people find it easier to understand the results if you talk about increases in the odds rather than decreases. That is, it is clearer to say:

"The odds increased by a factor of 2" than to say, "The odds decreased by a factor of 0.5". If you agree, then you can reverse the order when presenting odds.

When interpreting odds ratios, remember three points that were discussed in detail in chapter 6. 

**First, because odds ratios are multiplicative coefficients, positive and negative effects should be compared by taking the inverse of the negative effect (or vice versa). For example, a negative factor change of 0.5 has the same magnitude as a positive factor change of 2 = 1/0.5.**

**Second, interpretation assumes only that the other variables have been held constant, not held at specific values.** 

**Third, a constant factor change in the odds does not correspond to a constant change or constant factor change in the probability.**

As with binary outcomes, we discuss odds ratios because they are commonly used with these models and provide a compact means of interpretation. Yet we think they are overused, especially in data based on population samples instead of case-control studies. The meaning of the magnitude of multiplicative changes in odds is often unclear to audiences, perhaps even more so when thinking about transitions across thresholds that divide sets of categories. We are perhaps inclined to favor y-standardized coefficients over odds ratios for ordinal outcomes; the idea of a standard deviation change in the latent variable allows results to be understood more clearly because of the analogue to linear regression. Better still, however, are methods of interpretation that are based on predicted probabilities, which we discuss next.

## 1.9 Interpretations based on predicted probabilities

As noted, we usually prefer interpretations based on predicted probabilities. **We find these interpretations to be both clearer for our own thinking and more effective with audiences.** Probabilities can be estimated with the formula

$$\widehat{\mathrm{Pr}}\left(y=m\mid\mathbf{x}\right)=F\left(\widehat{\tau_m}-\mathbf{x}\widehat{\beta}\right)-F\left(\widehat{\tau}_{m-1}-\mathbf{x}\widehat{\beta}\right)$$

Cumulative probabilities are computed as

$$\widehat{\Pr}\left(y\leq m\mid\mathbf{x}\right)=\sum_{k\leq m}\widehat{\Pr}\left(y=k\mid\mathbf{x}\right)=F\left(\widehat{\tau}_m-\mathbf{x}\widehat{\boldsymbol{\beta}}\right)$$

The values of x can be based on observations in the sample or can be hypothetical values of interest.

让我详细解释这两个公式的含义，并给出一个例子。

1. **预测概率公式**：

$$
\widehat{\mathrm{Pr}}\left(y=m\mid\mathbf{x}\right) = F\left(\widehat{\tau_m}-\mathbf{x}\widehat{\beta}\right)-F\left(\widehat{\tau}_{m-1}-\mathbf{x}\widehat{\beta}\right)
$$

这个公式用于计算在给定特征$\mathbf{x}$的情况下，观测到结果为$m$的概率。$\widehat{\mathrm{Pr}}\left(y=m\mid\mathbf{x}\right)$表示结果等于$m$的概率的估计值。$F$代表累积分布函数，通常是指标函数的累积分布函数，它将一个值映射到概率上。$\widehat{\tau_m}$是模型中的一个参数，表示结果为$m$的临界值，$\widehat{\beta}$是模型中与特征相关的参数。

2. **累积概率公式**：

$$
\widehat{\Pr}\left(y\leq m\mid\mathbf{x}\right) = \sum_{k\leq m}\widehat{\Pr}\left(y=k\mid\mathbf{x}\right) = F\left(\widehat{\tau}_m-\mathbf{x}\widehat{\boldsymbol{\beta}}\right)
$$

这个公式用于计算在给定特征$\mathbf{x}$的情况下，观测到结果小于或等于$m$的概率。$\widehat{\Pr}\left(y\leq m\mid\mathbf{x}\right)$表示结果小于或等于$m$的概率的估计值。这个概率是对从结果等于1到结果等于$m$的所有可能结果的概率进行求和得到的。$\widehat{\tau}_m$是模型中的一个参数，表示结果为$m$的临界值，$\widehat{\boldsymbol{\beta}}$是模型中与特征相关的参数向量。

**例子：**

假设我们正在研究一个学生考试成绩的模型，特征$\mathbf{x}$包括学习时间和家庭背景等因素。我们想要估计一个学生考试成绩为及格的概率，并计算学生考试成绩小于或等于良好的概率。

假设我们的模型给出以下估计值：$\widehat{\tau_1} = 0$，$\widehat{\tau_2} = 1$，$\widehat{\beta} = 0.5$。现在，我们有一个学生，他每天学习3小时。

首先，我们计算学生考试成绩为及格的概率：

$$
\begin{align*}
\widehat{\mathrm{Pr}}(y=\text{及格}|\mathbf{x}) &= F(\widehat{\tau_2} - \mathbf{x}\widehat{\beta}) - F(\widehat{\tau_1} - \mathbf{x}\widehat{\beta}) \\
&= F(1 - 3 \times 0.5) - F(0 - 3 \times 0.5) \\
&= F(1.5) - F(-1.5)
\end{align*}
$$

这个结果是一个估计值，表示学生考试成绩为及格的概率。

接下来，我们计算学生考试成绩小于或等于良好的概率：

$$
\widehat{\Pr}(y\leq \text{良好}|\mathbf{x}) = F(\widehat{\tau}_3 - \mathbf{x}\widehat{\boldsymbol{\beta}})
$$

在这个例子中，我们假设良好的成绩对应的临界值是$\widehat{\tau}_3 = 2$。因此：

$$
\begin{align*}
\widehat{\Pr}(y\leq \text{良好}|\mathbf{x}) &= F(\widehat{\tau}_3 - \mathbf{x}\widehat{\boldsymbol{\beta}}) \\
&= F(2 - 3 \times 0.5) \\
&= F(0.5)
\end{align*}
$$

这个结果是一个估计值，表示学生考试成绩小于或等于良好的概率。



The following sections use predicted probabilities in a variety of ways. 

**We begin by examining the distribution of predictions for each observation in the estimation sample as a first step in evaluating your model.** 

**Next, we show how marginal effects provide an overall assessment of the impact of each variable.** To focus on particular types of respondents, we compute predictions for ideal types defined by substantively motivated characteristics for all independent variables. Extending methods from chapter 6, we show how to statistically test differences in the predictions between ideal types. For categorical predictors, tables of predictions computed as these variables change is an effective way to demonstrate the effects of these variables is to use tables of predictions computed as these variables change. **An important challenge is to decide where to hold the values of other variables when making predictions.** 

Finally, we plot predictions as a continuous independent variable changes.

## 1.10 Predicted probabilities with predict

After fitting a model with **``ologit``** or **``oprobit``**, a useful first step for assessing your model is to compute the in-sample predictions with the command

**`predict`** newvar1 [ newvar2 [newvar3··· ]] [if] [in]

where you specify one new variable name for each category of the dependent variable. For instance, in the following example, **``predict``** specifies that the variables **prlover**, **prworking**, **prmiddle**, and **prupper** be created with predicted values for the four outcome categories:

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog    
predict prlower prworking prmiddle prupper
```

    Ordered logistic regression                            Number of obs =   5,620
                                                           LR chi2(9)    = 1453.95
                                                           Prob > chi2   =  0.0000
    Log likelihood = -5016.2107                            Pseudo R2     =  0.1266
    
    ------------------------------------------------------------------------------
           class | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
          female |
         female  |       0.02       0.05     0.30   0.765        -0.09        0.12
                 |
           white |
          white  |       0.24       0.07     3.28   0.001         0.09        0.38
                 |
            year |
           1996  |      -0.08       0.07    -1.16   0.247        -0.22        0.06
           2012  |      -0.50       0.08    -6.59   0.000        -0.65       -0.35
                 |
            educ |
        hs only  |       0.37       0.08     4.73   0.000         0.22        0.52
        college  |       1.57       0.10    15.99   0.000         1.37        1.76
                 |
             age |      -0.05       0.01    -5.31   0.000        -0.07       -0.03
                 |
     c.age#c.age |       0.00       0.00     7.65   0.000         0.00        0.00
                 |
          income |       0.01       0.00    22.20   0.000         0.01        0.01
    -------------+----------------------------------------------------------------
           /cut1 |      -2.14       0.23                         -2.59       -1.69
           /cut2 |       0.92       0.23                          0.47        1.36
           /cut3 |       4.93       0.24                          4.46        5.41
    ------------------------------------------------------------------------------

(option pr assumed; predicted probabilities)

The message (**``option pr assumed; predicted probabilities``**) reflects that predict can compute many different quantities. **Because we did not specify an option indicating which quantity to predict, the default option **``pr``** for predicted probabilities was assumed.**

Predictions in the sample are useful for getting a general sense of what is going on in your model and can be useful for uncovering problems in your data. For example, if there are observations where the predicted probability of being in **``prlower``** (or any other outcome) are noticeably larger or smaller than the other predictions, you might check whether there are data problems for those observations. The range of predictions can also give you a rough idea of how large marginal effects can be for a given outcome. If the range of probabilities is small within the estimation sample, the effects of the independent variables will also be small. If the distribution of predictions has multiple modes — let’s say, two — it suggests there could be a binary predictor that is important. Although sometimes the distribution of predictions leads to additional data checking or model revision, often it only assures you that the predictions are reasonable and that you are ready for the methods of interpretation that we consider in the remainder of this chapter.

好的，让我详细解释一下。

1. **样本中的预测值**：假设我们有一个模型，用来预测学生考试成绩的可能结果。对于每个学生，我们可以使用这个模型来预测他们可能取得的不同等级的考试成绩，比如不及格、及格、良好和优秀。这些预测值告诉我们，根据模型，每个学生将以多大的概率获得不同等级的成绩。

2. **检测数据问题**：对于第二条，我们讨论的是样本中的预测值可能帮助我们发现数据中存在的问题。

当我们观察到某些观测的预测概率明显高于或低于其他观测时，这可能表明数据中存在问题。例如，如果某些观测的预测概率远高于其他观测，这可能是因为这些观测包含了异常值，或者数据记录可能存在错误。

举个例子，假设我们正在研究一个模型，用来预测学生考试成绩的可能结果。我们发现，某些学生的预测概率特别高，远高于其他学生。这可能意味着这些学生的数据存在问题，比如他们的成绩可能被错误地记录在了高分段，或者他们的学习时间等特征值可能存在异常值。

通过观察这些异常的预测值，我们可以发现并修正数据中的问题，例如，我们可以重新检查这些学生的成绩记录，或者确认他们的特征值是否正确录入。这样，我们就可以提高模型的准确性，并确保我们的分析结果是可靠的。


3. **边际效应的估计**：当我们说预测值的范围可以帮助我们了解模型的边际效应时，我们是指模型对于不同情况的反应程度。边际效应是指独立变量的一个单位变化如何影响因变量的变化。如果模型的预测值范围很小，意味着模型对于不同情况的反应差异不大，这可能表明模型的边际效应较小。换句话说，即使独立变量有所变化，模型对于结果的预测也不会有太大变化。

举个例子来说明，假设我们正在研究一个模型，用来预测学生考试成绩的可能结果。我们发现，无论学生的学习时间是少还是多，模型对于他们的考试成绩的预测都差不多。这就意味着学生的学习时间对于考试成绩的影响并不显著，模型的边际效应较小。

而当预测值的范围很大时，意味着模型对于不同情况的反应差异较大，这可能表明模型的边际效应较大。换句话说，独立变量的变化会导致结果的较大变化。


4. **检测重要预测变量**：当我们观察到预测分布中存在多个峰值时，这可能意味着有一个重要的二元预测变量影响着结果。换句话说，这种现象可能暗示着某个二元变量对于结果的影响非常重要。通过观察这些预测分布的特征，我们可以更深入地了解模型中各个变量的影响，并相应地调整我们的分析方法。

总之，通过观察样本中的预测结果，我们可以更好地了解模型的表现，并发现数据中可能存在的问题，以便进一步改进我们的分析。


An easy way to see the distribution of the predictions is with **``dotplot``**, one of our favorite commands for quickly checking data:

```
label var prlower "Pr(Lower)"
label var prworking "Pr(Working)"
label var prmiddle "Pr(Middle)"
label var prupper "Pr(Upper)"
dotplot prlower prworking prmiddle prupper, ///
    ylabel(0(.25)1, grid gmin gmax) ytitle("Probability")
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.6.png" style="width:550px;height:380px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The predicted probabilities for the extreme categories of lower and upper tend to be less than 0.20, with most predictions for the middle categories falling between 0.25 and 0.75. The probabilities for the middle two categories are generally larger than the probabilities of the extreme categories, reflecting the higher observed proportions of observations for these categories. The long tail for the probabilities for “Upper” lead us to examine the data further, but no problems were found. If you look at the probabilities of identifying as working class, you will notice a spike in the number of cases near its highest predicted probability, around 0.65. This is common for middle categories when plotting predicted probabilities for the ORM and should not be cause for concern. For extreme categories, the predicted probabilities of individual observations in the ORM are bound only by 0 and 1. For middle categories, however, the distance between estimated cutpoints implies a maximum predicted probability, where a greater distance between cutpoints implies a higher maximum (see section 7.15 for more about why this is so).

In this example, with **``predict``** we specified separate variables for each outcome category. Because of this, **``predict``** understood that we wanted predicted probabilities for each category. Had we specified only one variable, **``predict``** would generate predicted values of $y^*$, not probabilities. To compute the predicted probability for a single outcome category, you need the **``outcome(#)``** option, such as **``predict prmiddle, outcome(3)``**. The # specified with **``outcome(#)``** is the rank position of the category from lowest to highest. If the outcome variable is numbered with consecutive integers starting with 1, as in our example, then # corresponds to the outcome value. However, if our outcome values were numbered 0, 1, 2, and 3, then **``outcome(1)``** would provide the predicted probability that $y = 0$ , not that $y = 1$ . We find this extremely confusing in practice. To avoid it, we strongly recommend numbering outcome values with consecutive integers starting with 1 whenever working with ordinal or nominal outcomes.

Examining predicted probabilities within the sample provides a first, quick check of the model. To understand and present the substantive findings, however, you will usually want to compute predictions at specific, substantively informative values.

## 1.11 Marginal effects

The marginal change in the probability of outcome m is computed as
$$\frac{\partial\Pr(y=m\mid\mathbf{x})}{\partial x_k}=\frac{\partial F(\tau_m-\mathbf{x}\boldsymbol{\beta})}{\partial x_k}-\frac{\partial F(\tau_{m-1}-\mathbf{x}\boldsymbol{\beta})}{\partial x_k}$$

which is the slope of the curve relating $x_k$ to $\Pr(y=m|\mathbf{x})$, holding all other variables
constant. The value of the marginal change depends on the value of $x_k$ where the change is evaluated, as well as the values of all other $x’s$ Because the marginal change can be misleading when the probability curve is changing rapidly, we usually prefer using discrete change. The discrete change is the change in the probability of $m$ for a change in $x_k$ from the start value $x_k^{\mathrm{start}}$ to the end value $x_{k}^{\mathrm{end}}$ (for example, a change from $x_k = 0$ to $x_k = 1$), holding all other $x$'s constant. Formally,

$$\frac{\Delta\Pr\left(y=m\mid\mathbf{x}\right)}{\Delta x_k\left(x_k^{\mathrm{start}}\to x_k^{\mathrm{cod}}\right)}=\Pr\left(y=m\mid\mathbf{x},x_k=x_k^{\mathrm{cod}}\right)-\Pr\left(y=m\mid\mathbf{x},x_k=x_k^{\mathrm{start}}\right)$$

where $\Pr\left(y=m\mid\mathbf{x},x_k\right)$ is the probability that $y = m$ given $x$, noting a specific value for $X_k$.The change indicates that when $x_k$ changes from $x_{k}^{\mathrm{start}}\text{ to }x_{k}^{\mathrm{end}}$  the probability of outcome m changes by $\Delta\Pr\left(y=m\mid\mathbf{x}\right)/\Delta x_{k}$ holding all other variables at the specific values in $x$. The magnitude of the discrete change depends on the value at which $X_k$ starts, the amount of change in $x_k$, and the values of all other variables.

For both marginal and discrete change, we can compute average marginal effects (AMEs), marginal effects at the mean (MEMs), or marginal effects at representative values other than the means. As with the BRM, we find AMEs to be the most useful summary of the effects, thus we consider AMEs for the ORM in this section. MEMs are considered briefly in section 7.15. Examining the distribution of effects over observations is also valuable. To save space, we do not illustrate this in the current chapter, but this can be done using the commands presented in section 8.8.1.

当然，以下是你要求的完整答案：

首先，让我们来详细解释这两个公式。

**第一个公式**：
$$
\frac{\partial\Pr(y=m\mid\mathbf{x})}{\partial x_k} = \frac{\partial F(\tau_m-\mathbf{x}\boldsymbol{\beta})}{\partial x_k} - \frac{\partial F(\tau_{m-1}-\mathbf{x}\boldsymbol{\beta})}{\partial x_k}
$$

这个公式表示了因变量 $y$ 为 $m$ 的概率 $\Pr(y=m\mid\mathbf{x})$ 对于自变量 $x_k$ 的边际变化。让我们逐步分解这个公式：

1. $\frac{\partial\Pr(y=m\mid\mathbf{x})}{\partial x_k}$：表示因变量 $y$ 为 $m$ 的概率随着 $x_k$ 的变化而变化的速率。

2. $\frac{\partial F(\tau_m-\mathbf{x}\boldsymbol{\beta})}{\partial x_k}$：表示 $x_k$ 的微小变化对应的概率值 $F(\tau_m-\mathbf{x}\boldsymbol{\beta})$ 的变化量。

3. $\frac{\partial F(\tau_{m-1}-\mathbf{x}\boldsymbol{\beta})}{\partial x_k}$：类似地，表示 $x_k$ 的微小变化对应的概率值 $F(\tau_{m-1}-\mathbf{x}\boldsymbol{\beta})$ 的变化量。

换句话说，这个公式告诉我们，当自变量 $x_k$ 变化时，因变量 $y$ 为 $m$ 的概率的变化量，是由两部分组成的：一部分是 $y$ 大于 $m$ 的概率的变化量，另一部分是 $y$ 小于等于 $m$ 的概率的变化量。

**第二个公式**：
$$
\frac{\Delta\Pr\left(y=m\mid\mathbf{x}\right)}{\Delta x_k\left(x_k^{\mathrm{start}}\to x_k^{\mathrm{end}}\right)} = \Pr\left(y=m\mid\mathbf{x},x_k=x_k^{\mathrm{end}}\right) - \Pr\left(y=m\mid\mathbf{x},x_k=x_k^{\mathrm{start}}\right)
$$

这个公式表示，在自变量 $x_k$ 从起始值 $x_k^{\mathrm{start}}$ 变化到结束值 $x_{k}^{\mathrm{end}}$ 的情况下，因变量 $y$ 为 $m$ 的概率的变化量。让我们逐步分解这个公式：

1. $\frac{\Delta\Pr\left(y=m\mid\mathbf{x}\right)}{\Delta x_k\left(x_k^{\mathrm{start}}\to x_k^{\mathrm{end}}\right)}$：表示自变量 $x_k$ 从起始值到结束值的变化对应的因变量 $y$ 为 $m$ 的概率的变化量。

2. $\Pr\left(y=m\mid\mathbf{x},x_k=x_k^{\mathrm{end}}\right)$：表示当 $x_k$ 变为结束值时，$y$ 为 $m$ 的概率。

3. $\Pr\left(y=m\mid\mathbf{x},x_k=x_k^{\mathrm{start}}\right)$：表示当 $x_k$ 是起始值时，$y$ 为 $m$ 的概率。

换句话说，这个公式告诉我们，当自变量 $x_k$ 从起始值变化到结束值时，因变量 $y$ 为 $m$ 的概率的变化量，是结束值时的概率减去起始值时的概率。

现在，让我们来举个例子来说明这两个公式的用法。

假设我们有一个模型，可以预测学生的考试成绩。我们想知道的是，当学生花更多时间学习时，他们的考试成绩会如何变化。

首先，我们使用第一个公式来计算边际变化。我们假设学生学习时间是一个自变量 $x_k$，而考试成绩是因变量 $y$。我们计算学习时间对于考试成绩的边际变化，从而得知每增加一小时学习时间，考试成绩的概率会以多快的速度变化。

然后，我们使用第二个公式来计算离散变化。我们假设学生的起始学习时间是 $x_k^{\mathrm{start}}$，结束学习时间是 $x_{k}^{\mathrm{end}}$。我们计算学生在起始值和结束值时的考试成绩的概率，然后得出当学习时间从起始值变化到结束值时，考试成绩的概率的变化量。

通过这样的分析，我们就可以更好地了解学生学习时间对于考试成绩的影响，并且可以确定学生增加学习时间能够对提高考试成绩产生多大的影响。


To illustrate the use of marginal effects in ordinal models, we begin by examining the average marginal change for income. The marginal change can be computed with **``mchange``**, where we select the variable **``income``** and use **``amount(marginal)``** to request only marginal changes without any discrete changes. Because we have not included the **``atmeans``** option, **``mchange``** computes the AME over all observations:

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
mchange income, amount(marginal)
```

    ologit: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
    income       |                                           
        Marginal |    -0.001     -0.002      0.002      0.000 
         p-value |     0.000      0.000      0.000      0.000 
    
    Average predictions
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
      Pr(y|base) |     0.071      0.460      0.434      0.034 

The marginal changes are in the row labeled Marginal, with the significance level for the test of the hypothesis that the change is 0 listed in the row p-value. In this example, the marginal changes are all less than 0.003 in magnitude, but the p-values are nevertheless significant. We see that on average higher income decreases identification with lower and working class, while increasing identification with middle and upper class. Across all categories, the AMEs must sum to 0, because any increase in the probability of one category must be offset by a decrease in another category. The results in the output might not sum exactly to 0, however, because of rounding. The average predicted probabilities of each outcome are listed below the table of marginal changes. The average predicted probability of someone identifying as lower class is 0.07, as working class is 0.46, and so on. **These probabilities must, of course, sum to 1.**

In this example, the marginal changes with respect to income are small, and it is difficult to grasp how large the effects of income are in terms of changes in the probabilities of class identification. A marginal change is the instantaneous rate of change that does not correspond exactly to the amount of change in the probability for a change of one unit in the independent variable. If the probability curve is approximately linear where the change is evaluated, the marginal change will approximate the effect of a unit change in the variable on the probability of an outcome. The best way to determine how well the marginal change approximates the discrete change is to compute the discrete change, which we do next.

The variable income is measured in thousands of dollars. Looking at the descriptive statistics for this variable,

```
sum income
```

        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
          income |      5,620    68.07737    66.25833     .51205   324.2425

we see that the range is over $300,000$, so a unit change is too small for describing an effect of income on class identification. Another way of thinking about this is that a $1,000$ difference in income is substantively small compared with what we might anticipate would have an appreciable effect on whether people view themselves as, for example, working class rather than middle class. **By default, mchange computes discrete changes for both a 1-unit change and a standard deviation change, where we use brief to suppress showing the average probabilities**:

```
mchange income, brief
```

    ologit: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
    income       |                                           
              +1 |    -0.001     -0.002      0.002      0.000 
         p-value |     0.000      0.000      0.000      0.000 
             +SD |    -0.036     -0.119      0.126      0.030 
         p-value |     0.000      0.000      0.000      0.000 
        Marginal |    -0.001     -0.002      0.002      0.000 
         p-value |     0.000      0.000      0.000      0.000 

We can interpret the results for a change of a standard deviation in income as follows:

>On average, a standard deviation increase in income (about $66,000$) is associated with a 0.036 decrease in the probability of identifying as lower class and a 0.119 decrease in identifying as working class. This is offset by an increase of 0.126 in the probability of identifying as middle class and a 0.030 increase in upper-class identification. All effects are significant at the 0.001 level.

Instead of a standard deviation change, we might be interested in a change of a specific amount, like a $25,000$ increase. It might be tempting to (incorrectly) compute the discrete change for a 25-unit change in income by simply multiplying the 1-unit discrete change by 25. Although this will give you approximately the right answer if the probability curve is nearly linear over the range of the change, in some cases it can give misleading results and even the wrong sign. To be safe, do not do it! Instead, the **``delta(#)``** option for **`mchange`** computes the discrete change as an independent value changes from the base value to # units above the base value. Here we use **``delta(25)``**:

```
mchange income, delta(25) amount(delta) brief
```

    ologit: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
    income       |                                           
          +delta |    -0.016     -0.042      0.049      0.009 
         p-value |     0.000      0.000      0.000      0.000 

We can interpret the results as follows:

On average, a $25,000$ change in income is associated with a 0.049 increase in the probability of identifying as middle class and a 0.042 decrease in the probability of identifying as working class.

We can also compute changes in the predicted probability as a continuous variable changes **from its minimum to the maximum** by specifying the option **``amount(range)``**:

```
mchange income, amount(range) brief
```

    ologit: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
    income       |                                           
           Range |    -0.112     -0.514      0.371      0.255 
         p-value |     0.000      0.000      0.000      0.000 

With a variable like income where there might be a few respondents with very high incomes, **a trimmed range might be more informative.** **Here, by specifying **``trim(5)``**, we examine the effect of a change from the 5th percentile of income to the 95th percentile**:

```
mchange income, amount(range) trim(5) brief
```

    ologit: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
    income       |                                           
       5% to 95% |    -0.094     -0.365      0.385      0.074 
         p-value |     0.000      0.000      0.000      0.000 

The effects are substantially smaller, most noticeably for those identifying as upper class. If you look carefully, you will notice that as a result of decreasing the amount of change in income, the change in the probability of middle-class affiliation is increasing. **The reason for this apparent anomaly is explained in section 7.15**.

The **``mchange``** command uses **``margins``** to compute the changes. If you want the convenience of **``mchange``** but also want to see how **``margins``** is being used, you can specify the option **``commands``** to see the **``margins``** commands used by **``mchange``** or specify the **``detail``** option to obtain the full **``margins``** output.


### 1.11.1 Plotting marginal effects

As we suggested for the BRM, the AME is a valuable tool for examining the effects of your variables, and we often compute these effects for an initial review of the results of a model. Without doubt, AMEs are far more informative than the parameter estimates or odds ratios. There is, however, a lot of information to be absorbed. By default, for each continuous variable, **``mchange``** computes the marginal change and discrete changes for a 1-unit and a standard deviation change in continuous variables; for factor variables, **``mchange``** computes a discrete change from 0 to 1. One way to limit the amount of information is to only look at discrete changes of a standard deviation for continuous variables. For example,

当我们谈论一个自变量的平均边际效应时，我们实际上是在讨论这个自变量对于因变量的影响，而不是仅仅讨论它们之间的相关性或关系强度。平均边际效应提供了一个量化的指标，告诉我们当自变量的值发生变化时，因变量的预期变化量。

在实际应用中，平均边际效应通常是在拟合了统计模型之后计算得出的。比如，在逻辑回归模型中，我们可以计算出每个自变量对于因变量的平均边际效应，从而了解它们对于结果的实际影响。这种影响可以是因变量的变化概率，也可以是其他感兴趣的结果指标。

举个例子，假设我们在研究人们购买某种产品的决策时，使用了一个逻辑回归模型，并且其中一个自变量是收入水平。通过计算收入水平的平均边际效应，我们可以得知当收入增加一个单位时，购买该产品的概率预计会增加多少。这种量化的解释有助于我们更深入地理解模型结果，并且为决策提供更具体的指导。

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
mchange, amount(sd) brief
```

    ologit: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                            |     lower    working     middle      upper 
    ------------------------+-------------------------------------------
    female                  |                                           
             female vs male |    -0.001     -0.002      0.003      0.000 
                    p-value |     0.766      0.765      0.765      0.765 
    white                   |                                           
          white vs nonwhite |    -0.016     -0.031      0.041      0.006 
                    p-value |     0.002      0.001      0.001      0.001 
    year                    |                                           
               1996 vs 1980 |     0.004      0.012     -0.014     -0.003 
                    p-value |     0.243      0.249      0.246      0.253 
               2012 vs 1980 |     0.033      0.067     -0.086     -0.014 
                    p-value |     0.000      0.000      0.000      0.000 
               2012 vs 1996 |     0.029      0.055     -0.073     -0.011 
                    p-value |     0.000      0.000      0.000      0.000 
    educ                    |                                           
     hs only vs not hs grad |    -0.029     -0.047      0.070      0.006 
                    p-value |     0.000      0.000      0.000      0.000 
     college vs not hs grad |    -0.079     -0.252      0.286      0.046 
                    p-value |     0.000      0.000      0.000      0.000 
         college vs hs only |    -0.050     -0.205      0.216      0.039 
                    p-value |     0.000      0.000      0.000      0.000 
    age                     |                                           
                        +SD |    -0.018     -0.071      0.067      0.022 
                    p-value |     0.000      0.000      0.000      0.000 
    income                  |                                           
                        +SD |    -0.036     -0.119      0.126      0.030 
                    p-value |     0.000      0.000      0.000      0.000 

Even so, there are a lot of coefficients. Fortunately, they can be quickly understood by plotting them. **To explain how to do this, we begin by examining the AMEs for a standard deviation change in income and age. Because the model includes age and age-squared, when age is increased by a standard deviation, we need to increase age-squared by the appropriate amount.** This is done automatically by Stata because we entered age into the model with the factor-variable notation **c.age##c.age**. To compute the average discrete changes for a standard deviation increase, type

```
mchange age income, amount(sd) brief
```

    ologit: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
    age          |                                           
             +SD |    -0.018     -0.071      0.067      0.022 
         p-value |     0.000      0.000      0.000      0.000 
    income       |                                           
             +SD |    -0.036     -0.119      0.126      0.030 
         p-value |     0.000      0.000      0.000      0.000 

mchange leaves these results in memory, and they are used by our mchangeplot command to create the plot.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.7.png" style="width:550px;height:380px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The horizontal axis indicates the magnitude of the effect, with the letters within the graph marking the discrete change for each outcome. For example, the M in the row for income shows that, on average for a standard deviation change in income, the probability of identifying with the middle class increases by 0.126. Overall, it is apparent that the effects of income are larger than those for a standard deviation change in age. For both variables, the effects are in the same directions with the same relative magnitudes. (Before proceeding, you should make sure you see how the graph corresponds to the output from mchange above.)

The plot was produced with the following command:

```
    mchangeplot age income, symbols(L W M U) ///
    min(-.15) max(.15) gap(.05) aspectratio(.3) ///
    title("Social class: L=lower W=working M=middle U=upper", size(medsmall))
```

**After the variables are selected, symbols () specifies the letters to use for each outcome. By default, the first letters in the value labels are used, but here we chose to use capital letters instead of the lowercase letters used by class's value labels. The options **min()**, **max()**, and **gap()** define the tick marks and labels on the x-axis. The **ysize()** and **scale()** options affect the size of the graph and the scaled font size. Details on all options for **mchangeplot** can be found by typing **``help mchangeplot``**.**

Next, we consider the discrete change for a change from 0 to 1 for the binary variables **female** and **white**:

```
mchange female white, brief
```

    ologit: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                       |     lower    working     middle      upper 
    -------------------+-------------------------------------------
    female             |                                           
        female vs male |    -0.001     -0.002      0.003      0.000 
               p-value |     0.766      0.765      0.765      0.765 
    white              |                                           
     white vs nonwhite |    -0.016     -0.031      0.041      0.006 
               p-value |     0.002      0.001      0.001      0.001 

When producing the graph, we use the option **``sig(.05)``** to add an asterisk (*) to effects that are significant at the 0.05 level:

```
mchangeplot female white, symbols(L W M U) ///
    min(-.15) max(.15) gap(.05) aspectratio(.3) ///
    title("Social class: L=lower W=working M=middle U=upper", size(medsmall))
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.8.png" style="width:550px;height:380px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The effects of being female are small and nonsignificant, which is expected given that the coefficient for female is not significant. For the contrast between whites and nonwhites, on the other hand, we see significant differences, which we interpret as follows:

>The predicted probability of identifying as middle class is on average 0.04 higher for a white person than for an otherwise similar nonwhite person, while the predicted probability of identifying as working class is 0.03 lower.

For factor variables that have more than two categories, we want to examine the contrasts between all categories. Consider variables year and educ, each of which have three categories:

```
mchange educ year, brief
```

    ologit: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                            |     lower    working     middle      upper 
    ------------------------+-------------------------------------------
    educ                    |                                           
     hs only vs not hs grad |    -0.029     -0.047      0.070      0.006 
                    p-value |     0.000      0.000      0.000      0.000 
     college vs not hs grad |    -0.079     -0.252      0.286      0.046 
                    p-value |     0.000      0.000      0.000      0.000 
         college vs hs only |    -0.050     -0.205      0.216      0.039 
                    p-value |     0.000      0.000      0.000      0.000 
    year                    |                                           
               1996 vs 1980 |     0.004      0.012     -0.014     -0.003 
                    p-value |     0.243      0.249      0.246      0.253 
               2012 vs 1980 |     0.033      0.067     -0.086     -0.014 
                    p-value |     0.000      0.000      0.000      0.000 
               2012 vs 1996 |     0.029      0.055     -0.073     -0.011 
                    p-value |     0.000      0.000      0.000      0.000 



**``mchange``** computes all the pairwise contrasts. For example, with **``year``** the output compares those answering the survey in 1990 with those in 1980, in 2012 with 1980, and in 2012 with 1990. One of the contrasts is redundant in the sense that it can be computed from the other two. For example, looking at lower-class identity, the change in probability of 0.004 from 1980 to 1990 plus the change of 0.029 from 1990 to 2012 equals the change of 0.033 from 1980 to 2012. Still, it is often useful to examine all contrasts to find patterns. For this, we find that plotting the effects works well:

```
mchangeplot year, ///
  sig(.05) leftmargin(5) ///
  symbols(L W M U) min(-.15) max(.15) gap(.05) aspect(.3) ///
  title("Social class: L=lower  W=working  M=middle  U=upper", size(medsmall))
```
The **``significance()``** option specifies that *’s should be added to the plot symbols if the effect is significant at the given level, in this case, 0.05. The **``leftmargin()``** option increases the left margin of the graph to accommodate the value labels used with factor variables. The argument 5 represents the percentage of the graph size to be added to the left. The resulting graph looks like this:

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.9.png" style="width:550px;height:380px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

It is immediately apparent that there was little change from 1980 to 1996, while much larger and statistically significant changes occurred in 2012 compared with either of the earlier survey years.

Next, we plot the effects for educ without the **``sig()``** option because all the effects are significant:

```
mchangeplot educ, ///
  leftmargin(5) symbols(L W M U) min(-.30) max(.30) gap(.1) aspect(.3) ///
  title("Social class: L=lower  W=working  M=middle  U=upper", size(medsmall))
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.10.png" style="width:550px;height:380px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

These effects are larger than those for the year of the survey (notice that the x scale is not the same in the two graphs). Even though effects are statistically significant, the largest effects are comparing those who have a college degree with others, regardless of whether they have a high school diploma or did not graduate. With the overall pattern in mind, we can interpret multiple contrasts for a single category as follows:

>On average, having graduated from college increases a person’s probability of identifying as middle class by 0.22 compared with having graduated only from high school, and by 0.29 compared with not having graduated from high school.

Alternatively, we could interpret multiple categories for the same contrast:

>Compared with those who have graduated only from high school, we find that graduating from college on average increases the probability of identifying as upper class by 0.04 and of identifying as middle class by 0.22, while the probability of identifying as working class decreases by 0.21 and of identifying as lower class by 0.05.

### 1.11.2 Marginal effects for a quick overview

**AMEs are a much better way to obtain a quick overview of the magnitudes of effects than are the estimated coefficients. After fitting your model, you can obtain a table of all effects by simply typing **``mchange``**, perhaps restricting effects to discrete changes of a standard deviation:**

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
mchange, amount(sd) brief
```

    ologit: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                            |     lower    working     middle      upper 
    ------------------------+-------------------------------------------
    female                  |                                           
             female vs male |    -0.001     -0.002      0.003      0.000 
                    p-value |     0.766      0.765      0.765      0.765 
    white                   |                                           
          white vs nonwhite |    -0.016     -0.031      0.041      0.006 
                    p-value |     0.002      0.001      0.001      0.001 
    year                    |                                           
               1996 vs 1980 |     0.004      0.012     -0.014     -0.003 
                    p-value |     0.243      0.249      0.246      0.253 
               2012 vs 1980 |     0.033      0.067     -0.086     -0.014 
                    p-value |     0.000      0.000      0.000      0.000 
               2012 vs 1996 |     0.029      0.055     -0.073     -0.011 
                    p-value |     0.000      0.000      0.000      0.000 
    educ                    |                                           
     hs only vs not hs grad |    -0.029     -0.047      0.070      0.006 
                    p-value |     0.000      0.000      0.000      0.000 
     college vs not hs grad |    -0.079     -0.252      0.286      0.046 
                    p-value |     0.000      0.000      0.000      0.000 
         college vs hs only |    -0.050     -0.205      0.216      0.039 
                    p-value |     0.000      0.000      0.000      0.000 
    age                     |                                           
                        +SD |    -0.018     -0.071      0.067      0.022 
                    p-value |     0.000      0.000      0.000      0.000 
    income                  |                                           
                        +SD |    -0.036     -0.119      0.126      0.030 
                    p-value |     0.000      0.000      0.000      0.000 

With a simple command, you can plot the effects:

```
mchangeplot, sig(.05) symbols(L W M U) leftmargin(5)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.11.png" style="width:550px;height:380px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

A quick review highlights which variables we might, want to examine more closely.

## 1.12 Predicted probabilities for ideal types

**Ideal types define substantively interesting cases in the data by specifying values of the independent variables. Predicted probabilities for these types of individuals (or whatever the unit of analysis may be) can be computed with **``mtable``** or **``margins``**. Unlike marginal effects, by comparing two or more ideal types, you can compare probabilities as a whole set of independent variables vary, not just a change in a single variable.**

In our example, ideal types can be used to examine what more and less advantaged individuals look like and how they differ in their class identification. For instance, we might want to compare the following hypothetical individuals surveyed in 2012:

* A 25-year-old, nonwhite man without a high school diploma and with a household income of $30,000$ per year.
* A 60-year-old, white woman with a college degree and with a household income of $150,000$ per year.

To compute the predictions, we begin by using **``margins``** before showing how **``mtable``** can simplify the work. We use a **``at()``** to specify values of the independent variables. If there are variables whose values are not specified with a **``at()``**, we can use the option **``atmeans``** to assign them to their means. Otherwise, by default, **``margins``** and **``mtable``** would compute the average predicted probability over the sample for the unspecified independent variables. We do not want to do this because ideal types should be thought of as hypothetical observations, so averaging predictions over observations for some independent variables muddles the interpretation.

Using values we specified for our first ideal type, we run **``margins``**:

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
margins, at(female=0 white=0 year=3 educ=1 age=25 income=30)
```

    Adjusted predictions                                     Number of obs = 5,620
    Model VCE: OIM
    
    1._predict: Pr(class==1), predict(pr outcome(1))
    2._predict: Pr(class==2), predict(pr outcome(2))
    3._predict: Pr(class==3), predict(pr outcome(3))
    4._predict: Pr(class==4), predict(pr outcome(4))
    
    At: female =  0
        white  =  0
        year   =  3
        educ   =  1
        age    = 25
        income = 30
    
    ------------------------------------------------------------------------------
                 |            Delta-method
                 |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
        _predict |
              1  |       0.23       0.02    11.35   0.000         0.19        0.27
              2  |       0.63       0.01    59.17   0.000         0.61        0.65
              3  |       0.13       0.01    10.15   0.000         0.11        0.16
              4  |       0.00       0.00     6.68   0.000         0.00        0.00
    ------------------------------------------------------------------------------

**``margins``** can only compute a prediction for a single outcome. Because we did not specify which outcome, **``margins``** used the default prediction, which is described as **``Pr(class == l), predict()``**. This is the predicted probability for the first outcome. Hence, we find that the predicted probability of identifying as lower class for our first ideal type is 0.23.

```
margins, at(female=0 white=0 year=3 educ=1 age=25 income=30) predict(outcome(1))
margins, at(female=0 white=0 year=3 educ=1 age=25 income=30) predict(outcome(2))
margins, at(female=0 white=0 year=3 educ=1 age=25 income=30) predict(outcome(3))
margins, at(female=0 white=0 year=3 educ=1 age=25 income=30) predict(outcome(4))
```

It is easier, however, to use **``mtable``**, which computes predictions for all outcome categories and combines them into a single table. The option **``ci``** indicates that we want the output to show the confidence interval.

```
mtable, at(female=0 white=0 year=3 educ=1 age=25 income=30) ci
```

    Expression: Pr(class), predict(outcome())
    
               |    lower   working    middle     upper
     ----------+---------------------------------------
         Pr(y) |    0.230     0.634     0.133     0.003
            ll |    0.190     0.613     0.108     0.002
            ul |    0.270     0.655     0.159     0.004
    
    Specified values of covariates
    
               |   female     white      year      educ       age    income
     ----------+-----------------------------------------------------------
       Current |        0         0         3         1        25        30

We could also compute predicted probabilities for both ideal types at the same time:

```
mtable, atright norownum width(7) ///
    at(female=0 white=0 year=3 ed=1 age=25 income=30) ///
    at(female=1 white=1 year=3 ed=3 age=60 income=150)
```

    Expression: Pr(class), predict(outcome())
    
      lower  working   middle    upper   female    white     educ      age   income
    -------------------------------------------------------------------------------
      0.230    0.634    0.133    0.003        0        0        1       25       30
      0.008    0.138    0.759    0.095        1        1        3       60      150
    
    Specified values of covariates
    
               |    year
     ----------+--------
       Current |       3

The differences between the ideal types are striking: While our first type has a predicted probability of only 0.13 of identifying as middle class, our second has a probability of 0.76. The second type has a probability of less than 0.01 of identifying as lower class, while the first type has a probability of 0.23. The example makes plain the large effect that these variables together have on class identification.

Although having the results in a single table is much more convenient than having to combine results from four **``margins``** commands, we also did several things to make the output clearer. First, we used value labels for the dependent variable **``class``** to label the columns with predictions. Because it is easy to be confused about the outcome categories when using these models, we advise always assigning clear value labels to your dependent variable (see chapter 2). Option **``at right``** places the values of the covariates to the right of the predictions. Because the values of the covariates clearly identify the rows, we turned off the row numbers in the table of predictions by using **``norownum``**. And to fit the results more compactly, we specified the column widths with **``width(7)``**.

### 1.12.1 (Advanced) Testing differences between ideal types

>Although we regard this section as extremely useful, we mark it as advanced because it requires a firm grasp of using loops and local macros in Stata. If you are still getting used to these, you might want to skip this section until you are comfortable with both.

同We may want to know whether a difference between ideal types is statistically significant for the same reason that we may perform a significance test for a set of coefficients: we are considering a change that involves multiple variables, and we want to evaluate how likely it is that we would observe a difference this large just by chance. Although the differences between predictions for our two ideal types are almost certainly significant, we can test this by extending methods used for binary outcomes.

同To test differences in predictions, we need to overwrite the estimation results from **``ologit``** with the predictions generated by **``margins``**. An inherent limitation in **``margins``** is that posting can only be done for a single outcome. That is, we cannot post the predictions for our four outcomes at one time. (We hope this will be addressed in future versions of **``margins``**.) To deal with this inconvenience, we will use a **``forvalues``** loop to repeat the tests for each outcome. First, we list the model and store the estimates, because we will have to restore the model results after we post the predictions for a particular outcome:

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
estimates store olm
```

Next, we compute tests for each of the four outcome categories by using the following commands:

```
mlincom, clear
forvalues iout = 1/4 { // start loop
    quietly {
        mtable, out(`iout') post ///
            at(female=0 white=0 year=3 ed=1 age=25 income=30) ///
            at(female=1 white=1 year=3 ed=3 age=60 income=150)
    mlincom 1 - 2, stats(est pvalue) rowname(outcome `iout') add
    estimates restore olm
    }
 } // end loop
```

We start with **``mlincom, clear``** to erase previous results from **``mlincom``** before we accumulate the new results that we will use to make our table. The **``forvalues {...}``** loop runs the code between braces once for each outcome. We use **``quietly {...}``** so that the output from **``mtable``** and **``mlincom``** is not displayed. Instead, we will list results after the loop is completed. Within the loop, the **``mtable``** option **``post``** saves the estimates for outcome 'iout' to the matrix **``e(b)``** so that **``mlincom``** can test the difference between the predictions for the first and second ideal types. The **``mlincom``** command uses option **``add``** to collect the results to display later.

After the loop, running **``mlincom``** without options displays the results we just computed. The column named **``lincom``** has the linear combination of the estimates—in this case, the difference in predictions—while column **``pvalue``** is the p-value for testing that the difference is 0:

```
mlincom
```

             |   lincom    pvalue 
-------------+-------------------
   outcome 1 |    0.222     0.000 
   outcome 2 |    0.496     0.000 
   outcome 3 |   -0.626     0.000 
   outcome 4 |   -0.092     0.000 

## 1.13 Tables of predicted probabilities

When there are substantively important categorical predictors in the model, examining tables of predicted probabilities over values of these variables can be an effective way to interpret the results. In this example, we use **``mtable``** to look at predictions over the values of the year of the survey, which correspond to 1980, 1996, and 2012:

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
mtable, at(year=(1 2 3)) atmeans norownum
```

    Expression: Pr(class), predict(outcome())
    
        year     lower   working    middle     upper
    ------------------------------------------------
           1     0.049     0.473     0.462     0.016
           2     0.053     0.489     0.443     0.015
           3     0.078     0.565     0.347     0.010
    
    Specified values of covariates
    
               |        1.        1.        2.        3.                    
               |   female     white      educ      educ       age    income
     ----------+-----------------------------------------------------------
       Current |     .549      .814      .582      .241      45.2      68.1

The **``atmeans``** option holds other variables at their means in the estimation sample. We conclude the following:

>Changing only the year of the survey, and with income measured in 2012 dollars for all survey years, the probability of a respondent identifying as working class increased from 0.47 in 1980 to 0.57 in 2012, while the probability of identifying as middle class declined from 0.46 to 0.35.

To obtain confidence intervals for the predictions, we use the option **``stat(ci)``**, which could be abbreviated simply as **``ci``**:

```
mtable, at(year=(1 2 3)) atmeans stat(ci)
```

    Expression: Pr(class), predict(outcome())
    
               |     year     lower   working    middle     upper
     ----------+-------------------------------------------------
         Pr(y) |        1     0.049     0.473     0.462     0.016
            ll |        1     0.042     0.447     0.433     0.013
            ul |        1     0.056     0.499     0.491     0.020
         Pr(y) |        2     0.053     0.489     0.443     0.015
            ll |        2     0.046     0.468     0.421     0.012
            ul |        2     0.059     0.510     0.466     0.018
         Pr(y) |        3     0.078     0.565     0.347     0.010
            ll |        3     0.068     0.543     0.321     0.008
            ul |        3     0.088     0.587     0.372     0.012
    
    Specified values of covariates
    
               |        1.        1.        2.        3.                    
               |   female     white      educ      educ       age    income
     ----------+-----------------------------------------------------------
       Current |     .549      .814      .582      .241      45.2      68.1

The lower and upper bounds of the intervals print on separate rows beneath each prediction. For example:

>Holding independent variables at their sample means, respondents in 2012 had a 0.078 probability of identifying as lower class (95% CI: [0.068, 0.088]).

We might also want to generate tables for a combination of categorical independent variables. For example, how does class affiliation vary by race for the three years of our survey?

>While we are considering the probabilities implied by having year and white in the model as separate independent variables, we could also fit a model in which the interaction term **``i.year#i.white``** is included.

```
mtable, at(year=(1 2 3) white=(0 1)) atmeans norownum
```

    Expression: Pr(class), predict(outcome())
    
       white      year     lower   working    middle     upper
    ----------------------------------------------------------
           0         1     0.059     0.511     0.417     0.013
           0         2     0.063     0.526     0.399     0.012
           0         3     0.093     0.593     0.306     0.008
           1         1     0.047     0.464     0.472     0.017
           1         2     0.051     0.480     0.454     0.016
           1         3     0.075     0.558     0.356     0.010
    
    Specified values of covariates
    
               |        1.        2.        3.                    
               |   female      educ      educ       age    income
     ----------+-------------------------------------------------
       Current |     .549      .582      .241      45.2      68.1

The predictions vary by year within a given value of white. The way in which variables vary is determined by the order in which the variables are specified with **``ologit``**, not by the order of variables within the **``at()``** statement. The table might be clearer if predictions were arranged to vary by white within each value of year (in practice, we often have to try it both ways before deciding which is clearer for the purpose at hand). We can refit the **``ologit``** model with year listed before white, or we can specify the values of year within three separate **``at()``** statements:

```
mtable, atmeans norownum ///
    at(year=1 white=(0 1)) /// 1980
    at(year=2 white=(0 1)) /// 1996
    at(year=3 white=(0 1)) // 2012
```

    Expression: Pr(class), predict(outcome())
    
       white      year     lower   working    middle     upper
    ----------------------------------------------------------
           0         1     0.059     0.511     0.417     0.013
           1         1     0.047     0.464     0.472     0.017
           0         2     0.063     0.526     0.399     0.012
           1         2     0.051     0.480     0.454     0.016
           0         3     0.093     0.593     0.306     0.008
           1         3     0.075     0.558     0.356     0.010
    
    Specified values of covariates
    
               |        1.        2.        3.                    
               |   female      educ      educ       age    income
     ----------+-------------------------------------------------
       Current |     .549      .582      .241      45.2      68.1

The results are exactly the same as before with only the rows rearranged.

In these results, the specified values of the covariates are the same for all predictions, namely, their global means. A different possibility is that we want the values of other variables to vary depending on a person’s race and the year of the survey. For example, suppose that we want to compare whites and nonwhites for different survey years, holding all other variables constant at their local means within each race-year group. In other words, instead of looking at predictions when all independent variables except race and year are held to the same values, we compute predictions when the values of the other independent variables vary according to the means for whites and for blacks in different years. To do this, we specify the **``over()``** option:

```
mtable, over(year white) atmeans
```

    Expression: Pr(class), predict(outcome())
    
               |        1.                            2.        3.                                        
               |   female     white      year      educ      educ       age    
     ----------+--------------------------------------------------------------
             1 |     .659         0         1      .447      .136      42.6    
             2 |     .539         1         1      .553      .166      44.5   
             3 |     .617         0         2      .597      .199      40.1    
             4 |     .534         1         2      .609      .261      44.7    
             5 |     .576         0         3       .56      .234      44.1    
             6 |     .538         1         3      .581       .31      48.9    
    
               |                    
               |    lower   working    middle    upper   income  
     ----------+----------------------------------------------
             1 |    0.093     0.592    0.307     0.008    46.5    
             2 |    0.054     0.493    0.439     0.015    67.7    
             3 |    0.085     0.579    0.327     0.009    50.5    
             4 |    0.048     0.467    0.468     0.017    70.5     
             5 |    0.111     0.615    0.266     0.007    54.3    
             6 |    0.058     0.507    0.422     0.014    77.8    

The means of the independent variables are included in the table and are different for each row. For example, the values of female, white, year, educ, age, and income in row 1 are the means for the subsample that is black and responded to the survey in 1980:

```
sum female i.educ age income if white==0 & year==1, sep(9)
```
        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
          female |        132    .6590909    .4758206          0          1
                 |
            educ |
    not hs grad  |        132    .4166667    .4948848          0          1
        hs only  |        132    .4469697    .4990739          0          1
        college  |        132    .1363636    .3444816          0          1
                 |
             age |        132    42.55303     16.8257         18         83
          income |        132    46.48708    55.15685    1.57795   267.8147


Returning to the output from **``mtable``**, if we look at variable 3 (educ), for example, the difference between row 1 and row 2 shows that a higher proportion of white respondents had college degrees in 1980 (0.166) than did black respondents (0.136). The differences between rows 1 and 3 and between rows 2 and 4, on the other hand, reflect that the proportion of respondents with a college degree increased between 1980 and 1996.

**When there are many regressors in the table, it might be easier to see the changing predictions by listing the value of the regressors (that is, the **``at()``** variables) on the right by using the **``atright``** option.**

```
mtable, over(year white) atmeans atright
```

    Expression: Pr(class), predict(outcome())
    
               |                                                1.    
               |    lower   working    middle     upper    female     
     ----------+----------------------------------------------------------
             1 |    0.093     0.592     0.307     0.008      .659     
             2 |    0.054     0.493     0.439     0.015      .539     
             3 |    0.085     0.579     0.327     0.009      .617     
             4 |    0.048     0.467     0.468     0.017      .534     
             5 |    0.111     0.615     0.266     0.007      .576     
             6 |    0.058     0.507     0.422     0.014      .538     
  
               |                        2.        3.                    
               |white      year      educ      educ       age    income
     ----------+----------------------------------------------------------
             1 |    0         1      .447      .136      42.6      46.5
             2 |    1         1      .553      .166      44.5      67.7
             3 |    1         1      .553      .166      40.1      50.5
             4 |    0         2      .597      .199      44.7      70.5
             5 |    1         2      .609      .261      44.1      54.3
             6 |    1         3      .581       .31      48.9      77.8
    
    Specified values where .n indicates no values specified with at()
    
               |  No at()
     ----------+---------
       Current |       .n


Using **``over()``** in this way yields predictions that reflect the effects of both race and year and also compositional differences over race and year in the means of the other variables. In this respect, the predictions are not as simple to interpret as the examples above in which the values of other characteristics were the same regardless of year and race. Comparing the predictions we just made with those we made holding the independent variables to the same values for everyone, we can see the probabilities vary more when the means vary over groups. Substantively, this indicates that the differences in the population composition by race and year result in differences in predictions that are larger than they would be if we assumed that population characteristics were constant across race and time.

## 1.14 Plotting predicted probabilities

Plotting predicted probabilities for each outcome can also be useful for the ORM. These plots illustrate how predicted probabilities change as a continuous, independent variable changes. With the BRM, we showed two approaches for making plots: directly with **``marginsplot``** or in two steps with **``margins``** and **``graph``**. **Plotting multiple outcomes, however, can only be done using the latter technique because **``marginsplot``** is limited to plotting a single outcome.**

To illustrate graphing predictions, we consider how the probability of class affiliation changes as household income changes, holding all other variables at their sample means. Of course, the plot could also be constructed for other sets of characteristics.The option **``at(inc=0(25)250)``** tells **``mgen``** to generate predictions as income changes from 0 to 250 in increments of 25, leading to 11 sets of predictions. The option **``atmeans``** holds other variables to their means. We use **``stub(CL_)``** to add CL_ (indicating predictions for class) to the names of variables generated by **``mgen``**:

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
mgen, at(income=(0(25)250)) stub(CL_) atmeans
```

    Predictions from: margins, at(income=(0(25)250)) atmeans predict(outcome())
    
    Variable   Obs Unique      Mean       Min       Max  Label
    --------------------------------------------------------------------------------------
    CL_pr1      11     11  .0439624  .0074601  .1207294  pr(y=lower) from margins
    CL_ll1      11     11  .0385464  .0057745  .1067216  95% lower limit
    CL_ul1      11     11  .0493784  .0091458  .1347373  95% upper limit
    CL_income   11     11       125         0       250  household income
    CL_Cpr1     11     11  .0439624  .0074601  .1207294  pr(y<=lower)
    CL_pr2      11     11   .377087  .1301718  .6238775  pr(y=working) from margins
    CL_ll2      11     11  .3565982  .1081524  .6070259  95% lower limit
    CL_ul2      11     11  .3975758  .1521912  .6407292  95% upper limit
    CL_Cpr2     11     11  .4210494   .137632   .744607  pr(y<=working)
    CL_pr3      11     11  .5424338  .2492696  .7612023  pr(y=middle) from margins
    CL_ll3      11     11  .5212693  .2296227  .7417022  95% lower limit
    CL_ul3      11     11  .5635983  .2689165  .7807024  95% upper limit
    CL_Cpr3     11     11  .9634833  .8988342  .9938766  pr(y<=middle)
    CL_pr4      11     11  .0365167  .0061234  .1011657  pr(y=upper) from margins
    CL_ll4      11     11   .030147  .0047639  .0828273  95% lower limit
    CL_ul4      11     11  .0428865  .0074829  .1195042  95% upper limit
    CL_Cpr4     11     10         1         1         1  pr(y<=upper)
    -------------------------------------------------------------------------------------
    
    Specified values of covariates
    
            1.         1.         2.         3.         2.         3.           
       female      white       year       year       educ       educ        age 
    ---------------------------------------------------------------------------
     .5491103   .8140569   .4510676   .3099644   .5818505   .2414591   45.15712 

Each variable has 11 observations corresponding to different values of income. Variables containing predicted probabilities are stored in variables named **``CL_pr#``**. For example, **``CL_pr2``** is the predicted probability of identifying as working class, the second category of our outcome. Variables containing cumulative probabilities—that is, the probability of observing a given category or lower—are stored as variables **``CL_Cpr#``**. For example, **``CL_Cpr2``** is the predicted probability of a respondent identifying as either lower class or working class.

Although **``mgen``** assigns variable labels to the variables it generates, we can change these to improve the look of the plot that we are creating. Specifically, we use

```
label var CL_pr1 "Lower"
label var CL_pr2 "Working"
label var CL_pr3 "Middle"
label var CL_pr4 "Upper"
label var CL_Cpr1 "Lower"
label var CL_Cpr2 "Lower/Working"
label var CL_Cpr3 "Lower/Working/Middle"
```

Next, we plot the probabilities of individual outcomes by using **``graph``**. Here we plot the four probabilities against values of income.

```
graph twoway connected CL_pr1 CL_pr2 CL_pr3 CL_pr4 CL_income, ///
    title("Panel A: Predicted Probabilities") ///
    xtitle("Household income (2012 dollars)") ///
    xlabel(0(50)250) ylabel(0(.25)1, grid gmin gmax) ///
    ytitle("") name(tmpprob, replace)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.12.png" style="width:580px;height:380px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

**Standard options for **``graph``** are used to specify the axes and labels. The **``name(tmpprob, replace)``** option saves the graph with the name tmpprob so that we can combine it with our next graph, which plots the cumulative probabilities.** The values on the y-axis of each of the four lines indicate the predicted probabilities of each category when income equals the value on the x-axis and other variables are held at their means. At all values of income, these probabilities sum to 1. We will wait to discuss what it is showing substantively until after we complete the graph with cumulative probabilities.

A graph of cumulative probabilities uses lines to indicate the probability that y < # rather than y = #. To create this graph, we use the command

当我们谈论"probabilities"（概率）时，我们讨论的是某个事件发生的可能性大小。这个可能性通常用一个数值来表示，它介于0和1之间。当概率接近0时，表示事件几乎不可能发生；当概率接近1时，表示事件几乎肯定会发生；而当概率介于0和1之间的其他值时，表示事件发生的可能性在这两个极端之间。

举个例子，假设我们抛一枚公平的硬币，事件A是出现正面，事件B是出现反面。在这种情况下，事件A和事件B的概率都是0.5，因为硬币是公平的，正反面出现的可能性是相等的。

而"cumulative probabilities"（累积概率）则是指在给定的概率分布下，某一事件在或之前发生的概率总和。这通常用于描述随机变量的行为。在连续概率分布中，我们使用累积分布函数（CDF）来表示累积概率。CDF给出了随机变量小于或等于某个特定值的概率。

继续以上面的硬币抛掷的例子，假设我们对硬币进行了多次抛掷，并记录下每次抛掷结果为正面的次数。我们可以用累积概率来描述在这些抛掷中出现不同次数正面的概率。例如，累积概率可以告诉我们，在前10次抛掷中，出现正面次数小于或等于5次的概率是多少。

```
graph twoway connected CL_Cpr1 CL_Cpr2 CL_Cpr3 CL_income, ///
    title("Panel B: Cumulative Probabilities") ///
    xtitle("Household income (2012 dollars)") ///
    xlabel(0(50)250) ylabel(0(.25)1, grid gmin gmax) ///
    ytitle("") name(tmpcprob, replace)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.13.png" style="width:580px;height:380px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

```
graph combine tmpprob tmpcprob, col(1) iscale(*.9) imargin(small) ///
    ysize(4.6) xsize(3.287) ///
    caption("Other variables held at their means")
```

This leads to figure 7.2. Panel A plots the predicted probabilities for each outcome and shows that the probabilities of working class and middle class are larger than the probabilities of lower class and upper class. As income increases, the probability of a respondent identifying as lower class or working class decreases, while the probability of identifying as middle class or upper class increases. Panel B plots the cumulative probabilities. Both panels present the same information. In applications, you should use the graph that you find most effective.

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.14.png" style="width:610px;height:780px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

A different way of plotting cumulative probabilities is to use shading instead of lines, which looks like this:

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.15.png" style="width:710px;height:480px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

To create this graph, we use a twoway area plot type (see [G-2] twoway area). In our prior graph, CL_Cpr^ designated the height on the y axis at which a line should be drawn. In a **twoway area plot**, the value of CL_Cpr# indicates that the graph should be shaded from that value to the bottom of the graph. We used the following commands:

```
capture drop one
gen one = 1
label variable one "Upper"
label variable CL_Cpr3 "Middle"
label variable CL_Cpr2 "Working"
label variable CL_Cpr1 "Lower"
graph twoway ///
    (area one CL_income, fcolor(gs15)) ///
    (area CL_Cpr3 CL_income, fcolor(gs11)) ///
    (area CL_Cpr2 CL_income, fcolor(gs7)) ///
    (area CL_Cpr1 CL_income, fcolor(gs3)) ///
    , ///
    xtitle("Household income (2012 dollars)") ytitle("Probability") ///
    xlabel(0(50)250) ylabel(0(.25)1, grid gmin gmax)
```

First, we generate the variable **``one``**, whose values are all 1. An area plot using this variable is simply a solid block from the top of the graph to the bottom, which we use to depict the probability of the highest category ("Upper"). Accordingly, we label this variable with the name of the highest category. Next, we label the variables for the other cumulative probabilities according to the highest category each represents. Then, using the **``graph twoway``** command, we draw four area plots, each on top of the other to make a single graph. The first plot covers the entire plot region (everything below the value of variable **``one``**, which is always equal to 1), which we shade using **``fcolor(gs15)``**.The other area plots use the three cumulative probability variables and are shaded using progressively darker grays. The shading allows us to easily see how identification as middle class expands as household income increases. We can also quickly see how large the probabilities for the middle categories are relative to the extremes.

## 1.15 Probability plots and marginal effects

**Having considered various methods of interpretation, we now show the link between marginal effects and plots of predicted probabilities to hopefully provide you with new insights on the nature of ordinal models.** The following graph, based on section 7.14, shows how the probabilities of class affiliation change with income, holding all other variables at their means. The mean of income is indicated with a dashed, vertical line:

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.16.png" style="width:580px;height:380px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The slope of each probability curve evaluated at the mean of income, indicated by where the probability curves intersect the vertical line, is the marginal change in the probability of a given class affiliation with respect to income, with all variables held at their means. We can compute these changes by using **``mchange, atmeans``** to estimate MEMs:

```
ologit class i.female i.white i.year i.educ c.age##c.age income, nolog
mchange income, atmeans amount(marginal) dec(4)
```

    ologit: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
    income       |                                           
        Marginal |   -0.0006    -0.0022     0.0027     0.0002 
         p-value |    0.0000     0.0000     0.0000     0.0000 
    
    Predictions at base value
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
      Pr(y|base) |    0.0586     0.5107     0.4173     0.0134 
    
    Base values of regressors
    
                 |         1.         1.         2.         3.         2.         3.                      
                 |    female      white       year       year       educ       educ        age     income 
    -------------+---------------------------------------------------------------------------------------
              at |     .5491      .8141      .4511        .31      .5819      .2415      45.16      68.08 
    
    1: Estimates with margins option atmeans.

The marginal changes are in row Marginal, with the significance level for the change listed in row p-value. These changes correspond to the probability curves at the point of intersection with the vertical line. For the slope for middle class, shown with squares, is 0.0027.

**The magnitude of the marginal changes would differ if we computed the marginal effects at different values of the independent variables.** For example, we can compute the effects with income equal to $250,000$, with all other variables still kept at their means:

```
mchange income, at(income=250) atmeans amount(marginal) dec(4)
```

    ologit: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
    income       |                                           
        Marginal |   -0.0001    -0.0013     0.0003     0.0011 
         p-value |    0.0000     0.0000     0.0378     0.0000 
    
    Predictions at base value
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
      Pr(y|base) |    0.0075     0.1302     0.7612     0.1012 
    
    Base values of regressors
    
                 |         1.         1.         2.         3.         2.         3.                      
                 |    female      white       year       year       educ       educ        age     income 
    -------------+---------------------------------------------------------------------------------------
              at |     .5491      .8141      .4511        .31      .5819      .2415      45.16        250 
    
    1: Estimates with margins option atmeans.
    
The marginal change for the probability of identifying with the middle class is much smaller, corresponding to the leveling off of the curve (shown with ♦’s) on the right side of the graph.

In this example, the signs of the marginal effects for each outcome are the same throughout the range of income. This, however, does not need to be true. In the ORM, not only does the magnitude of the effect change as the values of the independent variables change, but even the sign can change. That is to say, the effect of a variable can be positive at one point and can be negative at other points, even if we have not included polynomial terms or interactions in the model. In a model without interaction or polynomials for a given independent variable, the sign of that variable’s regression coefficient will always be the same as the direction of changes in the probability of the highest outcome category as the independent variable increases.

In our example of subjective social class, because the coefficient for income is positive, increases in income will always increase the probability of identifying as upper class. Conversely, the change in the probability of the lowest category will be in the opposite direction as the regression coefficient. Thus increases in income always decrease the probability of identifying as lower class. The middle categories are more complicated. In terms of the latent variable model described in section 7.1.1, as income increases, some people shift from lower class to working class, while other people shift from working class to middle class. The specific implication for the probability of identifying as working class depends on whether the proportion entering the category from below is bigger or smaller than the proportion exiting from above. As a result, the sign of the predicted change in middle categories can change when computed at different values of income. Our running example does not provide a good illustration of this (for reasons explained below), so we use an example from chapter 8. Party affiliation is coded as strong Democrat; Democrat, Independent, or Republican; and strong Republican. We fit an OLM using age, income, race, gender, and education as predictors:

```
use partyid4, clear
ologit partystrong age income i.black i.female i.educ, nolog
```

    Ordered logistic regression                             Number of obs =  1,382
                                                            LR chi2(6)    = 173.85
                                                            Prob > chi2   = 0.0000
    Log likelihood = -1064.4742                             Pseudo R2     = 0.0755
    
    ------------------------------------------------------------------------------
     partystrong | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
             age |      -0.01       0.00    -2.19   0.028        -0.02       -0.00
          income |       0.01       0.00     3.74   0.000         0.00        0.01
                 |
           black |
            yes  |      -1.44       0.17    -8.65   0.000        -1.77       -1.12
                 |
          female |
            yes  |      -0.11       0.12    -0.93   0.350        -0.34        0.12
                 |
            educ |
        hs only  |       0.41       0.18     2.36   0.018         0.07        0.76
        college  |       0.90       0.22     4.18   0.000         0.48        1.32
    -------------+----------------------------------------------------------------
           /cut1 |      -1.41       0.27                         -1.95       -0.88
           /cut2 |       2.36       0.28                          1.81        2.90
    ------------------------------------------------------------------------------

Notice that the coefficient for income is positive. Next, we compute predictions as income increases from $0$ to $200,000$:

```
mgen, atmeans at(income=(0(20)200)) stub(olm) replace
```

    Predictions from: margins, atmeans at(income=(0(20)200)) predict(outcome())
    
    Variable   Obs Unique      Mean       Min       Max  Label
    -----------------------------------------------------------------------------------
    olmpr1      11     11  .1148384  .0444239  .2207461  pr(y=StrDem) from margins
    olmll1      11     11  .0838875  .0106582  .1826952  95% lower limit
    olmul1      11     11  .1457892  .0781895   .258797  95% upper limit
    olmincome   11     11       100         0       200  Income in $1,000s
    olmCpr1     11     11  .1148384  .0444239  .2207461  pr(y<=StrDem)
    olmpr2      11     11  .7031837   .623989  .7357607  pr(y=Middle) from margins
    olmll2      11     11  .6503714  .4858261   .710362  95% lower limit
    olmul2      11     11  .7559959  .7350006  .7621519  95% upper limit
    olmCpr2     11     11   .818022  .6684129  .9247167  pr(y<=Middle)
    olmpr3      11     11   .181978  .0752833  .3315871  pr(y=StrRep) from margins
    olmll3      11     11  .1166743  .0571399  .1613071  95% lower limit
    olmul3      11     11  .2472816  .0934267  .5018671  95% upper limit
    olmCpr3     11      8         1         1         1  pr(y<=StrRep)
    -----------------------------------------------------------------------------------
    
    Specified values of covariates
    
                       1.         1.         2.         3.
          age      black     female       educ       educ 
    -----------------------------------------------------
     45.94645   .1374819   .4934877   .5803184   .2590449 

```
label var olmpr1 "Strong Democrat"
label var olmpr2 "Middle"
label var olmpr3 "Strong Republican"
```

Plotting these predictions produces the following graph:

```
graph twoway connected olmpr1 olmpr2 olmpr3 olmincome, ///
    title("Predicted Probabilities") ///
    xtitle("Income in $1000's") ///
    xlabel(0(50)200) ylabel(0(.25)1, grid gmin gmax) ///
    ytitle("") name(tmpprob, replace)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.17.png" style="width:580px;height:380px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

As income increases, the probability of being a strong Republican increases steadily, while the probability of being a strong Democrat decreases. The changes in the probability of being "in the middle" are more complex. As income increases from $0$ to about $50,000$, the probability increases, and then it decreases till $200,000$. That is, the marginal effect of income on being politically in the middle is both positive and negative depending on where it is evaluated. We can see this by using **``mchange``** evaluated at the levels of income:

```
* discrete change at income of $0
mchange income, at(income=0) atmeans amount(sd) stat(change) brief
```

    ologit: Changes in Pr(y) | Number of obs = 1382
    
    Expression: Pr(partystrong), predict(outcome())
    
                 |    StrDem     Middle     StrRep 
    -------------+--------------------------------
    income       |                                
             +SD |    -0.040      0.021      0.019 

```
* discrete change at income of $60,000
mchange income, at(income=68) atmeans amount(sd) stat(change) brief
```

    ologit: Changes in Pr(y) | Number of obs = 1382
    
    Expression: Pr(partystrong), predict(outcome())
    
                 |    StrDem     Middle     StrRep 
    -------------+--------------------------------
    income       |                                
             +SD |    -0.026     -0.005      0.031 

```
* discrete change at income of $200,000
mchange income, at(income=200) atmeans amount(sd) stat(change) brief
```

    ologit: Changes in Pr(y) | Number of obs = 1382
    
    Expression: Pr(partystrong), predict(outcome())
    
                 |    StrDem     Middle     StrRep 
    -------------+--------------------------------
    income       |                                
             +SD |    -0.010     -0.048      0.058 

The changes in the probability of outcome 1, being a strong Democrat, are always negative and get smaller as the probability approaches 0. Changes in the probability of outcome 3, being a strong Republican, steadily increase as income increases. For the middle category, the change is positive, then 0, and then negative.

This pattern of change in probabilities must hold for any ORM. Indeed, Anderson (1984) made this a defining characteristic of an ordinal model; see Long (Forthcoming) for further discussion of this property.

When an independent variable changes over an extended range (technically, from negative infinity to positive infinity), the plot of probabilities must have a pattern similar to the following graph that extends the range of income from our example above:


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.18.png" style="width:580px;height:380px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

When an independent variable changes over an extended range (technically, from negative infinity to positive infinity), the plot of probabilities must have a pattern similar to the following graph that extends the range of income from our example above:

The height of the bell-shaped curve for the middle category depends on the distance between thresholds, which in turn depends on the relative size of the outcome categories. The observed range for the independent variable—income, in this case—might fall anywhere within this graph. For example, if our sample included only those cases with an income about $68,000$, corresponding to the peak of the probability curve for the middle category, our graph would show that the probability of being politically in the middle always decreases as income increases. Indeed, we had to change our example for this section because the upper- and lower-class categories in our example of subjective social class were so small.

If you have more categories, the probability curves will be ordered from left to right as illustrated in this graph:


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.3.2.19.png" style="width:580px;height:380px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

## 1.16 Less common models for ordinal outcomes

Stata can also fit several less commonly used models for ordinal outcomes. In concluding this chapter, we describe these models briefly and note their commands for estimation. Long (Forthcoming) provides further details. SPost commands do not work with all these models, but our **`m*`** commands do work with the estimation commands that support **`margins`**.

### 1.16.1 The stereotype logistic model

The stereotype logistic model, also referred to as the stereotype ORM, was proposed by Anderson (1984) in response to the restrictive assumption of parallel regressions in the ORM. The stereotype logistic model is a compromise between allowing the coefficients for each independent variable to vary by outcome category (as is the case with the multinomial logit model, considered in the next chapter) and restricting the coefficients to be identical across all outcomes (as was the case with the ordered logit model). The stereotype logistic model can be fit in Stata by using the **`slogit`** command (see [R] slogit). The one-dimensional version of the model is defined as

这段话是在讨论一种名为"stereotype logistic model"（刻板逻辑模型）的统计模型。它是对传统的有序Logit模型的改进，旨在克服其对于所有结果都采用相同系数的限制，同时又不像多项式Logit模型那样允许每个自变量的系数随着结果类别的变化而变化。该模型可以在Stata中使用**`slogit`**命令来拟合。

$$\ln\frac{\Pr\left(y=q\mid\mathbf{x}\right)}{\Pr\left(y=r\mid\mathbf{x}\right)}=\left(\theta_{q}-\theta_{r}\right)-\left(\phi_{q}-\phi_{r}\right)\left(\mathbf{x}\boldsymbol{\beta}\right)$$

where $\beta$ is a vector of coefficients associated with the independent variables, the $\alpha$'s are intercepts, and the $\gamma$'s are scale factors that mediate the effects of the x's. This one-dimensional model is ordinal as defined in section 7.15 and often produces very similar predictions to the ORM. When additional dimensions are added, it is no longer an ordinal model. Indeed, with enough dimensions, it is equivalent to the multinomial logit model. Accordingly, we postpone further discussion until chapter 8.

### 1.16.2 The generalized ordered logit model

The parallel regression assumption results from assuming the same coefficient vector ($ \beta $) in the $ J - 1 $ logit equations

$$\ln\Omega_{\leq m|>m}\left(\mathbf{x}\right)=\tau_{m}-\mathbf{x}\boldsymbol{\beta}$$

where $\Omega_{\leq m|>m}\left(\mathbf{x}\right)=\mathrm{Pr}\left(y\leq m\mid\mathbf{x}\right)/\mathrm{Pr}\left(y>m\mid\mathbf{x}\right)$ The generalized ordered logit model allows $ \beta $ to differ for each of the $ J - 1 $ comparisons. That is 

$$\ln\Omega_{\leq m|>m}\left(\mathrm{x}\right)=\tau_m-\mathbf{x}\beta_m\quad\mathrm{for~}j=1\mathrm{~to~}J-1$$

where predicted probabilities are computed as

$$\Pr\left(y=1\mid\mathbf{x}\right)=\frac{\exp\left(\tau_1-\mathbf{x\beta}_1\right)}{1+\exp\left(\tau_1-\mathbf{x\beta}_1\right)}$$

$$\Pr\left(y=j\mid\mathbf{x}\right)=\frac{\exp\left(\tau_j-\mathbf{x}\beta_j\right)}{1+\exp\left(\tau_j-\mathbf{x}\boldsymbol{\beta_j}\right)}-\frac{\exp\left(\tau_{j-1}-\mathbf{x}\beta_{j-1}\right)}{1+\exp\left(\tau_{j-1}-\mathbf{x}\boldsymbol{\beta}_{j-1}\right)}\quad\mathrm{for~}j=2\mathrm{~to~}J-1$$

$$\Pr\left(y=J\mid\mathbf{x}\right)=1-\frac{\exp\left(\tau_{J-1}-\mathbf{x\beta_{J-1}}\right)}{1+\exp\left(\tau_{J-1}-\mathbf{x\beta}_{J-1}\right)}$$

No formal constraint precludes negative predicted probabilities. Discussions of this model can be found in Clogg and Shihadeh (1994, 146-147), Fahrmeir and Tutz (1994, 91), and McCullagh and Nelder (1989, 155). A critical view of the model can be found in Greene and Hensher (2010, 189-192), who highlight that the model can predict negative "probabilities” and that it cannot be formulated in terms of a continuous latent dependent variable. Further, as noted by Long (Forthcoming), the generalized ordered logit model is not an ordinal regression model because, like the multinomial logit model, it does not necessarily make predictions that maintain the ordinality of the outcome.


1. **Parallel Regression Assumption**:
   这个假设源自于多项logit模型，其中假设不同类别之间的回归系数是相同的。具体地，在你提供的公式中，$\boldsymbol{\beta}$表示回归系数向量。这个假设认为在$J - 1$个logit方程中，这个系数向量是相同的。例如，如果我们用一个多项logit模型来预测学生通过不同课程的可能性，这个假设就是认为不同课程对学生通过考试的影响是相似的。

2. **Generalized Ordered Logit Model**:
   广义有序logit模型允许不同类别之间的回归系数不同。在你提供的公式中，$\beta_m$表示第$m$个比较的系数向量。这意味着在预测不同的类别之间，可以使用不同的回归系数。例如，假设我们要预测一家餐厅的顾客评价，有“不满意”、“一般”和“满意”三个等级，而这些等级之间的影响可能是不同的，比如食物质量对“满意”和“不满意”的影响可能是不同的。

3. **Predicted Probabilities**:
   预测概率是在给定自变量的情况下，某个类别的发生概率。在你的公式中，$\Pr\left(y=j\mid\mathbf{x}\right)$表示在给定自变量$\mathbf{x}$的情况下，因变量$y$取值为$j$的概率。这个概率通过使用模型中的参数$\tau_j$和系数向量$\beta_j$进行计算。举个例子，假设我们要预测一名学生通过一门课程的可能性，$\Pr\left(y=1\mid\mathbf{x}\right)$表示学生通过课程的概率，$\Pr\left(y=2\mid\mathbf{x}\right)$表示学生可能通过但也可能不通过的概率，$\Pr\left(y=3\mid\mathbf{x}\right)$表示学生不通过课程的概率。

4. **Negative Predicted Probabilities**:
   负的预测概率是指在模型中计算出的某个类别的发生概率为负数。这在实际应用中是不合理的，因为概率应该在0到1之间。如果模型产生负的预测概率，可能表示模型存在问题，需要进行调整或者修正。例如，在学生考试成绩预测的例子中，负的预测概率可能表示模型对学生考试成绩的预测不准确，需要重新调整模型参数或者考虑其他因素。



首先，我们来看你给出的第一个公式：
$$
\ln\Omega_{\leq m|>m}\left(\mathbf{x}\right)=\tau_{m}-\mathbf{x}\boldsymbol{\beta}
$$

这个公式是广义有序logit模型的一个基本方程。让我们来分解它：

- $\ln\Omega_{\leq m|>m}\left(\mathbf{x}\right)$表示的是两个概率的比值的自然对数。这个比值是$y\leq m$的概率与$y>m$的概率之比，其中$y$是因变量，$m$是一个类别标记。
- $\tau_{m}$是一个阈值参数，用来划分不同的类别。对于不同的$m$，有不同的阈值。
- $\mathbf{x}$是自变量的向量。
- $\boldsymbol{\beta}$是系数向量，表示自变量对因变量的影响。

这个方程的含义是：在给定自变量$\mathbf{x}$的情况下，$y$属于不同类别的概率之比的对数，等于一个类别的阈值参数$\tau_{m}$减去自变量$\mathbf{x}$与系数向量$\boldsymbol{\beta}$的内积。

接下来，我们来看第二个公式：
$$
\Pr\left(y=1\mid\mathbf{x}\right)=\frac{\exp\left(\tau_1-\mathbf{x\beta}_1\right)}{1+\exp\left(\tau_1-\mathbf{x\beta}_1\right)}
$$

这个公式是预测$y$等于第一个类别的概率的方程。让我们来解释它：

- $\Pr\left(y=1\mid\mathbf{x}\right)$表示的是在给定自变量$\mathbf{x}$的情况下，$y$等于第一个类别的概率。
- $\exp$是指数函数，用来计算参数$\tau_1-\mathbf{x\beta}_1$的指数。
- 分母$1+\exp\left(\tau_1-\mathbf{x\beta}_1\right)$确保了概率的范围在0到1之间。

这个方程的含义是：在给定自变量$\mathbf{x}$的情况下，$y$等于第一个类别的概率，是阈值参数$\tau_1$和自变量$\mathbf{x}$与系数向量$\boldsymbol{\beta}_1$的内积的指数函数，除以1加上这个指数函数。


第三个公式:
$$
\Pr\left(y=j\mid\mathbf{x}\right)=\frac{\exp\left(\tau_j-\mathbf{x}\beta_j\right)}{1+\exp\left(\tau_j-\mathbf{x}\boldsymbol{\beta_j}\right)}-\frac{\exp\left(\tau_{j-1}-\mathbf{x}\beta_{j-1}\right)}{1+\exp\left(\tau_{j-1}-\mathbf{x}\boldsymbol{\beta}_{j-1}\right)}\quad\mathrm{for~}j=2\mathrm{~to~}J-1
$$

这个公式用于计算$y$等于中间类别$j$的概率。让我们详细解释一下：

- $\Pr\left(y=j\mid\mathbf{x}\right)$表示的是在给定自变量$\mathbf{x}$的情况下，$y$等于中间类别$j$的概率。
- 第一项$\frac{\exp\left(\tau_j-\mathbf{x}\beta_j\right)}{1+\exp\left(\tau_j-\mathbf{x}\boldsymbol{\beta_j}\right)}$是阈值参数$\tau_j$和自变量$\mathbf{x}$与系数向量$\boldsymbol{\beta}_j$的内积的指数函数，除以1加上这个指数函数。
- 第二项$\frac{\exp\left(\tau_{j-1}-\mathbf{x}\beta_{j-1}\right)}{1+\exp\left(\tau_{j-1}-\mathbf{x}\boldsymbol{\beta}_{j-1}\right)}$类似地计算了$j$的前一个类别的概率。

这个方程的含义是：在给定自变量$\mathbf{x}$的情况下，$y$等于中间类别$j$的概率，是第$j$个阈值参数$\tau_j$和自变量$\mathbf{x}$与第$j$个系数向量$\boldsymbol{\beta_j}$的内积的指数函数，减去第$j-1$个阈值参数$\tau_{j-1}$和自变量$\mathbf{x}$与第$j-1$个系数向量$\boldsymbol{\beta}_{j-1}$的内积的指数函数。

第四个公式:
$$
\Pr\left(y=J\mid\mathbf{x}\right)=1-\frac{\exp\left(\tau_{J-1}-\mathbf{x\beta_{J-1}}\right)}{1+\exp\left(\tau_{J-1}-\mathbf{x\beta}_{J-1}\right)}
$$

这个公式用于计算$y$等于最后一个类别$J$的概率。让我们来看它的具体含义：

- $\Pr\left(y=J\mid\mathbf{x}\right)$表示的是在给定自变量$\mathbf{x}$的情况下，$y$等于最后一个类别$J$的概率。
- 这个概率通过$1$减去前面$J-1$个类别的概率得到。这是因为所有类别的概率之和应该等于1。

这个方程的含义是：在给定自变量$\mathbf{x}$的情况下，$y$等于最后一个类别$J$的概率，是1减去阈值参数$\tau_{J-1}$和自变量$\mathbf{x}$与系数向量$\boldsymbol{\beta}_{J-1}$的内积的指数函数，除以1加上这个指数函数。

让我们继续解释下一个部分。

5. **No Formal Constraint Precludes Negative Predicted Probabilities**:

   这部分提到了模型中可能出现负的预测概率的问题。让我来解释一下：

   - 正常情况下，预测概率应该在0到1之间，因为它们代表了事件发生的可能性。
   - 如果模型产生了负的预测概率，这可能是因为模型的参数设置或者数据不合适，导致了不合理的预测结果。
   - 这个问题需要引起注意，因为负的预测概率是没有实际意义的，而且可能会导致模型的准确性受到影响。

6. **Discussions of This Model**:

   这一部分提到了对该模型的讨论，以及一些参考文献。具体来说，它提到了一些支持和批评该模型的文献，并指出了该模型的一些局限性。例如，一些文献指出该模型可能产生负的概率，而且无法用连续的潜在变量来表示。

7. **Fitting the Model**:

   最后，这部分提到了如何在统计软件Stata中拟合该模型，以及一些需要注意的问题。例如，提到了一些命令无法处理的情况，以及如何处理因子变量等。


8. **The Model Can be Fit in Stata with the ``gologit2`` Command**:

   这一部分提到了如何在统计软件Stata中使用``gologit2``命令来拟合广义有序logit模型。让我来详细解释一下：

   - Stata是一种常用的统计软件，可以用于数据分析和建模。
   - ``gologit2``是Stata中用于拟合广义有序logit模型的一个命令。通过使用这个命令，可以方便地拟合模型并进行相关的统计分析。
   - 然而，该命令可能无法处理一些特殊情况，比如因子变量等，因此需要在使用时注意。

9. **Creating an Age-Squared Variable Explicitly**:

   这一部分提到了一个特定的问题，即在模型中无法直接使用因子变量符号来包含年龄的平方项。因此，需要显式地创建一个年龄的平方变量，以便在模型中使用。这一步骤需要在建模之前进行，以确保模型的正确性。

总的来说，这部分内容提供了一些关于如何在实际中应用广义有序logit模型的指导，包括在统计软件中拟合模型的方法以及一些需要注意的问题。希望这些解释能够帮助你更好地理解模型的应用和相关注意事项。如果还有其他问题或者需要进一步解释的地方，请随时提出。

The model can be fit in Stata with the **``gologit2``** command (Williams 2005). The command does not work with factor variables, so categorical variables in the model need to be constructed as a series of binary variables. Also, because we can no longer use factor-variable notation to include age-squared in the model, we need to create an age-squared variable explicitly:

```
use gssclass4, clear
gen year1996 = (year==2) if year < .
gen year2012 = (year==3) if year < .
gen educ_hs = (educ==2) if educ < .
gen educ_col = (educ==3) if educ < .
gen agesq = age*age if age < .
```

The specification of the dependent variable and independent variables is otherwise like the other estimation commands we consider. We estimate the parameters of the model by specifying the **``or``** option to obtain odds ratios:

```
gologit2 class female white year1996 year2012 educ_hs educ_col ///
    age agesq income, nolog or
```

    Generalized Ordered Logit Estimates                    Number of obs =   5,620
                                                           LR chi2(27)   = 1782.34
                                                           Prob > chi2   =  0.0000
    Log likelihood = -4852.0145                            Pseudo R2     =  0.1552
    
    ------------------------------------------------------------------------------
           class | Odds ratio   Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
    lower        |
          female |       0.87       0.10    -1.21   0.227         0.70        1.09
           white |       1.12       0.14     0.88   0.380         0.87        1.43
        year1996 |       0.86       0.14    -0.97   0.330         0.63        1.17
        year2012 |       0.47       0.07    -4.72   0.000         0.35        0.65
         educ_hs |       1.40       0.18     2.58   0.010         1.08        1.80
        educ_col |       4.32       1.13     5.58   0.000         2.58        7.21
             age |       0.93       0.02    -4.21   0.000         0.90        0.96
           agesq |       1.00       0.00     4.58   0.000         1.00        1.00
          income |       1.05       0.00    14.83   0.000         1.05        1.06
           _cons |       9.40       4.09     5.14   0.000         4.00       22.07
    -------------+----------------------------------------------------------------
    working      |
          female |       1.08       0.06     1.21   0.228         0.96        1.21
           white |       1.31       0.11     3.36   0.001         1.12        1.53
        year1996 |       0.94       0.07    -0.81   0.421         0.81        1.09
        year2012 |       0.71       0.06    -4.15   0.000         0.60        0.83
         educ_hs |       1.33       0.11     3.32   0.001         1.12        1.57
        educ_col |       4.74       0.50    14.66   0.000         3.85        5.84
             age |       0.95       0.01    -4.70   0.000         0.94        0.97
           agesq |       1.00       0.00     7.18   0.000         1.00        1.00
          income |       1.01       0.00    17.00   0.000         1.01        1.01
           _cons |       0.35       0.09    -4.22   0.000         0.22        0.57
    -------------+----------------------------------------------------------------
    middle       |
          female |       1.02       0.16     0.10   0.921         0.75        1.37
           white |       0.80       0.18    -1.03   0.302         0.52        1.23
        year1996 |       0.98       0.19    -0.12   0.903         0.66        1.44
        year2012 |       0.49       0.11    -3.09   0.002         0.31        0.77
         educ_hs |       0.65       0.17    -1.66   0.096         0.39        1.08
        educ_col |       1.78       0.48     2.15   0.032         1.05        3.01
             age |       0.99       0.03    -0.49   0.625         0.93        1.04
           agesq |       1.00       0.00     1.24   0.216         1.00        1.00
          income |       1.01       0.00    12.20   0.000         1.01        1.01
           _cons |       0.01       0.01    -6.09   0.000         0.00        0.06
    ------------------------------------------------------------------------------
    Note: _cons estimates baseline odds.
    
We have three sets of odds ratios labeled lower, working, and middle, compared with one set when we used **``ologit``**. The odds ratios indicate the factor change in odds of observing a value above the listed category versus observing values at or below the listed category. Accordingly, we can interpret the three coefficients for white as follows:

>Holding all other variables constant, white respondents have 1.12 times higher odds of identifying themselves as working, middle, or upper class than do nonwhite respondents. White respondents have 1.31 times higher odds of identifying themselves as middle or upper class than do nonwhite respondents. And, white respondents have 0.80 times lower odds of identifying themselves as upper class than do nonwhite respondents.

**The key difference between the generalized and the ordered logit models is that in the ordered logit model, the odds ratios described in these three sentences are constrained to be equal.** Based on the ordered logit model, we would replace 1.12, 1.31, and 0.80 in the paragraph above with the same value 1.27.

**``gologit2``** allows users to fit the model with some of the coefficients constrained to be equal, as in **``ologit``**, while others are allowed to vary. In addition to this, **``gologit2``** can fit two special cases of the general model: the proportional-odds model and the partial proportional-odds model (Lall, Walters, and Morgan 2002; Peterson and Harrell 1990). These models are less restrictive than the ordinal logit model fit by **``ologit``**, but they are more parsimonious than the multinomial logit model fit by **``mlogit``**.

### 16.3  (Advanced) Predictions without using factor-variable notation

>Factor variables make it much simpler to make predictions when there are linked variables, such as age and age-squared. Because **``gologit2``** does not support factor-variable notation, we use this model to illustrate how to make the correct predictions. **The most important point for most readers is likely that you want to use factor variables whenever possible! If you use factor-variable notation in your models, you do not need to worry about the issues discussed in this section. However, you might still find the section useful to deepen your understanding of predictions in nonlinear models.**

We can compute predicted probabilities for given values of observations as we did with the ordered logit model and use the same approach to interpretation. For example, here are results using the same ideal types that we used in section 7.12.

```
mtable, atright norownum width(7) ///
    at(female=0 white=0 year1996=0 year2012=1 educ_hs=0 educ_col=0 ///
    age=25 agesq=625 income=30) ///
    at(female=1 white=1 year1996=0 year2012=1 educ_hs=0 educ_col=1 ///
    age=60 agesq=3600 income=150)
```

    Expression: Pr(class), predict(outcome())
    
      lower  working   middle    upper   female    white  educ_col      age    agesq   income
    -----------------------------------------------------------------------------------------
      0.155    0.701    0.136    0.008        0        0         0       25      625       30
      0.000    0.122    0.809    0.069        1        1         1       60     3600      150
    
    Specified values of covariates
    
               | year1996  year2012  educ_hs
     ----------+----------------------------
       Current |        0         1        0

Comparing the results from **`ologit`** that were computed earlier in the chapter,

```
mtable, atright norownum width(7) ///
    at(female=0 white=0 year=3 ed=1 age=25 income=30) ///
    at(female=1 white=1 year=3 ed=3 age=60 income=150)
```

    Expression: Pr(class), predict(outcome())
    
      lower  working   middle    upper   female    white     educ      age   income
    -------------------------------------------------------------------------------
      0.230    0.634    0.133    0.003        0        0        1       25       30
      0.008    0.138    0.759    0.095        1        1        3       60      150
    
    Specified values of covariates
    
               |    year
     ----------+--------
       Current |       3

The main difference between the generalized and the ordered logit models is that the predicted probabilities for the categories with the highest probabilities (working class for the first ideal type and middle class for the second) are about 0.06 higher in the generalized ordered logit model.

We can also use **``mchange``** to obtain changes in the predicted probability for particular values of the independent variables, which provides an opportunity to illustrate how to deal with polynomial terms, such as age-squared, when you are not using factor-variable notation. Suppose that we want the discrete change for white, which is a binary variable. If factor-variable notation had been used, **``mchange``** would know that it is a binary variable. Because we are not using factor-variable notation, we must tell **``mchange``** to compute the change from 0 to 1 with the option amount (binary). It is tempting, but incorrect, to compute the change like this:

```
mchange white, amount(binary) atmeans // incorrect method!
```

    gologit2: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
    white        |                                           
          0 to 1 |    -0.001     -0.066      0.072     -0.005 
         p-value |     0.403      0.001      0.000      0.336 
    
    Predictions at base value
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
      Pr(y|base) |     0.011      0.503      0.466      0.020 
    
    Base values of regressors
    
                 |    female      white   year1996   year2012    educ_hs   educ_col        age      agesq 
    -------------+----------------------------------------------------------------------------------------
              at |      .549       .814       .451        .31       .582       .241       45.2       2325 
    
                 |    income 
    -------------+----------
              at |      68.1 

Because the mean of age is 45.16, agesq should be held at $45.16 \times 45.16 = 2039$, not 2325, which is the mean of agesq.

The correct way to compute marginal effects is to specify the value of agesq in **``at()``**:

```
mchange white, amount(binary) at(agesq=2039) atmeans // correct method
```

    gologit2: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
    white        |                                           
          0 to 1 |    -0.002     -0.064      0.070     -0.004 
         p-value |     0.402      0.001      0.000      0.335 
    
    Predictions at base value
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
      Pr(y|base) |     0.014      0.552      0.416      0.018 
    
    Base values of regressors
    
                 |    female      white   year1996   year2012    educ_hs   educ_col        age      agesq 
    -------------+----------------------------------------------------------------------------------------
              at |      .549       .814       .451        .31       .582       .241       45.2       2039 
    
                 |    income 
    -------------+----------
              at |      68.1 
    
    1: Estimates with margins option atmeans.

In this example, the differences are slight. Depending on the magnitudes of the coefficients for the linked variables and the distribution of those variables, however, the differences can be substantial. Fortunately, some simple programming tools can automate the process:

```
summarize age
local mnagesq = r(mean)*r(mean)
mchange white, amount(binary) at(agesq=`mnagesq') atmeans
```

**``summarize``** computes the mean of age, which is returned in **``r(mean)``**. The local macro **``mnagesq``** is set equal to the mean times the mean. Within the **``at()``** specification, **``agesq``** is set equal to this local macro.

Another limitation caused by the lack of support for factor-variable notation in **``gologit2``** is that you cannot use **``mchange``** to compute marginal effects of age because **``margins``** has no way to know that age and agesq must change together. **The only solution is to compute the appropriate predictions, specifying both the value of age and age-squared at two values of age and then subtracting the predictions.**

**For categorical independent variables with more than two variables, we need to explicitly constrain the other indicator variables to 0 as we let one of the indicators change from 0 to 1.** We can do this with **``at()``**. First, we compute the change between 1996 and 1980 (the base category), and then we compute the change between 2012 and 1980.

```
* change from 1980 to 1996
mchange year1996, amount(binary) at(year2012=0 agesq=2039) atmeans brief
```

    gologit2: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
    year1996     |                                           
          0 to 1 |     0.002      0.013     -0.014     -0.001 
         p-value |     0.325      0.469      0.436      0.903 

```
* change from 1980 to 2012
mchange year2012, amount(binary) at(year1996=0 agesq=2039) atmeans brief
```

    gologit2: Changes in Pr(y) | Number of obs = 5620
    
    Expression: Pr(class), predict(outcome())
    
                 |     lower    working     middle      upper 
    -------------+-------------------------------------------
    year2012     |                                           
          0 to 1 |     0.011      0.074     -0.073     -0.012 
         p-value |     0.000      0.000      0.000      0.004 

To compute the change from 1996 to 2012, we cannot use **``mchange``** because we need to change the value of two variables at once, namely, **year1996** and **year2012**. To estimate the changes, we use a simple program:

```
estimates store golm
mlincom, clear
forvalues iout = 1/4 {
    qui {
        mtable, atmeans post outcome(`iout') ///
        at(year1996=1 year2012=0 agesq=2039) /// 1996
        at(year1996=0 year2012=1 agesq=2039) // 2012
        mlincom 1 - 2, add rowname(outcome=`iout')
        estimates restore golm
    }
 }
mlincom
```

                 |   lincom    pvalue        ll        ul 
    -------------+---------------------------------------
       outcome=1 |   -0.009     0.000    -0.014    -0.004 
       outcome=2 |   -0.061     0.000    -0.094    -0.028 
       outcome=3 |    0.059     0.001     0.025     0.093 
       outcome=4 |    0.011     0.000     0.005     0.017 

For details on the commands used, see section 7.12.1.

### 1.16.4 The sequential logit model

Some ordinal outcomes represent the progress of events or stages in some process through which an individual can advance. For example, the outcome could be faculty rank, where the stages are assistant professor, associate professor, and full professor. **The key characteristic of the process is th at an individual must pass through each stage. The outcome is thus the result of a sequence of potential transitions:** an assistant professor may or may not make the transition to associate professor, and an associate professor may or may not make the transition to full professor

The most straightforward way to model an outcome like this is as a series of BRMs. Consider the binary logit model from chapter 5:

$$\ln\frac{\Pr\left(y=1\mid\mathbf{x}\right)}{\Pr\left(y=0\mid\mathbf{x}\right)}=\alpha+\mathbf{x}\boldsymbol{\beta}$$


where we have made the intercept explicit rather than including it in $ \beta $. To extend this to multiple transitions, we estimate for each transition the log odds of having made the transition (y > m) versus not having made the transition (y = m). For example, we estimate the log odds of being an associate or a full professor (y > 1) versus being an assistant professor (y = 1). We allow separate coefficients ($ \beta_m $) for each transition from $ y = m $:

$$\ln\frac{\Pr(y>m\mid\mathbf{x})}{\Pr(y=m\mid\mathbf{x})}=\alpha_m+\mathbf{x}\beta_m\quad\mathrm{for~}m=1\mathrm{~to~}J-1$$

where J is the number of stages.

This is an example of a broader group of models called sequential logit models (for example, Liao [1994, 26-28]). This model differs importantly from the generalized ordered logit model in that observations in which $ y < m $ are not used in the estimation of $ \beta_m $. For example, assistant professors are not used when modeling the transition from associate professor to full professor.

To demonstrate how to fit this model, we use the variable educ in the gssclass4 dataset as our outcome. The three values of educ represent two transitions: students may or may not graduate from high school, and high school graduates may or may not graduate from college. **To fit the model, we first use recode to create dummy variables representing whether or not respondents at each stage made the transition to the next.** The variable educ has the distribution

```
use gssclass4, clear
tab educ, miss
```

    educational |
     attainment |      Freq.     Percent        Cum.
    ------------+-----------------------------------
    not hs grad |        993       17.67       17.67
        hs only |      3,270       58.19       75.85
        college |      1,357       24.15      100.00
    ------------+-----------------------------------
          Total |      5,620      100.00

We create the variable gradcollege to indicate if someone with a high school diploma graduated from college, where those who did not graduate from high school (educ=1) are recoded as missing, not as 0. Those who did not graduate from high school are not included in the analysis of the transition to college graduation.

```
recode educ (1=0) (2 3=1), gen(gradhs)
label var gradhs "Graduate high school?"
recode educ (1=.) (2=0) (3=1), gen(gradcollege)
label var gradcollege "Graduate college?"
```

Next, we use **``logit``** to fit separate models for each transition, using race and sex as independent variables.

```
* HS degree vs not
logit gradhs i.white i.female, or nolog
```

    Logistic regression                                     Number of obs =  5,620
                                                            LR chi2(2)    =  25.40
                                                            Prob > chi2   = 0.0000
    Log likelihood = -2608.1189                             Pseudo R2     = 0.0048
    
    ------------------------------------------------------------------------------
          gradhs | Odds ratio   Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           white |
          white  |       1.53       0.13     5.09   0.000         1.30        1.80
                 |
          female |
         female  |       0.97       0.07    -0.48   0.631         0.84        1.11
           _cons |       3.39       0.29    14.35   0.000         2.87        4.00
    ------------------------------------------------------------------------------
    Note: _cons estimates baseline odds.

```
* College degree vs HS degree
logit gradcollege i.white i.female, or nolog
```

    Logistic regression                                     Number of obs =  4,627
                                                            LR chi2(2)    =   8.81
                                                            Prob > chi2   = 0.0122
    Log likelihood = -2795.2139                             Pseudo R2     = 0.0016
    
    ------------------------------------------------------------------------------
     gradcollege | Odds ratio   Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           white |
          white  |       1.15       0.10     1.64   0.100         0.97        1.37
                 |
          female |
         female  |       0.86       0.06    -2.39   0.017         0.75        0.97
           _cons |       0.40       0.04   -10.39   0.000         0.34        0.48
    ------------------------------------------------------------------------------
    Note: _cons estimates baseline odds.

We can interpret odds ratios for each of the transitions the same as we did in chapter 6. For example,

>Being white compared with being nonwhite increases the odds of graduating high school by a factor of 1.53, holding gender constant.

The **``seqlogit``** command (Buis 2007) fits equations for all transitions simultaneously and provides the likelihood for the full model. The **``seqlogit``** command can also be used with **``predict``** and **``margins``**— and accordingly, with our **``m*``** commands to compute probabilities of membership in each category. The syntax of **``seqlogit``** is complicated because it can be used to fit elaborate, branching sequences of transitions (see **``help seqlogit``** if installed). The **``tree ()``** option is required and is used to specify how outcome values map onto different transitions. In our case, we specify **``tree(1 : 2 3,2 : 3)``** because our two transitions are **``educ==1 or 2``** versus **``educ==3``** and **``educ==2``** versus **``educ==3``**:

```
seqlogit educ i.white i.female, tree(1: 2 3, 2 : 3) or nolog
```

    Transition tree:
    
    Transition 1: 1 : 2 3
    Transition 2: 2 : 3
    
    Computing starting values for: 
    
    Transition 1
    Transition 2
    
                                                            Number of obs =  5,620
                                                            LR chi2(4)    =  34.21
    Log likelihood = -5403.3329                             Prob > chi2   = 0.0000
    
    ------------------------------------------------------------------------------
            educ | Odds ratio   Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
    _2_3v1       |
           white |
          white  |       1.53       0.13     5.09   0.000         1.30        1.80
                 |
          female |
         female  |       0.97       0.07    -0.48   0.631         0.84        1.11
           _cons |       3.39       0.29    14.35   0.000         2.87        4.00
    -------------+----------------------------------------------------------------
    _3v2         |
           white |
          white  |       1.15       0.10     1.64   0.100         0.97        1.37
                 |
          female |
         female  |       0.86       0.06    -2.39   0.017         0.75        0.97
           _cons |       0.40       0.04   -10.39   0.000         0.34        0.48
    ------------------------------------------------------------------------------
    Note: _cons estimates baseline odds.
    
The coefficients here are precisely the same as when the equations were fit separately. The **``top``** equation presents coefficients for the transition to high school graduation (outcomes 2 and 3 versus 1) and the **``bottom``** for the transition to college graduate (outcome 3 versus 2). The log likelihood of the combined model fit with **``seqlogit``** is the sum of the log likelihoods of the separate binary logit models.

A more parsimonious model imposes the assumption that the coefficients for each independent variable are equal across all transitions. Instead of $\beta_m$ in equation (7.5), we have the same $\beta$ in all transition equations, while the intercepts differ:

$$\ln\frac{\Pr\left(y>m\mid\mathbf{x}\right)}{\Pr\left(y=m\mid\mathbf{x}\right)}=\alpha_m+\mathbf{x}\beta\quad\mathrm{for~}m=1\mathrm{~to~}J-1$$

where $J$ is the number of stages. This model is sometimes called the continuation ratio model and was first proposed by Fienberg (1980, p. 110).

The continuation ratio model can be fit using the user-written command **``ocratio``** (Wolfe 1998), although it uses a somewhat different parameterization than given here. Because **``ocratio``** is an older command, it does not support factor-variable notation and does not work with **``predict``, ``margins``,** or our **``m*``** commands. The continuation ratio model can be fit with **``seqlogit``** if you impose the equality constraints on coefficients by using the **``constraint define``** command. This command defines constraints on a model’s parameters that are imposed during estimation. Although further detail on constrained estimation is outside the scope of this book, we provide the example code below to show the use of the **``constraint define``** command and the **``constraint()``** option with **``seqlogit``**:

```
constraint define 1 [_2_3v1]1.female=[_3v2]1.female
constraint define 2 [_2_3v1]1.white=[_3v2]1.white
```

The key to understanding the constraints is understanding how **``seqlogit``** names the equations that it estimates. The equation comparing outcome 1 with outcomes 2 and 3 is named **``_2_3vl``** so that **``[_2_3vl]1.female``** indicates the coefficient for **``1.female``** in this equation. Accordingly, the constraint 1 defined above says that the coefficients for **``1.female``** are equal in both transition equations. Constraint 2 does the same thing for **``1.white``**. To impose these constraints during estimation, we add the option **``constraint(1 2)``** to the estimation command:

```
seqlogit educ i.white i.female, tree(1: 2 3, 2 : 3) 
```

    Transition tree:
    
    Transition 1: 1 : 2 3
    Transition 2: 2 : 3
    
    Computing starting values for: 
    
    Transition 1
    Transition 2
    
                                                             Number of obs = 5,620
                                                             Wald chi2(0)  =     .
    Log likelihood = -5406.6832                              Prob > chi2   =     .
    
     ( 1)  [_2_3v1]1.female - [_3v2]1.female = 0
     ( 2)  [_2_3v1]1.white - [_3v2]1.white = 0
    ------------------------------------------------------------------------------
            educ | Odds ratio   Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
    _2_3v1       |
           white |
          white  |       1.34       0.08     4.70   0.000         1.18        1.51
                 |
          female |
         female  |       0.90       0.04    -2.09   0.036         0.82        0.99
           _cons |       3.91       0.26    20.48   0.000         3.43        4.45
    -------------+----------------------------------------------------------------
    _3v2         |
           white |
          white  |       1.34       0.08     4.70   0.000         1.18        1.51
                 |
          female |
         female  |       0.90       0.04    -2.09   0.036         0.82        0.99
           _cons |       0.34       0.02   -15.84   0.000         0.30        0.39
    ------------------------------------------------------------------------------
    Note: _cons estimates baseline odds.


The log likelihood is smaller than it was with the unconstrained, sequential logit model. Because the second model is nested within the first model, we can see whether the difference in fit is statistically significant with an LR test:

```
lrtest contratio seqlogit
```

    Likelihood-ratio test
    Assumption: contratio nested within seqlogit
    
     LR chi2(2) =   6.70
    Prob > chi2 = 0.0351

The p-value is less than 0.05, so we reject the null hypothesis that the coefficients are equal across transitions. We could compare whether the coefficients for a particular independent variable are equal across transitions by constraining only those coefficients to be equal or by using **``test``** to compute a Wald test.


