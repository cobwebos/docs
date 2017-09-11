---
title: "排查 Azure VM 间的连接问题 | Microsoft Docs"
description: "了解如何排查 Azure VM 间的连接问题。"
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 524f6303f71590de75f7eb8fd2a9082c35dfa651
ms.contentlocale: zh-cn
ms.lasthandoff: 08/29/2017

---

# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>排查 Azure VM 间的连接问题

Azure 虚拟机 (VM) 间可能出现连接问题。 本文提供解决此问题的故障排除步骤。 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>症状

一个 Azure VM 无法连接到另一个 Azure VM。

## <a name="troubleshooting-guidance"></a>故障排除指南 

1. [检查 NIC 的配置是否不正确](#step-1-check-whether-nic-is-misconfigured)
2. [检查网络流量是否被 NSG 或 UDR 阻止](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [检查网络流量是否被 VM 防火墙阻止](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [检查 VM 应用或服务是否在侦听端口](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [检查问题是否由 SNAT 引起](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [检查 ACL 是否阻止了经典 VM 的流量](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [检查是否为经典 VM 创建了终结点](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [尝试连接到 VM 网络共享](#step-8-try-to-connect-to-a-vm-network-share)
9. [检查 Vnet 间连接](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>疑难解答步骤

请按照下列步骤排查问题。 完成每个步骤后，检查问题是否得到解决。 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>步骤 1：检查 NIC 的配置是否不正确

按照[如何为 Azure Windows VM 重置网络接口](../virtual-machines/windows/reset-network-interface.md)中的步骤进行操作。 

如果修改网络接口 (NIC) 后出现此问题，请按照下列步骤进行操作：

**多 NIC VM**

1. 添加 NIC。
2. 修复错误的 NIC 的问题或删除错误的 NIC。  然后再次添加 NIC。

有关详细信息，请参阅[添加或删除虚拟机的网络接口](virtual-network-network-interface-vm.md)。

**单 NIC VM** 

- [重新部署 Windows VM](../virtual-machines/windows/redeploy-to-new-node.md)
- [重新部署 Linux VM](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>步骤 2：检查网络流量是否被 NSG 或 UDR 阻止

使用[网络观察程序 IP 流验证](../network-watcher/network-watcher-ip-flow-verify-overview.md)和 [NSG 流日志记录](../network-watcher/network-watcher-nsg-flow-logging-overview.md)确定是否存在干扰流量流的网络安全组 (NSG) 或用户定义的路由 (UDR)。

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>步骤 3：检查网络流量是否被 VM 防火墙阻止

禁用防火墙，然后测试结果。 如果问题得到解决，则验证防火墙设置，然后重新启用防火墙。

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>步骤 4：检查 VM 应用或服务是否在侦听端口

可使用以下方法之一来检查 VM 应用或服务是否在侦听端口。

- 运行以下命令，检查服务器是否在侦听该端口。

**Windows VM**

    netstat –ano

**Linux VM**

    netstat -l

- 对虚拟机本身运行 telnet 命令，测试端口。 如果测试失败，则应用程序或服务未配置为侦听该端口。

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>步骤 5：检查问题是否由 SNAT 引起

在某些情况下，VM 位于负载均衡解决方案之后，而该解决方案与 Azure 外部的资源具有依赖关系。 在这些情况下，如果遇到间歇性连接问题，该问题可能因 [SNAT 端口耗尽](../load-balancer/load-balancer-outbound-connections.md)引起。 要解决该问题，请为负载均衡器后的每个 VM 创建一个 VIP（或为经典 VM 创建 ILPIP），并使用 NSG 或 ACL 进行保护。 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>步骤 6：检查 ACL 是否阻止了经典 VM 的流量

利用访问控制列表 (ACL)，可以选择允许还是拒绝虚拟机终结点的流量。 有关详细信息，请参阅[管理终结点上的 ACL](../virtual-machines/windows/classic/setup-endpoints.md#manage-the-acl-on-an-endpoint)。

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>步骤 7：检查是否为经典 VM 创建了终结点

在 Azure 中使用经典部署模型创建的所有 VM 都可以通过专用网络通道与同一云服务或虚拟网络中的其他虚拟机自动通信。 但是，其他虚拟网络上的计算机需要终结点才能定向虚拟机的入站网络流量。 有关详细信息，请参阅[如何设置终结点](../virtual-machines/windows/classic/setup-endpoints.md)。

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>步骤 8：尝试连接到 VM 网络共享

如果无法连接到 VM 网络共享，问题可能由 VM 中不可用的 NIC 引起。 要删除不可用的 NIC，请参阅[如何删除不可用的 NIC](../virtual-machines/windows/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>步骤 9：检查 Vnet 间连接

使用[网络观察程序 IP 流验证](../network-watcher/network-watcher-ip-flow-verify-overview.md)和 [NSG 流日志记录](../network-watcher/network-watcher-nsg-flow-logging-overview.md)确定是否存在干扰流量流的 NSG 或 UDR。 还可在[此处](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections)验证 Vnet 间配置。

### <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
