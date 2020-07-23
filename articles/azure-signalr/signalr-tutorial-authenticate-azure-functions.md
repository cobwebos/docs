---
title: 教程：使用 Azure Functions 进行身份验证 - Azure SignalR
description: 本教程介绍如何针对 Azure Functions 绑定，为 Azure SignalR 服务客户端进行身份验证
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: dfa17720b34962611d240aa7c35ba8092bf99082
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "74158147"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>教程：使用 Azure Functions 进行 Azure SignalR 服务身份验证

本教程逐步讲解如何使用 Azure Functions、应用服务身份验证和 SignalR 服务生成一个支持身份验证和私密消息传送的聊天室。

## <a name="introduction"></a>简介

### <a name="technologies-used"></a>使用的技术

* [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu) - 用于验证用户身份和发送聊天消息的后端 API
* [Azure SignalR 服务](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu) - 将新消息广播到连接的聊天客户端
* [Azure 存储](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu) - 托管聊天客户端 UI 的静态网站

### <a name="prerequisites"></a>先决条件

本教程需要以下软件。

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/)（版本 10.x）
* [.NET SDK](https://www.microsoft.com/net/download)（版本 2.x，Functions 扩展需要）
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools)（版本 2）
* 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/) (VS Code)
  * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) - 在 VS Code 中使用 Azure Functions
  * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) - 提供用于本地测试的网页

## <a name="sign-into-the-azure-portal"></a>登录到 Azure 门户

转到 [Azure 门户](https://portal.azure.com/)并使用自己的凭据登录。

## <a name="create-an-azure-signalr-service-instance"></a>创建 Azure SignalR 服务实例

将在本地生成并测试 Azure Functions 应用。 该应用将访问 Azure 中需要提前创建的 SignalR 服务实例。

1. 单击用于创建新 Azure 资源的“创建资源”( **+** ) 按钮。 

1. 搜索“SignalR 服务”并将其选中。  单击“创建”。 

    ![新建 SignalR 服务](media/signalr-tutorial-authenticate-azure-functions/signalr-quickstart-new.png)

1. 输入以下信息。

    | 名称 | 值 |
    |---|---|
    | 资源名称 | SignalR 服务实例的唯一名称 |
    | 资源组 | 创建具有唯一名称的新资源组 |
    | 位置 | 选择靠近自己的位置 |
    | 定价层 | 免费 |

1. 单击“创建”。 

1. 部署该实例后，在门户中打开它并找到其“设置”页。 将服务模式设置更改为“无服务器”  。

    ![SignalR 服务模式](media/signalr-concept-azure-functions/signalr-service-mode.png)


## <a name="initialize-the-function-app"></a>初始化函数应用

### <a name="create-a-new-azure-functions-project"></a>创建新的 Azure Functions 项目

1. 在新的 VS Code 窗口，使用菜单中的 `File > Open Folder` 在适当的位置创建一个空文件夹并将其打开。 这是要生成的应用程序的主项目文件夹。

1. 使用 VS Code 中的 Azure Functions 扩展初始化主项目文件夹中的函数应用。
   1. 在 VS Code 的菜单中选择“视图”>“命令面板”（Windows 快捷键为 `Ctrl-Shift-P`，macOS 快捷键为 `Cmd-Shift-P`）打开命令面板。 
   1. 搜索“Azure Functions:  Create New Project”命令并将其选中。
   1. 此时应会显示主项目文件夹。 选择该文件夹（或使用“浏览”找到它）。
   1. 当系统提示选择语言时，请选择“JavaScript”。 

      ![创建函数应用](media/signalr-tutorial-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>安装函数应用扩展

本教程使用 Azure Functions 绑定来与 Azure SignalR 服务交互。 与其他大多数绑定一样，SignalR 服务绑定可用作扩展，需要先通过 Azure Functions Core Tools CLI 安装该扩展才能使用它。

1. 在 VS Code 的菜单中选择“视图”>“终端”(Ctrl-\`) 打开一个终端。 

1. 确保当前目录是主项目文件夹。

1. 安装 SignalR 服务函数应用扩展。

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0
    ```

### <a name="configure-application-settings"></a>配置应用程序设置

在本地运行和调试 Azure Functions 运行时时，将从 **local.settings.json** 读取应用程序设置。 使用前面创建的 SignalR 服务实例的连接字符串更新此文件。

1. 在 VS Code 的“资源管理器”窗格中选择“local.settings.json”并将其打开。 

1. 将该文件的内容替换为以下内容。

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.14.1",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "http://127.0.0.1:5500",
            "CORSCredentials": true
        }
    }
    ```

   * 在名为 `AzureSignalRConnectionString` 的设置中输入 Azure SignalR 服务连接字符串。 在 Azure 门户中，从 Azure SignalR 服务资源的“密钥”页中获取该值；可以使用主连接字符串或辅助连接字符串。 
   * 不会在本地使用 `WEBSITE_NODE_DEFAULT_VERSION` 设置，但部署到 Azure 时需要此设置。
   * `Host` 节配置本地 Functions 主机的端口和 CORS 设置（在 Azure 中运行时，此设置不起作用）。

       > [!NOTE]
       > 实时服务器通常配置为从 `http://127.0.0.1:5500` 提供内容。 如果你发现它使用不同的 URL，或者你使用不同的 HTTP 服务器，请更改 `CORS` 设置以反映正确来源。

     ![获取 SignalR 服务密钥](media/signalr-tutorial-authenticate-azure-functions/signalr-get-key.png)

1. 保存文件。

    

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>创建一个用于在 SignalR 服务中验证用户身份的函数

当聊天应用在浏览器中首次打开时，需要使用有效的连接凭据连接到 Azure SignalR 服务。 我们将在函数应用中创建名为 negotiate  的 HTTP 触发的函数，以返回此连接信息。

> [!NOTE]
> 此函数必须命名为 negotiate  ，因为 SignalR 客户端需要以 `/negotiate` 结束的终结点。

1. 打开 VS Code 命令面板（Windows 快捷键为 `Ctrl-Shift-P`，macOS 快捷键为 `Cmd-Shift-P`）。

1. 搜索并选择“Azure Functions:  Create Function”命令。

1. 出现提示时，请提供以下信息。

    | 名称 | 值 |
    |---|---|
    | 函数应用文件夹 | 选择主项目文件夹 |
    | 模板 | HTTP 触发器 |
    | 名称 | negotiate |
    | 授权级别 | 匿名 |

    将创建包含新函数的名为 negotiate  的文件夹。

1. 打开 negotiate/function.json  以配置函数的绑定。 按如下所示修改该文件的内容。 这会添加一个用于生成有效凭据的输入绑定，使客户端能够连接到名为 `chat` 的 Azure SignalR 服务中心。

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    `signalRConnectionInfo` 绑定中的 `userId` 属性用于创建已经过身份验证的 SignalR 服务连接。 在本地开发期间，请将该属性保留为空。 将函数应用部署到 Azure 时，将会使用该属性。

1. 打开 negotiate/index.js  查看函数的正文。 按如下所示修改该文件的内容。

    ```javascript
    module.exports = async function (context, req, connectionInfo) {
        context.res.body = connectionInfo;
    };
    ```

    此函数从输入绑定中提取 SignalR 连接信息，并在 HTTP 响应正文中将此信息返回给客户端。 SignalR 客户端会使用此信息连接到 SignalR 服务实例。

## <a name="create-a-function-to-send-chat-messages"></a>创建用于发送聊天消息的函数

Web 应用还需要使用一个 HTTP API 来发送聊天消息。 我们将创建名为 *SendMessage* 的 HTTP 触发的函数，用于通过 SignalR 服务将消息发送到所有连接的客户端。

1. 打开 VS Code 命令面板（Windows 快捷键为 `Ctrl-Shift-P`，macOS 快捷键为 `Cmd-Shift-P`）。

1. 搜索并选择“Azure Functions:  Create Function”命令。

1. 出现提示时，请提供以下信息。

    | 名称 | 值 |
    |---|---|
    | 函数应用文件夹 | 选择主项目文件夹 |
    | 模板 | HTTP 触发器 |
    | 名称 | SendMessage |
    | 授权级别 | 匿名 |

    将创建包含新函数的名为 **SendMessage** 的文件夹。

1. 打开 **SendMessage/function.json** 以配置函数的绑定。 按如下所示修改该文件的内容。
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "$return",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    这会对原始函数做出两项更改：
    * 将路由更改为 `messages`，并将 HTTP 触发器限制为 **POST** HTTP 方法。
    * 添加 SignalR 服务输出绑定，用于将函数返回的消息发送到已连接至名为 `chat` 的 SignalR 服务中心的所有客户端。

1. 保存文件。

1. 打开 **SendMessage/index.js** 查看函数的正文。 按如下所示修改该文件的内容。

    ```javascript
    module.exports = async function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        return {
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        };
    };
    ```

    此函数从 HTTP 请求中提取正文，并通过在连接到 SignalR 服务的每个客户端上调用名为 `newMessage` 的函数，将此正文发送到所有这些客户端。

    此函数可以读取发送者的标识，并可以接受消息正文中的 *recipient* 值，以便能够以私密的方式将消息发送到单个用户。 本教程稍后将使用这些功能。

1. 保存文件。

## <a name="create-and-run-the-chat-client-web-user-interface"></a>创建并运行聊天客户端 Web 用户界面

聊天应用程序的 UI 是使用 Vue JavaScript 框架创建的简单单页应用程序 (SPA)。 该 UI 单独托管在函数应用中。 在本地，我们将使用 Live Server VS Code 扩展运行 Web 界面。

1. 在 VS Code 中，在主项目文件夹的根目录内创建名为 **content** 的新文件夹。

1. 在 **content** 文件夹中创建名为 **index.html** 的新文件。

1. 复制并粘贴 **[index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/2720a9a565e925db09ef972505e1c5a7a3765be4/docs/demo/chat-with-auth/index.html)** 的内容。

1. 保存文件。

1. 按 **F5** 在本地运行函数应用，并附加一个调试程序。

1. 打开 **index.html** 后，通过打开 VS Code 命令面板（Windows 快捷键为 `Ctrl-Shift-P`，macOS 快捷键为 `Cmd-Shift-P`）并选择“Live Server:  使用 Live Server 打开”来启动 Live Server。 Live Server 将在浏览器中打开该应用程序。

1. 应用程序已打开。 在聊天框中输入一条消息并按 Enter。 刷新应用程序以查看新消息。 由于未配置身份验证，所有消息将以“匿名”方式发送。

## <a name="deploy-to-azure-and-enable-authentication"></a>部署到 Azure 并启用身份验证

我们一直在本地运行函数应用和聊天应用程序。 现在，我们将其部署到 Azure，并在应用程序中启用身份验证和私密消息传送。

### <a name="log-into-azure-with-vs-code"></a>使用 VS Code 登录到 Azure

1. 打开 VS Code 命令面板（Windows 快捷键为 `Ctrl-Shift-P`，macOS 快捷键为 `Cmd-Shift-P`）。

1. 搜索并选择“Azure:  Sign in”命令。

1. 在浏览器中遵照说明完成登录过程。

### <a name="create-a-storage-account"></a>创建存储帐户

在 Azure 中运行的函数应用需要 Azure 存储帐户。 你还会使用 Azure 存储的静态网站功能托管聊天 UI 的网页。

1. 在 Azure 门户中，单击用于创建新 Azure 资源的“创建资源”(+  ) 按钮。 

1. 选择“存储”  类别，然后选择“存储帐户”  。

1. 输入以下信息。

    | 名称 | 值 |
    |---|---|
    | 订阅 | 选择包含 SignalR 服务实例的订阅 |
    | 资源组 | 选择相同资源组 |
    | 资源名称 | 存储帐户的唯一名称 |
    | 位置 | 选择其他资源所在的同一位置 |
    | 性能 | Standard |
    | 帐户类型 | StorageV2（常规用途 V2） |
    | 复制 | 本地冗余存储 (LRS) |
    | 访问层 | 热 |

1. 单击“查看 + 创建”  ，然后单击“创建”  。

### <a name="configure-static-websites"></a>配置静态网站

1. 创建存储帐户后，在 Azure 门户中打开它。

1. 选择“静态网站”  。

1. 选择“启用”以启用静态网站功能。 

1. 在“索引文档名称”  中，输入 index.html  。

1. 单击“ **保存**”。

1. “主终结点”  随即出现。 请记下此值。 配置函数应用需要它。

### <a name="configure-function-app-for-authentication"></a>为函数应用配置身份验证

到目前为止，聊天应用程序以匿名方式工作。 在 Azure 中，我们将使用[应用服务身份验证](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)来验证用户的身份。 可将已经过身份验证的用户的用户 ID 或用户名传递给 *SignalRConnectionInfo* 绑定，以生成进行用户身份验证时所需的连接信息。

发送消息时，应用可以确定是要发送到所有已连接的客户端，还是仅发送到已对给定用户进行身份验证的客户端。

1. 在 VS Code 中打开 **negotiate/function.json**。

1. 将一个[绑定表达式](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings)插入到 *SignalRConnectionInfo* 绑定的 *userId* 属性中：`{headers.x-ms-client-principal-name}`。 这会将值设置为经过身份验证的用户的用户名。 特性现在应如下所示。

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. 保存文件。


### <a name="deploy-function-app-to-azure"></a>将函数应用部署到 Azure

1. 打开 VS Code 命令面板（Windows 快捷键为 `Ctrl-Shift-P`，macOS 快捷键为 `Cmd-Shift-P`），然后选择“Azure Functions:  部署到函数应用”。

1. 出现提示时，请提供以下信息。

    | 名称 | 值 |
    |---|---|
    | 要部署的文件夹 | 选择主项目文件夹 |
    | 订阅 | 选择订阅 |
    | 函数应用 | 选择“创建新函数应用”  |
    | 函数应用名称 | 输入唯一的名称 |
    | 资源组 | 选择 SignalR 服务实例所在的同一资源组 |
    | 存储帐户 | 选择前面创建的存储帐户 |

    随即会在 Azure 中创建新的函数应用，并开始部署。 等待部署完成。

### <a name="upload-function-app-local-settings"></a>上传函数应用本地设置

1. 打开 VS Code 命令面板（Windows 快捷键为 `Ctrl-Shift-P`，macOS 快捷键为 `Cmd-Shift-P`）。

1. 搜索并选择“Azure Functions:  上传本地设置”命令。

1. 出现提示时，请提供以下信息。

    | 名称 | 值 |
    |---|---|
    | 本地设置文件 | local.settings.json |
    | 订阅 | 选择订阅 |
    | 函数应用 | 选择前面部署的函数应用 |

本地设置将上传到 Azure 中的函数应用。 如果系统提示是否要覆盖现有设置，请选择“全是”。 


### <a name="enable-app-service-authentication"></a>启用应用服务身份验证

应用服务身份验证支持使用 Azure Active Directory、Facebook、Twitter、Microsoft 帐户和 Google 的身份验证。

1. 打开 VS Code 命令面板（Windows 快捷键为 `Ctrl-Shift-P`，macOS 快捷键为 `Cmd-Shift-P`）。

1. 搜索并选择“Azure Functions:  在门户中打开”命令。

1. 选择订阅和函数应用的名称，在 Azure 门户中打开该函数应用。

1. 在已在门户中打开的函数应用中，找到“平台功能”选项卡，并选择“身份验证/授权”。  

1. 启用应用服务身份验证。 

1. 在“请求未经过身份验证时需执行的操作”中，选择“使用 {前面选择的身份验证提供程序} 登录”。 

1. 在“允许的外部重定向 URL”中，输入前面记下的存储帐户主要 Web 终结点的 URL。 

1. 遵循所选登录提供程序的文档完成配置。

    - [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)
    - [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook)
    - [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)
    - [Microsoft 帐户](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)
    - [Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google)

### <a name="update-the-web-app"></a>更新 Web 应用

1. 在 Azure 门户中，导航到函数应用的概述页。

1. 复制函数应用的 URL。

    ![获取 URL](media/signalr-tutorial-authenticate-azure-functions/signalr-get-url.png)

1. 在 VS Code 中打开 **index.html**，并将 `apiBaseUrl` 的值替换为函数应用的 URL。

1. 可以使用 Azure Active Directory、Facebook、Twitter、Microsoft 帐户或 Google 为应用程序配置身份验证。 通过设置 `authProvider` 的值来选择要使用的身份验证提供程序。

1. 保存文件。

### <a name="deploy-the-web-application-to-blob-storage"></a>将 Web 应用程序部署到 Blob 存储

将使用 Azure Blob 存储的静态网站功能托管 Web 应用程序。

1. 打开 VS Code 命令面板（Windows 快捷键为 `Ctrl-Shift-P`，macOS 快捷键为 `Cmd-Shift-P`）。

1. 搜索并选择“Azure 存储:  部署到静态网站”命令。

1. 输入以下值：

    | 名称 | 值 |
    |---|---|
    | 订阅 | 选择订阅 |
    | 存储帐户 | 选择前面创建的存储帐户 |
    | 要部署的文件夹 | 选择“浏览”  ，然后选择“内容”  文件夹 |

“内容”  文件夹中的文件现在应部署到静态网站。

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>启用函数应用跨域资源共享 (CORS)

尽管 **local.settings.json** 中有一个 CORS 设置，但该设置不会传播到 Azure 中的函数应用。 需要单独设置此项。

1. 在 Azure 门户中打开函数应用。

1. 在“平台功能”  选项卡下，选择“CORS”  。

    ![找到“CORS”](media/signalr-tutorial-authenticate-azure-functions/signalr-find-cors.png)

1. 在“允许的来源”  部分中，添加一个将静态网站项主终结点  作为值的条目（删除尾部 /  ）。

1. 为了使 SignalR JavaScript SDK 可从浏览器调用函数应用，必须启用 CORS 中对凭据的支持。 选中“启用 Access-Control-Allow-Credentials”复选框。

    ![启用 Access-Control-Allow-Credentials](media/signalr-tutorial-authenticate-azure-functions/signalr-cors-credentials.png)

1. 单击“保存”以保存 CORS 设置  。

### <a name="try-the-application"></a>尝试运行应用程序

1. 在浏览器中，导航到存储帐户的主要 Web 终结点。

1. 选择“登录”，使用所选的身份验证提供程序进行身份验证。 

1. 在主要聊天框中输入公共消息并发送这些消息。

1. 单击聊天历史记录中的用户名发送私人消息。 只有选定的接收者可以收到这些消息。

祝贺你！ 现已部署一个实时无服务器聊天应用！

![演示](media/signalr-tutorial-authenticate-azure-functions/signalr-serverless-chat.gif)

## <a name="clean-up-resources"></a>清理资源

若要清理本教程创建的资源，请使用 Azure 门户删除相应的资源组。

## <a name="next-steps"></a>后续步骤

本教程已介绍如何将 Azure Functions 与 Azure SignalR 服务配合使用。 接下来请详细了解如何使用 Azure Functions 的 SignalR 服务绑定来生成实时无服务器应用程序。

> [!div class="nextstepaction"]
> [使用 Azure Functions 生成实时应用](signalr-concept-azure-functions.md)
