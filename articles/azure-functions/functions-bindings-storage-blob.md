---
title: 适用于 Azure Functions 的 Azure Blob 存储触发器和绑定
description: 了解如何在 Azure Functions 中使用 Azure Blob 存储触发器和绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462303"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>适用于 Azure Functions 的 Azure Blob 存储绑定概述

Azure Functions 通过[触发器和绑定](./functions-triggers-bindings.md)与[Azure 存储](https://docs.microsoft.com/azure/storage/)集成。 通过与 Blob 存储集成，你可以构建对 blob 数据的更改进行响应的函数，以及读取和写入值。

| 操作 | 类型 |
|---------|---------|
| 将函数作为 blob 存储数据更改运行 | [触发器](./functions-bindings-storage-blob-trigger.md) |
| 在函数中读取 blob 存储数据 | [输入绑定](./functions-bindings-storage-blob-input.md) |
| 允许函数写入 blob 存储数据 |[输出绑定](./functions-bindings-storage-blob-output.md) |

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

- [在 blob 存储数据更改时运行函数](./functions-bindings-storage-blob-trigger.md)
- [函数运行时读取 blob 存储数据](./functions-bindings-storage-blob-input.md)
- [从函数中写入 blob 存储数据](./functions-bindings-storage-blob-output.md)
