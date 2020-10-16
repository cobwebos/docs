---
title: Azure Functions 的 Azure 事件网格绑定
description: 了解如何处理 Azure Functions 中的事件网格事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 115ff1b59ecbe3f4fdb089c2bd61da955fba3984
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104540"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Functions 的 Azure 事件网格绑定

本参考介绍了如何在 Azure Functions 中处理[事件网格](../event-grid/overview.md)事件。 有关如何在 HTTP 终结点中处理事件网格消息的详细信息，请参阅[将事件接收到 HTTP 终结点](../event-grid/receive-events.md)。

事件网格是一个 Azure 服务，它可以发送 HTTP 请求来告知发布方中发生的事件情况。 发布方是发起事件的服务或资源。 例如，Azure Blob 存储帐户是发布方，而 [Blob 上传或删除是事件](../storage/blobs/storage-blob-event-overview.md)。 某些 [Azure 服务原生支持向事件网格发布事件](../event-grid/overview.md#event-sources)。

事件处理程序接收并处理事件。 Azure Functions 是[原生支持处理事件网格事件的多个 Azure 服务](../event-grid/overview.md#event-handlers)之一。 在本参考中，你将了解如何使用事件网格触发器在从事件网格接收到事件时调用函数，并使用输出绑定将事件发送到[事件网格自定义主题](../event-grid/post-to-custom-topic.md)。

如果愿意，你可以使用 HTTP 触发器处理事件网格事件；请参阅[将事件接收到 HTTP 终结点](../event-grid/receive-events.md)。 目前，在以 [CloudEvents 架构](../event-grid/cloudevents-schema.md#azure-functions)传递事件时，无法为 Azure Functions 应用使用事件网格触发器。 应转而使用 HTTP 触发器。

| 操作 | 类型 |
|---------|---------|
| 在调度事件网格事件时运行函数 | [触发器](./functions-bindings-event-grid-trigger.md) |
| 发送事件网格事件 |[输出绑定](./functions-bindings-event-grid-output.md) |

本参考中的代码默认使用 .NET Core 语法，该语法在 Functions 2.x 版及更高版本中使用。 有关 1.x 语法的信息，请参阅 [1.x functions 模板](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)。

## <a name="add-to-your-functions-app"></a>添加到 Functions 应用

### <a name="functions-2x-and-higher"></a>Functions 2.x 及更高版本

使用触发器和绑定需要引用相应的程序包。 NuGet 包用于 .NET 类库，而扩展捆绑包用于其他所有应用程序类型。

| 语言                                        | 添加方式...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装 [NuGet 包]版本 2.x | |
| C # 脚本，Java，JavaScript，Python，PowerShell | 注册[扩展捆绑包]          | 建议将 [Azure Tools 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)用于 Visual Studio Code。 |
| C# 脚本（Azure 门户中仅限联机）         | 添加绑定                            | 若要更新现有绑定扩展而不必重新发布函数应用，请参阅[更新扩展]。 |

[core tools]: ./functions-run-local.md
[扩展捆绑包]: ./functions-bindings-register.md#extension-bundles
[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[更新扩展]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 应用会自动引用 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 程序包（版本 2.x）。

## <a name="next-steps"></a>后续步骤
* [在调度事件网格事件时运行函数](./functions-bindings-event-grid-trigger.md)
* [调度事件网格事件](./functions-bindings-event-grid-trigger.md)