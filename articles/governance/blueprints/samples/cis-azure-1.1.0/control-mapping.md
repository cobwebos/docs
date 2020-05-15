---
title: CIS Microsoft Azure Foundations Benchmark 蓝图示例控件
description: CIS Microsoft Azure Foundations Benchmark 蓝图示例到 Azure Policy 的建议映射。
ms.date: 05/06/2020
ms.topic: sample
ms.openlocfilehash: 2163162f52eb4ad7f580c01d6539c242bd332645
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82863954"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>CIS Microsoft Azure Foundations Benchmark 蓝图示例的建议映射

以下文章详细说明了 Azure 蓝图 CIS Microsoft Azure Foundations Benchmark 蓝图示例如何映射到 CIS Microsoft Azure Foundations Benchmark 建议。 有关建议的详细信息，请参阅 [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/)。

以下映射适用于 **CIS Microsoft Azure Foundations Benchmark v1.1.0** 建议。 使用右侧的导航栏可直接跳转到特定的建议映射。
许多的映射建议都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。   然后，找到并选择“\[预览\] 审核 CIS Microsoft Azure Foundations Benchmark v1.1.0 建议并部署特定 VM 扩展以支持审核要求”内置策略计划。 

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../../../policy/overview.md) 定义关联。 这些策略可以帮助你[评估控件的符合性](../../../policy/how-to/get-compliance-data.md)；但是，控件与一个或多个策略之间通常不是 1:1 或完全匹配。 因此，Azure Policy 中的符合性  仅引用策略本身；这不确保你完全符合控件的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性蓝图示例的控件和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md)。

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 确保为所有特权用户启用多重身份验证

未针对 Azure Active Directory 特权帐户启用多重身份验证时，此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你进行监视。

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应对订阅中拥有写入权限的帐户启用 MFA

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 确保为所有非特权用户启用多重身份验证

未针对 Azure Active Directory 非特权帐户启用多重身份验证时，此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你进行监视。

- 应在对订阅拥有读取权限的帐户上启用 MFA

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 确保没有任何来宾用户

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助监视可能需要删除的来宾帐户。

- 应从订阅中删除拥有读取权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户
- 应从订阅中删除拥有所有者权限的外部帐户

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1.23 确保未创建任何自定义订阅所有者角色

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助监视可能需要删除的自定义订阅所有者角色。

- 不应存在自定义订阅所有者角色

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 确保已选择标准定价层

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你监视未启用安全中心标准层的网络和虚拟机。

- 应选择安全中心标准定价层

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 确保“监视代理的自动预配”设置为“打开”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保启用 Log Analytics 代理的自动预配。

- 应该对订阅启用 Log Analytics 监视代理的自动预配

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 确保 ASC 默认策略设置“监视系统更新”不是处于“已禁用”状态

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保在虚拟机上安装系统更新。

- 应在计算机上安装系统更新

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 确保 ASC 默认策略设置“监视 OS 漏洞”不是处于“已禁用”状态

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你监视未修正的虚拟机漏洞。

- 应该修复计算机上安全配置中的漏洞

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 确保 ASC 默认策略设置“监视终结点保护”不是处于“已禁用”状态

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保在虚拟机上启用终结点保护。

- 监视 Azure 安全中心 Endpoint Protection 的缺失情况

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 确保 ASC 默认策略设置“监视磁盘加密”不是处于“已禁用”状态

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保加密虚拟机磁盘。

- 应在虚拟机上启用磁盘加密

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 确保 ASC 默认策略设置“监视网络安全组”不是处于“已禁用”状态

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你保护面向 Internet 的虚拟机。

- 应在面向 Internet 的虚拟机上应用自适应网络强化建议

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 确保 ASC 默认策略设置“启用下一代防火墙(NGFW)监视”不是处于“已禁用”状态

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 可帮助你通过限制访问来防范子网和虚拟机受到威胁。 此 CIS Microsoft Azure Foundations Benchmark 建议参考的安全中心策略已由两条新建议替代。 下面参考的策略解释了新建议。

- 子网应与网络安全组关联
- 面向 Internet 的虚拟机应使用网络安全组进行保护

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 确保 ASC 默认策略设置“监视漏洞评估”不是处于“已禁用”状态

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保检测和修正漏洞。

- 应该通过漏洞评估解决方案修复漏洞

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 确保 ASC 默认策略设置“监视存储 Blob 加密”不是处于“已禁用”状态

针对所有新的和现有的存储帐户启用 Azure 存储加密，并且不能禁用。 （这是默认的 Azure 功能；没有策略分配。）

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 确保 ASC 默认策略设置“监视 JIT 网络访问”不是处于“已禁用”状态

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你控制对虚拟机的访问。

- 应在虚拟机上应用实时网络访问控制

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 确保 ASC 默认策略设置“监视自适应应用程序允许列表”不是处于“已禁用”状态

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保在虚拟机上启用自适应应用程序控制。

- 应在虚拟机上启用自适应应用程序控制

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 确保 ASC 默认策略设置“监视 SQL 审核”不是处于“已禁用”状态

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保启用 SQL 服务器审核。

- 应启用 SQL 服务器上的审核

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 确保 ASC 默认策略设置“监视 SQL 加密”不是处于“已禁用”状态

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保在 SQL 数据库中启用透明数据加密。

- 应在 SQL 数据库上启用透明数据加密

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 确保已设置“安全联系人电子邮件”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保正确启用安全通知

- 应为订阅提供安全联系人电子邮件地址

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 确保已设置安全联系人的“电话号码”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保正确启用安全通知

- 应为订阅提供安全联系人电话号码

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 确保将“发送高严重性警报的电子邮件通知”设置为“打开”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保正确启用安全通知

- 应启用高严重性警报的电子邮件通知

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 确保将“同时将电子邮件发送给订阅所有者”设置为“打开”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保正确启用安全通知

- 应启用向订阅所有者发送高严重性警报的电子邮件通知

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 确保“需要安全传输”设置为“已启用”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你监视允许不安全连接的存储帐户。

- 应该启用安全传输到存储帐户

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 确保将针对存储帐户的默认网络访问规则设置为“拒绝”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你监视允许不受限访问的存储帐户。

- 审核对存储帐户的不受限的网络访问

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 确保启用“受信任的 Microsoft 服务”来访问存储帐户

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你监视不允许从受信任 Microsoft 服务进行访问的存储帐户。

- 存储帐户应允许从受信任的 Microsoft 服务访问

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 确保“审核”设置为“打开”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保启用 SQL 服务器审核。 

- 应启用 SQL 服务器上的审核

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 确保在“审核”策略中为 SQL 服务器正确设置“AuditActionGroups”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保正确配置 SQL 服务器审核。

- SQL 审核设置中应包含配置为捕获关键活动的操作组

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 确保审核保留期“大于 90 天”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保至少将 SQL 服务器日志保留 90 天。

- 应将 SQL 服务器的审核保留期配置为大于 90 天。

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 确保将 SQL 服务器上的“高级数据安全性”设置为“打开”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保在 SQL 服务器和 SQL 托管实例上启用高级数据安全性。

- 应在 SQL 服务器上启用高级数据安全性
- 应在 SQL 托管实例上启用高级数据安全性

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 确保“威胁检测类型”设置为“所有”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保在 SQL 服务器和 SQL 托管实例上正确配置高级威胁防护。

- 应在 SQL 服务器的“高级数据安全性”设置中将“高级威胁保护类型”设置为“所有”
- 应在 SQL 托管实例的“高级数据安全性”设置中将“高级威胁保护类型”设置为“所有”

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 确保设置“将警报发送到”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保正确启用高级数据安全性通知。

- SQL 服务器的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址
- SQL 托管实例的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 确保“电子邮件服务和协同管理员”设置为“已启用”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保正确启用高级数据安全性通知。

- 应在 SQL 服务器高级数据安全设置中为管理员和订阅所有者启用电子邮件通知
- 应在 SQL 托管实例高级数据安全设置中启用“向管理员和订阅所有者发送电子邮件通知”

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 确保配置 Azure Active Directory 管理员

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保为 SQL 服务器预配 Azure Active Directory 管理员。

- 应该为 SQL 服务器预配 Azure Active Directory 管理员

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 确保将 SQL 数据库上的“数据加密”设置为“打开”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保在 SQL 数据库中启用透明数据加密。

- 应在 SQL 数据库上启用透明数据加密

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 确保使用 BYOK（使用自己的密钥）加密 SQL 服务器的 TDE 保护器

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保使用自己的密钥加密 SQL 服务器和 SQL 托管实例的透明数据加密保护器。

- 应使用自己的密钥加密 SQL 服务器的 TDE 保护器
- 应使用自己的密钥加密 SQL 托管实例的 TDE 保护器

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 确保 MySQL 数据库服务器的“强制 SSL 连接”设置为“已启用”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保 MySQL 数据库服务器强制执行 TLS/SSL 连接。

- 应为 MySQL 数据库服务器启用“强制 SSL 连接”

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4.12 确保 PostgreSQL 数据库服务器的服务器参数“log_checkpoints”设置为“ON”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助确保 PostgreSQL 数据库服务器记录检查点。

- 应为 PostgreSQL 数据库服务器启用“记录检查点”

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 确保 PostgreSQL 数据库服务器的“强制 SSL 连接”设置为“已启用”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保 PostgreSQL 数据库服务器强制执行 TLS/SSL 连接。

- 应为 PostgreSQL 数据库服务器启用“强制 SSL 连接”

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4.14 确保 PostgreSQL 数据库服务器的服务器参数“log_connections”设置为“ON”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助确保 PostgreSQL 数据库服务器记录连接。

- 应为 PostgreSQL 数据库服务器启用“记录连接”

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4.15 确保 PostgreSQL 数据库服务器的服务器参数“log_disconnections”设置为“ON”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助确保 PostgreSQL 数据库服务器记录断开连接。

- 应为 PostgreSQL 数据库服务器记录断开连接。

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4.16 确保 PostgreSQL 数据库服务器的服务器参数“log_duration”设置为“ON”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助确保 PostgreSQL 数据库服务器记录已完成语句的持续时间。

- 应为 PostgreSQL 数据库服务器启用“记录持续时间”

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 确保 PostgreSQL 数据库服务器的服务器参数“connection_throttling”设置为“ON”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你缓解针对 PostgreSQL 数据库服务器的暴力攻击。

- 应为 PostgreSQL 数据库服务器启用连接限制

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 确保配置 Azure Active Directory 管理员

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保为 SQL 服务器预配 Azure Active Directory 管理员。 CIS Microsoft Azure Foundations Benchmark 包括此建议；但它是[建议 4.8](#48-ensure-that-azure-active-directory-admin-is-configured) 的副本。

- 应该为 SQL 服务器预配 Azure Active Directory 管理员

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 确保日志配置文件存在

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保所有 Azure 订阅的日志配置文件都存在。 

- Azure 订阅应有用于活动日志的日志配置文件

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 确保将“活动日志保留期”设置为 365 天或更长时间

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保至少将活动日志保留一年。

- 活动日志至少应保留一年

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 确保审核配置文件捕获所有活动

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保正确配置日志配置文件。

- Azure Monitor 日志配置文件应收集“写入”、“删除”和“操作”类别的日志

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 确保日志配置文件捕获所有区域（包括全球）的活动日志

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保正确配置日志配置文件。

- Azure Monitor 应从所有区域收集活动日志

## <a name="516-ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>5.1.6 确保使用 BYOK（使用自己的密钥）对存储帐户（包含的容器具有活动日志）加密

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保使用 BYOK 对包含活动日志的存储帐户进行加密。

- 必须使用 BYOK 对存储帐户（包含的容器具有活动日志）进行加密

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 确保 Azure KeyVault 日志记录设置为“已启用”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保为 Key Vault 启用诊断日志。

- 应启用 Key Vault 中的诊断日志

## <a name="521-ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>5.2.1 确保存在“创建策略分配”的活动日志警报

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保存在特定的活动日志警报。

- 特定策略操作应有活动日志警报

## <a name="522-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>5.2.2 确保存在“创建或更新网络安全组”的活动日志警报

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保存在特定的活动日志警报。

- 特定管理操作应有活动日志警报

## <a name="523-ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>5.2.3 确保存在“删除网络安全组”的活动日志警报

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保存在特定的活动日志警报。

- 特定管理操作应有活动日志警报

## <a name="524-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>5.2.4 确保存在“创建或更新网络安全组规则”的活动日志警报

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保存在特定的活动日志警报。

- 特定管理操作应有活动日志警报

## <a name="525-ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>5.2.5 确保存在“删除网络安全组规则”的活动日志警报

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保存在特定的活动日志警报。

- 特定管理操作应有活动日志警报

## <a name="526-ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>5.2.6 确保存在“创建或更新安全解决方案”的活动日志警报

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保存在特定的活动日志警报。

- 特定安全操作应有活动日志警报

## <a name="527-ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>5.2.7 确保存在“删除安全解决方案”的活动日志警报

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保存在特定的活动日志警报。

- 特定安全操作应有活动日志警报

## <a name="528-ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>5.2.8 确保存在“创建、更新或删除 SQL Server 防火墙规则”的活动日志警报

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保存在特定的活动日志警报。

- 特定管理操作应有活动日志警报

## <a name="529-ensure-that-activity-log-alert-exists-for-update-security-policy"></a>5.2.9 确保存在“更新安全策略”的活动日志警报

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保存在特定的活动日志警报。

- 特定安全操作应有活动日志警报

## <a name="61-ensure-that-rdp-access-is-restricted-from-the-internet"></a>6.1 确保限制从 Internet 进行的 RDP 访问

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保 RDP 访问受到限制。

- 应阻止来自 Internet 的 RDP 访问

## <a name="62-ensure-that-ssh-access-is-restricted-from-the-internet"></a>6.2 确保限制从 Internet 进行的 SSH 访问

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保 SSH 访问受到限制。

- 应阻止来自 Internet 的 SSH 访问

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 确保网络观察程序已启用

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保为部署资源的所有区域启用网络观察程序。 此策略需要一个指定所有适用区域的参数数组。 此策略计划定义中的默认值为“eastus”。

- 应启用网络观察程序

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 确保“OS 磁盘”已加密

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保在虚拟机上启用磁盘加密。

- 应在虚拟机上启用磁盘加密

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 确保“数据磁盘”已加密

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保在虚拟机上启用磁盘加密。

- 应在虚拟机上启用磁盘加密

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 确保加密“未附加的磁盘”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保加密未附加的磁盘。

- 应加密未附加的磁盘

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 确保仅安装已批准的扩展

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保仅安装已批准的虚拟机扩展。 此策略需要一个指定所有已批准虚拟机扩展的参数数组。 此策略计划定义包含客户应该验证的建议默认值。 

- 应仅安装已批准的 VM 扩展

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 确保已应用适用于所有虚拟机的最新 OS 修补程序

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保在虚拟机上安装系统更新。

- 应在计算机上安装系统更新

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 确保已安装适用于所有虚拟机的终结点保护

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保在虚拟机上启用终结点保护。

- 监视 Azure 安全中心 Endpoint Protection 的缺失情况

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 确保 Key Vault 可恢复

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保 Key Vault 对象在意外删除后可恢复。

- 应可恢复 Key Vault 对象

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 在 Azure Kubernetes 服务中启用基于角色的访问控制 (RBAC)

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保使用基于角色的访问控制来管理 Kubernetes 服务群集中的权限

- 应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)

## <a name="91-ensure-app-service-authentication-is-set-on-azure-app-service"></a>9.1 确保在 Azure 应用服务上设置应用服务身份验证

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保对应用服务应用请求进行身份验证。

- 应在 API 应用上启用身份验证
- 应在函数应用上启用身份验证
- 应在 Web 应用上启用身份验证

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 确保 Web 应用将所有 HTTP 流量重定向到 Azure 应用服务中的 HTTPS

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助你确保只能通过安全连接访问 Web 应用程序。

- 只能通过 HTTPS 访问 Web 应用程序

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9.3 确保 Web 应用使用最新版本的 TLS 加密

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助确保 Web 应用使用最新的 TLS 版本。

- 应在 API 应用中使用最新的 TLS 版本
- 应在函数应用中使用最新的 TLS 版本
- 应在 Web 应用中使用最新的 TLS 版本

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9.4 确保 Web 应用的“客户端证书(传入客户端证书)”设置为“打开”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助确保只有具有有效证书的客户端能够访问 Web 应用。

- 确保 API 应用的“客户端证书(传入客户端证书)”设置为“打开”
- 确保函数应用的“客户端证书(传入客户端证书)”设置为“打开”
- 确保 WEB 应用的“客户端证书(传入客户端证书)”设置为“打开”

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9.5 确保在应用服务中启用“注册到 Azure Active Directory”

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助确保 Web 应用使用托管标识。

- 确保在 API 应用中启用“注册到 Azure Active Directory”
- 确保在函数应用中启用“注册到 Azure Active Directory”
- 确保在 WEB 应用中启用“注册到 Azure Active Directory”

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9.6 确保用作 Web 应用一部分的“.Net Framework”版本是最新的

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助确保 Web 应用使用最新版本的 .Net Framework。

- 确保用作 API 应用一部分的“.Net Framework”版本是最新的
- 确保用作函数应用一部分的“.Net Framework”版本是最新的
- 确保用作 Web 应用一部分的“.Net Framework”版本是最新的

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9.7 确保用于运行 Web 应用的“PHP 版本”是最新的

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助确保 Web 应用使用最新版本的 PHP。

- 确保用作 API 应用一部分的“PHP 版本”是最新的
- 确保用作函数应用一部分的“PHP 版本”是最新的
- 确保用作 WEB 应用一部分的“PHP 版本”是最新的

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9.8 确保用于运行 Web 应用的“Python 版本”是最新的

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助确保 Web 应用使用最新版本的 Python。

- 确保用作 API 应用一部分的“Python 版本”是最新的
- 确保用作函数应用一部分的“Python 版本”是最新的
- 确保用作 Web 应用一部分的“Python 版本”是最新的

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9.9 确保用于运行 Web 应用的“Java 版本”是最新的

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助确保 Web 应用使用最新版本的 Java。

- 确保用作 API 应用一部分的“Java 版本”是最新的
- 确保用作函数应用一部分的“Java 版本”是最新的
- 确保用作 Web 应用一部分的“Java 版本”是最新的

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9.10 确保用于运行 Web 应用的“HTTP 版本”是最新的

此蓝图分配的 [Azure Policy](../../../policy/overview.md) 定义可帮助确保 Web 应用使用最新版本的 HTTP。

- 确保用于运行 API 应用的“HTTP 版本”是最新的
- 确保用于运行函数应用的“HTTP 版本”是最新的
- 确保用于运行 Web 应用的“HTTP 版本”是最新的

## <a name="next-steps"></a>后续步骤

了解 CIS Microsoft Azure 基础基准蓝图的控制映射后，接下来请阅读以下文章了解该蓝图，或者在 Azure 门户中访问 Azure Policy 以分配计划：

> [!div class="nextstepaction"]
> [CIS Microsoft Azure 基础基准蓝图 - 概述](./index.md)
> [CIS Microsoft Azure 基础基准蓝图 - 部署步骤](./deploy.md)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。