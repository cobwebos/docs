---
title: 什么是 Azure 安全中心？| Microsoft Docs
description: 了解 Azure 安全中心及其主要功能和工作原理。
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: bd0e517845b9cfcbe6090dff8d656edcca782c83
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126286"
---
# <a name="what-is-azure-security-center"></a>什么是 Azure 安全中心？
Azure 安全中心跨混合云工作负荷提供统一的安全管理和高级威胁防护。 有了安全中心，即可对各种工作负荷应用安全策略、减少受到的威胁，以及检测和响应攻击。

为什么要使用安全中心？

- **集中式策略管理** - 通过集中管理所有混合云工作负荷中的安全策略，确保符合公司或法规安全要求。
- **持续安全评估** - 监视计算机、网络、存储和数据服务以及应用程序的安全状态，发现潜在的安全问题。
- **可行的建议** - 借助高优先级且可行的安全建议，在攻击者利用安全漏洞前修复这些漏洞。
- **划分警报和事件的优先级** - 通过对安全警报和事件划分优先级，首先关注最紧急的威胁。
- **高级云防御** -通过对管理端口的及时访问，加上 VM 上运行的自适应应用程序控制措施，使威胁得到缓解。
- **集成式安全解决方案** - 收集、搜索并分析来自各种来源（包括已连接的合作伙伴解决方案）的安全数据。

“安全中心 - 概述”简要介绍了 Azure 和非 Azure 工作负载的安全状态，让你能够了解并查看工作负载的安全性并确定和缓解风险。 可通过内置的仪表板快速了解需要关注的安全警报和漏洞。

![概述][1]

## <a name="centralized-policy-management"></a>集中式策略管理
“策略和符合性”部分（见上文）简要介绍了订阅覆盖范围、策略符合性和安全状态。

### <a name="subscription-coverage"></a>订阅覆盖范围
此部分显示了你可访问（读取或写入）的订阅总数，以及运行订阅的安全中心覆盖级别（标准或免费）：

- **已覆盖（标准）** - 所覆盖的订阅在安全中心提供的最大保护级别下运行
- **已覆盖（免费）** - 所覆盖的订阅在安全中心提供的有限、免费保护级别下运行
- **未覆盖** - 此状态下的订阅不受安全中心监视

选择图表即可打开“覆盖率”窗口。 选择一个选项卡（“未覆盖”、“基本覆盖”或“标准覆盖”）即可显示每个状态下的订阅的列表。 在某一选项卡下选择一个订阅即可显示有关该订阅的其他信息。 通过此信息，你可确定订阅的所有者并与其进行联系，以启用安全中心或增加该订阅的覆盖率。

![安全中心覆盖率][9]

### <a name="policy-compliance"></a>策略符合性
策略符合性由所分配的所有策略的符合性因素而定。 管理组、订阅或工作区的总体符合性得分由分配项的平均值进行加权。 加权平均值取决于单个分配中的策略数以及该分配应用于的资源数。

例如，如果订阅有两台 VM 和一个分配有 5 个策略的计划，则订阅中有 10 项评估。 如果其中一台 VM 不符合其中的两个策略，则订阅的分配的总体符合性得分为 80%。

此部分显示你的总体符合性比率以及符合条件的最小订阅数。 选择“显示环境的策略符合性”会打开“策略管理”窗口。 “策略管理”显示了管理组、订阅和工作区的层次结构。 可在此处选择订阅或管理组，对安全策略进行管理。

![策略管理][10]

安全策略定义了工作负载的相应配置，有助于确保用户遵守公司或法规方面的安全要求。 可在安全中心定义策略，并根据工作负载类型或数据的机密性对其进行调整，从而确定安全中心监视和推荐的具体控件。 可在安全中心单击管理组或订阅，对安全策略进行编辑。 还可使用 Azure Policy 创建新的定义、定义其他策略并跨管理组分配策略。

选择“编辑设置”可编辑订阅、管理组、资源组和工作区级别的下述安全中心设置：

- **数据收集**：确定代理预配和安全[数据收集](security-center-enable-data-collection.md)设置。
- **电子邮件通知**：确定安全联系人和[电子邮件通知](security-center-provide-security-contact-details.md)设置。
- **定价层**：定义“免费”层或“标准”层的[定价选择](security-center-pricing.md)。 所选的层确定可用于范围内资源的安全中心功能。
- **编辑安全配置**：可查看并修改经过安全中心评估的操作系统配置，从而发现潜在的安全漏洞。

有关详细信息，请参阅[集成安全中心安全策略与Azure Policy](security-center-azure-policy.md)。

### <a name="manage-and-govern-your-security-posture"></a>管理并治理安全状态
“策略和符合性”下仪表板的右侧显示了可立即采取以改善整体安全状态的见解。 示例如下：

- 定义并分配安全中心策略，以查看和跟踪安全标准遵守情况
- 让安全中心警报适用于 SIEM 连接器
- 一段时间内的策略符合性

## <a name="continuous-security-assessment"></a>持续进行安全评估
“安全中心 - 概述”下的“资源安全机制”部分简要介绍了资源的安全机制，显示了所标识的问题数量以及每个资源类型的安全状态。 持续评估有助于发现潜在的安全问题，例如系统缺少安全更新，或者网络端口被公开。

### <a name="secure-score"></a>安全功能分数
Azure 安全中心的安全功能分数对安全建议进行评审并排列优先级，让你知道要先执行哪些建议，从而帮助你找到最严重的安全漏洞，让你能够设定调查的优先顺序。 安全功能分数是一种度量工具，可帮助你加强安全，实现安全的工作负载。 有关详细信息，请参阅 [Azure 安全中心的安全功能分数](security-center-secure-score.md)。

### <a name="health-monitoring"></a>运行状况监视
在“资源运行状况监视”下选择资源类型后，将显示包含已确定的资源和所有漏洞的列表。 资源类型包括计算和应用程序、网络、数据和存储，以及标识和访问。

我们选择了“计算和应用”。 “计算”下面有四个选项卡：

- **概述**：安全中心识别的监视问题和建议。
- **VM 和计算机**：列出 VM 和计算机及其当前安全状态。
- **云服务**：列出安全中心监视的 Web 角色和辅助角色。
- **应用服务(预览版)**：列出 Web 应用程序、应用服务环境，以及每个应用和环境的当前安全状态。

![安全运行状况监视][3]

有关详细信息，请参阅[安全运行状况监视](security-center-monitoring.md)。

## <a name="actionable-recommendations"></a>可行的建议
安全中心会分析 Azure 和非 Azure 资源的安全状态，以识别潜在的安全漏洞。 在“资源”下选择“建议”后，将显示一系列已排列优先级的安全建议，可指导你完成解决安全问题的过程。

![建议][4]

有关详细信息，请参阅[管理安全建议](security-center-recommendations.md)。

### <a name="most-prevalent-recommendations"></a>最常见的建议
“资源”下仪表板的右侧提供了一个列表，其包含针对最大资源数的最常见建议。 最常见的建议凸显了你应关注哪些方面。 选择右箭头将显示最具影响力的建议。

![最常见的建议][11]

这是你的环境中最具影响力的建议。 实施此建议将最大程度提高符合性。 在本例中，推荐“应用磁盘加密”。 如果选择“提高符合性”，将显示建议的说明，以及一个包含受影响资源的列表。

![应用磁盘加密][12]

## <a name="threat-protection"></a>威胁防护
此区域显示了在你的资源上检测到的安全警报以及这些警报的安全级别。

### <a name="prioritized-alerts-and-incidents"></a>划分警报和事件的优先级
安全中心使用高级分析和全局威胁智能来检测入侵攻击和入侵后的活动。 警报在划分优先级后分组到事件中，使你能够首先重点处理最严重的威胁。 也可创建自己的自定义安全警报。

如果选择“按严重性排列的安全警报”或“一段时间内的安全警报”，将显示警报的详细信息。

![划分警报和事件的优先级][7]

可以通过视觉性的、交互性的调查体验来快速评估攻击的范围和影响，并使用预定义的或即席的查询更深入地探查安全数据。

有关详细信息，请参阅[管理和响应安全警报](security-center-managing-and-responding-alerts.md)。

仪表板的右侧会显示相关信息，帮助你设定要先处理哪些警报和了解常见漏洞的位置：

- **最常被攻击的资源**：具有最多警报数的特定资源
- **最常见的警报**：影响最多资源的警报类型

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
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
[9]: ./media/security-center-intro/coverage.png
[10]: ./media/security-center-intro/policy-management.png
[11]: ./media/security-center-intro/highest-impact.png
[12]: ./media/security-center-intro/apply-disk-encryption.png
