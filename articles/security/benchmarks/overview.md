---
title: Azure 安全基准 V2 概述
description: Azure 安全基准 V2 概述
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fb6813875858ab6cc438df5abb78a75391adfeac
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280651"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Azure 安全基准概述 (V2) 

Azure 安全基准 (审计准则委员会) 提供了说明性的最佳实践和建议，以帮助提高 Azure 上的工作负荷、数据和服务的安全性。

此基准是一组全面的安全指南的一部分，其中包括：

- **云采用框架** –安全指南，包括 [策略](/azure/cloud-adoption-framework/strategy/define-security-strategy)、 [角色和责任](/azure/cloud-adoption-framework/organize/cloud-security)、 [Azure 十大安全最佳做法](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices)和 [参考实现](/azure/cloud-adoption-framework/ready/enterprise-scale/)。
- **Azure 良好构建的框架** –在 azure 上 [保护工作负荷](https://docs.microsoft.com/assessments/?mode=pre-assessment&session=local) 的指导。
- **Microsoft 安全性最佳做法** - [建议](/security/compass/microsoft-security-compass-introduction) ，以及 Azure 上的示例。

 Azure 安全基准侧重于以云为中心的控制领域。 这些控件与已知的安全基准（如中心的 Internet 安全 (CI) 控制版本7.1 和国家研究所标准和技术 (NIST) SP800）一致。
Azure 安全基准中包含以下控件：

| 审计准则委员会控制域 | 说明 
|--|--|
| [网络安全 (NS) ](security-controls-v2-network-security.md) | 网络安全包括用于保护和保护 Azure 网络的控件，包括保护虚拟网络、建立专用连接、阻止和减少外部攻击以及保护 DNS。 |
| [身份管理 (IM) ](security-controls-v2-identity-management.md) | 标识管理涵盖使用 Azure Active Directory 建立安全标识和访问控制的控件，包括使用单一登录、强身份验证、托管标识 (和服务原则) 应用程序、条件性访问和帐户异常监视。 |
| [ (PA) 的特权访问 ](security-controls-v2-privileged-access.md) | 特权访问涵盖了用于保护对 Azure 租户和资源的特权访问的控制，包括保护管理模型、管理帐户和特权访问工作站的各种控制，以防止有意和无意的风险。 |
| [数据保护 (DP) ](security-controls-v2-data-protection.md) | 数据保护涉及到对静态数据保护、传输过程中的数据保护，以及通过授权访问机制的控制，包括使用 Azure 中的访问控制、加密和日志记录发现、分类、保护和监视敏感数据资产。 |
| [资产管理 (AM) ](security-controls-v2-asset-management.md) | 资产管理涵盖控制以确保对 Azure 资源的安全性的可见性和管理，包括对安全人员权限的建议、对资产清单的安全访问，以及管理服务和资源的审批 (库存、跟踪和更正) 。 |
| [ (LT) 进行日志记录和威胁检测 ](security-controls-v2-logging-threat-detection.md) | 日志记录和威胁检测涵盖了用于检测 Azure 上的威胁、为 Azure 服务启用、收集和存储审核日志的控制，包括通过控制启用检测、调查和修正过程，从而在 Azure 服务中生成具有本机威胁检测功能的高质量警报;它还包括 Azure Monitor 收集日志，通过 Azure Sentinel、时间同步和日志保留来集中进行安全分析。 |
| [事件响应 (IR)](security-controls-v2-incident-response.md) | 事件响应涵盖事件响应生命周期-准备、检测和分析、包含和事件后活动中的控制，包括使用 azure 服务（如 Azure 安全中心和 Sentinel）自动执行事件响应过程。 |
| [状况和漏洞管理 (PV) ](security-controls-v2-posture-vulnerability-management.md) | 状况和漏洞管理重点介绍用于评估和改进 Azure 安全状况的控制，包括漏洞扫描、渗透测试和修正，以及 Azure 资源中的安全配置跟踪、报告和更正。 |
| [Endpoint Security (ES) ](security-controls-v2-endpoint-security.md) | Endpoint Security 涵盖终结点检测和响应中的控件，包括在 Azure 环境中使用终结点检测和响应 (EDR) 和反恶意软件服务。 |
| [ (BR) 备份和恢复 ](security-controls-v2-backup-recovery.md) | 备份和恢复涵盖了控制，以确保执行、验证和保护不同服务层上的数据和配置备份。 |
| [管理和策略 (GS) ](security-controls-v2-governance-strategy.md) | 调控和策略提供的指导可确保一致的安全策略和记录的管理方法来指导和维持安全保障，包括为不同的云安全功能、统一的技术策略和支持策略和标准建立角色和责任。 |

还可以通过 [电子表格格式](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark)下载 Azure 安全基准。

## <a name="azure-security-benchmark-recommendations"></a>Azure 安全基准检验建议

每项建议都包含以下信息：

- **Azure ID**：与建议对应的 Azure 安全基准检验 ID。
- **Cis 控制 V2.0 ID (s) **： CIS 控制 v2.0 控件 (s 的) ，与此建议对应。
- **NIST SP800-53 R4 ID (s) **： NIST SP800-53 r4 (中等) 控制 () ，这对应于此建议。
- **详细信息**：此建议的原理阐述，以及关于如何实现建议的指南的链接。 如果 Azure 安全中心支持此建议，这些信息也会列出。
- **责任**：是客户、服务提供商，还是同时负责实现此建议。 安全责任将在公有云中共同分担。 某些安全控制仅适用于云服务提供商，因此提供程序负责解决这些控制。 这些是一般的观测值–对于一些单独的服务，责任将不同于 Azure 安全基准中列出的内容。 单个服务的基线建议中介绍了这些差异。
- **客户安全利益干系人**：客户组织 [的安全功能](/azure/cloud-adoption-framework/organize/cloud-security#security-functions) ，这些功能可能对各自的控制有责任、责任或咨询。 这可能与组织不同，具体取决于公司的安全组织结构，以及设置的与 Azure 安全性相关的角色和职责。

> [!NOTE]
> 审计准则委员会与行业基准之间的控制映射 (例如 NIST 和 CI) 仅指示特定的 Azure 功能可用于完全或部分寻址在 NIST 或 CIS 中定义的控制要求。 应注意，此类实现不一定会转换为 CIS 或 NIST 中对应控件的完全符合性。

我们欢迎你提供详细反馈并积极参与 Azure 安全基准检验工作。 若要提供 Azure 安全基准团队直接输入，请在 https://aka.ms/AzSecBenchmark


- 请参阅第一个安全控制： [网络安全](security-control-network-security.md)
- 阅读 [Azure 安全基准检验简介](introduction.md)
- 下载 [Azure Security 准则 v2 excel 电子表格](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark)