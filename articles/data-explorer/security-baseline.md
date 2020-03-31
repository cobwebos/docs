---
title: 数据资源管理器的 Azure 安全基线
description: 数据资源管理器的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 92a607e4c17a16d07f07c97f626ed98a213d509b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289714"
---
# <a name="azure-security-baseline-for-data-explorer"></a>数据资源管理器的 Azure 安全基线

数据资源管理器的 Azure 安全基线包含可帮助您改进部署安全状态的建议。

此服务的基线取自[Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，它提供了有关如何使用最佳实践指南在 Azure 上保护云解决方案的建议。

有关详细信息，请参阅[Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

*有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虚拟网络上的网络安全组或 Azure 防火墙保护资源

**指南**：Azure 数据资源管理器支持将群集部署到虚拟网络中的子网。 此功能使您能够在 Azure 数据资源管理器群集流量上强制实施网络安全组 （NSG） 规则，将本地网络连接到 Azure 数据资源管理器群集的子网，以及使用服务终结点。

如何将 Azure 数据资源管理器群集部署到虚拟网络中：https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 监视和记录 Vnet、子网和 NICS 的配置和流量

**指南**：启用网络安全组 （NSG） 流日志并将日志发送到存储帐户进行流量审核。

如何启用 NSG 流日志：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

了解 Azure 安全中心提供的网络安全：https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="13-protect-critical-web-applications"></a>1.3： 保护关键 Web 应用程序

**指导**：不适用;建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒绝与已知恶意 IP 地址的通信

**指南**：在虚拟网络上启用 Azure DDoS 保护标准，保护您的数据资源管理器群集，以抵御 DDoS 攻击。 使用 Azure 安全中心集成威胁智能拒绝与已知恶意或未使用的 Internet IP 地址的通信。

如何配置 DDoS 保护：https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

了解 Azure 安全中心集成威胁情报：https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5： 记录网络数据包和流日志

**指南**：在用于保护 Azure 数据资源管理器群集的网络安全组 （NSG） 上启用流日志，并将日志发送到存储帐户以进行流量审核。

如何启用 NSG 流日志：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6： 部署基于网络的入侵检测/入侵防御系统

**指导**：不适用;此控件在终结点或防火墙上完成。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7： 管理 Web 应用程序的流量

**指导**：不适用;此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络服务标记定义与 Azure 数据资源管理器群集关联的网络安全组或 Azure 防火墙上的网络访问控件。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。

了解和使用服务标记：https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Azure 数据资源管理器的服务标记配置要求：https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 维护网络设备的标准安全配置

**指导**：客户使用 Azure 策略定义和实施网络资源的标准安全配置。

客户还可以使用 Azure 蓝图通过在单个蓝图定义中打包关键环境工件（如 ARM 模板、RBAC 控件和策略）来简化大规模 Azure 部署。 轻松将蓝图应用于新订阅和环境，并通过版本控制微调控制和管理。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何创建 Azure 蓝图：https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="110-document-traffic-configuration-rules"></a>1.10： 文档流量配置规则

**指南**：对网络安全组 （NSG） 和其他与数据资源管理器群集的网络安全和流量流相关的资源使用标记。 对于单个 NSG 规则，使用"描述"字段指定允许流量从网络传输的任何规则的业务需求和/或持续时间（等）。

如何创建和使用标记：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自动工具监控网络资源配置并检测更改

**指导**：使用 Azure 策略验证（和/或修复）网络资源的配置。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="logging-and-monitoring"></a>日志记录和监视

*有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的时间同步源

**指南**：Microsoft 维护 Azure 资源的时间源，客户可以更新客户拥有的计算部署的时间同步。

如何为 Azure 计算资源配置时间同步：https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全日志管理

**指南**： Azure 数据资源管理器使用诊断日志来深入了解引入成功和失败。 可将操作日志导出到 Azure 存储、事件中心或 Log Analytics 以监视引入状态。

如何监视 Azure 数据资源管理器引入操作：

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

如何在 Azure 数据资源管理器中引入和查询监视数据：

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：为 Azure 数据资源管理器启用诊断设置，以便访问和日志记录以服务特定操作和日志记录。 Azure 监视器中的 Azure 活动日志（包括有关资源的高级日志记录）默认情况下处于启用状态。

如何监视 Azure 数据资源管理器引入操作：https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

如何使用 Azure 监视器收集平台日志和指标：https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Azure 平台日志概述：https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="24-collect-security-logs-from-operating-system"></a>2.4： 从操作系统收集安全日志

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全日志存储保留

**指南**：在 Azure 监视器中，根据组织的合规性法规设置日志分析工作区保留期。 使用 Azure 存储帐户进行长期/存档存储。

如何为日志分析工作区设置日志保留参数：https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="26-monitor-and-review-logs"></a>2.6： 监视和审阅日志

**指导**：分析并监控日志是否存在异常行为，并定期查看结果。 为 Azure 数据资源管理器启用诊断设置后，使用 Azure 监视器的日志分析工作区查看日志并执行日志数据的查询。

了解日志分析工作区：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 监视器中执行自定义查询：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 启用异常活动警报

**指导**：不适用;Azure 数据资源管理器没有此功能。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反恶意软件日志记录

**指导**：不适用;Azure 数据资源管理器不处理反恶意软件日志记录。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9： 启用 DNS 查询日志记录

**指南**：不适用：DNS 查询不是 Azure 数据资源管理器的函数。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10： 启用命令行审核日志记录

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

*有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1： 维持管理账户清单

**指南**：在 Azure 数据资源管理器中，安全角色定义哪些安全主体（用户和应用程序）有权对安全资源（如数据库或表）进行操作，以及允许执行哪些操作。  可以利用 Kusto 查询来列出 Azure 数据资源管理器群集和数据库的管理角色中的原则。
使用 Kusto 查询在 Azure 数据资源管理器中的安全角色管理：https://docs.microsoft.com/azure/kusto/management/security-roles



**Azure 安全中心监视**：是

**责任**： 客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2： 在适用的情况下更改默认密码

**指南**：Azure AD 没有默认密码的概念。 需要密码的其他 Azure 资源强制创建具有复杂性要求和最小密码长度的密码，具体取决于服务。 您负责可能使用默认密码的第三方应用程序和市场服务。


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3： 确保使用专用管理账户

**指导**：客户围绕使用专用管理账户制定规范的操作程序。 使用 Azure 安全中心标识和访问管理监视管理帐户数。

客户还可以使用适用于 Microsoft 服务的 Azure AD 特权标识管理特权角色和 Azure ARM 启用"及时/刚刚足够"访问。 

什么是 Azure AD 特权标识管理？https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用 Azure 活动目录的单一登录 （SSO）

**指南**：在可能的情况下，客户将 SSO 与 Azure 活动目录 （Azure AD） 一起使用，而不是配置每个服务的个人独立凭据。 使用 Azure 安全中心标识和访问管理建议。

使用 Azure AD 了解 SSO：https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure 活动目录的访问使用多重身份验证。

**指南**：启用 Azure 活动目录 （Azure AD） 多重身份验证 （MFA）， 并遵循 Azure 安全中心标识和访问管理建议。

如何在 Azure 中启用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

如何在 Azure 安全中心内监视标识和访问：https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用配置为登录和配置 Azure 资源的多重身份验证 （MFA） 的 PAW（特权访问工作站）。

了解特权访问工作站：https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中启用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7： 记录和警报管理帐户上的可疑活动

**指南**：当环境中发生可疑或不安全活动时，请使用 Azure 活动目录 （Azure AD） 安全报告生成日志和警报。 使用 Azure 安全中心监视标识和访问活动

如何识别标记为有风险活动的 Azure AD 用户：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

如何监视 Azure 安全中心中的用户标识和访问活动：https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8：仅从已批准的位置管理 Azure 资源

**指南**：客户使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

如何在 Azure 中配置命名位置：https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="39-utilize-azure-active-directory"></a>3.9： 利用 Azure 活动目录

**指南**： Azure 活动目录 （Azure AD） 是验证 Azure 数据资源管理器的首选方法。 它支持多种身份验证方案：

用户身份验证（交互式登录）：用于验证人工主体。

应用程序身份验证（非交互式登录）：用于对必须在不存在人工用户的情况下运行/身份验证的服务和应用程序进行身份验证。

Azure 数据资源管理器访问控制概述：https://docs.microsoft.com/azure/kusto/management/access-control

使用 Azure 活动目录进行身份验证：https://docs.microsoft.com/azure/kusto/management/access-control/aad



**Azure 安全中心监视**：是

**责任**： 客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期查看和协调用户访问

**指南**： Azure 活动目录 （Azure AD） 提供日志以帮助发现陈旧的帐户。 此外，使用 Azure 标识访问审核可高效地管理组成员身份、对企业应用程序的访问和角色分配。 可以定期查看用户的访问权限，以确保只有正确的用户才能继续访问。 

如何使用 Azure AD 进行 Azure 数据资源管理器访问：https://docs.microsoft.com/azure/kusto/management/access-control/how-to-authenticate-with-aad

Azure 广告报告：https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 标识访问审核：https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 监视访问已停用帐户的尝试

**指南**：您可以使用 Azure 活动目录 （Azure AD） 登录活动、审核和风险事件日志源进行监视，以便与任何安全信息和事件管理 （SIEM） / 监视工具集成。

 您可以通过为 Azure 活动目录用户帐户创建诊断设置、将审核日志和登录日志发送到日志分析工作区来简化此过程。 客户在日志分析工作区中配置所需的警报。

如何将 Azure 活动日志集成到 Azure 监视器中：https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帐户登录行为偏差警报

**指南**：使用 Azure 活动目录 （Azure AD） 风险检测和身份保护功能配置自动响应，以识别与用户身份相关的可疑操作。 此外，您可以将数据引入 Azure Sentinel 以进行进一步调查。

如何查看 Azure AD 风险登录：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和启用身份保护风险策略：https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支持方案期间向 Microsoft 提供对相关客户数据的访问

**指南**：在 Microsoft 需要访问客户数据的支持方案中，客户密码箱提供了一个界面，供客户审阅、批准或拒绝客户数据访问请求。

了解客户密码箱：

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="data-protection"></a>数据保护

*有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 维护敏感信息清单

**指南**：使用标记来帮助跟踪存储或处理敏感信息的 Azure 资源。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 Azure 数据资源管理器群集应通过虚拟网络/子网与其他资源分开，在网络安全组 （NSG） 或 Azure 防火墙中正确标记和保护。 存储或处理敏感数据的数据资源管理器群集应足够隔离。

如何创建其他 Azure 订阅：https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组：https://docs.microsoft.com/azure/governance/management-groups/create

如何创建和使用标记：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何将 Azure 数据资源管理器群集部署到虚拟网络中：https://docs.microsoft.com/azure/data-explorer/vnet-deployment

如何创建具有安全配置的 NSG：https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 监控和阻止未经授权传输敏感信息

**指导**：不适用;对于由 Microsoft 管理的基础平台，Microsoft 将所有客户内容视为敏感内容，并竭尽全力防止客户数据丢失和暴露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已经实施并维护了一套强大的数据保护控制和功能。

了解 Azure 中的客户数据保护：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**： 默认情况下，Azure 数据资源管理器群集协商 TLS 1.2。 确保连接到 Azure 资源的任何客户端能够协商 TLS 1.2 或更高。

**Azure 安全中心监视**：不适用

**责任**： 共享

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5: <div>使用活动发现工具识别敏感数据</div>

**指南**：Azure 数据资源管理器尚不可用数据标识、分类和损失预防功能。 如果需要符合性，则实施第三方解决方案。

对于由 Microsoft 管理的基础平台，Microsoft 将所有客户内容视为敏感内容，并竭尽全力防止客户数据丢失和暴露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已经实施并维护了一套强大的数据保护控制和功能。

了解 Azure 中的客户数据保护：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6: <div>使用 Azure RBAC 控制对资源的访问</div>

**指南**：Azure 数据资源管理器使您能够使用基于角色的访问控制 （RBAC） 模型控制对数据库和表的访问。 在此模型下，主体（用户、组和应用）将映射到角色。 主体可以根据分配的角色访问资源。

有关如何为 Azure 数据资源管理器配置 RBAC 的角色、权限以及说明的列表：https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基于主机的数据丢失防护来实施访问控制

**指导**：不适用;此建议用于计算资源。

Microsoft 管理 Azure 数据资源管理器的基础基础结构，并实施了严格的控制，以防止客户数据丢失或暴露。

了解 Azure 中的客户数据保护：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 静态加密敏感信息

**指南**：Azure 磁盘加密有助于保护数据，以满足组织安全性和合规性承诺。 它为群集虚拟机的 OS 和数据磁盘提供卷加密。 它还与 Azure 密钥保管库集成，让我们可以控制和管理磁盘加密密钥和机密，并确保 VM 磁盘上的所有数据在 Azure 存储中进行静态加密。

如何为 Azure 数据资源管理器群集启用静态加密：https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 对关键 Azure 资源的更改进行日志和警报

**指南**：将 Azure 监视器与 Azure 活动日志一起用于创建 Azure 数据资源管理器群集上资源级更改时的警报。

如何为 Azure 活动日志事件创建警报：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

如何为 Azure 活动日志事件创建警报：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure 安全中心监视**：是

**责任**： 客户

## <a name="vulnerability-management"></a>漏洞管理

*有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1： 运行自动漏洞扫描工具

**指南**：按照 Azure 安全中心的建议，保护 Azure 数据资源管理器资源。

Microsoft 还会在支持 Azure 数据资源管理器的基础系统上执行漏洞管理。

了解 Azure 安全中心建议：https://docs.microsoft.com/azure/security-center/recommendations-reference

**Azure 安全中心监视**：是

**责任**： 微软

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动化操作系统修补程序管理解决方案

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自动第三方软件修补程序管理解决方案

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4： 比较背对背漏洞扫描

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5： 利用风险评级流程确定修复已发现漏洞的优先级。

**指南**：使用 Azure 安全中心提供的默认风险评级（安全分数）。
了解 Azure 安全中心安全分数：https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure 安全中心监视**：是

**责任**： 客户

## <a name="inventory-and-asset-management"></a>清单和资产管理

*有关详细信息，请参阅[安全控制：库存和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-utilize-azure-asset-discovery"></a>6.1： 利用 Azure 资产发现

**指南**：使用 Azure 资源图查询/发现订阅中的所有资源（如计算、存储、网络、端口和协议等）。 请确保租户中的适当（读取）权限，并枚举订阅中的所有 Azure 订阅以及资源。

尽管可以通过资源图发现经典 Azure 资源，但强烈建议今后创建和使用 Azure 资源管理器资源。

如何使用 Azure 资源图创建查询：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 订阅：https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

了解 Azure RBAC：https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="62-maintain-asset-metadata"></a>6.2： 维护资产元数据

**指南**：将标记应用于 Azure 资源，使元数据以逻辑方式将它们组织到分类中。

如何创建和使用标记：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3： 删除未经授权的 Azure 资源

**指南**：您可以酌情使用适当的命名约定、标记、管理组或单独的订阅来组织和跟踪资产。 您可以使用 Azure 资源图定期协调库存，并确保及时从订阅中删除未经授权的资源。 

如何创建其他 Azure 订阅：https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组：https://docs.microsoft.com/azure/governance/management-groups/create

如何创建和使用标记：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何使用 Azure 资源图创建查询：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单。

**指南**：您需要根据组织需要创建已批准的 Azure 资源和已批准的软件的清单，以便计算资源。  


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5： 监视未经批准的 Azure 资源

**指南**：您可以使用 Azure 策略对客户订阅中可以使用以下内置策略定义创建的资源类型施加限制：

    - 不允许的资源类型

    - 允许的资源类型

您将能够使用 

可以使用 Azure 监视器监视的活动日志。

此外，您可以使用 Azure 资源图查询/发现订阅中的资源。

教程：创建和管理策略以强制实施合规性：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

快速入门：使用 Azure 资源图资源管理器运行第一个资源图查询：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

使用 Azure 监视器创建、查看和管理活动日志警报：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6： 监控计算资源中的未经批准的软件应用程序

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7： 删除未经批准的 Azure 资源和软件应用程序

**指导**：不适用;此建议适用于计算资源和整个 Azure。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="68-utilize-only-approved-applications"></a>6.8： 仅利用已批准的申请

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-utilize-only-approved-azure-services"></a>6.9： 仅利用已批准的 Azure 服务

**指南**：您可以使用 Azure 策略对客户订阅中可以使用以下内置策略定义创建的资源类型施加限制：

    - 不允许的资源类型

    - 允许的资源类型

教程：创建和管理策略以强制实施合规性：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 策略示例：https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="610-implement-approved-application-list"></a>6.10： 实施已批准的申请列表

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器交互的能力

**指南**：使用 Azure 条件访问通过为"Microsoft Azure 管理"应用配置"阻止访问"来限制用户与 Azure 资源管理器交互的能力。 这将阻止 Azure 订阅中的资源的创建和更改。 

使用条件访问管理对 Azure 管理的访问：https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure 安全中心监视**：是

**责任**： 客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12： 限制用户在计算资源中执行脚本的能力

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13： 物理或逻辑隔离高风险应用程序

**指导**：不适用;此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

*有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用 Azure 策略别名创建自定义策略以审核或强制执行 Azure 资源的配置。 您还可以使用内置的 Azure 策略定义。

此外，Azure 资源管理器能够导出 JavaScript 对象符号 （JSON） 中的模板，应对其进行审查，以确保配置满足/超过组织的安全要求。

您还可以使用 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

如何查看可用的 Azure 策略别名：https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

教程：创建和管理策略以强制实施合规性：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

单个资源和多资源导出到 Azure 门户中的模板：https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

安全建议 - 参考指南：https://docs.microsoft.com/azure/security-center/recommendations-reference



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2： 为您的操作系统建立安全配置

**指导**：不适用;本指南用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3：维护所有 Azure 资源的安全配置

**指导**：使用 Azure 策略 [拒绝] 和 [部署（如果不存在））在整个 Azure 资源中强制实施安全设置。  您可以使用更改跟踪、策略合规性仪表板或自定义解决方案等解决方案轻松识别环境中的安全更改。

了解 Azure 策略效果：https://docs.microsoft.com/azure/governance/policy/concepts/effects

创建和管理策略以强制实施合规性：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

使用"更改跟踪"解决方案跟踪环境中的更改：https://docs.microsoft.com/azure/automation/change-tracking

获取 Azure 资源的合规性数据：https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4： 维护操作系统的安全配置

**指导**：不适用;本指南用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存储 Azure 资源配置

**指南**：使用 Azure 存储库安全地存储和管理代码，如自定义 Azure 策略、Azure 资源管理器模板、所需状态配置脚本等。要访问在 Azure DevOps 中管理的资源，可以授予或拒绝特定用户、内置安全组或与 Azure DevOps 集成或在 Azure 活动目录（如果与 TFS 集成）中定义的组的权限。

如何在 Azure DevOps 中存储代码：https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

有关 Azure DevOps 中的权限和组：https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存储自定义操作系统映像

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系统配置管理工具

**指南**：使用 Azure 策略定义和实现 Azure 资源的标准安全配置。 使用 Azure 策略别名创建自定义策略以审核或强制执行 Azure 资源的网络配置。 您还可以使用与您的特定资源相关的内置策略定义。  此外，您可以使用 Azure 自动化来部署配置更改。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用别名：https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的系统配置管理工具

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9： 实现 Azure 服务的自动配置监视

**指南**：使用 Azure 策略别名创建自定义策略以警报、审核和强制执行系统配置。 使用 Azure 策略 [审核]、[拒绝]和 [部署（如果不存在））自动强制 Azure 资源配置。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 实现操作系统的自动配置监控

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-securely-manage-azure-secrets"></a>7.11： 安全管理 Azure 机密

**指南**：Azure 磁盘加密为 Azure 数据资源管理器群集虚拟机的操作系统和数据磁盘提供卷加密。 它还与 Azure 密钥保管库集成，它允许您控制和管理磁盘加密密钥和机密，并确保在 Azure 存储中静态时对 VM 磁盘上的所有数据进行加密。

如何在 Azure 数据资源管理器中保护群集：https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Azure 安全中心监视**：当前不可用

**责任**： 微软

### <a name="712-securely-and-automatically-manage-identities"></a>7.12： 安全自动管理身份

**指南**：使用托管标识在 Azure AD 中为 Azure 服务提供自动托管标识。 托管标识允许您对支持 Azure AD 身份验证的任何服务（包括密钥保管库）进行身份验证，而无需在代码中进行任何凭据。如何配置托管标识：https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

为 Azure 数据资源管理器群集配置托管标识：https://docs.microsoft.com/azure/data-explorer/managed-identities


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13： 消除意外的凭据暴露

**指南**：实现凭据扫描程序以标识代码中的凭据。 凭据扫描程序还将鼓励将发现的凭据移动到更安全的位置，如 Azure 密钥保管库。 

如何设置凭据扫描程序：https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="malware-defense"></a>恶意软件防护

*有关详细信息，请参阅[安全控制：恶意软件防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1： 利用集中管理的反恶意软件

**指导**：不适用;此建议用于计算资源。

在支持 Azure 服务的基础主机（例如 Azure 数据资源管理器）上启用 Microsoft 反恶意软件，但不在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：要上载到非计算 Azure 资源的预扫描文件

**指南**：在支持 Azure 服务的基础主机上启用 Microsoft 反恶意软件（例如，Azure 数据资源管理器），但它不在客户内容上运行。

预扫描上载到非计算 Azure 资源的任何内容，如 Azure 数据资源管理器、数据湖存储、Blob 存储、用于 PostgreSQL 的 Azure 数据库等。在这种情况下，Microsoft 无法访问您的数据。



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3： 确保更新反恶意软件和签名

**指导**：不适用;此建议用于计算资源。

Microsoft 反恶意软件在支持 Azure 服务的基础主机上启用，但不在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

*有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1： 确保定期自动备份

**指南**：数据资源管理器群集使用的 Microsoft Azure 存储帐户中的数据始终被复制，以确保持久性和高可用性。 Azure 存储功能会复制数据，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 可以选择在同一数据中心中、跨同一区域中的局域数据中心或跨地理上隔离的区域复制数据。

了解 Azure 存储冗余和服务级别协议：https://docs.microsoft.com/azure/storage/common/storage-redundancy

将数据导出到存储 ：https://docs.microsoft.com/azure/kusto/management/data-export/export-data-to-storage



**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 执行完整的系统备份并备份任何客户托管密钥

**指南**：Azure 数据资源管理器在静态存储帐户中加密所有数据。 默认情况下，使用 Microsoft 管理的密钥对数据进行加密。 为了更进一步控制加密密钥，可以提供客户管理的密钥来用于对数据进行加密。 客户管理的密钥必须存储在 Azure密钥保管库中。 

使用 C# 配置客户托管密钥 ：https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

使用 Azure 资源管理器模板配置客户管理密钥：https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

如何备份 Azure 密钥保管库证书：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户托管密钥

**指南**：定期测试 Azure 密钥保管库机密的数据还原。

如何还原 Azure 密钥保管库证书：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

使用 C# 配置客户托管密钥 ：https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

使用 Azure 资源管理器模板配置客户管理密钥：https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理密钥

**指南**： 客户在密钥保管库中启用软删除，以保护密钥免受意外或恶意删除。  您还可以启用清除保护，以便如果保管库或对象处于已删除状态，则在保留期结束之前无法清除它。  

如何在密钥保管库中启用软删除和清除保护：https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

使用 C# 配置客户托管密钥 ：https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

使用 Azure 资源管理器模板配置客户管理密钥：https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Azure 安全中心监视**：是

**责任**： 客户

## <a name="incident-response"></a>事件响应

*有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-incident-response-guide"></a>10.1：创建事件响应指南

**指导**：为您的组织构建事件响应指南。 确保有书面事件响应计划，定义人员的所有角色以及事件处理/管理阶段，从检测到事件后审查。
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

    



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级处理过程

**指南**：安全中心为每个警报分配严重性，以帮助您优先处理应首先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

    Use tags to organize your Azure resources: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure 安全中心监视**：是

**责任**： 客户

### <a name="103-test-security-response-procedures"></a>10.3： 测试安全响应程序

**指南**：执行练习，以常规节奏测试系统的事件响应功能，以帮助保护 Azure 资源。 找出薄弱环节和差距，并根据需要修订计划。
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4： 提供安全事件联系人详细信息，并配置安全&nbsp;事件的警报通知

**指南**：如果 Microsoft 安全响应中心 （MSRC） 发现您的数据已被非法或未经授权的方访问，Microsoft 将使用安全事件联系信息与您联系。 事后查看事件，以确保问题得到解决。
    

如何设置 Azure 安全中心安全联系人：https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5： 将安全警报纳入事件响应系统

**指南**：使用"连续导出"功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 "连续导出"允许您手动或持续、持续的方式导出警报和建议。 您可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure 哨兵。
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6： 自动响应安全警报

**指南**：使用 Azure 安全中心的工作流自动化功能通过安全警报和建议上的"逻辑应用"自动触发响应，以保护 Azure 资源。
    

    How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation



**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

*有关详细信息，请参阅[安全控制：渗透测试和红队练习](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1：对 Azure 资源进行定期渗透测试，并确保在 60 天内修复所有关键安全发现

**指南**：请遵循 Microsoft 的接用规则，以确保您的渗透测试不违反 Microsoft 策略https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1： .

您可以在此处找到有关 Microsoft 针对 Microsoft 托管云基础架构、服务和应用程序进行红色团队和实时站点渗透测试的战略和实施的详细信息：https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心监视**：不适用

**责任**： 共享

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 了解有关[Azure 安全基线的更多信息](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
