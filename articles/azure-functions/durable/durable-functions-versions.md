---
title: Durable Functions 版本概述 - Azure Functions
description: 了解 Durable Functions 版本。
author: cgillum
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: azfuncdf
ms.openlocfilehash: d6662259494bba5747e01c4574186e9030112247
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88719404"
---
# <a name="durable-functions-versions-overview"></a>Durable Functions 版本概述

Durable Functions 是 [Azure Functions](../functions-overview.md) 和 [Azure WebJobs](../../app-service/webjobs-create.md) 的扩展，可用于在无服务器环境中编写有状态函数。 该扩展可用于管理状态、检查点和重启。 如果你不熟悉 Durable Functions，请参阅[概述文档](durable-functions-overview.md)。

## <a name="new-features-in-2x"></a>2\.x 中的新功能

此部分介绍在 2.x 版中添加的 Durable Functions 功能。

### <a name="durable-entities"></a>持久实体

在 Durable Functions 2.x 中，我们引入了一个新的[实体函数](durable-functions-entities.md)概念。

实体函数定义用于读取和更新较小状态片段（称为“持久实体”）的操作。 与业务流程协调程序函数类似，实体函数是具有特殊触发器类型“实体触发器”的函数。 与业务流程协调程序函数不同，实体函数没有任何特定的代码约束。 实体函数还会显式管理状态，而不是通过控制流隐式表示状态。

有关详细信息，请参阅[持久实体](durable-functions-entities.md)一文。

### <a name="durable-http"></a>持久 HTTP

在 Durable Functions 2.x 中，我们引入了新的[持久 HTTP](durable-functions-http-features.md#consuming-http-apis) 功能，该功能允许你：

* 直接从业务流程函数调用 HTTP API（有一些记录的限制）。
* 实现自动客户端 HTTP 202 状态轮询。
* 对 [Azure 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)的内置支持。

若要了解更多信息，请参阅 [HTTP 功能](durable-functions-http-features.md#consuming-http-apis)一文。

## <a name="migrate-from-1x-to-2x"></a>从 1.x 迁移到 2.x

此部分介绍如何将现有的 1.x 版 Durable Functions 迁移到 2.x 版，以便利用新功能。

### <a name="upgrade-the-extension"></a>升级扩展

在项目中安装最新的 2.x 版 Durable Functions 绑定扩展。

#### <a name="javascript-python-and-powershell"></a>JavaScript、Python 和 PowerShell

Durable Functions 2.x 在 [Azure Functions 扩展包](../functions-bindings-register.md#extension-bundles)的版本 2.x 中可用。

若要更新项目中的扩展包版本，请打开 host.json 并更新 `extensionBundle` 部分以使用版本 2.x (`[2.*, 3.0.0)`)。

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

#### <a name="net"></a>.NET

更新 .NET 项目以使用最新版本的 [Durable Functions 绑定扩展](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)。

有关详细信息，请参阅[注册 Azure Functions 绑定扩展](../functions-bindings-register.md#local-csharp)。

### <a name="update-your-code"></a>更新代码

Durable Functions 2.0 引入了几项中断性变更。 Durable Functions 1.x 应用程序在不更改代码的情况下预期不兼容 Durable Functions 2.x。 此部分列出了在将 1.x Functions 升级到 2.x 时必须完成的部分更改。

#### <a name="hostjson-schema"></a>Host.json 架构

Durable Functions 2.x 使用新的 host.json 架构。 基于 1.x 的主要更改如下：

* 存储特定的配置的 `"storageProvider"`（和 `"azureStorage"` 子节）。
* 用于跟踪和记录配置的 `"tracing"`。
* 适用于事件网格通知配置的 `"notifications"`（和 `"eventGrid"` 子节）。

有关详细信息，请参阅 [Durable Functions host.json 参考文档](durable-functions-bindings.md#durable-functions-2-0-host-json)。

#### <a name="default-taskhub-name-changes"></a>默认的任务中心名称更改

在版本 1.x 中，如果未在 host.json 中指定任务中心名称，则该名称默认为“DurableFunctionsHub”。 在版本 2.x 中，默认的任务中心名称现在派生自函数应用的名称。 因此，如果在升级到 2.x 时未指定任务中心名称，则代码会使用新的任务中心进行操作，并且所有正在进行的业务流程将不再有处理它们的应用程序。 若要解决此情况，可以显式将任务中心名称设置为 v1.x 默认值“DurableFunctionsHub”，也可以参阅我们的[零停机部署指南](durable-functions-zero-downtime-deployment.md)，详细了解如何处理正在进行的业务流程的中断性变更。

#### <a name="public-interface-changes-net-only"></a>公共接口更改（仅 .NET）

在 1.x 版中，Durable Functions 支持的各种“上下文”对象包含适合在单元测试中使用的抽象基类。 作为 Durable Functions 2.x 的一部分，这些抽象基类由接口取代。

下表显示了主要更改：

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` 或 `IDurableClient` |
| `DurableOrchestrationContext` 或 `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` 或 `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

如果抽象基类包含虚拟方法，则这些虚拟方法已由 `DurableContextExtensions` 中定义的扩展方法取代。

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.json 更改（JavaScript 和 C# Script）

在 Durable Functions 1.x 中，业务流程客户端使用的 `type` 为 `orchestrationClient`。 2\.x 版改用 `durableClient`。

#### <a name="raise-event-changes"></a>引发事件更改

在 Durable Functions 1.x 中，调用[引发事件](durable-functions-external-events.md#send-events) API 并指定不存在的实例会导致静默失败。 从 2.x 开始，将事件引发到不存在的业务流程会导致异常。
