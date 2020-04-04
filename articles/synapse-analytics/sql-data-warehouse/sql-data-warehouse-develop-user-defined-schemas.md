---
title: 使用用户定义的架构
description: 使用 T-SQL 用户定义的架构在 Synapse SQL 池中开发解决方案的提示。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7144fa75d156ca7aed9d8215592f89c167cfb221
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633455"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Synapse SQL 池中用户定义的架构
本文重点介绍使用 T-SQL 用户定义的架构在 Synapse SQL 池中开发解决方案的一些提示。

## <a name="schemas-for-application-boundaries"></a>应用程序边界的架构

传统数据仓库通常使用不同的数据库，根据工作负荷、域或安全性来创建应用程序边界。 

例如，传统的 SQL Server 数据仓库可能包括暂存数据库、数据仓库数据库和某些数据集市数据库。 在此拓扑中，每个数据库在体系结构中作为工作负载和安全边界运行。

相比之下，SQL 池在一个数据库中运行整个数据仓库工作负载。 不允许跨数据库联接。 SQL 池希望仓库使用的所有表都存储在一个数据库中。

> [!NOTE]
> SQL 池不支持任何类型的跨数据库查询。 因此，需要修改利用此模式的数据仓库实现。
> 
> 

## <a name="recommendations"></a>建议
以下是使用用户定义的架构整合工作负载、安全性、域和功能边界的建议：

- 使用一个 SQL 池数据库运行整个数据仓库工作负荷。
- 整合现有数据仓库环境以使用一个 SQL 池数据库。
- 利用**用户定义架构**可以提供以前使用数据库实现的边界。

如果以前未使用用户定义的架构，则您有一个干净的石板。 使用旧数据库名称作为 SQL 池数据库中用户定义的架构的基础。

如果架构已被使用，则有几个选项：

- 删除旧架构名称并重新开始。
- 通过将旧架构名称预挂起到表名称来保留旧架构名称。
- 在额外架构中的表上实现视图来重建旧架构结构，以保留旧架构名称。

> [!NOTE]
> 在首次检查时，选项 3 似乎像是最吸引人的选项。 但是，细节决定成败。 视图仅在 SQL 池中读取。 任何表修改或数据修改只能针对基础表执行。 选项 3 还在系统中引入了一个视图层。 如果已在体系结构中使用视图，可以再三考虑一下此选项。
> 
> 

### <a name="examples"></a>示例：
基于数据库名称实现用户定义的架构：

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

通过预先挂起到表名称来保留旧架构名称。 对工作负载边界使用架构：

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

使用视图保留旧架构名称：

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
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
> 如果架构策略发生任何更改，则需要检查数据库的安全模型。 在许多情况下，您可以通过在架构级别分配权限来简化安全模型。 如果需要更精细的权限，则可以使用数据库角色。
> 
> 

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。

