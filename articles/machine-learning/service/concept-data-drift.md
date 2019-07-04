---
title: 数据偏差监视 （预览）
titleSuffix: Azure Machine Learning service
description: 了解如何监视 Azure 机器学习服务的数据偏移。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: a03e3124647869e7148f271810bb523986a851c6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442381"
---
# <a name="what-is-data-drift-monitoring-preview"></a>什么是数据偏差监视 （预览）？

数据偏差是中的数据分布的更改。 在机器学习的上下文中，定型的机器学习模型可能会遇到由于偏差的降级的预测性能。 监视定型数据和用于进行预测的数据之间的偏差可以帮助检测性能问题。

机器学习模型仅用于为其定型数据。 部署到生产环境的模型，而不会监视其性能可能会导致未检测到和不利影响。 使用数据偏差监视时，可以检测和适应数据偏差。 

## <a name="when-to-monitor-for-data-drift"></a>何时数据偏差的监视器？

我们可以监视的度量值包括：

+ 量值的偏移 （偏差系数）
+ 偏移 （偏差贡献功能） 的原因
+ 距离度量值 （Wasserstein、 能源，等等。）

使用此监视到位，发出警报，或操作可以设置时检测到偏差和数据科学家可以调查此问题的根本原因。 

如果您认为可能会更改已部署的模型的输入的数据，应考虑使用数据偏差检测。

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>如何在 Azure 机器学习服务中监视数据偏差

使用**Azure 机器学习服务**，通过数据集或部署监视数据偏差。 若要监视数据偏差，请指定基准数据集-通常为训练数据集的模型的。 针对基准数据集，第二个数据集-部署-从收集模型输入的数据通常是进行测试。 这两个数据集[分析](how-to-explore-prepare-data.md#explore-with-summary-statistics)和输入数据偏差监视服务。 机器学习模型进行训练，以检测两个数据集之间的差异。 模型的性能将转换为偏移系数，测量两个数据集之间的偏移量。 使用[模型 interpretability](machine-learning-interpretability-explainability.md)计算偏移系数参与的功能。 从数据集配置文件，跟踪每个功能有关的统计信息。 

## <a name="data-drift-metric-output"></a>数据偏差指标输出

有多种方法来查看偏移的度量值：

* 使用 Jupyter 小组件。
* 使用`get_metrics()`函数对任何`datadriftRun`对象。
* 在您的模型在 Azure 门户中查看的度量值

以下度量值保存在每个迭代运行的数据偏移任务：

|指标|描述|
--|--|
wasserstein_distance|定义为一维数值的分布统计的距离。|
energy_distance|定义为一维数值的分布统计的距离。|
datadrift_coefficient|正式 Matthews 相关系数，其实数范围从-1 到 1。 在上下文中的偏差，0 表示无偏差，而 1 表示最大偏差。|
datadrift_contribution|功能的功能用于偏差的重要性。|

## <a name="next-steps"></a>后续步骤

请参阅示例并了解如何监视数据偏差：

+ [了解如何监视部署通过 Azure Kubernetes 服务 (AKS) 的模型数据偏差](how-to-monitor-data-drift.md)
+ 试用[Jupyter 笔记本示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)