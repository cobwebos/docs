---
title: 将数据分配到群集：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "将数据分配到群集" 模块来评分聚类分析模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: eff480d6763ae4bd277e6781663c559cc7c9169e
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152442"
---
# <a name="module-assign-data-to-clusters"></a>模块：向群集分配数据

本文介绍如何在 Azure 机器学习设计器（预览版）中使用 "*将数据分配到群集*" 模块。 该模块通过使用*K 平均值聚类分析*算法训练的聚类分析模型来生成预测。

"将数据分配到群集" 模块将返回一个数据集，其中包含每个新数据点的可能分配。 

## <a name="how-to-use-assign-data-to-clusters"></a>如何使用向群集分配数据
  
1. 在 Azure 机器学习设计器中，找到先前训练的聚类分析模型。 您可以使用以下方法之一创建和定型聚类分析模型：  
  
    - 使用[k 平均值聚类分析](k-means-clustering.md)模块配置 k 平均值聚类分析算法，并使用数据集和定型聚类分析模型模块（本文）来训练模型。  
  
    - 您还可以从工作区的 "**已保存的模型**" 组中添加现有的已定型聚类分析模型。

2. 将训练的模型附加到向**群集分配数据**的左侧输入端口。  

3. 附加新的数据集作为输入。 

   在此数据集中，标签是可选的。 通常情况下，聚类分析是一种无人监督的学习方法。 你不会提前知道这些类别。 但输入列必须与在定型聚类分析模型中使用的列相同，否则会发生错误。

    > [!TIP]
    > 若要减少从分类预测写入设计器的列数，请使用["选择数据集中的列](select-columns-in-dataset.md)"，然后选择列的子集。 
    
4. 如果希望结果包含完整的输入数据集（包括显示结果的列（群集分配）），请选中 "**检查是否仅显示结果**" 复选框。
  
    如果清除此复选框，则仅返回结果。 将预测创建为 web 服务的一部分时，此选项可能非常有用。
  
5.  运行管道。  
  
### <a name="results"></a>结果

+  若要查看数据集中的值，请右键单击该模块，然后选择 "**可视化**"。 或者选择该模块并切换到右侧面板中的 "**输出**" 选项卡，单击**端口输出**中的 "直方图" 图标以直观显示结果。

