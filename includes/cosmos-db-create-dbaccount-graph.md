1. 在新窗口中，登录到 [Azure 门户](https://portal.azure.com/)。

2. 在左窗格中，选择“新建” > “数据库” > “Azure Cosmos DB” > “创建”。
   
   ![Azure 门户“数据库”窗格](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. 在“新建帐户”下，为此 Azure Cosmos DB 帐户指定所需配置。 

    使用 Azure Cosmos DB，可以选择以下四个编程模型之一：Gremlin（图形）、MongoDB、SQL (DocumentDB) 和表（键/值）。 目前，每个模型都需要单独的帐户。
       
    在本快速入门文章中，我们针对图形 API 编程，因此，在填写表单时，请选择“Gremlin (图形)”。 如果有来自目录应用的文档数据、键/值（表）数据或从 MongoDB 应用迁移的数据，请意识到 Azure Cosmos DB 可以为所有任务关键型应用程序提供高度可用的全球分布式数据库服务平台。

    将以下屏幕截图中的信息作为指南，填写“新建帐户”边栏选项卡上的各字段。 你的值可能与屏幕截图中的值不同。
 
    ![“新建帐户”边栏选项卡](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

    设置|建议的值|说明
    ---|---|---
    ID|*唯一值*|标识此 Azure Cosmos DB 帐户的唯一名称。 由于 documents.azure.com 将追加到用户提供的 ID 用于创建 URI，因此，请使用唯一但可识别的 ID。 ID 必须仅包含小写字母、数字和连字符 (-) 字符， 并且必须包含 3 到 50 个字符。
    API|Gremlin（图形）|在本文的后面部分中，我们将针对[图形 API](../articles/cosmos-db/graph-introduction.md) 编程。|
    订阅|用户的订阅|要用于此 Azure Cosmos DB 帐户的 Azure 订阅。 
    资源组|*与 ID 相同的值*|帐户的新资源组名称。 为简单起见，可以使用与 ID 相同的名称。 
    位置|
            *离用户最近的区域*|要在其中托管 Azure Cosmos DB 帐户的地理位置。 选择离用户最近的位置，使其能够以最快的速度访问数据。

4. 选择“创建”可创建帐户。

5. 在工具栏上，选择“通知”图标![通知图标](./media/cosmos-db-create-dbaccount-graph/notification-icon.png)可监视部署过程。

    ![Azure 门户“通知”窗格](./media/cosmos-db-create-dbaccount-graph/notification.png)

6. 当“通知”窗口指示部署成功时，请关闭窗口。 从“仪表板”上的“所有资源”磁贴打开新帐户。 

    ![“所有资源”磁贴](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-all-resources.png)