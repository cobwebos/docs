---
title: Azure Functions 的 Azure 事件中心绑定
description: 了解如何在 Azure Functions 中使用 Azure 事件中心绑定。
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 11/08/2017
ms.author: cshoe
ms.openlocfilehash: 2e76853a7b1bf2e6dfda84ffa1454074c266d2c1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227275"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Functions 的 Azure 事件中心绑定

本文介绍如何使用 Azure Functions 的 [Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)绑定。 Azure Functions 支持事件中心的触发器和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>包 - Functions 2.x

对于 Azure Functions 版本 1.x，[Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet 包 2.x 版中提供了事件中心绑定。
[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub 存储库中提供了此包的源代码。


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>包 - Functions 2.x

对于 Functions 2.x，请使用 [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) 包 3.x 版。
[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
