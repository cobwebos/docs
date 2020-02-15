---
title: Azure Functions HTTP 触发器和绑定
description: 了解如何在 Azure Functions 中使用 HTTP 触发器和绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: ebf0728184a5fc104e3e1e7d8a199fec328dbdc0
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210168"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Azure Functions HTTP 触发器和绑定概述

Azure Functions 可以通过 HTTP 请求进行调用，以生成无服务器 Api 和响应[webhook](https://en.wikipedia.org/wiki/Webhook)。

| 操作 | 类型 |
|---------|---------|
| 从 HTTP 请求运行函数 | [触发器](./functions-bindings-http-webhook-trigger.md) |
| 从函数返回 HTTP 响应 |[输出绑定](./functions-bindings-http-webhook-output.md) |

本文中的代码默认为 .NET Core 语法，在函数版本2.x 和更高版本中使用。 有关 1.x 语法的信息，请参阅 [1.x functions 模板](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)。

## <a name="add-to-your-functions-app"></a>添加到函数应用

### <a name="functions-2x-and-higher"></a>函数1.x 和更高版本

使用触发器和绑定需要引用适当的包。 NuGet 包用于 .NET 类库，而扩展捆绑所有其他应用程序类型。

| 语言                                        | 添加依据 。                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装[NuGet 包]版本2。x | |
| C#脚本、Java、JavaScript、Python、PowerShell | 注册[扩展捆绑]          | 建议将[Azure 工具扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)用于 Visual Studio Code。 |
| C#脚本（仅在 Azure 门户中为联机）         | 添加绑定                            | 若要更新现有绑定扩展，而不必重新发布函数应用，请参阅[更新扩展]。 |

[core tools]: ./functions-run-local.md
[扩展捆绑]: ./functions-bindings-register.md#extension-bundles
[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[更新扩展]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函数1.x 应用程序会自动引用[Microsoft Azure Web 作业](https://www.nuget.org/packages/Microsoft.Azure.WebJobs)NuGet 包版本2.x。

## <a name="next-steps"></a>后续步骤

- [从 HTTP 请求运行函数](./functions-bindings-http-webhook-trigger.md)
- [从函数返回 HTTP 响应](./functions-bindings-http-webhook-output.md)
