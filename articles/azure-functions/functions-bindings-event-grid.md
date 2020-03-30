---
title: Azure 函数的 Azure 事件网格绑定
description: 了解如何处理 Azure Functions 中的事件网格事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 21654a3b325e8b8f0a3e49ee64b7624c8540d0d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461073"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure 函数的 Azure 事件网格绑定

此参考说明如何在 Azure 函数中处理[事件网格](../event-grid/overview.md)事件。 有关如何在 HTTP 终结点中处理事件网格消息的详细信息，请参阅[将事件接收到 HTTP 终结点](../event-grid/receive-events.md)。

事件网格是一个 Azure 服务，它可以发送 HTTP 请求来告知发布方中发生的事件情况。** 发布方是发起事件的服务或资源。 例如，Azure Blob 存储帐户是发布方，而 [Blob 上传或删除是事件](../storage/blobs/storage-blob-event-overview.md)。 某些 [Azure 服务原生支持向事件网格发布事件](../event-grid/overview.md#event-sources)。

事件处理程序接收并处理事件。** Azure Functions 是[原生支持处理事件网格事件的多个 Azure 服务](../event-grid/overview.md#event-handlers)之一。 在此引用中，您将学习使用事件网格触发器在从事件网格接收事件时调用函数，并使用输出绑定将事件发送到[事件网格自定义主题](../event-grid/post-to-custom-topic.md)。

如果您愿意，可以使用 HTTP 触发器来处理事件网格事件;因此，您可以使用 HTTP 触发器来处理事件网格事件。请参阅[将事件接收到 HTTP 终结点](../event-grid/receive-events.md)。 目前，当事件在[云事件架构](../event-grid/cloudevents-schema.md#azure-functions)中传递时，不能为 Azure 函数应用使用事件网格触发器。 应转而使用 HTTP 触发器。

| 操作 | 类型 |
|---------|---------|
| 调度事件网格事件时运行函数 | [触发](./functions-bindings-event-grid-trigger.md) |
| 发送事件网格事件 |[输出绑定](./functions-bindings-event-grid-output.md) |

此引用中的代码默认为 .NET Core 语法，用于函数版本 2.x 和更高版本。 有关 1.x 语法的信息，请参阅 [1.x functions 模板](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)。

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
[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[更新扩展]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函数 1.x 应用会自动具有[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包（版本 2.x）的引用。

## <a name="next-steps"></a>后续步骤
* [调度事件网格事件时运行函数](./functions-bindings-event-grid-trigger.md)
* [调度事件网格事件](./functions-bindings-event-grid-trigger.md)
