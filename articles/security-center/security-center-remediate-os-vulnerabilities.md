---
title: "修正 Azure 安全中心中的 OS 漏洞 | Microsoft 文档"
description: "本文档演示如何实现 Azure 安全中心建议**修正 OS 漏洞**。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 455b841af8d736c273ddac4a90e024cfa7771c43


---
# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>修正 Azure 安全中心中的 OS 漏洞
Azure 安全中心每日分析虚拟机 (VM) 操作系统 (OS) 的配置（该配置使 VM 更易受到攻击），并建议配置更改以解决这些漏洞。 有关受监视的具体配置的详细信息，请参阅[建议的配置规则列表](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。 当 VM 的 OS 配置与建议的配置规则不匹配时，安全中心建议你解决漏洞。

> [!NOTE]
> 本文档通过使用示例部署介绍该服务。  这并非一份循序渐进的指南。
> 
> 

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡中，选择“修正 OS 漏洞”。
   ![修正 OS 漏洞][1]
   
    “**修正 OS 漏洞**”边栏选项卡打开并列出了你的 VM 与建议的配置规则不匹配的 OS 配置。  对于每个 VM，边栏选项卡标识：
   
   * **失败的规则** - VM 的 OS 配置失败的规则数。
   * **上次扫描时间** - 安全中心上次扫描 VM 的 OS 配置的日期和时间。
   * **状态** - 漏洞的当前状态：
     
     * 未解决：漏洞尚未得到处理
     * 正在进行：漏洞目前已被应用，不需用户采取行动
     * 已解决：已解决漏洞（问题解决后，条目灰显）
   * **严重性** - 所有漏洞都设置为低严重性，意味着漏洞应当予以解决，但不需要立即引起注意。

选择 VM。 该 VM 的边栏选项卡将打开并显示失败的规则。
   ![失败的配置规则][2]

选择规则。 在此示例中，请选择“密码必须满足复杂性要求”。 此时将打开边栏选项卡，描述失败的规则和产生的影响。 查看详细信息，考虑如何应用操作系统配置。
  ![失败的规则的说明][3]

  安全中心使用通用配置枚举 (CCE) 来分配配置规则的唯一标识符。 在此边栏选项卡上提供以下信息：

* NAME - 规则的名称
* SEVERITY - CCE 严重性值（紧急、重要或警告）
* CCIED - 规则的 CCE 唯一标识符
* DESCRIPTION - 规则的说明
* VULNERABILITY - 如果不应用规则，对于出现的漏洞或风险的解释
* IMPACT - 应用规则后产生的业务影响
* EXPECTED VALUE - 安全中心针对规则分析 VM OS 配置时的预期值
* RULE OPERATION - 在安全中心针对规则分析 VM OS 配置过程中使用的规则操作
* ACTUAL VALUE - 在针对规则分析 VM OS 配置后返回的值
* EVALUATION RESULT - 分析的结果：传递、失败

## <a name="see-also"></a>另请参阅
本文档演示如何实现安全中心建议“修正 OS 漏洞”。 可以在[此处](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)查看配置规则的集。 安全中心使用 CCE（通用配置枚举）来分配配置规则的唯一标识符。 请访问 [CCE](http://cce.mitre.org) 站点以了解详细信息。

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md)了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) -- 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png



<!--HONumber=Dec16_HO2-->


