---
title: 算法和模块参考
titleSuffix: Azure Machine Learning service
description: 了解有关 Azure 机器学习可视界面中可用的模块
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 6602eb4bacdc3b6382c1b6873a465cdfc0632693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029335"
---
# <a name="algorithm--module-reference-overview"></a>算法和模块参考概述

本参考内容提供有关每个机器学习算法和模块可在 Azure 机器学习服务的可视界面 （预览版） 中的技术背景。 

每个模块表示一组代码，可以独立运行和执行机器学习任务中，提供所需的输入。 模块可能包含特定的算法，或执行在机器学习，如缺失值替换或统计分析中非常重要的任务。 

> [!TIP]
> 在可视界面中的任何实验中，可以获取有关特定模块的信息。 选择该模块，然后选择**更多帮助**中的链接**快速帮助**窗格。

按功能组织模块：

**数据格式转换**

  + [转换为 CSV ](convert-to-csv.md)

**数据输入和输出模块**执行将数据从云源移动到试验的工作。 可以同时运行试验，写入到 Azure 存储、 SQL 数据库或配置单元时，结果或中间数据，也可以使用云存储来试验之间交换数据。  

  + [导入数据](import-data.md)

  + [导出数据](export-data.md)

  + [手动输入数据](enter-data-manually.md)


**数据转换模块**支持上都是唯一的机器学习，例如规范化或装箱数据、 功能选择和维数约简的数据的操作。

  + [在数据集中选择列](select-columns-in-dataset.md)

  + [编辑元数据](edit-metadata.md)

  + [清理缺失数据](clean-missing-data.md)

  + [添加列](add-columns.md)

  + [Add Rows](add-rows.md)

  + [删除重复行](remove-duplicate-rows.md)

  + [拆分数据](split-data.md)

  + [规范化数据](normalize-data.md)

  + [分区和采样](partition-and-sample.md)


**机器学习算法**如聚类分析、 支持向量机或神经网络，可让您自定义机器学习任务使用适当的参数的单个模块内。  
  + [评分模型](score-model.md)

  + [将数据分配到群集 ](assign-data-to-clusters.md)

  + [训练模型](train-model.md)

  + [训练聚类分析模型](train-clustering-model.md)

  + [评估模型](evaluate-model.md)

  + [应用转换](apply-transformation.md)

  + [线性回归](linear-regression.md)

  + [神经网络回归](neural-network-regression.md)

  + [决策林回归](decision-forest-regression.md)

  + [提升的决策树回归](boosted-decision-tree-regression.md)

  + [双类提升的决策树](two-class-boosted-decision-tree.md)

  + [双类逻辑回归](two-class-logistic-regression.md)

  + [多类逻辑回归](multiclass-logistic-regression.md)

  + [多类神经网络](multiclass-neural-network.md)

  + [多类决策林](multiclass-decision-forest.md)

  + [双类平均感知器](two-class-averaged-perceptron.md)

  + [双类决策林](two-class-decision-forest.md)

  + [双类神经网络](two-class-neural-network.md)

  + [双类支持向量机](two-class-support-vector-machine.md)
  
  + [K 平均值聚类分析](k-means-clustering.md)


**Python 模块**轻松地运行自定义函数。 编写代码，并将其嵌入在模块中，若要将 Python 与试验服务相集成。
  + [执行 Python 脚本](execute-python-script.md)

  + [创建 Python 模型](create-python-model.md)


