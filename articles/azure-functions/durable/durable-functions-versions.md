---
title: Durable Functions 版本概述-Azure Functions
description: 了解 Durable Functions 版本。
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152884"
---
# <a name="durable-functions-versions-overview"></a>Durable Functions 版本概述

Durable Functions 是 [Azure Functions](../functions-overview.md) 和 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) 的扩展，可用于在无服务器环境中编写有状态函数。 该扩展可用于管理状态、检查点和重启。 如果你尚不熟悉 Durable Functions，请参阅[概述文档](durable-functions-overview.md)。

## <a name="new-features-in-2x"></a>2\.x 中的新增功能

本部分介绍了在版本2.x 中添加的 Durable Functions 的功能。

### <a name="durable-entities"></a>持久性实体

在 Durable Functions 1.x 中，我们引入了一个新的[实体函数](durable-functions-entities.md)概念。

实体函数定义读取和更新较小状态片段（称为“持久实体”）的操作。 与业务流程协调程序函数一样，实体函数是具有特殊触发器类型“实体触发器”的函数。 与 orchestrator 函数不同，实体函数没有任何特定的代码约束。 实体函数还会显式管理状态，而不是通过控制流隐式表示状态。

若要了解详细信息，请参阅[持久实体](durable-functions-entities.md)一文。

### <a name="durable-http"></a>持久 HTTP

在 Durable Functions 1.x 中，我们引入了新的[持久 HTTP](durable-functions-http-features.md#consuming-http-apis)功能，使你能够：

* 直接从业务流程函数调用 HTTP Api （有一些记录的限制）。
* 实现自动客户端 HTTP 202 状态轮询。
* [Azure 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)的内置支持。

若要了解详细信息，请参阅[HTTP 功能](durable-functions-http-features.md#consuming-http-apis)一文。

## <a name="migrate-from-1x-to-2x"></a>从1.x 迁移到2。x

本部分介绍如何将现有版本 1.x Durable Functions 迁移到版本2.x 以利用新功能。

### <a name="upgrade-the-extension"></a>升级扩展

在项目中安装[Durable Functions 绑定扩展](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)的版本2.x。 有关详细信息，请参阅[Register Azure Functions 绑定扩展](../functions-bindings-register.md)。

### <a name="update-your-code"></a>更新代码

Durable Functions 2.x 引入了几项重大更改。 Durable Functions 1.x 应用程序与不进行代码更改的 Durable Functions 2.x 不兼容。 本部分列出了将版本1.x 函数升级到2.x 时必须做出的一些更改。

#### <a name="hostjson-schema"></a>Host json 架构

Durable Functions 2.x 使用新的 host json 架构。 1\. x 的主要变化包括：

* 用于存储特定配置的 `"storageProvider"` （和 `"azureStorage"` 子节）。
* `"tracing"` 跟踪和日志记录配置。
* 事件网格通知配置的 `"notifications"` （和 `"eventGrid"` 子节）。

有关详细信息，请参阅[Durable Functions host json 参考文档](durable-functions-bindings.md#durable-functions-2-0-host-json)。

#### <a name="default-taskhub-name-changes"></a>默认 taskhub 名称更改

在版本1.x 中，如果未在 host. json 中指定任务中心名称，则默认为 "DurableFunctionsHub"。 在版本2.x 中，默认的任务中心名称现在派生自 function app 的名称。 因此，如果你在升级到2.x 时未指定任务中心名称，你的代码将使用新的任务中心操作，并且所有正在进行的业务流程将不再具有处理它们的应用程序。 若要解决此情况，可以显式将任务中心名称设置为默认值 "DurableFunctionsHub"，也可以按照我们的[零停机部署指南](durable-functions-zero-downtime-deployment.md)来了解如何处理正在进行的业务流程的重大更改。

#### <a name="public-interface-changes-net-only"></a>公共接口更改（仅适用于 .NET）

在版本1.x 中，Durable Functions 支持的各种_上下文_对象具有要在单元测试中使用的抽象基类。 作为 Durable Functions 2.x 的一部分，这些抽象基类被替换为接口。

下表表示主要更改：

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` 或 `IDurableClient` |
| `DurableOrchestrationContext` 或 `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` 或 `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

在抽象基类包含虚拟方法的情况下，这些虚方法已由 `DurableContextExtensions`中定义的扩展方法替换。

#### <a name="functionjson-changes-javascript-and-c-script"></a>函数 json 更改（JavaScript 和C#脚本）

在 Durable Functions 1.x 中，业务流程客户端绑定使用 `type` 的 `orchestrationClient`。 版本2.x 改用 `durableClient`。
