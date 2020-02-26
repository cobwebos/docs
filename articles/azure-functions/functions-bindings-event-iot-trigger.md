---
title: 适用于 Azure Functions 的 Azure IoT 中心绑定
description: 了解如何响应 Azure Functions 中发送到 IoT 中心事件流的事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: f63fe965b3f37add8ddf9d262f1ef1dae9fff966
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589722"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>适用于 Azure Functions 的 Azure IoT 中心触发器

本文介绍如何使用 IoT 中心的 Azure Functions 绑定。 IoT 中心支持基于[Azure 事件中心绑定](functions-bindings-event-hubs.md)。

有关设置和配置的详细信息，请参阅[概述](functions-bindings-event-iot.md)。

> [!IMPORTANT]
> 尽管下面的代码示例使用事件中心 API，但给定语法适用于 IoT 中心函数。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>后续步骤

- [将事件写入事件流（输出绑定）](./functions-bindings-event-iot-output.md)
