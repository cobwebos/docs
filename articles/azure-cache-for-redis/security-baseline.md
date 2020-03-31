---
title: 用于雷瑞斯 Azure 缓存的 Azure 安全基线
description: 用于雷瑞斯 Azure 缓存的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 364b87e4d64b8cc65fdf293032f4340bddec957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479132"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>用于雷瑞斯 Azure 缓存的 Azure 安全基线

Redis Azure 缓存的 Azure 安全基线包含一些建议，可帮助您改进部署的安全状况。

此服务的基线取自[Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，它提供了有关如何使用最佳实践指南在 Azure 上保护云解决方案的建议。

有关详细信息，请参阅[Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

*有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虚拟网络上的网络安全组或 Azure 防火墙保护资源

**指南**：在虚拟网络 （VNet） 中部署 Redis 实例的 Azure 缓存。 VNet 是云中的专用网络。 为 Azure Redis 缓存实例配置了 VNet 后，该实例不可公开寻址，而只能从 VNet 中的虚拟机和应用程序进行访问。

您还可以指定具有开始和结束 IP 地址范围的防火墙规则。 配置防火墙规则时，仅指定 IP 地址范围内的客户端连接可以连接到缓存。

如何为 Redis 配置高级 Azure 缓存的虚拟网络支持：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

如何为 Redis 防火墙规则配置 Azure 缓存：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 监视和记录 Vnet、子网和 NIC 的配置和流量

**指南**：当虚拟机部署在与 Redis 实例的 Azure 缓存相同的虚拟网络中时，可以使用网络安全组 （NSG） 来降低数据泄露的风险。 启用 NSG 流日志并将日志发送到 Azure 存储帐户以进行流量审核。 您还可以将 NSG 流日志发送到日志分析工作区，并使用流量分析提供有关 Azure 云中的流量流的见解。 流量分析的一些优点是能够可视化网络活动并识别热点、识别安全威胁、了解流量模式和查明网络错误配置。

如何启用 NSG 流日志：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用和使用流量分析：

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="13-protect-critical-web-applications"></a>1.3： 保护关键 Web 应用程序

**指导**：不适用;此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒绝与已知恶意 IP 地址的通信

**指南**：Azure 虚拟网络 （VNet） 部署为 Redis 的 Azure 缓存提供了增强的安全性和隔离性，以及子网、访问控制策略和其他功能，以进一步限制访问。 在 VNet 中部署时，Redis 的 Azure 缓存不可公开寻址，并且只能从 VNet 中的虚拟机和应用程序访问。

在与 Azure 缓存关联的 Redis 实例 VNets 上启用 DDoS 保护标准，以防止分布式拒绝服务 （DDoS） 攻击。 使用 Azure 安全中心集成威胁智能拒绝与已知恶意或未使用的 Internet IP 地址的通信。

如何为 Redis 配置高级 Azure 缓存的虚拟网络支持：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

使用 Azure 门户管理 Azure DDoS 保护标准：

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5： 记录网络数据包和流日志

**指南**：当虚拟机部署在与 Redis 实例的 Azure 缓存相同的虚拟网络中时，可以使用网络安全组 （NSG） 来降低数据泄露的风险。 启用 NSG 流日志并将日志发送到 Azure 存储帐户以进行流量审核。 您还可以将 NSG 流日志发送到日志分析工作区，并使用流量分析提供有关 Azure 云中的流量流的见解。 流量分析的一些优点是能够可视化网络活动并识别热点、识别安全威胁、了解流量模式和查明网络错误配置。

如何启用 NSG 流日志：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用和使用流量分析：

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6： 部署基于网络的入侵检测/入侵防御系统（IDS/IPS）

**指南**：在 Azure 应用服务或计算实例上运行的 Web 应用程序使用 Azure 缓存时，在 Azure 虚拟网络 （VNet） 中部署所有资源，并在 Web 应用程序网关上使用 Azure Web 应用程序防火墙 （WAF） 进行保护。 将 WAF 配置为在"预防模式"中运行。 预防模式阻止规则检测到的入侵和攻击。 攻击者会收到“403 未授权访问”异常，且连接会结束。 阻止模式会在 WAF 日志中记录此类攻击。

或者，您可以从 Azure 应用商店中选择一个产品/服务，该产品/服务支持具有有效负载检查和/或异常检测功能的 IDS/IPS 功能。

了解 Azure WAF 功能：

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

如何部署 Azure WAF：

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure 市场：

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7： 管理 Web 应用程序的流量

**指导**：不适用;此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络服务标记定义网络安全组 （NSG） 或 Azure 防火墙上的网络访问控件。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。

您还可以使用应用程序安全组 （ASG） 来帮助简化复杂的安全配置。 ASG 使您能够将网络安全配置为应用程序结构的自然扩展，从而允许您对虚拟机进行分组并根据这些组定义网络安全策略。

虚拟网络服务标签：

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

应用程序安全组：

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 维护网络设备的标准安全配置

**指南**：使用 Azure 策略为 Redis 实例定义和实施与 Azure 缓存相关的网络资源的标准安全配置。 在"Microsoft.Cache"和"Microsoft.Network"命名空间中使用 Azure 策略别名来创建自定义策略，以审核或强制执行 Redis 实例的 Azure 缓存的网络配置。 您还可以使用内置策略定义，例如：

应该启用只能通过安全方式连接到 Redis 缓存

应启用 DDoS 防护标准版

您还可以使用 Azure 蓝图通过在单个蓝图定义中打包关键环境工件（如 Azure 资源管理器 （ARM） 模板、基于角色的访问控制 （RBAC） 和策略，来简化大规模 Azure 部署。 轻松将蓝图应用于新订阅和环境，并通过版本控制微调控制和管理。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何创建 Azure 蓝图：

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="110-document-traffic-configuration-rules"></a>1.10： 文档流量配置规则

**指南**：对与 Azure 缓存关联的网络资源使用标记进行 Redis 部署，以便从逻辑上将它们组织到分类中。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自动工具监控网络资源配置并检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 Redis 实例的 Azure 缓存相关的网络资源的更改。 在 Azure 监视器中创建警报，这些警报将在对关键网络资源的更改时触发。

如何查看和检索 Azure 活动日志事件：

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 监视器中创建警报：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="logging-and-monitoring"></a>日志记录和监视

*有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的时间同步源

**指南**：Microsoft 维护用于 Azure 资源的时间源，例如日志中用于 Redis 的 Azure 缓存。

**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全日志管理

**指导**：启用 Azure 活动日志诊断设置，并将日志发送到日志分析工作区、Azure 事件中心或 Azure 存储帐户进行存档。 活动日志提供对在控制平面级别为 Redis 实例的 Azure 缓存执行的操作的见解。 使用 Azure 活动日志数据，可以确定在 Redis 实例的 Azure 缓存的控制平面级别执行的任何写入操作（PUT、POST、DELETE）的"什么、谁和时间"。

如何为 Azure 活动日志启用诊断设置：https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：启用 Azure 活动日志诊断设置，并将日志发送到日志分析工作区、Azure 事件中心或 Azure 存储帐户进行存档。 活动日志提供对在控制平面级别为 Redis 实例的 Azure 缓存执行的操作的见解。 使用 Azure 活动日志数据，可以确定在 Redis 实例的 Azure 缓存的控制平面级别执行的任何写入操作（PUT、POST、DELETE）的"什么、谁和时间"。

虽然通过启用诊断设置提供指标，但数据平面上的审核日志记录仍不适用于 Redis 的 Azure 缓存。

如何为 Azure 活动日志启用诊断设置：https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4： 从操作系统收集安全日志

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全日志存储保留

**指南**：在 Azure 监视器中，根据组织的合规性法规为与 Azure 缓存关联的日志分析工作区设置日志保留期。

请注意，数据平面上的审核日志记录尚未可用于 Redis 的 Azure 缓存。

如何设置日志保留参数：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="26-monitor-and-review-logs"></a>2.6： 监视和查看日志

**指导**：启用 Azure 活动日志诊断设置并将日志发送到日志分析工作区。 在日志分析中执行查询，以搜索术语、识别趋势、分析模式，并根据可能为 Redis 为 Azure 缓存收集的活动日志数据提供许多其他见解。

请注意，数据平面上的审核日志记录尚未可用于 Redis 的 Azure 缓存。

如何为 Azure 活动日志启用诊断设置：https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

如何在 Azure 监视器中的日志分析工作区中收集和分析 Azure 活动日志：https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 启用异常活动的警报

**指南**：您可以配置为根据与 Redis 实例的 Azure 缓存相关的指标和活动日志来接收警报。 Azure 监视器允许您配置警报以发送电子邮件通知、调用 Webhook 或调用 Azure 逻辑应用。

虽然通过启用诊断设置提供指标，但数据平面上的审核日志记录仍不适用于 Redis 的 Azure 缓存。

如何为雷定配置 Azure 缓存的警报：https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反恶意软件日志记录

**指导**：不适用;Redis 的 Azure 缓存不处理或生成与恶意软件相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9： 启用 DNS 查询日志记录

**指导**：不适用;Redis 的 Azure 缓存不处理或生成 DNS 相关日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10： 启用命令行审核日志记录

**指导**：不适用;本指南用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

*有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1： 维持管理帐户的清单

**指南**： Azure 活动目录 （AD） 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行临时查询以发现属于管理组成员的帐户。

如何使用 PowerShell 在 Azure AD 中获取目录角色：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 获取 Azure AD 中的目录角色成员：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2： 在适用的情况下更改默认密码

**指南**：通过 Azure 活动目录 （AD） 控制对雷瑞斯 Azure 缓存的平面访问。 Azure AD 没有默认密码的概念。 

通过访问键控制对 Redis Azure 缓存的数据平面访问。 这些键由连接到缓存的客户端使用，并可以随时重新生成。

不建议您将默认密码构建到应用程序中。 相反，您可以将密码存储在 Azure 密钥保管库中，然后使用 Azure 活动目录检索密码。

如何为 Redis 访问密钥重新生成 Azure 缓存：https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Azure 安全中心监视**：不适用

**责任**： 共享

### <a name="33-use-dedicated-administrative-accounts"></a>3.3： 使用专用管理帐户

**指导**：围绕使用专用管理帐户创建标准操作程序。 使用 Azure 安全中心标识和访问管理监视管理帐户数。

此外，为了帮助跟踪专用管理帐户，可以使用 Azure 安全中心或内置 Azure 策略的建议，例如：

- 应该为你的订阅分配了多个所有者

- 应从订阅中删除拥有所有者权限的已弃用帐户

- 应从订阅中删除拥有所有者权限的外部帐户

如何使用 Azure 安全中心监视标识和访问（预览）：https://docs.microsoft.com/azure/security-center/security-center-identity-access

如何使用 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4： 使用 Azure 活动目录的单一登录 （SSO）

**指南**：Redis 的 Azure 缓存使用访问密钥对用户进行身份验证，不支持数据平面级别的单一登录 （SSO）。 通过 REST API 可以访问 Redis Azure 缓存的控制平面，并支持 SSO。 要进行身份验证，请将请求的授权标头设置为从 Azure 活动目录获取的 JSON Web 令牌。

了解 Redis REST API 的 Azure 缓存：https://docs.microsoft.com/rest/api/redis/

使用 Azure AD 了解 SSO：https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure 活动目录的访问使用多重身份验证

**指南**：启用 Azure 活动目录 （AD） 多重身份验证 （MFA），并遵循 Azure 安全中心标识和访问管理建议。

如何在 Azure 中启用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心内监视标识和访问：https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用特权访问工作站 （PAW） 与多因素身份验证 （MFA） 配置为登录和配置 Azure 资源。

了解特权访问工作站：

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中启用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7： 从管理帐户记录和警报可疑活动

**指南**：当环境中发生可疑或不安全活动时，请使用 Azure 活动目录 （AD） 特权标识管理 （PIM） 生成日志和警报。

此外，使用 Azure AD 风险检测来查看有关风险用户行为的警报和报告。

如何部署特权标识管理 （PIM）：https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

了解 Azure AD 风险检测：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从已批准的位置管理 Azure 资源

**指南**：在 Azure 活动目录 （AD） 条件访问中配置命名位置，以仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

如何在 Azure 中配置命名位置：https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活动目录

**指南**：使用 Azure 活动目录 （AD） 作为中央身份验证和授权系统。 Azure AD 对静态和传输中的数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行盐渍、哈希和安全地存储。

Azure AD 身份验证不能用于直接访问 Redis 数据平面的 Azure 缓存，但是，Azure AD 凭据可用于在控件平面级别（即 Azure 门户）进行管理，以控制 Redis 访问密钥的 Azure 缓存。


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期审查和协调用户访问

**指南**： Azure 活动目录 （AD） 提供日志，以帮助您发现陈旧的帐户。 此外，使用 Azure 标识访问审核可高效地管理组成员身份、对企业应用程序的访问和角色分配。 可以定期查看用户访问权限，以确保只有正确的用户才能继续访问。 

了解 Azure AD 报告：https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 标识访问审核：https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 监视访问已停用帐户的尝试

**指南**：您可以访问 Azure 活动目录 （AD） 登录活动、审核和风险事件日志源，这允许您与 Azure Sentinel 或第三方 SIEM 集成。

您可以通过为 Azure AD 用户帐户创建诊断设置并将审核日志和登录日志发送到日志分析工作区来简化此过程。 您可以在日志分析中配置所需的日志警报。

如何将 Azure 活动日志集成到 Azure 监视器中：https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帐户登录行为偏差警报

**指南**：对于控制平面上的帐户登录行为偏差，请使用 Azure 活动目录 （AD） 标识保护和风险检测功能配置自动响应，以识别与用户身份相关的可疑操作。 您还可以将数据引入 Azure 哨兵以进行进一步调查。

如何查看 Azure AD 风险登录：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和启用身份保护风险策略：https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支持方案期间向 Microsoft 提供对相关客户数据的访问

**指导**：尚不可用;Redis 的 Azure 缓存尚不支持客户密码箱。

客户密码箱支持的服务列表：

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 安全中心监视**：当前不可用

**责任**：不适用

## <a name="data-protection"></a>数据保护

*有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 维护敏感信息的清单

**指南**：使用标记来帮助跟踪存储或处理敏感信息的 Azure 资源。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 Redis 实例的 Azure 缓存应由虚拟网络/子网分隔并正确标记。 或者，使用 Redis 防火墙的 Azure 缓存来定义规则，以便只有来自指定 IP 地址范围的客户端连接才能连接到缓存。

如何创建其他 Azure 订阅：

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组：

https://docs.microsoft.com/azure/governance/management-groups/create

如何将 Redis 的 Azure 缓存部署到 Vnet：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

如何为 Redis 防火墙规则配置 Azure 缓存：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 监控和阻止未经授权传输敏感信息

**指导**：尚不可用;数据标识、分类和损失预防功能尚未可用于 Redis 的 Azure 缓存。

Microsoft 管理 Redis Azure 缓存的基础基础结构，并实施了严格的控制，以防止客户数据丢失或暴露。

了解 Azure 中的客户数据保护：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：当前不可用

**责任**： 共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：默认情况下，Redis 的 Azure 缓存需要 TLS 加密通信。 目前支持 TLS 版本 1.0、1.1 和 1.2。 但是，TLS 1.0 和 TLS 1.1 即将在全行业范围内弃用，因此，请尽可能使用 TLS 1.2。 如果客户端库或工具不支持 TLS，则可以通过 Azure 门户或管理 API 来启用未加密的连接。 在无法进行加密连接的情况下，建议将缓存和客户端应用程序放入虚拟网络中。

了解 Redis 的 Azure 缓存在传输中的加密：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

了解 Vnet 缓存方案中使用的必要端口：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Azure 安全中心监视**：是

**责任**： 共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活动发现工具识别敏感数据

**指南**：数据识别、分类和损失预防功能尚未可用于 Redis 的 Azure 缓存。 标记包含敏感信息的实例，并根据需要实施第三方解决方案，以便实现合规性目的。

对于由 Microsoft 管理的基础平台，Microsoft 将所有客户内容视为敏感内容，并竭尽全力防止客户数据丢失和暴露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已经实施并维护了一套强大的数据保护控制和功能。

了解 Azure 中的客户数据保护：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制对资源的访问

**指导**：使用 Azure 活动目录 （AAD） 基于角色的访问控制 （RBAC） 来控制对 Redis 控件平面（即 Azure 门户）的 Azure 缓存的访问。 

如何在 Azure 中配置 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基于主机的数据丢失防护来实施访问控制

**指导**：不适用;此建议用于计算资源。

Microsoft 管理 Redis Azure 缓存的基础基础结构，并实施了严格的控制，以防止客户数据丢失或暴露。

了解 Azure 中的客户数据保护：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 静态加密敏感信息

**指南**：Redis 的 Azure 缓存将客户数据存储在内存中，虽然受到 Microsoft 实施的许多控件的强力保护，但默认情况下不会加密内存。 如果组织需要，请在存储在 Azure 缓存中以供 Redis 之前对内容进行加密。

如果使用 Redis 的 Azure 缓存功能"Redis 数据持久性"，则数据将发送到您拥有和管理的 Azure 存储帐户。 您可以在创建缓存期间配置"Redis 的新 Azure 缓存"边栏选项卡中的持久性，也可以在现有高级缓存的资源菜单上配置持久性。

Azure 存储中的数据将使用 256 位 AES 加密法（可用的最强大块加密法之一）以透明方式进行加密和解密，并符合 FIPS 140-2 规范。 无法禁用 Azure 存储加密。 可以依赖于使用 Microsoft 托管的密钥来加密存储帐户，或者，可以使用自己的密钥来管理加密。

如何为 Redis 在 Azure 缓存中配置持久性：https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

了解 Azure 存储帐户的加密：https://docs.microsoft.com/azure/storage/common/storage-service-encryption

了解 Azure 客户数据保护：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：不适用

**责任**： 共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 对关键 Azure 资源的更改进行日志和警报

**指南**：将 Azure 监视器与 Azure 活动日志一起用于创建警报，用于何时对 Redis 和其他关键或相关资源的 Azure 缓存的生产实例进行更改。

如何为 Azure 活动日志事件创建警报：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="vulnerability-management"></a>漏洞管理

*有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1： 运行自动漏洞扫描工具

**指南**：按照 Azure 安全中心的建议，为 Redis 实例和相关资源保护 Azure 缓存。

Microsoft 在支持 Redis Azure 缓存的基础系统上执行漏洞管理。

了解 Azure 安全中心建议：https://docs.microsoft.com/azure/security-center/recommendations-reference

**Azure 安全中心监视**：是

**责任**： 共享

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动化操作系统修补程序管理解决方案

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自动化的第三方软件修补程序管理解决方案

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4： 比较背对背漏洞扫描

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5： 使用风险评级流程确定修复已发现漏洞的优先级

**指南**：Microsoft 对支持 Redis 的 Azure 缓存的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**： 微软

## <a name="inventory-and-asset-management"></a>清单和资产管理

*有关详细信息，请参阅[安全控制：库存和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1： 使用 Azure 资产发现

**指南**：使用 Azure 资源图查询/发现订阅中的所有资源（如计算、存储、网络、端口和协议等）。  请确保租户中的适当（读取）权限，并枚举订阅中的所有 Azure 订阅以及资源。

尽管可以通过资源图发现经典 Azure 资源，但强烈建议今后创建和使用 Azure 资源管理器资源。

如何使用 Azure 资源图创建查询：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 订阅：https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

了解 Azure RBAC：https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="62-maintain-asset-metadata"></a>6.2： 维护资产元数据

**指南**：将标记应用于 Azure 资源，使元数据以逻辑方式将它们组织到分类中。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3： 删除未经授权的 Azure 资源

**指南**：在适当情况下，使用标记、管理组和单独的订阅来组织和跟踪 Redis 实例和相关资源的 Azure 缓存。 定期协调库存，确保及时从订阅中删除未经授权的资源。

此外，使用 Azure 策略对客户订阅中可以使用以下内置策略定义创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

如何创建其他 Azure 订阅：https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组：https://docs.microsoft.com/azure/governance/management-groups/create

如何创建和使用标记：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单

**指导**：不适用;此建议适用于计算资源和整个 Azure。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5： 监视未经批准的 Azure 资源

**指南**：使用 Azure 策略对客户订阅中可创建的资源类型使用以下内置策略定义进行限制：

不允许的资源类型

允许的资源类型

此外，使用 Azure 资源图查询/发现订阅中的资源。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 图形创建查询：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6： 监控计算资源中未经批准的软件应用程序

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7： 删除未经批准的 Azure 资源和软件应用程序

**指导**：不适用;此建议适用于计算资源和整个 Azure。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="68-use-only-approved-applications"></a>6.8： 仅使用已批准的应用程序

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9： 仅使用已批准的 Azure 服务

**指南**：使用 Azure 策略对客户订阅中可创建的资源类型使用以下内置策略定义进行限制：

不允许的资源类型

允许的资源类型

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 策略拒绝特定资源类型：

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="610-implement-approved-application-list"></a>6.10： 实施已批准的申请列表

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器交互的能力

**指南**：配置 Azure 条件访问，通过为"Microsoft Azure 管理"应用配置"阻止访问"来限制用户与 Azure 资源管理器 （ARM） 交互的能力。

如何配置条件访问以阻止对 ARM 的访问：

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12： 限制用户在计算资源中执行脚本的能力

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13： 物理或逻辑上隔离高风险应用程序

**指导**：不适用;此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

*有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用 Azure 策略为 Redis 实例定义和实现 Azure 缓存的标准安全配置。 在"Microsoft.Cache"命名空间中使用 Azure 策略别名创建自定义策略，以审核或强制执行 Redis 实例的 Azure 缓存配置。 您还可以使用与 Redis 实例的 Azure 缓存相关的内置策略定义，例如：

应该启用只能通过安全方式连接到 Redis 缓存

如何查看可用的 Azure 策略别名：https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2： 建立安全的操作系统配置

**指导**：不适用;本指南用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 策略 [拒绝] 和 [部署（如果不存在））在整个 Azure 资源中强制实施安全设置。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

了解 Azure 策略效果：https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4： 维护安全的操作系统配置

**指导**：不适用;本指南用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存储 Azure 资源的配置

**指南**：如果为 Redis 实例和相关资源的 Azure 缓存使用自定义 Azure 策略定义或 Azure 资源管理器模板，请使用 Azure 存储库安全地存储和管理代码。

如何在 Azure DevOps 中存储代码：https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 存储库文档：https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存储自定义操作系统映像

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系统配置管理工具

**指南**：使用"Microsoft.Cache"命名空间中的 Azure 策略别名创建自定义策略以警报、审核和强制执行系统配置。 此外，开发用于管理策略异常的流程和管道。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实现自动化配置监视

**指南**：使用"Microsoft.Cache"命名空间中的 Azure 策略别名创建自定义策略以警报、审核和强制执行系统配置。 使用 Azure 策略 [审核]、[拒绝]和 [部署（如果不存在）]自动为 Redis 实例和相关资源强制实施 Azure 缓存的配置。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 对操作系统实施自动配置监控

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11： 安全管理 Azure 机密

**指南**：对于在 Azure 应用服务上运行的 Azure 虚拟机或 Web 应用程序，用于访问 Redis 实例的 Azure 缓存，请使用 Azure 密钥保管库的托管服务标识来简化和保护 Azure 缓存，以便 Redis 密钥管理。 确保启用密钥保管库软删除。

如何与 Azure 托管标识集成：

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何创建密钥保管库：

https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用托管标识提供密钥保管库身份验证：

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12： 安全、自动地管理身份

**指南**：对于在 Azure 应用服务上运行的 Azure 虚拟机或 Web 应用程序，用于访问 Redis 实例的 Azure 缓存，请使用 Azure 密钥保管库的托管服务标识来简化和保护 Azure 缓存，以便 Redis 密钥管理。 确保启用密钥保管库软删除。

使用托管标识在 Azure 活动目录中为 Azure 服务提供自动托管标识。 托管标识允许您对支持 AAD 身份验证的任何服务（包括 Azure 密钥保管库）进行身份验证，而无需在代码中进行任何凭据。

如何配置托管标识：

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

如何与 Azure 托管标识集成：

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13： 消除意外的凭据暴露

**指南**：实现凭据扫描程序以标识代码中的凭据。 凭据扫描程序还将鼓励将发现的凭据移动到更安全的位置，如 Azure 密钥保管库。

如何设置凭据扫描程序：https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="malware-defense"></a>恶意软件防护

*有关详细信息，请参阅[安全控制：恶意软件防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1： 使用集中管理的反恶意软件

**指导**：不适用;此建议用于计算资源。

在支持 Azure 服务的基础主机（例如 Azure 应用服务）上启用 Microsoft 反恶意软件，但不在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：要上载到非计算 Azure 资源的预扫描文件

**指南**：在支持 Azure 服务的基础主机上启用 Microsoft 反恶意软件（例如，Redis 的 Azure 缓存），但它不在客户内容上运行。

预扫描上载到非计算 Azure 资源的任何内容，如应用服务、数据湖存储、Blob 存储、用于 PostgreSQL 的 Azure 数据库等。在这种情况下，Microsoft 无法访问您的数据。

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3： 确保更新反恶意软件和签名

**指导**：不适用;此建议用于计算资源。

在支持 Azure 服务的基础主机上启用 Microsoft 反恶意软件（例如，Redis 的 Azure 缓存），但它不在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

*有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1： 确保定期自动备份

**指导**：启用 Redis 持久性。 使用 Redis 持久性可以在 Redis 中持久存储数据。 还可以获取快照并备份数据，以便在出现硬件故障时进行加载。 这相对于基本级别或标准级别是一项巨大优势，因为基本级别或标准级别将所有数据存储在内存中，在出现故障的情况下，如果缓存节点停机，则可能导致数据丢失。

您还可以使用 Azure 缓存进行雷瑞斯导出。 使用导出可以将 Azure Redis 缓存中存储的数据导出到与 Redis 兼容的 RDB 文件。 可以使用此功能将一个 Azure Redis 缓存实例中的数据移到另一个 Azure Redis 缓存实例或另一个 Redis 服务器。 在导出过程中，在承载 Redis 服务器实例的 Azure 缓存的虚拟机上创建一个临时文件，并将该文件上载到指定的存储帐户。 导出操作完成后，无论状态为成功还是失败，都会删除临时文件。

如何启用 Redis 持久性：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

如何使用 Azure 缓存进行雷定导出：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 执行完整的系统备份并备份任何客户托管密钥

**指导**：启用 Redis 持久性。 使用 Redis 持久性可以在 Redis 中持久存储数据。 还可以获取快照并备份数据，以便在出现硬件故障时进行加载。 这相对于基本级别或标准级别是一项巨大优势，因为基本级别或标准级别将所有数据存储在内存中，在出现故障的情况下，如果缓存节点停机，则可能导致数据丢失。

您还可以使用 Azure 缓存进行雷瑞斯导出。 使用导出可以将 Azure Redis 缓存中存储的数据导出到与 Redis 兼容的 RDB 文件。 可以使用此功能将一个 Azure Redis 缓存实例中的数据移到另一个 Azure Redis 缓存实例或另一个 Redis 服务器。 在导出过程中，在承载 Redis 服务器实例的 Azure 缓存的虚拟机上创建一个临时文件，并将该文件上载到指定的存储帐户。 导出操作完成后，无论状态为成功还是失败，都会删除临时文件。

如果使用 Azure 密钥保管库为 Redis 实例存储 Azure 缓存的凭据，请确保定期自动备份密钥。

如何启用 Redis 持久性：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

如何使用 Azure 缓存进行雷定导出：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

如何备份密钥保管库密钥：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户托管密钥

**指南**： 使用 Azure 缓存进行雷定导入。 导入可用于从在任何云或环境中运行的任何 Redis 服务器（包括在 Linux、Windows 或任何云提供商（如 Amazon Web 服务和其他）上运行的 Redis 服务器中引入与 Redis 兼容的 RDB 文件。 导入数据是使用预先填充的数据创建缓存的简单方式。 在导入过程中，Azure Redis 缓存从 Azure 存储将 RDB 文件加载到内存中，再将密钥插入到缓存中。

定期测试 Azure 密钥保管库机密的数据还原。

如何使用 Azure 缓存进行雷瑞斯导入：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

如何恢复密钥保管库机密：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理密钥

**指南**： 来自 Redis 导出和 Redis 持久性的 Redis 备份的 Azure 缓存存储在所选的 Azure 存储帐户中。 Azure 存储中的数据将使用 256 位 AES 加密法（可用的最强大块加密法之一）以透明方式进行加密和解密，并符合 FIPS 140-2 规范。 无法禁用 Azure 存储加密。 可以依赖于使用 Microsoft 托管的密钥来加密存储帐户，或者，可以使用自己的密钥来管理加密。

了解 Azure 存储帐户的加密：https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Azure 安全中心监视**：是

**责任**： 微软

## <a name="incident-response"></a>事件响应

*有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指南

**指导**：为您的组织构建事件响应指南。 确保有书面事件响应计划，定义人员的所有角色以及事件处理/管理阶段，从检测到事件后审查。

如何在 Azure 安全中心内配置工作流自动化：

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

有关构建您自己的安全事件响应流程的指导：

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

微软安全响应中心的事件剖析：

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

客户还可以利用 NIST 的计算机安全事件处理指南来帮助创建他们自己的事件响应计划：

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2： 创建事件评分和优先级处理过程

**指南**：安全中心为每个警报分配严重性，以帮助您优先处理应首先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，清楚地标记订阅（对于前 生产，非 prod），并创建命名系统以明确识别和分类 Azure 资源。

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="103-test-security-response-procedures"></a>10.3： 测试安全响应程序

**指导**：进行练习，以常规节奏测试系统的事件响应能力。 找出薄弱环节和差距，并根据需要修订计划。

请参阅 NIST 出版物：IT 计划和功能测试、培训和锻炼计划指南：

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并配置安全事件的警报通知

**指南**：如果 Microsoft 安全响应中心 （MSRC） 发现客户的数据已被非法或未经授权的方访问，Microsoft 将使用安全事件联系信息与您联系。  事后查看事件，以确保问题得到解决。

如何设置 Azure 安全中心安全联系人：

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5： 将安全警报纳入事件响应系统

**指南**：使用"连续导出"功能导出 Azure 安全中心警报和建议。 "连续导出"允许您手动或持续、持续的方式导出警报和建议。 您可以使用 Azure 安全中心数据连接器来流式传输警报哨兵。

如何配置连续导出：

https://docs.microsoft.com/azure/security-center/continuous-export

如何将警报流式传输到 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6： 自动响应安全警报

**指南**：使用 Azure 安全中心的工作流自动化功能，通过安全警报和建议上的"逻辑应用"自动触发响应。

如何配置工作流自动化和逻辑应用：

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

*有关详细信息，请参阅[安全控制：渗透测试和红队练习](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：对 Azure 资源进行定期渗透测试，并确保在 60 天内补救所有关键安全发现

**指南**： 遵循 Microsoft 参与规则，确保您的渗透测试不违反 Microsoft 策略：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

您可以找到有关 Microsoft 针对 Microsoft 管理的云基础架构、服务和应用程序进行红色团队和实时站点渗透测试的战略和执行的详细信息，如下所示： 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心监视**：不适用

**责任**： 共享

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 了解有关[Azure 安全基线的更多信息](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
