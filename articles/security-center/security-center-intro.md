---
title: 什么是 Azure 安全中心？| Microsoft Docs
description: 了解 Azure 安全中心及其主要功能和工作原理。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: terrylan
ms.openlocfilehash: 08102ce4caead003925aa600f4f7f005b1c336e0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29395643"
---
# <a name="what-is-azure-security-center"></a>什么是 Azure 安全中心？
Azure 安全中心跨混合云工作负荷提供统一的安全管理和高级威胁防护。 有了安全中心，即可对各种工作负荷应用安全策略、减少受到的威胁，以及检测和响应攻击。

为什么要使用安全中心？

- **集中式策略管理** - 通过集中管理所有混合云工作负荷中的安全策略，确保符合公司或法规安全要求。
- **持续安全评估** - 监视计算机、网络、存储和数据服务以及应用程序的安全，发现潜在的安全问题。
- **可行的建议** - 借助高优先级且可行的安全建议，在攻击者利用安全漏洞前修复这些漏洞。
- **高级云防御** - 降低威胁的方式，一是通过恰时访问管理端口，二是以允许列表的形式控制在 VM 上运行的应用程序。
- **划分警报和事件的优先级** - 通过对安全警报和事件划分优先级，首先关注最紧急的威胁。
- **集成式安全解决方案** - 收集、搜索并分析来自各种来源（包括已连接的合作伙伴解决方案）的安全数据。

**安全中心 - 概述**概述了 Azure 和非 Azure 工作负荷的安全状态，介绍了如何发现并评估工作负荷的安全性，以及如何发现和缓解风险。 可以通过内置的仪表板快速了解那些需要关注的安全警报和漏洞。

![概述][1]

## <a name="centralized-policy-management"></a>集中式策略管理
安全策略定义了工作负载的相应配置，有助于确保用户遵守公司或法规方面的安全要求。 在安全中心，可以定义策略并根据工作负荷类型或数据机密性对其进行调整。

安全中心策略由以下部分组成：

- **数据收集**：确定代理预配和安全[数据收集](security-center-enable-data-collection.md)设置。
- **安全策略**：通过编辑[安全策略](security-center-policies.md)，确定安全中心监视和建议的控制措施。
- **电子邮件通知**：确定安全联系人和[电子邮件通知](security-center-provide-security-contact-details.md)设置。
- **定价层**：定义“免费”层或“标准”层的[定价选择](security-center-pricing.md)。 所选的层确定可用于范围内资源的安全中心功能。

![安全策略][2]

有关详细信息，请参阅[安全策略概述](security-center-policies-overview.md)。

## <a name="continuous-security-assessment"></a>持续进行安全评估
安全中心会分析计算资源、虚拟网络、存储和数据服务以及应用程序的安全状态。 持续评估有助于发现潜在的安全问题，例如系统缺少安全更新，或者网络端口被公开。 在“预防”部分选择一个磁贴，以查看其详细信息，包括一系列资源以及已发现的漏洞。

![安全运行状况监视][3]

有关详细信息，请参阅[安全运行状况监视](security-center-monitoring.md)。

## <a name="actionable-recommendations"></a>可行的建议
安全中心会分析 Azure 和非 Azure 资源的安全状态，以识别潜在的安全漏洞。 一系列划分优先级的安全建议会指导你完成解决安全问题的过程。

![建议][4]

有关详细信息，请参阅[管理安全建议](security-center-recommendations.md)。

## <a name="just-in-time-vm-access"></a>恰时 VM 访问
通过对 Azure VM 上管理端口的恰时控制访问减小网络攻击面，显著减小在暴力和其他网络攻击下的曝光面。

![恰时 VM 访问][5]

指定用户连接到虚拟机的规则。 需要时，可以通过安全中心或 PowerShell 请求访问。 只要请求符合规则，就会在请求的时限内自动授予访问权限。

有关详细信息，请参阅[使用恰时功能管理虚拟机访问](security-center-just-in-time.md)。

## <a name="adaptive-application-controls"></a>自适应应用程序控制
通过应用适合特定 Azure 工作负载且由机器学习提供支持的允许列表建议，阻止恶意软件和其他不需要的应用程序。

![自适应应用程序控制][6]

查看以后，单击即可应用建议的应用程序允许列表规则（由安全中心生成），或者编辑已配置的规则。

有关详细信息，请参阅[自适应应用程序控制](security-center-adaptive-application.md)。

## <a name="prioritized-alerts-and-incidents"></a>划分警报和事件的优先级
安全中心使用高级分析和全局威胁智能来检测入侵攻击和入侵后的活动。 警报在划分优先级后分组到事件中，使你能够首先重点处理最严重的威胁。 也可创建自己的自定义安全警报。

![划分警报和事件的优先级][7]

可以通过视觉性的、交互性的调查体验来快速评估攻击的范围和影响，并使用预定义的或即席的查询更深入地探查安全数据。

有关详细信息，请参阅[管理和响应安全警报](security-center-managing-and-responding-alerts.md)。

## <a name="integrate-your-security-solutions"></a>集成安全解决方案
可以在安全中心收集、搜索并分析各种来源（包括网络防火墙等已连接的合作伙伴解决方案以及其他 Microsoft 服务）的安全数据。

![集成安全解决方案][8]

有关详细信息，请参阅[集成安全解决方案](security-center-partner-integration.md)。

## <a name="next-steps"></a>后续步骤

- 若要开始使用安全中心，需要具有 Microsoft Azure 订阅。 如果尚无订阅，可注册[免费试用版](https://azure.microsoft.com/free/)。
- 安全中心的“免费”定价层通过 Azure 订阅启用。 若要利用高级安全管理和威胁检测功能，必须升级到“标准”定价层。 标准层在前 60 天免费。 有关详细信息，请参阅[安全中心定价页](https://azure.microsoft.com/pricing/details/security-center/)。
- 如果现在已准备好启用安全中心标准版，请参阅[快速入门：将 Azure 订阅载入到安全中心标准版](security-center-get-started.md)，详细了解相关步骤。


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
