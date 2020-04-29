---
title: 评估推荐器：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“评估推荐器”模块来评估推荐器模型预测的准确度。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 38144d5df04427a82989b78843466ecd55386196
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "76312254"
---
# <a name="evaluate-recommender"></a>评估推荐器

本文介绍如何使用 Azure 机器学习设计器（预览版）中的评估推荐器模块。 目的是衡量推荐模型的预测是否准确。 使用此模块，可以评估各种推荐结果：  
  
-   为用户和项预测的评分    
-   推荐给用户的项  
  
使用推荐模型创建预测时，每种受支持的预测类型返回的结果略有不同。 评估推荐器模块将根据评分数据集中的列格式推断出预测的类型。 例如，评分数据集可能包含：

- 用户-项-评分三元组
- 用户及其推荐项

此模块还根据所进行的预测类型应用恰当的性能指标。 

  
## <a name="how-to-configure-evaluate-recommender"></a>如何配置评估推荐器

“评估推荐器”模块将推荐模型用于“实际”数据，并将“实际”数据与预测输出进行比较。 例如，[评分 SVD 推荐器](score-svd-recommender.md)模块会生成多个评分数据集，可使用评估推荐器分析它们。

### <a name="requirements"></a>要求

评估推荐器需要以下数据集作为输入。 
  
#### <a name="test-dataset"></a>测试数据集

测试数据集包含用户-项-评分三元组形式的“实际”数据。  

#### <a name="scored-dataset"></a>评分数据集

评分数据集包含推荐模型生成的预测。  
  
此数据集中的列取决于你在评分过程中执行的预测类型。 例如，评分数据集可能包含以下某项：

- 用户、项和用户可能为该项给出的评分
- 用户及向其推荐的项的列表 

### <a name="metrics"></a>指标

根据输入类型生成模型的性能指标。 以下各节将详细介绍。

## <a name="evaluate-predicted-ratings"></a>评估预测评分  

评估预测评分时，评分数据集（评估推荐器的第二个输入）必须包含满足以下要求的用户-项-评分三元组：
  
-   数据集的第一列包含用户标识符。    
-   第二列包含项标识符。  
-   第三列包含相应的用户-项评分。  
  
> [!IMPORTANT] 
> 若要使计算成功，列名称必须分别为 `User`、`Item` 和 `Rating`。  
  
评估推荐器将“实际”数据集中的评分与评分数据集的预测评分进行比较。 然后，评估推荐器计算平均绝对误差 (MAE) 和均方根误差 (RMSE)。



## <a name="evaluate-item-recommendations"></a>评估项目建议

评估项目建议时，使用包含为每个用户推荐的项的评分数据集：
  
-   数据集的第一列必须包含用户标识符。    
-   所有后续列都应包含相应的推荐项标识符，并按项与用户的相关度排序。 

在连接此数据集之前，建议对数据集进行排序，使相关度最高的项排在最前面。  

> [!IMPORTANT] 
> 若要使评估推荐器正常工作，列名称必须为 `User`、`Item 1`、`Item 2` 和 `Item 3` 等。  
  
评估推荐器计算平均归一化折损累计增益 (NDCG)，并在输出数据集中返回它。  
  
由于不可能知道推荐项的真正“实际”情况，因此，评估推荐器使用测试数据集中的用户-项评分作为 NDCG 计算中的增益。 若要进行评估，推荐器评分模块只能为（测试数据集中）具有“实际”评分的项生成建议。  
  

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
