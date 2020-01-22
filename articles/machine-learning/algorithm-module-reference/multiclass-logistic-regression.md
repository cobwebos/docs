---
title: 多类逻辑回归：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "多类逻辑回归" 模块来创建逻辑回归模型，该模型可用于预测多个值。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 8d6140e97383fb07e65c87f74a555046f918ac41
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314855"
---
# <a name="multiclass-logistic-regression-module"></a>多类逻辑回归模块

本文介绍 Azure 机器学习设计器中的模块。

使用此模块可以创建逻辑回归模型，该模型可用于预测多个值。

使用逻辑回归的分类是一种监督的学习方法，因此需要标记的数据集。 您可以通过将模型和标记的数据集作为输入添加到模块（如[训练模型](./train-model.md)）来对模型进行定型。 然后，可以使用训练的模型来预测新输入示例的值。

Azure 机器学习还提供了[双类逻辑回归](./two-class-logistic-regression.md)模块，该模块适用于对 binary 或二叉分变量进行分类。

## <a name="about-multiclass-logistic-regression"></a>关于多类逻辑回归

逻辑回归是用于预测结果概率的已知方法，是分类任务的常用方法。 该算法通过将数据用于逻辑函数来预测事件发生的概率。 

在多类逻辑回归中，分类器可用于预测多个结果。

## <a name="configure-a-multiclass-logistic-regression"></a>配置多类逻辑回归

1. 将 "**多类逻辑回归**" 模块添加到管道。

2. 通过设置 "**创建训练人员模式**" 选项，指定要如何定型模型。

    + **单个参数**：如果你知道想要如何配置模型，并提供一组特定值作为参数，请使用此选项。

    + **参数范围**：如果你不确定最佳参数，并想要使用参数扫描，请使用此选项。

3. **优化容差**：指定优化器收敛的阈值。 如果迭代间的改进低于阈值，则算法将会停止并将返回当前模型。

4. **L1 正则化权重**， **L2 正则化权重**：为正则化参数 L1 和 L2 键入要使用的值。 对于上述两者，建议使用非零值。

    正则化是一种防止使用极端系数值的处罚模型的过度拟合的方法。 正则化的工作原理是将与系数值相关联的处罚添加到假设的错误。 具有极端系数值的准确模型会因处罚更多的数据，但具有更保守值的更不准确的模型将因处罚更少。

     L1 正则化和 L2 正则化具有不同的作用和用法。 L1 正则化可以应用于稀疏模型，在处理高维数据时，这很有用。 与此相反，L2 正则化更适用于非稀疏数据。  此算法支持 L1 和 L2 正则化值的线性组合：也就是说，如果 `x = L1` 和 `y = L2``ax + by = c` 定义正则化术语的线性跨度。

     为逻辑回归模型（如[弹性网络正则化](https://wikipedia.org/wiki/Elastic_net_regularization)）设计了 L1 和 L2 术语的不同线性组合。

6. **随机数字种子**：若要使结果可重复运行，请键入一个整数值作为算法的种子。 否则，系统时钟值将用作种子，这可能会在同一管道的运行中产生略微不同的结果。

8. 连接标签的数据集和训练模块之一：

    + 如果将 "**创建**训练人员模式" 设置为 "**单个参数**"，请使用 "[训练模型](./train-model.md)" 模块。

9. 运行管道。



## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 