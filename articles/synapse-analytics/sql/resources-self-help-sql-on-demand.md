---
title: SQL 按需版本预览版自助信息
description: 本部分包含的信息可帮助你排查 SQL 按需版本（预览版）的问题。
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421191"
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

如果查询失败，并且出现“由于当前的资源约束，无法执行此查询”错误消息，则意味着由于资源约束，SQL OD 此时无法执行它： 

- 请确保使用大小合理的数据类型。 另外，请为字符串列指定 Parquet 文件的架构，因为它们默认情况下将是 VARCHAR(8000)。 

- 如果你的查询针对 CSV 文件，请考虑[创建统计信息](develop-tables-statistics.md#statistics-in-sql-on-demand-preview)。 

- 若要优化查询，请参阅[适用于 SQL 按需版本的性能最佳做法](best-practices-sql-on-demand.md)。  

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
