---
title: 数据偏移监视 (预览版)
titleSuffix: Azure Machine Learning service
description: 了解 Azure 机器学习服务如何监视数据偏移。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: 13f73718fabd711e9c71a56ac4537b2ebef8a411
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371081"
---
# <a name="what-is-data-drift-monitoring-preview"></a>什么是数据偏移监视 (预览版)？

数据偏移是数据分发的变化。 在机器学习的上下文中, 经过训练的机器学习模型可能会因为偏差而遇到下降的预测性能。 监视定型数据和用于进行预测的数据之间的偏差有助于检测性能问题。

机器学习模型只与用于训练它们的数据一样好。 在不监视其性能的情况下将模型部署到生产环境可能会导致未检测到的不利影响。 利用数据偏移监视, 可以检测并适应数据偏移。 

## <a name="when-to-monitor-for-data-drift"></a>何时监视数据偏移？

可以监视的指标包括:

+ 偏移量 (偏差系数)
+ 偏移量的原因 (按功能的偏差份额)
+ 距离指标 (Wasserstein、能源等)

进行此监视后, 可以在检测到偏差时设置警报或操作, 数据科学家可调查问题的根本原因。 

如果你认为已部署模型的输入数据可能会更改, 则应考虑使用数据偏移检测。

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>如何在 Azure 机器学习服务中监视数据偏移

使用**Azure 机器学习服务**, 数据偏移通过数据集或部署来监视。 若要监视数据偏移, 请指定基线数据集, 这通常是模型的定型数据集。 第二个数据集 (通常是从部署中收集的模型输入数据) 根据基线数据集进行测试。 这两个数据集都被[分析](how-to-explore-prepare-data.md#explore-with-summary-statistics)并输入到数据偏移监视服务。 训练机器学习模型来检测两个数据集之间的差异。 模型的性能转换为偏移系数, 这会测量两个数据集之间的偏差量。 使用[model interpretability](machine-learning-interpretability-explainability.md)计算偏移系数的特征。 在数据集配置文件中, 将跟踪有关每个功能的统计信息。 

## <a name="data-drift-metric-output"></a>数据偏移指标输出

可以通过多种方式查看偏差指标:

* 使用 Jupyter[小组件。](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) `RunDetails`
* 对任何`datadriftRun`对象使用函数。`get_metrics()`
* 查看模型中的 Azure 门户的度量值

对于数据偏移任务, 以下度量值保存在每次运行迭代中:

|指标|描述|
--|--|
wasserstein_distance|为一维数值分布定义的统计距离。|
energy_distance|为一维数值分布定义的统计距离。|
datadrift_coefficient|正式 Matthews 相关性系数, 介于-1 和1之间。 在偏差上下文中, 0 表示无偏差, 1 表示最大偏移量。|
datadrift_contribution|导致偏移的功能的重要性。|

## <a name="next-steps"></a>后续步骤

请参阅示例并了解如何监视数据偏移:

+ [了解如何监视通过 Azure Kubernetes Service (AKS) 部署的模型的数据偏差](how-to-monitor-data-drift.md)
+ 试用[Jupyter Notebook 示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)