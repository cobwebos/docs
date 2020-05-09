---
title: Azure 安全基准蓝图示例控制
description: Azure 安全基准蓝图示例到 Azure Policy 的控制映射。
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: f80b72f06532adef28bf5e5afd1eb94c2e34ee2d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691307"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Azure 安全基准蓝图示例的控制映射

以下文章详细说明了 Azure 蓝图 Azure 安全基准蓝图示例如何映射到 Azure 安全基准控制。 有关控制的详细信息，请参阅 [Azure 安全基准](../../../../security/benchmarks/overview.md)。

以下映射是到 Azure 安全基准  控制的映射。 使用右侧的导航栏可直接跳转到特定的控制映射。 映射的控制是通过 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。   然后，找到并选择“ **\[预览\]：** 审核 Azure 安全基准建议并部署特定的支持 VM 扩展”内置策略计划。

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../../../policy/overview.md) 定义关联。 这些策略可以帮助你[评估控件的符合性](../../../policy/how-to/get-compliance-data.md)；但是，控件与一个或多个策略之间通常不是 1:1 或完全匹配。 因此，Azure Policy 中的符合性  仅引用策略本身；这不确保你完全符合控件的所有要求。 此外，符合性标准可能包含目前未由任何 Azure Policy 定义处理的控制。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性蓝图示例的控件和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md)。

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 在虚拟网络上使用网络安全组或 Azure 防火墙来保护资源

- 子网应与网络安全组关联
- 应在面向 Internet 的虚拟机上应用自适应网络强化建议
- 虚拟机应连接到已批准的虚拟网络
- 面向 Internet 的虚拟机应使用网络安全组进行保护
- 服务总线应使用虚拟网络服务终结点
- 应用服务应使用虚拟网络服务终结点
- SQL Server 应使用虚拟网络服务终结点
- 事件中心应使用虚拟网络服务终结点
- Cosmos DB 应使用虚拟网络服务终结点
- Key Vault 应使用虚拟网络服务终结点
- 审核对存储帐户的不受限的网络访问
- 存储帐户应使用虚拟网络服务终结点
- 容器注册表应使用虚拟网络服务终结点
- 虚拟网络应使用指定的虚拟网络网关
- 应在 Kubernetes 服务上定义经授权的 IP 范围
- \[预览\]：应禁用虚拟机上的 IP 转发
- 面向 Internet 的虚拟机应使用网络安全组进行保护
- 应在虚拟机上应用实时网络访问控制
- 应关闭虚拟机上的管理端口

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 监视和记录 VNet、子网和 NIC 的配置和流量

- 应启用网络观察程序

## <a name="13-protect-critical-web-applications"></a>1.3 保护关键的 Web 应用程序

- 确保 WEB 应用的“客户端证书(传入客户端证书)”设置为“打开”
- CORS 不应允许所有资源访问 Web 应用程序
- CORS 不应允许所有资源访问函数应用
- CORS 不应允许所有资源访问 API 应用
- 应当为 Web 应用程序禁用远程调试
- 应当为函数应用禁用远程调试
- 应当为 API 应用禁用远程调试

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 拒绝与已知的恶意 IP 地址通信

- 应启用 DDoS 防护标准版
- 应在虚拟机上应用实时网络访问控制
- 应在面向 Internet 的虚拟机上应用自适应网络强化建议

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 记录网络数据包和流日志

- 应启用网络观察程序

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 使用自动化工具监视网络资源配置并检测更改

- 部署必备组件用于审核“安全选项 - 网络访问”中的 Windows VM 配置
- 显示“安全选项 - Microsoft 网络客户端”中 Windows VM 配置的审核结果
- 部署必备组件以审核“安全选项 - 网络安全性”中的 Windows VM 配置
- 显示“安全选项 - 网络安全性”中 Windows VM 配置的审核结果
- 部署必备组件以审核“安全选项 - Microsoft 网络服务器”中的 Windows VM 配置
- 显示“安全选项 - Microsoft 网络服务器”中 Windows VM 配置的审核结果
- 部署必备组件以审核“管理模板 - 网络”中的 Windows VM 配置
- 显示“管理模板 - 网络”中 Windows VM 配置的审核结果

## <a name="22-configure-central-security-log-management"></a>2.2 配置安全日志集中管理

- 应在虚拟机上安装 Log Analytics 代理
- 应在虚拟机规模集上安装 Log Analytics 代理
- 部署必备组件以审核其上的 Log Analytics 代理未按预期连接的 Windows VM
- 显示其上的 Log Analytics 代理未按预期连接的 Windows VM 的审核结果
- Azure Monitor 日志配置文件应收集“写入”、“删除”和“操作”类别的日志
- Azure Monitor 应从所有区域收集活动日志
- 应该对订阅启用 Log Analytics 监视代理的自动预配

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 为 Azure 资源启用审核日志记录

- 应启用 Azure Data Lake Store 中的诊断日志
- 应启用逻辑应用的诊断日志
- 应启用 IoT 中心的诊断日志
- 应启用 Batch 帐户中的诊断日志
- 应启用虚拟机规模集中的诊断日志
- 应启用事件中心内的诊断日志
- 应启用搜索服务的诊断日志
- 应启用应用服务中的诊断日志
- 应启用 Data Lake Analytics 中的诊断日志
- 应启用 Key Vault 中的诊断日志
- 应启用服务总线中的诊断日志
- 应该在 Azure 流分析中启用诊断日志
- 应启用 SQL 服务器上的审核
- 审核诊断设置

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 从操作系统收集安全日志

- 应该对订阅启用 Log Analytics 监视代理的自动预配
- 应在虚拟机上安装 Log Analytics 代理
- 应在虚拟机规模集上安装 Log Analytics 代理
- 部署必备组件以审核其上的 Log Analytics 代理未按预期连接的 Windows VM
- 显示其上的 Log Analytics 代理未按预期连接的 Windows VM 的审核结果

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 启用针对异常活动的警报

- 应选择安全中心标准定价层
- 应在 SQL 服务器上启用高级数据安全性
- 应在 SQL 托管实例上启用高级数据安全性
- 应在 SQL 服务器的“高级数据安全性”设置中将“高级威胁保护类型”设置为“所有”
- 应在 SQL 托管实例的“高级数据安全性”设置中将“高级威胁保护类型”设置为“所有”

## <a name="28-centralize-anti-malware-logging"></a>2.8 集中进行反恶意软件日志记录

- Microsoft Antimalware for Azure 应配置为自动更新保护签名
- 监视 Azure 安全中心 Endpoint Protection 的缺失情况
- 应在虚拟机规模集上安装 Endpoint Protection 解决方案

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 维护管理帐户的清单

- 只多只为订阅指定 3 个所有者
- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户

## <a name="33-use-dedicated-administrative-accounts"></a>3.3 使用专用管理帐户

- 部署必备组件以审核其中的“管理员”组不只包含指定成员的 Windows VM
- 显示其中的“管理员”组不只包含指定成员的 Windows VM 的审核结果
- 部署先决条件，以便审核在其管理员组中不包含所有指定成员的 Windows VM
- 显示来自 Windows VM 的审核结果，其中的管理员组不包含所有指定成员
- 部署先决条件来审核管理员组包含任意指定成员的 Windows VM
- 显示其中的“管理员”组包含任意指定成员的 Windows VM 的审核结果
- 只多只为订阅指定 3 个所有者
- 应该为你的订阅分配了多个所有者

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 对所有基于 Azure Active Directory 的访问使用多重身份验证

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应对订阅中拥有写入权限的帐户启用 MFA
- 应在对订阅拥有读取权限的帐户上启用 MFA

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 记录管理帐户的可疑活动并发出警报

- 应选择安全中心标准定价层

## <a name="39-use-azure-active-directory"></a>3.9 使用 Azure Active Directory

- 应该为 SQL 服务器预配 Azure Active Directory 管理员
- Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证
- 确保在 API 应用中启用“注册到 Azure Active Directory”
- 确保在 WEB 应用中启用“注册到 Azure Active Directory”
- 确保在函数应用中启用“注册到 Azure Active Directory”

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 定期查看和协调用户访问权限

- 应从订阅中删除弃用的帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有读取权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户
- 应从订阅中删除拥有所有者权限的外部帐户

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 维护敏感信息的清单

- 应对 SQL 数据库中的敏感数据进行分类

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 加密传输中的所有敏感信息

- 应该启用安全传输到存储帐户
- 应在 API 应用中使用最新的 TLS 版本
- 应在 Web 应用中使用最新的 TLS 版本
- 应在函数应用中使用最新的 TLS 版本
- 应该只能通过 HTTPS 访问函数应用
- 只能通过 HTTPS 访问 Web 应用程序
- 只能通过 HTTPS 访问 API 应用
- 应为 MySQL 数据库服务器启用“强制 SSL 连接”
- 应为 PostgreSQL 数据库服务器启用“强制 SSL 连接”
- 应该启用只能通过安全方式连接到 Redis 缓存

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 使用有效的发现工具识别敏感数据

- 应对 SQL 数据库中的敏感数据进行分类
- 应在 SQL 服务器上启用高级数据安全性
- 应在 SQL 托管实例上启用高级数据安全性

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 使用 Azure RBAC 控制对资源的访问

- 应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)
- 审核自定义 RBAC 规则的使用

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 加密静态的敏感信息

- 应在 SQL 数据库上启用透明数据加密
- 应在虚拟机上启用磁盘加密
- 应加密未附加的磁盘
- 应使用自己的密钥加密 SQL 服务器的 TDE 保护器
- 应使用自己的密钥加密 SQL 托管实例的 TDE 保护器
- 自动化帐户变量应进行加密
- Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 记录对关键 Azure 资源的更改并发出警报

- Azure Monitor 应从所有区域收集活动日志

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 运行自动化漏洞扫描工具

- 应对 SQL Server 启用漏洞评估
- 应对 SQL 托管实例启用漏洞评估
- \[预览版\] 应在虚拟机上启用漏洞评估
- SQL Server 的漏洞评估设置应包含用来接收扫描报告的电子邮件地址

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 部署操作系统修补程序自动化管理解决方案

- 应在计算机上安装系统更新
- 应在虚拟机规模集上安装系统更新
- 如果在函数应用中使用“.Net Framework”，请确保它是最新版
- 如果在 Web 应用中使用“.Net Framework”，请确保它是最新版
- 如果在 API 应用中使用“.Net Framework”，请确保它是最新版

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 部署第三方软件修补程序自动化管理解决方案

- 确保用作 API 应用一部分的“PHP 版本”是最新的
- 确保用作 WEB 应用一部分的“PHP 版本”是最新的
- 确保用作函数应用一部分的“PHP 版本”是最新的
- 确保用作 Web 应用一部分的“Java 版本”是最新的
- 确保用作函数应用一部分的“Java 版本”是最新的
- 确保用作 API 应用一部分的“Java 版本”是最新的
- 确保用作 Web 应用一部分的“Python 版本”是最新的
- 确保用作函数应用一部分的“Python 版本”是最新的
- 确保用作 API 应用一部分的“Python 版本”是最新的
- Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 使用风险评分流程确定所发现漏洞的修正优先级

- 应该通过漏洞评估解决方案修复漏洞
- 应该修复计算机上安全配置中的漏洞
- 应该修复虚拟机规模集上安全配置中的漏洞
- 应该修复容器安全配置中的漏洞
- 应该修复 SQL 数据库中的漏洞

## <a name="68-use-only-approved-applications"></a>6.8 只使用已批准的应用程序

- 应选择安全中心标准定价层
- 应在虚拟机上启用自适应应用程序控制

## <a name="69-use-only-approved-azure-services"></a>6.9 只使用已批准的 Azure 服务

- 应将虚拟机迁移到新的 Azure 资源管理器资源
- 应将存储帐户迁移到新 Azure 资源管理器资源

## <a name="610-implement-approved-application-list"></a>6.10 实现已批准的应用程序列表

- 应选择安全中心标准定价层
- 应在虚拟机上启用自适应应用程序控制

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 维护安全的 Azure 资源配置

- \[预览\]：应在 Kubernetes 服务上定义 Pod 安全策略

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 维护安全的操作系统配置

- 应该修复计算机上安全配置中的漏洞
- 应该修复容器安全配置中的漏洞
- 应该修复虚拟机规模集上安全配置中的漏洞

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 针对 Azure 服务实现自动化配置监视

- \[预览\]：应在 Kubernetes 服务上定义 Pod 安全策略

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 针对操作系统实现自动化配置监视

- 应该修复计算机上安全配置中的漏洞
- 应该修复容器安全配置中的漏洞
- 应该修复虚拟机规模集上安全配置中的漏洞

## <a name="711-manage-azure-secrets-securely"></a>7.11 安全地管理 Azure 机密

- 应可恢复 Key Vault 对象

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 安全且自动地管理标识

- 应在函数应用中使用托管标识
- 应在 Web 应用中使用托管标识
- 应在 API 应用中使用托管标识

## <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 使用集中管理的反恶意软件

- 监视 Azure 安全中心 Endpoint Protection 的缺失情况
- 应在虚拟机规模集上安装 Endpoint Protection 解决方案

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 预扫描要上传到非计算 Azure 资源的文件

- 应选择安全中心标准定价层

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 确保反恶意软件和签名已更新

- Microsoft Antimalware for Azure 应配置为自动更新保护签名

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 确保定期执行自动备份

- 应为 Azure SQL 数据库启用长期异地冗余备份
- 应为 Azure Database for MySQL 启用异地冗余备份
- 应为 Azure Database for PostgreSQL 启用异地冗余备份
- 应为 Azure Database for MariaDB 启用异地冗余备份
- 应为虚拟机启用 Azure 备份

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 执行完整的系统备份并备份任何客户托管密钥

- 应为 Azure SQL 数据库启用长期异地冗余备份
- 应为 Azure Database for MySQL 启用异地冗余备份
- 应为 Azure Database for PostgreSQL 启用异地冗余备份
- 应为 Azure Database for MariaDB 启用异地冗余备份
- 应为虚拟机启用 Azure 备份

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 确保保护备份和客户托管密钥

- 应可恢复 Key Vault 对象

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 创建事件评分和优先级确定过程

- 应选择安全中心标准定价层

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 提供安全事件联系人详细信息并针对安全事件配置警报通知

- 应为订阅提供安全联系人电子邮件地址
- 应为订阅提供安全联系人电话号码
- SQL 服务器的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址
- SQL 托管实例的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址
- 应在 SQL 服务器高级数据安全设置中为管理员和订阅所有者启用电子邮件通知
- 应在 SQL 托管实例高级数据安全设置中启用“向管理员和订阅所有者发送电子邮件通知”

## <a name="next-steps"></a>后续步骤

查看 Azure 安全基准蓝图的控制映射后，在 Azure 门户中访问 Azure Policy 来分配计划：

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。