---
title: 适用于 Functions 2.x 的 Azure Cosmos DB 绑定
description: 了解如何在 Azure Functions 中使用 Azure Cosmos DB 触发器和绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605755"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Azure Functions 2.x 概述的 Azure Cosmos DB 触发器和绑定

> [!div class="op_single_selector" title1="选择要使用的 Azure Functions 运行时的版本： "]
> * [版本 1](functions-bindings-cosmosdb.md)
> * [第 2 版](functions-bindings-cosmosdb-v2.md)

这一组文章介绍了如何在 Azure Functions 2.x 中使用[Azure Cosmos DB](../cosmos-db/serverless-computing-database.md)绑定。 Azure Functions 支持 Azure Cosmos DB 的触发器、输入和输出绑定。

| 操作 | 类型 |
|---------|---------|
| 在创建或修改 Azure Cosmos DB 文档时运行函数 | [触发器](./functions-bindings-cosmosdb-v2-trigger.md) |
| 阅读 Azure Cosmos DB 文档 | [输入绑定](./functions-bindings-cosmosdb-v2-input.md) |
| 保存对 Azure Cosmos DB 文档所做的更改  |[输出绑定](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> 此参考适用于[Azure Functions 版本](functions-versions.md)2.x。  若要了解如何在 Functions 1.x 中使用这些绑定，请参阅[适用于 Azure Functions 1.x 的 Azure Cosmos DB 绑定](functions-bindings-cosmosdb.md)。
>
> 此绑定最初名为 DocumentDB。 在 Functions 2.x 版中，触发器、绑定和包均称为 Cosmos DB。

## <a name="supported-apis"></a>受支持的 API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>添加到函数应用

### <a name="functions-2x-and-higher"></a>函数1.x 和更高版本

使用触发器和绑定需要引用适当的包。 NuGet 包用于 .NET 类库，而扩展捆绑用于所有其他应用程序类型。

| 语言                                        | 添加依据 。                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装[NuGet 包]版本2。x | |
| C#脚本、Java、JavaScript、Python、PowerShell | 注册[扩展捆绑]          | 建议将[Azure 工具扩展]用于 Visual Studio Code。 |
| C#脚本（仅在 Azure 门户中为联机）         | 添加绑定                            | 若要更新现有绑定扩展，而不必重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[扩展捆绑]: ./functions-bindings-register.md#extension-bundles
[更新扩展]: ./install-update-binding-extensions-manual.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函数1.x 应用程序会自动引用[Microsoft Azure Web 作业](https://www.nuget.org/packages/Microsoft.Azure.WebJobs)NuGet 包版本2.x。

## <a name="next-steps"></a>后续步骤

- [在创建或修改 Azure Cosmos DB 文档时运行函数（触发器）](./functions-bindings-cosmosdb-v2-trigger.md)
- [读取 Azure Cosmos DB 文档（输入绑定）](./functions-bindings-cosmosdb-v2-input.md)
- [保存对 Azure Cosmos DB 文档所做的更改（输出绑定）](./functions-bindings-cosmosdb-v2-output.md)
