---
title: "Azure IoT 中心的高可用性和灾难恢复 | Microsoft Docs"
description: "介绍了Azure 和 IoT 中心功能，这些功能有助于构建带灾难恢复功能的 Azure IoT 高可用性解决方案。"
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: ae320e58-aa20-45b9-abdc-fa4faae8e6dd
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: elioda
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: b3ca2ed90dd14350d3962a558aaac41f2e007bbd
ms.contentlocale: zh-cn
ms.lasthandoff: 08/30/2017

---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT 中心高可用性和灾难恢复
作为一项 Azure 服务，IoT 中心在 Azure 区域级别使用冗余来提供高可用性 (HA)，而解决方案不需要执行任何额外的工作。 此外，Microsoft Azure 平台还包含了相关功能来帮助你构建提供灾难恢复 (DR) 功能或跨区域可用性的解决方案。 如果希望为设备或用户提供全局性的跨区域高可用性，请设计并编制解决方案来利用这些 Azure DR 功能。 [Azure 业务持续性技术指南](../resiliency/resiliency-technical-guidance.md)一文描述了针对业务连续性和 DR 的 Azure 内置功能。 [Azure 应用程序的灾难恢复和高可用性][Disaster recovery and high availability for Azure applications]一文针对 Azure 应用程序的 HA 和 DR 实现策略提供了体系结构指导。

## <a name="azure-iot-hub-dr"></a>Azure IoT 中心 DR
除了区域内部的 HA，IoT 中心还实施了无需用户干预的灾难恢复故障转移机制。 IoT 中心 DR 自行启动，其恢复时间目标 (RTO) 为 2 到 26 小时，恢复点目标 (RPO) 如下所示。

| 功能 | RPO |
| --- | --- |
| 注册表和通信操作的服务可用性 |可能丢失 CName  |
| 标识注册表中的标识数据 |丢失 0-5 分钟的数据 |
| 设备到云的消息 |所有未读的消息都丢失 |
| 操作监视消息 |所有未读的消息都丢失 |
| 云到设备的消息 |丢失 0-5 分钟的数据 |
| 云到设备的反馈队列 |所有未读的消息都丢失 |

## <a name="regional-failover-with-iot-hub"></a>IoT 中心区域故障转移
IoT 解决方案中对部署拓扑的完整处理不在本文的介绍范围内。 本文将讨论用于提供高可用性和灾难恢复功能的*区域性故障转移*部署模型。

在区域性故障转移模型中，解决方案后端主要在一个数据中心位置运行，辅助 IoT 中心和后端部署在另一个数据中心位置。 如果主数据中心内的 IoT 中心遭遇服务中断，或者从设备到主数据中心的网络连接中断，那么每当无法到达主网关时，设备都将使用辅助服务终结点。 使用跨区域故障转移功能可改善解决方案的可用性，使其优于单个区域的高可用性。

概而言之，为了实现 IoT 中心的区域故障转移模型，需要做好以下准备：

* **辅助 IoT 中心和设备路由逻辑**：如果主要区域的服务中断，设备必须开始连接到次要区域。 由于大多数服务状态感知的性质，解决方案管理员通常触发区域间的故障转移过程。 要使新终结点与设备通信，同时保留过程控制权，最佳方式是让它们定期在*监护*服务中检查是否存在当前活动的终结点。 该监护服务可以是 Web 应用程序，可使用 DNS 重定向技术将它复制并使其可访问（例如，使用 [Azure 流量管理器][Azure Traffic Manager]）。
* **标识注册表复制** - 为了可用，辅助 IoT 中心必须包含能够连接到解决方案的所有设备标识。 解决方案应该保留设备标识的异地复制备份，并在切换设备的活动终结点之前将其上传到辅助 IoT 中心。 IoT 中心的设备标识导出功能在此背景下非常有用。 有关详细信息，请参阅 [IoT 中心开发人员指南 - 标识注册表][IoT Hub developer guide - identity registry]。
* **合并逻辑** - 当主要区域再次可供使用时，所有在辅助站点中创建的状态和数据都必须迁移回到主要区域。 此状态和数据主要与设备标识和应用程序元数据相关，必须与主要 IoT 中心合并，也可能要与主要区域中的其他应用程序特定存储合并。 为简化此步骤，应当使用幂等操作。 幂等操作可以将副作用降到最低，包括来自最终一致的事件分布的副作用，以及来自事件的重复项目或失序传送的副作用。 此外，应用程序逻辑应该设计为能够容许潜在的不一致或“稍微”过期的状态。 之所以发生此情况是因为系统需要额外的时间来根据恢复点目标 (RPO)“修复”自身。

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure IoT 中心的详细信息，请参阅以下链接：

* [IoT 中心入门（教程）][lnk-get-started]
* [什么是 Azure IoT 中心？][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md

