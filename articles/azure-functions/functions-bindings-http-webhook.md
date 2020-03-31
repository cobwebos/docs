---
title: Azure Functions HTTP 触发器和绑定
description: 了解如何在 Azure 函数中使用 HTTP 触发器和绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462099"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Azure 函数 HTTP 触发器和绑定概述

Azure 函数可以通过 HTTP 请求调用，以生成无服务器 API 并响应[Webhook。](https://en.wikipedia.org/wiki/Webhook)

| 操作 | 类型 |
|---------|---------|
| 从 HTTP 请求运行函数 | [触发](./functions-bindings-http-webhook-trigger.md) |
| 从函数返回 HTTP 响应 |[输出绑定](./functions-bindings-http-webhook-output.md) |

本文中的代码默认为 .NET Core 语法，用于函数版本 2.x 和更高版本。 有关 1.x 语法的信息，请参阅 [1.x functions 模板](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)。

## <a name="add-to-your-functions-app"></a>添加到功能应用

### <a name="functions-2x-and-higher"></a>功能 2.x 及以上

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展包用于所有其他应用程序类型。

| 语言                                        | 添加...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装[NuGet 软件包]，版本 3.x | |
| C# 脚本、Java、JavaScript、Python、PowerShell | 注册[扩展包]          | 建议使用[Azure 工具扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)与可视化工作室代码一起使用。 |
| C# 脚本（仅在 Azure 门户中联机）         | 添加绑定                            | 要更新现有绑定扩展，而无需重新发布函数应用，请参阅[更新扩展]。 |

[core tools]: ./functions-run-local.md
[扩展包]: ./functions-bindings-register.md#extension-bundles
[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[更新扩展]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函数 1.x 应用会自动具有[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包（版本 2.x）的引用。

## <a name="next-steps"></a>后续步骤

- [从 HTTP 请求运行函数](./functions-bindings-http-webhook-trigger.md)
- [从函数返回 HTTP 响应](./functions-bindings-http-webhook-output.md)
