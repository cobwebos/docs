---
title: include 文件
description: include 文件
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 7/30/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: e23579479c61810d651bebae7b486b53aaaf0d42
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39361377"
---
### <a name="what-is-azure-firewall"></a>什么是 Azure 防火墙？

Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。 可以跨订阅和虚拟网络集中创建、实施和记录应用程序与网络连接策略。 Azure 防火墙目前处于公共预览状态。

### <a name="what-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Azure 防火墙公共预览版支持哪些功能？  

* 服务形式的有状态防火墙
* 内置的高可用性以及不受限制的云可伸缩性
* FQDN 筛选 
* 网络流量筛选规则
* 出站 SNAT 支持
* 跨 Azure 订阅和 VNET 集中创建、实施和记录应用程序与网络连接策略
* 与 Azure Monitor 完全集成，实现记录和分析功能 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>如何加入 Azure 防火墙公共预览版？

Azure 防火墙目前是托管的公共预览版，可使用 Register-AzureRmProviderFeature PowerShell 命令加入，如 Azure 防火墙公共预览版文档中所述。

### <a name="what-is-the-pricing-for-azure-firewall"></a>Azure 防火墙采用何种定价方式？

Azure 防火墙采用固定成本 + 可变成本的定价方式。 具体价格如下，公共预览期间还可在此基础上进一步提供 50% 的折扣。

* 固定费用：1.25 美元/防火墙/小时
* 可变费用：0.03 美元/GB（防火墙处理的入口或出口数据）。

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Azure 防火墙的典型部署模型是什么？

虽然可以在任何 VNET 中部署 Azure 防火墙，但客户通常会在中央 VNET 中部署 Azure 防火墙，并在中心辐射模型中让其他 VNET 与其对等互联。 然后，可将对等 VNET 的默认路由设置为指向此中央防火墙 VNET。

### <a name="how-can-i-install-the-azure-firewall"></a>如何安装 Azure 防火墙？

Azure 防火墙可通过 Azure 门户、PowerShell、REST API 或模板进行设置。 有关分步说明，请参阅[教程：使用 Azure 门户部署和配置 Azure 防火墙](../articles/firewall/tutorial-firewall-deploy-portal.md)。

### <a name="what-are-some-azure-firewall-concepts"></a>有哪些 Azure 防火墙概念？

Azure 防火墙支持规则和规则集合。 规则集合是一组共享相同顺序和优先级的规则。 规则集合按其优先级顺序执行，网络规则集合的优先级高于应用程序规则集合，所有规则都具有终止性。
有两种类型的规则集合：

* 应用程序规则：允许配置可从子网访问的完全限定域名 (FQDN)。 
* 网络规则：允许配置包含源地址、协议、目标端口和目标地址的规则。 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure 防火墙是否支持入站流量筛选？

Azure 防火墙公共预览版仅支持出站筛选。 暂定为 Azure 防火墙正式版规划针对非 HTTP/S 协议（例如：RDP、SSH、FTP）的入站保护功能。  
 
### <a name="what-logginganalytics-is-supported-by-the-azure-firewall"></a>Azure 防火墙支持哪些记录/分析功能？

Azure 防火墙与 Azure Monitor 集成，可用于查看和分析防火墙日志。 日志可发送到 Log Analytics、Azure 存储或事件中心。 它们可在 Log Analytics 中进行分析，也可通过 Excel 和 Power BI 等不同工具进行分析。 有关更多详细信息，请参阅[教程：监视 Azure 防火墙日志](../articles/firewall/tutorial-diagnostics.md)。

### <a name="how-does-azure-firewall-work-relative-to-existing-like-nvas-in-the-marketplace"></a>Azure 防火墙如何与 NVA 等市场上现有的产品配合工作？

Azure 防火墙是一项基础防火墙服务，可解决特定客户方案的问题。 你应当将第三方 NVA 与 Azure 防火墙混合使用。 更好地合作是核心任务。
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>应用程序网关 WAF 与 Azure 防火墙之间有何区别？

Web 应用程序防火墙 (WAF) 是应用程序网关的一项功能，可在出现常见攻击和漏洞时为 Web 应用程序提供集中的入站保护。 Azure 防火墙为所有端口和协议提供出站网络级别的保护，并为出站 HTTP/S 提供应用程序级别的保护。 暂定为 Azure 防火墙正式版规划针对非 HTTP/S 协议（例如，RDP、SSH、FTP）的入站保护功能。

### <a name="what-is-the-difference-between-network-security-groups-nsg-and-azure-firewall"></a>网络安全组 (NSG) 和 Azure 防火墙之间有何区别？

Azure 防火墙服务为网络安全组功能提供了补充，并可共同提供更好的深层防御网络安全性。 NSG 提供分布式网络层流量过滤，以限制每个订阅中虚拟网络内资源的流量。  Azure 防火墙是一个服务形式的完全有状态的集中式网络防火墙，可跨不同的订阅和虚拟网络 (VNet) 提供网络和应用程序级别的保护。 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>如何使用服务终结点设置 Azure 防火墙？

如果想要安地全访问 PaaS 服务，我们建议使用服务终结点。 Azure 防火墙客户可选择在 Azure 防火墙子网中启用服务终结点，并在连接的辐射式 VNET 中禁用它，从而获益于以下两个功能：服务端点安全性和针对所有流量的集中记录。

### <a name="what-are-the-known-service-limits"></a>有哪些已知的服务限制？

* Azure 防火墙的软限制为 1000 TB/防火墙/月。 
* 在中央 VNET 中运行的 Azure 防火墙受 VNET 对等限制的约束：最多 50 个辐射式 VNET。  
* Azure 防火墙不能与全球对等互连配合使用，因此应该在每个区域部署至少一个防火墙。
* Azure 防火墙支持 10000 个应用程序规则和 10000 个网络规则。