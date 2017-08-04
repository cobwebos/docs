---
title: "Microsoft Azure 云服务连接性和网络问题的常见问题解答 | Microsoft 文档"
description: "本文列出了一些关于 Microsoft Azure 云服务连接性和网络的常见问题解答。"
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 3fc0d34fdb617ebb1af9c9f33e018d5fe6ec9a7d
ms.contentlocale: zh-cn
ms.lasthandoff: 06/15/2017


---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure 云服务连接性和网络问题：常见问题解答 (FAQ)

本文包括一些关于 [Microsoft Azure 云服务](https://azure.microsoft.com/services/cloud-services)连接性和网络问题的常见问题解答。 还可以参阅[云服务 VM 大小页面](cloud-services-sizes-specs.md)，了解大小信息。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>无法在多 VIP 云服务中保留 IP
首先，请确保已打开想要为其保留 IP 的虚拟机实例。 其次，请确保为过渡和生产部署使用保留的 IP。 **请勿**在部署升级过程中更改设置。

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>设置了 NSG 时，如何使用远程桌面？
将规则添加到 NSG，允许端口 **3389** 和 **20000** 上的流量。  远程桌面使用端口 **3389**。  云服务实例经过负载均衡，因此无法直接控制要连接到哪个实例。  RemoteForwarder 和 RemoteAccess 代理管理 RDP 流量，允许客户端发送 RDP cookie 和指定要连接到的单个实例。  RemoteForwarder 和 RemoteAccess 代理要求打开端口 20000（如果你具有 NSG，此端口可能已被阻止）。

## <a name="can-i-ping-a-cloud-service"></a>是否可以 ping 云服务？

否，不能通过使用正常 "ping"/ICMP 协议 ping 云服务。 通过 Azure 负载均衡器不允许使用 ICMP 协议。

若要测试连接性，我们建议执行端口 ping 操作。 当 Ping.exe 使用 ICMP 时，其他工具（如 PSPing、Nmap 和 telnet）允许你测试到特定 TCP 端口的连接性。

有关详细信息，请参阅[使用端口 ping 而不是 ICMP 来测试 Azure VM 连接性](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/)。

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>如何防止接收来自未知 IP 地址的数千次点击，这些 IP 地址是否会对云服务造成某种形式的恶意攻击？
Azure 实现多层网络安全性，以保护其平台服务免受分布式拒绝服务 (DDoS) 攻击。 Azure DDoS 防御系统是 Azure 持续监视过程的一部分，通过渗透测试不断改进。 该 DDoS 防御系统的设计不仅可以抵御外部的攻击，还可以承受其他 Azure 租户的攻击。 有关详细信息，请参阅 [Microsoft Azure 网络安全性](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf)。

你还可以创建一个启动任务来选择性地阻止某些特定 IP 地址。 有关详细信息，请参阅[阻止特定 IP 地址](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)。

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>当尝试 RDP 到我的云服务实例时，我收到消息：“此用户帐户已过期。”
当绕过 RDP 设置中配置的到期日期时，你可能会收到“此用户帐户已过期”的错误消息。 你可以按照以下步骤从门户更改到期日期：
1. 登录到 Azure 管理控制台 (https://manage.windowsazure.com)，导航到云服务，然后选择“配置”选项卡。
2. 选择“远程”。
3. 更改“到期日期”字段中的日期，然后保存配置。

你现在应能够 RDP 到你的计算机了。

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>为什么 LoadBalancer 不平均地平衡流量？
有关内部负载均衡器工作原理的信息，请参阅 [Azure 负载均衡器新分发模式](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/)。

使用的分发算法是将流量映射到可用服务器的 5 元组（源 IP、源端口、目标 IP、目标端口和协议类型）哈希。 它仅在传输会话内部提供粘性。 同一 TCP 或 UDP 会话中的数据包将被定向到经过负载均衡的终结点后面的同一数据中心 IP (DIP) 实例。 客户端从同一源 IP 关闭并重新打开连接或发起新会话时，源端口会更改，并导致流量定向到其他 DIP 终结点。

