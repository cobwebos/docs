---
title: "Azure 安全中心的合作伙伴集成 | Microsoft Docs"
description: "了解如何将 Azure 安全中心与合作伙伴集成，以增强 Azure 资源的总体安全性。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 44beafeff5cbe58ac8ca37632879f6ffc2b67e53
ms.contentlocale: zh-cn
ms.lasthandoff: 08/19/2017

---
# <a name="partner-integration-in-azure-security-center"></a>Azure 安全中心的合作伙伴集成

本文介绍如何将 Azure 安全中心与合作伙伴集成，以增强总体安全性。 安全中心在 Azure 中提供集成体验，并充分利用 Azure 应用商店进行合作伙伴认证和计费。

> [!NOTE] 
> 自 2017 年 6 月开始，安全中心将使用 Microsoft Monitoring Agent 来收集和存储数据。 有关详细信息，请参阅 [Azure 安全中心平台迁移](security-center-platform-migration.md)。 本文中的信息表示转换到 Microsoft Monitoring Agent 后的安全中心功能。
>

## <a name="why-deploy-partner-solutions-from-security-center"></a>为什么从安全中心部署合作伙伴的解决方案

利用安全中心的合作伙伴集成的四个主要原因是：

- 易于部署。 安全中心建议大大降低了合作伙伴解决方案的部署难度。 使用默认设置和网络拓扑即可全自动执行部署过程。 另外，客户也可以选择半自动选项，这样更灵活，并且可以进行自定义。
- 集成检测。 自动收集、聚合合作伙伴解决方案中的安全事件，并将其作为安全中心警报和事件的一部分进行显示。 这些事件还与来自其他源的检测融合在一起，以提供高级威胁检测功能。
- 统一的运行状况监视和管理。 客户可以使用集成式运行状况事件，一目了然地监视所有合作伙伴解决方案。 可通过使用合作伙伴解决方案轻松地访问高级设置，进行基本管理。
- 导出到 SIEM。 客户可以使用 Azure 日志集成（预览版）将所有安全中心警报和合作伙伴警报以通用事件格式 (CEF) 导出到本地安全信息和事件管理 (SIEM) 系统。


## <a name="partners-that-integrate-with-security-center"></a>与安全中心集成的合作伙伴

安全中心当前与以下解决方案集成：

- 终结点保护（[Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html)、Symantec 和[适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)） 
- Web 应用程序防火墙（[Barracuda](https://www.barracuda.com/products/webapplicationfirewall)、[F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html)、[Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF)、[Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets)、[Azure 应用程序网关](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)） 
- 下一代防火墙（[Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/)、[Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/)、[Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2)、[Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)） 
- 漏洞评估 ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

随着时间推移，安全中心将扩展这些类别中的合作伙伴数量，并添加新类别。 

## <a name="deploy-a-partner-solution"></a>部署合作伙伴解决方案

根据 Azure 环境的设置和定义的安全策略，安全中心可能会建议部署合作伙伴解决方案。 安全中心建议将引导你完成选择和安装合作伙伴解决方案的过程。 总体部署体验可能因所用解决方案类型和合作伙伴而异。 有关详细信息，请参阅以下文章：

- [安装终结点保护](security-center-install-endpoint-protection.md)
- [添加 Web 应用程序防火墙](security-center-add-web-application-firewall.md)
- [添加下一代防火墙](security-center-add-next-generation-firewall.md)
- [未安装漏洞评估](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>管理合作伙伴解决方案

部署以后，若要查看有关解决方案运行状况的信息并执行基本的管理任务，请在“安全中心”边栏选项卡中选择“合作伙伴解决方案”选项。 有关管理安全中心的合作伙伴解决方案的详细信息，请参阅[监视 Azure 安全中心的合作伙伴解决方案](security-center-partner-solutions.md)。

![合作伙伴集成](./media/security-center-partner-integration/security-center-partner-integration-fig1-new2.png)

> [!NOTE]
> Symantec 终结点保护支持仅限于发现， 不提供任何运行状况警报。
>

## <a name="see-also"></a>另请参阅

本文已经介绍了如何在 Azure 安全中心集成合作伙伴的解决方案。 若要详细了解安全中心，请参阅以下文章：

* [安全中心规划和操作指南](security-center-planning-and-operations-guide.md)
* [管理和响应安全中心的安全警报](security-center-managing-and-responding-alerts.md)
* [安全中心按类型划分的安全警报](security-center-alerts-type.md)
* [在安全中心进行安全运行状况监视](security-center-monitoring.md)。 了解如何监视 Azure 资源的运行状况。
* [通过安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)。 了解如何监视合作伙伴解决方案的运行状况。
* [Azure 安全中心常见问题解答](security-center-faq.md)。 获取服务使用方面的常见问题解答。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。

