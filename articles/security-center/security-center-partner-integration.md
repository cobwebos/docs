---
title: "Azure 安全中心的合作伙伴集成 | Microsoft 文档"
description: "本文档说明如何将 Azure 安全中心与合作伙伴集成，以增强 Azure 资源的总体安全性。"
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 5001cd47b6ee51967d1286414ccefedd8e7e7813
ms.openlocfilehash: 095b5c9d1a888a4061450234f80c52c5834fbf53


---
# <a name="partner-integration-in-azure-security-center"></a>Azure 安全中心的合作伙伴集成
本文档说明如何将 Azure 安全中心与合作伙伴集成，以增强总体安全性并在 Azure 中提供集成体验，同时利用 Azure 应用商店进行合作伙伴认证和计费。

## <a name="why-deploy-partners-solutions-from-security-center"></a>为什么从安全中心部署合作伙伴的解决方案？

利用安全中心的合作伙伴集成的四个主要原因是：

- **易于部署**：按照安全中心建议部署合作伙伴解决方案更轻松。 部署过程可使用默认配置和网络拓扑完全自动执行，或者客户可以选择一个半自动选项，以允许更灵活性地自定义配置。
- **集成检测**：自动收集、聚合合作伙伴解决方案中的安全事件，并将其作为安全中心警报和事件的一部分进行显示。 这些事件还与来自其他源的检测融合在一起，以提供高级威胁检测功能。
- **统一运行状况监视和管理**：利用集成运行状况事件，客户可以一目了然地监视所有合作伙伴解决方案。 可通过使用合作伙伴解决方案轻松地访问高级配置，进行基本管理。
- **导出到 SIEM**：客户现在可以使用 Microsoft Azure 日志集成（预览版）以 CEF 格式将所有安全中心和合作伙伴的警报导出到本地 SIEM 系统


## <a name="what-partners-are-integrated-with-security-center"></a>哪些合作伙伴已与安全中心集成？
安全中心当前与以下合作伙伴集成：

- 终结点保护 (Trend Micro)、 
- Web 应用程序防火墙（Barracuda、F5、Imperva 和即将推出的 Microsoft WAF 和 Fortinet）、 
- 下一代防火墙（Check Point、Barracuda 和即将推出的 Fortinet 和 Cisco）解决方案。 
- 漏洞评估（Qualys - 预览版）解决方案。 

随着时间推移，安全中心将扩展这些现有类别中的合作伙伴数量，并添加新类别。 

## <a name="how-to-deploy-a-partner-solution"></a>如何部署合作伙伴解决方案？

根据 Azure 环境的配置和定义的安全策略，安全中心可能会建议部署合作伙伴解决方案。 该建议将引导你完成选择和安装合作伙伴解决方案的过程。 此时的总体部署体验可能因解决方案的类型和合作伙伴而异。 有关详细信息，请参阅以下链接：

- [添加 Web 应用程序防火墙](security-center-add-web-application-firewall.md)
- [添加下一代防火墙](security-center-add-next-generation-firewall.md)
- [安装终结点保护](security-center-install-endpoint-protection.md)
- [未安装漏洞评估](security-center-vulnerability-assessment-recommendations.md)

## <a name="how-to-manage-partner-solutions"></a>如何管理合作伙伴解决方案？

部署合作伙伴解决方案后，可以查看有关解决方案运行状况的信息，以及通过安全中心主仪表板中的“合作伙伴解决方案”磁贴执行基本管理任务。 有关管理安全中心的合作伙伴解决方案的详细信息，请阅读[监视 Azure 安全中心的合作伙伴解决方案](security-center-partner-solutions.md)。

![合作伙伴集成](./media/security-center-partner-integration/security-center-partner-integration-fig1.png)


## <a name="see-also"></a>另请参阅
在本文档中，已经学习了如何在 Azure 安全中心中集成合作伙伴的解决方案。 要了解有关安全中心的详细信息，请参阅以下内容：

* [Azure Security Center Planning and Operations Guide](security-center-planning-and-operations-guide.md)
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Azure 安全中心按类型划分的安全警报](security-center-alerts-type.md)
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) （Azure 安全中心的安全运行状况监视）- 了解如何监视 Azure 资源的运行状况。
* [Monitoring partner solutions with Azure Security Center](security-center-partner-solutions.md) （通过 Azure 安全中心监视合作伙伴解决方案）- 了解如何监视合作伙伴解决方案的运行状况。
* [Azure Security Center FAQ](security-center-faq.md) （Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。



<!--HONumber=Dec16_HO1-->


