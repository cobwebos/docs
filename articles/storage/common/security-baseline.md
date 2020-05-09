---
title: Azure 存储的 azure 安全基线
description: Azure 存储的 azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 92f2102d8d6353da2002a4ee9d5bfbe57a774e10
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792065"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Azure 存储的 azure 安全基线

Azure 存储空间的 Azure 安全基线包含的建议可帮助你提高部署的安全状况。

此服务的基线取自[Azure 安全基准1.0 版](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何在 Azure 上利用我们的最佳实践指南来保护云解决方案的建议。

有关详细信息，请参阅[Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

*有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络中使用网络安全组或 Azure 防火墙保护资源

**指南**：通过将访问权限限制为特定公共 IP 地址范围、选择 azure 上的虚拟网络（vnet）或特定的 Azure 资源，来配置存储帐户的防火墙。 你还可以配置专用终结点，以便从你的企业到存储服务的流量仅通过专用网络进行传输。

注意：经典存储帐户不支持防火墙和虚拟网络。

- [如何配置 Azure 存储防火墙](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [如何配置 Azure 存储的专用终结点](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1.2：监视和记录 Vnet、子网和 NIC 配置与流量

**指南**： Azure 存储提供分层的安全模型。 可将存储帐户的访问权限限制给源自指定的 IP 地址、IP 范围，或 Azure 虚拟网络 (VNet) 中某个子网列表的请求。 可以使用 Azure 安全中心并按照网络保护建议来帮助保护 Azure 中的网络资源。 此外，为通过存储帐户防火墙为存储帐户配置的虚拟网络/子网启用 NSG 流日志，并将日志发送到存储帐户以进行流量审核。 

请注意，如果你有附加到存储帐户的专用终结点，则无法为子网配置网络安全组（NSG）规则。 

- [配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [了解 Azure 安全中心提供的网络安全](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [了解 Azure 存储的专用终结点](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：不适用；建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知的恶意 IP 地址的通信

**指南**：对 Azure 存储帐户启用高级威胁防护。 适用于 Azure 存储的高级威胁防护提供额外的安全智能层，用于检测访问或利用存储帐户的异常和潜在有害尝试。 Azure 安全中心集成警报基于与已成功解析的 IP 地址关联的网络通信的活动，无论该 IP 地址是已知的危险 IP 地址（例如，已知的 cryptominer），还是以前无法识别为危险的 IP 地址。 当发生异常时，将触发安全警报。 

- [如何启用高级威胁防护](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

- [了解 Azure 安全中心集成威胁情报](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指南**：网络观察程序数据包捕获允许您创建捕获会话以跟踪存储帐户和虚拟机之间的流量。 为捕获会话提供了筛选器以确保仅捕获所需的流量。 数据包捕获有助于以主动和被动方式诊断网络异常。 其他用途包括收集网络统计信息，获得网络入侵信息，调试客户端与服务器之间的通信，等等。 远程触发数据包捕获能够减轻在所需虚拟机上手动运行数据包捕获的负担，并节省宝贵的时间。 

- [在门户中使用 Azure 网络观察程序管理数据包捕获](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-manage-portal)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6：部署基于网络的入侵检测/入侵防护系统

**指南**： Azure 存储的高级威胁防护提供额外的安全智能层，用于检测访问或利用存储帐户的异常和潜在有害尝试。 当发生异常时，将触发安全警报。 这些安全警报与 Azure 安全中心集成，还会通过电子邮件发送给订阅管理员，并详细介绍了可疑活动以及如何调查和修正威胁的建议。 

- [配置 Azure 存储的高级威胁防护](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7：管理 web 应用程序的流量

**指导**：不适用；建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：对于需要访问你的存储帐户的虚拟网络中的资源，请使用配置的虚拟网络的虚拟网络服务标记来定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应 "源" 或 "目标" 字段中指定服务标记名称（例如 "存储"），可以允许或拒绝相应服务的流量。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。 

如果需要将网络访问的作用域限定为特定的存储帐户，请使用虚拟网络服务终结点策略。

- [有关使用服务标记的详细信息](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [有关 Azure 存储的虚拟网络服务终结点策略的详细信息](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

**Azure 安全中心监视**：目前不可用

**责任**：服务

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现与 azure 策略关联的 Azure 存储帐户的网络资源的标准安全配置。 使用 "Microsoft 存储" 和 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行存储帐户资源的网络配置。 

你还可以使用与存储帐户相关的内置策略定义，例如：存储帐户应使用虚拟网络服务终结点 

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [适用于存储的 Azure 策略示例](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage)

- [用于网络的 Azure 策略示例](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [如何创建 Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：记录流量配置规则

**指南**：对网络安全组（NSG）和与网络安全和通信流相关的其他资源使用标记。 对于单独的 NSG 规则，请使用 "说明" 字段来指定允许进出网络流量的任何规则的业务需求和/或持续时间（等等）。 使用与标记相关的任何内置 Azure 策略定义（如 "需要标记和值"）来确保使用标记创建所有资源并通知现有未标记资源。 您可以使用 Azure PowerShell 或 Azure CLI 基于其标记对资源进行查找或执行操作。 

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何创建使用安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动工具来监视网络资源配置和检测更改

**指南**：使用 Azure 策略记录网络资源的配置更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。 

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何在 Azure Monitor 中创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：  日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-resource"></a>2.1：使用批准的时间同步资源

**指南**：不适用;Microsoft 维护 Azure 存储帐户的时间源。

**Azure 安全中心监视**：不适用

**责任**： Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：通过 Azure Monitor 引入日志来聚合由终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区查询和执行分析，并将 Azure 存储帐户用于长期/存档存储，还可以选择使用诸如不可变存储和强制保留保留的安全功能。

- [如何通过 Azure Monitor 收集平台日志和指标](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：启用 Azure 资源的审核日志记录

**指南**： Azure 存储分析提供 blob、队列和表的日志。 你可以使用 Azure 门户来配置为你的帐户记录了哪些日志。 

- [如何配置 Azure 存储帐户的监视](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-monitoring-for-a-storage-account)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留

**指南**：将安全事件日志存储在 Azure 存储帐户或 Log Analytics 工作区中时，可以根据组织的要求来设置保留策略。 

- [如何配置 Azure 存储帐户日志的保留策略](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

- [更改中的数据保持期 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指南**：若要查看 Azure 存储日志，可以使用常用的选项，例如通过 Log Analytics 产品的查询，以及直接查看日志文件的唯一选项。 在 Azure 存储中，日志存储在http://accountname.blob.core.windows.net/$logs 的 blob 中（默认情况下将隐藏日志记录文件夹），因此需要直接导航。 它不会显示在列表命令中） 

同时，为你的 Azure 存储帐户启用高级威胁防护。 适用于 Azure 存储的高级威胁防护提供额外的安全智能层，用于检测访问或利用存储帐户的异常和潜在有害尝试。 当发生异常时，将触发安全警报。 这些安全警报与 Azure 安全中心集成，还会通过电子邮件发送给订阅管理员，并详细介绍了可疑活动以及如何调查和修正威胁的建议。 

- [记录和检查数据](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored)

- [如何启用高级威胁防护](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：为异常活动启用警报

**指南**：在 Azure 安全中心中，为存储帐户启用高级威胁防护。 启用存储帐户的诊断设置，并将日志发送到 Log Analytics 工作区。 将 Log Analytics 工作区加入到 Azure Sentinel 上，因为它提供了一个安全业务流程自动化响应（之忠诚度）解决方案。 这样，就可以创建和使用行动手册（自动解决方案）来纠正安全问题。 

- [如何载入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [如何在 Azure 安全中心管理警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [如何在 log analytics 日志数据上发出警报](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [Azure 存储分析日志记录](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中反恶意软件日志记录

**指南**：使用 azure 安全中心并为 azure 存储启用威胁防护，使用哈希信誉分析来检测恶意软件上传到 azure 存储，并使用活动 Tor 出口节点（匿名代理）进行可疑访问。 

- [配置 Azure 存储的高级威胁防护](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**： Azure Monitor 中的 Azure DNS 分析（预览版）解决方案将深入了解有关安全性、性能和操作的 DNS 基础结构。 此功能当前不支持 Azure 存储帐户，但你可以使用第三方 dns 日志记录解决方案。 

- [使用 DNS Analytics 预览解决方案收集有关 DNS 基础结构的见解](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：不适用；基准适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

*有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**： Azure AD 具有内置角色，必须显式分配这些角色并可查询。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。 

- [如何使用 PowerShell 在 Azure AD 中获取目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中获取目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：更改默认密码（如果适用）

**指南**： Azure 存储帐户和 Azure Active Directory 具有默认密码或空白密码的概念。 Azure 存储实现了支持 Azure 基于角色的访问控制（RBAC）以及共享密钥和共享访问签名（SAS）的访问控制模型。 共享密钥和 SAS 身份验证的特性是没有与调用方关联的标识，因此不能执行安全主体基于权限的授权。 

- [授权访问 Azure 存储中的数据](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [了解 Azure 存储帐户的安全主体和访问控制](https://docs.microsoft.com/azure/storage/common/storage-introduction)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：创建有关使用可访问你的存储帐户的专用管理帐户的标准操作过程。 使用 Azure 安全中心的标识和访问管理来监视管理帐户的数量。 

你还可以通过使用 Azure AD Privileged Identity Management 适用于 Microsoft 服务和 Azure ARM 的特权角色来启用实时/刚好足够的访问。 

- [了解 Azure 安全中心的标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Privileged Identity Management 概述](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录（SSO）

**指南**：尽可能使用 Azure Active Directory SSO，而不是为每个服务配置单独的独立凭据。 使用 Azure 安全中心的标识和访问管理建议。 

- [了解 Azure AD 的 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [授权访问 Azure 存储中的数据](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [使用 Azure Active Directory 授予对 Blob 和队列的访问权限](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证。

**指南**：启用 Azure Active Directory 多重身份验证，并遵循 Azure 安全中心的标识和访问管理建议来帮助保护你的存储帐户资源。 

- [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [如何在 Azure 安全中心内监视标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：将 paw （特权访问工作站）用于 MFA，并将其配置为登录和配置存储帐户资源。 

- [了解特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：来自管理帐户的可疑活动的日志和警报

**指南**：将 Azure 安全中心风险检测警报发送到 Azure Monitor，并使用操作组配置自定义警报/通知。 为 Azure 存储帐户启用高级威胁防护，以生成可疑活动的警报。 此外，使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。 

- [如何设置 Azure 存储帐户的高级威胁防护](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

- [了解 Azure AD 风险检测](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

- [如何为自定义警报和通知配置操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问。 

- [如何在 Azure 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：使用 Azure Active Directory （Azure AD）作为中央身份验证和授权系统。 Azure AD 提供基于角色的访问控制 (RBAC)，用于精细地控制客户端对存储帐户中资源的访问权限。  尽可能使用 Azure AD 凭据作为最佳安全方案，而不是使用帐户密钥，这样会更容易受到威胁。 当应用程序设计要求使用共享访问签名来访问 Blob 存储时，请使用 Azure AD 凭据创建用户委派共享访问签名（SAS）（如果可能）以实现高级安全性。

- [如何创建和配置 Azure AD 实例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [使用 Azure 存储资源提供程序访问管理资源](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [如何在 Azure 门户中使用 RBAC 配置对 Azure Blob 和队列数据的访问权限](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [授权访问 Azure 存储中的数据](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [使用共享访问签名 (SAS) 授予对 Azure 存储资源的受限访问权限](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期查看和协调用户访问权限

**指南**：查看 Azure Active Directory 日志，以帮助发现可包括具有存储帐户管理角色的帐户的陈旧帐户。 此外，使用 Azure 标识访问评审来有效地管理组成员身份、访问可用于访问存储帐户资源和角色分配的企业应用程序。 应定期查看用户访问权限，以确保只有正确的用户才能继续访问。  

你还可以使用共享访问签名（SAS）提供对存储帐户中的资源的安全委派访问权限，而不会影响数据的安全性。 你可以控制客户端可以访问哪些资源、客户端对这些资源拥有哪些权限、SAS 的有效期，以及其他参数。

另外，请查看对容器和 blob 的匿名读取访问。 默认情况下，只有拥有相应权限的用户才能访问容器及其包含的任何 Blob。 你可以使用 Azure Monitor 来使用匿名身份验证条件来对存储帐户的匿名访问进行警报。

降低 unsuspected 用户帐户访问权限的一种有效方法是限制授予用户的访问权限的持续时间。 受时间限制的 SAS Uri 是自动终止用户对存储帐户的访问权限的一种有效方式。 此外，通常需要定期轮换存储帐户密钥，以确保通过存储帐户密钥进行意外访问的方式有限。

- [了解 Azure AD 报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [如何查看和更改 Azure 存储帐户级别的访问权限](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [使用共享访问签名 (SAS) 授予对 Azure 存储资源的受限访问权限](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

- [管理对容器和 blob 的匿名读取访问](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources)

- [监视 Azure 门户中的存储帐户](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

- [管理存储帐户访问密钥](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视器尝试访问停用的帐户

**指南**：使用存储分析来记录有关成功和失败的存储服务请求的详细信息。 所有日志以块 Blob 的形式存储在一个名为 $logs 的容器中，为存储帐户启用存储分析时会自动创建该容器。

为 Azure Active Directory 用户帐户创建诊断设置，将审核日志和登录日志发送到 Log Analytics 工作区。 你可以在 Log Analytics 工作区中配置所需的警报。 若要监视对 Azure 存储帐户的身份验证失败，可以创建警报，以便在达到存储资源指标的特定阈值时发出通知。 此外，使用 Azure Monitor 可以使用匿名身份验证条件来对存储帐户的匿名访问进行警报。

- [Azure 存储分析日志记录](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [如何将 Azure 活动日志集成到 Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [如何配置 Azure 存储帐户的指标警报](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帐户登录行为偏差的警报

**指南**：使用 Azure Active Directory 的风险和标识保护功能配置对检测到的与存储帐户资源相关的可疑操作的自动响应。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。 

- [如何查看 Azure AD 有风险的登录](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用 Identity Protection 风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [如何载入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持方案中为 Microsoft 提供对相关客户数据的访问权限

**指南**：在 Microsoft 需要访问客户数据的支持方案中，客户密码箱（存储帐户预览）提供了一个界面，供客户查看和批准或拒绝客户数据访问请求。 Microsoft 不需要，也不会请求访问存储帐户中存储的组织的机密。

- [了解客户密码箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

*有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记帮助跟踪存储或处理敏感信息的存储帐户资源。 

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：针对各个安全域（如环境、数据敏感度）使用单独的订阅、管理组和存储帐户实现隔离。  你可以根据所用网络的类型和子集限制存储帐户，以控制对你的应用程序和企业环境所需的存储帐户的访问级别。 配置网络规则后，只有通过指定的网络集请求数据的应用程序才能访问存储帐户。 可以通过 Azure AD RBAC 来控制对 Azure 存储的访问。 你还可以配置专用终结点以提高安全，因为虚拟网络与服务之间的流量通过 Microsoft 主干网络进行遍历，从而消除了公共 Internet 的泄露。 

- [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [虚拟网络服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止敏感信息的未授权传输。

**指南**：对于存储或处理敏感信息的存储帐户资源，请使用标记将资源标记为敏感。 若要通过渗透降低数据丢失的风险，请使用 Azure 防火墙限制 Azure 存储帐户的出站网络流量。 

此外，还可以使用虚拟网络服务终结点策略，通过服务终结点来筛选到 Azure 存储帐户的传出虚拟网络流量，并允许数据渗透仅用于特定的 Azure 存储帐户。

- [配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

- [Azure 存储的虚拟网络服务终结点策略](https://docs.microsoft.com/azure/private-link/create-private-endpoint-storage-portal)

- [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：你可以通过启用存储帐户所需的安全传输来强制使用 HTTPS。 启用此设置后，使用 HTTP 的连接将被拒绝。 此外，使用 Azure 安全中心和 Azure 策略为你的存储帐户强制实施安全传输。

- [如何在 Azure 存储中要求安全传输](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)

- [安全中心监视的 Azure 安全策略](https://docs.microsoft.com/azure/security-center/security-center-policy-definitions)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：对于 Azure 存储帐户和相关资源，数据标识功能尚不可用。 如果需要，请实现第三方解决方案以实现符合性。 

- [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指南**： Azure Active Directory （Azure AD）通过基于角色的访问控制（RBAC）授予对受保护资源的访问权限。 Azure 存储定义了一组内置的 RBAC 角色，它们包含用于访问 Blob 或队列数据的通用权限集。 

- [如何为 Azure 存储帐户分配 RBAC 角色](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal)

- [使用 Azure 存储资源提供程序访问管理资源](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [如何在 Azure 门户中使用 RBAC 配置对 Azure Blob 和队列数据的访问权限](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [如何创建和配置 AAD 实例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [授权访问 Azure 存储中的数据](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密静态敏感信息

**指南**：已为所有存储帐户启用 Azure 存储加密，因此无法将其禁用。 将数据保存到云时，Azure 存储会自动加密数据。 从 Azure 存储读取数据时，Azure 存储会在返回数据之前将其解密。 利用 Azure 存储加密，无需修改代码或将代码添加到任何应用程序，即可保护静态数据。 

- [了解静态 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建有关存储帐户资源发生更改的警报。 还可以启用 Azure 存储日志记录，以跟踪对 Azure 存储的每个请求的授权方式。 日志可指示请求是匿名提出的，还是使用 OAuth 2.0 令牌、共享密钥或共享访问签名 (SAS) 提出的。 此外，使用 Azure Monitor 可以使用匿名身份验证条件来对存储帐户的匿名访问进行警报。

- [如何为 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

- [Azure 存储分析日志记录](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [如何配置 Azure 存储帐户的指标警报](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

*有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：按照 Azure 安全中心的建议，持续审核和监视存储帐户的配置。 

- [安全建议-参考指南](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补程序管理解决方案

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自动第三方软件修补程序管理解决方案

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较后向后漏洞扫描

**指南**：不适用;Microsoft 对支持存储帐户的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评分过程来确定已发现漏洞的修正的优先级。

**指南**：使用 Azure 安全中心提供的默认风险等级（安全评分）。 

- [了解 Azure 安全中心安全分数](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="inventory-and-asset-management"></a>清单和资产管理

*有关详细信息，请参阅[安全控制：清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指南**：使用 Azure 资源关系图可查询和发现订阅中的所有资源（包括存储帐户）。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅以及订阅中的资源。 

- [如何通过 Azure Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [如何查看你的 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：将标记应用到存储帐户资源，使元数据以逻辑方式将它们组织到分类。 

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪存储帐户和相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。 

此外，请使用 Azure 存储的高级威胁防护来检测未经授权的 Azure 资源。 

- [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [配置 Azure 存储的高级威胁防护](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单。

**指南**：你将需要根据组织需求创建已批准的 Azure 资源的清单。 


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未经批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制： 

 - 不允许的资源类型 
 - 允许的资源类型 

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。 这可以帮助实现基于高安全性的环境，如具有存储帐户的环境。 

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何通过 Azure Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未经批准的软件应用程序

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未经批准的 Azure 资源和软件应用程序

**指南**：客户可以根据客户公司策略的要求，阻止创建或使用 Azure 策略的资源。 

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制： 

- 不允许的资源类型 
- 允许的资源类型 

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用 Azure 策略拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-implement-approved-application-list"></a>6.10：实施已批准的应用程序列表

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器进行交互的能力

**指南**：通过为 "Microsoft Azure 管理" 应用配置 "阻止访问"，使用 Azure 条件访问限制用户与 Azure 资源管理器的交互能力。 这可以防止在高安全性环境（如使用存储帐户的环境）中创建和更改资源。 

- [如何配置条件性访问以阻止访问 ARM](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：对高风险应用程序进行物理或逻辑分离

**指南**：不适用;此建议适用于在 Azure App Service 或计算资源上运行的 web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

*有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用 "Microsoft Storage" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行存储帐户实例的配置。 你还可以使用 Azure 存储帐户的内置 Azure 策略定义，例如： 

审核对存储帐户的不受限制的网络访问在存储帐户上部署高级威胁防护应将存储帐户迁移到新的 Azure 资源管理器资源应该启用安全传输到存储帐户 

使用 Azure 安全中心提供的建议作为存储帐户的安全配置基线。 

- [如何查看可用的 Azure 策略别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2：建立适用于你的操作系统的安全配置

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3：维护所有 Azure 资源的安全配置

**指南**：使用 Azure 策略 [拒绝] 和 [部署（如果不存在]）来跨存储帐户资源强制实施安全设置。 

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [了解 Azure Policy 效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4：维护操作系统的安全配置

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地存储 Azure 资源的配置

**指南**：使用 Azure Repos 安全地存储和管理代码，如自定义 azure 策略、azure 资源管理器模板、所需状态配置脚本等。若要访问 Azure DevOps 中管理的资源，可以授予或拒绝特定用户、内置安全组或在 Azure Active Directory （Azure AD）中定义的、与 Azure Active Directory DevOps 集成的权限，或在与 TFS 集成的情况下使用的组。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [关于 Azure DevOps 中的权限和组](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指南**：利用 Azure 策略来对存储帐户进行警报、审核和强制实施系统配置。 另外，开发一个用于管理策略例外的流程和管道。 

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署适用于操作系统的系统配置管理工具

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：实现 Azure 服务的自动配置监视

**指南**：利用 Azure 安全中心为 Azure 存储帐户资源执行基线扫描。 

- [如何修正 Azure 安全中心中的建议](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实现自动配置监视

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-securely-manage-azure-secrets"></a>7.11：安全管理 Azure 机密

**指南**： Azure 存储将数据保存到云时，会自动对其进行加密。 你可以使用 Microsoft 托管密钥来加密存储帐户，也可以使用自己的密钥来管理加密。 如果使用客户提供的密钥，则可以利用 Azure Key Vault 安全地存储密钥。 

此外，经常会定期轮换存储帐户密钥，以限制丢失或泄露存储帐户密钥的影响。

- [适用于静态数据的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [管理存储帐户访问密钥](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-securely-and-automatically-manage-identities"></a>7.12：安全、自动管理标识

**指南**：使用 Azure Active Directory 和托管标识授予对 Azure 存储帐户中的 blob 和队列的访问权限。 Azure Blob 和队列存储支持使用 Azure 资源的托管标识进行 Azure Active Directory (Azure AD) 身份验证。 Azure 资源的托管标识可以从 Azure 虚拟机 (VM)、函数应用、虚拟机规模集和其他服务中运行的应用程序使用 Azure AD 凭据授权对 Blob 和队列数据的访问权限。 将 Azure 资源的托管标识与 Azure AD 身份验证结合使用，可避免将凭据随在云中运行的应用程序一起存储。 

- [如何使用托管标识授予对 Azure blob 和队列数据的访问权限](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指导**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

- [如何设置凭据扫描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

*有关详细信息，请参阅[安全控制：恶意软件防护](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：不适用；此建议适用于计算资源。 Microsoft 处理底层平台的反恶意软件。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：对 azure 存储使用威胁防护，使用哈希信誉分析来检测恶意软件上传到 Azure 存储，并使用活动 Tor 出口节点（匿名代理）进行可疑访问。 

你还可以在上传到非计算 Azure 资源（如应用服务、Data Lake Storage、Blob 存储等）之前预扫描恶意软件的任何内容，以满足组织的要求。

- [配置 Azure 存储的高级威胁防护](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：确保更新反恶意软件和签名

**指导**：不适用；此建议适用于计算资源。 Microsoft 处理底层平台的反恶意软件。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

*有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期自动备份

**指南**：始终自动复制 Microsoft Azure 存储帐户中的数据以确保持久性和高可用性。 Azure 存储功能会复制数据，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 可以选择在同一数据中心中、跨同一区域中的局域数据中心或跨地理上隔离的区域复制数据。 

还可以启用 Azure 自动化，以定期拍摄 blob 快照。

- [了解 Azure 存储冗余和服务级别协议](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

- [创建 blob 的快照](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure 自动化概述](https://docs.microsoft.com/azure/automation/automation-intro)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整的系统备份并备份任何客户托管的密钥

**指南**：若要从存储帐户支持的服务备份数据，可以使用多种方法，包括使用 azcopy 或第三方工具。 Azure Blob 存储的不可变存储可让用户以 WORM（一次写入，多次读取）状态存储业务关键型数据对象。 此状态可以根据用户指定的时间间隔使数据保持不可擦除且不可修改的状态。

- [AzCopy 入门](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

- [为 Blob 存储设置和管理不可变性策略](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage?tabs=azure-portal)

使用 Azure CLI 或 PowerShell，可以在 Azure Key Vault 中支持客户管理/提供的密钥。 

- [如何在 Azure 中备份 key vault 密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证包括客户托管密钥在内的所有备份

**指南**：通过以下 PowerShell 命令定期执行 Key Vault 证书、密钥、托管存储帐户和机密的数据还原： 

AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [如何还原 Key Vault 证书](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [如何还原 Key Vault 密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [如何还原 Key Vault 托管存储帐户](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [如何还原 Key Vault 机密](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [AzCopy 是一个命令行实用工具，可用于在存储帐户中复制 blob、文件和表数据](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

注意：如果想要将数据复制到 Azure 表存储服务，并从 Azure 表存储服务复制数据，请安装 AzCopy 版本7.3。


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：若要在存储帐户上启用客户管理的密钥，必须使用 Azure Key Vault 来存储密钥。 必须同时启用密钥保管库上的“软删除”和“不清除”属性。 Key Vault 的软删除功能可以恢复已删除的保管库和保管库对象，例如密钥、机密和证书。 如果将存储帐户数据备份到 Azure 存储 blob，请启用软删除，在删除 blob 或 blob 快照时保存和恢复数据。 你应将备份视为敏感数据，并应用相关的访问和数据保护控件作为此基线的一部分。 此外，为了改进保护，你可以将关键业务数据对象存储在蠕虫（一次写入一次，读取多个）状态。

- [如何使用 Azure Key Vault 软删除](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

- [Azure 存储 Blob 的软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [使用不可变的存储来存储业务关键型 Blob 数据](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

*有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-incident-response-guide"></a>10.1：创建事件响应指南

**指南**：构建组织的事件响应指南。 请确保有一些书面事件响应计划，这些计划定义了人员的所有角色，以及从检测到后事件检查的事件处理/管理阶段。

- [有关构建你自己的安全事件响应过程的指导](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [客户还可以利用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件计分和优先级过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。 

此外，请明确标记订阅（例如 生产、非生产）使用标记并创建命名系统，以便清晰地识别和分类 Azure 资源，尤其是处理敏感数据的资源。 根据发生事件的 Azure 资源和环境的严重程度确定警报修正的优先级。

- [Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [使用标记来组织 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：执行试验以测试定期事件的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

- [NIST 发布指南，适用于 IT 计划和功能的测试、培训和试验计划](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息并为安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心（MSRC）发现你的数据被非法或未授权的一方访问，microsoft 将使用安全事件联系人信息与你联系。 在事实后查看事件，以确保解决问题。

- [如何设置 Azure 安全中心安全联系人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报合并到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 azure 资源的风险。 使用连续导出，可以手动或以持续、持续的方式导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](https://docs.microsoft.com/azure/security-center/continuous-export)

- [如何将警报流式传输到 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指南**：使用 Azure 安全中心的工作流自动化功能，可通过 "逻辑应用" 自动触发有关安全警报和建议的响应，以保护 Azure 资源。

- [如何配置工作流自动化和逻辑应用](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

*有关详细信息，请参阅[安全控制：渗透测试和 Red 团队练习](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11.1：对 Azure 资源进行定期渗透测试

**指南**：遵循 Microsoft 订婚规则确保你的渗透测试不违反 Microsoft 政策。 针对 Microsoft 托管的云基础结构、服务和应用程序，使用 Microsoft 的战略和对红色组合和活动站点渗透测试的执行。

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 了解有关[Azure 安全基线](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)的详细信息
