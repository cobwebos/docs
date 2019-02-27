---
title: 确定方案并计划分析过程 - Team Data Science Process | Azure 机器学习
description: 通过考虑一系列关键问题，确定高级分析数据处理的方案和计划。
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
ms.openlocfilehash: a8ca08d93992a6e1bfe8ae24b83354503154ef31
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416150"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>如何规定高级分析数据处理的方案和计划

若要创建可以对数据集执行高级分析处理的环境，需要什么资源？ 本文为一系列提出的问题提供建议，可以帮助确定与用户方案相关的任务和资源。

若要了解预测分析的大概步骤的顺序，请参阅[什么是 Team Data Science Process (TDSP)](overview.md)。 每个步骤都需要与用户特定方案相关的任务特定资源。

请回答以下方面的关键问题来标识你的方案：

* 数据物流
* 数据特征
* 数据集质量
* 首选的工具和语言

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>物流问题：数据位置和移动

物流问题涉及以下各项：

* 数据源位置
* Azure 中的目标位置
* 移动数据的要求，包括日程安排、数量和涉及的资源

在分析过程中，可能需要多次移动数据。 一种常见方案是先将本地数据移到 Azure 上某种形式的存储中，再移到机器学习工作室。

### <a name="what-is-your-data-source"></a>你的数据源是什么？

你的数据位于本地还是在云中？ 可能的位置包括：

* 公开可用的 HTTP 地址
* 本地或网络文件位置
* SQL Server 数据库
* Azure 存储容器

### <a name="what-is-the-azure-destination"></a>Azure 目标是什么？

若要进行处理或建模，数据需要位于何处？ 

* Azure Blob 存储
* SQL Azure 数据库
* Azure VM 上的 SQL Server
* HDInsight（Azure 上的 Hadoop）或 Hive 表
* Azure 机器学习
* 可装载的 Azure 虚拟硬盘

### <a name="how-are-you-going-to-move-the-data"></a>你打算如何移动数据？

有关用于将数据引入或载入不同存储和处理环境的过程和资源，请参阅：

* [将数据载入存储环境以进行分析](ingest-data.md)
* [将训练数据从各种数据源导入 Azure 机器学习工作室](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>是否需要按定期计划移动数据或在迁移期间修改数据？

当需要持续迁移数据时，请考虑使用 Azure 数据工厂 (ADF)。 在下列场景下，ADF 可能比较有用：

* 同时涉及本地和云资源的混合场景
* 在迁移过程中业务逻辑会处理、修改或更改数据的场景

有关详细信息，请参阅[使用 Azure 数据工厂将数据从本地 SQL Server 移动到 SQL Azure](move-sql-azure-adf.md)。

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>要将多少数据移动到 Azure？

非常大的数据集可能会超过某些环境的存储容量。 有关示例，请参阅下一部分中针对机器学习工作室大小限制的讨论。 在这种情况下，在分析过程中可以使用一个数据样本。 有关如何在不同 Azure 环境中向下采样数据集的详细信息，请参阅[ Team Data Science Process 中的示例数据](sample-data.md)。

## <a name="data-characteristics-questions-type-format-and-size"></a>数据特征问题：类型、格式和大小

这些问题是规划存储和处理环境的关键。 它们可以帮助你为数据类型选择合适的方案，并了解任何限制。

### <a name="what-are-the-data-types"></a>数据类型有哪些？

* 数值
* 分类
* 字符串
* 二进制

### <a name="how-is-your-data-formatted"></a>数据格式是如何设置的？

* 以逗号分隔 (CSV) 或以制表符分隔 (TSV) 的平面文件
* 压缩或未压缩
* Azure Blob
* Hadoop Hive 表
* SQL Server 表

### <a name="how-large-is-your-data"></a>数据规模有多大？

* 小型：小于 2 GB
* 中型：大于 2 GB 且小于 10 GB
* 大型：大于 10 GB

以 Azure 机器学习工作室环境为例：

* 关于 Azure 机器学习工作室支持的数据格式和类型的列表，请参阅[受支持的数据格式和数据类型](../studio/import-data.md#supported-data-formats-and-data-types)部分。
* 有关分析过程中使用的其他 Azure 服务的限制的信息，请参阅 [Azure 订阅和服务限制、配额及约束](../../azure-subscription-service-limits.md)。

## <a name="data-quality-questions-exploration-and-pre-processing"></a>数据质量问题：浏览和预处理

### <a name="what-do-you-know-about-your-data"></a>你对你的数据了解多少？

了解你的数据的基本特征：

* 它呈现出什么模式或趋势
* 它有哪些离群值
* 缺少多少值

此步骤非常重要，可以帮助你：

* 确定需要执行什么程度的预处理
* 构建提出最合适的功能或分析类型建议的假设
* 构建用于收集附加数据的计划

用于检查数据的有用技术包括描述性统计计算和可视化效果绘图。 有关如何在不同 Azure 环境中浏览数据集的详细信息，请参阅[在 Team Data Science Process 中浏览数据](explore-data.md)。

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>数据是否需要进行预处理或清理？

你可能需要预处理并清理数据，然后才能有效地将数据集用于机器学习。 原始数据通常包含干扰项并且不可靠。 它可能缺少值。 使用此类数据进行建模会产生误导性结果。 有关说明，请参阅[准备任务数据以增强机器学习](prepare-data.md)。

## <a name="tools-and-languages-questions"></a>工具和语言问题

有许多语言、开发环境和工具可供选择。 请注意你的需求和偏好。

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>希望使用什么语言进行分析？

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>应使用什么工具进行数据分析？

* [Microsoft Azure Powershell](/powershell/azure/overview) - 用于管理脚本语言中 Azure 资源的一种脚本语言
* [Azure 机器学习工作室](../studio/what-is-ml-studio.md)
* [Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](http://www.rstudio.com)
* [用于 Visual Studio 的 Python 工具](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Jupyter 笔记本](http://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>确定高级分析方案

解答了上一部分中的问题后，便可确定最适合情况的方案。 [用于 Azure 机器学习中高级分析的方案](plan-sample-scenarios.md)中概述了示例方案。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [什么是 Team Data Science Process (TDSP)？](overview.md)