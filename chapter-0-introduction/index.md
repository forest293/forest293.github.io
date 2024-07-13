# [Multilevel  Longitudinal Models] Introduction


Level-1 units within the same level-2 unit or cluster tend to be more similar to each other than to units in other clusters. One reason for this is that units do not end up in the same cluster by chance but through some mechanism that may be related to their characteristics. For instance, which schools children go to is influenced by their family background through place of residence or parental choice, so children within a school already have something in common from the first day of school. Perhaps more importantly, children within a school are subsequently affected by their peers, teachers, and school policies, making them even more similar. Such within-school similarity or dependence will be particularly apparent if there are large between-school differences in terms of children’s backgrounds, school environments, and school policies. In the same vein, siblings are similar at birth because they have the same parents and therefore share genes, and they subsequently become even more similar by being raised in the same family, where they share a common environment and experiences.

**Within-cluster dependence violates the assumption of ordinary regression models that responses are conditionally independent given the covariates (the residuals are independent). Consequently, ordinary regression produces incorrect standard errors, a problem that can be overcome by using multilevel models.**

More importantly, **multilevel modeling allows us to disentangle processes operating at different levels, both by including explanatory variables at the different levels and by attributing unexplained variability to the different levels.** 

**One important challenge in multilevel modeling is to distinguish within- and between-cluster effects of lower-level covariates.** For instance, for students nested in schools, an important student-level explanatory variable for achievement is socioeconomic status (SES). In addition to the effect of own SES, the school-average SES can be strongly associated with achievement, both through peer effects and because low-SES children tend to end up in worse schools.

Policy interventions often occur at the level of institutions, and it is important to understand how such higher-level variables affect the response variable. In the school setting, typical examples would be new curricula or different kinds of teacher professional development. The effect of such interventions on achievement is usually the primary concern, but it is also important to investigate the differential effects for different student subpopulations. For instance, a new science curriculum may be particularly beneficial for girls and hence reduce the gender gap in science achievement. If it does, then there is a cross-level interaction between the level-1 variable gender and the level-2 variable curriculum.

It will generally not be possible to explain all within-school and between-school variability in achievement. Quantifying the amount of unexplained variability at the different levels can be of interest in its own right, and sometimes multilevel models without explanatory variables are used to see how much the higher-level membership matters. For instance, achievement may vary less between schools in Scandinavia than in the U.S., and this tells us something about the societies and schooling systems.

**Repeated observations on the same units are also clustered data**, for instance, longitudinal or panel data on children’s weight, mothers’ postnatal depression, taxpayers’ tax liability, employees’ wages and union membership, and the investments or number of patents of firms. **Although longitudinal data are quite different from clustered cross-sectional data, the same kinds of questions arise and the same kinds of models can be used to address them.** An example is data on children’s weight, where interest is in growth, the relationship between weight and the level-1 variable time. Level-2 explanatory variables include gender and ethnicity. Cross-level interactions between these variables and time represent differences in growth rates between groups. Clearly, not all differences in initial weight and growth rate can be explained by gender and ethnicity, and multilevel models can quantify how much variability remains between children within gender and ethnic groups.

**Disentangling the within- and between-effects of level-1 (or time-varying) covariates can be important for causal inference.** For instance, evaluation of policies, such as bans of smoking in public places, typically rely on observational data where policies were implemented for some of the clusters, such as states, at different times during the longitudinal study. When considering the effect of the legislation on cigarette consumption, it is important to estimate the within-state effects, to control for any state-level variables that affect legislation as well as cigarette consumption. Within-state effects could of course be due to general time trends, which can be controlled by considering the mean difference in within-state differences for states that did and did not implement the policy.

这段话讨论了多层次模型（multilevel models）在统计分析中的应用，特别是在处理集群数据（clustered data）时的重要性。我们将分几个步骤来讲解：

### 第一步：理解集群数据和多层次结构
- **集群数据**：指的是数据点按照某种自然方式被分成不同的组或集群。例如，学生（level-1 units）被分在不同的学校（level-2 units）中。
- **多层次结构**：数据存在嵌套关系，即较低层级单位（如学生）位于较高层级单位（如学校）之内。

### 第二步：集群内部相似性的原因
- **非随机分配**：单元（如学生）不是随机分配到集群（如学校）的，而是通过与他们特征相关的机制。
- **家庭背景和选择**：学生的居住地或家长选择影响他们上哪所学校，导致同一学校的学生从一开始就有共同点。
- **学校环境的影响**：学生在学校内受同学、老师和学校政策的影响，进一步增加了他们的相似性。

### 第三步：违反普通回归模型的假设
- **条件独立性**：普通回归模型假设给定协变量后，响应是条件独立的，即残差是独立的。
- **集群内部依赖性**：集群内部的相似性或依赖性违反了这一假设，导致普通回归模型产生不正确的标准误。

### 第四步：多层次模型的应用
- **解决依赖性问题**：多层次模型可以克服普通回归模型的局限性，适用于处理集群数据。
- **不同层级的过程分离**：多层次模型允许我们通过在不同层级包含解释变量，并将未解释的变异归因于不同层级，来分离在不同层级上起作用的过程。

### 第五步：区分集群内外效应
- **学生成就的例子**：学生的经济社会地位（SES）是影响其成就的重要学生层级解释变量。除了自身的SES，学校平均SES也与成就强烈相关，这是通过同伴效应以及低SES儿童倾向于进入较差学校实现的。

### 第六步：政策干预和高阶层级变量的影响
- **机构层面的干预**：政策干预通常发生在机构层面，理解这些高阶层级变量如何影响响应变量（如学生成就）是很重要的。
- **不同学生亚群体的效应差异**：例如，新的科学课程可能对女孩特别有益，从而减少科学成就中的性别差距。

### 第七步：量化未解释的变异
- **多层次模型的应用**：即使没有解释变量，也可以使用多层次模型来量化不同层级上未解释变异的数量，这有助于我们了解社会和教育系统的特点。

### 第八步：重复观测和纵向数据
- **纵向数据的集群特性**：例如，对儿童体重、母亲的产后抑郁、纳税人的税收责任等的重复观测也是集群数据。
- **多层次模型的应用**：尽管纵向数据与集群横截面数据非常不同，但可以应用相同类型的模型来解决类似问题。

### 第九步：因果推断和政策评估
- **政策效果的评估**：在评估如公共场所禁烟政策这类通常依赖于观察数据的政策时，重要的是估计集群内部效应，并控制影响立法和香烟消费的集群层级变量。

### 总结
多层次模型为我们提供了一种强大的工具，用于分析和理解嵌套数据结构中的复杂关系，允许我们考虑和解释不同层级上的变量对响应变量的影响，以及这些变量之间可能存在的交互作用。通过这种方式，我们可以更准确地评估政策的影响，理解不同群体间的差异，并为决策提供更深入的洞见。

