---
title: 在 Azure 安全中心集成安全解决方案 | Microsoft Docs
description: 了解如何将 Azure 安全中心与合作伙伴集成，以增强 Azure 资源的总体安全性。
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2018
ms.author: terrylan
ms.openlocfilehash: ac1e49400f32194ada319f23b5233cc81de02a02
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>在 Azure 安全中心集成安全解决方案
本文档介绍如何管理已连接到 Azure 安全中心的安全解决方案，以及如何添加新的安全解决方案。

## <a name="integrated-azure-security-solutions"></a>集成式 Azure 安全解决方案
可以通过安全中心轻松地在 Azure 中启用集成式安全解决方案。 优势包括：

- **简化部署**：安全中心提供对集成式合作伙伴解决方案的简化预配。 对于反恶意软件和漏洞评估之类的解决方案，安全中心可以在虚拟机上预配所需的代理；对于防火墙设备，安全中心可以负责大部分必需的网络配置。
- **集成检测**：自动收集、聚合合作伙伴解决方案中的安全事件，并将其作为安全中心警报和事件的一部分进行显示。 这些事件还与来自其他源的检测融合在一起，以提供高级威胁检测功能。
- **统一运行状况监视和管理**：利用集成运行状况事件，客户可以一目了然地监视所有合作伙伴解决方案。 可通过使用合作伙伴解决方案轻松地访问高级设置，进行基本管理。

目前，集成式安全解决方案包括：

- 终结点保护（[Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html)、Symantec、McAfee、Windows Defender 和 System Center Endpoint Protection (SCEP)）
- Web 应用程序防火墙（[Barracuda](https://www.barracuda.com/products/webapplicationfirewall)、[F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html)、[Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF)、[Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets)、[Azure 应用程序网关](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)）
- 下一代防火墙（[Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/)、[Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/)、[Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2)、[Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)）
- 漏洞评估 ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

终结点保护集成体验可能因解决方案而异。 下表包含每个解决方案的体验的更多详细信息：

| 终结点保护               | 平台                             | 安全中心安装 | 安全中心发现 |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Windows Defender (Microsoft Antimalware)                  | Windows Server 2016                   | 否，内置到 OS           | 是                       |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2 | 通过扩展                | 是                       |
| Trend Micro – 所有版本         | Windows Server 系列                 | 通过扩展                | 是                       |
| Symantec v12.1.1100+              | Windows Server 系列                 | 否                           | 是                       |
| McAfee v10+                       | Windows Server 系列                 | 否                           | 是                       |
| Kaspersky                         | Windows Server 系列                 | 否                           | 否                        |
| Sophos                            | Windows Server 系列                 | 否                           | 否                        |



## <a name="how-security-solutions-are-integrated"></a>安全中心如何集成
从安全中心部署的 Azure 安全解决方案是自动连接的。 还可以连接其他安全数据源，包括：

- Azure AD Identity Protection
- 在本地或其他云中运行的计算机
- 支持通用事件格式 (CEF) 的安全解决方案
- Microsoft 高级威胁分析

![合作伙伴解决方案集成](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>管理集成式 Azure 安全解决方案和其他数据源

1. 登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)。

2. 在 **Microsoft Azure 菜单**上选择“安全中心”。 此时会打开“安全中心 - 概览”。

  ![安全中心概述](./media/security-center-partner-integration/overview.png)

3. 在“概览”下，选择“安全解决方案”。

在“安全解决方案”下，可以查看集成式 Azure 安全解决方案的运行状况信息，并执行基本的管理任务。 还可以连接其他类型的安全数据源，例如通用事件格式 (CEF) 的 Azure Active Directory Identity Protection 警报和防火墙日志。

### <a name="connected-solutions"></a>已连接解决方案

“已连接解决方案”部分包括目前连接到安全中心的安全解决方案，以及每个解决方案的运行状况信息。  

![已连接解决方案](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

请参阅[管理连接的合作伙伴解决方案](security-center-partner-solutions.md)，了解详细信息。

### <a name="discovered-solutions"></a>已发现解决方案

安全中心自动发现在 Azure 中运行但未连接到安全中心的安全解决方案，然后在“发现的解决方案”部分显示这些解决方案。 其中包括 Azure 解决方案，例如 [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)，以及合作伙伴解决方案。

> [!NOTE]
> 在订阅级别，安全中心标准层是已发现解决方案功能所必需的。 若要详细了解安全中心的定价层，请参阅[定价](security-center-pricing.md)。
>
>

在解决方案下选择“连接”，以便集成安全中心，获取有关安全警报的通知。

![已发现解决方案](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

安全中心还发现部署在订阅中的那些能够转发常见事件格式 (CEF) 日志的解决方案。 了解如何[连接安全解决方案](quick-security-solutions.md)，以便将使用 CEF 日志的安全解决方案连接到安全中心。

### <a name="add-data-sources"></a>添加数据源

“添加数据源”部分包括其他可以连接的可用数据源。 如需从任何此类源添加数据的说明，请单击“添加”。

![数据源](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>后续步骤

本文介绍了如何在安全中心集成合作伙伴的解决方案。 若要详细了解安全中心，请参阅以下文章：

* [将 Microsoft Advanced Threat Analytics 连接到 Azure 安全中心](security-center-ata-integration.md)
* [将 Azure Active Directory Identity Protection 连接到 Azure 安全中心](security-center-aadip-integration.md)
* [在安全中心进行安全运行状况监视](security-center-monitoring.md)。 了解如何监视 Azure 资源的运行状况。
* [通过安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)。 了解如何监视合作伙伴解决方案的运行状况。
* [Azure 安全中心常见问题解答](security-center-faq.md)。 获取安全中心使用方面的常见问题解答。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。
