---
title: 适用于 Azure Database for MySQL 的 Azure 安全基线
description: 适用于 Azure Database for MySQL 的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 414c8dc0a9e6c7aa47287fe49a3f21ee28605f78
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89244698"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>适用于 Azure Database for MySQL 的 Azure 安全基线

适用于 Azure Database for MySQL 的 Azure 安全基线包含的建议可帮助你改进部署的安全状况。

此服务的基线摘自 [Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

有关详细信息，请参阅[安全控制：网络安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络上使用网络安全组或 Azure 防火墙来保护资源

指南：使用专用终结点为 Azure Database for MySQL 配置专用链接。 使用专用链接可以通过专用终结点连接到 Azure 中的各种 PaaS 服务。 Azure 专用链接实质上是将 Azure 服务引入专用虚拟网络 (VNet) 中。 虚拟网络与 MySQL 实例之间的流量将遍历 Microsoft 主干网络。

或者，你可以使用虚拟网络服务终结点保护和限制对 Azure Database for MySQL 实现的网络访问。 虚拟网络规则是一种防火墙安全功能，用于控制是否允许 Azure Database for MySQL 服务器接受从虚拟网络中的特定子网发送的通信。

还可以使用防火墙规则保护 Azure Database for MySQL 服务器。 在指定哪些计算机具有访问权限之前，服务器防火墙将禁止所有对数据库服务器的访问。 要配置防火墙，请创建防火墙规则，以指定可接受的 IP 地址的范围。 可以在服务器级别创建防火墙规则。

如何为 Azure Database for MySQL 配置专用链接： https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

如何在 Azure Database for MySQL 中创建和管理 VNet 服务终结点和 VNet 规则： https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

如何配置 Azure Database for MySQL 防火墙规则： https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal

Azure 安全中心监视：不可用

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 VNet、子网和 NIC 的配置和流量

指南：将 Azure Database for MySQL 实例保护到专用终结点时，可以在同一虚拟网络中部署虚拟机。 可以使用网络安全组 (NSG) 来降低数据外泄的风险。 启用 NSG 流日志，并将日志发送到存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析的一些优点是能够直观显示网络活动、识别热点、识别安全威胁、了解流量流模式以及查明网络配置错误。

如何为 Azure Database for MySQL 配置专用链接： https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

如何启用 NSG 流日志： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用和使用流量分析： https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

Azure 安全中心监视：不适用

责任：空值

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知的恶意 IP 地址通信

指南：使用 Azure Database for MySQL 的高级威胁防护。 高级威胁防护可检测异常活动，表明尝试访问或利用数据库的行为异常且可能有害。

在与 Azure Database for MySQL 实例关联的虚拟网络上启用 DDoS 保护标准，以防范 DDoS 攻击。 使用 Azure 安全中心集成威胁情报拒绝与已知的恶意或未使用的 Internet IP 地址通信。

如何配置 Azure Database for MySQL 的高级威胁防护： https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

如何配置 DDoS 保护： https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

指南：将 Azure Database for MySQL 实例保护到专用终结点时，可以在同一虚拟网络中部署虚拟机。 随后可以配置网络安全组 (NSG) 来降低数据外泄的风险。 启用 NSG 流日志，并将日志发送到存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

如何启用 NSG 流日志： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用和使用流量分析： https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

指南：使用 Azure Database for MySQL 的高级威胁防护。 高级威胁防护可检测异常活动，表明尝试访问或利用数据库的行为异常且可能有害。

如何配置 Azure Database for MySQL 的高级威胁防护： https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Azure 安全中心监视：是

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

Azure 安全中心监视：不适用

责任：空值

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度降低网络安全规则的复杂性和管理开销

指南：对于需要访问 Azure Database for MySQL 实例的资源，使用虚拟网络服务标记来定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应源或目标字段中指定服务标记名称（例如 SQL.WestUs），可允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

注意：Azure Database for MySQL 使用“Microsoft.Sql”服务标记。

有关使用服务标记的详细信息： https://docs.microsoft.com/azure/virtual-network/service-tags-overview

了解 Azure Database for MySQL 的服务标记使用： https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description

Azure 安全中心监视：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

指南：通过 Azure Policy 为与 Azure Database for MySQL 实例关联的网络设置和网络资源定义和实现标准安全配置。 使用“Microsoft.DBforMySQL”和“Microsoft.Network”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Database for MySQL 实例的网络配置。 还可以使用与网络或 Azure Database for MySQL 实例相关的内置策略定义，例如：

- 应启用 DDoS 防护标准版

- 应为 MySQL 数据库服务器启用“强制 SSL 连接”

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

适用于网络的 Azure Policy 示例： https://docs.microsoft.com/azure/governance/policy/samples/

如何创建 Azure 蓝图： https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：记录流量配置规则

指南：对与 Azure Database for MySQL 实例的网络安全和流量流相关的资源使用标记，以提供元数据和逻辑组织。

使用与标记相关的任何内置 Azure Policy 定义（如“需要标记及其值”），以确保使用标记创建所有资源并向你告知现有的未标记资源。

可以使用 Azure PowerShell 或 Azure CLI 基于其标记对资源进行查找或执行操作。

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具监视网络资源配置并检测更改

指南：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Database for MySQL 实例相关的网络资源的更改。 在 Azure Monitor 中创建将在对关键网络资源进行更改时触发的警报。

如何查看和检索 Azure 活动日志事件： https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure Monitor 中创建警报： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

指南：Microsoft 维护用于 Azure 资源的时间源，如适用于日志中的时间戳的 Azure Database for MySQL。

Azure 安全中心监视：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置安全日志集中管理

指南：启用诊断设置和服务器日志，并引入日志来聚合 Azure Database for MySQL 实例生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区查询和执行分析，并将 Azure 存储帐户用于长期/存档存储。 或者，可以将数据启用并加入 Azure Sentinel 或第三方 SIEM。

了解 Azure Database for MySQL 的服务器日志： https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

如何加入 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure 安全中心监视：不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

指南：在 Azure Database for MySQL 实例上启用诊断设置，以便访问审核、慢查询和 MySQL 指标日志。 确保专门启用了 MySQL 审核日志。 自动可用的活动日志包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。 还可以启用 Azure 活动日志诊断设置，并将日志发送到相同的 Log Analytics 工作区或存储帐户。

了解 Azure Database for MySQL 的服务器日志： https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

如何配置和访问 Azure Database for MySQL 的慢查询日志： https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

如何配置和访问 Azure Database for MySQL 的审核日志： https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

如何配置 Azure 活动日志的诊断设置： https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Azure 安全中心监视：不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：空值

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留

指南：在 Azure Monitor 中，对于用于保存 Azure Database for MySQL 日志的 Log Analytics 工作区，根据组织的合规性法规设置保留期。 将 Azure 存储帐户用于长期/存档存储。

如何为 Log Analytics 工作区设置日志保留参数： https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

在 Azure 存储帐户中存储资源日志： https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和检查日志

指南：分析和监视 Azure Database for MySQL 实例的日志中是否存在异常行为。 使用 Azure Monitor 的 Log Analytics 检查日志并对日志数据执行查询。 或者，可以将数据启用并加入 Azure Sentinel 或第三方 SIEM。

如何加入 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

有关 Log Analytics 的详细信息： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure Monitor 中执行自定义查询： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：启用针对异常活动的警报

指南：启用 Azure Database for MySQL 的高级威胁防护。 高级威胁防护可检测异常活动，表明尝试访问或利用数据库的行为异常且可能有害。

此外，可以为 MySQL 启用服务器日志和诊断设置，并将日志发送到 Log Analytics 工作区。 将 Log Analytics 工作区加入 Azure Sentinel，因为它提供了安全业务流程自动化响应 (SOAR) 解决方案。 这样便可以创建 playbook（自动解决方案）并用于修正安全问题。

如何启用 Azure Database for MySQL 的高级威胁防护（预览版）： https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

了解 Azure Database for MySQL 的服务器日志： https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

如何配置和访问 Azure Database for MySQL 的慢查询日志： https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

如何配置和访问 Azure Database for MySQL 的审核日志： https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

如何配置 Azure 活动日志的诊断设置： https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

如何加入 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中进行反恶意软件日志记录

指南：不适用；Azure Database for MySQL 不会处理或生成与反恶意软件相关的日志。

Azure 安全中心监视：不适用

责任：空值

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

指南：不适用；Azure Database for MySQL 不会处理或生成与 DNS 相关的日志。

Azure 安全中心监视：不适用

责任：空值

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：空值

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

指南：维护对 Azure Database for MySQL 实例的管理平面（例如 Azure 门户）拥有管理访问权限的用户帐户的清单。 此外，还需维护对 Azure Database for MySQL 实例的数据平面（在数据库本身内）拥有访问权限的管理帐户的清单。 （创建 MySQL 服务器时，需为管理员用户提供凭据。 此管理员可用于创建其他 MySQL 用户。）

Azure Database for MySQL 不支持内置基于角色的访问控制，但你可以基于特定资源提供程序选项创建自定义角色。

了解 Azure 订阅的自定义角色： https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

了解 Azure Database for MySQL 资源提供程序操作： https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql

了解 Azure Database for MySQL 的访问管理： https://docs.microsoft.com/azure/mysql/concepts-security#access-management

Azure 安全中心监视：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

指南：Azure AD 没有默认密码的概念。

创建 Azure Database for MySQL 资源本身时，Azure 会强制创建具有强密码的管理用户。 但是，一旦创建了 MySQL 实例，便可以使用所创建的第一个服务器管理员帐户创建其他用户并向他们授予管理访问权限。 创建这些帐户时，请确保为每个帐户配置不同的强密码。

如何为 Azure Database for MySQL 创建其他帐户： https://docs.microsoft.com/azure/mysql/howto-create-users

如何更新管理员密码： https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

指南：围绕可以访问 Azure Database for MySQL 实例的专用管理帐户的使用，创建标准操作过程。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

了解 Azure 安全中心标识和访问： https://docs.microsoft.com/azure/security-center/security-center-identity-access

了解如何在 Azure Database for MySQL 中创建管理员用户： https://docs.microsoft.com/azure/mysql/howto-create-users

Azure 安全中心监视：不适用

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用 Azure Active Directory 的单一登录 (SSO)

指南：支持通过以下两种方法登录 Azure Database for MySQL：使用直接在数据库中配置的用户名/密码，以及使用 Azure Active Directory (AD) 标识并利用 Azure AD 令牌进行连接。 使用 Azure AD 令牌时，支持不同的方法，如 Azure AD 用户、Azure AD 组或连接到数据库的 Azure AD 应用程序。

另外，对 MySQL 的控制平面访问可通过 REST API 进行，并且支持 SSO。 若要进行身份验证，请将请求的授权标头设置为从 Azure Active Directory (AAD) 获取的 JSON Web 令牌。

使用 Azure Active Directory 向 Azure Database for MySQL 进行身份验证： https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

了解 Azure Database for MySQL REST API： https://docs.microsoft.com/rest/api/mysql/

了解 Azure AD 的 SSO： https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

指南：启用 Azure Active Directory 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理建议。 使用 Azure AD 令牌登录数据库时，这使你可以要求对数据库登录进行多重身份验证。

如何在 Azure 中启用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

使用 Azure Active Directory 向 Azure Database for MySQL 进行身份验证： https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

如何在 Azure 安全中心监视标识和访问： https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

指南：将特权访问工作站 (PAW) 与为登录和配置 Azure 资源而配置的多重身份验证 (MFA) 结合使用。

了解特权访问工作站： https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中启用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录管理帐户的可疑活动并发出警报

指南：启用 Azure Database for MySQL 的高级威胁防护以便针对可疑活动生成警报。

此外，可以使用 Azure AD Privileged Identity Management (PIM) 在环境中发生可疑活动或不安全的活动时生成日志和警报。

使用 Azure AD 风险检测查看有关风险用户行为的警报和报告。

如何配置 Azure Database for MySQL 的高级威胁防护： https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

如何部署 Privileged Identity Management (PIM)： https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

了解 Azure AD 风险检测： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

指南：使用条件访问命名位置仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行门户和 Azure 资源管理器访问。

如何在 Azure 中配置命名位置： https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

指南：使用 Azure Active Directory (AD) 作为中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中的数据使用强加密来保护数据。 Azure AD 还会进行加盐操作、哈希操作并安全地存储用户凭据。

若要登录 Azure Database for MySQL，建议使用 Azure AD 并利用 Azure AD 令牌进行连接。 使用 Azure AD 令牌时，支持不同的方法，如 Azure AD 用户、Azure AD 组或连接到数据库的 Azure AD 应用程序。 

Azure AD 凭据还可以用于在管理平面级别（例如，Azure 门户）进行管理，以控制 MySQL 管理员帐户。

使用 Azure Active Directory 向 Azure Database for MySQL 进行身份验证： https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Azure 安全中心监视：是

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期评审和协调用户访问权限

指南：检查 Azure Active Directory 日志，以帮助发现可能包含具有 Azure Database for MySQL 管理角色的陈旧帐户。 此外，使用 Azure 标识访问评审可高效地管理组成员身份、对可用于访问 Azure Database for MySQL 的企业应用程序的访问权限以及角色分配。 应定期（例如每 90 天一次）评审用户访问权限，以确保正确用户持续拥有访问权限。

了解 Azure AD 报告 https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 标识访问评审： https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视访问已停用帐户的尝试

指南：为 Azure Database for MySQL 和 Azure Active Directory 启用诊断设置，将所有日志都发送到 Log Analytics 工作区。 在 Log Analytics 中配置所需警报（例如失败的身份验证尝试）。

如何配置和访问 Azure Database for MySQL 的慢查询日志： https://docs.microsoft.com/Azure/mysql/howto-configure-server-logs-in-portal

如何配置和访问 Azure Database for MySQL 的审核日志： https://docs.microsoft.com/Azure/mysql/howto-configure-audit-logs-portal

如何将 Azure 活动日志集成到 Azure Monitor： https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Azure 安全中心监视：不可用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：对帐户登录行为偏差发出警报

指南：启用 Azure Database for MySQL 的高级威胁防护以便针对可疑活动生成警报。

使用 Azure Active Directory 标识保护和风险检测功能可配置对检测到的可疑操作的自动响应。 可以通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。

还可以将日志引入 Azure Sentinel 中以便进一步调查。

如何配置 Azure Database for MySQL 的高级威胁防护： https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Azure AD 标识保护概述： https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

如何查看 Azure AD 风险登录： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何加入 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure 安全中心监视：不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持方案期间为 Microsoft 提供对相关客户数据的访问权限

指南：不适用；Azure Database for MySQL 尚不支持客户密码箱。

客户密码箱支持的服务列表： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

Azure 安全中心监视：不适用

责任：空值

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

指南：使用标记可帮助跟踪存储或处理敏感信息的 Azure Database for MySQL 实例或相关资源。

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

指南：为开发、测试和生产实现单独的订阅和/或管理组。 结合使用专用链接、服务终结点和/或防火墙规则，以隔离和限制对 Azure Database for MySQL 实例的网络访问。

如何创建其他 Azure 订阅： https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组： https://docs.microsoft.com/azure/governance/management-groups/create

如何为 Azure Database for MySQL 配置专用链接： https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link

如何在 Azure Database for MySQL 中创建和管理 VNet 服务终结点和 VNet 规则： https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

如何配置 Azure Database for MySQL 防火墙规则： https://docs.microsoft.com/azure/mysql/concepts-firewall-rules


Azure 安全中心监视：不可用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

指南：使用 Azure VM 访问 Azure Database for MySQL 实例时，请使用专用链接、MySQL 网络配置、网络安全组和服务标记来减轻数据外泄的可能性。

Microsoft 会管理 Azure Database for MySQL 的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

如何减轻 Azure Database for MySQL 的数据外泄： https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

指南：Azure Database for MySQL 支持使用安全套接字层 (SSL) 将 MySQL 服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。 在 Azure 门户中，确保默认情况下为所有 Azure Database for MySQL 实例都启用了“强制执行 SSL 连接”。

Azure Database for MySQL 当前支持的 TLS 版本为 TLS 1.0、TLS 1.1、TLS 1.2。

如何为 Azure Database for MySQL 配置传输中加密： https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security

Azure 安全中心监视：不可用

责任：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

指南：数据标识、分类和丢失防护功能尚不可用于 Azure Database for MySQL。 如果需要，可实现第三方解决方案以实现合规性。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Azure 安全中心监视：不可用

责任：共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指导**：使用 azure RBAC)  (azure 基于角色的访问控制来控制对 Azure Database for MySQL 控制平面的访问 (例如 Azure 门户) 。 对于数据平面访问（在数据库本身内），使用 SQL 查询创建用户并配置用户权限。 Azure RBAC 不影响数据库中的用户权限。

如何配置 Azure RBAC： https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

如何使用 SQL 为 Azure Database for MySQL 配置用户访问： https://docs.microsoft.com/azure/mysql/howto-create-users

Azure 安全中心监视：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

指南：不适用；此建议旨在用于计算资源。

Microsoft 会管理 Azure Database for MySQL 的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密静态的敏感信息

指南：Azure Database for MySQL 服务使用 FIPS 140-2 验证的加密模块对静态数据进行存储加密。 数据（包括备份）在磁盘上加密，运行查询时创建的临时文件除外。 该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥由系统进行管理。 存储加密始终处于启用状态，无法禁用。

针对 Azure Database for MySQL 使用客户托管密钥进行数据加密，可创建自己的密钥 (BYOK) 来保护静态数据。 此时，必须请求访问权限才能使用此功能。 为此，请联系：

AskAzureDBforMySQL@service.microsoft.com

了解 Azure Database for MySQL 的静态加密： https://docs.microsoft.com/azure/mysql/concepts-security

如何为 Azure Database for MySQL 客户托管密钥： https://docs.microsoft.com/azure/mysql/concepts-data-encryption-mysql

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并发出警报

指南：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在 Azure Database for MySQL 的生产实例和其他关键或相关资源发生更改时发出的警报。

如何为 Azure 活动日志事件创建警报： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理。](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动化漏洞扫描工具

指南：当前不可用；Azure 安全中心尚不支持用于 Azure Database for MySQL 的漏洞评估。

Azure 安全中心内 Azure PaaS 服务的功能覆盖范围： https://docs.microsoft.com/azure/security-center/features-paas

Azure 安全中心监视：是

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：不适用；此项指导适用于计算资源。

Azure 安全中心监视：不适用

责任：空值

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方软件修补程序自动化管理解决方案

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

责任：空值

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：备份背靠背漏洞扫描

**指导**：不适用；此项指导适用于计算资源。

Azure 安全中心监视：不适用

责任：空值

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评分流程确定所发现漏洞的修正优先级

指南：Microsoft 对支持 Azure Database for MySQL 的基础系统执行漏洞管理。


Azure 安全中心监视：不适用

**责任**：Microsoft

## <a name="inventory-and-asset-management"></a>清单和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

指南：使用 Azure Resource Graph 可查询和发现订阅中的所有资源（包括 Azure Database for MySQL 实例）。 确保你在租户中拥有适当（读取）权限，并且能够枚举所有 Azure 订阅以及订阅中的资源。

如何使用 Azure Resource Graph 创建查询： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 订阅： https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

了解 Azure RBAC： https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

指南：将标记应用于 Azure Database for MySQL 实例和其他相关资源，从而将元数据按逻辑组织到分类中。

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

指南：使用标记、管理组和单独订阅（如果适用）来组织和跟踪 Azure Database for MySQL 实例和相关资源。 定期协调清单，并确保及时从订阅中删除未经授权的资源。

如何创建其他 Azure 订阅： https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组： https://docs.microsoft.com/azure/governance/management-groups/create

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准 Azure 资源和软件标题的清单

**指南**：不适用；此建议适用于计算资源和整个 Azure。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Graph 创建查询： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：空值

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：不适用；此建议适用于计算资源和整个 Azure。

Azure 安全中心监视：不适用

责任：空值

### <a name="68-use-only-approved-applications"></a>6.8：只使用已批准的应用程序

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：空值

### <a name="69-use-only-approved-azure-services"></a>6.9：只使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Policy 拒绝特定的资源类型： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-implement-approved-application-list"></a>6.10：实施已批准的应用程序列表

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：空值

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器进行交互的能力

指南：使用 Azure 条件访问可通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。 这可以防止在高安全环境中创建和更改资源，如包含敏感信息的 Azure Database for MySQL 实例。

如何配置条件访问以阻止访问 Azure 资源管理器： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：空值

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

Azure 安全中心监视：不适用

责任：空值

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

指南：通过 Azure Policy 为 Azure Database for MySQL 实例定义和实现标准安全配置。 使用“Microsoft.DBforMySQL”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Database for MySQL 实例的网络配置。 还可以使用与 Azure Database for MySQL 实例相关的内置策略定义，例如：

应为 MySQL 数据库服务器启用“强制 SSL 连接”

如何查看可用 Azure Policy 别名： https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：空值

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy [拒绝] 和 [不存在时部署] 在 Azure 资源中强制实施安全设置。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

了解 Azure Policy 效果： https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：空值

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

指南：如果对 Azure Database for MySQL 实例和相关资源使用自定义 Azure Policy 定义，请使用 Azure Repos 安全地存储和管理代码。

如何在 Azure DevOps 中存储代码： https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 文档： https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：空值

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

指南：使用“Microsoft.DBforMySQL”命名空间中的 Azure Policy 别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 此外，开发用于管理策略异常的进程和管道。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：空值

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：针对 Azure 服务实现自动化配置监视

指南：使用“Microsoft.DBforMySQL”命名空间中的 Azure Policy 别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 使用 Azure Policy [审核]、[拒绝] 和 [不存在时部署] 为 Azure Database for MySQL 实例和相关资源自动强制实施配置。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：空值

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 机密

指南：对于在 Azure 应用服务上运行的用于访问 Azure Database for MySQL 实例的 Azure 虚拟机或 Web 应用程序，请将托管服务标识与 Azure Key Vault 结合使用，以简化和保护 Azure Database for MySQL 密钥管理。 确保启用 Key Vault 软删除。

如何与 Azure 托管标识集成： https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何创建 Key Vault： https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用托管标识提供 Key Vault 身份验证： https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自动地管理标识

指南：Azure Database for MySQL 实例支持 Azure Active Directory 身份验证以访问数据库。  创建 Azure Database for MySQL 实例时，需为管理员用户提供凭据。 此管理员可用于创建其他数据库用户。  

对于在 Azure 应用服务上运行的用于访问 Azure Database for MySQL 实例的 Azure 虚拟机或 Web 应用程序，请将托管服务标识与 Azure Key Vault 结合使用，以存储和检索 Azure Database for MySQL 实例的凭据。 确保启用 Key Vault 软删除。

使用托管标识在 Azure Active Directory (AD) 中为 Azure 服务提供一个自动托管标识。 通过托管标识可向支持 Azure AD 身份验证的任何服务（包括 Key Vault）证明身份，无需在代码中放入任何凭据。

如何配置托管标识： https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

如何与 Azure 托管标识集成： https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Azure 安全中心监视：不适用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

如何设置凭据扫描器： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

指南：不适用；此建议旨在用于计算资源。

Microsoft 反恶意软件会在支持 Azure 服务（例如，Azure Database for SQL）的基础主机上启用，但它不会对客户内容运行。

Azure 安全中心监视：不适用

责任：空值

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预扫描要上传到非计算 Azure 资源的文件

指南：Microsoft 反恶意软件会在支持 Azure 服务（例如，Azure Database for MySQL）的基础主机上启用，但它不会对客户内容运行。

预扫描要上传到非计算 Azure 资源的任何内容，例如应用服务、Data Lake Storage、Blob 存储、Azure Database for MySQL 等。Microsoft 无法访问这些实例中的数据。

Azure 安全中心监视：不适用

**责任**：共享

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

指南：不适用；此建议旨在用于计算资源。

Microsoft 反恶意软件会在支持 Azure 服务（例如，Azure Database for MySQL）的基础主机上启用，但它不会对客户内容运行。

Azure 安全中心监视：不适用

责任：空值

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

指南：Azure Database for MySQL 会创建数据文件和事务日志的备份。 根据所支持的最大存储大小，我们会进行完整备份和差异备份（最大 4 TB 存储服务器）或快照备份（最大 16 TB 存储服务器）。 可以通过这些备份将服务器还原到所配置的备份保留期中的任意时间点。 默认的备份保留期为七天。 可以选择将其配置为长达 35 天。 所有备份都使用 AES 256 位加密进行加密。

了解 Azure Database for MySQL 的备份： https://docs.microsoft.com/azure/mysql/concepts-backup

了解 Azure Database for MySQL 初始配置： https://docs.microsoft.com/azure/mysql/tutorial-design-database-using-portal

Azure 安全中心监视：是

**责任**：共享

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整的系统备份并备份所有客户托管密钥

指南：Azure Database for MySQL 可自动创建服务器备份并将其存储在本地冗余或异地冗余存储中，具体取决于用户的选择。 备份可以用来将服务器还原到某个时间点。 备份和还原是任何业务连续性策略的基本组成部分，因为它们可以保护数据免遭意外损坏或删除。 

如果使用 Azure Key Vault 存储 Azure Database for MySQL 实例的凭据，请确保定期自动备份密钥。 

了解 Azure Database for MySQL 的备份： https://docs.microsoft.com/azure/mysql/howto-restore-server-portal 

如何备份 Key Vault 密钥： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


Azure 安全中心监视：是

**责任**：共享

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户端托管密钥

指南：在 Azure Database for MySQL 中进行还原时，会根据原始服务器的备份创建新的服务器。 可以使用两种类型的还原：时间点还原和异地还原。 时间点还原可以与任一备份冗余选项配合使用，所创建的新服务器与原始服务器位于同一区域。 异地还原只能在已将服务器配置为进行异地冗余存储的情况下使用，用于将服务器还原到另一区域。

估计的恢复时间取决于若干因素，包括数据库大小、事务日志大小、网络带宽，以及在同一区域同时进行恢复的数据库总数。 恢复时间通常少于 12 小时。

定期测试 Azure Database for MySQL 实例的还原。

了解 Azure Database for MySQL 中的备份和还原： https://docs.microsoft.com/azure/mysql/concepts-backup

Azure 安全中心监视：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户托管密钥

指南：Azure Database for MySQL 可以进行完整备份、差异备份和事务日志备份。 可以通过这些备份将服务器还原到所配置的备份保留期中的任意时间点。 默认的备份保留期为七天。 可以选择将其配置为长达 35 天。 所有备份都使用 AES 256 位加密进行加密。 确保启用 Key Vault 软删除。

了解 Azure Database for MySQL 中的备份和还原： https://docs.microsoft.com/azure/mysql/concepts-backup

Azure 安全中心监视：是

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保制定书面事件响应计划，其中定义人员的所有角色，以及从检测到事件后审查的事件处理/管理阶段。

如何在 Azure 安全中心内配置工作流自动化： https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

有关生成自己的安全事件响应过程的指南： https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 安全响应中心的事件剖析： https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

客户还可以利用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划： https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，请明确标记订阅（例如 生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期练习以测试系统的事件响应能力。 识别弱点和差距，并根据需要修改计划。

请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities（IT 规划和功能的测试、培训与演练计划指南）： https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。  事后审查事件，确保问题得到解决。

如何设置 Azure 安全中心安全联系人： https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Sentinel。

如何配置连续导出： https://docs.microsoft.com/azure/security-center/continuous-export

如何将警报流式传输到 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

指南：使用 Azure 安全中心的工作流自动化功能，通过“逻辑应用”对安全警报和建议自动触发响应。

如何配置工作流自动化和逻辑应用： https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：对 Azure 资源进行定期渗透测试，确保修正 60 天内的所有关键安全调查结果

指南：遵循 Microsoft Rules of Engagement 以确保渗透测试不违反 Microsoft 政策： https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

对于针对 Microsoft 托管云基础结构、服务和应用程序的红队和实时站点渗透测试的 Microsoft 策略和执行，可在此处找到有关详细信息： https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

Azure 安全中心监视：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
