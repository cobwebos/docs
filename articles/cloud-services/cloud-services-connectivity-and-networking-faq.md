---
title: Microsoft Azure 云服务连接性和网络问题的常见问题解答 | Microsoft 文档
description: 本文列出了一些关于 Microsoft Azure 云服务连接性和网络的常见问题解答。
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: genli
ms.openlocfilehash: e89549f51abb896c1ddf48a46de78fb5e4988f22
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure 云服务连接性和网络问题：常见问题解答 (FAQ)

本文包括一些关于 [Azure 云服务](https://azure.microsoft.com/services/cloud-services)连接性和网络问题的常见问题解答。 有关大小信息，请参阅[云服务 VM 大小页](cloud-services-sizes-specs.md)。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>无法在多 VIP 云服务中保留 IP
首先，请确保已打开想要为其保留 IP 的虚拟机实例。 其次，请确保为过渡和生产部署使用保留的 IP。 *请勿*在部署升级过程中更改设置。

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>设置了 NSG 时，如何使用远程桌面？
将规则添加到 NSG，允许端口 **3389** 和 **20000** 上的流量。 远程桌面使用端口 **3389**。 云服务实例经过负载均衡，因此无法直接控制要连接到哪个实例。 *RemoteForwarder* 和 *RemoteAccess* 代理管理远程桌面协议 (RDP) 流量，允许客户端发送 RDP cookie 和指定要连接到的单个实例。 *RemoteForwarder* 和 *RemoteAccess* 代理要求打开端口 **20000**（如果你具有 NSG，此端口可能已被阻止）。

## <a name="can-i-ping-a-cloud-service"></a>是否可以 ping 云服务？

否，不能通过使用正常 "ping"/ICMP 协议 ping 云服务。 通过 Azure 负载均衡器不允许使用 ICMP 协议。

若要测试连接性，我们建议执行端口 ping 操作。 当 Ping.exe 使用 ICMP 时，其他工具（如 PSPing、Nmap 和 telnet）允许你测试到特定 TCP 端口的连接性。

有关详细信息，请参阅[使用端口 ping 而不是 ICMP 来测试 Azure VM 连接性](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/)。

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>如何防止接收来自未知 IP 地址的数千次点击，这些 IP 地址是否会对云服务造成某种形式的恶意攻击？
Azure 实现多层网络安全性，以保护其平台服务免受分布式拒绝服务 (DDoS) 攻击。 Azure DDoS 防御系统是 Azure 持续监视过程的一部分，通过渗透测试不断改进。 该 DDoS 防御系统的设计不仅可以抵御外部的攻击，还可以承受其他 Azure 租户的攻击。 有关详细信息，请参阅 [Azure 网络安全](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf)。

还可以创建一个启动任务来选择性地阻止某些特定 IP 地址。 有关详细信息，请参阅[阻止特定 IP 地址](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)。

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>当尝试 RDP 到我的云服务实例时，我收到消息：“此用户帐户已过期。”
当绕过 RDP 设置中配置的到期日期时，你可能会收到“此用户帐户已过期”的错误消息。 你可以按照以下步骤从门户更改到期日期：

1. 登录到 [Azure 门户](https://portal.azure.com)，导航到云服务并选择“远程桌面”选项卡。

2. 选择“生产”或“暂存”部署槽位。

3. 更改“到期日期”字段中的日期，然后保存配置。

你现在应能够 RDP 到你的计算机了。

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>为什么 Azure 负载均衡器不平均地均衡流量？
有关内部负载均衡器工作原理的信息，请参阅 [Azure 负载均衡器新分发模式](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/)。

使用的分发算法是将流量映射到可用服务器的 5 元组（源 IP、源端口、目标 IP、目标端口和协议类型）哈希。 它仅在传输会话内部提供粘性。 同一 TCP 或 UDP 会话中的数据包将被定向到经过负载均衡的终结点后面的同一数据中心 IP (DIP) 实例。 客户端从同一源 IP 关闭并重新打开连接或发起新会话时，源端口会更改，并导致流量定向到其他 DIP 终结点。

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>如何将发往云服务的默认 URL 的传入流量重定向到自定义 URL？ 

可以使用 IIS 的 URL 重写模块将传入到云服务的默认 URL（例如 \*.cloudapp.net）的流量重定向到某个自定义 DNS 名称/URL。 由于 URL 重写模块默认在 Web 角色上启用并且其规则是在应用程序的 web.config 中配置的，因此，无论是否重新启动/重置映像，它在 VM 上都始终可用。 有关详细信息，请参阅：

- [为 URL 重写模块创建重写规则](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [删除默认链接](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>如何阻止/禁用发往云服务的默认 URL 的传入流量？ 

可以阻止发往云服务的默认 URL/名称（例如 \*）的传入流量。 在云服务定义 (*.csdef) 文件中的站点绑定配置下将主机标头设置为自定义 DNS 名称（例如 www.MyCloudService.com）。 
 

    <?xml version="1.0" encoding="utf-8"?> 
    <ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6"> 
      <WebRole name="MyWebRole" vmsize="Small"> 
        <Sites> 
          <Site name="Web"> 
            <Bindings> 
              <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" /> 
            </Bindings> 
          </Site> 
        </Sites> 
        <Endpoints> 
          <InputEndpoint name="Endpoint1" protocol="http" port="80" /> 
        </Endpoints> 
        <ConfigurationSettings> 
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" /> 
        </ConfigurationSettings> 
      </WebRole> 
    </ServiceDefinition> 
 
因为通过 csdef 文件强制实施了此主机标头绑定，所以，只能通过自定义名称“www.MyCloudService.com”访问该服务。 发往“*.cloudapp.net”域的所有传入请求都将失败。 如果在服务中使用了自定义 SLB 探测或内部负载均衡器，则阻止服务的默认 URL/名称可能会干扰探测行为。 

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>如何确保云服务面向公众的 IP 地址永不改变？

为了将云服务的 IP 地址列入允许列表，建议将一个保留 IP 与服务进行关联，否则，如果删除了部署，则会从订阅解除分配由 Azure 提供的虚拟 IP（也称 VIP）。 否则，如果删除了部署，则会从订阅解除分配由 Azure 提供的虚拟 IP。 为使 VIP 交换操作成功，需要为生产槽和暂存槽设置单独的保留 IP。 如果缺少这些 IP，交换操作会失败。 请根据以下文章来保留 IP 地址并将其与云服务进行关联：
 
- [保留现有云服务的 IP 地址](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [使用服务配置文件将保留 IP 关联到云服务](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

只要有多个实例用于你的角色，将 RIP 与云服务进行关联就应该不会导致任何停机时间。 另外，还可以将你的 Azure 数据中心的 IP 范围列入允许列表。 可以在 [Microsoft 下载中心](https://www.microsoft.com/en-us/download/details.aspx?id=41653)找到所有 Azure IP 范围。 

此文件包含 Azure 数据中心使用的 IP 地址范围（包括计算、SQL 和存储范围）。 每周都将发布更新的文件，反映当前已部署的范围和任何即将对 IP 范围进行的更改。 数据中心至少在一周后才会使用文件中显示的新范围。 请每周下载新的 xml 文件，并在网站上执行必要的更改以正确地标识 Azure 中运行的服务。 Azure ExpressRoute 用户可能会注意到，此文件用于在每个月第一周更新 Azure 空间的 BGP 播发。 

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>如何将 Azure 资源管理器虚拟网络与云服务一起使用？ 

不能将云服务置于 Azure 资源管理器虚拟网络中。 可以通过对等互连将 Azure 资源管理器虚拟网络与经典部署虚拟网络连接起来。  有关详细信息，请参阅[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)。
