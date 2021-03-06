---
title: 从线性模型到广义线性模型(2)——参数估计、假设检验
date: '2011-01-31T19:46:15+00:00'
author: 张缔香
categories:
  - 回归分析
  - 统计之都
  - 风险精算
tags:
  - IRWLS
  - 假设检验
  - 学习经历
  - 广义线性模型
slug: how-does-glm-generalize-lm-fit-and-test
---

# 1.GLM参数估计——极大似然法

为了理论上简化，这里把GLM的分布限定在指数分布族。事实上，实际应用中使用最多的分布就是指数分布族，所以这样的简化可以节省很多理论上的冗长论述，也不会限制实际应用。
  
如前文如述，指数分布族的概率密度函数可以统一地写为：

<p style="text-align: center;">
  $f_Y(y;\theta,\Psi)=exp[(y\theta &#8211; b(\theta))/{\Psi} + c(y;\Psi)]$
</p>

这里为了在模型中体现散布参数(dispersion parameter)$\phi$，把上述密度函数中的$\Psi$记做
  
$\Psi=a\_i(\phi)={\phi}/w\_i$
  
从而响应变量的单个观测值的(加权)对数似然函数可以表示为：

<p style="text-align: center;">
  $logL({\theta}_i,\phi;y_i)=w_i[(y_i{\theta}_i-b({\theta}_i))/{\phi}]+c(y_i,\phi)$
</p>

再结合观测值之间的独立性，全体观测值的对数似然函数可记做：$\sum\_i logL({\theta}\_i,\phi;y_i)$
  
一般情况下最大化上述的对数似然函数很难找到解析解(正态分布是特例之一)，因而必须使用数值方法求解。McCullagh和Nelder(1989)证明了使用Newton-Raphson方法，结合Fisher scoring算法，上述对数似然函数的最大化等价于连续迭代的加权最小二乘法(iteratively weighted least squares, or IRWLS)。

广义线性模型的IRWLS算法如下：
  
1.设置线性估计量和响应变量的均值的初始估计值： $\hat {\eta}\_0$和$\hat {\mu}\_0$
  
这里$\hat {\mu}\_0$是根据经验或是专家意见等信息对$\mu=E(Y)$的一个估计值，而$\hat {\eta}\_0$可以利用模型建立时选用的联接函数来获得，即$\hat {\eta}\_0=g(\hat {\mu}\_0)$。这一函数关系也用于计算步骤2和3中$\eta$对$\mu$一阶导数。
  
2.构造调整的因变量(adjusted dependent variable)：$z\_0=\hat {\eta}\_0+(y-{\hat \mu}\_0){d\eta \over d\mu}|\_{\hat {\eta}_0}$
  
3.构造权重：$w^{-1}\_0={({d\eta \over d\mu})}^2|{\hat {\eta}\_0V(\hat {\mu}_0)}$
  
这里$V(\hat {\mu}\_0)$是利用方差函数(variance function)和$\hat {\mu}\_0$构造的$Var(Y)$的估计值。
  
4.利用步骤2和3构造的调整的因变量和权重，拟合普通线性模型(ordinary linear model)，预测/拟合(predict)新的线性估计量和均值： $\hat {\eta}\_1$和$\hat {\mu}\_1$
  
5.重复步骤2-4直到收敛(满足一定的迭代步数或是精度要求)。
  
此时得到的模型就是极大似然估计方法下的广义线性模型。IRWLS的算法思路也从另一个方面说明了广义线性模型是普通线性模型的推广。在广义线性模型的实际应用中，IRWLS算法是最常用的极大似然估计求解方法。对于特殊的案例，也有其他的特殊的参数估计方法。比如对于在精算学科中最常用的列联表(contigency table)数据或案例就有Bailey-Simon法、边际总和法(marginal totals)、最小二乘法(least squares)、直接法(direct method)等。

# 2.假设检验

## 2.1 空模型和全模型

一个极端的情况，所有自变量$x_i$对于响应变量$Y$都没有影响，也即是为所有的响应变量$Y$拟合一个共同的均值，即只有一个参数。这样的模型称为空模型(null model)。对于普通线性模型(正态分布下的GLM)而言，空模型的具体形式就是$y=\mu + \epsilon$。对于特殊的数据或案例类型，可能存在着其他的限制条件(constraints)从而空模型的参数个数大于1。比如非寿险精算中经常用到的列联表(contigency table)数据，其空模型就可能包含了行号、列号、对角线序号等限制。

相反的一个极端情况就是，所有自变量$x_i$的每一个观测值或称为数据的样本点(data points)对于响应变量$Y$都有影响，这样的模型称为全模型(full or saturated model)。一般可以通过构造阶数足够高的多项式或者把所有的量化观测值(quantitative)视为质化观测值(qualitive)，并且引入适当数量的交叉项(interactions)来构造全模型。

统计建模的目的之一就是把样本数据划分为随机成分和系统成分两大部分。在这一点上，空模型认为响应变量的变动(variation)完全由随机性(random variation)造成，而全模型则认为响应变量的变动完全来自于系统成分(systematic)。一个直观地理解就是全模型是在现有的数据或样本的条件下，针对某一种分布所能拟合的最优模型，因而可以做为检验目标模型拟合优度的一个标准(measure)。

## 2.2 偏差(Deviance)

如果把全模型的对数似然函数记为$l(y,\phi|y)$，把目标模型的对数似然函数记为$l({\hat {\mu}},\phi|y)$，那么目标模型与全模型在拟合优度上的偏离的定义可写成$2(l(y,\phi|y)-l({\hat {\mu}},\phi|y))$。再结合观测值的独立性假设和指数散布族的假设，那么上述偏离的定义可以简化为：

<p style="text-align: center;">
  $\sum_i 2w_i(y_i({\hat {\theta}_i} &#8211; {\tilde {\theta}_i}) &#8211; b({\tilde {\theta}_i}) + b({\hat {\theta}_i})) /{\phi}$
</p>

其中$a\_i(\phi)={\phi}/w\_i$，$\tilde {\theta}$是全模型下的参数估计值，$\hat {\theta}$是目标模型下的参数估计值。如果把上式写成$D(y,\hat {\mu})/{\phi}$，那么$D(y,\hat {\mu})$称为偏差(Deviance)，$D(y,\hat {\mu})/{\phi}$则称为标准化偏差(scaled deviace)。
  
此外，皮尔逊卡方统计量(Pearson&#8217;s chi-square statistics)：

<p style="text-align: center;">
  $X^2={\sum_i (y_i &#8211; {{\hat \mu}_i})^2 \over Var({\hat {\mu}}_i)}$
</p>

也是衡量模型偏离程度(discrepancy)的统计量之一，在一些场合可以做为偏差的替代选择。

## 2.3 拟合优度检验

广义线性模型的假设检验可以分为两种：一是检验目标模型相对于数据或预测值的拟合有效性的检验(goodness of fit test)；另外一种则是对“大”模型以及对“大”模型的参数施加一定的线性约束(linear restrictions)之后得到的“小”模型之间的拟合优度比较检验。直观上的理解就是，“大”模型具有更多的参数，即从参数的线性约束总可把一个或多个参数用其他参数的线性组合来表示，然后代入“大”模型，从而参数的个数减少，派生出所谓的“小”模型，也就是说“大”和“小”并非任意的，而是具有一种派生关系(nested models)。如果把全模型认为是“大”模型，而目标模型是“小”模型，那么上述两种检验的本质是相同的。因而假设检验的零假设(null hypothsis)可以统一且直观地设定为：“小”模型(目标模型)是正确的模型。

如果把大模型记做$\Omega$，把小模型记做$\omega$，其标准化偏差之差记做$D\_{\omega} &#8211; D\_{\Omega}$，其自由度之差记做$df\_{\omega}-df\_{\Omega}$，则构造如下的统计量：${(D\_{\omega} &#8211; D\_{\Omega})/(df\_{\omega}-df\_{\Omega})} \over {\phi}$。

当$\phi$是已知常数时，比如泊松和二项分布的情况下$\phi=1$，上述统计量在零假设下渐近地(asymptotically)服从卡方分布(正态分布时正好是卡方分布)。当$\phi$未知时，通常需要用估计值代替。最常用的估计值是$\hat {\phi}=X^2/(n-p)$这里n是数据中观测值的数量，p是目标模型的参数个数。此时上述的统计量在零假设下近似地(approximately)服从F分布(正态分布时严格服从F分布)。注意上述两种情况下，渐近和近似的区别。

对于某一个参数，可以使用其估计值的标准误(standard error)来构造一个z统计量来检验其显著性，即$z=\hat {\beta}/se(\hat {\beta})$。在零假设下，z统计量在普通线性模型，也就是正态分布下的广义线性模型中就是我们熟知的t统计量，严格服从t分布。在其他分布下的广义线性模型中，渐近地服从正态分布。z检验也称为Wald检验，在广义线性模型中效果不如上述的偏差检验，因而较少使用。
