---
title: Azure IoT 中心与 Azure 事件中心的比较 | Microsoft Docs
description: 比较 IoT 中心与事件中心这两个 Azure服务，重点介绍功能差异和用例。 比较内容包括支持的协议、设备管理、监视和文件上传。
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 9ad95071de07777e38533ecec9e8558841d8b1ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633955"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>将 IoT 设备连接到 Azure：IoT 中心和事件中心

Azure 提供了专为各种类型的连接和通信开发的服务来帮助你将数据连接到云以利用其功能。 Azure IoT 中心和 Azure 事件中心都是云服务，都可以引入大量的数据进行处理或存储，以便获取业务见解。 这两项服务的相似之处在于它们都支持以低延迟和高可靠性引入数据，但它们是为不同的用途设计的。 IoT 中心是为解决将 IoT 设备大规模连接到 Azure 云时的独特要求而专门开发的，而事件中心是为大数据流式处理而设计的。 这就是 Microsoft 建议使用 Azure IoT 中心来将 IoT 设备连接到 Azure 的原因。

Azure IoT 中心是一个云网关，它连接 IoT 设备来收集数据以获得业务见解并实现自动化。 此外，IoT 中心还提供了用来丰富设备与后端系统之间关系的功能。 双向通信功能意味着，既可以从设备接收数据，又可以向设备发送命令和策略来执行操作，例如，更新属性或调用设备管理操作。  此云到设备连接还为通过 Azure IoT Edge 向边缘设备提供云智能这一重要功能提供支持。 IoT 中心提供的唯一设备级标识有助于更好地保护 IoT 解决方案免受潜在攻击。 

[Azure 事件中心][Azure Event Hubs]是 Azure 的大数据流式处理服务。 它是为高吞吐量数据流式处理方案设计的，在这种场景中，客户每天可能会发送数十亿个请求。 事件中心使用分区的使用者模型来横向扩展流并且集成到 Azure 的大数据和分析服务中，这些服务包括 Databricks、流分析、ADLS 和 HDInsight。 此服务设计用来为大数据应用和解决方案提供支持，它提供事件中心捕获和自动扩张之类的功能。 此外，IoT 中心还将事件中心用于其遥测数据流路径，因此，IoT 解决方案还会受益于事件中心的强大功能。

总而言之，虽然这两个解决方案都是为大规模数据引入而设计的，但是只有 IoT 中心提供了丰富的 IoT 特定功能，这些功能设计用来最大化将 IoT 设备连接到 Azure 云的业务价值。  如果你的 IoT 之旅刚刚开始，请一开始就使用 IoT 中心来为数据引入方案提供支持，这将确保当业务和技术方面需要 IoT 功能时有完善的这类功能可供立即访问。

下表详述了在针对 IoT 功能对 IoT 中心的两个层与事件中心进行评估时，二者的比较结果。 有关 IoT 中心的标准层和基本层的详细信息，请参阅[如何选择合适的 IoT 中心层][lnk-scaling]。

| IoT 功能 | IoT 中心标准层 | IoT 中心基本层 | 事件中心 |
| --- | --- | --- | --- |
| 设备到云的消息传递 | ![勾选标记][1] | ![勾选标记][1] | ![勾选标记][1] |
| 协议：HTTPS、AMQP、基于 WebSocket 的 AMQP | ![勾选标记][1] | ![勾选标记][1] | ![勾选标记][1] |
| 协议：MQTT、基于 WebSocket 的 MQTT | ![勾选标记][1] | ![勾选标记][1] |  |
| 每设备标识 | ![勾选标记][1] | ![勾选标记][1] |  |
| 从设备上传文件 | ![勾选标记][1] | ![勾选标记][1] |  |
| 设备预配服务 | ![勾选标记][1] | ![勾选标记][1] |  |
| 云到设备的消息传递 | ![勾选标记][1] |  |  |
| 设备孪生和设备管理 | ![勾选标记][1] |  |  |
| IoT Edge | ![勾选标记][1] |  |  |

即使唯一的用例是设备到云数据引入，我们也强烈建议使用 IoT 中心，因为它提供专用于 IoT 设备连接的服务。 

### <a name="next-steps"></a>后续步骤

若要进一步探索 IoT 中心的功能，请参阅 [IoT 中心开发人员指南][lnk-devguide]


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png
