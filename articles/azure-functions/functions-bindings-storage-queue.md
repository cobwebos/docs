---
title: 适用于 Azure Functions 的 Azure 队列存储触发器和绑定概述
description: 了解如何在 Azure Functions 中使用 Azure 队列存储触发器和输出绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 2d8f3985bc9a726735ebc0af0a3d3422f4fca54a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108858"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>适用于 Azure Functions 的 Azure 队列存储触发器和绑定概述

Azure Functions 可以在创建新的 Azure 队列存储消息时运行，并可在函数中写入队列消息。

| 操作 | 类型 |
|---------|---------|
| 在队列存储数据更改时运行函数 | [触发器](./functions-bindings-storage-queue-trigger.md) |
| 写入队列存储消息 |[输出绑定](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>添加到 Functions 应用

### <a name="functions-2x-and-higher"></a>Functions 2.x 及更高版本

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展捆绑包用于其他所有应用程序类型。

| 语言                                        | 添加方式...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装 [NuGet 包]版本 3.x | |
| C # 脚本，Java，JavaScript，Python，PowerShell | 注册[扩展捆绑包]          | 建议将 [Azure Tools 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)用于 Visual Studio Code。 |
| C# 脚本（Azure 门户中仅限联机）         | 添加绑定                            | 若要更新现有绑定扩展而不必重新发布函数应用，请参阅[更新扩展]。 |

[core tools]: ./functions-run-local.md
[扩展捆绑包]: ./functions-bindings-register.md#extension-bundles
[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[更新扩展]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 应用会自动引用 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 程序包（版本 2.x）。

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>后续步骤

- [在队列存储数据更改时运行函数（触发器）](./functions-bindings-storage-queue-trigger.md)
- [向队列存储写入消息（输出绑定）](./functions-bindings-storage-queue-output.md)