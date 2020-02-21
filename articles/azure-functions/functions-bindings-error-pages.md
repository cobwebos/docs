---
title: Azure Functions 错误处理指南
description: 了解如何处理 Azure Functions 中的错误以及指向特定绑定错误的链接。
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 0da8322a57d925608f7b52054c7a52e6cb3e5d06
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484649"
---
# <a name="azure-functions-error-handling"></a>Azure Functions 错误处理

在 Azure Functions 中处理错误对于避免丢失数据、丢失事件以及监视应用程序的运行状况非常重要。

本文介绍了用于错误处理的常规策略以及指向绑定特定错误的链接。

## <a name="handling-errors"></a>处理错误

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>绑定错误代码

与 Azure 服务集成时，错误可能源自基础服务的 Api。 以下文章的 "**异常" 和 "返回代码**" 部分提供了与特定于绑定的错误相关的信息：

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob 存储](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [事件中心](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [通知中心](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [队列存储](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [服务总线](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [表存储](functions-bindings-storage-table.md#exceptions-and-return-codes)
