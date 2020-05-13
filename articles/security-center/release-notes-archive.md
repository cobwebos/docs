---
title: 存档 Azure 安全中心的新增功能
description: 介绍 Azure 安全中心在六个月以前和更早版本中的新增功能和更改内容。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: c0883e91d5e806fb166c3ddeafc4ce130ff3f66f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210838"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>如何存档 Azure 安全中心的新增功能？

" [Azure Active Directory 中的新增功能？"](release-notes.md)发行说明 "页包含过去六个月的更新，而此页面包含较旧的项目。

此页提供以下信息：

- 新增功能
- Bug 修复
- 已弃用的功能

## <a name="november-2019"></a>2019 年 11 月

### <a name="threat-protection-for-azure-key-vault-in-public-preview-in-north-america-regions"></a>北美区域的公共预览版中的 Azure Key Vault 威胁防护

Azure Key Vault 是一项基本服务，通过提供集中管理云中密钥、机密、加密密钥和策略的功能，来保护数据和提高云应用程序的性能。 由于 Azure Key Vault 会存储敏感数据和业务关键数据，因此需要对密钥保管库和存储的数据的最大安全性。

Azure 安全中心对 Azure Key Vault 威胁防护的支持提供了额外的安全智能层，用于检测访问或利用密钥保管库的异常和潜在有害尝试。 这一新的保护层允许客户针对其密钥保管库解决威胁，而无需成为安全专家或管理安全监视系统。 功能北美区域中的公共预览版。


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Azure 存储的威胁防护包括恶意软件信誉屏蔽

适用于 Azure 存储的威胁防护提供了由 Microsoft 威胁智能提供支持的新检测功能，通过使用哈希信誉分析和来自活动 Tor 出口节点的可疑访问（匿名代理）来检测恶意软件上传到 Azure 存储。 你现在可以使用 Azure 安全中心查看跨存储帐户检测到的恶意软件。


### <a name="workflow-automation-with-logic-apps-preview"></a>逻辑应用的工作流自动化（预览）

在其环境中发现差异时，具有集中管理的安全性和 IT/操作的组织将实施内部工作流进程，以在组织内推动所需的操作。 在许多情况下，这些工作流是可重复的过程，自动化可大大简化组织内的过程。

今天，我们在安全中心引入了一项新功能，该功能使客户能够使用 Azure 逻辑应用创建自动化配置，并创建可根据特定 ASC 发现（如建议或警报）自动触发这些配置的策略。 可以将 Azure 逻辑应用配置为执行任意自定义操作，该操作由大型逻辑应用连接器团体支持，或使用安全中心提供的模板之一，例如发送电子邮件或打开 ServiceNow™票证。

有关运行工作流的自动和手动安全中心功能的详细信息，请参阅[工作流自动化](workflow-automation.md)。

若要了解如何创建逻辑应用，请参阅[Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)。


### <a name="quick-fix-for-bulk-resources-generally-available"></a>适用于大容量资源的快速修复功能已正式发布

由于将用户作为安全分数的一部分提供的多个任务，能够有效地纠正大型汽油中的问题可能会变得很困难。

若要简化对安全错误配置的修正，并能够快速修正对大量资源的建议并提高安全分数，请使用快速修复修补程序。

此操作可让你选择要对其应用修正的资源，并启动一个将代表你配置设置的修正操作。

快速修补程序现已在安全中心建议页中提供。

请参阅[安全建议的参考指南](recommendations-reference.md)中的建议启用了快速修复。


### <a name="scan-container-images-for-vulnerabilities-preview"></a>扫描容器映像中的漏洞（预览版）

Azure 安全中心现在可以扫描 Azure 容器注册表中的容器映像中的漏洞。

映像扫描的工作方式是分析容器映像文件，然后检查是否有任何已知的漏洞（由 Qualys 提供支持）。

将新的容器映像推送到 Azure 容器注册表时，会自动触发扫描本身。 找到的漏洞将作为安全中心建议，并包括在 Azure 安全分数中，并提供有关如何修补它们以减少所允许攻击面的信息。


### <a name="additional-regulatory-compliance-standards-preview"></a>其他法规遵从标准（预览版）

规章相容性仪表板提供基于安全中心评估的符合性状态的见解。 该仪表板显示了你的环境如何符合特定规章标准和行业基准规范指定的控制和要求，并提供有关如何满足这些要求的说明性建议。

目前，合规性仪表板支持四种内置的标准： Azure CIS 1.1.0、PCI-DSS、ISO 27001 和 SOC-TSP。 我们现在宣布推出其他受支持标准的公共预览版： NIST SP 800-53 R4、SWIFT CSP CSCF v2020、加拿大联邦 PBMM 和英国官方与英国 NHS 一起发布。 我们还发布了 Azure CIS 1.1.0 的更新版本，涵盖了来自标准和增强扩展性的更多控制。

[详细了解如何自定义合规性仪表板中的标准集](update-regulatory-compliance-packages.md)。


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Azure Kubernetes Service （预览版）的威胁防护

Kubernetes 很快就成为了部署和管理云中软件的新标准。 很少有用户对 Kubernetes 有丰富的经验，并且很多人只关注一般的工程和管理，并忽略安全方面。 需要认真配置 Kubernetes 环境，以确保不会为攻击者公开面向容器的受攻击面门。 安全中心正在将容器空间中的支持扩展到 Azure 中增长最快的一项服务（AKS）。

此公共预览版中的新功能包括：

- **发现 & 可见性**-在安全中心的已注册订阅内持续发现托管的 AKS 实例。
- **安全分数建议**-可操作的项可帮助客户遵循 AKS 中的安全最佳做法，作为客户的安全分数的一部分，例如 "应使用基于角色的访问控制来限制对 Kubernetes 服务群集的访问权限。
- **威胁检测**-基于主机和群集的分析，如 "检测到特权容器"。


### <a name="virtual-machine-vulnerability-assessment-preview"></a>虚拟机漏洞评估（预览版）

安装在虚拟机中的应用程序通常会有可能导致虚拟机破坏的漏洞。 我们将宣布安全中心标准层包含虚拟机的内置漏洞评估，无额外费用。 公共预览版中的漏洞评估由 Qualys 提供支持，可让你持续扫描虚拟机上所有已安装的应用程序，以查找有漏洞的应用程序，并在安全中心门户的体验中提供调查结果。 安全中心负责所有部署操作，使用户无需进行额外的工作。 接下来，我们计划提供漏洞评估选项来支持客户独特的业务需求。

[了解有关 Azure 虚拟机的漏洞评估的详细信息](security-center-vulnerability-assessment-recommendations.md)。


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure 虚拟机上 SQL server 的高级数据安全性（预览版）

Azure 安全中心支持在 IaaS Vm 上运行的 SQL 数据库的威胁防护和漏洞评估现在为预览版。

[漏洞评估](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)是一种易于配置的服务，可以发现、跟踪和帮助您修正潜在的数据库漏洞。 它以 Azure 安全分数的形式提供安全状况的可见性，并包含解决安全问题并增强数据库 fortifications 的步骤。

[高级威胁防护](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)可检测异常活动，这些活动表明访问或利用 SQL server 的异常和潜在有害尝试。 它会持续监视你的数据库中是否存在可疑活动，并针对异常数据库访问模式提供面向操作的安全警报。 这些警报提供可疑活动的详细信息和建议的操作来调查和缓解威胁。


### <a name="support-for-custom-policies-preview"></a>支持自定义策略（预览版）

Azure 安全中心现在支持自定义策略（预览版）。

我们的客户想要在安全中心使用其自己的安全评估来扩展他们的当前安全评估范围，并根据他们在 Azure 策略中创建的策略进行评估。 由于支持自定义策略，现在就可以了。

这些新策略将是安全中心建议体验、安全评分和规章遵从标准仪表板的一部分。 由于支持自定义策略，你现在可以在 Azure 策略中创建一个自定义计划，然后将其作为策略添加到安全中心，并将其可视化为建议。


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>通过适用于社区和合作伙伴的平台扩展 Azure 安全中心覆盖范围

使用安全中心不仅能接收来自 Microsoft 的建议，还能接收来自合作伙伴的现有解决方案（如 Check Point、Tenable 和 CyberArk），并提供更多的集成。  安全中心的简单载入流可以将您的现有解决方案连接到安全中心，使您可以在一个位置查看您的安全状况建议，运行统一报表，并利用所有安全中心的功能，同时应对内置和合作伙伴的建议。 你还可以将安全中心建议导出到合作伙伴产品。

[了解有关 Microsoft 智能安全关联的详细信息](https://www.microsoft.com/security/partnerships/intelligent-security-association)。



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>与导出建议和警报（预览）的高级集成

为了在安全中心之上启用企业级方案，现在可以在 Azure 门户或 API 之外的其他位置使用安全中心警报和建议。 这些可以直接导出到事件中心并 Log Analytics 工作区。 下面是一些可以围绕这些新功能创建的工作流：

- 通过导出到 Log Analytics 工作区，你可以创建具有 Power BI 的自定义仪表板。
- 使用 "导出到事件中心"，可以将安全中心警报和建议导出到第三方 Siem，并将其提供给第三方解决方案的实时或 Azure 数据资源管理器。


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>从 Windows 管理中心（预览版）将本地服务器载入到安全中心

Windows 管理中心是一个管理门户，适用于未在 Azure 中部署的 Windows 服务器提供多种 Azure 管理功能，例如备份和系统更新。 我们最近添加了一项功能，该功能可将这些非 Azure 服务器直接由 ASC 保护，以通过 Windows 管理中心体验。

通过这种新体验，用户将 WAC 服务器加入 Azure 安全中心，并直接在 Windows 管理中心体验中查看其安全警报和建议。


## <a name="september-2019"></a>2019 年 9 月

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>通过自适应应用程序控件改进功能管理规则

使用自适应应用程序控件来管理虚拟机的规则的体验已得到改进。 Azure 安全中心的自适应应用程序控件可帮助您控制哪些应用程序可以在您的虚拟机上运行。 除了对规则管理的一般改进之外，还可以通过新的权益来控制在添加新规则时将保护哪些文件类型。

[详细了解自适应应用程序控件](security-center-adaptive-application.md)。


### <a name="control-container-security-recommendation-using-azure-policy"></a>使用 Azure 策略控制容器安全建议

Azure 安全中心建议修正容器安全中的漏洞现在可以通过 Azure 策略启用或禁用。

若要查看已启用的安全策略，请在 "安全中心" 中打开 "安全策略" 页。


## <a name="august-2019"></a>2019 年 8 月

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Azure 防火墙的实时（JIT） VM 访问 

Azure 防火墙的实时（JIT） VM 访问现已正式发布。 使用它来保护 Azure 防火墙保护的环境以及 NSG 受保护的环境。

JIT VM 访问通过使用 NSG 和 Azure 防火墙规则，仅在需要时提供对 Vm 的受控访问，从而减少了网络容量耗尽攻击的风险。

为 Vm 启用 JIT 时，将创建一个策略，该策略确定要保护的端口、端口保持打开状态的时间，以及可以访问这些端口的已批准 IP 地址。 此策略可帮助你控制用户在请求访问时可执行的操作。

请求记录在 Azure 活动日志中，因此你可以轻松监视和审核访问。 此实时页面还可帮助你快速识别已启用 JIT 的现有 Vm 以及建议使用 JIT 的虚拟机。

[详细了解 Azure 防火墙](https://docs.microsoft.com/azure/firewall/overview)。


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>单击 "修正" 以提升安全状况（预览版）

安全评分是一个可帮助你评估工作负荷安全状况的工具。 它会查看你的安全建议并为你确定其优先级，因此你知道首先要执行哪些建议。 这可以帮助你找到最严重的安全漏洞来确定调查的优先级。

为了简化安全配置错误的补救，并帮助你快速提高安全分数，我们添加了一项新功能，使你可以通过一次单击来修正大量资源的建议。

此操作可让你选择要对其应用修正的资源，并启动一个将代表你配置设置的修正操作。

请参阅[安全建议的参考指南](recommendations-reference.md)中的建议启用了快速修复。


### <a name="cross-tenant-management"></a>跨租户管理

安全中心现在支持在 Azure Lighthouse 中进行跨租户管理方案。 这使你能够在安全中心查看和管理多个租户的安全状况。 

[了解有关跨租户管理体验的详细信息](security-center-cross-tenant-management.md)。


## <a name="july-2019"></a>2019 年 7 月

### <a name="updates-to-network-recommendations"></a>网络建议的更新

Azure 安全中心（ASC）已推出新的网络建议，并改进了现有的网络建议。 现在，使用安全中心可以确保更好地为资源提供更好的网络保护。 

[了解有关网络建议的详细信息](recommendations-reference.md#recs-network)。


## <a name="june-2019"></a>2019 年 6 月

### <a name="adaptive-network-hardening---generally-available"></a>自适应网络强化-正式发布

在公有云中运行的工作负荷的最大攻击面之一是与公共 Internet 之间的连接。 我们的客户发现很难知道哪个网络安全组（NSG）规则应该已准备就绪，以确保 Azure 工作负荷仅适用于所需的源范围。 利用此功能，安全中心可了解 Azure 工作负荷的网络流量和连接模式，并为面向 Internet 的虚拟机提供 NSG 规则建议。 这可以帮助我们的客户更好地配置其网络访问策略，并限制他们遭受攻击。 

[详细了解自适应网络强化](security-center-adaptive-network-hardening.md)。
