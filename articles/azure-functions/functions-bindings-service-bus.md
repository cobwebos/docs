---
title: Azure Functions 的 Azure 服务总线绑定
description: 了解如何在 Azure Functions 中发送 Azure 服务总线触发器和绑定。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621073"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions 的 Azure 服务总线绑定

Azure Functions 通过[触发器和绑定](./functions-triggers-bindings.md)与[Azure 服务总线](https://azure.microsoft.com/services/service-bus)集成。 与 Service Bus 集成后，便可以生成对队列或主题消息做出反应的函数。

| 操作 | 类型 |
|---------|---------|
| 在创建服务总线队列或主题消息时运行函数 | [触发器](./functions-bindings-service-bus-trigger.md) |
| 发送 Azure 服务总线消息 |[输出绑定](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>添加到函数应用

### <a name="functions-2x-and-higher"></a>函数1.x 和更高版本

使用触发器和绑定需要引用适当的包。 NuGet 包用于 .NET 类库，而扩展捆绑用于所有其他应用程序类型。

| 语言                                        | 添加依据 。                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装[NuGet 包]版本4。x | |
| C#脚本、Java、JavaScript、Python、PowerShell | 注册[扩展捆绑]          | 建议将[Azure 工具扩展]用于 Visual Studio Code。 |
| C#脚本（仅在 Azure 门户中为联机）         | 添加绑定                            | 若要更新现有绑定扩展，而不必重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[扩展捆绑]: ./functions-bindings-register.md#extension-bundles
[更新扩展]: ./install-update-binding-extensions-manual.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函数1.x 应用程序会自动引用[Microsoft Azure Web 作业](https://www.nuget.org/packages/Microsoft.Azure.WebJobs)NuGet 包版本2.x。

## <a name="next-steps"></a>后续步骤

- [在创建服务总线队列或主题消息时运行函数（触发器）](./functions-bindings-service-bus-trigger.md)
- [从 Azure Functions 发送 Azure 服务总线消息（输出绑定）](./functions-bindings-service-bus-output.md)
