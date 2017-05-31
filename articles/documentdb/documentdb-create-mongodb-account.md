---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-create-account
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4b2ae4a8d3b93b5c27e180a5875ae9e90483068a
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017



---

# <a name="create-an-azure-cosmos-db-account-with-mongodb-api"></a>使用 MongoDB API 创建 Azure Cosmos DB 帐户
现在可以将 Azure Cosmos DB 数据库用作为 MongoDB 编写的应用的数据存储。 若要使用此功能，需要 Azure 帐户和 Azure Cosmos DB 帐户。 本教程将指导用户创建用于 MongoDB 应用的 Azure Cosmos DB 帐户。 

可使用 Azure 门户或者 Azure CLI（结合 Azure Resource Manager 模板）创建支持 MongoDB 帐户的 Azure Cosmos DB。 本文说明如何使用 Azure 门户创建支持 MongoDB 帐户的 Azure Cosmos DB。 若要配合使用 Azure CLI 和 Azure Resource Manager 来创建帐户，请参阅[使用 Azure CLI 2.0 自动执行 Azure Cosmos DB 帐户管理](documentdb-automation-resource-manager-cli.md)。

## <a name="prerequisite"></a>先决条件
一个 Azure 帐户。 如果没有 Azure 帐户，则创建[免费的 Azure 帐户](https://azure.microsoft.com/free/)。
## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

1. 在 Internet 浏览器中，登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧导航窗格中，单击“Azure Cosmos DB”。

    ![门户左侧导航栏中的屏幕截图，其中突出显示 DocumentDB NoSQL 条目](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. 或者，单击“更多服务>”，在顶部搜索栏中键入 **DocumentDB**，然后单击“Azure Cosmos DB”。

    ![“更多服务”边栏选项卡的屏幕截图，可在其中搜索 DocumentDB NoSQL 条目](./media/documentdb-create-mongodb-account/more-services-search.PNG)

4. 在“Azure Cosmos DB”边栏选项卡顶部的操作栏顶部，单击“+添加”。

    ![“Cosmos DB 资源”边栏选项卡上“添加”按钮的屏幕截图](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. 在“Azure Cosmos DB 帐户”边栏选项卡中，指定帐户的所需配置。

   ![具有 MongoDB 协议支持的新建 Azure Cosmos DB 的边栏选项卡屏幕截图](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.PNG)

    - 在“ID”框中，输入一个名称用于标识帐户。  对“ID”进行验证后，“ID”框中会出现一个绿色的复选标记。  该“ID”值将成为 URI 中的主机名。  “ID”只能包含小写字母、数字及“-”字符，且长度必须为 3 到 50 个字符。 请注意，*documents.azure.com* 会追加到所选择的终结点名称，其结果将成为帐户终结点。

    - 对于“API”，请选择“MongoDB”。 这将指定要用于与 Azure Cosmos DB 数据库进行交互的通信 API。

    - 对于“订阅”，请选择要用于帐户的 Azure 订阅。 如果帐户只有一个订阅，则默认为选择该帐户。

    - 在“资源组”中，为帐户选择或创建资源组。  默认情况下，会选择 Azure 订阅下的现有资源组。  但是，可以选择创建要将帐户添加到其中的新资源组。 有关详细信息，请参阅 [使用 Azure 门户管理 Azure 资源](../azure-portal/resource-group-portal.md)。

    - 使用“位置”指定在其中托管帐户的地理位置。

6. 在配置了新的帐户选项后，单击“创建”。  可能需要几分钟来创建帐户。

   可以从通知中心监视进度。  

   ![通知中心的屏幕截图，其中显示正在创建 Azure Cosmos DB 帐户](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. 若要访问新帐户，请单击左侧菜单上的“Azure Cosmos DB”。 在常规的 DocumentDB 帐户和具有 Mongo 协议支持的 DocumentDB 帐户列表中，单击新帐户名称。
8. Azure Cosmos DB 帐户现已可用于 MongoDB 应用。

   ![默认帐户边栏选项卡的屏幕截图](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## <a name="next-steps"></a>后续步骤
* 了解如何[连接](documentdb-connect-mongodb-account.md)到具有 MongoDB 协议支持的 DocumentDB 帐户。

