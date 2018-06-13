---
title: Azure 高级威胁检测 | Microsoft Docs
description: 了解标识保护及其功能。
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: eb1bf9f6465a8a94fd2de75f581817887c333289
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895312"
---
# <a name="azure-advanced-threat-detection"></a>Azure 高级威胁检测
## <a name="introduction"></a>介绍

### <a name="overview"></a>概述

Microsoft 制定了一系列白皮书、安全概述、最佳做法以及清单，以帮助 Azure 客户了解 Azure 平台中和外围提供的各种安全相关功能。 这些主题涵盖各类不同宽度和深度的内容，并且定期更新。 本文档属于下面摘要部分中总结的系列的一部分。

### <a name="azure-platform"></a>Azure 平台

Azure 是一个公有云服务平台，支持最为广泛的操作系统、编程语言、框架、工具、数据库和设备选择。
支持以下编程语言：
-   使用 Docker 集成运行 Linux 容器。
-   使用 JavaScript、Python、.NET、PHP、Java 和 Node.js 生成应用
-   生成适用于 iOS、Android 和 Windows 设备的后端。

Azure 公有云服务支持数百万开发人员和 IT 专业人士已经有所依赖并信任的相同技术。

如果迁移到某个组织的公有云，该组织负责保护数据，并为系统提供安全性和管理。

Azure 的基础结构（从设备到应用程序）经过设计，可同时托管数百万的客户，并为企业提供可靠的基础，使之能够满足其安全需求。 Azure 提供多种安全性配置和自定义选项，以满足应用部署的要求。 本文档可帮助满足这些要求。

### <a name="abstract"></a>摘要

Microsoft Azure 通过 Azure Active Directory、Azure Operations Management Suite (OMS) 和 Azure 安全中心等服务提供内置的高级威胁检测功能。 安全服务和功能的此集合提供了一种简单快速了解 Azure 部署运行状况的方法。

本白皮书在威胁漏洞诊断和分析与针对服务器和其他 Azure 资源的恶意活动相关的风险方面，提供了“Microsoft Azure 方法”指导。 这可帮助你确定由 Azure 平台以及面向客户的安全服务和技术提供的优化解决方案中的识别和漏洞管理方法。

本白皮书重点介绍 Azure 平台和面向客户的控件的技术，不涉及 SLA、定价模型以及 DevOps 实践注意事项。

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory 标识保护

![Azure Active Directory 标识保护](./media/azure-threat-detection/azure-threat-detection-fig1.png)


[Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 是 [Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) 版本中的一项功能，概述了影响组织标识的风险事件和潜在漏洞。 十多年来，Microsoft 一直在保护基于云的标识的安全，并通过 Azure AD Identity Protection 持续为企业客户提供类似的保护系统。 Identity Protection 使用现有的 Azure AD 异常检测功能（可通过 [Azure AD 的异常活动报告](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports)得到），并引入了新的可以检测实时异常的风险事件类型。

Identity Protection 使用自适应机器学习算法和启发式学习法来检测异常行为以及可能表示标识已遭入侵的风险事件。 Identity Protection 使用此数据生成报告和警报，使你能够调查这些风险事件并采取相应的补救措施或缓解措施。

但是，Azure Active Directory Identity Protection 不只是一个监视和报告工具。 Identity Protection 根据风险事件计算每个用户的用户风险级别，让你配置基于风险的策略来自动保护组织的标识。

除了 Azure Active Directory 与 [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) 提供的其他[条件性访问控制](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access)以外，这些基于风险的策略可以自动阻止或提供自适应补救措施，包括重置密码和强制实施多重身份验证。

### <a name="identity-protections-capabilities"></a>Identity Protection 的功能

Azure Active Directory Identity Protection 不只是一个监视和报告工具。 若要保护组织的标识，可以配置基于风险的策略，该策略可在达到指定风险级别时自动响应检测到的问题。 除了 Azure Active Directory 与 EMS 提供的条件性访问控制以外，这些策略也可以自动阻止或启用自适应补救措施，包括密码重置和强制实施多重身份验证。

Azure Identity Protection 可帮助保护帐户和标识的一些示例包括：

[检测风险事件和有风险的帐户：](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   使用机器学习和启发式规则检测 6 种风险事件类型
-   计算用户风险级别
-   提供自定义建议，通过突显漏洞来改善整体安全局势

[调查风险事件：](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   针对风险事件发送通知
-   使用相关的区分上下文的信息调查风险事件
-   提供基本工作流来跟踪调查
-   轻松使用补救措施，例如密码重置

[基于风险的条件性访问策略：](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   通过阻止登录或请求多重身份验证质询来缓解有风险登录的策略。
-   阻止或保护有风险用户帐户的策略
-   要求用户注册多重身份验证的策略

### <a name="azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM)

使用 [Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)，

![Azure AD 特权标识管理](./media/azure-threat-detection/azure-threat-detection-fig2.png)

可以管理、控制和监视组织内的访问。 这包括访问 Azure AD 和其他 Microsoft Online Services（如 Office 365 或 Microsoft Intune）中的资源。

Azure AD Privileged Identity Management 可帮助你实现以下操作：

-   获取有关 Azure AD 管理员以及对 Office 365 和 Intune 等 Microsoft Online Services 的“实时”管理访问的警报和报告

-   获取有关管理员访问历史记录以及管理员分配更改的报告

-   获取有关访问特权角色的警报

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)

[Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) 是 Microsoft 基于云的 IT 管理解决方案，有助于管理和保护本地和云基础结构。 由于 OMS 作为基于云的服务实现，因此在基础结构服务上进行极低的投资即可快速使其启动并运行。 自动提供新增安全功能，从而节省持续维护和升级成本。

除自行提供有价值的服务外，OMS 还可与 [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/) 等 System Center 组件集成，将现有安全管理投资扩展到云。 System Center 和 OMS 可协同工作来提供完整的混合管理体验。

### <a name="holistic-security-and-compliance-posture"></a>安全性与符合性总体情况

[OMS 安全和审核仪表板](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)借助内置搜索查询找到需要关注的重要问题，从而提供有关组织的 IT 安全态势的全面观点。 “安全和审核”仪表板是提供 OMS 中所有安全相关内容的主屏幕。 它提供计算机安全状态的高级洞见。 还允许查看过去 24 小时、7 天或任何自定义时间范围的所有事件。

OMS 仪表板有助于用户快速轻松了解任何环境中的总体安全情况，在 IT 操作的上下文中即可实现，这些操作包括：软件更新评估、反恶意软件评估和配置基线。 此外，可访问现成的安全日志数据，简化安全性和符合性审核过程。

OMS 安全和审核仪表板有四个主要类别：

![OMS 安全和审核仪表板](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

-   安全域：在此区域中可进一步了解：随时间推移的安全记录、恶意软件评估、更新评估、网络安全、身份和访问信息、具有安全事件的计算机，并快速访问 Azure 安全中心仪表板。

-   值得注意的问题：此选项允许快速标识未解决的问题数和这些问题的严重性。

-   检测（预览版）：当针对资源的安全警报出现时对其进行可视化，从而确定攻击模式。

-   威胁智能：对出站恶意 IP 通信的服务器总数、恶意威胁类型和显示恶意 IP 来源的地图进行可视化，从而确定攻击模式。

-   常见安全查询：此选项提供用来监视环境的最常见安全查询的列表。 单击其中一个查询时，将打开“搜索”边栏选项卡及该查询的结果。

### <a name="insight-and-analytics"></a>见解与分析
[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 的中心是托管在 Azure 云中的 OMS 存储库。

![见解与分析](./media/azure-threat-detection/azure-threat-detection-fig4.png)

通过配置数据源和向订阅添加解决方案，将连接的源中的数据收集到存储库。

![订阅](./media/azure-threat-detection/azure-threat-detection-fig5.png)

数据源和解决方案将分别创建具有自身属性集的不同记录类型，但是仍可在对存储库的查询中同时对它们进行分析。 这允许使用相同的工具和方法来处理不同资源收集的各种数据。


与 Log Analytics 的大部分交互都会通过 OMS 门户完成，该门户在任意浏览器中运行，并提供对配置设置和多个工具的访问权限，以对收集的数据进行分析和操作。 可以在该门户中使用[日志搜索](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches)，在此过程中，可以构造查询（分析收集的数据）、[仪表板](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards)（可以使用最有价值的搜索的图形视图自定义）和[解决方案](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)（提供其他功能和分析工具）。

![分析工具](./media/azure-threat-detection/azure-threat-detection-fig6.png)

解决方案向 Log Analytics 添加功能。 解决方案主要在云中运行，并提供对 OMS 存储库中收集的数据的分析。 解决方案也可以定义要收集的新记录类型，并使用日志搜索或通过 OMS 仪表板中的解决方案提供的其他用户界面进行分析。
安全性和审核是这些类型解决方案的一个示例。



### <a name="automation--control-alert-on-security-configuration-drifts"></a>自动化和控制：安全配置偏移警报

Azure 自动化通过基于 PowerShell 并在 Azure 云中运行的 Runbook 自动执行管理流程。 也可在本地数据中心内的服务器上运行 Runbook 以管理本地资源。 Azure 自动化通过 PowerShell DSC (Desired State Configuration) 提供配置管理。

![Azure 自动化](./media/azure-threat-detection/azure-threat-detection-fig7.png)

可以创建和管理在 Azure 中托管的 DSC 资源，并将其应用到云和本地的系统，从而定义并自动实施其配置，或获取有关偏移的报告以帮助确保安全配置符合策略要求。

## <a name="azure-security-center"></a>Azure 安全中心

Azure 安全中心可帮助保护 Azure 资源。 它为 Azure 订阅提供集成的安全监控和策略管理。 在该服务中，可以定义不仅针对 Azure 订阅，还针对[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)的策略，这样便可更加具体。

![Azure 安全中心](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Microsoft 安全研究人员始终在不断地寻找威胁。 得益于 Microsoft 在云中和本地的广泛存在，他们可以访问大量的遥测数据。 由于能够广泛访问和收集各种数据集，Microsoft 可以通过本地消费者产品和企业产品以及联机服务发现新的攻击模式和趋势。

因此，攻击者发布新的越来越复杂的方式时，安全中心就可以快速更新其检测算法。 此方法可帮助你始终与变化莫测的威胁环境保持同步。

![安全中心](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

安全中心可以自动从 Azure 资源、网络以及连接的合作伙伴解决方案收集安全信息，对威胁进行检测。  分析该信息（需将多个来源的信息关联起来）即可确定威胁。
安全中心会对安全警报进行重要性分类，并提供威胁处置建议。

安全中心使用各种高级安全分析，远不止几种基于攻击特征的方法。 可利用大数据和[机器学习](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)技术的突破跨整个云结构对事件进行评估，检测那些使用手动方式不可能发现的威胁，并预测攻击的发展方式。 此类安全分析包括以下内容。

### <a name="threat-intelligence"></a>威胁智能

Microsoft 提供大量的全球威胁情报。
遥测数据的来源包括：Azure、Office 365、Microsoft CRM Online、Microsoft Dynamics AX、outlook.com、MSN.com、Microsoft 数字犯罪部门 (DCU)、Microsoft 安全响应中心 (MSRC)。

![威胁智能](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

研究人员也会收到在主要的云服务提供者之间共享的威胁情报信息，以及通过第三方的威胁情报源订阅的此类信息。 Azure 安全中心可能会在分析该信息后发出警报，提醒用户注意来自行为不端攻击者的威胁。 示例包括：

-   利用机器学习的力量 - Azure 安全中心有权访问大量有关云网络活动的数据，这些数据可用于检测针对 Azure 部署的威胁。 例如：

-   暴力攻击检测 - 机器学习可用于创建远程访问尝试的历史模式，从而检测针对 SSH、RDP 和 SQL 端口的暴力攻击。

-   出站 DDoS 和僵尸网络检测 - 针对云资源的攻击的常见目标是使用这些资源的计算能力来执行其他攻击。

-   新行为分析服务器和 VM - 服务器或虚拟机受到攻击后，攻击者将使用各种各样的技术在该系统上执行恶意代码，同时避免检测、确保持久性和避免安全控件。

-   Azure SQL 数据库威胁检测 - Azure SQL 数据库威胁检测可以识别异常数据库活动，指示企图访问或使用数据库的异常的潜在有害尝试。

### <a name="behavioral-analytics"></a>行为分析

行为分析是一种技术，该技术会对数据进行分析并将数据与一系列已知模式对比。 不过，这些模式不是简单的特征， 需要对大型数据集运用复杂的机器学习算法来确定，

![行为分析](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)


或者由分析专家通过仔细分析恶意行为来确定。 Azure 安全中心可以使用行为分析对虚拟机日志、虚拟网络设备日志、结构日志、故障转储和其他资源进行分析，确定受攻击的资源。

此外，还可以通过与其他信号的关联性，查看是否存在某个广泛传播活动的支持证据。 此关联性也可用于确定那些符合已确定的攻击特征的事件。

示例包括：
-   执行可疑进程：为了执行恶意软件而不被检测到，攻击者会运用多种技巧。 例如，攻击者可能会为恶意软件取一个与合法的系统文件相同的名称，但却将这些文件置于其他位置，可能会使用与正常文件非常类似的名称，或者会掩盖文件的实际扩展名。 安全中心会对进程行为建模，监视进程的执行情况，检测此类异常行为。

-   隐藏恶意软件和利用尝试：复杂的恶意软件从不向磁盘写入内容，或者会加密存储在磁盘上的软件组件，借此逃避传统的反恶意软件产品的检测。 但是，此类恶意软件可以通过内存分析检测到，因为恶意软件一运行就必然会在内存中留下踪迹。 当软件故障时，故障转储可捕获故障时的部分内存。 通过分析故障转储中的内存，Azure 安全中心可以检测到用于利用软件漏洞、访问机密数据以及偷偷存留在受攻击计算机中而不影响计算机性能的技术。

-   横向移动和内部侦测：为了留存在受攻击的网络中以及查找/获取有价值的数据，攻击者通常会尝试从受攻击的计算机横向移动到同一网络中的其他计算机。 安全中心会监视进程和登录活动，从而发现是否有人尝试在网络中扩大攻击者据点，例如是否存在远程命令执行、网络探测及帐户枚举。

-   恶意 PowerShell 脚本：攻击者出于各种目的，使用 PowerShell 在目标虚拟机上执行恶意代码。 安全中心会检查 PowerShell 活动中是否存在可疑活动的证据。

-   传出攻击：攻击者通常会以云资源为目标，目的是使用这些资源发起更多攻击。 例如，可以通过受攻击的虚拟机对其他虚拟机发起暴力破解攻击，可以发送垃圾邮件，也可以扫描 Internet 上的开放端口和其他设备。 将机器学习应用到网络流量以后，安全中心即可检测到出站网络通信何时超出标准。 就垃圾邮件来说，安全中心也可将非正常的电子邮件流量与 Office 365 提供的情报信息关联起来，确定该邮件到底是恶意邮件，还是合法的电子邮件促销活动。

### <a name="anomaly-detection"></a>异常检测

Azure 安全中心也通过异常检测确定威胁。 与行为分析（依赖于已知的从大型数据集派生的模式）相比，异常检测更“个性化”，注重特定于用户部署的基线。 运用机器学习确定部署的正常活动，并生成规则，定义可能表示安全事件的异常条件。 下面是一个示例：

-   入站 RDP/SSH 暴力攻击：部署中的有些虚拟机可能很忙，每天需要处理大量的登录，而其他虚拟机可能只有寥寥数个登录。 Azure 安全中心可以确定这些虚拟机的基线登录活动，并通过机器学习定义正常登录活动。 如果与为登录相关特性定义的基线之间存在任何差异，则可能会生成警报。 同样，是否具有显著性由机器学习决定。

### <a name="continuous-threat-intelligence-monitoring"></a>连续威胁情报监视

Azure 安全中心与全世界的安全性研究和数据科学团队合作，持续监视威胁态势的变化情况。 其中包括以下计划：

-   威胁情报监视：威胁情报包括现有的或新出现的威胁的机制、指示器、影响和可操作建议。 此信息在安全社区共享，Microsoft 会持续监视内部和外部源提供的威胁情报源。

-   信号共享：安全团队的见解会跨 Microsoft 的一系列云服务和本地服务、服务器、客户端终结点设备进行共享和分析。

-   Microsoft 安全专家：持续接触 Microsoft 的各个工作在专业安全领域（例如取证和 Web 攻击检测）的团队。

-   检测优化：针对实际的客户数据集运行相关算法，安全研究人员与客户一起验证结果。 通过检出率和误报率优化机器学习算法。

将这些措施结合起来，形成新的改进型检测方法，使用户能够即时受益，而用户不需采取任何措施。

## <a name="advanced-threat-detection-features---other-azure-services"></a>高级威胁检测功能 - 其他 Azure 服务

### <a name="virtual-machine-microsoft-antimalware"></a>虚拟机：Microsoft 反恶意软件

适用于 Azure 的 [Microsoft 反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)是一个针对应用程序和租户环境所提供的单一代理解决方案，可在在后台运行而无需人工干预。 可以根据应用程序工作负荷的需求，选择默认的基本安全性或高级的自定义配置（包括反恶意软件监视）来部署保护。 Azure 反恶意软件是为 Azure 虚拟机提供的安全选项，自动安装在所有 Azure PaaS 虚拟机上。

用于部署和启用应用程序的 Microsoft 反恶意软件的 Azure 功能

#### <a name="microsoft-antimalware-core-features"></a>Microsoft 反恶意软件核心功能

-   实时保护 - 监视云服务中和虚拟机上的活动，以检测和阻止恶意软件的执行。

-   计划的扫描 - 定期执行有针对性的扫描，以检测恶意软件，包括主动运行的程序。

-   恶意软件消除 - 自动针对检测到的恶意软件采取措施，例如删除或隔离恶意文件以及清除恶意注册表项。

-   签名更新 - 自动安装最新的保护签名（病毒定义）以确保按预定的频率保持最新保护状态。

-   反恶意软件引擎更新 - 自动更新 Microsoft 反恶意软件引擎。

-   反恶意软件平台更新 - 自动更新 Microsoft 反恶意软件平台。

-   **主动保护** - 将检测到的威胁和可疑资源的遥测元数据报告给 Microsoft Azure，以确保针对不断演变的威胁局势做出快速响应，并通过 Microsoft Active Protection System (MAPS) 启用实时同步签名传递。

-   示例报告 - 将示例提供并报告给 Microsoft 反恶意软件服务，以帮助改善服务并实现故障排除。

-   排除项 - 允许应用程序和服务管理员配置特定的文件、进程与驱动器，以便出于性能和/或其他原因将其从保护和扫描中排除。

-   恶意软件事件收集 - 在操作系统事件日志中记录反恶意软件服务的运行状况、可疑活动及采取的补救措施，并将这些数据收集到客户的 Azure 存储帐户。

### <a name="azure-sql-database-threat-detection"></a>Azure SQL 数据库威胁检测

[Azure SQL 数据库威胁检测](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)是内置于 Azure SQL 数据库服务的新安全智能功能。 全天候了解、分析及检测异常数据库活动，Azure SQL 数据库威胁检测可识别针对数据库的潜在威胁。

发生可疑数据库活动时，安全监管员或其他指定的管理员可以获取相关即时通知。 每个通知提供可疑活动的详细信息，以及如何进一步调查和缓解威胁的建议。

目前，Azure SQL 数据库威胁检测可检测潜在的漏洞和 SQL 注入攻击，以及异常的数据库访问模式。

收到威胁检测电子邮件通知后，用户即可使用邮件中的深层链接导航到相关的审核记录并进行查看，该链接可根据以下内容打开审核查看器和/或显示可疑事件发生时的相关审核记录的预配置审核 Excel 模板：
-   审核具有异常数据库活动的数据库/服务器的存储

-   对在事件写入审核日志时使用的存储表进行相关审核

-   审核事件发生后几小时内的记录。

-   审核事件发生时具有类似事件 ID 的记录（对于某些检测程序可选）

SQL 数据库威胁检测程序使用以下检测方法之一：

-   确定性检测 - 检测 SQL 客户端查询中与已知攻击匹配的可疑模式（基于规则）。 此方法具有高检测率和低误报率，但是覆盖率有限，因为它属于“原子检测”类别。

-   行为检测 - 检测异常活动，这些活动是过去 30 天内未显示的数据库的异常行为。  SQL 客户端异常活动的示例有失败登录/查询次数激增、提取大量数据、异常的 canonical 查询和访问数据库所用的 IP 地址不常见

### <a name="application-gateway-web-application-firewall"></a>应用程序网关 Web 应用程序防火墙

[Web 应用程序防火墙](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)是 [Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview)的一项功能，它为使用应用程序网关实现标准[应用程序传递控制](https://kemptechnologies.com/in/application-delivery-controllers)功能的 Web 应用程序提供保护。 Web 应用程序防火墙通过保护这些应用程序，免受 [OWASP 前 10 个常见的 Web 漏洞](https://www.owasp.org/index.php/Top_10_2010-Main)中大多数漏洞的威胁，来实现此目的

![应用程序网关 Web 应用程序防火墙](./media/azure-threat-detection/azure-threat-detection-fig13.png)

-   SQL 注入保护

-   跨站点脚本保护

-   常见 Web 攻击保护，例如命令注入、HTTP 请求走私、HTTP 响应拆分和远程文件包含攻击

-   防止 HTTP 协议违反行为

-   防止 HTTP 协议异常行为，例如缺少主机用户代理和接受标头

-   防止自动程序、爬网程序和扫描程序

-   检测常见应用程序错误配置（即 Apache、IIS 等）

在应用程序网关配置 WAF 可提供以下好处：

-   无需修改后端代码即可保护 Web 应用程序免受 Web 漏洞和攻击的威胁。

-   在应用程序网关背后同时保护多个 Web 应用程序。 应用程序网关支持在单个网关背后托管最多 20 个网站，并保护所有这些网站免受 Web 攻击的威胁。

-   使用应用程序网关 WAF 日志生成的实时报告，针对攻击监视 Web 应用程序。

-   某些合规性控件要求 WAF 解决方案保护所有面向 Internet 的终结点。 使用已启用 WAF 的应用程序网关，可以满足这些合规性要求。

### <a name="anomaly-detection--an-api-built-with-azure-machine-learning"></a>异常检测 - 通过 Azure 机器学习生成的 API

异常检测是使用 Azure 机器学习生成的 API，可用于检测时序数据中异常模式的不同类型。 API 将异常分数分配给时序中的每个数据点，这些分数可用于生成警报、通过仪表板进行监视或与出票系统连接。

[异常检测 API](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) 可以检测时序数据中以下类型的异常：

-   峰值和谷值：例如，监视服务中的登录失败次数或电子商务网站中的结账次数时，异常的峰值和谷值指示安全攻击或服务中断。

-   正负趋势：监视计算中的内存使用情况时，可用内存大小缩小指示可能存在内存泄漏（以此为例）；监视服务队列长度时，持续上升的趋势可能指示存在基础软件问题。

-   级别更改和值的动态范围的更改：例如，监视器会监视服务升级后服务延迟中的级别更改或升级后较低级别的异常。

基于机器学习的 API 支持：

-   灵活可靠的检测：通过异常检测模型，用户能配置敏感性设置并在周期性和非周期性数据集中检测异常。 用户可以调整异常检测模型，以便按照需要调整检测 API 的敏感度。 这意味着可选择使用或不使用周期模式来检测具有不同可见度的数据异常。

-   可缩放的及时检测：对于数百万动态更改数据集，如果使用由专家的专业知识设置的预设置阈值进行监视的传统方法，成本高且不可缩放。 此 API 中的异常检测模型经过学习，并通过历史数据和实时数据自动优化模型。

-   可操作的主动检测：慢速趋势和级别更改检测可应用于早期异常检测。 检测到的早期异常信号可用于指导人员调查问题并对其采取措施。  此外，可基于此异常检测 API 服务开发根本原因分析模型和警报工具。

异常检测 API 是针对各种方案（例如服务运行状况和 KPI 监视、IoT、性能监视和网络流量监视）的高效解决方案。 以下是一些可使用此 API 的常见方案：
- IT 部门需要可及时跟踪事件、错误代码、使用情况日志和性能（CPU、内存等）的工具。

-   在线商务网站需要跟踪客户活动、页面查看次数、点击次数等。

-   公用事业公司需要跟踪水、天然气、电和其他资源的用量。

-   设施/建筑管理服务需要监视温度、湿度、人流量等。

-   IoT/制造商需要使用时序传感器数据监视工作流、质量等。

-   客户服务中心等服务提供商需要监视服务需求趋势、事件量、等候队列长度等。

-   业务分析部门需要实时监视业务 KPI（如销售量、客户满意度、定价）的异常变化。

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) 是 Microsoft Cloud Security 堆栈的一个重要组成部分。 这是一种综合解决方案，可帮助向云迁移的组织充分利用云应用程序，同时通过提升的活动可见性保持掌控能力。 它还有助于增强跨云应用程序的对关键数据的保护能力。

借助有助于发现影子 IT、评估风险、强制实施策略、调查活动和阻止威胁的工具，组织可以更安全地移到云端，同时保持对关键数据的控制。

<table style="width:100%">
 <tr>
   <td>发现</td>
   <td>使用 Cloud App Security 发现影子 IT。 通过在云环境中发现应用、活动、用户、数据和文件，获得可见性。 发现连接到云的第三方应用。</td>
 </tr>
 <tr>
   <td>调查</td>
   <td>通过使用云取证工具深入了解网络中的风险应用、特定用户和文件，从而调查云应用。 从云中收集的数据中查找模式。 生成报告以监视云。</td>

 </tr>
 <tr>
   <td>控制</td>
   <td>通过设置策略和警报实现对网络云流量的最大控制，从而降低风险。 使用 Cloud App Security 将用户迁移到安全的经过批准的替代云应用。</td>

 </tr>
 <tr>
   <td>保护</td>
   <td>使用 Cloud App Security 批准或阻止应用程序，强制执行数据丢失防护、控制权限和共享，以及生成自定义报告和警报。</td>

 </tr>
 <tr>
   <td>控制</td>
   <td>通过设置策略和警报实现对网络云流量的最大控制，从而降低风险。 使用 Cloud App Security 将用户迁移到安全的经过批准的替代云应用。</td>

 </tr>
</table>


![Cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Cloud App Security 通过以下方式将可见性集成到云端

-   使用 Cloud Discovery 映射并确定组织使用的云环境和云应用。


-   批准和禁止云中的应用。



-   为实现连接到的应用的可见性和管理，使用利用提供程序 API 的、易于部署的应用连接器。

-   通过设置策略并不断对其进行微调，实现持续控制。

从这些源收集数据时，Cloud App Security 会对数据运行复杂的分析。 它会立即向你发出有关异常活动的警报，帮助你获得对云环境的深度了解。 可以在 Cloud App Security 中配置策略，并使用它来保护云环境中的所有内容。

## <a name="third-party-atd-capabilities-through-azure-marketplace"></a>Azure Marketplace 提供的第三方 ATD 功能

### <a name="web-application-firewall"></a>Web 应用程序防火墙

Web 应用程序防火墙会检查入站 Web 流量，并阻止 SQL 注入、跨站点脚本、恶意软件上传和应用程序 DDoS 及其他针对 Web 应用程序的攻击。 它还会检查后端 Web 服务器的响应，实现针对数据丢失预防 (DLP)。 集成的访问控制引擎使管理员能够为身份验证、授权和核算 (AAA) 创建精细的访问控制策略，这将增强组织的身份验证和用户控制。

亮点：
-   检测并阻止 SQL 注入、跨站点脚本、恶意软件上传、应用程序 DDoS 或任何其他针对应用程序的攻击。

-   身份验证和访问控制。

-   扫描出站流量以检测敏感数据，并可屏蔽或阻止信息泄露。

-   使用缓存、压缩和其他流量优化功能，加快 Web 应用程序内容的传送。

以下是 Azure 应用商店中提供的 Web 应用程序防火墙的示例：

[Barracuda Web 应用程序防火墙、Brocade 虚拟 Web 应用程序防火墙 (Brocade vWAF)、Imperva SecureSphere 和 ThreatSTOP IP 防火墙。](https://azure.microsoft.com/marketplace/partners/brocade_communications/brocade-virtual-web-application-firewall-templatevtmcluster/)

## <a name="next-steps"></a>后续步骤

- [Azure 安全中心检测功能](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)

Azure 安全中心的高级检测功能可帮助用户确定以 Microsoft Azure 资源为目标的活跃威胁，并提供快速响应所需的见解。

- [Azure SQL 数据库威胁检测](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)

Azure SQL 数据库威胁检测可帮助解决有关数据库潜在威胁的问题。
