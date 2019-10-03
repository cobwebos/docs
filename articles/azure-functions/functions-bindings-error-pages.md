---
title: Azure Functions 错误处理指南
description: 了解如何处理 Azure Functions 中的错误以及指向特定绑定错误的链接。
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: d301940998175adb6469e46cff2de918b5cae9df
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155283"
---
# <a name="azure-functions-error-handling"></a>Azure Functions 错误处理

在 Azure Functions 中处理错误对于避免丢失数据、丢失事件以及监视应用程序的运行状况非常重要。

本文介绍了用于错误处理的常规策略以及指向绑定特定错误的链接。

## <a name="handling-errors"></a>处理错误

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>绑定错误代码

与 Azure 服务集成时，错误可能源自基础服务的 Api。 以下文章的 "**异常" 和 "返回代码**" 部分提供了与特定于绑定的错误相关的信息：

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob 存储](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [事件中心](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [通知中心](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [队列存储](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [服务总线](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [表存储](functions-bindings-storage-table.md#exceptions-and-return-codes)
