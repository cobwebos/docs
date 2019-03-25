---
title: 快速入门 - Azure SignalR 服务 REST API
description: 有关使用 Azure SignalR 服务 REST API 的快速入门。
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 88a5a1bcff8542ac500bbb5e0da790f77c90a825
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530784"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>快速入门：从控制台应用广播实时消息

Azure SignalR 服务提供了 [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) 来支持服务器到客户端通信方案，例如广播。 你可以选择可以进行 REST API 调用的任何编程语言。 你可以将消息发布到所有已连接的客户端、通过名称指定的特定客户端或者一组客户端。

在本快速入门中，你将学习如何从命令行应用使用 C# 将消息发送到已连接的客户端应用。

## <a name="prerequisites"></a>先决条件

本快速入门可以在 macOS、Windows 或 Linux 上运行。

* [.NET Core SDK](https://www.microsoft.com/net/download/core)
* 你选择的文本编辑器或代码编辑器。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 <https://portal.azure.com/> 的 Azure 门户。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

在该服务进行部署时，让我们先去准备代码。 克隆[来自 GitHub 的示例应用](https://github.com/aspnet/AzureSignalR-samples.git)，设置 SignalR 服务连接字符串，并在本地运行该应用程序。

1. 打开 git 终端窗口。 切换到要克隆示例项目的文件夹。

1. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>生成并运行示例

此示例是一个控制台应用，展示了如何使用 Azure SignalR 服务。 它提供了两种模式：

- 服务器模式：使用简单的命令调用 Azure SignalR 服务 REST API。
- 客户端模式：连接到 Azure SignalR 服务，并从服务器接收消息。

另外，你还可以查明如何生成访问令牌来向 Azure SignalR 服务证明身份。

### <a name="build-the-executable-file"></a>生成可执行文件

我们使用 macOS osx.10.13-x64 作为示例。 你可以找到有关如何在其他平台上进行生成的[参考资料](https://docs.microsoft.com/dotnet/core/rid-catalog)。

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>启动客户端

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>启动服务器

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>运行示例而不发布

你还可以运行以下命令来启动服务器或客户端

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>使用 user-secrets 指定连接字符串

你可以运行示例的根目录中的 `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"`。 之后，将不再需要选项 `-c "<ConnectionString>"`。

## <a name="usage"></a>使用情况

在服务器启动后，使用以下命令发送消息：

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

可以启动具有不同客户端名称的多个客户端。

## <a name="usage"> </a> 与第三方服务集成

Azure SignalR 服务允许第三方服务与系统集成。

### <a name="definition-of-technical-specifications"></a>定义技术规范

下表显示到目前为止受支持的 REST API 的所有版本。 也可找到每个特定版本的定义文件

版本 | API 状态 | 门 | 特定
--- | --- | --- | ---
`1.0-preview` | 可用 | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | 可用 | 标准 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

适用于每个特定版本的 API 的列表在下表中提供。

API | `1.0-preview` | `1.0`
--- | --- | ---
[广播到所有对象](#broadcast) | **&#x2713;** | **&#x2713;**
[广播到组](#broadcast-group) | **&#x2713;** | **&#x2713;**
广播到某些组 | **&#x2713;** (已弃用) | `N / A`
[发送到特定用户](#send-user) | **&#x2713;** | **&#x2713;**
发送到某些用户 | **&#x2713;** (已弃用) | `N / A`
[将用户添加到组](#add-user-to-group) | `N / A` | **&#x2713;**
[从组中删除用户](#remove-user-from-group) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>广播到所有人

版本 | API HTTP 方法 | 请求 URL | 请求正文
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | 同上

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>广播到组

版本 | API HTTP 方法 | 请求 URL | 请求正文
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | 同上

<a name="send-user"> </a>
### <a name="sending-to-specific-users"></a>发送到特定用户

版本 | API HTTP 方法 | 请求 URL | 请求正文
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | 同上

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>将用户添加到组

版本 | API HTTP 方法 | 请求 URL
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>从组中删除用户

版本 | API HTTP 方法 | 请求 URL
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你学习了如何使用 REST API 从 SignalR 服务向客户端广播实时消息。 接下来，了解有关如何使用基于 REST API 构建的 SignalR 服务绑定开发和部署 Azure Functions 的更多信息。

> [!div class="nextstepaction"]
> [使用 Azuer SignalR 服务绑定开发 Azure Functions](signalr-quickstart-azure-functions-csharp.md)