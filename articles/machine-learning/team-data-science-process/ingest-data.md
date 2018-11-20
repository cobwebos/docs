---
title: 将数据载入 Azure 存储环境进行分析 | Microsoft 文档
description: 将数据移入和移出 Azure Blob 存储
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: a5db14b99a81c373fbc72f523798e1f3bbdf9285
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344495"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>将数据载入存储环境以进行分析

团队数据科学过程要求引入或载入各种不同存储环境中的数据在过程的每个阶段中都以最合适的方式进行处理或分析。 常用于处理的数据目标包括 Azure Blob 存储、SQL Azure 数据库、Azure VM 上的 SQL Server、 HDInsight (Hadoop) 和 Azure 机器学习。 

以下文章介绍如何将数据引入到存储和处理数据的各种目标环境中。

* 到/从 [Azure Blob 存储](move-azure-blob.md)
* 到 [Azure VM 上的 SQL Server](move-sql-server-virtual-machine.md)
* 到 [Azure SQL 数据库](move-sql-azure.md)
* 到 [Hive 表](move-hive-tables.md)
* 到 [SQL 分区表](parallel-load-sql-partitioned-tables.md)
* 从[本地 SQL Server](move-sql-azure-adf.md)

技术和业务需求以及数据的初始位置、格式和大小，将确定需要将数据引入其中以实现分析的目标的目标环境。 要求数据在多个环境之间移动以实现构建预测模型所需的各种任务，这样的方案是不常见的。 例如，此系列任务可能包括数据浏览、预处理、清理、向下采样和模型定型。
