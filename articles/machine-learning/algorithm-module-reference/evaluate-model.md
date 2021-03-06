---
title: 评估模型：模块引用
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的 "评估模型" 模块来度量定型模型的准确性。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 0ad4ceedf9c1d65339c9e4aabebc0a47475ed568
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693802"
---
# <a name="evaluate-model-module"></a>评估模型模块

本文介绍了 Azure 机器学习服务的可视界面（预览）的模块。

使用此模块来度量定型模型的准确性。 您提供包含从模型生成的分数的数据集，而 "**评估模型**" 模块计算一组符合行业标准的评估指标。
  
 "**评估模型**" 返回的指标取决于您要评估的模型的类型：  
  
-   **分类模型**    
-   **回归模型**    



> [!TIP]
> 如果你不熟悉模型评估[，则建议使用 EdX 中的](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/)Stephen Elston 的视频系列。 


有三种方法可以使用 "**评估模型**" 模块：

+ 针对定型数据生成分数，并基于这些分数评估模型
+ 在模型上生成分数，但将这些分数与保留测试集的分数进行比较
+ 使用相同的数据集比较两个不同但相关的模型的分数

## <a name="use-the-training-data"></a>使用定型数据

若要评估某一模型，必须连接包含一组输入列和评分的数据集。  如果没有其他数据可用，则可以使用原始数据集。

1. 将[评分模型](./score-model.md)的**评分数据集**输出连接到**评估模型**的输入。 
2. 单击 "**评估模型**" 模块，并运行管道以生成评估分数。

## <a name="use-testing-data"></a>使用测试数据

机器学习的一个常见方案是将原始数据集分隔到定型和测试数据集、使用[Split](./split-data.md)模块或[分区和示例](./partition-and-sample.md)模块。 

1. 将[评分模型](score-model.md)的**评分数据集**输出连接到**评估模型**的输入。 
2. 将包含测试数据的拆分数据模块的输出连接到 "**评估模型**" 的右侧输入。
2. 单击 "**评估模型**" 模块，然后选择 "**运行所选项**" 以生成评估分数。

## <a name="compare-scores-from-two-models"></a>比较两个模型的分数

您还可以连接另一组分数以**评估模型**。  分数可能是具有已知结果的共享评估集，也可能是来自同一数据的不同模型的一组结果。

此功能非常有用，因为您可以轻松地将两个不同模型的结果与相同数据进行比较。 或者，您可以将两个不同运行的分数与不同参数的相同数据进行比较。

1. 将[评分模型](score-model.md)的**评分数据集**输出连接到**评估模型**的输入。 
2. 将第二个模型的 "评分模型" 模块的输出连接到 "**评估模型**" 的右侧输入。
3. 右键单击 "**评估模型**"，然后选择 "**运行选定**项" 以生成评估分数。

## <a name="results"></a>结果

运行 "**评估模型**" 后，右键单击模块并选择 "**评估结果**" 以查看结果。 你可以：

+ 将结果另存为数据集，以方便使用其他工具进行分析
+ 在接口中生成可视化效果

如果将数据集连接到 "**评估模型**" 的两个输入，结果将包含这两个数据集或这两个模型的度量值。
将在报表中显示附加到左侧端口的模型，然后在报表中显示该模型或数据，后跟在右端口上附加的数据集或模型的度量值。  

例如，下图表示对相同数据生成的两个聚类分析模型的结果进行比较，但使用不同的参数。  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

因为这是聚类分析模型，所以，计算结果不同于比较两个回归模型中的分数或比较两个分类模型。 不过，整体显示是相同的。 

## <a name="metrics"></a>指标

本部分介绍为与**评估模型**一起使用而支持的特定模型类型返回的度量值：

+ [分类模型](#bkmk_classification)
+ [回归模型](#bkmk_regression)

###  <a name="bkmk_classification"></a>分类模型的指标

评估分类模型时，将报告以下度量值。 如果比较模型，则按您选择进行评估的指标对它们进行排序。  
  
-   **准确性**衡量分类模型在总体事例中的结果比例。  
  
-   **精度**是所有正面结果的真实结果的比例。  
  
-   **召回**是模型返回的所有正确结果的小数部分。  
  
-   **F 分数**计算为精度和值介于0和1之间的加权平均值，其中理想的 F 分数值为1。  
  
-   **AUC**测量曲线下的区域，在 y 轴上绘制了真实的正值，x 轴上的误报。 此指标很有用，因为它提供了单个数字来比较不同类型的模型。  
  
- **平均日志丢失**是用于表示错误结果的损失的单个分数。 它的计算方式为两个概率分布（真实值）和模型中的差异。  
  
- **训练日志丢失**是一条分数，表示分类器通过随机预测的优势。 日志丢失通过将其输出的概率与标签中的已知值（真假）进行比较来度量模型的不确定性。 您希望最大程度地减少整个模型的日志丢失情况。

##  <a name="bkmk_regression"></a>回归模型的指标
 
为回归模型返回的度量值通常用于估计错误量。  如果观测值和预测值之间的差异很小，则将模型视为适合数据。 不过，查看残差的模式（任何一个预测点和其对应的实际值之间的差异）可以告诉你有关模型中的潜在偏差的很多信息。  
  
 报告以下指标用于评估回归模型。 在比较模型时，将按您选择进行评估的指标对它们进行排序。  
  
- **平均绝对错误（MAE）** 度量预测对实际结果的接近程度;因此，分数越低越好。  
  
- **根本方差方形错误（RMSE）** 创建单个值，该值汇总了模型中的错误。 通过对差值进行求值，指标就会忽略过度预测与预测下之间的差异。  
  
- **相对绝对错误（RAE）** 是预期值和实际值之间的相对绝对差值;相对，因为平均值差异除以算术平均值。  
  
- **相对平方误差（RSE）** 同样，通过将预测值的总平方误差除以实际值的总平方误差来规范化。  
  
- 表示**零个错误（MZOE）** 指示预测是否正确。  换句话说： `x!=y` 时 `ZeroOneLoss(x,y) = 1`;否则 `0`。
  
- **确定系数**（通常称为 R<sup>2</sup>）表示模型的预测能力，其值介于0和1之间。 如果为零，则表示模型是随机的（说明 nothing）;1表示有合适的大小。 不过，应小心解释 R<sup>2</sup>值，因为低值可能是完全正常的，很大的值可能是可疑的。
  

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 