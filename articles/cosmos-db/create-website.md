---
title: 使用模板部署 Web 应用 - Azure Cosmos DB
description: 了解如何使用 Azure 资源管理器模板部署 Azure Cosmos 帐户、Azure App Service Web 应用和示例 web 应用程序。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 5038d9968e37b956774d1c5f8abdb14865422e8b
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027747"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板从 GitHub 中的 web 应用部署 Azure Cosmos DB 和 Azure App Service

本教程介绍如何在首次运行时，对连接到 Azure Cosmos DB 的 web 应用程序执行 "无接触" 部署，而无需在 Azure 门户中剪切和粘贴 Azure Cosmos DB 到 `appsettings.json` Azure 应用服务应用程序设置的任何连接信息。 所有这些操作都是在单个操作中通过使用 Azure 资源管理器模板来完成的。 在此处的示例中，我们将从[Web 应用教程](sql-api-dotnet-application.md)部署[Azure Cosmos DB ToDo 示例](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)。

资源管理器模板非常灵活，允许你在 Azure 中的任何服务之间编写复杂的部署。 这包括高级任务，例如从 GitHub 部署应用程序和将连接信息注入到 Azure 门户 Azure App Service 的应用程序设置中。 本教程将演示如何使用单个资源管理器模板执行以下操作。

* 部署 Azure Cosmos 帐户。
* 部署 Azure App Service 托管计划。
* 部署 Azure App Service。
* 将 Azure Cosmos 帐户中的终结点和密钥注入到 Azure 门户中的应用服务应用程序设置。
* 将 web 应用程序从 GitHub 存储库部署到应用服务。

生成的部署具有完全功能的 web 应用程序，该应用程序可以连接到 Azure Cosmos DB，而不必从 Azure 门户中剪切并粘贴 Azure Cosmos DB 的终结点 URL 或身份验证密钥。

## <a name="prerequisites"></a>先决条件

> [!TIP]
> 虽然本教程不会假设先前有使用 Azure 资源管理器模板或 JSON 的经验，但是，如果想修改引用的模板或部署选项，则需要有其中每个领域的知识。

## <a name="step-1-deploy-the-template"></a>步骤1：部署模板

首先，选择下方的 "**部署到 Azure** " 按钮，打开 Azure 门户以创建自定义部署。 还可以从[Azure 快速入门模板库](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)查看 Azure 资源管理模板

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

在 Azure 门户中，选择要部署到的订阅，然后选择或创建新的资源组。 然后填写以下值。

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="模板部署 UI 的屏幕截图":::

* **区域**-资源管理器需要此项。 输入资源所在位置参数所使用的相同区域。
* **应用程序名称**-此名称由该部署的所有资源使用。 请确保选择一个唯一的名称，以避免与现有 Azure Cosmos DB 和应用服务帐户冲突。
* **位置**-部署资源的区域。
* **应用服务计划层**-应用服务计划的定价层。
* **应用服务计划实例**-应用服务计划的辅助进程数。
* **存储库 URL** -GitHub 上的 web 应用程序的存储库。
* **Branch** -GitHub 存储库的分支。
* **数据库名称**-Azure Cosmos 数据库名称。
* **容器名称**-Azure Cosmos 容器名称。

填写值后，选择 "**创建**" 按钮以开始部署。 此步骤需要5到10分钟才能完成。

> [!TIP]
> 该模板不会验证在模板中输入的 Azure App Service 名称和 Azure Cosmos 帐户名称是否有效且可用。 强烈建议在提交部署之前，先确认你打算提供的名称的可用性。


## <a name="step-2-explore-the-resources"></a>步骤2：浏览资源

### <a name="view-the-deployed-resources"></a>查看部署的资源

模板部署资源后，可以在资源组中看到每个资源。

:::image type="content" source="./media/create-website/resource-group.png" alt-text="资源组":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>查看 Cosmos DB 终结点和密钥

接下来，在门户中打开 Azure Cosmos 帐户。 以下屏幕截图显示了 Azure Cosmos 帐户的终结点和密钥。

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Cosmos 键":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>在应用程序设置中查看 Azure Cosmos DB 密钥

接下来，导航到资源组中的 Azure App Service。 单击 "配置" 选项卡以查看应用服务的应用程序设置。 应用程序设置包含连接到 Cosmos DB 所需的 Cosmos DB 帐户和主键值，以及从模板部署中传入的数据库和容器名称。

:::image type="content" source="./media/create-website/application-settings.png" alt-text="应用程序设置":::

### <a name="view-web-app-in-deployment-center"></a>在部署中心中查看 web 应用

接下来，请前往应用服务的部署中心。 此时，你将看到存储库指向传递给模板的 GitHub 存储库。 下面的状态表示 "成功（活动）"，这意味着应用程序已成功部署并已启动。

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="部署中心":::

### <a name="run-the-web-application"></a>运行 Web 应用程序

单击 "部署中心" 顶部的 "**浏览**" 以打开 web 应用程序。 Web 应用程序将打开到主屏幕。 单击 "**新建**"，并在字段中输入一些数据，然后单击 "保存"。 生成的屏幕将显示保存到 Cosmos DB 的数据。

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="主屏幕":::

## <a name="step-3-how-does-it-work"></a>步骤3：工作原理

此操作需要三个元素。

### <a name="reading-app-settings-at-runtime"></a>在运行时读取应用程序设置

首先，应用程序需要在 `Startup` ASP.NET MVC web 应用程序的类中请求 Cosmos DB 终结点和密钥。 [Cosmos DB 的示例](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)可在本地运行，您可以在其中输入连接信息到 appsettings.js。 但是，在部署时，此文件将与应用程序一起部署。 如果这三行代码无法从 appsettings.js上的设置中访问，它将尝试 Azure App Service 中的应用程序设置。

:::image type="content" source="./media/create-website/startup.png" alt-text="启动":::

### <a name="using-special-azure-resource-management-functions"></a>使用特殊的 Azure 资源管理功能

若要在部署时将这些值提供给应用程序，Azure 资源管理器模板可以使用特殊的 Azure 资源管理功能（包括[引用](../azure-resource-manager/templates/template-functions-resource.md#reference)和[listKeys](../azure-resource-manager/templates/template-functions-resource.md#listkeys)从 Cosmos DB 帐户获取值并将这些值插入到应用程序设置值，其密钥名称与上面以 "{section： key}" 格式的应用程序中使用的项名称匹配），从 Cosmos DB 帐户请求这些值。 例如 `CosmosDb:Account`。

:::image type="content" source="./media/create-website/template-keys.png" alt-text="模板密钥":::

### <a name="deploying-web-apps-from-github"></a>从 GitHub 部署 web 应用

最后，我们需要将 web 应用程序从 GitHub 部署到应用服务中。 这是使用下面的 JSON 实现的。 需要注意的两点是此资源的类型和名称。 `"type": "sourcecontrols"`和 `"name": "web"` 属性值都是硬编码的，不应更改。

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="从 GitHub 进行部署":::

## <a name="next-steps"></a>后续步骤

祝贺你！ 你已部署 Azure Cosmos DB、Azure App Service 和示例 web 应用程序，该应用程序自动具有连接到 Cosmos DB 所必需的连接信息，所有操作都在单个操作中，无需剪切和粘贴敏感信息。 使用此模板作为起点，你可以将其修改为以相同方式部署你自己的 web 应用程序。

* 有关此示例的 Azure 资源管理器模板，请参阅[Azure 快速入门模板库](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)
* 有关示例应用程序的源代码，请参阅[GitHub 上的 Cosmos DB 执行应用](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)。
