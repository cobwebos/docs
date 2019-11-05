---
title: 排列功能重要性：模块引用
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的排列功能重要性模块来计算给定定型模型和测试数据集的特征变量的排列特征重要性分数。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 8a2ddb92101957a3dcadebb17dffa39113825e4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517938"
---
# <a name="permutation-feature-importance"></a>排列特征重要性

本文介绍如何在 Azure 机器学习设计器（预览版）中使用[排列功能重要性](permutation-feature-importance.md)模块来计算数据集的一组功能重要性分数。 使用这些分数有助于确定要在模型中使用的最佳功能。

在此模块中，功能值随机随机打乱，一次一列，并且在前后测量模型的性能。 你可以选择提供的一个标准指标来衡量性能。

模块返回的分数表示定型模型在排列之后的性能**变化**。 重要的功能通常对混排过程更为敏感，因此会产生较高的重要性分数。 

本文提供了有关在机器学习中排列特征重要性、理论基础及其应用程序的大致概述：[排列特征重要性](http://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)  

## <a name="how-to-use-permutation-feature-importance"></a>如何使用排列功能重要性

若要生成一组功能评分，需要具有已训练的模型以及测试数据集。  

1.  将**排列功能重要性**模块添加到管道。 您可以在 "**功能选择**" 类别中找到此模块。 

2.  将定型模型连接到左侧输入。 模型必须是回归模型或分类模型。  

3.  在右侧输入中，连接一个数据集，最好是与用于定型模型的数据集不同的数据集。 此数据集用于根据定型模型进行评分，并用于在更改功能值后对模型进行评分。  

4.  对于 "**随机种子**"，请键入一个值，用作随机化的种子。 如果指定0（默认值），则根据系统时钟生成一个数字。

     种子值是可选的，但是，如果想要跨同一管道的运行可再现性，应提供一个值。  

5.  对于**测量性能的指标**，请选择在排列之后计算模型质量时要使用的单个度量值。  

     Azure 机器学习设计器支持以下度量值，具体取决于你是要评估分类还是使用回归模型：  

    -   **分类**

        准确性、精度、回调、平均日志丢失  

    -   **回归**

        精度，召回，平均绝对错误，根本平均平方误差，相对绝对错误，相对平方误差，确定系数  

     有关这些评估指标和如何计算指标的更详细说明，请参阅[评估模型](evaluate-model.md)。  

6.  运行管道。  

7.  模块输出功能列的列表和与它们关联的分数，按分数顺序排列，按降序排列。  


##  <a name="technical-notes"></a>技术说明

本部分提供了实现的详细信息、提示以及常见问题的解答。

### <a name="how-does-this-compare-to-other-feature-selection-methods"></a>这与其他功能选择方法相比如何？

排列功能重要性：随机更改每个特征列的值、一次一列，然后计算模型。 

排列特征重要性提供的排名通常与从[基于筛选器的特征选择](filter-based-feature-selection.md)中获得的排名不同，后者在创建模型**之前**计算分数。 

这是因为排列功能的重要性不会度量功能和目标值之间的关联，而是捕获每个特征对模型预测的影响程度。
  
## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 
