---
title: Azure IoT 中心输出绑定用于 Azure 函数
description: 了解如何使用 Azure 函数将消息写入 Azure IoT 中心流。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277422"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Azure IoT 中心输出绑定用于 Azure 函数

本文介绍如何使用 IoT 中心的 Azure 函数输出绑定。 IoT 中心支持基于 [Azure 事件中心绑定](functions-bindings-event-hubs.md)。

有关设置和配置详细信息的信息，请参阅[概述](functions-bindings-event-iot.md)。

> [!IMPORTANT]
> 虽然下述代码示例使用事件中心 API，但给定的语法适用于 IoT 中心函数。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>后续步骤

- [响应发送到事件中心事件流的事件（触发器）](./functions-bindings-event-iot-trigger.md)
