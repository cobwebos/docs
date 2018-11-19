---
title: 对 Azure Blob 容器、SQL Server 和 Hive 表中的数据采样 | Microsoft Docs
description: 如何浏览存储在各个 Azure 环境中的数据。
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 60431578ba8b5c740fe5e1124dcdad32c3cb2ede
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344071"
---
# <a name="heading"></a>对 Azure Blob 容器、SQL Server 和 Hive 表中的数据采样

以下文章介绍了如何对存储在三个不同 Azure 位置之一的数据进行采样：

* [**Azure Blob 容器数据**](sample-data-blob.md)的采样方法是先以编程方式下载该数据，并使用样本 Python 代码对其采样。
* [**SQL Server 数据**](sample-data-sql-server.md)是使用 SQL 和 Python 编程语言进行采样。 
* [**Hive 表数据**](sample-data-hive.md)是使用 Hive 查询进行采样。

此采样任务是[团队数据科学流程 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中的一个步骤。

**为什么对数据采样？**

如果计划要分析的数据集很大，通常最好是对数据进行向下采样，以将数据减至较小但具备代表性且更易于管理的规模。 这有利于数据了解、探索和功能设计。 它在 Cortana Analytics 进程中的作用是能够快速建立数据处理函数和机器学习模型的快速原型。

