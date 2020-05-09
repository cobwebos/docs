---
title: FedRAMP High 蓝图示例控制
description: FedRAMP High 蓝图示例的控制映射。 每个控制都映射到一个或多个协助评估的 Azure 策略。
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: cceca23e4bdc749c553eaf41b5f9599be3c9bf7d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "77150606"
---
# <a name="control-mapping-of-the-fedramp-high-blueprint-sample"></a>FedRAMP High 蓝图示例的控制映射

以下文章详细说明了 Azure 蓝图 FedRAMP High 蓝图示例如何映射到 FedRAMP High 控制。 有关这些控件的详细信息，请参阅 [FedRAMP 安全控件基线](https://www.fedramp.gov/)。

以下映射是到 **FedRAMP High** 控制的映射。 使用右侧的导航栏可直接跳转到特定的控制映射。 许多的映射控制措施都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。   然后，找到并选择“ **\[预览\]：审核 FedRAMP High 控制并部署特定 VM 扩展以支持审核要求**内置策略计划。

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../../../policy/overview.md) 定义关联。 这些策略可以帮助你[评估控件的符合性](../../../policy/how-to/get-compliance-data.md)；但是，控件与一个或多个策略之间通常不是 1:1 或完全匹配。 因此，Azure Policy 中的符合性  仅引用策略本身；这不确保你完全符合控件的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性蓝图示例的控件和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。
> 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/fedramp-h/control-mapping.md)。

## <a name="ac-2-account-management"></a>AC-2 帐户管理

此蓝图可帮助你查看可能不符合组织的帐户管理要求的帐户。 此蓝图分配 [Azure Policy](../../../policy/overview.md) 定义，这些定义用于审核对订阅和弃用帐户具有读、写和所有者权限的外部帐户。 通过查看受到这些策略审核的帐户，可以采取适当的措施，确保满足帐户管理要求。

- 应从订阅中删除弃用的帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除拥有读取权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) 帐户管理 | 基于角色的方案

Azure 实施了[基于角色的访问控制](../../../../role-based-access-control/overview.md) (RBAC) 来帮助你管理谁有权访问 Azure 中的资源。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图还分配 [Azure Policy](../../../policy/overview.md) 定义，用于审核 Azure Active Directory 身份验证在 SQL 服务器和 Service Fabric 中的使用。 使用 Azure Active Directory 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理。 此外，此蓝图还分配一个 Azure Policy 定义用于审核自定义 RBAC 规则的使用。 了解实施自定义 RBAC 规则的位置有助于验证需求以及实施是否适当，因为自定义 RBAC 规则容易出错。

- 应该为 SQL 服务器预配 Azure Active Directory 管理员
- 审核自定义 RBAC 规则的使用
- Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) 帐户管理 | 帐户监视/异常使用

实时 (JIT) 虚拟机访问会锁定发往 Azure 虚拟机的入站流量，降低遭受攻击的可能性，同时在需要时还可轻松连接到 VM。 所有访问虚拟机的 JIT 请求都记录在活动日志中，用于监视异常使用情况。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，有助于你监视能够支持实时访问但尚未配置的虚拟机。

- 应在虚拟机上应用实时网络访问控制

## <a name="ac-4-information-flow-enforcement"></a>AC-4 信息流强制

跨域资源共享 (CORS) 支持从外部域请求应用服务资源。 Microsoft 建议只允许必需的域与 API、函数和 web 应用程序进行交互。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，有助于你监视 Azure 安全中心中的 CORS 资源访问限制。 了解 CORS 实现有助于你验证信息流控制措施是否实现。

- CORS 不应允许所有资源都能访问你的 Web 应用程序

## <a name="ac-5-separation-of-duties"></a>AC-5 职责分离

仅分配一个 Azure 订阅所有者并不能实现管理冗余。 相反，分配过多的 Azure 订阅所有者会增大违规的可能性，因为会有更多的所有者帐户可能会泄密。 此蓝图可帮助你通过分配用于审核 Azure 订阅所有者数目的 [Azure Policy](../../../policy/overview.md) 定义，来保持适当的 Azure 订阅所有者数目。 此蓝图还分配 Azure Policy 定义，有助于你控制 Windows 虚拟机上管理员组的成员身份。 管理订阅所有者和虚拟机管理员权限有助于实现适当的职责分离。

- 只多只为订阅指定 3 个所有者
- 审核在其管理员组中包含任何指定成员的 Windows VM
- 审核在其管理员组中不包含所有指定成员的 Windows VM
- 部署要求以审核在其管理员组中包含任何指定成员的 Windows VM
- 部署要求以审核在其管理员组中不包含所有指定成员的 Windows VM
- 应该为你的订阅分配了多个所有者

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) 最小特权 | 用户特权评审

Azure 实施了[基于角色的访问控制](../../../../role-based-access-control/overview.md) (RBAC) 来帮助你管理谁有权访问 Azure 中的资源。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图分配 [Azure Policy](../../../policy/overview.md) 定义，用于审核应优先评审的帐户。 评审这些帐户指标可帮助确保实现最低特权控制措施。

- 只多只为订阅指定 3 个所有者
- 审核在其管理员组中包含任何指定成员的 Windows VM
- 审核在其管理员组中不包含所有指定成员的 Windows VM
- 部署要求以审核在其管理员组中包含任何指定成员的 Windows VM
- 部署要求以审核在其管理员组中不包含所有指定成员的 Windows VM
- 应该为你的订阅分配了多个所有者

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) 远程访问 | 自动监视/控制

此蓝图可帮助你监视和控制远程访问，因为它会分配 [Azure Policy](../../../policy/overview.md) 定义用于监视 Azure 应用服务应用程序的远程调试处于关闭状态，此蓝图还会分配策略定义用于审核允许来自无密码帐户的远程连接的 Linux 虚拟机。 此蓝图还将分配一个 Azure Policy 定义，用于帮助监视对存储帐户的无限制访问。 监视这些指标可以帮助确保远程访问方法符合安全策略。

- \[预览\]：审核允许通过没有密码的帐户进行远程连接的 Linux VM
- \[预览\]：部署要求以审核允许通过没有密码的帐户进行远程连接的 Linux VM
- 审核对存储帐户的不受限的网络访问
- 应为 API 应用禁用远程调试
- 应对函数应用禁用远程调试
- 应禁用 Web 应用程序的远程调试

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) 审核记录的内容 | 计划的审核记录内容的集中管理

Azure Monitor 收集的日志数据存储在支持集中配置和管理的 Log Analytics 工作区中。 此蓝图通过分配 [Azure Policy](../../../policy/overview.md) 定义来确保事件被记录下来，这些定义审核并强制在 Azure 虚拟机上部署 Log Analytics 代理。

- \[预览\]：审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VMSS 中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VM 的 Log Analytics 工作区 — 报告不匹配
- \[预览\]：为 Linux VM 规模集(VMSS)部署 Log Analytics 代理
- \[预览\]：为 Linux VM 部署 Log Analytics 代理
- \[预览\]：为 Windows VM 规模集(VMSS)部署 Log Analytics 代理
- \[预览\]：为 Windows VM 部署 Log Analytics 代理

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 对审核处理失败的响应

此蓝图分配 [Azure Policy](../../../policy/overview.md) 定义用于监视审核和事件日志记录配置。 监视这些配置可以提供审核系统故障或配置错误的指标，帮助你采取纠正措施。

- 审核诊断设置
- 应在 SQL Server 的高级数据安全设置上启用审核
- 应在托管实例上启用高级数据安全性
- 应在 SQL 服务器上启用高级数据安全性

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) 审核评审、分析和报告 | 中心评审和分析

Azure Monitor 收集的日志数据存储在支持集中报告和分析的 Log Analytics 工作区中。 此蓝图通过分配 [Azure Policy](../../../policy/overview.md) 定义来确保事件被记录下来，这些定义审核并强制在 Azure 虚拟机上部署 Log Analytics 代理。

- \[预览\]：审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VMSS 中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VM 的 Log Analytics 工作区 — 报告不匹配
- \[预览\]：为 Linux VM 规模集(VMSS)部署 Log Analytics 代理
- \[预览\]：为 Linux VM 部署 Log Analytics 代理
- \[预览\]：为 Windows VM 规模集(VMSS)部署 Log Analytics 代理
- \[预览\]：为 Windows VM 部署 Log Analytics 代理

## <a name="au-6-5-audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>AU-6 (5) 审核评审、分析和报告 | 集成/扫描和监视功能

此蓝图提供的策略定义用于审核记录，分析虚拟机、虚拟机规模集、SQL 托管实例和 SQL 服务器上的漏洞评估。
这些策略定义还审核诊断日志配置，以提供对 Azure 资源内执行的操作的见解。 这些见解提供有关已部署资源的安全状态的实时信息，可帮助你指定补救措施的优先级。
为了实现详细的漏洞扫描和监视，我们建议你同时利用 Azure Sentinel 和 Azure 安全中心。

- \[预览\]：应在虚拟机上启用漏洞评估
- \[预览\]：启用用于 VM 的 Azure Monitor
- \[预览\]：为 VM 规模集 (VMSS) 启用 Azure Monitor
- 应对 SQL Server 启用漏洞评估
- 审核诊断设置
- 应对 SQL 托管实例启用漏洞评估
- 应对 SQL Server 启用漏洞评估
- 应该修复计算机上安全配置中的漏洞
- 应该修复 SQL 数据库中的漏洞
- 应该通过漏洞评估解决方案修复漏洞
- 应该修复虚拟机规模集上安全配置中的漏洞

## <a name="au-12-audit-generation"></a>AU-12 审核生成

此蓝图提供的策略定义审核并强制部署 Azure 虚拟机上的 Log Analytics 代理并强制配置针对其他 Azure 资源类型的审核设置。
这些策略定义还审核诊断日志配置，以提供对 Azure 资源内执行的操作的见解。 此外，审核和高级数据安全在 SQL 服务器上配置。

- \[预览\]：审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VMSS 中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VM 的 Log Analytics 工作区 — 报告不匹配
- \[预览\]：为 Linux VM 规模集(VMSS)部署 Log Analytics 代理
- \[预览\]：为 Linux VM 部署 Log Analytics 代理
- \[预览\]：为 Windows VM 规模集(VMSS)部署 Log Analytics 代理
- \[预览\]：为 Windows VM 部署 Log Analytics 代理
- 审核诊断设置
- 应在 SQL Server 的高级数据安全设置上启用审核
- 应在托管实例上启用高级数据安全性
- 应在 SQL 服务器上启用高级数据安全性
- 在 SQL 服务器上部署高级数据安全
- 对 SQL 服务器部署审核
- 为网络安全组部署诊断设置

## <a name="au-12-01-audit-generation--system-wide--time-correlated-audit-trail"></a>AU-12 (01) 审核生成 | 系统范围/时间相关的审核跟踪

此蓝图通过分配 [Azure Policy](../../../policy/overview.md) 定义来帮助确保记录系统事件，这些定义用于审核在 Azure 资源上的日志设置。
此内置策略要求你指定一个资源类型的数组以检查是否已启用诊断设置。

- 审核诊断设置

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) 最少的功能 | 防止程序执行

Azure 安全中心中的自适应应用程序控制是一种智能、自动化端到端的应用程序允许列表解决方案，可以阻止或防止特定软件在虚拟机上运行。 应用程序控制可以在强制模式下运行，从而禁止未批准的应用程序运行。 此蓝图分配了一个 Azure Policy 定义，用于帮助监视建议使用应用程序允许列表但尚未对其进行配置的虚拟机。

- 应在虚拟机上启用自适应应用程序控制

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) 最少的功能 | 授权软件/允许列表

Azure 安全中心中的自适应应用程序控制是一种智能、自动化端到端的应用程序允许列表解决方案，可以阻止或防止特定软件在虚拟机上运行。 应用程序控制帮助你为虚拟机创建批准的应用程序列表。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，用于帮助监视建议使用应用程序允许列表但尚未对其进行配置的虚拟机。

- 应在虚拟机上启用自适应应用程序控制

## <a name="cm-11-user-installed-software"></a>CM-11 用户安装的软件

Azure 安全中心中的自适应应用程序控制是一种智能、自动化端到端的应用程序允许列表解决方案，可以阻止或防止特定软件在虚拟机上运行。 应用程序控制可以帮助你强制执行和监视软件限制策略的符合性。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，用于帮助监视建议使用应用程序允许列表但尚未对其进行配置的虚拟机。

- 应在虚拟机上启用自适应应用程序控制

## <a name="cp-7-alternate-processing-site"></a>CP-7 备用处理站点

Azure Site Recovery 将在虚拟机上运行的工作负荷从主位置复制到辅助位置。 如果在主站点发生故障，工作负荷将故障转移到辅助位置。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，用于审核没有配置灾难恢复的虚拟机。 监视此指标可以帮助确保必要的应变控制措施已到位。

- 审核没有配置灾难恢复的虚拟机

## <a name="cp-9-05--information-system-backup--transfer-to-alternate-storage-site"></a>CP-9 (05) 信息系统备份 | 转移到备用存储站点

此蓝图以电子方式将对组织的系统备份信息进行审核的 Azure 策略定义分配给备用存储站点。 对于存储元数据的物理装运，请考虑使用 Azure Data Box。

- 应为存储帐户启用异地冗余存储
- 应为 Azure Database for PostgreSQL 启用异地冗余备份
- 应为 Azure Database for MySQL 启用异地冗余备份
- 应为 Azure Database for MariaDB 启用异地冗余备份
- 应为 Azure SQL 数据库启用长期异地冗余备份

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) 标识和身份验证（组织用户）| 对特权帐户的网络访问

此蓝图分配 [Azure Policy](../../../policy/overview.md) 定义用于审核拥有所有者和/或写入权限但未启用多重身份验证的帐户，从而帮助你限制和控制特权访问。 即使某个身份验证信息片段已泄密，多重身份验证也有助于保护帐户的安全。 通过监视未启用多重身份验证的帐户，可以识别出更有可能会泄密的帐户。

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应在对订阅拥有写入权限的帐户上启用 MFA

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) 标识和身份验证（组织用户）| 网络访问非特权帐户

此蓝图分配一个 [Azure Policy](../../../policy/overview.md) 定义，用于审核拥有读取权限但未启用多重身份验证的帐户，从而帮助你限制和控制访问。 即使某个身份验证信息片段已泄密，多重身份验证也有助于保护帐户的安全。 通过监视未启用多重身份验证的帐户，可以识别出更有可能会泄密的帐户。

- 应在对订阅拥有读取权限的帐户上启用 MFA

## <a name="ia-5-authenticator-management"></a>IA-5 验证器管理

此蓝图分配 [Azure Policy](../../../policy/overview.md) 定义，用于审核允许来自无密码帐户的远程连接并/或在密码文件中设置了不正确权限的 Linux 虚拟机。 此蓝图还会分配一个策略定义用于审核 Windows 虚拟机密码加密类型的配置。 监视这些指标有助于确保系统验证器符合组织的标识和身份验证策略。

- \[预览\]：审核未将密码文件权限设为 0644 的 Linux VM
- \[预览\]：审核具有不使用密码的帐户的 Linux VM
- \[预览\]：审核未存储使用可逆加密的密码的 Windows VM
- \[预览\]：部署要求以审核未将密码文件权限设置为 0644 的 Linux VM
- \[预览\]：部署要求以审核具有不使用密码的帐户的 Linux VM
- \[预览\]：部署要求以审核未存储使用可逆加密的密码的 Windows VM

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) 验证器管理 |基于密码的身份验证

此蓝图通过分配 [Azure Policy](../../../policy/overview.md) 定义用于审核不强制实施最低强度和其他密码要求的 Windows 虚拟机，来帮助你强制实施强密码。 感知虚拟机是否违反密码强度策略有助于采取纠正措施，确保所有虚拟机用户帐户的密码符合组织的密码策略。

- \[预览\]：审核允许重用之前的 24 个密码的 Windows VM
- \[预览\]：审核未将最长密码期限设为 70 天的 Windows VM
- \[预览\]：审核未将最短密码期限设为 1 天的 Windows VM
- \[预览\]：审核未启用密码复杂性设置的 Windows VM
- \[预览\]：审核未将最短密码长度限制为 14 个字符的 Windows VM
- \[预览\]：审核未存储使用可逆加密的密码的 Windows VM
- \[预览\]：部署要求以审核允许重用之前的 24 个密码的 Windows VM
- \[预览\]：部署要求以审核未将最长密码期限设为 70 天的 Windows VM
- \[预览\]：部署要求以审核未将最短密码期限设为 1 天的 Windows VM
- \[预览\]：部署要求以审核未启用密码复杂性设置的 Windows VM
- \[预览\]：部署要求以审核未将最短密码长度限制为 14 个字符的 Windows VM
- \[预览\]：部署要求以审核未存储使用可逆加密的密码的 Windows VM

## <a name="ra-5-vulnerability-scanning"></a>RA-5 漏洞扫描

此蓝图分配 [Azure Policy](../../../policy/overview.md) 定义用于在 Azure 安全中心内监视操作系统漏洞、SQL 漏洞和虚拟机漏洞，来帮助你管理信息系统漏洞。 Azure 安全中心提供报告功能，使你能够实时洞察已部署的 Azure 资源的安全状态。 此蓝图还会分配策略定义用于审核和强制执行 SQL 服务器上的高级数据安全。 高级数据安全包括漏洞评估和高级威胁防护功能，可帮助你了解已部署资源中的漏洞。

- 应在托管实例上启用高级数据安全性
- 应在 SQL 服务器上启用高级数据安全性
- 在 SQL 服务器上部署高级数据安全
- 应该修复虚拟机规模集上安全配置中的漏洞
- 应该修复虚拟机上安全配置中的漏洞
- 应该修复 SQL 数据库中的漏洞
- 应该通过漏洞评估解决方案修复漏洞

## <a name="sc-5-denial-of-service-protection"></a>SC-5 拒绝服务保护

Azure 的分布式拒绝服务 (DDoS) 标准层通过基本服务层提供额外功能和缓解功能。 这些额外功能包括 Azure Monitor 集成和查看攻击后的缓解报告的功能。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，用于审核是否启用 DDoS 标准层。 了解服务层之间的功能差异有助于为 Azure 环境选择最佳解决方案来解决拒绝服务保护问题。

- 应启用 DDoS 防护标准版

## <a name="sc-7-boundary-protection"></a>SC-7 边界保护

此蓝图通过分配一个 [Azure Policy](../../../policy/overview.md) 定义用于根据 Azure 安全中心的网络安全组强化建议进行监视，以此帮助你管理和控制系统边界。 Azure 安全中心分析面向 Internet 的虚拟机的流量模式，并提供网络安全组规则建议，以减少潜在的攻击面。
此外，此蓝图还会分配策略定义用于监视不受保护的终结点、应用程序和存储帐户。 不受防火墙保护的终结点和应用程序，以及具有无限制访问权限的存储帐户，可能会允许意外访问信息系统中包含的信息。

- 应该强化面向 Internet 的虚拟机的网络安全组规则
- 应该限制通过面向 Internet 的终结点进行访问
- 应在与 VM 关联的网络安全组上限制 Web 端口
- 审核对存储帐户的不受限的网络访问

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) 边界保护 | 接入点

实时 (JIT) 虚拟机访问会锁定发往 Azure 虚拟机的入站流量，降低遭受攻击的可能性，同时在需要时还可轻松连接到 VM。 实时虚拟机访问有助于限制对 Azure 中资源的外部连接数。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，有助于你监视能够支持实时访问但尚未配置的虚拟机。

- 应在虚拟机上应用实时网络访问控制

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) 边界保护 | 外部电信服务

实时 (JIT) 虚拟机访问会锁定发往 Azure 虚拟机的入站流量，降低遭受攻击的可能性，同时在需要时还可轻松连接到 VM。 实时虚拟机访问有助于通过促进访问请求和审批流程来管理流量策略的例外情况。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义，有助于你监视能够支持实时访问但尚未配置的虚拟机。

- 应在虚拟机上应用实时网络访问控制

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) 传输保密性和完整性 | 加密或备用物理保护

此蓝图分配 [Azure Policy](../../../policy/overview.md) 定义来帮助你监视针对通信协议实施的加密机制，以此帮助你保护传输信息的机密性和完整性。 确保通信得到适当的加密可帮助你满足组织的要求，或者防范信息遭到未经授权的泄漏和修改。

- 只能通过 HTTPS 访问 API 应用
- 审核未使用安全通信协议的 Windows Web 服务器
- 部署要求以审核未使用安全通信协议的 Windows Web 服务器
- 应该只能通过 HTTPS 访问函数应用
- 应该启用只能通过安全方式连接到 Redis 缓存
- 应该启用安全传输到存储帐户
- 只能通过 HTTPS 访问 Web 应用程序

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) 保护静态信息 | 加密保护

此蓝图分配 [Azure Policy](../../../policy/overview.md) 定义用于强制实施特定的加密控制措施并审核弱加密设置的使用，从而帮助你强制实施有关通过使用加密控制措施保护静态信息的策略。 了解 Azure 资源中的哪些位置采用欠佳的加密配置有助于采取纠正措施，以确保根据信息安全策略配置资源。 具体地说，该蓝图分配的策略定义要求对数据湖存储帐户进行加密；要求 SQL 数据库上的透明数据加密；审核 SQL 数据库、虚拟机磁盘和自动化帐户变量上缺少的加密。

- 应在托管实例上启用高级数据安全性
- 应在 SQL 服务器上启用高级数据安全性
- 在 SQL 服务器上部署高级数据安全
- 部署 SQL DB 透明数据加密
- 应在虚拟机上启用磁盘加密
- 要求对 Data Lake Store 帐户加密
- 应在 SQL 数据库上启用透明数据加密

## <a name="si-2-flaw-remediation"></a>SI-2 缺陷修正

此蓝图分配 [Azure Policy](../../../policy/overview.md) 定义用于在 Azure 安全中心内监视缺少的系统更新、操作系统漏洞、SQL 漏洞和虚拟机漏洞，从而帮助你管理信息系统缺陷。 Azure 安全中心提供报告功能，使你能够实时洞察已部署的 Azure 资源的安全状态。 此蓝图还会分配一个策略定义用于确保虚拟机规模集的操作系统的修补。

- 要求自动在虚拟机规模集上执行 OS 映像修补
- 应在虚拟机规模集上安装系统更新
- 应在虚拟机上安装系统更新
- 应该修复虚拟机规模集上安全配置中的漏洞
- 应该修复虚拟机上安全配置中的漏洞
- 应该修复 SQL 数据库中的漏洞
- 应该通过漏洞评估解决方案修复漏洞

## <a name="si-3-malicious-code-protection"></a>SI-3 恶意代码防护

此蓝图分配 [Azure Policy](../../../policy/overview.md) 定义用于监视 Azure 安全中心中虚拟机上缺失的终结点防护并在 Windows 虚拟机上强制执行 Microsoft 反恶意软件解决方案，从而帮助管理终结点防护，包括恶意代码防护。

- 为 Windows Server 部署默认 Microsoft IaaSAntimalware 扩展
- 应在虚拟机规模集上安装 Endpoint Protection 解决方案
- 监视 Azure 安全中心 Endpoint Protection 的缺失情况

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) 恶意代码防护 | 集中管理

此蓝图分配 [Azure Policy](../../../policy/overview.md) 定义用于监视 Azure 安全中心中虚拟机上缺失的终结点防护，从而帮助管理终结点防护，包括恶意代码防护。 Azure 安全中心提供集中管理和报告功能，用于实时洞察已部署的 Azure 资源的安全状态。

- 应在虚拟机规模集上安装 Endpoint Protection 解决方案
- 监视 Azure 安全中心 Endpoint Protection 的缺失情况

## <a name="si-4-information-system-monitoring"></a>SI-4 信息系统监视

此蓝图有助于通过审核和跨 Azure 资源强制执行日志记录和数据安全来监视系统。 具体而言，分配的策略审核并强制执行 Log Analytics 代理的部署和 SQL 数据库、存储帐户和网络资源的强化安全设置。 这些功能有助于检测异常行为和攻击指标，以便你采取相应措施。

- \[预览\]：审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VMSS 中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VM 的 Log Analytics 工作区 — 报告不匹配
- \[预览\]：为 Linux VM 规模集(VMSS)部署 Log Analytics 代理
- \[预览\]：为 Linux VM 部署 Log Analytics 代理
- \[预览\]：为 Windows VM 规模集(VMSS)部署 Log Analytics 代理
- \[预览\]：为 Windows VM 部署 Log Analytics 代理
- 应在托管实例上启用高级数据安全性
- 应在 SQL 服务器上启用高级数据安全性
- 在 SQL 服务器上部署高级数据安全
- 在存储帐户上部署高级威胁防护
- 对 SQL 服务器部署审核
- 创建虚拟网络时部署网络观察程序
- 在 SQL 服务器上部署威胁检测
- 允许的位置
- 允许的资源组位置

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) 信息系统监视 | 分析流量 / 隐蔽性外泄

Azure 存储高级威胁防护会检测试图访问或利用存储帐户的异常或可能有害的企图。 保护警报包括异常访问模式、异常提取/上传和可疑存储活动。 这些指标有助于检测信息的隐蔽性外泄。

- 在存储帐户上部署高级威胁防护

> [!NOTE]
> 特定 Azure Policy 定义的可用性在 Azure 政府和其他国家云中可能会有所不同。 

## <a name="next-steps"></a>后续步骤

现在，你已了解了 FedRAMP High 蓝图的控制映射，请访问以下文章来了解蓝图和部署此示例的方式：

> [!div class="nextstepaction"]
> [FedRAMP High 蓝图 - 概述](./index.md)
> [FedRAMP High 蓝图 - 部署步骤](./deploy.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。