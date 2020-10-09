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
ms.date: 09/17/2020
ms.openlocfilehash: 5d312e5ccc0eeed3e17ffa855c496779c5769c5c
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707723"
---
**一个训练作业的计算目标可以在下一个训练作业中重复使用**。 例如，将远程 VM 附加到你的工作区后，可以将其重复用于多个作业。  对于机器学习管道，请对每个计算目标使用适当的[管道步骤](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true)。

可为大多数作业的训练计算目标使用下述任一资源。  但是，并非所有资源都可用于自动化机器学习、机器学习管道或设计器：

|训练目标|[自动化 ML](../articles/machine-learning/concept-automated-ml.md) | [ML 管道](../articles/machine-learning/concept-ml-pipelines.md) | [Azure 机器学习设计器](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[本地计算机](../articles/machine-learning/how-to-attach-compute-targets.md#local)| 是 | &nbsp; | &nbsp; |
|[Azure 机器学习计算群集](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| 是 | 是 | 是 |
|[Azure 机器学习计算实例](../articles/machine-learning/how-to-create-manage-compute-instance.md) | 是（通过 SDK）  | 是 |  |
|[远程 VM](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | 是  | 是 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| 是（仅限 SDK 本地模式） | 是 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | 是 | &nbsp; |
