---
title: "Azure 操作安全性概述 | Microsoft Docs"
description: "本文提供 Azure 操作安全性的概述。"
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
ms.date: 07/27/2017
ms.author: tomsh
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: d51ba4a40ff39f84ccad28c953fe57c845542b38
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---

# <a name="azure-operational-security-overview"></a>Azure 操作安全性概述
Azure 操作安全性是指用户可用于在 Microsoft Azure 中保护其数据、应用程序和其他资产的服务、控件和功能。 [Azure 操作安全性](https://docs.microsoft.com/azure/security/azure-operational-security)是一个框架，它融合了通过 Microsoft 独有的各种功能获得的知识，包括 Microsoft 安全开发生命周期 (SDL)、Microsoft 安全响应中心计划以及对网络安全威胁形态的深刻认识。

Azure 操作安全性是指用户可用于在 Microsoft Azure 中保护其数据、应用程序和其他资产的服务、控件和功能。

本“Azure 操作安全性概述”文章重点介绍以下方面：

- Azure Operations Management Suite
-   Azure 安全中心
-   Azure 监视器
-   Azure 网络观察程序
-   Azure 存储分析
-   Azure Active Directory

## <a name="azure-operations-management-suite"></a>Azure Operations Management Suite
IT Operations 负责管理数据中心基础结构、应用程序和数据，包括这些系统的稳定性和安全性。 但是，若要获得日益增多的复杂 IT 环境的安全洞察信息，通常需要组织从多个安全性和管理系统收集数据。

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) 是 Microsoft 基于云的 IT 管理解决方案，有助于管理和保护本地和云基础结构。

OMS 是基于云的 IT 管理解决方案，其中包含许多产品/服务，例如 IT 自动化、安全性和符合性、Log Analytics 以及备份和恢复。 因此，它非常适合用于帮助管理和保护本地与云中的 IT 基础结构。

OMS 的核心功能由 Azure 中运行的一组服务提供。 每个服务提供特定的管理功能，可以组合这些服务来实现不同的管理方案。 其中包括：

-   Log Analytics
-   自动化
-   备份
-   Site Recovery

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) 通过将托管资源的数据收集到中心存储库来为 OMS 提供监视服务。 这些数据可能包括事件、性能数据或通过 API 提供的自定义数据。 收集后，可以分析、导出数据或针对它们发出警报。 使用这种方法可以整合来自各种源的数据，因此可将 Azure 服务中的服务合并到现有的本地环境。 此外，它还能将数据收集与针对该数据执行的操作明确区分开来，以便能够针对所有类型的数据执行所有操作。

### <a name="automation"></a>自动化
借助 Microsoft [Azure 自动化](https://docs.microsoft.com/azure/automation/automation-intro)，用户可以自动完成通常要在云环境和企业环境中执行的手动、长时间进行、易出错且重复性高的任务。 它可以节省时间，可以提高常规管理任务的可靠性，甚至可以将这些任务安排成按特定的时间间隔自动执行。 可以使用 Runbook 实现这些过程的自动化，或者使用 Desired State Configuration 实现配置管理的自动化。

### <a name="backup"></a>备份
[Azure 备份](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)是基于 Azure 的服务，可用于备份（或保护）和还原 Microsoft 云中的数据。 Azure 备份将现有的本地或异地备份解决方案替换为安全可靠、性价比高的云端解决方案。 Azure 备份提供多个组件，可将其下载并部署到适当计算机、服务器或云端。 依据要保护的内容选择部署的组件或代理。 无论是保护本地数据还是云端数据，所有 Azure 备份组件均可用于将数据备份到 Azure 的恢复服务保管库中。 请参阅 [Azure 备份组件表](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use)。

### <a name="site-recovery"></a>Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) 通过协调本地虚拟机和物理机到 Azure 或辅助站点的复制来提供业务连续性。 如果主站点不可用，可故障转移到辅助位置，使用户能够继续工作，系统恢复正常后可故障回复。 智能和有效的威胁检测。

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) 是 Microsoft 的综合性标识即服务 (IDaaS) 解决方案，它可以：

-   启用 IAM 作为云服务
-   提供中心访问管理、单一登录 (SSO) 及报告功能
-   支持应用程序库中[数千个应用程序](https://azure.microsoft.com/marketplace/active-directory/)（包括 Salesforce、Google Apps、Box、Concur 等）的集成访问管理。

Azure AD 还包含整套[标识管理功能](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports)，例如[多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)、[设备注册]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview)、[自助密码管理](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)、[自助组管理](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)、[特权帐户管理](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)、[基于角色的访问控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)、[应用程序使用情况监视](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)、[多样化审核](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)以及[安全监视和警报](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)。

借助 Azure Active Directory，为合作伙伴与客户（企业或消费者）发布的所有应用程序都具有相同的标识和访问管理功能。 这可让你大幅降低运营成本。

## <a name="azure-security-center"></a>Azure 安全中心
[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-get-started)有助于预防、检测和响应威胁，同时增加 Azure 资源的可见性和安全可控性。 该服务提供订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

在[安全中心](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine)可以查看虚拟机的安全设置和监视威胁，帮助保护 Azure 中的虚拟机数据。 安全中心可对虚拟机进行以下监视：

-   包含建议配置规则的操作系统 (OS) 安全设置
-   缺少的系统安全更新和关键更新
-   终结点保护建议
-   磁盘加密验证
-   基于网络的攻击

Azure 安全中心使用[基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) 提供可在 Azure 中分配给用户、组和服务的[内置角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)。

安全中心会评估资源的配置以识别安全问题和漏洞。 如果分配有资源所属的订阅或资源组的“所有者”、“参与者”或“读取者”角色，则仅可在安全中心看到与资源相关的信息。

>[!Note]
>若要深入了解安全中心中的角色和允许的操作，请参阅 [Azure 安全中心中的权限](https://docs.microsoft.com/azure/security-center/security-center-permissions)。

安全中心使用 Microsoft Monitoring Agent - Operations Management Suite 和 Log Analytics 服务同样使用此代理。 通过此代理收集的数据存储在与 Azure 订阅关联的现有 Log Analytics [工作区](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access)或新工作区中，具体取决于 VM 的地理位置。

## <a name="azure-monitor"></a>Azure 监视器
云应用中的性能问题可能会影响业务。 使用多个互连的组件和频繁发布版本时，性能随时可能会下降。 开发一款应用后，用户通常会发现其中的问题，而你在测试时却找不到这样的问题。 应该立即知道这些问题，并使用工具来诊断和解决问题。

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) 是用于监视 Azure 中运行的服务的基本工具。 它可以提供有关服务吞吐量和周边环境的基础结构级数据。 如果在 Azure 中管理所有应用，并想要确定是否需要扩展或缩减资源，则 Azure Monitor 可以提供初始信息。

此外，还可以利用监视数据深入了解应用程序的情况。 了解这些情况有助于改进应用程序的性能或可维护性，或者实现本来需要手动干预的操作的自动化。 其中包括：

-   Azure 活动日志
-   Azure 诊断日志
-   度量值
-   Azure 诊断

### <a name="azure-activity-log"></a>Azure 活动日志
该日志方便用户了解对订阅中的资源执行的操作。 [活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)此前称为“审核日志”或“操作日志”，因为它报告订阅的控制平面事件。

### <a name="azure-diagnostic-logs"></a>Azure 诊断日志
[Azure 诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)由资源发出，提供与该资源的操作相关的各种频繁生成的数据。 这些日志的内容因资源类型而异。

例如，Windows 事件系统日志是适用于 VM 的一个诊断日志类别，而 Blob、表和队列日志是适用于存储帐户的诊断日志类别。

诊断日志不同于[活动日志（以前称为审核日志或操作日志）](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)。 活动日志提供针对订阅中的资源执行的操作的深入信息。 诊断日志提供资源本身执行的操作的深入信息。

### <a name="metrics"></a>度量值
在 Azure 监视器中可以使用遥测来查看 Azure 上的工作负荷的性能与运行状况。 最重要的 Azure 遥测数据类型是大多数 Azure 资源发出的指标（也称为性能计数器）。 Azure Monitor 提供多种方式来配置和使用这些[指标](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)，以便进行监视与故障排除。

### <a name="azure-diagnostics"></a>Azure 诊断
这是 Azure 中可对部署的应用程序启用诊断数据收集的功能。 可以使用各种不同源的诊断扩展。 目前支持的有 [Azure 云服务 Web 和辅助角色](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service)、运行 Microsoft Windows 的 [Azure 虚拟机](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service)，以及 [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)。


## <a name="network-watcher"></a>网络观察程序
客户在 Azure 中通过协调与组建 VNet、ExpressRoute、应用程序网关、负载均衡器等各种网络资源来构建端到端网络。 监视适用于每个网络资源。

端到端网络可能采用了复杂的配置并要求在资源之间交互，创建的复杂方案需要通过网络观察程序进行基于方案的监视。

[网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)会简化 Azure 网络的监视和诊断。 使用网络观察程序提供的诊断和可视化工具可以在 Azure 虚拟机上创建远程数据包捕获，使用流日志洞察网络流量，以及诊断 VPN 网关和连接。

网络观察程序目前提供以下功能：

- [拓扑](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) - 提供网络级视图，显示资源组中网络资源之间的各种互连和关联。
-   [可变数据包捕获](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) - 捕获传入和传出虚拟机的数据包数据。 高级筛选选项和精细控制（例如设置时间与大小限制）提供了多样性。 数据包数据可以存储在 Blob 存储中，或者以 .cap 格式存储在本地磁盘上。
-   [IP 流验证](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) - 根据流信息和 5 元组数据包参数（目标 IP、源 IP、目标端口、源端口和协议）检查数据包是被允许还是被拒绝。 如果数据包被安全组拒绝，则返回拒绝数据包的规则和组。
-   [下一跃点](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) - 确定 Azure 网络结构中路由的数据包的下一跃点，以便诊断任何错误配置的用户定义路由。
-   [安全组视图](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) - 获取在 VM 上应用的有效安全规则。
-   NSG 流日志记录[](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) - 使用网络安全组的流日志可以捕获被组中的安全规则允许或拒绝的流量的相关日志。 流由 5 元组信息（源 IP、目标 IP、源端口、目标端口和协议）定义。
-   [虚拟网络网关和连接故障排除](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) - 提供排查虚拟网络网关和连接问题的功能。
-   [网络订阅限制](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) - 用于查看网络资源用量与限制。
-   [配置诊断日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) – 提供单个窗格来为资源组中的网络资源启用或禁用诊断日志。

若要详细了解如何配置网络观察程序，请参阅[配置网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)。

## <a name="developer-operations-devops"></a>开发运营 (DevOps)
在开发 DevOps 应用程序之前，团队需要负责收集软件程序的业务要求和编写代码。 然后，独立的 QA 团队会在隔离的开发环境中测试程序，如果满足要求，则发放代码供运营部门部署。 部署团队进一步划分为孤立的小组，例如网络和数据库小组。 每次将软件程序投放到独立的团队时，就会增加一些瓶颈。

[DevOps](https://www.visualstudio.com/learn/what-is-devops/) 可让团队更快、更经济地交付更安全、更优质的解决方案。 使用软件和服务时，客户期望可以获得动态可靠的体验。  团队必须快速迭代软件更新，衡量更新造成的影响，然后通过新的开发迭代快速响应，以解决问题或提供更高的价值。  Microsoft Azure 等云平台消除了传统的瓶颈，帮助将基础结构商品化。 由于关键的优势以及在营收中的份量，软件在每家企业中都占据主导地位。 没有任何组织、开发人员或者 IT 工作人员可以或者应该避免 DevOps 的活动。

成熟的 DevOps 从业者会采用以下多种做法。 这些做法涉及到基于业务方案构建策略的[人员](https://www.visualstudio.com/learn/what-is-devops-culture/)。  工具可以帮助自动完成各种做法：

-   [敏捷的规划和项目管理](https://www.visualstudio.com/learn/what-is-agile/)技巧用于规划工作并将其划分到小组、管理团队效能，以及帮助团队快速适应不断变化的业务需求。
-   [版本控制（通常使用 Git 实现）](https://www.visualstudio.com/learn/what-is-git/)可让世界各地的团队共享资源，以及集成用于自动化发行管道的软件开发工具。
-   [持续集成](https://www.visualstudio.com/learn/what-is-continuous-integration/)能够推动代码的持续合并与测试，帮助提前发现缺陷。  其他优势包括减少应对合并问题所浪费的时间，以及快速获得开发团队的反馈。
-   向开发和测试环境[持续交付](https://www.visualstudio.com/learn/what-is-continuous-delivery/)软件解决方案可帮助组织快速修复 bug，应对不断变化的业务要求。
-   运行中应用程序的[监视](https://www.visualstudio.com/learn/what-is-monitoring/)（包括生产环境中应用程序的运行状况）以及客户使用情况信息可帮助组织建立假设，并快速验证或推翻策略。  以各种日志格式捕获和存储丰富的数据。
-   [基础结构即代码 (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) 实务可以自动化并验证网络和虚拟机的创建与解除流程，帮助交付安全、稳定的应用程序托管平台。
-   利用[微服务](https://www.visualstudio.com/learn/what-are-microservices/)体系结构将业务用例隔离到小型可重用的服务。  此体系结构提高了可伸缩性和效率。

## <a name="next-steps"></a>后续步骤
若要了解有关 OMS 安全和审核解决方案的详细信息，请参阅以下文章：

- [Operations Management Suite | 安全性和符合性](https://www.microsoft.com/cloud-platform/security-and-compliance)。
- [监视和响应 Operations Management Suite 安全和审核解决方案中的安全警报](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-responding-alerts)。
- [监视 Operations Management Suite 安全和审核解决方案中的资源](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-monitoring-resources)。

