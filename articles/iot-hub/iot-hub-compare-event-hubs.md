---
title: Azure IoT 中心与 Azure 事件中心的比较 | Microsoft Docs
description: 比较 IoT 中心与事件中心这两个 Azure服务，重点介绍功能差异和用例。 比较内容包括支持的协议、设备管理、监视和文件上传。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 7a589ba80b61ea5ef9ea1c941e9a0218a1653c99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60735503"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>将 IoT 设备连接到 Azure：IoT 中心和事件中心

Azure 提供了专为各种类型的连接和通信开发的服务来帮助你将数据连接到云以利用其功能。 Azure IoT 中心和 Azure 事件中心都是云服务，都可以引入大量的数据进行处理或存储，以便获取业务见解。 这两项服务的相似之处在于它们都支持以低延迟和高可靠性引入数据，但它们是为不同的用途设计的。 IoT 中心是为解决将 IoT 设备连接到 Azure 云时的独特要求而开发的，而事件中心是为大数据流式处理而设计的。 Microsoft 建议使用 Azure IoT 中心将 IoT 设备连接到 Azure

Azure IoT 中心是连接 IoT 设备的云网关，它会收集数据并促成业务见解和自动化。 此外，IoT 中心还提供了用来丰富设备与后端系统之间关系的功能。 双向通信功能表示既可以从设备接收数据，也可以将命令和策略发回到设备。 例如，使用云到设备的消息传送可以更新属性或调用设备管理操作。 借助云到设备的通信，还可以使用 Azure IoT Edge 将云中情报发送到边缘设备。 IoT 中心提供的唯一设备级标识有助于更好地保护 IoT 解决方案免受潜在攻击。 

[Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)是 Azure 的大数据流式处理服务。 它是为高吞吐量数据流式处理方案设计的，在这种场景中，客户每天可能会发送数十亿个请求。 事件中心使用分区的使用者模型来横向扩展流并且集成到 Azure 的大数据和分析服务中，这些服务包括 Databricks、流分析、ADLS 和 HDInsight。 此服务设计用来为大数据应用和解决方案提供支持，它提供事件中心捕获和自动扩张之类的功能。 此外，IoT 中心还将事件中心用于其遥测数据流路径，因此，IoT 解决方案也会受益于事件中心的强大功能。

概括而言，这两个解决方案都适合大规模的数据引入。 仅凭 IoT 中心就能提供丰富的 IoT 特定功能，使客户在将 IoT 设备连接到 Azure 云后可以最大程度地提高企业价值。  如果你的 IoT 之旅刚刚开始，请一开始就使用 IoT 中心来为数据引入方案提供支持，这将确保当业务和技术方面需要 IoT 功能时有完善的这类功能可供立即访问。

下表详述了在针对 IoT 功能对 IoT 中心的两个层与事件中心进行评估时，二者的比较结果。 有关 IoT 中心的标准层和基本层的详细信息，请参阅[如何选择合适的 IoT 中心层](iot-hub-scaling.md)。

| IoT 功能 | IoT 中心标准层 | IoT 中心基本层 | 事件中心 |
| --- | --- | --- | --- |
| 设备到云的消息传递 | ![勾选标记][checkmark] | ![勾选标记][checkmark] | ![勾选标记][checkmark] |
| 协议：HTTPS、AMQP、基于 WebSocket 的 AMQP | ![勾选标记][checkmark] | ![勾选标记][checkmark] | ![勾选标记][checkmark] |
| 协议：MQTT、基于 WebSocket 的 MQTT | ![勾选标记][checkmark] | ![勾选标记][checkmark] |  |
| 每设备标识 | ![勾选标记][checkmark] | ![勾选标记][checkmark] |  |
| 从设备上传文件 | ![勾选标记][checkmark] | ![勾选标记][checkmark] |  |
| 设备预配服务 | ![勾选标记][checkmark] | ![勾选标记][checkmark] |  |
| 云到设备的消息传递 | ![勾选标记][checkmark] |  |  |
| 设备孪生和设备管理 | ![勾选标记][checkmark] |  |  |
| 设备流（预览版） | ![勾选标记][checkmark] |  |  |
| IoT Edge | ![勾选标记][checkmark] |  |  |

即使唯一的用例是设备到云数据引入，我们也强烈建议使用 IoT 中心，因为它提供专用于 IoT 设备连接的服务。 

### <a name="next-steps"></a>后续步骤

若要进一步探索 IoT 中心的功能，请参阅 [IoT 中心开发人员指南](iot-hub-devguide.md)。

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
