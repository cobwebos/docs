---
title: 使用模板部署 Web 应用 - Azure Cosmos DB
description: 了解如何使用 Azure 资源管理器模板部署 Azure Cosmos 帐户、Azure 应用服务 Web 应用以及示例 Web 应用程序。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 8e6a6d1c557a765e55152685f08e80ad54bbd903
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362004"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板从 GitHub 部署 Azure Cosmos DB、Azure 应用服务与 Web 应用

本教程介绍如何在首次运行时对连接到 Azure Cosmos DB 的 Web 应用程序执行“无接触”部署，而无需在 Azure 门户中将 Azure Cosmos DB 中的任何连接信息剪切并粘贴到 `appsettings.json` 或 Azure 应用服务应用程序设置。 所有这些操作均使用 Azure 资源管理器模板一次性完成。 在此处的示例中，将从 [Web 应用教程](sql-api-dotnet-application.md)部署 [Azure Cosmos DB ToDo 示例](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)。

资源管理器模板非常灵活，允许你在 Azure 的任何服务之间编写复杂的部署。 这包括诸如从 GitHub 部署应用程序及在 Azure 门户中将连接信息注入 Azure 应用服务的应用程序设置等高级任务。 本教程演示如何使用单个资源管理器模板执行以下操作。

* 部署 Azure Cosmos 帐户。
* 部署 Azure 应用服务托管计划。
* 部署 Azure 应用服务。
* 在 Azure 门户中将 Azure Cosmos 帐户中的终结点和密钥注入应用服务应用程序设置。
* 将 Web 应用程序从 GitHub 存储库部署到应用服务。

产生的部署拥有功能齐全的 Web 应用程序，它可连接到 Azure Cosmos DB，而无需从 Azure 门户剪切并粘贴 Azure Cosmos DB 的终结点 URL 或身份验证密钥。

## <a name="prerequisites"></a>先决条件

> [!TIP]
> 虽然本教程不会假设先前有使用 Azure 资源管理器模板或 JSON 的经验，但是，如果想修改引用的模板或部署选项，则需要有其中每个领域的知识。

## <a name="step-1-deploy-the-template"></a>步骤 1：部署模板

首先选择下面的“部署到 Azure”按钮，打开 Azure 门户以创建自定义部署。 还可以从 [Azure 快速启动模板库](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)查看 Azure 资源管理模板

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

位于 Azure 门户后，选择要部署到的订阅并选择或创建新的资源组。 然后填写以下值。

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="模板部署 UI 的屏幕截图":::

* **区域** - 资源管理器需要此项。 输入位置参数所用的相同区域（资源所在位置）。
* **应用程序名称** - 此名称由此部署的所有资源使用。 确保选择唯一名称以避免与现有 Azure Cosmos DB 和应用服务帐户冲突。
* **位置** - 部署资源的区域。
* **应用服务计划层** - 应用服务计划的定价层。
* **应用服务计划实例** - 应用服务计划的辅助角色数目。
* **存储库 URL** - GitHub 上的 Web 应用程序的存储库。
* **分支** - GitHub 存储库的分支。
* **数据库名称** - Azure Cosmos 数据库的名称。
* **容器名称** - Azure Cosmos 容器的名称。

填写这些值后，选择“创建”按钮以开始部署。 此步骤应在 5 到 10 分钟内完成。

> [!TIP]
> 该模板不验证模板中输入的 Azure 应用服务名称和 Azure Cosmos 帐户名称是否有效和可用。 强烈建议在提交部署之前，先确认你打算提供的名称的可用性。


## <a name="step-2-explore-the-resources"></a>步骤 2：浏览资源

### <a name="view-the-deployed-resources"></a>查看已部署的资源

模板部署资源后，可以在资源组中看到每个资源。

:::image type="content" source="./media/create-website/resource-group.png" alt-text="资源组":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>查看 Cosmos DB 终结点和密钥

接下来在门户中打开 Azure Cosmos 帐户。 以下屏幕截图显示 Azure Cosmos 帐户的终结点和密钥。

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Cosmos 密钥":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>查看应用程序设置中的 Azure Cosmos DB 密钥

接下来导航到资源组中的 Azure 应用服务。 单击“配置”选项卡以查看应用服务的应用程序设置。 应用程序设置包含连接到 Cosmos DB 所需的 Cosmos DB 帐户和主密钥值，以及从模板部署中传入的数据库和容器名称。

:::image type="content" source="./media/create-website/application-settings.png" alt-text="应用程序设置":::

### <a name="view-web-app-in-deployment-center"></a>在部署中心查看 Web 应用

接下来转到应用服务的部署中心。 此处将看到存储库指向传入模板的 GitHub 存储库。 另外下面的“状态”指示“成功(活动)”，这意味着应用程序已成功部署并启动。

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="部署中心":::

### <a name="run-the-web-application"></a>运行 Web 应用程序

单击部署中心顶部的“浏览”以打开 Web 应用程序。 Web 应用程序将打开主屏幕。 单击“新建”并在字段中输入一些数据，然后单击“保存”。 产生的屏幕显示数据保存到 Cosmos DB。

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="主屏幕":::

## <a name="step-3-how-does-it-work"></a>步骤 3：工作原理

此操作成功需要三个元素。

### <a name="reading-app-settings-at-runtime"></a>在运行时读取应用程序设置

首先，应用程序需要请求 ASP.NET MVC Web 应用程序中 `Startup` 类的 Cosmos DB 终结点和密钥。 [Cosmos DB To Do 示例](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)可在本地运行，可在其中将连接信息输入 appsettings.json。 但在部署时，此文件将与应用一起部署。 如果红色框内的这些行无法从 appsettings.json 访问设置，它将尝试从 Azure 应用服务的应用程序设置中访问。

:::image type="content" source="./media/create-website/startup.png" alt-text="屏幕截图显示了一个方法，其中有多个用红色标记的字符串变量，包括 databaseName、容器名称、帐户和密钥。":::

### <a name="using-special-azure-resource-management-functions"></a>使用特殊的 Azure 资源管理函数

为了使这些值在部署时可用于应用程序，Azure 资源管理器模板可以使用特殊的 Azure 资源管理函数（包括 [引用](../azure-resource-manager/templates/template-functions-resource.md#reference) 和 [listKeys](../azure-resource-manager/templates/template-functions-resource.md#listkeys)）从 Cosmos DB 帐户请求这些值，这些函数从 Cosmos DB 帐户获取值并将其插入应用程序设置值，这些设置值具有与上述应用程序中所用密钥名称相匹配的密钥名称且格式为 {section:key}。 例如，`CosmosDb:Account`。

:::image type="content" source="./media/create-website/template-keys.png" alt-text="模板密钥":::

### <a name="deploying-web-apps-from-github"></a>从 GitHub 部署 Web 应用

最后，我们需要将 Web 应用程序从 GitHub 部署到应用服务。 使用下面的 JSON 完成此操作。 需要注意的两点是此资源的类型和名称。 `"type": "sourcecontrols"` 和 `"name": "web"` 属性值都是硬编码的，不应更改。

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="从 GitHub 进行部署":::

## <a name="next-steps"></a>后续步骤

祝贺你！ 已部署 Azure Cosmos DB、Azure 应用服务，以及自动拥有连接到 Cosmos DB 所需的连接信息的示例 Web 应用程序，一个操作即可完成一切，而无需剪切和粘贴敏感信息。 使用此模板作为起点，可以修改它以相同方式部署自己的 Web 应用程序。

* 有关此示例的 Azure 资源管理器模板，可转到 [Azure 快速启动模板库](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)查找。
* 有关示例应用的源代码，可转到 [GitHub 上的 Cosmos DB To Do 应用](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)查找。
