---
title: 适用于 Azure Functions 的 Azure IoT 中心绑定
description: 了解如何在 Azure Functions 中响应发送到 IoT 中心事件流的事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: f63fe965b3f37add8ddf9d262f1ef1dae9fff966
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77589722"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure Functions 的 Azure IoT 中心触发器

本文介绍如何使用 IoT 中心的 Azure Functions 绑定。 IoT 中心支持基于 [Azure 事件中心绑定](functions-bindings-event-hubs.md)。

若要了解设置和配置详细信息，请参阅[概述](functions-bindings-event-iot.md)。

> [!IMPORTANT]
> 虽然下述代码示例使用事件中心 API，但给定的语法适用于 IoT 中心函数。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>后续步骤

- [将事件写入事件流（输出绑定）](./functions-bindings-event-iot-output.md)
