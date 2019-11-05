---
title: 训练 SVD 推荐器： Module Reference
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure 机器学习服务中使用训练 SVD 推荐器模块来使用 SVD 算法训练 Bayesian 推荐器。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 3b86d77470a4f3d4fe5b005e562a8adae21f8bc7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515663"
---
# <a name="train-svd-recommender"></a>训练 SVD 推荐器

本文介绍如何在 Azure 机器学习设计器（预览版）中使用**训练 SVD 推荐器**模块。 使用此模块可以基于**SVD** （单值分解）算法训练推荐模型。  

**训练 SVD 推荐器**模块读取用户-项-分级三元组的数据集。 它将返回训练的 SVD 推荐器。 然后，可以使用训练的模型通过[评分 SVD 推荐器](score-svd-recommender.md)模块预测评级或生成建议。  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>有关建议模型和 SVD 推荐器的详细信息  

建议系统的主要目的是向系统的*用户*推荐一个或多个*项目*。 项目的示例可以是电影、餐馆、书籍或歌曲。 用户可以是人员、人员组或具有项首选项的其他实体。  

推荐器系统有两种主要方法。 

+ 第一种是**基于内容**的方法，它会对用户和项使用功能。 用户可以通过诸如 "年龄" 和 "性别" 之类的属性进行描述，而 "作者" 和 "制造商" 等属性可以描述项目。 可在社交婚介网站上找到基于内容的建议系统的典型示例。 
+ 第二种方法是**协作筛选**，它仅使用用户的标识符和项，并从用户向项提供的一系列（稀疏）矩阵获取有关这些实体的隐式信息。 我们可以从已分级的项目和已对相同项目评级的其他用户了解用户。  

SVD 推荐器使用用户和项的标识符，以及用户向这些项提供的一系列评级。 它是一个**协作推荐器**。 

有关 SVD 推荐器的详细信息，请参阅相关的研究论文：[适用于推荐器系统的矩阵因子分解技术](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf)。


## <a name="how-to-configure-train-svd-recommender"></a>如何配置定型 SVD 推荐器  

+ [准备定型数据](#prepare-data)
+ [模型](#train-the-model)

### <a name="prepare-data"></a>准备数据

在尝试使用该模块之前，您的数据必须采用建议模型所需的格式。 需要**用户-项-分级三元**组的定型数据集。

#### <a name="required-dataset-of-user-item-ratings"></a>用户-项-分级所需的数据集

用于定型的输入数据包含正确格式的正确数据类型，这一点很重要： 

+ 第一列必须包含用户标识符。
+ 第二列必须包含项标识符。
+ 第三列包含用户/项目对的评级。 评分值必须为数值类型。  

                                                                                                                                                                                                           

Azure 机器学习设计器中的**餐馆分级**数据集（单击 "**保存的数据集**"，然后单击 "**示例**"）演示预期格式：

|Id|placeID|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

在此示例中，你可以看到一个用户已将两个不同的餐厅分级。 

### <a name="train-the-model"></a>训练模型

1.  将**定型 SVD 推荐器**模块添加到设计器中的管道，并将其连接到定型数据。  
   
2.  对于 "**因素数量**"，请键入指定要用于推荐器的系数数的数字。  
    
    每个因素都度量用户与项的关联程度。 系数数量也是潜在系数空间的维数。 随着用户和项目数的增加，最好设置更多的因素。 但是，如果数字太大，性能可能会降低。
    
3.  **建议算法迭代数**，指示算法处理输入数据的次数。 此数字越大，预测就越准确;但是，定型速度较慢。 默认值为30。

4.  对于 "**学习速率**"，请键入在学习时定义步长大小的0.0 和2.0 之间的数字。

    学习速率确定每次迭代时所采取的步骤的大小。 如果步长太大，则可能超过最佳解决方案。 如果步长太小，训练将花费更长的时间来聚合最佳解决方案。 
  
5.  运行管道。  


## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 
