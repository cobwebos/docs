---
title: 泊松回归：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用泊松回归模块创建泊松回归模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: a1a09357c7f80d4af0198a33a2e0007782ef232f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905259"
---
# <a name="poisson-regression"></a>泊松回归

本文介绍 Azure 机器学习设计器中的模块。

使用此模块可以在管道中创建泊松回归模型。 泊松回归用于预测数值（通常为计数）。 因此，仅在要预测的值满足以下条件时，才应使用此模块创建回归模型：

- 响应变量呈[泊松分布](https://en.wikipedia.org/wiki/Poisson_distribution)。  

- 计数不能为负值。 如果你尝试对负标签使用此方法，则它将彻底失败。

- 泊松分布是一种离散分布。因此，对非整数使用此方法是没有意义的。

> [!TIP]
> 如果你的目标不是计数，泊松回归可能并不是适当的方法。 试用[设计器中的其他回归模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference#machine-learning-algorithms)。 

设置回归方法后，必须使用包含要预测的值示例的数据集来训练模型。 然后，可以使用训练后的模型进行预测。

## <a name="more-about-poisson-regression"></a>有关泊松回归的详细信息

泊松回归是一种特殊的回归分析，通常用于模型计数。 例如，泊松回归可用于以下方案：

- 为航班关联的寒冷天气次数建模

- 估计事件期间的紧急服务呼叫次数

- 预测促销后的客户查询数

- 创建列联表

由于响应变量呈泊松分布，因此与最小二乘回归等相比，该模型对数据及其概率分布做出了不同的假设。 因此，对泊松模型的解释应不同于其他回归模型。

## <a name="how-to-configure-poisson-regression"></a>如何配置泊松回归

1. 在设计器中将 **泊松回归** 模块添加到管道。 可以在“机器学习算法”下的“回归”类别中找到此模块 。

2. 添加包含正确类型的训练数据的数据集。 

    我们建议你先使用[规范化数据](normalize-data.md)来规范输入数据集，然后将其用于训练回归量。

3. 在“泊松回归”模块的右侧窗格中，设置“创建训练程序模式”选项，以指定模型的训练方式 。  
  
    - **单个参数**：如果知道自己想要如何配置模型，请提供一组特定的值作为参数。
  
    - **参数范围**：如果不确定最佳参数，请使用[优化模型超参数](tune-model-hyperparameters.md)模块进行参数扫描。 训练程序遍历指定的多个值，以找到最佳配置。
  
4. 优化容错：键入一个值，用于定义优化期间的容许区间。 值越小，拟合就会越慢，但准确性越高。

5. L1 正则化权重和 L2 正则化权重 ：键入要用于 L1 和 L2 正则化的值。 **“正则化”将约束添加到有关独立于训练数据的模型各个方面的算法。 正则化通常也用来避免过度拟合。 

    - L1 正则化很有用，如果目标是能够尽可能为稀疏的模型。

        L1 正则化是通过减去从学员尝试最大程度减少丢失表达式的权重向量的 L1 权重。 L1 范数是 L0 范数的适当近似值，即数目的非零坐标进度的近似值。

    - L2 正则化可防止权重向量中的任何单个坐标在数量级上增长过多。 如果目标是获得整体权重较小的模型，则 L2 正则化很有用。

    在此模块中，你可以应用 L1 和 L2 正则化的组合。 通过组合 L1 和 L2 正则化，可以对参数值的大小施加惩罚。 学员尝试最大程度减少产生负面影响降至最低丢失的一种折中。

    有关 L1 和 L2 正则化的详细讨论，请参阅[机器学习的 L1 和 L2 正则化](https://msdn.microsoft.com/magazine/dn904675.aspx)。

6. L-BFGS 的内存大小：指定要为模型拟合和优化保留的内存量。

     L-BFGS 是基于 Broyden-Fletcher-Goldfarb-Shanno (BFGS) 算法的一种特定优化方法。 该方法使用有限的内存 (L) 来计算下一步的方向。

     通过更改此参数，可以影响为下一步的计算而存储的先前位置数和梯度数。

7. 将训练数据集和未训练的模型连接到其中一个训练模块： 

    - 如果将“创建训练程序模式”设置为“单个参数”，请使用[训练模型](train-model.md)模块。********

    - 如果将“创建训练程序模式”设置为“参数范围”，请使用[优化模型超参数](tune-model-hyperparameters.md)模块 。

    > [!WARNING]
    > 
    > - 如果将参数范围传递给[训练模型](train-model.md)模块，则它只使用参数范围列表中的第一个值。
    > 
    > - 如果将一组参数值传递给[优化模型超参数](tune-model-hyperparameters.md)模块，则当它期望每个参数有一系列设置时，它会忽略这些值，并为学习器使用默认值。
    > 
    > - 如果选择“参数范围”选项并为任何参数输入单个值，则整个整理过程中都会使用你指定的单个值，即使其他参数的值发生一系列更改。

8.  提交管道。

## <a name="results"></a>结果

在训练完成后：

+ 若要保存已训练模型的快照，请选择训练模块，然后切换到右侧面板中的“输出 + 日志”选项卡。 单击“注册数据集”图标。  可以在模块树中找到已作为模块保存的模型。 

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
