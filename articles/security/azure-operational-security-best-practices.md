---
title: "Azure 操作安全性最佳做法 | Microsoft Docs"
description: "本文提供有关 Azure 操作安全性的一套最佳做法。"
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: ed3c4bf2dcdda3e04a18682da568ae73f41bb37d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-operational-security-best-practices"></a>Azure 操作安全性最佳做法
Azure 操作安全性是指用户可用于在 Microsoft Azure 中保护其数据、应用程序和其他资产的服务、控件和功能。 Azure 操作安全性建立在一个框架上，该框架融合了通过 Microsoft 独有的各种功能获得的知识，包括 Microsoft 安全开发生命周期 (SDL)、Microsoft 安全响应中心计划以及对网络安全威胁形态的深刻认识。

本文介绍一系列 Azure 数据库安全最佳做法。 这些最佳做法衍生自我们的 Azure 数据库安全经验和客户的经验。

对于每项最佳做法，本文将说明：
-   最佳实践是什么
-   为何要启用该最佳实践
-   如果无法启用该最佳实践，可能的结果是什么
- 如何学习启用最佳实践

这篇 Azure 操作安全性最佳做法以共识以及 Azure 平台功能和特性集（因为在编写本文时已存在）为基础。 看法和技术将随着时间改变，本文会定期更新以反映这些更改。

本文介绍的 Azure 操作安全性最佳做法包括：

-   监视、管理和保护云基础结构
-   管理标识和执行单一登录 (SSO)
-   跟踪请求、分析使用趋势和诊断问题
-   使用集中式监视解决方案监视服务
-   防范、检测和应对威胁
-   基于端到端方案的网络监视
-   使用经过验证的 DevOps 工具确保部署

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>监视、管理和保护云基础结构
IT Operations 负责管理数据中心基础结构、应用程序和数据，包括这些系统的稳定性和安全性。 但是，若要获得日益增多的复杂 IT 环境的安全洞察信息，通常需要组织从多个安全性和管理系统收集数据。

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) 是 Microsoft 基于云的 IT 管理解决方案，有助于管理和保护本地和云基础结构。

[OMS 安全和审核解决方案](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources)允许 IT 人员主动监视所有资源，这有助于最大程度地降低安全事件的影响。 OMS 安全和审核具有可用于监视资源的安全域。

有关 OMS 的详细信息，请参阅文章 [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx)。

为帮助防范、检测和应对威胁，[Operations Management Suite (OMS) 安全和审核解决方案](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)将收集并处理关于资源的数据，其中包括：

-   安全事件日志
-   Windows 事件跟踪 (ETW) 事件
-   AppLocker 审核事件
-   Windows 防火墙日志
-   高级威胁分析事件
-   基线评估的结果
-   反恶意软件评估的结果
-   更新/修补程序评估的结果
-   代理上显式启用的 syslog 流


## <a name="manage-identity-and-implement-single-sign-on"></a>管理标识和执行单一登录
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。

[Azure AD](https://azure.microsoft.com/services/active-directory/) 还包含整套[标识管理](https://docs.microsoft.com/azure/security/security-identity-management-overview)功能，例如[多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)、设备注册、自助密码管理、自助组管理、特权帐户管理、基于角色的访问控制、应用程序使用情况监视、多样化审核以及安全监视和警报。

以下功能可以帮助保护基于云的应用程序的安全，简化 IT 流程，削减成本，以及确保实现公司的合规目标：

-   用于云的标识和访问管理
-   简化用户对任意云应用程序的访问
-   保护敏感数据和应用程序
-   为员工启用自助服务
-   与 Azure Active Directory 集成

### <a name="identity-and-access-management-for-the-cloud"></a>用于云的标识和访问管理
Azure Active Directory (Azure AD) 是一种综合的[标识和访问管理云解决方案](https://www.microsoft.com/cloud-platform/identity-management)，提供一组可靠的功能用于管理用户和组。 它有助于保护对本地和云应用程序的访问，这些应用程序包括 Office 365 等 Microsoft Web 服务以及许多非 Microsoft 服务型软件 (SaaS) 应用程序。
若要了解如何在 Azure AD 中启用标识保护，请参阅[启用 Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable)。

### <a name="simplify-user-access-to-any-cloud-app"></a>简化用户对任意云应用程序的访问
[支持单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps)，简化用户从 Windows、Mac、Android 和 iOS 设备对数千个云应用程序的访问。 用户可以使用自己的公司凭据，从基于 Web 的个性化访问面板或移动应用启动应用程序。 借助 Azure AD 应用程序代理模块，打破 SaaS 应用程序限制并发布本地 Web 应用程序，进而提供高度安全的远程访问和单一登录功能。

### <a name="protect-sensitive-data-and-applications"></a>保护敏感数据和应用程序
启用 [Azure 多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)，通过提供一层额外的身份验证，防止在未经授权的情况下对本地和云应用程序进行访问。 使用安全监控、警报和基于机器学习的报告（它们可识别不一致的访问模式）保护业务和降低潜在威胁。

### <a name="enable-self-service-for-your-employees"></a>为员工启用自助服务
将一些重要任务（如重置密码以及创建和管理组）委托给你的员工。 通过 Azure AD [支持自助式密码修改](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)、重置以及自助式组管理功能。

### <a name="integrate-with-azure-active-directory"></a>与 Azure Active Directory 集成
将 [Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) 及任何其他本地目录扩展到 Azure AD，实现对基于云的所有应用程序的单一登录。 可从各种本地目录将用户属性自动同步到你的云目录。

若要了解有关 Azure Active Directory 集成和如何启用它的详细信息，请阅读文章[将本地目录与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)。

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>跟踪请求、分析使用趋势和诊断问题
[Azure 存储分析](https://docs.microsoft.com/azure/storage/storage-analytics)执行日志记录并为存储帐户提供指标数据。 可以使用此数据跟踪请求、分析使用情况趋势以及诊断存储帐户的问题。

默认为新存储帐户启用存储分析指标。 可以启用记录，配置两个指标并在 Azure 门户中记录；有关详细信息，请参阅[在 Azure 门户中监视存储帐户](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)。 还可以通过 REST API 或客户端库以编程方式启用存储分析。 使用“设置服务属性”操作分别为每项服务启用存储分析。

有关使用存储分析及其他工具来识别、诊断和排查 Azure 存储相关问题的深入指导，请参阅[监视、诊断和排查 Microsoft Azure 存储问题](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)。

若要了解有关 Azure Active Directory 集成和如何启用它的详细信息，请阅读文章[启用和配置存储分析](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN)。

## <a name="monitoring-services"></a>监视服务
云应用程序很复杂，包含很多移动部件。 监视可以为用户提供数据，确保应用程序始终处于健康运行状态。 监视还有助于避免潜在问题，或者解决过去的问题。 此外，还可以利用监视数据深入了解应用程序的情况。 了解这些情况有助于改进应用程序的性能或可维护性，或者实现本来需要手动干预的操作的自动化。

### <a name="monitor-azure-resources"></a>监视 Azure 资源
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) 是一款平台服务，提供用于监视 Azure 资源的单一源。 通过 Azure 监视器，可直观显示、查询、路由和存档来自 Azure 内部资源的度量和日志并对其采取操作。 可使用监视器门户边栏选项卡、[监视器 PowerShell Cmdlet](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)、[跨平台 CLI](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) 或 [Azure 监视器 REST API](https://msdn.microsoft.com/library/dn931943.aspx) 来处理此数据。

### <a name="enable-autoscale-with-azure-monitor"></a>使用 Azure Monitor 启用自动缩放
启用 [Azure Monitor 自动缩放](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started)仅适用于虚拟机规模集 (VMSS)、云服务、应用服务计划和应用服务环境。

### <a name="manage-roles-permissions-and-security"></a>管理角色权限和安全
很多团队需要严格[控制对监视数据和设置的访问](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security)。 例如，如果有专门负责监视的团队成员（支持工程师、DevOps 工程师），或者使用托管服务提供程序，则可能希望向他们授予仅访问监视数据的权限，同时限制其创建、修改或删除资源的能力。

这里介绍如何快速地将内置监视 RBAC 角色应用到 Azure 中的用户，或者为需要有限的监视权限的用户构建自己的自定义角色。 然后讨论与 Azure Monitor 相关资源的安全注意事项，以及如何限制对它们所含数据的访问。

## <a name="prevent-detect-and-respond-to-threats"></a>防范、检测和应对威胁
安全中心可以自动从 Azure 资源、网络以及连接的合作伙伴解决方案收集安全信息，对威胁进行检测。 分析该信息（通常需将多个来源的信息关联起来）即可确定威胁。 安全中心会对安全警报进行重要性分类，并提供威胁处置建议。

-   [配置 Azure 订阅的安全策略](https://docs.microsoft.com/azure/security-center/security-center-policies)。
-   利用[安全中心内的建议](https://docs.microsoft.com/azure/security-center/security-center-recommendations)帮助保护 Azure 资源。
-   查看并管理当前的[安全警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。

[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)有助于预防、检测和响应威胁，同时增加 Azure 资源的可见性和安全可控性。 它提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

安全中心提供易于使用且非常有效的威胁预防、检测和响应功能（内置于 Azure）。 主要功能包括：

-   了解云安全状态
-   控制云安全
-   轻松部署集成的云安全解决方案
-   检测威胁和快速响应

### <a name="understand-cloud-security-state"></a>了解云安全状态
使用 Azure 安全中心，可以获得所有 Azure 资源的安全状态的核心情况。 一眼就可验证适当的安全控件是否配置到位且配置正确，还可快速确认任何需要注意的资源。

### <a name="take-control-of-cloud-security"></a>控制云安全
根据公司的云安全需求，为你的 Azure 订阅定义[安全策略](https://docs.microsoft.com/azure/security-center/security-center-policies)，使其适应每个订阅中的应用程序类型或数据敏感性。 使用策略驱动型建议来指导资源所有者执行实现所需控制的过程，排除对云安全的猜测结果。

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>轻松部署集成的云安全解决方案
启用来自 Microsoft 及其合作伙伴的[安全解决方案](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)，包括行业领先的防火墙和反恶意软件。 使用精简的预配部署安全解决方案，甚至还为你配置了网络更改。 自动收集合作伙伴解决方案中的安全事件，以供分析和发出警报。

### <a name="detect-threats-and-respond-fast"></a>检测威胁和快速响应
凭借分析驱动型综合方法，保持先于当前云威胁和新出现的云威胁。 深入了解 Azure 部署中与云安全相关的事件，将其与 Microsoft 全球[威胁智能](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)和专业知识相结合，让安全中心帮助你及早检测到实际的威胁，减少错误判断。 云安全警报让你深入了解攻击活动（包括相关事件和受影响的资源），并建议快速解决问题和恢复的方法。

## <a name="end-to-end-scenario-based-network-monitoring"></a>基于端到端方案的网络监视
客户在 Azure 中通过协调与组建 VNet、ExpressRoute、应用程序网关、负载均衡器等各种网络资源来构建端到端网络。 监视适用于每个网络资源。

[网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 借助网络观察程序随附的网络诊断和可视化工具，可以了解、诊断和洞察 Azure 中的网络。

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>使用数据包捕获实现远程网络监视的自动化
使用网络观察程序监视和诊断网络问题，无需登录虚拟机 (VM)。 通过设置警报触发[数据包捕获](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture)，并获取数据包级别上的实时性能信息访问权限。 如果遇到问题，可进行详细调查，获得更精确的诊断。

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>使用流日志深入了解网络流量
使用[网络安全组流日志](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-overview)更深入地了解网络流量模式。 流日志提供的信息可帮助收集符合性数据、审核和监视网络安全配置文件。

### <a name="diagnose-vpn-connectivity-issues"></a>诊断 VPN 连接问题
利用网络观察程序可[诊断最常见的 VPN 网关和连接问题](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity)。 不仅能够标识问题，还可以使用创建的详细日志协助进一步调查。

若要了解有关如何配置和启用网络观察程序的详细信息，请阅读文章[配置网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)。

## <a name="secure-deployment-using-proven-devops-tools"></a>使用经过验证的 DevOps 工具确保部署
以下是 Microsoft 云空间中 Azure DevOps 实践列表的部分内容，这些内容可以使企业和团队多产且高效。

-   **基础结构即代码 (IaC)：**基础结构即代码是一组技术和实践，使 IT 专业人员不再需要日复一日地生成和管理模块化基础结构。 IT 专业人员生成和维护新式服务器环境的方式就像是软件开发人员生成和维护应用程序代码的方式。 在 Azure 中，通过 [Azure 资源管理器]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/)可以使用声明性模板预配应用程序。 在单个模板中，可以部署多个服务及其依赖项。 在应用程序生命周期的每个阶段，可使用相同模板重复部署应用程序。
-   **持续集成和部署：**可以将 Visual Studio Online 团队项目配置为[自动生成并部署](https://www.visualstudio.com/docs/build/overview)到 Azure Web 应用或云服务。 VSO 在每次代码签入后对 Azure 执行一次生成，然后自动部署二进制文件。 这里介绍的包生成过程与 Visual Studio 中的 Package 命令等效，而发布步骤与 Visual Studio 中的 Publish 命令等效。
-   **Release Management：**Visual Studio [Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) 是实现多阶段部署和管理发布过程自动化的极佳解决方案。 创建托管的持续部署管道，快速、轻松地频繁发布。 通过 Release Management，可以使发布过程自动化，还可以拥有预定义的批准工作流。 根据需要进行本地部署和部署到云、扩展和自定义。
-   **应用性能监视：**检测和解决问题，持续改进应用程序。 快速诊断实时应用程序中的任何问题。 了解用户使用它来做了些什么。 配置是添加 JS 代码和 webconfig 项的简单方式，只需几分钟即可在门户中看到结果和所有详细信息。[App Insights](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) 帮助企业更快检测问题和修正。
-   **负载测试和自动缩放：**可以发现应用中的性能问题，提高部署质量，确保应用始终保持运行或可用，以迎合业务需求。 确保应用可以处理下一次启动或市场营销活动的流量。 使用 Visual Studio Online 立即开始运行基于云的[负载测试](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)。

## <a name="next-steps"></a>后续步骤
- 了解有关 [Azure 操作安全性](https://docs.microsoft.com/azure/security/azure-operational-security)的详细信息。
- 了解有关 [Operations Management Suite | 安全性和符合性](https://www.microsoft.com/cloud-platform/security-and-compliance)的详细信息。
- [Operations Management Suite 安全和审核解决方案入门](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)。
