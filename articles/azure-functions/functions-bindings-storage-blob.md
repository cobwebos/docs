---
title: Azure Blob 存储触发器和用于 Azure 函数的绑定
description: 了解如何在 Azure 函数中使用 Azure Blob 存储触发器和绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277227"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Blob 存储绑定，用于 Azure 函数概述

Azure 函数通过[触发器和绑定](./functions-triggers-bindings.md)与[Azure 存储](https://docs.microsoft.com/azure/storage/)集成。 通过与 Blob 存储集成，可以生成响应 Blob 数据更改以及读取和写入值的函数。

| 操作 | 类型 |
|---------|---------|
| 当 Blob 存储数据更改时运行函数 | [触发](./functions-bindings-storage-blob-trigger.md) |
| 在函数中读取 blob 存储数据 | [输入绑定](./functions-bindings-storage-blob-input.md) |
| 允许函数写入 blob 存储数据 |[输出绑定](./functions-bindings-storage-blob-output.md) |

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
[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[更新扩展]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函数 1.x 应用会自动具有[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包（版本 2.x）的引用。

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>后续步骤

- [Blob 存储数据更改时运行函数](./functions-bindings-storage-blob-trigger.md)
- [运行函数时读取 Blob 存储数据](./functions-bindings-storage-blob-input.md)
- [从函数写入 blob 存储数据](./functions-bindings-storage-blob-output.md)
