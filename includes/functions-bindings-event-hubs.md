---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 47e9e37676a4afa9ec29393bf970c368f3f9e5be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586116"
---
## <a name="add-to-your-functions-app"></a>添加到功能应用

### <a name="functions-2x-and-higher"></a>功能 2.x 及以上

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展包用于所有其他应用程序类型。

| 语言                                        | 添加...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装[NuGet 软件包]，版本 3.x | |
| C# 脚本、Java、JavaScript、Python、PowerShell | 注册[扩展包]          | 建议使用[Azure 工具扩展]与可视化工作室代码一起使用。 |
| C# 脚本（仅在 Azure 门户中联机）         | 添加绑定                            | 要更新现有绑定扩展，而无需重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[扩展包]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[更新扩展]: ../articles/azure-functions/install-update-binding-extensions-manual.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函数 1.x 应用会自动具有[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包（版本 2.x）的引用。