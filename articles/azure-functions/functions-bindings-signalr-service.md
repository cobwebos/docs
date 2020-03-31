---
title: Azure Functions SignalR 服务绑定
description: 了解如何将 Azure Functions 与 SignalR 服务绑定配合使用。
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523030"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions 的 SignalR Service 绑定

这组文章介绍了如何通过在 Azure 函数中使用 SignalR 服务绑定对连接到[Azure SignalR 服务的](https://azure.microsoft.com/services/signalr-service/)客户端进行身份验证和发送实时消息。 Azure Functions 支持 SignalR 服务的输入和输出绑定。

| 操作 | 类型 |
|---------|---------|
| 返回服务终结点 URL 和访问令牌 | [输入绑定](./functions-bindings-signalr-service-input.md) |
| 发送信号R服务消息 |[输出绑定](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>添加到功能应用

### <a name="functions-2x-and-higher"></a>功能 2.x 及以上

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展包用于所有其他应用程序类型。

| 语言                                        | 添加...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装[NuGet 软件包]，版本 3.x | |
| C# 脚本、Java、JavaScript、Python、PowerShell | 注册[扩展包]          | 建议使用[Azure 工具扩展]与可视化工作室代码一起使用。 |
| C# 脚本（仅在 Azure 门户中联机）         | 添加绑定                            | 要更新现有绑定扩展，而无需重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[扩展包]: ./functions-bindings-register.md#extension-bundles
[更新扩展]: ./install-update-binding-extensions-manual.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

若要详细了解如何配置 SignalR 服务并将其与 Azure Functions 一起使用，请参阅[通过 Azure SignalR 服务进行的 Azure Functions 开发和配置](../azure-signalr/signalr-concept-serverless-development-config.md)。

### <a name="annotations-library-java-only"></a>注释库（仅限 Java）

要在 Java 函数中使用 SignalR 服务注释，您需要向*pom.xml*文件添加依赖于*azure 函数-java-库信号器*项目（版本 1.0 或更高版本）。

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>后续步骤

- [返回服务终结点 URL 和访问令牌（输入绑定）](./functions-bindings-signalr-service-input.md)
- [发送信号器服务消息（输出绑定）](./functions-bindings-signalr-service-output.md) 
