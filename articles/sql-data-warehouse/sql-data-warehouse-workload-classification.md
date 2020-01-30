---
title: 工作负荷分类
description: 有关使用分类来管理 Azure SQL 数据仓库中查询的并发性、重要性和计算资源的指南。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: ab7c8ba64057b4f27e00a2928a65de8eadc78c4b
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768829"
---
# <a name="azure-sql-data-warehouse-workload-classification"></a>Azure SQL 数据仓库工作负荷分类

本文介绍为传入请求分配资源类和重要性的 SQL 数据仓库工作负荷分类过程。

## <a name="classification"></a>分类

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

工作负荷管理分类允许通过分配[资源类](resource-classes-for-workload-management.md#what-are-resource-classes)和[重要性](sql-data-warehouse-workload-importance.md)将工作负荷策略应用到请求。

虽然有多种方法可对数据仓库工作负荷进行分类，但最简单、最常见的分类是加载和查询。 可以加载包含 insert、update 和 delete 语句的数据。  使用 select 查询数据。 数据仓库解决方案通常会提供负载活动的工作负荷策略，例如，使用更多资源分配更高的资源类。 不同的工作负荷策略可能适用于查询，如相对于负载活动的重要性较低。

你还可以 subclassify 负载和查询工作负荷。 Subclassification 使你可以更好地控制工作负荷。 例如，查询工作负荷可以包含多维数据集刷新、仪表板查询或即席查询。 可以用不同的资源类或重要性设置对其中每个查询工作负荷进行分类。 负载还可以从 subclassification 受益。 大型转换可以分配给较大的资源类。 高重要性可用于确保关键销售数据在天气数据或社交数据馈送之前是加载程序。

并非所有语句都是分类的，因为它们不需要资源或需要重要性来影响执行。  DBCC 命令、BEGIN、COMMIT 和 ROLLBACK TRANSACTION 语句未分类。

## <a name="classification-process"></a>分类过程

现在，通过将用户分配到具有分配给[sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)它的对应资源类的角色，可以在 SQL 数据仓库中进行分类。 将请求的特征化超出了资源类登录名的能力仅限于此功能。 "[创建工作负荷分类器](/sql/t-sql/statements/create-workload-classifier-transact-sql)" 语法现在提供了更丰富的分类方法。  使用此语法，SQL 数据仓库用户可以通过 `workload_group` 参数分配重要性和分配给请求的系统资源量。 

> [!NOTE]
> 分类按请求进行评估。 单个会话中的多个请求可以不同地分类。

## <a name="classification-weighting"></a>分类权重

作为分类过程的一部分，权重用于确定分配了哪个工作负荷组。  权重如下所示：

|分类器参数 |重量   |
|---------------------|---------|
|成员名称：用户      |64       |
|成员名称： ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

`membername` 参数是必需的。  但是，如果指定的成员名称是数据库用户而不是数据库角色，则用户的权重较高，因此选择了分类器。

如果用户是具有在多个分类器中分配或匹配的不同资源类的多个角色的成员，则会授予此用户最高的资源类分配。  此行为与现有资源类分配行为一致。

## <a name="system-classifiers"></a>系统分类器

工作负荷分类具有系统工作负荷分类器。 系统分类器将现有资源类角色成员身份映射到资源类资源分配，并具有普通重要性。 不能删除系统分类器。 若要查看系统分类器，你可以运行以下查询：

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>混合资源类分配与分类器

以你的名义创建的系统分类器提供迁移到工作负荷分类的简单途径。 使用具有分类优先级的资源类角色映射，可以在开始创建具有重要性的新分类器时错误分类。

请考虑下列方案：

- 现有数据仓库具有分配给 largerc 资源类角色的数据库用户 DBAUser。 资源类分配是通过 sp_addrolemember 来完成的。
- 数据仓库现已更新工作负荷管理。
- 若要测试新的分类语法，数据库角色 DBARole （DBAUser 是的成员）具有创建的分类器，以将其映射到 mediumrc 和高重要性。
- 当 DBAUser 登录并运行查询时，该查询将分配给 largerc。 因为用户优先于角色成员身份。

为了简化错误分类的故障排除，建议你在创建工作负荷分类器时删除资源类角色映射。  下面的代码返回现有资源类角色成员身份。  为从相应资源类返回的每个成员名称运行[sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) 。

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>后续步骤

- 有关创建分类器的详细信息，请参阅[创建工作负荷分类器（transact-sql）](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)。  
- 请参阅快速入门，了解如何创建工作负荷分类器[创建工作负荷分类器](quickstart-create-a-workload-classifier-tsql.md)。
- 请参阅操作指南文章，[配置工作负荷重要性](sql-data-warehouse-how-to-configure-workload-importance.md)，以及如何[管理和监视工作负荷管理](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)。
- 参阅 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) 以查看查询和分配的重要性。