---
title: Azure 防火墙常见问题解答
description: Azure 防火墙常见问题解答。 是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/29/2020
ms.author: victorh
ms.openlocfilehash: 8b55f31f12ab1057ac2e0f625a0285b6518cc44a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845770"
---
# <a name="azure-firewall-faq"></a>Azure 防火墙常见问题解答

## <a name="what-is-azure-firewall"></a>什么是 Azure 防火墙？

Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。 可以跨订阅和虚拟网络集中创建、实施和记录应用程序与网络连接策略。

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Azure 防火墙支持哪些功能？

* 状态防火墙作为服务
* 内置高可用性，随附无限的云扩展性
* FQDN 筛选
* FQDN 标记
* 网络流量筛选规则
* 出站 SNAT 支持
* 入站 DNAT 支持
* 跨 Azure 订阅和 VNET 集中创建、实施和记录应用程序与网络连接策略
* 与 Azure Monitor 完全集成，实现记录和分析功能

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Azure 防火墙的典型部署模型是什么？

可在任何虚拟网络中部署 Azure 防火墙，但客户往往在中心虚拟网络中部署它，并在中心辐射模型中将其对等互连到其他虚拟网络。 然后，可将对等互连的虚拟网络中的默认路由设置为指向此中心防火墙虚拟网络。 支持全局 VNet 对等互连，但不建议使用全局 VNet 对等互连，因为跨区域可能会产生性能和延迟问题。 为了获得最佳性能，请为每个区域部署一个防火墙。

该模型的优点是能够跨不同订阅集中控制多个辐射 VNET。 由于不需要在每个 VNet 中单独部署防火墙，因此还能节约成本。 应根据客户流量模式衡量成本节约与相关对等互连成本。

## <a name="how-can-i-install-the-azure-firewall"></a>如何安装 Azure 防火墙？

可以使用 Azure 门户、PowerShell、REST API 或使用模板设置 Azure 防火墙。 有关分步说明，请参阅[教程：使用 Azure 门户部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。

## <a name="what-are-some-azure-firewall-concepts"></a>有哪些 Azure 防火墙概念？

Azure 防火墙支持规则和规则集合。 规则集合是一组共享相同顺序和优先级的规则。 规则集合按其优先顺序执行。 网络规则集合的优先级高于应用程序规则集合，所有规则都具有终止性。

有三种类型的规则集合：

* *应用程序规则*：配置可从子网访问的完全限定的域名（fqdn）。
* *网络规则*：配置包含源地址、协议、目标端口和目标地址的规则。
* *NAT 规则*：配置 DNAT 规则以允许传入连接。

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure 防火墙是否支持入站流量筛选？

Azure 防火墙支持入站和出站筛选。 入站保护通常用于非 HTTP/S 协议。 例如 RDP、SSH 和 FTP 协议。 为了获得最佳的入站 HTTP/S 保护，请[在 Azure 应用程序网关上使用 Azure Web 应用程序](../web-application-firewall/ag/ag-overview.md)防火墙等 web 应用程序防火墙。

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Azure 防火墙支持哪些日志记录和分析服务？

Azure 防火墙与 Azure Monitor 集成，可用于查看和分析防火墙日志。 日志可发送到 Log Analytics、Azure 存储或事件中心。 它们可在 Log Analytics 中进行分析，也可通过 Excel 和 Power BI 等不同工具进行分析。 有关详细信息，请参阅[教程：监视 Azure 防火墙日志](tutorial-diagnostics.md)。

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure 防火墙的工作原理与市场中现有的服务（例如 NVA）有何不同？

Azure 防火墙是一项基础防火墙服务，可解决特定客户方案的问题。 应该会混合使用第三方 Nva 和 Azure 防火墙。 更好地合作是核心任务。

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>应用程序网关 WAF 与 Azure 防火墙之间有何区别？

Web 应用程序防火墙 (WAF) 是应用程序网关的一项功能，可在出现常见攻击和漏洞时为 Web 应用程序提供集中的入站保护。 Azure 防火墙防火墙为非 HTTP/S 协议（例如，RDP、SSH、FTP）提供入站保护、为所有端口和协议提供出站网络级别的保护，并为出站 HTTP/S 提供应用程序级别的保护。

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>网络安全组 (NSG) 和 Azure 防火墙之间有何区别？

Azure 防火墙服务为网络安全组功能提供了补充。 两者共同提供了更好的“深层防御”网络安全性。 网络安全组提供分布式网络层流量过滤，以限制每个订阅中虚拟网络内资源的流量。 Azure 防火墙是一个服务形式的完全有状态的集中式网络防火墙，可跨不同的订阅和虚拟网络提供网络和应用程序级别的保护。

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Azure 防火墙子网上是否支持网络安全组（Nsg）？

Azure 防火墙是一种具有多个保护层的托管服务，其中包括使用 NIC level Nsg （不可查看）的平台保护。  Azure 防火墙子网上不需要子网级别的 Nsg，已禁用，以确保不会中断服务。

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>如何使用服务终结点设置 Azure 防火墙？

若要安全访问 PaaS 服务，我们建议使用服务终结点。 可以选择在 Azure 防火墙子网中启用服务终结点，并在连接的分支虚拟网络中禁用它们。 这样可以受益于以下两个功能：服务终结点安全性和针对所有流量的集中日志记录。

## <a name="what-is-the-pricing-for-azure-firewall"></a>Azure 防火墙采用何种定价方式？

请参阅[Azure 防火墙定价](https://azure.microsoft.com/pricing/details/azure-firewall/)。

## <a name="how-can-i-stop-and-start-azure-firewall"></a>如何停止和启动 Azure 防火墙？

可以使用 Azure PowerShell 的 *deallocate* 和 *allocate* 方法。

例如：

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> 必须将防火墙和公共 IP 重新分配到原始资源组和订阅。

## <a name="what-are-the-known-service-limits"></a>有哪些已知的服务限制？

有关 Azure 防火墙服务限制，请参阅[azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)。

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>中心虚拟网络中的 Azure 防火墙能否转发并筛选两个分支虚拟网络之间的网络流量？

能，可以在中心虚拟网络中使用 Azure 防火墙来路由和筛选两个分支虚拟网络之间的流量。 每个分支虚拟网络中的子网必须具有指向 Azure 防火墙的 UDR，作为使此方案正常工作的默认网关。

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Azure 防火墙能否转发和筛选同一虚拟网络或对等互连虚拟网络中子网之间的网络流量？

可以。 但是，配置 Udr 以重定向同一 VNET 中子网之间的流量需要额外关注。 虽然使用 VNET 地址范围作为 UDR 的目标前缀就足够了，但这也会通过 Azure 防火墙实例将所有流量从一台计算机路由到同一子网中的另一台计算机。 为避免这种情况，请在 UDR 中包含下一跃点类型为 **VNET** 的子网路由。 管理这些路由可能很麻烦并且容易出错。 建议的内部网络分段方法是使用不需要 UDR 的网络安全组。

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>专用网络之间是否存在 Azure 防火墙出站 SNAT？

当目标 IP 地址是每个[IANA RFC 1918](https://tools.ietf.org/html/rfc1918)的专用 ip 范围时，Azure 防火墙不会使用 SNAT。 如果你的组织使用专用网络的公共 IP 地址范围，Azure 防火墙会将流量 SNATs 到 AzureFirewallSubnet 中某个防火墙专用 IP 地址。

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>是否支持向网络虚拟设备强制建立隧道/链接？

当前不支持强制隧道。 Azure 防火墙必须具有直接的 Internet 连接。 如果 AzureFirewallSubnet 知道通过 BGP 的本地网络的默认路由，则必须将其替代为 0.0.0.0/0 UDR，将 NextHopType 值设置为 Internet 以保持 Internet 直接连接。

如果你的配置需要通过强制隧道连接到本地网络，并且可以确定 Internet 目标的目标 IP 前缀，则可以通过 AzureFirewallSubnet 上用户定义的路由将本地网络的这些范围配置为下一跃点。 或者，可以使用 BGP 来定义这些路由。

## <a name="are-there-any-firewall-resource-group-restrictions"></a>是否有任何防火墙资源组限制？

可以。 防火墙、子网、VNet 和公共 IP 地址都必须位于同一资源组中。

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>为入站网络流量配置 DNAT 时，是否还需要配置相应的网络规则以允许该流量？

不。 NAT 规则会隐式添加一个对应的网络规则来允许转换后的流量。 可以通过以下方法替代此行为：显式添加一个网络规则集合并在其中包含将匹配转换后流量的拒绝规则。 若要详细了解 Azure 防火墙规则处理逻辑，请参阅 [Azure 防火墙规则处理逻辑](rule-processing.md)。

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>如何在应用程序规则目标 FQDN 中使用通配符？

如果配置 * **. contoso.com**，则允许*anyvalue*contoso.com，但不允许 contoso.com （域顶点）。 如果希望允许域顶点，则必须将其显式配置为目标 FQDN。

## <a name="what-does-provisioning-state-failed-mean"></a>*预配状态的定义：失败*意味着？

只要应用了配置更改，Azure 防火墙就会尝试更新其所有底层后端实例。 在极少数情况下，这些后端实例中的一个可能无法使用新配置进行更新，并且更新过程将停止，并出现预配状态失败。 你的 Azure 防火墙仍可正常运行，但应用的配置可能处于不一致的状态，其中某些实例具有以前的配置，而其他实例具有已更新的规则集。 如果发生这种情况，请尝试再次更新配置，直到操作成功并且防火墙处于*成功*的预配状态。

### <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Azure 防火墙如何处理计划内维护和计划外故障？
Azure 防火墙在主动-主动配置中包含多个后端节点。  对于任何计划内维护，我们有连接排出逻辑来正常更新节点。  每个 Azure 区域的非工作时间计划更新，以进一步限制中断风险。  对于计划外问题，我们将实例化一个新节点来替换失败的节点。  与新节点的连接通常在10秒内从故障时间内重新建立。

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>防火墙名称是否有字符限制？

可以。 防火墙名称有50个字符的限制。

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>为什么 Azure 防火墙需要/26 子网大小？

在缩放时，Azure 防火墙必须预配更多虚拟机实例。 /26 地址空间可确保防火墙有足够的可用 IP 地址来容纳缩放。

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>在服务缩放时，防火墙子网大小是否需要更改？

不。 Azure 防火墙不需要大于/26 的子网。

## <a name="how-can-i-increase-my-firewall-throughput"></a>如何提高防火墙的吞吐量？

Azure 防火墙的初始吞吐量容量为 2.5-3 Gbps。 目前，scale out 仅基于 CPU 使用情况。 在某些情况下，具有网络规则的防火墙不会进行扩展以提高吞吐量，因为网络规则不会显著影响 CPU 使用率。 如果你的防火墙需要更高的吞吐量，请联系支持人员以增加防火墙的初始吞吐量容量。

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Azure 防火墙需要多长时间才能扩大？

目前，Azure 防火墙需要5到7分钟的时间来横向扩展。如果突发需要更快的自动缩放，请联系支持人员以提高防火墙的初始吞吐量容量。

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>默认情况下，Azure 防火墙是否允许访问 Active Directory？

不。 默认情况下，Azure 防火墙块 Active Directory 访问。 若要允许访问，请配置 AzureActiveDirectory 服务标记。 有关详细信息，请参阅[Azure 防火墙服务标记](service-tags.md)。
