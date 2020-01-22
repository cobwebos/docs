---
title: 计算推荐器： Module reference
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "评估推荐器" 模块来评估推荐器模型预测的准确性。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 0890e13acbba8dae31de28d7c78a81bd9b516853
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76312254"
---
# <a name="evaluate-recommender"></a>评估推荐器

本文介绍如何使用 Azure 机器学习设计器中的 "评估推荐器" 模块。 目标是衡量建议模型做出的预测的准确性。 使用此模块可以评估不同类型的建议：  
  
-   为用户和项目预测的评级    
-   为用户推荐的项  
  
使用建议模型创建预测时，将为每个受支持的预测类型返回略微不同的结果。 "评估推荐器" 模块将推导评分数据集的列格式的预测类型。 例如，评分的数据集可能包含：

- 用户-项-分级三元组
- 用户及其推荐项

该模块还根据所进行的预测类型应用适当的性能度量值。 

  
## <a name="how-to-configure-evaluate-recommender"></a>如何配置评估推荐器

"评估推荐器" 模块使用具有相应 "地面" 数据的建议模型比较预测输出。 例如，[评分 SVD 推荐器](score-svd-recommender.md)模块生成可通过使用 "评估推荐器" 分析的评分数据集。

### <a name="requirements"></a>要求

计算推荐器需要以下数据集作为输入。 
  
#### <a name="test-dataset"></a>测试数据集

测试数据集包含用户-项-分级三元组形式的 "基本" 数据。  

#### <a name="scored-dataset"></a>评分的数据集

评分的数据集包含建议模型生成的预测。  
  
此第二个数据集中的列取决于您在评分过程中执行的预测类型。 例如，评分的数据集可能包含以下项之一：

- 用户可能为项目提供的用户、项目和分级
- 建议用户和项目的列表 

### <a name="metrics"></a>指标

模型的性能指标是基于输入类型生成的。 以下各节将详细介绍。

## <a name="evaluate-predicted-ratings"></a>评估预测评级  

评估预测评级时，评分的数据集（评估推荐器的第二个输入）必须包含满足以下要求的用户-项目-分级三元组：
  
-   数据集的第一列包含用户标识符。    
-   第二列包含项标识符。  
-   第三列包含相应的用户项评级。  
  
> [!IMPORTANT] 
> 若要使计算成功，列名称必须分别为 `User`、`Item`和 `Rating`。  
  
"评估推荐器" 将 "诚实" 数据集中的评级与评分的数据集的预测分级进行比较。 然后，它计算平均绝对误差（MAE）和根本均值方误差（RMSE）。



## <a name="evaluate-item-recommendations"></a>评估项建议

评估项建议时，使用包含每个用户的建议项的评分数据集：
  
-   数据集的第一列必须包含用户标识符。    
-   所有后续列都应包含相应的推荐项标识符，这些标识符按项与用户的相关程度排序。 

在连接此数据集之前，建议对数据集进行排序，使最相关的项目排在最前面。  

> [!IMPORTANT] 
> 若要使计算推荐器正常工作，列名称必须 `User`、`Item 1`、`Item 2`、`Item 3` 等。  
  
"评估推荐器" 计算平均标准化折扣累积增益（NDCG），并在输出数据集中返回。  
  
由于不可能知道建议项的实际 "基本"，因此，"评估推荐器" 在测试数据集中使用用户项评级，作为 NDCG 计算的好处。 为了进行评估，推荐器评分模块仅必须为具有 "诚实" 分级的项（在测试数据集中）生成建议。  
  

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 
