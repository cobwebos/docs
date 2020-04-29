---
title: 适用于 Azure Functions 的 Azure IoT 中心输出绑定
description: 了解如何使用 Azure Functions 将消息写入 Azure IoT 中心流。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277422"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>适用于 Azure Functions 的 Azure IoT 中心输出绑定

本文介绍如何使用 IoT 中心的 Azure Functions 输出绑定。 IoT 中心支持基于 [Azure 事件中心绑定](functions-bindings-event-hubs.md)。

若要了解设置和配置详细信息，请参阅[概述](functions-bindings-event-iot.md)。

> [!IMPORTANT]
> 虽然下述代码示例使用事件中心 API，但给定的语法适用于 IoT 中心函数。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>后续步骤

- [响应发送到事件中心事件流的事件（触发器）](./functions-bindings-event-iot-trigger.md)
