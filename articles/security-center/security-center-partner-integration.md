---
title: "Azure 安全中心的合作伙伴和解决方案集成 | Microsoft Docs"
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
ms.date: 09/12/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 8cc44da0f61362018d2757da58ca4fb3a9a43764
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---
# <a name="partner-and-solutions-integration-in-azure-security-center"></a>Azure 安全中心的合作伙伴和解决方案集成

本文介绍如何将 Azure 安全中心与合作伙伴集成，以增强总体安全性。 安全中心在 Azure 中提供集成体验，并充分利用 Azure 应用商店进行合作伙伴认证和计费。

## <a name="why-deploy-partner-solutions-from-security-center"></a>为什么从安全中心部署合作伙伴的解决方案

利用安全中心的合作伙伴集成的四个主要原因是：

- 易于部署。 安全中心建议大大降低了合作伙伴解决方案的部署难度。 使用默认设置和网络拓扑即可全自动执行部署过程。 另外，客户也可以选择半自动选项，这样更灵活，并且可以进行自定义。
- 集成检测。 自动收集、聚合合作伙伴解决方案中的安全事件，并将其作为安全中心警报和事件的一部分进行显示。 这些事件还与来自其他源的检测融合在一起，以提供高级威胁检测功能。
- 统一的运行状况监视和管理。 客户可以使用集成式运行状况事件，一目了然地监视所有合作伙伴解决方案。 可通过使用合作伙伴解决方案轻松地访问高级设置，进行基本管理。
- 导出到 SIEM。 客户可以使用 Azure 日志集成（预览版）将所有安全中心警报和合作伙伴警报以通用事件格式 (CEF) 导出到本地安全信息和事件管理 (SIEM) 系统。


## <a name="partners-that-integrate-with-security-center"></a>与安全中心集成的合作伙伴

当前 Azure Marketplace 中提供的合作伙伴解决方案与安全中心的本机集成包括：

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

部署后，若要查看有关解决方案运行状况的信息和执行基本管理任务，请在“安全中心”仪表板中选择“合作伙伴解决方案”选项。

![合作伙伴解决方案集成](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

打开安全解决方案时看到的内容可能因基础结构而异。 以前面的图像为例，此页包含三个部分：

- **已连接解决方案**：显示已连接到安全中心的解决方案。
- **已发现解决方案**：显示未连接到安全中心的解决方案。 可以连接这些解决方案，然后这些解决方案将显示在“已连接解决方案”下。  如果安全中心未检测到任何未连接的解决方案，则会隐藏此部分。
- **添加数据源**：显示可添加到安全中心的 Azure 和非 Azure 数据源。

### <a name="connected-solutions"></a>已连接解决方案

“已连接解决方案”部分显示当前与安全中心连接的所有安全解决方案。 

![已连接解决方案](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

在每一项上看到的信息可能会因解决方案而异。 在每个磁贴上提供的信息可能包括：

- 合作伙伴的公司图标。  如果安全中心没有公司图标，则会显示伙伴名称的第一个字符。
- 解决方案类型。
- 可能显示计算机名。
- 运行状况。  如果未发送运行状况指示符，则安全中心将显示上次收到事件的日期和时间以指示设备是否正在报告。 如果安全中心未收到来自特定解决方案的运行状况指示符，此部分将不会显示解决方案磁贴。

> [!NOTE]
> “监视安全中心”显示上次收到事件的日期和时间以指示设备是否正在报告。 如果在过去 14 天内发送过警报或事件，未发送运行状况指示符的解决方案会显示为“已连接”。
>  

上述某些解决方案可能已完全集成到 Azure 中，而其他解决方案可能在本地。 由于安全中心支持[通用事件格式 (CEF)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-connect-products#what-is-cef)，因此它可以与使用 CEF 的解决方案连接，例如支持 CEF 的防火墙。 此解决方案添加到安全中心后，防火墙会将 CEF 格式的日志发送至安全中心，安全中心会将其发送到 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 进行处理。 防火墙是一种非 Azure 资源，它发送事件，但不发送运行状况指示器。  安全中心具有的关于运行状况的唯一信息是此设备上次发送事件的时间。  对于所有非 Azure 资源，安全中心在磁贴的运行状况区域显示上次收到事件的日期和时间，以指示该非 Azure 资源是否仍在报告。

### <a name="discovered-solutions"></a>已发现解决方案

“已发现解决方案”部分显示已通过 Azure 添加、安全中心建议连接到它的所有解决方案。

![已发现解决方案](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

安全中心可与内置 Azure 解决方案（如 [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)）集成。 如果有 Azure AD Identity Protection 许可证，但它未连接到安全中心，则 Azure AD Identity Protection 将在“已发现解决方案”下列出。 若要将此解决方案与安全中心集成，请单击 **Azure AD Identity Protection** 磁贴上的“连接”，将显示以下页：

![Azure AD Identity Protection](./media/security-center-partner-integration/security-center-partner-integration-fig6.png)

若要完成连接 Azure AD Identity Protection，需要选择要在其中保存数据的工作区。 Azure AD Identity Protection 中的所有数据将从此步骤中选择的工作区区域传递。  将通过工作区选择器选择工作区，数据将从该工作区开始传递。

若要连接到安全中心，必须是全局管理员或安全管理员。如果没有权限，“连接”按钮将禁用，并且如果没有权限，将显示一条消息说明为什么该按钮处于禁用状态。

Azure AD Identity Protection 警报将通过安全中心的检测管道，此管道使你能够从安全中心和 Azure Active Directory Identity Protection 收到警报。 安全中心将合并看起来与创建[安全事件](https://docs.microsoft.com/azure/security-center/security-center-incident)相关的所有警报。 安全事件说明将提供有关可疑活动的详细信息。

### <a name="add-data-sources"></a>添加数据源

可以添加要与安全中心集成的 Azure 和非 Azure 计算机。  添加非 Azure 计算机意味着可以添加本地计算机或支持 CEF 的设备。 

![数据源](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="see-also"></a>另请参阅

本文已经介绍了如何在 Azure 安全中心集成合作伙伴的解决方案。 若要详细了解安全中心，请参阅以下文章：

* [安全中心规划和操作指南](security-center-planning-and-operations-guide.md)
* [管理和响应安全中心的安全警报](security-center-managing-and-responding-alerts.md)
* [安全中心按类型划分的安全警报](security-center-alerts-type.md)
* [在安全中心进行安全运行状况监视](security-center-monitoring.md)。 了解如何监视 Azure 资源的运行状况。
* [通过安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)。 了解如何监视合作伙伴解决方案的运行状况。
* [Azure 安全中心常见问题解答](security-center-faq.md)。 获取服务使用方面的常见问题解答。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。

