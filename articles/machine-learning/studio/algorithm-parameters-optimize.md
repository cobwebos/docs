---
title: 优化算法
titleSuffix: ML Studio (classic) - Azure
description: 说明如何为 Azure 机器学习 Studio （经典）中的算法选择最佳参数集。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 04148b482cb07665f43df5bd86a77175cbbaf08b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218270"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>选择参数以优化 Azure 机器学习 Studio （经典）中的算法

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

本主题介绍如何为 Azure 机器学习 Studio （经典）中的算法选择正确的超参数集。 大多数机器学习算法使用参数来设置。 训练模型时，需要为这些参数提供值。 经过训练的模型效力取决于选择的模型参数。 查找最佳参数集的过程称为*模型选择*。



有多种方法可选择模型。 在机器学习中，交叉验证是用于模型选择的最广泛使用的方法之一，并且是 Azure 机器学习 Studio （经典）中的默认模型选择机制。 由于 Azure 机器学习 Studio （经典）同时支持 R 和 Python，因此你始终可以通过使用 R 或 Python 来实现其自己的模型选择机制。

查找最佳参数集有四个步骤：

1. **定义参数空间**：对于算法，首先决定要考虑的确切参数值。
2. **定义交叉验证设置**：决定如何为数据集选择交叉验证折叠。
3. **定义指标**：决定用于确定最佳参数集的指标，例如准确性、均方根误差、精度、撤销率或 F 分数。
4. **训练、评估和比较**：对于每个独一无二的参数值组合，交叉验证基于定义的误差指标进行执行。 评估和比较后，可选择最佳模型。

下图演示了如何在 Azure 机器学习 Studio （经典）中实现此目的。

![查找最佳参数集](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>定义参数空间
可在模型初始化步骤中定义参数集。 所有机器学习算法的参数窗格中具有两个训练模式：单个参数和参数范围。 选择参数范围模式。 在参数范围模式下，可为每个参数输入多个值。 可在文本框中输入以逗号分隔的值。

![双类提升决策树，单个参数](./media/algorithm-parameters-optimize/fig2.png)

 或者，使用**使用范围生成器**定义网格的最大和最小网格点和生成的总点数。 默认情况下，参数值按线性刻度生成。 但是，如果“对数刻度”处于选中状态，这些值会在对数刻度中生成（即相邻点的比率是常量，而不是它们的差）。 对于整数参数，可使用连字符定义范围。 例如，"1-10" 表示1和10之间的所有整数（包括两者）都形成参数集。 也支持混合模式。 例如，参数集 "1-10，20，50" 将包含整数1-10、20和50。

![双类提升决策树，参数范围](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>定义交叉验证折叠
[分区和示例][partition-and-sample]模块可用于随机将折叠分配到数据。 在模块的以下示例配置中，我们定义 5 个折叠并随机将折叠号分配到示例实例。

![分区和采样](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>定义指标
[优化模型超参数][tune-model-hyperparameters]模块支持凭经验为给定算法和数据集选择最佳参数集。 除了有关训练模型的其他信息，此模块的“属性”窗格包括确定最佳参数集的指标。 它分别具有两个不同的下拉列表框用于分类和回归算法。 如果正在考虑的算法是分类算法，则忽略回归指标，反之亦然。 在此特定示例中，该指标为**准确性**。   

![扫描参数](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>训练、评估和比较
相同的[优化模型超参数][tune-model-hyperparameters]模块训练与参数集对应的所有模型，计算各种指标，然后根据所选的指标创建最佳训练模型。 此模块具有两个必需输入：

* 未训练的学习器
* 数据集

该模块还具有一个可选数据集输入。 使用折叠信息将数据集连接到必需数据集输入。 如果未向数据集分配任何折叠信息，那么默认情况下会自动执行 10 个折叠交叉验证。 如果未执行折叠分配且在可选数据集端口提供验证数据集，则将选择训练-测试模式且第一个数据集用于为每个参数组合训练模型。

![提升决策树分类器](./media/algorithm-parameters-optimize/fig6a.png)

然后会在验证数据集上评估模型。 模块的左侧输出端口显示不同的指标作为参数值的函数。 右输出端口根据所选指标提供对应于最佳模型的已训练模型（本例中为**准确性**）。  

![验证数据集](./media/algorithm-parameters-optimize/fig6b.png)

可以通过可视化右侧输出端口查看所选的确切参数。 保存为已训练模型后，此模型可用于对测试集进行评分或可操作性 Web 服务。

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
