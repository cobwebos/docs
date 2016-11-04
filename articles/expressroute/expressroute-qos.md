---
title: ExpressRoute 的 QoS 要求 | Microsoft Docs
description: 本页提供有关为 ExpressRoute 线路配置和管理 QoS 的详细要求。
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''

ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2016
ms.author: cherylmc

---
# ExpressRoute QoS 要求
Skype for Business 具有各种工作负荷，它们要求的 QoS 处理方式各有差异。如果你打算通过 ExpressRoute 使用语音服务，应遵守以下所述要求。

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> QoS 要求仅适用于 Microsoft 对等互连。Azure 公共对等互连和 Azure 专用对等互连上接收自网络流量中的 DSCP 值将重置为 0。
> 
> 

下表提供了 Skype for Business 使用的 DSCP 标记列表。有关详细信息，请参阅[管理 Skype for Business 的 QoS](https://technet.microsoft.com/library/gg405409.aspx)。

| **流量类** | **处理方式（DSCP 标记）** | **Skype for Business 工作负荷** |
| --- | --- | --- |
| **语音** |EF (46) |Skype/Lync 语音 |
| **交互式** |AF41 (34) |视频 |
| AF21 (18) |应用共享 | |
| CS3 (24) |SIP 信号 | |
| **默认** |AF11 (10) |文件传输 |
| CS0 (0) |任何其他项目 | |

* 你应该将工作负荷分类，并标记正确的 DSCP 值。遵循[此处](https://technet.microsoft.com/library/gg405409.aspx)提供的指导，了解如何在网络中设置 DSCP 标记。
* 应在网络中配置并支持多个 QoS 队列。语音必须是独立的类，并可接收 RFC 3246 中指定的 EF 处理方式。
* 你可以确定每个流量类的队列机制、阻塞检测策略和带宽分配。但是，必须预留 Skype for Business 工作负荷的 DSCP 标记。如果使用上面未列出的 DSCP 标记（例如 AF31 (26)），则必须先将此 DSCP 值重写为 0，然后再将数据包发送到 Microsoft。Microsoft 只发送使用上表所示 DSCP 值标记的数据包。

## 后续步骤
* 请参阅[路由](expressroute-routing.md)和 [NAT](expressroute-nat.md) 的要求。
* 请参阅以下链接来配置你的 ExpressRoute 连接。
  
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md)
  * [配置路由](expressroute-howto-routing-classic.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0921_2016-->