---
title: 适用于 Functions 2.x 的 Azure Cosmos DB 绑定
description: 了解如何在 Azure Functions 中使用 Azure Cosmos DB 触发器和绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605755"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Azure Cosmos DB 触发器和绑定，用于 Azure 函数 2.x 概述

> [!div class="op_single_selector" title1="选择要使用的 Azure Functions 运行时的版本： "]
> * [版本 1](functions-bindings-cosmosdb.md)
> * [版本 2](functions-bindings-cosmosdb-v2.md)

这组文章介绍了如何使用 Azure 函数 2.x 中的[Azure Cosmos DB](../cosmos-db/serverless-computing-database.md)绑定。 Azure Functions 支持 Azure Cosmos DB 的触发器、输入和输出绑定。

| 操作 | 类型 |
|---------|---------|
| 创建或修改 Azure Cosmos DB 文档时运行函数 | [触发](./functions-bindings-cosmosdb-v2-trigger.md) |
| 阅读 Azure 宇宙数据库文档 | [输入绑定](./functions-bindings-cosmosdb-v2-input.md) |
| 保存对 Azure 宇宙数据库文档的更改  |[输出绑定](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> 此引用适用于[Azure 函数版本 2.x](functions-versions.md)。  若要了解如何在 Functions 1.x 中使用这些绑定，请参阅[适用于 Azure Functions 1.x 的 Azure Cosmos DB 绑定](functions-bindings-cosmosdb.md)。
>
> 此绑定最初名为 DocumentDB。 在 Functions 2.x 版中，触发器、绑定和包均称为 Cosmos DB。

## <a name="supported-apis"></a>受支持的 API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>添加到功能应用

### <a name="functions-2x-and-higher"></a>功能 2.x 及以上

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展包用于所有其他应用程序类型。

| 语言                                        | 添加...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装[NuGet 软件包]，版本 3.x | |
| C# 脚本、Java、JavaScript、Python、PowerShell | 注册[扩展包]          | 建议使用[Azure 工具扩展]与可视化工作室代码一起使用。 |
| C# 脚本（仅在 Azure 门户中联机）         | 添加绑定                            | 要更新现有绑定扩展，而无需重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[扩展包]: ./functions-bindings-register.md#extension-bundles
[更新扩展]: ./install-update-binding-extensions-manual.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函数 1.x 应用会自动具有[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包（版本 2.x）的引用。

## <a name="next-steps"></a>后续步骤

- [创建或修改 Azure 宇宙数据库文档时运行函数（触发器）](./functions-bindings-cosmosdb-v2-trigger.md)
- [阅读 Azure 宇宙数据库文档（输入绑定）](./functions-bindings-cosmosdb-v2-input.md)
- [保存对 Azure Cosmos DB 文档的更改（输出绑定）](./functions-bindings-cosmosdb-v2-output.md)
