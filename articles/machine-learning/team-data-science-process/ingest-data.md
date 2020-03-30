---
title: 将数据加载到 Azure 存储环境 - 团队数据科学流程
description: 了解如何将数据引入到存储和处理数据的各种目标环境中。
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
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720531"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>将数据载入存储环境以进行分析

团队数据科学流程要求在每个阶段以最适当的方式引入或加载数据。 数据目标可以包括 Azure Blob 存储、SQL Azure 数据库、Azure VM 上的 SQL 服务器、HDInsight（Hadoop）、同步分析和 Azure 机器学习。 

以下文章介绍如何将数据引入到存储和处理数据的各种目标环境中。

* 到/从 [Azure Blob 存储](move-azure-blob.md)
* 到 [Azure VM 上的 SQL Server](move-sql-server-virtual-machine.md)
* 到[Azure SQL 数据库](move-sql-azure.md)
* 到 [Hive 表](move-hive-tables.md)
* 到 [SQL 分区表](parallel-load-sql-partitioned-tables.md)
* 从[本地 SQL Server](move-sql-azure-adf.md)

技术和业务需求以及数据的初始位置、格式和大小将决定最佳数据引入计划。 最好的计划有几个步骤并不罕见。 例如，此系列任务可能包括数据浏览、预处理、清理、向下采样和模型定型。  Azure 数据工厂是一种推荐的 Azure 资源，用于协调数据移动和转换。
