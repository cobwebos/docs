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
ms.openlocfilehash: 2591ab6984ebe4f864540ab290881a6e47f0be71
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580598"
---
**计算目标可从一个训练作业重复用于下一个定型作业**。 例如，将远程 VM 附加到你的工作区后，可以将其重复用于多个作业。

|培训 &nbsp;目标| GPU 支持 |[自动 ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML 管道](../articles/machine-learning/service/concept-ml-pipelines.md) | [Azure 机器学习设计器](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[本地计算机](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| 可能 | 是 | &nbsp; | &nbsp; |
|[Azure 机器学习计算群集](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| 是 | 是 & <br/>超参数&nbsp;优化 | 是 | 是 |
|[远程 VM](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |是 | 是 & <br/>超参数优化 | 是 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | 是 | 是 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | 是 | &nbsp; |
