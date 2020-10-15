---
title: 用于 IoT 的 Defender 常见问题
description: 查找有关 Azure Defender 用于 IoT 功能和服务的常见问题的解答。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: 5e7eabd44ea8c56fbb102f9e48812745a31de62a
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089190"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Azure Defender for IoT 常见问题

本文提供了常见问题的列表以及有关用于 IoT 的 Defender 的答案。

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>什么是 Azure 独特的 IoT 安全性价值主张？

Defender for IoT 使企业能够将其现有的网络安全视图扩展到整个 IoT 解决方案。 Azure 提供了业务解决方案的端到端视图，使你能够根据企业安全状况和收集的数据来进行业务相关操作和决策。 使用 Azure IoT、Azure IoT Edge 和 Azure 安全中心的组合安全性使你可以创建具有所需安全性的所需解决方案。

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>我们的组织使用专有的非标准工业协议。 是否支持它们？ 

Azure Defender for IoT 提供全面的协议支持。 除嵌入协议支持外，还可以保护运行专用和自定义协议的 IoT 和设备，或与任何标准不相同的协议。 使用范围开放式开发环境 (O) ) SDK，开发人员可以创建基于定义的协议解码网络流量的 dissector 插件。 流量由服务进行分析，以提供完整的监视、警报和报告功能。 使用地平线来：
- 扩展可见性和控制，无需升级到新版本。
- 通过在现场上开发作为外部插件来保护专有信息。 
- 本地化警报、事件和协议参数的文本。

这是将协议作为插件开发的唯一解决方案，不需要专门的开发人员团队或版本来支持新的协议。 开发人员、合作伙伴和客户可以使用范围安全地开发协议，并分享见解和知识。 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>我是否必须从 Microsoft 合作伙伴购买硬件设备？
Azure Defender for IoT 传感器运行于 [硬件规范指南](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)中所述的特定硬件规格上，客户可以从 Microsoft 合作伙伴购买经过认证的硬件，或者使用提供的材料 (BOM) 并自行购买。 

经过认证的硬件已在实验室中测试，以保证驱动程序稳定性、数据包丢弃和网络大小。


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>法规不允许我们将系统连接到 Internet。 我们仍然可以使用适用于 IoT 的 Defender 吗？

可以！ Azure Defender for IoT 平台本地解决方案部署为物理或虚拟传感器设备，该设备通过 SPAN、RSPAN 或点击) 被动引入网络 (流量，以便分析、发现和连续监视和 IoT 网络。 对于大型企业，多个传感器可以将其数据聚合到本地管理控制台。

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>应该将监视端口连接到网络中的哪个位置？

Azure Defender for IoT 传感器连接到 SPAN 端口或网络分流点，并立即开始通过被动 (无代理) 监视收集 ICS 网络流量。 它不会对网络中的网络产生任何影响，因为它不会放置在数据路径中，也不会主动扫描 OT 设备。

例如：
- 物理)  (虚拟的单个设备可以位于 "车间" DMZ 层中，并将所有商店楼层单元流量路由到此层。
- 另外，还可以在每个商店楼层单元中找到小型微型传感器，并将其放置在商店楼层外围层中。 其他设备 (虚拟或物理) 可以在 SCADA、Historian 或 MES) 的车间 (DMZ 层监视流量。

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>IoT 如何与竞赛进行比较？

其他解决方案提供了一组允许客户创建其自己的解决方案的功能，而 IoT 用于 IoT 提供了一个独特的端到端 IoT 安全解决方案，该解决方案可跨所有相关 Azure 资源的安全性提供丰富的视图。 Azure 可实现快速部署并与 IoT 中心模块孪生完整集成，以便与现有设备管理工具轻松集成。


## <a name="do-i-have-to-be-an-azure-iot-customer"></a>是否必须成为 Azure IoT 客户？

是的。 对于云连接的部署，Azure Defender 用于 IoT 依赖于 Azure IoT 连接和基础结构。
## <a name="can-i-create-my-own-alerts"></a>我是否可以创建自己的警报？

是的。 可以对预先确定的行为集（例如 IP 地址和开放端口）设置自定义警报。 请参阅[创建自定义警报](quickstart-create-custom-alerts.md)以详细了解自定义警报以及如何创建它们。

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>可以在何处查看日志？ 是否可以自定义日志？

- 使用连接的 Log Analytics 工作区查看警报和建议。 在该工作区中配置存储大小和持续时间。

- 安全代理中的原始数据也可以存储在 Log Analytics 帐户中。 更改此选项的配置之前，请考虑大小、持续时间、存储要求和关联成本。



## <a name="what-happens-when-the-internet-connection-stops-working"></a>Internet 连接停止工作时会发生什么情况？

只要设备正在运行，传感器和代理就会继续运行并存储数据。 数据根据大小配置存储在安全消息缓存中。 当设备重新连接时，安全消息会恢复发送。





## <a name="next-steps"></a>后续步骤

若要详细了解如何开始将 Defender 用于 IoT，请参阅以下文章：

- 阅读用于 IoT 的 Defender [概述](overview.md)
- 验证[服务先决条件](service-prerequisites.md)
- 了解有关如何[入门](getting-started.md)的详细信息
- 了解 [用于 IoT 的 Defender 安全警报](concept-security-alerts.md)
