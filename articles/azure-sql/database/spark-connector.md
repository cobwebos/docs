---
title: 将 Spark 连接器与 Microsoft Azure SQL 和 SQL Server 一起使用
description: 了解如何将 Spark 连接器与 Azure SQL 数据库、Azure SQL 托管实例和 SQL Server 一起使用。
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: carlrab
ms.date: 09/02/2020
ms.openlocfilehash: 46fa489c5a72c3de923f5281cc9be205925dd42d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90988103"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>使用 Spark 连接器加快实时大数据分析
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 从2020年9月，此连接器不会主动维护。 不过，现在提供了 [Apache Spark Connector for SQL Server 和 AZURE SQL](https://docs.microsoft.com/sql/connect/spark/connector) ，并支持 Python 和 R 绑定、更易于使用的界面来大容量插入数据以及其他许多改进。 我们强烈建议你评估并使用新连接器，而不是此连接器。 有关旧连接器 (此页面的信息) 只是出于存档目的进行保留。

通过使用 Spark 连接器，Azure SQL 数据库、Azure SQL 托管实例和 SQL Server 中的数据库可以充当 Spark 作业的输入数据源或输出数据接收器。 由此，可在大数据分析中利用实时事务数据，并保留临时查询或报告的结果。 与内置 JDBC 连接器相比，此连接器能够将数据批量插入数据库。 它的性能可以比逐行插入快 10 倍到 20 倍。 Spark 连接器支持 Azure Active Directory (Azure AD) authentication 来连接到 Azure SQL 数据库和 Azure SQL 托管实例，使你能够使用 Azure Databricks 帐户从 Azure AD 连接数据库。 它提供与内置 JDBC 连接器类似的接口。 可以轻松迁移现有的 Spark 作业以使用此新连接器。

## <a name="download-and-build-a-spark-connector"></a>下载并构建 Spark 连接器

以前从此页面链接到的旧连接器的 GitHub 存储库不会主动维护。 相反，我们强烈建议你评估并使用 [新连接器](https://github.com/microsoft/sql-spark-connector)。

### <a name="official-supported-versions"></a>官方支持的版本

| 组件                             | 版本                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 或更高版本           |
| Scala                                 | 2.10 或更高版本            |
| Microsoft JDBC Driver for SQL Server  | 6.2 或更高版本             |
| Microsoft SQL Server                  | SQL Server 2008 或更高版本 |
| Azure SQL 数据库                    | 支持                |
| Azure SQL 托管实例            | 支持                |

Spark 连接器利用 Microsoft JDBC Driver for SQL Server 在 Spark 工作器节点和数据库之间移动数据：

数据流如下所示：

1. Spark 主节点连接到 SQL 数据库或 SQL Server 中的数据库，并从特定的表中或使用特定的 SQL 查询加载数据。
2. Spark 主节点将数据分发到辅助角色节点以进行转换。
3. 工作器节点连接到与 SQL 数据库或 SQL Server 连接的数据库并将数据写入数据库。 用户可选择使用逐行插入或批量插入。

下图演示了此数据流。

   ![关系图显示了所述的流，其中的主节点直接连接到数据库并连接到三个工作节点（连接到数据库）。](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>生成 Spark 连接器

目前，连接器项目使用 maven。 若要构建不带依赖项的连接器，可以运行：

- mvn 清理包
- 从 releases 文件夹中下载最新版本的 JAR
- 包括 SQL 数据库 Spark JAR

## <a name="connect-and-read-data-using-the-spark-connector"></a>使用 Spark 连接器连接和读取数据

可以从 Spark 作业连接到 SQL 数据库和 SQL Server 中的数据库以读取或写入数据。 也可在 SQL 数据库或 SQL Server 的数据库中运行 DML 或 DDL 查询。

### <a name="read-data-from-azure-sql-and-sql-server"></a>从 Azure SQL 和 SQL Server 读取数据

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>使用指定的 SQL 查询从 Azure SQL 和 SQL Server 读取数据

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
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-and-sql-server"></a>将数据写入 Azure SQL 和 SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>在 Azure SQL 和 SQL Server 中运行 DML 或 DDL 查询

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-from-spark-using-azure-ad-authentication"></a>使用 Azure AD 身份验证从 Spark 进行连接

可以使用 Azure AD 身份验证连接到 Azure SQL 数据库和 SQL 托管实例。 Azure AD 身份验证可用于集中管理数据库用户的标识，并替代 SQL Server 身份验证。

### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>使用 ActiveDirectoryPassword 身份验证模式进行连接

#### <a name="setup-requirement"></a>设置要求

如果使用 ActiveDirectoryPassword 身份验证模式，则需要下载 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) 及其依赖项，并将他它们包含在 Java 生成路径中。

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-an-access-token"></a>使用访问令牌进行连接

#### <a name="setup-requirement"></a>设置要求

如果使用基于访问令牌的身份验证模式，则需要下载 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) 及其依赖项，并将他它们包含在 Java 生成路径中。

若要了解如何获取对 Azure SQL 数据库或 Azure SQL 托管实例中数据库的访问令牌，请参阅[使用 Azure Active Directory 身份验证进行身份验证](authentication-aad-overview.md)。

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-using-bulk-insert"></a>通过批量插入操作写入数据

传统的 jdbc 连接器使用逐行插入的方式将数据写入数据库。 可以使用 Spark 连接器，以批量插入的方式将数据写入 Azure SQL 和 SQL Server。 在加载大型数据集或将数据加载到使用列存储索引的表中时，该方式显着提高了写入性能。

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
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>后续步骤

如果尚未下载连接器，请从 [azure-sqldb-spark GitHub 存储库](https://github.com/Azure/azure-sqldb-spark)下载 Spark 连接器，并浏览存储库中的其他资源：

- [示例 Azure Databricks 笔记本](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [示例脚本 (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

此外，还可以查看 [Apache Spark SQL、数据框架和数据集指南](https://spark.apache.org/docs/latest/sql-programming-guide.html)以及 [Azure Databricks 文档](https://docs.microsoft.com/azure/azure-databricks/)。
