---
title: Azure IoT 中心与 Azure 事件中心的比较 | Microsoft Docs
description: 比较 IoT 中心与事件中心这两个 Azure服务，重点介绍功能差异和用例。 比较内容包括支持的协议、设备管理、监视和文件上传。
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 303a2bde0a1e0b25ca6eb145e7b0cd6c91fff351
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Azure IoT 中心与 Azure 事件中心的比较

Azure IoT 中心和 Azure 事件中心都是云服务，都可以引入大量的数据进行处理或存储，以便获取业务见解。 这两项服务类似的地方是，二者都支持事件和遥测数据的处理，延迟低且可靠性高。 不过，只有 IoT 中心在开发时设计了启用大规模物联网方案所需的特定功能。 

Azure IoT 中心是一个云网关，可以连接设备并收集获取业务见解和实现自动化所需的数据。 有了它，就可以轻松地将数据流式传输到云并对设备进行大规模的管理。 IoT 中心和其他数据引入服务的重要区别是，IoT 中心包含的功能可以丰富设备和后端系统之间的关系。 双向通信功能意味着，既可以从设备接收数据，也可以向设备发送消息，以便更新属性或调用某个操作。 设备级标识可以用来确保系统安全性。 分布式计算可以将云服务逻辑移到边缘设备。

[Azure 事件中心][Azure Event Hubs]是一项事件引入服务，可以处理和存储大量数据和遥测。 不管使用的是跨数据中心的方案还是数据中心内部的方案，事件中心主要用于大规模事件引入，而不提供 IoT 中心提供的特定于 IoT 的丰富功能。 因此，不建议对 IoT 解决方案使用事件中心。 

下表详述了在针对 IoT 功能对 IoT 中心的两个层与事件中心进行评估时，二者的比较结果。 有关 IoT 中心的标准层和基本层的详细信息，请参阅[如何选择合适的 IoT 中心层][lnk-scaling]。

| IoT 功能 | IoT 中心标准层 | IoT 中心基本层 | 事件中心 |
| --- | --- | --- | --- |
| 设备到云的消息传递 | ![勾选标记][1] | ![勾选标记][1] | ![勾选标记][1] |
| 协议：HTTPS、AMQP、基于 Websocket 的 AMQP | ![勾选标记][1] | ![勾选标记][1] | ![勾选标记][1] |
| 协议：MQTT、基于 Websocket 的 MQTT | ![勾选标记][1] | ![勾选标记][1] |  |
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