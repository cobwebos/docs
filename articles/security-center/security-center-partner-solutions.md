---
title: "管理 Azure 安全中心的合作伙伴解决方案 | Microsoft 文档"
description: "本文档将对 Azure 安全中心如何让你轻松监视与 Azure 订阅集成的合作伙伴解决方案的运行状态进行指导。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 6b7d2da8ababba65146503ecfbe6fd6e142a359c
ms.openlocfilehash: 2d7ab8fbed1d4edb60416c7c6b7ae08ddbdec91a


---
# <a name="monitoring-partner-solutions-with-azure-security-center"></a>通过 Azure 安全中心监视合作伙伴解决方案
本文档指导你完成对 Azure 安全中心中合作伙伴解决方案的运行状态的监视。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。 这并非一份循序渐进的指南。
>
>

## <a name="monitoring-partner-solutions"></a>监视合作伙伴解决方案
可通过“安全中心”边栏选项卡的“合作伙伴解决方案”磁贴轻松监视与 Azure 订阅集成的合作伙伴解决方案的运行状态。

![“合作伙伴解决方案”磁贴][1]

“合作伙伴解决方案”磁贴显示合作伙伴解决方案的数量和这些解决方案的状态摘要。

合作伙伴解决方案的“状态”可能为：

* 受保护（绿色）- 没有运行状况问题。
* 不正常（红色）- 有一个运行状况问题需要立即注意。
* 停止报告（橙色）- 解决方案已停止报告其运行状况。
* 未知的保护状态（橙色）- 由于向现有解决方案添加新资源的过程失败，此时解决方案的运行状况未知。
* 未报告（灰色）- 解决方案尚未报告任何内容，但如果它已连接且仍在部署，则可能不会报告解决方案的状态。

如果没有与订阅集成的解决方案，磁贴将表明没有任何解决方案。 可以通过选择“合作伙伴解决方案”磁贴打开“建议”边栏选项卡来部署合作伙伴安全解决方案。

![没有合作伙伴解决方案][2]

查看合作伙伴解决方案的运行状况：

1. 选择“合作伙伴解决方案”磁贴。 会打开一个边栏选项卡，其中显示连接到安全中心的合作伙伴解决方案的列表。

   ![合作伙伴解决方案][3]
2. 选择一个合作伙伴解决方案。 此示例中选择的是 **F5-WAF2** 解决方案。  会打开一个边栏选项卡，其中显示合作伙伴解决方案和与该解决方案相关联的资源的状态。 选择“解决方案控制台”以打开此解决方案的合作伙伴管理体验。

   ![合作伙伴解决方案详细信息][4]
3. 返回到“F5-WAF2”边栏选项卡，然后选择“链接应用”。 “链接应用程序”边栏选项卡随即打开。 此处，可将资源连接到合作伙伴解决方案。

   ![将资源链接到合作伙伴解决方案][5]

## <a name="see-also"></a>另请参阅
在本文档中，已向你介绍安全中心的“合作伙伴解决方案”磁贴。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) - 了解建议如何帮助保护 Azure 资源。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) （Azure 安全中心的安全运行状况监视）- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) — 了解如何管理和响应安全警报。
* [Azure Security Center FAQ](security-center-faq.md) （Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/) — 获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png



<!--HONumber=Feb17_HO1-->


