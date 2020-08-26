---
title: 适用于 Azure Database for MariaDB 的 Azure 安全基线
description: 适用于 Azure Database for MariaDB 的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd195fee513b2eed9049c7841f791d73a41b876b
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87366826"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>适用于 Azure Database for MariaDB 的 Azure 安全基线

适用于 Azure Database for MariaDB 的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

有关详细信息，请参阅[安全控制：网络安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络中使用网络安全组或 Azure 防火墙保护资源

**指南**：配置专用终结点 Azure Database for MariaDB 的专用链接。 使用专用链接可以通过专用终结点连接到 Azure 中的各种 PaaS 服务。 Azure 专用链接实质上是将 Azure 服务引入专用虚拟网络 (VNet) 中。 虚拟网络和 MariaDB 实例之间的流量通过 Microsoft 主干网络。

或者，你可以使用虚拟网络服务终结点来保护和限制对 Azure Database for MariaDB 实现的网络访问。 虚拟网络规则是一种防火墙安全功能，用于控制您的 Azure Database for MariaDB 是否接受从虚拟网络中的特定子网发送的通信。

你还可以通过防火墙规则保护 Azure Database for MariaDB。 在指定哪些计算机具有访问权限之前，服务器防火墙将禁止所有对数据库服务器的访问。 要配置防火墙，请创建防火墙规则，以指定可接受的 IP 地址的范围。 可以在服务器级别创建防火墙规则。

如何配置 Azure Database for MariaDB 的专用链接：https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

如何在 Azure Database for MariaDB 服务器中创建和管理 VNet 服务终结点和 VNet 规则：https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

如何配置 Azure Database for MariaDB 防火墙规则：https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 VNet、子网和 NIC 的配置与流量

**指南**：在 Azure Database for MariaDB 服务器保护到专用终结点时，你可以在同一虚拟网络中部署虚拟机。 可以使用网络安全组 (NSG) 来降低数据外泄的风险。 启用 NSG 流日志，并将日志发送到存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析具有以下优势：能够直观显示网络活动、识别热点、识别安全威胁、了解流量流模式以及查明网络配置错误。

如何配置 Azure Database for MariaDB 的专用链接：https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

如何启用 NSG 流日志： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal 如何启用和使用流量分析：https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：对 Azure Database for MariaDB 使用高级威胁防护。 高级威胁防护可检测异常活动，表明尝试访问或利用数据库的行为异常且可能有害。

在与 Azure Database for MariaDB 实例相关联的虚拟网络上启用 DDoS 保护，以防范 DDoS 攻击。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。

如何配置 Azure Database for MariaDB 的高级威胁防护：https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

如何配置 DDoS 保护： https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指南**：在 Azure Database for MariaDB 服务器保护到专用终结点时，你可以在同一虚拟网络中部署虚拟机。 随后可以配置网络安全组 (NSG) 来降低数据外泄的风险。 启用 NSG 流日志，并将日志发送到存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析具有以下优势：能够直观显示网络活动、识别热点、识别安全威胁、了解流量流模式以及查明网络配置错误。

如何启用 NSG 流日志： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal 如何启用和使用流量分析：https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：对 Azure Database for MariaDB 使用高级威胁防护。 高级威胁防护可检测异常活动，表明尝试访问或利用数据库的行为异常且可能有害。
如何配置 Azure Database for MariaDB 的高级威胁防护：https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Azure 安全中心监视**：是

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：对于需要访问 Azure Database for MariaDB 实例的资源，可以使用虚拟网络服务标记来定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应源或目标字段中指定服务标记名称（例如 SQL.WestUs），可允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。
注意： Azure Database for MariaDB 使用 "Microsoft .Sql" 服务标记。

有关使用服务标记的详细信息： https://docs.microsoft.com/azure/virtual-network/service-tags-overview 了解 Azure Database for MariaDB 的服务标记用法：https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现与 Azure 策略关联的 Azure Database for MariaDB 实例的网络设置和网络资源的标准安全配置。 使用 "DBforMariaDB" 和 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制实施 Azure Database for MariaDB 实例的网络配置。 你还可以使用与网络或 Azure Database for MariaDB 实例相关的内置策略定义，例如：

- 应启用 DDoS 防护标准版

- 应为 MariaDB 服务器启用专用终结点

- MariaDB 服务器应使用虚拟网络服务终结点

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

适用于网络的 Azure Policy 示例： https://docs.microsoft.com/azure/governance/policy/samples/

如何创建 Azure 蓝图： https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对与 MariaDB 实例相关的资源使用标记，以提供元数据和逻辑组织。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure CLI 根据资源的标记查找资源或对其执行操作。

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure 安全中心监视：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Database for MariaDB 实例相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。
如何查看和检索 Azure 活动日志事件： https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 如何在 Azure Monitor 中创建警报：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure 安全中心监视**：不适用

责任：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护用于 Azure 资源的时间源，如日志中的时间戳 Azure Database for MariaDB。


**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：启用诊断设置和服务器日志，并引入日志来聚合 Azure Database for MariaDB 实例生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储。 或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。
如何配置和访问 Azure Database for MariaDB 的服务器日志：https://docs.microsoft.com/azure/mariadb/concepts-server-logs

如何配置和访问 Azure Database for MariaDB 的审核日志： https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal 如何载入 Azure Sentinel：https://docs.microsoft.com/azure/sentinel/quickstart-onboard



Azure 安全中心监视：不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：对 Azure Database for MariaDB 实例启用诊断设置，以便访问审核、安全和诊断日志。 确保专门启用了 MariaDB 审核日志。 自动可用的活动日志包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。 还可以启用 Azure 活动日志诊断设置，并将日志发送到相同的 Log Analytics 工作区或存储帐户。

如何配置和访问 Azure Database for MariaDB 的服务器日志： https://docs.microsoft.com/azure/mariadb/concepts-server-logs 如何配置和访问 Azure Database for MariaDB 的审核日志： https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal 如何配置 Azure 活动日志的诊断设置：https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



Azure 安全中心监视：不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，对于用于保存 Azure Database for MariaDB 日志的 Log Analytics 工作区，请根据组织的符合性法规设置保留期。 使用 Azure 存储帐户进行长期/存档存储。
如何为 Log Analytics 工作区设置日志保持参数：将 https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period 资源日志存储在 Azure 存储帐户中：https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Azure 安全中心监视**：不适用

责任：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指南**：分析和监视 MariaDB 实例中的日志，以了解异常行为。 使用 Azure Monitor 的 Log Analytics 工作区查看日志并对日志数据执行查询。 或者，可以将数据启用并加入 Azure Sentinel 或第三方 SIEM。

如何加入 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

有关 "Log Analytics" 工作区的详细信息：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure Monitor 中执行自定义查询： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Azure 安全中心监视**：不适用

责任：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：针对异常活动启用警报

**指南**：为 MariaDB 启用高级威胁防护。 适用于 Azure Database for MariaDB 的高级威胁防护可检测异常活动，这些活动表明访问或利用数据库的异常和潜在有害尝试。

此外，你可以为 MariaDB 启用服务器日志和诊断设置，并将日志发送到 Log Analytics 工作区。 将 Log Analytics 工作区加入 Azure Sentinel，因为它提供了安全业务流程自动化响应 (SOAR) 解决方案。 这样便可以创建 playbook（自动化解决方案）并将其用于修正安全问题。

如何为 MariaDB 启用高级威胁防护：https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

如何配置和访问 MariDB 的服务器日志：https://docs.microsoft.com/azure/mariadb/concepts-server-logs

如何配置和访问 MariaDB 的审核日志：https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

如何加入 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：不适用;MariaDB 不会处理或产生与反恶意软件相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：不适用;MariaDB 不会处理或产生 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用;基准测试用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：维护对 MariaDB 实例的管理平面（Azure 门户/Azure 资源管理器）具有管理访问权限的用户帐户的清单。 此外，维护有权访问 MariaDB 实例的数据平面的管理帐户的清单。 （在创建 MariaDB 服务器时，你为管理员用户提供凭据。 此管理员可用于创建其他 MariaDB 用户。）

了解 MariaDB 的访问管理：https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

了解 Azure 订阅的 Azure 内置角色：https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**： Azure Active Directory 没有默认密码的概念。

创建 MariaDB 资源本身时，Azure 会强制创建具有强密码的管理用户。 但是，一旦创建了 MariaDB 实例，你就可以使用你创建的帐户的第一个服务器管理员帐户创建其他用户并授予其管理访问权限。 创建这些帐户时，请确保为每个帐户配置不同的强密码。

如何为 MariaDB 创建其他帐户：https://docs.microsoft.com/azure/mariadb/howto-create-users


**Azure 安全中心监视**：不适用

责任：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：创建有关使用可访问 MariaDB 实例的专用管理帐户的标准操作过程。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

了解 Azure 安全中心标识和访问： https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指南**：对 MariaDB 的数据平面访问由存储在数据库中的标识控制，不支持 SSO。 MariaDB 的控制平面访问通过 REST API 提供，并支持 SSO。 若要进行身份验证，请将请求的授权标头设置为从 Azure Active Directory (AAD) 获取的 JSON Web 令牌。

了解 Azure Database for MariaDB REST API：https://docs.microsoft.com/rest/api/mariadb/

**Azure 安全中心监视**：不适用

责任：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure AD MFA，并遵循 Azure 安全中心标识和访问管理建议。

如何在 Azure 中启用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心监视标识和访问： https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：将 PAW（特权访问工作站）与为登录和配置 Azure 资源而配置的 MFA 结合使用。

了解特权访问工作站： https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中启用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心监视**：不适用

责任：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：为 MariaDB 启用高级威胁防护，以生成可疑活动的警报。

此外，可以使用 Azure AD Privileged Identity Management (PIM) 在环境中发生可疑活动或不安全的活动时生成日志和警报。 使用 Azure AD 风险检测查看有关风险用户行为的警报和报告。

如何为 MariaDB 设置高级威胁防护：https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

如何部署 Privileged Identity Management (PIM)： https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

了解 Azure AD 风险检测： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置只允许从 IP 地址范围或国家/地区的特定逻辑分组访问，以限制对 Azure 资源（例如 MariaDB）的访问。

如何在 Azure 中配置命名位置： https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (AAD) 作为中心身份验证和授权系统。 AAD 通过对静态数据和传输中数据使用强加密来保护数据。 AAD 还会对用户凭据进行加盐、哈希处理和安全存储。

Azure AD 身份验证不能用于直接访问 MariaDB 数据平面，但是，可以在管理平面级别（例如 Azure 门户）使用 Azure AD 凭据来控制 MariaDB 管理员帐户。

如何更新 MariaDB 的管理员密码：https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Azure 安全中心监视**：是

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：查看 Azure Active Directory 日志，以帮助发现可包括具有 MariaDB 管理角色的帐户的陈旧帐户。 此外，使用 Azure 标识访问评审来有效地管理组成员身份、访问可用于访问 MariaDB 的企业应用程序以及角色分配。 应定期（例如每 90 天一次）评审用户访问权限，以确保正确用户持续拥有访问权限。

了解 Azure AD 报告： https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 标识访问评审： https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Azure 安全中心监视：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视访问已停用帐户的企图

**指南**：启用 MariaDB 和 Azure Active Directory 的诊断设置，将所有日志发送到 Log Analytics 工作区。 在 Log Analytics 工作区中配置所需的警报（例如身份验证尝试失败）。

如何配置和访问 MariaDB 的服务器日志：https://docs.microsoft.com/azure/mariadb/concepts-server-logs

如何配置和访问 MariaDB 的审核日志：https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

如何将 Azure 活动日志集成到 Azure Monitor： https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：为 MariaDB 启用高级威胁防护。 适用于 Azure Database for MariaDB 的高级威胁防护可检测异常活动，这些活动表明访问或利用数据库的异常和潜在有害尝试。

使用 Azure Active Directory 标识保护和风险检测功能可配置对检测到的可疑操作的自动响应。 可以通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。

如何为 MariaDB 启用高级威胁防护：https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

如何配置和启用 Identity Protection 风险策略：https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何查看 Azure AD 风险登录： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何加入 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：不适用;客户密码箱尚不支持 Azure Database for MariaDB。

客户密码箱支持的服务列表： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记来帮助跟踪 Azure Database for MariaDB 实例或存储或处理敏感信息的相关资源。

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure 安全中心监视：不适用

责任：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实现单独的订阅和/或管理组。 结合使用专用链接、服务终结点和/或 MariaDB 防火墙规则，隔离和限制对 MariaDB 实例的网络访问。

如何创建其他 Azure 订阅： https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组： https://docs.microsoft.com/azure/governance/management-groups/create

如何配置 Azure Database for MariaDB 的专用链接：https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

如何配置 Azure Database for MariaDB 的服务终结点：https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

如何配置 Azure Database for MariaDB 的防火墙规则：https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：使用 Azure Vm 访问 MariaDB 实例时，请使用专用链接、MariaDB 网络配置、网络安全组和服务标记，以减轻数据渗透。

Microsoft 管理 MariaDB 的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

如何减小 Azure Database for MariaDB 的数据渗透：https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：不适用

责任：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**： Azure Database for MariaDB 支持使用传输层安全性（TLS）将 Azure Database for MariaDB 服务器连接到客户端应用程序，之前称为安全套接字层（SSL）。 通过在数据库服务器与客户端应用程序之间强制实施 TLS 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。 在 Azure 门户中，确保已为所有 MariaDB 实例启用 "强制 SSL 连接"。

如何在传输中为 MariaDB 配置加密：https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

Azure 安全中心监视：不可用

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：目前尚不 Azure Database for MariaDB 支持数据标识、分类和丢失防护功能。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于由 Microsoft 管理的底层平台，Microsoft 将所有客户内容都视为敏感内容，竭尽全力防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Azure 安全中心监视：不可用

责任：共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指南**：使用 Azure AD RBAC 来控制对 Azure Database for MariaDB 管理平面（Azure 门户/Azure 资源管理器）的访问。 对于数据平面访问（在数据库本身内），使用 SQL 查询创建用户并配置用户权限。

如何在 Azure 中配置 RBAC： https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

如何使用 SQL for MariaDB 配置用户访问权限：https://docs.microsoft.com/azure/mariadb/howto-create-users

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**：不适用;此准则用于计算资源。

Microsoft 管理 MariaDB 的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**： Azure Database for MariaDB 服务使用 FIPS 140-2 验证的加密模块进行静态数据的存储加密。 数据（包括备份）在磁盘上加密，运行查询时创建的临时文件除外。 该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥由系统进行管理。 存储加密始终处于启用状态，无法禁用。

了解 MariaDB 的静态加密：https://docs.microsoft.com/azure/mariadb/concepts-security

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建 Azure Database for MariaDB 和其他关键或相关资源的生产实例发生更改的警报。

如何针对 Azure 活动日志事件创建警报： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理。](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：当前不可用;Azure 安全中心尚不支持 Azure Database for MariaDB 服务器的漏洞评估。


**Azure 安全中心监视**：是

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方自动软件修补管理解决方案

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**： Microsoft 对支持 Azure Database for MariaDB server 的基础系统执行漏洞管理。


**Azure 安全中心监视**：不适用

**责任**：Microsoft

## <a name="inventory-and-asset-management"></a>清单和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指南**：使用 Azure 资源关系图可查询和发现订阅中的所有资源（包括 Azure Database for MariaDB 服务器）。 确保你在租户中拥有适当（读取）权限，并且能够枚举所有 Azure 订阅以及订阅中的资源。

如何使用 Azure Resource Graph 创建查询： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 订阅： https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

了解 Azure RBAC： https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：将标记应用到 Azure Database for MariaDB 服务器和其他相关资源，这些资源提供了元数据以逻辑方式将它们组织到分类。

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪 Azure Database for MariaDB 服务器和相关资源。 定期协调清单，并确保及时从订阅中删除未经授权的资源。

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

**指南**：使用 Azure Policy 对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Graph 创建查询： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 安全中心监视**：不适用

**责任**：客户

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

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Policy 拒绝特定的资源类型： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



Azure 安全中心监视：不适用

责任：客户

### <a name="610-implement-approved-application-list"></a>6.10：实施已批准的应用程序列表

**指导**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6.11： <div>限制用户通过脚本与 Azure 资源管理器进行交互的能力</div>

**指导**：使用 Azure 条件访问，通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。 这可以防止在高安全性环境（如 Azure Database for MariaDB 包含敏感信息的服务器）中创建和更改资源。

如何配置条件访问以阻止访问 Azure 资源管理器： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。


**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：定义和实现 Azure 策略的 Azure Database for MariaDB 实例的标准安全配置。 使用 "DBforMariaDB" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制实施 Azure Database for MariaDB 服务器的网络配置。 你还可以使用与 Azure Database for MariaDB 服务器相关的内置策略定义，例如：

- 应为 Azure Database for MariaDB 启用异地冗余备份

如何查看可用的 Azure Policy 别名： https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy [拒绝] 和 [不存在时部署] 在 Azure 资源中强制实施安全设置。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

了解 Azure Policy 效果： https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果将自定义 Azure 策略定义用于 Azure Database for MariaDB 服务器和相关资源，请使用 Azure Repos 安全地存储和管理你的代码。

如何在 Azure DevOps 中存储代码： https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 文档： https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指南**：使用 "DBforMariaDB" 命名空间中的 Azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 另外，开发一个用于管理策略例外的流程和管道。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实施自动配置监视

**指南**：使用 "DBforMariaDB" 命名空间中的 Azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 使用 Azure 策略 [audit]、[拒绝] 和 [部署（如果不存在））自动强制实施 Azure Database for MariaDB 实例和相关资源的配置。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：对于在 Azure App Service 上运行的用于访问 Azure Database for MariaDB 服务器的 Azure 虚拟机或 web 应用程序，请将托管服务标识与 Azure Key Vault 结合使用以简化和保护 Azure Database for MariaDB 服务器机密管理。 确保启用 Key Vault 软删除。

如何与 Azure 托管标识集成： https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何创建 Key Vault： https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用托管标识提供 Key Vault 身份验证： https://docs.microsoft.com/azure/key-vault/managed-identity 



**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**： Azure Database for MariaDB server 当前不支持访问数据库 Azure Active Directory 身份验证。  在创建 Azure Database for MariaDB 服务器时，我们会提供管理员用户的凭据。 可以通过此管理员创建其他 MariaDB 用户。  

对于正在用于访问 Azure Database for MariaDB 服务器 Azure App Service 上运行的 Azure 虚拟机或 web 应用程序，请将托管服务标识与 Azure Key Vault 一起使用，以存储和检索 Azure Database for MariaDB 服务器的凭据。  确保启用 Key Vault 软删除。

使用托管标识在 Azure Active Directory (AD) 中为 Azure 服务提供一个自动托管标识。 通过托管标识可向支持 Azure AD 身份验证的任何服务（包括 Key Vault）证明身份，无需在代码中放入任何凭据。 如何配置托管标识： https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm 。 如何与 Azure 托管标识集成： https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity 。



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

如何设置凭据扫描器： https://secdevtools.azurewebsites.net/helpcredscan.html

Azure 安全中心监视：不适用

责任：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用；此建议适用于计算资源。

Microsoft 反恶意软件会在支持 Azure 服务（例如，Azure 应用服务）的基础主机上启用，但不会对客户内容运行。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：在支持 Azure 服务的基础主机（例如 Azure Database for MariaDB server）上启用了 Microsoft 反恶意软件，但它不会在客户内容上运行。

预先扫描要上传到非计算 Azure 资源的任何内容，例如应用服务、Data Lake Storage、Blob 存储、Azure Database for MariaDB 服务器等。Microsoft 无法访问这些实例中的数据。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用；此建议适用于计算资源。

在支持 Azure 服务的基础主机（例如 Azure Database for MariaDB server）上启用了 Microsoft 反恶意软件，但它不会在客户内容上运行。


**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**： Azure Database for MariaDB 获取完整备份、差异备份和事务日志备份。  Azure Database for MariaDB 可自动创建服务器备份并将其存储在用户配置的本地冗余或异地冗余存储中。 备份可以用来将服务器还原到某个时间点。 备份和还原是任何业务连续性策略的基本组成部分，因为它们可以保护数据免遭意外损坏或删除。  默认的备份保留期为七天。 可以选择将其配置为长达 35 天。 所有备份都使用 AES 256 位加密进行加密。

了解 MariaDB 的备份：https://docs.microsoft.com/azure/mariadb/concepts-backup

了解 MariaDB 初始配置：https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Azure 安全中心监视**：是

**责任**：共享

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整的系统备份并备份所有客户托管密钥

**指南**： Azure Database for MariaDB 自动创建服务器备份并将其存储在用户配置的本地冗余或异地冗余存储中。 备份可以用来将服务器还原到某个时间点。  备份和还原是任何业务连续性策略的基本组成部分，因为它们可以保护数据免遭意外损坏或删除。

如果对 MariaDB 服务器中存储的数据使用客户端数据加密 Key Vault，请确保密钥的定期自动备份。

了解 MariaDB 的备份：https://docs.microsoft.com/azure/mariadb/concepts-backup

如何备份 Key Vault 密钥： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Azure 安全中心监视**：是

责任：共享

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：在 Azure Database for MariaDB 中，从原始服务器的备份执行还原，以便定期测试备份。 可以使用两种类型的还原：时间点还原和异地还原。 时间点还原可以与任一备份冗余选项配合使用，所创建的新服务器与原始服务器位于同一区域。 异地还原只能在已将服务器配置为进行异地冗余存储的情况下使用，用于将服务器还原到另一区域。

估计的恢复时间取决于若干因素，包括数据库大小、事务日志大小、网络带宽，以及在同一区域同时进行恢复的数据库总数。 恢复时间通常少于 12 小时。

了解 Azure Database for MariaDB 中的备份和还原：https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**： Azure Database for MariaDB 获取完整备份、差异备份和事务日志备份。 可以通过这些备份将服务器还原到所配置的备份保留期中的任意时间点。 默认的备份保留期为七天。 可以选择将其配置为长达 35 天。 所有备份都使用 AES 256 位加密进行加密。

了解 Azure Database for MariaDB 中的备份和还原：https://docs.microsoft.com/azure/mariadb/concepts-backup


**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

- 有关生成自己的安全事件响应过程的指南： https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/
- Microsoft 安全响应中心的事件剖析： https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/
- 客户还可以利用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划： https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心对用于发出警报的调查结果或分析的置信度，以及导致警报的活动背后存在恶意意图的可信度。 

此外，使用标记清楚地标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- Azure 安全中心中的安全警报：https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

- 使用标记来组织 Azure 资源：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

- 请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities（IT 规划和功能的测试、培训与演练计划指南）： https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

- 如何设置 Azure 安全中心安全联系人： https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- 如何配置连续导出： https://docs.microsoft.com/azure/security-center/continuous-export
- 如何将警报流式传输到 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，以保护 Azure 资源。
    

如何配置工作流自动化和逻辑应用： https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心监视**：不适用

责任：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：定期对 Azure 资源执行渗透测试，确保在 60 天内修正所有发现的关键安全问题

指南：遵循 Microsoft Rules of Engagement 以确保渗透测试不违反 Microsoft 政策： 

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

可在以下位置找到有关 Microsoft 管理的云基础结构、服务和应用程序的 Microsoft 策略、红组和实时站点渗透测试的详细信息：https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
