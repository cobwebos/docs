---
title: 使用 Azure Functions 将 API 添加到 Azure 静态 Web 应用
description: 通过使用 Azure Functions 将无服务器 API 添加到静态 Web 应用，开始使用 Azure 静态 Web 应用。
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: c84367a5e3ab57090f59196e8474c14cba87f32b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250141"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>使用 Azure Functions 将 API 添加到 Azure 静态 Web 应用预览版

可以通过与 Azure Functions 集成将无服务器 API 添加到 Azure 静态 Web 应用。 本文演示如何将 API 添加和部署到 Azure 静态 Web 应用站点。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。
  - 如果没有帐户，可以 [免费创建一个](https://azure.microsoft.com/free)。
- [Visual Studio Code](https://code.visualstudio.com/)
- 适用于 Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Live Server Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) 扩展。
- [Node.js](https://nodejs.org/download/) 在本地运行 API 应用

## <a name="create-a-git-repository"></a>创建 Git 存储库

以下步骤演示如何创建新的存储库并将文件克隆到计算机。

1. 请确保已登录到 GitHub，并导航到 https://github.com/staticwebdev/vanilla-basic/generate 以创建新的存储库。
1. 在“存储库名称”框中，输入“my-vanilla-api”。
1. 单击“从模板创建存储库”。

   :::image type="content" source="media/add-api/create-repository.png" alt-text="从 vanilla-basic 创建新的存储库":::

创建项目后，在浏览器中复制新存储库的 URL。 可以在 Visual Studio Code 中使用此 URL 来克隆 Git 存储库。

1. 按 F1 打开命令面板中的命令。
1. 将 URL 粘贴到“Git:Clone”提示符中，然后按 Enter。

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="从 vanilla-basic 创建新的存储库":::

    按照提示选择要克隆项目的存储库位置。

## <a name="create-the-api"></a>创建 API

接下来，将 Azure Functions 项目创建为应用程序的 API。 

1. 在 my-vanilla-api 项目中，创建名为 api 的子文件夹。
1. 按 F1 打开命令面板
1. 键入“Azure Functions:Create New Project...”
1. 按 Enter
1. 选择“浏览”
1. 选择 api 文件夹作为项目工作区的目录
1. 选择“选择”

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="从 vanilla-basic 创建新的存储库":::

1. 根据提示提供以下信息：

    - _选择语言_：选择 **JavaScript**
    - _为项目的第一个函数选择模板_：选择“HTTP 触发器”
    - _提供函数名称_：输入 **GetMessage**
    - _授权级别_：选择“匿名”，这使任何人都可以调用你的函数终结点。
        - 若要了解授权级别，请参阅[授权密钥](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)。

Visual Studio Code 使用 HTTP 触发的函数生成 Azure Functions 项目。

您的应用程序的项目结构现在与下面的示例类似。

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

接下来，你将更改 `GetMessage` 函数以将消息返回到前端。

1. `GetMessage`用以下代码更新_Api/GetMessage/index.js_下的函数。

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. 使用以下设置更新 `api/GetMessage/function.json` 下的 `GetMessage` 配置。

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

- 向函数发出 HTTP 请求时触发
- 适用于所有请求，而不考虑身份验证状态
- 通过 /api/message 路由公开

## <a name="run-the-api-locally"></a>在本地运行 API

Visual Studio Code 与 [Azure Functions Core Tools](../azure-functions/functions-run-local.md) 集成，方便你在将内容发布到 Azure 之前在本地开发计算机上运行此项目。

> [!TIP]
> 在继续操作之前，请确保已安装 [先决条件](#prerequisites) 部分中列出的所有资源。

1. 按 **F5** 运行函数以启动函数应用。

1. 如果尚未安装 Azure Functions Core Tools，请在提示符下选择“安装”。

    核心工具显示 _终端_ 面板中正在运行的应用程序的输出。 作为输出的一部分，可以看到 HTTP 触发函数的 URL 终结点在本地运行。

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="从 vanilla-basic 创建新的存储库":::

1. 在核心工具运行的情况下，导航到以下 URL 以验证 API 是否正常运行： `http://localhost:7071/api/message` 。

   浏览器中的响应应类似于以下示例：

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="从 vanilla-basic 创建新的存储库":::

1. 按 **Shift + F5** 停止调试会话。

### <a name="call-the-api-from-the-application"></a>从应用程序调用 API

部署到 Azure 时，对 API 的请求会自动路由到函数应用，用于发送到路由的请求 `api` 。 在本地工作，你必须将应用程序设置配置为向本地 API 发出代理请求。

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>更新 HTML 文件以访问 API

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
       <p>Loading content from the API: <b id="name">...</b></p>
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

1. 按 **F5** 启动 API 项目。

1. 按 F1 并选择“Live Server:使用 Live Server 打开”来启动 Live Server。

    你现在应该可以在网页中看到 API 消息。

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="从 vanilla-basic 创建新的存储库":::

   > [!NOTE]
   > 可以使用其他 HTTP 服务器或代理来提供 `index.html` 文件。 从 `file:///` 访问 `index.html` 将不起作用。

1. 按 **Shift + F5** 停止 API 项目。

### <a name="commit-and-push-your-changes-to-github"></a>提交所做的更改并将其推送到 GitHub

使用 Visual Studio Code，提交所做的更改并将其推送到远程 Git 存储库。

1. 按 F1 打开命令面板
1. 键入“Git:Commit All”
1. 添加提交消息，然后按 **enter**
1. 按 **F1**
1. 键入 **Git： push** 并按 **enter**

## <a name="create-a-static-web-app"></a>创建静态 Web 应用

1. 导航到 [Azure 门户](https://portal.azure.com)
1. 单击“创建资源”
1. 搜索 **静态 Web 应用**
1. 单击 " **静态 Web 应用 (预览") **
1. 单击“创建” 

接下来，添加特定于应用的设置。

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

接下来，添加以下生成详细信息。

1. **/** 为_应用位置_输入。
1. 在“API 位置”框中输入“api”。
1. 清除“应用项目位置”中的默认值，将框留空。
1. 单击“查看 + 创建”。
1. 单击“创建”按钮

    单击 " _创建_ " 按钮后，Azure 将执行两项任务。 首先，创建底层云服务来支持应用程序。 接下来，后台进程开始生成并部署应用程序。

1. 单击 " **转到资源** " 按钮，转到 web 应用的 " _概述_ " 页。

    在后台生成应用时，可以单击包含用于查看生成状态的链接的标题。

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="从 vanilla-basic 创建新的存储库" 链接。

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="从 vanilla-basic 创建新的存储库":::

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
