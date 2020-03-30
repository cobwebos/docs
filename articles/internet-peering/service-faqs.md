---
title: 对等服务 - 常见问题解答
titleSuffix: Azure
description: 对等服务 - 常见问题解答
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775467"
---
# <a name="peering-service---faqs"></a>对等服务 - 常见问题解答

您可以查看以下信息，了解一般问题。

**运营商能否使用其现有的直接与 Microsoft 对等互连来支持对等互连服务？**

是的，运营商可以利用其现有的 PNI 来支持对等服务。 对等服务 PNI 需要多元化来支持 HA。 如果现有的 PNI 已经具有多样性，则不需要新的基础结构。 如果现有的 PNI 需要多样性，则可以将其增强。

**运营商能否使用新的直接对等互连来支持对等互连服务？**

是的，这也是可能的。 微软将与运营商合作，创建新的直接对等互连，以支持对等服务。  

**为什么直接对等互连是支持对等服务的要求？**

对等互连服务背后的主要驱动因素之一是通过连接良好的 SP 与 Microsoft 在线服务提供连接。 PNI 始终位于 Gbps 范围内，因此是运营商和 Microsoft 之间高吞吐量连接的基本构建基块。

**支持对等互连服务的直接对等互连有哪些多样性要求？**

PNI 必须支持本地冗余和异地冗余。 本地冗余定义为特定对等站点中的两组不同的路径。 地理冗余要求开利在不同 Microsoft 边缘站点具有其他连接，以防主站点发生故障。 对于短暂的故障持续时间，运营商可以路由流量通过备份站点。

**运营商已经提供SLA和企业级互联网，这如何提供不同？**

一些运营商在其网络部分提供SLA和企业级互联网。 在对等服务中，微软将在微软网络部分提供SLA流量。 通过选择对等服务，客户将获得端到端的 SLA。 ISP 可以覆盖从其站点到 ISP 网络上的 Microsoft 边缘的 SLA。 微软全球网络从微软边缘到最终用户应用程序的SLA现在由微软覆盖。

**如果服务提供商已经使用 PNI 与 Microsoft 对等，则需要进行哪些类型的更改来支持对等互连服务？**

* 软件更改以标识对等服务用户及其流量。 可能需要路由策略更改才能通过对等互连服务连接在最接近的 Microsoft 边缘交换用户的流量。
* 确保连接具有本地冗余和异地冗余。
