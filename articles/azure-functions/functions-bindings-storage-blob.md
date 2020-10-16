---
title: 适用于 Azure Functions 的 Azure Blob 存储触发器和绑定
description: 了解如何在 Azure Functions 中使用 Azure Blob 存储触发器和绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: e56d1add36d4296526348d12d7c0b6eb03108f27
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104353"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>适用于 Azure Functions 的 Azure Blob 存储绑定概述

Azure Functions 通过[触发器和绑定](./functions-triggers-bindings.md)与 [Azure 存储](../storage/index.yml)集成。 与 Blob 存储集成即可构建函数，用于响应 blob 数据中的更改以及读取和写入值。

| 操作 | 类型 |
|---------|---------|
| 在 blob 存储数据更改时运行函数 | [触发器](./functions-bindings-storage-blob-trigger.md) |
| 在函数中读取 blob 存储数据 | [输入绑定](./functions-bindings-storage-blob-input.md) |
| 允许函数写入 blob 存储数据 |[输出绑定](./functions-bindings-storage-blob-output.md) |

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

- [在 blob 存储数据更改时运行函数](./functions-bindings-storage-blob-trigger.md)
- [函数运行时读取 blob 存储数据](./functions-bindings-storage-blob-input.md)
- [通过函数写入 blob 存储数据](./functions-bindings-storage-blob-output.md)