---
title: 使用 Azure Functions 将 API 添加到 Azure 静态 Web 应用
description: 通过使用 Azure Functions 将无服务器 API 添加到静态 Web 应用，开始使用 Azure 静态 Web 应用。
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596146"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>使用 Azure Functions 将 API 添加到 Azure 静态 Web 应用预览版

可以通过与 Azure Functions 集成将无服务器 API 添加到 Azure 静态 Web 应用。 本文演示如何将 API 添加和部署到 Azure 静态 Web 应用站点。

有关如何保护 API 路由的信息，请参阅[路由指南](routes.md)。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free)。
- [Visual Studio Code](https://code.visualstudio.com/)
- 适用于 Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Live Server Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) 扩展。

## <a name="create-a-git-repository"></a>创建 Git 存储库 

以下步骤演示如何创建新的存储库并将文件克隆到计算机。

1. 导航到 https://github.com/staticwebdev/vanilla-basic/generate 以创建新的存储库。
1. 在“存储库名称”框中，输入“my-vanilla-api”。
1. 单击“从模板创建存储库”。

   :::image type="content" source="media/add-api/create-repository.png" alt-text="从 vanilla-basic 创建新的存储库":::

创建项目后，可以使用 Visual Studio Code 克隆 Git 存储库。

1. 按 F1 打开命令面板中的命令。
1. 将 URL 粘贴到“Git:Clone”提示符中，然后按 Enter。

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="使用 Visual Studio Code 克隆 GitHub 项目":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="使用 Visual Studio Code 克隆 GitHub 项目":::


## <a name="create-your-local-project"></a>创建本地项目

在本部分，你将使用 Visual Studio Code 创建一个本地 Azure Functions 项目。 稍后，将 Functions 应用发布到 Azure。

1. 在 my-vanilla-api 项目中，创建名为 api 的子文件夹。

   > [!NOTE]
   > 可以为此文件夹指定任何名称。 此示例使用 `api`。 

2. 按 F1 打开命令面板
3. 键入“Azure Functions:Create New Project...”
4. 按 Enter
5. 选择“浏览”
6. 选择 api 文件夹作为项目工作区的目录
7. 选择“选择”

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="使用 Visual Studio Code 创建新的 Azure Functions":::

8. 根据提示提供以下信息：

    - _选择函数项目的语言_：选择“JavaScript”
    - _为项目的第一个函数选择模板_：选择“HTTP 触发器”
    - _提供函数名称_：键入“GetMessage”
    - _授权级别_：选择“匿名”，这使任何人都可以调用你的函数终结点。
        - 若要了解授权级别，请参阅[授权密钥](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)。

9. Visual Studio Code 将使用此信息生成一个包含 HTTP 触发器的 Azure Functions 项目。
    - 可以在 Visual Studio Code 的资源管理器窗口中查看本地项目文件。
    - 若要详细了解所创建的文件，请参阅[生成的项目文件](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files)。

10. 应用的项目结构现在应类似于此示例。

    ```files
    ├── api
    │   ├── GetMessage
    │   │   ├── function.json
    │   │   ├── index.js
    │   │   └── sample.dat
    │   ├── host.json
    │   ├── local.settings.json
    │   ├── package.json
    │   └── proxies.json
    ├── index.html
    ├── readme.md
    └── styles.css
    ```

11. 接下来，使用以下代码更新 api/GetMessage/index.js 下的 `GetMessage` 函数。

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. 使用以下设置更新 `api/GetMessage/function.json` 下的 `GetMessage` 配置。

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```
    
对于上述设置，API 终结点：

- 由向函数发出的 HTTP 请求触发
- 适用于所有请求，而不考虑身份验证状态
- 通过 /api/message 路由公开

## <a name="run-the-function-locally"></a>在本地运行函数

Visual Studio Code 与 [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local) 集成，方便你在将内容发布到 Azure 之前在本地开发计算机上运行此项目。

1. 按 F5 运行函数以启动 Functions 应用，随即在“终端”面板中显示 Core Tools 输出。

2. 如果尚未安装 Azure Functions Core Tools，请在提示符下选择“安装”。

    安装 Core Tools 后，应用会在“终端”面板中启动。 作为输出的一部分，可以看到 HTTP 触发函数的 URL 终结点在本地运行。

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="使用 Visual Studio Code 创建新的 Azure Functions":::

3. 在 Core Tools 运行时，导航至以下 URL 以执行 `GET` 请求。

   <http://localhost:7071/api/message>

   系统会返回响应，在浏览器中如下所示：

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="使用 Visual Studio Code 创建新的 Azure Functions":::

验证函数是否正常运行后，可以从 JavaScript 应用程序调用 API。

### <a name="call-the-api-from-the-application"></a>从应用程序调用 API

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>更新文件以访问 API

1. 接下来，使用以下代码更新 index.html 文件的内容，以便从 API 函数中提取文本并将其显示在屏幕上：

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading message from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

   在 Core Tools 运行时，使用 [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) Visual Studio Code 扩展提供 index.html 文件，并在浏览器中打开它。 

2. 按 F1 并选择“Live Server:使用 Live Server 打开”来启动 Live Server。

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="使用 Visual Studio Code 创建新的 Azure Functions":::

   > [!NOTE]
   > 可以使用其他 HTTP 服务器或代理来提供 `index.html` 文件。 从 `file:///` 访问 `index.html` 将不起作用。

### <a name="commit-and-push-your-changes-to-github"></a>提交所做的更改并将其推送到 GitHub

使用 Visual Studio Code，提交所做的更改并将其推送到远程 Git 存储库。

1. 按 F1 打开命令面板
1. 键入“Git:Commit All”
1. 添加提交消息
1. 键入“Git: push”

## <a name="create-a-static-web-app"></a>创建静态 Web 应用

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="在 Azure 门户上创建静态应用 - 屏幕 1":::

1. 导航到 [Azure 门户](https://portal.azure.com)
1. 单击“创建资源”
1. 搜索“静态 Web 应用”
1. 单击“静态 Web 应用(预览)”
1. 单击“创建” 
1. 选择 Azure 订阅
1. 选择或创建新资源组
1. 将应用命名为 my-vanilla-api。
1. 选择离你最近的区域
1. 选择免费 SKU
1. 单击“使用 GitHub 登录”按钮，然后使用 GitHub 进行身份验证
1. 选择首选组织
1. 从“存储库”下拉列表中选择“my-vanilla-api”
1. 从“分支”下拉列表中选择“master”
1. 单击“下一步:生成 >”按钮以编辑生成配置

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="在 Azure 门户上创建静态应用 - 屏幕 2":::

接下来，添加以下生成详细信息。

1. 对于“应用位置”，输入“./”。

1. 在“API 位置”框中输入“api”。

   这是上一步中创建的 API 文件夹的名称。
   
1. 清除“应用项目位置”中的默认值，将框留空。

1. 单击“查看 + 创建”。

| 设置 | 说明             | 必选 |
| -------- | ----------------------- |
|  应用位置 | 静态应用程序源代码的位置 | 是 |
|  API 位置 | API 后端的位置。 此设置指向 Azure Functions 应用项目的根文件夹 | 否 |
|  应用项目位置 | 生成输出文件夹的位置。 某些前端 JavaScript 框架包含将生产文件置于文件夹中的生成步骤。 此设置指向生成输出文件夹。 | 否 |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="在 Azure 门户上创建静态应用 - 屏幕 3":::

1. 单击“创建” 
1. 等待部署完成（这可能需要一分钟）
1. 导航到 `https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"`
1. 确保生成成功

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="GitHub 工作流":::

将可在 `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>` 上使用部署的 API。

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="GitHub 工作流":::

还可以从 Azure 门户中找到应用程序 URL：

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="从 Azure 门户访问静态应用 URL":::

也可以在 `https://<STATIC_APP_NAME>.azurestaticapps.net` 上直接访问 Azure 静态 Web 应用，并在浏览器中检查结果。

## <a name="clean-up-resources"></a>清理资源

如果你不希望保留此应用程序供将来使用，则可以使用以下步骤删除 Azure 静态 Web 应用及其相关资源。

1. 导航到 [Azure 门户](https://portal.azure.com)
1. 在顶部搜索栏中，键入“资源组”
1. 单击“资源组” 
1. 选择“myResourceGroup”
1. 在“myResourceGroup”页中，确保列出的资源是要删除的资源。
1. 选择“删除”
1. 在文本框中键入“myResourceGroup”
1. 选择“删除”。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [配置应用设置](./application-settings.md)
