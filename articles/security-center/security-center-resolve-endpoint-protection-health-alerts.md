---
title: "在 Azure 安全中心解决终结点保护运行状况警报 | Microsoft 文档"
description: "本文档介绍如何实现 Azure 安全中心建议**解决终结点保护运行状况警报**。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4050f453-98fc-4314-8438-d476469757fb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5e6b136d6bd3b11fb82126d104fd0cb149255118


---
# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>在 Azure 安全中心解决终结点保护运行状况警报
Azure 安全中心建议解决检测到的终结点保护运行状况警报。  通过安全中心可查看出现终结点保护故障的虚拟机 (VM) 以及故障的数量。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。 这并非一份循序渐进的指南。
> 
> 

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡中，选择“解决终结点保护运行状况警报”。
   ![解决终结点保护运行状况警报][1]
2. 这将打开边栏选项卡“终结点保护故障”，该边栏选项卡会列出出现故障的 VM 以及每个 VM 的故障数量。 从列表中选择 VM。
   ![终结点保护故障][2]
3. 所选的 VM 会打开“故障列表”边栏选项卡，显示故障列表。 从列表中选择一个故障查看详细信息。 这会打卡一个包含所选故障的信息的边栏选项卡。
   ![故障列表][3]
   ![故障事件][4]

## <a name="see-also"></a>另请参阅
若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) - 了解建议如何帮助你保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) - 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md)-- 查找有关使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/) - 获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png



<!--HONumber=Nov16_HO3-->


