---
title: 对等互连服务-常见问题
titleSuffix: Azure
description: 对等互连服务-常见问题
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775467"
---
# <a name="peering-service---faqs"></a>对等互连服务-常见问题

对于一般问题，你可以查看以下信息。

**运营商是否可以使用与 Microsoft 的现有直接对等互连来支持对等互连服务？**

是的，运营商可以利用其现有的 PNI 支持对等互连服务。 对等互连服务 PNI 需要多样性来支持 HA。 如果现有 PNI 已有多样性，则不需要新的基础结构。 如果现有 PNI 需要多样性，则可以对其进行扩充。

**运营商是否可以使用与 Microsoft 的新直接对等互连来支持对等互连服务？**

是的，这也是可能的。 Microsoft 将与运营商合作创建新的直接对等互连，以支持对等互连服务。  

**为什么直接对等互连要求支持对等互连服务？**

对等互连服务背后的主要驱动因素之一是通过连接良好的 SP 提供与 Microsoft 联机服务的连接。 PNI 始终在 Gbps 范围内，因此是运营商和 Microsoft 之间的高吞吐量连接的基本构建基块。

**对直接对等互连的支持对等互连服务的多样性要求是什么？**

PNI 必须支持本地冗余和异地冗余。 本地冗余定义为特定对等站点中的两个不同的路径集。 异地冗余要求在主站点出现故障的情况下，在其他 Microsoft 边缘站点上运营商具有额外的连接。 对于短的故障持续时间，运营商可以通过备份站点路由流量。

**该运营商已提供 SLA 和企业级 Internet，该产品的不同之处是什么？**

某些运营商在网络部分提供 SLA 和企业级 Internet。 在对等互连服务中，Microsoft 将在网络的 Microsoft 部分提供 SLA 提供流量。 通过选择 "对等互连服务"，客户将获得端到端 SLA。 Isp 可能会涵盖向 ISP 网络上的 Microsoft edge 站点的 SLA。 Microsoft 向最终用户应用程序的 Microsoft 全球网络中的 SLA 现在已由 Microsoft 涵盖。

**如果服务提供商已使用 PNI 与 Microsoft 建立了对等互连，则需要哪种类型的更改来支持对等互连服务？**

* 用于标识对等服务用户及其流量的软件更改。 可能需要通过对等互连服务连接在最近的 Microsoft edge 交换用户流量的路由策略更改。
* 确保连接具有本地冗余和异地冗余。
