---
title: 在 Spark 池（预览版）与 SQL 池之间导入和导出数据
description: 本文介绍如何使用自定义连接器在 SQL 池与 Spark 池（预览版）之间来回移动数据。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420421"
---
# <a name="introduction"></a>简介

Spark SQL Analytics 连接器设计用来高效地在 Azure Synapse 中的 Spark 池（预览版）与 SQL 池之间传输数据。 Spark SQL Analytics 连接器仅适用于 SQL 池，不适用于 SQL 按需版本。

## <a name="design"></a>设计

可以使用 JDBC 来执行 Spark 池与 SQL 池之间的数据传输。 但是，假设有两个分布式系统（例如 Spark 池和 SQL 池），则 JDBC 往往会成为串行数据传输的瓶颈。

Spark 池到 SQL Analytics 连接器是适用于 Apache Spark 的一个数据源实现。 它使用 Azure Data Lake Storage Gen 2 以及 SQL 池中的 Polybase 高效地在 Spark 群集与 SQL Analytics 实例之间传输数据。

![连接器体系结构](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的身份验证

系统之间的身份验证在 Azure Synapse Analytics 中无缝进行。 有一个令牌服务，它与 Azure Active Directory 进行连接来获取在访问存储帐户或数据仓库服务器时使用的安全令牌。 因此，只要在存储帐户和数据仓库服务器上配置了 AAD 身份验证，就不需要创建凭据或在连接器 API 中指定凭据。 如果没有，则可以指定 SQL 身份验证。 可在[用法](#usage)部分中找到更多详细信息。

## <a name="constraints"></a>约束

- 此连接器仅适用于 Scala。

## <a name="prerequisites"></a>先决条件

- 在需要向其/从其传输数据的数据库/SQL 池中具有 db_exporter  角色。

若要创建用户，请连接到数据库，然后按照以下示例操作：

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

若要分配角色，请使用以下命令：

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>用法

不需要提供 import 语句，对于笔记本体验，它们是预先导入的。

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>将数据传输到与工作区相连的逻辑服务器（数据仓库实例）中的 SQL 池，或者从该池向外传输数据

> [!NOTE]
> **在笔记本体验中，import 不是必需的**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>读取 API

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

上述 API 适用于 SQL 池中的内部（托管）表以及外部表。

#### <a name="write-api"></a>写入 API

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

其中的 TableType 可以是 Constants.INTERNAL 或 Constants.EXTERNAL

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

向 Azure 存储和 SQL Server 进行的身份验证已完成

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>如果将数据传输到工作区外部的逻辑服务器中的 SQL 池或数据库，或者从该池或数据库向外传输数据

> [!NOTE]
> 在笔记本体验中，import 不是必需的

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>读取 API

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>写入 API

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>使用 SQL 身份验证而非 AAD

#### <a name="read-api"></a>读取 API

目前，该连接器不支持对工作区外部的 SQL 池使用基于令牌的身份验证。 需要使用 SQL 身份验证。

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>写入 API

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>使用 PySpark 连接器

> [!NOTE]
> 在给出此示例时，只考虑了笔记本体验。

假设你有一个要写入到数据仓库的数据帧“pyspark_df”。

在 PySpark 中使用此数据帧创建一个临时表

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

使用 magic 在 PySpark 笔记本中运行 Scala 单元格

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
同样，在读取方案中，使用 Scala 读取数据并将其写入到一个临时表中，在 PySpark 中使用 Spark SQL 查询该临时表并将结果写入一个数据帧。

## <a name="next-steps"></a>后续步骤

- [创建 SQL 池]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [为 Azure Synapse Analytics 工作区创建新的 Apache Spark 池](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 