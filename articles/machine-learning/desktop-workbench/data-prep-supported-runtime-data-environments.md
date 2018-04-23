---
title: 用于 Azure 机器学习数据准备的执行和数据环境的支持组合 | Microsoft Docs
description: 本文档提供了 Azure 机器学习数据准备支持的不同运行时和数据源组合的完整列表
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: bdd1c51c915787d9e9522f6691ae0ff06d546484
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="supported-matrix-for-this-release"></a>针对此版本的支持矩阵 
如果获取任一 Pandas 或 Spark 数据帧，那么当代码使用 Azure 机器学习数据源或 Azure 机器学习数据准备加载数据时，以下试验计算环境和数据位置的组合将受到支持：

|     |本地文件  |Azure Blob 存储  |SQL Server 数据库***  |
|---------|---------|---------|---------|---------|
|本地 Python    |     支持    |不支持         | 不支持        |         |
|Docker (Linux VM) Python     |仅在项目文件中受支持*         | 不支持        |        不支持 |         |
|Docker (Linux VM) PySpark     |仅在项目文件中受支持*     |支持         | 支持 \*\*        |         |
|Azure 数据科学虚拟机 Python     |仅在项目文件中受支持*         |不支持         |不支持         |         |
|Azure 数据科学虚拟机 PySPark     | 仅在项目文件中受支持*        |不支持         |不支持         |         |
|Azure HDInsight PySpark     | 不支持        |支持         |支持 \*\*         |         |
|Azure HDInsight Python     | 不支持        | 不支持        | 不支持        |         |

目前任何计算目标都不支持 Azure Data Lake Store。

*使用本地文件路径时，项目中的文件会被复制到计算环境中，然后在该环境中读取。 不会复制项目之外的文件，并且路径将不会在计算环境中解析。 请考虑使用数据源替换，以便在本地运行时代码可以使用本地文件。 然后针对不同运行配置切换到 Azure 存储 Blob。 还可以在数据源上使用采样支持，只在某些运行配置中管理大型数据的运行。

\** 使用 Maven JDBC SQL Server 驱动程序 6.2.1. 必须确保此包（或一个兼容包）包含在计算环境的 spark_dependencies.yml 文件中。

***如果可从计算环境访问数据库，则支持 Azure SQL 数据库或 SQL Server。 
