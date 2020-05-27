---
title: 什么是 Azure Synapse Analytics？
description: Azure Synapse Analytics 概述
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 5c458b1c04a7f3be1a43d725591426e619286b7d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587863"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>什么是 Azure Synapse Analytics（工作区预览版）？

[!INCLUDE [preview](includes/note-preview.md)]

当今的企业分析师需要大规模地处理任何类型的数据，不管是原始数据、提炼的数据还是特选的数据。 在过去，生成这些类型的分析解决方案需要企业将大数据与数据仓库技术（例如 Spark 和 SQL）结合到一起。 接下来则需要将这些技术集成到丰富的数据管道中，以便处理关系存储和 Data Lake 中的数据。  

此类解决方案很难生成、配置、保护和维护，会延迟智能见解的迅速提取。

Azure Synapse 是一个集成式的分析服务，可以缩短在不同的数据仓库和大数据分析系统中对各种任意规模的数据进行见解提取所需的时间。 它汇集了企业数据仓库中所用的 SQL 技术的精华、大数据分析中所用的 Spark 技术，以及用于协调活动和数据移动的 Pipelines。  

Azure Synapse 随附 Web 原生的 Studio 用户体验，该体验提供用于管理、监视、代码编写与安全保护的单一体验和模型。

Azure Synapse 允许企业使用最熟悉的分析方法，在任意规模的任何数据中收集见解，既简单又快捷。 它与 Power BI 深度集成，使数据工程师能够生成端到端运行的分析解决方案来提供商业智能。

此外，Azure Synapse 通过其内置的 AzureML 支持，利用机器学习简化了预测模型的生成和高级分析。

## <a name="key-features--benefits"></a>重要功能和优势

### <a name="industry-leading-sql"></a>行业领先的 SQL

* Synapse SQL 是一个分布式查询系统，使企业能够使用数据工程师所熟悉的标准 T-SQL 体验来实现数据仓库和数据虚拟化方案。 它还扩展了 SQL 的功能，可以解决流式处理和机器学习方案的问题。

* Synapse SQL 提供无服务器和预配资源模型，并根据需求提供消耗和计费选项。  要使性能和成本可预测，可以对池进行预配，保留对 SQL 表中存储的数据进行处理的能力。 对于计划外的或突发性的工作负载，可以使用无服务器的、始终可用的 SQL 终结点。
* 使用内置的流式处理功能可将数据从云数据源载入到 SQL 表中
* 使用机器学习模型将 AI 与 SQL 集成，并使用 T-SQL PREDICT 函数对数据进行评分

### <a name="industry-standard-apache-spark"></a>行业标准 Apache Spark

Apache Spark for Azure Synapse 深入无缝地集成了 Apache Spark - 用于数据准备、数据工程、ETL 和机器学习的最流行的开源大数据引擎。

* Apache Spark 2.4 的集成了 SparkML 算法和 AzureML 的 ML 模型原生支持 Linux Foundation Delta Lake。
* 简化的资源模型使你无需担心群集的管理。
* Spark 可以快速启动并主动自动缩放。
* 原生支持 .NET for Spark，使你能够在 Spark 应用程序中重复利用自己的 C# 专业知识和现有的 .Net 代码。

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>SQL 和 Apache Spark 在 Data Lake 上的互操作

Azure Synapse 消除了结合使用 SQL 和 Spark 时存在的传统技术阻碍。 你可以根据需求和专业知识无缝混搭使用这两种技术。

* 与 Hive 兼容的共享元数据系统使得 Spark 或 Hive 可以无缝使用基于 Data Lake 中的文件定义的表。
* SQL 和 Spark 可以直接浏览和分析 Data Lake 中存储的 Parquet、CSV、TSV 和 JSON 文件。
* 在 SQL 与 Spark 数据库之间以可缩放的方式快速加载和卸载数据

### <a name="built-in-orchestration-via-pipelines"></a>通过管道提供内置业务流程

Azure Synapse 内置了与 Azure 数据工厂相同的数据集成引擎和体验，使你可以创建丰富的数据管道，而无需使用单独的业务流程引擎。

* 在 Synapse 与 85 个以上的本地数据源之间移动数据
* 协调 Notebooks、Pipelines、Spark 作业、SQL 脚本和存储过程
* 使用数据流活动的无代码 ETL

### <a name="unified-management-monitoring-and-security"></a>统一的管理、监视和安全性

Azure Synapse 为企业提供单一方式来管理分析资源、监视使用情况和活动，以及强制实施安全措施。

* 将用户分配到角色可以简化对分析资源的访问
* 对数据和代码进行精细的访问控制
* 可在单个仪表板中监视 SQL 与 Spark 中的资源、使用情况和用户

### <a name="synapse-studio"></a>Synapse Studio

Synapse Studio 是一个 Web 原生的体验，它将数据工程师所需的全部信息整合在一起，使他们能够在一个位置执行生成完整解决方案所需完成的每项任务。

* 在一个位置生成端到端的分析解决方案：引入、浏览、准备、协调、可视化
* 让编写 SQL 或 Spark 代码的数据工程师保持行业领先的工作效率：创作、调试和性能优化
* 与企业 CI/CD 过程集成

## <a name="next-steps"></a>后续步骤

* [创建工作区](quickstart-create-workspace.md)
* [使用 Synapse Studio](quickstart-synapse-studio.md)
* [创建 SQL 池](quickstart-create-sql-pool.md)
* [使用 SQL 按需版本](quickstart-sql-on-demand.md)
* [创建 Apache Spark 池](quickstart-create-apache-spark-pool.md)
