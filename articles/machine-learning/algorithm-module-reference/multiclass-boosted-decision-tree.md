---
title: 多类提升决策树：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "多类提升决策树" 模块来创建使用标记数据的分类器。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920053"
---
# <a name="multiclass-boosted-decision-tree"></a>多类提升决策树

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块来创建基于提升决策树算法的机器学习模型。

提升决策树是一种系综学习方法，在此方法中，第二个树将纠正第一个树的错误，第三个树更正第一个和第二个树的错误，等等。 预测基于系综的树。

## <a name="how-to-configure"></a>配置方式 

此模块创建一个未训练的分类模型。 由于分类是监督式学习方法，因此您需要一个带标签的*数据集*，其中包含一个标签列，其中包含所有行的值。

您可以使用[训练模型](././train-model.md)来训练这种类型的模型。 

1.  向管道添加 "**多类提升决策树**" 模块。

1.  通过设置 "**创建培训人员模式**" 选项指定您希望模型的定型方式。

    + **单个参数**：如果你知道要如何配置模型，则可以提供一组特定值作为参数。
    
    + **参数范围**：如果你不确定最佳参数，并想要运行参数扫描，请选择此选项。 选择要循环访问的值的范围，[调整模型超参数](tune-model-hyperparameters.md)循环访问提供的设置的所有可能组合，以确定产生最佳结果的超参数。  

1. **每个树的最大叶数**限制可以在任何树中创建的最大终端节点（叶）数。
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **每个叶节点的最小样本数**指示在树中创建任何终端节点（叶）所需的事例数。  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **学习速率**定义学习时的步长大小。 输入介于0和1之间的数字。

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **构造的树数**指示在系综中创建的决策树的总数。 通过创建多个决策树，你可能获得更好的覆盖范围，但训练时间会增加。

1. **随机数字种子**可以选择设置非负整数作为随机种子值。 指定种子可确保跨运行的可再现性具有相同的数据和参数。  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> 如果将 "**创建训练人员模式**" 设置为 "**单个参数**"，则连接标记的数据集和[训练模型](./train-model.md)模块。

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 
