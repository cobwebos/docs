---
title: Azure Functions SignalR 服务绑定
description: 了解如何将 Azure Functions 与 SignalR 服务绑定配合使用。
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523030"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions 的 SignalR Service 绑定

这一组文章介绍了如何使用 Azure Functions 中的 SignalR 服务绑定对连接到[Azure SignalR 服务](https://azure.microsoft.com/services/signalr-service/)的客户端进行身份验证和发送。 Azure Functions 支持 SignalR 服务的输入和输出绑定。

| 操作 | 类型 |
|---------|---------|
| 返回服务终结点 URL 和访问令牌 | [输入绑定](./functions-bindings-signalr-service-input.md) |
| 发送 SignalR 服务消息 |[输出绑定](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>添加到函数应用

### <a name="functions-2x-and-higher"></a>函数1.x 和更高版本

使用触发器和绑定需要引用适当的包。 NuGet 包用于 .NET 类库，而扩展捆绑用于所有其他应用程序类型。

| 语言                                        | 添加依据 。                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装[NuGet 包]版本2。x | |
| C#脚本、Java、JavaScript、Python、PowerShell | 注册[扩展捆绑]          | 建议将[Azure 工具扩展]用于 Visual Studio Code。 |
| C#脚本（仅在 Azure 门户中为联机）         | 添加绑定                            | 若要更新现有绑定扩展，而不必重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[扩展捆绑]: ./functions-bindings-register.md#extension-bundles
[更新扩展]: ./install-update-binding-extensions-manual.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

有关如何配置和使用 SignalR Service 并 Azure Functions 一起使用的详细信息，Azure Functions 请参阅[使用 Azure SignalR 服务进行开发和配置](../azure-signalr/signalr-concept-serverless-development-config.md)。

### <a name="annotations-library-java-only"></a>批注库（仅 Java）

若要在 Java 函数中使用 SignalR 服务注释，需要在*pom*文件中添加对*SignalR*项目的依赖关系（版本1.0 或更高版本）。

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>后续步骤

- [返回服务终结点 URL 和访问令牌（输入绑定）](./functions-bindings-signalr-service-input.md)
- [发送 SignalR 服务消息（输出绑定）](./functions-bindings-signalr-service-output.md) 
