---
title: 适用于 Azure Database for PostgreSQL 的 Azure 安全基线-超大规模
description: Azure Database for PostgreSQL 超大规模安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: dd6d3ef6f610dcbdc511297e459cc5856bebe033
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030039"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql---hyperscale"></a>适用于 Azure Database for PostgreSQL 的 Azure 安全基线-超大规模

适用于 Azure Database for PostgreSQL 的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全性

有关详细信息，请参阅[安全控制：网络安全](/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：在指定哪些计算机具有权限之前，Azure Database for PostgreSQL 服务器防火墙阻止对超大规模（Citus）协调器节点的所有访问。 防火墙基于每个请求的起始 IP 地址授予对服务器的访问权限。 要配置防火墙，请创建防火墙规则，以指定可接受的 IP 地址的范围。 可以在服务器级别创建防火墙规则。

* [如何在 Azure Database for PostgreSQL-超大规模（Citus）中配置防火墙规则](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-firewall-rules)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指南**：不适用;Azure Database for PostgreSQL-超大规模（Citus）无法与虚拟网络、子网或网络安全组关联。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：不适用;Azure Database for PostgreSQL-超大规模（Citus）使用的终结点均由 Microsoft 管理。 你负责管理要部署到本地系统的其他所有控件。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：不适用;Azure Database for PostgreSQL-超大规模（Citus）无法与虚拟网络、子网或网络安全组关联。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：不适用;Azure Database for PostgreSQL-超大规模（Citus）使用的终结点均由 Microsoft 管理。 你负责管理要部署到本地系统的其他所有控件。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：不适用;Azure Database for PostgreSQL-超大规模（Citus）无法与虚拟网络、子网或网络安全组关联。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：通过 Azure Policy 为与 Azure Database for PostgreSQL 实例关联的网络设置和网络资源定义和实现标准安全配置。 使用 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制实施 Azure Database for PostgreSQL 实例的网络配置。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [用于网络的 Azure Policy 示例](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [如何创建 Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：不适用;Azure Database for PostgreSQL-超大规模（Citus）无法与虚拟网络、子网或网络安全组关联。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：不适用;Azure Database for PostgreSQL-超大规模（Citus）无法与虚拟网络、子网或网络安全组关联。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护用于 Azure 资源的时间源，如日志中时间戳的 Azure Database for PostgreSQL 超大规模（Citus）。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：对于控制平面审核日志记录，请启用 Azure 活动日志诊断设置，并将日志发送到 Log Aalytics 工作区、Azure 事件中心或 Azure 存储帐户进行存档。 使用 Azure 活动日志数据，可以确定在控制平面级别针对 Azure 资源执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

此外，通过 Azure Monitor 引入日志来聚合超大规模（Citus）生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用存储帐户进行长期/存档存储。 或者，可以启用数据并将其加入 Azure Sentinel 或第三方安全事件和事件管理 (SIEM)。

* [如何启用 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [超大规模（Citus）中的指标](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-monitoring)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：超大规模（Citus）为服务器组中的每个节点提供了度量值。 这些指标有助于深入了解支持资源的行为。 每项指标以一分钟为频率发出，历史记录长达 30 天。

对于控制平面审核日志记录，启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区、Azure 事件中心或 Azure 存储帐户以进行存档。 使用 Azure 活动日志数据，可以确定在控制平面级别针对 Azure 资源执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

此外，通过 Azure Monitor 引入日志来聚合超大规模（Citus）生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用存储帐户进行长期/存档存储。 或者，可以启用数据并将其加入 Azure Sentinel 或第三方安全事件和事件管理 (SIEM)。

* [超大规模（Citus）中的指标](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-monitoring)

* [如何启用 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，Log Analytics 用于保存超大规模（Citus）日志的工作区，请根据组织的符合性法规设置保留期。 使用 Azure 存储帐户进行长期/存档存储。

* [如何为 Log Analytics 工作区设置日志保留参数](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [在 Azure 存储帐户中存储资源日志](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指南**：分析和监视来自超大规模（Citus）实例的日志以了解异常行为。 使用 Azure Monitor 的 Log Analytics 检查日志并对日志数据执行查询。 或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [有关 Log Analytics 的详细信息](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [如何在 Azure Monitor 中执行自定义查询](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：你可以为超大规模（Citus）启用诊断设置，并将日志发送到 Log Analytics 工作区。 你可以根据 Azure 服务的监视指标来配置和接收警报。 使用 Azure Monitor 的 Log Analytics 检查日志并对日志数据执行查询。 或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

将 Log Analytics 工作区加入 Azure Sentinel，因为它提供了安全业务流程自动化响应 (SOAR) 解决方案。 这样便可以创建 playbook（自动解决方案）并用于修正安全问题。

* [超大规模（Citus）中的指标](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-alert-on-metric)

* [如何配置 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：不适用;Azure Database for PostgreSQL 超大规模（Citus）不会处理或产生与反恶意软件相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：不适用;Azure Database for PostgreSQL 不会处理或产生与 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：维护对超大规模（Citus）实例的控制平面（例如 Azure 门户）具有管理访问权限的用户帐户的清单。 此外，维护有权访问超大规模（Citus）实例的数据平面（在数据库本身中）的管理帐户的清单。

超大规模（Citus）不支持内置的基于角色的访问控制，但你可以基于特定的资源提供程序操作创建自定义角色。

此外，PostgreSQL 引擎使用角色来控制对数据库对象的访问，新创建的超大规模（Citus）服务器组附带了几个预定义的角色。 若要修改用户权限，请使用 PgAdmin 或 psql 等工具的标准 PostgreSQL 命令。

* [了解 Azure 订阅的自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)

* [了解 Azure Database for PostgreSQL 资源提供程序操作](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql)

* [了解 Azure Database for PostgreSQL 的访问管理](https://docs.microsoft.com/azure/postgresql/concepts-security#access-management])

* [如何在 Azure Database for PostgreSQL-超大规模（Citus）中创建用户](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

* [如何使用 psql 连接到 PostgreSQL-超大规模（Citus）](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#connect-to-the-database-using-psql)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure AD 没有默认密码。 其他需要密码的 Azure 资源会强制创建具有复杂性要求和最小密码长度的密码，该长度因服务而异。 你对可能使用默认密码的第三方应用程序和市场服务负责。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕使用用于访问超大规模（Citus）实例的专用管理帐户创建标准操作过程。 用于管理 Azure 资源的管理员帐户与 Azure Active Directory 相关联，还存在用于管理数据库访问权限的超大规模（Citus）服务器组中存在的本地服务器管理员帐户。 使用 Azure 安全中心的标识和访问管理来监视 Azure Active Directory 中的管理帐户数。

* [了解 Azure 安全中心的标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [如何在 Azure Database for PostgreSQL-超大规模（Citus）中创建用户](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指南**：无论是使用直接在数据库中配置的用户名/密码还是使用 AZURE ACTIVE DIRECTORY （AD）标识，都支持登录到 Azure Database for PostgreSQL-超大规模（Citus），并使用 Azure AD 令牌来进行连接。 使用 Azure AD 令牌时，支持不同的方法，如 Azure AD 用户、Azure AD 组或连接到数据库的 Azure AD 应用程序。

* [使用 Azure Active Directory 对 Azure Database for PostgreSQL 进行身份验证](https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

指南：启用 Azure Active Directory 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理建议。 使用 Azure AD 令牌登录数据库时，这使你可以要求对数据库登录进行多重身份验证。

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [使用 Azure Active Directory 对 Azure Database for PostgreSQL 进行身份验证](https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication)

* [如何在 Azure 安全中心监视标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：将特权访问工作站 (PAW) 与为登录和配置 Azure 资源而配置的多重身份验证 (MFA) 结合使用。

* [了解特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="37-alert-on-account-login-behavior-deviation"></a>3.7：帐户登录行为偏差的警报

**指南**：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (AD) Privileged Identity Management (PIM) 生成日志和警报。

使用 Azure AD 风险检测查看有关风险用户行为的警报和报告。

* [如何部署 Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [了解 Azure AD 风险检测](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

指南：使用条件访问命名位置仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行门户和 Azure 资源管理器访问。

* [如何在 Azure 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

指南：使用 Azure Active Directory (AD) 作为中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中的数据使用强加密来保护数据。 Azure AD 还会进行加盐操作、哈希操作并安全地存储用户凭据。

Azure AD 凭据还可以用于在管理平面级别（例如，Azure 门户）进行管理，以控制 PostgreSQL 管理员帐户。

* [使用 Azure Active Directory 对 Azure Database for PostgreSQL 进行身份验证](https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：查看和协调有权访问本地数据库的两个用户的访问权限，以及通过 Azure Active Directory。

查看 Azure Active Directory （AD）日志，以帮助发现可包括具有超大规模（Citus）管理角色的帐户。 此外，使用 Azure 标识访问评审来有效地管理组成员身份、访问可用于访问超大规模（Citus）和角色分配的企业应用程序。 应定期（例如每 90 天一次）评审用户访问权限，以确保正确用户持续拥有访问权限。

* [查看 PostgreSQL 用户和分配的角色](https://www.postgresql.org/docs/current/database-roles.html)

* [了解 Azure AD 报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [如何使用 Azure 标识访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：在 AZURE ACTIVE DIRECTORY （AD）中，你有权访问 Azure AD 登录活动、审核和风险事件日志源，从而允许你与任何 SIEM/监视工具集成。

可以通过为 Azure Active Directory 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 你可以在 Log Analytics 工作区中配置所需的警报。

* [如何将 Azure 活动日志集成到 Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帐户登录行为偏差时发出警报

**指南**：使用 Azure Active Directory 的标识保护和风险检测功能来配置在 AZURE ACTIVE DIRECTORY （AD）级别上检测到的可疑操作的自动响应。 可以通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。

还可以将日志引入 Azure Sentinel 中以便进一步调查。

* [Azure AD Identity Protection 概述](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

* [如何查看 Azure AD 风险登录](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：当前不可用;超大规模（Citus）尚不支持客户密码箱。

* [客户密码箱支持的服务列表](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：目前不可用

**责任**：目前不可用

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记来帮助跟踪超大规模（Citus）实例或存储或处理敏感信息的相关资源。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实现单独的订阅和/或管理组。 结合使用管理角色和防火墙规则，隔离和限制对 Azure Database for PostgreSQL 实例的网络访问。

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [了解 Azure Database for PostgreSQL-超大规模（Citus）中的防火墙规则](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-firewall-rules)

* [了解超大规模中的角色（Citus）](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**： Microsoft 管理 Azure Database for PostgreSQL-超大规模（Citus）的底层基础结构，并已实施严格控制来防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：连接到超大规模（Citus）协调器节点的客户端应用程序需要传输层安全性（TLS）1.2。 通过在数据库服务器与客户端应用程序之间强制实施 TLS 连接，可以加密服务器与应用程序之间的数据流，这有助于防止“中间人”攻击。

对于通过 Azure 门户预配的所有 Azure Database for PostgreSQL 服务器，默认情况下会启用 TLS 连接的强制。

在某些情况下，第三方应用程序需要从受信任的证书颁发机构（CA）证书文件（.cer）生成的本地证书文件才能安全连接。

* [如何在 Azure Database for PostgreSQL-超大规模（Citus）中配置 TLS](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-ssl-connection-security)

* [需要证书验证才可启用 TLS 连接性的应用程序](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-ssl-connection-security)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：超大规模（Citus）尚不支持数据标识、分类和丢失防护功能。

对于由 Microsoft 管理的底层平台，Microsoft 将所有客户内容都视为敏感内容，竭尽全力防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**：使用 Azure 基于角色的访问控制（RBAC）来控制对超大规模（Citus）控制平面（例如 Azure 门户）的访问。 RBAC 不影响数据库中的用户权限。

若要在数据库级别修改用户权限，请使用 PgAdmin 或 psql 等工具的标准 PostgreSQL 命令。

* [如何在 Azure 中配置 RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

* [如何使用 SQL for Azure Database for PostgreSQL 配置用户访问权限](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Azure 安全中心监视**：是

责任：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**：不适用；此建议适用于计算资源。

Microsoft 管理超大规模（Citus）的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：至少一天，Azure Database for PostgreSQL 超大规模（Citus）对数据文件和数据库事务日志进行快照备份。 利用备份，你可以将服务器还原到保留期内的任何时间点。 （对于所有群集，保持期目前为35天。）使用 AES 256 位加密对所有备份进行加密。 PostgreSQL 超大规模（Citus）产品使用 Microsoft 托管的密钥进行加密。

* [了解 Azure PostgreSQL-超大规模（Citus）备份的加密](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

Azure 安全中心监视：不适用

**责任**：共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以便为超大规模（Citus）的生产实例以及其他关键或相关资源的发生更改创建警报。

* [如何针对 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](/azure/security/benchmarks/security-control-vulnerability-management)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：当前不可用;Azure 安全中心尚不支持 Azure Database for PostgreSQL-超大规模（Citus）的漏洞评估。

* [Azure 安全中心 Azure PaaS 服务的功能范围](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure 安全中心监视**：目前不可用

**责任**：目前不可用

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**：对于由 microsoft 管理的基础平台，microsoft 将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指南**：对于由 microsoft 管理的基础平台，microsoft 将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**：对于由 microsoft 管理的基础平台，microsoft 将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**： Microsoft 对支持 Azure Database for PostgreSQL-超大规模（Citus）的基础系统执行漏洞管理。 对于与数据库群集和相关资源的配置相关的漏洞，请使用常见的风险评分计划（例如，公共漏洞计分系统）或第三方扫描工具提供的默认风险评级。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动资产发现解决方案

**指南**：使用 Azure 资源关系图可查询和发现订阅中的所有资源（包括超大规模（Citus）实例）。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

* [如何使用 Azure Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：将标记应用到超大规模（Citus）实例和其他相关资源，以逻辑方式将它们组织到分类。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪超大规模（Citus）实例和相关资源。 定期协调清单，并确保及时从订阅中删除未经授权的资源。

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义和维护已批准的 Azure 资源的清单

**指导**：在 Azure 策略中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：使用 Azure Policy 对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：不适用；此建议适用于计算资源和整个 Azure。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure 策略中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指导**：使用 Azure 条件访问，通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。 这可以防止在高安全环境中创建和更改资源，如包含敏感信息的超大规模（Citus）的实例。

* [如何配置条件访问以阻止访问 Azure 资源管理器](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指导**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：通过 Azure 策略为超大规模（Citus）实例定义和实现标准安全配置。 使用 Azure 策略创建自定义策略，以便审核或强制实施 Azure Database for PostgreSQL 实例的网络配置。

此外，Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足/超过组织的安全要求。

* [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [在 Azure 门户中将单资源和多资源导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 策略“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。 此外，你可以使用 Azure 资源管理器模板维护组织所需的 Azure 资源的安全配置。

* [了解 Azure Policy 效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

* [创建和管理策略以强制实施符合性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure 资源管理器模板概述](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果对超大规模（Citus）实例和相关资源使用自定义 Azure 策略定义，请使用 Azure Repos 安全地存储和管理你的代码。

* [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure 策略“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。 此外，你可以使用 Azure 资源管理器模板维护组织所需的 Azure 资源的安全配置。

* [了解 Azure Policy 效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

* [创建和管理策略以强制实施符合性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure 资源管理器模板概述](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：使用“Microsoft.DBforPostgreSQL”命名空间中的 Azure Policy 别名创建自定义策略，以审核和强制执行系统配置，并针对其发出警报。 使用 Azure 策略 [audit]、[拒绝] 和 [部署（如果不存在））自动强制实施 Azure Database for PostgreSQL 实例和相关资源的配置。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：不适用;您可以使用 Azure Key Vault 安全地管理您的数据库管理员密码。 为该 Key Vault 机密分配适当范围的权限，以限制对数据库密码的访问。 避免将任何连接字符串或数据库机密存储在代码存储库中。

* [如何连接到 PostgreSQL 数据库](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#connect-to-the-database-using-psql)

* [如何创建 Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [如何使用托管标识提供 Key Vault 身份验证](https://docs.microsoft.com/azure/key-vault/managed-identity)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全地自动管理标识

**指南**：Azure Database for PostgreSQL 服务器支持通过 Azure Active Directory 身份验证访问数据库。 创建 Azure Database for PostgreSQL 服务器时，需要为管理员用户提供凭据。 此管理员可用于创建其他数据库用户。

对于在 Azure 应用服务上运行的用于访问 Azure Database for PostgreSQL 服务器的 Azure 虚拟机或 Web 应用程序，请结合使用托管服务标识与 Azure Key Vault ，以存储和检索 Azure Database for PostgreSQL 服务器的凭据。 请确保启用 Key Vault 软删除。

使用托管标识在 Azure Active Directory (AD) 中为 Azure 服务提供一个自动托管标识。 通过托管标识可向支持 Azure AD 身份验证的任何服务（包括 Key Vault）证明身份，无需在代码中放入任何凭据。

* [如何配置托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

* [如何与 Azure 托管标识集成](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

**Azure 安全中心监视**：目前不可用

**责任**：目前不可用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据暴露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据移动到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](/azure/security/benchmarks/security-control-malware-defense)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用；此建议适用于计算资源。

Microsoft 反恶意软件会在支持 Azure 服务（例如，Azure 应用服务）的基础主机上启用，但不会对客户内容运行。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：在支持 Azure 服务的底层主机上启用了 Microsoft 反恶意软件，例如超大规模（Citus）--但是，它不会在客户内容上运行。

预扫描要上传到非计算 Azure 资源的任何内容，例如应用服务、Data Lake Storage、Blob 存储、Azure Database for PostgreSQL 等。Microsoft 无法访问这些实例中的数据。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用；此建议适用于计算资源。

Microsoft 反恶意软件在支持 Azure 服务的基础主机（例如超大规模（Citus））上启用，但是，它不会在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**： Azure Database for PostgreSQL –超大规模（Citus）自动创建每个节点的备份，并将其存储在本地冗余存储中。 备份可用于将超大规模（Citus）群集还原到指定时间。

* [如何在 Azure Database for PostgreSQL-超大规模（Citus）中进行备份和还原](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：至少一天，Azure Database for PostgreSQL 会对数据文件和数据库事务日志进行快照备份。 利用备份，你可以将服务器还原到保留期内的任何时间点。 对于所有群集，保持期目前为35天。 所有备份都使用 AES 256 位加密进行加密。

在支持可用性区域的 Azure 区域中，备份快照存储在三个可用性区域中。 只要至少有一个可用性区域处于联机状态，超大规模（Citus）群集就是可恢复的。

* [如何在 Azure Database for PostgreSQL-超大规模（Citus）中进行备份和还原](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：在 Azure Database for PostgreSQL 中，还原超大规模（Citus）群集将从原始节点的备份创建新群集。 你可以将群集还原到过去35天内的任何时间点。 还原过程会在与原始群集相同的 Azure 区域、订阅和资源组中创建新群集。 新群集配置与原始群集配置相同：相同数量的节点、Vcore 数、存储大小和用户角色。

不会从原始服务器组中保留防火墙设置和 PostgreSQL 服务器参数;它们将重置为默认值。 防火墙将阻止所有连接。 还原后需要手动调整这些设置。

* [如何在 Azure Database for PostgreSQL-超大规模（Citus）中进行备份和还原](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：无法还原已删除的超大规模（Citus）群集。 如果删除群集，则属于该群集的所有节点都将被删除且无法恢复。 若要保护群集资源后从意外删除或意外更改中进行部署，管理员可以利用管理锁。

* [如何在 Azure Database for PostgreSQL-超大规模（Citus）中进行备份和还原](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

* [如何在 Azure 安全中心配置工作流自动化](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心对用于发出警报的调查结果或分析的置信度，以及导致警报的活动背后存在恶意意图的可信度。

此外，应清楚地标记订阅（例如 生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

Azure 安全中心监视：不适用

责任：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期练习以测试系统的事件响应能力。 识别弱点和差距，并根据需要修改计划。

* [请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南）

**Azure 安全中心监视**：不适用

责任：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。 事后审查事件，确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出功能可手动或以连续不断的方式导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Sentinel。

* [如何配置连续导出](https://docs.microsoft.com/azure/security-center/continuous-export)

* [如何将警报流式传输到 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

* [如何配置工作流自动化和逻辑应用](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 安全中心监视**：不适用

责任：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指南**：*[遵循 Microsoft Rules of Engagement，确保渗透测试不违反 Microsoft 政策](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [可在此处详细了解如何针对 Microsoft 托管云基础结构、服务和应用程序执行红队测试和实时站点渗透测试，以及 Microsoft 的相关策略](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
