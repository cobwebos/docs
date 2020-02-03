---
title: 不同 Azure 存储位置中的示例数据-团队数据科学流程
description: Azure blob 容器、SQL Server 和 Hive 表中的示例数据，可将其缩小为更小但更具代表性且更易于管理的大小。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 22e91d50227fcb44c7b90478d76379c14161ae05
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718597"
---
# <a name="heading"></a>对 Azure Blob 容器、SQL Server 和 Hive 表中的数据采样

以下文章介绍了如何对存储在三个不同 Azure 位置之一的数据进行采样：

* [**Azure Blob 容器数据**](sample-data-blob.md)的采样方法是先以编程方式下载该数据，并使用样本 Python 代码对其采样。
* [**SQL Server 数据**](sample-data-sql-server.md)是使用 SQL 和 Python 编程语言进行采样。 
* [**Hive 表数据**](sample-data-hive.md)是使用 Hive 查询进行采样。

此采样任务是[团队数据科学流程 (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) 中的一个步骤。

**为什么对数据采样？**

如果计划要分析的数据集很大，通常最好是对数据进行向下采样，以将数据减至较小但具备代表性且更易于管理的规模。 缩小可以简化数据理解、探索和特征工程。 Cortana Analytics 过程中的这一采样角色是启用数据处理功能和机器学习模型的快速原型设计。

