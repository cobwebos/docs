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
ms.openlocfilehash: 50905eb987defac612f1055b450b682726f0a56f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752946"
---
|定型&nbsp;目标| GPU 支持 |[自动化机器学习](../articles/machine-learning/service/concept-automated-ml.md) | [机器学习管道](../articles/machine-learning/service/concept-ml-pipelines.md) | [可视界面](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[本地计算机](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| 您可能 | 是 | &nbsp; | &nbsp; |
|[Azure 机器学习计算](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| 是 | 是 （& a) <br/>hyperparameter&nbsp;tuning | 是 | 是 |
|[远程 VM](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |是 | 是 （& a) <br/>超参数优化 | 是 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | 是 | 是 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | 是 | &nbsp; |

**所有计算目标都可重复用于多个训练作业**。 例如，将远程 VM 附加到你的工作区后，可以将其重复用于多个作业。