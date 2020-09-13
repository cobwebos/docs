---
title: Synapse SQL 中用户定义的架构
description: 在下面的部分中，你将找到有关使用 T-sql 用户定义的架构来开发使用 Azure Synapse Analytics 的 Synapse SQL 功能的解决方案的各种技巧。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: aebe1d995f3cb6da4663876b8d39d36a1a8b16c9
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030161"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Synapse SQL 中用户定义的架构

在下面的部分中，你将发现使用 T-sql 用户定义的架构在 Synapse SQL 中开发解决方案的各种技巧。

## <a name="schemas-for-application-boundaries"></a>应用程序边界的架构

传统分析体系结构通常使用不同的数据库，根据工作负荷、域或安全性来创建应用程序边界。 例如，传统的 SQL Server 分析基础结构可能包括过渡数据库、分析数据库和数据市场数据库。 在此拓扑中，每个数据库均作为体系结构中的工作负载和安全边界来运行。

相反，Synapse SQL 将在一个数据库中运行整个分析工作负荷。 不允许跨数据库联接。 Synapse SQL 要求仓库使用的所有表都存储在一个数据库中。

> [!NOTE]
> SQL 池不支持任何种类的跨数据库查询。 因此，需要修改利用此模式的分析实现。 SQL 点播 (预览版) 支持跨数据库查询。

## <a name="user-defined-schema-recommendations"></a>用户定义的架构建议

其中包括使用用户定义的架构合并工作负荷、安全性、域和功能边界的建议：

- 使用一个数据库来运行整个分析工作负荷。
- 合并现有分析环境以使用一个数据库。
- 利用 **用户定义的架构** 来提供以前使用数据库实现的边界。

如果以前未使用过用户定义的架构，则可以使用一次干净的。 使用旧数据库名称作为你的 Synapse SQL 数据库中用户定义架构的基础。

如果已使用架构，则可采用以下几个选项：

- 删除旧架构名称并重新开始
- 保留旧架构名称，方法是将旧架构名称预先挂起到表名
- 保留旧架构名称，方法是在其他架构中通过对表实现视图来重新创建旧架构结构。

> [!NOTE]
> 第一次检查时，选项3可能看起来是最具吸引力的选择。 视图在 Synapse SQL 中是只读的。 任何表修改或数据修改只能针对基础表执行。 选项 3 还在系统中引入了一个视图层。 如果已在体系结构中使用视图，则可能要为此进行一些其他思考。
> 
> 

### <a name="examples"></a>示例

基于数据库名称实现用户定义的架构。

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

保留旧架构名称，方法是将它们预先挂起到表名中。 使用工作负荷边界的架构。

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

使用视图保留旧架构名称。

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> 架构策略中的任何更改都需要查看数据库的安全模型。 在许多情况下，可以在架构级别分配权限，以简化安全模型。

如果需要更精细的权限，可以使用数据库角色。 有关数据库角色的详细信息，请参阅 [管理数据库角色和用户](../../analysis-services/analysis-services-database-users.md) 一文。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [Synapse SQL 开发概述](develop-overview.md)。
