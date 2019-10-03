---
title: 训练聚类模型:模块参考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的 "定型聚类分析模型" 模块来定型聚类分析模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 4883b1420913eb4e5f3bd5f13a95e410370d9184
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128478"
---
# <a name="train-clustering-model"></a>训练聚类模型

本文介绍了 Azure 机器学习服务的可视界面 (预览) 的模块。

使用此模块可以定型聚类分析模型。

该模块采用了一个已使用[K 平均值聚类分析](k-means-clustering.md)模块进行配置的未训练的聚类模型, 并使用标签或未标记的数据集训练该模型。 该模块创建一个可用于预测的定型模型, 以及针对定型数据中每个事例的一组群集分配。

> [!NOTE]
> 聚类分析模型不能使用[训练模型](train-model.md)模块定型, 后者是用于定型机器学习模型的通用模块。 这是因为[训练模型](train-model.md)仅适用于监督学习算法。 K 平均值和其他聚类分析算法允许无人监督学习, 这意味着该算法可以从未标记的数据中进行学习。  
  
## <a name="how-to-use-train-clustering-model"></a>如何使用定型聚类模型  
  
1.  将 "**训练聚类模型**" 模块添加到 Studio 中的试验。 可以在 "**训练**" 类别的 "**机器学习模块**" 下找到该模块。  
  
2. 添加[K 平均值聚类分析](k-means-clustering.md)模块或另一个创建兼容的聚类分析模型的自定义模块, 并设置聚类分析模型的参数。  
    
3.  将定型数据集附加到**定型聚类分析模型**的右侧输入。
  
5.  在 "**列集**" 中, 选择要在生成群集中使用的数据集中的列。 请确保选择可提供良好功能的列: 例如, 避免使用 Id 或其他具有唯一值的列或具有相同值的列。

    如果标签可用, 可以将其用作一项功能, 或将其保留。  
  
6. 如果要将定型数据与新的群集标签一起输出, 请选择选项 "**仅检查追加或取消选中结果**"。

    如果取消选择此选项, 则仅输出群集分配。 

7. 运行试验, 或单击 "**训练聚类模型**" 模块并选择 "**运行所选项**"。  
  
### <a name="results"></a>结果

完成训练后:


+  若要查看数据集中的值, 请右键单击该模块, 选择 "**结果数据集**", 然后单击 "**可视化**"。

+ 若要保存训练的模型以供以后重复使用, 请右键单击该模块, 选择 "**定型模型**", 然后单击 "**另存为定型模型**"。

+ 若要从模型中生成评分, 请使用 "[将数据分配给群集](assign-data-to-clusters.md)"。



## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 