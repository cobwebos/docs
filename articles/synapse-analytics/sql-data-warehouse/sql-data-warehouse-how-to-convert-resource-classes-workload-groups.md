---
title: 将资源类转换为工作负荷组
description: 了解如何创建与 Azure SQL 数据仓库中的资源类相似的工作负荷组。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c61e8df05c4bc199c0d91b8ed0cbd73fa6f196cf
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192313"
---
# <a name="convert-resource-classes-to-workload-groups"></a>将资源类转换为工作负荷组

工作负荷组提供隔离和包含系统资源的机制。  此外，工作负荷组让你能够为在其中运行的请求设置执行规则。  查询超时执行规则允许取消失控查询，而无需用户干预。  本文介绍如何获取现有资源类并创建具有类似配置的工作负荷组。  此外，本文中还添加了一个可选的查询超时规则。

> [!NOTE]
> 有关同时使用工作负荷组和资源类的指南，请参阅[工作负荷分类](sql-data-warehouse-workload-classification.md)概念文档中的[将资源类分配和分类器混合使用](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers)部分。

## <a name="understanding-the-existing-resource-class-configuration"></a>了解现有的资源类配置

工作负荷组需要名为 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 的参数，该参数指定分配给每个请求的总体系统资源百分比。  [资源类](resource-classes-for-workload-management.md#what-are-resource-classes)的资源分配是通过分配并发槽完成的。  若要确定为 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 指定的值，请使用 sys.dm_workload_management_workload_groups_stats <link tbd> DMV。  例如，以下查询将返回一个值，该值可用于 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 参数，以创建类似于 staticrc40 的工作负荷组。

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> 工作负荷组基于总体系统资源百分比运行。  

由于工作负荷组根据总体系统资源百分比运行，因此在纵向扩展和缩减的过程中，分配给静态资源类的资源相对于总体系统资源的百分比会发生变化。  例如，DW1000c 上的 staticrc40 会分配到 19.2% 的总体系统资源。  在 DW2000c，则会分配 9.6%。  无论是纵向扩展以提高并发度，还是为每个请求分配更多的资源，此模型都是类似的。

## <a name="create-workload-group"></a>创建工作负荷组

在 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 已知的情况下，可以使用 CREATE WORKLOAD GROUP <link> 语法来创建工作负荷组。  可以选择指定大于零的 `MIN_PERCENTAGE_RESOURCE` 以隔离工作负荷组的资源。  此外，还可以选择指定小于 100 的 `CAP_PERCENTAGE_RESOURCE`，以限制工作负荷组可以使用的资源量。  

使用 mediumrc 作为示例的基础，以下代码将设置 `MIN_PERCENTAGE_RESOURCE` 为专用于10% 的系统资源， `wgDataLoads` 并保证一个查询能够运行所有时间。  此外， `CAP_PERCENTAGE_RESOURCE` 设置为40%，并将此工作负荷组限制为四个并发请求。  通过将 `QUERY_EXECUTION_TIMEOUT_SEC` 参数设置为 3600，运行超过 1 小时的任何查询都将自动取消。

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 10
 ,MIN_PERCENTAGE_RESOURCE = 10
 ,CAP_PERCENTAGE_RESOURCE = 40
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>创建分类器

以前，查询到资源类的映射是使用 [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class) 完成的。  要实现相同的功能并将请求映射到工作负荷组，请使用 [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法。  使用 sp_addrolemember 仅允许你根据登录名将资源映射到请求。  分类器提供登录名之外的其他选项，例如：
    - label
    - 会话
    - 时间。下面的示例分配来自 `AdfLogin` 登录名的查询（这些查询的 [OPTION LABEL](sql-data-warehouse-develop-label.md) 也设置为 `factloads`）分配到上面创建的工作负荷组 `wgDataLoads`。

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>使用示例查询进行测试

下面是一个示例查询和 DMV 查询，可用于确保正确配置工作负荷组与分类器。

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>后续步骤

- [工作负荷隔离](sql-data-warehouse-workload-isolation.md)
- [如何创建工作负荷组](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?&view=azure-sqldw-latest)
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
