---
title: 排查 Azure 专用链路连接问题
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
ms.openlocfilehash: 667fa1c85c63ffb87e49c4bf99112f57d0c85a72
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031915"
---
# <a name="troubleshoot-private-link-service-connectivity-problems"></a>排查专用链接服务连接问题

本指南提供验证和诊断专用链接服务设置的连接的分步指导。 

通过 azure 专用链接，可通过虚拟网络中的专用终结点访问 Azure PaaS 服务（例如，Azure 存储、Azure Cosmos DB 和 SQL 数据库）和 Azure 托管的客户/合作伙伴服务。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 你还可以在虚拟网络（VNet）中创建自己的专用链接服务，并将其专用于你的客户。 

可以启用在 Azure 标准负载均衡器后面运行的服务，以获取专用链接访问权限。 服务使用者可以在其虚拟网络中创建专用终结点，并将其映射到此服务以私下访问它。

以下是私有链接服务提供的连接方案
- 来自同一区域的虚拟网络 
- 突破对等互连虚拟网络
- 全局对等互连的虚拟网络
- 通过 VPN 或 Express Route 线路的本地客户

## <a name="deployment-troubleshooting"></a>部署故障排除

请查看有关在[专用链接服务上禁用网络策略](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)的信息，以解决无法从所选子网中为专用链接服务选择源 IP 地址的疑难解答情况。

请确保为从中选择源 IP 地址的子网禁用了设置**privateLinkServiceNetworkPolicies** 。

## <a name="diagnosing-connectivity-problems"></a>诊断连接问题

如果在设置专用链接时遇到连接问题，请执行下列步骤，以确保所有常用配置都符合预期。

1. 通过浏览资源查看专用链接服务配置 

    a）中转到**专用链接中心**

      ![专用链接中心](./media/private-link-tsg/private-link-center.png)

    b）从左侧导航窗格中选择 "**专用链接服务**"

      ![专用链接服务](./media/private-link-tsg/private-link-service.png)

    c）筛选并选择要诊断的专用链接服务

    d）查看专用终结点连接
     - 请确保要从中查找连接的专用终结点与**已批准**的连接状态一起列出。 
     - 如果**挂起**，请选择它并批准。 

       ![专用终结点连接](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 通过单击名称，导航到要从中进行连接的专用终结点。 请确保 "连接状态" 显示为 "**已批准**"。

       ![专用终结点连接概述](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 双方获得批准后，再次尝试连接。

    e）通过 "概述" 选项卡和 "属性" 选项卡中的**资源 ID**检查**别名** 
     - 请确保**别名/资源 id**与用于创建此服务的专用终结点的**别名/资源 id**匹配。 

       ![验证别名](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![验证资源 ID](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f）查看可见性（概述部分）信息
     - 确保你的订阅处于**可见性**范围内

       ![验证可见性](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g）查看负载均衡器（概述）信息
     - 可以通过单击 "负载均衡器" 链接导航到负载均衡器

       ![验证负载均衡器](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - 请确保负载均衡器设置按预期配置
       - 查看前端 IP 配置
       - 查看后端池
       - 查看负载均衡规则

       ![验证负载均衡器属性](./media/private-link-tsg/pls-ilb-properties.png)

     - 请确保负载均衡器正在按以上设置运行
       - 在负载均衡器后端池可用的任何子网中选择 VM
       - 尝试从上面的 VM 访问负载均衡器前端
       - 如果按负载均衡规则连接到后端池，则负载均衡器可以正常运行
       - 还可以通过 Azure Monitor 查看负载均衡器指标，以查看数据是否通过负载均衡器流动

2. 使用[**Azure Monitor**](https://docs.microsoft.com/azure/azure-monitor/overview)来查看数据的流动

    a）在专用链接服务资源中，选择 "**指标**"
     - 选择**bytes in**或**bytes out**
     - 查看数据在尝试连接到专用链接服务时流动。 预计延迟为大约10分钟。

       ![验证专用链接服务指标](./media/private-link-tsg/pls-metrics.png)

3. 如果问题仍未解决并且连接问题仍然存在，请与[Azure 支持](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)团队联系。 

## <a name="next-steps"></a>后续步骤

 * [创建专用链接服务（CLI）](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)

 * [专用终结点疑难解答指南](https://docs.microsoft.com/azure/private-link/private-endpoint-connectivity-troubleshooting)
