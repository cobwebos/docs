---
title: 排查 Azure 私有终结点连接问题
description: 诊断私有终结点连接的循序渐进指南
services: private-endpoint
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
ms.openlocfilehash: df4ec6ddbba029eb29d2440717697968f8c79302
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191064"
---
# <a name="troubleshoot-private-endpoint-connectivity-problems"></a>排查专用终结点连接问题

本指南提供验证和诊断专用终结点连接设置的分步指导。 

Azure 专用终结点是一个网络接口，该接口将你私下并安全地连接到专用链接服务。 此解决方案通过提供从虚拟网络到 Azure 服务资源的专用连接，帮助保护 Azure 中的工作负荷。 这会有效地将这些服务引入到你的虚拟网络。 

以下是专用终结点可用的连接方案 
- 来自同一区域的虚拟网络 
- 突破对等互连虚拟网络
- 全局对等互连的虚拟网络
- 通过 VPN 或 Express Route 线路的本地客户

## <a name="diagnosing-connectivity-problems"></a>诊断连接问题 
继续执行下面列出的步骤，以确保所有常见配置都符合预期，以解决专用终结点设置的连接问题。

1. 通过浏览资源查看专用终结点配置 

    a）中转到**专用链接中心**

      ![专用链接中心](./media/private-endpoint-tsg/private-link-center.png)

    b）从左侧导航窗格中选择专用终结点
    
      ![专用终结点](./media/private-endpoint-tsg/private-endpoints.png)

    c）筛选并选择要诊断的专用终结点

    d）查看虚拟网络和 DNS 信息
    
     - 验证连接状态是否已**批准**
     - 请确保 VM 已连接到承载专用终结点的 VNet
     - 已分配 FQDN 信息（复制）和专用 IP 地址
    
       ![VNet 和 DNS 配置](./media/private-endpoint-tsg/vnet-dns-configuration.png)    
    
2. 使用[**Azure Monitor**](https://docs.microsoft.com/azure/azure-monitor/overview)来查看数据的流动

    a）在专用终结点资源上选择 "**监视器**"
     - 如果尝试连接到专用终结点，请选择 "数据" 或 "数据输出" 并查看数据是否流动。 预计延迟为大约10分钟。
    
       ![验证专用终结点遥测](./media/private-endpoint-tsg/private-endpoint-monitor.png)

3. 使用**网络观察**程序中的 VM 连接疑难解答

    a）选择客户端 VM

    b）选择 "**连接故障排除**" 部分 "**出站连接**" 选项卡
    
      ![网络观察程序-测试出站连接](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c）选择 "**使用网络观察程序进行详细连接跟踪**"
    
      ![网络观察程序-连接故障排除](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d）选择 "**按 FQDN 测试**"
     - 将 FQDN 粘贴到专用终结点资源中
     - 提供端口（*通常为443，适用于 Azure 存储或 COSMOS，1336 For Sql ...* ）

    e）单击 "**测试**" 并验证测试结果
    
      ![网络观察程序-测试结果](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
4. 测试结果中的 DNS 解析必须具有分配给专用终结点的同一专用 IP 地址

    a）如果 DNS 设置不正确，请执行以下操作
     - 使用专用区域： 
       - 请确保客户端 VM VNet 与专用区域关联
       - 查看专用 DNS 存在的区域记录，如果不存在，则创建
    
     - 使用自定义 DNS：
       - 查看客户的 DNS 设置，并验证 DNS 配置是否正确。
       请参阅[私有终结点概述-DNS 配置](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)以获得指导。

    b）如果连接由于 NSG/Udr 而失败
     - 查看 NSG 出站规则并创建适当的出站规则以允许流量
    
       ![NSG 出站规则](./media/private-endpoint-tsg/nsg-outbound-rules.png)

5. 如果连接具有验证的结果，则连接问题可能与应用程序层的机密、令牌和密码等其他方面相关。
   - 在这种情况下，请检查与专用终结点关联的专用链接资源的配置。 请参阅[专用链接故障排除指南](troubleshoot-private-link-connectivity.md)。 

6. 如果问题仍未解决并且连接问题仍然存在，请与[Azure 支持](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)团队联系。 

## <a name="next-steps"></a>后续步骤

 * [在更新的子网（Azure 门户）上创建专用终结点](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)

 * [专用链接故障排除指南](troubleshoot-private-link-connectivity.md)
