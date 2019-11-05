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
ms.date: 05/30/2019
ms.openlocfilehash: aa1120db5451583153928bd2cbc383a46781a267
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489575"
---
**计算目标可从一个训练作业重复用于下一个定型作业**。 例如，将远程 VM 附加到你的工作区后，可以将其重复用于多个作业。

|培训 &nbsp;目标| GPU 支持 |[自动 ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML 管道](../articles/machine-learning/service/concept-ml-pipelines.md) | [Azure 机器学习设计器](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[本地计算机](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| 可能 | 是 | &nbsp; | &nbsp; |
|[Azure 机器学习计算实例](../articles/machine-learning/service/concept-compute-instance.md)| 是 | | 是 |  |
|[Azure 机器学习计算群集](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| 是 | 是 & <br/>超参数&nbsp;优化 | 是 | 是 |
|[远程 VM](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |是 | 是 & <br/>超参数优化 | 是 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | 是 | 是 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | 是 | &nbsp; |
