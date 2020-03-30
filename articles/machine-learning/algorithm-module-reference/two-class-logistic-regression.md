---
title: 双类逻辑回归：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“双类逻辑回归”模块创建可用于预测两个（且只有两个）结果的逻辑回归模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7eb1ad00c3c947c3ed6d4ca450bddc0956a08d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455819"
---
# <a name="two-class-logistic-regression-module"></a>双类逻辑回归模块

本文介绍 Azure 机器学习设计器（预览版）中的一个模块。

使用此模块，可以创建可用于预测两个（且只有两个）结果的逻辑回归模型。 

逻辑回归是一种众所周知的统计方法，用于对许多类型的问题进行建模。 此算法是一种“监督式学习”** 方法；因此，你必须提供已包含结果的数据集来训练模型。  

### <a name="about-logistic-regression"></a>关于逻辑回归  

逻辑回归是统计学中著名的用于预测结果概率的方法，尤其是常用于分类任务。 该算法通过将数据拟合到逻辑函数来预测事件发生的概率。
  
在此模块中，分类算法针对二分变量或二元变量进行了优化。 如果需要对多个结果进行分类，请使用[多类逻辑回归](./multiclass-logistic-regression.md)模块。

##  <a name="how-to-configure"></a>配置方式  

若要训练此模型，必须提供一个包含标签或分类列的数据集。 因为此模块专用于解决双类问题，所以标签或分类列必须包含恰好两个值。 

例如，标签列可能是 [Voted]，并且可能的值为“Yes”或“No”。 或者，它可能是 [Credit Risk]，并且可能的值为“High”或“Low”。 
  
1.  将“双类逻辑回归”模块添加到管道****。  
  
2.  通过设置“创建训练器模式”选项来指定如何训练模型。****  
  
    -   **单一参数**：如果您知道如何配置模型，则可以提供一组特定的值作为参数。  

    -   **参数范围**：如果您不确定最佳参数，则可以使用[Tune 模型 Hyper 参数](tune-model-hyperparameters.md)模块找到最佳参数。 提供一些值范围，培训师会对多个设置组合进行遍舍，以确定生成最佳结果的值的组合。
  
3.  对于“优化容差”****，请指定在优化模型时要使用的阈值。 如果两次迭代之间的改进低于指定的阈值，则会认为算法收敛于某个解，并且训练停止。  
  
4.  对于“L1 正则化权重”**** 和“L2 正则化权重”****，请键入要用于正则化参数 L1 和 L2 的值。 对于这两个值，建议使用非零值。  
     *正则化*是一种通过惩罚具有极端系数值的模型来防止过度拟合的方法。 正则化的工作原理是将与系数值相关联的处罚添加到假设的错误。 因此，具有极端系数值的准确模型受到的处罚更大，但具有保守值的不准确的模型受到的处罚更小。  
  
     L1 和 L2 正则化具有不同的效果和用途。  
  
    -   L1 可用于稀疏模型，这在处理高维数据时非常有用。  
  
    -   与此相反，L2 正则化更适合用于非稀疏数据。  
  
     此算法支持 L1 和 L2 正则化值的线性组合：也就是说，如果 <code>x = L1</code> 且 <code>y = L2</code>，则 <code>ax + by = c</code> 定义正则化术语的线性跨度。  
  
    > [!NOTE]
    >  想了解 L1 和 L2 正则化的详细信息？ 以下本文讨论了 L1 和 L2 正则如何不同，以及它们如何影响模型拟合，以及逻辑回归和神经网络模型的代码示例：[用于机器学习的 L1 和 L2 正则](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > 已为逻辑回归模型设计了 L1 和 L2 术语的不同线性组合，例如[弹性网络正则化](https://wikipedia.org/wiki/Elastic_net_regularization)。 建议你参考这些组合来定义在你的模型中有效的线性组合。
      
5.  对于“用于 L-BFGS 的内存大小”****，请指定要用于 *L-BFGS* 优化的内存量。  
  
     L-BFGS 表示“limited memory Broyden-Fletcher-Goldfarb-Shanno”。 它是一种常用的参数估计优化算法。 此参数指示要存储以用于下一步计算的过去位置和渐变的数目。  
  
     此优化参数限制用来计算下一步和方向的内存量。 指定的内存越少，训练越快，但准确性越低。  
  
6.  对于“随机数种子”****，请键入一个整数值。 如果希望结果在同一管道的多个运行上可重现，则定义种子值非常重要。  
  
  
8. 向管道添加带标记的数据集，并连接[训练模块](module-reference.md)之一。  
  
    -   如果将“创建训练程序模式”设置为“单个参数”，请使用[训练模型](./train-model.md)模块。********  
  
9. 提交管道。  
  
## <a name="results"></a>结果

训练完成后：
 
  
+ 若要对新数据进行预测，请使用训练后的模型和新数据作为[评分模型](./score-model.md)模块的输入。 


## <a name="next-steps"></a>后续步骤

参阅 Azure 机器学习[可用的模块集](module-reference.md)。 