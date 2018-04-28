---
title: 具有 Azure SQL 数据库和 SQL Server 的 Spark 连接器| Microsoft Docs
description: 了解如何使用适用于 Azure SQL 数据库和 SQL Server 的 Spark 连接器
services: sql-database
author: allenwux
manager: craigg
ms.service: sql-database
ms.custom: ''
ms.topic: article
ms.date: 04/17/2018
ms.author: xiwu
ms.openlocfilehash: 46849d551b6996caaf020caec1ab8104d5388c8f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>通过适用于 Azure SQL 数据库和 SQL Server 的 Spark 连接器，加速实时大数据分析

适用于 Azure SQL 数据库和 SQL Server 的 Spark 连接器可让 SQL 数据库（包括 Azure SQL 数据库和 SQL Server）充当 Spark 作业的输入数据源或输出数据接收器。 由此，可在大数据分析中利用实时事务数据，并保留临时查询或报告的结果。 与内置 JDBC 连接器相比，此连接器能够将数据批量插入 SQL 数据库。 它的性能可以比逐行插入快 10 倍到 20 倍。 适用于 Azure SQL 数据库和 SQL Server 的 Spark 连接器也支持 AAD 身份验证。 由此，可使用 AAD 帐户从 Azure Databricks 安全地连接到 Azure SQL 数据库。 它提供与内置 JDBC 连接器类似的接口。 可以轻松迁移现有的 Spark 作业以使用此新连接器。

## <a name="download"></a>下载
要开始使用，请从 GitHub 上的 [azure-sqldb-spark 存储库](https://github.com/Azure/azure-sqldb-spark)将 Spark 下载到 SQL DB 连接器。

## <a name="official-supported-versions"></a>官方支持的版本

| 组件                            |版本                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 或更高版本           |
| Scala                                |2.10 或更高版本            |
| Microsoft JDBC Driver for SQL Server |6.2 或更高版本             |
| Microsoft SQL Server                 |SQL Server 2008 或更高版本 |
| Azure SQL 数据库                   |支持                |

适用于 Azure SQL 数据库和 SQL Server 的 Spark 连接器利用 Microsoft JDBC Driver for SQL Server 在 Spark 辅助角色节点和 SQL 数据库之间移动数据：
 
数据流如下所示：
1. Spark 主节点连接到 SQL Server 或 Azure SQL 数据库，并从特定的表中或使用特定的 SQL 查询加载数据
2. Spark 主节点将数据分发到辅助角色节点以进行转换。 
3. 辅助角色节点连接到 SQL Server 或 Azure SQL 数据库并将数据写入数据库。 用户可选择使用逐行插入或批量插入。

### <a name="build-the-spark-to-sql-db-connector"></a>构建 Spark 到 SQL DB 的连接器
目前，连接器项目使用 maven。 若要构建不带依赖项的连接器，可以运行：

- mvn 清理包
- 从 releases 文件夹中下载最新版本的 JAR
- 包括 SQL DB Spark JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>使用连接器，将 Spark 连接到 SQL DB
可从 Spark 作业连接到 Azure SQL 数据库或 SQL Server，然后读取或写入数据。 也可在 Azure SQL 数据库或 SQL Server 数据库中运行 DML 或 DDL 查询。

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>从 Azure SQL 数据库或 SQL Server 读取数据

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients"
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>通过特定的 SQL 查询从 Azure SQL 数据库或 SQL Server 读取数据
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>将数据写入 Azure SQL 数据库或 SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients"
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>在 Azure SQL 数据库或 SQL Server 中运行 DML 或 DDL 查询
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.SqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>使用 AAD 身份验证将 Spark 连接到 Azure SQL 数据库
可使用 Azure Active Directory (AAD) 身份验证连接到 Azure SQL 数据库。 使用 AAD 身份验证集中管理数据库用户的身份，并充当 SQL Server 身份验证的替代方案。
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>使用 ActiveDirectoryPassword 身份验证模式进行连接
#### <a name="setup-requirement"></a>安装程序要求
如果使用 ActiveDirectoryPassword 身份验证模式，则需要下载 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) 及其依赖项，并将他它们包含在 Java 生成路径中。

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>使用访问令牌进行连接
#### <a name="setup-requirement"></a>安装程序要求
如果使用基于访问令牌的身份验证模式，则需要下载 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) 及其依赖项，并将他它们包含在 Java 生成路径中。

请参阅[通过 SQL 数据库使用 Azure Active Directory 身份验证进行身份验证](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication)，了解如何获取到 Azure SQL 数据库的访问令牌。

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>使用批量插入将数据写入 Azure SQL 数据库或 SQL Server
传统的 jdbc 连接器使用逐行插入的方式将数据写入 Azure SQL 数据库或 SQL Server。 通过批量插入，可使用 Spark 到 SQL DB 的连接器将数据写入 SQL 数据库。 在加载大型数据集或将数据加载到使用列存储索引的表中时，该方式显着提高了写入性能。

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "databaseName"      -> "zeqisql",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>后续步骤
从 [azure-sqldb-spark GitHub 存储库](https://github.com/Azure/azure-sqldb-spark)下载适用于 Azure SQL 数据库和 SQL Server 的 Spark 连接器（如果尚未下载），并浏览该存储库中的其他资源：

-   [示例 Azure Databricks 笔记本](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [示例脚本 (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

此外，还可以查看 [Apache Spark SQL、数据框架和数据集指南](http://spark.apache.org/docs/latest/sql-programming-guide.html)以及 [Azure Databricks 文档](https://docs.microsoft.com/en-us/azure/azure-databricks/)。

