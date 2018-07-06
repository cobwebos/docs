---
title: Azure 安全中心就绪情况路线图 | Microsoft Docs
description: 本文档提供了 Azure 安全中心的就绪情况路线图。
services: security-center
documentationcenter: na
author: terrylan
manager: ndicola
editor: ''
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: yurid
ms.openlocfilehash: 8726aae7545809573b63f29cd8ef568c34aee5b4
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109165"
---
# <a name="azure-security-center-readiness-roadmap"></a>Azure 安全中心就绪情况路线图
本文档提供的就绪情况路线图有助于 Azure 安全中心入门。

## <a name="understanding-security-center"></a>了解安全中心
Azure 安全中心为 Azure、本地和其他云中运行的工作负载提供统一的安全管理和高级威胁防护功能。 

使用以下资源，开始使用安全中心。

文章
* [Azure 安全中心简介](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Azure 安全中心快速入门指南](https://docs.microsoft.com/azure/security-center/security-center-get-started)

视频
* [简介视频](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
* [安全中心的防护、检测和响应功能概述](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>规划和操作
若要充分利用安全中心，必须了解在需要进行安全操作、监视、管理和事件响应的情况下，组织中的不同个人或团队是如何使用服务的。

在规划和操作过程中，请参考以下资源。


文章
* [Azure 安全中心规划和操作指南](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

视频
* [使用安全中心进行混合云工作负荷保护](https://mva.microsoft.com/training-courses/hybrid-cloud-workload-protection-with-azure-security-center-18173?l=X4WqTA3jE_1106218965)

### <a name="onboarding-computers-to-security-center"></a>将计算机载入到安全中心
安全中心将自动检测未启用安全中心标准层的所有 Azure 订阅或工作区。 其中包括使用安全中心免费层的 Azure 订阅和未启用安全解决方案的工作区。

在载入过程中，请参考以下资源。

文章
* [载入到 Azure 安全中心标准层以增强安全性](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

视频
* [Azure Security Center Hybrid - Overview](https://youtu.be/NMa4L_M597k)（Azure 安全中心混合层 - 概述）

## <a name="mitigating-security-issues-using-security-center"></a>使用安全中心缓解安全问题
安全中心会自动收集、分析以及整合 Azure 资源、网络和所连合作伙伴解决方案（如，防火墙和终结点保护解决方案）的日志数据，检测真正的威胁并减少误报。

若要管理安全警报和保护资源，请参考以下资源。

文章    
* [在 Azure 安全中心进行安全运行状况监视](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [保护 Azure 安全中心中的虚拟机](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-recommendations)
* [保护 Azure 安全中心中的网络](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [保护 Azure 安全中心中的应用程序](https://docs.microsoft.com/azure/security-center/security-center-application-recommendations)
* [在 Azure 安全中心保护 Azure SQL 服务和数据](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


视频   
* [Mitigating Security Issues using Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)（使用 Azure 安全中心缓解安全问题）

### <a name="security-center-for-incident-response"></a>负责事件响应的安全中心
为降低成本并减少破坏，必须在攻击发生之前制定事件响应计划。 可在事件响应的不同阶段使用 Azure 安全中心。

若要了解如何将安全中心纳入事件响应流程中，请参考以下资源。

视频  
* [Azure Security Center in Incident Response](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)（Azure 安全中心在事件响应中的作业）
* [Respond quickly to threats with next-generation security operation, and investigation](https://youtu.be/e8iFCz5RM4g)（通过下一代安全操作和调查对威胁进行快速响应）

文章    
* [利用 Azure 安全中心进行事件响应](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [依据安全攻略自动进行响应](https://docs.microsoft.com/azure/security-center/security-center-playbooks)

## <a name="advanced-cloud-defense"></a>高级云防御

Azure VM 可以充分利用安全中心的高级云防御功能。 这些功能包括实时虚拟机 (VM) 访问和自适应应用程序控制。

若要了解如何在安全中心使用这些功能，请参考以下资源。

视频  
* [Azure Security Center – Just-in-Time VM Access](https://youtu.be/UOQb2FcdQnU)（Azure 安全中心 - 实时 VM 访问）
* [Azure Security Center - Adaptive Application Controls](https://youtu.be/wWWekI1Y9ck)（Azure 安全中心 - 自适应应用程序控制）

文章    
* [使用恰时功能管理虚拟机访问](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Azure 安全中心的自适应应用程序控制](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>动手活动

* [安全中心动手实验](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [安全中心的 Web 应用程序防火墙 (WAF) 建议攻略](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Azure Security Center Playbook: Security Alerts](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)（Azure 安全中心 Playbook：安全警报）

## <a name="additional-resources"></a>其他资源
* [安全中心文档页](https://docs.microsoft.com/azure/security-center/)
* [安全中心 REST API 文档页](https://msdn.microsoft.com/library/mt704034.aspx)
* [Azure 安全中心常见问题 (FAQ)](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [安全中心定价页](https://azure.microsoft.com/pricing/details/security-center/)
* [标识安全最佳做法](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices)
* [网络安全最佳实践](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)
* [PaaS 建议](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [合规性](https://www.microsoft.com/en-us/trustcenter/compliance/due-diligence-checklist)
* [OMS customers can now use Azure Security Center to protect their hybrid cloud workloads](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)（OMS 客户现在可以使用 Azure 安全中心来保护其混合云工作负荷）

## <a name="community-resources"></a>社区资源

* [安全中心 UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [安全中心社区论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



