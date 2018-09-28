---
title: 快速入门 - Azure SignalR 服务 REST API | Microsoft Docs
description: 有关使用 Azure SignalR 服务 REST API 的快速入门。
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: 93c1198ecfba6db809228ed6dcd99c705f53926c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972753"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>快速入门：从控制台应用广播实时消息

Azure SignalR 服务提供了 [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) 来支持服务器到客户端通信方案，例如广播。 你可以选择可以进行 REST API 调用的任何编程语言。 你可以将消息发布到所有已连接的客户端、通过名称指定的特定客户端或者一组客户端。

在本快速入门中，你将学习如何从命令行应用使用 C# 将消息发送到已连接的客户端应用。

## <a name="prerequisites"></a>先决条件

本快速入门可以在 macOS、Windows 或 Linux 上运行。
* [.NET Core SDK](https://www.microsoft.com/net/download/core)
* 你选择的文本编辑器或代码编辑器。


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="sign-in-to-azure"></a>登录到 Azure

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

在服务器启动后，使用以下命令发送消息

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

可以启动具有不同客户端名称的多个客户端。


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]
