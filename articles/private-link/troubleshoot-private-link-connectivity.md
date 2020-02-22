---
title: 排查 Azure 专用链接的连接问题
description: 诊断专用链接连接的循序渐进指南
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
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539461"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>排查 Azure 专用链接的连接问题

本文提供验证和诊断 Azure 专用链接设置的连接的分步指导。

使用 Azure 专用链接，可以通过虚拟网络中的专用终结点访问 Azure 平台即服务（PaaS）服务，例如 Azure 存储、Azure Cosmos DB 和 Azure SQL 数据库，以及 Azure 托管的客户或合作伙伴服务。 虚拟网络和服务之间的流量通过 Microsoft 主干网络进行遍历，这将消除公共 internet 的公开。 你还可以在虚拟网络中创建自己的专用链接服务，并将其专门提供给客户。

可以启用在 Azure 负载均衡器的标准层后运行的服务，以实现专用链接访问。 服务使用者可以在其虚拟网络中创建专用终结点，并将其映射到此服务以私下访问它。

以下是专用链接提供的连接方案：

- 来自同一区域的虚拟网络
- 突破对等互连虚拟网络
- 全局对等互连的虚拟网络
- 通过 VPN 或 Azure ExpressRoute 线路的本地客户

## <a name="deployment-troubleshooting"></a>部署故障排除

请查看有关在[专用链接服务上禁用网络策略](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)的信息，以解决无法从所选子网中为专用链接服务选择源 IP 地址的疑难解答情况。

请确保为从中选择源 IP 地址的子网禁用了设置**privateLinkServiceNetworkPolicies** 。

## <a name="diagnose-connectivity-problems"></a>诊断连接问题

如果你的专用链接设置出现连接问题，请查看以下步骤以确保所有常用配置都符合预期。

1. 通过浏览资源来查看专用链接配置。

    a. 中转到**专用链接中心**。

      ![专用链接中心](./media/private-link-tsg/private-link-center.png)

    b. 在左侧窗格中，选择 "**专用链接服务**"。

      ![专用链接服务](./media/private-link-tsg/private-link-service.png)

    c. 筛选并选择要诊断的专用链接服务。

    d. 查看专用终结点连接。
     - 请确保要从中查找连接的专用终结点与**已批准**的连接状态一起列出。
     - 如果状态为 "**挂起**"，请选择它并批准它。

       ![专用终结点连接](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 通过选择名称，从连接到的专用终结点。 请确保连接状态显示为 "**已批准**"。

       ![专用终结点连接概述](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 在双方都获得批准后，再次尝试连接。

    e. "**概述**" 选项卡上的 "**别名**" 和 "**属性**" 选项卡上的**资源 ID** 。
     - 请确保**别名**和**资源 id**信息与用于创建此服务的专用终结点的**别名**和**资源 id**相匹配。

       ![验证别名信息](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![验证资源 ID 信息](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. 查看 "**概览**" 选项卡上的**可见性**信息。
     - 请确保你的订阅处于**可见性**范围内。

       ![验证可见性信息](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. 查看 "**概览**" 选项卡上的**负载均衡器**信息。
     - 可以通过选择 "负载均衡器" 链接来前往负载均衡器。

       ![验证负载均衡器信息](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - 请确保负载均衡器设置按预期方式配置。
       - 查看**前端 IP 配置**。
       - 查看**后端池**。
       - 查看**负载均衡规则**。

       ![验证负载均衡器属性](./media/private-link-tsg/pls-ilb-properties.png)

     - 请确保负载均衡器按以前的设置工作。
       - 选择负载均衡器后端池可用的任何子网中的 VM。
       - 尝试从以前的 VM 访问负载均衡器前端。
       - 如果根据负载均衡规则，连接使其成为后端池，则负载均衡器可操作。
       - 还可以通过 Azure Monitor 查看负载均衡器指标，以查看数据是否通过负载均衡器流动。

1. 使用[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)查看数据是否正在流动。

    a. 在 "专用链接服务资源" 中，选择 "**指标**"。
     - 选择**Bytes In**或**bytes Out**。
     - 尝试连接到专用链接服务时，请查看数据是否流动。 预计延迟约为10分钟。

       ![验证专用链接服务指标](./media/private-link-tsg/pls-metrics.png)

1. 如果问题仍未解决并且连接问题仍然存在，请与[Azure 支持](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)团队联系。

## <a name="next-steps"></a>后续步骤

 * [创建专用链接服务（CLI）](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Azure 专用终结点疑难解答指南](troubleshoot-private-endpoint-connectivity.md)
