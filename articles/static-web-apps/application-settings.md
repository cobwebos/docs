---
title: 为 Azure 静态 Web 应用配置应用程序设置
description: 了解如何为 Azure 静态 Web 应用配置应用程序设置
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 36aa0a4a87e439c128c5247b6850100a7f2e826e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595566"
---
# <a name="configure-application-settings-for-azure-static-web-apps-preview"></a>为 Azure 静态 Web 应用预览版配置应用程序设置

应用程序设置包含可能更改的值（例如数据库连接字符串）的配置设置。 通过添加应用程序设置，可以修改应用的配置输入，而无需更改应用程序代码。

应用程序设置包含：

- 静态加密
- 复制到[过渡环境](review-publish-pull-requests.md)和生产环境

应用程序设置有时也称为环境变量。

> [!IMPORTANT]
> 本文所述的应用程序设置仅适用于 Azure 静态 Web 应用的后端 API。
>
> 有关将环境变量用于前端 Web 应用程序的信息，请参阅 [JavaScript 框架](#javascript-frameworks-and-libraries)或[静态站点生成器](#static-site-generators)文档。

## <a name="prerequisites"></a>先决条件

- Azure 静态 Web 应用应用程序
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="types-of-application-settings"></a>应用程序设置的类型

Azure 静态 Web 应用应用程序通常有两个方面。 第一个是 Web 应用程序或静态内容，由 HTML、CSS、JavaScript 和图像表示。 第二个是后端 API，由 Azure Functions 应用程序提供支持。

本文演示如何在 Azure Functions 中管理后端 API 的应用程序设置。

本文中所述的应用程序设置不能在静态 Web 应用程序中使用或引用。 但是，许多前端框架和静态站点生成器允许在开发过程中使用环境变量。 生成时，这些变量会被替换为生成的 HTML 或 JavaScript 中其相应的值。 由于 HTML 和 JavaScript 中的数据容易被站点访问者发现，因此，请避免将敏感信息置于前端应用程序中。 包含敏感信息的设置最好置于应用程序的 API 部分。

有关如何在 JavaScript 框架或库中使用环境变量的信息，请参阅以下文章以了解更多详细信息。

### <a name="javascript-frameworks-and-libraries"></a>JavaScript 框架和库

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>静态站点生成器

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>关于 API 应用设置

Azure 静态 Web 应用中的 API 由 Azure Functions 提供支持，使你能够在 local.settings.json 文件中定义应用程序设置。 此文件定义配置的 `Values` 属性中的应用程序设置。

以下示例 local.settings.json 演示如何为 `DATABASE_CONNECTION_STRING` 添加值。

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

`Values` 属性中定义的设置可以作为环境变量从代码中引用，这些变量可从 `process.env` 对象中获取。

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

GitHub 存储库不会跟踪 `local.settings.json` 文件，因为敏感信息（如数据库连接字符串）通常包含在文件中。 由于本地设置会保留在计算机中，因此需要将设置手动上传到 Azure。

通常，不会经常上传设置，而且并非每次生成都需要上传。

## <a name="uploading-application-settings"></a>上传应用程序设置

可以通过 Azure 门户或使用 Azure CLI 配置应用程序设置。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

Azure 门户提供一个用于创建、更新和删除应用程序设置的接口。

1. 导航到 [Azure 门户](https://portal.azure.com)。

1. 在搜索栏中，搜索并选择“静态 Web 应用”。

1. 单击边栏中的“配置”选项。

1. 选择要将应用程序设置应用到的环境。 过渡环境在拉取请求生成时自动创建，并在拉取请求合并后提升为生产环境。 可以设置每个环境的应用程序设置。

1. 单击“添加”按钮以添加新应用设置。

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Azure 静态 Web 应用配置视图":::

1. 输入“名称”和“值” 

1. 单击 **“确定”**

### <a name="using-the-azure-cli"></a>使用 Azure CLI

可以使用 `az rest` 命令将设置批量上传至 Azure。 命令接收应用程序设置作为名为 `properties` 的父属性中的 JSON 对象。

创建具有适当值的 JSON 文件的最简单方法是创建 local.settings.json 文件的修改版本。

1. 若要确保不会公开公布包含敏感数据的新文件，请将以下条目添加到 .gitignore 文件中。

   ```bash
   local.settings*.json
   ```

2. 接下来，创建 local.settings.json 文件的副本，并将其命名为 local.settings.properties.json。

3. 在新文件中，从文件（应用程序设置除外）中删除所有其他数据，然后将 `Values` 重命名为 `properties`。

   文件现在应类似于以下示例：

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

Azure CLI 命令需要一些特定于帐户的值才能运行上传。 在静态 Web 应用资源的“概述”窗口中，可以访问以下信息：

1. 静态站点名称
2. 资源组名称
3. 订阅 ID

:::image type="content" source="media/application-settings/overview.png" alt-text="Azure 静态 Web 应用概述":::

4. 从终端或命令行执行以下命令。 确保将 `<YOUR_STATIC_SITE_NAME>`、`<YOUR_RESOURCE_GROUP_NAME>` 和 `<YOUR_SUBSCRIPTION_ID>` 的占位符替换为“概述”窗口中的值。

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> “local.settings.properties.json”文件必须位于运行此命令的同一目录中。 你可以按需命名此文件。 此名称并不重要。

### <a name="view-application-settings-with-the-azure-cli"></a>通过 Azure CLI 查看应用程序设置

可通过 Azure CLI 查看应用程序设置。

1. 从终端或命令行执行以下命令。 请务必将占位符 `<YOUR_SUBSCRIPTION_ID>`、`<YOUR_RESOURCE_GROUP_NAME>` 和 `<YOUR_STATIC_SITE_NAME>` 替换为你的值。

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [设置本地开发](local-development.md)
