---
title: 训练 SVD 推荐器： Module Reference
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中使用训练 SVD 推荐器模块通过使用 SVD 算法训练 Bayesian 推荐器。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 1778ba543e070bbffbbc8579b280373d834492fd
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920785"
---
# <a name="train-svd-recommender"></a>训练 SVD 推荐器

本文介绍如何在 Azure 机器学习设计器（预览版）中使用训练 SVD 推荐器模块。 使用此模块可以基于单值分解（SVD）算法为建议模型定型。  

训练 SVD 推荐器模块读取用户-项-分级三元组的数据集。 它将返回训练的 SVD 推荐器。 然后，可以使用训练的模型通过[评分 SVD 推荐器](score-svd-recommender.md)模块预测评级或生成建议。  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>有关建议模型和 SVD 推荐器的详细信息  

建议系统的主要目的是向系统的*用户*推荐一个或多个*项目*。 项目的示例可能为电影、餐馆、书籍或歌曲。 用户可以是人员、一组人员或具有项首选项的另一个实体。  

推荐器系统有两种主要方法： 

+ **基于内容**的方法可为用户和项使用功能。 用户可以通过诸如年龄和性别之类的属性进行描述。 项目可以由 "作者" 和 "制造商" 等属性描述。 你可以在社交婚介网站上找到基于内容的建议系统的典型示例。 
+ **协作筛选**仅使用用户和项目的标识符。 它从用户给定的（稀疏）矩阵矩阵中获取有关这些实体的隐式信息。 我们可以从已分级的项目和已对相同项目评级的其他用户了解用户。  

SVD 推荐器使用用户和项的标识符，以及用户向这些项提供的一系列评级。 这是一个*协作推荐器*。 

有关 SVD 推荐器的详细信息，请参阅相关的研究论文：[适用于推荐器系统的矩阵因子分解技术](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf)。


## <a name="how-to-configure-train-svd-recommender"></a>如何配置定型 SVD 推荐器  

### <a name="prepare-data"></a>准备数据

使用模块之前，输入数据必须采用建议模型所需的格式。 需要用户-项-分级三元组的定型数据集。

+ 第一列包含用户标识符。
+ 第二列包含项标识符。
+ 第三列包含用户/项目对的评级。 评分值必须为数值类型。  

Azure 机器学习设计器中的**电影分级**数据集（选择**数据集**，然后是**示例**）演示了预期的格式：

![电影分级](media/module/movie-ratings-dataset.png)

在此示例中，可以看到一个用户已对多个电影评级。 

### <a name="train-the-model"></a>定型模型

1.  将定型 SVD 推荐器模块添加到设计器中的管道，并将其连接到定型数据。  
   
2.  对于 "**因素数量**"，请指定要用于推荐器的系数数。  
    
    每个因素都度量用户与项的关联程度。 系数数量也是潜在系数空间的维数。 随着用户和项目数量的增加，最好设置更多的因素。 但如果数字太大，性能可能会下降。
    
3.  **建议算法迭代数**指示算法处理输入数据的次数。 此数字越大，预测就越精确。 但数字越大，培训速度越慢。 默认值为 30。

4.  对于 "**学习速率**"，请输入一个介于0.0 和2.0 之间的数字，用于定义学习的步长大小。

    学习速率确定每次迭代的步骤大小。 如果步长太大，则可能超过最佳解决方案。 如果步长太小，培训需要更长的时间来寻找最佳解决方案。 
  
5.  运行管道。  


## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 
