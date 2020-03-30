---
title: 排列特征重要性：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何在给定经训练的模型和测试数据集的情况下，使用 Azure 机器学习中的“排列特征重要性”模块，计算特征变量的排列特征重要性分数。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: e4511cf4393172e7d2b1ab8a985c76d8f98d4015
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456057"
---
# <a name="permutation-feature-importance"></a>排列特征重要性

本文介绍如何在 Azure 机器学习设计器（预览版）中使用排“排列特征重要性”模块计算数据集的一组特征重要性分数。 使用这些分数有助于确定最适合在模型中使用的特征。

在本模块中，将对特征值进行随机重排，每次重排一列。 在随机重排前后度量模型的性能。 可以选择其中一个标准指标来度量性能。

模块返回的分数表示经训练的模型在排列后的性能变化**。 重要特征通常对重排过程更为敏感，因此其重要性分数较高。 

本文概述了排列功能及其理论基础及其在机器学习中的应用：[排列特征的重要性](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)。  

## <a name="how-to-use-permutation-feature-importance"></a>如何使用排列特征重要性

要生成一组特征评分，需要有经训练的模型和测试数据集。  

1.  将“排列特征重要性”模块添加到管道。 可以在“特征选择”类别中找到此模块****。 

2.  将经训练的模型连接到左输入。 模型必须是回归模型或分类模型。  

3.  在右输入中，连接数据集。 优先选择与训练模型所用的数据集不同的数据集。 此数据集用于根据经训练的模型进行评分。 此外还用于在特征值发生变化后评估模型。  

4.  对于“随机种子”，输入一个值，用作随机化种子****。 如果指定 0（默认值），将根据系统时钟生成一个数字。

     虽然种子值可选，但如果想要使同一管道的运行结果可重现，应提供一个值。  

5.  对于“度量性能的指标”，选择在排列后计算模型质量时要使用的单个指标****。  

     Azure 机器学习设计器支持以下指标，具体取决于评估的是分类模型还是回归模型：  

    -   **分类**

        精度、精度、召回  

    -   **回归**

        精度、召回率、平均绝对误差、均方根误差、相对绝对误差、相对平方误差和决定系数  

     若要更详细地了解这些评估指标及其计算方式，请参阅[评估模型](evaluate-model.md)。  

6.  提交管道。  

7.  模块输出特征列的列表和与其关联的分数。 列表按分数降序排列。  


##  <a name="technical-notes"></a>技术说明

排列特征重要性的原理是随机更改每个特征列的值，每次更改一列。 然后评估模型。 

模块提供的排列通常与从[基于筛选器的特征选择](filter-based-feature-selection.md)获得的不同。 基于筛选器的特征选择会在创建模型前计算分数**。 

造成这种差异的原因在于，排列特征重要性不会度量特征和目标值之间的关联。 它会表现每个特征对模型预测的影响。
  
## <a name="next-steps"></a>后续步骤

参阅 Azure 机器学习[可用的模块集](module-reference.md)。 
