---
title: 将资源类转换为工作负荷组
description: 了解如何创建一个与 Azure SQL 数据仓库中的资源类类似的工作负荷组。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5708dccce5f7cdcc33fe6bfca980126cd8b5ee7f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685706"
---
# <a name="convert-resource-classes-to-workload-groups"></a>将资源类转换为工作负荷组
工作负荷组提供一种隔离和包含系统资源的机制。  此外，工作负荷组允许您为在其中运行的请求设置执行规则。  查询超时执行规则允许在没有用户干预的情况下取消失控的查询。  本文介绍如何使用现有的资源类并创建具有类似配置的工作负荷组。  此外，还添加了一个可选的查询超时规则。

## <a name="understanding-the-existing-resource-class-configuration"></a>了解现有的资源类配置
工作负荷组需要一个名为 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 的参数，该参数指定每个请求分配的总体系统资源的百分比。  分配并发槽即可为[资源类](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes)分配资源。  若要确定指定 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`的值，请使用 _workload_management_workload_groups_stats <link tbd> DMV。  例如，下面的查询查询将返回一个值，该值可用于 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 参数以创建类似于 staticrc40 的工作负荷组。   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> 工作负荷组基于总体系统资源的百分比进行操作。  

由于工作负荷组基于总体系统资源的百分比进行操作，因此，随着增加和减少，分配给静态资源类的资源百分比相对于整个系统资源发生变化。  例如，DW1000c 上的 staticrc40 分配总体系统资源的9.6%。  在 DW2000c，分配19.2%。  如果希望增加并发性，而不是为每个请求分配更多资源，则此模型类似。   

## <a name="create-workload-group"></a>创建工作负荷组
使用已知的 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`，可以使用 CREATE 工作负荷组 <link> 语法来创建工作负荷组。  您可以选择指定一个大于零的 `MIN_PERCENTAGE_RESOURCE` 以隔离工作负荷组的资源。  此外，还可以选择指定小于100的 `CAP_PERCENTAGE_RESOURCE`，以限制工作负荷组可以使用的资源量。  

下面的示例将 `MIN_PERCENTAGE_RESOURCE` 设置为将9.6% 的系统资源专用于 `wgDataLoads`，保证一个查询能够运行所有时间。  此外，`CAP_PERCENTAGE_RESOURCE` 设置为38.4%，并将此工作负荷组限制为四个并发请求。  通过将 `QUERY_EXECUTION_TIMEOUT_SEC` 参数设置为3600，将自动取消运行超过1小时的任何查询。

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>创建分类器
以前，对资源类的查询映射是通过[sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class)完成的。  若要获得相同的功能并将请求映射到工作负荷组，请使用[CREATE 工作负荷分类器](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)语法。  仅允许使用 sp_addrolemember 将资源映射到基于登录名的请求。  分类器除了登录外，还提供其他选项，例如：
    - label
    - 会话
    - 时间下面的示例将 `AdfLogin` 登录名的查询分配给上述工作负荷组 `wgDataLoads` 的[选项标签](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label)设置为 `factloads`。

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```
## <a name="test-with-a-sample-query"></a>使用示例查询进行测试
下面的示例查询和 DMV 查询可确保正确配置工作负荷组和分类器。

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

工作负荷隔离-链接 tbd 如何创建工作负荷组-链接 tbd