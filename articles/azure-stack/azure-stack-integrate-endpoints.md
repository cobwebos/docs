---
title: "Azure 堆栈数据中心集成-发布终结点"
description: "了解如何在你的数据中心中发布 Azure 堆栈终结点"
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 01/16/2018
ms.author: victorh
keywords: 
ms.openlocfilehash: 1cc74cb2214918d6bfd0c0827cf5d9832b84f317
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure 堆栈数据中心集成-发布终结点

*适用范围： Azure 堆栈集成系统*

Azure 堆栈将设置其基础结构角色的各种终结点 (Vip 的虚拟 IP 地址)。 这些 Vip 是从公共 IP 地址池分配的。 每个 VIP 访问控制列表 (ACL) 中的软件定义网络层保护。 Acl 还跨物理交换机 （TORs 和 BMC） 用于进一步强化处理解决方案。 为每个终结点在部署时指定的外部 DNS 区域中创建 DNS 条目。


下面的体系结构关系图显示了不同的网络层和 Acl:

![体系结构图](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>端口和协议 （入站）

下表中列出所需的到外部网络的发布 Azure 堆栈终结点的基础结构 Vip。 此列表显示每个终结点、 所需的端口和协议。 终结点所需的 SQL 资源提供程序和其他人，等其他资源提供程序特定的资源提供程序部署文档中介绍。

未列出 Vip，因为它们不需要发布 Azure 堆栈的内部基础结构。

> [!NOTE]
> 用户 Vip 是动态的由用户自行通过 Azure 堆栈运算符没有控件的定义。


|终结点 (VIP)|DNS 主机记录|协议|端口|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|门户 （管理员）|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Azure 资源管理器 （管理员）|Adminmanagement。*&lt;区域 >。&lt;fqdn >*|HTTPS|443<br>30024|
|门户 （用户）|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|Azure 资源管理器 （用户）|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|图形|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|证书吊销列表|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP 和 UDP|53|
|密钥保管库 （用户）|*.vault.*&lt;region>.&lt;fqdn>*|TCP|443|
|密钥保管库 （管理员）|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|TCP|443|
|存储队列|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|存储表|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|存储 Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|

## <a name="ports-and-urls-outbound"></a>端口和 Url （出站）

Azure 堆栈仅支持透明的代理服务器。 在部署中其中透明代理的上行以传统的代理服务器，你必须允许下列端口和 Url 用于出站通信：


|目的|代码|协议|端口|
|---------|---------|---------|---------|
|标识|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net|HTTP<br>HTTPS|80<br>443|
|应用商店联合|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|修补程序和更新|https://&#42;.azureedge.net|HTTPS|443|
|注册|https://management.azure.com|HTTPS|443|
|使用情况|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|

## <a name="firewall-publishing"></a>防火墙发布

上一节中列出的端口适用于通过现有的防火墙中发布 Azure 堆栈服务时的入站通信。

我们建议你使用防火墙设备来帮助安全 Azure 堆栈。 但是，这不是严格要求。 虽然防火墙可以帮助与分布式的拒绝服务 (DDOS) 攻击，以及内容检查等，但它们也可能会变为如 blob、 表和队列的 Azure 存储服务的形成吞吐量瓶颈。

它基于标识模型 (Azure AD 或 AD FS)，可能或可能不需要发布的 AD FS 终结点。 如果使用断开连接的部署模式时，必须发布的 AD FS 终结点。 （有关详细信息，请参阅数据中心集成标识主题）。

Azure 资源管理器 （管理员）、 管理员门户和密钥保管库 （管理员） 终结点一定不需要外部发布。 它取决于方案。 例如，作为服务提供商，你可能想要限制的受攻击面并仅在您的网络，而不是从 Internet 管理从 Azure 堆栈。

对于企业组织，外部网络可以是现有的企业网络。 在此类方案中，你必须将这些终结点，若要从企业网络中运行 Azure 堆栈。

## <a name="edge-firewall-scenario"></a>边缘防火墙方案

在边缘部署中，Azure 堆栈带有或不带前面遮挡它防火墙 （由 ISP） 边缘路由器后面直接部署。

![Azure 堆栈边缘部署的体系结构图示](media/azure-stack-integrate-endpoints/Integrate-Endpoints-02.png)

通常情况下，公共路由的 IP 地址为指定的公共 VIP 池在部署时，在边缘部署。 这种情况下使用户可以体验像在公共云如 Azure 的完整自受控的云体验。

### <a name="using-nat"></a>使用 NAT

尽管不建议因为产生系统开销，但是你可以使用网络地址转换 (NAT) 进行发布终结点。 对于完全由用户控制的终结点发布，这需要每个包含用户可能使用的所有端口的 VIP 用户的 NAT 规则。

另一个注意事项是 Azure 不支持使用 VPN 隧道连接到终结点使用与 Azure 混合云方案中的 NAT 设置。

## <a name="enterpriseintranetperimeter-network-firewall-scenario"></a>企业/intranet/外围网络防火墙方案

在企业/intranet/外围部署中，在第二个防火墙，这通常是外围网络 (也称为 DMZ) 的一部分后面部署 Azure 堆栈。

![Azure 堆栈防火墙方案](media/azure-stack-integrate-endpoints/Integrate-Endpoints-03.png)

如果 Azure 堆栈的公共 VIP 池已指定公共路由的 IP 地址，这些地址将逻辑属于的外围网络，并且需要在主防火墙发布规则。

### <a name="using-nat"></a>使用 NAT

如果将非公共路由的 IP 地址用于 Azure 堆栈的公共 VIP 池，NAT 使用辅助防火墙在发布 Azure 堆栈终结点。 在此方案中，你需要超过其边缘，在主防火墙上和辅助防火墙上配置发布的规则。 如果你想要使用 NAT，请考虑以下几点：

- 由于用户控制其自己的终结点和软件定义网络 (SDN) 堆栈中的其自己发布规则管理防火墙规则时，NAT 会增加开销。 用户必须联系 Azure 堆栈运算符获取发布，其 Vip 和更新的端口列表。
- 虽然 NAT 使用情况限制的用户体验，它使能够完全控制运算符对发布请求。
- 对于使用 Azure 的混合云方案，请考虑 Azure 不支持设置使用 VPN 隧道连接到终结点使用 nat。


## <a name="next-steps"></a>后续步骤

[Azure 堆栈数据中心集成的安全](azure-stack-integrate-security.md)