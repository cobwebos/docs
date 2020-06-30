---
title: SQL 按需版本预览版自助信息
description: 本部分包含的信息可帮助你排查 SQL 按需版本（预览版）的问题。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 7a6b145e9a1efb29bbb6c233f2a09498b4a4ea7f
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213119"
---
# <a name="self-help-for-sql-on-demand-preview"></a>SQL 按需版本（预览版）的自助信息

本文介绍如何排查 Azure Synapse Analytics 中的 SQL 按需版本（预览版）的最常见问题。

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>SQL 按需版本在 Synapse Studio 中灰显

如果 Synapse Studio 无法建立到 SQL 按需版本的连接，你会看到 SQL 按需版本是灰显的，或者显示“脱机”状态。 通常，当发生下列情况之一时，会出现此问题：

1) 你的网络阻止了与 Azure Synapse 后端的通信。 最常见的情况是端口 1443 被阻止。 若要使 SQL 按需版本运行，请解除阻止此端口。 其他问题可能也会阻止 SQL 按需版本运行，[有关详细信息，请访问完整的故障排除指南](../troubleshoot/troubleshoot-synapse-studio.md)。
2) 你无权登录到 SQL 按需版本。 若要获取访问权限，可以要求某位 Azure Synapse 工作区管理员将你添加到工作区管理员或 SQL 管理员角色。 [有关详细信息，请参阅关于访问控制的完整指南](access-control.md)。

## <a name="query-fails-because-file-cannot-be-opened"></a>查询失败，因为无法打开文件

如果查询失败并出现“无法打开文件，因为该文件不存在或正被另一个进程使用”错误，但你确定文件存在且未被其他进程使用，则表明 SQL 按需版本无法访问该文件。 发生此问题的原因通常是你的 Azure Active Directory 标识无权访问该文件。 默认情况下，SQL 按需版本尝试使用 Azure Active Directory 标识来访问文件。 若要解决此问题，你需要有访问该文件的适当权限。 最简单的方法是在要查询的存储帐户上向自己授予“存储 Blob 数据参与者”角色。 [有关详细信息，请参阅关于 Azure Active Directory 访问控制（针对存储）的完整指南](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>查询失败，原因是当前的资源约束导致查询无法执行 

如果查询失败并出现错误消息“由于当前的资源约束，无法执行此查询”，则表示由于资源约束，SQL 按需版本此时无法执行它： 

- 请确保使用大小合理的数据类型。 另外，请为字符串列指定 Parquet 文件的架构，因为它们默认情况下将是 VARCHAR(8000)。 

- 如果你的查询针对 CSV 文件，请考虑[创建统计信息](develop-tables-statistics.md#statistics-in-sql-on-demand-preview)。 

- 若要优化查询，请参阅[适用于 SQL 按需版本的性能最佳做法](best-practices-sql-on-demand.md)。  

## <a name="create-statement-is-not-supported-in-master-database"></a>主数据库不支持 CREATE 'STATEMENT' 语句

如果查询失败并显示以下错误消息：

> “未能执行查询。 错误：主数据不支持 CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT。” 

这意味着 SQL 按需版本的主数据库不支持创建以下内容：
  - 外部表
  - 外部数据源
  - 数据库范围的凭据
  - 外部文件格式

解决方案：

  1. 创建用户数据库：

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. 在 <DATABASE_NAME> 的上下文中执行 create 语句，该语句之前在主数据库中失败。 
  
  创建外部文件格式的示例：
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 SQL 按需版本，请参阅以下文章：

- [查询单个 CSV 文件](query-single-csv-file.md)

- [查询文件夹和多个 CSV 文件](query-folders-multiple-csv-files.md)

- [查询特定的文件](query-specific-files.md)

- [查询 Parquet 文件](query-parquet-files.md)

- [查询 Parquet 嵌套类型](query-parquet-nested-types.md)

- [查询 JSON 文件](query-json-files.md)

- [创建和使用视图](create-use-views.md)

- [创建和使用外部表](create-use-external-tables.md)

- [将查询结果存储到存储中](create-external-table-as-select.md)
