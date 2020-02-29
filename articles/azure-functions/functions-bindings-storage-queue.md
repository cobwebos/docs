---
title: 用于 Azure Functions 概述的 Azure 队列存储触发器和绑定
description: 了解如何在 Azure Functions 中使用 Azure 队列存储触发器和输出绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71321fa631bafde5b1dbaac2d165cb68fd8116d4
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164587"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>用于 Azure Functions 概述的 Azure 队列存储触发器和绑定

Azure Functions 可以在创建新的 Azure 队列存储消息时运行，并可在函数内写入队列消息。

| 操作 | 类型 |
|---------|---------|
| 将函数作为队列存储数据更改运行 | [触发器](./functions-bindings-storage-queue-trigger.md) |
| 写入队列存储消息 |[输出绑定](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>添加到函数应用

### <a name="functions-2x-and-higher"></a>函数1.x 和更高版本

使用触发器和绑定需要引用适当的包。 NuGet 包用于 .NET 类库，而扩展捆绑用于所有其他应用程序类型。

| 语言                                        | 添加依据 。                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装[NuGet 包]版本2。x | |
| C#脚本、Java、JavaScript、Python、PowerShell | 注册[扩展捆绑]          | 建议将[Azure 工具扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)用于 Visual Studio Code。 |
| C#脚本（仅在 Azure 门户中为联机）         | 添加绑定                            | 若要更新现有绑定扩展，而不必重新发布函数应用，请参阅[更新扩展]。 |

[core tools]: ./functions-run-local.md
[扩展捆绑]: ./functions-bindings-register.md#extension-bundles
[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[更新扩展]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函数1.x 应用程序会自动引用[Microsoft Azure Web 作业](https://www.nuget.org/packages/Microsoft.Azure.WebJobs)NuGet 包版本2.x。

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>后续步骤

- [将函数作为队列存储数据更改运行（触发器）](./functions-bindings-storage-queue-trigger.md)
- [写入队列存储消息（输出绑定）](./functions-bindings-storage-queue-output.md)
