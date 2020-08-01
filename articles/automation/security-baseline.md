---
title: Azure 自动化的 azure 安全基线
description: 用于自动化的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 948ada46085d83c8cf453cdde23cb407ea2dd2d8
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87447704"
---
# <a name="azure-security-baseline-for-automation"></a>用于自动化的 Azure 安全基线

用于自动化的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全性

有关详细信息，请参阅[安全控制：网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**： azure Automation 帐户尚不支持 Azure 私有链接，以便通过专用终结点限制对服务的访问。 针对 Azure 中的资源进行身份验证和运行的 runbook 在 Azure 沙箱上运行，并利用共享的后端资源，Microsoft 负责彼此隔离;它们的网络是不受限制的，可以访问公共资源。 除了支持混合 Runbook 辅助角色以外，Azure Automation 目前不具备专用网络的虚拟网络集成。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控件不适用。

若要为 runbook 获取进一步隔离，可以使用在 Azure 虚拟机上运行的混合 Runbook 辅助角色。 创建 Azure 虚拟机时，必须创建虚拟网络（VNet）或使用现有 VNet，并使用子网配置 Vm。 确保所有部署的子网均具有一个网络安全组，该安全组与特定于应用程序的受信任端口和源的网络访问控制一起应用。 有关特定于服务的要求，请参阅该服务的安全建议。

或者，如果你有特定的要求，还可以使用 Azure 防火墙来满足此要求。

* [Azure 中的虚拟网络和虚拟机](../virtual-machines/windows/network-overview.md)

* [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

* [Runbook 执行环境](./automation-runbook-execution.md#runbook-execution-environment)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指南**： Azure Automation 目前没有针对混合 Runbook 辅助角色支持的专用网络的虚拟网络集成。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控件不适用。

如果你使用的是 Azure 虚拟机支持的混合 Runbook 辅助角色，请确保使用网络安全组（NSG）启用了包含这些辅助角色的子网，并将流日志配置为将日志转发到存储帐户以进行流量审核。 你还可以将 NSG 流日志转发到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析具有以下优势：能够直观显示网络活动、识别热点、识别安全威胁、了解流量流模式以及查明网络配置错误。

尽管 NSG 规则和用户定义的路由不适用于专用终结点，但仍支持出站连接的 NSG 流日志和监视信息，并且可以使用它们。

* [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**： Azure Automation 目前尚不支持混合 Runbook 辅助角色的虚拟网络集成。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控件不适用。

如果你使用的是 Azure 虚拟机支持的混合 Runbook 辅助角色，请在托管混合 Runbook 辅助角色的虚拟网络上启用分布式拒绝服务（DDoS）标准保护，以防范 DDoS 攻击。 使用 Azure 安全中心的威胁智能，可以拒绝与已知的恶意 IP 地址的通信。 在启用了威胁情报的每个虚拟网络段上配置 Azure 防火墙，并将配置为针对恶意网络流量进行**警报和拒绝**。

可以使用 Azure 安全中心的实时网络访问权限，在有限的时间段内将 Windows 虚拟机的公开权限限制为已批准的 IP 地址。 同时，使用 Azure 安全中心自适应网络强化建议进行 NSG 配置，以根据实际流量和威胁智能限制端口和源 Ip。

* [如何配置 DDoS 保护](../virtual-network/manage-ddos-protection.md)

* [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

* [了解 Azure 安全中心集成的威胁情报](../security-center/threat-protection.md)

* [了解 Azure 安全中心自适应网络强化](../security-center/security-center-adaptive-network-hardening.md)

* [了解 Azure 安全中心实时网络访问控制](../security-center/security-center-just-in-time.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**： Azure Automation 当前没有针对混合 Runbook 辅助角色支持的专用网络的虚拟网络集成，如果你使用的是现成的服务，而没有混合辅助角色，则此控制将不适用。

如果你使用的是 Azure 虚拟机支持的混合 Runbook 辅助角色，则可以将 NSG 流日志记录到存储帐户，以便为作为 Runbook 辅助角色的 Azure 虚拟机生成流记录。 调查异常活动时，可以启用网络观察程序数据包捕获，以便查看网络流量的异常和意外活动。

* [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何启用网络观察程序](../network-watcher/network-watcher-create.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**： Azure Automation 目前尚不支持混合 Runbook 辅助角色的虚拟网络集成。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控件不适用。

如果使用在 Azure 虚拟机上托管的混合 Runbook 辅助角色，则可以组合网络观察程序和开源 ID 工具提供的数据包捕获，为这些工作进程的各种威胁执行网络入侵检测。 此外，你可以根据需要将 Azure 防火墙部署到虚拟网络段，并且启用了威胁情报，并将其配置为针对恶意网络流量 "警报和拒绝"。

* [使用网络观察程序和开源工具执行网络入侵检测](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

* [如何配置 Azure 防火墙警报](../firewall/threat-intel.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络服务标记定义在 azure 中配置的网络安全组或 azure 防火墙上的网络访问控制，这需要访问自动化资源。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应 "源" 或 "目标" 字段中指定服务标记名称（例如，GuestAndHybridManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

* [了解和使用服务标记](../virtual-network/service-tags-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现 azure 自动化与 azure 策略一起使用的网络资源的标准安全配置。

你还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、RBAC 控件和策略）来简化大规模的 Azure 部署。 可将蓝图应用到新的订阅，并通过版本控制来微调控制措施和管理。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [用于网络的 Azure Policy 示例](/azure/governance/policy/samples/#network)

* [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对 nsg 以及与网络安全和流量流相关的其他资源使用标记。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure CLI 根据资源的标记查找资源或对其执行操作。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视资源配置，并检测对网络资源所做的更改。 在 Azure Monitor 中创建当关键资源发生更改时触发的警报。

* [如何查看和检索 Azure 活动日志事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护 Azure 资源的时间源。 但是，你可以选择管理在 Windows 虚拟机上运行的任何混合 Runbook 辅助角色的时间同步设置。

* [如何配置 Azure 计算资源的时间同步](../virtual-machines/windows/time-sync.md)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：将日志数据转发到 Azure Monitor 日志以聚合由 Azure Automation 资源生成的安全数据。 在 Azure Monitor 中，使用日志查询搜索和执行分析，并使用 Azure 存储帐户进行长期/存档存储。 Azure 自动化可以将 runbook 作业状态、作业流、自动化状态配置数据、更新管理和更改跟踪或清单日志发送到 Log Analytics 工作区。 此信息可从 Azure 门户、Azure PowerShell 和 Azure Monitor 日志 API 中查看，这使你可以执行简单调查。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

* [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

* [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [将 Azure 自动化作业数据转发到 Azure Monitor 日志](./automation-manage-send-joblogs-log-analytics.md)

* [将 DSC 与 Azure Monitor 日志集成](./automation-dsc-diagnostics.md)

* [链接的 Log Analytics 工作区支持的区域](./how-to/region-mappings.md)

* [查询更新管理日志](./update-management/update-mgmt-query-logs.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：启用 Azure Monitor 以访问审核和活动日志，包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

* [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

* [查看和检索 Azure 活动日志事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：将 Azure 自动化与多租户 runbook worker 一起使用时，此控件不适用，并且平台将处理底层虚拟机。

使用混合 Runbook 辅助角色功能时，Azure 安全中心为 Windows 虚拟机提供安全事件日志监视。 如果你的组织想要保留安全事件日志数据，则可以将其存储在数据收集层中，此时可在 Log Analytics 中查询该数据。 下面的链接详细说明了不同的层：最小、最常见和全部。

* [在 Azure 安全中心内配置数据收集层](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的符合性法规设置 Log Analytics 工作区保持期。 使用 Azure 存储帐户进行长期/存档存储。

* [更改 Log Analytics 中的数据保留期](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [自动化帐户的数据保留详细信息](./automation-managing-data.md#data-retention)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指南**：分析和监视日志中的异常行为，并定期检查结果。 使用 Azure Monitor 日志查询来查看日志，并对日志数据执行查询。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

* [了解 Azure Monitor 中的日志查询](../azure-monitor/log-query/get-started-portal.md)

* [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/log-query/get-started-queries.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：将 Azure 安全中心与 Azure Monitor 配合使用来监视和警报安全日志和事件中的异常活动。

或者，你可以将和机载数据启用到 Azure Sentinel。

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

* [如何在 Azure 安全中心管理警报](../security-center/security-center-managing-and-responding-alerts.md)

* [如何发出 Azure Monitor 日志数据的警报](../azure-monitor/learn/tutorial-response.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：将 Azure 自动化与多租户 runbook worker 一起使用时，此控制不适用，并且平台将处理底层虚拟机。

但是，在使用混合 Runbook 辅助角色功能时，可以将 Microsoft 反恶意软件用于 Azure 云服务和虚拟机。 将虚拟机配置为将事件记录到 Azure 存储帐户。 配置 Log Analytics 工作区以从存储帐户中引入事件，并在适当的位置创建警报。 遵循 Azure 安全中心中的建议： "计算 &amp; 应用"。

* [如何为云服务和虚拟机配置 Microsoft 反恶意软件](../security/fundamentals/antimalware.md)

* [如何为虚拟机启用来宾级别监视](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：根据组织的需要，从 Azure MARKETPLACE 实现 DNS 日志记录解决方案的第三方解决方案。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：将 Azure 自动化与多租户 runbook worker 一起使用时，此控件不适用，并且平台将处理底层虚拟机。

但是，在使用混合 Runbook 辅助角色功能时，Azure 安全中心为 Azure 虚拟机提供安全事件日志监视。 安全中心在所有支持的 Azure Vm 上预配 Log Analytics 代理，以及在启用自动预配时创建的任何新的代理。 或者，你可以手动安装该代理。 代理启用进程创建事件4688和事件4688内的命令行字段。 虚拟机上创建的新进程由事件日志记录，并由安全中心的检测服务监视。

* [Azure 安全中心中的数据收集](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：使用 Azure Active Directory 内置管理员角色，这些角色可以显式分配并可以进行查询。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。 每次使用 runbook 的自动化帐户运行方式帐户时，请确保还会在你的清单中跟踪这些服务主体，因为它们常常需要提升的权限。 删除任何未使用的运行方式帐户，以最大程度地降低公开攻击面。

* [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [删除运行方式帐户或经典运行方式帐户](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [管理 Azure 自动化运行方式帐户](./manage-runas-account.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**： Azure Automation 帐户没有默认密码的概念。 客户负责第三方应用程序和 marketplace 服务，该服务可以使用在服务上或其混合 Runbook 辅助角色上运行的默认密码。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。 每次使用 runbook 的自动化帐户运行方式帐户时，请确保还会在你的清单中跟踪这些服务主体，因为它们常常需要提升的权限。 将这些标识的范围限定为你的 runbook 成功执行其自动过程所需的最低特权权限。 删除任何未使用的运行方式帐户，以最大程度地降低公开攻击面。

还可以通过使用 Microsoft 服务的 Azure AD Privileged Identity Management 特权角色和 Azure 资源管理器来启用实时/足够访问权限。

* [详细了解 Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

* [删除运行方式帐户或经典运行方式帐户](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [管理 Azure 自动化运行方式帐户](./manage-runas-account.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指南**：尽可能将 SSO 与 Azure Active Directory 结合使用，而不是为每个服务配置单独的单独凭据。 请使用 Azure 安全中心标识和访问管理建议。

* [单一登录到 Azure Active Directory 中的应用程序](../active-directory/manage-apps/what-is-single-sign-on.md)

* [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

* [使用 Azure AD 向 Azure 进行身份验证](./automation-use-azure-ad.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure AD 多重身份验证（MFA）并遵循 Azure 安全中心的标识和访问管理建议。

* [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-for-all-administrative-tasks"></a>3.6：将专用计算机用于所有管理任务

**指南**：将 paw 用于多重身份验证，并将其配置为在生产环境中登录和配置 Azure 自动化帐户资源。

* [了解特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指南**：利用 Azure AD 风险检测来查看警报和报告有风险的用户行为。 客户可以选择将 Azure 安全中心风险检测警报转发到 Azure Monitor 并使用操作组配置自定义警报/通知。

* [了解 Azure 安全中心风险检测（可疑活动）](../active-directory/identity-protection/overview-identity-protection.md)

* [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [如何为自定义警报和通知配置操作组](../azure-monitor/platform/action-groups.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：建议使用条件性访问命名位置，以仅允许来自 IP 地址范围或国家/地区的特定逻辑分组的访问。

* [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：使用 Azure AD 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。 如果使用混合 Runbook 辅助角色，则可以使用托管标识（而不是运行方式帐户）来启用更无缝的安全权限。

* [如何创建和配置 Azure AD 实例](../active-directory-domain-services/tutorial-create-instance.md)

* [将 runbook 身份验证用于托管标识](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：Azure AD 提供有助于发现陈旧帐户的日志。 此外，使用 Azure 标识访问评审还可有效管理组成员身份、对企业应用程序的访问权限以及角色分配。 可以定期查看用户访问权限，以确保只有正确的用户才能继续访问。 每次使用 runbook 的自动化帐户运行方式帐户时，还应确保在清单中跟踪这些服务主体，因为他们经常需要提升权限。 删除任何未使用的运行方式帐户，以最大程度地降低公开攻击面。

* [了解 Azure AD 报告](../active-directory/reports-monitoring/index.yml)

* [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

* [删除运行方式帐户或经典运行方式帐户](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [管理 Azure 自动化运行方式帐户](./manage-runas-account.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：你有权访问 Azure AD 登录活动、审核和风险事件日志源，以便与任何 SIEM/监视工具集成。

可以通过为 Azure Active Directory 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 你可以在 Log Analytics 工作区中配置所需的警报。

* [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure AD 风险和标识保护功能来配置对检测到的与网络资源的用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 中以便进一步调查。

* [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

* [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：对于 Azure Automation 帐户，Microsoft 支持人员可以在开放支持案例中访问平台资源元数据，而无需使用其他工具。

但是，当使用由 Azure 虚拟机支持的混合 Runbook 辅助角色，而第三方需要访问客户数据（例如在支持请求期间）时，请使用 Azure 虚拟机客户密码箱（预览版）来查看和批准或拒绝客户数据访问请求。

* [了解客户密码箱](../security/fundamentals/customer-lockbox-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记帮助跟踪存储或处理敏感信息的 Azure 自动化资源。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实现单独的订阅和/或管理组。 使用独立的自动化帐户资源隔离环境。 混合 Runbook 辅助角色等资源应该由虚拟网络/子网分开、正确标记并在网络安全组（NSG）或 Azure 防火墙内进行保护。 对于存储或处理敏感数据的虚拟机，请在不使用策略和过程时将其关闭。

* [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../governance/management-groups/create.md)

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

* [如何配置警报或警报，以及如何拒绝 Azure 防火墙](../firewall/threat-intel.md)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：使用混合 Runbook 辅助角色功能时，请利用网络外围网络外围的 Azure Marketplace 中的第三方解决方案来监视敏感信息的未授权传输并在警报信息安全专业人员时阻止此类传输。

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，并全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

* [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：加密传输中的所有敏感信息。 确保连接到 Azure 虚拟网络中的 Azure 资源的任何客户端都能够协商 TLS 1.2 或更高版本。 Azure 自动化完全支持并强制实施传输层（TLS）1.2 以及所有客户端调用或更高版本的所有外部 HTPS 终结点（通过 webhook、DSC 节点、混合 runbook 辅助角色）。

请按照 Azure 安全中心的建议，了解静态加密和传输中的加密（如果适用）。

* [了解 Azure 传输中的加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Azure 自动化 TLS 1.2 强制](https://azure.microsoft.com/updates/azure-automation-tls12-enforcement/)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：使用第三方活动发现工具来确定由组织的技术系统存储、处理或传输的所有敏感信息，包括位于现场或远程服务提供商的信息，以及更新组织的敏感信息清单。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**：使用 Azure AD RBAC 通过内置角色定义来控制对 Azure 自动化资源的访问，并按照权限最少的访问模型或 "刚好足够" 的访问模型为访问自动化资源的用户分配访问权限。 使用混合 Runbook 辅助角色时，请利用这些虚拟机的托管标识避免使用服务主体，同时同时使用多租户或混合 Runbook 辅助角色时，请确保对 Runbook worker 的标识应用适当的范围 RBAC 权限。

* [如何在 Azure 中配置 RBAC](../role-based-access-control/role-assignments-portal.md)

* [混合 Runbook 辅助角色的 Runbook 权限](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

* [管理角色权限和安全性](./automation-role-based-access-control.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**： Azure Automation 目前不公开基础多租户 runbook 辅助角色的虚拟机，这由平台处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控件不适用。

如果你使用的是 Azure 虚拟机支持的混合 Runbook 辅助角色，则需要使用第三方基于主机的数据丢失防护解决方案来对托管的混合 Runbook 辅助角色虚拟机强制实施访问控制。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：通过 Azure 自动化使用客户托管的密钥。 Azure 自动化支持使用客户管理的密钥来加密所有使用的安全资产，例如凭据、证书、连接和加密的变量。 将加密变量与 runbook 结合使用，以防止意外的公开。

使用混合 Runbook 辅助角色时，虚拟机上的虚拟磁盘使用服务器端加密或 Azure 磁盘加密（ADE）加密。 Azure 磁盘加密利用 Windows 的 BitLocker 功能，通过来宾 VM 中的客户托管密钥来加密托管磁盘。 使用客户托管密钥的服务器端加密改进了 ADE，它通过加密存储服务中的数据使你可以为 VM 使用任何 OS 类型和映像。

* [Azure 托管磁盘的服务器端加密](../virtual-machines/windows/disk-encryption.md)

* [适用于 Windows VM 的 Azure 磁盘加密](../virtual-machines/windows/disk-encryption-overview.md)

* [为自动化帐户使用客户管理的密钥](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Azure Automation 中的托管变量](./shared-resources/variables.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建关键 Azure 资源（如网络组件、Azure 自动化帐户和 runbook）发生更改的警报。

* [网络安全组的诊断日志记录](../private-link/private-link-overview.md#logging-and-monitoring)

* [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：按照 Azure 安全中心的建议执行 azure 资源上的漏洞评估

* [Azure 安全中心的安全建议](../security-center/security-center-recommendations.md)

* [安全中心推荐参考](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**： Azure Automation 目前不公开基础多租户 runbook 辅助角色的虚拟机，这由平台处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控件不适用。

如果使用 Azure 虚拟机支持的混合 Runbook 辅助角色，请使用 Azure 更新管理来管理虚拟机的更新和修补程序。 更新管理依赖于本地配置的更新存储库来修补受支持的 Windows 系统。 System Center Updates Publisher （Updates Publisher）等工具允许你将自定义更新发布到 Windows Server Update Services （WSUS）。 此方案允许更新管理将使用 Configuration Manager 作为其更新存储库的计算机与第三方软件配合使用。

* [Azure 中的更新管理](./update-management/update-mgmt-overview.md)

* [管理 Vm 的更新和修补程序](./update-management/update-mgmt-manage-updates-for-vm.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指南**： Azure Automation 目前不公开基础多租户 runbook 辅助角色的虚拟机，这由平台处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控件不适用。

如果你使用的是 Azure 虚拟机支持的混合 Runbook 辅助角色，则可以使用 Azure 更新管理来管理虚拟机的更新和修补程序。 更新管理依赖于本地配置的更新存储库来修补受支持的 Windows 系统。 System Center Updates Publisher （Updates Publisher）等工具允许你将自定义更新发布到 Windows Server Update Services （WSUS）。 此方案允许更新管理将使用 Configuration Manager 作为其更新存储库的计算机与第三方软件配合使用。

* [Azure 中的更新管理解决方案](./update-management/update-mgmt-overview.md)

* [管理 Azure VM 的更新和修补程序](./update-management/update-mgmt-manage-updates-for-vm.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**：按一致的间隔导出扫描结果，并比较结果以验证是否已修正了漏洞。 使用 Azure 安全中心建议的漏洞管理建议时，客户可能会透视到所选解决方案的门户以查看历史扫描数据。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**：使用 Azure 安全中心提供的默认风险等级（安全分数）来帮助确定发现的漏洞的修正。

* [了解 Azure 安全中心安全功能分数](../security-center/secure-score-security-controls.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可查询和发现订阅中的所有 Azure 自动化资源。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

* [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

* [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪 Azure 自动化资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。 删除任何未使用的运行方式帐户，以最大程度地降低公开攻击面。

* [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../governance/management-groups/create.md)

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [删除运行方式帐户或经典运行方式帐户](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [管理 Azure 自动化运行方式帐户](./manage-runas-account.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已获批 Azure 资源的清单

**指导**：你将需要根据组织需求，创建已获批 Azure 资源以及已获批用于计算资源的软件的清单。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：使用 Azure 策略对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

此外，还可使用 Azure 资源关系图查询/发现订阅中的资源。 这可以帮助实现基于高安全性的环境，如具有存储帐户的环境。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

* [Azure 自动化的 azure 策略示例内置功能](./policy-samples.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**： Azure 自动化产品/服务当前不公开底层多租户 runbook 辅助角色的虚拟机，这由平台处理。 如果在没有混合辅助角色的情况下使用现成的服务，则此控件不适用。 但是，可以安装、删除和管理 runbook，或者可以通过门户或 cmdlet 访问 runbook 的 Python 模块。 应删除或更新 runbook 的未批准或旧模块。

如果你使用的是由 Azure 虚拟机支持的混合 Runbook 辅助角色，则 Azure Automation 会在部署、操作和停止工作负荷和资源的过程中提供完全控制。 利用 Azure 虚拟机库存自动收集有关虚拟机上所有软件的信息。 可从 Azure 门户获取软件名称、版本、发布者和刷新时间。 若要获取安装日期和其他信息的访问权限，客户需要启用来宾级别诊断，并将 Windows 事件日志引入 Log Analytics 工作区。

* [Azure 自动化简介](./automation-intro.md)

* [如何启用 Azure VM 清单](./automation-tutorial-installed-software.md)

* [在 Azure 自动化中管理模块](./shared-resources/modules.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：客户可以根据客户公司指南的要求，阻止创建或使用 Azure 策略的资源。 你可以实现自己的过程来删除未经授权的资源。 在 Azure 自动化产品中，可以安装、删除和管理 runbook，或者可以通过门户或 cmdlet 访问 runbook 的 Python 模块。 应删除或更新 runbook 的未批准或旧模块。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [在 Azure 自动化中管理模块](./shared-resources/modules.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：使用混合 Runbook 辅助角色功能时，可以使用 Azure 安全中心的自适应应用程序控制，以确保仅执行授权的软件，并且阻止所有未经授权的软件在 Azure 虚拟机上执行。

* [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：使用 Azure 策略对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/index.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指南**：使用混合 Runbook 辅助角色功能时，可以将 Azure 安全中心自适应应用程序控制功能与混合辅助角色虚拟机一起使用。

自适应应用程序控制是 Azure 安全中心提供的智能、自动化、端到端的解决方案，有助于控制可在 Azure 和非 Azure 计算机（Windows 和 Linux）上运行的应用程序。 如果这不能满足组织的要求，请实现第三方解决方案。

* [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：使用 azure 条件性访问策略，通过为来自不安全或未批准的位置或设备的 "Microsoft Azure 管理" 应用配置 "阻止访问"，限制用户与 Azure 资源管理器的交互能力。

* [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指南**：使用混合 Runbook 辅助角色功能时，可以使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。 还可以利用 Azure 安全中心自适应应用程序控制来确保仅执行已授权软件，并阻止所有未授权软件在 Azure 虚拟机上执行。

* [如何在 Windows 环境中控制 PowerShell 脚本的执行](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：你的 azure 环境中部署的高风险应用程序可以通过使用虚拟网络、子网、订阅和管理组等构造的单独网络和资源容器来隔离，可以使用 Azure 防火墙、Web 应用程序防火墙（WAF）或网络安全组（NSG）对其进行充分的保护。

* [Azure 中的虚拟网络和虚拟机](../virtual-machines/windows/network-overview.md)

* [Azure 防火墙概述](../firewall/overview.md)

* [Azure Web 应用程序防火墙概述](../web-application-firewall/overview.md)

* [网络安全组](../virtual-network/security-overview.md)

* [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)

* [使用 Azure 管理组来组织资源](../governance/management-groups/overview.md)

* [订阅决策指南](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用 azure 策略别名创建自定义策略，以审核或强制执行 Azure 自动化和相关资源的配置。 你还可以使用内置的 Azure Policy 定义。

此外，Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足/超过组织的安全要求。

还可以使用来自 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

* [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

* [Azure 自动化的 azure 策略示例内置功能](./policy-samples.md)

* [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

* [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**： Azure Automation 目前不公开基础多租户 runbook 辅助角色的虚拟机或操作系统。 这由平台处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控件不适用。

使用混合 Runbook 辅助角色功能时，使用 Azure 安全中心建议 [修正虚拟机上的安全配置漏洞] 来维护虚拟机上的安全配置。

* [如何监视 Azure 安全中心建议](../security-center/security-center-recommendations.md)

* [如何修正 Azure 安全中心建议](../security-center/security-center-remediate-recommendations.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 azure 资源管理器模板和 azure 策略安全配置与 azure 自动化关联的 azure 资源。 Azure 资源管理器模板是用于部署 Azure 资源的基于 JSON 的文件，任何自定义模板都需要在代码存储库中安全地进行存储和维护。 使用源代码管理集成功能，可让你的自动化帐户中的 runbook 在源代码管理存储库中使用脚本来保持最新状态。 使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

* [使用源代码管理集成](./source-control-integration.md)

* [有关创建 Azure 资源管理器模板的信息](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

* [使用 Azure 资源管理器模板部署自动化帐户](./quickstart-create-automation-account-template.md#deploy-the-template)

* [Azure 自动化的 azure 策略示例内置功能](./policy-samples.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**： Azure Automation 目前不公开基础多租户 runbook 辅助角色的虚拟机或 OS，此操作由平台处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控件不适用。

使用混合 Runbook 辅助角色功能时，有几个选项可用于为部署 Azure 虚拟机维护安全配置：

- Azure 资源管理器模板：这些是基于 JSON 的文件，用于从 Azure 门户部署 VM，需要维护自定义模板。 Microsoft 对基本模板执行维护。
- 自定义虚拟硬盘（VHD）：在某些情况下，可能需要使用自定义 VHD 文件，如处理无法通过其他方式管理的复杂环境时。
- Azure 自动化状态配置：部署基本操作系统后，可使用此功能对设置进行更细致的控制，并通过自动化框架进行强制。

在大多数情况下，与 Azure 自动化状态配置结合的 Microsoft 基本 VM 模板可以帮助满足和维护安全要求。

* [有关如何下载 VM 模板的信息](../virtual-machines/windows/download-template.md)

* [有关创建 ARM 模板的信息](../virtual-machines/windows/ps-template.md)

* [如何将自定义 VM VHD 上传到 Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：使用 Azure DevOps 来安全地存储和管理代码，如自定义 azure 策略、Azure 资源管理器模板和所需的状态配置脚本。 若要访问在 Azure DevOps 中管理的资源，可以授予或拒绝特定用户、内置安全组或在 Azure Active Directory 中定义的、与 Azure DevOps 集成或 Active Directory （如果与 TFS 集成）的权限。 使用源代码管理集成功能，可让你的自动化帐户中的 runbook 在源代码管理存储库中使用脚本来保持最新状态。

* [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

* [使用源代码管理集成](./source-control-integration.md)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**： Azure Automation 目前不公开基础多租户 runbook 辅助角色的虚拟机或 OS，此操作由平台处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控件不适用。

使用混合 Runbook 辅助角色功能时，请确保正确地限制对存储帐户中的自定义 OS 映像的访问，以便只有经过授权的用户才能访问该映像。

* [了解 Azure 中的 RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [如何在 Azure 中配置 RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure Policy 为 Azure 资源定义和实施标准安全配置。 使用 Azure Policy 别名创建自定义策略，审核或强制实施 Azure 资源的网络配置。 还可以使用与特定资源相关的内置策略定义。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用别名](../governance/policy/concepts/definition-structure.md#aliases)

* [Azure 自动化的 azure 策略示例内置功能](./policy-samples.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指南**： Azure Automation 目前不公开基础多租户 runbook 辅助角色的虚拟机或 OS，此操作由平台处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控件不适用。

使用混合 Runbook 辅助角色功能时，请在 Runbook worker 上使用 Azure 自动化状态配置，该服务是适用于任何云或本地数据中心中所需状态配置（DSC）节点的配置管理服务。 它可让你从中心的安全位置快速轻松地扩展到数千台计算机。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。

* [加入 Azure Automation State Configuration 管理的计算机](./automation-dsc-onboarding.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：使用 azure 策略来警报和审核 azure 资源配置，策略可用于检测未配置专用终结点的某些资源。

使用混合 Runbook 辅助角色功能时，请利用 Azure 安全中心对 Azure 虚拟机执行基线扫描。 用于自动配置的其他方法包括 Azure 自动化状态配置。

* [如何在 Azure 安全中心修正建议](../security-center/security-center-remediate-recommendations.md)

* [Azure Automation State Configuration 入门](./automation-dsc-getting-started.md)

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Azure 自动化的 azure 策略示例内置功能](./policy-samples.md)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**： Azure 自动化产品/服务当前不公开基础多租户 runbook 辅助角色的虚拟机或 OS，这由平台处理。 如果在没有混合辅助角色的情况下使用现成的服务，则此控件不适用。

使用混合 Runbook 辅助角色功能时，请在任何云或本地数据中心中，为所需状态配置（DSC）节点的 Runbook worker 使用 Azure 自动化状态配置。 它可让你从中心的安全位置快速轻松地扩展到数千台计算机。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。

* [加入 Azure Automation State Configuration 管理的计算机](./automation-dsc-onboarding.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：将托管服务标识与 Azure Key Vault 结合使用，以便简化和保护云应用程序的机密管理。

* [为自动化帐户使用客户管理的密钥](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [将 runbook 身份验证用于托管标识](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

* [如何创建 Key Vault](../key-vault/secrets/quick-create-portal.md)

* [如何使用托管标识提供 Key Vault 身份验证](../key-vault/general/managed-identity.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：使用托管标识在 Azure AD 中为 Azure 服务提供自动托管标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

* [如何配置托管标识](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [为自动化帐户使用客户管理的密钥](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**： Azure 自动化产品/服务当前不公开基础多租户 runbook 辅助角色的虚拟机或 OS，这由平台处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控件不适用。

使用混合 Runbook 辅助角色功能时，请使用适用于 Azure Windows 虚拟机的 Microsoft 反恶意软件持续监视和保护 Runbook 辅助角色资源。

* [如何为云服务和虚拟机配置 Microsoft 反恶意软件](../security/fundamentals/antimalware.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：不适用;Azure 自动化即服务不存储文件。 支持 Azure 服务（例如 Azure 自动化）的基础主机上启用了 Microsoft 反恶意软件，但它不会在你的内容上运行。

* [了解适用于 Azure 云服务和虚拟机的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**： Azure Automation 目前不公开基础多租户 runbook 辅助角色的虚拟机或 OS，此操作由平台处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控件不适用。

使用混合 Runbook 辅助角色功能时，请使用适用于 Azure 的 Microsoft 反恶意软件自动将最新的签名、平台和引擎更新默认安装到 Runbook Worker。 请遵循 Azure 安全中心中的建议：“计算和应用”用于确保所有终结点都具有最新的签名。 Windows OS 可以通过额外的安全性进行进一步保护，以通过与 Azure 安全中心集成的 Microsoft Defender 高级威胁防护服务来限制受病毒或恶意软件攻击的风险。

* [如何为 Azure 云服务和虚拟机部署 Microsoft Antimalware](../security/fundamentals/antimalware.md)

* [Microsoft Defender 高级威胁防护](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：使用 azure 资源管理器部署 azure 自动化帐户和相关资源。 Azure 资源管理器提供导出模板的功能，可以将这些模板用作还原 Azure Automation 帐户和相关资源的备份。 使用 Azure 自动化定期调用 Azure 资源管理器模板导出 API。

使用源代码管理集成功能，可让你的自动化帐户中的 runbook 在源代码管理存储库中使用脚本来保持最新状态。

* [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)

* [Azure 自动化资源的 azure 资源管理器模板参考](/azure/templates/microsoft.automation/allversions)

* [使用 Azure 资源管理器模板创建自动化帐户](./quickstart-create-automation-account-template.md)

* [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

* [资源组-导出模板](/rest/api/resources/resourcegroups/exporttemplate)

* [Azure Automation 简介](./automation-intro.md)

* [如何在 Azure 中备份密钥保管库密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [为自动化帐户使用客户管理的密钥](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [使用源代码管理集成](./source-control-integration.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：使用 azure 资源管理器部署 azure 自动化帐户和相关资源。 Azure 资源管理器提供导出模板的功能，可以将这些模板用作还原 Azure Automation 帐户和相关资源的备份。 使用 Azure 自动化定期调用 Azure 资源管理器模板导出 API。 在 Azure Key Vault 中备份客户托管的密钥。 可以使用 Azure 门户或 PowerShell 将 runbook 导出到脚本文件。

* [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)

* [Azure 自动化资源的 azure 资源管理器模板参考](/azure/templates/microsoft.automation/allversions)

* [使用 Azure 资源管理器模板创建自动化帐户](./quickstart-create-automation-account-template.md)

* [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

* [资源组-导出模板](/rest/api/resources/resourcegroups/exporttemplate)

* [Azure Automation 简介](./automation-intro.md)

* [如何在 Azure 中备份密钥保管库密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [为自动化帐户使用客户管理的密钥](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [自动化帐户的 Azure 数据备份](./automation-managing-data.md#data-backup)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：如果需要，请确保定期定期将 Azure 资源管理器模板部署到隔离的订阅。 测试已备份客户托管密钥的还原。

* [使用 ARM 模板和 Azure 门户部署资源](../azure-resource-manager/templates/deploy-portal.md)

* [如何在 Azure 中还原密钥保管库密钥](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [为自动化帐户使用客户管理的密钥](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：使用 azure DevOps 安全地存储和管理你的代码，例如 azure 资源管理器模板。 若要保护在 Azure DevOps 中管理的资源，可以授予或拒绝特定用户、内置安全组或在 Azure Active Directory 中定义的、与 Azure DevOps 集成或 Active Directory （如果与 TFS 集成）的权限。

使用源代码管理集成功能，可让你的自动化帐户中的 runbook 在源代码管理存储库中使用脚本来保持最新状态。

* [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

* [使用源代码管理集成](./source-control-integration.md)

**Azure 安全中心监视**：不适用

责任：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

* [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心对用于发出警报的调查结果或分析的置信度，以及导致警报的活动背后存在恶意意图的可信度。

此外，使用标记清楚地标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

* [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

* [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

* [NIST 发布 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

* [如何配置连续导出](../security-center/continuous-export.md)

* [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，以保护 Azure 资源。

* [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指南**：遵循 Microsoft 订婚规则确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

* [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)
