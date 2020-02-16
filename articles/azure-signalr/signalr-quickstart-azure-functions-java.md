---
title: 通过 Java 使用 Azure Functions 和 SignalR 服务创建聊天室
description: 使用 Azure SignalR 服务和 Azure Functions 创建聊天室的快速入门。
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 890fc381afe0146e721e084e2dcd7eae9215d004
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083202"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>快速入门：通过 Java 使用 Azure Functions 和 SignalR 服务创建聊天室

Azure SignalR 服务可让你轻松地向应用程序添加实时功能，而 Azure Functions 是一个无服务器平台，可让你无需管理任何基础结构即可运行代码。 在本快速入门中，你将通过 Java 使用 SignalR 服务和 Functions 构建一个无服务器的实时聊天应用程序。

## <a name="prerequisites"></a>必备条件

- 代码编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)
- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)。 用于在本地运行 Azure Functions 应用。

   > [!NOTE]
   > Java 中所需的 SignalR 服务绑定仅在 Azure Function Core Tools 版本2.4.419（主机版本 2.0.12332）或更高版本中受支持。

   > [!NOTE]
   > 若要安装扩展，Azure Functions Core Tools 需要安装 [.NET Core SDK](https://www.microsoft.com/net/download)。 但是，构建 JavaScript Azure Function 应用不需要了解 .NET。

- [Java 开发人员工具包](https://www.azul.com/downloads/zulu/)版本 8
- [Apache Maven](https://maven.apache.org) 版本 3.0 或更高版本

> [!NOTE]
> 本快速入门可以在 macOS、Windows 或 Linux 上运行。

## <a name="log-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 <https://portal.azure.com/> 的 Azure 门户。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>配置和运行 Azure 函数应用

1. 在打开 Azure门户的浏览器中，通过在门户顶部的搜索框中搜索先前部署的 SignalR 服务实例的名称，确认该实例已成功创建。 选择该实例以将其打开。

    ![搜索 SignalR 服务实例](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. 选择“密钥”  以查看 SignalR 服务实例的连接字符串。

1. 选择并复制主连接字符串。

    ![创建 SignalR 服务](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. 在代码编辑器中，打开克隆存储库中的 *src/chat/java* 文件夹。

1. 将 *local.settings.sample.json* 重命名为 *local.settings.json*。

1. 在 **local.settings.json** 中，将连接字符串粘贴到 **AzureSignalRConnectionString** 设置的值中。 保存文件。

1. 包含这些函数的主文件位于 *src/chat/java/src/main/java/com/function/Functions.java* 中：

    - **negotiate** - 使用 *SignalRConnectionInfo* 输入绑定生成并返回有效的连接信息。
    - **sendMessage** - 在请求正文中接收聊天消息，并使用 *SignalR* 输出绑定将消息广播到所有连接的客户端应用程序。

1. 在终端中，确保你位于 *src/chat/java* 文件夹中。 生成函数应用。

    ```bash
    mvn clean package
    ```

1. 在本地运行函数应用。

    ```bash
    mvn azure-functions:run
    ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，使用 Maven 生成并运行了一个实时无服务器应用程序。 接下来，了解如何从头开始创建 Java Azure Functions。

> [!div class="nextstepaction"]
> [使用 Java 和 Maven 创建第一个函数](../azure-functions/functions-create-first-java-maven.md)