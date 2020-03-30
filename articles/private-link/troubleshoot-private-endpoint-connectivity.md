---
title: 排查 Azure 专用终结点连接问题
description: 诊断专用终结点连接的分步指南
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
ms.openlocfilehash: fcc482e6231bbd925fd500a37989052765dede58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538528"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>排查 Azure 专用终结点连接问题

本文提供分步指导，以验证和诊断 Azure 专用终结点连接设置。

Azure 专用终结点是一个网络接口，可私下安全地连接到专用链接服务。 此解决方案通过从虚拟网络向 Azure 服务资源提供专用连接，帮助您保护 Azure 中的工作负荷。 此解决方案有效地将这些服务引入虚拟网络。

以下是专用终结点可用的连接方案：

- 来自同一区域的虚拟网络
- 区域对等虚拟网络
- 全球对等虚拟网络
- 通过 VPN 或 Azure 快速路由电路在本地的客户

## <a name="diagnose-connectivity-problems"></a>诊断连接问题 

查看这些步骤，以确保所有常规配置都按预期解决专用终结点设置的连接问题。

1. 通过浏览资源查看专用终结点配置。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 转到**私人链接中心**。

      ![专用链接中心](./media/private-endpoint-tsg/private-link-center.png)

    b.保留“数据库类型”设置，即设置为“共享”。 在左侧窗格中，选择 **"专用终结点**"。
    
      ![专用终结点](./media/private-endpoint-tsg/private-endpoints.png)

    c. 筛选并选择要诊断的专用终结点。

    d.单击“下一步”。 查看虚拟网络和 DNS 信息。
     - 验证连接状态是否**已批准**。
     - 确保 VM 与承载专用终结点的虚拟网络具有连接。
     - 检查已分配 FQDN 信息（副本）和专用 IP 地址。
    
       ![虚拟网络和 DNS 配置](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. 使用[Azure 监视器](https://docs.microsoft.com/azure/azure-monitor/overview)查看数据是否流动。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在专用终结点资源上，选择 **"监视器**"。
     - 选择**数据内**或**数据出**。 
     - 查看尝试连接到专用终结点时数据是否流动。 预计延迟约 10 分钟。
    
       ![验证专用终结点遥测](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  使用 Azure 网络观察程序的**VM 连接故障排除**。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择客户端 VM。

    b.保留“数据库类型”设置，即设置为“共享”。 选择**连接疑难解答**，然后选择**出站连接**选项卡。
    
      ![网络观察程序 - 测试出站连接](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. 选择 **"使用网络观察程序"进行详细的连接跟踪**。
    
      ![网络观察程序 - 连接故障排除](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d.单击“下一步”。 选择**按 FQDN 进行的测试**。
     - 从专用终结点资源粘贴 FQDN。
     - 提供端口。 通常，将 443 用于 Azure 存储或 Azure 宇宙数据库，将 1336 用于 SQL。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 选择 **"测试**"并验证测试结果。
    
      ![网络观察程序 - 测试结果](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. 测试结果中的 DNS 解析必须具有相同的专用 IP 地址分配给专用终结点。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 如果 DNS 设置不正确，请按照以下步骤操作：
     - 如果您使用专用区域： 
       - 确保客户端 VM 虚拟网络与专用区域关联。
       - 检查是否存在专用 DNS 区域记录。 如果不存在，请创建它。
     - 如果使用自定义 DNS：
       - 查看自定义 DNS 设置，并验证 DNS 配置是否正确。
       有关指南，请参阅[专用终结点概述：DNS 配置](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)。

    b.保留“数据库类型”设置，即设置为“共享”。 如果由于网络安全组 （NSG） 或用户定义的路由而连接失败：
     - 查看 NSG 出站规则，并创建适当的出站规则以允许流量。
    
       ![NSG 出站规则](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. 如果连接已验证结果，则连接问题可能与应用程序层中的秘密、令牌和密码等其他方面有关。
   - 在这种情况下，请查看与专用终结点关联的专用链接资源的配置。 有关详细信息，请参阅 Azure[专用链接故障排除指南](troubleshoot-private-link-connectivity.md)。

1. 如果问题仍未解决且连接问题仍然存在，请与[Azure 支持](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)团队联系。

## <a name="next-steps"></a>后续步骤

 * [在更新的子网（Azure 门户）上创建专用终结点](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Azure 专用链接故障排除指南](troubleshoot-private-link-connectivity.md)
