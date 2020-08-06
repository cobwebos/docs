---
title: Azure 开发测试实验室的 azure 安全基准
description: Azure 开发测试实验室的 azure 安全基准
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 158ead7531b0b3da2e495e36e40e761961bea498
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87761002"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azure 开发测试实验室的 azure 安全基准

Azure 开发测试实验室的 Azure 安全基准包含的建议可帮助你提高部署的安全状况。

此服务的基线取自[Azure 安全基准1.0 版](../security/benchmarks/overview.md)，其中提供了有关如何在 Azure 上保护云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源
**指南：** Microsoft 维护 Azure 资源的时间源。 不过，你可以管理计算资源的时间同步设置。

请参阅以下文章，了解如何配置 Azure 计算资源的时间同步： [azure 中 Windows vm 的时间同步](../virtual-machines/windows/time-sync.md)。 

**Azure 安全中心监视：** 当前不可用

**责任：** 微软

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理
**指南：** 启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区、Azure 事件中心或 Azure 存储帐户以进行存档。 活动日志可让你深入了解在 Azure 开发测试实验室实例的管理平面上完成的操作。 使用 Azure 活动日志数据，您可以确定任何写入操作的 "内容、人员和时间" (PUT、POST、DELETE) 在开发测试实验室实例的管理平面级别完成。

有关详细信息，请参阅[创建诊断设置以将平台日志和指标发送到不同的目标](../azure-monitor/platform/diagnostic-settings.md)。

**Azure 安全中心监视：** 当前不可用

**责任：** 面向

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录
**指南：** 启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区、Azure 事件中心或 Azure 存储帐户以进行存档。 活动日志可让你深入了解在 Azure 开发测试实验室实例的管理平面上完成的操作。 使用 Azure 活动日志数据，可以确定任何写入操作的 "操作内容、人员和时间" (PUT、POST、DELETE) 在开发测试实验室实例的管理平面级别完成。

有关详细信息，请参阅[创建诊断设置以将平台日志和指标发送到不同的目标](../azure-monitor/platform/diagnostic-settings.md)。

**Azure 安全中心监视：** 当前不可用

**责任：** 面向

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志
**指南：** Azure 开发测试实验室虚拟机 (Vm) 由客户创建和拥有。 因此，组织负责监视它。 可以使用 Azure 安全中心监视计算操作系统。 安全中心从操作系统收集的数据包括 OS 类型和版本、OS（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址和登录用户。 Log Analytics 代理还会收集故障转储文件。

有关详细信息，请参阅下列文章： 

- [如何使用 Azure Monitor 收集 Azure 虚拟机内部主机日志](../azure-monitor/learn/quick-collect-azurevm.md)
- [了解 Azure 安全中心数据收集](../security-center/security-center-enable-data-collection.md)

**Azure 安全中心监视：** 是的

**责任：** 面向

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期
***指南：** 在 Azure Monitor 中，根据组织的符合性规定，设置与 Azure 开发测试实验室实例关联的 Log Analytics 工作区的日志保持期。

有关详细信息，请参阅以下文章：[如何设置日志保持参数](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志
**指南：** 启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中运行查询以搜索术语，确定趋势，分析模式，并根据可能已为 Azure 开发测试实验室收集的活动日志数据提供许多其他见解。

有关详细信息，请参阅下列文章：

- [如何启用 Azure 活动日志的诊断设置](../azure-monitor/platform/diagnostic-settings.md)
- [如何收集和分析 Azure Monitor 的 Log Analytics 工作区中的 Azure 活动日志](../azure-monitor/platform/activity-log.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：针对异常活动启用警报
**指南：** 使用 Azure Log Analytics 工作区监视和警报与 Azure 开发测试实验室相关的安全日志和事件中的异常活动。

有关详细信息，请参阅以下文章：[如何在 log analytics 日志数据上发出警报](../azure-monitor/learn/tutorial-response.md)

**Azure 安全中心监视：** 当前不可用

**责任：** 面向

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录
**指南：** 不适用。 Azure 开发测试实验室不会处理或产生与反恶意软件相关的日志。

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录
**指南：** 不适用。 Azure 开发测试实验室不会处理或产生与 DNS 相关的日志。

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录
**指南：** Azure 开发测试实验室创建由客户拥有和管理的 Azure 计算计算机。 使用所有受支持的 Azure Windows Vm 上的 Microsoft Monitoring Agent，记录进程创建事件和 `CommandLine` 字段。 对于支持的 Azure Linux Vm，你可以基于每个节点手动配置控制台日志记录，并使用 Syslog 来存储数据。 同时，使用 Azure Monitor 的 Log Analytics 工作区查看日志，并对来自 Azure Vm 的已记录数据运行查询。

- [Azure 安全中心中的数据收集](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [如何在 Azure Monitor 中运行自定义查询](../azure-monitor/log-query/get-started-queries.md)
- [Azure Monitor 中的 Syslog 数据源](../azure-monitor/platform/data-sources-syslog.md)

**Azure 安全中心监视：** 是的

**责任：** 面向

## <a name="identity-and-access-control"></a>标识和访问控制
有关详细信息，请参阅[安全控制：标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单
**指南：** Azure Active Directory (Azure AD) 包含内置角色，必须显式分配这些角色并可查询。 使用 Azure AD PowerShell 模块运行即席查询，以发现属于管理组成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)
- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)
- [Azure 开发测试实验室角色](devtest-lab-add-devtest-user.md)  

**Azure 安全中心监视：** 是的

**责任：** 面向

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码
**指南：** Azure Active Directory (Azure AD) 没有默认密码的概念。 其他需要密码的 Azure 资源会强制创建具有复杂性要求和最小密码长度要求的密码，这些要求因服务而异。 你负责第三方应用程序和可能使用默认密码的 Marketplace 服务。

开发测试实验室没有默认密码的概念。 

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户
**指南：** 围绕使用专用管理帐户创建标准操作过程。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：

- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

- [如何使用 Azure 安全中心监视标识和访问（预览）](../security-center/security-center-identity-access.md)  
- [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Azure 开发测试实验室角色](devtest-lab-add-devtest-user.md)  

**Azure 安全中心监视：** 是的

**责任：** 面向

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用
**指南：** 开发测试实验室使用 Azure AD 服务进行标识管理。 当你为用户授予基于开发测试实验室的环境的访问权限时，请考虑以下两个重要方面：

- **资源管理：** 它提供对 Azure 门户的访问权限，以管理 (创建 Vm、创建环境、启动、停止、重新启动、删除和应用) 项目等资源的资源。 资源管理通过使用基于角色的访问控制 (RBAC) 在 Azure 中完成。 你将角色分配给用户，并设置资源和访问级别权限。
- **虚拟机 (网络级) **：在默认配置中，vm 使用本地管理员帐户。 如果域服务 (Azure AD 域服务、本地域或基于云的域) ，则可以将计算机加入域。 然后，用户可以使用域加入项目将其基于域的标识连接到计算机。 

- [开发测试实验室的参考体系结构](devtest-lab-reference-architecture.md#architecture)
- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证
**指南：** 启用 Azure Active Directory (AD) 多因素身份验证 (MFA) 并遵循 Azure 安全中心的标识和访问管理建议。

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)  
- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视：*** 是

**责任：** 面向


### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）
**指南：** 使用特权访问工作站 (Paw) ，其配置为登录和配置 Azure 资源。

- [了解特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)  
- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)  

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报
**指南：** 当环境中发生可疑或不安全的活动时，使用 Azure Active Directory (Azure AD) 安全报告来生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

- [如何确定标记为存在风险活动的 Azure AD 用户](../active-directory/identity-protection/overview-identity-protection.md)  
- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)  

**Azure 安全中心监视：** 当前不可用

**责任：** 面向

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源
**指南：** 使用条件性访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问。

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)  

**Azure 安全中心监视：** 当前不可用

**责任：** 面向

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory
**指南：** 使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)  

**Azure 安全中心监视：** 当前不可用

**责任：** 面向

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问
**指南：** Azure Active Directory (Azure AD) 会提供日志，以帮助发现陈旧的帐户。 此外，使用 Azure 标识访问评审来有效地管理组成员身份、访问企业应用程序和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

- [了解 Azure AD 报告](../active-directory/reports-monitoring/overview-reports.md)  
- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)  

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视访问已停用帐户的企图
**指南：** 你有权访问 Azure Active Directory (Azure AD) 登录活动、审核和风险事件日志源，这允许你与任何安全信息和事件管理 (SIEM) /Monitoring 工具集成。

可以通过为 Azure Active Directory 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 你可以在 Log Analytics 工作区中配置警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)  

**Azure 安全中心监视：** 当前不可用

**责任：** 面向

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报
**指南：** 使用 Azure Active Directory (Azure AD) 的风险和标识保护功能，配置对检测到的与用户标识相关的可疑操作的自动响应。

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)  
- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)  

**Azure 安全中心监视：** 当前不可用

**责任：** 面向

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限
**指南：** Azure 开发测试实验室目前不支持客户密码箱。

- [支持客户密码箱的服务列表](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability) 

**Azure 安全中心监视：** 不适用

**责任：** 面向

## <a name="data-protection"></a>数据保护
有关详细信息，请参阅[安全控制：数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单
**指南：** 使用标记帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](../azure-resource-manager/resource-group-using-tags.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统
**指南：** 为开发、测试和生产实施单独的订阅或管理组。 Azure 开发测试实验室实例应由虚拟网络/子网分隔并正确标记。 

- [如何创建其他 Azure 订阅](../billing/billing-create-subscription.md)
- [如何创建管理组](../governance/management-groups/create.md)
- [如何为开发测试实验室配置虚拟网络](devtest-lab-configure-vnet.md)
- [如何创建和使用标记](../azure-resource-manager/resource-group-using-tags.md)
- [如何创建和使用开发测试实验室的标记](devtest-lab-add-tag.md)

**Azure 安全中心监视：** 当前不可用

**责任：** 面向

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输
**指南：** 尚不可用;Azure 开发测试实验室尚不支持数据标识、分类和丢失防护功能。

Microsoft 管理 Azure 开发测试实验室的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视：** 当前不可用

**责任：** 共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息
**指南：** 默认情况下，Azure 开发测试实验室需要 TLS 加密的通信。 目前支持 TLS 版本1.2。 如果客户端库或工具不支持 TLS，则可以通过 Azure 门户或管理 API 来启用未加密的连接。 在这种情况下，不可能进行加密连接，因此建议将实验室和客户端应用程序放入虚拟网络。

[了解开发测试实验室的传输方案中的加密](https://techcommunity.microsoft.com/t5/azure-developer-community-blog/azure-devtest-labs-enforcing-tls-1-2-starting-may-01-2020/ba-p/1236279)

**Azure 安全中心监视：** 是的

**责任：** 共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据
**指南：** Azure 开发测试实验室尚不支持数据标识、分类和丢失防护功能。 标记包含敏感信息的实例，并根据需要实现第三方解决方案（如有必要）。

对于由 Microsoft 管理的底层平台，Microsoft 将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视：** 当前不可用

**责任：** 面向

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问
**指南：** 使用 Azure Active Directory (Azure AD) 基于角色的访问控制 (RBAC) 来控制对 Azure 开发测试实验室中的实验室的访问。

- [如何在 Azure 中配置 RBAC](../role-based-access-control/role-assignments-portal.md)
- [了解开发测试实验室中的角色](devtest-lab-add-devtest-user.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制
**指南：** 如有必要在开发测试实验室中创建的计算资源的符合性，请实现第三方工具（例如基于主机的自动数据丢失防护解决方案），以便在将数据复制到系统时，对数据强制实施访问控制。

对于由 Microsoft 管理的底层平台，Microsoft 将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视：** 不适用

**责任：** 不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息
**指南：** Azure 开发测试实验室存储以下客户数据：

- 包含应用项目生成的部署和扩展日志的[项目结果](add-artifact-vm.md)
- 用于从公式创建虚拟机的[公式文档](devtest-lab-manage-formulas.md)
- 实验室虚拟机的 OS 和数据磁盘 

项目结果和公式文档将发送到作为每个实验室部署的一部分创建的 Azure 存储帐户。 Azure 存储中的数据将使用 256 位 AES 加密法（可用的最强大块加密法之一）以透明方式进行加密和解密，并符合 FIPS 140-2 规范。 无法禁用 Azure 存储加密。 可以依赖于使用 Microsoft 托管的密钥来加密存储帐户，或者，可以使用自己的密钥来管理加密。 有关详细信息，请参阅[实验室存储帐户的加密](encrypt-storage.md)。

默认情况下，使用平台托管密钥对所有实验室 OS 和数据磁盘进行加密。 写入到现有托管磁盘的所有托管磁盘、快照、映像和数据都将通过平台管理的密钥自动进行静态加密。 作为实验室所有者，你可以配置要使用客户托管密钥加密的实验室操作系统磁盘。 使用实验室数据磁盘的客户托管密钥的加密当前无法通过实验室本身进行配置。 不过，订阅管理员现在可以为订阅中的实验室磁盘配置此设置。 有关详细信息，请参阅[使用客户托管密钥加密实验室开发测试 LABS OS 磁盘](encrypt-disks-customer-managed-keys.md)。

**Azure 安全中心监视：** 不适用

**责任：** 共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报
**指南：** 将 Azure Monitor 与 Azure 活动日志结合使用，以便为开发测试实验室实例和其他关键或相关资源的发生更改创建警报。

- [如何为 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)
- [如何创建开发测试 Labs 活动日志事件的警报](create-alerts.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向



## <a name="vulnerability-management"></a>漏洞管理
有关详细信息，请参阅[安全控制：漏洞管理。](../security/benchmarks/security-control-vulnerability-management.md)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具
**指南：** 请根据 Azure 安全中心的建议，确保 Azure 开发测试实验室实例和相关资源的安全。

Microsoft 对支持 Azure 开发测试实验室的底层资源执行漏洞管理。

- [了解 Azure 安全中心建议](../security-center/recommendations-reference.md) 

**Azure 安全中心监视：** 是的

**责任：** 共享

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案
**指南：** 使用 Azure 更新管理确保在开发测试实验室中托管的 Windows 和 Linux Vm 上安装最新的安全更新。 对于 Windows 虚拟机，请确保已启用 Windows 更新并将其设置为自动更新。 此设置当前不可通过开发测试实验室配置，但实验室管理员/订阅管理员可以在其订阅中的基础计算 Vm 上配置此设置。 

- [如何在 Azure 中为 Vm 配置更新管理](../automation/update-management/update-mgmt-overview.md)
- [了解通过安全中心监视的 Azure 安全策略](../security-center/security-center-policy-definitions.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方自动软件修补管理解决方案
***指南：*** 作为实验室管理员，你可以使用[开发测试实验室项目](add-artifact-vm.md)自动更新对实验室自定义映像的更新，包括安全修补程序和其他更新。 

了解有关[开发测试实验室映像工厂](image-factory-create.md)的详细信息，它是一种配置为代码的解决方案，可定期自动构建和分发映像以及所有需要的配置。 

作为订阅管理员，你还可以使用 Azure 更新管理解决方案来管理开发测试实验室 Vm 的更新和修补程序。 更新管理依赖于本地配置的更新存储库来修补受支持的 Windows 系统。 利用 System Center Updates Publisher (Updates Publisher) 的工具，您可以将自定义更新发布到 Windows Server Update Services (WSUS) 中。 此方案允许更新管理将使用 Configuration Manager 作为其更新存储库的计算机与第三方软件配合使用。

- [Azure 中的更新管理解决方案](../automation/update-management/update-mgmt-overview.md)
- [管理 Vm 的更新和修补程序](../automation/update-management/update-mgmt-overview.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描
**指南：** 按一致的间隔导出扫描结果，并比较结果以验证是否已修正了漏洞。 使用 Azure 安全中心建议的漏洞管理建议时，客户可能会透视到所选解决方案的门户以查看历史扫描数据。

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级
**指南：** 使用 Azure 安全中心提供 (安全分数) 的默认风险评级。

- [了解 Azure 安全中心安全分数](../security-center/secure-score-security-controls.md)

**Azure 安全中心监视：** 是的

**责任：** 面向

## <a name="inventory-and-asset-management"></a>库存和资产管理
有关详细信息，请参阅[安全控制：清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动资产发现解决方案
**指南：** 使用 Azure 资源关系图可查询和发现所有资源 (包括订阅中) 的开发测试实验室资源。 确保你具有适当的 (读取租户中的) 权限，并且可以枚举订阅中的所有 Azure 订阅和资源。

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)
- [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)
- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据
**指南：** 将标记应用于 Azure 资源，使元数据根据分类逻辑组织它们。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)
- [配置开发测试实验室的标记](devtest-lab-add-tag.md)

**Azure 安全中心监视：** 不可用

**责任：** 面向

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源
**指南：** 使用标记、管理组和单独的订阅，并根据需要单独的实验室来组织和跟踪实验室与实验室相关的资源。 定期协调清点，并确保从订阅快速删除未经授权的资源。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)
- [如何创建管理组](../governance/management-groups/create.md)
- [如何使用开发测试实验室创建实验室](devtest-lab-create-lab.md)
- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)
- [如何为实验室配置标记](devtest-lab-add-tag.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义和维护已批准的 Azure 资源的清单
**指南：** 根据组织的需要，为计算资源创建已批准的 Azure 资源和批准的软件的清单。 作为订阅管理员，你还可以使用自适应应用程序控件（Azure 安全中心的一项功能）来帮助你定义一组允许在已配置的实验室计算机上运行的应用程序。 此功能适用于 Azure 和非 Azure Windows（所有版本，不管是经典部署模型还是 Azure 资源管理器部署模型）和 Linux 计算机。

- [如何启用自适应应用程序控制](../security-center/security-center-adaptive-application.md)
 
**Azure 安全中心监视：** 不适用**责任：** 客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源
**指南：** 使用 Azure 策略将对可在客户订阅中创建的资源类型的限制 () 使用以下内置策略定义：

- 不允许的资源类型
- 允许的资源类型

同时，使用 Azure 资源关系图在)  (中查询/发现订阅中的资源。 它可以在基于高安全性的环境（例如，具有存储帐户的环境）中提供帮助。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序
**指南：** Azure Automation 在部署、操作和解除工作负荷和资源的实现过程中提供完全控制。 作为订阅管理员，你可以使用 Azure 虚拟机库存自动收集有关你的订阅中的开发测试实验室 Vm 的所有软件的信息。 "软件名称"、"版本"、"发布者" 和 "刷新时间" 属性可从 Azure 门户中获取。 若要获取安装日期和其他信息的访问权限，客户需要启用来宾级别诊断，并将 Windows 事件日志引入 Log Analytics 工作区。

除了使用更改跟踪来监视软件应用程序外，Azure 安全中心的自适应应用程序控件还可以使用机器学习来分析计算机上运行的应用程序，并通过此智能创建允许列表。 此功能极大地简化了配置和维护应用程序允许列表策略的过程，使你能够避免在你的环境中使用不需要的软件。 你可以配置审核模式或强制模式。 审核模式只审核受保护 Vm 上的活动。 强制模式确实强制执行这些规则，并确保阻止不允许运行的应用程序。 

- [Azure 自动化简介](../automation/automation-intro.md)
- [如何启用 Azure VM 清单](../automation/automation-tutorial-installed-software.md)
- [了解自适应应用程序控件](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向


### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序
**指南：** Azure Automation 在部署、操作和解除工作负荷和资源的实现过程中提供完全控制。 作为订阅管理员，你可以使用更改跟踪来确定在开发测试实验室中托管的 Vm 上安装的所有软件。 你可以实现自己的进程，或使用 Azure 自动化状态配置来删除未经授权的软件。

- [Azure 自动化简介](../automation/automation-intro.md)
- [使用更改跟踪解决方案跟踪环境中的更改](../automation/change-tracking.md)
- [Azure 自动化状态配置概述](../automation/automation-dsc-overview.md)

**Azure 安全中心监视：** 不可用

**责任：** 面向

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序
**指南：** 作为订阅管理员，你可以使用 Azure 安全中心自适应应用程序控制来确保只执行已授权的软件，并且阻止所有未经授权的软件在开发测试实验室中托管的 Azure Vm 上执行。

- [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视：** 是的

**责任：** 面向

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务
**指南：** 使用 Azure 策略将对可在客户订阅中创建的资源类型的限制 () 使用以下内置策略定义： 

- 不允许的资源类型
- 允许的资源类型

请参阅以下文章： 
- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/not-allowed-resource-types.md)

**Azure 安全中心监视：** 是的

**责任：** 面向


### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单
**指南：** 自适应应用程序控制是 Azure 安全中心的一种智能的、自动化的端到端解决方案，可帮助你控制哪些应用程序可在 Azure 和非 Azure 计算机上运行 (Windows 和 Linux) ，开发测试实验室托管。 请注意，你必须是订阅管理员才能为开发测试实验室中托管的基础计算资源配置此设置。 如果此设置不符合组织的要求，请实现第三方解决方案。

- [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力
**指南：** 使用 Azure 条件性访问，通过为**Microsoft Azure 管理**应用配置 "**阻止访问**"，限制用户与 Azure 资源管理器的交互能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视：** 是的

**责任：** 面向

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能
**指南：** 根据脚本的类型，可能会使用特定于操作系统的配置或第三方资源来限制用户在开发测试实验室中托管的 Vm 内执行脚本的能力。 你还可以使用 Azure 安全中心的自适应应用程序控制，以确保仅执行授权的软件，并且阻止所有未经授权的软件在底层 Azure Vm 上执行。

- [如何在 Windows 环境中控制 PowerShell 脚本的执行](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)
- [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视：** 不可用

**责任：** 面向


### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：物理或逻辑上分离高风险应用程序
**指南：** 你的 Azure 环境中部署的高风险应用程序可以通过虚拟网络、子网、订阅、管理组等进行隔离。 使用 Azure 防火墙、Web 应用程序防火墙 (WAF) 或网络安全组 (NSG) 充分保护。

- [为开发测试实验室配置虚拟网络](devtest-lab-configure-vnet.md)
- [Azure 防火墙概述](../firewall/overview.md)
- [Web 应用程序防火墙概述](../web-application-firewall/overview.md)
- [网络安全概述](../virtual-network/security-overview.md)
- [Azure 虚拟网络概述]()
- [使用 Azure 管理组来组织资源](../governance/management-groups/overview.md)
- [订阅决策指南](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure 安全中心监视：** 不可用

**责任：** 面向

## <a name="secure-configuration"></a>安全配置
**有关详细信息，请参阅安全控制：安全配置。**

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置
**指南：** 使用 Azure 策略别名创建自定义策略，以便审核或强制执行在开发测试实验室中创建的 Azure 资源的配置。 你还可以使用内置的 Azure Policy 定义。

此外，Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足/超过组织的安全要求。

还可以使用来自 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

- [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)
- [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)
- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)
- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置
**指南：** 使用 Azure 安全中心建议为在开发测试实验室中创建的所有基础计算资源保留安全配置。 此外，你可以使用自定义的操作系统映像或 Azure 自动化状态配置或开发测试实验室项目来建立你的组织所需的操作系统的安全配置。

- [如何监视 Azure 安全中心建议](../security-center/security-center-recommendations.md)
- [安全建议 - 参考指南](../security-center/recommendations-reference.md)
- [Azure Automation State Configuration 概述](../automation/automation-dsc-overview.md)
- [在 Azure 中上传 VHD 并使用它创建新 Windows VM](../virtual-machines/windows/upload-generalized-managed.md)
- [使用 Azure CLI 从自定义磁盘创建 Linux VM](../virtual-machines/linux/upload-vhd.md)
- [创建自定义映像并将其分发到多个开发测试实验室](image-factory-save-distribute-custom-images.md)

**Azure 安全中心监视：** 不适用

**责任：** 不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置
**指南：** 使用 Azure 策略 "**拒绝**并**部署（如果不存在**）" 规则在开发测试实验室中创建的 azure 资源上强制实施安全设置。 此外，你可以使用 Azure 资源管理器模板来维护你的组织所需的 Azure 资源的安全配置。

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)
- [创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)
- [Azure 资源管理器模板概述](../azure-resource-manager/templates/overview.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置
**指南：** 遵循 Azure 安全中心的建议，在作为实验室的一部分创建的底层 Azure 计算资源上执行漏洞评估。 此外，还可以使用 Azure 资源管理器模板、自定义操作系统映像或 Azure 自动化状态配置来维护组织所需的操作系统的安全配置。 你还可以使用 "映像工厂" 解决方案，它是一种配置即代码解决方案，它使用所有所需配置定期自动生成和分发映像。

此外，Microsoft 发布的 Azure Marketplace 虚拟机映像由 Microsoft 进行管理和维护。

- [如何实现 Azure 安全中心漏洞评估建议](../security-center/security-center-vulnerability-assessment-recommendations.md)
- [Azure Automation State Configuration 概述](../automation/automation-dsc-overview.md)
- [将 VHD 上传到 Azure 并创建新的 VM 的示例脚本](../virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script.md)
- [如何在开发测试实验室中创建映像工厂](image-factory-create.md)

**Azure 安全中心监视：** 是的

**责任：** 共享

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置
**指南：** 使用 Azure DevOps 来安全地存储和管理代码，如自定义 Azure 策略、Azure 资源管理器模板和所需的状态配置脚本。 若要访问在 Azure DevOps 中管理的资源，可以授予或拒绝特定用户、内置安全组或 (Azure Active Directory 中定义的组的权限（如果与 Azure DevOps 集成） Azure AD) 。

- [Azure Repos Git 教程](/devops/repos/git/gitworkflow?view=azure-devops)
- [关于权限和组](/devops/organizations/security/about-permissions?view=azure-devops&tabs=preview-page)
- [Azure 开发测试实验室与 Azure DevOps 工作流之间的集成](devtest-lab-dev-ops.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像
**指南：** 如果使用自定义映像，请使用基于角色的访问控制 (RBAC) ，确保只有经过授权的用户才能访问这些映像。 使用共享映像库，你可以将映像共享到需要它的特定实验室。 对于容器映像，请将其存储在 Azure 容器注册表中，并使用 RBAC 确保只有经过授权的用户才能访问该映像。

- [了解 Azure 中的 RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)
- [如何在 Azure 中配置 RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)
- [为开发测试实验室配置共享映像库](configure-shared-image-gallery.md)
- [了解容器注册表的 RBAC](../container-registry/container-registry-roles.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具
**指南：** 使用 Azure 策略定义和实现 Azure 资源的标准安全配置。 使用 Azure 策略别名创建自定义策略，以审核或强制执行在开发测试实验室中创建的 Azure 资源的网络配置。 还可以使用与特定资源相关的内置策略定义。 此外，你也可以使用 Azure 自动化来部署配置更改。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [如何使用别名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具
**指南：** Azure 自动化状态配置是一项配置管理服务，适用于任何云或本地数据中心内的所需状态配置 (DSC) 节点。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。 你还可以编写可在每个实验室计算机上安装的自定义项目，以确保它们遵循组织策略。 

- [加入 Azure Automation State Configuration 管理的计算机](../automation/automation-dsc-onboarding.md)
- [为开发测试实验室虚拟机创建自定义项目](devtest-lab-artifact-author.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实施自动配置监视
**指南：** 使用 Azure 安全中心对在开发测试实验室中创建的 Azure 资源执行基线扫描。 此外，使用 Azure Policy 警告和审核 Azure 资源配置。

- [如何在 Azure 安全中心修正建议](../security-center/security-center-remediate-recommendations.md)
 
**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视
**指南：** 使用 Azure 安全中心对 OS 和容器的 Docker 设置执行基线扫描。

- [了解 Azure 安全中心容器建议](../security-center/security-center-container-recommendations.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密
**指南：** 将托管服务标识与 Azure Key Vault 结合使用，为你的云应用程序简化和安全密钥管理。

- [在开发测试实验室中配置托管标识以部署 Azure 资源管理器环境](use-managed-identities-environments.md)
- [在开发测试实验室中配置托管标识以部署虚拟机](enable-managed-identities-lab-vms.md)
- [如何创建密钥保管库](../key-vault/quick-create-portal.md)
- [如何使用托管标识提供 Key Vault 身份验证](../key-vault/managed-identity.md)

**Azure 安全中心监视：** 是的

**责任：** 面向

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识
**指南：** 使用托管标识在 Azure AD 中通过自动管理的标识来提供 Azure 服务。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

- [在开发测试实验室中配置托管标识以部署 Azure 资源管理器环境](use-managed-identities-environments.md)
- [在开发测试实验室中配置托管标识以部署虚拟机](enable-managed-identities-lab-vms.md)
 
**Azure 安全中心监视：** 是的

**责任：** 面向

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露
**指南：** 实现凭据扫描器来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

- 如何设置凭据扫描器

**Azure 安全中心监视：** 不适用

**责任：** 面向


## <a name="malware-defense"></a>恶意软件防护
有关详细信息，请参阅*安全控制：恶意软件防护*。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件
**指南：** 使用适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件持续监视和保护你的资源。 对于 Linux，请使用第三方反恶意软件解决方案。 另外，使用 Azure 安全中心的数据服务威胁检测来检测上传到存储帐户的恶意软件。

- 如何为云服务和虚拟机配置 Microsoft Antimalware
- Azure 安全中心的威胁防护

**Azure 安全中心监视：** 是的

**责任：** 面向

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件
**指南：** 支持 Azure 服务的基础主机上启用了 Microsoft 反恶意软件 (例如，Azure App Service 托管在实验室) ，但它不会在内容上运行。
预先扫描上传到非计算 Azure 资源的任何文件，例如应用服务、Data Lake Storage、Blob 存储等。

使用 Azure 安全中心的数据服务威胁检测来检测上传到存储帐户的恶意软件。

- 了解适用于 Azure 云服务和虚拟机的 Microsoft Antimalware
- 了解 Azure 安全中心的数据服务威胁检测

**Azure 安全中心监视：** 是的

**责任：** 不适用


### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新
**指南：** 部署后，默认情况下，Microsoft 反恶意软件将自动安装最新的签名、平台和引擎更新。 遵循 Azure 安全中心中的建议： "计算 & 应用"，确保开发测试实验室基础计算资源的所有终结点都是最新的签名。 Windows OS 可以通过附加的安全性进行进一步保护，以通过与 Azure 安全中心集成的 Microsoft Defender 高级威胁防护服务来限制受病毒或恶意软件攻击的风险。

- 如何为 Azure 云服务和虚拟机部署 Microsoft Antimalware
- Microsoft Defender 高级威胁防护

**Azure 安全中心监视：** 是的

**责任：** 面向

## <a name="data-recovery"></a>数据恢复
有关详细信息，请参阅[安全控制：数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期自动备份
**指南：** 目前，Azure 开发测试实验室不支持 VM 备份和快照。 但是，可以在开发测试实验室中托管的底层 Azure Vm 上启用和配置 Azure 备份。 而且，您还可以为自动备份配置所需的频率和保留期，前提是您对基础计算资源具有适当的访问权限。 

- [概要了解 Azure VM 备份](../backup/backup-azure-vms-introduction.md)
- [通过 VM 设置备份 Azure VM](../backup/backup-azure-vms-first-look-arm.md)

**Azure 安全中心监视：** 是的

**责任：** 面向

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥
**指南：** 目前，Azure 开发测试实验室不支持 VM 备份和快照。 不过，你可以使用 PowerShell 或 REST Api 创建托管在开发测试实验室中的基础 Azure Vm 或附加到这些实例的托管磁盘的快照，前提是你有权访问基础计算资源。 你还可以在 Azure Key Vault 中备份任何客户管理的密钥。

在目标 Azure Vm 上启用 Azure 备份，以及所需的频率和保留期。 它包括完整的系统状态备份。 如果使用的是 Azure 磁盘加密，Azure VM 备份会自动处理客户托管密钥的备份。

- [在使用加密的 Azure Vm 上备份](../backup/backup-azure-vms-encryption.md)
- [Azure VM 备份概述](../backup/backup-azure-vms-introduction.md)
- [概要了解 Azure VM 备份](../backup/backup-azure-vms-introduction.md)
- [如何在 Azure 中备份 Key Vault 密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视：** 是的

**责任：** 面向

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥
**指南：** 确保能够定期在 Azure 备份中执行内容数据还原。 如有必要，请测试将内容还原到隔离的虚拟网络或订阅。 同时，测试已备份客户托管密钥的还原。

如果你使用的是 Azure 磁盘加密，则可以使用磁盘加密密钥还原 Azure VM。 使用磁盘加密时，可以使用磁盘加密密钥还原 Azure VM。

- [在使用加密的 Azure Vm 上备份](../backup/backup-azure-vms-encryption.md)
- [如何从 Azure VM 备份恢复文件](../backup/backup-azure-restore-files-from-vm.md)
- [如何在 Azure 中还原密钥保管库密钥](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)
- [如何备份和还原已加密的 VM](../backup/backup-azure-vms-encryption.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥
**指南：** 当你通过 Azure 备份来备份托管磁盘时，将静态加密 Vm，并) 存储服务加密 (SSE。 Azure 备份还可以备份使用 Azure 磁盘加密进行加密的 Azure VM。 Azure 磁盘加密与在 Key Vault 中作为机密受到保护的 BitLocker 加密密钥 (BEK) 相集成。 Azure 磁盘加密还与 Azure Key Vault 密钥加密密钥 (KEK) 相集成。 在 Key Vault 中启用软删除，以防意外或恶意删除密钥。

- [VM 的软删除](../backup/soft-delete-virtual-machines.md)
- [Azure Key Vault-软删除概述](../key-vault/general/soft-delete-overview.md)

**Azure 安全中心监视：** 是的

**责任：** 面向

## <a name="incident-response"></a>事件响应
有关详细信息，请参阅[安全控制：事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导
**指南：** 构建组织的事件响应指南。 请确保有一些书面事件响应计划，这些计划定义事件处理/管理的人员和阶段的所有角色，并从检测到后事件检查。

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
- [使用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程
**指南：** Azure 安全中心为每个警报分配一个严重性，以帮助你确定应该首先调查的警报的优先级。 严重性取决于安全中心在查找或用于发出警报的分析中的置信度，以及导致警报的活动的恶意意图的置信度。

此外，使用标记清楚地标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)
- [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视：** 是的

**责任：** 面向

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程
**指南：** 进行练习，测试定期事件的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

- [NIST 发布指南，适用于 IT 计划和功能的测试、培训和试验计划](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知
**指南：** 如果 Microsoft 安全响应中心 (MSRC) 发现你的数据被非法或未授权的一方访问，Microsoft 将使用安全事件联系人信息与你联系。 事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中
**指南：** 使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)
- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

#### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报
**指南：** 使用 Azure 安全中心的工作流自动化功能，通过 "逻辑应用" 自动触发有关安全警报和建议的响应，以保护 Azure 资源。

- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)
 
Azure 安全中心监视： * * * * 不适用

**责任：** 面向


## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习
*有关详细信息，请参阅安全控制：[渗透测试和 red 团队练习](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*


### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：定期对 Azure 资源执行渗透测试，确保在 60 天内修正所有发现的关键安全问题
**指南：** 遵循 Microsoft 交往规则确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视：** 不适用

**责任：** 共享

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [Azure 开发测试实验室中的环境的安全警报](environment-security-alerts.md)
