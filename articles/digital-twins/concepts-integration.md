---
title: 与其他服务集成
titleSuffix: Azure Digital Twins
description: 了解部署 Azure 数字孪生时的入口和出口要求。
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 118b02ab694d27dbe4e13cbfa1a617a56b052772
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043062"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>将 Azure 数字孪生与其他服务集成

Azure 数字孪生通常与其他服务一起使用，以多种方式创建使用数据的灵活连接的解决方案。

使用 [**事件路由**](concepts-route-events.md)，Azure 数字孪生可以接收来自上游服务（如 [IoT 中心](../iot-hub/about-iot-hub.md) 或 [逻辑应用](../logic-apps/logic-apps-overview.md)）的数据，这些数据用于传递遥测和通知。 

Azure 数字孪生还可以将数据路由到下游服务，如 [Azure Maps](../azure-maps/about-azure-maps.md) 和 [时序见解](../time-series-insights/overview-what-is-tsi.md)，用于存储、工作流集成、分析等。 

## <a name="data-ingress"></a>数据入口

Azure 数字孪生可以由任何服务（[IoT 中心](../iot-hub/about-iot-hub.md)、 [逻辑应用](../logic-apps/logic-apps-overview.md)、你自己的自定义服务等）中的数据和事件驱动。 这使你能够从环境中的物理设备收集遥测数据，并使用云中的 Azure 数字孪生图处理此数据。

Azure 数字孪生使您可以 "自带" IoT 中心来使用该服务，而不是在幕后使用内置的 IoT 中心。 你可以使用当前在生产环境中使用的现有 IoT 中心，或部署一个新的 IoT 中心用于此目的。 这使你可以完全访问 IoT 中心的所有设备管理功能。

若要将数据从任何源引入 Azure 数字孪生，请使用 [**azure 函数**](../azure-functions/functions-overview.md)。 了解有关此模式的详细信息，请参阅 [*如何：从 IoT 中心引入遥测*](how-to-ingest-iot-hub-data.md)，或在 Azure 数字孪生教程中亲自尝试 [*：连接端到端解决方案*](tutorial-end-to-end.md)。 

你还可以了解如何将 Azure 数字孪生连接到 [*如何：与逻辑应用集成*](how-to-integrate-logic-apps.md)的逻辑应用触发器。

## <a name="data-egress-services"></a>数据传出服务

Azure 数字孪生可以将数据发送到连接的 **终结点**。 支持的终结点可以是：
* [事件中心](../event-hubs/event-hubs-about.md)
* [事件网格](../event-grid/overview.md)
* [服务总线](../service-bus-messaging/service-bus-messaging-overview.md)

使用管理 Api 或 Azure 门户将端点附加到 Azure 数字孪生。 在 [*操作方法：管理终结点和路由*](how-to-manage-routes-apis-cli.md)中了解有关如何将终结点附加到 Azure 数字孪生的详细信息。

还有很多你可能想要最终定向数据的其他服务，例如 [Azure 存储](../storage/common/storage-introduction.md)、 [Azure Maps](../azure-maps/about-azure-maps.md)或 [时序见解](../time-series-insights/overview-what-is-tsi.md)。 若要将数据发送到此类服务，请将目标服务附加到终结点。

例如，如果你还在使用 Azure Maps 并且想要将位置与 Azure 数字孪生克隆 [图形](concepts-twins-graph.md)关联，则可以将 Azure Functions 与事件网格结合使用，以便在部署中的所有服务之间建立通信。 有关详细信息， [*请参阅如何：使用 Azure 数字孪生更新 Azure Maps 室内地图*](how-to-integrate-maps.md)

你还可以 [*了解如何将*](how-to-integrate-time-series-insights.md)数据以类似于时序见解的方式路由到时序见解。

## <a name="next-steps"></a>后续步骤

详细了解终结点并将事件路由到外部服务：
* [*概念：路由 Azure 数字孪生事件*](concepts-route-events.md)

请参阅如何设置用于从 IoT 中心引入数据的 Azure 数字孪生：
* [*如何：从 IoT 中心引入遥测数据*](how-to-ingest-iot-hub-data.md)