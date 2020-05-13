---
title: 使用更改源创建端到端 Azure Cosmos DB Java SDK v4 应用程序示例
description: 本指南将指导你完成一个简单的 Java SQL API 应用程序，该应用程序将文档插入 Azure Cosmos DB 容器中，同时使用更改源维护容器的具体化视图。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: 5e8656e891d250547174aa3deb27a94eebaa0ba3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125666"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>如何创建使用 Azure Cosmos DB SQL API 和更改源处理器的 Java 应用程序

> [!IMPORTANT]  
> 有关 Azure Cosmos DB Java SDK v4 的详细信息，请查看 Azure Cosmos DB Java SDK v4 发行说明、 [Maven 存储库](https://mvnrepository.com/artifact/com.azure/azure-cosmos)、AZURE COSMOS DB java sdk v4[性能提示](performance-tips-java-sdk-v4-sql.md)和 Azure Cosmos DB Java sdk v4[故障排除指南](troubleshoot-java-sdk-v4-sql.md)。
>

本操作方法指南将指导你完成一个简单的 Java 应用程序，该应用程序使用 Azure Cosmos DB SQL API 将文档插入 Azure Cosmos DB 容器中，同时使用更改源和更改源处理器维护容器的具体化视图。 Java 应用程序使用 Azure Cosmos DB Java SDK v4 与 Azure Cosmos DB SQL API 通信。

## <a name="prerequisites"></a>必备条件

* Azure Cosmos DB 帐户的 URI 和密钥

* Maven

* Java 8

## <a name="background"></a>背景

Azure Cosmos DB 更改源提供事件驱动的接口来触发操作，以响应文档插入。 此功能有很多用途。 例如，在读取和写入繁重的应用程序中，更改源的主要用途是创建容器的实时**具体化视图**，因为它是引入的文档。 具体化视图容器包含相同的数据，但为了提高读取效率，该容器进行了分区，因此可以提高应用程序的读取和写入效率。

管理更改源事件的工作在很大程度上由 SDK 中内置的更改源处理器库负责。 如果需要，此库的功能非常强大，可以在多个工作线程之间分配更改源事件。 你只需为更改源库提供回调。

这个简单的示例演示了如何使用单个辅助角色在具体化视图中创建和删除文档。

## <a name="setup"></a>设置

克隆应用示例存储库（如果尚未这样做）：

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

在存储库目录中打开终端。 运行以下命令来生成应用

```bash
mvn clean package
```

## <a name="walkthrough"></a>演练

1. 首先检查是否有一个 Azure Cosmos DB 帐户。 在浏览器中打开**Azure 门户**，转到你的 Azure Cosmos DB 帐户，然后在左窗格中导航到 "**数据资源管理器**"。

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
    * **InventoryContainer** -更改源始终需要租赁容器;租约跟踪应用程序读取更改源的进度。


    ![空容器](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. 在终端中，现在应会看到一条提示

    ```bash
    Press enter to start creating the materialized view...
    ```

    按 Enter。 现在，下面的代码块将在另一个线程上执行并初始化更改源处理器： 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 （Maven：： azure-cosmos） Async API

    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for change feed processor start
    ```

    ```"SampleHost_1"``` 是更改源处理器工作线程的名称。 ```changeFeedProcessorInstance.start()``` 是实际启动更改源处理器的组件。

    返回到浏览器中的 Azure 门户数据资源管理器。 在“InventoryContainer-leases”容器下，单击“项”以查看其内容。   此时会看到，更改源处理器已填充了租约容器，即，处理器已在 InventoryContainer 的某些分区中为 ```SampleHost_1``` 工作线程分配了一个租约。 

    ![租约](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. 再次在终端中按 Enter。 这会触发将 10 个文档插入 InventoryContainer 的事件。  每个文档插入都在更改源中显示为 JSON;以下回调代码通过将 JSON 文档镜像到具体化视图来处理这些事件：

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 （Maven：： azure-cosmos） Async API

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

1. 让代码运行 5-10 秒。 然后返回到 Azure 门户数据资源管理器并导航到**InventoryContainer > 项**。 此时会看到，项正在插入到库存容器；请记下分区键 (```id```)。

    ![源容器](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. 现在，请在数据资源管理器中导航到“InventoryContainer-pktype”>“项”。  这是具体化视图-此容器中的项**InventoryContainer** ，因为它们是通过更改源以编程方式插入的。 记下分区键 (```type```)。 因此，此具体化视图已针对 ```type``` 查询筛选进行优化，但它在 InventoryContainer 中效率不高，因为此容器是按 ```id``` 分区的。 

    ![具体化视图](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. 我们将同时从 InventoryContainer 和 InventoryContainer-pktype 删除某个文档，只需使用一个 ```upsertItem()``` 调用即可。   首先，请查看 Azure 门户数据资源管理器。 我们将删除 ```/type == "plums"``` 的文档；下面以红框突出显示了此项

    ![具体化视图](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    再次按 Enter，以调用示例代码中的函数 ```deleteDocument()```。 此函数（如下所示）更新插入 ```/ttl == 5``` 的文档的新版本，这会将该文档的生存时间 (TTL) 设置为 5 秒。 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 （Maven：： azure-cosmos） Async API

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

    更改源 ```feedPollDelay``` 设置为 100ms; 因此，更改源几乎可立即响应此更新和 ```updateInventoryTypeMaterializedView()``` 上面所示的调用。 最后一个函数调用将 TTL 为 5 秒的新文档更新插入到 InventoryContainer-pktype。 

    效果是，在大约 5 秒后，该文档将会过期并从两个容器中删除。

    此过程是必需的，因为更改源只能在项插入或更新时发出事件，而不会对项删除发出事件。

1. 再次按 Enter 以关闭程序并清理其资源。
