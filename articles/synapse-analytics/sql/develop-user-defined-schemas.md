---
title: Synapse SQL 中用户定义的架构
description: 在以下各节中，您将找到使用 T-SQL 用户定义的架构使用 Azure 突触分析的突触 SQL 功能开发解决方案的各种提示。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428701"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Synapse SQL 中用户定义的架构

在以下各节中，您将找到使用 T-SQL 用户定义的架构在 Synapse SQL 中开发解决方案的各种提示。

## <a name="schemas-for-application-boundaries"></a>应用程序边界的架构

传统的分析体系结构通常使用单独的数据库来根据工作负载、域或安全性创建应用程序边界。 例如，传统的 SQL Server 分析基础结构可能包括暂存数据库、分析数据库和数据集市数据库。 在此拓扑中，每个数据库在体系结构中作为工作负载和安全边界运行。

相反，Synapse SQL 在一个数据库中运行整个分析工作负荷。 不允许跨数据库联接。 Synapse SQL 期望仓库使用的所有表都存储在一个数据库中。

> [!NOTE]
> SQL 池不支持任何类型的跨数据库查询。 因此，需要修订利用此模式的分析实现。 SQL 按需（预览）支持跨数据库查询。

## <a name="user-defined-schema-recommendations"></a>用户定义的架构建议

其中包括使用用户定义的架构整合工作负载、安全性、域和功能边界的建议：

- 使用一个数据库运行整个分析工作负荷。
- 整合现有分析环境以使用一个数据库。
- 利用**用户定义架构**可以提供以前使用数据库实现的边界。

如果以前未使用用户定义的架构，则您拥有干净的石板。 使用旧数据库名称作为 Synapse SQL 数据库中用户定义的架构的基础。

如果架构已被使用，则有几个选项：

- 删除旧架构名称并重新开始
- 通过将旧架构名称预先挂起到表名称来保留旧架构名称
- 通过在额外的架构中实现对表的视图来保留旧架构名称，从而重新创建旧的架构结构。

> [!NOTE]
> 在第一次检查时，选项 3 可能看起来是最具吸引力的选择。 视图仅在 Synapse SQL 中读取。 任何表修改或数据修改只能针对基础表执行。 选项 3 还在系统中引入了一个视图层。 如果您已经在体系结构中使用视图，则可能需要对此进行额外的考虑。
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

通过将旧架构名称预先挂起到表名称来保留它们。 使用工作负荷边界的架构。

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
> 架构策略的任何更改都需要检查数据库的安全模型。 在许多情况下，您可以通过在架构级别分配权限来简化安全模型。

如果需要更精细的权限，则可以使用数据库角色。 有关数据库角色的详细信息，请参阅[管理数据库角色和用户](../../analysis-services/analysis-services-database-users.md)一文。

## <a name="next-steps"></a>后续步骤

有关更多开发提示，请参阅[Synapse SQL 开发概述](develop-overview.md)。
