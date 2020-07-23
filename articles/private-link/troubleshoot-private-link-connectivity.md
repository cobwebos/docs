---
title: 排查 Azure 专用链接的连接问题
description: 有关诊断专用链接连接的分步指导
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 1e5253d617c87d5869cebc817da6d265ebfdfa7e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "77539461"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>排查 Azure 专用链接的连接问题

本文提供有关验证和诊断 Azure 专用链接连接设置的分步指导。

使用 Azure 专用链接，可以通过虚拟网络中的专用终结点访问 Azure 平台即服务 (PaaS) 服务（例如 Azure 存储、Azure Cosmos DB 和 Azure SQL 数据库），以及 Azure 托管的客户服务或合作伙伴服务。 虚拟网络和服务之间的流量通过 Microsoft 主干网络进行遍历，这将消除公共 internet 的公开。 你还可以在虚拟网络中创建自己的专用链接服务，并将其专门提供给自己的客户。

可以允许用户通过专用链接访问在 Azure 负载均衡器标准层后面运行的服务。 服务的使用者可在其虚拟网络中创建专用终结点，并将此终结点映射到此服务，然后以私密方式访问此服务。

下面是专用链接可用的连接方案：

- 同一区域中的虚拟网络
- 区域对等互连的虚拟网络
- 全球对等互连的虚拟网络
- 基于 VPN 或 Azure ExpressRoute 线路的客户本地连接

## <a name="deployment-troubleshooting"></a>部署故障排除

无法从所选子网中为专用链接服务选择源 IP 地址时，请查看[在专用链接服务中禁用网络策略](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)中的故障排除信息。

对于从中选择了源 IP 地址的子网，请确保禁用 privateLinkServiceNetworkPolicies 设置  。

## <a name="diagnose-connectivity-problems"></a>诊断连接问题

如果遇到专用链接设置的连接问题，请查看下面的步骤，确保所有常规配置都符合预期。

1. 通过浏览资源查看专用链接的配置。

    a. 转到“专用链接中心”  。

      ![专用链接中心](./media/private-link-tsg/private-link-center.png)

    b. 在左侧导航窗格中选择“专用链接服务”  。

      ![专用链接服务](./media/private-link-tsg/private-link-service.png)

    c. 筛选并选择要诊断的专用链接服务。

    d. 查看专用终结点连接。
     - 请确保要从中查找连接的专用终结点与**已批准**的连接状态一起列出。
     - 如果状态为 "**挂起**"，请选择它并批准它。

       ![专用终结点连接](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 通过选择名称，从连接到的专用终结点。 请确保连接状态显示为 "**已批准**"。

       ![专用终结点连接概述](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 两端都获得批准后，再次尝试连接。

    e. "**概述**" 选项卡上的 "**别名**" 和 "**属性**" 选项卡上的**资源 ID** 。
     - 请确保**别名**和**资源 id**信息与用于创建此服务的专用终结点的**别名**和**资源 id**相匹配。

       ![验证别名信息](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![验证资源 ID 信息](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. 查看 "**概览**" 选项卡上的**可见性**信息。
     - 请确保你的订阅处于**可见性**范围内。

       ![验证可见性信息](./media/private-link-tsg/pls-overview-pane-visibility.png)

    如， 查看 "**概览**" 选项卡上的**负载均衡器**信息。
     - 可以通过选择 "负载均衡器" 链接来前往负载均衡器。

       ![验证负载均衡器信息](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - 请确保负载均衡器设置按预期方式配置。
       - 查看**前端 IP 配置**。
       - 查看**后端池**。
       - 查看**负载均衡规则**。

       ![验证负载均衡器属性](./media/private-link-tsg/pls-ilb-properties.png)

     - 确保负载均衡器根据前面的设置工作。
       - 在除提供负载均衡器后端池的子网以外的任何其他子网中选择一个 VM。
       - 尝试从上述 VM 访问负载均衡器前端。
       - 如果根据负载均衡规则连接到了后端池，则表示负载均衡器正常运行。
       - 还可以通过 Azure Monitor 查看负载均衡器指标，以确定数据是否通过负载均衡器流动。

1. 使用 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 查看是否有数据在流动。

    a. 在专用链接服务资源中选择“指标”  。
     - 选择**Bytes In**或**bytes Out**。
     - 尝试连接到专用链接服务时，请查看数据是否流动。 预计延迟大约为 10 分钟。

       ![验证专用链接服务指标](./media/private-link-tsg/pls-metrics.png)

1. 如果问题仍未解决，并且连接问题仍然存在，请联系 [Azure 支持](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)团队。

## <a name="next-steps"></a>后续步骤

 * [创建专用链接服务 (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Azure 专用终结点故障排除指南](troubleshoot-private-endpoint-connectivity.md)
