---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f771b6b0416c5777c1ebde7e2cf2c4ffc6f375ff
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155305"
---
Azure Functions 中引发的错误可以来自以下任何来源：

- 使用内置 Azure Functions[触发器和绑定](..\articles\azure-functions\functions-triggers-bindings.md)
- 对底层 Azure 服务的 Api 的调用
- 调用 REST 终结点
- 对客户端库、包或第三方 Api 的调用

以下稳定的错误处理方法对于避免丢失数据或丢失消息很重要。 建议的错误处理做法包括下列操作：

- [启用 Application Insights](../articles/azure-functions/functions-monitoring.md)
- [使用结构化错误处理](#use-structured-error-handling)
- [幂等性设计](../articles/azure-functions/functions-idempotent.md)
- 实施重试策略（如果适用）

### <a name="use-structured-error-handling"></a>使用结构化错误处理

捕获和发布错误对监视应用程序的运行状况非常重要。 任何函数代码的最顶层应包含 try/catch 块。 在 catch 块中，可以捕获并发布错误。

### <a name="retry-support"></a>重试支持

以下触发器具有内置重试支持：

* [Azure Blob 存储](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure 队列存储](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure 服务总线（队列/主题）](../articles/azure-functions/functions-bindings-service-bus.md)

默认情况下，这些触发器最多触发5次请求。 在第五次重试后，这两个触发器都会向[病毒队列](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages)写入一条消息。

需要为任何其他触发器或绑定类型手动实现重试策略。 手动实现可能包括将错误信息写入[病毒消息队列](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs)。 通过向病毒队列中写入，你有机会稍后重试操作。 此方法与 Blob 存储触发器使用的方法相同。
