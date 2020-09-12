---
title: Cosmos DB 的 Azure 安全基线
description: Cosmos DB 的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 051f6e6ef15cc94e2b607c74e666bae9c91c1160
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401616"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Cosmos DB 的 Azure 安全基线

Cosmos DB 的 Azure 安全基线包含可帮助你改善部署安全态势的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

有关详细信息，请参阅[安全控制：网络安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络中使用网络安全组或 Azure 防火墙保护资源

**指南**：通过使用 Azure 私有链接，你可以通过专用终结点连接到 Azure Cosmos 帐户。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 还可以通过在网络安全组中配置一组严格的出站规则 (NSG) 并将该 NSG 与子网相关联，从而降低数据渗透的风险。

还可以使用服务终结点来保护 Azure Cosmos 帐户。 通过启用服务终结点，可将 Azure Cosmos 帐户配置为仅允许从 Azure 虚拟网络的特定子网进行访问。 启用 Azure Cosmos DB 服务终结点后，便可以将对 Azure Cosmos 帐户的访问限制为只能通过来自虚拟网络中某个子网的连接进行。

还可以使用 IP 防火墙保护存储在 Azure Cosmos 帐户中的数据。 Azure Cosmos DB 支持使用基于 IP 的访问控制来提供入站防火墙支持。 可以使用 Azure 门户、Azure 资源管理器模板、Azure CLI 或 Azure PowerShell 在 Azure Cosmos 帐户上设置 IP 防火墙。

Azure 专用链接概述： https://docs.microsoft.com/azure/private-link/private-link-overview

如何配置 Azure Cosmos DB 的专用终结点：  https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

如何创建采用安全配置的网络安全组： https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

如何在 Cosmos DB 中配置 IP 防火墙： https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 VNet、子网和 NIC 的配置与流量

**指导**：使用 Azure 安全中心并遵循网络保护建议来帮助保护与 Azure Cosmos 帐户相关的网络资源。

虚拟机部署到 Azure Cosmos 帐户所在的同一虚拟网络中后，可以使用网络安全组 (NSG) 来降低数据外泄的风险。 启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

了解 Azure 安全中心提供的网络安全性： https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

如何启用 NSG 流日志： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用和使用流量分析： https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：使用跨源资源共享 (CORS) 功能可使一个域中运行的 Web 应用程序能够访问另一个域中的资源。 Web 浏览器实施一种称为“同源策略”的安全限制，防止网页调用不同域中的 API。 但是，CORS 提供了一种安全的方式，允许源域调用另一个域中的 API。 为 Azure Cosmos 帐户启用 CORS 支持后，仅对经过身份验证的请求进行评估，以根据指定的规则确定是否允许这些请求。

配置跨源资源共享： https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Azure 安全中心监视**：是

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：使用高级威胁防护 (ATP) 进行 Azure Cosmos DB。 适用于 Azure Cosmos DB 的 ATP 提供一个附加的安全智能层，用于检测是否有人试图以异常且可能有害的方式访问或恶意利用 Azure Cosmos。 使用此保护层可以应对威胁并将其与中心安全监视系统相集成。

在与 Azure Cosmos DB 实例相关联的虚拟网络上启用 DDoS 保护，以防范 DDoS 攻击。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。

如何配置 Azure Cosmos DB 高级威胁防护： https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

如何配置 DDoS 保护： https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

了解 Azure 安全中心集成的威胁情报： https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指导**：启用网络安全组 (NSG) 流日志，并将日志发送到存储帐户以进行流量审核。 可将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

如何启用 NSG 流日志： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用和使用流量分析： https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：使用适用于 Azure Cosmos DB 的高级威胁防护 (ATP)。 适用于 Azure Cosmos DB 的 ATP 提供一个附加的安全智能层，用于检测是否有人试图以异常且可能有害的方式访问或恶意利用 Azure Cosmos。 使用此保护层可以应对威胁并将其与中心安全监视系统相集成。 

如何配置 Cosmos DB 高级威胁防护： https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Azure 安全中心监视**：是

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：不适用；建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：对于需要访问 Azure Cosmos 帐户的资源，请使用虚拟网络服务标记来定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 AzureCosmosDB），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

有关使用服务标记的详细信息： https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现 Azure 策略的网络资源的标准安全配置。 使用 "Microsoft.DocumentDB" 和 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 Azure Cosmos DB 实例的网络配置。 还可以为 Azure Cosmos DB 利用内置策略定义，例如：

- 为 Cosmos DB 帐户部署高级威胁防护

- Cosmos DB 应使用虚拟网络服务终结点

还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、基于角色的访问控制 (RBAC)和策略），来简化大规模的 Azure 部署。 可以轻松将蓝图应用到新的订阅和环境，并通过版本控制来微调控制措施和管理。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何创建 Azure 蓝图： https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：对与 Azure Cosmos DB 部署关联的网络资源使用标记，以便按逻辑将这些资源组织到分类中。

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Cosmos DB 实例相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。 

如何查看和检索 Azure 活动日志事件： https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure Monitor 中创建警报： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护用于 Azure 资源的时间源，如日志中的时间戳 Azure Cosmos DB。


**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：通过 Azure Monitor 引入日志来聚合 Azure Cosmos DB 生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用存储帐户进行长期/存档存储。 或者，可以启用数据并将其加入 Azure Sentinel 或第三方安全事件和事件管理 (SIEM)。 

如何为 Azure Cosmos DB 启用诊断日志： https://docs.microsoft.com/azure/cosmos-db/logging

如何加入 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：为 Azure Cosmos DB 启用诊断设置，并将日志发送到 Log Analytics 工作区或存储帐户。 Azure Cosmos DB 中的诊断设置用于收集资源日志。 这些日志是按请求捕获的，也称为“数据平面日志”。 数据平面操作的一些示例包括删除、插入和读取。 还可以启用 Azure 活动日志诊断设置，并将日志发送到相同的 Log Analytics 工作区。

如何启用 Azure Cosmos DB 的诊断设置： https://docs.microsoft.com/azure/cosmos-db/logging

如何启用 Azure 活动日志的诊断设置： https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure Monitor 中，根据组织的合规性规章，为与 Azure Cosmos DB 实例关联的 Log Analytics 工作区设置日志保留期。

如何设置日志保留参数： https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：可以在 Log Analytics 工作区中执行查询，以搜索字词、识别趋势、分析模式，并根据收集的 Azure Cosmos DB 日志提供其他许多见解。

如何在 Log Analytics 工作区中对 Azure Cosmos DB 执行查询： https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：针对异常活动启用警报

**指南**：在 Azure 安全中心中，为 Azure Cosmos DB 启用高级威胁防护，以监视安全日志和事件中的异常活动。 在 Azure Cosmos DB 中启用诊断设置，并将日志发送到 Log Analytics 工作区。

 

你还可以将 Log Analytics 工作区加入 Azure Sentinel，因为它提供了一个安全业务流程自动响应 (之忠诚度) 解决方案。 这样便可以创建 playbook（自动化解决方案）并将其用于修正安全问题。 此外，还可以使用 Azure Monitor 在 Log Analytics 工作区中创建自定义日志警报。

Azure Cosmos DB 的威胁防护警报列表： https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

如何加入 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

使用 Azure Monitor 创建、查看和管理日志警报： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：不适用；Azure Cosmos DB 不会处理或生成与反恶意软件相关的日志。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：不适用；Azure Cosmos DB 不会处理或生成与 DNS 相关的日志。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：可以使用 Azure 门户中的“标识和访问控制(IAM)”窗格配置基于角色的访问控制 (RBAC)，并维护有关 Azure Cosmos DB 资源的库存。 角色将应用到 Active Directory 中的用户、组、服务主体和托管标识。 对于个人和组，可使用内置角色或自定义角色。

Azure Cosmos DB 为 Azure Cosmos DB 中的常见管理方案提供内置的 RBAC。 在 Azure Active Directory (AD) 中具有配置文件的个人可以将这些 Azure 角色分配给用户、组、服务主体或托管标识，以授予或拒绝对 Azure Cosmos DB 资源的资源和操作的访问权限。

还可以使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组的成员的帐户。 

此外，可以使用 Azure Active Directory 和帐户特定的主密钥来控制 Azure Cosmos DB 中的某些操作。  使用“disableKeyBasedMetadataWriteAccess”帐户设置控制密钥访问。

了解 Azure Cosmos DB 中基于角色的访问控制： https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

使用 Azure Cosmos DB 操作（Microsoft DocumentDB 命名空间）生成自己的自定义角色： https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

在 Azure Active Directory 中创建新角色： https://docs.microsoft.com/azure/role-based-access-control/custom-roles

如何使用 PowerShell 获取 Azure Active Directory 中的目录角色： https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 获取 Azure Active Directory 中目录角色的成员： https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

将用户访问权限仅限于数据操作： https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：不存在与 Azure AD 或 Azure Cosmos DB 相关的默认密码或空白密码的概念。 Azure Cosmos DB 使用两种类型的密钥来验证用户身份并提供其数据和资源的访问权限：主密钥和资源令牌。 随时可以重新生成这些密钥。

了解如何保护对 Azure Cosmos DB 中数据的访问： https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

如何重新生成 Azure Cosmos DB 密钥： https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

如何使用 Azure Active Directory 以编程方式访问密钥： https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指导**：不适用；Azure Cosmos DB 不支持管理员帐户。  所有访问都与 Azure Active Directory 和 Azure 基于角色的访问控制 (Azure RBAC) 相集成。



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指南**： Azure Cosmos DB 使用两种类型的密钥来向用户授权，并且不支持在数据平面级别上对 (SSO) 进行单一登录。 可以通过 REST API 访问 Cosmos DB 的控制平面，并支持 SSO。 若要进行身份验证，请将请求的授权标头设置为从 Azure Active Directory (AAD) 获取的 JSON Web 令牌。

了解 Azure Database for Cosmos DB REST API： https://docs.microsoft.com/rest/api/cosmos-db/

了解 Azure Active Directory 的 SSO： https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure Active Directory 多重身份验证，并遵循 Azure 安全中心标识和访问管理的建议。

如何在 Azure 中启用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心监视标识和访问： https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指导**：使用配置了多重身份验证的特权访问工作站 (PAW) 来登录并配置 Azure 资源。

了解特权访问工作站： https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中启用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：使用适用于 Azure Cosmos DB 的高级威胁防护 (ATP)。 适用于 Azure Cosmos DB 的 ATP 提供一个附加的安全智能层，用于检测是否有人试图以异常且可能有害的方式访问或恶意利用 Azure Cosmos。 使用此保护层可以应对威胁并将其与中心安全监视系统相集成。 

此外，当环境中出现可疑或不安全的活动时，可以使用 Azure Active Directory (AD) Privileged Identity Management (PIM) 生成日志和警报。

使用 Azure AD 风险检测查看有关风险用户行为的警报和报告。

如何部署 Privileged Identity Management (PIM)： https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

了解 Azure AD 风险检测： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：配置条件访问策略的位置条件并管理命名位置。 使用命名位置可以创建 IP 地址范围或者国家和地区的逻辑分组。 可以限制对已配置的命名位置的敏感资源（如 Azure Cosmos DB 实例）的访问。

如何在 Azure 中配置命名位置： https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

指南：使用 Azure Active Directory (AD) 作为中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中的数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

如何创建和配置 Azure Active Directory 实例： https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

如何配置和管理 Azure SQL Azure Active Directory 身份验证： https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：Azure Active Directory 提供日志来帮助发现过时的帐户。 此外，可以使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

如何使用 Azure 标识访问评审： https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视访问已停用帐户的企图

**指南**：你可以为 Azure Active Directory 用户帐户创建诊断设置，将审核日志和登录日志发送到 Log Analytics 工作区，你可以在该工作区中配置所需的警报。

如何将 Azure 活动日志集成到 Azure Monitor： https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：使用适用于 Azure Cosmos DB 的高级威胁防护 (ATP)。 适用于 Azure Cosmos DB 的 ATP 提供一个附加的安全智能层，用于检测是否有人试图以异常且可能有害的方式访问或恶意利用 Azure Cosmos。 使用此保护层可以应对威胁并将其与中心安全监视系统相集成。 

你还可以使用 Azure AD Identity Protection 和风险检测功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 此外，还可以将日志引入 Azure Sentinel 以便进一步调查。

如何查看 Azure Active Directory 风险的登录： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和启用 Identity Protection 风险策略： https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何加入 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：是

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：当前不可用;适用于 Azure Database for Cosmos DB 客户密码箱尚不受支持。

客户密码箱支持的服务列表： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 安全中心监视**：目前不可用

**责任**：不适用

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪用于存储或处理敏感信息的 Azure Cosmos DB 实例。

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 Azure Cosmos DB 实例按虚拟网络/子网进行分隔，进行了相应的标记，并在网络安全组 (NSG) 或 Azure 防火墙中受到保护。 应该隔离存储敏感数据的 Azure Cosmos DB 实例。 使用 Azure 专用链接可以通过专用终结点连接到 Azure Cosmos DB 实例帐户。 专用终结点是虚拟网络中某个子网内的一组专用 IP 地址。 然后，可以将访问限制为只能从选定的专用 IP 地址进行。 

如何创建其他 Azure 订阅： https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组： https://docs.microsoft.com/azure/governance/management-groups/create

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何配置 Azure Cosmos DB 的专用终结点： https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

如何创建采用安全配置的网络安全组： https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：你可以为 Azure Cosmos DB 部署高级威胁防护，这会检测异常活动，指示访问或利用数据库的异常和潜在有害尝试。 它可以触发以下警报：

- 从异常位置访问

- 异常数据提取

此外，在使用虚拟机访问 Azure Cosmos DB 实例时，使用专用链接、防火墙、网络安全组和服务标记，以减轻数据渗透的可能性。 Microsoft 管理 Azure Cosmos DB 的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

如何配置 Cosmos DB 高级威胁防护： https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：是

**责任**：共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：与 Azure Cosmos DB 建立的所有连接都支持 HTTPS。 Azure Cosmos DB 还支持 TLS1.2。 可以在服务器端强制实施最低 TLS 版本。 为此，请联系 [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)。

Cosmos DB 安全性概述： https://docs.microsoft.com/azure/cosmos-db/database-security

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：自动数据标识、分类和丢失防护功能尚不适用于 Azure Cosmos DB。 但是，可以使用 Azure 认知搜索集成进行分类和数据分析。 还可以实施第三方解决方案（如果出于合规性目的而需要如此）。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

使用 Azure 认知搜索为 Azure Cosmos DB 数据编制索引： https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp;bc=/azure/cosmos-db/breadcrumb/toc.json

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指导**：Azure Cosmos DB 为 Azure Cosmos DB 中的常见管理方案提供内置的基于角色的访问控制 (RBAC)。 在 Azure Active Directory 中有配置文件的个人可以将这些 Azure 角色分配给用户、组、服务主体或托管标识，以授予或拒绝对 Azure Cosmos DB 资源的资源和操作的访问权限。 角色分配范围仅限控制平面访问，包括对 Azure Cosmos 帐户、数据库、容器和套餐（吞吐量）的访问。

如何在 Azure Cosmos DB 中实施 RBAC： https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：不适用；此项指导适用于计算资源。

Microsoft 管理 Cosmos DB 的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：默认情况下，存储在 Cosmos DB 中的所有用户数据已经过静态加密。 没有任何控件可以关闭静态加密。 Azure Cosmos DB 在运行帐户的所有区域中使用 AES-256 加密。

默认情况下，Microsoft 管理用于对 Azure Cosmos 帐户中的数据进行加密的密钥。 你可以根据需要使用自己的密钥来添加另一个加密层。

了解 Azure Cosmos DB 的静态加密： https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

了解 Azure Cosmos DB 静态加密的密钥管理： https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

如何为 Azure Cosmos DB 帐户配置客户管理的密钥： https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，可以创建要在 Azure Cosmos DB 生产实例发生更改时发出的警报。

如何针对 Azure 活动日志事件创建警报： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

如何针对 Azure 活动日志事件创建警报： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理。](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：请遵循 Azure 安全中心针对 Azure Cosmos DB 实例提供的建议。 

Microsoft 在支持 Azure Cosmos DB 实例的底层主机上执行系统修补和漏洞管理。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

Azure 安全中心提供的支持功能： https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Azure 安全中心监视**：是

**责任**：共享

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：不适用；此项指导适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方自动软件修补管理解决方案

**指导**：不适用；此项指导适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：不适用；此项指导适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：不适用；此项指导适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="inventory-and-asset-management"></a>清单和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指导**：使用 Azure 门户或 Azure Resource Graph 发现订阅中的所有资源（不局限于 Azure Cosmos DB，同时还包括计算、其他存储、网络、端口和协议等资源）。  确保你在租户中拥有适当的权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

如何使用 Azure Resource Graph 创建查询： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 订阅： https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

了解 Azure 基于角色的访问控制： https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到提供元数据的 Azure Cosmos DB 实例和相关资源，以便按逻辑将其组织到分类中。

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

哪些 Azure Cosmos DB 资源支持标记： https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，使用标记、管理组与单独的订阅来组织和跟踪资产（包括但不限于 Azure Cosmos DB 资源）。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

如何创建其他 Azure 订阅： https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组： https://docs.microsoft.com/azure/governance/management-groups/create

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单

**指导**：不适用；此项指导适用于计算资源和 Azure（作为一个整体）。

**Azure 安全中心监视**：不适用

**责任**：不适用

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

**指导**：不适用；此项基线适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：不适用；此项指导适用于计算资源和 Azure（作为一个整体）。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：不适用；此项指导适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型 

- 允许的资源类型

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Policy 拒绝特定的资源类型： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-implement-approved-application-list"></a>6.10：实施已批准的应用程序列表

**指导**：不适用；此项指导适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器进行交互的功能

**指导**：使用 Azure 条件访问，通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。 这可防止在高度安全的环境中创建和更改资源。

如何配置条件访问以阻止访问 Azure 资源管理器： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指导**：不适用；此项指导适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：不适用；此项指导适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。


**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用 Azure Policy 为 Cosmos DB 实例定义和实施标准安全配置。 在“Microsoft.DocumentDB”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Cosmos DB 实例的配置。 还可以为 Azure Cosmos DB 利用内置策略定义，例如：

- 为 Cosmos DB 帐户部署高级威胁防护

- Cosmos DB 应使用虚拟网络服务终结点

如何查看可用的 Azure Policy 别名： https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：不适用；此项指导适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy [拒绝] 和 [不存在时部署] 在 Azure 资源中强制实施安全设置。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

了解 Azure Policy 效果： https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：不适用；此项指导适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果将自定义 Azure 策略定义用于 Cosmos DB 或相关资源，请使用 Azure Repos 安全地存储和管理你的代码。

Azure Repos 文档： https://docs.microsoft.com/azure/devops/repos/index?view=azure-devopshttps://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指导**：在“Microsoft.DocumentDB”命名空间中使用 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 另外，开发一个用于管理策略例外的流程和管道。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实施自动配置监视

**指导**：在“Microsoft.DocumentDB”命名空间中使用 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 使用 Azure Policy [审核]、[拒绝] 和 [不存在时部署] 为 Azure Cosmos DB 实例和相关资源自动强制实施配置。 

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：对于在 Azure App Service 上运行的 Azure 虚拟机或 web 应用程序，用于访问 Azure Cosmos DB 实例，请将托管服务标识与 Azure Key Vault 结合使用，以简化和保护 Azure Cosmos DB 密钥管理。 确保启用 Key Vault 软删除。

如何与 Azure 托管标识集成： https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何创建 Key Vault： https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

如何对 Key Vault 进行身份验证： https://docs.microsoft.com/azure/key-vault/general/authentication

如何分配 Key Vault 访问策略： https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：对于在 Azure App Service 上运行的 Azure 虚拟机或 web 应用程序，用于访问 Azure Cosmos DB 实例，请将托管服务标识与 Azure Key Vault 结合使用，以简化和保护 Azure Cosmos DB 密钥管理。

使用托管标识在 Azure Active Directory (AD) 中为 Azure 服务提供一个自动托管标识。 通过托管标识可向支持 Azure AD 身份验证的任何服务（包括 Key Vault）证明身份，无需在代码中放入任何凭据。

如何配置托管标识： https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

如何与 Azure 托管标识集成： https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure 安全中心监视**：是

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

如何设置凭据扫描程序： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：不适用；此项指导适用于计算资源。 在支持 Azure 服务 (的基础主机上启用了 Microsoft 反恶意软件，例如 Azure App Service) ，但它不会在客户内容上运行。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：在支持 Azure 服务的基础主机上启用了 Microsoft 反恶意软件 (例如 Azure App Service) ，但它不会在客户内容上运行。

你需负责预先扫描要上传到非计算 Azure 资源（包括 Azure Cosmos DB）的任何文件。 Microsoft 无法访问客户数据，因此无法代表你对客户内容执行反恶意软件扫描。


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指导**：不适用；基准适用于计算资源。 支持 Azure 服务的底层主机上启用了 Microsoft 反恶意软件，但它不会在客户内容上运行。


**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：Azure Cosmos DB 每四个小时拍摄一次数据快照。 所有备份均单独存储在一个存储服务中，并会进行全局复制，以便在发生区域性灾难时可以复原。 在任何给定时间，只保留最后两个快照。 不过，如果删除了容器或数据库，Azure Cosmos DB 会将给定容器或数据库中的现有快照保留 30 天。 若要从备份还原，请与 Azure 支持部门联系。

了解 Azure Cosmos DB 自动备份： https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：Azure Cosmos DB 会定期自动备份数据。 如果删除了数据库或容器，可以提交支持票证或联系 Azure 支持部门，以便从自动联机备份中还原数据。 Azure 支持仅适用于选定的计划，例如“标准”、“开发人员”以及更高级别的计划。 若要还原备份的特定快照，Azure Cosmos DB 要求在该快照的备份周期的持续时间内可用。 

如果使用 Key Vault 来存储 Cosmos DB 实例的凭据，请确保定期自动备份密钥。

了解 Azure Cosmos DB 自动备份： https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

如何还原 Azure Cosmos DB 中的数据： https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

如何备份 Key Vault 密钥： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：如果删除了数据库或容器，可以提交支持票证或联系 Azure 支持部门，以便从自动联机备份中还原数据。 Azure 支持仅适用于选定的计划，例如“标准”、“开发人员”以及更高级别的计划。 若要还原备份的特定快照，Azure Cosmos DB 要求在该快照的备份周期的持续时间内可用。

使用 PowerShell 测试 Azure Key Vault 中存储的机密的还原。 Restore-AzureKeyVaultKey cmdlet 会在指定的密钥保管库中创建密钥。 此密钥是输入文件中已备份密钥的副本，其名称与原始密钥相同。

了解 Azure Cosmos DB 自动备份：

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

如何还原 Azure Cosmos DB 中的数据：

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

如何还原 Azure Key Vault 机密：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：由于 Cosmos DB 中存储的所有用户数据都会进行静态加密和传输中加密，因此你无需采取任何措施。 实现这一理念的另一方式是默认“开启”静态加密。 没有任何控件可以关闭或打开它。 Azure Cosmos DB 在运行帐户的所有区域中使用 AES-256 加密。

在 Key Vault 中启用“软删除”，以防止意外删除或恶意删除密钥。

了解 Azure Cosmos DB 中的数据加密： https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

如何在 Key Vault 中启用“软删除”： https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 安全中心监视**：是

**责任**：共享

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

你还可以利用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划： https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

如何在 Azure 安全中心内配置工作流自动化： https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

有关生成自己的安全事件响应过程的指南： https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 安全响应中心的事件剖析： https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及有关导致发出警报的活动存在恶意企图的置信度。

此外，请明确标记订阅（例如 生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

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

可在以下位置找到有关 Microsoft 管理的云基础结构、服务和应用程序的 Microsoft 策略、红组和实时站点渗透测试的详细信息：  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
