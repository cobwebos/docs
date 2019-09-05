---
title: 示例 - CIS Microsoft Azure Foundations Benchmark 蓝图 - 建议映射
description: CIS Microsoft Azure Foundations Benchmark 蓝图示例到 Azure Policy 的建议映射。
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/09/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d02dca4438b55d51cccb5cc1b55679eb72b73991
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232801"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>CIS Microsoft Azure Foundations Benchmark 蓝图示例的建议映射

以下文章详细说明了 Azure 蓝图 CIS Microsoft Azure Foundations Benchmark 蓝图示例如何映射到 CIS Microsoft Azure Foundations Benchmark 建议。 有关建议的详细信息，请参阅 [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/)。

以下映射适用于 **CIS Microsoft Azure Foundations Benchmark v1.1.0** 建议。 使用右侧的导航栏可直接跳转到特定的建议映射。
许多的映射建议都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。   然后，找到并选择“\[预览\] 审核 CIS Microsoft Azure Foundations Benchmark v1.1.0 建议并部署特定 VM 扩展以支持审核要求”内置策略计划。 

> [!NOTE]
> 完整的蓝图示例即将推出。 关联的 Azure Policy 计划现已推出。

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 确保为所有特权用户启用多重身份验证

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应在对订阅拥有写入权限的帐户上启用 MFA

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 确保为所有非特权用户启用多重身份验证

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应在对订阅拥有读取权限的帐户上启用 MFA

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 确保没有任何来宾用户

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除拥有读取权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 确保 ASC 默认策略设置“监视系统更新”不是处于“已禁用”状态

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应在计算机上安装系统更新

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 确保 ASC 默认策略设置“监视 OS 漏洞”不是处于“已禁用”状态

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应该修复计算机上安全配置中的漏洞

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 确保 ASC 默认策略设置“监视终结点保护”不是处于“已禁用”状态

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 监视 Azure 安全中心 Endpoint Protection 的缺失情况

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 确保 ASC 默认策略设置“监视磁盘加密”不是处于“已禁用”状态

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应在虚拟机上启用磁盘加密

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 确保 ASC 默认策略设置“监视 Web 应用程序防火墙”不是处于“已禁用”状态

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应该强化 IaaS 上 Web 应用程序的 NSG 规则

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 确保 ASC 默认策略设置“监视漏洞评估”不是处于“已禁用”状态

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应该通过漏洞评估解决方案修复漏洞

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 确保 ASC 默认策略设置“监视 JIT 网络访问”不是处于“已禁用”状态

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应在虚拟机上应用实时网络访问控制

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 确保 ASC 默认策略设置“监视 SQL 加密”不是处于“已禁用”状态

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应在 SQL 数据库上启用透明数据加密

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 确保“需要安全传输”设置为“已启用”

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应该启用安全传输到存储帐户

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 确保将针对存储帐户的默认网络访问规则设置为“拒绝”

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 审核对存储帐户的不受限的网络访问

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 确保“审核”设置为“打开”

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应在 SQL Server 的高级数据安全设置上启用审核

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 确保在“审核”策略中为 SQL 服务器正确设置“AuditActionGroups”

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- SQL 审核设置中应包含配置为捕获关键活动的操作组

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 确保审核保留期“大于 90 天”

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应将 SQL 服务器的审核保留期配置为大于 90 天。

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 确保将 SQL 服务器上的“高级数据安全性”设置为“打开”

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应在 SQL 服务器上启用高级数据安全性

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 确保“威胁检测类型”设置为“所有”

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应在 SQL 服务器的“高级数据安全性”设置中将“高级威胁保护类型”设置为“所有”
- 应在 SQL 托管实例的“高级数据安全性”设置中将“高级威胁保护类型”设置为“所有”

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 确保设置“将警报发送到”

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- SQL 服务器的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 确保“电子邮件服务和协同管理员”设置为“已启用”

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- SQL 托管实例的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 确保配置 Azure Active Directory 管理员

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应该为 SQL 服务器预配 Azure Active Directory 管理员

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 确保将 SQL 数据库上的“数据加密”设置为“打开”

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应在 SQL 数据库上启用透明数据加密

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 确保使用 BYOK（使用自己的密钥）加密 SQL 服务器的 TDE 保护器

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应使用自己的密钥加密 SQL 服务器的 TDE 保护器
- 应使用自己的密钥加密 SQL 托管实例的 TDE 保护器

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 确保 Azure KeyVault 日志记录设置为“已启用”

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应启用 Key Vault 中的诊断日志

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 确保“OS 磁盘”已加密

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应在虚拟机上启用磁盘加密

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 确保“数据磁盘”已加密

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应在虚拟机上启用磁盘加密

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 确保已应用适用于所有虚拟机的最新 OS 修补程序

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 应在计算机上安装系统更新

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 确保已安装适用于所有虚拟机的终结点保护

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 监视 Azure 安全中心 Endpoint Protection 的缺失情况

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 在 Azure Kubernetes 服务中启用基于角色的访问控制 (RBAC)

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- \[预览\]：应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 确保 Web 应用将所有 HTTP 流量重定向到 Azure 应用服务中的 HTTPS

此蓝图分配与此 CIS 建议相符的 [Azure Policy](../../../policy/overview.md) 定义。

- 只能通过 HTTPS 访问 Web 应用程序

## <a name="next-steps"></a>后续步骤

了解 CIS Microsoft Azure Foundations Benchmark 蓝图的控制映射后，接下来请阅读以下文章了解该蓝图，或者在 Azure 门户中访问 Azure Policy 以分配计划：

> [!div class="nextstepaction"]
> [CIS Microsoft Azure Foundations Benchmark 蓝图 - 概述](./index.md)
> [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。