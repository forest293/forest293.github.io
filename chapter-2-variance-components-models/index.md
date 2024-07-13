# [Multilevel  Longitudinal Models] Chapter 2 ：Variance-components models



## 1 Introduction


In this chapter, we consider the simple situation of clustered data without covariates or explanatory variables. For this setting, we introduce a two-level linear model which we refer to as a variance-components model. Other names for this model include one-way random-effects ANOVA or unconditional hierarchical linear model (where “unconditional” refers to not including and therefore not conditioning on covariates). The variance-components model is important in its own right and is also useful for introducing and motivating the notions of random effects and variance components. We also describe basic principles of estimation and prediction in this simple setting. However, this means that some parts of the chapter may be a bit demanding, and you could skip sections 2.10 and 2.11 on first reading.

In clustered data, it is usually important to allow for dependence or correlations among the responses observed for units belonging to the same cluster. For example, the adult weights of siblings are likely to be correlated because siblings are genetically related to each other and have usually been raised within the same family. Variance-components models are designed to model and estimate such within-cluster correlations.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.7.png" style="width:450px;height:570px;" alt="图片描述">
  <figcaption><strong>Figure 2.1: Examples of clustered data</strong></figcaption>
</figure>

**In this chapter, we consider the simple situation of clustered data without covariates or explanatory variables.** For this setting, we introduce a two-level linear model which we refer to as a variance-components model. Other names for this model include one-way random-effects ANOVA or unconditional hierarchical linear model (where “unconditional” refers to not including and therefore not conditioning on covariates). **The variance-components model is important in its own right and is also useful for introducing and motivating the notions of random effects and variance components. We also describe basic principles of estimation and prediction in this simple setting.** However, this means that some parts of the chapter may be a bit demanding, and you could skip sections 2.10 and 2.11 on first reading.

## 2 How reliable are peak-expiratory-flow measurements?

The data come from a reliability study conducted by Professor Martin Bland using 17 of his family and colleagues as subjects. The purpose was to illustrate a way of assessing the quality of two instruments for measuring people’s peak-expiratory-flow rate (PEFR). The PEFR, which is roughly speaking how strongly subjects can breathe out, is a central clinical measure in respiratory medicine.

The subjects had their PEFR measured twice (in liters per minute) with the standard Wright peak-flow meter and twice with the new Mini Wright peak-flow meter. The methods were used in random order to avoid confounding practice (prior experience) effects with method effects. If the new method agrees sufficiently well with the old, the old method may be replaced with the more convenient Mini meter. Interestingly, the paper reporting this study (Bland and Altman 1986) is the most cited paper in The Lancet, one of the most prestigious medical journals.

The data are presented in table 2.1 and are stored in pefr.dta in the same form as in the table, with the following variable names:

* id: subject identifier
* wp1: Wright peak-flow meter, occasion 1
* wp2: Wright peak-flow meter, occasion 2
* wm1: Mini Wright flow meter, occasion 1
* wm2: Mini Wright flow meter, occasion 2


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.8.png" style="width:450px;height:570px;" alt="图片描述">
  <figcaption><strong>Peak-expiratory-flow rate measured on two occasions using both the Wright and the Mini Wright peak-flow meters</strong></figcaption>
</figure>


## 3 Inspecting within-subject dependence

The first and second recordings on the Mini Wright peak-flow meter can be plotted against the subject identifier with a horizontal line representing the overall mean:

```
generate mean_wm = (wm1+wm2)/2
summarize mean_wm
```

        Variable |        Obs        Mean    Std. dev.       Min        Max
    -------------+---------------------------------------------------------
         mean_wm |         17    453.9118    111.2912      243.5        650


```
twoway (scatter wm1 id, msymbol(circle))                                ///
       (scatter wm2 id, msymbol(circle_hollow)),                        ///
       xtitle(Subject id) xlabel(1/17) ytitle(Mini Wright measurements) ///
       legend( order(1 "Occasion 1" 2 "Occasion 2")) yline(453.9118)    
```


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.9.png" style="width:650px;height:470px;" alt="图片描述">
  <figcaption><strong>Figure 2.2: First and second measurements of peak-expiratory-flow using Mini Wright meter versus subject number (the horizontal line represents the overall mean)</strong></figcaption>
</figure>

![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.20.png)

It may be tempting to model the response of unit (here measurement occasion) in cluster (here subject) by using a standard regression model without covariates:

$$y_{ij}=\beta+\xi_{ij}$$

where $\xi_{ij}$ are residuals, disturbances, or error terms that are uncorrelated over both subjects and occasions (the Greek letter is pronounced “xi”).

However, it is clear from the figure that repeated measurements on the same subject tend to be closer to each other than to the measurements on a different subject. Indeed, if this were not the case, the Mini Wright peak-flow meter would be useless as a tool for discriminating between the subjects in this sample. Because there are large differences between subjects (for example, compare subjects 9 and 15) and only small differences within subjects, the responses for occasions 1 and 2 on the same subject tend to lie on the same side of the overall mean, shown as a horizontal line in the figure, and are therefore positively correlated. We expect that the underlying population covariance, defined as the expectation of products of deviations from the expectation, is also positive. See also section 2.4.4

We can also see that there is within-subject dependence by considering prediction of a subject’s response at occasion 2 if we only know all the subjects’ responses at occasion 1. If the response for a given subject at occasion 2 were independent of his or her response at occasion 1, a good prediction would be the mean response at occasion 1 across all subjects. However, it is clear that a much better prediction here is the subject’s own response at occasion 1 because the responses are highly dependent within subject.

The within-subject dependence is due to between-subject heterogeneity. If all subjects were more or less alike (for example, pick subjects 2, 4, 10, 11, 14, and 17), there would be much less within-subject dependence.

## 4 The variance-components model

### 4.1 Model specification

As we saw in the previous section and in figure 2.2, it is unreasonable to assume that the deviations $\xi_{ij}$ of $y_{ij}$ from the population mean $\beta$ are uncorrelated within subjects in the regression model.

We can model the within-subject dependence by splitting the residual $\xi_{ij}$ into two uncorrelated components: a permanent component $\xi_j$ ($\xi$ is pronounced “zeta”),which is specific to each subject $j$ and constant across occasions $i$; and an idiosyncratic component $\epsilon_{ij}$ which is specific
to each occasion $i$ for each subject $j$. We then obtain a variancecomponents model,

$$y_{ij}=\beta+\underbrace{\zeta_j+\epsilon_{ij}}_{\xi_ij}$$

首先，我们来分两部分来理解这个模型。
* **第一部分：理解基本概念**
1. **回归模型中的偏差**：在统计学中，回归模型是用来预测一个变量（因变量）基于一个或多个其他变量（自变量）的关系。在这个模型中，我们关注的是因变量 $ y_{ij} $ 与一个总体均值 $ \beta $ 的偏差，记作 $ \xi_{ij} $。
2. **相关性问题**：当我们假设 $ \xi_{ij} $ 在同一个受试者（subject）内部是不相关的，这通常是不合理的。因为同一个受试者在不同时间点的测量值很可能是有关联的。
3. **分解偏差**：为了解决这个问题，我们可以将偏差 $ \xi_{ij} $ 分解成两个不相关的部分：
   - **永久性偏差** $ \zeta_j $：这个偏差是针对每个受试者 $ j $ 特有的，并且在所有时间点 $ i $ 上都是常数。
   - **偶发性偏差** $ \epsilon_{ij} $：这个偏差是针对每个受试者 $ j $ 在每个时间点 $ i $ 特有的。

* **第二部分：理解公式**
现在我们来看公式：

$$y_{ij}=\beta+\underbrace{\zeta_j+\epsilon_{ij}}_{\xi_ij}$$

这个公式实际上是在说，对于每个受试者 $ j $ 在每个时间点 $ i $ 的观测值 $ y_{ij} $，可以由以下几部分组成：
1. **总体均值** $ \beta $：这是所有观测值的中心点，可以理解为如果没有其他因素影响，所有观测值的平均值。
2. **永久性偏差** $ \zeta_j $：这是受试者 $ j $ 特有的偏差，反映了这个受试者与总体均值的差异。
3. **偶发性偏差** $ \epsilon_{ij} $：这是在特定时间点 $ i $ 受试者 $ j $ 的特有偏差，它可能反映了测量误差、受试者当时的状态变化等。
4. **总偏差** $ \xi_{ij} $：这是将永久性偏差和偶发性偏差合并起来的结果，代表了 $ y_{ij} $ 与总体均值 $ \beta $ 的总偏差。
  
* **例子**
假设我们正在研究不同人每天的步数。我们想要建立一个模型来预测步数，但我们知道每个人的步数可能会因为个体差异而有所不同。
- $ \beta $ 可能是所有研究对象平均每天走的步数，比如5000步。
- $ \zeta_j $ 可能是某个特定人（比如张三）平均每天比总体多走的步数，比如200步。
- $ \epsilon_{ij} $ 可能是张三在某一天（比如周一）比他自己的平均水平多走的步数，比如50步。
那么，张三在周一的步数可以表示为：
$$
 y_{1j} = 5000 + 200 + 50 = 5250 
$$
这样，我们就可以更准确地预测张三在特定日子的步数，并且考虑到了个体差异和日常波动。希望这个例子能帮助你更好地理解这个模型。


as shown for subject in figure 2.3.

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.11.png" style="width:250px;height:90px;" alt="图片描述">
  <figcaption><strong>Figure 2.3: Illustration of variance-components model for a subject $j$</strong></figcaption>
</figure>


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.21.png" style="width:550px;height:330px;" alt="图片描述">
  <figcaption><strong>Figure 2.3: Illustration of variance-components model for a subject $j$</strong></figcaption>
</figure>


**Here $\xi_j$ is the random deviation of subject j’s mean measurement (over a hypothetical population of measurement occasions) from the $\zeta_{j}$,often called a random effect or random intercept of subject $j$,has zero population mean and is uncorrelated across subjects. The $\zeta_{j}$ can be viewed as representing individual differences due to personal characteristics. The component $\epsilon_{ij}$ often called the within-subject residual, is the random deviation of $y_{ij}$ from subject j’s mean.** This residual has zero population mean and is uncorrelated across occasions and subjects.

Note that the subject mean,$\beta+\zeta_{j}$ is not the same as the sample mean $(y_{1j}+y_{2j})/2$ for the subject because the sample mean of the within-subject residual, $(\epsilon_{1j}+\epsilon_{2j})/2$ is not necessarily 0, although the population mean, $E(\epsilon_{ij})$ is 0. In the figure, the two within-subject residuals have a negative sample mean—they could also have been both positive or both negative.

In classical psychometric test theory, (2.3) represents a measurement model where $\beta+\zeta_{j}$ is the true score for subject $j$, defined as the longterm mean measurement, and $\epsilon_{ij}$ is the measurement error at occasion $i$ for subject .

The random intercept $\zeta_{j}$ has variance $\psi $ (pronounced “psi”), interpretable as the between-subject variance, and the residual $\epsilon_{ij}$ has variance $\theta$ (pronounced “theta”), interpretable as the within-subject variance of $y_{ij}$.

The model is a simple example of a two-level model, where occasions are level-1 units and subjects are level-2 units or clusters. The random intercept $\zeta_{j}$ is then referred to as the level-2 residual with level-2 (between-subject) variance  $\psi $ ; $\epsilon_{ij}$ is referred to as the level-1 residual with level-1 (between-occasion, within-subject) variance $\theta$.

* **受试者均值和样本均值**
  
首先，我们来更深入地理解受试者均值 $ \beta + \zeta_j $ 和样本均值 $ \frac{(y_{1j} + y_{2j})}{2} $。
- **受试者均值**：这个值是理论上的，表示如果我们可以无限次测量同一个受试者，那么所有测量值的平均数将趋近于这个值。这个值反映了受试者的真实水平，不受任何特定测量误差的影响。
- **样本均值**：这个值是我们在有限次测量中计算出的平均值。由于每次测量都可能受到随机误差的影响，样本均值可能会偏离受试者均值。然而，如果我们多次测量同一个受试者，样本均值将会趋近于受试者均值。
* **测量误差的期望值**
  
- **测量误差的期望值**：虽然我们假设测量误差的总体期望值 $ E(\epsilon_{ij}) $ 是0，但这并不意味着每次测量的误差都是0。实际上，每次测量的误差可能为正或为负，但在多次测量中，这些误差会相互抵消，使得长期平均误差为0。

* **随机截距和残差的方差**
- **随机截距 $ \zeta_j $**：这个值代表了受试者与总体平均水平的差异，并且是固定不变的。它的方差 $ \psi $ 反映了不同受试者之间这种差异的大小。方差 $ \psi $ 越大，表示受试者之间的差异越大。
- **残差 $ \epsilon_{ij} $**：这个值代表了在特定时间点的测量误差，它是随机变化的。它的方差 $ \theta $ 反映了在不同时间点测量的变异性。方差 $ \theta $ 越大，表示在不同时间点的测量误差越大。

** **两级模型的结构**
- **两级模型**：在这个模型中，我们有两个层次的单位。第一级单位是时间点，每个时间点的测量都是独立的。第二级单位是受试者，每个受试者在多个时间点都有观测值。
- **随机截距 $ \zeta_j $**：作为第二级单位的属性，它代表了受试者与总体平均水平的差异。每个受试者都有一个不同的随机截距，这些截距的分布具有方差 $ \psi $。
- **残差 $ \epsilon_{ij} $**：作为第一级单位的属性，它代表了在特定时间点的测量误差。每次测量的误差都是独立的，并且具有方差 $ \theta $。

* **例子**
让我们用一个更详细的例子来说明这个模型：
假设我们正在研究一个班级学生的学习成绩，我们想要了解每个学生的真实成绩和每次考试的随机误差。
- **总体均值 $ \beta $**：假设班级所有学生的平均成绩是70分。
- **随机截距 $ \zeta_j $**：对于某个特定的学生，比如张三，他的真实成绩比班级平均水平高出10分，所以 $ \zeta_j = 10 $。
- **测量误差 $ \epsilon_{ij} $**：在第一次考试中，张三因为紧张少得了5分，而在第二次考试中，他因为准备充分多得了3分。
那么，张三两次考试的成绩可以表示为：
- 第一次考试：$ y_{11} = 70 + 10 - 5 = 75 $ 分
- 第二次考试：$ y_{12} = 70 + 10 + 3 = 83 $ 分
在这个例子中，张三的受试者均值 $ \beta + \zeta_j = 70 + 10 = 80 $ 分，而他的样本均值 $ \frac{(75 + 83)}{2} = 79 $ 分。我们可以看到，尽管存在随机误差，但样本均值仍然接近受试者均值。
此外，我们可以进一步分析这个模型：
- **随机截距的方差 $ \psi $**：如果我们观察多个学生的随机截距 $ \zeta_j $，我们会发现它们的分布具有方差 $ \psi $。这个方差反映了学生之间成绩差异的大小。
- **残差的方差 $ \theta $**：如果我们观察同一个学生在不同考试中的测量误差 $ \epsilon_{ij} $，我们会发现它们的分布具有方差 $ \theta $。这个方差反映了考试之间成绩波动的大小。
通过这个模型，我们可以更准确地估计每个学生的真实成绩，并了解成绩的波动性。


### 4.2 Path diagram

We can display the random part of the model (every term except β) by using a path diagram (similar to a directed acyclic graph or DAG), as shown in figure 2.4. Here the rectangles represent the observed responses $y_{1j}$ and $y_{2j}$ for each subject $j$, where the $j$ subscript is implied by the label “subject j” inside the frame surrounding the diagram. The long arrows from $\zeta_j$ to the responses represent regressions with slopes equal to  ：


1. The short arrows pointing at the responses from below represent the additive level-1 residuals $\epsilon_{1j}$ and $\epsilon_{2j}$.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.12.png" style="width:350px;height:270px;" alt="图片描述">
  <figcaption><strong>Figure 2.4: Path diagram of random part of random-intercept model</strong></figcaption>
</figure>


The path diagram makes it clear that the dependence between the two responses is solely due to the shared random intercept. The responses are conditionally independent given $ζ_j$ because they are regressed on $ζ_j$ and there is no arrow directly connecting them. (There is also no two-way arrow connecting the level-1 errors $\epsilon_{1j}$ and $\epsilon_{2j}$ to indicate that they are correlated.) It follows that the responses are conditionally uncorrelated given $ζ_j$.


$$\mathrm{Cor}(y_{ij},y_{i^{\prime}j}|\zeta_j)=0$$

This can also be seen by imagining that the data in figure 2.2 were generated by the model depicted in figure 2.3, where the dependence is solely due to the measurements being shifted up or down by the shared random intercept $ζ_j$ for each cluster $j$. One way of conditioning on $ζ_j$ is to imagine a dataset consisting of just one cluster (or consisting of a subset of clusters with identical values of $ζ_j$). For that dataset, the responses would be uncorrelated. Another way of understanding conditional independence is to consider predicting the response $y_{2j}$ at occasion 2 for a subject. If we knew $ζ_j$ (and$\beta$), knowing $y_{1j}$ would not improve our prediction.


The (marginal) within-subject correlation is induced by $ζ_j$ because this is shared by all responses for the same subject (see section 2.4.4). As we will see in later chapters, path diagrams can be particularly useful for conveying the structure of complex models involving several random effects.


* **路径图的组成**
1. **观察到的响应**：路径图中的矩形代表每个受试者 $ j $ 的观测响应 $ y_{1j} $ 和 $ y_{2j} $。这里的下标 $ j $ 是隐含的，由路径图周围的框架内的“受试者 j”标签表示。
2. **随机截距 $ \zeta_j $**：从 $ \zeta_j $ 指向响应的长箭头表示回归关系，其斜率等于1。这意味着 $ \zeta_j $ 对每个响应的直接影响是相等的。
3. **第一级残差 $ \epsilon_{1j} $ 和 $ \epsilon_{2j} $**：指向响应的短箭头表示第一级残差，这些残差是加性的，意味着它们直接添加到响应上。

* **路径图的含义**
- **条件独立性**：路径图清晰地表明，两个响应之间的依赖性完全是由于共享的随机截距 $ \zeta_j $。给定 $ \zeta_j $，响应是条件独立的，因为它们都是对 $ \zeta_j $ 进行回归，并且没有直接连接它们的箭头。此外，也没有双向箭头连接第一级误差 $ \epsilon_{1j} $ 和 $ \epsilon_{2j} $ 来表明它们是相关的。因此，给定 $ \zeta_j $，响应是条件不相关的。
- **条件不相关**：公式 $\mathrm{Cor}(y_{ij},y_{i'j}|\zeta_j)=0$ 表示，在给定随机截距 $ \zeta_j $ 的条件下，不同时间点 $ i $ 和 $ i' $ 的响应是不相关的。

* **条件独立性的解释**
- **条件数据集**：通过想象数据集中只包含一个集群（或包含具有相同 $ \zeta_j $ 值的集群子集），可以更容易地理解条件独立性。对于那个数据集，响应是不相关的。
- **预测响应**：如果我们知道了 $ \zeta_j $（和 $ \beta $），知道第一次时间点的响应 $ y_{1j} $ 并不会改善我们对第二次时间点响应 $ y_{2j} $ 的预测。

* **受试者内相关性的产生**
- **受试者内相关性**：边际受试者内相关性是由 $ \zeta_j $ 引起的，因为 $ \zeta_j $ 被同一受试者的所有响应共享（见第2.4.4节）。

* **路径图的用途**
- **复杂模型**：在后续章节中，我们将看到路径图在传达涉及多个随机效应的复杂模型的结构时特别有用。

* **例子**
假设我们有一个班级，我们想要了解学生在两次不同考试中的表现。路径图可以帮助我们可视化每个学生（受试者）在两次考试中的得分是如何受到他们个人能力（随机截距 $ \zeta_j $）的影响。
- **随机截距 $ \zeta_j $**：假设张三的随机截距是10，这意味着他的平均能力比班级平均水平高出10分。
- **第一次考试 $ y_{11} $**：张三在第一次考试中的表现可能受到他的个人能力的影响，以及一些随机误差 $ \epsilon_{1j} $。
- **第二次考试 $ y_{12} $**：同样，张三在第二次考试中的表现也受到他的个人能力和另一个随机误差 $ \epsilon_{2j} $ 的影响。
通过路径图，我们可以清晰地看到张三在两次考试中的表现是如何受到他的个人能力的影响，并且两次考试的表现是条件独立的，因为它们都是基于张三的个人能力来预测的。

希望这个更详细的解释和例子能够帮助你更好地理解路径图和随机截距模型。


### 4.3 Between-subject heterogeneity


Each response differs from the overall mean $\beta $ by a total residual or error $\xi _{ij} $, the sum of two error terms or error components:$\zeta _{j}$ and $\epsilon _{ij}.$

$$\xi_{ij}=\zeta_j+\epsilon_{ij}$$

The random intercept $ζ_j$ is shared between measurement occasions for the same subject $j$, whereas $\epsilon_{ij}$ is unique for each occasion $i$ (and subject).

The variance of the responses becomes $\psi +\theta$.

<!--  
$$\begin{array}{rcl}\mathrm{Var}(y_{ij})&=&E\{(y_{ij}-\underbrace{\beta}_{E(y_{ij})})^2\}&=&E\{(\zeta_{j}+\epsilon_{ij})^2\}&=&E(\zeta_{j}^2)+2\underbrace{E(\zeta_{j}\epsilon_{ij})}_{E(y_{ij})}&+E(\epsilon_{ij}^2)\\&&=&\psi+\theta\end{array}$$
-->


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.13.png" style="width:650px;height:120px;" alt="图片描述">
  <figcaption><strong>Figure 2.4: Path diagram of random part of random-intercept model</strong></figcaption>
</figure>

which is the sum of variance components representing between-subject and within-subject variances. The proportion of the total variance that is between subjects is

$$\rho~=~\frac{\mathrm{Var}(\zeta_{j})}{\mathrm{Var}(y_{ij})}=\frac{\psi}{\psi+\theta}$$

 
The coefficient $ρ$ is similar to the coefficient of determination $R^2$ in linear regression discussed in section 1.5, because it expresses how much of the total variability is "explained" by subjects.

1. In the measurement context,$\psi$is the variance of subjects' true scores$\beta +\zeta_j$,$\theta$is the **measurement-error variance** (the squared standard error of measurement), and $\rho$ is a reliability, here a test-retest reliability. Note that the reliability is not just a characteristic of the measurement instrument; it also depends on the between-subject variance$\psi$ which can differ between populations.

好的，让我们一步一步来理解这段话中提到的统计概念和公式。

**1.** 
**总残差或误差（Total Residual or Error）**

首先，我们有一个响应（response）$ y_{ij} $，它与整体平均值 $ \beta $ 之间的差异被称为总残差或误差 $ \xi_{ij} $。这个总残差由两个部分组成：随机截距 $ \zeta_j $ 和随机误差 $ \epsilon_{ij} $。
公式是这样的：
$$
 \xi_{ij} = \zeta_j + \epsilon_{ij} 
$$
- $ \zeta_j $ 是随机截距，它对于同一个受试者 $ j $ 在不同测量时间点是共享的。
- $ \epsilon_{ij} $ 是随机误差，对于每个测量时间点 $ i $（和受试者）都是独特的。

**2.** 
**响应的方差（Variance of the Responses）**


这个公式是统计学中描述随机效应模型中响应变量 $ y_{ij} $ 方差的一个关键公式。让我们逐步分解这个公式来更好地理解它。

* **公式分解**

1. **响应变量的方差**：

   $ \text{Var}(y_{ij}) $
   这是第 $ i $ 次测量，第 $ j $ 个受试者的响应变量 $ y_{ij} $ 的方差。

2. **期望的平方**：

   $ E\{(y_{ij} - \beta)^2\} $
   这里，$ \beta $ 是所有响应的平均值，$ E $ 表示期望值。这个表达式计算的是 $ y_{ij} $ 与平均值 $ \beta $ 之差的平方的期望值，也就是 $ y_{ij} $ 相对于平均值的变异性。

3. **随机误差的平方**：

   $ E\{(\zeta_j + \epsilon_{ij})^2\} $
   展开 $ y_{ij} $ 的表达式，我们得到 $ \zeta_j + \epsilon_{ij} $，其中 $ \zeta_j $ 是随机截距，$ \epsilon_{ij} $ 是随机误差。这个表达式计算的是随机截距和随机误差之和的平方的期望值。

4. **展开平方**：

   $ E(\zeta_j^2) + 2E(\zeta_j\epsilon_{ij}) + E(\epsilon_{ij}^2) $
   当我们展开 $ (\zeta_j + \epsilon_{ij})^2 $ 时，我们得到三个部分：
   - $ E(\zeta_j^2) $：随机截距的平方的期望值。
   - $ 2E(\zeta_j\epsilon_{ij}) $：随机截距和随机误差乘积的两倍期望值。这里乘以2是因为在展开过程中，交叉项会出现两次。
   - $ E(\epsilon_{ij}^2) $：随机误差的平方的期望值。

5. **方差成分**：

   $ \psi + \theta $
   最后，我们将上述三个部分合并，得到响应变量 $ y_{ij} $ 的总方差。这里：
   - $ \psi $ 代表 $ E(\zeta_j^2) $，即随机截距的方差。代表受试者之间的方差（between-subject variance）。
   - $ \theta $ 代表 $ E(\epsilon_{ij}^2) $，即随机误差的方差。代表测量误差的方差（measurement-error variance）。

* **公式解释**

这个公式告诉我们，响应变量 $ y_{ij} $ 的方差可以分解为两部分：
- 一部分是由于受试者之间的差异（由 $ \psi $ 表示，即随机截距的方差）。
- 另一部分是由于测量误差（由 $ \theta $ 表示，即随机误差的方差）。
这个分解非常重要，因为它允许我们理解数据中的变异性是如何由不同的因素（受试者间差异和测量误差）共同贡献的。

* **例子**

假设我们有一个班级的学生，我们想要了解他们的考试成绩的变异性。在这个例子中：
- $ \beta $ 是所有学生的平均成绩。
- $ \zeta_j $ 是第 $ j $ 个学生的成绩与其平均成绩的差异。
- $ \epsilon_{ij} $ 是第 $ i $ 次考试，第 $ j $ 个学生的随机误差。
如果我们计算一个学生的考试成绩的方差，我们会考虑这个学生的考试成绩与平均成绩的差异（$ \zeta_j $ 的方差）以及考试本身的随机误差（$ \epsilon_{ij} $ 的方差）。这两个部分加起来就给出了学生考试成绩的总方差。
希望这个详细的解释能帮助你更好地理解这个公式。如果你还有任何问题，请随时提问。



**3.** 
**方差成分的解释**

方差成分 $ \psi $ 和 $ \theta $ 的和代表了总方差，即受试者内部和受试者之间的总变异性。

**4.** 
**受试者间方差的比例（Proportion of Between-Subject Variance）**

受试者间方差的比例 $ \rho $ 可以用下面的公式表示：
$$
 \rho = \frac{\text{Var}(\zeta_j)}{\text{Var}(y_{ij})} = \frac{\psi}{\psi + \theta} 
$$
这个比例 $ \rho $ 类似于线性回归中的决定系数 $ R^2 $，因为它表达了总变异性中有多少是由受试者“解释”的。

**5.** 
**可靠性（Reliability）**

在测量的背景下：
- $ \psi $ 是受试者真实分数 $ \beta + \zeta_j $ 的方差。
- $ \theta $ 是测量误差的方差（测量误差的标准差的平方）。
- $ \rho $ 是可靠性，这里指的是测试-重测可靠性。需要注意的是，可靠性不仅仅是测量工具的特性；它还依赖于受试者间的方差 $ \psi $，这在不同人群中可能会有所不同。

**6.** 
**例子**

假设我们有一个测试，我们想要了解不同学生在多次测试中的得分变化。我们有以下数据：
- $ \beta $：所有学生的平均得分。
- $ \zeta_j $：第 $ j $ 个学生的随机截距，表示该学生得分高于或低于平均得分的部分。
- $ \epsilon_{ij} $：第 $ i $ 次测试，第 $ j $ 个学生的随机误差。

如果我们计算一个学生的得分方差，我们会将该学生的随机截距的方差加上该次测试的随机误差的方差。这个总方差就是 $ \psi + \theta $。

可靠性 $ \rho $ 告诉我们，学生得分的总变异性中有多大比例是由于学生之间的差异（而不是由于随机误差）。如果 $ \rho $ 接近 1，那么大部分变异性可以归因于学生之间的差异，这表明测试是可靠的。如果 $ \rho $ 接近 0，则表明测试结果主要由随机误差决定，测试不太可靠。


### 4.4 Within-subject dependence

* **Intraclass correlation**

The marginal (not conditional on $S_j$) covariance between the measurements on two occasions $i$ and $i'$ for the same subject $j$ is defined as

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.22.png" style="width:450px;height:67px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The corresponding marginal correlation is the above covariance divided by the product of the standard deviations:

$$\mathrm{Cor}(y_{ij},y_{i^{\prime}j})=\frac{\mathrm{Cov}(y_{ij},y_{i^{\prime}j})}{\sqrt{\mathrm{Var}(y_{ij})}\sqrt{\mathrm{Var}(y_{i^{\prime}j})}}$$

It follows from the variance-components model that the population means at both occasions are constrained to be equal to $β$ and the standard deviations are constrained to be equal to $\sqrt{\psi+\theta}$. The marginal (not conditional on $ξ_j$) covariance between the measurements equals $\psi$.

<!--  
$$\begin{aligned}\mathrm{Cov}(y_{ij},y_{i'j})&=\quad E\{(y_{ij}-\underbrace{\beta}_{E(y_{ij})})(y_{i'j}-\underbrace{\beta}_{E(y_{i'j})})\}=E\{(\zeta_{j}+\epsilon_{ij})(\zeta_{j}+\epsilon_{i'j})\}\\&=\quad E(\zeta_{j}^{2})+\underbrace{E(\zeta_{j}\epsilon_{i'j})}_{0}+\underbrace{E(\epsilon_{ij}\zeta_{j})}_{0}+\underbrace{E(\epsilon_{ij}\epsilon_{i'j})}_{0}=E(\zeta_{j}^{2})=\psi\end{aligned}$$
-->

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.14.png" style="width:650px;height:140px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The corresponding correlation, called the intraclass correlation , becomes

$$\mathrm{Cor}(y_{ij},y_{i^{\prime}j})~=~\frac{\mathrm{Cov}(y_{ij},y_{i^{\prime}j})}{\sqrt{\mathrm{Var}(y_{ij})}\sqrt{\mathrm{Var}(y_{i^{\prime}j})}}=\frac{\psi}{\sqrt{\psi+\theta}\sqrt{\psi+\theta}}=\frac{\psi}{\psi+\theta}~=~\rho $$

Thus,$\rho$, previously given in (2.4), also represents the within-cluster correlation, which cannot be negative in the variance-components model because $ψ\geq0$.

We see that between-cluster heterogeneity and within-cluster correlations are different ways of describing the same phenomenon; both are 0 when there is no between-cluster variance ($\psi=0$), and both increase when the between-cluster variance increases relative to the within-cluster variance.

The intraclass correlation is estimated by simply plugging in estimates for the unknown parameters:

$$\widehat{\rho}=\frac{\widehat{\psi}}{\widehat{\psi}+\widehat{\theta}}$$

Figure 2.5 shows simulated data with an estimated intraclass correlation of $\hat\rho = 0.58$ (top panel) and data with an estimated intraclass correlation of $\hat\rho = 0.87$ (bottom panel). The intraclass correlation is higher in the right panel because the clusters are more distinguishable, here because the within-cluster variance is considerably lower than in the left panel, whereas the between-cluster variance is similar.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.15.png" style="width:550px;height:440px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.16.png" style="width:550px;height:440px;" alt="图片描述">
  <figcaption><strong>Figure 2.5: Illustration of lower intraclass correlation (top) and higher intraclass correlation (bottom)</strong></figcaption>
</figure>


* **类内相关系数（Intraclass Correlation）**
类内相关系数是一个衡量同一受试者在不同时间点上测量结果相关性的统计量。

**1. 协方差（Covariance）**

首先，我们来理解协方差的概念。**协方差是衡量两个变量联合变化趋势的统计量。如果两个变量一起增加或一起减少，它们的协方差是正的；如果一个增加而另一个减少，协方差是负的。**
公式是：

<!--$$ \text{Cov}(y_{ij},y_{i'j}) = E[\{y_{ij} - E(y_{ij})\}\{y_{i'j} - E(y_{i'j})\}] $$-->


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.22.png" style="width:450px;height:67px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


这个公式是协方差的计算公式，用来衡量两个变量之间的线性关系强度。让我们一步步地分解这个公式，并用一个简单的例子来帮助理解。

* 公式分解

- $ \text{Cov}(y_{ij},y_{i'j}) $ 是变量 $ y_{ij} $ 和 $ y_{i'j} $ 的协方差。
- $ E $ 表示期望值，也就是平均值。
- $ y_{ij} $ 是第 $ i $ 次对第 $ j $ 个受试者的测量结果。
- $ E(y_{ij}) $ 是 $ y_{ij} $ 的期望值，即所有 $ y_{ij} $ 的平均值。
- $ y_{i'j} $ 是第 $ i' $ 次对第 $ j $ 个受试者的测量结果。
- $ E(y_{i'j}) $ 是 $ y_{i'j} $ 的期望值，即所有 $ y_{i'j} $ 的平均值。

* 公式解释
协方差的计算可以分解为以下几个步骤：

1. **计算每次测量与平均值的差**：

   对于每次测量 $ y_{ij} $ 和 $ y_{i'j} $，我们计算它们与各自平均值的差。例如，如果 $ y_{ij} $ 是 10，而 $ E(y_{ij}) $ 是 8，那么差就是 $ 10 - 8 = 2 $。

2. **计算差的乘积**：

   然后，对于每一对测量值（$ y_{ij} $ 和 $ y_{i'j} $），我们计算它们差值的乘积。如果 $ y_{ij} $ 的差是 2，而 $ y_{i'j} $ 的差是 -1（意味着 $ y_{i'j} $ 比平均值低 1），那么乘积就是 $ 2 \times -1 = -2 $。

3. **计算乘积的平均值**：

   我们对所有这样的乘积求和，然后除以乘积的总数（如果是两个测量值，总数就是 2），得到平均乘积，这就是协方差的值。

* 例子

假设我们有3个学生（$ j $），每个学生参加了两次考试（$ i $ 和 $ i' $），成绩如下：
| 学生编号 | 第一次考试分数 ($ y_{ij} $) | 第二次考试分数 ($ y_{i'j} $) |
|----------|---------------------------|---------------------------|
| 学生1    | 85                        | 88                        |
| 学生2    | 75                        | 70                        |
| 学生3    | 90                        | 95                        |

1. 步骤1: 计算平均分

首先，我们计算每次考试的平均分：
- 第一次考试平均分 $ E(y_{ij}) = (85 + 75 + 90) / 3 = 250 / 3 \approx 83.33 $
- 第二次考试平均分 $ E(y_{i'j}) = (88 + 70 + 95) / 3 \approx 253 / 3 \approx 84.33 $

2. 步骤2: 计算差值

然后，我们计算每个学生每次考试分数与平均分的差：
| 学生编号 | 第一次考试差值 ($ y_{ij} - E(y_{ij}) $) | 第二次考试差值 ($ y_{i'j} - E(y_{i'j}) $) |
|----------|-----------------------------------------|-----------------------------------------|
| 学生1    | $ 85 - 83.33 = 1.67 $                  | $ 88 - 84.33 = 3.67 $                 |
| 学生2    | $ 75 - 83.33 = -8.33 $                 | $ 70 - 84.33 = -14.33 $               |            
| 学生3    | $ 90 - 83.33 = 6.67 $                  | $ 95 - 84.33 = 10.67 $                |

3. 步骤3: 计算乘积和平均值

接下来，我们计算每对学生考试分数差值的乘积，然后求平均：
$$ \text{Cov}(y_{ij},y_{i'j}) = \frac{(1.67 \times 3.67) + (-8.33 \times -14.33) + (6.67 \times 10.67)}{3} $$
$$ \text{Cov}(y_{ij},y_{i'j}) = \frac{6.11 + 119.08 + 71.34}{3} \approx \frac{196.53}{3} \approx 65.51 $$
这个值表示学生在第一次考试和第二次考试分数之间的线性关系强度。

* 结论

通过这个例子，我们可以看到协方差是如何计算的，它反映了两个变量之间的共同变化趋势。如果协方差是正的，这意味着当一个变量的值高于其平均值时，另一个变量也倾向于高于其平均值，表明它们之间存在正相关关系。


**2. 相关系数（Correlation）**

相关系数是协方差的一个标准化版本，它告诉我们两个变量之间的线性关系有多强。
公式是：
$$ \text{Cor}(y_{ij},y_{i'j}) = \frac{\text{Cov}(y_{ij},y_{i'j})}{\sqrt{\text{Var}(y_{ij})}\sqrt{\text{Var}(y_{i'j})}} $$
这里的 $ \text{Var} $ 是方差，表示一个变量的离散程度。相关系数的值介于 -1 和 1 之间，值越接近 1 或 -1，表示变量之间的线性关系越强。

**3. 简化的协方差计算**

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.14.png" style="width:650px;height:140px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

这个公式是计算两个不同时间点上，同一个受试者测量值的协方差的公式。我们首先解释公式，然后通过一个具体的例子来进一步理解。
* **公式解释**

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.24.png" style="width:450px;height:170px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


1. **协方差的初始定义**：
   $$ \text{Cov}(y_{ij},y_{i'j}) = E\{(y_{ij}-\beta)(y_{i'j}-\beta)\} $$
   这里，$ y_{ij} $ 和 $ y_{i'j} $ 分别是同一个受试者 $ j $ 在不同时间点 $ i $ 和 $ i' $ 的测量值，$ \beta $ 是所有测量值的总体均值。
2. **展开乘积**：
   $$ E\{(\zeta_{j}+\epsilon_{ij})(\zeta_{j}+\epsilon_{i'j})\} $$
   这里，我们将 $ y_{ij} $ 和 $ y_{i'j} $ 用它们各自的组成部分表示，即随机截距 $ \zeta_j $ 和随机误差 $ \epsilon_{ij} $、$ \epsilon_{i'j} $。
3. **分配律**：
   $$ E(\zeta_{j}^{2}) + E(\zeta_{j}\epsilon_{i'j}) + E(\epsilon_{ij}\zeta_{j}) + E(\epsilon_{ij}\epsilon_{i'j}) $$
   根据分配律，我们将乘积展开为四个部分的和。
4. **独立性**：
   由于 $ \zeta_j $ 和 $ \epsilon_{ij} $ 是独立随机变量，$ \zeta_j $ 与 $ \epsilon_{i'j} $ 以及 $ \epsilon_{ij} $ 与 $ \zeta_j $ 之间的乘积期望值为0：
   $$ E(\zeta_{j}\epsilon_{i'j}) = 0 $$
   $$ E(\epsilon_{ij}\zeta_{j}) = 0 $$
   同样，由于 $ \epsilon_{ij} $ 和 $ \epsilon_{i'j} $ 是独立随机误差，它们的乘积期望值也为0：
   $$ E(\epsilon_{ij}\epsilon_{i'j}) = 0 $$

在统计学中，如果两个随机变量是独立的，那么其中一个变量的值不会给另一个变量提供任何信息。换句话说，知道一个变量的值并不会影响我们对另一个变量的预测。对于独立的随机变量 $ X $ 和 $ Y $，其乘积的期望值 $ E(XY) $ 等于各自期望值的乘积 $ E(X)E(Y) $。
在我们的例子中，我们有两个随机变量：
- $ \zeta_j $：表示第 $ j $ 个受试者的随机截距，它在不同的测量时间点 $ i $ 和 $ i' $ 上是相同的。
- $ \epsilon_{ij} $ 和 $ \epsilon_{i'j} $：分别表示第 $ i $ 次和第 $ i' $ 次测量的随机误差。
由于 $ \zeta_j $ 和 $ \epsilon_{ij} $ 是从不同的分布中抽取的，并且 $ \epsilon_{ij} $ 和 $ \epsilon_{i'j} $ 是在不同的时间点上测量的误差，它们之间没有相关性，因此我们说它们是独立的。同样，$ \zeta_j $ 与 $ \epsilon_{i'j} $ 也是独立的，因为随机截距与特定时间点的误差没有相关性。

* 独立性导致期望值为0的解释：

1. **$ E(\zeta_{j}\epsilon_{i'j}) = 0 $** 和 **$ E(\epsilon_{ij}\zeta_{j}) = 0 $**:

   由于 $ \zeta_j $ 和 $ \epsilon_{i'j} $ 是独立的，它们的乘积的期望值 $ E(\zeta_{j}\epsilon_{i'j}) $ 等于它们各自期望值的乘积，即 $ E(\zeta_j)E(\epsilon_{i'j}) $。由于 $ \zeta_j $ 和 $ \epsilon_{i'j} $ 的期望值都是0（随机截距和随机误差的平均值都是0），所以 $ E(\zeta_{j}\epsilon_{i'j}) = 0 \times 0 = 0 $。

2. **$ E(\epsilon_{ij}\epsilon_{i'j}) = 0 $**:

   同样，$ \epsilon_{ij} $ 和 $ \epsilon_{i'j} $ 是在不同时间点的独立误差项，它们的期望值也是0。即使它们不是在相同时间点，由于它们是独立且期望值为0的随机变量，它们的乘积的期望值也是0。
* 例子：
假设我们有以下两个随机变量：
- $ X $：一个随机变量，期望值 $ E(X) = 0 $。
- $ Y $：另一个随机变量，也与 $ X $ 独立，期望值 $ E(Y) = 0 $。
由于 $ X $ 和 $ Y $ 是独立的，它们的乘积的期望值 $ E(XY) $ 为：
$$ E(XY) = E(X)E(Y) = 0 \times 0 = 0 $$
这就是为什么在协方差的公式中，涉及独立项乘积的期望值为0的原因。
希望这个解释能帮助你理解独立性如何导致某些期望值为0。如果你还有任何疑问，请随时提问。


5. **最终结果**：
   $$ E(\zeta_{j}^{2}) = \psi $$
   最后，我们只剩下 $ \zeta_j $ 的平方的期望值，这等于随机截距的方差 $ \psi $。

* 例子

假设我们有一个班级，有2个学生（$ j $），每个学生进行了2次考试（$ i $ 和 $ i' $），成绩如下：
| 学生编号 | 第一次考试分数 ($ y_{ij} $) | 第二次考试分数 ($ y_{i'j} $) |
|----------|---------------------------|---------------------------|
| 学生1    | 80                        | 85                        |
| 学生2    | 70                        | 75                        |

1. 步骤1: 计算总体均值 ($ \beta $)

$$ \beta = \frac{80 + 85 + 70 + 75}{4} = 75 $$

2. 步骤2: 计算随机截距 ($ \zeta_j $)

$$ \zeta_1 = 80 - 75 = 5 $$
$$ \zeta_2 = 70 - 75 = -5 $$

3. 步骤3: 假设随机误差 ($ \epsilon_{ij} $ 和 $ \epsilon_{i'j} $) 为0

为了简化，我们假设没有测量误差，即 $ \epsilon_{ij} = \epsilon_{i'j} = 0 $。

4. 步骤4: 计算协方差

根据公式，我们有：
$$ \text{Cov}(y_{11},y_{12}) = E\{(5 + 0)(5 + 0)\} + E\{(-5 + 0)(-5 + 0)\} $$
$$ \text{Cov}(y_{11},y_{12}) = E(25) + E(25) $$
$$ \text{Cov}(y_{11},y_{12}) = 25 + 25 = 50 $$
在这个例子中，$ \psi $（随机截距的方差）是50。这意味着两个不同时间点上，同一个学生考试成绩的协方差等于随机截距的方差。



在方差分量模型中，我们假设每个受试者在不同时间点的测量结果的期望值都是相同的（等于 $ \beta $），并且每个测量结果的方差都是相同的（等于 $ \psi + \theta $）。因此，协方差的计算可以简化为：
$$ \text{Cov}(y_{ij},y_{i'j}) = E(\zeta_j^2) = \psi $$
这里的 $ \zeta_j $ 是受试者 $ j $ 的随机截距，它在不同时间点是相同的，而 $ \epsilon_{ij} $ 和 $ \epsilon_{i'j} $ 是随机误差，它们在不同时间点是独立的，所以它们的期望值是 0。



**4. 类内相关系数的最终表达式**

$$\mathrm{Cor}(y_{ij},y_{i^{\prime}j})~=~\frac{\mathrm{Cov}(y_{ij},y_{i^{\prime}j})}{\sqrt{\mathrm{Var}(y_{ij})}\sqrt{\mathrm{Var}(y_{i^{\prime}j})}}=\frac{\psi}{\sqrt{\psi+\theta}\sqrt{\psi+\theta}}=\frac{\psi}{\psi+\theta}~=~\rho $$

这个公式是用来计算两个测量值 $ y_{ij} $ 和 $ y_{i'j} $ 之间的相关系数，它们是同一个受试者 $ j $ 在不同时间点 $ i $ 和 $ i' $ 的测量结果。相关系数是一个介于 -1 和 1 之间的数，用来衡量两个变量之间的线性关系强度。
* **公式分解**
公式：
$$ \text{Cor}(y_{ij},y_{i'j}) = \frac{\text{Cov}(y_{ij},y_{i'j})}{\sqrt{\text{Var}(y_{ij})}\sqrt{\text{Var}(y_{i'j})}} $$

1. **相关系数（Correlation）**:

   - $ \text{Cor}(y_{ij},y_{i'j}) $ 是变量 $ y_{ij} $ 和 $ y_{i'j} $ 之间的相关系数。

2. **协方差（Covariance）**:

   - $ \text{Cov}(y_{ij},y_{i'j}) $ 是变量 $ y_{ij} $ 和 $ y_{i'j} $ 之间的协方差。

3. **方差（Variance）**:

   - $ \text{Var}(y_{ij}) $ 和 $ \text{Var}(y_{i'j}) $ 分别是 $ y_{ij} $ 和 $ y_{i'j} $ 的方差。

4. **相关系数的计算**:

   - 相关系数是协方差除以两个变量方差的乘积的平方根。这使得相关系数成为一个无量纲的量（没有单位），并且可以比较不同量纲的变量之间的线性关系。

* 公式中的特定情况

在这个特定的公式中，我们有：
- $ \text{Cov}(y_{ij},y_{i'j}) = \psi $，这是两个测量值之间的协方差，等于随机截距的方差。
- $ \text{Var}(y_{ij}) = \psi + \theta $ 和 $ \text{Var}(y_{i'j}) = \psi + \theta $，这是每个测量值的方差，等于随机截距的方差加上随机误差的方差。
将这些值代入相关系数的公式，我们得到：
$$ \text{Cor}(y_{ij},y_{i'j}) = \frac{\psi}{\sqrt{(\psi + \theta)} \cdot \sqrt{(\psi + \theta)}} $$
由于分母是两个相同的项的乘积的平方根，我们可以进一步简化：
$$ \text{Cor}(y_{ij},y_{i'j}) = \frac{\psi}{(\psi + \theta)} $$
这个表达式告诉我们，相关系数 $ \rho $ 等于随机截距的方差 $ \psi $ 除以总方差 $ \psi + \theta $。


* 结论

这个公式展示了如何使用协方差和方差来计算两个变量之间的相关系数。通过这个公式，我们可以量化两个测量值之间的线性关系强度。

5. **类内相关系数估计**

这个公式是用来估计类内相关系数 $ \hat{\rho} $ 的，它是基于样本数据而非总体参数。这里的 $ \hat{\rho} $ 表示的是估计值，而不是总体的真实值 $ \rho $。这个估计值是基于样本数据计算得到的，用来近似总体的相关系数。

* 公式分解

公式：
$$ \widehat{\rho} = \frac{\widehat{\psi}}{\widehat{\psi} + \widehat{\theta}} $$

1. **$ \widehat{\rho} $**: 这是类内相关系数的估计值。

2. **$ \widehat{\psi} $**: 这是随机截距方差的估计值，表示为样本数据中的受试者间变异性的估计。

3. **$ \widehat{\theta} $**: 这是随机误差方差的估计值，表示为样本数据中的受试者内（即测量误差）变异性的估计。

4. **估计类内相关系数**:
   - 类内相关系数的估计值是通过将随机截距方差的估计值除以总方差的估计值得到的。总方差的估计值是随机截 intercept 方差和随机误差方差估计值的和。

* 公式解释

这个公式的关键在于，它使用了样本数据来估计总体参数。在实际应用中，我们通常没有总体参数（如 $ \psi $ 和 $ \theta $）的直接信息，因此我们用样本数据来估计这些参数。
- **随机截 intercept 方差的估计 ($ \widehat{\psi} $)**: 这通常是通过分析数据中受试者间的平均变异性来得到的。例如，在ANOVA（方差分析）中，组间（受试者间）的平方和可以用于估计 $ \psi $。
- **随机误差方差的估计 ($ \widehat{\theta} $)**: 这通常是通过分析数据中受试者内（即测量误差）的平均变异性来得到的。例如，在ANOVA中，组内（受试者内）的平方和可以用于估计 $ \theta $。

* 例子

假设我们有以下样本数据：
- 随机截 intercept 方差的估计值 $ \widehat{\psi} = 100 $
- 随机误差方差的估计值 $ \widehat{\theta} = 40 $
我们可以计算类内相关系数的估计值：
$$ \widehat{\rho} = \frac{100}{100 + 40} = \frac{100}{140} \approx 0.71 $$
这意味着根据样本数据，我们估计两个测量值之间的相关系数大约是 0.71，表明它们之间存在较强的正线性关系。

* 结论

这个公式是一个实用的工具，它允许我们使用样本数据来估计总体的相关系数。这种估计在统计分析中非常常见，尤其是在我们无法直接观察到总体参数时。




* **例子：学生考试成绩的类内相关系数**

假设我们有一个班级，里面有3个学生，我们对他们进行了两次考试。我们想要了解这两次考试的成绩之间有多大的相关性。

* 假设数据如下：

| 学生编号 | 第一次考试分数 | 第二次考试分数 |
|----------|--------------|--------------|
| 学生1    | 85           | 88           |
| 学生2    | 75           | 72           |
| 学生3    | 90           | 92           |

1. 步骤1: 计算平均分（$ \beta $）

首先，我们计算所有学生两次考试的平均分作为 $ \beta $。
$$ \beta = \frac{(85 + 88) + (75 + 72) + (90 + 92)}{6} = \frac{515}{6} \approx 85.83 $$

2. 步骤2: 计算每个学生的随机截距（$ \zeta_j $）

接下来，我们计算每个学生的随机截距，即他们的成绩与平均成绩的差异。
| 学生编号 | $ \zeta_j $ |
|----------|--------------|
| 学生1    | $ 85 - 85.83 $ |
| 学生2    | $ 75 - 85.83 $ |
| 学生3    | $ 90 - 85.83 $ |

3. 步骤3: 计算协方差（$ \text{Cov}(y_{ij},y_{i'j}) $）

根据方差分量模型，我们知道协方差 $ \text{Cov}(y_{ij},y_{i'j}) $ 等于随机截距的方差 $ \psi $。在这个简化的例子中，我们假设 $ \psi $ 是已知的，或者我们可以通过观察数据来估计它。
假设我们观察到的 $ \psi $ 是：
$$ \psi = \frac{(85 - 85.83)^2 + (75 - 85.83)^2 + (90 - 85.83)^2}{3 - 1} $$
$$ \psi \approx \frac{(-0.83)^2 + (-10.83)^2 + (4.17)^2}{2} $$
$$ \psi \approx \frac{0.69 + 117.37 + 17.49}{2} \approx \frac{135.55}{2} \approx 67.78 $$

4. 步骤4: 计算方差（$ \text{Var}(y_{ij}) $）和标准差

我们知道每个学生两次考试的分数的方差是 $ \psi + \theta $。在这个例子中，我们假设 $ \theta $ 也是已知的或者可以估计。
假设 $ \theta $ 是：
$$ \theta = \frac{(88 - 85)^2 + (72 - 75)^2 + (92 - 90)^2}{3 - 1} $$
$$ \theta = \frac{9 + 9 + 4}{2} = \frac{22}{2} = 11 $$
所以，每个学生两次考试的分数的方差是：
$$ \text{Var}(y_{ij}) = \psi + \theta = 67.78 + 11 = 78.78 $$
标准差是方差的平方根：
$$ \sqrt{\text{Var}(y_{ij})} = \sqrt{78.78} \approx 28.07 $$

5. 步骤5: 计算类内相关系数（$ \rho $）

最后，我们使用之前计算的 $ \psi $ 和 $ \theta $ 来计算类内相关系数 $ \rho $：
$$ \rho = \frac{\psi}{\psi + \theta} = \frac{67.78}{78.78} \approx 0.86 $$
这意味着这两次考试的成绩之间有较强的正相关性。

* 结论

通过这个例子，我们可以看到如何计算类内相关系数，它反映了同一受试者在不同时间点上的测量结果之间的相关性。在这个例子中，我们假设了一些数据来演示计算过程，实际应用中，这些数据将来自于实际的测量和观察。希望这个例子能帮助你更好地理解类内相关系数的概念。如果你有任何疑问或需要进一步的帮助，请随时告诉我。



* **Intraclass correlation versus Pearson correlation**


In contrast to the estimated intraclass correlation, the Pearson correlation $r$ is obtained by plugging in separate sample means $\overline y_i$ and $\overline y_{i'}$ and sample standard deviations $s_{yi}$ and $s_{y_i'} $ for the two occasions in the estimate of the marginal correlation (2.5),

<!--$$r=\frac{\frac1{J-1}\sum_{j=1}^J(y_{ij}-\overline{y}_{i\cdot})(y_{i^{\prime}j}-\overline{y}_{i^{\prime}\cdot})}{s_{y_i}s_{y_i^{\prime}}}$$-->

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.18.png" style="width:450px;height:100px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

where $J$ is the number of clusters. Here it is not assumed that the population means and standard deviations are constant across occasions.

To give more insight into the interpretation of the estimated intraclass correlation and Pearson correlation, consider what happens if we alter the second Mini Wright peak-flow measurements by adding 100 to them, as shown in figure 2.6. (Such a systematic increase could, for instance, be due to a practice effect.) For the variance-components model, it is obvious that the within-cluster variance has increased, giving a much smaller intraclass correlation than for the original data (estimated as 0.63 instead of 0.97). In contrast, the Pearson correlation is 0.97 in both cases (figures 2.2 and 2.6) because it is based on deviations of the first and second measurements from their respective means. In contrast, the intraclass correlation is based on deviations from the overall or pooled mean.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.19.png" style="width:550px;height:450px;" alt="图片描述">
  <figcaption><strong>Figure 2.6: First recording of Mini Wright meter and second recording plus 100 versus subject number (the horizontal line represents the overall mean)</strong></figcaption>
</figure>

The Pearson correlation can be thought of as a measure of relative agreement, which refers to how well rankings of subjects at each occasion agree, and is therefore not affected by linear transformations of the measurements. In contrast, the intraclass correlation is a measure of absolute agreement.


The intraclass correlation is useful when the units $i$ are exchangeable with identical means and standard deviations. For instance, for twin data, there may not even be such a thing as the first and second twin (presuming that birth order is either irrelevant or unknown). Whereas the Pearson correlation can only be obtained by making an arbitrary assignment to $y_{1j}$ and $y_{2j}$ for each twin-pair, the intraclass correlation does not require this. Twins are an example of exchangeable dyads, where the intraclass correlation is more appropriate; married heterosexual couples are an example of nonexchangeable or distinguishable dyads, where the Pearson correlation between husbands $y_{1j}$ and wives $y_{2j}$ is more appropriate because it is usually difficult to justify that husbands and wives have the same population mean and the same variance. Another difference between the estimated intraclass correlation and the Pearson correlation is that the latter is only defined for pairs of variables, whereas the former summarizes dependence for clusters of size 2 and larger, and clusters of variable sizes; see, for example, exercise 2.4.

>1. If the arrows between $S_j$ and the $y_{ij}$ were reversed, $S_j$ would become a so-called "collider" and conditional independence would not hold (see, for example, Morgan and Winship [2015, chap. 3]).


让我们一步步地来理解皮尔逊相关系数（Pearson correlation, $ r $）和类内相关系数（intraclass correlation, $ \rho $）的区别，以及它们各自的计算公式。

* **皮尔逊相关系数（Pearson correlation, $ r $）**

皮尔逊相关系数衡量的是两个变量之间的线性关系强度。它的计算公式是：

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.18.png" style="width:450px;height:100px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

让我们分解这个公式：

1. **分子**：


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.png" style="width:350px;height:110px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>


   - 这里，$ y_{ij} $ 和 $ y_{i'j} $ 分别是第 $ i $ 次和第 $ i' $ 次对第 $ j $ 个受试者的测量值。
   - $ \overline{y}_i. $ 和 $ \overline{y}_i'. $ 分别是第 $ i $ 次和第 $ i' $ 次测量的所有受试者的平均值。
   - $ J $ 是受试者的数量。
   - 分子计算的是各个受试者在两次测量中与各自平均值的偏差乘积的和，然后除以 $ J-1 $（自由度）。

1. **分母**：

$$
s_{y_i} s_{y_i'}
$$
   - $ s_{y_i} $ 和 $ s_{y_i'} $ 分别是第 $ i $ 次和第 $ i' $ 次测量的标准差。

3. **最终表达式**：

   - 皮尔逊相关系数 $ r $ 是分子和分母的比值，它衡量了两次测量值之间的线性关系强度。
皮尔逊相关系数衡量的是两个变量之间的线性关系强度。它是通过以下步骤计算的：

1. **计算每个变量的平均值**：
   - 对于第 $ i $ 次测量，所有受试者的平均值 $ \overline{y}_{i\cdot} $。
   - 对于第 $ i' $ 次测量，所有受试者的平均值 $ \overline{y}_{i'\cdot} $。

2. **计算偏差乘积的和**：

   - 对每个受试者，计算其测量值与相应测量的平均值的偏差（例如，$ y_{ij} - \overline{y}_{i\cdot} $）。
   - 然后，将这些偏差两两相乘（$ (y_{ij} - \overline{y}_i.)(y_i'_j - \overline{y}_i'.) $）。
   - 将所有受试者的乘积相加，得到总和。

3. **计算标准差**：

   - 分别计算第 $ i $ 次和第 $ i' $ 次测量的标准差 $ s_{y_i} $ 和 $ s_{y_i'} $。

4. **计算相关系数**：

   - 将偏差乘积的和除以 $ J-1 $（自由度），得到分子。
   - 将分子除以两次测量的标准差的乘积，得到皮尔逊相关系数 $ r $。

* **类内相关系数（Intraclass correlation, $ \rho $）**

类内相关系数衡量的是同一个受试者在不同时间点测量结果之间的一致性。它是通过以下步骤计算的：

1. **计算总体平均值**：

   - 将所有受试者的所有测量值合并，计算总体平均值。

2. **计算随机截距和随机误差的方差**：

   - 随机截距的方差 $ \psi $ 反映了受试者间的差异。
   - 随机误差的方差 $ \theta $ 反映了测量误差。

3. **计算类内相关系数**：

   - 类内相关系数 $ \rho $ 是随机截距方差 $ \psi $ 与总方差（$ \psi + \theta $）的比值。

* **例子**

假设我们有3个受试者（$ J=3 $）的两次测量数据：
| 受试者编号 | 第一次测量 $ y_{ij} $ | 第二次测量 $ y_{i'j} $ |
|-----------|---------------------|---------------------|
| 受试者1   | 10                  | 12                  |
| 受试者2   | 8                   | 10                  |
| 受试者3   | 12                  | 14                  |

* 计算皮尔逊相关系数

1. **计算平均值**：

   - 第一次测量的平均值 $ \overline{y}_{i\cdot} = (10 + 8 + 12) / 3 = 10 $
   - 第二次测量的平均值 $ \overline{y}_{i'\cdot} = (12 + 10 + 14) / 3 = 12 $

2. **计算偏差乘积的和**：

   - $ (10-10)(12-12) + (8-10)(10-12) + (12-10)(14-12) = 0 - 4 + 4 = 0 $

3. **计算标准差**：

   - 第一次测量的标准差 $ s_{y_i} = \sqrt{\frac{(10-10)^2 + (8-10)^2 + (12-10)^2}{3-1}} = \sqrt{6} $
   - 第二次测量的标准差 $ s_{y_i'} = \sqrt{\frac{(12-12)^2 + (10-12)^2 + (14-12)^2}{3-1}} = \sqrt{2} $

4. **计算皮尔逊相关系数 $ r $**：

   - $ r = \frac{0}{\sqrt{6}\sqrt{2}} = 0 $

* 计算类内相关系数

1. **计算总体平均值**：

   - $ \beta = (10 + 8 + 12 + 12 + 10 + 14) / 6 = 10.83 $

2. **假设随机截距和随机误差的方差**：

   - 假设 $ \psi = 4 $（受试者间的差异）
   - 假设 $ \theta = 2 $（测量误差）

3. **计算类内相关系数 $ \rho $**：

   - $ \rho = \frac{\psi}{\psi + \theta} = \frac{4}{4 + 2} = \frac{4}{6} \approx 0.67 $

* 结论

通过这个例子，我们可以看到皮尔逊相关系数和类内相关系数的计算方法和它们所衡量的不同方面。皮尔逊相关系数基于每次测量的样本均值和标准差，而类内相关系数基于总体均值和标准差。皮尔逊相关系数衡量的是相对一致性，而类内相关系数衡量的是绝对一致性。

* **应用场景及区别**

皮尔逊相关系数（Pearson correlation, $ r $）和类内相关系数（Intraclass correlation, $ \rho $）虽然都是用来衡量变量间的相关性，但它们的应用场景和所强调的相关性类型有所不同。

* **皮尔逊相关系数的应用场景：**

1. **相对一致性**：

   - 皮尔逊相关系数衡量的是相对一致性，即个体在两个不同测量中的相对排名是否一致。如果所有测量值都因为某种系统性原因（如测量工具的偏差）而增加或减少，皮尔逊相关系数仍然能够反映出个体间相对排名的一致性。

2. **独立样本**：

   - 当你处理的是两个独立样本集，或者你关心的是两个不同条件下变量之间的关系时，使用皮尔逊相关系数是合适的。

3. **非交换性数据**：

   - 在数据点之间没有交换性（即数据点的身份是重要的）时，皮尔逊相关系数是合适的。例如，比较不同个体的成绩和他们的运动表现。

4. **任意配对**

   - 当你想要分析任意两个配对之间的关系，而这些配对之间没有内在联系时，皮尔逊相关系数是适用的。

* **类内相关系数的应用场景：**

1. **绝对一致性**：

   - 类内相关系数衡量的是绝对一致性，即个体在不同测量中的值是否接近总体均值。它适用于评估测量的可靠性，即测量结果是否稳定。

2. **重复测量**：

   - 当同一个受试者或对象在不同时间点或条件下被重复测量时，类内相关系数是合适的。例如，医学研究中对同一患者的多次血压测量。

3. **交换性数据**：

   - 当数据点之间具有交换性（即数据点的身份不重要）时，类内相关系数是合适的。例如，双胞胎的数据，我们通常认为双胞胎的身份在分析中是可以交换的。

4. **评估一致性**：

   - 当研究目的是评估不同测量之间的一致性或可靠性时，类内相关系数是一个好的选择。

5. **多等级数据**：

   - 当数据具有层次结构或多等级结构时，类内相关系数可以概括不同大小的组内的依赖性。

* 例子说明区别：

- **皮尔逊相关系数例子**：
  假设我们有两组学生的成绩数据，一组是数学成绩，另一组是科学成绩。我们想要了解这两门学科的成绩是否存在线性关系。在这种情况下，使用皮尔逊相关系数是合适的，因为它可以帮助我们了解学生在两门学科间的相对表现是否一致。

- **类内相关系数例子**：
  假设我们对同一组学生在不同时间点进行了多次阅读理解测试。我们想要评估这些测试结果的一致性，以确定测试是否可靠。在这种情况下，使用类内相关系数是合适的，因为它可以帮助我们了解学生在不同时间点的测试成绩是否接近总体均值，从而评估测试的稳定性。

* 结论：
选择使用皮尔逊相关系数还是类内相关系数，取决于数据的性质和你的研究目的。理解每种相关系数的特点和适用场景，可以帮助你更准确地分析数据并得出有效的结论。



## 5 Estimation using Stata

**In Stata, maximum likelihood estimates for variance-components models can be obtained using **`xtreg, mle`** or **`mixed, mle`** (the default for mixed). Restricted maximum likelihood (REML) estimates can be obtained using **`mixed, reml`**, and feasible generalized least-squares (FGLS) estimates can be obtained using **`xtreg, re`** (the default method).** See sections 2.10.2 and 3.10.1 for information on these estimation methods. **Briefly, the methods produce practically identical results when the number of clusters is large, but REML is preferable when the number of clusters is small, say less than 42.** See display 2.1 for the rule of thumb of 42 for choosing between estimation methods.

The **`xtreg`** command is the most computationally efficient for variance-components models. However, **the postestimation command **`predict`** for **`mixed`** is more useful than **`predict`** for **`xtreg`**. The **`mixed`** command is also preferable when the number of clusters is small because it produces REML estimates when used with the **`reml`** option.** The community-contributed command **`gllamm`** can also be used for maximum likelihood estimation as demonstrated in the gllamm companion for this book that is available from www.gllamm.org. We do not generally recommend using **`gllamm`** for the linear models considered in this volume, but the command is useful for more complex models.

### 5.1 Data preparation: Reshaping from wide form to long form

We now set up the data for estimation in Stata. Currently, the responses for occasions 1 and 2 are in wide form as two separate variables, **wp1** and **wp2** for the Wright peak-flow meter, and **wm1** and **wm2** for the Mini Wright peak-flow meter.

```
** 2.5.1 Data preparation: Reshaping to long form
list if id<6, clean noobs
```

        id   wp1   wp2   wm1   wm2   mean_wm  
         1   494   490   512   525     518.5  
         2   395   397   430   415     422.5  
         3   516   512   520   508       514  
         4   434   401   428   444       436  
         5   476   470   500   500       500  

For model fitting, we need to stack the occasion 1 and 2 measurements using a given meter into one variable. We can use the **`reshape`** command to obtain such a long form with one variable, **wp**, for both Wright peak-flow meter measurements; one variable, **wm**, for both Mini Wright peak-flow meter measurements; and a variable, **occasion** (equal to 1 and 2), for the measurement occasion:

```
* stack repeated observations for same method into single variable
reshape long wp wm, i(id) j(occasion)
```
    
    
    Data                               Wide   ->   Long
    -----------------------------------------------------------------------------
    Number of observations               17   ->   34          
    Number of variables                   6   ->   5           
    j variable (2 values)                     ->   occasion
    xij variables:
                                    wp1 wp2   ->   wp
                                    wm1 wm2   ->   wm
    -----------------------------------------------------------------------------
    

```
list if id<6, clean noobs
```
    
        id   occasion    wp    wm   mean_wm  
         1          1   494   512     518.5  
         1          2   490   525     518.5  
         2          1   395   430     422.5  
         2          2   397   415     422.5  
         3          1   516   520       514  
         3          2   512   508       514  
         4          1   434   428       436  
         4          2   401   444       436  
         5          1   476   500       500  
         5          2   470   500       500  

### 5.2 Using xtreg

We can estimate the parameters of the variance-components model (2.3) by using the **`xtreg`** command with the **`mle`** option, which stands for maximum likelihood estimation (see section 2.10.2).

Before using **`xtreg`** and any command starting with **`xt`**, the data should be declared as clustered data (referred to as panel data in Stata documentation because longitudinal or panel data are a common example of clustered data) by using the **`xtset`** command. Here it is sufficient to declare that **`id`** is the cluster identifier $ j=1, \ldots, 17 $:

```
** 2.5.2 Using xtreg
xtset id
```

        Panel variable: id (balanced)

The output states that our data are balanced, meaning that the cluster size is constant (here two measurements per subject).

We are now ready to use the xtreg command. As in the regress command, the response variable wm and covariates are listed after the command name. In variance-components models, the fixed part is just the intercept $\beta $, which is included by default, so we do not specify any covariates. The random part includes a random intercept $\zeta _ {j} $ for the clusters defined in the xtset command. The level-1 residual $\epsilon _{ij} $ need not be specified because it is always included. Therefore the command is simply

```
xtreg wm, mle
```
    
    Iteration 0:  Log likelihood = -187.89003
    Iteration 1:  Log likelihood = -184.95979
    Iteration 2:  Log likelihood = -184.76189
    Iteration 3:  Log likelihood =  -184.5855
    Iteration 4:  Log likelihood =  -184.5784
    Iteration 5:  Log likelihood = -184.57839
    
    Random-effects ML regression                           Number of obs    =   34
    Group variable: id                                     Number of groups =   17
    
    Random effects u_i ~ Gaussian                          Obs per group:
                                                                        min =    2
                                                                        avg =  2.0
                                                                        max =    2
    
                                                           Wald chi2(0)     = 0.00
    Log likelihood = -184.57839                            Prob > chi2      =    .
    
    ------------------------------------------------------------------------------
              wm | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |     453.91      26.19    17.33   0.000       402.59      505.24
    -------------+----------------------------------------------------------------
        /sigma_u |     107.05      18.68                         76.04      150.69
        /sigma_e |      19.91       3.41                         14.23       27.87
             rho |       0.97       0.02                          0.92        0.99
    ------------------------------------------------------------------------------
    LR test of sigma_u=0: chibar2(01) = 46.27              Prob >= chibar2 = 0.000


We see in the output that there are 34 observations belonging to 17 groups (the clusters, here subjects) and that there are 2 observations in each group (the minimum, maximum, and hence average number are all 2). In the Stata output and in the Stata documentation for xtreg, the $i$ subscript is used for clusters (instead of $j$ used in this book), $u_i$ is used for the random intercept (instead of $\zeta_j$), and the$t$subscript is used for occasions (instead of$i$ used in this book).


The estimate of the overall population mean $\beta$, given next to cons in the output, is 453.91. The estimate of the between-subject standard deviation $\psi$ of the random intercepts of subjects, referred to as $\sigma_u$, is 107.05, and the estimate of the within-subject standard deviation $\theta$, referred to as $\sigma_e$, is 19.91. It follows that the intraclass correlation is estimated as.


$$\widehat{\rho}=\frac{\widehat{\psi}}{\widehat{\psi}+\widehat{\theta}}=\frac{107.0464^2}{19.91083^2+107.0464^2}=0.97$$


which is referred to as rho in the output. This estimate is close to 1, indicating that the Mini Wright peak-flow meter is very reliable. The parameter estimates are also given under “ML” in table 2.2 below.

An alternative estimator for **`xtreg`** is feasible generalized least squares (FGLS), obtained using the **`re`** option. As discussed in sections 2.10.2 and 3.10.1, FGLS is similar to ML.

The **`vce(robust)`** option can be used to obtain standard errors that are robust to misspecification of the residual variance and covariance structure (robust standard errors for nonclustered data were used in section 1.6). However, robust standard errors do not perform well when the number of clusters is small, as in the data considered here (see again display 2.1 for the rule of thumb that at least 42 clusters are needed).

To be explicit about the structure of the model, we will run the **`xtset`** command every time we run **`xtreg`** although this is not required if the data have already been **`xtset`** in the current Stata session.

### 5.3 Using mixed

The variance-components model considered here is a simple special case of a linear mixed-effects model that can be fit using the **`mixed`** command (which replaced **`xtmixed`** in Stata 13). The **`mixed`** command can also be used for models with random slopes as well as models with more than one clustering variable (for example, three-level models). The structure of the model is completely specified in the **`mixed`** command instead of using the **`xtset`** command to define any aspect of the model as for **`xtreg`**.

The fixed part of the model, here $\beta$, is specified as in any estimation command in Stata (the response variable followed by a list of covariates). The random part, except the residual $\epsilon_{ij}$, is specified after two vertical bars (or pipes),||. The cluster identifier, here id, is first given to define the clusters $j$ over which $\zeta_j$ varies. This is followed by a colon and nothing, because a random intercept $\zeta_j$ is included by default (it can be excluded using the **`noconstant`** option). Finally, we request maximum likelihood estimation by using the **`mle`** option (the default), and we use the **`stddeviations`** option to obtain standard deviation estimates instead of variances for the error components.

```
mixed wm || id:, mle stddeviations
```
    
    Performing EM optimization ...
    
    Performing gradient-based optimization: 
    Iteration 0:  Log likelihood = -184.57839  
    Iteration 1:  Log likelihood = -184.57839  
    
    Computing standard errors ...
    
    Mixed-effects ML regression                             Number of obs    =  34
    Group variable: id                                      Number of groups =  17
                                                            Obs per group:
                                                                         min =   2
                                                                         avg = 2.0
                                                                         max =   2
                                                            Wald chi2(0)     =   .
    Log likelihood = -184.57839                             Prob > chi2      =   .
    
    ------------------------------------------------------------------------------
              wm | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |     453.91      26.19    17.33   0.000       402.59      505.24
    ------------------------------------------------------------------------------
    
    ------------------------------------------------------------------------------
      Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
    -----------------------------+------------------------------------------------
    id: Identity                 |
                       sd(_cons) |     107.05      18.68         76.04      150.69
    -----------------------------+------------------------------------------------
                    sd(Residual) |      19.91       3.41         14.23       27.87
    ------------------------------------------------------------------------------
    LR test vs. linear model: chibar2(01) = 46.27         Prob >= chibar2 = 0.0000
    

The table of estimates for the fixed part of the model has the same form as that for **`xtreg`** and all Stata estimation commands. The random part is given under the Random-effects Parameters header. Here **`sd(_cons)`** is the estimate of the random-intercept standard deviation $ \sqrt{\psi} $, and **`sd(Residual)`** is the estimate of the standard deviation $sqrt{\theta} $ of the level-1 residuals. All of these estimates are identical to the estimates from **`xtreg`** given in table 2.2 under "ML". We could also obtain estimated variances (instead of standard deviations) with their standard errors by omitting the **`stddeviations`** option.


As will be discussed in section 2.10.2, it is preferable to use REML instead of ML when the number of clusters is small as in this dataset. Simply use the same syntax but now with the **`reml`** option:

```
mixed wm || id:, reml stddeviations
```
    
    Performing EM optimization ...
    
    Performing gradient-based optimization: 
    Iteration 0:  Log restricted-likelihood = -180.37921  
    Iteration 1:  Log restricted-likelihood = -180.37921  
    
    Computing standard errors ...
    
    Mixed-effects REML regression                           Number of obs    =  34
    Group variable: id                                      Number of groups =  17
                                                            Obs per group:
                                                                         min =   2
                                                                         avg = 2.0
                                                                         max =   2
                                                            Wald chi2(0)     =   .
    Log restricted-likelihood = -180.37921                  Prob > chi2      =   .
    
    ------------------------------------------------------------------------------
              wm | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |     453.91      26.99    16.82   0.000       401.01      506.82
    ------------------------------------------------------------------------------
    
    ------------------------------------------------------------------------------
      Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
    -----------------------------+------------------------------------------------
    id: Identity                 |
                       sd(_cons) |     110.40      19.84         77.63      157.00
    -----------------------------+------------------------------------------------
                    sd(Residual) |      19.91       3.41         14.23       27.87
    ------------------------------------------------------------------------------
    LR test vs. linear model: chibar2(01) = 46.96         Prob >= chibar2 = 0.0000
    
    
The estimates, also shown under "REML" in table 2.2, are the same as the ML estimates except that the between-subject standard deviation estimate is larger (110.40 compared with 107.05) which results in a larger estimated standard error for $\beta$, as will be discussed in section 2.10.3.

Robust standard errors can be obtained when **`mixed`** is used with the **`mle`** option by specifying **`vce(robust)`**, but are not available with the **`reml`** option. The use of robust standard errors is generally not recommended when there are few clusters (fewer than about 42) as in this application.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.1.png" style="width:450px;height:270px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

After estimating the parameters with **`mixed`** (either with the **`mle`** or **`reml`** option), the estimated intraclass correlation can be obtained using **`estat icc`**. For REML, used in the last estimation command, we obtain


```
estat icc
```
    
    Intraclass correlation
    
    ------------------------------------------------------------------------------
                           Level |        ICC   Std. err.     [95% conf. interval]
    -----------------------------+------------------------------------------------
                              id |   .9684963    .015274      .9201564    .9879528
    ------------------------------------------------------------------------------
   

## 6 Hypothesis tests and confidence intervals

### 6.1 Hypothesis test and confidence interval for the population mean

In the regression tables produced by `xtreg` and `mixed`, $z$ statistics are reported for instead of the statistics given by the `regress` command.

Like the $t$ statistic in ordinary linear regression, the $z$ statistic for the null hypothesis

$$H_{0}:\beta=0\quad\mathrm{against}\quad H_{a}:\beta\neq0$$

is given by

$$z~=~\frac{\widehat{\beta}}{\widehat{\mathrm{SE}}(\widehat{\beta})}$$

Squaring the $z$ statistic gives the Wald statistic, an approximation to the likelihood-ratio statistic, described in section 2.6.2 for a different problem, namely testing the between-cluster variance.

An asymptotic 95% confidence interval for $\beta$ is given by

$$\widehat{\beta} \pm z_{0.975}\widehat{\mathrm{SE}}(\widehat{\beta})$$


where $z_{0.975}$ is the 97.5th percentile of the standard normal distribution, that is, $z_{0.975} = 1.96$. This kind of confidence interval based on assuming a normal sampling distribution is often called a Wald confidence interval. In the Mini Wright application, the 95% Wald confidence interval for the population mean $\beta$ is from 402.59 to 505.24, as shown, for instance, in the output from xtreg in section 2.5.2.

The reason the statistic is called $z$ instead of $t$ and $z_{0.975}$ is used for the confidence interval instead of $t_{0.975, df}$ is that a standard normal sampling distribution is assumed under the null hypothesis instead of a $t$ distribution. The $t$ distribution is a finite-sample distribution whose shape depends on the degrees of freedom. For the variance-components model, the finite-sample distribution does not have a simple form, so Stata's commands use the asymptotic (large-sample) sampling distribution. As of Stata 14, the **`mixed`** command can also provide degrees of freedom (df) For a distribution that approximates the finite-sample distribution of the test statistic. Among several approximations for the degrees of freedom, the Kenward–Roger method generally appears to be preferable (Kenward and Roger 1997; Schaalje, McBride, and Fellingham 2002), and this approximation is obtained using the **`dfmethod(kroger)`** option (which works only in combination with the **`reml`** option):

```
mixed wm || id:, reml dfmethod(kroger) stddeviations
```
    
    Performing EM optimization ...
    
    Performing gradient-based optimization: 
    Iteration 0:  Log restricted-likelihood = -180.37921  
    Iteration 1:  Log restricted-likelihood = -180.37921  
    
    Computing standard errors ...
    
    Computing degrees of freedom ...
    
    Mixed-effects REML regression                         Number of obs    =    34
    Group variable: id                                    Number of groups =    17
                                                          Obs per group:
                                                                       min =     2
                                                                       avg =   2.0
                                                                       max =     2
    DF method: Kenward–Roger                              DF:          min = 16.00
                                                                       avg = 16.00
                                                                       max = 16.00
                                                          F(0, .)          = .
    Log restricted-likelihood = -180.37921                Prob > F         = .
    
    ------------------------------------------------------------------------------
              wm | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |     453.91      26.99    16.82   0.000       396.69      511.13
    ------------------------------------------------------------------------------
    
    ------------------------------------------------------------------------------
      Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
    -----------------------------+------------------------------------------------
    id: Identity                 |
                       sd(_cons) |     110.40      19.84         77.63      157.00
    -----------------------------+------------------------------------------------
                    sd(Residual) |      19.91       3.41         14.23       27.87
    ------------------------------------------------------------------------------
    LR test vs. linear model: chibar2(01) = 46.96         Prob >= chibar2 = 0.0000


There are 16 degrees of freedom (see DF: in top-right of output), making the confidence interval broader, spanning from 396.69 to 511.13 (compared with 402.59 to 505.24 based on the asymptotic distribution). Here, the degrees of freedom are just df = J - 1, but there is generally no simple expression and df need not be an integer. For models that include covariates (discussed in detail in chapter 3), the degrees of freedom can be different for each coefficient and only basic summaries (min, avg, and max) are given in the output. In this case, the postestimation command **`estat df`** can be used to obtain the degrees of freedom for each t test. Alternatively, replay the results by typing **`mixed, dftable(pvalue)`**. In display 2.1, we again suggest using the rule of thumb of 42 for deciding how many clusters are enough for relying on asymptotic tests and confidence intervals.

The estimated standard error (26.99) that is used for the z or t statistic takes a different form than in standard linear regression, as will be shown in section 2.10.3. The standard error estimate depends on $ψ$ and hence differs between ML and REML and the latter performs better when the number of clusters is small. (When the **`dfmethod(kroger)`** option is used, a small-sample correction is applied to the estimator of $ψ$ and hence the standard error of $\hat\beta$ but here this did not affect the results.)

As for standard linear regression, robust standard errors based on the sandwich estimator are available. These can be obtained for the maximum likelihood estimator in **`mixed`** or **`xtreg, mle`** and for the feasible generalized least-squares (FGLS) estimator in **`xtreg, re`** with the **`vce(robust)`** option. **However, it should be noted that robust standard errors are known to perform poorly with a small number of clusters. $J<42$**


>For simplicity, and acknowledging that it is a very rough rule of thumb, we provide the single answer "42" (or more) to each of the three questions below. As in Adams (1979) and Angrist and Pischke (2009), this precise number is chosen to highlight that each question is too broad or vague to have a simple answer. When the model includes $q$ cluster-level covariates, $J - q$ should be at least 42. For variance-components models, $q = 0$.
>> How many clusters are needed to use robust (instead of model-based) standard errors? At least 42 (that is, $J - q\geq 42$).
>>>Angrist and Pischke (2009, 319) use $J\geq 42$ as a rule of thumb. Snijders and Bosker (2012, 200) use $J\geq 40$ and recommend multiplying the robust standard errors by $\sqrt{J/(J-q-1)}$ (they also recommend using an approximate finite-sample null distribution). The multiplier becomes substantial when $q$ is large and $J - q$ is not much more than 42. For example, when $J - q = 42$, the multiplier increases from 1.01 to 1.13 when $q$ increases from 0 (that is, the variance-components model) to 10. Snijders and Bosker (2012) and Cameron and Miller (2015) point out that more clusters are needed (than 40 and 50, respectively) when there is great imbalance between clusters.

1. **稳健标准误（Robust Standard Errors）**
稳健标准误是一种在统计分析中用来估计模型参数不确定性的方法。它对异常值和非正态分布的误差项更为稳健，意味着即使数据不是完美符合正态分布，它也能给出相对准确的估计。

1. **群组数据（Clusters）**
当我们处理多群组数据时，比如不同国家、公司或时间段的数据，每个国家、公司或时间段可以被视为一个群组。这些群组可能在某些特征上相似，但在其他特征上不同。

1. **群组数量（J）和群组内变量（q）**
- $ J $：群组的总数。
- $ q $：群组内变量的数量，这些变量是模型中用来解释群组内部差异的因素。

1. **规则：至少42个群组**
- 这里提到的“至少42个群组”是一个经验规则，用来指导我们何时使用稳健标准误。这个数字并不是绝对的，而是一个经验上的参考值。

1. **公式：$ J - q \geq 42 $
- 这个公式告诉我们，在使用稳健标准误时，群组的数量减去群组内变量的数量应该至少是42。这是因为如果群组数量太少，稳健标准误可能无法准确反映模型参数的不确定性。

1. **例子：Snijders和Bosker的建议**
- Snijders和Bosker建议，如果群组数量 $ J $ 至少是40，他们推荐使用一个修正因子来调整稳健标准误，这个修正因子是 $ \sqrt{J/(J-q-1)} $。
- 这个修正因子的目的是调整标准误的大小，使其更能反映群组内的相关性。

1. **修正因子的影响**
- 当 $ q $ 较大，且 $ J - q $ 接近42时，修正因子会变得比较大。这意味着，如果群组内变量很多，即使群组数量接近42，稳健标准误也可能被高估。

1. **不平衡群组**
- 如果群组之间存在很大的不平衡，比如某些群组的样本量远大于其他群组，那么可能需要更多的群组来确保稳健标准误的准确性。

1. **总结**
- 这段内容的核心是，在使用稳健标准误时，群组的数量需要足够多，以确保估计的准确性。42是一个经验上的参考值，但实际情况可能需要根据群组内变量的数量和群组之间的不平衡来调整。


> How many clusters are needed to use maximum likelihood (instead of REML) estimation? At least 42 (that is, $J - q\geq 42$).
>>Snijders and Bosker (2012) argue that $J - q - 1$ should be at least 50 for the difference between ML and REML to be immaterial, both for point estimation of the level-2 variance (page 60) and for estimation of the standard errors of the regression coefficients (page 94). In other words, they recommend 51 instead of 42.
>>One reason we suggest using 42 is that this is the rule of thumb described above for robust standard errors, and we would generally recommend making a switch to ML estimation together with a switch to robust standard errors which do not rely on correct specification of the covariance structure. Robust standard errors are currently not available in mixed with the reml option, but if they were available, they would be expected to be close to the robust standard errors provided by mixed with the ml option when $J - q$ is large.
>>One situation where REML should be used even when the number of clusters is large is to obtain standard errors of empirical Bayes EB predictions that account for the uncertainty in the regression coefficients (and intercept) estimates—see section 2.11.3.


1. **最大似然估计（Maximum Likelihood, ML）**
最大似然估计是一种参数估计方法，它通过寻找能够使观测数据出现概率（似然）最大的参数值来进行估计。

2. **限制最大似然估计（Restricted Maximum Likelihood, REML）**
限制最大似然估计是另一种参数估计方法，它与最大似然估计类似，但对参数空间进行了限制，以避免过度拟合。

3. **群组数量（J）和群组内变量（q）**
- $ J $：群组的总数。
- $ q $：群组内变量的数量，这些变量是模型中用来解释群组内部差异的因素。

4. **经验规则：至少42个群组**
- 这里提到的“至少42个群组”是一个经验规则，用来指导我们何时使用最大似然估计而不是限制最大似然估计。这个数字并不是绝对的，而是一个经验上的参考值。
5. **Snijders和Bosker的建议**
- Snijders和Bosker建议，为了使ML和REML估计的差异变得不重要，$ J - q - 1 $ 应该至少是50。这意味着他们推荐使用51个群组而不是42个。

6. **公式：$ J - q \geq 42 $ 和 $ J - q - 1 \geq 50 $
- 这两个公式告诉我们，在使用最大似然估计时，群组的数量减去群组内变量的数量应该至少是42或50。这是因为如果群组数量太少，ML和REML估计的差异可能会变得显著。

7. **稳健标准误与最大似然估计的结合**
- 稳健标准误是一种不依赖于协方差结构正确指定的标准误估计方法。建议在转换到最大似然估计的同时，也转换到稳健标准误，因为稳健标准误在$ J - q $较大时，与ML估计结合使用时，预计会非常接近。

8. **特殊情况下的REML使用**
- 即使群组数量很大，有时也应该使用REML来获取经验贝叶斯（Empirical Bayes, EB）预测的标准误，特别是当这些预测需要考虑回归系数（和截距）估计的不确定性时。

9. **总结**
- 这段内容的核心是，在选择使用最大似然估计还是限制最大似然估计时，群组的数量是一个重要的考虑因素。虽然42是一个经验上的参考值，但Snijders和Bosker建议使用51作为更保守的估计。此外，稳健标准误与最大似然估计的结合使用可以提供更稳健的估计结果，尤其是在群组数量较大时。




>How many clusters are needed to use asymptotic (instead of approximate finite-sample) null distributions? At least 42 (that is, $J - q\geq 42$).
>>For testing individual regression coefficients, the question is whether the standard normal distribution is an adequate approximation of the t distribution. With 41 degrees of freedom (approximately $J - 1$ for variance-components models and $J - q - 1$ for models with covariates, as mentioned at the end of section 3.6.1), a 95% confidence interval based on the t distribution is only 3% wider than its counterpart based on the standard normal distribution. When the standard normal distribution yields a p value of 0.043, the t distribution yields a p -value of 0.050. In other words, if the asymptotic p-value is between 0.043 and 0.050, the t test will no longer be significant.
>>Note that finite sample approximations are recommended whenever REML is used (that is, when $J - q <42$).



1. **渐近分布（Asymptotic Distribution）**
渐近分布是指当样本量趋于无穷大时，统计量的分布。在实际应用中，我们通常使用渐近分布来近似小样本的分布，特别是在样本量较大时。

2. **有限样本分布（Finite-Sample Distribution）**
有限样本分布是指在有限样本量下，统计量的确切分布。当样本量较小时，使用有限样本分布可以更准确地反映统计量的特性。

3. **群组数量（J）和群组内变量（q）**
- $ J $：群组的总数。
- $ q $：群组内变量的数量，这些变量是模型中用来解释群组内部差异的因素。

4. **经验规则：至少42个群组**
- 这里提到的“至少42个群组”是一个经验规则，用来指导我们何时可以使用渐近分布来代替有限样本分布。这个数字是一个经验上的参考值，用来确保渐近分布的近似是合理的。

5. **标准正态分布与t分布的比较**
- 在进行回归系数的假设检验时，我们通常关心的是标准正态分布是否足以近似t分布。t分布用于小样本情况下，考虑了样本量对分布的影响。
- 当自由度为41（大约等于 $ J - 1 $ 对于方差分量模型，或 $ J - q - 1 $ 对于包含协变量的模型）时，基于t分布的95%置信区间只比基于标准正态分布的置信区间宽3%。

6. **p值的比较**
- 当标准正态分布给出的p值为0.043时，t分布给出的p值是0.050。这意味着，如果渐近的p值在0.043和0.050之间，那么基于t检验的结果将不再显著。

7. **有限样本近似的推荐**
- 当使用限制最大似然估计（REML）时，即当 $ J - q < 42 $ 时，推荐使用有限样本近似。这是因为在群组数量较少的情况下，渐近分布可能不够准确，而有限样本分布能提供更精确的估计。

8. **总结**
- 这段内容的核心是，在决定使用渐近分布还是有限样本分布时，群组的数量是一个关键因素。经验规则建议至少需要42个群组来使用渐近分布。然而，当群组数量较少时，使用有限样本分布更为合适。

### 6.2 Hypothesis test and confidence interval for the between-cluster variance


We now consider testing hypotheses regarding the between-cluster variance,$\psi$. In particular, we are often interested in the null hypothesis

$$H_0:\psi=0\quad\text{against}\quad H_a:\psi>0$$

This null hypothesis is equivalent to the hypothesis that $\beta_j^*=0$ or that there is no random intercept in the model. If the null hypothesis is true, we can use ordinary regression instead of a variance-components model.

The test we will be using most in this book for testing variance components is the likelihood-ratio test.

在统计分析中，我们经常使用似然比检验来测试群组间方差 $ \psi $ 是否为零的假设。如果零假设 $ H_0: \psi = 0 $ 成立，意味着不存在群组间的变异，可以采用普通回归模型；如果群组间存在显著差异（即备择假设 $ H_a: \psi > 0 $ 成立），则需要使用方差分量模型来更准确地描述数据结构。似然比检验通过比较零假设和备择假设下的似然来决定是否拒绝零假设。


* **Likelihood-ratio test**
A likelihood-ratio test can be obtained by fitting the model with and without the random intercept. The likelihood-ratio test statistic then is

$$L = 2(l_1-l_0)$$


where $l_1$ is the maximized log likelihood for the variance-components model (which includes $\zeta_j$) and $l_0$ is the maximized log likelihood for the model without $\zeta_j$. If REML is used, $l_1$ and $l_0$ are the corresponding REML likelihoods.

Importantly, the distribution of $L$ under $H_0$ is not $\chi^2$ with 1 degree of freedom (df) as usual. This is because the null hypothesis is on the boundary of the parameter space since $\psi\geq 0$, violating the regularity conditions of standard statistical test theory.

For datasets simulated under the null hypothesis, without the random intercept, we would expect positive within-cluster correlations in about half the datasets and negative within-cluster correlations in the other half. Thus,$\psi$would be estimated as positive half the time and as 0 (because $\psi$ would have to be negative to produce negative correlations but is constrained to be nonnegative) the other half the time. The correct asymptotic sampling distribution under the null hypothesis hence takes the simple form of a 50:50 mixture of a spike at 0 and $a^2$with 1 df, often written as$0.5\chi^2(0)+0.5\chi^2(1)$, where $\chi^2(0)$ is a spike of height 1 at 0. The correct $p$-value can be obtained by simply dividing the "naive" $p$-value, based on the $\chi^2$ with 1 df, by 2. These results were derived for ML by Stram and Lee (1994) and others, and Morrell (1998) showed that they also hold for REML.

The p-value based on the mixture of chi-square distributions is given at the bottom of the **`xtreg`** and **`mixed`** output, where the correct sampling distribution is referred to as **chibar2(01)** (click on **chibar2(01)**, which is shown in blue in the Stata Results window to find an explanation). We can also perform the likelihood-ratio test (based on REML) ourselves by fitting the variance-components model, storing the estimates, then fitting the model without the random intercept, and finally comparing the models by using the **`lrtest`** command:


1. **似然比检验的基本概念**
似然比检验是一种统计检验方法，用来比较两个模型的拟合优度。这里的两个模型分别是包含随机截距的方差分量模型（模型1）和不包含随机截距的普通回归模型（模型0）。

2. **似然函数和对数似然**
- 似然函数（Likelihood function）是一个关于模型参数的函数，表示给定参数时观测数据出现的概率。
- 对数似然（Log likelihood）是似然函数的自然对数，通常用于简化计算。

3. **似然比检验的计算**
- 似然比检验统计量 $ L $ 的计算公式是：
  
$$L = 2(l_1 - l_0)$$
  
  其中，$ l_1 $ 是包含随机截距的方差分量模型的最大对数似然值，$ l_0 $ 是不包含随机截距的模型的最大对数似然值。

4. **似然比检验统计量的分布**
- 在零假设 $ H_0 $ 下，即群组间方差 $ \psi = 0 $ 时，$ L $ 的分布不是标准的卡方分布（$ \chi^2 $ 分布）与1自由度。这是因为零假设位于参数空间的边界上，$ \psi $ 必须非负，这违反了标准统计检验理论的正则性条件。

5. **零假设下的分布特性**
- 在零假设下，我们期望大约一半的数据集显示出群体内的正相关，另一半显示出负相关。因此，$ \psi $ 有一半的时间被估计为正，另一半时间被估计为0（因为 $ \psi $ 必须是负的才能产生负相关，但它被限制为非负）。

6. **正确的渐近抽样分布**
- 正确的渐近抽样分布是一个简单的混合分布，形式为：
  
  $$ 0.5\chi^2(0) + 0.5\chi^2(1) $$ 
  
  这里，$ \chi^2(0) $ 是在0处高度为1的尖峰，表示 $ \psi = 0 $ 的情况。

7. **p值的计算**
- 基于上述混合分布的p值可以通过将基于 $ \chi^2 $ 与1自由度的“天真”p值除以2来获得。

8. **软件实现**
- 在某些统计软件（如Stata）中，可以直接使用命令 `xtreg` 和 `mixed` 来拟合模型，并使用 `lrtest` 命令来执行似然比检验。正确的抽样分布在软件输出中可能被称为 **chibar2(01)**。

9. **总结**
- 似然比检验是一种比较包含和不包含随机截距的两个模型拟合优度的方法。检验统计量的计算基于两个模型的最大对数似然值之差。在零假设下，检验统计量的分布是一个混合分布，需要特别的方法来计算p值。


```
quietly mixed wm || id:, reml
estimates store ri
quietly mixed wm, reml
lrtest ri .
```
    
    Likelihood-ratio test
    Assumption: . nested within ri
    
     LR chi2(1) =  46.96
    Prob > chi2 = 0.0000
    
    Note: The reported degrees of freedom assumes the null hypothesis is not on the boundary of the
          parameter space. If this is not true, then the reported test is conservative.
    Note: LR tests based on REML are valid only when the fixed-effects specification is identical for both
          models.
    
Here the **`quietly prefix`** command is used to suppress output from **`mixed`**. In the **`lrtest`** command, **`ri`** refers to the estimates stored under that name, and “.” refers to the current (or last) estimates. As the note in the output and the notation **LR chi2(1)** imply, we now have to divide the p-value by 2. We see that the test of the null hypothesis has a very small p-value, and the null hypothesis is rejected at standard significance levels.

Recall that the number of clusters is only 17, perhaps too few to rely on the asymptotic distribution of the likelihood-ratio statistic. The same comment applies to the score test described below.

It does of course not make sense to test the null hypothesis that $\theta = 0$, or in other words that all_{ij} = 0$, because this would force all responses $y_{ij}$ for the same cluster $j$ to be identical.

❖ Score test

There are two approximations to the likelihood-ratio statistic: the Wald statistic and the score or Lagrange multiplier statistic (see display 2.2 below if you are interested in the details). For variance parameters, Wald tests do not perform well but score tests can be used. Breusch and Pagan’s Lagrange multiplier test is a score test based on a quadratic approximation of the log likelihood at . The implementation in Stata is based on the FGLS estimator (see section 2.10.2), obtained using the **`re`** option of **`xtreg`**:

```
quietly xtset id
quietly xtreg wm, re
xttest0
```

The test is then performed using the postestimation command **`xttest0`**:


    Breusch and Pagan Lagrangian multiplier test for random effects
    
            wm[id,t] = Xb + u[id] + e[id,t]
    
            Estimated results:
                             |       Var     SD = sqrt(Var)
                    ---------+-----------------------------
                          wm |   12214.63       110.5198
                           e |   396.4412       19.91083
                           u |   12187.51        110.397
    
            Test: Var(u) = 0
                                 chibar2(01) =    15.88
                              Prob > chibar2 =   0.0000

Leading to the same conclusion as before. Here the p-value has already been divided by 2, taking into account that the null hypothesis is on the boundary of the parameter space.


The graph below shows twice the log likelihood, $2l\psi)$, as a function of the parameter $\psi$. (If there are other parameters, this is twice the profile likelihood, maximized with respect to the other parameters.) The maximum of this curve is at the maximum likelihood estimate $\hat\psi}$. We describe three statistics for testing $H_0:\psi = 0$:
- The likelihood-ratio statistic is given by $2\{l\hat\psi}) - l(0\}$ and is represented by the arrow labeled "L" pointing from $2l\psi)$ to $2l(0)$.
- The Wald statistic is based on approximating the function $2l\psi)$ by the dotted quadratic curve at the maximum likelihood estimate $\hat\psi}$. The value of this curve at $\psi = 0$ is an approximation of $2l(0)$, and the Wald statistic is the decrease in the quadratic curve from $\hat\psi}$ to $\psi = 0$, as shown by the arrow labeled "Wald". Because the quadratic approximation to twice the log likelihood at the mode is $2l\hat\psi}) -\frac{\psi -\hat\psi})^2}{SE\hat\psi})^2}$, it follows that the Wald statistic is $\{\hat\psi} / SE\hat\psi}))^2\}$.
- The score statistic is based on approximating $2l\psi)$ by the dashed quadratic curve at the null hypothesis value,$\psi = 0$. The maximum value of this curve is an approximation of $2l\hat\psi})$; the score statistic is the difference between that maximum and $2l(0)$, as shown by the arrow labeled "Score".

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.4.png" style="width:550px;height:410px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

1. **基本概念**
假设我们有一个参数 $ \psi $，它表示群组间的方差。我们想要测试的零假设 $ H_0 $ 是 $ \psi = 0 $，即群组间没有差异。
1. **对数似然函数**
- 对数似然函数 $ 2l(\psi) $ 是参数 $ \psi $ 的函数，表示在给定 $ \psi $ 时观测数据的对数似然的两倍。
1. **最大似然估计**
- 最大似然估计 $ \hat{\psi} $ 是使 $ 2l(\psi) $ 最大的 $ \psi $ 值，即对数似然函数的最大点。
1. **三种统计量**
- **似然比统计量（Likelihood-ratio statistic）**：这个统计量通过比较 $ \psi = \hat{\psi} $ 时的对数似然和 $ \psi = 0 $ 时的对数似然来计算。如果 $ \psi = 0 $ 时的对数似然很低，而 $ \psi = \hat{\psi} $ 时的对数似然明显更高，这表明 $ \psi $ 不为零的可能性很大。似然比统计量的计算公式是 $ 2l(\hat{\psi}) - l(0) $。
- **Wald统计量**：这个统计量基于在 $ \hat{\psi} $ 处对 $ 2l(\psi) $ 进行二次近似。想象一下，如果我们在 $ \hat{\psi} $ 附近画一个抛物线（二次曲线），这个抛物线在 $ \psi = 0 $ 时的值就是 $ 2l(0) $ 的近似。Wald统计量是这个抛物线从 $ \hat{\psi} $ 到 $ \psi = 0 $ 降低的量。公式是 $ (\hat{\psi} / SE(\hat{\psi}))^2 $，其中 $ SE(\hat{\psi}) $ 是 $ \hat{\psi} $ 的标准误。
- **得分统计量（Score statistic）**：这个统计量基于在 $ \psi = 0 $ 处对 $ 2l(\psi) $ 进行的二次近似。如果我们在 $ \psi = 0 $ 附近画一个抛物线，这个抛物线的最大值就是 $ 2l(\hat{\psi}) $ 的近似。得分统计量是这个最大值和 $ 2l(0) $ 之间的差值。
1. **图形化理解**
- 想象一下，我们有一个山脉的地形图，其中 $ 2l(\psi) $ 表示海拔高度，$ \psi $ 表示沿着山脉的位置。最大似然估计 $ \hat{\psi} $ 就是山脉的最高点。
- 似然比统计量就像是从山脚（$ \psi = 0 $）到山顶（$ \psi = \hat{\psi} $）的垂直距离。
- Wald统计量就像是从山顶到山脚画一条平滑曲线，然后计算这条曲线从山顶到山脚下降的高度。
- 得分统计量就像是在山脚画一条平滑曲线，然后计算这条曲线的最高点和山脚之间的高度差。
1. **总结**
- 这三种统计量都是用来测试 $ \psi $ 是否为零的不同方法。它们各自以不同的方式近似 $ 2l(\psi) $ 并计算 $ \psi $ 从零到估计值的变化量。在实际应用中，我们通常会计算这三种统计量，并根据它们的p值来判断是否拒绝零假设。

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.2.png" style="width:550px;height:410px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

The Wald statistic is obtained by fitting only the unconstrained model, whereas the score statistic is obtained by fitting only the constrained model. A nice feature of the score statistic is that several model extensions can be tested by fitting only one model.

Adapted from Brian Ripley’s notes for a course on Applied Statistics at the University of Oxford in 2005.

* **F test**

We can also base the test for unexplained between-cluster heterogeneity on a regression model that includes dummy variables for clusters instead of including a random intercept for clusters. Such a model can be thought of as a one-way ANOVA model with clusters as a (fixed) factor. As will be discussed in section 3.7.2, the model is often called a fixed-effects model because clusters are represented by fixed rather than random effects.

The natural test in this setting is an F test for the joint null hypothesis that all clusters have the same mean (or that the coefficients of the 16 dummy variables are all 0). This F test can be obtained using **`xtreg`** with the **`fe`** (where fe stands for “fixed effects”) option:

```
quietly xtset id
xtreg wm, fe
```
    
    Fixed-effects (within) regression               Number of obs     =         34
    Group variable: id                              Number of groups  =         17
    
    R-squared:                                      Obs per group:
         Within  =      .                                         min =          2
         Between =      .                                         avg =        2.0
         Overall =      .                                         max =          2
    
                                                    F(0, 17)          =       0.00
    corr(u_i, Xb) =      .                          Prob > F          =          .
    
    ------------------------------------------------------------------------------
              wm | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |     453.91       3.41   132.93   0.000       446.71      461.12
    -------------+----------------------------------------------------------------
         sigma_u |  111.29118
         sigma_e |  19.910831
             rho |  .96898482   (fraction of variance due to u_i)
    ------------------------------------------------------------------------------
    F test that all u_i=0: F(16, 17) = 62.48                     Prob > F = 0.0000

We see from the bottom of the output that the null hypothesis is clearly rejected. (Use of the **`xtreg`** command with the **`fe`** option is discussed in detail in section 3.7.2.)


If the uncertainty in the estimated level-1 residual variance is ignored and $\hat\theta}$ is treated as the true $\theta$, then the $F$ test can be replaced by $a^2$ test. A similar $\chi^2$ test is described in Raudenbush and Bryk (2002) and implemented in the HLM software of Raudenbush et al. (2019). Instead of basing the $\chi^2$ statistic on the estimated mean $\hat\beta$ from the fixed-effects model, they base it on the estimated mean from the variance-components model.

* **Confidence intervals**

Both **`mixed`** and **`xtreg`** report confidence intervals for the random-intercept standard deviation. These intervals are obtained by exponentiating the limits of the Wald confidence interval for the log standard deviation. The reason for this is that the sampling distribution of $\log(\hat{\psi})$ approaches normality faster than that of $\hat{\psi}$ as the number of clusters increases. The intervals may be valid, as long as the lower limit is not too close to 0.

## 7 Model as data-generating mechanism

Figure 2.7 shows how the responses $y_{ij}$ can be viewed as resulting from sequential (or hierarchical) sampling, first of $\zeta_j$ and then of $y_{ij}$ given $\zeta_j$. For concreteness, we consider normal distributions for $\zeta_j$ and $\epsilon_{ij}$, but these distributional assumptions are usually not important for inferences.

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.5.png" style="width:550px;height:410px;" alt="图片描述">
  <figcaption><strong>Figure 2.7: Illustration of hierarchical sampling in variancecomponents model</strong></figcaption>
</figure>

As seen in the top of the figure, the random intercept $ζ_j$ has a normal distribution with mean 0 (and variance $\psi$). Drawing a realization from this distribution for subject $j$ determines the mean $\beta + ζ_j$ of the distribution from which responses $y_{ij}$ for this subject are subsequently drawn. At a given measurement occasion $i$, a response $y_{ij}$ is therefore sampled from a normal distribution with mean $\beta + ζ_j$ (and variance $\theta$), $y_{ij} \sim N(\beta + ζ_j, \theta)$ (see the bottom distribution in the figure). Equivalently, a residual (or measurement error) $\epsilon_{ij}$ is drawn from a normal distribution with mean 0 and variance $\theta$. The hierarchical sampling perspective is the reason why multilevel models are sometimes called hierarchical models. In this description, we have viewed the variance-components model as the data-generating mechanism for $y_{ij}$ for given occasions and subjects.

The variance-components model is often motivated in terms of two-stage survey sampling, namely random sampling of clusters, such as schools, followed by random sampling of units (for example, students) from clusters. In this view, the top distribution of figure 2.7 represents the distribution of cluster means $\beta + \zeta_j$ in the population of clusters, where each cluster comes with a realized value of $\zeta_j$. In stage 1, cluster $j$ is sampled, and the bottom distribution represents the population of units in that cluster, where each unit in the cluster comes with a realized value of $\epsilon_{ij}$ and hence $y_{ij}$. We then randomly draw units from the cluster population, which determines the $y_{ij}$ in our sample.


However, when motivating the model through survey sampling, it is important to remember that the sampling itself does not produce the within-cluster dependence. Such dependence must already exist in the population from which the sample was drawn, which we shall refer to as the finite population (because it is not infinite). Two-stage sampling merely guarantees that the sample contains multiple units per cluster, making it possible to separately estimate the between-cluster and within-cluster variance components $ψ$ and $θ$. In contrast, a simple random sample of, say, 1,000 students from all U.S. high school students would be unlikely to contain any two students belonging to the same school, so we could only estimate the total variance, $ψ+θ$, not the separate variance components.

Because the dependence preexists in the finite population, it is more useful to think of the variance-components model as the data-generating mechanism that generated the responses $y_{ij}$ for the finite population. Furthermore, it is the model parameters $\beta$, $\psi$, and $\theta$ of this underlying variance-components model that we wish to estimate, not any finite population characteristic. Even if the sample contained the entire finite population, that is, all high school students in the U.S., we would still estimate the parameters with error, because all we have observed is a realization from the model, albeit for a large number of clusters and units. This imprecision of parameter estimates is even more pronounced if the finite population is small, for instance, all high school students in Monaco.

When the model is viewed as a data-generating mechanism, randomness comes from drawing the response from a distribution [the $z_j$ from $N(0, \psi)$ and the $\epsilon_{ij}$ from $N(0, \theta)$, resulting in $y_{ij} = \beta + z_j + \epsilon_{ij}$], not only from sampling a subset of units from a finite population. In the survey sampling literature, inference about the data-generating mechanism is referred to as superpopulation inference because data on the finite population (for example, all U.S. high school students) still represent only a sample from the model. Remembering that we wish to make inferences regarding the data-generating mechanism is particularly important in multilevel modeling, where it is not unusual to have data on the entire finite population of clusters (for example, all U.S. states) or the entire finite population of units within clusters (for example, both eyes on each head).

We can think of the randomness in the data as arising from two sources: the data-generating mechanism that produced the $y_{ij}$ for the finite population and survey sampling of a subset of the finite population into the sample. When the parameters of interest are finite population characteristics, such as the proportion of eligible U.S. voters intending to vote for a given presidential candidate, the only randomness we need to care about when making inferences is randomness due to survey sampling, that is, sampling individuals from the finite population (as in an opinion poll). Such inferences, taking into account design features such as stratification, primary sampling units, and sample selection probabilities, are called design-based inferences. (In Stata, design-based inference is performed using the `svyset` command and the `svy` prefix command.)

When estimating model parameters of the data-generating mechanism (or superpopulation or infinite population parameters) rather than finite population characteristics, randomness due to drawing units from the finite population is often ignored, and the inferences are called “model based”. For single-level data, ignoring randomness due to survey sampling is legitimate for simple (equal probability) random sampling (conditional on the covariates) because under such a design, the finite population distribution also holds in the sample. This means that the responses in the sample can be viewed as directly drawn from the model. For multilevel data, another design that preserves the distribution is two-stage sampling if simple random sampling (conditional on the covariates) is employed in each stage. Such sampling designs are called “ignorable”.

To summarize, in this book we make inferences regarding the parameters of statistical models or data-generating mechanisms under the assumption that the sampling design is ignorable. We see no problem with applying this approach to data that contain the entire finite population of clusters or the entire finite population of units within clusters.


## 8 Fixed versus random effects


In the peak-expiratory-flow data, each subject $j$ has a different effect $\zeta_j$ on the measured peak-expiratory-flow rates. In analysis of variance (ANOVA) terminology (see sections 1.4 and 1.9), the subjects can therefore be thought of as the levels of a factor or categorical explanatory variable. Because the effects of subjects are random, **the variance-components model is therefore sometimes referred to as a one-way random-effects ANOVA model.**

The one-way random-effects ANOVA model can be written as

$$y_{ij} = \beta+\zeta_j+\epsilon_{ij},\quad E(\epsilon_{ij}|\zeta_j)=0,E(\zeta_j)=0,\mathrm{Var}(\epsilon_{ij}|\zeta_j)=\theta,\mathrm{Var}(\zeta_j)=\psi(2.6)$$

where $\zeta_j$ is a random intercept. In contrast, the one-way fixed-effects ANOVA model is

$$y_{ij} = \beta+\alpha_j+\epsilon_{ij},\quad E(\epsilon_{ij})=0,\mathrm{Var}(\epsilon_{ij})=\theta,\quad\sum_{j=1}^J\alpha_j=0\quad(2.7)$$


where $α_j$ are unknown, fixed, cluster-specific parameters. In the random-effects model, the random intercepts are uncorrelated across clusters and uncorrelated with the level-1 residuals. In both models, the level-1 residuals are uncorrelated across units. Both random-effects models and fixed-effects models include cluster-specific intercepts- $ζ_j$ and $α_j$, respectively-to account for unobserved heterogeneity. Thus, a natural question is whether to use a random- or fixed-effects approach.

where $alpha_j$; are unknown, fixed, cluster-specific parameters. In the random-effects model, the random intercepts are uncorrelated across clusters and uncorrelated with the level-1 residuals. In both models, the level-1 residuals are uncorrelated across units. Both random-effects models and fixed-effects models include cluster-specific intercepts-$zeta_j$ and $alpha_j$, respectively-to account for unobserved heterogeneity. Thus, a natural question is whether to use a random- or fixed-effects approach.

One way of answering this question is by being explicit about the target of inference, namely, whether interest concerns the population of clusters or the particular clusters in the dataset. Here the "population of clusters" refers to the infinite population, or the data-generating mechanism for the clusters. If we are interested in the population of clusters, the random-effects model is appropriate. In that model, $\beta$ represents the population mean for the population of clusters (and for each cluster, the population of units in the cluster) and $\psi$ represents the variance for the population of clusters. The model specifies how the cluster-specific means $\beta + \zeta_j$ are generated. In the variance-components model, $\psi$ represents between-cluster variability due to cluster-level random (unexplained) processes that affect the response variable. As we will see in later chapters, the data-generating model for the cluster means can also contain cluster-level covariates to explain between-cluster variability.

If we do not wish to generalize beyond the particular clusters in the sample, the fixed-effects model is appropriate. In that model, $\beta$ represents the mean for the sample of clusters (and for each cluster, the population of units in the cluster). The model allows each cluster to have a different mean $\beta + \alpha_j$, but does not specify how the means are generated. If the cluster means were generated by a random process, we merely condition on their realized values and do not learn about the process. It is not possible to include cluster-level covariates in fixed-effects models, so in this approach, no attempt is made to explain between-cluster variability.

Standard errors, confidence intervals, and p-values are based on the notion of repeated samples of data from a model. For instance, the standard error for $\hat{\beta}$ is the standard deviation of the estimates over repeated samples. **In the random-effects approach, the random intercepts $\zeta_j$ change in repeated samples (in addition to $\epsilon_{ij}$). New clusters are drawn and new units are drawn from the new clusters. In contrast, in the fixed-effects approach the fixed intercepts $\alpha_j$ remain constant in repeated samples (only $\epsilon_{ij}$ changes). New units are drawn from the existing clusters.** In section 2.10.3, we see that this leads to a larger standard error for $\hat{\beta}$ in the random-effects approach (compared with the fixed-effects approach) because we are generalizing to the population of clusters and not just making inferences for the particular clusters in the data.


As we will see in section 2.11, the random-effects approach allows the $\zeta_j$ to be predicted after estimating the model parameters. In that sense, we can make inferences regarding the effects of clusters in the sample. These predictions can have better properties than the estimates of $\alpha_j$ in the fixed-effects approach (because of "shrinkage" or "partial pooling"), and this is sometimes the reason for adopting a random-effects approach. 

A necessary assumption when treating the cluster effects as random is that they are exchangeable in the sense that their joint distribution (across clusters) does not change if the cluster labels $j$ are permuted. In other words, there is no a priori ordering or grouping of the clusters. This assumption may appear unreasonable if the clusters are, for instance, countries. Sweden seems very different from Nigeria. While the same could be said about two individual people, an important difference is that the distinct nature of different countries is known to us a priori. When studying country-level processes (or data-generating mechanisms), such as effects of different healthcare systems, the aim is to make inferences that generalize to countries not included in the sample. It is then necessary to view countries as exchangeable, after conditioning on country-level covariates.

A fixed-effects approach should be used if it does not make sense at all to think of the clusters in the sample merely as examples of possible clusters, as it may not make sense, for example, to think of male and female as examples of possible (biological) sexes. In this case, we do not want to generalize to any population. Another reason for using fixed effects is when the differences between clusters are of primary interest, for example when the clusters are treatments in a clinical trial or countries in an international comparison study.

A random-effects approach should be used only if there is a sufficient number of clusters in the sample, typically more than 10 or 20. The reason for this is that the between-cluster variance $\psi$ is poorly estimated if there are few clusters, even if REML is used. Poor estimation of $\psi$ translates to poor estimation of the standard error of $\hat{\beta}$. In two-level models, large-sample properties or asymptotics, such as consistency of point estimates and standard errors, rely on the number of clusters going to infinity, possibly for fixed cluster sizes. In contrast, the fixed-effects approach does not rely on a large number of clusters as long as the total sample size is large. 

Regarding cluster sizes, these need not be large in either approach unless inference regarding the cluster-specific intercepts is of interest. If clusters are small and cluster-specific inference is of interest, the random-effects approach is superior because of "shrinkage" or "partial pooling" (see section 2.11). For parameter estimation in random-effects models, it is only required that there are a good number of clusters of size 2 or more; it does not matter if there are also "clusters" of size 1. Such singleton clusters do not provide information on the within-cluster correlation or on how the total variance is partitioned into $\psi$ and $\theta$, but they do contribute to the estimation of $\beta$ and $\psi + \theta$.

In the peak-expiratory-flow application, the one-way fixed-effects ANOVA model has 19 parameters ($\beta$, $\alpha_1$, ..., $\alpha_{17}$, $\theta$) and one constraint $\sum\limits_{j}\alpha_j = 0$. The one-way random-effects ANOVA model is thus much more parsimonious, having only 3 parameters ($\beta$, $\psi$, $\theta$).


### **第一部分：随机效应模型与固定效应模型的基本概念**

#### **随机效应模型（Random-Effects Model）**

随机效应模型假设，不同的组（比如不同的受试者）对测量结果有不同的影响，这种影响是随机的。在方差分析中，我们可以将不同的受试者视为一个因素的不同水平。这里的“随机”意味着每个受试者的影响是从一个共同的分布中随机抽取的。

**公式：**
$y_{ij} = \beta + \zeta_j + \epsilon_{ij}$

- $y_{ij}$：第$i$个观测值在第$j$个组中的结果。
- $\beta$：总体均值。
- $\zeta_j$：第$j$个组的随机截距（即受试者效应）。
- $\epsilon_{ij}$：第$i$个观测值在第$j$个组中的随机误差。

**期望和方差：**

- $E(\epsilon_{ij}|\zeta_j) = 0$：给定受试者效应，误差项的期望为0。
- $E(\zeta_j) = 0$：所有受试者效应的平均值为0。
- $\mathrm{Var}(\epsilon_{ij}|\zeta_j) = \theta$：给定受试者效应，误差项的方差为$\theta$。
- $\mathrm{Var}(\zeta_j) = \psi$：受试者效应的方差为$\psi$。

#### **固定效应模型（Fixed-Effects Model）**

固定效应模型假设，不同的组对测量结果有不同的影响，但这种影响是固定的，不是随机的。这意味着每个组的影响是已知的，并且不会随着样本的不同而变化。
**公式：**
$y_{ij} = \beta + \alpha_j + \epsilon_{ij}$
- $\alpha_j$：第$j$个组的固定效应。
**期望和方差：**
- $E(\epsilon_{ij}) = 0$：误差项的期望为0。
- $\mathrm{Var}(\epsilon_{ij}) = \theta$：误差项的方差为$\theta$。
- $\sum_{j=1}^J \alpha_j = 0$：所有组的固定效应之和为0。


### 第二部分：随机效应与固定效应模型的选择
1. **目标的推断**：如果你对所有可能的组（比如所有可能的受试者）感兴趣，随机效应模型更合适。如果你只对样本中的特定组感兴趣，固定效应模型更合适。
2. **标准误、置信区间和p值**：随机效应模型在重复抽样时，随机截距会变化，导致$\hat{\beta}$的标准误更大。固定效应模型中，固定截距在重复抽样时保持不变。
3. **预测和推断**：随机效应模型允许对组效应进行预测，这些预测可能比固定效应模型的估计具有更好的属性。
4. **可交换性**：随机效应模型要求组效应在交换组标签后具有相同的联合分布，即没有先验的顺序或分组。
5. **固定效应的使用**：当样本中的组不能代表所有可能的组时，或者当组间差异是主要兴趣时，应使用固定效应模型。
6. **随机效应的使用**：当样本中有足够的组（通常超过10或20个）时，应使用随机效应模型。
7. **组大小**：两种方法都不需要大的组大小，除非对组特定的截距进行推断。
8. **模型参数**：随机效应模型通常更简洁，因为它只需要估计更少的参数。


### 第三部分：随机效应与固定效应模型的进一步理解

#### **随机效应模型的应用场景**

1. **泛化到更大的群体**：如果你的研究目标是泛化到一个更大的群体，比如从研究几个学校的学生泛化到所有学校的学生，随机效应模型是合适的。因为随机效应模型允许我们估计群体间变异性（$\psi$）。
2. **预测组效应**：在随机效应模型中，我们可以预测每个组的效应（$\zeta_j$）。这种预测通常比固定效应模型中的估计更为精确，因为它们利用了群体间的平均信息，这种现象被称为“收缩”或“部分汇总”。
3. **交换性假设**：随机效应模型要求组效应是交换的，这意味着如果我们重新标记组，组效应的联合分布不会改变。这在实际中可能不总是合理的，比如不同国家的效应可能由于文化、经济等因素而有本质的不同。

#### **固定效应模型的应用场景**

1. **特定群体的分析**：如果你只对研究中的特定群体感兴趣，比如只关心研究中包含的几个学校，固定效应模型是合适的。
2. **不泛化**：固定效应模型不涉及泛化到更大的群体，它只关注样本中的组。因此，它不估计群体间变异性，也不会提供关于群体间差异的信息。
3. **组间差异的主要兴趣**：如果研究的主要兴趣在于比较不同组之间的差异，固定效应模型可能更合适，因为它允许每个组有一个独特的效应。

#### **模型选择的考虑因素**

1. **样本大小**：随机效应模型通常需要更多的组来准确估计群体间变异性。如果组的数量较少，固定效应模型可能是更好的选择。
2. **组内和组间变异性**：如果组内的变异性远大于组间的变异性，固定效应模型可能更合适。相反，如果组间的变异性是一个重要的研究问题，随机效应模型可能更合适。
3. **模型的简洁性**：随机效应模型通常参数更少，更简洁。固定效应模型可能会因为每个组都有一个独特的参数而变得复杂。
4. **数据结构**：如果数据结构允许，比如有足够的组和每个组中有足够的观测值，随机效应模型可以提供更丰富的信息。

### 第四部分：实际例子和应用

让我们通过一个实际的例子来进一步理解这些概念：
假设我们正在研究不同学校学生的数学成绩。我们有来自5个不同学校的学生的成绩数据。
- **随机效应模型**：我们可能会假设每个学校有一个影响学生成绩的随机效应。我们感兴趣的是所有可能学校的学生成绩的总体趋势和学校间成绩的变异性。在这个模型中，我们可以估计所有学校学生的平均成绩（$\beta$），学校间成绩的变异性（$\psi$），以及每个学校相对于平均水平的成绩差异（$\zeta_j$）。
- **固定效应模型**：如果我们只对这5个特定学校的学生成绩感兴趣，并且不打算将结果泛化到其他学校，我们可能会选择固定效应模型。在这个模型中，每个学校都有一个固定效应（$\alpha_j$），代表了该学校学生成绩的平均差异。
通过这个例子，我们可以看到随机效应模型和固定效应模型在实际研究中的应用和它们的区别。


### 第五部分：模型估计和模型选择的统计考量

#### 模型估计
1. **参数估计**：在随机效应模型中，我们估计的是总体均值（$\beta$）、群体间变异性（$\psi$）和误差项的方差（$\theta$）。在固定效应模型中，我们估计的是总体均值（$\beta$）和每个组的固定效应（$\alpha_j$），以及误差项的方差（$\theta$）。
2. **限制条件**：固定效应模型中，为了可识别性，需要有一个限制条件，比如 $\sum_{j=1}^J \alpha_j = 0$。这意味着所有组的固定效应之和必须为零。
3. **随机效应的估计**：在随机效应模型中，$\zeta_j$ 是随机变量，它们的估计通常涉及到复杂的统计技术，如限制最大似然估计（REML）或贝叶斯方法。

#### 模型选择
1. **信息准则**：可以使用Akaike信息准则（AIC）或贝叶斯信息准则（BIC）来比较不同模型的拟合优度。通常选择信息准则值较低的模型。
2. **假设检验**：可以使用F检验或似然比检验（LRT）来比较随机效应模型和固定效应模型的拟合优度。如果随机效应模型的拟合显著优于固定效应模型，可能会倾向于选择随机效应模型。
3. **交叉验证**：在某些情况下，可以使用交叉验证来评估模型的预测能力。如果随机效应模型在预测新数据时表现更好，这可能是选择它的一个理由。
4. **理论依据**：最终，模型的选择应该基于理论依据和研究问题。如果理论或先前的研究支持群体间存在随机变异性，随机效应模型可能更合适。

### 第六部分：实际数据分析示例
让我们通过一个简单的数据分析示例来说明这些概念：
假设我们有以下数据集，它包含了三个不同学校（A、B、C）的学生数学成绩：
| 学生编号 | 学校 | 数学成绩 |
|----------|------|----------|
| 1        | A    | 75       |
| 2        | A    | 85       |
| ...      | ...  | ...      |
| 20       | C    | 80       |

#### 随机效应模型分析

1. **模型设定**：设定 $y_{ij} = \beta + \zeta_j + \epsilon_{ij}$，其中 $i$ 表示学生，$j$ 表示学校。
2. **参数估计**：估计 $\beta$、$\zeta_j$（对于学校A、B、C）和 $\theta$。
3. **结果解释**：$\beta$ 表示所有学校学生的平均成绩，$\zeta_j$ 表示每个学校相对于平均水平的成绩差异。
#### 固定效应模型分析
1. **模型设定**：设定 $y_{ij} = \beta + \alpha_j + \epsilon_{ij}$，其中 $\alpha_j$ 是学校的固定效应。
2. **参数估计**：估计 $\beta$、$\alpha_A$、$\alpha_B$、$\alpha_C$ 和 $\theta$，并应用限制条件 $\alpha_A + \alpha_B + \alpha_C = 0$。
3. **结果解释**：$\beta$ 表示基准学校（假设为学校A）的平均成绩，$\alpha_B$ 和 $\alpha_C$ 表示学校B和C相对于学校A的成绩差异。

### 第七部分：模型诊断和模型假设检验
#### 模型诊断
1. **残差分析**：检查残差是否随机分布，没有模式或趋势。这可以帮助我们确定模型是否适当。
2. **方差齐性检验**：检查不同组的残差方差是否一致。如果残差方差不同，可能需要考虑更复杂的模型。
3. **正态性检验**：检查残差是否服从正态分布。如果残差不服从正态分布，可能需要转换数据或使用非参数方法。
4. **随机效应的显著性检验**：检查随机效应是否显著不同于零。如果随机效应不显著，可能不需要随机效应模型。
#### 模型假设检验
1. **固定效应与随机效应的比较**：可以使用LRT（似然比检验）来比较固定效应模型和随机效应模型的拟合优度。
2. **随机效应的显著性**：可以使用Wald检验或似然比检验来检验随机效应是否显著。如果不显著，固定效应模型可能是更好的选择。
3. **交叉项的检验**：如果模型中包括交互作用，需要检验这些交互作用是否显著。

### 第八部分：模型的实用性和局限性

#### 实用性
1. **灵活性**：随机效应模型和固定效应模型都提供了灵活性，可以根据研究问题和数据特性进行选择。
2. **泛化能力**：随机效应模型允许我们泛化到更大的群体，而固定效应模型则专注于特定样本。
3. **控制未观察到的异质性**：两种模型都可以控制未观察到的异质性，提高估计的准确性。
#### 局限性
1. **随机效应模型的假设**：随机效应模型的假设可能在某些情况下不成立，比如当组效应不满足交换性时。
2. **固定效应模型的泛化能力**：固定效应模型不提供关于群体间差异的信息，限制了其泛化能力。
3. **计算复杂性**：随机效应模型的参数估计可能涉及复杂的计算，特别是当模型包含多个随机效应时。
4. **数据要求**：两种模型都对数据有一定的要求，比如需要足够的组和观测值来估计模型参数。

### 第九部分：实际应用中的考虑

1. **数据的可用性**：在实际应用中，数据的可用性可能限制了模型的选择。例如，如果数据中只有少数几个组，随机效应模型可能不是最佳选择。
2. **研究目的**：研究的目的应该指导模型的选择。如果目的是比较不同组之间的差异，固定效应模型可能更合适；如果目的是估计群体间变异性，随机效应模型可能更合适。
3. **模型的解释性**：在选择模型时，应该考虑模型结果的解释性。随机效应模型和固定效应模型提供了不同的视角来解释数据。
4. **软件和计算资源**：不同的统计软件和计算资源可能影响模型的选择和参数估计的可行性。
### 结论
随机效应模型和固定效应模型是分析层次数据的强大工具。它们各自有优势和局限性，选择哪种模型取决于研究问题、数据特性和理论依据。通过逐步分析和理解这些模型，我们可以更准确地解释数据并做出合理的推断。


### 第十部分：具体案例分析
让我们通过一个具体案例来进一步理解随机效应和固定效应模型的应用。
**案例背景**：假设我们研究的是不同医院的患者康复时间。我们有来自10个不同地区的医院数据。
**数据结构**：
- Level-1（个体层面）：每个患者的康复时间。
- Level-2（组/医院层面）：不同医院的特定特征，如医院规模、医疗资源等。
**研究问题**：
- 不同医院的患者康复时间是否存在显著差异？
- 医院特征是否影响患者的康复时间？
#### 随机效应模型的应用
1. **模型设定**：$y_{ij} = \beta_0 + \beta_1 x_{ij} + \zeta_j + \epsilon_{ij}$，其中 $y_{ij}$ 是第 $i$ 个患者（$i = 1, \ldots, n_j$）在第 $j$ 个医院的康复时间，$x_{ij}$ 是患者的某个特征，$\zeta_j$ 是医院 $j$ 的随机效应，$\epsilon_{ij}$ 是误差项。
2. **参数解释**：
   - $\beta_0$：所有医院患者的平均康复时间。
   - $\beta_1$：患者特征对康复时间的影响。
   - $\zeta_j$：第 $j$ 个医院的随机效应，反映了医院未观测到的异质性。
3. **模型估计**：使用最大似然估计或限制最大似然估计（REML）来估计模型参数。
4. **模型检验**：检验医院随机效应的显著性，以确定是否需要随机效应模型。
#### 固定效应模型的应用
1. **模型设定**：$y_{ij} = \beta_0 + \beta_1 x_{ij} + \alpha_j + \epsilon_{ij}$，其中 $\alpha_j$ 是第 $j$ 个医院的固定效应。
2. **参数解释**：
   - $\beta_0$ 和 $\beta_1$：同随机效应模型。
   - $\alpha_j$：第 $j$ 个医院相对于基准医院（通常为医院1）的固定效应。
3. **模型估计**：固定效应模型的参数估计通常较为简单，可以直接使用最小二乘法。
4. **模型检验**：检验固定效应是否显著，以确定不同医院是否对康复时间有显著影响。
#### 模型选择
- 如果我们对泛化到所有可能的医院感兴趣，随机效应模型可能更合适。
- 如果我们只对样本中的医院感兴趣，固定效应模型可能更合适。
- 如果医院随机效应显著，表明不同医院之间存在未观测到的异质性，支持随机效应模型的使用。
- 如果医院数量较少或医院效应不显著，固定效应模型可能是更好的选择。
### 第十一部分：模型的进一步讨论
1. **模型的稳健性**：在实际应用中，可能需要检查模型对异常值或数据分布的敏感性。
2. **模型的敏感性分析**：通过改变模型假设或参数，评估模型结果的稳定性。
3. **模型的扩展**：根据研究需要，可以扩展模型以包括更多的随机效应、固定效应或交互作用。
4. **模型的实用性**：考虑模型在实际研究中的可行性，包括数据的可用性、模型的复杂性和解释性。



## 9 Crossed versus nested effects

So far, we have considered the random or fixed effects of a single cluster identifier or factor, subjects. Another potential factor in the peak-expiratory-flow dataset is the measurement occasion with 2 levels, occasions 1 and 2. In the variance-components model, occasion was allowed to have an effect on the response variable only via the residual term $epsilon_{ij}$, which takes on a different value for each combination of subject and occasion and has mean 0 for each occasion across subjects. We have therefore implicitly treated occasions as nested (and exchangeable) within subjects, meaning that occasion (2 versus 1) does not have a common systematic effect for all subjects.

If all subjects had been measured and remeasured in the same sessions and if there were anything specific to the session (for example, time of day, temperature, or calibration of the measurement instrument) that could influence measurements on all subjects in a similar way, then subjects and occasions would be crossed. We would then include an occasion-specific term (“main effect of occasion”) in the model that takes on the same value for all subjects.

The distinction between nested and crossed factors is illustrated in figure 2.8.

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.6.png" style="width:440px;height:250px;" alt="图片描述">
  <figcaption><strong>Figure 2.7: Illustration of hierarchical sampling in variancecomponents model</strong></figcaption>
</figure>

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.7.png" style="width:440px;height:250px;" alt="图片描述">
  <figcaption><strong>Figure 2.7: Illustration of hierarchical sampling in variancecomponents model</strong></figcaption>
</figure>

In the nested case, the effect of occasion 1 (or 2) is different for every subject; in the crossed case, there is a main effect of occasion that is the same for each subject (and possibly an occasion by subject interaction).

In the crossed case, the model can be described as a two-way ANOVA model. A subject-by-occasion interaction could in this case be included in addition to the main effects of each factor. However, because there are no replications for each subject-occasion combination in the peak-expiratory-flow application, an interaction term would be confounded with the error term $epsilon_{ij}$.

If a random effect is specified for subjects and a fixed effect for occasion, we obtain a so-called **mixed-effects two-way ANOVA model**. Such a model can be fit by introducing a dummy variable for the second occasion in the fixed part of the model:


### 1. 随机效应与固定效应

在统计学中，我们经常需要考虑数据中的不同来源的变异。随机效应和固定效应是两种不同的处理方式：

- **随机效应**：假设效应是从一个更大的群体中随机抽样的，我们感兴趣的是这个群体的变异性。
- **固定效应**：假设效应是研究中特定的，我们感兴趣的是这些特定效应对结果的影响。

### 2. 单个簇标识符或因子

这里的“簇”可以理解为数据的一个分组单位。比如在医学研究中，不同的患者可以看作不同的簇。

### 3. 测量场合（Occasion）
在提到的峰值呼气流量数据集中，测量场合是一个潜在的影响因素，有两个水平：场合1和场合2。这里的“场合”可以理解为数据收集的具体时间点或环境。

### 4. 方差组分模型

方差组分模型是一种统计模型，它允许我们估计数据中不同来源的方差。在这个模型中，场合被允许通过残差项 $ \epsilon_{ij} $ 对响应变量产生影响，这个残差项对于每个主体和场合的组合都有不同的值，并且在所有主体中对于每个场合的平均值为0。

### 5. 嵌套与交叉

- **嵌套（Nested）**：场合是嵌套在主体内部的，意味着每个主体有自己的场合效应，场合1和场合2对每个主体有不同的影响。
- **交叉（Crossed）**：场合效应对所有主体都是相同的，即场合效应是交叉的。


### 7. 两因素方差分析（Two-way ANOVA）

在交叉的情况下，模型可以被描述为两因素方差分析模型：
- **主效应**：每个因素（如场合）对结果的独立影响。
- **交互作用**：两个因素共同作用对结果的影响。

### 8. 混合效应模型的构建
混合效应模型（Mixed-Effects Model）是一种统计模型，用于处理具有层次结构的数据。在这种模型中，我们通常有固定效应和随机效应：
- **固定效应**：通常指的是我们感兴趣的主要因素或处理效应，我们假设这些效应在整个研究中是一致的。
- **随机效应**：用来表示数据中的随机变异，例如个体之间的差异或时间点之间的差异。

### 9. 两因素方差分析（Two-Way ANOVA）
在传统的两因素方差分析中，我们研究两个独立变量（因素）对一个依赖变量的影响。每个因素都有多个水平，我们想要了解这些因素的单个效应以及它们之间可能的交互效应。

### 10. 混合效应两因素方差分析模型
当我们的数据结构不适合传统的两因素方差分析时，比如数据具有嵌套结构或者我们想要考虑随机效应，我们可以使用混合效应两因素方差分析模型。这种模型允许我们：
- 考虑固定效应，比如不同场合对峰值呼气流速的影响。
- 考虑随机效应，比如不同个体之间的变异性。
- 通过引入虚拟变量来处理固定效应，比如场合的效应。

### 11. 虚拟变量的引入
在混合效应模型中，我们通过引入虚拟变量来表示固定效应。例如，如果我们有两个场合，我们可以创建一个虚拟变量，当数据点是第一个场合时，虚拟变量取值为0；当数据点是第二个场合时，虚拟变量取值为1。

### 12. 模型公式
假设我们的数据集中有变量 $ Y $（响应变量，比如峰值呼气流速），$ Subject $（个体），和 $ Occasion $（场合）。我们的混合效应模型可以表示为：

$$ Y_{ijk} = \mu + \alpha_i + \beta_j + \epsilon_{ijk} $$

其中：
- $ Y_{ijk} $ 是第 $ i $ 个个体在第 $ j $ 个场合的第 $ k $ 次测量的响应变量。
- $ \mu $ 是总体均值。
- $ \alpha_i $ 是第 $ i $ 个个体的随机效应。
- $ \beta_j $ 是第 $ j $ 个场合的固定效应。
- $ \epsilon_{ijk} $ 是误差项，对于每个个体和场合的组合都有不同的值，且均值为0。

### 13. 模型的拟合
混合效应模型通常使用最大似然估计或贝叶斯方法来拟合。这些方法可以帮助我们估计模型中的参数，比如固定效应的大小和随机效应的方差。

### 14. 结果解释
一旦模型拟合完成，我们可以解释固定效应和随机效应对响应变量的影响。例如，我们可以比较不同场合的峰值呼气流速是否有显著差异，或者个体之间的变异性是否显著。

### 15. 模型假设
在使用混合效应模型时，我们还需要考虑一些基本假设，比如误差项的正态性、方差齐性等。这些假设需要通过数据的探索性分析来检查。


```
generate occ2 = occasion==2
mixed wm occ2 || id:, reml stddeviations
```
    
    Performing EM optimization ...
    
    Performing gradient-based optimization: 
    Iteration 0:  Log restricted-likelihood = -177.43977  
    Iteration 1:  Log restricted-likelihood = -177.43977  
    
    Computing standard errors ...
    
    Mixed-effects REML regression                        Number of obs    =     34
    Group variable: id                                   Number of groups =     17
                                                         Obs per group:
                                                                      min =      2
                                                                      avg =    2.0
                                                                      max =      2
                                                         Wald chi2(1)     =   0.17
    Log restricted-likelihood = -177.43977               Prob > chi2      = 0.6806
    
    ------------------------------------------------------------------------------
              wm | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
    -------------+----------------------------------------------------------------
            occ2 |       2.88       7.00     0.41   0.681       -10.84       16.61
           _cons |     452.47      27.22    16.62   0.000       399.12      505.82
    ------------------------------------------------------------------------------
    
    ------------------------------------------------------------------------------
      Random-effects parameters  |   Estimate   Std. err.     [95% conf. interval]
    -----------------------------+------------------------------------------------
    id: Identity                 |
                       sd(_cons) |     110.35      19.84         77.57      156.98
    -----------------------------+------------------------------------------------
                    sd(Residual) |      20.42       3.61         14.44       28.87
    ------------------------------------------------------------------------------
    LR test vs. linear model: chibar2(01) = 43.71         Prob >= chibar2 = 0.0000
    

Instead of creating the dummy variable occ2, we could have used the factor-variable notation **`i.occasion`** within the **`mixed`** or **`xtreg`** commands.

We see that there is no evidence for an effect of occasion, which in this example could only be interpreted as a practice effect because occasion 1 was not at the same time for the subjects. If there had been considerably more than two occasions, we could have specified a random effect for occasion that is crossed with the random effect for subject. Such models with crossed random effects are discussed in chapter 9.

## 10 Parameter estimation

### 10.1 Model assumptions

We now explicitly state a set of assumptions that are sufficient for everything we want to do in this chapter but are not always necessary. We briefly state which assumptions are needed for properties such as consistency and efficiency of the standard estimators for variance components models discussed in section 2.10.2.

* **Mean structure and covariance structure**

The total residual $\xi_{ij}=\zeta_j+\epsilon_{ij}$ is assumed to have zero expectation:

$$E(\zeta_j+\epsilon_{ij}) = 0$$

This assumption implies that the expectation of the response (conditional expectation if there were covariates), called the mean structure, is

$$E(y_{ij})=\beta $$

If the mean structure is correctly specified, standard point estimators $\widehat{\beta}$ (such as ML, REML, and FGLS) of the parameter $\beta$ will be consistent, meaning that $\widehat{\beta}$ approaches $\beta$ as the number of clusters $J$ tends to infinity. A consistent estimator need not be unbiased in small samples, meaning that the average of $\widehat{\beta}$, over repeated samples, may not equal $\beta$. For $\widehat{\beta}$ to be unbiased, the distribution of $\zeta_j + \epsilon_{ij}$ must in general be symmetric (for instance, a normal distribution).


For the covariance structure, it is assumed that the random intercept $\zeta_j$ (with constant variance $\psi$) and the level-1 residual $\epsilon_{ij}$ (with constant variance $\theta$) are uncorrelated, Cor($\epsilon_{ij}$, $\zeta_j$) = 0. From this it follows that the variance of the total residual is

$$\mathrm{Var}(\zeta_j+\epsilon_{ij}) = \psi+\theta $$

The $\epsilon_{ij}$ are assumed to be uncorrelated across units $i$, from which it follows that the covariance between total residuals for two units $i$ and $i'$ in the same cluster is

$$\mathrm{Cov}(\zeta_j+\epsilon_{ij},\zeta_j+\epsilon_{i^{\prime}j}) = \psi $$


Both $\zeta_j$ and $\epsilon_{ij}$ are also uncorrelated across different clusters so that there are no correlations between the total residuals of units in different clusters. These assumptions imply that the covariance structure of the responses is

$$\mathrm{Var}(y_{ij})\quad=\quad\psi+\theta $$
$$\mathrm{Cov}(y_{ij},y_{i^{\prime}j})\quad=\quad\psi\quad\mathrm{if} i\neq i^{\prime}$$
$$\mathrm{Cov}(y_{ij},y_{i^{\prime}j^{\prime}})\quad=\quad0\quad\mathrm{if~}j\neq j^{\prime}$$


(The corresponding matrix is shown for the case where in $n_j = 3$ display 3.2.)

If both the mean and covariance structure are correct, then the estimators of all parameters in the variance-components model are consistent and asymptotically efficient, and the model-based standard errors are consistent.

An efficient estimator is one that has a smaller standard error than any other estimator. Asymptotically efficient estimators acquire that property only asymptotically, as the sample size goes to infinity. For many estimators, the asymptotic sampling distribution is normal, making it easy to construct large-sample confidence intervals and tests. In variance-components models and other two-level models, “large sample” and “asymptotics” refer to the number of clusters going to infinity, possibly with fixed cluster size.

Consistent estimates for $\beta $ can be obtained even if the covariance structure is not correct. In this case, model-based standard errors will be inconsistent, but robust standard errors can be used instead if there are enough clusters (for example, $J\ge42$); see display 2.1.


1. **总残差和期望值**

首先，我们有一个总残差 $ \xi_{ij} $，它由随机截距 $ \zeta_j $ 和观测误差 $ \epsilon_{ij} $ 组成：
$$
 \xi_{ij} = \zeta_j + \epsilon_{ij} 
$$
这个总残差的期望值被假定为0：
$$
 E(\zeta_j + \epsilon_{ij}) = 0 
$$
这意味着，如果我们有一个响应变量 $ y_{ij} $，它的期望值（如果有协变量的话就是条件期望）被称为均值结构，并且是：
$$
 E(y_{ij}) = \beta 
$$

2. **一致性和无偏性**

如果均值结构被正确指定，那么标准的点估计量 $ \widehat{\beta} $（例如最大似然估计MLE、限制最大似然估计REML、广义最小二乘法FGLS）将会是一致的。这里的“一致”意味着随着簇的数量 $ J $ 趋向于无穷大，估计量 $ \widehat{\beta} $ 会趋向于真实的参数 $ \beta $。
然而，一致的估计量并不一定在小样本中是无偏的。无偏性意味着估计量的平均值，即在重复抽样中，应该等于真实的参数 $ \beta $。为了使 $ \widehat{\beta} $ 无偏，$ \zeta_j + \epsilon_{ij} $ 的分布通常需要是对称的，比如正态分布。

3. **协方差结构**

接下来，我们考虑协方差结构。假设随机截距 $ \zeta_j $ 有恒定的方差 $ \psi $，而一级残差 $ \epsilon_{ij} $ 有恒定的方差 $ \theta $，并且它们是不相关的：
$$
 \text{Cor}(\epsilon_{ij}, \zeta_j) = 0 
$$
由此，我们可以得出总残差的方差是：
$$
 \text{Var}(\zeta_j + \epsilon_{ij}) = \psi + \theta 
$$

4. **残差的不相关性**

我们还假设 $ \epsilon_{ij} $ 在不同的单位 $ i $ 之间是不相关的，这意味着同一簇内两个单位 $ i $ 和 $ i' $ 的总残差的协方差是：
$$
 \text{Cov}(\zeta_j + \epsilon_{ij}, \zeta_j + \epsilon_{i'j}) = \psi 
$$
此外，不同簇的 $ \zeta_j $ 和 $ \epsilon_{ij} $ 也是不相关的，这意味着不同簇的单位之间的总残差没有相关性。

5. 响应的协方差结构

这些假设意味着响应的方差和协方差结构如下：
$$
 \text{Var}(y_{ij}) = \psi + \theta 
$$
$$
 \text{Cov}(y_{ij}, y_{i'j}) = \psi \quad \text{if } i \neq i' 
$$
$$
 \text{Cov}(y_{ij}, y_{i'j'}) = 0 \quad \text{if } j \neq j' 
$$

6. 估计量的性质

如果均值和协方差结构都被正确指定，那么方差分量模型中所有参数的估计量是一致的，并且是渐近有效的。有效的估计量是指其标准误差比其他估计量更小。
渐近有效性意味着随着样本大小趋向于无穷大，估计量会获得这一属性。对于许多估计量，其渐近抽样分布是正态的，这使得构建大样本置信区间和检验变得容易。在方差分量模型和其他两级模型中，“大样本”和“渐近”是指簇的数量趋向于无穷大，可能伴随着固定的簇大小。
即使协方差结构不正确，也可以获得 $ \beta $ 的一致估计。在这种情况下，基于模型的标准误差将是不一致的，但如果簇的数量足够多（例如，$ J \geq 42 $），可以使用稳健的标准误差。

7. 一致性和渐近有效性的例子

想象一下，我们有一个班级的学生，每个学生都参加了一个数学测试。我们想要估计这个班级的平均数学水平。这里，每个学生的测试成绩 $ y_{ij} $ 可以看作是我们的响应变量，其中 $ i $ 表示学生，$ j $ 表示测试。
- **均值结构**：我们假设每个学生的成绩平均来说，反映了班级的平均数学水平 $ \beta $。即使有些学生可能因为紧张而考得不好，或者有些学生可能超常发挥，我们期望这些偏差在大量学生中会相互抵消。
- **一致性**：如果我们多次进行这样的测试，并且每次测试的学生数量 $ J $ 都足够多，我们估计的班级平均数学水平 $ \widehat{\beta} $ 会越来越接近真实的平均数学水平 $ \beta $。
- **协方差结构**：我们假设每个学生的成绩 $ y_{ij} $ 受到两个因素的影响：班级平均水平 $ \beta $ 和个别学生的特殊因素 $ \zeta_j + \epsilon_{ij} $。这里，$ \zeta_j $ 可以看作是班级内部的随机因素，比如座位位置或教室环境的影响；$ \epsilon_{ij} $ 是个别学生的特殊误差，比如考试当天的状态。
- **无偏性**：如果我们的测试设计得很好，那么 $ \zeta_j + \epsilon_{ij} $ 的分布应该是对称的，比如正态分布。这样，即使个别学生的成绩有偏差，但平均来看，这些偏差不会影响我们对 $ \beta $ 的估计。
- **稳健标准误**：如果我们不能确定 $ \zeta_j + \epsilon_{ij} $ 的分布是否对称，我们可以使用稳健的标准误来估计 $ \beta $。这就像是，即使我们不能完全理解每个学生的特殊因素，我们仍然可以通过观察足够多的学生来得到一个可靠的班级平均数学水平的估计。

8. 大样本和渐近性质
- **大样本**：在统计学中，当我们说“大样本”，我们通常是指样本量足够大，以至于我们可以忽略一些小的偏差和随机性。在这段文本中，大样本指的是有很多个班级（或簇）参加测试的情况。
- **渐近性质**：当我们说一个估计量是“渐近有效的”，我们是指随着样本量的增加，这个估计量的性能会越来越好。例如，它会越来越接近真实值，其标准误差会越来越小。

* **Distributional assumptions**

The maximum likelihood estimator (and restricted maximum likelihood estimator) is based on specifying normal distributions for both $\zeta_j$ and $\epsilon_{ij}|\zeta_j.$ (Under normality, the assumptions above that the mean and variance of $\epsilon_{ij}$ do not depend on $\zeta_j$ actually imply that $\epsilon_{ij}$ and $\zeta_j$ are independent.) These normality assumptions are not required for consistent estimation of model parameters and standard errors, or for asymptotic normality of the estimators. The assumptions do, however, matter in empirical Bayes prediction of the random effects (see section 2.11.2)

### 10.2 Different estimation methods


A classical method for estimating the parameters of statistical models is maximum likelihood (ML) as will be explained below. When the number of clusters is not sufficiently large, the ML estimator of $ \psi $ is downward biased. For this reason, restricted maximum likelihood (REML) estimation has been developed, which would be unbiased for balanced data if negative values of $ \widehat{\psi} $ were permitted. For unbalanced data and when negative variances are not permitted (as in mixed, reml), the bias is generally smaller than for ML. While the bias for $ \psi $ does not lead to bias in the point estimate $ \widehat{\beta} $, it does affect the estimated standard error of $ \widehat{\beta} $ as will be seen in section 2.10.3. When the number of clusters is small, it is therefore recommended to use REML. See display 2.1 for the rule of thumb to choose REML if $ J < 42 $.

Another estimation method, particularly popular in econometrics, is feasible generalized least squares (FGLS). If the variance parameters were known, the ML estimates of $ \beta $ would be obtained by generalized least squares (GLS). However, this method is not feasible because the variance parameters are not known, so _feasible_ GLS plugs in consistent estimates for the variances. All three methods, ML, REML, and FGLS, are consistent for all parameters if the mean and covariance structure are correctly specified, and hence produce very similar estimates when the number of clusters is large

We now give a brief description of ML estimation and relate it to REML and FGLS, as well as ANOVA. The likelihood function is just the joint probability density of all the observed responses $y_{ij}$ , $(i = 1,...,n_j),(j=1,...,J)$, as a function of the model parameters $\beta$, $\psi$, and $\theta$. The likelihood contribution for cluster $j$ can be obtained by integrating the joint distribution of the $y_{ij}$ and $\zeta_j$ over the random intercept. The product of the likelihood contributions for all clusters is the likelihood, often called the _marginal likelihood_ (averaged over $\zeta_j$). The idea is to find parameter estimates $\widehat{\beta}$, $\widehat{\psi}$, and $\widehat{\theta}$ that maximize the likelihood function, thus making the responses appear as likely as possible.


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.8.png" style="width:540px;height:100px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

and the SSE is the sum of squared deviations of the responses from their cluster means,

$$\mathrm{SSE~}=\sum_{j=1}^J\sum_{i=1}^n(y_{ij}-\overline{y}_{\cdot j})^2$$

The population mean $\beta$ is estimated by the sample mean,

$$\widehat{\beta} = \overline{y}_{..}$$

and the ML estimator of the within-cluster variance $\theta$ is

$$\widehat{\theta}~=~\frac{1}{J(n-1)}\mathrm{SSE~=~MSE}$$

The ML estimator of the between-cluster variance $\psi$ is given by

![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.10.png)


where the subtraction of the second term is required because the level-1 residuals contribute to the MSS. With a small number of clusters, boundary estimates of $\widehat{\psi} = 0 $ can occur frequently. The ML estimators for $\beta$ and $\theta$ are unbiased if the model is true, whereas the estimator for $\psi$ has downward bias.
The REML estimator of $\psi$ is given by

$$\widehat{\psi} = \frac{\mathrm{MSS}}{(J-1)n}-\frac{\widehat{\theta}}{n} = \frac{1}{n} \mathrm{(MMS-MSE)}$$

where MMS is the model mean square from the one-way ANOVA. This estimator is unbiased and corresponds to the moment or ANOVA estimator and the FGLS variance estimators implemented in Stata when cluster-sizes are constant as here. The estimators generally differ for unbalanced data. When negative estimates are set to 0, as in Stata and as shown for the ML estimator above, REML has some positive bias, but the bias is smaller than the negative bias of the ML estimator. The difference between REML and ML is that REML divides the MSS by $n$ times the ANOVA model degrees of freedom $J-1$, whereas ML divides by $n$ times $J$. The difference therefore becomes small when the number of clusters $J$ is large. In the example considered in this chapter, there are only $J=17$ clusters, so the difference between ML and REML estimates will not be negligible (see table 2.2). 

Unlike ML, REML estimates the random-intercept variance taking into account the loss of 1 degree of freedom resulting from the estimation of the overall mean $\beta$. In models considered in the next chapters that include covariates, further degrees of freedom are lost because of estimation of additional regression coefficients. Although REML is not unbiased for $\psi$ when $\widehat{\psi}$ is forced to be nonnegative, it is less biased than ML if the number of clusters is small. The ML estimators of $\beta$ and $\theta$ given above for balanced data are identical to the REML estimators.

Section 3.10.1 provides more details on ML, REML, and FGLS estimation of models with covariates.

### 10.3  Inference for β

* **Estimate and standard error: Balanced case**

We first consider the balanced case where $n_j=n$. As mentioned in the previous section, the ML and REML estimator $\widehat{\beta}$ of $\beta$ in the variance-components model is simply the overall sample mean 

$$\widehat{\beta}~=~\frac{1}{Jn}\sum_{j=1}^{J}\sum_{i=1}^{n}y_{ij}=\frac{1}{J}\sum_{j=1}^{J}\overline{y}_{\cdot j}$$

an unweighted mean of the cluster means. The estimated standard error is given by

$$\widehat{\mathrm{SE}}(\widehat{\beta})~=~\sqrt{\frac{n\widehat{\psi}+\widehat{\theta}}{Jn}}=\sqrt{\frac{\widehat{\psi}+\widehat{\theta}/n}J}$$

Remember that $\beta$ represents the mean $E(\beta+\zeta_j)$ of the cluster means for the population of clusters. When the cluster size $n$ is infinite, the cluster means are known with complete precision and uncertainty about $\beta$ comes only from having a finite sample of $J$ clusters (rather than the infinite population of clusters). The estimated standard error then takes the familiar form $\sqrt{\widehat\psi/J}$--because $\widehat\beta$ is the sample mean of $J$ (precisely known) cluster means, its estimated standard error is the standard deviation of the cluster means divided by the square root of the number of clusters. We see that $\widehat{\rm SE}(\widehat\beta)$ depends on $\widehat\psi$ and that the estimated standard error will therefore differ between ML and REML. When the number of clusters is small, the REML standard error is preferable.

In the fixed-effects model (with the random $\zeta_j$ replaced with fixed $\alpha_j$; see section 2.8), the estimator of $\beta$ is the same, but now the estimated standard error is

$$\widehat{\mathrm{SE}}(\widehat{\beta}^F) = \sqrt{\frac{\widehat{\theta}}{Jn}}$$

which is smaller than the standard error $\widehat{SE}(\widehat{\beta})$ in the random-effects model if $\widehat{\psi}>0$. Because $\beta$ now represents the sample mean $1/J\sum_{j=1}^{J}(\beta+\alpha_j)$ of the cluster means for the $J$ clusters in the data, the standard error becomes 0 when the cluster size $n$ is infinite.

Now consider the model without cluster-specific random or fixed effects (no $\zeta_j$ or $\alpha_j$) that assumes residuals to be independent. Such a single-level model would be used when the nesting of units in clusters is ignored. We refer to the corresponding estimator of $\beta$ as the ordinary least-squares (OLS) estimator $\widehat{\beta}_{\mathrm{OLS}}$. The estimator is the same as for the random- and fixed-effects models except that the estimated standard error is now approximately

$$\widehat{\mathrm{SE}}(\widehat{\beta}^\mathrm{OLS}) \approx \sqrt{\frac{\widehat{\psi}+\widehat{\theta}}{Jn}}$$

where we have approximated the OLS estimate of the residual variance $\widehat{\sigma^2}$ by the sum of the estimated variance components $\widehat{\psi} + \widehat{\theta}$ (the approximation is better for larger $n$).

We see that

$$\widehat{\mathrm{SE}}(\widehat{\beta}^{F})\leq\widehat{\mathrm{SE}}(\widehat{\beta}^{{\mathrm{OLS}}})\leq\widehat{\mathrm{SE}}(\widehat{\beta})$$

This relationship is best understood by remembering that the standard error is the standard deviation of the estimates over repeated samples (repeated random draws of $y_{ij}$ for all units). In the fixed-effects case, only the $\epsilon_{ij}$ change from sample to sample (with variance $\theta$). In the OLS case, the total residuals change (with variance $\psi + \theta$), but they are drawn independently (because they are assumed to be independent). In contrast, the total residuals are not drawn independently in the random-effects case; they result from drawing one value $\zeta_j$ for all units in the cluster and drawing an $\epsilon_{ij}$ for each unit. As a consequence, the total residuals $\zeta_j + \epsilon_{ij}$ for a cluster tend to change in the same direction, leading to larger variability in the resulting $\widehat{\beta}$. The difference between the OLS and the random-effects standard error is particularly pronounced if the $\zeta_j$ vary considerably (large $\psi$), and if a change in a $\zeta_j$ affects a large number of units (large $n$).


* **平衡情况（Balanced Case）**
在统计分析中，"平衡"意味着每个组或群体内的观测数是相同的。这里，我们假设有 $ J $ 个组，每个组有 $ n $ 个观测值。

* **估计值（Estimate）**
- **最大似然估计（MLE）** 和 **限制最大似然估计（REML）** 是用来估计模型参数的两种方法。在平衡情况下，$ \beta $ 的估计值 $ \widehat{\beta} $ 是所有数据的总体样本均值。

* 公式分解
$$
 \widehat{\beta} = \frac{1}{Jn} \sum_{j=1}^{J} \sum_{i=1}^{n} y_{ij} 
$$
- 这里，$ y_{ij} $ 是第 $ j $ 组的第 $ i $ 个观测值。
- $ \sum $ 表示求和符号，$ \sum_{i=1}^{n} y_{ij} $ 表示对第 $ j $ 组内所有观测值求和。
- $ \frac{1}{Jn} $ 是将总和除以总观测数，得到平均值。

* **标准误差（Standard Error）**
- 标准误差是估计值的变异性或不确定性的度量。

* 公式分解
$$
 \widehat{\mathrm{SE}}(\widehat{\beta}) = \sqrt{\frac{n\widehat{\psi} + \widehat{\theta}}{Jn}} 
$$
- $ \widehat{\psi} $ 和 $ \widehat{\theta} $ 是模型中估计的方差组分，分别代表组间和组内的变异性。
- $ n $ 是每个组内的观测数。
- $ J $ 是组的总数。
- 这个公式告诉我们，随着组数 $ J $ 的增加，标准误差会减小，因为有更多的数据来估计 $ \beta $。

* **固定效应模型（Fixed-Effects Model）**
- 在固定效应模型中，我们假设每个组的效应 $ \zeta_j $ 是固定的，不是随机变化的。

* 公式分解
$$
 \widehat{\mathrm{SE}}(\widehat{\beta}^F) = \sqrt{\frac{\widehat{\theta}}{Jn}} 
$$
- 这里的标准误差只考虑了组内的变异性 $ \widehat{\theta} $。

* **普通最小二乘估计（OLS）**
- OLS 是不考虑组效应的简单线性回归模型。
* 公式分解
$$
 \widehat{\mathrm{SE}}(\widehat{\beta}^\mathrm{OLS}) \approx \sqrt{\frac{\widehat{\psi} + \widehat{\theta}}{Jn}} 
$$
- 这个标准误差的估计考虑了组间和组内的总变异性。

* 标准误差的关系
- 固定效应模型的标准误差通常小于 OLS，而 OLS 的标准误差又小于随机效应模型的，因为随机效应模型考虑了更多的不确定性来源。

* 例子
- 假设我们有 $ J = 5 $ 个组，每组有 $ n = 10 $ 个观测值。
- 我们收集了所有观测值并计算了每个组的均值 $ \overline{y}_{\cdot j} $。
- 然后，我们计算所有组均值的总和，除以 $ Jn $ 得到 $ \widehat{\beta} $。
- 我们估计了组间和组内的方差 $ \widehat{\psi} $ 和 $ \widehat{\theta} $。
- 使用这些估计值，我们可以计算 $ \widehat{\beta} $ 的标准误差。
希望这个更详细的解释能帮助你更好地理解这些统计概念。如果你还有任何疑问，或者需要进一步的解释，请随时告诉我。


在统计中，估计值是我们根据样本数据计算出来的，用来近似总体参数的值。在这个上下文中，$ \beta $ 代表的是所有组的总体平均效应。我们用所有组内数据的总和除以总的观测数（$ Jn $）来得到这个估计值。
想象一下，你有5个班级的学生考试成绩，每个班级有10个学生。要估计所有学生的平均成绩，你会把所有学生的成绩加起来，然后除以50（5个班级乘以每班10个学生）。这就是$ \widehat{\beta} $的计算方式。

* 标准误差（Standard Error）的进一步解释
标准误差衡量的是估计值的不确定性。它告诉我们，如果我们多次从总体中抽取样本并计算估计值，这些估计值会围绕真实值 $ \beta $ 波动的范围。

* 随机效应模型中的标准误差
在随机效应模型中，我们认为每个组的效应 $ \zeta_j $ 是随机的，这意味着每个组都有自己的特定效应，这些效应在总体中是随机分布的。因此，我们不仅要考虑组内变异性 $ \widehat{\theta} $，还要考虑组间变异性 $ \widehat{\psi} $。
公式 $ \widehat{\mathrm{SE}}(\widehat{\beta}) $ 中的 $ n $ 表示每个组的大小，它告诉我们组的大小如何影响标准误差。组的大小越大，每个组的均值估计越精确，因此标准误差越小。

* 固定效应模型中的标准误差
在固定效应模型中，我们认为每个组的效应 $ \zeta_j $ 是固定的，但未知。这里，我们只关心组内的变异性 $ \widehat{\theta} $，因为我们假设每个组的效应是特定的，不是随机变化的。

* 普通最小二乘估计（OLS）中的标准误差
OLS不考虑组效应，它假设所有的变异性都来自于残差。因此，OLS的标准误差是基于总的变异性 $ \widehat{\psi} + \widehat{\theta} $ 来计算的。

* 标准误差关系的实际意义
- **固定效应模型**：因为我们只考虑组内的变异性，所以标准误差通常较小。
- **随机效应模型**：由于考虑了组间和组内的变异性，标准误差通常较大。
- **OLS**：不考虑组效应，只考虑总的变异性，因此其标准误差介于固定效应和随机效应之间。

* 例子：峰值呼气流速（Peak-Expiratory-Flow）
假设我们有一个研究，测量了不同患者的峰值呼气流速。我们有以下数据：
- 5个不同的患者组，每组有10个患者。
- 我们测量了每个患者的峰值呼气流速，并计算了每组的平均值。
如果我们使用随机效应模型，我们可能会发现 $ \widehat{\operatorname{SE}}(\widehat{\beta}) $ 是 26.19。这意味着如果我们多次进行这样的研究，估计值 $ \widehat{\beta} $ 的标准偏差大约是26.19。
如果我们使用固定效应模型，我们可能会发现 $ \widehat{\operatorname{SE}}(\widehat{\beta}^{\mathrm{F}}) $ 是 3.41，这表明固定效应模型的不确定性较小。
如果我们使用OLS，我们可能会得到一个介于两者之间的标准误差。


* **例子：学生考试成绩的分析**
假设我们有一个学校，它有5个不同的班级，每个班级有10个学生。我们想要研究不同班级之间的平均成绩差异。我们收集了每个学生的成绩，现在我们想要估计所有班级的平均成绩，并且评估这个估计的不确定性。

1. 步骤1：收集数据
首先，我们收集每个学生的成绩，记为 $ y_{ij} $，其中 $ i $ 是学生在班级内的编号，$ j $ 是班级的编号。

2. 步骤2：计算每班的平均成绩
对于每个班级 $ j $，我们计算班级的平均成绩 $ \overline{y}_{\cdot j} $：

![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.11.png)

例如，如果第一个班级的学生成绩是 85, 90, 78, 88, 92, 84, 91, 82, 87, 93，那么第一个班级的平均成绩是：

$$ \overline{y}_{\cdot 1} = \frac{85 + 90 + 78 + 88 + 92 + 84 + 91 + 82 + 87 + 93}{10} = 86.1 $$

1. 步骤3：计算总体平均成绩的估计值 $ \widehat{\beta} $
然后，我们计算所有班级平均成绩的总和，然后除以班级数量 $ J $：

$$ \widehat{\beta} = \frac{1}{J} \sum_{j=1}^{J} \overline{y}_{\cdot j} $$

假设其他班级的平均成绩分别是 87.2, 85.5, 84.8, 89.1，那么：
$$ \widehat{\beta} = \frac{86.1 + 87.2 + 85.5 + 84.8 + 89.1}{5} = 86.52 $$

1. 步骤4：估计方差组分
接下来，我们需要估计组间方差 $ \widehat{\psi} $ 和组内方差 $ \widehat{\theta} $。这通常通过模型拟合来完成，但为了简化，我们假设已经得到了这些估计值。

1. 步骤5：计算标准误差
现在我们有了 $ \widehat{\psi} $ 和 $ \widehat{\theta} $，我们可以计算 $ \widehat{\beta} $ 的标准误差：
$$ \widehat{\mathrm{SE}}(\widehat{\beta}) = \sqrt{\frac{n\widehat{\psi} + \widehat{\theta}}{Jn}} $$
假设 $ n = 10 $，$ J = 5 $，$ \widehat{\psi} = 20 $，$ \widehat{\theta} = 5 $，那么：
$$ \widehat{\mathrm{SE}}(\widehat{\beta}) = \sqrt{\frac{10 \times 20 + 5}{5 \times 10}} = \sqrt{\frac{205}{50}} = \sqrt{4.1} \approx 2.03 $$

1. 步骤6：理解标准误差
这个标准误差告诉我们，如果我们多次进行这样的研究，我们估计的平均成绩 $ \widehat{\beta} $ 会围绕真实平均成绩波动，其标准偏差大约是2.03。

1. 步骤7：固定效应和OLS的比较
如果我们使用固定效应模型，我们只考虑 $ \widehat{\theta} $，忽略 $ \widehat{\psi} $，标准误差会小一些：
$$ \widehat{\mathrm{SE}}(\widehat{\beta}^F) = \sqrt{\frac{\widehat{\theta}}{Jn}} = \sqrt{\frac{5}{5 \times 10}} = \sqrt{0.1} \approx 0.316 $$
如果我们使用OLS模型，我们会得到一个介于固定效应和随机效应之间的标准误差。


For the peak-expiratory-flow application, we see from the output of mixed with the reml option in section 2.5.2 that $\widehat{\operatorname{SE}}(\widehat{\beta})=26.19$ and from the output of xtreg with the fe option in section 2.6.2 that $\widehat{\operatorname{SE}}(\widehat{\beta}^{\mathrm{F}})=3.41$. We obtain the estimated model-based standard error for the OLS estimator by using the regress command,



```
regress wm
```
    
          Source |       SS           df       MS      Number of obs   =        34
    -------------+----------------------------------   F(0, 33)        =      0.00
           Model |           0         0           .   Prob > F        =         .
        Residual |  403082.735        33  12214.6283   R-squared       =    0.0000
    -------------+----------------------------------   Adj R-squared   =    0.0000
           Total |  403082.735        33  12214.6283   Root MSE        =    110.52
    
    ------------------------------------------------------------------------------
              wm | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |     453.91      18.95    23.95   0.000       415.35      492.47
    ------------------------------------------------------------------------------

Although the clustered nature of the data is not taken into account in the OLS estimator $\widehat{\beta}^{\mathrm{OLS}}$ of the population mean, a sandwich estimator can be used to produce robust standard errors for $\widehat{\beta}^{\mathrm{OLS}}$, taking the clustering into account. Using the `regress` command with the `vce(cluster id)` option, we obtain the following:

```
regress wm, vce(cluster id)
```
    
    Linear regression                               Number of obs     =         34
                                                    F(0, 16)          =       0.00
                                                    Prob > F          =          .
                                                    R-squared         =     0.0000
                                                    Root MSE          =     110.52
    
                                        (Std. err. adjusted for 17 clusters in id)
    ------------------------------------------------------------------------------
                 |               Robust
              wm | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
           _cons |     453.91      26.99    16.82   0.000       396.69      511.13
    ------------------------------------------------------------------------------

The estimated robust standard error is , which is identical, to two decimal places, to the estimated model-based standard error of from REML estimation of the variance-components model. (We have used a sandwich estimator before to obtain robust standard errors for nonclustered data in section 1.6.) Fitting an ordinary regression model to clustered data by OLS, almost invariably combined with robust standard errors for clustered data, is called pooled OLS in econometrics and is a special case of generalized estimating equations. With this approach, we learn nothing about the between and within-cluster variances or intraclass correlation. We would also like to point out that the number of clusters is perhaps too small here (smaller than the rule of thumb of 42 discussed in display 2.1) to rely on robust standard errors.


* **Estimate: Unbalanced case**

In the unbalanced case, the ML and REML estimator of $\beta$ in the variance components model becomes a weighted mean of the cluster means:

![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.12.png)


(where the weights depend on the variance estimates and therefore differ between ML and REML). Small clusters have a weight similar to large clusters if $\widehat{\theta}$ is small compared with $\widehat{\psi}$. In contrast, the OLS estimator, which disregards clustering and treats the data as single level, is

with cluster means given weights proportional to the cluster sizes $n_j$. Thus, **a variance-components model tends to give more weight to smaller clusters than does an ordinary regression model.** In the random-effects case, one new observation for a new cluster adds more information regarding the mean for the population of clusters than one new observation for a cluster already included in the sample. In OLS, whether the new observation is from a new or existing cluster is immaterial because clustering is ignored.

## 11 Assigning values to the random intercepts

Remember that the cluster-specific intercepts $\zeta_j$ are treated as random variables and not as model parameters in multilevel models. However, having obtained estimates $\widehat{\beta}$, $\widehat{\psi}$, and $\widehat{\theta}$ of the model parameters $\beta$, $\psi$, and $\theta$ (using ML, REML, or FGLS) we may wish to assign values to the random intercepts $\zeta_j$ for individual clusters; this would be analogous to obtaining predicted residuals $\widehat{\epsilon}_i$ in ordinary linear regression.

There are a number of reasons why we may want to obtain values for the random intercepts $\zeta_j$ for individual clusters. For instance, we will use such assigned values for model diagnostics (see sections 3.9 and 4.8.4), for interpreting and visualizing models (see section 4.8.3), and for inference regarding individual clusters (see section 4.8.5), such as small area estimation (see exercise 3.9) and disease mapping (see section 13.13). An example of inference for individual clusters would be to assign values to subjects' true expiratory flow $\beta + \zeta_j$ based on the fallible measurements $y_{ij}$.


It is easy to assign values to the total residuals, $\widehat{\xi}_{ij} = y_{ij} - \widehat{\beta}$. However, the total residuals are partitioned as $\xi_{ij} = \zeta_j + \epsilon_{ij}$, and different methods have been proposed for assigning values to its constituent components $\zeta_j$ and $\epsilon_{ij}$. A common feature of the methods is that a value is first assigned to the $\zeta_j$ and then $\epsilon_{ij}$ is obtained using the relation $\epsilon_{ij} = \xi_{ij} - \zeta_j$.

Values are assigned to the random intercepts $\zeta_j$ by either prediction estimation. We continue treating $\zeta_j$ as a random variable when prediction is used, whereas the $\zeta_j$ are instead viewed as unknown fixed parameters when estimation is used. The predominant approaches to assigning values to the $\zeta_j$ are maximum "likelihood" estimation, described in section 2.11.1, and empirical Bayes prediction, described in section 2.11.2.

### 11.1 Maximum “likelihood” estimation

We first substitute the parameter estimate into the variance-components $\hat \beta$ model (2.1), giving

<!--$$y_{ij} = \widehat{\beta}+\underbrace{\zeta_j+\epsilon_{ij}}_{\xi_{ij}}$$-->


![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.14.png)


It is easy to assign values to the total residuals, $\widehat \xi_{ij} = y_{ij} - \widehat{\beta}$. However, the total residuals are partitioned as $\xi_{ij} = \zeta_j + \epsilon_{ij}$, and different methods have been proposed for assigning values to its constituent components $\zeta_j$ and $\epsilon_{ij}$. A common feature of the methods is that a value is first assigned to the $\zeta_j$ and then $\epsilon_{ij}$ is obtained using the relation $\epsilon_{ij} = \xi_{ij} - \zeta_j$.

Values are assigned to the random intercepts $\zeta_j$ by either prediction estimation. We continue treating $\zeta_j$ as a random variable when prediction is used, whereas the $\zeta_j$ are instead viewed as unknown fixed parameters when estimation is used. The predominant approaches to assigning values to the $\zeta_j$ are maximum "likelihood" estimation, described in section 2.11.1, and empirical Bayes prediction, described in section 2.11.2.

$$\text{Likelihood}(y_{1j},y_{2j}|\zeta_{j})$$

treating the model parameters as known. This approach of treating $\zeta_j$ as an unknown (and fixed) parameter contradicts the original model specification, where $\zeta_j$ was treated as a random effect. We put "likelihood" in quotes because it differs from the marginal likelihood (briefly described in section 2.10.2) that is used in ML estimation of the model parameters $(\beta,\psi,\theta)$ in three ways: 1) the model parameters are treated as known, 2) the random effect is treated as an unknown parameter, and 3) the "likelihood" is based on the data for just one cluster.

We can rearrange the above model by subtracting $\widehat{\beta}$ from $y_{ij}$ to obtain estimated total residuals $\widehat{\xi}_{ij}$ and regard these as the responses:

$$\widehat \xi_{ij} = y_{ij}-\widehat{\beta}=\zeta_j+\epsilon_{ij}$$



The ML estimator of $\zeta_j$ is simply the cluster mean of the estimated total residual over the $n_j$ units (here $n_j=2$) for which we have data:

$$\widehat \zeta_j^{\mathrm{ML}}=\frac{1}{n_j}\sum_{i=1}^{n_j}\widehat \xi_{ij}=\frac{1}{2}(\widehat \xi_{1j}+\widehat \xi_{2j})$$



#### 基本概念

1. **参数估计**：我们通过数据得到模型参数的估计值。
2. **方差组分模型**：这是一个统计模型，用来分析数据中的变异性如何分配到不同的来源。
3. **随机效应**：在模型中，我们认为某些效应（如 $ \zeta_j $）是随机的，意味着它们在不同的组（或称为“簇”）之间是变化的。

#### 公式解释

1. **模型公式**：

   $ y_{ij} = \widehat{\beta} + \zeta_j + \epsilon_{ij} $
   - 这里，$ y_{ij} $ 是第 $ j $ 组中的第 $ i $ 个观测值。
   - $ \widehat{\beta} $ 是我们对总体平均效应的估计。
   - $ \zeta_j $ 是第 $ j $ 组的随机效应。
   - $ \epsilon_{ij} $ 是第 $ j $ 组第 $ i $ 个观测值的随机误差。

2. **总残差**：

   $ \xi_{ij} = \zeta_j + \epsilon_{ij} $
   - 这是 $ y_{ij} $ 减去 $ \widehat{\beta} $ 后的值，表示观测值与总体平均效应的差异。

3. **估计总残差**：

   $ \widehat \xi_{ij} = y_{ij} - \widehat{\beta} $
   - 这是我们根据观测值和估计的 $ \beta $ 计算出的残差。

4. **随机效应的估计**：

   - 有两种主要方法来估计随机效应 $ \zeta_j $：最大似然估计（MLE）和经验贝叶斯预测（EB预测）。

5. **最大似然估计**：

   $ \widehat{\zeta_j}^{\text{ML}} = \frac{1}{n_j} \sum_{i=1}^{n_j} \widehat{\xi}_{ij} $
   - 这是通过将每个组的估计总残差平均来得到的 $ \zeta_j $ 的估计值。

#### 例子
假设我们有一个班级的学生考试成绩数据，我们想要分析班级之间的平均成绩差异。

1. **收集数据**：我们有3个班级，每个班级有2个学生的成绩。
2. **计算 $ \widehat{\beta} $**：我们首先计算所有学生成绩的平均值作为 $ \widehat{\beta} $。
3. **计算总残差 $ \xi_{ij} $**：对于每个学生的成绩，我们减去 $ \widehat{\beta} $ 来得到总残差。
4. **估计随机效应 $ \zeta_j $**：对于每个班级，我们计算总残差的平均值作为该班级随机效应的估计。
5. **计算 $ \epsilon_{ij} $**：对于每个学生，我们用总残差减去 $ \zeta_j $ 来得到该学生的随机误差估计。


#### 最大似然估计（MLE）

最大似然估计是一种基于观测数据来估计模型参数的方法，它寻找能够使观测数据出现概率最大的参数值。

**公式**：

$ \widehat{\zeta_j}^{\text{ML}} = \frac{1}{n_j} \sum_{i=1}^{n_j} \widehat{\xi}_{ij} $
- $ \widehat \xi_{ij} $ 是估计的总残差，计算方式为 $ y_{ij} - \widehat{\beta} $。
- $ n_j $ 是第 $ j $ 组的观测数。

**解释**：

- MLE 通过计算每个组内所有估计残差的均值来估计随机效应 $ \zeta_j $。

#### 经验贝叶斯预测（EB预测）

经验贝叶斯方法结合了先验信息和观测数据来估计参数，通常用于处理具有随机效应的复杂模型。

**特点**：

- 它使用先验分布来描述随机效应的不确定性。
- 结合观测数据，计算随机效应的后验分布。

**公式**：

经验贝叶斯预测通常不直接给出一个封闭形式的公式，因为它涉及到积分计算。不过，基本思想是：
$ \widehat{\zeta_j}^{\text{EB}} = \int \zeta_j f(\zeta_j | \mathbf{y}_j) d\zeta_j $
- 这里，$ f(\zeta_j | \mathbf{y}_j) $ 是给定观测数据 $ \mathbf{y}_j $ 下 $ \zeta_j $ 的后验分布。

#### 例子
假设我们有3个农场，每个农场有2头牛，我们想要估计每个农场的平均牛奶产量（以升为单位）的差异。

**数据**：

- 农场1：牛1产奶量 10L，牛2产奶量 12L
- 农场2：牛1产奶量 8L，牛2产奶量 9L
- 农场3：牛1产奶量 15L，牛2产奶量 16L

**步骤1：计算 $ \widehat{\beta} $**

$ \widehat{\beta} = \frac{\sum_{j=1}^{3} \sum_{i=1}^{2} y_{ij}}{3 \times 2} $
$ \widehat{\beta} = \frac{10 + 12 + 8 + 9 + 15 + 16}{6} = 12 $

**步骤2：计算估计总残差 $ \widehat{\xi}_{ij} $**

- 农场1：$ \widehat \xi_{11} = 10 - 12 = -2 $，$ \widehat \xi_{12} = 12 - 12 = 0 $
- 农场2：$ \widehat \xi_{21} = 8 - 12 = -4 $，$ \widehat \xi_{22} = 9 - 12 = -3 $
- 农场3：$ \widehat \xi_{31} = 15 - 12 = 3 $，$ \widehat \xi_{32} = 16 - 12 = 4 $

**步骤3：使用MLE估计随机效应 $ \zeta_j $**

- 农场1：$ \widehat{\zeta_1}^{\text{ML}} = \frac{-2 + 0}{2} = -1 $
- 农场2：$ \widehat{\zeta_2}^{\text{ML}} = \frac{-4 - 3}{2} = -3.5 $
- 农场3：$ \widehat{\zeta_3}^{\text{ML}} = \frac{3 + 4}{2} = 3.5 $

**步骤4：使用EB预测随机效应 $ \zeta_j $**

这一步在没有具体先验分布和模型的情况下很难用一个简单的公式来展示。通常，我们会使用统计软件来计算后验分布，并从中得到 $ \zeta_j $ 的估计值。

#### 总结
通过这个例子，我们可以看到MLE方法是如何直接使用观测数据来估计随机效应的，而EB方法则需要更多的统计背景知识，包括先验分布和后验分布的概念。在实际应用中，选择哪种方法取决于数据的特性、模型的复杂性以及我们对参数的先验知识。


* **Implementation via OLS**

The model in (2.8) has a different mean $\zeta_j$ for each subject, and we can estimate these means by regressing $\widehat{\xi}_{ij}$ on dummy variables for each of the subjects and excluding the overall intercept because this is now redundant. The OLS estimates of the regression coefficients for the subject dummies are the required ML estimates $\widehat{\zeta}_j^{ML}$ of the $\zeta_j$.

To obtain these estimates in Stata, we first refit the model by using `mixed` (with the `quietly` prefix to suppress the output), and then we subtract the predicted fixed part $\widehat{\beta}$, obtained by using `predict` with the `xb` option, from the responses:

```
quietly mixed wm || id:, reml 
predict pred, xb
generate res = wm - pred
```

Next we regress the variable **res** on dummy variables for the 17 subjects, using the factor-variable notation for categorical variables with no base (or reference) category, `ibn.id`, and with the `noconstant` option to suppress the overall intercept.

```
regress res ibn.id, noconstant
```
    
          Source |       SS           df       MS      Number of obs   =        34
    -------------+----------------------------------   F(17, 17)       =     58.81
           Model |  396343.235        17   23314.308   Prob > F        =    0.0000
        Residual |      6739.5        17  396.441176   R-squared       =    0.9833
    -------------+----------------------------------   Adj R-squared   =    0.9666
           Total |  403082.735        34  11855.3746   Root MSE        =    19.911
    
    ------------------------------------------------------------------------------
             res | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
    -------------+----------------------------------------------------------------
              id |
              1  |      64.59      14.08     4.59   0.000        34.88       94.29
              2  |     -31.41      14.08    -2.23   0.039       -61.12       -1.71
              3  |      60.09      14.08     4.27   0.001        30.38       89.79
              4  |     -17.91      14.08    -1.27   0.220       -47.62       11.79
              5  |      46.09      14.08     3.27   0.004        16.38       75.79
              6  |     158.59      14.08    11.26   0.000       128.88      188.29
              7  |     -41.91      14.08    -2.98   0.008       -71.62      -12.21
              8  |     -68.91      14.08    -4.89   0.000       -98.62      -39.21
              9  |     196.09      14.08    13.93   0.000       166.38      225.79
             10  |     -15.41      14.08    -1.09   0.289       -45.12       14.29
             11  |     -27.91      14.08    -1.98   0.064       -57.62        1.79
             12  |     161.59      14.08    11.48   0.000       131.88      191.29
             13  |    -210.41      14.08   -14.94   0.000      -240.12     -180.71
             14  |      18.09      14.08     1.28   0.216       -11.62       47.79
             15  |    -190.41      14.08   -13.52   0.000      -220.12     -160.71
             16  |     -93.91      14.08    -6.67   0.000      -123.62      -64.21
             17  |      -6.91      14.08    -0.49   0.630       -36.62       22.79
    ------------------------------------------------------------------------------

From the output, we see that, for instance,$ \widehat{\zeta}_{1}^{\mathrm{ML}} $ =64.58823.$


```
quietly xtset id
quietly xtreg wm, mle
predict ml2, u
```


(Whether ML or FGLS estimation is used in the xtreg command does not matter here because these methods and REML all yield the same estimate $ \widehat{\beta} $ for balanced data, and the variance estimates do not affect the predictions of $ \xi_j $ .)


* **Implementation via the mean total residual**

Alternatively, the subject-specific means can be calculated using the `egen` command:

```
egen ml = mean(res), by(id)
```

We obtain the same result as before; for instance, for the first subject we get:

```
sort id
display ml[1]
```

    . display ml[1]
    64.588235


In this subsection, we have used all the terminology usually associated with estimating model parameters. However, it is important to remember that $\zeta_j$ is not a parameter in the original model. It is only for the purpose of assigning values to $\zeta_j$ that we reformulate the problem by treating the original parameters $\beta$, $\psi$, and $\theta$ as known constants and the $\zeta_j$ as unknown parameters. The "likelihood" described here must hence be distinguished from the marginal likelihood used for ML estimation of the model parameters.

这段话的意思是：我们通常用一些专业术语来讨论如何确定一个数学模型中的数字（参数）。但是，这里我们遇到了一个特殊情况，有一个数字$\zeta_j$，它本来不是我们模型的一部分。我们只是为了方便，假装其他几个数字（参数）$\beta$、$\psi$和$\theta$我们已经知道了，然后我们把$\zeta_j$当作未知数来找它。这里我们说的“可能性”（似然）和我们平时用来找原始数字的方法（最大似然估计）里的“可能性”不一样。


假设我们有一个简单的线性模型：$ y = \beta x + \epsilon $
其中，$ y $ 是观测值，$ x $ 是自变量，$ \beta $ 是斜率，$ \epsilon $ 是误差项。
在原始模型中，$ \beta $ 是我们需要估计的参数。但假设我们现在想要估计一个新的量，比如说数据的方差 $ \sigma^2 $。在这个新问题中，我们将 $ \beta $ 视为已知（即使在原始问题中它是未知的），而 $ \sigma^2 $ 成为了我们的未知参数 $ \zeta_j $。
我们可能会构建一个新的似然函数来估计 $ \sigma^2 $，这个似然函数将基于 $ \beta $ 的已知值。这个新的似然函数与原始问题中用于估计 $ \beta $ 的似然函数是不同的。同样，如果我们想要比较不同模型的拟合优度，我们可能会使用边际似然，这涉及到对所有可能的 $ \beta $ 值进行积分。


### 11.2 Empirical Bayes prediction

Having obtained estimates $\widehat{\beta} $, $\widehat{\psi} $, and $\widehat{\theta}$ of the model parameters by any of the methods described earlier in this chapter, we can treat them as the true parameter values and predict the random intercepts $\zeta_j$ for individual clusters (subjects in the application). Here we continue to treat $\zeta_j$ as a random variable, not as a fixed parameter as in ML estimation.

ML estimation of $\zeta_j$ uses the responses $y_{ij}$ for subject $j$ as the only information about $\zeta_j$ by maximizing the "likelihood" of observing these particular values:

$$\text{Likelihood}(y_{1j},y_{2j}|\zeta_{j})$$

In contrast, empirical Bayes prediction also uses the prior distribution of $\zeta_j$, summarizing our knowledge about $\zeta_j$ before seeing the data for subject $j$:

$$\operatorname{Prior}(\zeta_j)$$

This prior distribution is just the normal distribution specified for the random intercept with zero mean and estimated variance $\widehat{\psi}.$ It represents what we know about $ \zeta_j $ before we have seen the responses $y_{1j}$ and $y_{2j}$ for subject $ j $. For instance, the most likely value of $ \zeta_j $ is 0. (Obviously, we have already used all responses to obtain the estimate $ \widehat{\psi} $, but we now pretend that $ \psi $ is known and not estimated.)

Once we have observed the responses, we can combine the prior distribution with the "likelihood" to obtain the posterior distribution of $ \zeta_j $ given the observed responses $ y_{1j} $ and $ y_{2j} $ . According to Bayes theorem,

$$\mathrm{Posterior}(\zeta_j|y_{1j},y_{2j}) \propto \mathrm{Prior}(\zeta_j)\times\mathrm{Likelihood}(y_{1j},y_{2j}|\zeta_j)$$

where $ \propto $ means "proportional to". The posterior of $ \zeta_j $ represents our updated knowledge regarding $ \zeta_j $ after seeing the data $y_{1j}$ and $y_{2j}$ for subject $ j $.

The empirical Bayes prediction is just the mean of the posterior distribution with parameter estimates $( \widehat{\beta} , \widehat{\psi} , \text{and} \widehat{\theta} ) $ plugged in. In a linear model with normal error terms, the posterior is normal and the mean is thus equal to the mode.

Figure 2.9 shows the prior, "likelihood", and posterior for a hypothetical example of a subject with $ n_j = 2 $ responses. In both panels, the estimated total residuals $ \widehat{\xi}_{ij} $ are 3 and 5, and the estimated total variance is $ \widehat{\psi} + \widehat{\theta} = 5 $ . In the top panel, 80% of this variance is due to within-subject variability, whereas in the bottom panel, 80% is due to between-subject variability. In both cases, the "likelihood" (dotted curve) has its maximum at $ \zeta_j = 4 $, that is, the mode is 4 (see vertical dotted lines). The ML estimate therefore is $\widehat{\zeta}_{j}^{\mathrm{ML}}=4$. In contrast, the mode (and mean) of the posterior depends on the relative sizes of the variance components and is $1.33$ in the top panel and $3.56$ in the bottom panel (see vertical dashed lines). The larger between-subject variance in the bottom panel means that the prior distribution is less informative and will have less influence on the predictions. At the same time the "likelihood" provides more information than in the top panel, giving a prediction much closer to the ML estimate. The mean of the posterior lies between the mean of the prior ($0$, vertical solid lines) and the mode of the "likelihood".


<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.17.png" style="width:540px;height:400px;" alt="图片描述">
  <figcaption><strong></strong></figcaption>
</figure>

<figure style="text-align:center;">
  <img src="https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.6.25.18.png" style="width:540px;height:400px;" alt="图片描述">
  <figcaption><strong>Figure 2.9: Prior distribution, "likelihood"(normalized), and posterior distribution for a hypothetical subject with $n_j = 2$ responses with total residuals $\widehat{\xi}_{1j} = 3$ and $\widehat{\xi}_{2j} = 5$[the vertical lines represent modes (and means) of the distributions]</strong></figcaption>
</figure>





1. **参数估计**：我们通过某种方法得到了模型参数的估计值，记为 $ \hat{\beta} $，$ \hat{\psi} $ 和 $ \hat{\theta} $。这些估计值被当作真实的参数值来使用。

2. **随机截距的预测**：我们用这些参数估计值来预测个体群体（比如研究中的受试者）的随机截距 $ \zeta_j $。这里，$ \zeta_j $ 被视为一个随机变量，而不是像在最大似然估计中那样被视为一个固定参数。

3. **最大似然估计**：在最大似然估计中，我们只使用受试者 $ j $ 的响应 $ y_{ij} $ 来估计 $ \zeta_j $，通过最大化观察到这些特定值的“可能性”来实现。

4. **经验贝叶斯预测**：与最大似然估计不同，经验贝叶斯预测还会使用 $ \zeta_j $ 的先验分布，这是我们在看到受试者 $ j $ 的数据之前的 $ \zeta_j $ 的知识。

5. **先验分布（Prior Distribution）**：这个先验分布是正态分布，均值为0，方差为 $ \hat{\psi} $。它代表了我们在看到响应 $ y_{1j} $ 和 $ y_{2j} $ 之前的 $ \zeta_j $ 的知识。
- 先验分布是基于我们对世界的先入为主的知识或信念。在我们的例子中，我们假设学生的平均成绩是围绕0分的正态分布。这意味着我们认为大多数学生的成绩会接近0分，但也有可能高于或低于0分。

6. **似然函数（Likelihood）**
- 似然函数是基于我们观察到的数据来评估某个参数（在这个例子中是学生的平均成绩）的概率。如果我们观察到学生A两次考试的成绩是3分和5分，似然函数会告诉我们，给定这些成绩，学生A的平均成绩是4分的可能性。

7. **后验分布（Posterior Distribution）**：一旦我们观察到响应，我们就可以结合先验分布和“可能性”来获得给定观察响应的 $ \zeta_j $ 的后验分布。这是根据贝叶斯定理来计算的。
- 后验分布是结合了先验知识和似然函数后得到的新的概率分布。它代表了我们在看到数据后对参数的新认识。

8. **经验贝叶斯预测（Bayesian Prediction）**：经验贝叶斯预测就是后验分布的均值，这在正态误差项的线性模型中等于众数。
- 贝叶斯预测是后验分布的均值。在我们的例子中，这意味着我们根据先验知识和观察到的数据来更新我们对学生平均成绩的预测。


9. 图2.9的详细解释
- 图2.9展示了两种情况，每种情况都有两种变异性：内部变异性和外部变异性。
- **内部变异性**：指的是每次考试的难度不同导致的分数波动。在顶部面板中，80%的方差是由于内部变异性，这意味着学生的分数更可能接近班级平均分。因此，即使学生A的两次考试成绩是3分和5分，我们的预测（后验均值）也会偏向于先验分布的均值0分，结果是1.33分。
- **外部变异性**：指的是学生之间的能力差异。在底部面板中，80%的方差是由于外部变异性，这意味着学生的分数更可能远离班级平均分。因此，后验均值会更多地受到似然函数的影响，结果是3.56分。
#### 后验均值的计算
- 后验均值是通过将先验分布和似然函数相乘，然后根据贝叶斯定理进行归一化得到的。归一化的目的是确保所有可能的预测值的概率之和为1。

10. 例子
- 假设我们有两个学生，学生A和学生B，他们各自有两次考试的成绩。
- 学生A的成绩是3分和5分，学生B的成绩是2分和6分。
- 我们使用经验贝叶斯方法来预测他们的平均成绩。

* 学生A

- 先验分布：我们假设所有学生的平均成绩围绕0分的正态分布。
- 似然函数：基于学生A的两次成绩，我们预测他的成绩是4分。
- 后验分布：结合先验知识和似然函数，我们预测学生A的平均成绩是1.33分（假设内部变异性较大）。

* 学生B

- 先验分布：与学生A相同。
- 似然函数：基于学生B的两次成绩，我们预测他的成绩是4分。
- 后验分布：结合先验知识和似然函数，我们预测学生B的平均成绩是3.56分（假设外部变异性较大）。

* 结论
- 通过贝叶斯方法，我们可以根据先验知识和观察到的数据来更新我们对某个参数的预测。
- 在内部变异性较大的情况下，我们的预测会偏向于先验分布的均值。
- 在外部变异性较大的情况下，我们的预测会更多地受到似然函数的影响。

现在，让我们用一个简单的例子来说明：
假设我们有一个班级，我们想要预测每个学生的数学成绩。我们已经有了一些关于学生平均成绩的信息（$ \hat{\beta} $，$ \hat{\psi} $，$ \hat{\theta} $），但我们想要更精确地预测每个学生的成绩 $ \zeta_j $。
- **最大似然估计**：我们只看每个学生在一次考试中的表现，然后基于这次考试的成绩来估计他们的成绩。
- **经验贝叶斯预测**：我们不仅看这次考试的成绩，还考虑了学生以往的成绩分布（先验分布）。结合这次考试的成绩和以往的成绩分布，我们可以得到一个更准确的预测（后验分布的均值）。
在这个例子中，经验贝叶斯预测会给我们一个更全面的视角，因为它不仅考虑了最新的数据，还考虑了过去的信息。这样，我们的预测会更加准确和可靠。

In fact, there is a simple formula relating the empirical Bayes prediction $\widetilde{\zeta}_j^{\mathrm{EB}}$ to the maximum "likelihood" estimator $\widetilde{\zeta}_j^{\mathrm{ML}}$ in linear random-intercept models:

$$\widetilde{\zeta}_j^{\mathrm{EB}} = \widehat{R}_j\widehat{\zeta}_j^{\mathrm{ML}}\quad\mathrm{where}\quad \widehat{R}_j = \frac{\widehat{\psi}}{\widehat{\psi}+\widehat{\theta}/n_j}$$

Here $\widehat{R}_j$ is similar to the estimated intraclass correlation, except that we divide the estimated level-1 variance $\widehat{\theta}$ by the number of responses $n_j$. $\widehat{R}_j$ can be interpreted as the _reliability_ of the ML estimator of $\zeta_j$, defined as the proportion of the variance of the ML estimator that is due to the variance of the random intercept. $\widehat{R}_j$ is also known as the _shrinkage factor_ because $0\leq \widehat{R}_j<1$ so that the empirical Bayes prediction is shrunken toward $0$ (the mean of the prior). There will be more shrinkage (that is, greater influence of the prior) if we have

$$\widetilde{\zeta}_j^{\mathrm{EB}} = \widehat{R}_j\widehat{\zeta}_j^{\mathrm{ML}}\quad\mathrm{where}\quad \widehat{R}_j = \frac{\widehat{\psi}}{\widehat{\psi}+\widehat{\theta}/n_j}$$

Here $\widehat{R}_j$ is similar to the estimated intraclass correlation, except that we divide the estimated level-1 variance $\widehat{\theta}$ by the number of responses $n_j$. $\widehat{R}_j$ can be interpreted as the reliability of the ML estimator of $\zeta_j$, defined as the proportion of the variance of the ML estimator that is due to the variance of the random intercept. $\widehat{R}_j$ is also known as the shrinkage factor because $0 \leq \widehat{R}_j < 1$ so that the empirical Bayes prediction is shrunken toward $0$ (the mean of the prior). There will be more shrinkage (that is, greater influence of the prior) if we have


* a small random-intercept variance ($\widehat{\psi}$) (an informative prior)
* a large level-1 residual variance ($\widehat{\theta}$) (uninformative data)
* a small cluster size ($n_j$) (an uninformative cluster)

A nice feature of empirical Bayes prediction is that the prediction error, defined as the difference $\widetilde \zeta_j^{\mathrm{EB}}-\zeta_j$ between the prediction and the truth, has zero mean over repeated samples of $\zeta_j$ and $\epsilon_{ij}$ (or repeated samples of clusters and units from clusters) when model parameters are treated as fixed and known. Empirical Bayes predictions also have the smallest possible variance (for given model parameters). In linear mixed models, the empirical Bayes predictor is therefore also known as the best linear unbiased predictor (BLUP). Because predictions are based on estimated model parameters, the term estimated best linear unbiased predictor (EBLUP) is sometimes used.

Empirical Bayes predictions are conditionally biased in the sense that their mean over repeated samples of $\epsilon_{ij}$ for a given $\zeta_j$ (or repeated samples of units from the same cluster) will be too close to 0 because of shrinkage . In contrast, the ML estimator is conditionally unbiased but has a greater prediction-error variance.

In most applications, shrinkage is desirable because it only affects clusters that provide little information and effectively downplays their influence, borrowing strength from other clusters. For instance, the empirical Bayes predictor for the cluster mean $\beta+\zeta_j$ becomes

<!--$$\widehat{\beta}+\widetilde{\zeta}_{j}^{\mathrm{EB}} = \widehat{\beta}+\widehat{R}_{j}\widehat{\zeta}_{j}^{\mathrm{ML}} = \widehat{\beta}+\widehat{R}_{j}(\overline{y}_{\cdot j}-\widehat{\beta}) = (1-\widehat{R}_{j})\widehat{\beta}+\widehat{R}_{j}\overline{y}_{\cdot j}$$-->

![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.7.12.1.png)

We see that the empirical Bayes prediction can be expressed as a weighted average of the estimated population mean $\widehat{\beta}$, which is based on data for all clusters, and the cluster sample mean $\overline{y}_{.j}$, based only on the data for cluster $j$. Hence, clusters with low reliabilities borrow more strength from other clusters than do clusters with high reliabilities. Since $\widehat{\beta}$ is based on total pooling of data across clusters, shrinkage estimation is sometimes described as partial pooling .

For the Mini Wright meter measurements, the shrinkage factor can be calculated from the REML estimates of $\sqrt{\psi}$ and $\sqrt{\theta}$ in table 2.2:

**1. 统计模型背景**

首先在我们讨论公式之前，让我们了解一下我们面对的是一个什么样的统计问题。我们通常会有一堆数据，比如学生的成绩、病人的恢复时间等等。这些数据可能会分为不同的组或者簇，每组有自己的特征。在这个模型中，我们假设每个簇有一个截距，这个截距是随机的，代表了这个簇与总体平均值的差异。

**2. 经验贝叶斯预测（Empirical Bayes prediction, EB预测）**

经验贝叶斯预测是一种统计方法，它结合了来自数据的证据和先验知识来做出更准确的预测。在这个问题中，我们关注的是线性随机截距模型。

**3. 最大似然估计（Maximum Likelihood estimation, ML估计）**

最大似然估计是一种常用的参数估计方法。它通过最大化观测数据的概率来估计参数。在这个问题中，$\widehat{\zeta}_j^{\mathrm{ML}}$ 是第 $j$ 个簇的随机截距的ML估计。

**4. 收缩因子（Shrinkage Factor, $\widehat{R}_j$）**

收缩因子 $\widehat{R}_j$ 是一个介于0和1之间的数，用于调整ML估计，使其更接近先验分布的均值。它定义为：

$$\widehat{R}_j = \frac{\widehat{\psi}}{\widehat{\psi} + \frac{\widehat{\theta}}{n_j}}$$

- $\widehat{\psi}$：随机截距的估计方差，反映了簇间变异的不确定性。
- $\widehat{\theta}$：一级残差方差，反映了簇内个体之间的变异。
- $n_j$：第 $j$ 个簇中的响应数量，即簇的大小。

**5. 收缩（Shrinkage）**

收缩是指将估计值向某个参考点（通常是先验均值）拉拢的过程。在这个问题中，收缩是向0拉拢，因为0通常是先验均值。收缩的程度由 $\widehat{R}_j$ 决定：

- 如果 $\widehat{R}_j$ 接近1，那么收缩很小，ML估计的影响更大。
- 如果 $\widehat{R}_j$ 接近0，那么收缩很大，先验均值的影响更大。

收缩因子 $\widehat{R}_j$ 的值在0和1之间：

- 当 $\widehat{R}_j$ 接近1时，表示最大似然估计非常可靠，经验贝叶斯预测与最大似然估计相差不大。
- 当 $\widehat{R}_j$ 接近0时，表示最大似然估计不太可靠，经验贝叶斯预测会更多地依赖先验信息。

**6. 收缩的条件**

收缩的显著性取决于簇内的变异性、簇的大小以及随机截距的方差。
收缩更多地发生在以下情况：

- **小的随机截距方差**（$\widehat{\psi}$小）：这意味着先验分布比较集中，我们对簇间差异的不确定性较小。
- **大的一级残差方差**（$\widehat{\theta}$大）：这意味着簇内个体之间的变异较大，数据提供的信息较少。
- **小的簇大小**（$n_j$小）：这意味着每个簇提供的信息较少。

- 如果簇内的变异性大（$\widehat{\theta}$大），簇小（$n_j$小），那么收缩会更显著，因为数据不够来准确估计每个簇的特定情况。
- 如果随机截距的方差较小（$\widehat{\psi}$小），表示先验信息很强烈，也会增加收缩。

**7. 经验贝叶斯预测的公式**

经验贝叶斯预测公式实际上就是将最大似然估计调整（或者说“收缩”）到0（先验平均值）的一个程度：

经验贝叶斯预测的误差平均为0，这是其一个优点

将上述概念结合起来，我们得到经验贝叶斯预测的公式：

$$\widetilde{\zeta}_j^{\mathrm{EB}} = \widehat{R}_j \cdot \widehat{\zeta}_j^{\mathrm{ML}}$$


这个公式告诉我们，EB预测是ML估计乘以收缩因子 $\widehat{R}_j$。
这里的 $\widehat{R}_j$ 是一个权重，它告诉我们应该如何调整最大似然估计。

**8. 例子1**

假设我们有以下数据：

- 随机截距方差 $\widehat{\psi} = 2$
- 一级残差方差 $\widehat{\theta} = 10$
- 簇大小 $n_j = 5$

我们可以计算调整因子 $\widehat{R}_j$：

$$\widehat{R}_j = \frac{2}{2 + \frac{10}{5}} = \frac{2}{4} = 0.5$$

这意味着我们的EB预测将会是ML估计的一半。如果我们的ML估计是4，那么EB预测将会是：

$$\widetilde{\zeta}_j^{\mathrm{EB}} = 0.5 \times 4 = 2$$

这个EB预测值比ML估计更接近0，这是因为我们对簇间差异的不确定性较小，而簇内变异较大，且簇的大小较小。

希望这个更详细的解释能帮助你更好地理解经验贝叶斯预测和收缩因子的概念。


**9. 例子2**

举一个更实际的例子来说明这个概念。假设我们在一个学校里收集了不同班级学生的数学成绩，每个班级是一个簇。我们想要预测每个班级的平均成绩。

- 假设总体平均成绩是 $\widehat{\beta}$。
- 某个班级的平均成绩是 $\overline{y}_{\cdot j}$。
- 班级的大小是 $n_j$。

如果我们直接使用班级的平均成绩来预测，我们可能会高估或低估总体情况，特别是当班级很小或者成绩很不寻常时。经验贝叶斯方法会考虑这些因素，提供一个更加平衡的预测：

<!--$$\widehat{\beta} + \widetilde{\zeta}_{j}^{\mathrm{EB}} = (1 - \widehat{R}_{j}) \times \widehat{\beta} + \widehat{R}_{j} \times \overline{y}_{\cdot j}$$-->

![](https://cdn.jsdelivr.net/gh/forest293/Hugo-image/2024.7.12.1.png)

这个预测是总体平均成绩和班级平均成绩的加权平均，权重由收缩因子 $\widehat{R}_{j}$ 决定。

```
* using shrinkage factor
display 110.40^2/(110.40^2+(19.91^2)/2)
```

    .98399821

Instead of typing the rounded estimates, we can access the unrounded counterparts directly after estimation. For regression coefficients, we have already used the syntax `_b[varname]`. To find out how to access the standard deviations, we first fit the model by using ``mixed`` with the ``estmetric`` and ``coeflegend`` options.

The estimation metric for each standard deviation is the logarithm of the standard deviation. We can access the estimated logarithms by using the syntax ``_b[lns1_1_1:_cons]`` and ``_b[lnsig_e:_cons]`` (see left column of output).


```
mixed wm || id:, reml estmetric coeflegend
display exp(_b[lns1_1_1:_cons])
display exp(_b[lnsig_e:_cons])
```

    . display exp(_b[lns1_1_1:_cons])
    110.39706
    
    . display exp(_b[lnsig_e:_cons])
    19.910826

and compute the shrinkage factor by using

```
display exp(_b[lns1_1_1:_cons])^2/(exp(_b[lns1_1_1:_cons])^2 + exp(_b[lnsig_e:_cons])^2/2)
```

    .98399606

Alternatively, we can use `estat sd, post` to post the results as standard deviations. Before doing so, we store the estimates as they were posted by `mixed`.

```
estimates store mixed
estat sd, post coeflegend
```

    ------------------------------------------------------------------------------
                 | Coefficient  Legend
    -------------+----------------------------------------------------------------
    wm           |
           _cons |     453.91  _b[wm:_cons]
    -------------+----------------------------------------------------------------
    id           |
        sd(_cons)|     110.40  _b[id:sd(_cons)]
    -------------+----------------------------------------------------------------
    Residual     |
            sd(e)|      19.91  _b[Residual:sd(e)]
    ------------------------------------------------------------------------------

Because of the `coeflegend` option, we can see how to refer to the standard deviation estimates, so we can obtain the shrinkage factor by using:

```
display _b[id:sd(_cons)]^2/(_b[id:sd(_cons)]^2 + ///
	_b[Residual:sd(e)]^2/2)
```

    .98399606

We then restore the original mixed results by using

```
estimates restore mixed
```

We can now obtain empirical Bayes predictions in two ways: either by multiplying the ML estimates obtained in section 2.11.1 by the shrinkage factor,

```
generat eb1 = .98399606*ml
```

or by using the `predict` command with the `reffects` (for “random effects”) option after estimation using `mixed`,

```
* using mixed
predict eb2, reffects
sort id
format eb1 eb2 %8.2f
list id eb1 eb2 if occasion==1, clean noobs
```
    
        id       eb1       eb2  
         1     63.55     63.55  
         2    -30.91    -30.91  
         3     59.13     59.13  
         4    -17.63    -17.63  
         5     45.35     45.35  
         6    156.05    156.05  
         7    -41.24    -41.24  
         8    -67.81    -67.81  
         9    192.95    192.95  
        10    -15.17    -15.17  
        11    -27.47    -27.47  
        12    159.00    159.00  
        13   -207.04   -207.04  
        14     17.80     17.80  
        15   -187.36   -187.36  
        16    -92.41    -92.41  
        17     -6.80     -6.80  


It should be kept in mind that empirical Bayes predictions can be sensitive to the distributional assumption for the random intercepts (here normality) unless the cluster sizes are very large.

### 11.3 Empirical Bayes standard errors

There are several different kinds of variances (squared standard errors) for empirical Bayes predictions that can be used to express uncertainty regarding the predictions. These variances do not take into account the uncertainty in the parameter estimates because the parameters are typically treated as known in empirical Bayes prediction. The exception is prediction based on REML estimation where uncertainty in the estimation of $\beta$ is taken into account. Obviously, model-parameter uncertainty matters more in small samples.

* **Posterior and comparative standard errors**

As defined above, the empirical Bayes prediction of $\zeta_j$ is the mean of the posterior distribution of $\zeta_j$, given the responses $y_{1j}$ and $y_{2j}$, and examples of such posterior distributions are shown as dashed curves in figure 2.9. The corresponding variance of the posterior distribution expresses the degree of uncertainty regarding the prediction (if the parameters were known). In linear models, the posterior variance is given by

$$\mathrm{Var}(\zeta_j|y_{1j},y_{2j}) = (1-R_j) \psi $$

As expected, this variance is smaller than the prior variance $\psi$ because of the information gained regarding the random intercept by knowing the responses $y_{1j}$ and $y_{2j}$ for cluster (here subject) $j$.

Another way to express uncertainty regarding predictions is via the variance of the prediction errors, $\widetilde \zeta_j^{\mathrm{EB}}-\zeta_j$, over repeated samples of $\zeta_j$ and $\epsilon_{ij}$ (or repeated samples of clusters $j$ and units $i$), also known as the mean squared error of prediction (MSEP). In linear models, the MSEP happens to be equal to the posterior variance,

$$\operatorname{Var}(\widetilde \zeta_j^{\mathrm{EB}}-\zeta_j)=(1-R_j)\psi$$

The square root of the MSEP is often called the *comparative standard error* because it can be used for inferences regarding differences between clusters’ true random intercepts.

The comparative standard error can be estimated by plugging in the estimated shrinkage factor, calculated earlier as $\widehat{R}_j=0.98399821$, and $\widehat{\psi}$as follows:


#### 经验贝叶斯预测

经验贝叶斯预测是一种统计方法，它结合了先验信息和观测数据来估计未知参数。这种方法特别适用于参数很多但每个参数的观测数据较少的情况。

#### 后验方差

1. **概念**：
   - 后验方差是参数在给定观测数据后的不确定性度量。它是后验分布的方差，表示在考虑了数据之后，我们对参数估计的不确定性。
2. **公式**：
   - 公式 $ \mathrm{Var}(\zeta_j|y_{1j},y_{2j}) = (1-R_j) \psi $ 表示后验方差是如何计算的。
   - 这里，$ \zeta_j $ 是我们要预测的参数，比如一个群体的随机截距。
   - $ y_{1j} $ 和 $ y_{2j} $ 是这个群体的观测数据。
   - $ R_j $ 是缩减因子，它衡量了观测数据对预测的影响程度。
   - $ \psi $ 是先验方差，表示在没有观测数据时，我们对参数的不确定性。
3. **理解**：
   - 缩减因子 $ R_j $ 越接近1，意味着观测数据对预测的贡献越大，后验方差就越小，表示预测的不确定性降低了。
   - 如果 $ R_j $ 接近0，那么后验方差就接近先验方差，表示我们对参数的预测不确定性很高。

#### 比较标准误差

1. **概念**：
   - 比较标准误差是预测误差的标准差，用于衡量不同群体或个体之间预测值的差异。
2. **公式**：
   - 公式 $ \operatorname{Var}(\widetilde \zeta_j^{\mathrm{EB}}-\zeta_j) = (1-R_j)\psi $ 表示预测误差的方差。
   - $ \widetilde \zeta_j^{\mathrm{EB}} $ 是根据经验贝叶斯方法得到的预测值。
   - $ \zeta_j $ 是真实值。
3. **计算**：
   - 比较标准误差的计算是取预测误差方差的平方根：
     $ \text{比较标准误差} = \sqrt{(1 - R_j) \psi} $

#### 例子

假设我们有一个简单的线性模型，其中 $ \zeta_j $ 是某个群体的随机截距，我们有两组数据 $ y_{1j} $ 和 $ y_{2j} $。我们先前估计的缩减因子 $ \widehat{R}_j $ 是0.98399821，先验方差 $ \widehat{\psi} $ 是某个已知的值，比如10。
- **计算后验方差**：
  $ \mathrm{Var}(\zeta_j|y_{1j},y_{2j}) = (1 - 0.98399821) \times 10 $
  计算得到后验方差大约是0.16。
- **计算比较标准误差**：
  $ \text{比较标准误差} = \sqrt{0.16} $
  计算得到比较标准误差大约是0.4。

#### 结论

通过这个例子，我们可以看到，后验方差和比较标准误差是如何帮助我们理解预测的不确定性的。后验方差越小，表示我们对参数的预测越确定；比较标准误差越小，表示不同群体或个体之间的预测差异越小。


The comparative standard error can be estimated by plugging in the estimated shrinkage factor, calculated earlier as $\widehat{R}_j=0.98399821$, and $\widehat{\psi}$as follows:

```
* comparative standard errors
generate comp_se = sqrt((1-.98399821)*exp(_b[lns1_1_1:_cons])^2)
display comp_se[1]
```
    
    13.965028

When predicting the random effects with Stata’s `predict` command, you can add the `reses()` (for “random-effects standard errors”) option to produce such comparative standard errors after ML estimation using `mixed`, so identical results would be obtained as follows:

```
 mixed wm || id:, mle
 predict eb, reffects reses(comp_se)
```

(After REML estimation, the predict command produces standard errors that take into account the uncertainty of $\hat \beta$—see section 2.11.3.)

* **Diagnostic standard errors**

For linear variance-components models, the sampling distribution of the empirical Bayes predictions (over repeated samples of $\zeta_j$ and $\epsilon_{ij}$, or of clusters and units from clusters) is normal with mean 0 and variance

$$\operatorname{Var}(\widetilde \zeta_j^{\mathrm{EB}})=\frac{\psi}{\psi+\theta/n_j}\psi=R_j\psi$$

This variance is useful for deciding if the empirical Bayes prediction for a given cluster is aberrant. For instance, $95\%$ of predictions should be no larger in absolute value than about two sampling standard deviations. Thus, the sampling standard deviation is often called the diagnostic standard error.

We can estimate the diagnostic standard error from the estimated prior variance $\widehat{\psi}$ and prediction error variance obtained earlier, using the relationship

$$\mathrm{Var}(\widetilde{\zeta}_j^{\mathrm{EB}})=R_j\psi=\psi-(1-R_j)\psi=\psi-\mathrm{Var}(\widetilde{\zeta}_j^{\mathrm{EB}}-\zeta_j)$$

The corresponding estimated standard error can be obtained as follows:

```
* diagnostic standard errors
generate diag_se = sqrt(exp(_b[lns1_1_1:_cons])^2 - comp_se^2)
display diag_se[1]
```

    106.13241

If $\widehat{R}_j >0.5$, as is usually the case in practice, we obtain the following relation among the empirical Bayes variances:

$$\mathrm{Var}(\zeta_j|y_{1j},y_{2j})=\mathrm{Var}(\widetilde{\zeta}^{\mathrm{EB}}_j-\zeta_j)<\mathrm{Var}(\widetilde{\zeta}^{\mathrm{EB}}_j)$$

As we would expect, the relation is satisfied for the Mini Wright data because $\widehat{R}_j=0.98$.

#### 抽样分布的基本概念
首先，我们讨论的是经验贝叶斯预测的抽样分布。抽样分布是指如果我们多次从同一个群体中抽取样本，然后进行预测，这些预测值的分布情况。这里，我们假设这个分布是正态分布的，正态分布是一种常见的分布，它有一个中心值（均值）和描述分布宽度（方差）的参数。

#### 抽样分布的方差

1. **方差的公式**：
   - 公式 $ \operatorname{Var}(\widetilde \zeta_j^{\mathrm{EB}}) = \frac{\psi}{\psi + \frac{\theta}{n_j}} \psi = R_j\psi $
2. **公式分解**：
   - 这里，$ \psi $ 是先验方差，它代表了在没有观测数据之前，我们对参数 $ \zeta_j $ 的不确定性。
   - $ \theta $ 是与残差方差相关的参数。
   - $ n_j $ 是第 $ j $ 个群体中的观测数量。
   - $ R_j $ 是缩减因子，它表示观测数据对预测值的影响程度。
3. **缩减因子 $ R_j $**：
   - 缩减因子 $ R_j $ 的计算公式是 $ R_j = \frac{\theta}{\psi + \theta} $。它是一个0到1之间的数，表示观测数据相对于先验信息的重要性。
4. **方差的实际意义**：
   - 这个方差告诉我们，对于给定的群体，预测值的不确定性有多大。如果方差较小，表示预测值比较集中，不确定性较小。

#### 诊断标准误差

1. **诊断标准误差**：
   - 诊断标准误差是方差的平方根，它用来衡量单个预测值与真实值之间的差异。
2. **如何使用**：
   - 例如，如果我们知道95%的预测值应该在真实值的两个标准偏差之内，那么这个标准误差就非常有用。
3. **估计诊断标准误差**：
   - 我们可以通过先验方差 $ \widehat{\psi} $ 和预测误差方差来估计诊断标准误差。

#### 具体例子

假设我们有以下参数：
- 先验方差 $ \psi = 100 $
- 残差方差参数 $ \theta = 50 $
- 群体大小 $ n_j = 10 $
- 缩减因子 $ R_j = 0.9 $
1. **计算方差**：
   - $ \operatorname{Var}(\widetilde \zeta_j^{\mathrm{EB}}) = \frac{100}{100 + \frac{50}{10}} \times 100 = \frac{100}{100 + 5} \times 100 = \frac{100}{105} \times 100 \approx 95.24 $
2. **计算诊断标准误差**：
   - $ \text{Diagnostic Standard Error} = \sqrt{95.24} \approx 9.76 $
这意味着，如果我们对这个群体进行多次抽样，95%的预测值应该在真实值的±19.52（大约是两个标准偏差）范围内。

#### 经验贝叶斯方差之间的关系

如果 $ \widehat{R}_j > 0.5 $，通常在实践中是这样的，我们可以得到以下关系：
- $ \mathrm{Var}(\zeta_j|y_{1j},y_{2j}) = \mathrm{Var}(\widetilde{\zeta}^{\mathrm{EB}}_j - \zeta_j) < \mathrm{Var}(\widetilde{\zeta}^{\mathrm{EB}}_j) $
这表示后验方差小于预测误差的方差，也小于抽样分布的方差。

#### 结论

通过这个详细的解释和例子，你应该能够理解经验贝叶斯预测的抽样分布和方差，以及如何使用它们来评估预测的不确定性。

## 12 Summary and further reading

In this chapter, we introduced the idea of decomposing the total variance of the response variable into variance components, specifically the between-cluster variance $ \psi $ and the within-cluster variance $ \theta $ . This was accomplished by specifying a model that includes corresponding error components; a level-2 random intercept $ \xi_j $ for clusters; and a level-1 residual $ \epsilon_{ij} $ for units within clusters, where $ \epsilon_{ij} $ is uncorrelated with $ \xi_j $ . The random intercept induces correlations among responses for units in the same cluster, known as the intraclass correlation.

The random intercept is a random variable and not a model parameter. The realizations of $ \xi_j $ change in repeated samples, either because clusters are sampled or because the random intercept is redrawn from the data-generating model for given clusters. An alternative to random intercepts are fixed intercepts. Some guidelines for choosing between random and fixed intercepts were given, and this issue will be revisited in the next chapter.

The concepts discussed in this chapter underlie all multilevel or hierarchical modeling. By considering the simplest case of a multilevel model, we provided some insight into estimation of model parameters and prediction of random effects. We also discussed how to conduct hypothesis testing and construct confidence intervals for variance-components models. Although the expressions for estimators and predictors become more complex for the models discussed in later chapters of this volume, the basic ideas remain the same.

For further reading about variance-components models, we recommend Snijders and Bosker (2012, chap. 3), as well as many of the books referred to in later chapters of this volume. Streiner, Norman and Cairney (2015), Shavelson and Webb (1991), and Dunn (2004) are excellent books on linear measurement models. We refer to Skrondal and Rabe-Hesketh (2009) for an overview of methods for prediction of random effects and responses in multilevel models.

The exercises cover a range of applications, such as measurement of peak expiratory flow (exercise 2.1), measurement of psychological distress (exercise 2.2), essay grading (exercise 2.5), neuroticism of twins (exercise 2.3), birthweights of siblings (exercise 2.7), head sizes of brothers (exercise 2.6), and achievement of children nested in neighborhoods and schools (exercise 2.4). Exercise 2.8 is about random-effects meta-analysis, a topic not discussed in this chapter.



#### 方差组分模型的概念

1. **总方差的分解**：
   - 在统计分析中，我们经常需要理解数据中的变异性。方差组分模型帮助我们将总方差分解为不同的部分，以便更好地理解数据的结构。

2. **组分介绍**：
   - 组分之一是**组间方差**（between-cluster variance），记为 $ \psi $。它表示不同群体（如不同的学校或家庭）之间的差异。
   - 另一个组分是**组内方差**（within-cluster variance），记为 $ \theta $。它表示同一群体内部个体的差异。

3. **模型的组成部分**：
   - 为了实现方差的分解，模型中引入了相应的误差组分：
     - **Level-2 随机截距** $ \xi_j $：代表群体级别的随机效应，例如，不同学校的学生平均成绩可能有不同的基线水平。
     - **Level-1 残差** $ \epsilon_{ij} $：代表个体级别的随机误差，例如，同一学校内不同学生的成绩波动。

4. **随机截距与固定截距**：
   - **随机截距**是随机变量，它的实现值在不同的样本中会变化，这可能是因为群体本身的变化，或者是因为我们重新从数据生成模型中抽取随机截距。
   - **固定截距**则是在整个模型中保持不变的参数。选择随机截距还是固定截距取决于数据的特性和研究目的。

5. **多级或层次模型的基础**：
   - 这一章讨论的概念是所有多级或层次模型的基础。通过考虑最简单的多级模型案例，我们可以获得对模型参数估计和随机效应预测的一些见解。

6. **假设检验和置信区间**：
   - 我们还讨论了如何对方差组分模型进行假设检验和构建置信区间，这是统计分析中常用的方法来评估模型参数的不确定性。

#### 进一步阅读和练习

- 为了更深入地了解方差组分模型，推荐了一些文献，如Snijders和Bosker的书籍，以及Streiner, Norman和Cairney关于线性测量模型的书籍。

- 练习题覆盖了一系列应用，包括测量峰值呼气流速、心理困扰、作文评分、双胞胎的神经质、兄弟姐妹的出生体重、兄弟的头围，以及在社区和学校中的儿童成就等。

- 练习2.8是关于随机效应元分析的，这是本章没有讨论的一个主题。

#### 结论

通过这段介绍，我们了解到方差组分模型是一种强大的工具，它可以帮助我们理解数据中的变异性，并在多级或层次模型中进行参数估计和预测。希望这个解释有助于你更好地理解方差组分模型的基本概念。$

