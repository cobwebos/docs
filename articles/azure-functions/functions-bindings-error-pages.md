---
title: Azure Functions 错误处理指南 | Microsoft Docs
description: 提供有关如何处理函数执行时发生的错误的常规指南，并链接到特定于绑定的错误主题。
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: ef8f2d5a63f7924097362f6aa0ebc78cc0f6455f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480707"
---
# <a name="azure-functions-error-handling"></a>Azure Functions 错误处理

本主题提供有关如何处理函数执行时发生的错误的常规指南。 对于可能会发生的特定于绑定的错误，它还提供指向说明这些错误的主题的链接。 

## <a name="handing-errors-in-functions"></a>处理函数中的错误
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>绑定错误代码

与 Azure 服务集成时，可能会引发来源于底层服务 API 的错误。 有关这些服务的错误代码文档的链接，可以在以下触发器和绑定参考主题的**异常和返回代码**部分中找到：

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob 存储](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [事件中心](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [通知中心](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [队列存储](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [服务总线](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [表存储](functions-bindings-storage-table.md#exceptions-and-return-codes)
