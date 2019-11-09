---
title: 排列功能重要性：模块引用
titleSuffix: Azure Machine Learning service
description: 在给定定型模型和测试数据集的情况下，了解如何使用 Azure 机器学习服务中的排列功能重要性模块来计算特征变量的排列特征重要性分数。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: ddd631b809d4a0635107069f48281db4c0a2e7e0
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837557"
---
# <a name="permutation-feature-importance"></a>排列特征重要性

本文介绍如何在 Azure 机器学习设计器（预览版）中使用排列功能重要性模块来计算数据集的一组功能重要性分数。 使用这些分数有助于确定要在模型中使用的最佳功能。

在此模块中，功能值随机随机打乱，一次一列。 模型的性能在前后测量。 您可以选择一个标准指标来衡量性能。

模块返回的分数表示定型模型在排列之后的性能*变化*。 重要的功能通常对混排过程更为敏感，因此，它们会产生较高的重要性分数。 

本文概述了排列功能及其理论基础，以及它在机器学习中的应用程序：[排列功能重要性](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)。  

## <a name="how-to-use-permutation-feature-importance"></a>如何使用排列功能重要性

生成一组功能评分要求您具有已训练的模型以及测试数据集。  

1.  将排列功能重要性模块添加到管道。 您可以在 "**功能选择**" 类别中找到此模块。 

2.  将定型模型连接到左侧输入。 模型必须是回归模型或分类模型。  

3.  在右侧输入中，连接数据集。 最好选择与用于定型模型的数据集不同的数据集。 此数据集用于根据定型模型进行评分。 它还用于在功能值更改后评估模型。  

4.  对于 "**随机种子**"，请输入一个值，用作随机化的种子。 如果指定0（默认值），则根据系统时钟生成一个数字。

     种子值是可选的，但是，如果想要跨同一管道的运行可再现性，应提供一个值。  

5.  对于**衡量性能的指标**，请选择在排列之后计算模型质量时要使用的单个度量值。  

     Azure 机器学习设计器支持以下指标，具体取决于你是要评估分类还是使用回归模型：  

    -   **分类**

        准确性、精度、回调、平均日志丢失  

    -   **回归**

        精度，召回，平均绝对错误，根本平均平方误差，相对绝对错误，相对平方误差，确定系数  

     有关这些评估指标和如何计算指标的更详细说明，请参阅[评估模型](evaluate-model.md)。  

6.  运行管道。  

7.  模块输出功能列的列表以及与它们关联的分数。 列表按分数的降序排序。  


##  <a name="technical-notes"></a>技术说明

排列功能重要性：随机更改每个特征列的值（一次一列）。 然后计算模型。 

模块提供的排名通常与从[基于筛选器的特征选择](filter-based-feature-selection.md)中获得的排名不同。 基于筛选器的特征选择*在*创建模型之前计算分数。 

区别在于排列功能的重要性不会度量功能和目标值之间的关联。 相反，它会从模型中捕获每个特征对预测的影响程度。
  
## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 
