---
title: 训练 SVD 推荐器：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何通过 Azure 机器学习中的“训练 SVD 推荐器”模块使用 SVD 算法训练 Bayesian 推荐器。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: a5740e851fbd8f7ba82e179f7e5299d6c7090596
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890248"
---
# <a name="train-svd-recommender"></a>训练 SVD 推荐器

本文介绍如何在 Azure 机器学习设计器中使用训练 SVD 推荐器模块。 使用此模块，可以基于奇异值分解 (SVD) 算法训练建议模型。  

“训练 SVD 推荐器”模块读取“用户-项目-评级”三元组的数据集。 它返回训练后的 SVD 推荐器。 然后，可以通过[为 SVD 推荐器评分](score-svd-recommender.md)模块使用训练后的模型来预测评级或生成建议。  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>有关建议模型和 SVD 推荐器的详细信息  

建议系统的主要目标是向系统的用户推荐一个或多个项目。  项目示例可能为电影、餐馆、书籍或歌曲。 用户可以是具有项目首选项的人员、一组人员或其他实体。  

推荐器系统有两种主要方法： 

+ **基于内容**方法同时使用用户和项目的特性。 可以通过年龄和性别等属性来描述用户。 可以通过作者和制造商等属性来描述项目。 你可以在社交婚介网站上找到基于内容的建议系统的典型示例。 
+ **协作式筛选**仅使用用户和项目的标识符。 它从用户给项目的评级的矩阵（稀疏）中获取关于这些实体的隐式信息。 我们可以通过某个用户已评级的项目以及对相同项目进行了评级的其他用户来了解该用户。  

SVD 推荐器使用用户和项目的标识符，以及用户对项目的评级的矩阵。 它是一个*协作式推荐器*。 

有关 SVD 推荐器的详细信息，请参阅相关的研究论文：[推荐器系统的矩阵分解技术](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf)。


## <a name="how-to-configure-train-svd-recommender"></a>如何配置“训练 SVD 推荐器”  

### <a name="prepare-data"></a>准备数据

在使用此模块之前，输入数据必须处于建议模型所需的格式。 需要用户-项目-评级三元组的训练数据集。

+ 第一列包含用户标识符。
+ 第二列包含项目标识符。
+ 第三列包含用户-项目对的评级。 评级值必须是数字类型的。  

Azure 机器学习设计器中的“评级”数据集（依次选择“数据集”和“示例”）演示了所需的格式  ：

![电影评分](media/module/movie-ratings-dataset.png)

在此示例中，你可以看到单个用户对多个电影进行了评级。 

### <a name="train-the-model"></a>定型模型

1.  在设计器中将“训练 SVD 推荐器”模块添加到你的管道，并将其连接到训练数据。  
   
2.  对于“因素数目”，请指定要用于推荐器的因素数目。  
    
    每个因素都衡量用户与项目的关联程度。 因素数目也是潜在因素空间的维数。 随着用户和项目数量的增长，最好设置较大的因素数目。 但是如果该数目太大，性能可能会下降。
    
3.  “建议算法迭代数”指示算法应当将输入数据处理多少次。 此数目越大，预测越准确。 但是，此数目越大，训练越慢。 默认值为 30。

4.  对于“学习速率”，请输入一个介于 0.0 和 2.0 之间的数字，用以定义学习步幅。

    学习速率决定了每次迭代时的步幅。 如果步幅太大，则可能会越过最优解。 如果步幅太小，则训练需要花费更长的时间才能找到最优解。 
  
5.  提交管道。  


## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
