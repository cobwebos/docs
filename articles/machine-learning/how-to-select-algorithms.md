---
title: 如何选择机器学习算法
titleSuffix: Azure Machine Learning
description: 如何在聚类、分类或回归试验中选择监督式和非监督式学习的 Azure 机器学习算法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 05/07/2020
ms.openlocfilehash: ad384896301e809940f6e99df2f5562cfdb6a6fe
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927575"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>如何选择 Azure 机器学习的算法

一个常见的问题是“我应该使用哪种机器学习算法？” 所选的算法主要取决于数据科学方案的两个不同方面：

 - **要将数据用于何种用途？** 具体而言，从以往的数据中学习信息是要解答哪个业务问题？

 - **数据科学方案的要求是什么？** 具体而言，解决方案支持的准确度、训练时间、线性度、参数数目和特征数目是多少？

 ![选择算法时的考虑因素：想要知道什么？ 方案要求是什么？](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>业务方案和机器学习算法速查表

[Azure 机器学习算法速查表](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)可帮助你解决首要考虑因素：**要将数据用于何种用途**？ 在机器学习算法速查表中，查找想要执行的任务，然后找到适用于预测分析解决方案的 [Azure 机器学习设计器](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri)算法。 

机器学习设计器提供全面的算法阵容，例如[多类决策林](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri)、[推荐系统](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri)、[神经网络回归](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri)、[多类神经网络](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)和 [K 平均值聚类](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri)。 每种算法旨在用于解决一种不同类型的机器学习问题。 有关完整列表，以及有关每种算法的工作原理与如何优化参数以优化算法的文档，请参阅[机器学习设计器算法和模块参考](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)。

> [!NOTE]
> 若要下载机器学习算法速查表，请转到 [Azure 机器学习算法速查表](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)。
> 
> 

除了遵循 Azure 机器学习算法速查表中的指导以外，在为解决方案选择机器学习算法时，还应该记住其他要求。 下面是要考虑的其他因素，例如准确度、训练时间、线性度、参数数目和特征数目。

## <a name="comparison-of-machine-learning-algorithms"></a>机器学习算法的比较

一些学习算法会对数据的结构或期望的结果做出特定假设。 如果找到符合需求的算法，它可以提供更有用的结果、更准确的预测或更快的定型时间。

下表总结了分类、回归和群集系列中算法的一些最重要的特征：

| **算法** | **准确性** | **定型时间** | **线性** | **参数** | **说明** |
| --- |:---:|:---:|:---:|:---:| --- |
| **分类系列** | | | | | |
| [双类逻辑回归](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri) |好  |快速 |是 |4 | |
| [双类决策林](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-decision-forest?WT.mc_id=docs-article-lazzeri) |很好 |中等 |否 |5 |显示较慢的评分时间。 建议不要处理“一对多”多类分类，因为在累积树预测中由于踏板锁定导致的计分时间较慢 |
| [双类提升决策树](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-boosted-decision-tree?WT.mc_id=docs-article-lazzeri) |很好 |中等 |否 |6 |内存占用量大 |
| [双类神经网络](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-neural-network?WT.mc_id=docs-article-lazzeri) |好 |中等 |否 |8 | |
| [双类平均感知器](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-averaged-perceptron?WT.mc_id=docs-article-lazzeri) |好 |中等 |是 |4 | |
| [双类支持向量机](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) |好 |快速 |是 |5 |适用于大型特征集 |
| [多类逻辑回归](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri) |好 |快速 |是 |4 | |
| [多类决策林](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri) |很好 |中等 |否 |5 |显示较慢的评分时间 |
| [多类提升决策树](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-boosted-decision-tree?WT.mc_id=docs-article-lazzeri) |很好 |中等 |否 |6 | 降低了准确性，同时降低了覆盖范围 |
| [多类神经网络](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri) |好 |中等 |否 |8 | |
| [一对多多类](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/one-vs-all-multiclass?WT.mc_id=docs-article-lazzeri) | - | - | - | - |查看所选双类方法的属性 |
| **回归系列** | | | | | |
| [线性回归](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/linear-regression?WT.mc_id=docs-article-lazzeri) |好 |快速 |是 |4 | |
| [决策林回归](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/decision-forest-regression?WT.mc_id=docs-article-lazzeri)|很好 |中等 |否 |5 | |
| [提升决策树回归](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/boosted-decision-tree-regression?WT.mc_id=docs-article-lazzeri) |很好 |中等 |否 |6 |内存占用量大 |
| [神经网络回归](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri) |好 |中等 |否 |8 | |
| **群集系列** | | | | | |
| [K 平均值聚类](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri) |很好 |中等 |是 |8 |聚类算法 |

## <a name="requirements-for-a-data-science-scenario"></a>数据科学方案的要求

一旦知道要对数据执行哪些操作，就需要确定解决方案的其他要求。 

出于以下要求做出选择和可能的权衡：

- 精确度
- 定型时间
- 线性
- 参数数目
- 特征数量

## <a name="accuracy"></a>精确度

机器学习的准确性衡量模型在总体事例结果比例上的有效性。 在机器学习设计器中，"[评估模型" 模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri)计算一组符合行业标准的评估指标。 您可以使用此模块来度量定型模型的准确性。

获取最准确的答案并非总是必需的。 有时，近似值便已足够，具体取决于想要将其用于何处。 如果是这种情况，您可以通过使用更接近的方法来显著地缩短您的处理时间。 大致的方法也通常会避免过度拟合。

可通过三种方式使用“评估模型”模块：

- 针对定型数据生成分数，以便对模型进行评估
- 在模型中生成评分，但将这些评分与保留的测试集中的评分进行比较
- 使用相同的数据集比较两个不同但相关的模型的评分

有关可用于评估机器学习模型准确性的指标和方法的完整列表，请参阅[评估模型模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri)。

## <a name="training-time"></a>定型时间

在监督式学习中，定型意味着使用历史数据生成一个最小化错误的机器学习模型。 算法之间定型模型所需的分钟数或小时数差异较大。 训练时间通常与准确性密切相关;其中一项通常伴随另一个。 

此外，相较于其他算法，某些算法对数据点数目更敏感。 您可以选择特定的算法，因为您有时间限制，尤其是在数据集很大的情况下。

在机器学习设计器中，创建和使用机器学习模型的过程通常分为三个步骤：

1.  通过选择特定类型的算法，然后定义其参数或超参数，配置模型。 

2.  提供一个数据集，该数据集带有标记并具有与算法兼容的数据。 将数据和模型连接到[定型模型模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri)。

3.  训练完成后，使用训练的模型和一个[计分模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri)来对新数据进行预测。

## <a name="linearity"></a>线性

统计和机器学习中的线性意味着数据集中的变量和常数之间存在线性关系。 例如，线性分类算法假设可以用直线（或其更高的模拟）分隔类。

许多机器学习算法都使用线性。 在 Azure 机器学习设计器中，它们包括： 

- [多类逻辑回归](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [双类逻辑回归](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [支持矢量计算机](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

线性回归算法假定数据趋势遵循一条直线。 对于某些问题而言，此假设并不不错，但对于其他一些问题，它会降低准确性。 尽管它们有缺点，但线性算法作为第一种策略。 它们往往算法简单且可快速定型。

![非线性类边界](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***非线性类边界***：*依赖线性分类算法会导致低准确性。*

![非线性趋势数据](./media/how-to-select-algorithms/nonlinear-trend.png)

***具有非线性趋势的数据***：*使用线性回归方法会产生比所需的更大的错误。*

## <a name="number-of-parameters"></a>参数数目

参数是数据科学家在设置算法时要旋转的旋钮。 它们是影响算法行为的数字，如容错或迭代次数，或算法行为方式的变体之间的选项。 算法的定型时间和准确性有时会很敏感，只会获得适当的设置。 通常情况下，具有大量参数的算法需要进行最多的试用和错误，才能找到好的组合。

另外，机器学习设计器中还有[优化模型超参数模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri)：此模块的目标是确定机器学习模型的最佳超参数。 该模块使用不同的设置组合来生成并测试多个模型。 它将比较所有模型的指标，以获取设置组合。 

虽然这是确保已跨越参数空间的好方法，但定型模型所需的时间与参数的数量呈指数级增长。 优点是通常情况下，参数较多说明算法具有更大的灵活性。 只要你能提供正确的参数设置组合，它通常能达到很好的精度。

## <a name="number-of-features"></a>特征数量

在机器学习中，一项功能是您尝试分析的一种可量化的现象。 对于某些类型的数据，相较于数据点的数量，特征的数量可能非常大。 这通常出现在遗传学或文本数据的情况下。 

大量功能可以会阻碍一些学习算法，使定型时间特别长。 [支持矢量计算机](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)特别适用于具有大量功能的方案。 出于此原因，许多应用程序中都使用了这些应用程序，从信息检索到文本和图像分类。 支持矢量机可用于分类和回归任务。

功能选择是指在给定指定输出的情况下将统计测试应用于输入的过程。 目标是确定哪些列能够更准确地预测输出。 机器学习设计器中的 "[基于筛选器的特征选择" 模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri)提供了多个可供选择的特征选择算法。 该模块包含“皮尔逊相关”和卡方值等相关性方法。

你还可以使用[排列功能重要性模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri)来计算数据集的一组功能重要性分数。 然后，你可以利用这些分数帮助你确定要在模型中使用的最佳功能。

## <a name="next-steps"></a>后续步骤

 - [了解 Azure 机器学习设计器的详细信息](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri)
 - 有关 Azure 机器学习设计器中可用的所有机器学习算法的说明，请参阅[机器学习设计器算法和模块参考](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - 若要浏览深度学习、机器学习和 AI 之间的关系，请参阅[深度学习与机器学习](https://docs.microsoft.com/azure/machine-learning/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
