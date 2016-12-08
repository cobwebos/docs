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
ms.date: 11/28/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 0c946ce6a96f2e3644b9890dad5d60a35ad4bcb7
ms.openlocfilehash: 10184c52d532eb56e66212fafdea3d059b0c43e3


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

已部署在安全中心的合作伙伴解决方案可以轻松地从主安全中心仪表板中的“合作伙伴解决方案”磁贴进行访问：

![合作伙伴集成](./media/security-center-partner-integration/security-center-partner-integration-fig1.png)

若要根据安全中心建议部署新的合作伙伴解决方案，请执行以下步骤：

> [!NOTE]
> 以下示例中的步骤假定你有要使用 Web 应用程序防火墙保护的工作负荷。

1. 在安全中心仪表板中，单击“建议”磁贴。
2. 在“建议”边栏选项卡上，单击“添加 Web 应用程序防火墙”。
3. 在“添加 Web 应用程序防火墙”边栏选项卡下单击应用程序名称。
4. 在“添加 Web 应用程序防火墙”边栏选项卡上，单击“新建”。
5. “新建 Web 应用程序防火墙”边栏选项卡显示当前正在提供 Web 应用程序防火墙功能的合作伙伴的列表。
6. 选择相应的合作伙伴解决方案并执行步骤（这些步骤可能会因合作伙伴而异）。

此时的总体部署体验可能会因合作伙伴而异。 有关管理安全中心的合作伙伴解决方案的详细信息，请阅读[监视 Azure 安全中心的合作伙伴解决方案](security-center-partner-solutions.md)。

## <a name="see-also"></a>另请参阅
在本文档中，已经学习了如何在 Azure 安全中心中集成合作伙伴的解决方案。 要了解有关安全中心的详细信息，请参阅以下内容：

* [Azure Security Center Planning and Operations Guide](security-center-planning-and-operations-guide.md)
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Azure 安全中心按类型划分的安全警报](security-center-alerts-type.md)
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) （Azure 安全中心的安全运行状况监视）- 了解如何监视 Azure 资源的运行状况。
* [Monitoring partner solutions with Azure Security Center](security-center-partner-solutions.md) （通过 Azure 安全中心监视合作伙伴解决方案）- 了解如何监视合作伙伴解决方案的运行状况。
* [Azure Security Center FAQ](security-center-faq.md) （Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。



<!--HONumber=Nov16_HO5-->


