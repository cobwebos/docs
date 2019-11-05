---
title: 计算推荐器： Module Reference
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的 "评估推荐器" 模块来评估推荐器模型预测的准确性。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: dad11f0e03e55186dfee1e7a4f1f82fb44275693
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517990"
---
# <a name="evaluate-recommender"></a>评估推荐器

本文介绍如何使用 Azure 机器学习设计器（预览版）中的 "**评估推荐器**" 模块来度量建议模型做出的预测的准确性。 使用此模块，可以评估四种不同类型的建议：  
  
-   预测给定用户和项目的评级  
  
-   为给定用户推荐的项  
  
使用建议模型创建预测时，将为每个受支持的预测类型返回略微不同的结果。 "**评估推荐器**" 模块将推导评分数据集的列格式的预测类型。 例如，评分的**数据集**可能包含：

- 用户-项-分级三元组
- 用户及其推荐项

该模块还根据所进行的预测类型应用适当的性能度量值。 

  
## <a name="how-to-configure-evaluate-recommender"></a>如何配置评估推荐器

"**评估推荐器**" 模块将推荐模型的预测输出与相应的 "地面" 数据进行比较。 例如，[评分 SVD 推荐器](score-svd-recommender.md)模块生成可通过**评估推荐器**进行分析的评分的数据集。

### <a name="requirements"></a>要求

**计算推荐器**需要以下数据集作为输入。 
  
#### <a name="test-dataset"></a>测试数据集

**测试数据集**包含**用户-项-分级三元**组形式的 "基本" 数据。  

#### <a name="scored-dataset"></a>评分的数据集

**评分的数据集**包含推荐模型生成的预测。  
  
此第二个数据集中的列依赖于在评分过程中执行的预测类型。 例如，评分的数据集可能包含以下任何内容：

- 用户可能为项目提供的用户、项目和分级
- 建议用户和项目的列表 

### <a name="metrics"></a>度量值

模型的性能指标是基于输入类型生成的。 有关详细信息，请参阅以下部分：

+ [评估预测评级](#evaluate-predicted-ratings)
+ [评估项建议](#evaluate-item-recommendations)

## <a name="evaluate-predicted-ratings"></a>评估预测评级  

评估预测评级时，评分的数据集（**评估推荐器**的第二个输入）必须包含**用户-项-分级三元**组，满足以下要求：
  
-   数据集的第一列包含用户标识符。  
  
-   第二列包含项标识符。  
  
-   第三列包含相应的用户项评级。  
  
> [!IMPORTANT] 
> 若要使计算成功，列名称必须分别为 `User`、`Item`和 `Rating`。  
  
"**评估推荐器**" 将 "诚实" 数据集中的评分与评分的数据集的预测分级进行比较，并计算**平均绝对误差**（MAE）和**根本方差方误差**（RMSE）。



## <a name="evaluate-item-recommendations"></a>评估项建议

评估项建议时，使用包含每个用户的建议项的评分的数据集：
  
-   数据集的第一列必须包含用户标识符。  
  
-   所有后续列都应包含相应的推荐项标识符，这些标识符按项与用户的相关程度排序。 

    在连接此数据集之前，我们建议您对数据集进行排序，使最相关的项目排在最前面。  



> [!IMPORTANT] 
> 若要使**计算推荐器**正常工作，列名称必须 `User`、`Item 1`、`Item 2`、`Item 3` 等。  
  
"**评估推荐器**" 计算平均标准化折扣累积增益（**NDCG**），并在输出数据集中返回。  
  
由于不可能知道建议项的实际 "基本"，因此，"**评估推荐器**" 在测试数据集中使用用户项评级，作为 NDCG 计算的好处。 为了进行评估，推荐器评分模块仅必须为具有诚实评级（在测试数据集中）的项生成建议。  
  

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 
