---
title: 适用于 Functions 2.x 及更高版本的 Azure Cosmos DB 绑定
description: 了解如何在 Azure Functions 中使用 Azure Cosmos DB 触发器和绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: dde3b95c7997a7f742a9d48a964f4275169f6e9c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106920"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>适用于 Azure Functions 2.x 及更高版本的 Azure Cosmos DB 触发器和绑定概述

> [!div class="op_single_selector" title1="选择要使用的 Azure Functions 运行时的版本： "]
> * [版本 1](functions-bindings-cosmosdb.md)
> * [版本 2 及更高版本](functions-bindings-cosmosdb-v2.md)

此系列文章介绍如何在 Azure Functions 2.x 及更高版本中使用 [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) 绑定。 Azure Functions 支持 Azure Cosmos DB 的触发器、输入和输出绑定。

| 操作 | 类型 |
|---------|---------|
| 创建或修改 Azure Cosmos DB 文档时运行函数 | [触发器](./functions-bindings-cosmosdb-v2-trigger.md) |
| 读取 Azure Cosmos DB 文档 | [输入绑定](./functions-bindings-cosmosdb-v2-input.md) |
| 保存对 Azure Cosmos DB 文档的更改  |[输出绑定](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> 此参考适用于 [Azure Functions 2.x 及更高版本](functions-versions.md)。  若要了解如何在 Functions 1.x 中使用这些绑定，请参阅[适用于 Azure Functions 1.x 的 Azure Cosmos DB 绑定](functions-bindings-cosmosdb.md)。
>
> 此绑定最初名为 DocumentDB。 在 Functions 2.x 及更高版本中，触发器、绑定和包均称为 Cosmos DB。

## <a name="supported-apis"></a>受支持的 API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>添加到 Functions 应用

### <a name="functions-2x-and-higher"></a>Functions 2.x 及更高版本

使用触发器和绑定需要引用相应的程序包。 NuGet 包用于 .NET 类库，而扩展捆绑包用于其他所有应用程序类型。

| 语言                                        | 添加方式...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装 [NuGet 包]版本 3.x | |
| C # 脚本，Java，JavaScript，Python，PowerShell | 注册[扩展捆绑包]          | 建议将 [Azure 工具扩展]用于 Visual Studio Code。 |
| C# 脚本（Azure 门户中仅限联机）         | 添加绑定                            | 若要更新现有绑定扩展而不必重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[扩展捆绑包]: ./functions-bindings-register.md#extension-bundles
[更新扩展]: ./functions-bindings-register.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 应用会自动引用 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 程序包（版本 2.x）。

## <a name="next-steps"></a>后续步骤

- [创建或修改 Azure Cosmos DB 文档时运行函数（触发器）](./functions-bindings-cosmosdb-v2-trigger.md)
- [读取 Azure Cosmos DB 文档（输入绑定）](./functions-bindings-cosmosdb-v2-input.md)
- [保存对 Azure Cosmos DB 文档的更改（输出绑定）](./functions-bindings-cosmosdb-v2-output.md)