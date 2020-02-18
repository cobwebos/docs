---
title: 适用于 Azure Functions 的 Azure 事件网格绑定
description: 了解如何处理 Azure Functions 中的事件网格事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: c9891751ac24a630819d9b0a708ffbd288ac1327
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77365152"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>适用于 Azure Functions 的 Azure 事件网格绑定

本参考说明如何处理 Azure Functions 中的[事件网格](../event-grid/overview.md)事件。 有关如何在 HTTP 终结点中处理事件网格消息的详细信息，请参阅[将事件接收到 http 终结](../event-grid/receive-events.md)点。

事件网格是一个 Azure 服务，它可以发送 HTTP 请求来告知发布方中发生的事件情况。 发布方是发起事件的服务或资源。 例如，Azure Blob 存储帐户是发布方，而 [Blob 上传或删除是事件](../storage/blobs/storage-blob-event-overview.md)。 某些 [Azure 服务原生支持向事件网格发布事件](../event-grid/overview.md#event-sources)。

事件处理程序接收并处理事件。 Azure Functions 是[原生支持处理事件网格事件的多个 Azure 服务](../event-grid/overview.md#event-handlers)之一。 在此引用中，你将了解如何使用事件网格触发器在从事件网格接收到事件时调用函数，以及使用输出绑定将事件发送到[事件网格自定义主题](../event-grid/post-to-custom-topic.md)。

如果愿意，可以使用 HTTP 触发器处理事件网格事件;请参阅[将事件接收到 HTTP 终结点](../event-grid/receive-events.md)。 目前，在以 [CloudEvents 架构](../event-grid/cloudevents-schema.md#azure-functions)传递事件时，无法为 Azure Functions 应用使用事件网格触发器。 应转而使用 HTTP 触发器。

| 操作 | 类型 |
|---------|---------|
| 在调度事件网格事件时运行函数 | [触发器](./functions-bindings-event-grid-trigger.md) |
| 发送事件网格事件 |[输出绑定](./functions-bindings-event-grid-output.md) |

此引用中的代码默认为 .NET Core 语法，在函数版本2.x 和更高版本中使用。 有关 1.x 语法的信息，请参阅 [1.x functions 模板](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)。

## <a name="add-to-your-functions-app"></a>添加到函数应用

### <a name="functions-2x-and-higher"></a>函数1.x 和更高版本

使用触发器和绑定需要引用适当的包。 NuGet 包用于 .NET 类库，而扩展捆绑所有其他应用程序类型。

| Language                                        | 添加依据 。                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装[NuGet 包]版本2。x | |
| C#脚本、Java、JavaScript、Python、PowerShell | 注册[扩展捆绑]          | 建议将[Azure 工具扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)用于 Visual Studio Code。 |
| C#脚本（仅在 Azure 门户中为联机）         | 添加绑定                            | 若要更新现有绑定扩展，而不必重新发布函数应用，请参阅[更新扩展]。 |

[core tools]: ./functions-run-local.md
[扩展捆绑]: ./functions-bindings-register.md#extension-bundles
[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[更新扩展]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函数1.x 应用程序会自动引用[Microsoft Azure Web 作业](https://www.nuget.org/packages/Microsoft.Azure.WebJobs)NuGet 包版本2.x。

## <a name="next-steps"></a>后续步骤
* [在调度事件网格事件时运行函数](./functions-bindings-event-grid-trigger.md)
* [调度事件网格事件](./functions-bindings-event-grid-trigger.md)
