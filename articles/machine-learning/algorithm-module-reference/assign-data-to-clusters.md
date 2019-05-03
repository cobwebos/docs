---
title: 将数据分配到群集：模块参考
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure 机器学习服务中使用群集模块将分配的数据进行评分聚类分析模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b370c6ef5be311ed9c8df2737fa1b01144d61011
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028765"
---
# <a name="assign-data-to-clusters"></a>将数据分配到群集

本文介绍如何使用[将数据分配到群集](assign-data-to-clusters.md)可视界面，以便生成预测使用 K 平均值聚类分析算法训练的聚类分析模型中的模块。

该模块返回包含每个新的数据点的可能分配的数据集。 


## <a name="how-to-use-assign-data-to-clusters"></a>如何使用将分配到群集的数据
  
1.  在 visual 界面中，找到以前已训练的聚类分析模型。 可以创建和使用其中一种方法来训练聚类分析模型：  
  
    - 配置 K 平均值算法使用[K-means 聚类分析](k-means-clustering.md)模块，然后训练模型使用的数据集和[训练聚类分析模型](train-clustering-model.md)模块。  
  
  
    您还可以添加从现有的训练聚类分析模型**保存模型**组工作区中。

2. 将训练的模型附加到的左侧输入端口[将数据分配到群集](assign-data-to-clusters.md)。  

3. 附加新的数据集作为输入。 在此数据集，标签是可选的。 因此不应将事先知道类别，聚类分析是通常情况下，非监督式的学习方法。

    但是，输入的列必须是与用于的训练聚类分析模型，或出现错误的列相同。

    > [!TIP]
    > 若要减少从群集预测的输出列数，请使用[选择数据集中的列](select-columns-in-dataset.md)，并选择列的子集。 
    
4. 保留选项**结果仅用于检查追加或取消选中**如果想要包含完整输入数据集，以及指示结果 （群集分配） 的列的结果。
  
    如果取消选择此选项，你获得的结果。 创建预测 web 服务的一部分时，这可能很有用。
  
5.  运行试验。  
  
### <a name="results"></a>结果

 
+  若要查看在数据集中的值，用鼠标右键单击该模块，选择**结果数据集**，然后单击**可视化**。

