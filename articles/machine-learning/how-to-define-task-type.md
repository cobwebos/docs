---
title: 为自动化机器学习运行定义机器学习任务
titleSuffix: Azure Machine Learning
description: 了解如何为自动化机器学习运行定义机器学习任务
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: cb2fbcda7f30db24b876a66df22071f14df74814
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653246"
---
# <a name="how-to-define-a-machine-learning-task"></a>如何定义机器学习任务 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍支持的机器学习任务，以及如何为自动化机器学习（自动化 ML）试验运行定义这些任务。


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a> 什么是机器学习任务？

机器学习任务通过创建预测模型来表示要解决的问题的类型。 自动化 ML 支持三种不同类型的任务，其中包括分类、回归和时序预测。

任务类型| 说明| 示例
----|----|----
分类 | 用于预测数据集中特定行的类别的任务。 | 在信用卡上进行的欺诈检测。 目标列将是“检测到的欺诈”  ，其类别为 *True* 或 *False*。 在这种情况下，我们将数据中的每一行分类为 true 或 false。
回归 | 预测连续数量输出的任务。 | 汽车费用基于其功能，目标列将为“价格”  。
预测 |用于在确定未来趋势的方向时做出明智估计的任务。| 未来 48 小时预测能源需求。 目标列将为“需求”  ，预测的值将用于显示能源需求中的模式。

在自动化和优化过程中，自动化机器学习支持以下算法。 用户不需要指定算法。

分类 | 回归 | 时序预测
-- |-- |--
[逻辑回归](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [弹性网络](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [弹性网络](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[渐进提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[渐进提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[渐进提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[决策树](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[决策树](https://scikit-learn.org/stable/modules/tree.html#regression)|[决策树](https://scikit-learn.org/stable/modules/tree.html#regression)
[K 近邻](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 近邻](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 近邻](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[线性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C 支持向量分类 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 分类器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[深度神经网络回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [深度神经网络回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 线性分类器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[线性回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[线性回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[朴素贝叶斯](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>设置任务类型
可以通过 SDK 或 Azure 机器学习工作室为自动化 ML 试验设置任务类型。

使用 `AutoMLConfig` 构造函数中的 `task` 参数来指定试验类型。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

可以在 Azure 机器学习工作室中将任务设置为自动化 ML 试验运行创建过程的一部分。 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![任务类型选择](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>后续步骤

+ 详细了解 Azure 机器学习中的[自动化机器学习](concept-automated-ml.md)。
+ 详细了解如何在 Azure 机器学习中[自动训练时序预测模型](how-to-auto-train-forecast.md)
+ 尝试[自动化机器学习分类](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)教程。
+ 尝试[自动化机器学习回归](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb)示例笔记本。

