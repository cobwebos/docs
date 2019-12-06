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
ms.openlocfilehash: 25a865296d2ebaabcf9043341878928763d61a6f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875490"
---
**计算目标可从一个训练作业重复用于下一个定型作业**。 例如，将远程 VM 附加到你的工作区后，可以将其重复用于多个作业。  对于机器学习管道，请对每个计算目标使用相应的[管道步骤](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)。

|培训 &nbsp;目标|[自动 ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML 管道](../articles/machine-learning/service/concept-ml-pipelines.md) | [Azure 机器学习设计器](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|
|[本地计算机](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| 是 | &nbsp; | &nbsp; |
|[Azure 机器学习计算群集](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| 是 & <br/>超参数&nbsp;优化 | 是 | 是 |
|[远程 VM](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) | 是 & <br/>超参数优化 | 是 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| 是 | 是 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla) | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch) | &nbsp; | 是 | &nbsp; |
