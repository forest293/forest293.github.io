# [Multilevel  Longitudinal Models] Chapter 3 ：Random-intercept models with covariates


## 2 Does smoking during pregnancy affect birthweight?

Abrevaya (2006) investigates the effect of smoking on birth outcomes with the Natality datasets derived from birth certificates by the U.S. National Center for Health Statistics. This is of considerable public health interest because many pregnant women in the U.S. continue to smoke during pregnancy. Indeed, around the time of the study, it was estimated that only 18% to 25% of smokers quit smoking once they become pregnant, according to the 2004 Surgeon General’s Report on The Health Consequences of Smoking.

Abrevaya identified multiple births from the same mothers in nine datasets from 1990–1998 by matching mothers across the datasets. Unlike, for instance, the Nordic countries, a unique person identifier such as a social security number is rarely available in U.S. datasets. Perfect matching is thus precluded, and matching must proceed by identifying mothers who have identical values on a set of variables in all datasets. In this study, matching was accomplished by considering mother’s state of birth and child’s state of birth, as well as mother’s county and city of birth; mother’s age, race, education, and marital status; and, if married, husband’s age and race. For the matching on mother’s and child’s states of birth to be useful, the data were restricted to combinations of states that occur rarely.

Here we consider the subset of the matches where the observed interval between births was consistent with the interval since the last birth recorded on the birth certificate. The data are restricted to births with complete data for the variables considered by Abrevaya (2006), singleton births (no twins or other multiple births), and births to mothers for whom at least two births between 1990 and 1998 could be matched and whose race was classified as White or Black.

The birth outcome we will concentrate on is birthweight. Abrevaya (2006) motivates his study by citing a report from the U.S. Surgeon General:

The dataset used by Abrevaya (2006) is available from the Journal of Applied Econometrics Data Archive. Here we took a 10% random sample of the mothers, yielding 8,604 births from 3,978 mothers. We use the following variables from smoking.dta:

- momid: mother identifier  
- birwt: birthweight (in grams)  
- smoke: dummy variable for mother smoking during pregnancy (1: smoking; 0: not smoking)  
- male: dummy variable for baby being male (1: male; 0: female)  
- mage: mother’s age at the birth of the child (in years)  
- Mother’s education (reference category: did not graduate from high school)  
- hsgrad: dummy variable for having graduated from high school (1: graduated; 0: otherwise)  
- somecoll: dummy variable for having some college education, but no degree (1: some college; 0: otherwise)  
- collgrad: dummy variable for having graduated from college (1: graduated; 0: otherwise)  
- married: dummy variable for mother being married (1: married; 0: unmarried)  
- black: dummy variable for mother being Black (1: Black; 0: White)  
- Kessner index (reference category: Kessner index = 1, or adequate prenatal care)  
- kessner2: dummy variable for Kessner index = 2, or intermediate prenatal care (1: index=2; 0: otherwise)  
- kessner3: dummy variable for Kessner index = 3, or inadequate prenatal care (1: index=3; 0: otherwise)  
- Timing of first prenatal visit (reference category: first trimester)  
- novisit: dummy variable for no prenatal care visit (1: no visit; 0: otherwise)  
- pretri2: dummy variable for first prenatal care visit having occurred in second trimester (1: yes; 0: otherwise)  
- pretri3: dummy variable for first prenatal care visit having occurred in third trimester (1: yes; 0: otherwise)


Smoking status was determined from the answer to the question asked on the birth certificate whether there was tobacco use during pregnancy.

The dummy variables for mother’s education—hsgrad, somecoll, and collgrad—were derived from the years of education given on the birth certificate. The Kessner index is a measure of the adequacy of prenatal care (1: adequate; 2: intermediate; 3: inadequate) based on the timing of the first prenatal visit and the number of prenatal visits, taking into account the gestational age of the fetus.

### 2.1 Data structure and descriptive statistics

The data have a two-level structure with births (or children or pregnancies) as units at level 1 and mothers as clusters at level 2. **In multilevel models, the response variable always varies at the lowest level, taking on different values for different level-1 units within the same level-2 cluster.** However, **covariates can either vary at level 1 (and therefore usually also at level 2) or vary at level 2 only.** For instance, while smoke can change from one pregnancy to the next, black is constant between pregnancies. smoke is therefore said to be a level-1 variable, whereas black is a level-2 variable. Among the variables listed above, black appears to be the only one that cannot in principle change between pregnancies. However, because of the way the matching was done, the education dummy variables (hsgrad, somecoll, and collgrad) and married also remain constant across births for the same mother and are thus level-2 variables. There are a total of 5 level-2 covariates and 8 level-1 covariates here.

As we will see in this chapter, the distinction between level-1 and level-2 covariates is important in several ways. Keeping track of the number of level-2 covariates is necessary when the number of level-2 units is not large because the rule of thumb in display 2.1 for choosing between ML and REML, deciding whether robust standard errors can be trusted, and whether asymptotic tests can be used, is $J-q>42$, where $q$ is the number of level-2 covariates. When considering the maximum number of level-2 covariates that can reasonably be included in the model, a rough rule of thumb is that there should be at least about 10 clusters per level-2 covariate; $J/q\geq10$.

It is useful to know not just whether variables vary at levels 1 and 2 but also how much they vary at each of the levels. Variation at the two levels can be explored using the `xtsum` command (after reading the data and declaring their two-level structure by using `xtset`):


#### 协变量（Covariate）的层次

- **Level-1协变量**：这些是与个体层次相关的变量，比如学生的性别、年龄等。
- **Level-2协变量**：这些是与群体层次相关的变量，比如学校的资源水平、学校类型等。

#### 为什么Level-2协变量的数量很重要？

当Level-2单位（如学校）的数量不多时，Level-2协变量的数量会影响模型的估计和检验。这是因为：
1. **模型估计方法**：选择最大似然估计（MLE）还是限制最大似然估计（REML）。
2. **标准误的稳健性**：判断稳健标准误是否可信。
3. **渐近检验的适用性**：判断是否可以使用基于渐近理论的检验。

#### 经验法则公式解析

#### $ J - q > 42 $

- **$ J $**：Level-2单位的数量，例如学校的数量。
- **$ q $**：Level-2协变量的数量。
- 这个条件告诉我们，如果Level-2单位的数量减去Level-2协变量的数量大于42，我们可以考虑使用MLE或REML方法，并且可以信赖稳健标准误和使用渐近检验。

#### 例子

假设有45所学校，考虑使用3个Level-2协变量（比如学校的资源水平、师生比例、学校类型）：
- $ J = 45 $（学校数量）
- $ q = 3 $（Level-2协变量数量）
- $ J - q = 45 - 3 = 42 $
在这个例子中，$ J - q = 42 $，正好等于42，这意味着我们处于经验法则的边界上。如果学校数量再少一些，或者协变量数量再多一些，我们可能就无法使用MLE或REML方法，或者不能信赖稳健标准误和渐近检验了。

#### $ J/q \geq 10 $

- 这个条件告诉我们，对于每个Level-2协变量，至少应该有10个Level-2单位。这是一个粗略的经验法则，用于确定模型中可以合理包含的Level-2协变量的最大数量。

#### 例子
如果我们有45所学校，根据经验法则，我们可以包含的Level-2协变量的最大数量是：
- $ J = 45 $（学校数量）
- $ q $ 应该满足 $ 45/q \geq 10 $
- 解这个不等式，我们得到 $ q \leq 4.5 $
因为我们不能有半个协变量，所以我们可以说最多可以有4个Level-2协变量。

#### 变量在不同层次的变异性

了解变量在不同层次的变异性对于模型的构建和解释非常重要。可以使用`xtsum`命令来探索这种变异性。在使用`xtsum`之前，需要使用`xtset`来声明数据的层次结构。

#### 总结

- 理解Level-1和Level-2协变量的区别。
- 知道Level-2协变量数量对模型估计和检验的影响。
- 掌握经验法则公式，并能够应用它们来决定模型中可以包含的协变量数量。
- 使用`xtsum`和`xtset`来探索和分析数据的层次结构。
希望这个更详细的解释能帮助你更好地理解这些概念。如果你还有任何疑问，或者需要进一步的例子，请随时告诉我。

```
use "F:\【3】Electronic books\0[Applied Statistics]\（3）第三本\datasets\smoking.dta"
quietly xtset momid 
xtsum birwt smoke black
```
    
    Variable         |      Mean   Std. dev.       Min        Max |    Observations
    -----------------+--------------------------------------------+----------------
    birwt    overall |  3469.931   527.1394        284       5642 |     N =    8604
             between |             451.1943       1361     5183.5 |     n =    3978
             within  |             276.7966   1528.431   5411.431 | T-bar =  2.1629
                     |                                            |
    smoke    overall |  .1399349   .3469397          0          1 |     N =    8604
             between |             .3216459          0          1 |     n =    3978
             within  |             .1368006  -.5267318   .8066016 | T-bar =  2.1629
                     |                                            |
    black    overall |  .0717108   .2580235          0          1 |     N =    8604
             between |              .257512          0          1 |     n =    3978
             within  |                    0   .0717108   .0717108 | T-bar =  2.1629

The total number of observations is $N=8604$; the number of clusters is $J=3978$ (n in the output); and there are on average about 2.2 births per mother (`T-bar` in the output) in the dataset.

Three different sample standard deviations are given for each variable. The first is the **overall standard deviation**, $s_{ro}$, defined as usual as the square root of the mean squared deviation of observations from the overall mean:

$$s_{xO} = \sqrt{\frac{1}{N-1}\sum_{j=1}^{J}\sum_{i=1}^{n_{j}}(x_{ij}-\overline{x}_{..})^2}$$

The second is the **between standard deviation**, defined as the square root of the mean squared deviation of the cluster means from the overall mean:

$$s_{xB} = \sqrt{\frac{1}{J-1}\sum_{j=1}^J(\overline x_{\cdot j}-\overline{x}_{\cdot\cdot})^2}$$

This third is the **within standard deviation**, defined as the square root of the mean squared deviation of observations from the cluster means:

$$s_{xW} = \sqrt{\frac{1}{N-1}\sum_{j=1}^{J}\sum_{i=1}^{n_j}(x_{ij}-\overline{x}_{\cdot j})^2}$$

#### 1. 标准差的概念

首先，标准差是一种衡量数据分散程度的统计量，它表示数据点与平均值的偏差大小。

#### 2. 整体标准差（Overall Standard Deviation）

**公式**：
$$ s_{xO} = \sqrt{\frac{1}{N-1}\sum_{j=1}^{J}\sum_{i=1}^{n_{j}}(x_{ij}-\overline{x}_{..})^2} $$
- **$ s_{xO} $**：表示整体标准差。
- **$ N $**：是所有观察值的总数。
- **$ J $**：是群体（如学校）的数量。
- **$ n_j $**：是第 $ j $ 个群体中的观察值数量。
- **$ x_{ij} $**：表示第 $ j $ 个群体中第 $ i $ 个观察值。
- **$ \overline{x}_{..} $**：表示所有观察值的平均值。

**解释**：
整体标准差测量的是所有观察值与整体平均值的偏差。它考虑了所有数据点，不考虑它们属于哪个群体。

#### 3. 组间标准差（Between Standard Deviation）

**公式**：
$$ s_{xB} = \sqrt{\frac{1}{J-1}\sum_{j=1}^J(\overline x_{\cdot j}-\overline{x}_{\cdot\cdot})^2} $$
- **$ s_{xB} $**：表示组间标准差。
- **$ \overline x_{\cdot j} $**：表示第 $ j $ 个群体的平均值。
**解释**：
组间标准差测量的是不同群体的平均值与整体平均值之间的偏差。它反映了群体间的差异。

#### 4. 组内标准差（Within Standard Deviation）

**公式**：
$$ s_{xW} = \sqrt{\frac{1}{N-1}\sum_{j=1}^{J}\sum_{i=1}^{n_j}(x_{ij}-\overline{x}_{\cdot j})^2} $$
- **$ s_{xW} $**：表示组内标准差。
**解释**：
组内标准差测量的是每个群体内的观察值与该群体平均值的偏差。它反映了群体内部的差异。

- **整体标准差**：测量所有观察值与整体平均值的偏差。
- **组间标准差**：测量不同群体的平均值与整体平均值的偏差。
- **组内标准差**：测量每个群体内的观察值与该群体平均值的偏差。


#### 例子

假设我们有3所学校（群体），每所学校有10名学生，总共30名学生（观察值）。学生的成绩如下：
- 学校A：学生成绩 [60, 65, 70, 75, 80, 85, 90, 95, 100, 105]
- 学校B：学生成绩 [50, 55, 60, 65, 70, 75, 80, 85, 90, 95]
- 学校C：学生成绩 [70, 72, 74, 76, 78, 80, 82, 84, 86, 88]

#### 1. 计算整体平均分 $ \overline{x}_{..} $

首先，我们需要计算所有学生成绩的总和，然后除以学生总数（30）。

$$ \overline{x}_{..} = \frac{\text{所有成绩总和}}{30} $$

把所有成绩加起来：

$$ \text{总和} = 60 + 65 + \cdots + 105 $$

$$ \text{总和} = 1650 $$
然后计算平均分：

$$ \overline{x}_{..} = \frac{1650}{30} = 55 $$

#### 2. 计算每个学校的平均分 $ \overline{x}_{\cdot j} $

- 学校A的平均分：

$$ \overline{x}_{\cdot A} = \frac{60 + 65 + \cdots + 105}{10} = \frac{675}{10} = 67.5 $$

- 学校B和学校C的计算方法相同，分别计算它们的平均分。

#### 3. 计算整体标准差 $ s_{xO} $

使用公式：

$$ s_{xO} = \sqrt{\frac{1}{N-1}\sum_{j=1}^{J}\sum_{i=1}^{n_{j}}(x_{ij}-\overline{x}_{..})^2} $$

我们需要计算每个学生的成绩与整体平均分的差的平方，然后求和，最后除以（N-1）并开方。

$$ \sum_{i=1}^{30}(x_{i}-\overline{x}_{..})^2 = (60-55)^2 + (65-55)^2 + \cdots + (105-55)^2 $$

计算差的平方并求和：

$$ \text{求和} = (5)^2 + (10)^2 + \cdots + (50)^2 $$

$$ \text{求和} = 25 + 100 + \cdots + 2500 $$

$$ \text{求和} = 4550 $$

然后计算整体标准差：

$$ s_{xO} = \sqrt{\frac{4550}{29}} \approx \sqrt{156.55} \approx 12.52 $$

#### 4. 计算组间标准差 $ s_{xB} $

使用公式：

$$ s_{xB} = \sqrt{\frac{1}{J-1}\sum_{j=1}^J(\overline x_{\cdot j}-\overline{x}_{\cdot\cdot})^2} $$
计算每个学校平均分与整体平均分的差的平方，然后求和，最后除以（J-1）并开方。

$$ \sum_{j=1}^{3}(\overline x_{\cdot j}-\overline{x}_{\cdot\cdot})^2 = (67.5 - 55)^2 + (62.5 - 55)^2 + (78 - 55)^2 $$

$$ \text{求和} = 122.5 + 62.5 + 552 $$

$$ \text{求和} = 737.5 $$
然后计算组间标准差：

$$ s_{xB} = \sqrt{\frac{737.5}{2}} \approx \sqrt{368.75} \approx 19.21 $$

#### 5. 计算组内标准差 $ s_{xW}$

使用公式：

$$ s_{xW} = \sqrt{\frac{1}{N-1}\sum_{j=1}^{J}\sum_{i=1}^{n_j}(x_{ij}-\overline{x}_{\cdot j})^2} $$

对于每个学校，计算每个学生的成绩与该学校平均分的差的平方，然后求和，最后除以（N-1）并开方。

我们以学校A为例：

$$ \sum_{i=1}^{10}(x_{Ai}-\overline{x}_{\cdot A})^2 = (60-67.5)^2 + (65-67.5)^2 + \cdots + (105-67.5)^2 $$
$$ \text{求和} = 62.5 + 12.25 + \cdots + 302.25 $$
$$ \text{求和} = 412.5 $$

计算组内标准差（注意这里我们需要计算所有学校的总和）：

$$ s_{xW} = \sqrt{\frac{\text{所有学校求和}}{29}} $$
$$ s_{xW} = \sqrt{\frac{412.5 + \text{学校B和C的求和}}{29}} $$

假设学校B和C的求和分别为400和450，那么总和为：

$$ \text{总和} = 412.5 + 400 + 450 = 1262.5 $$

然后计算组内标准差：

$$ s_{xW} = \sqrt{\frac{1262.5}{29}} \approx \sqrt{43.53} \approx 6.60 $$

通过这个例子，我们可以看到：

- **整体标准差** $ s_{xO} $ 反映了所有学生成绩的分散程度。
- **组间标准差** $ s_{xB} $ 反映了不同学校平均成绩之间的分散程度。
- **组内标准差** $ s_{xW} $ 反映了每个学校内部学生成绩的分散程度。

We see that birthweight and smoking vary more between mothers than within mothers, whereas being Black does not vary at all within mothers, as expected. It is important to be aware of how much the level-1 variables vary within clusters because some estimators utilize only within-cluster variability of covariates (and not between-cluster variability).

There are two different ways of expressing the mean or proportion for a level-2 variable: considering the summary either across units (with the level-1 units as the unit of analysis) or across clusters (with the clusters as the unit of analysis). For instance, the mean for Black produced by `xtsum` is the mean (or proportion, because Black is binary) across units, the proportion of children born to Black mothers. We could also consider the mean across mothers, or the proportion of mothers who are Black. To do so, we define a dummy variable `pickone` equal to 1 for one child per mother by using the `egen` command with the `tag()` function.


```
egen pickone = tag(momid)
```

and summarize **black** across mothers by specifying if `pickone==1` in the command:

```
summarize black if pickone==1
```
    
        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
           black |      3,978    .0713927     .257512          0          1

We see that the proportion of mothers who are Black is very close to the proportion of children born to Black mothers, probably because the average number of children per mother in the dataset does not differ much between Black and White mothers.

We can calculate the number of children per mother by using `egen` with the `count()` function:

```
egen num = count(birwt), by(momid)
tabulate num if pickone==1
```
    
            num |      Freq.     Percent        Cum.
    ------------+-----------------------------------
              2 |      3,330       83.71       83.71
              3 |        648       16.29      100.00
    ------------+-----------------------------------
          Total |      3,978      100.00

Most mothers have two children in the data, and about 16% have three.

For level-1 categorical variables, such as **smoke**, `xttab` produces a table of summaries:

```
quietly xtset momid
xttab smoke
```
    
                      Overall             Between            Within
        smoke |    Freq.  Percent      Freq.  Percent        Percent
    ----------+-----------------------------------------------------
     Nonsmoke |    7400     86.01      3565     89.62          95.69
       Smoker |    1204     13.99       717     18.02          79.03
    ----------+-----------------------------------------------------
        Total |    8604    100.00      4282    107.64          92.90
                                  (n = 3978)

In the Overall table, we see that mothers smoked during their pregnancies for 14% of the children. According to the Between table, 90% of mothers had at least one pregnancy where they did not smoke, and 18% of mothers had at least one pregnancy where they did smoke. The Within table shows that the women who were ever nonsmokers during a pregnancy (row 1) were nonsmokers for an average of 96% of their pregnancies. The women who ever smoked during a pregnancy (row 2) did so for an average of 79% of their pregnancies.

## 3 The linear random-intercept model with covariates

### 3.1 Model specification

An obvious model to consider for the continuous response variable, birthweight, is a multiple linear regression model (discussed in chapter 1), including smoking status and various other variables as explanatory variables or covariates.

The model for the birthweight $y_ij$ of child $i$ of mother $j$ is specified as

$$y_{ij} = \beta_1+\beta_2x_{2ij}+\cdots+\beta_px_{pij}+\xi_{ij}$$

where $x_{2ij}$ through $x_{pij}$ are covariates and $\xi _{ij}$ is a residual or error term.

It may be unrealistic to assume that the birthweights of children born to the same mother are uncorrelated given the observed covariates, or in other words that the residuals $\xi _{ij}$ and $\xi _{i'j}$ are uncorrelated. **We can therefore use the idea introduced in the previous chapter to split the total residual or error into two error components: $\zeta _{j}$,** which is shared between children of the same mother $j$, and $\epsilon _{ij}$, which is unique for each child $ij$:

$$\xi _{ij} = \zeta _{j} + \epsilon _{ij}$$

Substituting for $\xi _{ij}$ into the multiple regression model (3.1), we obtain a linear random-intercept model with covariates :

$$\begin{aligned}y_{ij}&=\quad\beta_1+\beta_2x_{2ij}+\cdots+\beta_px_{pij}+(\zeta_j+\epsilon_{ij})\end{aligned}$$

$$=\quad(\beta_1+\zeta_j)+\beta_2x_{2ij}+\cdots+\beta_px_{pij}+\epsilon_{ij}$$


This model can be viewed as a regression model with an added level-2 residual $\xi_j$, or with a mother-specific intercept $\beta_1 + \xi_j$. The random intercept $\xi_j$ can be considered a latent variable that is not estimated along with the fixed parameters $\beta_1$ through $\beta_p$, but whose variance $\psi$ is estimated together with the variance $\theta$ of the level-1 residuals $\epsilon_{ij}$. The linear random-intercept model with covariates is the simplest example of a linear mixed (effects) model where there are both fixed and random effects.

The random intercept or level-2 residual $\zeta_j$ is a mother-specific error component, which remains constant across births, whereas the level-1 residual $\epsilon_{ij}$ is a child-specific error component, which varies between children $i$ as well as mothers $j$. The $\zeta_j$ are uncorrelated over mothers, the $\epsilon_{ij}$ are uncorrelated over mothers and children, and the two error components are uncorrelated with each other.

The mother-specific error component $\zeta_j$ represents the combined effects of all omitted mother characteristics or unobserved heterogeneity at the mother level. (Strictly speaking, $\zeta_j$ represents only the component of this combined effect that is uncorrelated with the covariates because of the level-2 exogeneity assumption discussed below.) If $\zeta_j$ is positive, the total residuals for mother $j$, $\xi_{ij}$, will tend to be positive, leading to heavier babies than predicted by the covariates. If $\zeta_j$ is negative, the total residuals will tend to be negative. Because $\zeta_j$ is shared by all responses for the same mother, it induces within-mother dependence among the total residuals $\xi_{ij}$. On first reading, you may want to skip the following more technical material and go to section 3.3.5.


我们来一步一步地理解这个线性混合模型（linear mixed model），特别是它如何应用于分析婴儿出生体重这样的连续响应变量。我会尽量简化解释，让你更容易理解。

**1. 第一步：理解多重线性回归模型**

首先，我们有一个多重线性回归模型，用来预测婴儿的出生体重 $ y_{ij} $。这个模型包括吸烟状态和其他一些变量作为解释变量或协变量。模型可以写成：

$ y_{ij} = \beta_1 + \beta_2 x_{2ij} + \cdots + \beta_p x_{pij} + \xi_{ij} $

这里：
- $ y_{ij} $ 是第 $ i $ 个婴儿（母亲 $ j $ 的孩子）的出生体重。
- $ \beta_1, \beta_2, \ldots, \beta_p $ 是模型参数。
- $ x_{2ij}, \ldots, x_{pij} $ 是协变量，比如母亲的年龄、体重、是否吸烟等。
- $ \xi_{ij} $ 是残差项，也就是模型未能解释的部分。

**2. 第二步：考虑同一母亲的婴儿体重的相关性**

在实际中，同一母亲所生的婴儿体重可能存在相关性，因为它们共享相同的遗传和环境因素。因此，我们不能简单地假设 $ \xi_{ij} $ 和 $ \xi_{i'j} $（不同婴儿的残差）是不相关的。

**3. 第三步：引入随机截距模型**

为了解决这个问题，我们可以将残差 $ \xi_{ij} $ 分解为两部分：
- $ \zeta_j $：同一母亲 $ j $ 的所有孩子共享的残差部分。
- $ \epsilon_{ij} $：每个婴儿 $ ij $ 独有的残差部分。
这样，模型可以写成：
$ \xi_{ij} = \zeta_j + \epsilon_{ij} $
将这个分解代入原始的多重线性回归模型，我们得到：
$ y_{ij} = (\beta_1 + \zeta_j) + \beta_2 x_{2ij} + \cdots + \beta_p x_{pij} + \epsilon_{ij} $
这个模型现在包括了一个随机截距 $ \zeta_j $，它代表了每个母亲特有的效应。

**4. 第四步：理解模型的组成部分**

- **固定效应**：$ \beta_1, \beta_2, \ldots, \beta_p $ 是固定参数，它们对所有母亲和婴儿都是一样的。
- **随机效应**：$ \zeta_j $ 是随机截距，它为每个母亲引入了一个特定的效应。
- **残差**：$ \epsilon_{ij} $ 是每个婴儿独有的随机误差。

**5. 第五步：模型的解释**

- **$ \zeta_j $**：这个随机截距代表了所有未观测到的母亲特征的综合效应。如果 $ \zeta_j $ 是正的，那么这位母亲所生的婴儿的出生体重通常会比仅根据协变量预测的要重。如果 $ \zeta_j $ 是负的，则相反。
- **$ \epsilon_{ij} $**：这个残差代表了每个婴儿特有的随机波动，它在不同的婴儿和母亲之间是不相关的。
通过这种方式，模型能够同时考虑婴儿出生体重的个体差异和母亲之间的差异，从而更准确地预测和分析数据。



### 3.2 Model assumptions


We now explicitly state a set of assumptions that are sufficient for everything we want to do in this chapter but are not always necessary. For this purpose, all observed covariates for unit $i$ in cluster $j$ are placed in the vector $\mathbf{x}_{ij}$, and the covariates for all the units in cluster $j$ are placed in the matrix $\mathbf{X}_j$ (with rows $\mathbf{x_i}_j'$).

It is assumed that the level-1 residual $\epsilon_{ij}$ has zero expectation or mean, given the covariates and the random intercept:

$$E(\epsilon_{ij}|\mathbf{X}_j,\zeta_j)=0$$

This mean-independence assumption implies that $E(\epsilon_{ij}|\mathbf X_j)=0$ and that Cor($\epsilon_{ij},\mathbf x_{ij})=0$ and Cor($\epsilon_{ij},\overline{\mathbf{x}}_{.j})=0$. **We call this lack of correlation between covariates and level-1 residual "level-1 exogeneity". (See also section 1.13 on the exogeneity assumption in standard linear regression.)**

The random intercept $\zeta_j$ is assumed to have zero expectation given the covariates,

$$E(\zeta_j|\mathbf{X}_j) = 0$$

**and this mean-independence assumption implies that Cor($\zeta_j, x_{ij} $)=0 and Cor($\zeta_j, \overline{x}_{.j} $)=0. We call the lack of correlation between the covariates and the random intercept "level-2 exogeneity".** Violations of the exogeneity assumptions are called level-1 endogeneity and level-2 endogeneity, respectively.

We assume that the level-1 residual is homoskedastic (has constant variance) for given covariates and random intercept,

$$Var(\epsilon_{ij}|X_j,\zeta_j) = θ$$

which implies that Var($\epsilon_{ij}$) = θ and that Cor($\epsilon_{ij},\zeta_j$) = 0. It is also assumed that the random intercept is homoskedastic given the covariates,

$$Var(\zeta_j|X_j) = ψ$$

which implies that $Var(\zeta_j) = ψ.$

It is assumed that the level-1 residuals are uncorrelated for two units i and i' (whether they are nested in the same cluster j or in different clusters j and j') given the covariates and random intercept(s),

$$\mathrm{Cov}(\epsilon_{ij},\epsilon_{i^{\prime}j^{\prime}}|\mathbf X_j,\mathbf X_{j^{\prime}},\zeta_j,\zeta_{j^{\prime}}) = 0\quad\mathrm{if}\quad i\neq i^{\prime}\quad\mathrm{or}\quad j\neq j^{\prime}\quad(3.7)$$

and that random intercepts are uncorrelated for different clusters j and j' given the covariates,

$$\mathrm{Cov}(\zeta_j,\zeta_{j^{\prime}}|\mathbf X_j,\mathbf X_{j^{\prime}}) = 0\quad\mathrm{~if~} j\neq j^{\prime}\quad(3.8)$$

These assumptions imply the mean and residual covariance structure of the responses described in sections 3.3.3 and 3.3.4.

For (restricted) maximum likelihood estimation, normal distributions are specified for $\epsilon_{ij}| \mathbf X_j, \zeta_j$ and $\zeta_j| \mathbf X_{-j}.$ (Together with the assumptions (3.3) and (3.5), this implies that $\zeta_j$ and $\epsilon_{ij}$ are independent--a stronger property than lack of correlation). **Such specification of the distributions is necessary to construct the likelihood and restricted likelihood for ML and REML estimation, respectively. However, it is important to note that the estimators behave well even if the distributions are different from normal.** Indeed, consistent estimation of the regression coefficients $\beta_1,...,\beta_p$ relies only on correct specification of the mean structure. If, additionally, the total residuals have a symmetric distribution, estimation of the regression coefficients is also unbiased.

Correct specification of both the mean and covariance structure is needed only for consistent model-based standard errors, efficient estimation of the regression coefficients, and consistent estimation of $\psi$ and $\theta$. If the covariance structure is misspecified, robust standard errors are consistent and perform well if there are enough clusters in the data. How many clusters are needed for reliable robust standard errors? We can use our rule of thumb of 42 discussed in display 2.1, where 42 now refers to the number of clusters $minus$ the number $(q)$ of level-2 covariates, that is, $J-q\geq42$.


* **协变量和随机截距**

首先，我们来讨论协变量和随机截距的概念。在统计模型中，协变量是我们用来解释响应变量（比如婴儿的出生体重）的变量。在群组（比如家庭）数据中，我们通常有两级变量：

- **一级协变量** ($\mathbf{x}_{ij}$)：指的是与单个观测单位（比如一个婴儿）相关的变量。

- **二级协变量**：可能与群组（比如一个家庭）有关，但在模型中通常不直接使用。

随机截距 ($\zeta_j$) 是每个群组特有的效应，它反映了群组内所有成员共享的未观测到的因素。在婴儿出生体重的例子中，这可能包括母亲的遗传特征、生活方式等。

* **残差的期望为零**

残差（或误差项）是实际观测值与模型预测值之间的差异。在一个好的模型中，我们希望这个差异是随机的，而不是系统性的。期望为零的假设意味着，平均来说，我们的模型没有系统性地高估或低估响应变量的值。

- **一级残差** ($\epsilon_{ij}$)：指的是单个观测单位的误差。
- **期望为零**：$E(\epsilon_{ij}|\mathbf{X}_j,\zeta_j)=0$ 表示，给定所有协变量和随机截距，我们期望误差项的平均值为零。

* **外生性**

外生性是指模型中的误差项与解释变量不相关。这有两个层面：
- **一级外生性**：指的是一级残差与一级协变量不相关。
- **二级外生性**：指的是随机截距与一级协变量不相关。
如果违反了这些外生性假设，我们可能会遇到内生性问题，这可能导致估计的参数有偏误。

* **同质性（Homoscedasticity）**

同质性假设指的是残差的方差不依赖于解释变量的值。这有两个层面：
- **一级同质性**：指的是给定协变量和随机截距后，一级残差的方差是恒定的。
- **二级同质性**：指的是给定协变量后，随机截距的方差是恒定的。

* **残差的无相关性**

这个假设表明，不同观测单位的残差之间是不相关的。这包括：
- 不同婴儿之间的残差不相关。
- 不同母亲（群组）之间的随机截距不相关。

* **最大似然估计**

最大似然估计（MLE）是一种常用的参数估计方法。在给定数据的情况下，我们寻找参数值，使得观测到的数据出现的概率（似然性）最大。在本例中，我们假设残差和随机截距都遵循正态分布，这有助于我们构建似然函数。

* **模型的一致性和效率**

- **一致性**：指的是随着样本量的增加，估计量会收敛到真实的参数值。
- **效率**：指的是在所有一致估计量中，具有最小方差的估计量。
正确指定模型的均值和协方差结构对于获得一致和高效的估计至关重要。如果协方差结构被错误指定，我们可以使用稳健标准误差来获得一致的估计量，尤其是在群组数量足够多的情况下。

* **例子**

假设我们有100位母亲和她们的200个婴儿的数据。每位母亲可能有一个或多个婴儿。我们想要估计母亲的吸烟习惯如何影响婴儿的出生体重。

- 我们收集每位母亲的年龄、体重、是否吸烟等协变量。
- 对于每个婴儿，我们记录其出生体重。
- 我们假设每个母亲的吸烟习惯和其他协变量可以解释婴儿出生体重的大部分变异。
- 我们进一步假设，即使在考虑了这些协变量之后，同一个母亲的婴儿之间仍然存在出生体重的相关性，这是由母亲的未观测到的特征（比如遗传）所导致的。
- 我们使用线性混合模型来估计吸烟对出生体重的影响，同时考虑到每个母亲特有的随机截距。
通过这个模型，我们可以更准确地估计吸烟对出生体重的影响，同时考虑到数据的群组结构。
希望这个更详细的解释能帮助你更好地理解这些统计模型的假设和它们的意义。如果还有任何疑问，请随时提问。

### 3.3 Mean structure

Assumption (3.3) implies that the cluster-specific or conditional regression (averaged over $\epsilon_{ij}$ but given $\zeta_j$ and $X_j$) is linear:

$$E(y_{ij}|X_j,\zeta_j)=E(\beta_1+\beta_2x_{2ij}+\cdots+\beta_px_{pij})+E(\zeta_j|X_j,\zeta_j)+E(\epsilon_{ij}|X_j,\zeta_j)$$
$$=\beta_1+\beta_2x_{2ij}+\cdots+\beta_px_{pij}+\zeta_j$$

We see that the covariates for other units in the cluster do not affect the mean response for unit $i$ once we control for the covariates $x_{ij}$ for unit $i$ and the random intercept $\zeta_j$. The covariates $X_j$ for the cluster are then said to be "strictly exogenous given the random intercept".

It follows from $(3.4)$ that the population-averaged or marginal regression (averaged over $\zeta_j$ and $\epsilon_{ij}$ but given $X_j$ ) is linear:

![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.16.png)

We sometimes refer to this relationship between the mean and covariates as the mean structure.


*  **公式分解**
首先，我们来看一个公式，这个公式描述了在给定随机截距 $\zeta_j$ 和协变量 $X_j$ 的条件下，单位 $i$ 的响应变量 $y_{ij}$ 的期望值：

$$
 E(y_{ij}|X_j,\zeta_j)=\beta_1+\beta_2x_{2ij}+\cdots+\beta_px_{pij}+\zeta_j 
$$

这个公式实际上是在说，对于群组 $j$ 中的单位 $i$，其响应变量 $y_{ij}$ 的平均值（或期望值）可以通过以下步骤计算得出：

1. **固定效应部分**：这部分是模型的主体，其中 $\beta_1, \beta_2, \ldots, \beta_p$ 是模型参数，$x_{2ij}, \ldots, x_{pij}$ 是单位 $i$ 的协变量。这部分表示了在不考虑随机截距的情况下，协变量对响应变量的平均影响。

2. **随机截距部分**：这是群组 $j$ 的特定效应，反映了该群组中所有单位共享的、未被协变量捕捉到的影响。这个效应是随机的，意味着它在不同的群组之间是变化的。

* **公式直观理解**

这个公式的核心思想是，每个单位的响应变量不仅受到其自身协变量的影响，还受到其所属群组的特定效应的影响。这种效应是“随机”的，因为它是随机分配给每个群组的，而不是由协变量直接决定的。

* **公式中的“严格外生性”**

当我们说协变量 $X_j$ 对于随机截距是“严格外生的”，我们是在说协变量不会影响到随机截距的值。这意味着，尽管随机截距 $\zeta_j$ 可能会影响到群组内所有单位的响应变量，但它本身并不受这些协变量的直接影响。

* **人口平均或边际回归**

接下来，我们考虑整个人群中的响应变量 $y_{ij}$ 的期望值，这就是所谓的人口平均或边际回归：
$$
 E(y_{ij}|X_j) = \beta_1 + \beta_2x_{2ij} + \cdots + \beta_px_{pij} 
$$
这个公式告诉我们，当我们不考虑随机截距（即，我们对所有可能的群组取平均）时，响应变量 $y_{ij}$ 的期望值只取决于协变量。这意味着，随机截距 $\zeta_j$ 对于整个人口的平均响应没有影响。

* **例子：学校和学生成绩**

假设我们想要研究学校环境对学生成绩的影响。我们有以下数据：

- **群组 $j$**：学校

- **单位 $i$**：学生

- **协变量 $x_{ij}$**：包括学生的家庭背景、学习时间等

- **随机截距 $\zeta_j$**：表示每个学校特有的效应，比如学校的教学质量

在这个模型中，我们假设：
- 一旦我们控制了学生的家庭背景和学习时间，以及学校的教学质量，其他学生的家庭背景和学习时间就不会影响这个学生的成绩。
- 学生的成绩期望值只与他们自己的家庭背景、学习时间以及学校的教学质量有关。

* **总结**

通过这些假设和公式，我们可以更好地理解线性混合模型中的固定效应和随机效应如何影响响应变量的期望值。这些假设帮助我们构建一个合理的模型，以便更准确地估计协变量对响应变量的影响。
希望这个更详细的解释能帮助你更好地理解这些概念。如果还有任何疑问，请随时提问。


### 3.4 Residual covariance structure

It follows from assumptions (3.5) and (3.6) that total residuals or error terms are homoskedastic (having constant variance) given the covariates $X_{j}$,

$$\mathrm{Var}(\xi_{ij}|\mathbf{X_j}) = \mathrm{Var}(\zeta_j+\epsilon_{ij}|\mathbf{X}_j)=\psi+\theta $$

or, equivalently, that the responses , given the covariates, are homoskedastic,

$$\mathrm{Var}(y_{ij}|\mathbf{X}_j)=\psi+\theta $$

The conditional correlation between the total residuals for any two children $i$ and $i'$ of the same mother $j$, given the covariates, also called the residual correlation, is

$$\rho\equiv\mathrm{Cor}(\xi_{ij},\xi_{i^{\prime}j}|\mathbf{X}_j) = \frac\psi{\psi+\theta}$$

where $\psi $ is the corresponding covariance. Thus, $\rho$ is also the conditional or residual intraclass correlation of responses $y_{ij} $ and $y_{i'j'}$ given the covariates:

$$\rho\equiv\mathrm{Cor}(y_{ij},y_{i'j}|\mathbf{X}_j) = \frac{\psi}{\psi+\theta}$$

It is important to distinguish between the intraclass correlation in a model not containing any covariates—sometimes called the *unconditional* intraclass correlation—and the *conditional* or *residual* intraclass correlation in a model containing covariates. The residual covariance structure is shown in matrix form in display 3.2.

让我们深入探讨同质性（Homoscedasticity）和残差相关性（Residual Correlation）的概念，并用更详细的例子来说明。
* **同质性（Homoscedasticity）的详细解释**
同质性是指在给定协变量的条件下，不同观测值的误差项具有相同的方差。这在统计模型中是一个重要的假设，因为它允许我们对模型的误差项进行一致的解释。

* **公式的详细分解**

$$
 \text{Var}(\xi_{ij}|\mathbf{X}_j) = \text{Var}(\zeta_j+\epsilon_{ij}|\mathbf{X}_j) = \psi + \theta 
$$
这个公式告诉我们，给定协变量 $\mathbf{X}_j$ 后，总残差 $\xi_{ij}$ 的方差等于随机截距 $\zeta_j$ 的方差 $\psi$ 加上一级残差 $\epsilon_{ij}$ 的方差 $\theta$。
- $\text{Var}(\xi_{ij}|\mathbf{X}_j)$：这是给定协变量后，总残差 $\xi_{ij}$ 的方差。
- $\text{Var}(\zeta_j+\epsilon_{ij}|\mathbf{X}_j)$：这是给定协变量后，随机截距和一级残差之和的方差。
- $\psi + \theta$：这是两个方差的和，表示总的方差。

* **残差相关性（Residual Correlation）的详细解释**
残差相关性描述了在给定协变量的条件下，同一个群组内不同观测值的残差之间的相关性。

* **公式的详细分解**

$$
 \rho \equiv \text{Cor}(\xi_{ij}, \xi_{i'j}|\mathbf{X}_j) = \frac{\psi}{\psi + \theta} 
$$

这个公式告诉我们，同一个母亲 $j$ 的两个孩子 $i$ 和 $i'$ 的总残差之间的相关性是由随机截距的方差 $\psi$ 与总方差（$\psi + \theta$）的比值决定的。
- $\rho$：这是残差之间的条件相关性。
- $\text{Cor}(\xi_{ij}, \xi_{i'j}|\mathbf{X}_j)$：这是给定协变量后，两个孩子的总残差之间的相关性。
- $\frac{\psi}{\psi + \theta}$：这是随机截距方差与总方差的比值，它决定了残差之间的相关性。

* **无条件与条件内类相关性的区别**

- **无条件内类相关性**：这是在不考虑任何协变量的情况下，群组内观测值之间的相关性。它反映了群组内成员之间的天然联系。
- **条件内类相关性**：这是在控制了协变量后，群组内观测值之间的相关性。它反映了在考虑了已知因素后，群组内成员之间仍然存在的相关性。

让我们通过一个具体的例子来深入理解同质性和残差相关性的概念。

* **例子：学校和学生考试成绩**
假设我们正在进行一项研究，目的是了解不同学校环境对学生考试成绩的影响。我们的数据集包括来自多个学校的学生，每个学生都有自己的考试成绩和一些相关的协变量，比如家庭背景、学习时间等。此外，我们还考虑了每个学校可能具有的独特影响，这可能包括学校的教育资源、教师质量等因素。

* **数据结构**
- **群组 $j$**：学校
- **单位 $i$**：学生
- **协变量 $\mathbf{X}_j$**：包括学生的家庭背景、学习时间等
- **随机截距 $\zeta_j$**：表示每个学校特有的效应，比如学校的教学质量
- **一级残差 $\epsilon_{ij}$**：表示每个学生特有的随机波动

* **模型设定**

我们构建了一个线性混合模型来分析数据：

$$
 y_{ij} = \beta_0 + \beta_1 x_{1ij} + \ldots + \beta_p x_{pij} + \zeta_j + \epsilon_{ij} 
$$

其中：
- $ y_{ij} $ 是第 $ i $ 个学生在第 $ j $ 所学校的考试成绩。
- $ x_{1ij}, \ldots, x_{pij} $ 是第 $ i $ 个学生的协变量。
- $ \beta_0, \ldots, \beta_p $ 是模型参数。
- $ \zeta_j $ 是第 $ j $ 所学校的随机截距。
- $ \epsilon_{ij} $ 是第 $ i $ 个学生的成绩误差。

* **同质性的应用**

在模型中，我们假设给定协变量 $ \mathbf{X}_j $ 后，所有学生的考试成绩残差的方差是恒定的，即同质性。这意味着，无论我们观察哪个学生或哪所学校，只要协变量相同，残差的方差就保持不变。

$$
 \text{Var}(\xi_{ij}|\mathbf{X}_j) = \text{Var}(\zeta_j+\epsilon_{ij}|\mathbf{X}_j) = \psi + \theta 
$$

这个假设允许我们对模型的误差项进行一致的解释，并且使得模型更加可靠。

* **残差相关性的解释**

我们还假设同一个学校内不同学生的成绩残差之间存在一定的相关性，这种相关性是由学校的未观测到的特征（如教学质量）所导致的。

$$
 \rho \equiv \text{Cor}(\xi_{ij}, \xi_{i'j}|\mathbf{X}_j) = \frac{\psi}{\psi + \theta} 
$$

这里，$ \rho $ 表示同一个学校内两个学生成绩残差之间的条件相关性。这个相关性是由随机截距 $ \zeta_j $ 的方差 $ \psi $ 与总方差（$ \psi + \theta $）的比值决定的。

* **无条件与条件内类相关性的比较**

- **无条件内类相关性**：如果我们不考虑任何协变量，只是简单地观察同一个学校内学生的成绩，我们可能会发现学生成绩之间存在一定的天然联系，这就是无条件内类相关性。
- **条件内类相关性**：当我们在模型中控制了学生的协变量（如家庭背景、学习时间）后，我们仍然发现同一个学校内学生的成绩之间存在相关性，这就是条件内类相关性。这种相关性更能反映学校环境对学生成绩的实际影响。

* **总结**
通过这些假设和公式，我们可以更好地理解线性混合模型中的固定效应和随机效应如何影响响应变量的方差和相关性。这些假设帮助我们构建一个合理的模型，以便更准确地估计协变量对响应变量的影响。
希望这个更详细的解释能帮助你更好地理解这些概念。如果还有任何疑问，请随时提问。


### 3.5 Graphical illustration of random-intercept model

A graphical illustration of the random-intercept model, $y_{ij} = \beta_1 +\beta_2 x_{ij} + \zeta_j + \epsilon_{ij}$, with a single covariate $x_{ij}$ is given in figure 3.1.

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.16.1.png" style="width:540px;height:400px;" alt="图片描述">
  <figcaption><strong>Figure 3.1: Illustration of random-intercept model for one mother</strong></figcaption>
</figure>

The bottom line is the population-averaged line, averaged across all mothers, with intercept $\beta_1$ and slope $\beta_2$. Mother $j$ 's regression line has a positive random intercept $\zeta_j$ and therefore a regression line that is above the population-averaged line and parallel to it.

Another graphical illustration of this model with distributional assumptions is given in figure 3.2.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.17.png" style="width:540px;height:270px;" alt="图片描述">
  <figcaption><strong>Figure 3.2: Illustration of random-intercept model for one mother</strong></figcaption>
</figure>

Here the solid line is $E(y_{ij}|x_{ij}) = \beta_1 + \beta_2 x_{ij}$, the population-averaged regression line for the population of all mothers j. The normal density curve centered on this line represents the random-intercept distribution with variance $\psi$ and the hollow circle represents a realization $\zeta_j$ from this distribution for mother j (this could have been placed anywhere along the line). This negative random intercept $\zeta_j$ produces the dotted mother-specific regression line $E(y_{ij}|x_{ij},\zeta_j)=(\beta_1+\zeta_j)+\beta_2 x_{ij}$. This line is parallel to and below the population-averaged regression line. For a mother with a positive $\zeta_j$, the mother-specific regression line would be parallel to and above the population-averaged regression line. Observed responses $y_{ij}=(\beta_1+\zeta_j)+\beta_2 x_{ij}+\epsilon_{ij}$ are shown for two values of $x_{ij}$. The responses are sampled from the two normal distributions [with means $(\beta_1+\zeta_j)+\beta_2 x_{ij}$ and variance $\theta$] shown on the dotted line.



首先，我们从基础的概念开始。
1. 总体平均回归线 (Population-Averaged Regression Line)
首先，我们有一个公式：

$$
E(y_{ij}|x_{ij}) = \beta_1 + \beta_2 x_{ij}
$$

这个公式表示的是，对于所有的母亲（用 j 表示），在给定她们的某个特征 $x_{ij}$（比如年龄、收入等）的情况下，她们的某个结果 $y_{ij}$（比如孩子的身高、体重等）的期望值（平均值）。

- $\beta_1$ 是截距，即使 $x_{ij}$ 为0时，$y_{ij}$ 的期望值。
- $\beta_2$ 是斜率，表示 $x_{ij}$ 每增加一个单位，$y_{ij}$ 期望值增加的量。

**2. 随机截距分布 (Random-Intercept Distribution)**

在这个模型中，我们假设每个母亲都有一个随机的截距 $\zeta_j$，这个截距是从某个分布中随机抽取的，通常假设是从正态分布中抽取，其方差为 $\psi$。
- $\psi$ 表示随机截距的方差，也就是不同母亲之间截距的变异程度。

**3. 个体特定的回归线 (Mother-Specific Regression Line)**

对于每个母亲 j，她的个体特定的回归线可以表示为：

$$
E(y_{ij}|x_{ij},\zeta_j) = (\beta_1 + \zeta_j) + \beta_2 x_{ij}
$$
这里，$\zeta_j$ 是随机截距，它使得每个母亲的回归线与总体平均回归线平行，但位置不同。
- 如果 $\zeta_j$ 是负的，那么个体特定的回归线就会低于总体平均回归线。
- 如果 $\zeta_j$ 是正的，那么个体特定的回归线就会高于总体平均回归线。

**4. 观察到的响应 (Observed Responses)**

实际观察到的数据 $y_{ij}$ 不仅仅是由回归线决定的，还受到随机误差 $\epsilon_{ij}$ 的影响：

$$
y_{ij} = (\beta_1 + \zeta_j) + \beta_2 x_{ij} + \epsilon_{ij}
$$
- $\epsilon_{ij}$ 通常假设是从正态分布中抽取，其方差为 $\theta$。

**5. 正态分布和观察值**

对于每个 $x_{ij}$ 的值，观察到的 $y_{ij}$ 值是从具有特定均值和方差的正态分布中抽取的。均值由个体特定的回归线决定，方差 $\theta$ 通常认为是固定的。

* **例子**

假设：
- $\beta_1 = 10$
- $\beta_2 = 2$
- $\psi = 4$（随机截距的方差）
- $\theta = 1$（误差的方差）
对于一个特定的母亲，假设她的随机截距 $\zeta_j = -2$，则她的个体特定回归线为：

$$
E(y_{ij}|x_{ij},\zeta_j) = (10 - 2) + 2 x_{ij} = 8 + 2 x_{ij}
$$

如果 $x_{ij} = 3$，则她的预测值是：

$$
E(y_{ij}|x_{ij},\zeta_j) = 8 + 2 \times 3 = 14
$$
实际观察到的值 $y_{ij}$ 可能会因为随机误差 $\epsilon_{ij}$ 而略有不同，比如 $y_{ij} = 14 + \epsilon_{ij}$。


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.16.2.png" style="width:540px;height:400px;" alt="图片描述">
  <figcaption><strong>Figure 3.3: Illustration of different $\psi$, $\theta$, and the corresponding residual intraclass correlations $\rho$</strong></figcaption>
</figure>

In the top left panel where $\rho = 0.9$, vertical distances $|\zeta_j|$ of lines from the population average line tend to be greater than distances $|e_{ij}|$ of responses from cluster-specific lines, whereas the opposite is true in the bottom left panel where $\rho = 0.1$. In the right panels, the scatter of lines around the population-average line is of the same magnitude as the scatter of points around their cluster-specific lines, giving $\rho = 0.5$.

## 4 Estimation using Stata

## 5 Coefficients of determination or variance explained

As discussed in section 1.5 for standard linear regression (without random intercepts), the coefficient of determination, or R-squared, can be viewed as the proportional reduction in prediction error variance, comparing the model of interest with the null model that does not include any covariates.

$$R^2 = \frac{\mathrm{TSS-SSE}}{\mathrm{TSS}}$$

Here TSS is the sum of squared prediction errors, or residuals $y_{i}-\bar{y}$, for the null model and SSE is the sum of squared residuals, $y_{i}-\hat{y}_{i}$, for the model of interest. Therefore, $R^{2}$ can be written as

$$R^2 = \frac{\sum_i(y_i-\overline{y})^2-\sum_i(y_i-\widehat{y}_i)^2}{\sum_i(y_i-\overline{y})^2} \approx \frac{\frac{\sum_i(y_i-\overline{y})^2}{N-1}-\frac{\sum_i(y_i-\widehat{y}_i)^2}{N-p}}{\frac{\sum_i(y_i-\overline{y})^2}{N-1}} = \frac{\widehat{\sigma_0^2}-\widehat{\sigma_1^2}}{\widehat{\sigma_0^2}}$$

where $\widehat{\sigma_{1}^{2}}$ is the estimated residual variance for the model of interest and $\widehat{\sigma_{0}^{2}}$ is the estimated residual variance for the null model. The final quantity on the right is also called the adjusted $R$ square . We see that $R^{2}$ is approximately the proportional reduction in the estimated residual variance, where the approximation improves as $N$ increases.
This idea can easily be applied to a linear random-intercept model, where the total residual variance is given by

$$\mathrm{Var}(\zeta_j+\epsilon_{ij}) = \psi+\theta $$

Snijders and Bosker (2012, chap. 7) define a coefficient of determination for this model as the proportional reduction in the estimated total residual variance comparing the model of interest with the null model without covariates.

$$R^2~=~\frac{(\widehat{\psi}_0+\widehat{\theta}_0)-(\widehat{\psi}_1+\widehat{\theta}_1)}{\widehat{\psi}_0+\widehat{\theta}_0}$$

where $\widehat{\psi}_0$ and $\widehat{\theta}_0$ are the estimates for the null model, and $\widehat{\psi}_1$ and $\widehat{\theta}_1$ are the estimates for the model of interest.


* **1. 决定系数 $ R^2 $ 的定义**

决定系数 $ R^2 $，也称为系数的确定性，是衡量线性回归模型拟合优度的一个统计量。它表示模型中自变量对因变量变异性的解释程度。$ R^2 $ 的值通常在0到1之间，值越接近1，表示模型解释的变异性越多。

* **2. $ R^2 $ 的计算**

在标准线性回归（没有随机截距）的情况下，$ R^2 $ 可以通过以下步骤计算：

**步骤 1：计算总平方和（TSS）**
总平方和（TSS）是因变量真实值与其均值之差的平方和。它衡量了如果我们没有任何自变量，只用因变量的均值来预测，会有多大的预测误差。

$$
 \text{TSS} = \sum_{i} (y_i - \bar{y})^2 
$$

- $ y_i $ 是第 $ i $ 个观测值
- $ \bar{y} $ 是所有观测值的均值

**步骤 2：计算残差平方和（SSE）**

残差平方和（SSE）是因变量真实值与模型预测值之差的平方和。它衡量了我们的模型预测与实际观测值之间的误差。

$$
 \text{SSE} = \sum_{i} (y_i - \hat{y}_i)^2 
$$
- $ \hat{y}_i $ 是模型预测的第 $ i $ 个观测值

**步骤 3：计算 $ R^2 $**

$$
 R^2 = \frac{\text{TSS} - \text{SSE}}{\text{TSS}} 
$$
这个公式告诉我们，$ R^2 $ 是模型预测误差减少的比例。

* **3. $ R^2 $ 的另一种表达方式**

$$
 R^2 = \frac{\sum_i(y_i - \overline{y})^2 - \sum_i(y_i - \hat{y}_i)^2}{\sum_i(y_i - \overline{y})^2} 
$$

这个公式可以进一步近似为：

$$
 R^2 = \frac{\widehat{\sigma_0^2} - \widehat{\sigma_1^2}}{\widehat{\sigma_0^2}} 
$$

- $ \widehat{\sigma_0^2} $ 是空模型（没有自变量）的残差方差的估计值
- $ \widehat{\sigma_1^2} $ 是感兴趣模型（包含自变量）的残差方差的估计值
这个公式告诉我们，$ R^2 $ 可以看作是估计的残差方差减少的比例。

* **4. 随机截距模型中的 $ R^2 $**

在随机截距模型中，我们考虑了不同群体（例如不同学校的学生）之间的变异性。这种模型的残差方差包括了群体内和群体间的变异性。

$$
 \text{Var}(\zeta_j + \epsilon_{ij}) = \psi + \theta 
$$

- $ \zeta_j $ 是随机截距，代表了第 $ j $ 个群体的效应
- $ \epsilon_{ij} $ 是随机误差，代表了第 $ j $ 个群体中第 $ i $ 个观测值的误差

* **5. 随机截距模型的 $ R^2 $ 计算**

$$
 R^2 = \frac{(\widehat{\psi}_0 + \widehat{\theta}_0) - (\widehat{\psi}_1 + \widehat{\theta}_1)}{\widehat{\psi}_0 + \widehat{\theta}_0} 
$$

- $ \widehat{\psi}_0 $ 和 $ \widehat{\theta}_0 $ 是空模型（没有自变量）的群体间和群体内残差方差的估计值
- $ \widehat{\psi}_1 $ 和 $ \widehat{\theta}_1 $ 是感兴趣模型（包含自变量）的群体间和群体内残差方差的估计值
这个公式告诉我们，随机截距模型中的 $ R^2 $ 是模型包含自变量后，估计的总残差方差减少的比例。

* **6. 例子**

假设我们想研究不同学校的学生考试成绩。我们有以下数据：
| 学校 | 学生 | 成绩 (y) |
|------|------|----------|
| A    | 1    | 60       |
| A    | 2    | 70       |
| A    | 3    | 80       |
| B    | 1    | 90       |
| B    | 2    | 100      |

**步骤 1：计算 TSS 和 SSE**

假设成绩的平均值 $ \bar{y} $ 是 80。

$$
 \text{TSS} = (60-80)^2 + (70-80)^2 + (80-80)^2 + (90-80)^2 + (100-80)^2 
$$

$$
 \text{TSS} = 400 + 100 + 0 + 100 + 400 = 1000 
$$

如果我们的随机截距模型预测成绩 $ \hat{y}_i $ 为：
- 学校A的预测成绩均值为75
- 学校B的预测成绩均值为95
那么：

$$
 \text{SSE} = (60-75)^2 + (70-75)^2 + (80-75)^2 + (90-95)^2 + (100-95)^2 
$$

$$
 \text{SSE} = 225 + 25 + 25 + 25 + 25 = 350 
$$

**步骤 2：计算 $ R^2 $**

$$
 R^2 = \frac{1000 - 350}{1000} 
$$

$$
 R^2 = 0.65 
$$
这个结果告诉我们，我们的模型能解释65%的成绩变异性。

**步骤 3：随机截距模型的 $ R^2 $**

假设空模型（没有自变量）的残差方差估计值为：
- 学校间：$ \widehat{\psi}_0 = 100 $
- 学校内：$ \widehat{\theta}_0 = 20 $
感兴趣模型（包含自变量）的残差方差估计值为：
- 学校间：$ \widehat{\psi}_1 = 50 $
- 学校内：$ \widehat{\theta}_1 = 10 $

$$
 R^2 = \frac{(100 + 20) - (50 + 10)}{100 + 20} 
$$

$$
 R^2 = \frac{120 - 60}{120} 
$$

$$
 R^2 = 0.5 
$$
这个结果告诉我们，当我们使用包含自变量的模型时，我们能解释总残差方差的一半。这意味着我们的模型在考虑了学校间和学校内的差异后，能显著减少预测误差。



通过这些步骤和例子，我们可以看到 $ R^2 $ 是一个重要的统计指标，它告诉我们模型相比于一个没有任何自变量的模型，能解释多少因变量的变异性。在随机截距模型中，它衡量的是模型在考虑了群体间和群体内变异性后，能解释多少总变异性。这个指标帮助我们评估模型的拟合程度和预测能力。

* **7. 理解 $ R^2 $ 的含义**

$ R^2 $ 值提供了模型解释变异性的比例，但它并不完美。以下是一些关键点，帮助我们更全面地理解 $ R^2 $：
- **范围**：$ R^2 $ 的值在0到1之间，0表示模型不解释任何变异性，1表示模型解释了所有的变异性。
- **改进的指标**：随着模型的改进（例如添加更多的自变量），$ R^2 $ 通常会增加。但是，即使 $ R^2 $ 增加，模型也可能是过度拟合的。
- **过度拟合**：如果模型中添加了太多自变量，可能会导致过度拟合，即模型在训练数据上表现很好，但在新的、未见过的数据上表现不佳。
- **惩罚项**：调整后的 $ R^2 $ 考虑了模型中自变量的数量，对 $ R^2 $ 进行惩罚，以避免过度拟合。

* **8. 调整后的 $ R^2 $**

调整后的 $ R^2 $ 是对普通 $ R^2 $ 的调整，考虑了模型中自变量的数量。计算公式如下：

$$
 \text{Adjusted } R^2 = 1 - (1-R^2)\frac{N-1}{N-p} 
$$

- $ N $ 是观测值的数量。
- $ p $ 是模型中自变量的数量。
调整后的 $ R^2 $ 通常会低于普通的 $ R^2 $，因为它对自变量的数量进行了惩罚。

* **9. 例子：调整后的 $ R^2 $**
假设我们有100个观测值（$ N = 100 $）和5个自变量（$ p = 5 $），并且我们的模型的 $ R^2 $ 是0.8。

$$
 \text{Adjusted } R^2 = 1 - (1-0.8)\frac{100-1}{100-5} 
$$

$$
 \text{Adjusted } R^2 = 1 - 0.2 \times \frac{99}{95} 
$$

$$
 \text{Adjusted } R^2 = 1 - 0.2 \times 1.042 
$$

$$
 \text{Adjusted } R^2 = 1 - 0.2084 
$$

$$
 \text{Adjusted } R^2 = 0.7916 
$$

调整后的 $ R^2 $ 考虑了自变量的数量，提供了一个更现实的模型拟合优度的指标。


First, we fit the null model, also often called the *unconditional model* :

```
quietly xtset momid
xtreg birwt, mle vce(robust)
```
    
    Iteration 0:  Log likelihood = -65475.527
    Iteration 1:  Log likelihood = -65475.486
    
    Random-effects ML regression                          Number of obs    = 8,604
    Group variable: momid                                 Number of groups = 3,978
    
    Random effects u_i ~ Gaussian                         Obs per group:
                                                                       min =     2
                                                                       avg =   2.2
                                                                       max =     3
    
                                                          Wald chi2(0)     =     .
    Log likelihood = -65475.486                           Prob > chi2      =     .
    
                                  (Std. err. adjusted for 3,978 clusters in momid)
    ------------------------------------------------------------------------------
                 |               Robust
           birwt | Coefficient  std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |    3467.97       7.14   485.95   0.000      3453.98     3481.96
    -------------+----------------------------------------------------------------
        /sigma_u |     368.29       7.27                        354.30      382.82
        /sigma_e |     377.66       6.19                        365.73      389.98
             rho |       0.49       0.01                          0.46        0.51
    ------------------------------------------------------------------------------

The estimates for this model were also given under “Null model” in table 3.1. The total variance is estimated as

$$\widehat{\psi}_0+\widehat{\theta}_0 = 368.2866^2+377.6578^2=278260.43$$

For the model including all covariates, whose estimates were given under “Full model” in table 3.1, the total residual variance is estimated as

$$\widehat{\psi}_1+\widehat{\theta}_1 = 338.7674^2+370.6654^2=252156.19$$

It follows that

$$R^2~=~\frac{278260.43-252156.19}{278260.43}=0.10$$

so 10% of the variance is explained by the covariates

Raudenbush and Bryk (2002, chap. 4) suggest considering the proportional reduction in each of the variance components separately. In our example, the proportion of level-2 variance explained by the covariates is

$$R_2^2 = \frac{\widehat{\psi}_0-\widehat{\psi}_1}{\widehat{\psi}_0}=\frac{368.2866^2-338.7674^2}{368.2866^2}=0.15$$

and the proportion of level-1 variance explained by covariates is

$$R_{1}^{2}~=~\frac{\widehat{\theta}_{0}-\widehat{\theta}_{1}}{\widehat{\theta}_{0}}=\frac{377.6578^{2}-370.6654^{2}}{377.6578^{2}}=0.04$$

Stata's versions, which can be obtained by **`xtreg`** with the **`re`** option, are based on interpreting the coefficient of determination in standard linear regression as the squared correlation between $y_{i}$ and its prediction $\hat{y}_{i}$  and generalizing that concept to two-level models, instead of using the notion of proportional reduction in residual variance.

Let us now fit a random-intercept model that includes only the level-2 covariates:

```
quietly xtset momid
xtreg birwt hsgrad somecoll collgrad married black, mle vce(robust)
```
    
    Fitting constant-only model:
    Iteration 0:  Log likelihood = -65475.527
    Iteration 1:  Log likelihood = -65475.486
    
    Fitting full model:
    Iteration 0:  Log likelihood = -65330.325
    Iteration 1:  Log likelihood = -65330.247
    
    Random-effects ML regression                         Number of obs    =  8,604
    Group variable: momid                                Number of groups =  3,978
    
    Random effects u_i ~ Gaussian                        Obs per group:
                                                                      min =      2
                                                                      avg =    2.2
                                                                      max =      3
    
                                                         Wald chi2(5)     = 288.27
    Log likelihood = -65330.247                          Prob > chi2      = 0.0000
    
                                  (Std. err. adjusted for 3,978 clusters in momid)
    ------------------------------------------------------------------------------
                 |               Robust
           birwt | Coefficient  std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
          hsgrad |     131.44      26.66     4.93   0.000        79.18      183.70
        somecoll |     180.69      28.14     6.42   0.000       125.53      235.85
        collgrad |     232.89      27.27     8.54   0.000       179.44      286.35
         married |     114.77      27.24     4.21   0.000        61.37      168.16
           black |    -201.48      29.75    -6.77   0.000      -259.78     -143.17
           _cons |    3216.48      27.04   118.94   0.000      3163.48     3269.49
    -------------+----------------------------------------------------------------
        /sigma_u |     348.14       7.10                        334.51      362.33
        /sigma_e |     377.76       6.19                        365.82      390.09
             rho |       0.46       0.01                          0.43        0.49
    ------------------------------------------------------------------------------

In general, and as we can see from comparing the estimates for this model (given under "Level-2 covariates" in table 3.1) with the estimates from the null model, adding level-2 covariates will reduce mostly the level-2 variance. Consequently, the residual intraclass correlation for the model with only level-2 covariates (0.46) is smaller than the intraclass correlation for the null model (0.49). However, adding level-1 covariates can reduce both variances, as we can see by comparing the estimates for the above model with the full model. The reason is that many level-1 covariates vary both within and between clusters and can hence be decomposed as $x_{ij} = (x_{ij} - \bar x_{.j}) + \bar x_{.j}$, where $x_{ij} - \bar x_{.j}$ only varies at level-1 and $\bar x_{.j}$ only varies at level-2. Note|that the estimated level-2 variance can increase when adding level-1 covariates, potentially producing a negative $R_2^2$.

Keep in mind that the coefficient of determination expresses to what extent the responses can be predicted from the covariates and not how appropriate the model is for the data. Indeed, a data-generating model could very well have a small $R^2$.


**1. 无条件模型（Null Model）与有条件模型（Full Model）**

首先，我们需要理解无条件模型和有条件模型的概念：
- **无条件模型**：这个模型不包含任何自变量（covariates），它只估计因变量在不同群体（如学校、医院）的平均水平是否存在差异。这个模型可以给我们一个基准，用来比较添加自变量后的模型。
- **有条件模型**：这个模型包括了自变量，用来解释因变量的变异性。自变量可以是群体内部的（level-1）或群体间的（level-2）。

**2. 自变量对残差方差的影响**

当我们添加群体间自变量（level-2 covariates）时，通常会减少群体间的残差方差。这是因为这些自变量解释了一部分群体间的差异。
- **类内相关性（Intraclass Correlation）**：这是一个统计指标，用来衡量同一个群体内个体之间的相似度。类内相关性降低意味着个体之间的差异更多是由于群体内部因素，而不是群体间因素。

**3. 添加群体内自变量（Level-1 Covariates）**

当我们添加群体内自变量时，可以同时减少群体内和群体间的残差方差。这是因为这些自变量在群体内和群体间都有变化，可以解释更多的变异性。
- **分解自变量**：群体内自变量可以被分解为两部分：一部分只与群体内的变化有关（$x_{ij} - \bar x_{.j}$），另一部分只与群体间的变化有关（$\bar{x}_{.j}$）。

**4. 公式解释**

$$
x_{ij} = (x_{ij} - \bar x_{.j}) + \bar x_{.j}
$$

- $x_{ij}$：表示第 $i$ 个观测值在第 $j$ 个群体的自变量值。
- $\bar{x}_{.j}$：表示第 $j$ 个群体的自变量的平均值。
- $x_{ij} - \bar{x}_{.j}$：表示第 $i$ 个观测值与它所在群体的平均值的差，这部分只与群体内的变化有关。

**5. 例子**

假设我们研究学生的考试成绩，我们有以下数据：
| 学生 | 学校 | 成绩 |
|------|------|------|
| A    | 1    | 60   |
| B    | 1    | 70   |
| C    | 2    | 80   |
| D    | 2    | 90   |
- **无条件模型**：只考虑成绩的平均值和群体（学校）间的差异。
- **有条件模型**：包括学生的性别、家庭背景等自变量。

**6. 系数的确定性（Coefficient of Determination）**

$R^2$ 是一个统计指标，用来衡量模型中自变量对因变量变异性的解释程度。但它并不表示模型对数据的适用性。
- **$R^2$ 的计算**：$R^2 = 1 - \frac{\text{Residual Variance}}{\text{Total Variance}}$。
- **负的 $R^2$**：如果添加自变量后，群体间残差方差增加，可能会导致负的 $R^2$，这并不意味着模型更差，只是说明自变量对群体间变异性的解释不如群体内。

**7. 总结**
通过添加自变量，我们可以更好地理解数据中的变异性，并减少残差方差。但重要的是要记住，$R^2$ 只是衡量预测能力的一个指标，并不完全反映模型的适用性。有时候，即使 $R^2$ 很小，数据生成模型也可能是合适的。

## 6 Hypothesis tests and confidence intervals

### 6.1 Hypothesis tests for individual regression coefficients

The most commonly used hypothesis test concerns an individual regression parameter, say, $\beta_2$ , with null hypothesis

$$H_{0}:\beta_{2}=0$$

versus the two-sided alternative

$$H_a:\beta_2\neq0$$

Stata reports the test statistic

$$z~=~\frac{\widehat{\beta_2}}{\widehat{\mathrm{SE}}(\widehat{\beta}_{2})}$$

which has an asymptotic standard normal null distribution. For instance, in the output from mixed in section 3.4.2, the z statistic for the coefficient of smoke is -11.41, which gives a two-sided p-value of less than 0.001.

The square of the z statistic is called a Wald statistic,
<!--
$$w~=~\left\{\frac{\widehat{\beta}_{2}}{\widehat{\mathrm{SE}}(\widehat{\beta}_{2})}\right\}^{2}$$
-->

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.16.3.png" style="width:250px;height:140px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

which has an asymptotic $\chi^2(1)$ distribution under the null hypothesis. The distribution has 1 degree of freedom because the null hypothesis imposes one restriction, $\beta_2=0$.


1. 第一步：理解假设检验

假设检验是一种统计方法，用来决定我们是否有足够的证据拒绝一个假设（零假设）。在回归分析中，我们经常测试某个变量（比如 $\beta_2$）是否对模型有显著影响。

**例子**：假设你是一名老师，想要知道学生是否因为吸烟（变量 $\beta_2$）而影响他们的考试成绩。你的零假设 $H_0$ 可能是“吸烟对考试成绩没有影响”，而备择假设 $H_a$ 是“吸烟对考试成绩有影响”。

2. 第二步：检验统计量
Stata 计算了一个叫做 $z$ 的统计量来帮助我们决定是否拒绝零假设。

**公式**：

$ z = \frac{\widehat{\beta_2}}{\widehat{\mathrm{SE}}(\widehat{\beta}_{2})} $$
- $\widehat{\beta}_{2}$ 是回归分析中计算出的 $\beta_2$ 的估计值。
- $\widehat{\mathrm{SE}}(\widehat{\beta_2})$ 是 $\widehat{\beta}_{2}$ 的标准误差。

**例子**：假设你发现吸烟的学生的平均考试成绩比不吸烟的学生低5分。这个5分就是 $\widehat{\beta}_{2}$。如果这个估计的标准误差是1分，那么 $z$ 值就是 $5 / 1 = 5$。

3. 第三步：解释 $z$ 统计量

$z$ 值告诉我们 $\widehat{\beta}_{2}$ 与其标准误差相比有多大。$z$ 值越大，我们越有理由拒绝零假设。

**例子**：如果 $z$ 值是5，这意味着吸烟对成绩的影响比标准误差大5倍。这通常被认为是非常显著的，因为正常情况下，我们不会期望看到这么大的偏离。

4. 第四步：Wald 统计量

Wald 统计量是 $z$ 值的平方，它遵循一个自由度为1的卡方分布。

**公式**：
<!--
$$w~=~\left\{\frac{\widehat{\beta}_{2}}{\widehat{\mathrm{SE}}(\widehat{\beta}_{2})}\right\}^{2}$$
-->

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.16.3.png" style="width:250px;height:140px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

**例子**：在我们的例子中，如果 $z$ 值是5，那么 Wald 统计量 $w$ 就是 $5^2 = 25$。

5. 第五步：渐近分布的局限性

渐近分布假设随着样本量的增加，我们的估计会越来越准确。但在样本量小的情况下，这种假设可能不成立。

**例子**：如果你只调查了10个学生，那么得出的结论可能不太可靠。但如果调查了1000个学生，那么结论就更可信。

6. 第六步：Kenward-Roger 近似

Kenward-Roger 近似是一种更准确的估计自由度的方法，特别是在样本量小的情况下。

**例子**：假设你在一个小城镇的学校进行调查，样本量不大。使用 Kenward-Roger 近似可以帮助你更准确地估计 $z$ 值或 Wald 统计量的显著性。

7. 第七步：应用到实际数据

在实际应用中，我们通常会使用软件（如 Stata）来计算这些统计量，并根据结果做出决策。

**例子**：如果你是一名研究人员，你可能会使用 Stata 来分析吸烟和考试成绩之间的关系。Stata 会计算出 $z$ 值和 Wald 统计量，并告诉你是否有足够的证据拒绝零假设。


The asymptotic distributions treat the standard error as known which is a poor approximation when the number of clusters is small. For this reason, a $t$ (or $F$) distribution is sometimes used instead of the standard normal (or chi-square), where various approximations are used for the error degrees of freedom. The Kenward-Roger approximation generally appears to be preferable to the others (Kenward and Roger 1997; Schaalie, McBride, and Fellingham 2002), and can be obtained using mixed with the reml and dfmethod(kroger) options (dfmethod() was introduced in Stata 14). We now demonstrate the use of these options although the default asymptotic distributions should perform well here because there are thousands of clusters in the birthweight data ($J-q ＞＞ 42$):

* **渐近分布与标准误差**
在统计学中，渐近分布是指当样本量变得非常大时，某些统计量的分布。在回归分析中，我们经常使用渐近分布来近似检验统计量，如 $z$ 值和 Wald 统计量。
* **渐近分布的局限性**：
渐近分布假设标准误差（$\widehat{\mathrm{SE}}$）是已知的，这在样本量很大时是合理的。但是，当样本量较小，特别是聚类数量较少时，这种假设可能不准确。

**例子**：假设你在做一项研究，研究吸烟对小城镇中学生考试成绩的影响。如果这个城镇只有几个学校（聚类），那么每个学校的
吸烟学生数量可能很少。在这种情况下，你估计的标准误差可能不够准确。

* **使用 $t$ 或 $F$ 分布**
由于渐近分布在小样本情况下可能不够准确，我们有时会使用 $t$ 分布或 $F$ 分布来代替标准正态分布或卡方分布。这些分布考虑了标准误差的不确定性。

**例子**：如果你在研究中发现吸烟学生的平均成绩比不吸烟的学生低5分，但这个估计的标准误差是1分，你可能会使用 $t$ 分布来计算 $t$ 值：
$$ t = \frac{5}{1} $$
这将考虑标准误差的不确定性。
* **Kenward-Roger 近似**
Kenward-Roger 近似是一种用于估计自由度的方法，它在小样本情况下通常比其他方法更准确。

**例子**：继续上面的例子，如果你使用 Kenward-Roger 近似来估计自由度，你可能会得到一个更准确的 $t$ 值或 $F$ 值，从而更准确地评估吸烟对考试成绩的影响。

* **在 Stata 中使用 Kenward-Roger 近似**
在 Stata 中，你可以使用 `mixed` 命令和 `reml` 选项来请求受限最大似然估计，然后使用 `dfmethod(kroger)` 选项来指定 Kenward-Roger 近似来估计自由度。

**例子**：假设你在分析一个包含成千上万个聚类（如成千上万个学校）的大数据集。虽然在这种情况下，渐近分布可能已经足够准确，但你仍然可以选择使用 Kenward-Roger 近似来确保准确性：

```
stata
mixed y x1 x2, reml dfmethod(kroger)
```

这里 `y` 是因变量，`x1` 和 `x2` 是自变量。

* **总结**
- 渐近分布在小样本情况下可能不够准确。
- $t$ 分布和 $F$ 分布考虑了标准误差的不确定性，适用于小样本。
- Kenward-Roger 近似是一种在小样本情况下更准确的自由度估计方法。
- 在 Stata 中，你可以使用 `mixed` 命令和相关选项来实现这些方法。


```
mixed birwt smoke male mage hsgrad somecoll collgrad      ///
  married black kessner2 kessner3 novisit pretri2 pretri3 ///
  || momid:, reml dfmethod(kroger) stddeviations
```
    
    Performing EM optimization ...
    
    Performing gradient-based optimization: 
    Iteration 0:  Log restricted-likelihood = -65093.489  
    Iteration 1:  Log restricted-likelihood = -65093.484  
    
    Computing standard errors ...
    
    Computing degrees of freedom ...
    
    Mixed-effects REML regression                      Number of obs    =    8,604
    Group variable: momid                              Number of groups =    3,978
                                                       Obs per group:
                                                                    min =        2
                                                                    avg =      2.2
                                                                    max =        3
    DF method: Kenward–Roger                           DF:          min = 4,002.61
                                                                    avg = 6,074.95
                                                                    max = 7,886.19
                                                       F(13, 7344.24)   =    53.24
    Log restricted-likelihood = -65093.484             Prob > F         =   0.0000
    
    ------------------------------------------------------------------------------
           birwt | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           smoke |    -218.27      18.18   -12.01   0.000      -253.91     -182.64
            male |     120.94       9.57    12.64   0.000       102.19      139.69
            mage |       8.10       1.35     6.02   0.000         5.47       10.74
          hsgrad |      56.85      25.06     2.27   0.023         7.71      105.99
        somecoll |      80.68      27.34     2.95   0.003        27.08      134.28
        collgrad |      90.82      28.03     3.24   0.001        35.87      145.77
         married |      49.92      25.53     1.96   0.051        -0.13       99.98
           black |    -211.41      28.31    -7.47   0.000      -266.91     -155.90
        kessner2 |     -92.92      19.94    -4.66   0.000      -132.01      -53.82
        kessner3 |    -150.85      40.87    -3.69   0.000      -230.96      -70.74
         novisit |     -30.02      65.75    -0.46   0.648      -158.91       98.87
         pretri2 |      92.85      23.21     4.00   0.000        47.35      138.35
         pretri3 |     178.70      51.68     3.46   0.001        77.39      280.01
           _cons |    3117.08      40.94    76.15   0.000      3036.83     3197.33
    ------------------------------------------------------------------------------
    
    ------------------------------------------------------------------------------
      Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
    -----------------------------+------------------------------------------------
    momid: Identity              |
                       sd(_cons) |     339.28       6.31        327.15      351.87
    -----------------------------+------------------------------------------------
                    sd(Residual) |     370.86       3.87        363.35      378.52
    ------------------------------------------------------------------------------
    LR test vs. linear model: chibar2(01) = 1110.69       Prob >= chibar2 = 0.0000
    

The degrees of freedom range between 4,002.61 and 7,886.19, depending on the covariate. These numbers are clearly large enough to rely on the asymptotic standard normal distribution, but we briefly look at
the degrees of freedom in more detail. The estat df command can be used to see the degrees of freedom associated with each test:

```
estat df
```
    
    Degrees of freedom
    ------------------------------
                 |   Kenward–Roger
    -------------+----------------
    birwt        |
           smoke |        7886.193
            male |        7170.964
            mage |        5879.349
          hsgrad |         4106.56
        somecoll |        4210.434
        collgrad |        4416.463
         married |        4173.793
           black |        4002.613
        kessner2 |        7634.771
        kessner3 |        7518.665
         novisit |         7649.74
         pretri2 |        7503.903
         pretri3 |        7467.864
           _cons |        5428.044
    ------------------------------

The degrees of freedom are smaller for level-2 covariates (for example, 4002.6 for black) than for level-1 covariates (for example, 7886.2 for smoke). These agree roughly with the simple approximations discussed by Snijders and Bosker (2012, 95), namely about $J-q-1$ for level-2 covariates (for example, for black, df ≈ 3978 - 5 - 1) and $N-p$ for level-1 covariates (for example, for smoke, df ≈ 8604 - 14), where N is the total sample size and p is the total number of regression parameters, including the intercept.

A likelihood-ratio test (described below) is less commonly used for testing individual regression parameters.

### 6.2 Joint hypothesis tests for several regression coefficients

Consider now the null hypothesis that the regression coefficients of two covariates $x_{2i j}$ and $x_{3 i j}$ are both 0 ,

$$H_0:\beta_2=\beta_3=0$$

versus the alternative hypothesis that at least one of the parameters is nonzero. For example, for the smoking-and-birthweight application, we may want to test the null hypothesis that the quality of prenatal care (as measured by the Kessner index) makes no difference to birthweight (controlling for the other covariates), where the Kessner index is represented by two dummy variables, **`kessner2`** and **`kessner3`**.

We can also test the simultaneous hypothesis that three or more regression coefficients are all 0, but the expression for the Wald statistic becomes convoluted unless matrix expressions are used.

The Wald test for the null hypothesis that the coefficients of the dummy variables **`kessner2`** and **`kessner3`** are both 0 can be performed using the **`testparm`** command. To base the test on robust standard errors, we first reestimate the model by using **`xtreg`** with the **`mle`** and **`vce(robust)`** options:

```
** 3.6.2 Joint hypothesis tests for several regression coefficients

quietly xtset momid
quietly xtreg birwt smoke male mage hsgrad somecoll collgrad  ///
  married black kessner2 kessner3 novisit pretri2 pretri3, mle vce(robust)
testparm kessner2 kessner3
```
    
    . testparm kessner2 kessner3
    
     ( 1)  [birwt]kessner2 = 0
     ( 2)  [birwt]kessner3 = 0
    
               chi2(  2) =   23.75
             Prob > chi2 =    0.0000

We reject the null hypothesis at the 5% level with $w = 23.75$, degrees of freedom $(df)=2, p<0.001$.

If the number of clusters is small, with **`J-q < 42`** (see display 2.1), it is better to perform an approximate F test, which can be obtained by estimating the model using **`mixed`** with the **`reml`** and **`dfmethod(kroger)`** options and then using the **`testparm`** command with the **`small`** option. In addition to providing a finite-sample approximation to the sampling distribution, this approach also has the advantage that the standard errors perform better than ML-based or robust standard errors when the number of clusters is small.

The analogous likelihood-ratio test statistic is

$$L~=~2(l_1-l_0)$$

where $l_1$ and $l_0$ are now the maximized log likelihoods for the models including and excluding both kessner2 and kessner3, respectively. Under the null hypothesis, the likelihood-ratio statistic also has an asymptotic $\chi^2(2)$ null distribution (there is no finite-sample approximation).

A likelihood-ratio test of the null hypothesis that the coefficients of the dummy variables **`kessner2`** and **`kessner3`** are both 0 can be performed by estimating both models by maximum likelihood and then using the **`lrtest`** command:


* **第一步：理解问题背景**
在统计分析中，我们经常需要评估模型中的某些变量是否对结果有显著影响。当我们有多个数据组（或称为“聚类”）时，我们可能会使用多级模型或混合效应模型。如果聚类的数量不多，我们就需要一种更精确的方法来评估这些变量的影响。

* **第二步：近似F检验**

**近似F检验**是一种在聚类数量较少时使用的统计方法。它通过以下步骤进行：
1. **使用`mixed`命令**：这是一个用于估计混合效应模型的命令。`mixed`命令可以让我们考虑数据的层次结构或聚类结构。
2. **`reml`选项**：`reml`代表“限制最大似然”，这是一种估计模型参数的方法，它考虑了缺失数据的情况，通常用于多级模型。
3. **`dfmethod(kroger)`选项**：这是自由度的计算方法。Kroger方法是一种计算自由度的方法，它在小样本情况下表现更好。
4. **`testparm`命令**：这个命令用于测试模型中参数的显著性。当我们加上`small`选项时，它会提供一个针对小样本的近似F检验。

* **第三步：似然比检验**


* (1) 第一步：理解似然比检验
似然比检验是一种统计方法，用于比较两个嵌套模型（一个模型是另一个模型的特殊情况）的拟合优度。这种方法基于两个模型的对数似然值。

* (2) 第二步：完全模型和缩减模型
假设我们有两个模型：
- **完全模型**：$ y = \beta_0 + \beta_1 x + \beta_2 kessner2 + \beta_3 kessner3 + \epsilon $
- **缩减模型**：$ y = \beta_0 + \beta_1 x + \epsilon $

其中：
- $ y $ 是观测到的结果。
- $ x $ 是解释变量。
- $ kessner2 $ 和 $ kessner3 $ 是我们感兴趣的两个虚拟变量。
- $ \epsilon $ 是误差项。

* (3) 第三步：估计两个模型

1. **完全模型**：包含所有感兴趣的变量。
2. **缩减模型**：不包含某些感兴趣的变量。
我们需要使用最大似然估计来估计这两个模型。

* (4) 第四步：计算对数似然值

对于每个模型，我们计算它的对数似然值：
- $ l_1 $ 是完全模型的最大对数似然值。
- $ l_0 $ 是缩减模型的最大对数似然值。

* (5) 第五步：似然比统计量的计算

似然比统计量 $ L $ 由下式给出：

$$
 L = 2(l_1 - l_0) 
$$

这里：
- $ l_1 $ 是完全模型的对数似然值。
- $ l_0 $ 是缩减模型的对数似然值。

* (6) 第六步：零假设和备择假设

在似然比检验中，我们通常测试的零假设是：
- $ H_0 $：两个模型没有显著差异，即 $ \beta_2 = \beta_3 = 0 $。

备择假设是：
- $ H_1 $：两个模型有显著差异，即 $ \beta_2 \neq 0 $ 或 $ \beta_3 \neq 0 $。

* (7) 第七步：卡方分布

在零假设下，似然比统计量 $ L $ 遵循自由度为2的卡方分布（$\chi^2(2)$）。这是因为我们在比较两个模型时，完全模型比缩减模型多出了两个参数（$ \beta_2 $ 和 $ \beta_3 $）。

* (8) 第八步：执行检验
1. **计算 $ L $ 值**：首先计算 $ L = 2(l_1 - l_0) $。
2. **查找卡方分布表**：找到自由度为2的卡方分布的临界值。
3. **比较**：如果计算出的 $ L $ 值大于临界值，则拒绝零假设，认为 $ kessner2 $ 和 $ kessner3 $ 的系数至少有一个不为0。
* (9) 例子
假设：
- $ l_1 = 10 $（完全模型的对数似然值）
- $ l_0 = 8 $（缩减模型的对数似然值）

计算 $ L $ 值：

$$
 L = 2(10 - 8) = 4 
$$

查找自由度为2的卡方分布表，通常在显著性水平0.05时，临界值约为5.99。
因为 $ 4 < 5.99 $，我们不能拒绝零假设，即没有足够的证据表明 $ kessner2 $ 和 $ kessner3 $ 的系数不为0。

* (10) 总结

**似然比检验**是比较两个模型的另一种方法：用于比较两个嵌套模型的拟合优度。
1. **完全模型**：包含所有感兴趣的变量。
2. **缩减模型**：不包含某些感兴趣的变量。
3. **最大似然估计**：这是估计模型参数的另一种方法，它寻找使模型似然函数最大化的参数值。
4. **似然比统计量** $ L $：这是通过计算两个模型的对数似然值之差，然后乘以2得到的。这个统计量在零假设下遵循卡方分布。

* **第四步：执行似然比检验**
执行似然比检验的步骤如下：
1. **估计两个模型**：首先，你需要使用最大似然估计来估计完全模型和缩减模型。
2. **使用`lrtest`命令**：这个命令会计算似然比统计量，并告诉你这个统计量在卡方分布下是否显著。

* **例子**
假设我们有以下两个模型：
- **完全模型**：$ y = \beta_0 + \beta_1 x + \beta_2 kessner2 + \beta_3 kessner3 + \epsilon $
- **缩减模型**：$ y = \beta_0 + \beta_1 x + \epsilon $
其中，$ y $是我们观测到的结果，$ x $是一个解释变量，$ kessner2 $和$ kessner3 $是我们感兴趣的两个虚拟变量（dummy variables），$ \epsilon $是误差项。
1. **估计两个模型**：我们首先使用最大似然估计来估计这两个模型。
2. **计算对数似然值**：对于每个模型，我们计算它的对数似然值，记作$ l_1 $和$ l_0 $。
3. **计算似然比统计量**：我们计算$ L = 2(l_1 - l_0) $。
4. **比较卡方分布**：我们将计算出的$ L $值与自由度为2的卡方分布进行比较，以确定$ kessner2 $和$ kessner3 $是否显著。

* **总结**
- **近似F检验**适用于聚类数量较少的情况，它通过`mixed`命令和`testparm`命令来执行。
- **似然比检验**通过比较包含和不包含某些变量的模型来评估这些变量的显著性。
- **检验统计量** $ L $遵循卡方分布，我们可以通过比较这个统计量与卡方分布的临界值来做出决策。
希望这个更详细的解释能帮助你更好地理解这个问题。如果你还有任何疑问，请随时提问。

```
* Likelihood-ratio test
quietly xtset momid
quietly xtreg birwt smoke male mage hsgrad somecoll collgrad  ///
  married black kessner2 kessner3 novisit pretri2 pretri3, mle
estimates store full
  
quietly xtreg birwt smoke male mage hsgrad somecoll collgrad   ///
  married black novisit pretri2 pretri3, mle
lrtest full .
```
    
    Likelihood-ratio test
    Assumption: . nested within full
    
     LR chi2(2) =  26.90
    Prob > chi2 = 0.0000
    
The likelihood-ratio statistic ($L = 26.90$) is similar to the Wald statistic above ($w = 23.66$), and is even closer to the Wald statistic from maximum likelihood estimation with model-based standard errors (not shown here, $w = 26.94$). This similarity is not surprising because the Wald test is an approximation of the likelihood-ratio test as explained in display 2.2. The display also describes the score test, also known as the Lagrange multiplier test, a less commonly used approximation. All three tests are asymptotically equivalent to each other but may produce different conclusions in small samples. In the current setting with a large number of clusters, the Wald test based on robust standard errors would be our test of choice because, unlike the other methods, it does not rely on correct specification of the residual covariance structure. Note that likelihood-ratio tests for regression coefficients cannot be based on log likelihoods from **``REML``** estimation.

Sometimes it is required to test hypotheses regarding linear combinations of coefficients, as demonstrated in section 1.8. In section 3.7.4, we will encounter a special case of this when testing the null hypothesis that two regression coefficients are equal, or in other words that the difference between the coefficients is 0, a simple example of a contrast. Wald tests of such hypotheses can be performed in Stata by using the **``lincom``** and **``contrast``** commands. If the model has been fit using mixed with the **``reml``** and **``dfmethod(kroger)``** options, **``lincom``** and **``contrast``** with the small option perform -tests, based on the Kenward–Roger error degrees of freedom.

### 6.3 Predicted means and confidence intervals

We can use the **``margins``** command to obtain predicted means for mothers and pregnancies with particular covariate values, for example different combinations of education level and smoking status. If we evaluate the other covariates at particular values of our choice, we obtain adjusted means, called adjusted predictions in Stata. Alternatively, we can obtain what Stata calls predictive margins, the mean birthweight we would obtain if the distributions of the other covariates were the same for all combinations of education and smoking status. As mentioned in section 1.7, in linear models, predictive margins can be obtained by evaluating the other covariates at their means.

The **``margins``** command works only if factor notation is used for the categorical variables for which we want to make predictions (education and smoking status). We therefore define a categorical variable for level of education with appropriate value labels.

```
generate education = hsgrad*1 + somecoll*2 + collgrad*3
label define ed 0 "No HS Degree" 1 "HS Degree" 2 "Some Coll" 3 "College", replace
label values education ed

```

and refit the model, declaring education and smoke as categorical variables by using **``i.education``** and **``i.smoke``**:

```

quietly xtset momid
quietly xtreg birwt i.smoke male mage i.education married black ///
  kessner2 kessner3 novisit pretri2 pretri3, mle vce(robust)
  
```

We then use the **`margins`** command to obtain predictive margins for all combinations of **`smoke`** and **`education`**:

```
margins i.smoke#i.education 
```
    
    Predictive margins                                       Number of obs = 8,604
    Model VCE: Robust
    
    Expression: Linear prediction, predict()
    
    -----------------------------------------------------------------------------------------
                            |            Delta-method
                            |     Margin   std. err.      z    P>|z|     [95% conf. interval]
    ------------------------+----------------------------------------------------------------
            smoke#education |
    Nonsmoker#No HS Degree  |    3430.92      24.67   139.07   0.000      3382.56     3479.27
       Nonsmoker#HS Degree  |    3487.76      13.82   252.32   0.000      3460.67     3514.86
       Nonsmoker#Some Coll  |    3511.60      13.77   254.99   0.000      3484.61     3538.59
         Nonsmoker#College  |    3521.75      12.00   293.42   0.000      3498.22     3545.27
       Smoker#No HS Degree  |    3212.59      26.55   121.01   0.000      3160.55     3264.62
          Smoker#HS Degree  |    3269.43      20.49   159.59   0.000      3229.28     3309.59
          Smoker#Some Coll  |    3293.27      21.87   150.59   0.000      3250.41     3336.14
            Smoker#College  |    3303.42      22.05   149.83   0.000      3260.21     3346.63
    -----------------------------------------------------------------------------------------
  
Here the interaction syntax **``i.smoke#i.education``** was used to specify that we want predictions for all combinations of the values of smoke and education. The standard errors and confidence intervals for the predictions are based on the estimated standard errors from the random intercept model.

We can plot these predictive margins with confidence intervals by using **``marginsplot``** (available from Stata 12).

```
marginsplot, xdimension(education)
```

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.1.png" style="width:590px;height:400px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

### 6.4 Hypothesis test for random-intercept variance

Consider testing the null hypothesis that the between-cluster variance is 0:

$$H_0:\psi=0\quad\mathrm{against}\quad H_a:\psi>0$$

This null hypothesis is equivalent to the hypothesis that $\zeta_{j}=0$ or that there is no random intercept in the model. If this is true, a multilevel model is not required.

Likelihood-ratio tests are typically used with the test statistic

$$L = 2(l_1-l_0)$$

where $l_{1}$ is the maximized log likelihood for the random-intercept model (which includes $\zeta_j$ ) and $l_0$ is the maximized log likelihood for an ordinary regression model (without $\zeta_j$ ). This test is also valid if the restricted log likelihood is used. A correct p-value is obtained by dividing the naive p-value based on the $\chi^2(1)$  by 2, as was discussed in more detail in section 2.6.2 . When ML or REML estimation has been used with model-based standard errors (not the vce(robust) option), the result for the correct test procedure is provided in the last row of output from xtmixed and mixed , where the notation chi2bar2(01) indicates that the p value has been divided by 2. You can see this in section 3.6.1 where we fit the full model by using mixed with the reml option and obtained L = 1120 and p < 0.001.
Alternative tests for the random-intercept variance were described in section 2.6.2 .

## 7 Between and within effects of level-1 covariates

We now turn to the estimated regression coefficients for the random-intercept model with covariates. For births where the mother smoked during the pregnancy, the population mean birthweight is estimated to be 218 grams lower than for births where the mother did not smoke, holding all other covariates constant. This estimate represents either a comparison between children of different mothers, one of whom smoked during the pregnancy and one of whom did not (holding all other covariates constant), or a comparison between children of the same mother, where the mother smoked during one pregnancy and not during the other (holding all other covariates constant). This is neither purely a between-mother comparison (because smoking status can change between pregnancies) nor purely a within-mother comparison (because some mothers either smoke or do not smoke during all their pregnancies).

### 7.1 Between-mother effects

If we wanted to obtain purely between-mother effects of the covariates, we could average the response and covariates for each mother $j$ over children and perform the regression on the resulting cluster means.

$$\frac{1}{n_{j}}\sum_{i=1}^{n_{j}}y_{ij}\quad=\quad\frac{1}{n_{j}}\sum_{i=1}^{n_{j}}(\beta_{1}+\beta_{2}x_{2ij}+\cdots+\beta_{p}x_{pij}+\zeta_{j}+\epsilon_{ij})$$

or

$$\begin{array}{rcl}\overline y_{\cdot j}&=&\beta_1+\beta_2\overline x_{2\cdot j}+\cdots+\beta_p\overline x_{p\cdot j}+\zeta_j+\overline{\epsilon}_{\cdot j}\end{array}\quad(3.11)$$

Here $\bar y_{j}$ is the mean response for mother $j$, $\bar x_{2, j}$ is the mean of the first covariate smoke for mother $j$, etc., and $\bar \varepsilon_{j}$ is the mean of the level-1 residuals in the original regression model $(3.2)$ . The error term $\zeta_j+\bar \varepsilon_{j}$ has population mean $E\left(\zeta_j+\bar \varepsilon_{j}\right)=0$ and is heteroskedastic with variance $\operatorname{Var}\left(\zeta_j+\bar \varepsilon_{j}\right)=\psi+\theta / n_{j}$. Any information on the regression coefficients from within-mother variability is eliminated, and the coefficients of covariates that do not vary between mothers are absorbed by the intercept.

Ordinary least-squares (OLS) estimates $\hat{\beta}^{\mathrm{B}}$ of the parameters $\beta$ in the between regression $(3.11)$ whose corresponding covariates vary between mothers (here all covariates) can be obtained using xtmeg with the be (between) option (robust standard errors not available):



* **随机截距模型**
随机截距模型是一种混合效应模型，它考虑了数据的层次结构。在这个例子中，层次结构是母亲和孩子。每个母亲可以看作是一个群体，而她的孩子是这个群体中的个体。

* **母亲间效应（Between-mother effects）**

**目标：** 我们想要研究不同母亲之间的差异，即不考虑同一个母亲在不同怀孕期的变化。
**方法：** 为了实现这一点，我们对每个母亲 \( j \) 的所有孩子的响应变量（比如出生体重）和协变量（比如母亲是否吸烟）取平均值。
**公式分解：**

1. **原始模型：**

$$
 y_{ij} = \beta_1 + \beta_2 x_{2ij} + \cdots + \beta_p x_{pij} + \zeta_j + \epsilon_{ij} 
$$

   - $ y_{ij} $ 是第 $ j $ 个母亲第 $ i $ 个孩子的出生体重。
   - $ x_{2ij}, \ldots, x_{pij} $ 是第 $ j $ 个母亲第 $ i $ 个孩子的协变量（比如是否吸烟）。
   - $ \beta_1, \beta_2, \ldots, \beta_p $ 是回归系数。
   - $ \zeta_j $ 是随机截距，表示第 $ j $ 个母亲特有的效应。
   - $ \epsilon_{ij} $ 是误差项。

2. **平均值模型：**

$$
 \overline y_{\cdot j} = \beta_1 + \beta_2 \overline x_{2\cdot j} + \cdots + \beta_p \overline x_{p\cdot j} + \zeta_j + \overline \epsilon_{\cdot j} 
$$

   - $ \overline y_{\cdot j} $ 是第 $ j $ 个母亲所有孩子的出生体重的平均值。
   - $ \overline x_{2\cdot j}, \ldots, \overline x_{p\cdot j} $ 是第 $ j $ 个母亲所有孩子的协变量的平均值。
   - $ \overline \epsilon_{\cdot j} $ 是第 $ j $ 个母亲所有孩子的误差项的平均值。

* **误差项的方差：**

$$
 \operatorname{Var}(\zeta_j + \overline \epsilon_{\cdot j}) = \psi + \theta / n_{j} 
$$

- $ \psi $ 是随机截距 $ \zeta_j $ 的方差。
- $ \theta $ 是误差项 $ \epsilon_{ij} $ 的方差。
- $ n_{j} $ 是第 $ j $ 个母亲的孩子数量。

* **解释：**

- 这个模型消除了来自同一个母亲不同怀孕期变化的信息，只考虑了不同母亲之间的差异。
- 如果某些协变量在不同母亲之间没有变化，那么这些协变量的系数会被截距吸收，因为它们无法在模型中进一步解释变异性。

* **普通最小二乘（OLS）估计：**

- 使用 `xtmeg` 命令的 `be`（between）选项可以估计参数 $ \beta $ 的值，这里的协变量在不同母亲之间是变化的。

* **例子：**

假设我们有3个母亲，每个母亲有2个孩子，数据如下：
| 母亲 | 孩子 | 出生体重 (g) | 母亲吸烟 |
|------|------|-------------|----------|
| 1    | 1    | 3400        | 否       |
| 1    | 2    | 3500        | 否       |
| 2    | 1    | 3200        | 是       |
| 3    | 1    | 3100        | 是       |
| 3    | 2    | 3000        | 否       |

1. **计算平均值：**

   - 母亲1：平均出生体重 = (3400 + 3500) / 2 = 3450g，平均吸烟 = 0（都不吸烟）
   - 母亲2：平均出生体重 = 3200g，平均吸烟 = 1（吸烟）
   - 母亲3：平均出生体重 = (3100 + 3000) / 2 = 3050g，平均吸烟 = 0.5（一个吸烟，一个不吸烟）

2. **回归分析：**
   - 使用这些平均值进行回归分析，估计吸烟对出生体重的影响。

```
quietly xtset momid
xtreg birwt smoke male mage hsgrad somecoll collgrad married black kessner2 ///
   kessner3 novisit pretri2 pretri3, be
```
    
    Between regression (regression on group means)  Number of obs     =      8,604
    Group variable: momid                           Number of groups  =      3,978
    
    R-squared:                                      Obs per group:
         Within  = 0.0299                                         min =          2
         Between = 0.1168                                         avg =        2.2
         Overall = 0.0949                                         max =          3
    
                                                    F(13,3964)        =      40.31
    sd(u_i + avg(e_i.)) = 424.7306                  Prob > F          =     0.0000
    
    ------------------------------------------------------------------------------
           birwt | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           smoke |    -286.15      23.23   -12.32   0.000      -331.68     -240.61
            male |     104.94      19.50     5.38   0.000        66.72      143.16
            mage |       4.40       1.51     2.92   0.003         1.45        7.35
          hsgrad |      58.81      25.51     2.30   0.021         8.79      108.83
        somecoll |      85.07      28.13     3.02   0.003        29.91      140.23
        collgrad |      99.88      29.35     3.40   0.001        42.33      157.42
         married |      41.91      26.11     1.61   0.108        -9.27       93.10
           black |    -218.40      28.58    -7.64   0.000      -274.43     -162.37
        kessner2 |    -101.49      37.66    -2.70   0.007      -175.32      -27.67
        kessner3 |    -201.96      79.29    -2.55   0.011      -357.41      -46.51
         novisit |     -51.03     124.21    -0.41   0.681      -294.54      192.49
         pretri2 |     125.48      44.72     2.81   0.005        37.80      213.15
         pretri3 |     241.12     100.66     2.40   0.017        43.78      438.46
           _cons |    3241.45      46.16    70.22   0.000      3150.95     3331.95
    ------------------------------------------------------------------------------


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.2.png" style="width:590px;height:500px;" alt="图片描述">
  <figcaption><strong>Table 3.2: Random-, between-, and within-effects estimates for smoking data (in grams); MLE of random-intercept model (3.2), OLS of (3.11), OLS of (3.12), and MLE of random-intercept model including all cluster means</strong></figcaption>
</figure>

### 7.2 Within-mother effects

If we wanted to obtain purely within-mother effects, we could subtract the between-mother regression (3.11) from the original model (3.2) to obtain the within model.

$$y_{ij}-\overline y_{\cdot j} = \beta_2(x_{2ij}-\overline x_{2\cdot j})+\cdots+\beta_p(x_{pij}-\overline x_{p\cdot j})+\epsilon_{ij}-\overline{\epsilon}_{\cdot j}\quad (3.12)$$

Here the response and all covariates have simply been centered around their respective cluster means. The error term $\varepsilon_{i j}-\bar \varepsilon_{: j}$ has population mean $E(\varepsilon_{i j}-\bar \varepsilon_{: j})=0$ and is heteroskedastic with variance Var$(\varepsilon_{i j}-\bar{\varepsilon}_{: j})=\theta(1-1 / n_j)$. Covariates that do not vary within clusters drop out of the equation because the mean-centered covariate is 0. Importantly, the random intercept $\zeta_j$ drops out for the same reason.

OLS can be used to estimate the within effects $\beta^W$ in $(3.12)$ . The standard errors of the estimated coefficients of covariates that vary little within clusters will be large because estimation is solely based on the within-cluster variability.

Identical estimates of within-mother effects can be obtained by replacing the random intercept $\zeta_j$ for each mother in the original model in $(3.2)$  by a fixed intercept $\alpha_j$. This could be accomplished using dummy variables for each mother and omitting the intercept $\beta_1$ so that $\alpha_j$ represents the total intercept for mother $j$, previously represented by $\beta_1+\zeta_j$. Letting $d_{kj}$ be the dummy variable for the kth mother, ($k=1,\ldots,3,978$), equal to 1 if $j=k$ and 0 otherwise. Then the fixed-effects model can be written as

$$\begin{aligned}y_{ij}&=\quad\beta_2x_{2ij}+\cdots+\beta_px_{pij}+\sum_{k=1}^{3,978}d_{kj}\alpha_k+\epsilon_{ij}\\&=\quad\beta_2x_{2ij}+\cdots+\beta_px_{pij}+\alpha_j+\epsilon_{ij}\end{aligned}$$

The text in the image is as follows:
and estimated by OLS. In this model, all mother-specific effects are accommodated by $\alpha_{j}$, leaving only within-mother variation to be explained by covariates. The coefficients of level-2 covariates can therefore not be estimated, which can also be seen by considering that the set of dummy variables is collinear with any such covariates. For example, black is the sum of the dummy variables for all Black mothers. In practice, it is more convenient to eliminate the intercepts by mean-centering all covariates, as in $(3.12)$, instead of estimating 3,978 intercepts.

The within estimates $\hat{\beta}^{W}$ for the coefficients of covariates that vary within mothers can be obtained using xtmixed with the fe (fixed effects) option:

我们的目标是研究同一个母亲在不同怀孕期的变化，即同一个母亲在不同孩子之间的差异。

* **方法**

为了实现这个目标，我们可以从原始模型中减去母亲间效应的回归模型，得到母亲内部效应的模型。

* 原始模型（模型 3.2）

原始模型是：

$$ y_{ij} = \beta_1 + \beta_2 x_{2ij} + \cdots + \beta_p x_{pij} + \zeta_j + \epsilon_{ij} $$

- $ y_{ij} $ 是第 $ j $ 个母亲第 $ i $ 个孩子的出生体重。
- $ x_{2ij}, \ldots, x_{pij} $ 是第 $ j $ 个母亲第 $ i $ 个孩子的协变量（比如是否吸烟）。
- $ \beta_1, \beta_2, \ldots, \beta_p $ 是回归系数。
- $ \zeta_j $ 是随机截距，表示第 $ j $ 个母亲特有的效应。
- $ \epsilon_{ij} $ 是误差项。

* **母亲间效应的回归模型（模型 3.11）**

母亲间效应的回归模型是：

$$ \overline y_{\cdot j} = \beta_1 + \beta_2 \overline x_{2\cdot j} + \cdots + \beta_p \overline x_{p\cdot j} + \zeta_j + \overline \epsilon_{\cdot j} $$

- $ \overline y_{\cdot j} $ 是第 $ j $ 个母亲所有孩子的出生体重的平均值。
- $ \overline x_{2\cdot j}, \ldots, \overline{x}_{p\cdot j} $ 是第 $ j $ 个母亲所有孩子的协变量的平均值。
- $ \overline \epsilon_{\cdot j} $ 是第 $ j $ 个母亲所有孩子的误差项的平均值。

* **母亲内部效应的回归模型（模型 3.12）**

通过从原始模型中减去母亲间效应的回归模型，我们得到：

$$ y_{ij} - \overline y_{\cdot j} = \beta_2(x_{2ij} - \overline x_{2\cdot j}) + \cdots + \beta_p(x_{pij} - \overline x_{p\cdot j}) + \epsilon_{ij} - \overline \epsilon_{\cdot j} $$

- $ y_{ij} - \overline y_{\cdot j} $ 是第 $ j $ 个母亲第 $ i $ 个孩子的出生体重与该母亲所有孩子的出生体重平均值的差。
- $ x_{2ij} - \overline x_{2\cdot j}, \ldots, x_{pij} - \overline x_{p\cdot j} $ 是第 $ j $ 个母亲第 $ i $ 个孩子的协变量与该母亲所有孩子的协变量平均值的差。
- $ \epsilon_{ij} - \overline \epsilon_{\cdot j} $ 是第 $ j $ 个母亲第 $ i $ 个孩子的误差项与该母亲所有孩子的误差项平均值的差。

步骤：
- 1. **计算每个母亲的平均值：**
   - 对于每个母亲，计算她所有孩子的出生体重的平均值。
   - 对于每个母亲，计算她所有孩子的协变量的平均值。
- 2. **计算每个孩子的差值：**
   - 对于每个孩子，计算他的出生体重与他母亲的出生体重平均值的差。
   - 对于每个孩子，计算他的协变量与他母亲的协变量平均值的差。
- 3. **构建新的回归模型：**
   - 使用这些差值构建新的回归模型，这个模型将只包含母亲内部的变化。
- **误差项的方差：**
$$ \text{Var}(\epsilon_{ij} - \overline{\epsilon}_{\cdot j}) = \theta(1 - \frac{1}{n_j}) $$
- $ \theta $ 是误差项 $ \epsilon_{ij} $ 的方差。
- $ n_j $ 是第 $ j $ 个母亲的孩子数量。
这个方差表达式告诉我们，误差项的方差会随着每个母亲的孩子数量的增加而减小。

* **解释：**

- 这个模型通过将每个孩子的出生体重和协变量减去它们各自的平均值，来消除母亲间的差异。
- 随机截距 $ \zeta_j $ 和不在同一母亲不同孩子之间变化的协变量在模型中消失了，因为它们的平均值为0。
- 误差项 $ \epsilon_{ij} - \overline{\epsilon}_{\cdot j} $ 的方差是异方差的，取决于每个母亲的孩子数量 $ n_j $。



* **固定效应模型的详细解释**

另一种方法是用固定效应模型来估计母亲内部效应。我们可以为每个母亲设置一个固定截距 $ \alpha_j $。
$$ y_{ij} = \beta_2 x_{2ij} + \cdots + \beta_p x_{pij} + \sum_{k=1}^{3,978} d_{kj} \alpha_k + \epsilon_{ij} $$
- $ d_{kj} $ 是第 $ k $ 个母亲的虚拟变量，如果 $ j = k $ 则为1，否则为0。
- $ \alpha_k $ 是第 $ k $ 个母亲的固定截距。
**步骤：**
- 1. **为每个母亲创建虚拟变量：**
   - 对于每个母亲，创建一个虚拟变量，如果孩子属于这个母亲，则虚拟变量为1，否则为0。
- 2. **构建新的回归模型：**
   - 使用这些虚拟变量和协变量构建新的回归模型。
- **优点：**
- 这个模型通过为每个母亲设置一个唯一的截距来考虑母亲特定的效应，只留下母亲内部的变化由协变量来解释。
- **缺点：**
- 如果母亲的数量很多，模型中会有很多虚拟变量，这可能会导致模型估计的复杂性和计算成本。


* **普通最小二乘（OLS）估计**


我们可以使用普通最小二乘法来估计模型（3.12）中的系数 $ \beta^W $。
**优点：**
- OLS是一种简单且常用的估计方法。
**缺点：**
- 如果协变量在同一母亲的孩子之间变化不大，那么估计的标准误差会很大，因为估计完全基于母亲内部的变异性。

* **例子**

假设我们有3个母亲，每个母亲有2个孩子，数据如下：

| 母亲 | 孩子 | 出生体重 (g) | 母亲吸烟 |
|------|------|-------------|----------|
| 1    | 1    | 3400        | 否       |
| 1    | 2    | 3500        | 否       |
| 2    | 1    | 3200        | 是       |
| 3    | 1    | 3100        | 是       |
| 3    | 2    | 3000        | 否       |
- 1. **计算平均值：**
   - 母亲1：平均出生体重 = 3450g，平均吸烟 = 0
   - 母亲2：平均出生体重 = 3200g，平均吸烟 = 1
   - 母亲3：平均出生体重 = 3050g，平均吸烟 = 0.5
- 2. **计算差值：**
   - 母亲1的孩子1：$ y_{11} - \overline y_{\cdot 1} = 3400 - 3450 = -50 $，$ x_{21} - \overline x_{2\cdot 1} = 0 - 0 = 0 $
   - 母亲1的孩子2：$ y_{12} - \overline y_{\cdot 1} = 3500 - 3450 = 50 $，$ x_{22} - \overline x_{2\cdot 1} = 0 - 0 = 0 $
   - 母亲2的孩子1：$ y_{21} - \overline y_{\cdot 2} = 3200 - 3200 = 0 $，$ x_{21} - \overline x_{2\cdot 2} = 1 - 1 = 0 $
   - 母亲3的孩子1：$ y_{31} - \overline y_{\cdot 3} = 3100 - 3050 = 50 $，$ x_{32} - \overline x_{3\cdot 2} = 1 - 0.5 = 0.5 $
   - 母亲3的孩子2：$ y_{32} - \overline y_{\cdot 3} = 3000 - 3050 = -50 $，$ x_{32} - \overline x_{3\cdot 2} = 0 - 0.5 = -0.5 $
- 3. **回归分析：**
   - 使用这些差值进行回归分析，估计吸烟对出生体重的影响。


```
quietly xtset momid
xtreg birwt smoke male mage kessner2 kessner3 novisit pretri2 pretri3, ///
   fe vce(robust)
```
    
    Fixed-effects (within) regression               Number of obs     =      8,604
    Group variable: momid                           Number of groups  =      3,978
    
    R-squared:                                      Obs per group:
         Within  = 0.0465                                         min =          2
         Between = 0.0557                                         avg =        2.2
         Overall = 0.0546                                         max =          3
    
                                                    F(8, 3977)        =      26.78
    corr(u_i, Xb) = -0.0733                         Prob > F          =     0.0000
    
                                  (Std. err. adjusted for 3,978 clusters in momid)
    ------------------------------------------------------------------------------
                 |               Robust
           birwt | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           smoke |    -104.55      31.64    -3.30   0.001      -166.58      -42.52
            male |     125.64      11.08    11.34   0.000       103.92      147.35
            mage |      23.16       3.08     7.52   0.000        17.12       29.20
        kessner2 |     -91.49      25.56    -3.58   0.000      -141.61      -41.38
        kessner3 |    -128.09      49.02    -2.61   0.009      -224.20      -31.98
         novisit |      -4.81      91.23    -0.05   0.958      -183.68      174.07
         pretri2 |      81.29      28.68     2.83   0.005        25.06      137.52
         pretri3 |     153.06      61.63     2.48   0.013        32.23      273.89
           _cons |    2767.50      88.42    31.30   0.000      2594.14     2940.86
    -------------+----------------------------------------------------------------
         sigma_u |  440.05052
         sigma_e |  368.91787
             rho |  .58725545   (fraction of variance due to u_i)
    ------------------------------------------------------------------------------

The estimates of the within-mother effects were also reported under “Within effects” in table 3.2. The estimated coefficient of `smoke`, $\widehat \beta_{2}^{W}=-105$ grams, is dramatically smaller, in absolute value, than the corresponding ML estimate for the random-intercept model, $\widehat \beta_{2}^{\mathrm{ML}}=-218$ grams. The within-effect can be interpreted as the difference in mean birthweight between births for a given mother who changes smoking status between pregnancies, conditioning on the level-1 covariates. Level-2 covariates, whether observed or unobserved, are implicitly controlled for because mother is held constant in the comparison, along with all her characteristics. Therefore each mother truly serves as her own control.

Although $\beta_{1}$ is eliminated in the fixed-effects approach and there is no concept of a random-intercept standard deviation $\sqrt{\psi}$, the output from **`xtreg,fe`** provides estimates of both parameters ($2767.504$ next to `cons` and $440.05052$ next to `sigma_u`^{2}).

The original random-intercept model in (3.2) implicitly assumes that the between and within effects of the set of covariates that vary both between and within mothers are identical because the between-mother model (3.11) and the within-mother model (3.12) derived from the random-intercepts model (3.2) have the same regression coefficients, $\beta=(\beta_{1}, \beta_{2}, \ldots, \beta_{p})^{\prime}$. The estimators for the random-intercept model therefore use both within- and between-mother information. Not surprisingly therefore, the estimate of $\beta_{2}$ in the smoking-and-birthweight example lies between the within-effect estimate and between-effect estimate, $-105>-218>-286$.

这段话主要解释了固定效应模型和随机截距模型估计结果之间的差异，以及这些差异的意义。让我们一步步分析：

**1. 固定效应模型的估计结果**

**固定效应模型估计的“within-mother effects”（母亲内部效应）**：
- 在固定效应模型中，对于吸烟（`smoke`）的估计系数（$ \widehat \beta_{2}^{W}$）是 -105 克。这意味着，在控制了其他变量后，如果同一个母亲在不同怀孕期改变吸烟状态，她的孩子的平均出生体重预期会相差 105 克。

**2. 随机截距模型的估计结果**

**随机截距模型的估计**：
- 随机截距模型给出的吸烟对孩子出生体重影响的估计（$\widehat \beta_{2}^{\mathrm{ML}}$）是 -218 克。这比固定效应模型的估计结果的绝对值要大。

**3. 母亲内部效应的解释**

**母亲内部效应的含义**：
- 固定效应模型中的“within-mother effects”可以理解为，在考虑了一级协变量（level-1 covariates，即每次怀孕特有的协变量）后，对于同一个母亲在不同怀孕期改变吸烟状态的孩子们的平均出生体重的差异。
- 这种模型考虑了所有母亲特有的、在比较中保持不变的二级协变量（level-2 covariates），无论是观察到的还是未观察到的。因此，每个母亲都作为她自己的对照组。

**4. 固定效应模型的参数估计**

**固定效应模型的参数**：
- 尽管在固定效应方法中消除了 $\beta_1$（模型的截距项），并且没有随机截距标准差（$\sqrt \psi$）的概念，`xtreg, fe`命令的输出还是提供了这两个参数的估计值：
  - `cons`（常数项）旁边的值是 2767.504，这是在所有协变量为零时的预期出生体重。
  - `sigma_u`² 旁边的值是 440.05052，这是不同母亲之间变异性的估计。

**5. 随机截距模型的假设**

**随机截距模型的假设**：
- 随机截距模型（3.2）隐含地假设在不同母亲之间和同一个母亲不同怀孕期内，协变量的效应是相同的，因为从随机截距模型（3.2）推导出的母亲间模型（3.11）和母亲内部模型（3.12）具有相同的回归系数 $\beta=(\beta_1, \beta_2, \ldots, \beta_p)^{\prime}$。
- 因此，随机截距模型的估计器使用了母亲之间和母亲内部的信息。

**6. 吸烟对出生体重影响的估计值比较**

**吸烟对出生体重影响的估计值**：
- 在吸烟和出生体重的例子中，$\beta_2$ 的估计值介于母亲内部效应估计值和母亲间效应估计值之间，即 -105 > -218 > -286。

- 总结

这段话说明了固定效应模型和随机截距模型在估计同一个协变量（如吸烟）对因变量（如出生体重）影响时的差异。固定效应模型只关注同一个母亲在不同怀孕期的变化，而随机截距模型同时考虑了不同母亲之间的差异以及同一个母亲在不同怀孕期的变化。**因此，固定效应模型的估计结果通常更适用于评估协变量在个体内部变化的影响。**

## 8 Fixed versus random effects revisited

In section 2.8, we discussed whether the effects of clusters should be treated as random or fixed in models without covariates, and we revisit the issues involved in making this decision in table 3.3. We have previously argued that the decision depends on whether inferences are for the population of clusters or only for the clusters included in the sample. This appears as the first question in table 3.3.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.9.28.3.png" style="width:590px;height:630px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

To make inferences regarding the population of clusters (or the datagenerating mechanism for the clusters), the random-effects model must be used.

Turning to the next question in the table, the random-effects model allows estimation of coefficients of cluster-level covariates, unlike the fixed好的，以下是图片中的英文原文：
effects model. However, these inferences require a sufficient number of clusters and assumptions regarding the random-intercept distribution. The most important of these assumptions that is relaxed by the fixed-effects approach is level-2 exogeneity of level-1 covariates (lack of correlation between $\zeta_j$ and $\overline{x}_{j}$ or no cluster-level unobserved confounding). Another assumption made by the random-effects approach is that $\zeta_j$ has a constant variance $\psi$, although this assumption can be relaxed using robust standard errors if there are enough clusters. The standard assumption of a normal distribution for $\zeta_j$ is actually not required for consistent estimation of regression coefficients.

The main reason for using a fixed-effects approach is to estimate the within-cluster effects of covariates, free from bias due to unobserved cluster-level confounding. Although this endogeneity problem can be addressed using a random-effects model that also includes the cluster means of level-1 covariates, this approach yields inconsistent estimates of parameters pertaining to the between-cluster relationships. Fortunately, there is no endogeneity problem due to omitted covariates when estimating a treatment or intervention effect in a randomized experiment.

In section 2.10.3, we stressed that the standard error for the estimator of the mean $\beta$ in a random-effects model without covariates is for the population of clusters (allowing for random sampling of clusters), whereas the standard error in the corresponding fixed-effects model is for the particular clusters at hand. Generalization to the population of clusters is clearly a necessity for inferences regarding the coefficients of cluster-level covariates (which require a random-effects model). Should we worry about the standard errors from the fixed effects model because they pertain only to the clusters that happen to be in the sample? Not if the coefficients of the level-1 covariates are assumed to be identical across clusters (as they have been in this chapter). In this case, the same relationship is assumed to hold for other clusters not included in the sample and generalizes in that sense. We will see in the next chapter that the random-effect approach allows the assumption of identical within-cluster relationships to be relaxed by including random slopes.

While the cluster-specific intercepts are model parameters in the fixed-effects model and can be estimated via the dummy-variable approach, they are just random residuals in the random-effects model, and only their variance is estimated as a model parameter. However, the $c_{j}$ can be predicted by empirical Bayes (EB) after estimating the model parameters. If inference regarding the cluster-specific effects are of interest, cluster sizes should not be too small, and the random-effects approach performs better with small cluster sizes than the fixed-effects approach because of shrinkage or partial pooling as discussed in section 2.11.2.

For inferences regarding regression coefficients, there are no particular requirements regarding cluster sizes in either fixed or random-effects models. In fixed-effects models, singletons clusters (of size 1) provide no information on the regression coefficients ($\beta_2,\cdots,\beta_p$), and it makes no difference whether or not they are discarded. In contrast, it is important not to discard singleton clusters in random-effects models because they do provide some information on the regression coefficients and total residual variance. Singletons obviously provide no information on the decomposition of the total residual variance into $\psi$ and $\theta$.

The fixed-effects model is much less parsimonious than the random-intercept model because it includes one parameter $\alpha_j$ for each cluster, whereas the random-intercept model has only one parameter $\psi$ for the variance of the random intercepts $\xi_j$. Eliminating the $\alpha_j$ by mean centering, as shown in section 3.7.2, simplifies the estimation problem but does not make the estimates of the remaining parameters any more efficient.

As the cluster-size becomes large, the random effects estimators for the coefficients of level-1 covariates approach their fixed-effects counterparts, and the coefficients of level-2 covariates become consistent even under level-2 endogeneity of the level-1 covariates. Unfortunately, both the fixed-effects and random-effects models assume exogeneity of the covariates with respect to the level-1 residual $\epsilon_{ij}$ (level-1 exogeneity). It is not straightforward to check for level-1 endogeneity. To correct for level-1 endogeneity, external instrumental variables are usually required (see section 5.7).



**固定效应模型和随机效应模型的区别，以及它们在模型中处理群体（如学校、医院或母亲群体）效应时的应用**

### 第一部分：模型选择的决策

**是否将群体效应视为随机或固定**：
- 在没有协变量的模型中，决定是将群体效应视为随机还是固定，取决于推断的目标是针对群体总体还是仅针对样本中的群体。
- 如果目标是针对群体总体（或群体的数据生成机制），则必须使用随机效应模型。

- **例子**：假设你是一位教育研究员，想要研究不同学校的学生数学成绩。每个学校都有其独特的文化和教学方法，这可能会影响学生的成绩。如果你只关心从你研究的几所学校中得到的结果，那么固定效应模型可能更合适。但如果你想从这几所学校推断出所有学校的情况，那么随机效应模型将更适合。
- **决策依据**：选择哪种模型取决于你的目标。如果你的目标是推广到所有可能的学校（即群体总体），那么随机效应模型是必要的。

### 第二部分：模型的特点和应用

**随机效应模型**：

- 允许估计群体水平协变量的系数，这是固定效应模型所不允许的。
- 这些推断需要足够数量的群体，并假设随机截距分布的某些条件。最重要的假设是一级协变量的二级外生性（即 $\zeta_j$ 与 $\overline{x}_j$ 之间没有相关性，或者没有群体水平的未观测混杂因素）。
- 随机效应方法的另一个假设是 $\zeta_j$ 具有恒定的方差 $\psi$，但如果有足够的群体，即使使用稳健标准误差，这个假设也可以放宽。

- **例子**：继续上面的例子，随机效应模型就像你认为每所学校都有其独特的教学质量，但这些质量是随机分布的。你感兴趣的是学校类型（如公立或私立）如何普遍影响学生的成绩。
- **特点**：这种模型允许你估计学校特征（如学校类型）对学生成绩的影响，但它需要足够的学校数量，并假设这些学校是随机选取的。

**固定效应模型**：

- 主要用于估计协变量在群体内部效应，不受未观测群体水平混杂因素的影响。
- 尽管可以通过包含群体平均数的随机效应模型来解决这种内生性问题，但这种方法会产生不一致的群体间关系的参数估计。

- **例子**：在同一个学校内，如果学校决定实施一种新的教学方法，固定效应模型可以帮助你了解这种变化如何影响学生的成绩，同时控制学校的独特因素。
- **特点**：这种模型专注于学校内部的变化，可以控制那些未观测到的学校特定因素。

**实验中的处理效应**：

- 在随机实验中估计处理或干预效应时，由于忽略了协变量，因此不存在由于遗漏协变量而导致的内生性问题。

**标准误差的解释**：

- 随机效应模型中的标准误差是针对群体总体的（允许群体的随机抽样），而固定效应模型中的标准误差是针对手头特定群体的。
- 如果假设一级协变量的系数在群体间是相同的（如本章所讨论的），则不需要担心固定效应模型的标准误差仅针对样本中的群体。在这种情况下，相同的关系被假设适用于未包含在样本中的其他群体，并且在这种意义上具有普遍性。

**模型参数**：

- 在固定效应模型中，群体特定的截距是模型参数，可以通过虚拟变量方法估计。
- 在随机效应模型中，这些截距只是随机残差，只有它们的方差被估计为模型参数。

**模型的简洁性**：

- 固定效应模型比随机截距模型更不简洁，因为它为每个群体包含一个参数 $\alpha_j$，而随机截距模型只有一个参数 $\psi$ 用于随机截距的方差。
- **例子**：固定效应模型可能会变得复杂，因为它需要为每所学校估计一个特定的效应。相比之下，随机效应模型只需要估计随机效应的方差，这使得模型更简单。


**群体大小的影响**：

- 随着群体大小的增加，随机效应模型中一级协变量系数的估计值接近其固定效应对应值，即使在一级协变量的二级内生性下，二级协变量的系数也变得一致。
- 不幸的是，固定效应和随机效应模型都假设协变量与一级残差 $\epsilon_{ij}$ 无关（一级外生性）。检查一级内生性并不直接。为了纠正一级内生性，通常需要外部工具变量。

- **例子**：如果你的数据中只有少数几所学校，随机效应模型可能不会很有效，因为它需要足够的群体数量来准确估计随机效应的分布。在这种情况下，固定效应模型可能更合适。
- **选择依据**：如果你的数据集中群体数量较少，或者你只关心这些特定的群体，固定效应模型可能更合适。

### 第三部分：模型选择的具体应用


**随机效应模型的应用**：

- 当我们想要从样本中的群体推广到群体总体时，随机效应模型是合适的。
- 如果我们对群体水平的协变量如何影响群体内部的个体感兴趣，随机效应模型可以提供这种估计。

**固定效应模型的应用**：

- 如果我们只对样本中的特定群体内的变化感兴趣，并且我们担心存在未观测到的群体水平的混杂因素，那么固定效应模型是更好的选择。
- 固定效应模型通过为每个群体分配一个固定的截距，可以控制所有时间不变的群体特定效应。

### 第四部分：模型的比较和选择

**群体大小的影响**：

- 在固定效应模型中，如果群体大小为1（即只包含一个观测值的群体），那么这个群体对于估计回归系数没有提供任何信息，可以丢弃它们而不影响估计结果。
- 相比之下，在随机效应模型中，即使是大小为1的群体也提供了关于回归系数和总残差方差的一些信息，因此不应丢弃这些群体。

**模型的简洁性**：

- 固定效应模型由于为每个群体估计一个参数，因此模型较为复杂。
- 随机效应模型通常更为简洁，因为它只估计随机截距的方差这一个参数。

**模型的效率**：

- 通过均值中心化可以简化固定效应模型的估计问题，但这并不会提高剩余参数的估计效率。
- 随着群体大小的增加，随机效应模型的估计量会逐渐接近固定效应模型的估计量。

**模型的假设**：

- 两种模型都假设协变量与一级残差（$\epsilon_{ij}$）不相关（一级外生性）。
- 如果存在一级内生性问题，通常需要使用外部工具变量来纠正。

### 第五部分：模型的预测和应用

**预测**：

- **固定效应模型**：可以通过为每个群体分配一个特定的截距来进行预测。
- **随机效应模型**：可以通过估计随机截距的分布来进行预测。

**应用**：

- **固定效应模型**：适用于政策评估、处理效应分析等，特别是当我们有理由相信群体特定效应可能会影响结果时。
- **随机效应模型**：适用于多层次模型、群体随机试验等，特别是当我们想要从样本推广到总体时。

**预测**：
- **例子**：如果你使用固定效应模型发现实施新教学方法可以提高成绩，你可以预测在这所学校实施这种方法将如何影响学生。随机效应模型可以帮助你预测这种效果在其他学校的可能性。
- **应用**：固定效应模型适用于当你想要控制特定群体的所有独特因素时，而随机效应模型适用于当你想要从样本推广到总体时。

### 第六部分：模型的选择和实际操作

**实际操作**：

- **固定效应模型**：可以通过创建虚拟变量或使用特定估计技术来实现。
- **随机效应模型**：可以通过最大似然估计或贝叶斯方法来实现。

**选择建议**：

- 考虑研究设计、数据结构、群体大小和研究目标。
- 考虑模型的假设和限制，以及是否需要对未观测到的混杂因素进行调整。

- **例子**：在实际操作中，固定效应模型可以通过创建虚拟变量（比如为每所学校一个变量）来实现。随机效应模型通常需要更复杂的统计软件来估计。
- **选择建议**：选择哪种模型取决于你的数据结构、研究目标和对模型假设的舒适度。

### 实际应用中的考虑

1. **数据结构**：如果群体内部的变异性很大，而群体之间的变异性相对较小，固定效应模型可能更合适。如果群体之间的变异性很大，随机效应模型可能更合适。

2. **推断目标**：如果目标是进行群体间的比较，随机效应模型可能更合适。如果目标是研究群体内的变化，固定效应模型可能更合适。

3. **群体大小**：如果群体很小，随机效应模型可能由于收缩效应（shrinkage）或部分汇总（partial pooling）而表现更好。

4. **模型假设**：如果对随机截距的分布有明确假设，随机效应模型可能更合适。如果没有这些假设，或者担心群体特定的效应可能与协变量相关，固定效应模型可能更合适。

### 总结
选择固定效应模型还是随机效应模型取决于研究的具体目标、数据结构、以及对模型假设的考虑。在实际应用中，可能需要尝试不同的模型，并比较它们的估计结果和解释能力。


### 11 Summary

We discussed linear random-intercept models, which are important for investigating the relationship between a continuous response and a set of covariates when the data have a clustered or hierarchical structure. Topics included hypothesis testing, different kinds of coefficients of determination, the choice between fixed- and random-effects approaches, model diagnostics, consequences of using standard regression for clustered data, and power and sample-size determination.


