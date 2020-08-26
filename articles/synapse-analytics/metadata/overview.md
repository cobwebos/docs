---
title: 共享元数据模型
description: Azure Synapse Analytics 允许不同的工作区计算引擎在其 Spark 池（预览版）、SQL 按需版本引擎（预览版）与 SQL 池之间共享数据库和表。
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: c11a0ccb08f03775a07716e6c547d849cda347dd
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387330"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Azure Synapse Analytics 共享元数据

Azure Synapse Analytics 允许不同的工作区计算引擎在其 Spark 池（预览版）和 SQL 按需版本引擎（预览版）之间共享数据库和表。

[!INCLUDE [preview](../includes/note-preview.md)]

共享支持所谓的新式数据仓库模式，可使工作区 SQL 引擎能够访问通过 Spark 创建的数据库和表。 它还允许 SQL 引擎创建自身的不与其他引擎共享的对象。

## <a name="support-the-modern-data-warehouse"></a>支持新式数据仓库

共享元数据模型通过以下方式支持新式数据仓库模式：

1. 通过将准备好的数据存储在（可能已分区的）基于 Parquet 的表（可能包含在多个数据库中）中，使用 Spark 有效地准备和构建 Data Lake 中的数据。

2. Spark 创建的数据库及其所有表将显示在任何 Azure Synapse 工作区 Spark 池实例中，并可从任何 Spark 作业使用。 使用此功能需要拥有相应的[权限](#security-model-at-a-glance)，因为工作区中的所有 Spark 池共享同一个基础目录元存储。

3. Spark 创建的数据库及其基于 Parquet 的表将显示在工作区 SQL 按需版本引擎中。 [数据库](database.md)将在 SQL 按需版本元数据中自动创建，而通过 Spark 作业创建的[外部表和托管表](table.md)可以外部表的形式在相应数据库的 `dbo` 架构内的 SQL 按需版本元数据中供访问。 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

对象同步以异步方式发生。 对象将在略微延迟几秒后出现在 SQL 上下文中。 这些对象出现后即可供查询，但有权访问这些对象的 SQL 引擎不能更新或更改它们。

## <a name="shared-metadata-objects"></a>共享元数据对象

Spark 允许创建数据库、外部表、托管表和视图。 由于 Spark 视图要求通过 Spark 引擎处理 Spark SQL 定义语句，而不能由 SQL 引擎来处理，因此，只会与工作区 SQL 引擎共享数据库及其包含的使用 Parquet 存储格式的外部表和托管表。 Spark 视图仅在 Spark 池实例之间共享。

## <a name="security-model-at-a-glance"></a>安全模型概览

Spark 数据库和表及其在 SQL 引擎中的已同步表示形式在基础存储级别受到保护。 当查询提交者有权使用的任何引擎查询表时，会将查询提交者的安全主体传递到基础文件。 将在文件系统级别检查权限。

有关详细信息，请参阅 [Azure Synapse Analytics 共享数据库](database.md)。

## <a name="change-maintenance"></a>更改维护

如果使用 Spark 删除或更改了元数据对象，系统会拾取更改，并将其传播到 SQL 按需版本引擎。 同步以异步方式进行，在短暂的延迟后，SQL 引擎中即会反映更改。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure Synapse Analytics 的共享元数据数据库](database.md)
- [详细了解 Azure Synapse Analytics 的共享元数据表](table.md)

