---
title: Azure Functions 的 Azure 事件中心绑定
description: 了解如何在 Azure Functions 中使用 Azure 事件中心触发器和绑定。
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 41495f6f10f7337c065a06d0d9b71142475bb5c4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506309"
---
# <a name="azure-event-hubs-trigger-and-bindings-for-azure-functions"></a>适用于 Azure Functions 的 Azure 事件中心触发器和绑定

本文介绍如何使用 Azure Functions 的 [Azure 事件中心](../event-hubs/event-hubs-about.md)绑定。 Azure Functions 支持事件中心的触发器和输出绑定。

| 操作 | 类型 |
|--------|------|
| 响应发送到事件中心事件流的事件。 | 触发器 |
| 将事件写入事件流 | [输出绑定](./functions-bindings-event-hubs-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>后续步骤

- [响应发送到事件中心事件流的事件（触发器）](./functions-bindings-event-hubs-trigger.md)
- [将事件写入事件流（输出绑定）](./functions-bindings-event-hubs-output.md)
