---
title: 创建由 Azure Cosmos DB 触发的函数 | Microsoft Docs
description: 使用 Azure Functions 创建当数据添加到 Azure Cosmos DB 调用的无服务器函数。
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/27/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 9ba7d8c403a7778a52b858a41ad41bca405cb199
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38586915"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>创建由 Azure Cosmos DB 触发的函数

了解如何创建在数据添加到 Azure Cosmos DB 或在其中更改数据时触发的函数。 若要了解有关 Azure Cosmos DB 的详细信息，请参阅 [Azure Cosmos DB：使用 Azure Functions 的无服务器数据库计算](..\cosmos-db\serverless-computing-database.md)。

![在日志中查看消息。](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>先决条件

完成本教程：

+ 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-function-app"></a>创建 Azure Function App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

接下来，在新的 Function App 中创建一个函数。

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>创建 Azure Cosmos DB 触发器

1. 展开 Function App，单击“Functions”旁边的 + 按钮。 如果这是 Function App 中的第一个函数，请选择“自定义函数”。 此时将显示函数模板的完整集合。

    ![Azure 门户中的 Functions 快速入门页](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

2. 在搜索栏中键入 `cosmos`，然后选择需要用于 Azure Cosmos DB 触发器模板的语言。

    ![选择 Azure Cosmos DB 触发器](./media/functions-create-cosmos-db-triggered-function/select-cosmos-db-trigger-portal.png)

3. 使用图像下面的表中指定的设置来配置新的触发器。

    ![创建 Azure Cosmos DB 触发函数](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)
    
    | 设置      | 建议的值  | 说明                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Name** | 默认 | 使用模板建议的默认函数名称。 |
    | 集合名称 | Items | 要监视的集合的名称。 |
    | **创建租赁集合（如果不存在）** | 已选中 | 集合不存在，因此创建集合。 |
    | **数据库名称** | 任务 | 数据库的名称和要监视的集合。 |

4. 选择“Azure Cosmos DB 帐户连接”标签旁边的“新建”，然后选择现有的 Cosmos DB 帐户或“+ 新建”。 
 
    ![配置 Azure Cosmos DB 连接](./media/functions-create-cosmos-db-triggered-function/functions-create-CosmosDB.png)

6. 创建新的 Cosmos DB 帐户时，请按照表中的指定使用“新帐户”设置。

    | 设置      | 建议的值  | 说明                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **ID** | 数据库的名称 | Azure Cosmos DB 数据库的唯一 ID  |
    | **API** | SQL | 本主题使用 SQL API。  |
    | **订阅** | Azure 订阅 | 要在其下创建此新 Cosmos DB 帐户的订阅。  |
    | **资源组** | myResourceGroup |  使用包含函数应用的现有资源组。 |
    | **位置**  | 西欧 | 选择一个靠近函数应用的位置，或者一个靠近的其他应用使用已存储文档的位置。  |

6. 单击“确定”创建该数据库。 创建数据库可能需要几分钟的时间。 创建数据库后，数据库连接字符串存储为函数应用设置。 此应用设置的名称插入 Azure Cosmos DB 帐户连接中。 

7. 单击“创建”创建你的 Azure Cosmos DB 触发函数。 创建函数后，将显示基于模板的函数代码。  

    ![运用 C# 语言的 Cosmos DB 函数模板](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    此函数模板将文档的数量和第一个文档 ID 写入到日志中。 

接下来，连接到你的 Azure Cosmos DB 帐户并在数据库中创建“任务”集合。 

## <a name="create-the-items-collection"></a>创建项集合

1. 在浏览器的新选项卡中打开 [Azure 门户](https://portal.azure.com)的第二个实例。 

2. 在门户左侧展开图标栏，在搜索字段中键入 `cosmos`，然后选择“Azure Cosmos DB”。

    ![搜索 Azure Cosmos DB 服务](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

2. 选择你的 Azure Cosmos DB 帐户，然后选择“数据资源管理器”。 
 
3. 在“集合”中，选择“taskDatabase”并选择“新集合”。

    ![创建集合](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

4. 在“添加集合”中，使用图像下的表中所示的设置。 
 
    ![定义 taskCollection](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)
 
    | 设置|建议的值|说明 |
    | ---|---|--- |
    | **数据库 ID** | 任务 |新数据库的名称。 它必须匹配函数绑定中定义的名称。 |
    | **集合 ID** | Items | 新集合的名称。 它必须匹配函数绑定中定义的名称。  |
    | **存储容量** | 固定 (10 GB)|使用默认值。 此值是数据库的存储容量。 |
    | **吞吐量** |400 RU| 使用默认值。 如果想要减少延迟，以后可以增加吞吐量。 |
    | **[分区键](../cosmos-db/partition-data.md#design-for-scale)** | /category|一个分区键，用于将数据均匀分配到每个分区。 选择正确的分区键对于创建高性能集合而言很重要。 | 

1. 单击“确定”创建“任务”集合。 创建该集合可能需要一点时间。

函数绑定中指定的集合存在后，可以通过将文档添加到此新集合来测试函数。

## <a name="test-the-function"></a>测试函数

1. 展开数据资源管理器中的新“taskCollection”集合，选择“文档”，然后选择“新建文档”。

    ![在 taskCollection 创建文档](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

2. 将新文档的内容替换为以下内容，然后选择“保存”。

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. 切换到包含门户中的函数的第一个浏览器选项卡。 展开函数日志并验证新的文档已触发该函数。 会看到 `task1` 文档 ID 值已写入日志。 

    ![在日志中查看消息。](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

4. （可选）返回到文档进行更改，然后单击“更新”。 然后，回到函数日志，并验证此更新同样已触发该函数。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤

你已经创建一个函数，当在 Azure Cosmos DB 中添加或修改文档时会运行此函数。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

有关 Azure Cosmos DB 触发器的详细信息，请参阅[适用于 Azure Functions 的 Azure Cosmos DB 绑定](functions-bindings-cosmosdb.md)。
