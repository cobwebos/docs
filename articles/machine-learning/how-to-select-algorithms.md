---
title: 如何选择机器学习算法
titleSuffix: Azure Machine Learning
description: 如何在聚类、分类或回归实验中为受监督和无监督学习选择 Azure 机器学习算法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 03/05/2020
ms.openlocfilehash: e0482bac9569a834adf3e1cdef2b3f702980eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328657"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>如何为 Azure 机器学习选择算法

一个常见的问题是"我应该使用哪种机器学习算法？ 您选择的算法主要取决于数据科学方案的两个不同方面：

 - **您想如何处理您的数据？** 具体来说，您希望通过从过去的数据中学习来回答哪些业务问题？

 - **数据科学方案有哪些要求？** 具体而言，解决方案支持的准确性、训练时间、线性度、参数数和要素数是多少？

 ![选择算法的注意事项：您想知道什么？ 方案要求是什么？](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>业务场景和机器学习算法备忘表

[Azure 机器学习算法备忘表](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)可帮助您首先考虑：**您希望如何处理数据**？ 在机器学习算法备忘表上，查找要执行的任务，然后查找预测分析解决方案的[Azure 机器学习设计器](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri)算法。 

机器学习设计器提供了一个全面的算法组合，如[多类决策林](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri)、[推荐系统](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri)、[神经网络回归](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri)、[多类神经网络](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)和[K-手段聚类](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri)。 每种算法都旨在解决不同类型的机器学习问题。 有关完整列表，请参阅[机器学习设计器算法和模块参考](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)，以及有关每种算法的工作原理以及如何调整参数以优化算法的文档。

> [!NOTE]
> 要下载机器学习算法备忘单，请转到[Azure 机器学习算法备忘单](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)。
> 
> 

除了 Azure 机器学习算法备选表中的指南外，在为解决方案选择机器学习算法时，请记住其他要求。 以下是需要考虑的其他因素，如精度、训练时间、线性度、参数数和要素数。

## <a name="additional-requirements-for-a-data-science-scenario"></a>数据科学方案的其他要求

了解要对数据执行哪些操作后，需要确定解决方案的其他要求。 

针对以下要求做出选择和可能的权衡：

- 精确度
- 定型时间
- 线性
- 参数数目
- 特征数量

## <a name="accuracy"></a>精确度

机器学习的准确性衡量模型的有效性与真实结果与总案例的比例。 在机器学习设计器中，[评估模型模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri)计算一组行业标准评估指标。 您可以使用此模块测量经过训练的模型的准确性。

获得尽可能准确的答案并不总是必要的。 有时，近似值便已足够，具体取决于想要将其用于何处。 如果是这样的话，你也许能够通过坚持更多的近似方法来显著缩短您的处理时间。 近似方法也自然倾向于避免过度拟合。

可通过三种方式使用“评估模型”模块：

- 在培训数据上生成分数，以评估模型
- 在模型中生成评分，但将这些评分与保留的测试集中的评分进行比较
- 使用相同的数据集比较两个不同但相关的模型的评分

有关可用于评估机器学习模型准确性的指标和方法的完整列表，请参阅[评估模型模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri)。

## <a name="training-time"></a>定型时间

在监督学习中，培训意味着使用历史数据构建机器学习模型，以尽量减少错误。 算法之间定型模型所需的分钟数或小时数差异较大。 训练时间往往与准确性密切相关;一个通常伴随着另一个。 

此外，相较于其他算法，某些算法对数据点数目更敏感。 您可以选择特定算法，因为您有时间限制，尤其是在数据集较大时。

在机器学习设计器中，创建和使用机器学习模型通常是一个三步过程：

1.  通过选择特定类型的算法，然后定义其参数或超参数来配置模型。 

2.  提供已标记且数据与算法兼容的数据集。 将数据和模型连接到[训练模型模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri)。

3.  培训完成后，使用经过训练的模型与其中一个[评分模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri)一起对新数据进行预测。

## <a name="linearity"></a>线性

统计和机器学习中的线性化意味着数据集中的变量和常量之间存在线性关系。 例如，线性分类算法假定类可以通过直线（或其更高维模拟）分隔。

许多机器学习算法都使用线性。 在 Azure 机器学习设计器中，它们包括： 

- [多类逻辑回归](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [双类逻辑回归](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [支持矢量机](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

线性回归算法假定数据趋势遵循一条直线。 这种假设对一些问题并不坏，但对于另一些假设则降低了准确性。 尽管存在缺点，线性算法还是作为第一策略广受欢迎的。 它们往往算法简单且可快速定型。

![非线性类边界](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***非线性类边界***：*依靠线性分类算法会导致精度低。*

![非线性趋势数据](./media/how-to-select-algorithms/nonlinear-trend.png)

***具有非线性趋势的数据***：*使用线性回归方法会产生比必要更大的误差。*

## <a name="number-of-parameters"></a>参数数目

参数是数据科学家在设置算法时要旋转的旋钮。 它们是影响算法行为的数字，如容错或迭代次数，或算法行为变体之间的选项。 算法的训练时间和准确性有时对获得正确的设置非常敏感。 通常情况下，具有大量参数的算法需要进行最多的试用和错误，才能找到好的组合。

或者，机器学习设计器中有[Tune 模型超参数模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri)：此模块的目标是确定机器学习模型的最佳超参数。 该模块使用不同的设置组合来生成并测试多个模型。 它将比较所有模型的指标，以获取设置组合。 

虽然这是确保跨越参数空间的好方法，但训练模型所需的时间会随着参数数成倍增长而增加。 优点是通常情况下，参数较多说明算法具有更大的灵活性。 只要你能提供正确的参数设置组合，它通常能达到很好的精度。

## <a name="number-of-features"></a>特征数量

在机器学习中，一个功能是您尝试分析的现象的可量化变量。 对于某些类型的数据，相较于数据点的数量，特征的数量可能非常大。 这通常出现在遗传学或文本数据的情况下。 

大量的功能会拖累一些学习算法，使训练时间变得无法用。 [支持向量机](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)特别适用于具有大量功能的方案。 因此，它们已被广泛应用于从信息检索到文本和图像分类的许多应用。 支持向量计算机可用于分类和回归任务。

特征选择是指在给定指定输出的情况下对输入应用统计测试的过程。 目标是确定哪些列能够更准确地预测输出。 机器学习设计器中的[基于筛选器的功能选择模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri)提供了多种功能选择算法供您选择。 该模块包含“皮尔逊相关”和卡方值等相关性方法。

您还可以使用["排列要素重要性"模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri)计算数据集的一组要素重要性分数。 然后，您可以利用这些分数来帮助确定在模型中使用的最佳功能。


## <a name="next-steps"></a>后续步骤

 - [了解有关 Azure 机器学习设计器的更多](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - 有关 Azure 机器学习设计器中提供的所有机器学习算法的说明，请参阅[机器学习设计器算法和模块参考](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - 要探索深度学习、机器学习和 AI 之间的关系，请参阅[深度学习与机器学习](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
