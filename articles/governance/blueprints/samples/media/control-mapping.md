---
title: 媒体蓝图示例控制措施
description: 媒体蓝图示例的控制映射。 每个控制都映射到一个或多个协助评估的 Azure 策略。
ms.date: 02/24/2020
ms.topic: sample
ms.openlocfilehash: bdc4ef65c6d61a9d4217d7873bb29bd787842e71
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201734"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>媒体蓝图示例的控制映射

以下文章详细说明了 Azure 蓝图媒体蓝图示例如何映射到媒体控制措施。 有关控制措施的详细信息，请参阅[媒体](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)。

以下映射适用于**媒体**控制措施。 使用右侧的导航栏可直接跳转到特定的控制映射。 许多的映射控制措施都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。   然后，找到并选择“ **\[预览\]：** 审核媒体控制措施”内置策略计划。

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../../../policy/overview.md) 定义关联。 这些策略可以帮助你[评估控件的符合性](../../../policy/how-to/get-compliance-data.md)；但是，控件与一个或多个策略之间通常不是 1:1 或完全匹配。 因此，Azure Policy 中的符合性  仅引用策略本身；这不确保你完全符合控件的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性蓝图示例的控件和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md)。

## <a name="access-control"></a>访问控制

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1 - 确保不存在根访问密钥

- \[预览\]：部署必备组件用于审核在受信任的根中不包含指定证书的 Windows VM

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 - 密码、PIN 和令牌必须受保护

- \[预览\]：部署先决条件，以便审核未将最短密码长度限制为 14 个字符的 Windows VM

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 - 禁止共享帐户访问

- 应从服务总线命名空间中删除 RootManageSharedAccessKey 以外的所有授权规则

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 - 系统必须将访问权限限制给已获授权的用户。

- 审核对存储帐户的不受限的网络访问

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1.14 - 系统必须强制实施访问权限。

- \[预览\]：部署必备组件用于审核“用户权限分配”中的 Windows VM 配置

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1.15 - 阻止未经授权访问安全相关的信息或功能。

- \[预览\]：显示“安全选项 - 系统设置”中 Windows VM 配置的审核结果

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 - 必须通过适当的角色分配强制实施职责分离。

- [预览版\]：应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40 - 确保系统不会同时连接受信任的网络和不受信任的网络。

- \[预览\]：部署必备组件用于审核“安全选项 - 网络访问”中的 Windows VM 配置

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 和 AC-1.43 - 必须限制非员工的远程访问，仅允许访问特意批准的信息系统

- \[预览\]：显示允许通过没有密码的帐户进行远程连接的 Linux VM 中的审核结果

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50 - 记录所有信息系统组件的安全相关事件。

- 应启用逻辑应用的诊断日志

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54 - 确保为所有云控制台用户启用多重身份验证 (MFA)。

- 应对订阅中拥有写入权限的帐户启用 MFA
- 为了防止帐户或资源出现违规问题，应为所有拥有写入特权的订阅帐户启用多重身份验证 (MFA)。

## <a name="auditing--logging"></a>审核和日志记录

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1 - 必须记录成功和失败事件。

- 应启用搜索服务的诊断日志

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL-2.16 - 网络设备/实例必须记录由该网络设备/实例（ELB、Web 应用程序防火墙等）分类为关键安全事件的任何事件。

- \[预览\]：显示“安全选项 - 帐户”中 Windows VM 配置的审核结果

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17 - 服务器/实例必须记录由该服务器/实例分类为关键安全事件的任何事件

- \[预览\]：显示“安全选项 - 帐户”中 Windows VM 配置的审核结果

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 - 域事件必须记录由域管理软件分类为关键或高严重性安全事件的任何事件

- \[预览\]：显示“安全选项 - 帐户”中 Windows VM 配置的审核结果
- \[预览\]：部署必备组件用于审核“安全选项 - Microsoft 网络客户端”中的 Windows VM 配置

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20 - 域事件必须记录由域安全控制措施分类为关键安全事件的任何事件

- \[预览\]：显示“安全选项 - 帐户”中 Windows VM 配置的审核结果

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21 - 域事件必须记录对域日志进行的任何访问或更改

- \[预览\]：显示“安全选项 - 恢复控制台”中 Windows VM 配置的审核结果

## <a name="cryptographic-controls"></a>加密控制措施

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2 - 应用程序和系统必须使用当前的加密解决方案来保护数据。

- 应在 SQL 数据库上启用透明数据加密
- 应启用透明数据加密以保护静态数据并满足符合性要求

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5 - 数字证书必须由批准的证书颁发机构签名。

- \[预览\]：显示包含在指定天数内过期的证书的 Windows VM 的审核结果

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6 - 数字证书必须唯一分配到用户或设备。

- \[预览\]：部署必备组件用于审核包含在指定天数内过期的证书的 Windows VM

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7 - 必须存储加密材料，以根据记录的保留时长启用记录解密。

- 应在虚拟机上启用磁盘加密
- 建议通过 Azure 安全中心监视未启用磁盘加密的 VM

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8 - 必须安全存储机密和私钥。

- 应在 SQL 数据库上启用透明数据加密
- 应启用透明数据加密以保护静态数据并满足符合性要求

## <a name="change--config-management"></a>更改和配置管理

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2 - 只有已获授权的用户才能在系统上实施已批准的更改。

- 应在计算机上安装系统更新
- 建议通过 Azure 安全中心监视服务器上缺失的安全系统更新

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12 - 维护信息系统的最新、完整、准确且随时可用的基线配置。

- 应在计算机上安装系统更新
- 建议通过 Azure 安全中心监视服务器上缺失的安全系统更新

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13 - 采用自动化工具维护信息系统的基线配置。

- 应在计算机上安装系统更新
- 建议通过 Azure 安全中心监视服务器上缺失的安全系统更新

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14 - 识别并禁用不必要和/或不安全的功能、端口、协议和服务。

- 网络接口应禁用 IP 转发
- \[预览\]：应禁用虚拟机上的 IP 转发

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19 - 监视安全配置设置的更改。

- 为网络安全组部署诊断设置

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22 - 确保仅在公司系统上安装已授权的软件和更新。

- 应在计算机上安装系统更新
- 建议通过 Azure 安全中心监视服务器上缺失的安全系统更新

## <a name="identity--authentication"></a>标识和身份验证

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1 - 必须将用户帐户唯一分配到个人，用于访问未分类为“公开”的信息。 必须使用标准化的逻辑格式构建帐户 ID。

- 应从订阅中删除拥有所有者权限的外部帐户
- 为了防止发生未受监视的访问，应从订阅中删除拥有所有者权限的外部帐户。

## <a name="network-security"></a>网络安全

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2 - 仅限已获授权的用户访问网络设备管理功能。

- \[预览\]：部署必备组件用于审核“安全选项 - 网络访问”中的 Windows VM 配置

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3 - 所有网络设备必须使用最安全的配置。

- \[预览\]：部署必备组件用于审核“安全选项 - 网络访问”中的 Windows VM 配置

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5 - 必须审批并定期审核通过防火墙与系统建立的所有网络连接。

- \[预览\]：显示“Windows 防火墙属性”中 Windows VM 配置的审核结果

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7 - 受信任网络与任何不受信任网络或公共网络之间的任何边界上必须存在适当的控制措施。

- \[预览\]：部署必备组件用于审核“Windows 防火墙属性”中的 Windows VM 配置

## <a name="security-planning"></a>安全规划

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3 - 必须识别可能会对公司信息和内容的机密性、完整性或可用性产生负面影响的威胁，并确定出现此类威胁的可能性。

- 应在 SQL 托管实例的“高级数据安全性”设置中将“高级威胁保护类型”设置为“所有”

### <a name="security-continuity"></a>安全连续性

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5 - 长期存储中的数据必须在整个保留期内都可供访问，并在出现媒体降级和技术变革时受到保护。

- 应将 SQL 服务器的审核保留期配置为大于 90 天。
- 审核配置的审核保持期少于 90 天的 SQL 服务器。

## <a name="system-integrity"></a>系统完整性

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3 - 只有已获授权的人员才能监视网络和用户活动。

- 应该修复 SQL 数据库中的漏洞
- 监视漏洞评估扫描结果并提供如何补救数据库漏洞的相关建议。

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4 - 面向 Internet 的系统必须具备入侵检测。

- 在 SQL 服务器上部署威胁检测

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13 - 应在整个公司内实施标准化的集中管理式反恶意软件。

- 为 Windows Server 部署默认 Microsoft IaaSAntimalware 扩展

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14 - 反恶意软件每周必须扫描计算机和媒体至少一次。

- 为 Windows Server 部署默认 Microsoft IaaSAntimalware 扩展

## <a name="vulnerability-management"></a>漏洞管理

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4 - 确保每月扫描应用程序的漏洞。

- 应该修复虚拟机规模集上安全配置中的漏洞
- 审核虚拟机规模集上的 OS 漏洞，以保护其免受攻击。

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5 - 确保识别漏洞、与威胁进行关联，并评估其风险。

- 应该修复虚拟机规模集上安全配置中的漏洞
- 审核虚拟机规模集上的 OS 漏洞，以保护其免受攻击。

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6 - 确保在相互议定的时限内修正识别到的漏洞。

- 应该修复虚拟机规模集上安全配置中的漏洞
- 审核虚拟机规模集上的 OS 漏洞，以保护其免受攻击。

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7 - 必须仅限已获授权的人员访问和使用漏洞管理系统。

- 应该修复虚拟机规模集上安全配置中的漏洞
- 审核虚拟机规模集上的 OS 漏洞，以保护其免受攻击。

> [!NOTE]
> 特定 Azure Policy 定义的可用性在 Azure 政府和其他国家云中可能会有所不同。 

## <a name="next-steps"></a>后续步骤

你已了解媒体蓝图示例的控制映射。 接下来，请访问以下文章，了解概述以及如何部署此示例：

> [!div class="next step action"]
> [媒体蓝图 - 概述](./control-mapping.md)
> [媒体蓝图 - 部署步骤](./deploy.md)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。
