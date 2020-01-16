---
title: 使用 Azure 数据资源管理器连接器 Apache Spark 在 Azure 数据资源管理器和 Spark 群集之间移动数据。
description: 本主题说明如何在 Azure 数据资源管理器和 Apache Spark 群集之间移动数据。
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: 868e9e068244af91e218d906bee115b58906152f
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027898"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>用于 Apache Spark 的 Azure 数据资源管理器连接器（预览版）

[Apache Spark](https://spark.apache.org/)是一种统一的分析引擎，用于大规模数据处理。 Azure 数据资源管理器是一种快速、完全托管的数据分析服务，可用于对大量数据进行实时分析。 

适用于 Spark 的 azure 数据资源管理器连接器实现了用于跨 Azure 数据资源管理器和 Spark 群集移动数据的数据源和数据接收器，以同时使用这两种功能。 使用 Azure 数据资源管理器和 Apache Spark，可以构建面向数据驱动型方案（如机器学习 (ML)、提取-转换-加载 (ETL) 和 Log Analytics）的可缩放快速应用程序。 写入 Azure 数据资源管理器可以在批处理和流式处理模式下完成。
从 Azure 数据资源管理器读取支持列修剪和谓词下推，这可通过在 Azure 数据资源管理器中筛选出数据来减少传输的数据量。

Azure 数据资源管理器 Spark 连接器是可在任何 Spark 群集上运行的[开放源代码项目](https://github.com/Azure/azure-kusto-spark)。 Azure 数据资源管理器 Spark 连接器使 Azure 数据资源管理器用于标准 Spark 源和接收器操作（如写入、读取和 writeStream）的有效数据存储。 

> [!NOTE]
> 尽管下面的一些示例引用[Azure Databricks](https://docs.azuredatabricks.net/) Spark 群集，但 Azure 数据资源管理器 Spark 连接器不会直接依赖于 Databricks 或任何其他 Spark 分布。

## <a name="prerequisites"></a>必备组件

* [创建 Azure 数据资源管理器群集和数据库](/azure/data-explorer/create-cluster-database-portal) 
* 创建 Spark 群集
* 安装 Azure 数据资源管理器连接器库和[依赖关系](https://github.com/Azure/azure-kusto-spark#dependencies)中列出的库，包括以下[Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library)库：
    * [Kusto 数据客户端](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto 引入客户端](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* 用于[Spark 2.4、Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases)和[Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)存储库的预建库

## <a name="how-to-build-the-spark-connector"></a>如何生成 Spark 连接器

可以从以下详述的[源](https://github.com/Azure/azure-kusto-spark)构建 Spark 连接器。

> [!NOTE]
> 此步骤是可选的。 如果使用的是预建库，请使用[Spark 群集安装程序](#spark-cluster-setup)。

### <a name="build-prerequisites"></a>构建必备组件

* 已安装 Java 1.8 SDK
* 已安装[Maven](https://maven.apache.org/download.cgi) 1。x
* Apache Spark 版本2.4.0 或更高版本

> [!TIP]
> 2.3. x 版本也受支持，但可能需要在 pom 依赖项中进行一些更改。

对于使用 Maven 项目定义的 Scala/Java 应用程序，请使用以下项目链接应用程序（最新版本可能不同）：

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>生成命令

生成 jar 并运行所有测试：

```
mvn clean package
```

若要生成 jar，请运行所有测试，并将 jar 安装到本地 Maven 存储库：

```
mvn clean install
```

有关详细信息，请参阅[连接器使用情况](https://github.com/Azure/azure-kusto-spark#usage)。

## <a name="spark-cluster-setup"></a>Spark 群集设置

> [!NOTE]
> 执行以下步骤时，建议使用最新的 Azure 数据资源管理器 Spark 连接器版本：

1. 基于使用 Spark 2.4.4 和 Scala 2.11 的 Azure Databricks 群集设置以下 Spark 群集设置： 

    ![Databricks 群集设置](media/spark-connector/databricks-cluster.png)
    
1. 从 Maven 安装最新的 kusto 库：

    ![导入 Azure 数据资源管理器库](media/spark-connector/db-create-library.png)

1. 验证是否安装了所有必需的库：

    ![验证是否已安装库](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>身份验证

通过 Azure 数据资源管理器 Spark 连接器，你可以使用[Azure AD 应用程序](#azure-ad-application-authentication)、 [Azure AD 访问令牌](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)、[设备身份验证](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication)（适用于非生产方案）或[Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault)对 Azure Active Directory （Azure AD）进行身份验证。 用户必须安装 keyvault 包，并提供应用程序凭据来访问 Key Vault 资源。

### <a name="azure-ad-application-authentication"></a>Azure AD 应用程序身份验证

最简单的身份验证方法。 建议为 Azure 数据资源管理器 Spark 连接器使用此方法。

|属性  |Description  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD 应用程序（客户端）标识符。      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD 身份验证机构。 Azure AD Directory （租户） ID。        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Azure AD 客户端的应用程序密钥。     |

### <a name="azure-data-explorer-privileges"></a>Azure 数据资源管理器权限

必须在 Azure 数据资源管理器群集上授予以下权限：

* 对于读取（数据源），Azure AD 应用程序必须对目标数据库拥有*查看器*权限，或对目标表具有*管理员*权限。
* 对于写入（数据接收器），Azure AD 应用程序必须对目标数据库具有*引入器*权限。 它还必须具有对目标数据库的*用户*特权才能创建新表。 如果目标表已存在，则可以配置针对目标表的*管理员*权限。
 
有关 Azure 数据资源管理器主体角色的详细信息，请参阅[基于角色的授权](/azure/kusto/management/access-control/role-based-authorization)。 有关管理安全角色的详细，请参阅[安全角色管理](/azure/kusto/management/security-roles)。

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

1. 以批处理方式将 Spark 数据帧写入 Azure 数据资源管理器群集：

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
    
   或使用简化的语法：
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. 写入流式传输数据：

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark 源：从 Azure 数据资源管理器读取

1. 读取少量数据时，定义数据查询：

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

1. 读取大量数据时，必须提供临时 blob 存储。 提供存储容器 SAS 密钥或存储帐户名称、帐户密钥和容器名称。 此步骤只需要用于 Spark 连接器的当前预览版本。

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    在上面的示例中，我们不会使用连接器接口访问 Key Vault。 另外，我们还使用了一种更简单的方法来使用 Databricks 机密。

1. 从 Azure 数据资源管理器读取：

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

## <a name="next-steps"></a>后续步骤

* 详细了解[Azure 数据资源管理器 Spark 连接器](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [代码示例](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)

