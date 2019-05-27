---
title: SQL 数据仓库分类 | Microsoft Docs
description: 有关使用分类管理 Azure SQL 数据仓库中查询的并发性、重要性和计算资源的指导。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: c4630e11ef4001c546aecfe74dbced346b6d30e4
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000117"
---
# <a name="sql-data-warehouse-workload-classification"></a>SQL 数据仓库工作负荷分类

本文介绍用于向传入请求分配资源类和重要性的 SQL 数据仓库工作负荷分类过程。

## <a name="classification"></a>分类

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

使用工作负荷管理分类可以通过分配[资源类](resource-classes-for-workload-management.md#what-are-resource-classes)和[重要性](sql-data-warehouse-workload-importance.md)对请求应用工作负荷策略。

可通过多种方法来分类数据仓库工作负荷，而最简单且最常用的分类方法是加载和查询。 可以使用 insert、update 和 delete 语句加载数据。  可以使用 select 查询数据。 数据仓库解决方案通常对加载活动使用工作负荷策略，例如，分配具有更多资源的更高资源类。 可对查询应用不同的工作负荷策略，例如，分配比加载活动更低的重要性。

还可以将加载和查询工作负荷进一步分类。 进一步分类能够更好地控制工作负荷。 例如，查询工作负荷可能包括多维数据集刷新、仪表板查询或即席查询。 可以使用不同的资源类或重要性设置将其中的每个查询工作负荷分类。 加载活动也可以受益于进一步分类。 可将大型转换分配到较大的资源类。 可以使用较高的重要性来确保先加载重要销售数据，再加载天气数据或社交数据馈送。

不会对所有语句分类，因为它们不需要会影响执行的资源或重要因素。  DBCC 命令、BEGIN、COMMIT 和 ROLLBACK TRANSACTION 语句不会进行分类。

## <a name="classification-process"></a>分类过程

目前，SQL 数据仓库中的分类是通过将用户分配到某个角色来实现的，该角色具有一个使用 [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) 分配的相应资源类。 使用此功能时，将请求特征化，使之超出资源类登录范围的能力会受到限制。 现在，可以通过 [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql) 语法来利用更丰富的分类方法。  SQL 数据仓库用户可以使用此语法向请求分配重要性和资源类。  

> [!NOTE]
> 分类是按每个请求评估的。 可以不同的方式对单个会话中的多个请求进行分类。

## <a name="classification-precedence"></a>分类过程

在分类过程中，将使用优先顺序来确定要分配哪个资源类。 基于数据库用户的分类优先于角色成员身份。 如果创建一个将 UserA 数据库用户映射到 mediumrc 资源类的分类器， 请将 RoleA 数据库角色（UserA 是其成员）映射到 largerc 资源类。 将数据库用户映射到 mediumrc 资源类的分类器优先于将 RoleA 数据库角色映射到 largerc 资源类的分类器。

如果某个用户是多个角色的成员，并且这些角色分配有不同的资源类或者在多个分类器中相匹配，则会为该用户分配最高的资源类。  此行为与现有的资源类分配行为保持一致。

## <a name="system-classifiers"></a>系统分类器

工作负荷分类采用系统工作负荷分类器。 系统分类器将现有的资源类角色成员身份映射到具有一般重要性的资源类资源分配。 无法删除系统分类器。 若要查看系统分类器，可运行以下查询：

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>混合使用资源类分配和分类器

使用自动创建的系统分类器能够轻松迁移到工作负荷分类。 开始创建具有重要性的新分类器时，使用具有分类优先顺序的资源类角色映射可能会导致错误分类。

请考虑以下方案：

- 某个现有的数据仓库包含已分配到 largerc 资源类角色的数据库用户 DBAUser。 资源类分配是使用 sp_addrolemember 进行的。
- 现已使用工作负荷管理更新该数据仓库。
- 为了测试新的分类语法，为数据库角色 DBARole（DBAUser 是其成员）创建了一个分类器（用于将用户映射到 mediumrc），并且该角色具有较高的重要性。
- 当 DBAUser 登录并运行查询时，该查询将分配到 largerc， 因为用户优先于角色成员身份。

为了简化分类错误的排查，我们建议在创建工作负荷分类器时删除资源类角色映射。  以下代码返回现有的资源类角色成员身份。  针对相应资源类返回的每个成员名称运行 [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql)。

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

- 创建分类器的详细信息，请参阅[创建工作负荷分类器 (Transact SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)。  
- 有关如何创建工作负荷分类器的信息，请参阅快速入门[创建工作负荷分类器](quickstart-create-a-workload-classifier-tsql.md)。
- 请参阅操作指南文章向[配置工作负荷重要性](sql-data-warehouse-how-to-configure-workload-importance.md)和如何[管理和监视工作负荷管理](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)。
- 参阅 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) 以查看查询和分配的重要性。