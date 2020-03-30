---
title: 远程工作：远程工作的网络虚拟设备 （NVA） 注意事项 |Azure VPN 网关
description: 本文可帮助您了解在 COVID-19 大流行期间，在 Azure 中使用网络虚拟设备 （NVA） 时应考虑的事情。
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/21/2020
ms.author: scottnap
ms.openlocfilehash: 8a22e8f0bde2d13b4055566d96680fadc2db6e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337094"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>远程工作：远程工作的网络虚拟设备 （NVA） 注意事项

>[!NOTE]
>本文介绍如何利用网络虚拟设备、Azure、Microsoft 网络和 Azure 合作伙伴生态系统远程工作并缓解因 COVID-19 危机而面临的网络问题。
>

某些 Azure 客户利用 Azure 应用商店的第三方网络虚拟设备 （NVA） 为其在 COVID-19 流行期间在家工作的员工提供关键服务，例如点对点 VPN。 本文概述了在利用 Azure 中的 NVA 提供远程访问解决方案时需要考虑的一些高级指导。

## <a name="nva-performance-considerations"></a>NVA 性能注意事项

Azure 应用商店中的所有主要 NVA 供应商都应就 VM 大小和部署其解决方案时使用的实例数提出建议。  虽然几乎所有 NVA 供应商都允许您选择给定区域中可用的任何大小，但遵循有关 Azure VM 实例大小的供应商建议非常重要，因为这些建议是供应商已完成性能的 VM 大小在 Azure 中测试。  

### <a name="consider-the-following"></a>请考虑以下事项

- **容量和并发用户数**- 此数字对于点对点 VPN 用户尤其重要，因为每个连接的用户将创建一个加密（IPSec 或 SSL VPN）隧道。  
- **聚合吞吐量**- 您需要的聚合带宽量，以容纳提供远程访问所需的用户数量。
- **您需要的 VM 大小**- 应始终使用 NVA 供应商建议的 VM 大小。  对于点对点 VPN，如果您要具有大量并发用户连接，则应使用更大的 VM 大小，如[Dv2 和 DSv2 系列](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Dv2 和 Dsv2 系列")VM。 这些 VM 往往有更多的 vCPU，并可以处理更多的并发 VPN 会话。  除了拥有更多的虚拟内核外，Azure 中较大的 VM 大小比较小的 VM 大小具有更多的聚合带宽容量。
    > **重要提示：** 每个供应商以不同的方式利用资源。  如果不清楚应使用什么实例大小来容纳估计的用户负载，则应直接联系软件供应商并征求他们的建议。
- **实例数**- 如果您希望拥有大量用户和连接，则对扩展 NVA 实例大小可以实现什么有限制。  请考虑部署多个 VM 实例。
- **IPSec VPN 与 SSL VPN -** 一般来说，IPSec VPN 实现的性能优于 SSL VPN 实现。  
- **许可**- 确保您为 NVA 解决方案购买的软件许可证将涵盖您在 COVID-19 流行期间可能经历的突然增长。  许多 NVA 许可程序限制解决方案能够实现的连接或带宽数量。
- **加速网络**- 考虑支持加速网络的 NVA 解决方案。  使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 此高性能路径从数据路径绕过主机，减少延迟、抖动和 CPU 利用率，用于受支持的 VM 类型上要求最苛刻的网络工作负载。 大多数通用和计算优化的实例大小都支持加速网络，具有两个或多个 vCPU。

## <a name="monitoring-resources"></a>监视资源

每个 NVA 解决方案都有自己的工具和资源来监视其 NVA 的性能。  请咨询供应商文档，确保您了解性能限制，并可以检测 NVA 何时接近或达到容量。  除此之外，您还可以查看 Azure 监视器网络见解，并查看有关网络虚拟设备的基本性能信息，例如：

- CPU 使用率
- 网络传入
- 网络传出
- 入站流数
- 出站流数

## <a name="next-steps"></a>后续步骤

大多数主要的 NVA 合作伙伴都发布了有关 COVID-19 期间突然意外增长的扩展指南。 下面是一些指向合作伙伴资源的有用链接。

[梭子鱼在家工作，同时在 COVID-19 期间保护您的数据](https://www.barracuda.com/covid-19/work-from-home "在 COVID-19 期间启用在家工作，同时保护您的数据")

[用于 COVID-19 准备的思科 AnyConnect 实施和性能/扩展参考](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "用于 COVID-19 准备的思科 AnyConnect 实施和性能/扩展参考")

[Citrix COVID-19 响应支持中心](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19 响应支持中心")

[F5 指导解决远程工作人员急剧增加的问题](https://www.f5.com/business-continuity "F5 指导解决远程工作人员急剧增加的问题")

[Fortinet COVID-19 针对客户和合作伙伴的更新](https://www.fortinet.com/covid-19.html "COVID-19 针对客户和合作伙伴的更新")

[帕洛阿尔托网络COVID-19响应中心](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "帕洛阿尔托网络COVID-19响应中心")
