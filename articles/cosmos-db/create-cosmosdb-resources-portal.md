---
title: 快速入门 - 从 Azure 门户创建 Azure Cosmos DB 资源
description: 本快速入门介绍如何使用 Azure 门户创建 Azure Cosmos 数据库、容器和项。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/05/2020
ms.openlocfilehash: 31bdcd9994ebfcea77b25422997463cc6a9bfddc
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053501"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>快速入门：从 Azure 门户创建 Azure Cosmos 帐户、数据库、容器和项

> [!div class="op_single_selector"]
> * [Azure 门户](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可以使用 Azure Cosmos DB 快速创建和查询键/值数据库、文档数据库和图形数据库，所有这些资源都可受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本快速入门演示如何使用 Azure 门户创建 Azure Cosmos DB [SQL API](sql-api-introduction.md) 帐户、创建文档数据库和容器，并将数据添加到容器。 

## <a name="prerequisites"></a>先决条件

Azure 订阅，或免费的 Azure Cosmos DB 试用帐户
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

转到 [Azure 门户](https://portal.azure.com/)以创建 Azure Cosmos DB 帐户。 搜索“Azure Cosmos DB”，然后选择它。

   :::image type="content" source="./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png" alt-text="Azure 门户“数据库”窗格":::

1. 选择 **添加** 。
1. 在“创建 Azure Cosmos DB 帐户”页上，输入新 Azure Cosmos 帐户的基本设置。 

    |设置|值|说明 |
    |---|---|---|
    |订阅|订阅名称|选择要用于此 Azure Cosmos 帐户的 Azure 订阅。 |
    |资源组|资源组名称|选择一个资源组，或者选择“新建”，然后输入新资源组的唯一名称。 |
    |帐户名|唯一的名称|输入标识此 Azure Cosmos 帐户的名称。 由于 documents.azure.com 将追加到所提供的名称以创建 URI，因此，请使用唯一的名称。<br><br>名称只能包含小写字母、数字和连字符 (-)。 它的长度必须介于 3 到 31 个字符之间。|
    |API|要创建的帐户的类型|选择“Core (SQL)”，以便使用 SQL 语法创建文档数据库并进行查询。 <br><br>API 确定要创建的帐户的类型。 Azure Cosmos DB 提供五种 API：适用于文档数据的 Core (SQL) 和 MongoDB、适用于图形数据的 Gremlin、Azure 表和 Cassandra。 目前，你必须为每种 API 创建单独的帐户。 <br><br>[详细了解 SQL API](introduction.md)。|
    |应用免费层折扣|应用或不应用|使用 Azure Cosmos DB 免费层，你将在帐户中获得每秒的前 400 RU 免费的吞吐量和 5 GB 的免费存储。 了解[免费层](https://azure.microsoft.com/pricing/details/cosmos-db/)的详细信息。|
    |位置|离用户最近的区域|选择用于托管 Azure Cosmos DB 帐户的地理位置。 使用离用户最近的位置，使他们能够以最快的速度访问数据。|
    |帐户类型|生产或非生产|如果帐户将用于生产工作负荷，请选择“生产”。 如果帐户将用于非生产环境（例如开发、测试、QA 或过渡），请选择“非生产”。 这是一个 Azure 资源标记设置，用于调整门户体验，但不会影响基础 Azure Cosmos DB 帐户。 可以随时更改此值。|
    |异地冗余|启用或禁用|通过将你的区域与另一区域进行配对来启用或禁用帐户的全局分发。 稍后可以将更多区域添加到帐户。|
    |多区域写入|启用或禁用|借助多区域写入功能，可以利用全球数据库和容器的预配吞吐量。|
    |可用性区域|启用或禁用|可用性区域有助于进一步提高应用程序的可用性和复原能力。|


> [!NOTE]
> 每个 Azure 订阅最多可以有一个免费层 Azure Cosmos DB 帐户，并且你必须在创建帐户时选择加入使用。 如果看不到用于应用免费层折扣的选项，这意味着订阅中的另一个帐户已启用免费层。
   
   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail.png" alt-text="Azure Cosmos DB 的“新建帐户”页面":::

1. 选择“查看 + 创建”。 可以跳过“网络”和“标记”部分 。

1. 检查帐户设置，然后选择“创建”。 创建帐户需要几分钟时间。 等待门户页显示“你的部署已完成”消息。 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Azure 门户“通知”窗格":::

1. 选择“转到资源”，转到 Azure Cosmos DB 帐户页。 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png" alt-text="Azure Cosmos DB 帐户页面":::

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>添加数据库和容器 

可以使用 Azure 门户中的数据资源管理器来创建数据库和容器。 

1.  在 Azure Cosmos DB 帐户页上的左侧导航栏中选择“数据资源管理器”，然后选择“新建容器”。 
    
    可能需要向右滚动才能看到“添加容器”窗口。
    
    :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png" alt-text="Azure 门户 >“数据资源管理器”>“添加集合”窗格":::
    
1.  在“添加容器”窗格中，输入新容器的设置。
    
    |设置|建议的值|说明
    |---|---|---|
    |**数据库 ID**|ToDoList|输入 ToDoList 作为新数据库的名称。 数据库名称必须包含 1 到 255 个字符，不能包含 `/, \\, #, ?` 或尾随空格。 选中“预配数据库吞吐量”选项，这样就可以在数据库中的所有容器之间共享预配给该数据库的吞吐量。 此选项还有助于节省成本。 |
    |**吞吐量**|400|将吞吐量保留为每秒 400 个请求单位 (RU/s)。 如果想要减少延迟，以后可以增加吞吐量。| 
    |**容器 ID**|Items|输入 *Items* 作为新容器的名称。 容器 ID 与数据库名称的字符要求相同。|
    |**分区键**| /category| 本文中所述的示例使用 /category 作为分区键。|

    
    对于本示例，请不要添加“唯一键”。 使用唯一键可将数据完整性层添加到数据库，因为它能确保每个分区键的一个或多个值的唯一性。 有关详细信息，请参阅 [Azure Cosmos DB 中的唯一键](unique-keys.md)。
    
1.  选择“确定”。 数据资源管理器将显示新建的数据库和容器。

## <a name="add-data-to-your-database"></a>将数据添加到数据库

使用数据资源管理器将数据添加到新的数据库。

1. 在“数据资源管理器”中展开“ToDoList”数据库，然后展开“项”容器。 接下来，依次选择“项”、“新建项”。 
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png" alt-text="在 Azure 门户的数据资源管理器中创建新文档":::
   
1. 在“文档”窗格的右侧，将以下结构添加到文档：

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. 选择“保存”。
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png" alt-text="通过复制添加 JSON 数据，然后在 Azure 门户上的数据资源管理器中选择“保存”":::
   
1. 再次选择“新建文档”，创建并保存具有唯一 `id` 的另一个文档，以及所需的其他任何属性和值。 文档可以采用任何结构，因为 Azure Cosmos DB 不会对数据施加任何架构。

## <a name="query-your-data"></a>查询数据

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

如果希望仅删除数据库并在将来使用 Azure Cosmos 帐户，则可以通过以下步骤删除数据库：

* 转到 Azure Cosmos 帐户。
* 打开**数据资源管理器**，右键单击要删除的数据库，然后选择“删除数据库”。
* 输入数据库 ID/数据库名称以确认删除操作。 

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建 Azure Cosmos DB 帐户，以及如何使用数据资源管理器创建数据库和容器。 现在可以将其他数据导入 Azure Cosmos DB 帐户了。 

> [!div class="nextstepaction"]
> [将数据导入 Azure Cosmos DB](import-data.md)
