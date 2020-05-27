---
title: 教程 - 使用 Cosmos DB 终结点实现 Azure Databricks
description: 本教程介绍如何使用针对 Cosmos DB 启用的服务终结点在虚拟网络中实现 Azure Databricks。
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: e18c2b0f03f9ac2155c441580d62d6085581de12
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779599"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>教程：使用 Cosmos DB 终结点实现 Azure Databricks

本教程介绍如何使用针对 Cosmos DB 启用的服务终结点实现 VNet 注入 Databricks 环境。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在虚拟网络中创建 Azure Databricks 工作区
> * 创建 Cosmos DB 服务终结点
> * 创建 Cosmos DB 帐户并导入数据
> * 创建 Azure Databricks 群集
> * 从 Azure Databricks 笔记本查询 Cosmos DB

## <a name="prerequisites"></a>先决条件

开始之前，请执行以下操作：

* 在虚拟网络中[ Azure Databricks 工作区](quickstart-create-databricks-workspace-vnet-injection.md)。

* 下载 [Spark 连接器](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar)。

* 从 [NOAA 国家环境信息中心](https://www.ncdc.noaa.gov/stormevents/)下载示例数据。 选择状态或区域，然后选择“搜索”。 在下一页上，接受默认值并选择“搜索”。 然后选择页面左侧的“CSV 下载”以下载结果。

* 下载 Azure Cosmos DB 数据迁移工具的[预编译二进制文件](https://aka.ms/csdmtool)。

## <a name="create-a-cosmos-db-service-endpoint"></a>创建 Cosmos DB 服务终结点

1. 将 Azure Databricks 工作区部署到虚拟网络后，导航到 [Azure 门户](https://portal.azure.com)中的虚拟网络。 请注意通过 Databricks 部署创建的公共和私有子网。

   ![虚拟网络子网](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. 选择“public-subnet”并创建 Cosmos DB 服务终结点。 然后“保存”。
   
   ![添加 Cosmos DB 服务终结点](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>创建 Cosmos DB 帐户

1. 打开 Azure 门户。 在屏幕的左上方，选择“创建资源”>“数据库”>“Azure Cosmos DB”。

2. 使用以下设置填写“基础知识”选项卡上的实例详细信息 ：

   |设置|值|
   |-------|-----|
   |订阅|*用户的订阅*|
   |资源组|你的资源组|
   |帐户名|db-vnet-service-endpoint|
   |API|Core (SQL)|
   |位置|美国西部|
   |异地冗余|禁用|
   |多区域写入|启用|

   ![添加 Cosmos DB 服务终结点](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. 选择“网络”选项卡并配置虚拟网络。 

   a. 选择创建的虚拟网络作为先决条件，然后选择“public-subnet”。 请注意，“private-subnet”包含一条“缺少 Microsoft AzureCosmosDB 终结点”的注释 。 这是因为你仅在“public-subnet”上启用了 Cosmos DB 服务终结点。

   b. 请确保已启用“允许从 Azure 门户访问”。 通过该设置可从 Azure 门户访问 Cosmos DB 帐户。 如果此选项设置为“拒绝”，则在尝试访问帐户时将收到错误。 

   > [!NOTE]
   > 本教程没有必要这样做，但是如果希望能够从本地计算机访问 Cosmos DB 帐户，还可以启用“允许从我的 IP 进行访问”。 例如，若使用 Cosmos DB SDK 连接到帐户，则需启用此设置。 如果禁用该设置，将收到“拒绝访问”错误。

   ![Cosmos DB 帐户网络设置](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. 选择“查看 + 创建”，然后选择“创建”可在虚拟网络中创建 Cosmos DB 帐户 。

5. 创建 Cosmos DB 帐户后，请导航至“设置”下的“密钥” 。 复制主连接字符串并将其保存在文本编辑器中以供以后使用。

    ![Cosmos DB 帐户密钥页](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. 选择“数据资源管理器”和“新集合”，将新数据库和集合添加到 Cosmos DB 帐户中 。

    ![Cosmos DB 新集合](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>将数据上传到 Cosmos DB

1. 打开 [Cosmos DB 的数据迁移工具](https://aka.ms/csdmtool)的图形界面版本，“Dtui.exe”。

    ![Cosmos DB 数据迁移工具](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. 在“源信息”选项卡上，从下拉列表的导入中选择 CSV 文件  。 然后选择“添加文件”并添加所下载的 storm 数据 CSV 作为先决条件。

    ![Cosmos DB 数据迁移工具源信息](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. 在“目标信息”选项卡上，输入连接字符串。 连接字符串格式为 `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`。 AccountEndpoint 和 AccountKey 包含在上一节保存的主连接字符串中。 将 `Database=<your database name>` 附加到连接字符串的末尾，然后选择“验证”。 然后，添加集合名称和分区键。

    ![Cosmos DB 数据迁移工具目标信息](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. 选择“下一步”，直到看到“摘要”页。 然后，选择“导入”。

## <a name="create-a-cluster-and-add-library"></a>创建群集并添加库

1. 在 [Azure 门户](https://portal.azure.com)中导航到 Azure Databricks 服务，然后选择“启动工作区”。

2. 创建新群集。 选择群集名称并接受其余的默认设置。

   ![新的群集设置](./media/service-endpoint-cosmosdb/create-cluster.png)

3. 创建群集后，请导航到群集页并选择“库”选项卡。选择“安装新库”并上传 Spark 连接器 jar 文件以安装库。

    ![安装 Spark 连接器库](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    可验证库是否已安装在“库”选项卡上。

    ![Databricks 群集“库”选项卡](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>从 Databricks 笔记本查询 Cosmos DB

1. 导航到 Azure Databricks 工作区并创建一个新的 python 笔记本。

    ![创建新的 Databricks 笔记本](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. 运行以下 python 代码以设置 Cosmos DB 连接配置。 相应地更改“终结点”、“主密钥”、“数据库”和“集合”   。

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. 使用以下 python 代码加载数据并创建临时视图。

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. 使用以下 magic 命令执行返回数据的 SQL 语句。

    ```python
    %sql
    select * from storm
    ```

    已成功将 VNet 注入 Databricks 工作区连接到启用服务终结点的 Cosmos DB 资源。 要了解有关如何连接到 Cosmos DB 的详细信息，请参阅[适用于 Apache Spark 的 Azure Cosmos DB 连接器](https://github.com/Azure/azure-cosmosdb-spark)。

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、Azure Databricks 工作区以及所有相关资源，请将其删除。 删除作业可避免产生不必要的计费。 若计划将来使用 Azure Databricks 工作区，则可停止群集并在以后重启。 如果不打算继续使用此 Azure Databricks 工作区，请使用以下步骤删除在本教程中创建的所有资源：

1. 在 Azure 门户的左侧菜单中，单击“资源组”，并单击所创建的资源的名称。

2. 在资源组页上选择“删除”，在文本框中键入要删除的资源的名称，然后再次选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本教程中，已将 Azure Databricks 工作区部署到虚拟网络，并使用了 Cosmos DB Spark 连接器在 Databricks 中查询 Cosmos DB 数据。 若要了解有关在虚拟网络中使用 Azure Databricks 的详细信息，请继续阅读有关在 Azure Databricks 中使用 SQL Server 的教程。

> [!div class="nextstepaction"]
> [教程：从 Azure Databricks 笔记本查询虚拟网络中的 SQL Server Linux Docker 容器](vnet-injection-sql-server.md)
