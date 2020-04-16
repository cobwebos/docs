---
title: 教程 - 使用更改源的端到端 Async Java SQL API 应用程序示例
description: 本教程演练一个简单的 Java SQL API 应用程序，它可将文档插入到 Azure Cosmos DB 容器，同时使用更改源维护该容器的具体化视图。
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: 5eab523dde2a13a85b0c8ff5bcbb3ecb5912e78e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587214"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>教程 - 使用更改源的端到端 Async Java SQL API 应用程序示例

本指南教程演练一个简单的 Java SQL API 应用程序，它可将文档插入到 Azure Cosmos DB 容器，同时使用更改源维护该容器的具体化视图。

## <a name="prerequisites"></a>先决条件

* 个人计算机

* Azure Cosmos DB 帐户的 URI 和密钥

* Maven

* Java 8

## <a name="background"></a>背景

Azure Cosmos DB 更改源提供事件驱动的接口，用于在响应文档插入时触发操作。 此功能有很多用途。 例如，在读取和写入负载繁重的应用程序中，更改源的主要用途是在引入文档时创建容器的实时具体化视图。  具体化视图容器包含相同的数据，但为了提高读取效率，该容器进行了分区，因此可以提高应用程序的读取和写入效率。

管理更改源事件的工作主要由 SDK 中内置的更改源处理器库负责。 此库足够强大，可以根据需要在多个工作线程之间分配更改源事件。 你只需为更改源库提供一个回调。

此简单示例使用单个工作线程在具体化视图中创建和删除文档，以此演示更改源处理器库。

## <a name="setup"></a>设置

克隆应用示例存储库（如果尚未这样做）：

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> 可以选择使用 Java SDK 4.0 或 Java SDK 3.7.0 来学习本快速入门。 若要使用 Java SDK 3.7.0，请在终端中键入 ```git checkout SDK3.7.0```。  否则，请仍旧使用 ```master``` 分支，该分支默认为 Java SDK 4.0。

在存储库目录中打开终端。 运行以下命令来生成应用

```bash
mvn clean package
```

## <a name="walkthrough"></a>演练

1. 首先检查是否有一个 Azure Cosmos DB 帐户。 在浏览器中打开 Azure 门户，转到你的 Azure Cosmos DB 帐户，然后在左侧窗格中导航到“数据资源管理器”。  

    ![Azure Cosmos DB 帐户](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. 在终端中使用以下命令运行应用：

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. 看到以下内容时，请按 Enter

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    然后，在浏览器中返回到 Azure 门户数据资源管理器。 此时会看到已添加包含三个空容器的数据库 GroceryStoreDatabase：  

    * **InventoryContainer** - 示例杂货店的库存记录，已按项 ```id```（一个 UUID）进行分区。
    * **InventoryContainer-pktype** - 库存记录的具体化视图，已针对项 ```type``` 的查询进行优化
    * **InventoryContainer-leases** - 更改源始终需要一个租约容器；租约跟踪应用读取更改源的进度。


    ![空容器](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. 在终端中，现在应会看到一条提示

    ```bash
    Press enter to start creating the materialized view...
    ```

    按 Enter。 现在，以下代码块将在另一个线程中执行并初始化更改源处理器： 


    **Java SDK 4.0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start
    ```

    **Java SDK 3.7.0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start    
    ```

    ```"SampleHost_1"``` 是更改源处理器工作线程的名称。 ```changeFeedProcessorInstance.start()``` 是实际启动更改源处理器的组件。

    在浏览器中返回到 Azure 门户数据资源管理器。 在“InventoryContainer-leases”容器下，单击“项”以查看其内容。   此时会看到，更改源处理器已填充了租约容器，即，处理器已在 InventoryContainer 的某些分区中为 ```SampleHost_1``` 工作线程分配了一个租约。 

    ![租约](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. 再次在终端中按 Enter。 这会触发将 10 个文档插入 InventoryContainer 的事件。  每个文档插入事件在更改源中以 JSON 显示；以下回调代码通过将 JSON 文档镜像到具体化视图来处理这些事件：

    **Java SDK 4.0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

    **Java SDK 3.7.0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosContainer feedContainer, CosmosContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.feedPollDelay(Duration.ofMillis(100));
        cfOptions.startFromBeginning(true);
        return ChangeFeedProcessor.Builder()
            .options(cfOptions)
            .hostName(hostName)
            .feedContainer(feedContainer)
            .leaseContainer(leaseContainer)
            .handleChanges((List<CosmosItemProperties> docs) -> {
                for (CosmosItemProperties document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(CosmosItemProperties document) {
        typeContainer.upsertItem(document).subscribe();
    }    
    ```

1. 让代码运行 5-10 秒。 然后返回到 Azure 门户数据资源管理器，并导航到“InventoryContainer”>“项”。  此时会看到，项正在插入到库存容器；请记下分区键 (```id```)。

    ![源容器](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. 现在，请在数据资源管理器中导航到“InventoryContainer-pktype”>“项”。  这是具体化视图 - 此容器中的项会镜像 InventoryContainer，因为它们已由更改源以编程方式插入。  记下分区键 (```type```)。 因此，此具体化视图已针对 ```type``` 查询筛选进行优化，但它在 InventoryContainer 中效率不高，因为此容器是按 ```id``` 分区的。 

    ![具体化视图](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. 我们将同时从 InventoryContainer 和 InventoryContainer-pktype 删除某个文档，只需使用一个 ```upsertItem()``` 调用即可。   首先查看 Azure 门户数据资源管理器。 我们将删除 ```/type == "plums"``` 的文档；下面以红框突出显示了此项

    ![具体化视图](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    再次按 Enter，以调用示例代码中的函数 ```deleteDocument()```。 此函数（如下所示）更新插入 ```/ttl == 5``` 的文档的新版本，这会将该文档的生存时间 (TTL) 设置为 5 秒。 
    
    **Java SDK 4.0**
    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    **Java SDK 3.7.0**
    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    更改源 ```feedPollDelay``` 设置为 100 毫秒；因此，更改源几乎可以即时响应此更新，并按如上所示调用 ```updateInventoryTypeMaterializedView()```。 最后一个函数调用将 TTL 为 5 秒的新文档更新插入到 InventoryContainer-pktype。 

    效果是，在大约 5 秒后，该文档将会过期并从两个容器中删除。

    之所以需要完成此过程，是因为更改源只会针对项插入或更新操作发出事件，而不会针对项删除操作发出事件。

1. 再次按 Enter 以关闭程序并清理其资源。
