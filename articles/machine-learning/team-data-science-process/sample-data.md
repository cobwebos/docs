---
title: 不同 Azure 存储位置中的示例数据 - Team Data Science Process
description: Azure blob 容器、SQL Server 和 Hive 表中的示例数据，可将其缩小为更小但更具代表性且更易于管理的大小。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc91aec80f74488125649cfe807757ba5ae49c9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60253094"
---
# <a name="heading"></a>对 Azure Blob 容器、SQL Server 和 Hive 表中的数据采样

以下文章介绍了如何对存储在三个不同 Azure 位置之一的数据进行采样：

* [**Azure Blob 容器数据**](sample-data-blob.md)的采样方法是先以编程方式下载该数据，并使用样本 Python 代码对其采样。
* [**SQL Server 数据**](sample-data-sql-server.md)是使用 SQL 和 Python 编程语言进行采样。 
* [**Hive 表数据**](sample-data-hive.md)是使用 Hive 查询进行采样。

此采样任务是[团队数据科学流程 (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) 中的一个步骤。

**为什么对数据采样？**

如果计划要分析的数据集很大，通常最好是对数据进行向下采样，以将数据减至较小但具备代表性且更易于管理的规模。 这有利于数据了解、探索和功能设计。 它在 Cortana Analytics 进程中的作用是能够快速建立数据处理函数和机器学习模型的快速原型。

