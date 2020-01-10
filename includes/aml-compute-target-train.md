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
ms.openlocfilehash: 06800e7bb18634f1cbe847ced5450172106ec6f9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75840637"
---
**计算目标可从一个训练作业重复用于下一个定型作业**。 例如，将远程 VM 附加到你的工作区后，可以将其重复用于多个作业。  对于机器学习管道，请对每个计算目标使用相应的[管道步骤](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)。

|培训 &nbsp;目标|[自动 ML](../articles/machine-learning/concept-automated-ml.md) | [ML 管道](../articles/machine-learning/concept-ml-pipelines.md) | [Azure 机器学习设计器](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[本地计算机](../articles/machine-learning/how-to-set-up-training-targets.md#local)| 是 | &nbsp; | &nbsp; |
|[Azure 机器学习计算实例（预览）](../articles/machine-learning/concept-compute-instance.md) | | 是 |  |
|[Azure 机器学习计算群集](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| 是 & <br/>超参数&nbsp;优化 | 是 | 是 |
|[远程 VM](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | 是 & <br/>超参数优化 | 是 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| 是（仅限 SDK 本地模式） | 是 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | 是 | &nbsp; |
