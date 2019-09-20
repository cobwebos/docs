---
title: 先决条件 - ExpressRoute：Azure | Microsoft Docs
description: 本页提供了在订购 Azure ExpressRoute 线路之前需要满足的要求列表。 其中包括了一个清单。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4ef1a768356078c160b798c1a96412adda0330f6
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123297"
---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute 先决条件和清单
若要使用 ExpressRoute 连接到 Microsoft 云服务，需确认是否符合以下部分中所列的要求。

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure 帐户
* 使用中的有效 Microsoft Azure 帐户。 需有此帐户才能设置 ExpressRoute 线路。 ExpressRoute 线路是 Azure 订阅中的资源。 即使连接仅限于非 Azure Microsoft 云服务（如 Office 365），Azure 订阅也是一项要求。
* 激活 Office 365 订阅（如果要使用 Office 365 服务）。 有关详细信息，请参阅本文中的“Office 365 的具体要求”部分。

## <a name="connectivity-provider"></a>连接服务提供商

* 可使用 [ExpressRoute 连接合作伙伴](expressroute-locations.md#partners) 连接到 Microsoft 云。 有 [三种方法](expressroute-introduction.md)可以在本地网络与 Microsoft 之间建立连接。
* 即使提供商不是 ExpressRoute 连接合作伙伴，也可通过[云交换提供商](expressroute-locations.md#connectivity-through-exchange-providers)连接到 Microsoft 云。

## <a name="network-requirements"></a>网络要求
* **每个对等互连位置的冗余性**：Microsoft 要求在 Microsoft 的路由器和每个 ExpressRoute 线路上的对等互连路由器之间建立冗余的 BGP 会话（即使只有[一个到云交换的物理连接](expressroute-faqs.md#onep2plink)）。
* **灾难恢复的冗余**：Microsoft 强烈建议你在不同的对等互连位置设置至少两条 ExpressRoute 线路，避免单点故障。
* **路由**：你或提供商需设置和管理针对[路由域](expressroute-circuit-peerings.md)的 BGP 会话，具体取决于连接到 Microsoft 云的方式。 某些以太网连接服务提供商或云交换服务提供商可能会以增值服务的形式提供 BGP 管理。
* **NAT**：Microsoft 只能通过 Microsoft 对等互连接受公共 IP 地址。 如果使用的是本地网络中的专用 IP 地址，你或提供商需[使用 NAT](expressroute-nat.md)将专用 IP 地址转换为公共 IP 地址。
* **QoS**：Skype for Business 具有各种服务（例如语音、视频、文本），这些服务所要求的 QoS 处理方式各有差异。 你和提供商应遵循 [QoS 要求](expressroute-qos.md)。
* **网络安全**：通过 ExpressRoute 连接到 Microsoft 云时，应考虑[网络安全](../best-practices-network-security.md)。

## <a name="office-365"></a>Office 365
如果打算在 ExpressRoute 上启用 Office 365，请参阅以下文档详细了解 Office 365 方面的要求。

* [Overview of ExpressRoute for Office 365（适用于 Office 365 的 ExpressRoute 概述）](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Routing with ExpressRoute for Office 365](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)（通过适用于 Office 365 的 ExpressRoute 进行路由）
* [使用 ExpressRoute 实现高可用性和故障转移](https://aka.ms/erhighavailability)
* [Office 365 URL 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [针对 Office 365 的网络规划和性能优化](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Network bandwidth calculators and tools](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)（网络带宽计算器和工具）
* [Office 365 与本地环境的集成](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Office 365 中的 ExpressRoute 高级培训视频](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>后续步骤
* 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
* 查找 ExpressRoute 连接服务提供商。 请参阅 [ExpressRoute 合作伙伴和对等位置](expressroute-locations.md)。
* 请参阅[路由](expressroute-routing.md)、[NAT](expressroute-nat.md) 和 [QoS](expressroute-qos.md) 的要求。
* 配置 ExpressRoute 连接。
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md)
  * [配置路由](expressroute-howto-routing-arm.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)
