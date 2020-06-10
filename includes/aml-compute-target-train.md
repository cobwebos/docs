---
title: include 文件
description: include 文件
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: 764e4c33182499b922499c798d57fd05d8859742
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673384"
---
**一个训练作业的计算目标可以在下一个训练作业中重复使用**。 例如，将远程 VM 附加到你的工作区后，可以将其重复用于多个作业。  对于机器学习管道，请对每个计算目标使用适当的[管道步骤](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)。

|训练目标|[自动化 ML](../articles/machine-learning/concept-automated-ml.md) | [ML 管道](../articles/machine-learning/concept-ml-pipelines.md) | [Azure 机器学习设计器](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[本地计算机](../articles/machine-learning/how-to-set-up-training-targets.md#local)| 是 | &nbsp; | &nbsp; |
|[Azure 机器学习计算群集](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| 是且 <br/>超参数优化 | 是 | 是 |
|[Azure 机器学习计算实例](../articles/machine-learning/concept-compute-instance.md) | 是且 <br/>超参数优化 | 是 |  |
|[远程 VM](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | 是且 <br/>超参数优化 | 是 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| 是（仅限 SDK 本地模式） | 是 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | 是 | &nbsp; |
