---
title: Durable Functions versions overview - Azure Functions
description: Learn about Durable Functions versions.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 93c35eb4f69cc4f9b16f669d96c2df53f50bcf84
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231190"
---
# <a name="durable-functions-versions-overview"></a>Durable Functions versions overview

Durable Functions 是 [Azure Functions](../functions-overview.md) 和 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) 的扩展，可用于在无服务器环境中编写有状态函数。 该扩展可用于管理状态、检查点和重启。 If you are not already familiar with Durable Functions, see the [overview documentation](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>New features in 2.x

This section describes the features of Durable Functions that are added in version 2.x.

### <a name="durable-entities"></a>Durable entities

In Durable Functions 2.x, we introduced a new [entity functions](durable-functions-entities.md) concept.

实体函数定义读取和更新较小状态片段（称为“持久实体”）的操作。 与业务流程协调程序函数一样，实体函数是具有特殊触发器类型“实体触发器”的函数。 Unlike orchestrator functions, entity functions do not have any specific code constraints. 实体函数还会显式管理状态，而不是通过控制流隐式表示状态。

To learn more, see the [durable entities](durable-functions-entities.md) article.

### <a name="durable-http"></a>Durable HTTP

In Durable Functions 2.x, we introduced a new [Durable HTTP](durable-functions-http-features.md#consuming-http-apis) feature that allows you to:

* Call HTTP APIs directly from orchestration functions (with some documented limitations).
* Implement automatic client-side HTTP 202 status polling.
* Built-in support for [Azure Managed Identities](../../active-directory/managed-identities-azure-resources/overview.md).

To learn more, see the [HTTP features](durable-functions-http-features.md#consuming-http-apis) article.

## <a name="migrate-from-1x-to-2x"></a>Migrate from 1.x to 2.x

This section describes how to migrate your existing version 1.x Durable Functions to version 2.x to take advantage of the new features.

### <a name="upgrade-the-extension"></a>Upgrade the extension

Install version 2.x of the [Durable Functions bindings extension](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) in your project. See [Register Azure Functions binding extensions](../functions-bindings-register.md) for more information.

### <a name="update-your-code"></a>Update your code

Durable Functions 2.x introduces several breaking changes. Durable Functions 1.x applications are not compatible with Durable Functions 2.x without code changes. This section lists some of the changes you must make when upgrading your version 1.x functions to 2.x.

#### <a name="hostjson-schema"></a>Host.json schema

Durable Functions 2.x uses a new host.json schema. The main changes from 1.x include:

* `"storageProvider"` (and the `"azureStorage"` subsection) for storage-specific configuration.
* `"tracking"` for tracking and logging configuration.
* `"notifications"` (and the `"eventGrid"` subsection) for event grid notification configuration.

See the [Durable Functions host.json reference documentation](durable-functions-bindings.md#durable-functions-2-0-host-json) for details.

#### <a name="public-interface-changes-net-only"></a>Public interface changes (.NET only)

In version 1.x, the various _context_ objects supported by Durable Functions have abstract base classes intended for use in unit testing. As part of Durable Functions 2.x, these abstract base classes are replaced with interfaces.

The following table represents the main changes:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` 或 `IDurableClient` |
| `DurableOrchestrationContext` 或 `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` 或 `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

In the case where an abstract base class contained virtual methods, these virtual methods have been replaced by extension methods defined in `DurableContextExtensions`.

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.json changes (JavaScript and C# Script)

In Durable Functions 1.x, the orchestration client binding uses a `type` of `orchestrationClient`. Version 2.x uses `durableClient` instead.
