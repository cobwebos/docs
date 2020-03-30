---
title: 使用适用于 Apache Spark 的 Azure 数据资源管理器连接器在 Azure 数据资源管理器与 Spark 群集之间移动数据。
description: 本主题介绍如何在 Azure 数据资源管理器与 Apache Spark 群集之间移动数据。
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208573"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>用于 Apache Spark 的 Azure 数据资源管理器连接器

[Apache Spark](https://spark.apache.org/)是用于大规模数据处理的统一分析引擎。 Azure 数据资源管理器是一个快速、完全托管的数据分析服务，可用于实时分析大量数据。 

Spark 的 Azure 数据资源管理器连接器是可在任何 Spark 群集上运行的[开源项目](https://github.com/Azure/azure-kusto-spark)。 它实现数据源和数据源接收器，用于跨 Azure 数据资源管理器和 Spark 群集移动数据。 使用 Azure 数据资源管理器和 Apache Spark，可以构建针对数据驱动方案的快速且可扩展的应用程序。 例如，机器学习 （ML）、提取转换加载 （ETL） 和日志分析。 使用连接器，Azure 数据资源管理器将成为标准 Spark 源和接收器操作（如写入、读取和写入流）的有效数据存储。

您可以在批处理或流模式下写入 Azure 数据资源管理器。 从 Azure 数据资源管理器读取支持列修剪和谓词向下，这将筛选 Azure 数据资源管理器中的数据，从而减少传输数据的数量。

本主题介绍如何安装和配置 Azure 数据资源管理器 Spark 连接器，以及如何在 Azure 数据资源管理器和 Apache Spark 群集之间移动数据。

> [!NOTE]
> 尽管下面的某些示例提到了 [Azure Databricks](https://docs.azuredatabricks.net/) Spark 群集，但 Azure 数据资源管理器 Spark 连接器并不直接依赖于 Databricks 或任何其他 Spark 分发版。

## <a name="prerequisites"></a>先决条件

* [创建 Azure 数据资源管理器群集和数据库](/azure/data-explorer/create-cluster-database-portal) 
* 创建 Spark 群集
* 安装 Azure 数据资源管理器连接器库：
    * [Spark 2.4、Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) 的预生成库 
    * [马文回购](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* 已安装 [Maven 3.x](https://maven.apache.org/download.cgi)

> [!TIP]
> 也支持版本 2.3.x，不过，这可能需要在 pom.xml 依赖项中进行某些更改。

## <a name="how-to-build-the-spark-connector"></a>如何生成 Spark 连接器

> [!NOTE]
> 此步骤是可选的。 如果使用的是预生成库，请转到 [Spark 群集设置](#spark-cluster-setup)。

### <a name="build-prerequisites"></a>生成先决条件

1. 安装[依赖项](https://github.com/Azure/azure-kusto-spark#dependencies)中列出的库，包括以下[Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library)库：
    * [Kusto 数据客户端](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto 引入客户端](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. 有关构建火花连接器，请参阅[此源](https://github.com/Azure/azure-kusto-spark)。

1. 对于使用 Maven 项目定义的 Scala/Java 应用程序，请将应用程序链接到以下项目（在最新版本中可能不同）：
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>生成命令

生成 jar 并运行所有测试：

```
mvn clean package
```

生成 jar，运行所有测试，并将 jar 安装到本地 Maven 存储库：

```
mvn clean install
```

有关详细信息，请参阅[连接器用法](https://github.com/Azure/azure-kusto-spark#usage)。

## <a name="spark-cluster-setup"></a>Spark 群集设置

> [!NOTE]
> 在执行以下步骤时，建议使用最新的 Azure 数据资源管理器 Spark 连接器版本。

1. 使用 Spark 2.4.4 和 Scala 2.11 基于 Azure 数据砖块群集配置以下 Spark 群集设置：

    ![Databricks 群集设置](media/spark-connector/databricks-cluster.png)
    
1. 从 Maven 安装最新 spark-kusto-connector 库：
    
    ![导入库](media/spark-connector/db-libraries-view.png)![选择火花-库斯托连接器](media/spark-connector/db-dependencies.png)

1. 验证是否已安装所有必需的库：

    ![验证是否已安装库](media/spark-connector/db-libraries-view.png)

1. 对于使用 JAR 文件进行安装，请验证是否安装了其他依赖项：

    ![添加依赖项](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>身份验证

Azure 数据资源管理器 Spark 连接器使您能够使用以下方法之一使用 Azure 活动目录 （Azure AD） 进行身份验证：
* [Azure AD 应用程序](#azure-ad-application-authentication)
* [Azure AD 访问令牌](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [设备身份验证](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication)（用于非生产方案）
* [Azure 密钥保管库](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault)用于访问密钥保管库资源、安装 Azure 密钥保管包并提供应用程序凭据。

### <a name="azure-ad-application-authentication"></a>Azure AD 应用程序身份验证

Azure AD 应用程序身份验证是最简单、最常见的身份验证方法，建议用于 Azure 数据资源管理器 Spark 连接器。

|属性  |描述  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD 应用程序（客户端）标识符。      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD 身份验证颁发机构。 Azure AD 目录（租户）ID。        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    客户端的 Azure AD 应用程序密钥。     |

### <a name="azure-data-explorer-privileges"></a>Azure 数据资源管理器权限

授予 Azure 数据资源管理器群集上的以下权限：

* 对于读取（数据源），Azure AD 标识必须在目标数据库上具有*查看器*权限，或在目标表上具有*管理员*权限。
* 对于写入（数据接收器），Azure AD 标识必须在目标数据库上具有*引入者*权限。 此外，它必须对目标数据库拥有“用户”特权，这样才能创建新表。** 如果目标表已存在，则必须在目标表上配置*管理员*权限。
 
有关 Azure 数据资源管理器主体角色的详细信息，请参阅[基于角色的授权](/azure/kusto/management/access-control/role-based-authorization)。 有关如何管理安全角色，请参阅[安全角色管理](/azure/kusto/management/security-roles)。

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark 接收器：写入 Azure 数据资源管理器

1. 设置接收器参数：

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. 将 Spark 数据帧分批写入 Azure 数据资源管理器群集：

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   或者使用简化的语法：
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. 写入流数据：

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark 源：从 Azure 数据资源管理器读取数据

1. 读取[少量数据](/azure/kusto/concepts/querylimits)时，定义数据查询：

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. 可选：**如果**提供临时 Blob 存储（而不是 Azure 数据资源管理器），则创建 Blob 由调用方负责。 这包括预配存储、旋转访问密钥和删除瞬态项目。 
    KustoBlobStorageUtils 模块包含帮助程序功能，用于根据帐户和容器坐标和帐户凭据删除 blob，或具有写入、读取和列表权限的完整 SAS URL。 当不再需要相应的 RDD 时，每个事务都会在单独的目录中存储瞬态 Blob 项目。 此目录作为 Spark 驱动程序节点上报告的读取事务信息日志的一部分捕获。

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    在上面的示例中，不会使用连接器接口访问密钥保管库;因此，不会使用连接器接口访问密钥保管库。使用数据砖机密的更简单的方法。

1. 从 Azure 数据资源管理器读取。

    * **如果**提供瞬态 Blob 存储，请从 Azure 数据资源管理器中读取，如下所示：

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * 如果**Azure 数据资源管理器**提供瞬态 Blob 存储，请从 Azure 数据资源管理器中读取，如下所示：
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 数据资源管理器 Spark 连接器](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Java 和 Python 的示例代码](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
