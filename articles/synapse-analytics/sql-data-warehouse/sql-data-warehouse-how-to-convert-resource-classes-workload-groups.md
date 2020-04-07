---
title: 将资源类转换为工作负荷组
description: 了解如何在 Azure SQL 数据仓库中创建一个类似于资源类的工作负荷组。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8cee874106598c7d81b923d7dd32ba91902d9326
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745188"
---
# <a name="convert-resource-classes-to-workload-groups"></a>将资源类转换为工作负荷组

工作负荷组提供用于隔离和遏制系统资源的机制。  此外，工作负荷组允许针对其中运行的请求设置执行规则。  使用查询超时执行规则可以取消失控的查询，而无需用户的干预。  本文介绍如何根据现有的资源类创建一个采用类似配置的工作负荷组。  此外，将添加一个可选的查询超时规则。

## <a name="understanding-the-existing-resource-class-configuration"></a>了解现有的资源类配置

工作负荷组需要名为 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 的参数，该参数指定分配给每个请求的总体系统资源百分比。  [资源类](resource-classes-for-workload-management.md#what-are-resource-classes)的资源分配是通过分配并发槽实现的。  若要确定 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 的值，请使用 sys.dm_workload_management_workload_groups_stats <link tbd> DMV。  例如，以下查询将返回一个可用于 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 参数，以创建类似于 staticrc40 的工作负荷组的值。

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> 工作负荷组基于总体系统资源百分比运行。  

由于工作负荷组基于总体系统资源百分比运行，因此，在纵向扩展和缩减的过程中，分配给静态资源类的、相对于总体系统资源的资源百分比会发生变化。  例如，DW1000c 上的 staticrc40 会分配 9.6% 的总体系统资源。  在 DW2000c 上，则会分配 19.2%。  无论是纵向扩展以提高并发度，还是为每个请求分配更多的资源，此模型都是类似的。

## <a name="create-workload-group"></a>创建工作负荷组

在 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 已知的情况下，可以使用 CREATE WORKLOAD GROUP <link> 语法来创建工作负荷组。  可以选择性地指定一个大于零的 `MIN_PERCENTAGE_RESOURCE` 以隔离工作负荷组的资源。  此外，可以选择性地指定小于 100 的 `CAP_PERCENTAGE_RESOURCE`，以限制工作负荷组可以消耗的资源量。  

以下示例将 `MIN_PERCENTAGE_RESOURCE` 设置为提供 9.6% 的系统资源专供 `wgDataLoads` 使用，并保证一个查询始终都能够运行。  此外，`CAP_PERCENTAGE_RESOURCE` 设置为 38.4%，并将此工作负荷组限制为四个并发请求。  `QUERY_EXECUTION_TIMEOUT_SEC` 参数设置为 3600，这样，运行超过 1 小时的任何查询将自动取消。

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>创建分类器

以前，查询到资源类的映射是通过 [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class) 实现的。  若要实现相同的功能并将请求映射到工作负荷组，请使用 [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法。  仅允许使用 sp_addrolemember 将资源映射到基于登录名的请求。  除了登录名以外，分类器还提供其他选项，例如：
    - label
    - 会话
    - 时间。以下示例分配来自 `AdfLogin` 登录名的查询，这些查询的 [OPTION LABEL](sql-data-warehouse-develop-label.md) 也设置为 `factloads` 到上面创建的工作负荷组 `wgDataLoads` 的映射。

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>使用示例查询进行测试

以下示例查询和 DMV 查询可确保正确配置工作负荷组与分类器。

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

- [工作负载隔离](sql-data-warehouse-workload-isolation.md)
- [如何创建工作负载组链接](quickstart-configure-workload-isolation-tsql.md)
