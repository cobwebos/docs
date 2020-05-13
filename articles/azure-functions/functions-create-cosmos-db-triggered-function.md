---
title: 创建由 Azure Cosmos DB 触发的函数
description: 使用 Azure Functions 创建当数据添加到 Azure Cosmos DB 调用的无服务器函数。
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: how-to
ms.date: 04/28/2020
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: c16bd728fe81796d671762615ec8dc4ad6e1d87d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123719"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>创建由 Azure Cosmos DB 触发的函数

了解如何创建在数据添加到 Azure Cosmos DB 或在其中更改数据时触发的函数。 若要了解有关 Azure Cosmos DB 的详细信息，请参阅 [Azure Cosmos DB：使用 Azure Functions 的无服务器数据库计算](../cosmos-db/serverless-computing-database.md)。

:::image type="content" source="./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png" alt-text="Azure Cosmos DB 代码":::

## <a name="prerequisites"></a>必备条件

完成本教程：

+ 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="sign-in-to-azure"></a>登录 Azure
使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

在创建触发器之前，必须已拥有使用 SQL API 的 Azure Cosmos DB 帐户。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>创建 Azure Function App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

接下来，在新的 Function App 中创建一个函数。

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>创建 Azure Cosmos DB 触发器

1. 在 function app 中，从左侧菜单中选择 "**函数**"，然后从顶部菜单中选择 "**添加**"。 

1. 在 "**新建函数**" 页上， `cosmos` 在搜索字段中输入，然后选择 " **Azure Cosmos DB 触发器**" 模板。

   :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-choose-cosmos.png" alt-text="Azure 门户中的 "函数" 页":::


1. 按照下表中指定的设置配置新触发器：

    | 设置      | 建议的值  | 说明                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **新建函数** | 接受默认名称 | 函数的名称。 |
    | **Cosmos DB 帐户连接** | 接受默认新名称 | 选择 "**新建**"，先前创建的**数据库帐户**，然后单击 **"确定"**。 此操作为帐户连接创建应用程序设置。 此设置由数据库连接的绑定使用。 |
    | **数据库名称** | 任务 | 包含要监视的集合的数据库的名称。 |
    | 集合名称**** | 项 | 要监视的集合的名称。 |
    | **租用的集合名称** | 租用 | 用于存储租用的集合的名称。 |
    | **创建租约集合（如果不存在）** | 是 | 检查是否存在租约集合并自动创建它。 |

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png" alt-text="创建 Azure Cosmos DB 触发函数":::

1. 选择 "**创建函数**"。 

    Azure 创建 Cosmos DB 触发器函数。

1. 若要显示基于模板的函数代码，请选择 "**代码 + 测试**"。

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png" alt-text="运用 C# 语言的 Cosmos DB 函数模板":::

    此函数模板将文档的数量和第一个文档 ID 写入到日志中。

接下来，连接到 Azure Cosmos DB 帐户并在 `Tasks` 数据库中创建 `Items` 容器。

## <a name="create-the-items-container"></a>创建 Items 容器

1. 在浏览器的新选项卡中打开 [Azure 门户](https://portal.azure.com)的第二个实例。

1. 在门户左侧展开图标栏，在搜索字段中键入 `cosmos`，然后选择“Azure Cosmos DB”****。

    ![搜索 Azure Cosmos DB 服务](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. 选择你的 Azure Cosmos DB 帐户，然后选择“数据资源管理器”****。 

1. 在 **SQL API** 下，选择“Tasks”**** 数据库，然后选择“新建容器”****。

    ![创建容器](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. 在“添加容器”**** 中，使用图像下的表中所示的设置。 

    ![定义 Tasks 容器](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | 设置|建议的值|说明 |
    | ---|---|--- |
    | **数据库 ID** | 任务 |新数据库的名称。 它必须匹配函数绑定中定义的名称。 |
    | **容器 ID** | Items | 新容器的名称。 它必须匹配函数绑定中定义的名称。  |
    | **[分区键](../cosmos-db/partition-data.md)** | /category|一个分区键，用于将数据均匀分配到每个分区。 选择正确的分区键对于创建高性能容器而言很重要。 | 
    | **吞吐量** |400 RU| 使用默认值。 如果想要减少延迟，以后可以增加吞吐量。 |    

1. 单击“确定”**** 以创建 Items 容器。 创建该容器可能需要很短的时间。

函数绑定中指定的容器存在后，可以通过向此新容器添加项来测试函数。

## <a name="test-the-function"></a>测试函数

1. 在数据资源管理器中展开新的 **Items** 容器，选择“项”****，然后选择“新建项”****。

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png" alt-text="在 Items 容器中创建项":::

1. 将新项的内容替换为以下内容，然后选择“保存”****。

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. 切换到包含门户中的函数的第一个浏览器选项卡。 展开函数日志并验证新的文档已触发该函数。 会看到 `task1` 文档 ID 值已写入到日志。 

    ![查看日志中的消息。](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. （可选）返回到文档进行更改，然后单击“更新”****。 然后，回到函数日志，并验证此更新同样已触发该函数。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤

你已经创建一个函数，当在 Azure Cosmos DB 中添加或修改文档时会运行此函数。 有关 Azure Cosmos DB 触发器的详细信息，请参阅[适用于 Azure Functions 的 Azure Cosmos DB 绑定](functions-bindings-cosmosdb.md)。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
