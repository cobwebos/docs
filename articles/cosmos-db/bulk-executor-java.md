---
title: 使用 Bulk Executor Java 库在 Azure Cosmos DB 中执行批量操作 | Microsoft Docs
description: 使用 Azure Cosmos DB 的 Bulk Executor Java 库在 Azure Cosmos DB 集合中批量导入和更新文档。
keywords: Java 批量执行程序
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: f241a98cdcc847ddb579b86b51034d1438ee1395
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300707"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>使用 Bulk Executor Java 库针对 Azure Cosmos DB 数据执行批量操作

本教程提供有关使用 Azure Cosmos DB 的批量执行程序 Java 库导入和更新 Azure Cosmos DB 文档的说明。 若要了解 Bulk Executor 库及它如何帮助你利用大量吞吐量和存储，请参阅 [Bulk Executor 库概述](bulk-executor-overview.md)一文。 在本教程中，我们将构建一个可生成随机文档的 Java 应用程序，然后将文档批量导入 Azure Cosmos DB 集合。 导入后，我们将批量更新文档的某些属性。 

## <a name="prerequisites"></a>先决条件

* 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。  

* 无需 Azure 订阅即可[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，也无需缴纳费用或承诺金。 或者，可以使用 URI 为 `https://localhost:8081` 的 [Azure Cosmos DB 模拟器](https://docs.microsoft.com/azure/cosmos-db/local-emulator)。 [对请求进行身份验证](local-emulator.md#authenticating-requests)中提供了主密钥。  

* [Java 开发工具包 (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)  
  - 在 Ubuntu 上运行 `apt-get install default-jdk`，以便安装 JDK。  

  - 请确保设置 JAVA_HOME 环境变量，使之指向在其中安装了 JDK 的文件夹。

* [下载](http://maven.apache.org/download.cgi)和[安装](http://maven.apache.org/install.html) [Maven](http://maven.apache.org/) 二进制存档  
  
  - 在 Ubuntu 上，可以通过运行 `apt-get install maven` 来安装 Maven。

* 使用 Java 快速入门文章的[创建数据库帐户](create-sql-api-java.md#create-a-database-account)部分所述的步骤创建 Azure Cosmos DB SQL API 帐户。

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在，我们从 GitHub 下载示例 Java 应用程序来接着处理代码。 此应用程序针对 Azure Cosmos DB 数据执行批量操作。 若要克隆该应用程序，请打开命令提示符，导航到要将该应用程序复制到的目录，然后运行以下命令：

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

克隆的存储库包含相对于“\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor”文件夹的两个示例：“bulkimport”和“bulkupdate”。 “bulkimport”应用程序生成随机文档并将其导入 Azure Cosmos DB。 “bulkupdate”应用程序更新 Azure Cosmos DB 中的某些文档。 在后续部分，我们将查看其中每个示例应用中的代码。 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>将数据批量导入 Azure Cosmos DB

1. Azure Cosmos DB 的连接字符串将作为参数读取，并分配到 CmdLineConfiguration.java 文件中定义的变量。  

2. 接下来，使用以下语句初始化 DocumentClient 对象：  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. 根据等待时间和限制请求使用较大重试值初始化 DocumentBulkExecutor 对象。 然后，这些值将设置为 0，以将阻塞控制权传递给 DocumentBulkExecutor（在其生存期内都会保留此控制权）。  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the collection's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
```

4. 调用 importAll API，用于生成要批量导入 Azure Cosmos DB 集合的随机文档。 可以在 CmdLineConfiguration.java 文件中配置命令行配置。

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
```
   批量导入 API 接受 JSON 序列化文档的集合并使用以下语法，有关更多详细信息，请参阅 [API 文档](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)：

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   importAll 方法接受以下参数：
 
   |**Parameter**  |**说明**  |
   |---------|---------|
   |isUpsert    |   用于启用文档更新插入的标志。 如果已存在具有给定 ID 的文档，则会更新该文档。  |
   |disableAutomaticIdGeneration     |   用于禁用自动生成 ID 的标志。 此值默认设置为 true。   |
   |maxConcurrencyPerPartitionRange    |  每个分区键范围的最大并发度。 默认值为 20。  |

   **批量导入响应对象定义**批量导入 API 调用的结果包含以下 get 方法：

   |**Parameter**  |**说明**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   从提供给批量导入 API 调用的文档中成功导入的文档总数。      |
   |double getTotalRequestUnitsConsumed()   |  批量导入 API 调用消耗的请求单位 (RU) 总数。       |
   |Duration getTotalTimeTaken()   |    批量导入 API 调用完成执行所花费的总时间。     |
   |List<Exception> getErrors() |  如果分批提供给批量导入 API 调用的某些文档无法插入，则获取错误列表。       |
   |List<Object> getBadInputDocuments()  |    未在批量导入 API 调用中成功导入的格式不当文档列表。 用户应修复返回的文档，并重试导入。 格式不当的文档包括其 ID 值不是字符串（null 或其他任何数据类型被视为无效）的文档。     |

5. 准备好批量导入应用程序后，请使用“mvn clean package”命令从源代码生成命令行工具。 此命令在目标文件夹中生成一个 jar 文件：  

   ```java
   mvn clean package
   ```

6. 生成目标依赖关系后，可使用以下命令调用批量导入程序应用程序：  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint URI>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   批量导入程序会创建新的数据库和集合，并在 App.config 文件中指定数据库名称、集合名称与吞吐量值。 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中批量更新数据

可以使用 BulkUpdateAsync API 更新现有文档。 此示例将 Name 字段设置为新值，并从现有文档中删除 Description 字段。 有关完整的受支持字段更新操作集，请参阅 [API 文档](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)。 

1. 定义更新项以及相应的字段更新操作。 此示例使用 SetUpdateOperation 更新 Name 字段，并使用 UnsetUpdateOperation 删除所有文档中的 Description 字段。 还可以执行其他操作，例如，根据特定的值递增文档字段、将特定的值推送到数组字段，或者从数组字段中删除特定的值。 若要了解批量更新 API 提供的不同方法，请参阅 [API 文档](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)。  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                      
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. 调用 updateAll API，用于生成随后要批量导入 Azure Cosmos DB 集合的随机文档。 可以在 CmdLineConfiguration.java 文件中配置要传递的命令行配置。

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   批量更新 API 接受一系列可更新的项。 每个更新项指定要针对 ID 和分区键值标识的文档执行的字段更新操作列表。 有关更多详细信息，请参阅 [API 文档](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)：

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   updateAll 方法接受以下参数：

   |**Parameter** |**说明** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  每个分区键范围的最大并发度。 默认值为 20。  |
 
   **批量导入响应对象定义**批量导入 API 调用的结果包含以下 get 方法：

   |**Parameter** |**说明**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   从提供给批量更新 API 调用的文档中成功更新的文档总数。      |
   |double getTotalRequestUnitsConsumed() |  批量更新 API 调用消耗的请求单位 (RU) 总数。       |
   |Duration getTotalTimeTaken()  |   批量更新 API 调用完成执行所花费的总时间。      |
   |List<Exception> getErrors()   |     如果分批提供给批量更新 API 调用的某些文档无法插入，则获取错误列表。      |

3. 准备好批量更新应用程序后，请使用“mvn clean package”命令从源代码生成命令行工具。 此命令在目标文件夹中生成一个 jar 文件：  

   ```
   mvn clean package
   ```

4. 生成目标依赖关系后，可使用以下命令调用批量更新应用程序：

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint URI>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>性能提示 

使用 Bulk Executor 库时，请注意以下几点，以获得更好的性能：

* 为获得最佳性能，请从 Cosmos DB 帐户写入区域中的 Azure VM 运行应用程序。  
* 为实现更高的吞吐量：  

   * 请将 JVM 的堆大小设为足够大的数字，以免在处理大量文档时出现任何内存问题。 建议的堆大小：max(3GB, 3 * sizeof(在一个批中传递给批量导入 API 的文档总数))。  
   * 会有一段预处理时间，因此，在对大量的文档执行批量操作时可以获得更高的吞吐量。 如果想要导入 10,000,000 个文档，针对 10 批文档（每个批的大小为 1,000,000）运行批量导入 10 次，比针对 100 批文档（每个批的大小为 100,000 个文档）运行批量导入 100 次会更有利。  

* 建议在单个虚拟机中，为整个应用程序实例化对应于特定 Azure Cosmos DB 集合的单个 DocumentBulkExecutor 对象。  

* 原因是单个批量操作 API 执行会消耗客户端计算机的大量 CPU 和网络 IO。 而发生这种情况的原因是在内部生成了多个任务，因此，每次执行批量操作 API 调用时，请避免在应用程序进程中生成多个并发任务。 如果单个虚拟机上运行的单个批量操作 API 调用无法占用整个集合的吞吐量（如果集合吞吐量超过 100 万 RU/秒），最好是创建独立的虚拟机来并发执行批量操作 API 调用。

    
## <a name="next-steps"></a>后续步骤
* 若要了解 maven 包的详细信息以及 Bulk Executor Java 库的发行说明，请参阅 [Bulk Executor SDK 详细信息](sql-api-sdk-bulk-executor-java.md)。


