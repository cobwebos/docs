---
title: Azure Functions 的 Azure 服务总线绑定
description: 了解如何在 Azure 函数中发送 Azure 服务总线触发器和绑定。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277409"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions 的 Azure 服务总线绑定

Azure 函数通过[触发器和绑定](./functions-triggers-bindings.md)与[Azure 服务总线](https://azure.microsoft.com/services/service-bus)集成。 通过与服务总线集成，可以生成响应和发送队列或主题消息的函数。

| 操作 | 类型 |
|---------|---------|
| 创建服务总线队列或主题消息时运行函数 | [触发](./functions-bindings-service-bus-trigger.md) |
| 发送 Azure 服务总线消息 |[输出绑定](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>添加到功能应用

### <a name="functions-2x-and-higher"></a>功能 2.x 及以上

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展包用于所有其他应用程序类型。

| 语言                                        | 添加...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装[NuGet 软件包]，版本 4.x | |
| C# 脚本、Java、JavaScript、Python、PowerShell | 注册[扩展包]          | 建议使用[Azure 工具扩展]与可视化工作室代码一起使用。 |
| C# 脚本（仅在 Azure 门户中联机）         | 添加绑定                            | 要更新现有绑定扩展，而无需重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[扩展包]: ./functions-bindings-register.md#extension-bundles
[更新扩展]: ./install-update-binding-extensions-manual.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函数 1.x 应用会自动具有[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包（版本 2.x）的引用。

## <a name="next-steps"></a>后续步骤

- [创建服务总线队列或主题消息时运行函数（触发器）](./functions-bindings-service-bus-trigger.md)
- [从 Azure 函数发送 Azure 服务总线消息（输出绑定）](./functions-bindings-service-bus-output.md)
