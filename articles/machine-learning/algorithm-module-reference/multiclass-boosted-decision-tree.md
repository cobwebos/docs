---
title: 多类提升决策树：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“多类提升决策树”模块来创建使用标记数据的分类器。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: cfe35f81526a729092edf522f693ccd18494d1ec
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137818"
---
# <a name="multiclass-boosted-decision-tree"></a>多类提升决策树

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块，可以根据提升决策树算法创建机器学习模型。

提升决策树是一种集成学习方法，在此方法中，第二个树将针对第一个树的误差进行纠正，第三个树将针对第一个和第二个树的误差进行纠正，依此类推。 预测基于树的集合。

## <a name="how-to-configure"></a>配置方式 

此模块创建一个未训练的分类模型。 由于分类是一种监督式学习方法，所以，你需要一个标记的数据集，其中包含一个标签列，该列在所有行中都有一个值  。

可以使用[训练模型](././train-model.md)来训练这种类型的模型。 

1.  将“多类提升决策树”模块添加到管道  。

1.  通过设置“创建训练程序模式”选项，指定所希望的模型训练方式  。

    + **单个参数**：如果知道自己想要如何配置模型，可以提供一组特定的值作为参数。
    
    + **参数范围**：如果你不确定最佳参数，并想要运行参数扫描，请选择此选项。 选择要循环访问的值的范围，[调整模型超参数](tune-model-hyperparameters.md)循环访问提供的设置的所有可能组合，以确定产生最佳结果的超参数。  

1. “每个树的最大叶数”限制可在任何树中创建的终端节点（叶）的最大数目****。
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. “每个叶节点的最少样本数”指示在树中创建任何终端节点（叶）所需的事例数****。  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. “学习速率”定义学习时的步幅****。 请输入介于 0 到 1 之间的数字。

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. “构造的树数”指示要在集成中创建的决策树的总数****。 通过创建更多决策树，你可能会获得更好的覆盖范围，但训练时间将会增加。

1. “随机数种子”可以选择性地设置非负整数作为随机种子值****。 指定种子可以确保具有相同数据和参数的运行之间的可再现性。  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

1. 训练模型：

    + 如果将“创建训练程序模式”设置为“单个参数”，请连接标记的数据集和[训练模型](train-model.md)模块********。  
  
    + 如果将 "**创建训练培训模式**" 设置为 "**参数范围**"，请使用[优化模型超参数](tune-model-hyperparameters.md)连接标记的数据集并为模型定型。  
  
    > [!NOTE]
    > 
    > 如果将参数范围传递到[定型模型](train-model.md)，则它仅使用单个参数列表中的默认值。  
    > 
    > 如果将一组参数值传递到[优化模型超参数](tune-model-hyperparameters.md)模块，则在它需要每个参数的一系列设置时，它将忽略这些值，并使用学习器的默认值。  
    > 
    > 如果选择 "**参数范围**" 选项并为任何参数输入单个值，则会在整个扫描中使用您指定的单个值，即使其他参数在一系列值中发生变化。

## <a name="next-steps"></a>后续步骤

参阅 Azure 机器学习[可用的模块集](module-reference.md)。 
