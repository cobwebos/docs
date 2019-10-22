---
title: 将数据分配到群集：模块引用
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的 "将数据分配给群集" 模块来评分聚类分析模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 3caf568590beb5b1f9d346d472c93f134a4f3a5f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693230"
---
# <a name="module-assign-data-to-clusters"></a>模块：向群集分配数据

本文介绍如何使用 Azure 机器学习可视化界面中的 "*将数据分配到群集*" 模块。 该模块通过使用*K 平均值聚类分析*算法训练的聚类分析模型来生成预测。

"将数据分配到群集" 模块将返回一个数据集，其中包含每个新数据点的可能分配。 


## <a name="how-to-use-assign-data-to-clusters"></a>如何使用向群集分配数据
  
1. 在 Azure 机器学习的可视化界面中，找到先前训练的聚类分析模型。 您可以使用以下方法之一创建和定型聚类分析模型：  
  
    - 使用[k 平均值聚类分析](k-means-clustering.md)模块配置 k 平均值聚类分析算法，并使用数据集和定型聚类分析模型模块（本文）来训练模型。  
  
    - 您还可以从工作区的 "**已保存的模型**" 组中添加现有的已定型聚类分析模型。

2. 将训练的模型附加到向**群集分配数据**的左侧输入端口。  

3. 附加新的数据集作为输入。 

   在此数据集中，标签是可选的。 通常情况下，聚类分析是一种无人监督的学习方法。 你不会提前知道这些类别。 但输入列必须与在定型聚类分析模型中使用的列相同，否则会发生错误。

    > [!TIP]
    > 若要减少从分类预测写入接口的列数，请使用[选择数据集中的列](select-columns-in-dataset.md)，然后选择列的子集。 
    
4. 如果希望结果包含完整的输入数据集（包括显示结果的列（群集分配）），请选中 "**检查是否仅显示结果**" 复选框。
  
    如果清除此复选框，则仅返回结果。 将预测创建为 web 服务的一部分时，此选项可能非常有用。
  
5.  运行管道。  
  
### <a name="results"></a>结果

+  若要查看数据集中的值，请右键单击该模块，选择 "**结果数据集**"，然后选择 "**可视化**"。

