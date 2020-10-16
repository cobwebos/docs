---
title: Azure Functions HTTP 触发器和绑定
description: 了解如何在 Azure Functions 中使用 HTTP 触发器和绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 5236a3be9f8908d886274764dfc0c0da5b3565bc
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104472"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Azure Functions HTTP 触发器和绑定概述

Azure Functions 可以通过 HTTP 请求进行调用，以生成无服务器 Api 和响应 [webhook](https://en.wikipedia.org/wiki/Webhook)。

| 操作 | 类型 |
|---------|---------|
| 通过 HTTP 请求运行函数 | [触发器](./functions-bindings-http-webhook-trigger.md) |
| 从函数返回 HTTP 响应 |[输出绑定](./functions-bindings-http-webhook-output.md) |

本文中的代码默认使用 .NET Core 语法，该语法在 Functions 2.x 版及更高版本中使用。 有关 1.x 语法的信息，请参阅 [1.x functions 模板](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)。

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
[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[更新扩展]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 应用会自动引用 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 程序包（版本 2.x）。

## <a name="next-steps"></a>后续步骤

- [通过 HTTP 请求运行函数](./functions-bindings-http-webhook-trigger.md)
- [从函数返回 HTTP 响应](./functions-bindings-http-webhook-output.md)