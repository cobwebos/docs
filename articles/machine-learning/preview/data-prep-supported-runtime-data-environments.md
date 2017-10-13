---
title: "用于 Azure 机器学习数据准备的执行和数据环境的支持组合 | Microsoft Docs"
description: "本文档提供了 Azure ML 数据准备支持的不同运行时和数据源组合的完整列表"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 02e0ca96bff7781c242b4c5e965b229065e71725
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="supported-matrix-for-this-release"></a>针对此版本的支持矩阵 
如果获取任一 Pandas 或 Spark 数据帧，那么当代码使用数据源或数据准备加载数据时，以下试验计算环境和数据位置的组合将受到支持：

|     |本地文件  |Azure BLOB  |SQL Server 数据库***  |
|---------|---------|---------|---------|---------|
|本地 Python    |     支持    |不支持         | 不支持        |         |
|Docker (Linux VM) Python     |仅在项目文件中受支持*         | 不支持        |        不支持 |         |
|Docker (Linux VM) PySpark     |仅在项目文件中受支持*     |支持         | 支持**        |         |
|Azure 数据科学虚拟机 Python     |仅在项目文件中受支持*         |不支持         |不支持         |         |
|Azure 数据科学虚拟机 PySPark     | 仅在项目文件中受支持*        |不支持         |不支持         |         |
|Azure HDInsight PySpark     | 不支持        |支持         |支持**         |         |
|Azure HDInsight Python     | 不支持        | 不支持        | 不支持        |         |

目前任何计算目标都不支持 Azure Data Lake Store。

*在使用本地文件路径时，项目中的文件会被复制到计算环境中，然后在该环境中读取。 不会复制项目之外的文件，并且路径将不会在计算环境中解析。 请考虑使用数据源替换，以便代码在本地运行时可以使用本地文件，然后切换到 Azure 存储 blob 以进行不同的运行配置。 还可以在数据源上使用抽样支持，只在某些运行配置中管理大型数据的运行。

**使用 Maven JDBC SQL Server 驱动程序 6.2.1。 必须确保此包（或一个兼容包）包含在计算环境的 spark_dependencies.yml 文件中。

***假如可从计算环境访问数据库，则支持 Azure SQL 数据库、Azure SQL 数据仓库或 Microsoft SQL Server。 
