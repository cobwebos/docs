---
title: 适用于 Key Vault 的 Azure 安全基线
description: 适用于 Key Vault 的 Azure 安全基线
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9b767693691557f684bee59aa1764395dc42bffe
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590034"
---
# <a name="azure-security-baseline-for-key-vault"></a>适用于 Key Vault 的 Azure 安全基线

适用于 Key Vault 的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线是从[Azure 安全基准1.0 版（版本](https://docs.microsoft.com/azure/security/benchmarks/overview)）中提取的，它提供了有关如何在 Azure 上通过最佳实践指南来保护云解决方案的建议。

有关详细信息，请参阅[Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

*有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用网络安全组或虚拟网络上的 Azure 防火墙保护资源

**指南**：将 Azure Key Vault 与 Azure Private Link 集成。 使用 azure 专用链接服务，可以通过虚拟网络中的专用终结点访问 Azure 服务（例如 Azure Key Vault）和 Azure 托管的客户/合作伙伴服务。
Azure 专用终结点是一个网络接口，可以通过私密且安全的方式将你连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

如何将 Key Vault 与 Azure Private Link 集成： https://docs.microsoft.com/azure/key-vault/private-link-service

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 Vnet、子网和 Nic 的配置和流量

**指南**：使用 Azure 安全中心并按照网络保护建议来帮助保护 Azure 中的 Key Vault 配置资源。 


有关 Azure 安全中心提供的网络安全的详细信息，请执行以下操作： 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 web 应用程序

**指南**：不适用;此建议适用于在 Azure App Service 或计算资源上运行的 web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知的恶意 IP 地址的通信

**指南**：在与 Key Vault 实例关联的 Azure 虚拟网络上启用 Azure DDoS 保护，以防范分布式拒绝服务攻击。 使用 Azure 安全中心集成威胁情报拒绝与已知的恶意或未使用的 Internet IP 地址的通信。

 
使用 Azure 门户： azure 安全中心内 Azure 服务层的 https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection 威胁检测： https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指南**： Azure Key Vault 不使用网络安全组（NSG），也不捕获 Azure Key Vault 的流日志。 相反，使用 Azure 专用链接来保护 Azure Key Vault 实例，并启用诊断设置来记录指标和审核事件。
将 Key Vault 与 Azure Private Link 集成： https://docs.microsoft.com/azure/key-vault/private-link-service

Azure Key Vault 日志记录： https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统（IDS/IPS）

**指南**：可以通过配置 Azure Key Vault 的高级威胁防护（ATP）来满足此要求。 ATP 提供额外的安全智能层。 此工具检测到 Azure Key Vault 帐户访问或利用潜在有害尝试。


Azure 安全中心检测到异常活动时，会显示警报。 它还向订阅管理员发送有关可疑活动的详细信息，以及如何调查和修正确定的威胁的建议。


为 Azure Key Vault 设置高级威胁防护： https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault


**Azure 安全中心监视**：是

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 web 应用程序的流量

**指南**：不适用;此建议适用于在 Azure App Service 或计算资源上运行的 web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：降低网络安全规则的复杂性和管理开销

**指南**：对于需要访问 Azure Key Vault 实例的资源，请使用 Azure 服务标记 Azure Key Vault 来定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应 "源" 或 "目标" 字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。


Azure 服务标记概述：

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现与 Azure 策略关联的 Azure Key Vault 实例的网络资源的标准安全配置。 使用 "KeyVault" 和 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制实施 Azure Key Vault 实例的网络配置。 你还可以使用与 Azure Key Vault 相关的内置策略定义，例如：


Key Vault 应使用虚拟网络服务终结点


教程：创建和管理策略以强制实施符合性：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Azure 策略示例：

https://docs.microsoft.com/azure/governance/policy/samples/#networ

快速入门：在门户中定义和分配蓝图：

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：记录流量配置规则

**指南**：对与 Azure Key Vault 实例的网络安全和流量流相关的资源使用标记，以提供元数据和逻辑组织。


使用与标记相关的任何内置 Azure 策略定义（如 "需要标记和值"）来确保使用标记创建所有资源并通知现有未标记资源。


您可以使用 Azure PowerShell 或 Azure CLI 基于其标记对资源进行查找或执行操作。


使用标记来组织 Azure 资源：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Key Vault 实例相关的网络资源的更改。 在 Azure Monitor 中创建警报，以便在对关键网络资源进行更改时触发。

查看和检索 Azure 活动日志事件：

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


使用 Azure Monitor 创建、查看和管理活动日志警报：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

*有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**：不适用;对于日志中的时间戳，Microsoft 维护用于 Azure 资源的时间源，如 Azure Key Vault。

**Azure 安全中心监视**：不适用

**责任**： Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：通过 Azure Monitor 引入日志来聚合 Azure Key Vault 生成的安全数据。 在 Azure Monitor 中，使用 Azure Log Analytics 工作区查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储。 或者，你可以将和机载数据启用到 Azure Sentinel 或第三方 SIEM。 

Azure Key Vault 日志记录：

https://docs.microsoft.com/azure/key-vault/key-vault-logging

快速入门：如何板载 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：启用 Azure 资源的审核日志记录

**指南**：对 Azure Key Vault 实例启用诊断设置，以便访问审核、安全和诊断日志。 自动可用的活动日志，包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

Azure Key Vault 日志记录：

https://docs.microsoft.com/azure/key-vault/key-vault-logging

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留

**指南**：在 Azure Monitor 中，对于用于保存 Azure Key Vault 日志的 Log Analytics 工作区，请根据组织的符合性法规设置保留期。 将 Azure 存储帐户用于长期/存档存储。

更改数据保持期： https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指南**：分析和监视日志中的异常行为，并定期查看 Azure Key Vault 保护资源的结果。 使用 Azure Monitor 的 Log Analytics 工作区查看日志数据并执行查询。 或者，你可以将和机载数据启用到 Azure Sentinel 或第三方 SIEM。 


快速入门：板载 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard


Azure Monitor 中的 Log Analytics 入门： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure Monitor 中的日志查询入门： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Azure 安全中心监视**：是

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：为异常活动启用警报

**指南**：在 Azure 安全中心中，为 Key Vault 启用高级威胁防护（ATP）。 在 Azure Key Vault 中启用诊断设置，并将日志发送到 Log Analytics 工作区。 将 Log Analytics 工作区加入到 Azure Sentinel 上，因为它提供了一个安全业务流程自动化响应（之忠诚度）解决方案。 这样，就可以创建和使用行动手册（自动解决方案）来纠正安全问题。

快速入门：板载 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

管理和响应 Azure 安全中心的安全警报：

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts


响应事件 Azure Monitor 警报：

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中反恶意软件日志记录

**指南**：不适用;Azure Key Vault 不会处理或产生与反恶意软件相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：不适用;Azure Key Vault 不会处理或产生 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

*有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：维护 Azure Active Directory 注册的应用程序的清单，以及有权访问 Azure Key Vault 密钥、机密和证书的任何用户帐户。 你可以使用 Azure 门户或 PowerShell 来查询和协调 Key Vault 访问。 若要在 PowerShell 中查看访问权限，请使用以下命令：


（AzResource-ResourceId [KeyVaultResourceID]）。AccessPolicies

向 Azure Active Directory 注册应用程序：

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

保护对密钥保管库的访问：

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：更改默认密码（如果适用）

**指南**：不适用;Azure Key Vault 没有默认密码的概念，因为 Active Directory 提供身份验证，并使用基于角色的访问控制来保护身份。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：创建有关使用可访问 Azure Key Vault 实例的专用管理帐户的标准操作过程。 使用 Azure 安全中心的标识和访问管理（当前为预览版）来监视活动管理帐户的数量。


监视标识和访问（预览）：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用带有 Azure Active Directory 的单一登录（SSO）

**指南**：将 Azure 服务主体与 AppId、TenantID 和 ClientSecret 一起使用，以便无缝地对应用程序进行身份验证并检索用于访问 Azure Key Vault 机密的令牌。


使用 .NET Azure Key Vault 的服务到服务身份验证：

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory 多重身份验证，并遵循 Azure 安全中心的标识和访问管理（当前为预览版）建议来帮助保护启用了事件中心的资源。


规划基于云的 Azure 多重身份验证部署：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


监视标识和访问（预览）：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：将特权访问工作站（PAW）与 Azure 多重身份验证（MFA）结合使用，配置为登录并配置已启用 Key Vault 的资源。


特权访问工作站： https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

规划基于云的 Azure 多重身份验证部署： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：来自管理帐户的可疑活动的日志和警报

**指南**：在环境中发生可疑或不安全活动时，使用 AZURE ACTIVE DIRECTORY （AAD） PRIVILEGED IDENTITY MANAGEMENT （PIM）来生成日志和警报。 使用 AAD 风险检测来查看警报和报告有风险的用户行为。 对于其他日志记录，请将 Azure 安全中心风险检测警报发送到 Azure Monitor，并使用操作组配置自定义警报/通知。


为 Azure Key Vault 启用高级威胁防护（ATP）以生成可疑活动的警报。


部署 Azure AD Privileged Identity Management （PIM）：

https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

为 Azure Key Vault （预览版）设置高级威胁防护： https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault


Azure Key Vault 的警报（预览版）：

https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv


Azure Active Directory 风险检测：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events


在 Azure 门户中创建和管理操作组：

https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：配置条件访问策略的位置条件并管理命名位置。 通过命名位置，可以创建 IP 地址范围或国家和地区的逻辑分组。 你可以将敏感资源（如 Key Vault 机密）的访问权限限制为已配置的命名位置。

Azure Active Directory 条件访问中的位置条件是什么？： https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：使用 AZURE ACTIVE DIRECTORY （AAD）作为 Azure 资源（如 Key Vault）的集中身份验证和授权系统。 这允许基于角色的访问控制（RBAC）管理敏感资源。

快速入门：在 Azure Active Directory 中创建新租户： https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期查看和协调用户访问权限

**指南**：查看 AZURE ACTIVE DIRECTORY （AAD）日志，以帮助发现具有 Azure Key Vault 管理角色的过时帐户。 此外，还可以使用 AAD 访问评审来有效地管理组成员身份、访问可用于访问 Azure Key Vault 和角色分配的企业应用程序。 应定期查看用户访问权限，如每90天，确保只有正确的用户才能继续访问。


Azure Active Directory 报表和监视文档： https://docs.microsoft.com/azure/active-directory/reports-monitoring/


什么是 Azure AD 访问评审？： https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视器尝试访问停用的帐户

**指南**：对 Azure Key Vault 和 Azure Active Directory 启用诊断设置，将所有日志发送到 Log Analytics 工作区。 在 Log Analytics 中配置所需的警报（例如，尝试访问禁用的机密）。

将 Azure AD 日志与 Azure Monitor 日志集成： https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

从旧的 Key Vault 解决方案迁移： https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution       

**Azure 安全中心监视**：是

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帐户登录行为偏差的警报

**指南**：使用 Azure Active Directory 的标识保护和风险检测功能来配置对检测到的与 Azure Key Vault 受保护资源相关的可疑操作的自动响应。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。


Azure Active Directory 门户中的 "有风险的登录" 报告： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

如何：配置和启用风险策略： https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


如何载入 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：是

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持方案中为 Microsoft 提供对相关客户数据的访问权限

**指南**：不适用;Azure Key Vault 不支持客户密码箱。

公开上市的支持的服务和方案： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability


**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-protection"></a>数据保护

*有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记来帮助跟踪 Azure 资源，这些资源可存储或处理有关已启用 Azure Key Vault 资源的敏感信息。


使用标记来组织 Azure 资源： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：通过使用配置为限制对特定子网的访问的虚拟网络服务终结点，你可以保护对 Azure Key Vault 的访问。


防火墙规则生效后，只能在请求源自允许的子网或 IP 地址范围时执行 Azure Key Vault 数据平面操作。 这也适用于 Azure 门户中的 Azure Key Vault 访问。 虽然你可以从 Azure 门户浏览到密钥保管库，但如果你的客户端计算机不在允许列表中，则你可能无法列出密钥、机密或证书。 这也会影响 Azure Key Vault 选取器和其他 Azure 服务。 如果防火墙规则阻止你的客户端计算机这样做，则可以看到密钥保管库列表，但不能看到列表密钥。


配置 Azure Key Vault 防火墙和虚拟网络： https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault 的虚拟网络服务终结点： https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止敏感信息的未授权传输

**指南**： Azure Key Vault 中存储的所有数据都被视为敏感数据。 使用 Azure Key Vault 数据平面访问控制来控制对 Azure Key Vault 机密的访问。 你还可以使用 Key Vault 的内置防火墙来控制网络层的访问。 若要监视对 Azure Key Vault 的访问，请启用 Key Vault 诊断设置，并将日志发送到 Azure 存储帐户或 Log Analytics 工作区。

保护对密钥保管库的访问： https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

配置 Azure Key Vault 防火墙和虚拟网络： https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault 日志记录： https://docs.microsoft.com/azure/key-vault/key-vault-logging

**Azure 安全中心监视**：是

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：要 Azure Key Vault 进行身份验证、管理和数据平面访问的所有流量都经过加密，并通过 HTTPS：端口443进行。 （但是，对于 CRL，有时会出现 HTTP [端口 80] 通信。）


访问防火墙后 Azure Key Vault： https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall

**Azure 安全中心监视**：不适用

**责任**： Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用活动发现工具识别敏感数据

**指南**：不适用;Azure Key Vault （机密、密钥和证书）中的所有数据都被视为敏感数据。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="46-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.6：使用活动发现工具识别敏感数据

**指南**：保护对 Azure Key Vault 实例的管理和数据平面的访问。


保护对密钥保管库的访问：

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**： Microsoft 管理 Azure Key Vault 的底层基础结构，并实施了严格控制以防止客户数据丢失或泄露。


什么是 Azure Key Vault？ https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azure 客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：不适用

**责任**： Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密静态敏感信息

**指南**：所有托管对象（密钥、证书和机密）都在 Azure Key Vault 中静态加密。


Azure Key Vault 的安全性控制： https://docs.microsoft.com/azure/key-vault/key-vault-security-controls


**Azure 安全中心监视**：不适用

**责任**： Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：对关键 Azure 资源的更改进行记录和警报

**指南**：在 Azure Monitor 中使用 Azure Key Vault Analytics 解决方案查看 Azure Key Vault 审核事件日志。

Azure Monitor 中的 Azure Key Vault 分析解决方案： https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault


**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

*有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**： Microsoft 对支持 Azure Key Vault 的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**： Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补程序管理解决方案

**指南**：不适用;Microsoft 对支持 Key Vault 的基础系统执行修补程序管理。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自动第三方软件修补程序管理解决方案

**指南**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较后向后漏洞扫描

**指南**： Microsoft 对支持 Key Vault 的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**： Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评分过程来确定已发现漏洞的修正的优先级

**指南**：使用 Azure 安全中心提供的默认风险等级（安全评分）。

提高 Azure 安全中心的安全评分：

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Azure 安全中心监视**：当前不可用

**责任**：客户

## <a name="inventory-and-asset-management"></a>清单和资产管理

*有关详细信息，请参阅[安全控制：清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指南**：使用 Azure 资源关系图可查询和发现订阅中的所有资源（包括 Azure Key Vault 实例）。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅以及订阅中的资源。

快速入门：使用 Azure 资源关系图资源管理器运行第一个资源关系图查询： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

获取当前帐户可以访问的订阅。： https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

什么是适用于 Azure 资源的基于角色的访问控制（RBAC）？ https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：将标记应用于 Azure Key Vault 资源，使元数据以逻辑方式将它们组织到分类。


如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪 Azure Key Vault 实例和相关资源。 定期协调清点，并确保及时地从订阅中删除未经授权的资源。


创建其他 Azure 订阅：

https://docs.microsoft.com/azure/billing/billing-create-subscription


为资源组织和管理创建管理组：

https://docs.microsoft.com/azure/governance/management-groups/create


使用标记来组织 Azure 资源： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单

**指南**：定义你的计算资源的已批准 Azure 资源和批准的软件的列表

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未经批准的 Azure 资源

**指南**：使用 Azure 策略对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

此外，使用 Azure 资源关系图来查询/发现订阅中的资源。

教程：创建和管理策略以强制实施符合性： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

快速入门：使用 Azure 资源关系图资源管理器运行第一个资源关系图查询： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未经批准的软件应用程序

**指南**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未经批准的 Azure 资源和软件应用程序

**指南**：不适用;此建议适用于 Azure 整体和计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="68-use-only-approved-applications"></a>6.8：仅使用批准的应用程序

**指南**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用批准的 Azure 服务

**指南**：使用 Azure 策略对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

教程：创建和管理策略以强制实施符合性： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 策略示例： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-implement-approved-application-list"></a>6.10：实现已批准的应用程序列表

**指南**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11：限制用户通过脚本与 AzureResources Manager 交互的能力

**指南**：通过为 "Microsoft Azure 管理" 应用配置 "阻止访问"，使用 Azure 条件访问限制用户与 Azure 资源管理器的交互能力。 这可以防止在高安全性环境（如 Key Vault 配置的环境）中创建和更改资源。

使用条件性访问管理对 Azure 管理的访问权限： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

**指南**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：对高风险应用程序进行物理或逻辑分离

**指南**：不适用;此建议适用于在 Azure App Service 或计算资源上运行的 web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

*有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用 "KeyVault" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制实施 Azure Key Vault 实例的配置。 你还可以将内置的 Azure 策略定义用于 Azure Key Vault 例如：

- 应可恢复 Key Vault 对象
- 将 Key Vault 的诊断设置部署到 Log Analytics 工作区
- 应启用 Key Vault 中的诊断日志
- Key Vault 应使用虚拟网络服务终结点
- 将 Key Vault 的诊断设置部署到事件中心

使用 Azure 安全中心提供的建议作为 Azure Key Vault 实例的安全配置基线。

如何查看可用的 Azure 策略别名： https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

教程：创建和管理策略以强制实施符合性： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：是

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure 策略 [拒绝] 和 [部署（如果不存在]）在已启用 Azure Key Vault 的资源之间强制实施安全设置。 


教程：创建和管理策略以强制实施符合性：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 


  
了解 Azure 策略影响： 

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地存储 Azure 资源的配置

**指南**：如果对已启用的 Azure Key Vault 资源使用自定义 azure 策略定义，请使用 Azure DevOps/存储库来安全地存储和管理你的代码。


如何在 Azure DevOps 中存储代码： 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 


Azure Repos 文档： 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指南**：使用 "KeyVault" 命名空间中的 Azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 此外，开发用于管理策略异常的进程和管道。



如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署适用于操作系统的系统配置管理工具

**指南**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：实现 Azure 服务的自动配置监视

**指南**：使用 Azure 安全中心对受 Azure Key Vault 保护的资源执行基线扫描 

  

如何在 Azure 安全中心修正建议： 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Azure 安全中心监视**：是

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实现自动配置监视

**指南**：不适用;此基准用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：将托管服务标识与 Azure Key Vault 结合使用，以便为你的云应用程序简化和安全密钥管理。 确保启用 Azure Key Vault 软删除。


如何与 Azure 托管标识集成：

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity


如何创建 Key Vault：

https://docs.microsoft.com/azure/key-vault/quick-create-portal


如何使用托管标识提供 Key Vault 身份验证： 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全、自动管理标识

**指南**：将托管服务标识与 Azure Key Vault 结合使用，以便为你的云应用程序简化和安全密钥管理。 

  

如何与 Azure 托管标识集成： 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  



如何创建 Key Vault： 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

如何使用托管标识提供 Key Vault 身份验证：  
https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据公开

**指南**：通过实现凭据扫描器来识别代码中的凭据。 凭据扫描器还鼓励将发现的凭据迁移到更安全的位置，例如 Azure Key Vault。  
  
 如何设置凭据扫描器： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

*有关详细信息，请参阅[安全控制：恶意软件防护](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用;此建议用于计算资源。 Microsoft 处理底层平台的反恶意软件。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预扫描要上载到非计算 Azure 资源的文件

**指南**：在支持 Azure 服务的基础主机（例如 Azure Key Vault）上启用了 Microsoft 反恶意软件，但它不会在客户内容上运行。


预先扫描正在上传或发送到非计算 Azure 资源（如 Azure Key Vault）的任何内容。 Microsoft 无法访问这些实例中的数据。


了解适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件： https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：确保更新反恶意软件和签名

**指南**：不适用;此建议用于计算资源。 Microsoft 处理底层平台的反恶意软件。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

*有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期自动备份

**指南**：通过以下 PowerShell 命令确保定期自动备份 Key Vault 证书、密钥、托管存储帐户和机密：

- 备份-AzKeyVaultCertificate
- 备份-AzKeyVaultKey
- 备份-AzKeyVaultManagedStorageAccount
- 备份-AzKeyVaultSecret

或者，你可以将 Key Vault 备份存储在 Azure 备份中。

如何备份 Key Vault 证书： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

如何备份 Key Vault 密钥： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

如何备份 Key Vault 托管存储帐户： https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

如何备份 Key Vault 机密： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

如何启用 Azure 备份： https://docs.microsoft.com/azure/backup

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整的系统备份并备份任何客户托管的密钥

**指南**：通过以下 PowerShell 命令执行 Key Vault 证书、密钥、托管存储帐户和机密的备份：

- 备份-AzKeyVaultCertificate
- 备份-AzKeyVaultKey
- 备份-AzKeyVaultManagedStorageAccount
- 备份-AzKeyVaultSecret

或者，你可以将 Key Vault 备份存储在 Azure 备份中。

如何备份 Key Vault 证书： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

如何备份 Key Vault 密钥： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

如何备份 Key Vault 托管存储帐户： https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

如何备份 Key Vault 机密： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

如何启用 Azure 备份： https://docs.microsoft.com/azure/backup

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证包括客户托管密钥在内的所有备份

**指南**：通过以下 PowerShell 命令定期执行 Key Vault 证书、密钥、托管存储帐户和机密的数据还原：

- 还原-AzKeyVaultCertificate
- 还原-AzKeyVaultKey
- 还原-AzKeyVaultManagedStorageAccount
- 还原-AzKeyVaultSecret

如何还原 Key Vault 证书： https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

如何还原 Key Vault 密钥： https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

如何还原 Key Vault 托管存储帐户： https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

如何还原 Key Vault 机密： https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：确保为 Azure Key Vault 启用软删除。 软删除可恢复已删除的密钥保管库和保管库对象，例如密钥、机密和证书。 

如何使用 Azure Key Vault 软删除： 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure 安全中心监视**：当前不可用

**责任**：客户

## <a name="incident-response"></a>事件响应

*有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指南

**指南**：构建组织的事件响应指南。 请确保有一些书面事件响应计划，这些计划定义了人员的所有角色，以及从检测到后事件检查的事件处理/管理阶段。 这些流程应重点关注保护敏感系统，如使用 Key Vault 机密的系统。



如何在 Azure 安全中心内配置 Workflow 自动化： 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   



有关构建你自己的安全事件响应过程的指导：  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/



Microsoft 安全响应中心事件解析：   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   



客户还可以利用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划： 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件计分和优先级过程

**指南**：安全中心为每个警报分配一个严重性，以帮助你确定应该首先调查的警报的优先级。 严重性取决于安全中心在查找或用于发出警报的分析中的置信度，以及导致警报的活动的恶意意图的置信度。 此外，清楚地标记订阅（例如 生产、非生产），并创建一个命名系统来明确识别和分类 Azure 资源，尤其是处理敏感数据（例如 Azure Key Vault 机密）的情况。

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：执行练习，测试定期事件的事件响应功能，以帮助保护 Azure Key Vault 实例和相关资源。 确定薄弱点和间隙，并根据需要修改计划。


请参阅 NIST 发布：针对 IT 计划和功能的测试、培训和试验计划指南： 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息并为安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心（MSRC）发现你的数据被非法或未授权的一方访问，microsoft 将使用安全事件联系人信息与你联系。  在事实后查看事件，以确保解决问题。



如何设置 Azure 安全中心安全联系人：

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报合并到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure Key Vault 支持的资源的风险。 使用连续导出，可以手动或以持续、持续的方式导出警报和建议。  可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。 

 

如何配置连续导出： 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

如何将警报流式传输到 Azure Sentinel： 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指南**：使用 Azure 安全中心的工作流自动化功能，可通过 "逻辑应用" 自动触发有关安全警报和建议的响应，以保护受 Azure Key Vault 保护的资源。 

 

如何配置工作流自动化和逻辑应用： 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

*有关详细信息，请参阅[安全控制：渗透测试和 Red 团队练习](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：对 Azure 资源进行定期渗透测试，并确保60天内所有关键安全发现的修正

**指南**：你不会直接对 Azure Key Vault 服务执行笔测试，但建议你测试使用 Key Vault 的 Azure 资源，以确保密码的安全性。


你将需要遵循 Microsoft 交往规则，以确保你的渗透测试不违反 Microsoft 策略：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1


可在以下位置找到有关 Microsoft 管理的云基础结构、服务和应用程序的 Microsoft 策略、红组和实时站点渗透测试的详细信息： 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心监视**：不适用

**责任**：共享

