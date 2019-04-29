---
title: 了解 Azure IoT 中心自定义终结点 | Microsoft Docs
description: 开发人员指南 - 使用路由查询将设备到云的消息路由到自定义终结点。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e5e92c40cef15e99431dc9652820c71e87935f67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61244338"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>对设备到云的消息使用消息路由和自定义终结点

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

借助 IoT 中心[消息路由](iot-hub-devguide-routing-query-syntax.md)，用户可将设备到云的消息路由到面向服务的终结点。 路由还提供了一种查询功能，让你先筛选数据，再将其路由到终结点。 所配置的每个路由查询包含以下属性：

| 属性      | 说明 |
| ------------- | ----------- |
| **名称**      | 用于标识查询的唯一名称。 |
| **源**    | 要处理的数据流的来源。 例如，设备遥测。 |
| **条件** | 针对消息应用程序属性、系统属性、消息正文、设备孪生标记和设备孪生属性运行的路由查询的查询表达式，用于确定该查询是否是终结点的匹配项。 要详细了解如何构造查询，请参阅[消息路由查询语法](iot-hub-devguide-routing-query-syntax.md) |
| **终结点**  | IoT 中心将匹配查询的消息发送到的终结点的名称。 建议所选终结点与 IoT 中心位于同一区域。 |

一条消息可能与多个路由查询中的条件匹配，在这种情况下，IoT 中心会将该消息传递到与每个匹配查询关联的终结点。 IoT 中心还会自动删除重复的消息传递，因此如果消息与具有相同目标的多个查询匹配，仅将其写入该目标位置一次。

## <a name="endpoints-and-routing"></a>终结点和路由

IoT 中心具有默认的[内置终结点](iot-hub-devguide-messages-read-builtin.md)。 将订阅中的其他服务链接到中心可创建自定义终结点来路由消息。 IoT 中心目前支持将 Azure 存储容器、事件中心、服务总线队列和服务总线主题用作自定义终结点。

使用路由和自定义终结点时，如果消息不与任何查询匹配，则只将其传送到内置终结点。 若要将消息传递到内置终结点以及自定义终结点，请添加用于将消息发送到内置事件终结点的路由。

> [!NOTE]
> * IoT 中心仅支持将数据作为 blob 写入 Azure 存储容器。
> * 不支持将启用“会话”或“重复检测”选项的服务总线队列和主题用作自定义终结点。

有关在 IoT 中心创建自定义终结点的详细信息，请参阅 [IoT 中心终结点](iot-hub-devguide-endpoints.md)。

有关从自定义终结点进行读取的详细信息，请参阅：

* 从 [Azure 存储容器](../storage/blobs/storage-blobs-introduction.md)读取。

* 从[事件中心](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)读取。

* 从[服务总线队列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)读取。

* 从[服务总线主题](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)读取。

## <a name="next-steps"></a>后续步骤

* 有关 IoT 中心终结点的详细信息，请参阅 [IoT 中心终结点](iot-hub-devguide-endpoints.md)。

* 要详细了解用于定义路由查询的查询语言，请参阅[消息路由查询语法](iot-hub-devguide-routing-query-syntax.md)。

* 要了解如何使用路由查询和自定义终结点，请参阅[使用路由处理 IoT 中心设备到云的消息](tutorial-routing.md)教程。