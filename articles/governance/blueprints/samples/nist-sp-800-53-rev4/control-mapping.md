---
title: 示例 - NIST SP 800-53 R4 蓝图 - 控制映射
description: NIST SP 800-53 R4 蓝图示例到 Azure Policy 和 RBAC 的控制映射。
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b887c4e6812d201dc83465a578f71e1742e8e9cf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343091"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>NIST SP 800-53 R4 蓝图示例的控制映射

以下文章详细说明了 Azure 蓝图 SP 800-53 R4 蓝图示例如何映射到 NIST SP 800-53 R4 控制措施。 有关控制措施的详细信息，请参阅 [NIST SP 800-53](https://nvd.nist.gov/800-53)。

以下映射适用于 NIST SP 800-53 (Rev. 4) 控制措施  。 使用右侧的导航栏可直接跳转到特定的控制映射。 许多的映射控制措施都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。   然后，找到并选择“[预览]：审核 NIST SP 800-53 R4 控制措施并部署特定 VM 扩展以支持审核要求”内置策略计划  。

## <a name="ac-2-account-management"></a>AC-2 帐户管理

此蓝图可帮助你查看可能不符合你组织的帐户管理要求的帐户。 此蓝图分配五个 Azure Policy 定义，这些定义用于审核对订阅和弃用帐户具有读、写和所有者权限的外部帐户。 通过查看受到这些策略审核的帐户，可以采取适当的措施，确保满足帐户管理要求。

- [预览]: Audit deprecated accounts on a subscription
- [预览]: Audit deprecated accounts with owner permissions on a subscription
- [预览]: Audit external accounts with owner permissions on a subscription
- [预览]: Audit external accounts with read permissions on a subscription
- [预览]: Audit external accounts with write permissions on a subscription

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) 帐户管理 | 基于角色的方案

Azure 实施了[基于角色的访问控制](../../../../role-based-access-control/overview.md) (RBAC) 来帮助你管理谁有权访问 Azure 中的资源。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图还分配了两个 [Azure Policy](../../../policy/overview.md) 定义，用于审核 Azure Active Directory 身份验证在 SQL Server 和 Service Fabric 中的使用。 使用 Azure Active Directory 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理。

- 审核确认已为 SQL Server 预配了 Azure Active Directory 管理员
- 审核确认已在 Service Fabric 中使用 Azure Active Directory，用于实施客户端身份验证

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) 帐户管理 | 帐户监视/异常使用

实时 (JIT) 虚拟机访问会锁定发往 Azure 虚拟机的入站流量，降低遭受攻击的可能性，同时在需要时还可轻松连接到 VM。 所有访问虚拟机的 JIT 请求都记录在活动日志中，用于监视异常使用情况。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，有助于你监视能够支持实时访问但尚未配置的虚拟机。

- [预览]：监视 Azure 安全中心内可能的网络即时(JIT)访问

## <a name="ac-4-information-flow-enforcement"></a>AC-4 信息流强制

跨域资源共享 (CORS) 支持从外部域请求应用服务资源。 Microsoft 建议只允许必需的域与 API、函数和 web 应用程序进行交互。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，有助于你监视 Azure 安全中心中的 CORS 资源访问限制。
了解 CORS 实现有助于你验证信息流控制措施是否实现。

- [预览]: Audit CORS resource access restrictions for a Web Application

## <a name="ac-5-separation-of-duties"></a>AC-5 职责分离

仅分配一个 Azure 订阅所有者并不能实现管理冗余。 相反，分配过多的 Azure 订阅所有者会增大违规的可能性，因为会有更多的所有者帐户可能会泄密。 此蓝图可帮助你通过分配两个用于审核 Azure 订阅所有者数目的 [Azure Policy](../../../policy/overview.md) 定义，来保持适当的 Azure 订阅所有者数目。 此蓝图还分配了四个 Azure Policy 定义，有助于你控制 Windows 虚拟机上管理员组的成员身份。 管理订阅所有者和虚拟机管理员权限有助于实现适当的职责分离。

- [预览]: Audit maximum number of owners for a subscription
- [预览]: Audit minimum number of owners for subscription
- 审核 Windows VM 内的管理员组不包括指定的成员
- 审核 Windows VM 内的管理员组包括指定的成员
- 部署 VM 扩展来审核 Windows VM 内的管理员组不包括指定的成员
- 部署 VM 扩展来审核 Windows VM 内的管理员组包括指定的成员

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) 最小特权 | 用户特权评审

Azure 实施了[基于角色的访问控制](../../../../role-based-access-control/overview.md) (RBAC) 来帮助你管理谁有权访问 Azure 中的资源。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图分配了六个 [Azure Policy](../../../policy/overview.md) 定义，用于审核应优先评审的帐户。 评审这些帐户指标可帮助确保实现最低特权控制措施。

- [预览]: Audit maximum number of owners for a subscription
- [预览]: Audit minimum number of owners for subscription
- 审核 Windows VM 内的管理员组不包括指定的成员
- 审核 Windows VM 内的管理员组包括指定的成员
- 部署 VM 扩展来审核 Windows VM 内的管理员组不包括指定的成员
- 部署 VM 扩展来审核 Windows VM 内的管理员组包括指定的成员

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) 远程访问 | 自动监视/控制

此蓝图可帮助你监视和控制远程访问，因为它会分配三个 [Azure Policy](../../../policy/overview.md) 定义，用于监视 Azure 应用服务应用程序的远程调试处于关闭状态，此蓝图还会分配两个策略定义，用于审核允许来自无密码帐户的远程连接的 Linux 虚拟机。 此蓝图还将分配一个 Azure Policy 定义，用于帮助监视对存储帐户的无限制访问。 监视这些指标可以帮助确保远程访问方法符合安全策略。

- [预览]: Audit remote debugging state for a Function App
- [预览]: Audit remote debugging state for a Web Application
- [预览]: Audit remote debugging state for an API App
- [预览]: Audit that Linux VMs do not allow remote connections from accounts without passwords
- [预览]: Deploy VM extension to audit that Linux VMs do not allow remote connections from accounts without passwords
- 审核对存储帐户的不受限的网络访问

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) 审核记录的内容 | 计划的审核记录内容的集中管理

Azure Monitor 收集的日志数据存储在支持集中配置和管理的 Log Analytics 工作区中。 此蓝图通过分配七个 [Azure Policy](../../../policy/overview.md) 定义确保事件被记录下来，这些定义审核并强制在 Azure 虚拟机上部署 Log Analytics 代理。

- [预览]：审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- [预览]：审核 VMSS 中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- [预览]: Audit Log Analytics Workspace for VM - Report Mismatch
- [预览]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [预览]: Deploy Log Analytics Agent for Linux VMs
- [预览]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [预览]: Deploy Log Analytics Agent for Windows VMs

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 对审核处理失败的响应

此蓝图分配五个 [Azure Policy](../../../policy/overview.md) 定义，用于监视审核和事件日志记录配置。 监视这些配置可以提供审核系统故障或配置错误的指标，帮助你采取纠正措施。

- [预览]: Monitor unaudited SQL servers in Azure Security Center
- 审核诊断设置
- 审核没有高级数据安全的 SQL 托管实例
- 审核 SQL 服务器级别审核设置
- 审核没有高级数据安全的 SQL 服务器

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) 审核评审、分析和报告 | 中心评审和分析

Azure Monitor 收集的日志数据存储在支持集中报告和分析的 Log Analytics 工作区中。 此蓝图通过分配七个 [Azure Policy](../../../policy/overview.md) 定义确保事件被记录下来，这些定义审核并强制在 Azure 虚拟机上部署 Log Analytics 代理。

- [预览]：审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- [预览]：审核 VMSS 中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- [预览]: Audit Log Analytics Workspace for VM - Report Mismatch
- [预览]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [预览]: Deploy Log Analytics Agent for Linux VMs
- [预览]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [预览]: Deploy Log Analytics Agent for Windows VMs

## <a name="au-12-audit-generation"></a>AU-12 审核生成

此蓝图通过分配 15 个 [Azure Policy](../../../policy/overview.md) 定义帮助确保记录了系统事件，这些定义用于审核在 Azure 资源上的日志设置。 这些策略定义审核并强制部署 Azure 虚拟机上的 Log Analytics 代理并强制配置针对其他 Azure 资源类型的审核设置。 这些策略定义还审核诊断日志配置，以提供对 Azure 资源内执行的操作的见解。 此外，审核和高级数据安全在 SQL 服务器上配置。

- [预览]：审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- [预览]：审核 VMSS 中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- [预览]: Audit Log Analytics Workspace for VM - Report Mismatch
- [预览]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [预览]: Deploy Log Analytics Agent for Linux VMs
- [预览]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [预览]: Deploy Log Analytics Agent for Windows VMs
- [预览]: Monitor unaudited SQL servers in Azure Security Center
- 为网络安全组应用诊断设置
- 审核诊断设置
- 审核没有高级数据安全的 SQL 托管实例
- 审核 SQL 服务器级别审核设置
- 审核没有高级数据安全的 SQL 服务器
- 在 SQL 服务器上部署高级数据安全
- 对 SQL 服务器部署审核

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) 最少的功能 | 防止程序执行

Azure 安全中心中的自适应应用程序控制是一种智能、自动化端到端的应用程序允许列表解决方案，可以阻止或防止特定软件在虚拟机上运行。 应用程序控制可以在强制模式下运行，从而禁止未批准的应用程序运行。 此蓝图分配了一个 Azure Policy 定义，用于帮助监视建议使用应用程序允许列表但尚未对其进行配置的虚拟机。

- [预览]: Monitor possible app whitelisting in Azure Security Center

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) 最少的功能 | 授权软件/允许列表

Azure 安全中心中的自适应应用程序控制是一种智能、自动化端到端的应用程序允许列表解决方案，可以阻止或防止特定软件在虚拟机上运行。 应用程序控制帮助你为虚拟机创建批准的应用程序列表。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，用于帮助监视建议使用应用程序允许列表但尚未对其进行配置的虚拟机。

- [预览]: Monitor possible app whitelisting in Azure Security Center

## <a name="cm-11-user-installed-software"></a>CM-11 用户安装的软件

Azure 安全中心中的自适应应用程序控制是一种智能、自动化端到端的应用程序允许列表解决方案，可以阻止或防止特定软件在虚拟机上运行。 应用程序控制可以帮助你强制执行和监视软件限制策略的符合性。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，用于帮助监视建议使用应用程序允许列表但尚未对其进行配置的虚拟机。

- [预览]: Monitor possible app whitelisting in Azure Security Center

## <a name="cp-7-alternate-processing-site"></a>CP-7 备用处理站点

Azure Site Recovery 将在虚拟机上运行的工作负荷从主位置复制到辅助位置。 如果在主站点发生故障，工作负荷将故障转移到辅助位置。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，用于审核没有配置灾难恢复的虚拟机。 监视此指标可以帮助确保必要的应变控制措施已到位。

- 审核没有配置灾难恢复的虚拟机

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) 标识和身份验证（组织用户）| 对特权帐户的网络访问

此蓝图分配两个 [Azure Policy](../../../policy/overview.md) 定义，用于审核拥有所有者和/或写入权限但未启用多重身份验证的帐户，从而帮助你限制和控制特权访问。 即使某个身份验证信息片段已泄密，多重身份验证也有助于保护帐户的安全。 通过监视未启用多重身份验证的帐户，可以识别出更有可能会泄密的帐户。

- [预览]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [预览]: Audit accounts with write permissions who are not MFA enabled on a subscription

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) 标识和身份验证（组织用户）| 网络访问非特权帐户

此蓝图分配一个 [Azure Policy](../../../policy/overview.md) 定义，用于审核拥有读取权限但未启用多重身份验证的帐户，从而帮助你限制和控制访问。 即使某个身份验证信息片段已泄密，多重身份验证也有助于保护帐户的安全。 通过监视未启用多重身份验证的帐户，可以识别出更有可能会泄密的帐户。

- [预览]: Audit accounts with read permissions who are not MFA enabled on a subscription

## <a name="ia-5-authenticator-management"></a>IA-5 验证器管理

此蓝图分配五个 [Azure Policy](../../../policy/overview.md) 定义，用于审核允许来自无密码帐户的远程连接并/或在密码文件中设置了不正确权限的 Linux 虚拟机。 此蓝图还分配了一个策略定义，用于审核 Windows 虚拟机密码加密类型的结合形式。 监视这些指标有助于确保系统验证器符合组织的标识和身份验证策略。

- [预览]: Audit that Linux VMs do not have accounts without passwords
- [预览]: Deploy VM extension to audit that Linux VMs do not have accounts without passwords
- [预览]: Audit that Linux VMs have the passwd file permissions set to 0644
- [预览]: Audit that Windows VMs store passwords using reversible encryption
- [预览]: Deploy VM extension to audit that Linux VMs have the passwd file permissions set to 0644

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) 验证器管理 |基于密码的身份验证

此蓝图通过分配 12 个 [Azure Policy](../../../policy/overview.md) 定义用于审核不强制实施最低强度和其他密码要求的 Windows 虚拟机，来帮助你强制实施强密码。 感知虚拟机是否违反密码强度策略有助于采取纠正措施，确保所有虚拟机用户帐户的密码与组织的密码策略相符。

- [预览]: Audit that Windows VMs cannot re-use the previous 24 passwords
- [预览]: Audit that Windows VMs have a maximum password age of 70 days
- [预览]: Audit that Windows VMs have a minimum password age of 1 day
- [预览]: Audit that Windows VMs have the password complexity setting enabled
- [预览]: Audit that Windows VMs restrict the minimum password length to 14 characters
- [预览]: Audit that Windows VMs store passwords using reversible encryption
- [预览]: Deploy VM extension to audit that Windows VMs cannot re-use the previous 24 passwords
- [预览]: Deploy VM extension to audit that Windows VMs have a maximum password age of 70 days
- [预览]: Deploy VM extension to audit that Windows VMs have a minimum password age of 1 day
- [预览]: Deploy VM extension to audit that Windows VMs have the password complexity setting enabled
- [预览]: Deploy VM extension to audit that Windows VMs restrict the minimum password length to 14 characters
- [预览]: Deploy VM extension to audit that Windows VMs store passwords using reversible encryption

## <a name="ra-5-vulnerability-scanning"></a>RA-5 漏洞扫描

此蓝图分配了四个 [Azure Policy](../../../policy/overview.md) 定义用于在 Azure 安全中心内监视操作系统漏洞、SQL 漏洞和虚拟机漏洞，来帮助你管理信息系统漏洞。 Azure 安全中心提供报告功能，使你能够实时洞察已部署的 Azure 资源的安全状态。 此蓝图还分配了三个策略定义，用于审核和强制执行 SQL 服务器上的高级数据安全。 高级数据安全包括漏洞评估和高级威胁防护功能，可帮助你了解已部署资源中的漏洞。

- 审核没有高级数据安全的 SQL 托管实例
- 审核没有高级数据安全的 SQL 服务器
- 在 SQL 服务器上部署高级数据安全
- [预览]: Audit OS vulnerabilities on your virtual machine scale sets in Azure Security Center
- [预览]: Monitor OS vulnerabilities in Azure Security Center
- [预览]: Monitor SQL vulnerability assessment results in Azure Security Center
- [预览]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="sc-5-denial-of-service-protection"></a>SC-5 拒绝服务保护

Azure 的分布式拒绝服务 (DDoS) 标准层通过基本服务层提供额外功能和缓解功能。 这些额外功能包括 Azure Monitor 集成和查看攻击后的缓解报告的功能。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，用于审核是否启用 DDoS 标准层。 了解服务层之间的功能差异有助于为 Azure 环境选择最佳解决方案来解决拒绝服务保护问题。

- [预览]: Audit standard tier of DDoS protection is enabled for a virtual network

## <a name="sc-7-boundary-protection"></a>SC-7 边界保护

此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，用于监视具有宽松规则的网络安全组，从而帮助你管理和控制系统边界。 过于宽松的规则可能会允许意外的网络访问，应该对其进行评审。 此蓝图还分配了一个策略定义，用于监视 Azure 安全中心中的网络安全组强化建议。 Azure 安全中心分析面向 Internet 的虚拟机的流量模式，并提供网络安全组规则建议，以减少潜在的攻击面。
此外，此蓝图还分配了三个策略定义，用于监视不受保护的终结点、应用程序和存储帐户。 不受防火墙保护的终结点和应用程序，以及具有无限制访问权限的存储帐户，可能会允许意外访问信息系统中包含的信息。

- [预览]: Monitor Internet-facing virtual machines for Network Security Group traffic hardening recommendations
- [预览]: Monitor permissive network access in Azure Security Center
- [预览]: Monitor unprotected network endpoints in Azure Security Center
- [预览]: Monitor unprotected web application in Azure Security Center
- 审核对存储帐户的不受限的网络访问

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) 边界保护 | 接入点

实时 (JIT) 虚拟机访问会锁定发往 Azure 虚拟机的入站流量，降低遭受攻击的可能性，同时在需要时还可轻松连接到 VM。 实时虚拟机访问有助于限制对 Azure 中资源的外部连接数。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，有助于你监视能够支持实时访问但尚未配置的虚拟机。

- [预览]：监视 Azure 安全中心内可能的网络即时(JIT)访问

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) 边界保护 | 外部电信服务

实时 (JIT) 虚拟机访问会锁定发往 Azure 虚拟机的入站流量，降低遭受攻击的可能性，同时在需要时还可轻松连接到 VM。 实时虚拟机访问有助于通过促进访问请求和审批流程来管理流量策略的例外情况。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，有助于你监视能够支持实时访问但尚未配置的虚拟机。

- [预览]：监视 Azure 安全中心内可能的网络即时(JIT)访问

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) 保护静态信息 | 加密保护

此蓝图分配了 9 个 [Azure Policy](../../../policy/overview.md) 定义，用于强制实施特定的加密控制措施并审核弱加密设置的使用，从而帮助你强制实施有关通过使用加密控制措施保护静态信息的策略。 了解 Azure 资源中的哪些位置采用欠佳的加密配置有助于采取纠正措施，以确保根据信息安全策略配置资源。 具体地说，该蓝图分配的策略定义要求对数据湖存储帐户进行加密；要求 SQL 数据库上的透明数据加密；审核 SQL 数据库、虚拟机磁盘和自动化帐户变量上缺少的加密。

- [预览]: Monitor unencrypted SQL databases in Azure Security Center
- [预览]: Monitor unencrypted VM Disks in Azure Security Center
- 审核确认指向存储帐户的传输的安全性
- 审核没有高级数据安全的 SQL 托管实例
- 审核没有高级数据安全的 SQL 服务器
- 审核透明数据加密状态
- 在 SQL 服务器上部署高级数据安全
- 部署 SQL DB 透明数据加密
- 对 Data Lake Store 帐户强制加密

## <a name="si-2-flaw-remediation"></a>SI-2 缺陷修正

此蓝图分配了六个 [Azure Policy](../../../policy/overview.md) 定义，用于在 Azure 安全中心内监视缺少的系统更新、操作系统漏洞、SQL 漏洞和虚拟机漏洞，从而帮助你管理信息系统缺陷。 Azure 安全中心提供报告功能，使你能够实时洞察已部署的 Azure 资源的安全状态。 此蓝图还分配了一个策略定义，用于确保虚拟机规模集的操作系统的自动升级。

- [预览]: Audit any missing system updates on virtual machine scale sets in Azure Security Center
- [预览]: Audit OS vulnerabilities on your virtual machine scale sets in Azure Security Center
- [预览]: Monitor missing system updates in Azure Security Center
- [预览]: Monitor OS vulnerabilities in Azure Security Center
- [预览]: Monitor SQL vulnerability assessment results in Azure Security Center
- [预览]: Monitor VM Vulnerabilities in Azure Security Center
- 在 VMSS 上通过应用运行状况检查强制执行 OS 自动升级

## <a name="si-3-malicious-code-protection"></a>SI-3 恶意代码防护

此蓝图分配了三个 [Azure Policy](../../../policy/overview.md) 定义，用于监视 Azure 安全中心中虚拟机上缺失的终结点防护并在 Windows 虚拟机上强制执行 Microsoft 反恶意软件解决方案，从而帮助管理终结点防护，包括恶意代码防护。

- [预览]: Audit the endpoint protection solution on virtual machine scale sets in Azure Security Center
- [预览]: Monitor missing Endpoint Protection in Azure Security Center
- 为 Windows Server 部署默认 Microsoft IaaSAntimalware 扩展

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) 恶意代码防护 | 集中管理

此蓝图分配了两个 [Azure Policy](../../../policy/overview.md) 定义，用于监视 Azure 安全中心中虚拟机上缺失的终结点防护，从而帮助管理终结点防护，包括恶意代码防护。 Azure 安全中心提供集中管理和报告功能，用于实时洞察已部署的 Azure 资源的安全状态。

- [预览]: Audit the endpoint protection solution on virtual machine scale sets in Azure Security Center
- [预览]: Monitor missing Endpoint Protection in Azure Security Center

## <a name="si-4-information-system-monitoring"></a>SI-4 信息系统监视

此蓝图有助于通过审核和跨 Azure 资源强制执行日志记录和数据安全来监视系统。 具体而言，分配的策略审核并强制执行 Log Analytics 代理的部署和 SQL 数据库、存储帐户和网络资源的强化安全设置。 这些功能有助于检测异常行为和攻击指标，以便你采取相应措施。

- [预览]：审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- [预览]：审核 VMSS 中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- [预览]: Audit Log Analytics Workspace for VM - Report Mismatch
- [预览]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [预览]: Deploy Log Analytics Agent for Linux VMs
- [预览]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [预览]: Deploy Log Analytics Agent for Windows VMs
- 审核没有高级数据安全的 SQL 托管实例
- 审核没有高级数据安全的 SQL 服务器
- 在 SQL 服务器上部署高级数据安全
- 在存储帐户上部署高级威胁防护
- 对 SQL 服务器部署审核
- 创建虚拟网络时部署网络观察程序
- 在 SQL 服务器上部署威胁检测

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) 信息系统监视 | 分析流量 / 隐蔽性外泄

Azure 存储高级威胁防护会检测试图访问或利用存储帐户的异常或可能有害的企图。 保护警报包括异常访问模式、异常提取/上传和可疑存储活动。 这些指标有助于检测信息的隐蔽性外泄。

- 在存储帐户上部署高级威胁防护

## <a name="next-steps"></a>后续步骤

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。