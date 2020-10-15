---
title: Azure Functions 错误处理指南
description: 了解如何处理 Azure Functions 中的错误，并提供指向特定绑定错误的链接。
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 0617d55f7c67c788b1e898d963f7d509cef72d49
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096838"
---
# <a name="azure-functions-error-handling"></a>Azure Functions 错误处理

处理 Azure Functions 中的错误对于避免丢失数据、错过事件以及监视应用程序的运行状况非常重要。

本文介绍了用于错误处理的常规策略，并提供了指向绑定特定错误的链接。

## <a name="handling-errors"></a>处理错误

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>绑定错误代码

与 Azure 服务集成时，错误可能源自底层服务的 API。 以下文章的**异常和返回代码**部分中提供了与特定于绑定的错误有关的信息：

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob 存储](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [事件中心](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT 中心](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [通知中心](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [队列存储](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [服务总线](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [表存储](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
