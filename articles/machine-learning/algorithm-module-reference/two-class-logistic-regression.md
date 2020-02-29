---
title: 双类逻辑回归：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中使用 "双类逻辑回归" 模块来创建逻辑回归模型，该模型可用于预测两个（且只有两个）的结果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: b1afd99a569de96e9075b4b5b6eff902abd8642e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77916755"
---
# <a name="two-class-logistic-regression-module"></a>双类逻辑回归模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块可创建一个逻辑回归模型，该模型可用于预测两个（且只有两个）结果。 

逻辑回归是一种众所周知的统计方法，用于建模多种类型的问题。 此算法是一种*监督的学习*方法; 因此，您必须提供已经包含用于定型模型的结果的数据集。  

### <a name="about-logistic-regression"></a>关于逻辑回归  

逻辑回归是用于预测结果概率的众所周知方法，特别是分类任务的常见方法。 该算法通过将数据用于逻辑函数来预测事件发生的概率。
  
在此模块中，分类算法针对二叉分或 binary 变量进行了优化。 如果需要分类多个结果，请使用 "[多类逻辑回归](./multiclass-logistic-regression.md)" 模块。

##  <a name="how-to-configure"></a>配置方式  

若要训练此模型，你必须提供包含标签或类列的数据集。 由于此模块适用于两类问题，因此标签或类列必须只包含两个值。 

例如，标签列可能是 [投票]，其中可能的值为 "Yes" 或 "No"。 或者，它可能是 [信用风险]，可能的值为 "高" 或 "低"。 
  
1.  将 "**双类逻辑回归**" 模块添加到管道。  
  
2.  通过设置 "**创建训练人员模式**" 选项，指定要如何定型模型。  
  
    -   **单个参数**：如果你知道要如何配置模型，则可以提供一组特定值作为参数。  

    -   **参数范围**：如果您不能确定最佳参数，可以使用 "[优化模型超参数](tune-model-hyperparameters.md)" 模块找到最佳参数。 提供一定范围的值，而讲师会循环访问设置的多个组合，以确定产生最佳结果的值的组合。
  
3.  对于**优化容错**，请指定优化模型时使用的阈值。 如果迭代间的改进低于指定的阈值，则将算法视为已在解决方案上聚合，而定型停止。  
  
4.  对于**L1 正则化权重**和**l2 正则化权重**，键入用于正则化参数 L1 和 l2 的值。 对于上述两者，建议使用非零值。  
     *正则化*是一种防止使用极端系数值的处罚模型的过度拟合的方法。 正则化的工作原理是将与系数值相关联的处罚添加到假设的错误。 因此，具有极端系数值的准确模型会因处罚更多的数据，但具有更保守值的更不准确的模型将因处罚更少。  
  
     L1 正则化和 L2 正则化具有不同的作用和用法。  
  
    -   L1 正则化可以应用于稀疏模型，在处理高维数据时，这很有用。  
  
    -   与此相反，L2 正则化更适用于非稀疏数据。  
  
     此算法支持 L1 和 L2 正则化值的线性组合：也就是说，如果 <code>x = L1</code> 和 <code>y = L2</code>，则 <code>ax + by = c</code> 会定义正则化术语的线性跨度。  
  
    > [!NOTE]
    >  想要了解有关 L1 和 L2 正则化的详细信息？ 以下文章介绍了 L1 和 L2 正则化的不同之处，以及它们如何影响模型管接头，以及用于逻辑回归和神经网络模型的代码示例： [L1 和 L2 正则化 for 机器学习](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > 为逻辑回归模型设计了 L1 和 L2 术语的不同线性组合：例如，[弹性网络正则化](https://wikipedia.org/wiki/Elastic_net_regularization)。 建议引用这些组合以定义在您的模型中有效的线性组合。
      
5.  对于**l-l-bfgs 的内存大小**，指定用于*l l-bfgs*优化的内存量。  
  
     L-L-BFGS 代表 "memory Broyden-Fletcher-Goldfarb-Shanno"。 它是一种优化算法，常用于参数估计。 此参数指示过去的位置和渐变来为下一个步骤的计算存储的数。  
  
     此优化参数限制用于计算下一个步骤和方向的内存量。 使用的内存越少，训练速度就越快，但准确性越低。  
  
6.  对于 "**随机数种子**"，请键入一个整数值。 如果希望结果在同一管道的多个运行上可重现，则定义种子值非常重要。  
  
  
8. 向管道添加标记的数据集，并连接其中一个[定型模块](module-reference.md)。  
  
    -   如果将 "**创建**训练人员模式" 设置为 "**单个参数**"，请使用 "[训练模型](./train-model.md)" 模块。  
  
9. 运行管道。  
  
## <a name="results"></a>结果

训练完成后：
 
  
+ 若要对新数据进行预测，请使用定型模型和新数据作为[评分模型](./score-model.md)模块的输入。 


## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 