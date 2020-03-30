---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77474123"
---
Azure Functions 中引发的错误可能来自以下任一来源：

- 使用内置 Azure Functions [触发器和绑定](..\articles\azure-functions\functions-triggers-bindings.md)
- 调用底层 Azure 服务的 API
- 调用 REST 终结点
- 调用客户端库、包或第三方 API

遵循可靠的错误处理做法对于避免数据丢失或遗漏消息非常重要。 建议的错误处理做法包括下列操作：

- [启用 Application Insights](../articles/azure-functions/functions-monitoring.md)
- [使用结构化错误处理](#use-structured-error-handling)
- [幂等性设计](../articles/azure-functions/functions-idempotent.md)
- [实施重试策略](../articles/azure-functions/functions-reliable-event-processing.md)（如果适用）

### <a name="use-structured-error-handling"></a>使用结构化错误处理

捕获和发布错误对于监视应用程序的运行状况至关重要。 任何函数代码的最顶层应包含 try/catch 块。 在 catch 块中，可以捕获并发布错误。

### <a name="retry-support"></a>重试支持

以下触发器具有内置重试支持：

* [Azure Blob 存储](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure 队列存储](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure 服务总线（队列/主题）](../articles/azure-functions/functions-bindings-service-bus.md)

默认情况下，这些触发器最多重试请求五次。 第五次重试后，Azure 队列存储和 Azure 服务总线触发器会将消息写入 [有害队列](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages)。

需要为任何其他触发器或绑定类型手动实现重试策略。 手动实现可能包括将错误信息写入到一个[有害队列](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs)。 写入到有害队列后，你就有机会在稍后重试操作。 Blob 存储触发器也使用此方法。
