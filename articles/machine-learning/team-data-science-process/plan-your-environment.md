---
title: 确认方案并计划分析过程 - Azure | Microsoft Docs
description: 规划高级分析时需考虑一系列关键问题。
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 421520dd-7728-4d29-889c-ebe6a0a6fb07
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 16cc7c5841708b8b27cff4fcc7c93cdbb2fe0fa4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838327"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>如何规定高级分析数据处理的方案和计划
将环境设置为对数据集执行高级分析处理时，应计划包括哪些资源？ 本文为一系列提出的问题提供建议，有助于确定与用户方案相关的任务和资源。 [什么是 Team Data Science Process (TDSP)？](overview.md)中概述了预测分析的高级步骤顺序。 每个步骤都需要与用户特定方案相关的任务特定资源。 确定方案的关键问题涉及数据物流、特征、数据集的质量以及执行分析要使用的工具和语言。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>物流问题：数据位置和移动
物流问题涉及数据源的位置、Azure 中的目标和移动数据的要求，包括时间安排、数量和涉及的资源。 在分析过程中可能需要移动几次数据。 一种常见方案是先将本地数据移到 Azure 上某种形式的存储中，再移到机器学习工作室。

1. **数据源是什么？** 位于本地还是在云中？ 例如：
   
   * 数据可通过 HTTP 地址公开发布。
   * 数据驻留在本地/网络文件位置。
   * 数据在 SQL Server 数据库中。
   * 数据存储在 Azure 存储容器中
2. **什么是 Azure 目标？** 若要进行处理或建模，其需要位于何处？ 例如：
   
   * Azure Blob 存储
   * SQL Azure 数据库
   * Azure VM 上的 SQL Server
   * HDInsight（Azure 上的 Hadoop）或 Hive 表
   * Azure 机器学习
   * 可安装的 Azure 虚拟硬盘。
3. **计划如何移动数据？** 以下文章概述了用于将数据引入或载入不同存储和处理环境的过程和资源：
   
   * [将数据载入存储环境以进行分析](ingest-data.md)
   * [将训练数据从各种数据源导入 Azure 机器学习工作室](../studio/import-data.md)。
4. **是否需要按定期计划移动数据或在迁移期间修改数据？** 请在以下情况下考虑使用 Azure 数据工厂 (ADF)：需要连续迁移数据，尤其是对于可访问本地和云资源的混合方案；需要处理或修改数据；或者已在迁移期间添加业务逻辑。 有关详细信息，请参阅[使用 Azure 数据工厂将数据从本地 SQL Server 移到 SQL Azure](move-sql-azure-adf.md)
5. **要将多少数据移动到 Azure？** 非常大的数据集可能会超过某些环境的存储容量。 有关示例，请参阅下一部分中针对机器学习工作室大小限制的讨论。 在这种情况下，可在分析过程中使用数据的示例。 有关如何在不同 Azure 环境中向下采样数据集的详细信息，请参阅[ Team Data Science Process 中的示例数据](sample-data.md)。

## <a name="data-characteristics-questions-type-format-and-size"></a>数据特征问题：类型、格式和大小
这些问题是规划存储和处理环境的关键，这两方面适用于不同类型数据且有各自的特定限制。

1. **数据类型有哪些？** 例如：
   
   * 数值
   * 分类
   * 字符串
   * 二进制
2. **格式设置的方式是什么？** 例如：
   
   * 以逗号分隔 (CSV) 或以制表符分隔 (TSV) 的平面文件
   * 压缩或未压缩
   * Azure Blob
   * Hadoop Hive 表
   * SQL Server 表
3. **数据规模有多大？**
   
   * 小型：小于 2 GB
   * 中型：大于 2 GB 且小于 10 GB
   * 大型：大于 10 GB

以 Azure 机器学习工作室环境为例：

* 关于 Azure 机器学习工作室支持的数据格式和类型的列表，请参阅[受支持的数据格式和数据类型](../studio/import-data.md#data-formats-and-data-types-supported)部分。
* 有关 Azure 机器学习工作室的数据限制的信息，请参阅[为机器学习导入和导出数据](../studio/faq.md#machine-learning-studio-questions)的**我的模块可以使用多大的数据集？** 部分

有关分析过程中使用的其他 Azure 服务的限制的信息，请参阅 [Azure 订阅和服务限制、配额及约束](../../azure-subscription-service-limits.md)。

## <a name="data-quality-questions-exploration-and-pre-processing"></a>数据质量问题：浏览和预处理
1. **你对数据有什么了解？** 通过浏览数据了解数据的基本特征。 包括数据呈现的模式或趋势，数据的异常值是多少或者丢失了多少值。 此步骤非常重要，可用于确定所需的预处理程度、提出可建议最适合的功能或分析类型的假设，以及制定其他数据集合的计划。 计算描述性统计信息和绘制可视化效果是对数据检查有用的技术。 有关如何在不同 Azure 环境中浏览数据集的详细信息，请参阅[在 Team Data Science Process 中浏览数据](explore-data.md)。
2. **是否需要预处理或清理数据？**
   预处理和清理数据是重要的任务，通常必须先执行此任务才能有效地使用数据集进行机器学习。 原始数据通常具有干扰性且不可靠，还可能缺少值。 使用此类数据进行建模会产生误导性结果。 有关说明，请参阅[准备任务数据以增强机器学习](prepare-data.md)。

## <a name="tools-and-languages-questions"></a>工具和语言问题
有很多选项，具体取决于所需的或最喜欢的语言和开发环境或工具。

1. **希望分析时采用哪些语言？**  
   
   * R
   * Python
   * SQL
2. **应使用什么工具进行数据分析？**
   
   * [Microsoft Azure Powershell](/powershell/azure/overview) - 用于管理脚本语言中 Azure 资源的一种脚本语言。
   * [Azure 机器学习工作室](../studio/what-is-ml-studio.md)
   * [Revolution Analytics](http://www.revolutionanalytics.com/revolution-r-open)
   * [RStudio](http://www.rstudio.com)
   * [用于 Visual Studio 的 Python 工具](http://microsoft.github.io/PTVS/)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Jupyter 笔记本](http://jupyter.org/)
   * [Microsoft Power BI](http://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>确定高级分析方案
解答了上一部分中的问题后，便可确定最适合情况的方案。 [用于 Azure 机器学习中高级分析的方案](plan-sample-scenarios.md)中概述了示例方案。

