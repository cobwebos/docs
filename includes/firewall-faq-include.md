---
title: include 文件
description: include 文件
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 9/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 1242cf80ffd283471eb507612949a81d4b3fd594
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47401607"
---
### <a name="what-is-azure-firewall"></a>什么是 Azure 防火墙？

Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。 可以跨订阅和虚拟网络集中创建、实施和记录应用程序与网络连接策略。

### <a name="what-capabilities-are-supported-in-azure-firewall"></a>Azure 防火墙支持哪些功能？  

* 服务形式的有状态防火墙
* 内置的高可用性以及不受限制的云可伸缩性
* FQDN 筛选
* FQDN 标记
* 网络流量筛选规则
* 出站 SNAT 支持
* 入站 DNAT 支持
* 跨 Azure 订阅和 VNET 集中创建、实施和记录应用程序与网络连接策略
* 与 Azure Monitor 完全集成，实现记录和分析功能 

### <a name="what-is-the-pricing-for-azure-firewall"></a>Azure 防火墙采用何种定价方式？

Azure 防火墙采用固定成本 + 可变成本的定价方式：

* 固定费用：1.25 美元/防火墙/小时
* 可变费用：0.03 美元/GB（防火墙处理的入口或出口数据）

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Azure 防火墙的典型部署模型是什么？

可在任何虚拟网络中部署 Azure 防火墙，但客户往往在中心虚拟网络中部署它，并在中心辐射模型中将其对等互连到其他虚拟网络。 然后，可将对等互连的虚拟网络中的默认路由设置为指向此中心防火墙虚拟网络。

### <a name="how-can-i-install-the-azure-firewall"></a>如何安装 Azure 防火墙？

可以使用 Azure 门户、PowerShell、REST API 或使用模板设置 Azure 防火墙。 有关分步说明，请参阅[教程：使用 Azure 门户部署和配置 Azure 防火墙](../articles/firewall/tutorial-firewall-deploy-portal.md)。

### <a name="what-are-some-azure-firewall-concepts"></a>有哪些 Azure 防火墙概念？

Azure 防火墙支持规则和规则集合。 规则集合是一组共享相同顺序和优先级的规则。 规则集合按其优先顺序执行。 网络规则集合的优先级高于应用程序规则集合，所有规则都具有终止性。

有两种类型的规则集合：

* *应用程序规则*：用于配置可从子网访问的完全限定域名 (FQDN)。 
* *网络规则*：用于配置包含源地址、协议、目标端口和目标地址的规则。 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure 防火墙是否支持入站流量筛选？

Azure 防火墙支持入站和出站筛选。 入站保护适用于非 HTTP/S 协议。 例如 RDP、SSH 和 FTP 协议。
 
### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Azure 防火墙支持哪些日志记录和分析服务？

Azure 防火墙与 Azure Monitor 集成，可用于查看和分析防火墙日志。 日志可发送到 Log Analytics、Azure 存储或事件中心。 它们可在 Log Analytics 中进行分析，也可通过 Excel 和 Power BI 等不同工具进行分析。 有关详细信息，请参阅[教程：监视 Azure 防火墙日志](../articles/firewall/tutorial-diagnostics.md)。

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure 防火墙的工作原理与市场中现有的服务（例如 NVA）有何不同？

Azure 防火墙是一项基础防火墙服务，可解决特定客户方案的问题。 应将第三方 NVA 与 Azure 防火墙混合使用。 更好地合作是核心任务。
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>应用程序网关 WAF 与 Azure 防火墙之间有何区别？

Web 应用程序防火墙 (WAF) 是应用程序网关的一项功能，可在出现常见攻击和漏洞时为 Web 应用程序提供集中的入站保护。 Azure 防火墙为所有端口和协议提供出站网络级别的保护，并为出站 HTTP/S 提供应用程序级别的保护。 暂定为 Azure 防火墙正式版规划针对非 HTTP/S 协议（例如，RDP、SSH、FTP）的入站保护功能。

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>网络安全组 (NSG) 和 Azure 防火墙之间有何区别？

Azure 防火墙服务为网络安全组功能提供了补充。 两者共同提供了更好的“深层防御”网络安全性。 网络安全组提供分布式网络层流量过滤，以限制每个订阅中虚拟网络内资源的流量。 Azure 防火墙是一个服务形式的完全有状态的集中式网络防火墙，可跨不同的订阅和虚拟网络提供网络和应用程序级别的保护。 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>如何使用服务终结点设置 Azure 防火墙？

若要安全访问 PaaS 服务，我们建议使用服务终结点。 可以选择在 Azure 防火墙子网中启用服务终结点，并在连接的分支虚拟网络中禁用它们。 这样可以受益于以下两个功能：服务终结点安全性和针对所有流量的集中日志记录。

### <a name="how-can-i-stop-and-start-azure-firewall"></a>如何停止和启动 Azure 防火墙？

可以使用 Azure PowerShell 的 *deallocate* 和 *allocate* 方法。

例如：

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzureRmFirewall -Name "FW Name” -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzureRmFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzureRmPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzureRmFirewall -AzureFirewall $azfw
```

> [!NOTE]
> 必须将防火墙和公共 IP 重新分配到原始资源组和订阅。

### <a name="what-are-the-known-service-limits"></a>有哪些已知的服务限制？

* Azure 防火墙的软限制为每个防火墙每月 1000 TB。 
* 在中心虚拟网络中运行的 Azure 防火墙实例存在虚拟网络对等互连的限制：最多 50 个分支虚拟网络。  
* Azure 防火墙不能与全球对等互连配合使用，因此应该在每个区域部署至少一个防火墙。
* Azure 防火墙支持 1 万个应用程序规则和 1 万个网络规则。

### <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>中心虚拟网络中的 Azure 防火墙能否转发并筛选两个分支虚拟网络之间的网络流量？

能，可以在中心虚拟网络中使用 Azure 防火墙来路由和筛选两个分支虚拟网络之间的流量。 每个分支虚拟网络中的子网必须具有指向 Azure 防火墙的 UDR，作为使此方案正常工作的默认网关。

### <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network"></a>Azure 防火墙能否转发并筛选同一虚拟网络中子网之间的网络流量？

即使 UDR 指向作为默认网关的 Azure 防火墙，也会直接路由同一虚拟网络或直接对等互连虚拟网络中的子网之间的流量。 建议的内部网络分段方法是使用网络安全组。 若要在此方案中将子网到子网流量发送到防火墙，UDR 必须在这两个子网上显式地包含目标子网网络前缀。
