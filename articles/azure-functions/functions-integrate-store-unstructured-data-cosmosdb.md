---
title: 使用 Azure Cosmos DB 和 Azure Functions 存储非结构化数据
description: 使用 Azure Functions 和 Cosmos DB 存储非结构化数据
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5bc3895cb219338acde492b871dce806db70622b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91661153"
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>使用 Azure Functions 和 Azure Cosmos DB 存储非结构化数据

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 是存储非结构化数据和 JSON 数据的良好方式。 将 Cosmos DB 与 Azure Functions 结合使用，可以快速、轻松地存储数据，并且所需的代码也比在关系数据库中存储数据时所需的代码少得多。

> [!NOTE]
> 此时，Azure Cosmos DB 触发器、输入绑定和输出绑定仅可与 SQL API 和图形 API 帐户一起使用。

在 Azure Functions 中，输入和输出绑定提供从函数连接到外部服务数据的声明性方式。 本文介绍了如何更新现有的函数，以便添加输出绑定，在 Azure Cosmos DB 文档中存储非结构化数据。

## <a name="prerequisites"></a>先决条件

为完成此教程：

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

在创建输出绑定之前，必须已具有使用 SQL API 的 Azure Cosmos DB 帐户。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-an-output-binding"></a>添加输出绑定

1. 在 Azure 门户中，导航到之前创建的函数应用并选择它。

1. 选择“函数”，然后选择 HttpTrigger 函数。

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-select-http-function.png" alt-text="在 Azure 门户中选择 Http 函数。" border="true":::

1. 选择“集成”和“+ 添加输出”。

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-add-output-binding.png" alt-text="在 Azure 门户中选择 Http 函数。" border="true":::

1. 根据表中的指定使用“创建输出”设置：

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-configure-cosmosdb-binding.png" alt-text="在 Azure 门户中选择 Http 函数。" border="true":::

    | 设置      | 建议的值  | 说明                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **绑定类型** | Azure Cosmos DB | 要选择的绑定类型的名称，用于创建到 Azure Cosmos DB 的输出绑定。 |
    | 文档参数名称 | taskDocument | 引用代码中的 Cosmos DB 对象的名称。 |
    | **数据库名称** | taskDatabase | 用于保存文档的数据库的名称。 |
    | 集合名称 | taskCollection | 数据库集合的名称。 |
    | 如果为 true，则创建 Cosmos DB 数据库和集合 | 是 | 集合不存在，因此创建集合。 |
    | Cosmos DB 帐户连接 | 新设置 | 选择“新建”，然后选择前面创建的 Azure Cosmos DB 帐户和数据库帐户，然后选择“确定”。 为帐户连接创建应用程序设置。 此设置由数据库连接的绑定使用。 |

1. 选择“确定”以创建绑定。

## <a name="update-the-function-code"></a>更新函数代码

根据你选择的语言将现有函数代码替换为以下代码：

# <a name="c"></a>[C#](#tab/csharp)

将现有 C# 函数替换为以下代码：

```csharp
#r "Newtonsoft.Json"

using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, out object taskDocument, ILogger log)
{
    string name = req.Query["name"];
    string task = req.Query["task"];
    string duedate = req.Query["duedate"];

    // We need both name and task parameters.
    if (!string.IsNullOrEmpty(name) && !string.IsNullOrEmpty(task))
    {
        taskDocument = new
        {
            name,
            duedate,
            task
        };

        return (ActionResult)new OkResult();
    }
    else
    {
        taskDocument = null;
        return (ActionResult)new BadRequestResult();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

将现有 JavaScript 函数替换为以下代码：

```js
module.exports = async function (context, req) {

    // We need both name and task parameters.
    if (req.query.name && req.query.task) {

        // Set the output binding data from the query object.
        context.bindings.taskDocument = req.query;

        // Success.
        context.res = {
            status: 200
        };
    }
    else {
        context.res = {
            status: 400,
            body: "The query options 'name' and 'task' are required."
        };
    }
};
```
---

此代码示例读取 HTTP 请求查询字符串，并将其分配到 `taskDocument` 对象中的字段。 `taskDocument` 绑定从此绑定参数发送对象数据，该参数将存储在已绑定文档数据库中。 该数据库在首次运行函数时创建。

## <a name="test-the-function-and-database"></a>测试函数和数据库

1. 选择“测试/运行”。 在“查询”下选择“+ 添加参数”，然后将以下参数添加到查询字符串： 

    + `name`
    + `task`
    + `duedate`

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png" alt-text="在 Azure 门户中选择 Http 函数。" border="true":::


1. 选择“运行”并验证是否返回了 200 状态。

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function-output.png" alt-text="在 Azure 门户中选择 Http 函数。" border="true":::


1. 在 Azure 门户中，搜索并选择“Azure Cosmos DB”。

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png" alt-text="在 Azure 门户中选择 Http 函数。" border="true":::

1. 选择 Azure Cosmos DB 帐户，然后选择“数据资源管理器”。

1. 展开“TaskCollection”节点，选择新的文档，确认该文档包含查询字符串值以及一些其他的元数据。

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-data-explorer-check-document.png" alt-text="在 Azure 门户中选择 Http 函数。" border="true":::

你已成功地将绑定添加到 HTTP 触发器，用以在 Azure Cosmos DB 中存储非结构化数据。

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

若要详细了解如何绑定到 Cosmos DB 数据库，请参阅 [Azure Functions Cosmos DB 绑定](functions-bindings-cosmosdb.md)。

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps-2.md)]
