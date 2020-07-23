---
title: 远程工作：用于远程工作的网络虚拟设备 (NVA) 的注意事项 | Azure VPN 网关
description: 本文帮助你了解在流行全球的 COVID-19（新冠病毒）疫情期间，在 Azure 中使用网络虚拟设备 (NVA) 时应该注意的一些事项。
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/21/2020
ms.author: scottnap
ms.openlocfilehash: 8a22e8f0bde2d13b4055566d96680fadc2db6e11
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80337094"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>远程工作：用于远程工作的网络虚拟设备 (NVA) 的注意事项

>[!NOTE]
>本文介绍如何利用网络虚拟设备、Azure、Microsoft 网络和 Azure 合作伙伴生态系统远程进行工作，以及如何缓解因 COVID-19（新冠病毒）危机而面临的网络问题。
>

在 COVID-19 疫情期间，某些 Azure 客户利用 Azure 市场中的第三方网络虚拟设备 (NVA) 为其在家办公的员工提供点到站点 VPN 等关键服务。 本文从较高层面概述了有关在 Azure 中利用 NVA 提供远程访问解决方案时需要注意的一些事项的指导。

## <a name="nva-performance-considerations"></a>NVA 性能注意事项

Azure 市场中的所有主流 NVA 供应商应已提供有关在部署其解决方案时要使用的 VM 大小和实例数目的建议。  尽管几乎所有 NVA 供应商都允许选择可在给定区域中使用的任何大小，但遵循供应商在 Azure VM 实例大小方面的建议非常重要，因为这些 VM 大小建议是供应商在 Azure 中执行性能测试后给出的。  

### <a name="consider-the-following"></a>注意以下事项

- **容量和并发用户数** - 此数字对于点到站点 VPN 用户尤为重要，因为每个连接的用户将创建一个加密的（IPSec 或 SSL VPN）隧道。  
- **聚合吞吐量** - 需要多大的聚合带宽才能为所需数目的用户提供远程访问。
- **所需的 VM 大小** - 始终应使用 NVA 供应商建议的 VM 大小。  对于点到站点 VPN，如果你有大量的并发用户连接，则应使用较大的 VM 大小，例如 [Dv2 和 DSv2 系列](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Dv2 和 Dsv2 系列") VM。 这些 VM 往往配备了更多的 vCPU，可以处理更多的并发 VPN 会话。  除了提供更多的虚拟核心以外，Azure 中较大 VM 提供的聚合带宽容量也要高于较小的 VM。
    > **重要提示：** 每家供应商以不同的方式利用资源。  如果你不确定要使用哪种实例大小来适应预估的用户负载，应直接联系软件供应商，请他们提供建议。
- **实例数** - 如果预期会出现大量的用户和连接，纵向扩展 NVA 实例大小可实现的效果存在限制。  请考虑部署多个 VM 实例。
- **IPSec VPN 与 SSL VPN** - 一般情况下，IPSec VPN 实现的性能要优于 SSL VPN 实现。  
- **许可** - 确保为 NVA 解决方案购买的软件许可证涵盖了 COVID-19 疫情期间可能会出现的流量突增。  许多 NVA 许可计划会限制解决方案支持的连接数或带宽。
- **加速网络** - 考虑使用一个支持加速网络的 NVA 解决方案。  使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，降低延迟、抖动，以及受支持 VM 类型上的最苛刻网络工作负荷的 CPU 利用率。 大多数常规用途实例以及具有两个或更多 vCPU 的计算优化实例都支持加速网络。

## <a name="monitoring-resources"></a>监视资源

每种 NVA 解决方案提供自身的工具和资源用于监视其 NVA 的性能。  请查阅供应商文档，确保了解性能限制，并在 NVA 接近或达到容量限制时能够检测到这种情况。  除此之外，还可以在 Azure Monitor 网络见解中查看有关网络虚拟设备的基本性能信息，例如：

- CPU 使用率
- 网络传入
- 网络传出
- 入站流数
- 出站流数

## <a name="next-steps"></a>后续步骤

大多数主流 NVA 合作伙伴已经围绕在 COVID-19 疫情期间出现流量意外突增时如何进行扩展发布了指导。 下面是一些一些有用的合作伙伴资源链接。

[Barracuda Enable Work from home while securing your data during COVID-19](https://www.barracuda.com/covid-19/work-from-home "在 COVID-19 疫情期间实现在家办公和数据保护")（在 COVID-19 疫情期间 Barracuda 可以实现在家办公和数据保护）

[Cisco AnyConnect Implementation and Performance/Scaling Reference for COVID-19 Preparation](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "应对 COVID-19 的 Cisco AnyConnect 实施方案和性能/缩放参考")（应对 COVID-19 的 Cisco AnyConnect 实施方案和性能/缩放参考）

[Citrix COVID-19 Response Support Center](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19 响应支持中心")（Citrix COVID-19 响应支持中心）

[F5 Guidance to Address the Dramatic Increase in Remote Workers](https://www.f5.com/business-continuity "F5 针对远程工作者大幅增加提供的指导")（F5 针对远程工作者大幅增加提供的指导）

[Fortinet COVID-19 Updates for Customers and Partners](https://www.fortinet.com/covid-19.html "面向客户和合作伙伴的 COVID-19 最新消息")（面向客户和合作伙伴的 Fortinet COVID-19 最新消息）

[Palo Alto Networks COVID-19 Response Center](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks COVID-19 响应中心")（Palo Alto Networks COVID-19 响应中心）
