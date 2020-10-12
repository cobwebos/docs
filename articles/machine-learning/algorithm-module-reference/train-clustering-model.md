---
title: 训练聚类分析模型：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中使用“训练聚类分析模型”模块来训练聚类分析模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 0dfde6fef7cc91edf2101095018bc031d392c4ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898175"
---
# <a name="train-clustering-model"></a>训练聚类模型

本文介绍 Azure 机器学习设计器中的模块。

使用此模块来训练聚类分析模型。

此模块采用已使用 [K-Means 聚类分析](k-means-clustering.md)模块配置的未训练的聚类分析模型，并使用标记的或未标记的数据集来训练模型。 此模块既创建可用于预测的已训练模型，又为训练数据中的每个案例创建一组群集分配。

> [!NOTE]
> 聚类分析模型不能使用[训练模型](train-model.md)模块（用于训练机器学习模型的通用模块）进行训练， 因为[训练模型](train-model.md)仅适用于监督式学习算法。 K-means 和其他聚类分析算法允许非监督式学习，这意味着算法可以从非标记的数据进行学习。  
  
## <a name="how-to-use-train-clustering-model"></a>如何使用“训练聚类分析模型”  

1.  在设计器中向管道添加“训练聚类分析模型”模块。 可以在“机器学习模块”下的“训练”类别中找到此模块 。  
  
2. 添加 [K-Means 聚类分析](k-means-clustering.md)模块，或另一个可创建兼容的聚类分析模型的自定义模块，并设置聚类分析模型的参数。  
    
3.  将训练数据集附加到**训练聚类分析模型**的右侧输入。
  
5.  在“列集”中，从数据集选择用于构建群集的列。 确保选择可以生成良好特征的列：例如，避免使用有唯一值的 ID 或其他列，或者所有值都相同的列。

    如果某个标签可用，则可将它用作特征，或者不用它。  
  
6. 如果需要输出训练数据和新的群集标签，请选择“选中以便进行追加，或者取消选中以便只获取结果”选项。

    如果取消选择此选项，则只输出群集分配。 

7. 提交管道，或单击“训练聚类分析模型”模块，然后选择“运行选中项” 。  
  
### <a name="results"></a>结果

在训练完成后：

+ 若要保存已训练模型的快照，请选择“训练模型”模块右侧面板中的“输出”选项卡。 选择“注册数据集”图标将模型保存为可重用模块。

+ 若要从模型生成分数，请使用[将数据分配到群集](assign-data-to-clusters.md)。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 