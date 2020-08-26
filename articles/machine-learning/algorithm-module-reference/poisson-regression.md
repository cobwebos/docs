---
title: 泊松回归：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用泊松回归模块创建泊松回归模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 6682d9426ed3fe011fe5c493ec34fcdf0a2b35b7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095911"
---
# <a name="poisson-regression"></a>Poisson 回归

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块可在管道中创建泊松回归模型。 泊松回归用于预测数值，通常是计数。 因此，仅当您尝试预测的值满足以下条件时，才应使用此模块创建回归模型：

- 响应变量具有[泊松分布](https://en.wikipedia.org/wiki/Poisson_distribution)。  

- 计数不能为负值。 如果你尝试对负标签使用此方法，则它将彻底失败。

- 泊松分布是离散分布;因此，对非整数使用此方法没有意义。

> [!TIP]
> 如果你的目标不是计数，泊松回归可能并不是适当的方法。 [在设计器中尝试其他回归模块](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference#machine-learning-algorithms)。 

设置回归方法之后，必须使用包含要预测的值示例的数据集来训练该模型。 然后，可以使用训练后的模型进行预测。

## <a name="more-about-poisson-regression"></a>有关泊松回归的详细信息

泊松回归是一种特殊的回归分析，通常用于模型计数。 例如，泊松回归可用于以下方案：

- 为航班关联的寒冷天气次数建模

- 估计事件期间紧急服务调用的数目

- 预测促销之后的客户查询数

- 创建列联表

由于响应变量具有泊松分布，因此，该模型对数据和其概率分布的概率与最小二乘法的假设不同。 因此，泊松模型应不同于其他回归模型。

## <a name="how-to-configure-poisson-regression"></a>如何配置泊松回归

1. 在设计器（预览版）中将**泊松回归**模块添加到管道。 可以在 "**回归**" 类别下的 "**机器学习算法**" 下找到此模块。

2. 添加包含正确类型的定型数据的数据集。 

    建议使用[规范化数据](normalize-data.md)来规范化输入数据集，然后再使用它来训练回归量。

3. 在 "**泊松回归**" 模块的右窗格中，通过设置 "**创建训练人员模式**" 选项来指定你希望如何定型模型。  
  
    - **单个参数**：如果知道自己想要如何配置模型，请提供一组特定的值作为参数。
  
    - **参数范围**：如果不能确定最佳参数，请使用[优化模型超参数](tune-model-hyperparameters.md)模块执行参数扫描。 讲师会循环访问指定的多个值，以找到最佳配置。
  
4. **优化容差**：键入定义优化过程中的容差间隔值。 值越小，拟合就会越慢，但准确性越高。

5. **L1 正则化权重**和**l2 正则化权重**：键入值以用于 L1 和 l2 正则化。 **“正则化”将约束添加到有关独立于训练数据的模型各个方面的算法。 正则化通常也用来避免过度拟合。 

    - L1 正则化很有用，如果目标是能够尽可能为稀疏的模型。

        L1 正则化是通过减去从学员尝试最大程度减少丢失表达式的权重向量的 L1 权重。 L1 范数是 L0 范数的适当近似值，即数目的非零坐标进度的近似值。

    - L2 正则化可防止权重向量中的任何单个坐标在数量级上增长过多。 如果目标是获得整体权重较小的模型，则 L2 正则化很有用。

    在此模块中，你可以应用 L1 和 L2 正则化的组合。 通过将 L1 和 L2 正则化结合使用，可以对参数值的量施加负面影响。 学员尝试最大程度减少产生负面影响降至最低丢失的一种折中。

    有关 L1 和 L2 正则化的详细讨论，请参阅[l1 和 L2 正则化 for 机器学习](https://msdn.microsoft.com/magazine/dn904675.aspx)。

6. **L-l-bfgs 的内存大小**：指定为模型管接头和优化保留的内存量。

     L-BFGS 是基于 Broyden – Fletcher – Goldfarb – Shanno （L-BFGS）算法进行优化的特定方法。 方法使用有限数量的内存（L）来计算下一个步骤的方向。

     通过更改此参数，您可以影响为下一步计算而存储的过去位置和渐变的数目。

7. 将定型数据集和未定型的模型连接到定型模块之一： 

    - 如果将“创建训练程序模式”设置为“单个参数”，请使用[训练模型](train-model.md)模块。********

    - 如果将 "**创建训练人员模式**" 设置为 "**参数范围**"，请使用[调谐模型超参数](tune-model-hyperparameters.md)模块。

    > [!WARNING]
    > 
    > - 如果将参数范围传递到[定型模型](train-model.md)，则它仅使用 "参数范围" 列表中的第一个值。
    > 
    > - 如果将一组参数值传递到[优化模型超参数](tune-model-hyperparameters.md)模块，则在它需要每个参数的一系列设置时，它将忽略这些值，并使用学习器的默认值。
    > 
    > - 如果选择“参数范围”选项并为任何参数输入单个值，则整个整理过程中都会使用你指定的单个值，即使其他参数的值发生一系列更改。

8.  提交管道。

## <a name="results"></a>结果

在训练完成后：

+ 若要保存训练的模型的快照，请选择训练模块，然后切换到右侧面板中的 "**输出 + 日志**" 选项卡。 单击图标 "**注册数据集**"。  可以在模块树中找到已作为模块保存的模型。 

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
