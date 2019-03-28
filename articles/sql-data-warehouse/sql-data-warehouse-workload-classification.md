---
title: SQL 数据仓库分类 |Microsoft Docs
description: 有关使用分类来管理并发性，重要性，以及计算 Azure SQL 数据仓库中的查询的资源的指导。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: c27856da0a5131f2c0e8dfd4d929b577a0a68421
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520122"
---
# <a name="sql-data-warehouse-workload-classification-preview"></a>SQL 数据仓库工作负荷分类 （预览版）

此文章介绍了 SQL 数据仓库工作负荷分类程序传入的请求分配的资源类和重要性。

> [!Note]
> 在第 2 代 SQL 数据仓库中可以使用工作负荷分类。

## <a name="classification"></a>分类

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

工作负荷管理分类允许将应用于通过分配请求的工作负荷策略[资源类](resource-classes-for-workload-management.md#what-are-resource-classes)并[重要性](sql-data-warehouse-workload-importance.md)。

虽然有多种方法可以对数据仓库工作负荷进行分类，最简单且最常见分类为负载和查询。 加载数据使用 insert、 update 和 delete 语句。  查询使用选择的数据。 数据仓库解决方案通常会加载活动，例如，分配更高的资源类具有更多资源的工作负荷策略。 不同工作负荷策略可应用到查询中，例如比较，以加载活动的重要性较低。

此外可以 subclassify 工作负载和查询负载。 细分类提供更好地控制您的工作负载。 例如，查询工作负荷可以包含的多维数据集刷新、 仪表板查询或即席查询。 你可以对每个不同的资源类或重要性设置这些查询工作负荷分类。 负载可以是有益的细分类。 大型转换可以分配给较大资源类。 可以使用较高的优先级，以确保密钥的销售数据之前的天气数据的加载程序或者社交数据馈送。

并非所有语句都归类为它们不需要的资源，或者需要重要性来影响执行。  DBCC 命令，BEGIN、 COMMIT 和 ROLLBACK TRANSACTION 语句不会被归类。

## <a name="classification-process"></a>分类过程

SQL 数据仓库中的分类立即通过将用户分配到具有分配给它使用相应的资源类的角色来实现[sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)。 通过此功能有限的特征化之外的资源类的登录请求的功能。 用于分类的更丰富方法现可通过[创建工作负荷分类器](/sql/t-sql/statements/create-workload-classifier-transact-sql)语法。  使用此语法中，SQL 数据仓库用户可以向请求分配重要性和资源类。  

> [!NOTE]
> 分类评估基于每个请求。 在单个会话中的多个请求可以以不同的方式进行分类。

## <a name="classification-precedence"></a>分类优先顺序

分类过程的一部分，优先顺序已准备就绪，若要确定哪些资源类分配。 分类基于的数据库用户优先于角色的成员身份。 如果创建的分类器用户 a 数据库用户映射到 mediumrc 资源类。 然后，将 RoleA 数据库角色 （用户 a 的成员） 映射到 largerc 资源类。 将数据库用户映射到 mediumrc 资源类的分类器将优先于将 RoleA 数据库角色映射到 largerc 资源类的分类器。

如果用户是具有不同的资源类分配，或在多个分类器中匹配的多个角色的成员，为用户提供最高资源类分配。  此行为在与现有的资源类分配行为一致。

## <a name="system-classifiers"></a>系统分类器

工作负荷分类都有系统工作负荷分类器。 系统分类器会将现有的资源类角色成员身份与普通重要性映射到资源类的资源分配中。 不能删除系统分类器。 若要查看系统分类器，可以运行以下查询：

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>混合资源类分配与分类器

系统替你创建的分类器提供用于将迁移到工作负荷分类的轻松途径。 分类优先级使用资源类角色映射，可能会导致错误分类，当你开始使用重要性创建新的分类器。

假设出现了下面这种情景：

•An 现有数据仓库具有 DBAUser 分配到 largerc 资源类角色的数据库用户。 使用 sp_addrolemember 做是资源类分配。
• 该数据仓库工作负荷管理现已更新。
• 如要测试的新分类语法 DBARole （即 DBAUser 的成员），已创建为其映射到 mediumrc 和重要性为高的分类器的数据库角色。
•When DBAUser 登录并运行一个查询，查询将分配到 largerc 级别。 因为用户将优先于角色成员身份。

若要简化故障排除的错误分类，我们建议在创建工作负荷分类器删除资源类角色映射。  下面的代码将返回现有的资源类角色成员身份。  运行[sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql)为每个成员名称返回从相应的资源类。

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

有关 SQL 数据仓库工作负荷分类和重要性的详细信息，请参阅[创建工作负荷分类器](quickstart-create-a-workload-classifier-tsql.md)并[SQL 数据仓库重要性](sql-data-warehouse-workload-importance.md)。 参阅 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) 以查看查询和分配的重要性。
