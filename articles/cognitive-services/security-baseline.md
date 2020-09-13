---
title: 认知服务的 Azure 安全基线
description: 认知服务的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 796ef0b82c15940ce9200e0b50916ca657f10b99
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89393887"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>认知服务的 Azure 安全基线

认知服务的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全性

有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**： Azure 认知服务提供分层的安全模型。 借助此模型，可保护认知服务帐户，使其可供网络的特定子集访问。 配置网络规则后，仅通过指定网络集请求数据的应用程序才能访问帐户。 可以使用请求筛选来限制对资源的访问，仅允许来自指定 IP 地址、IP 范围或来自 Azure 虚拟网络中的子网列表的请求。

针对认知服务的虚拟网络和服务终结点支持仅限于一组特定的区域。

* [如何配置 Azure 认知服务虚拟网络](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)

* [Azure 虚拟网络概述](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指南**：当虚拟机部署在与 Azure 认知服务容器相同的虚拟网络中时，可以使用 (NSG) 的网络安全组，以降低数据渗透的风险。 启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

* [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：如果你在某个容器中使用认知服务，则可以使用面向正面的 web 应用程序防火墙解决方案来补充你的容器部署，该解决方案筛选恶意流量并支持端到端 TLS 加密，使容器终结点保密且安全。

请记住，出于计费目的，要求认知服务容器提交计量信息。 唯一例外是脱机容器，因为后者遵循不同的计费方法。 如果无法允许列出认知服务容器依赖的各种网络通道，则容器不能正常运行。 主机应该允许列出端口 443 和以下域：
- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

另请注意，必须在认知服务容器为 Microsoft 服务器创建的安全通道上禁用防火墙解决方案的深度数据包检查。 如果不能这样做，则容器无法正常运行。

* [了解 Azure 认知服务容器安全性](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：当虚拟机部署在与 Azure 认知服务容器相同的虚拟网络中时，请通过 azure 策略为相关网络资源定义和实施标准安全配置。 使用 "Cognitiveservices account" 和 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 Azure 缓存的 Redis 实例的网络配置。 还可以利用内置策略定义，例如：
- 应启用 DDoS 防护标准版

你还可以使用 Azure 蓝图通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure 基于角色的访问控制 (Azure RBAC) 和策略）来简化大规模的 Azure 部署。 轻松将蓝图应用到新的订阅和环境，并通过版本控制来微调控制措施和管理。

如果在容器中使用认知服务，则可以使用面向正面的 web 应用程序防火墙解决方案来增强容器部署，该解决方案可筛选恶意流量并支持端到端 TLS 加密，使容器终结点成为私有终结点并确保其安全。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何创建 Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

* [了解 Azure 认知服务容器安全性](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：当虚拟机部署在与 Azure 认知服务容器相同的虚拟网络中时，可以使用 (NSG) 的网络安全组，以降低数据渗透的风险。 启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

* [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：如果在容器中使用认知服务，则可以使用面向正面的 web 应用程序防火墙解决方案来增强容器部署，该解决方案可筛选恶意流量并支持端到端 TLS 加密，使容器终结点成为私有终结点并确保其安全。 你可以从支持 ID/IPS 功能的 Azure Marketplace 中选择一个产品/服务，并且能够禁用有效负载检查。

请记住，出于计费目的，要求认知服务容器提交计量信息。 唯一的例外是脱机容器，因为它们遵循不同的计费方法。 如果无法允许列出认知服务容器依赖的各种网络通道，则容器不能正常运行。 主机应该允许列出端口 443 和以下域：
- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

另请注意，必须在认知服务容器为 Microsoft 服务器创建的安全通道上禁用防火墙解决方案的深度数据包检查。 如果不能这样做，则容器无法正常运行。

* [了解 Azure 认知服务容器安全性](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

* [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：如果在容器中使用认知服务，则可以使用面向正面的 web 应用程序防火墙解决方案来增强容器部署，该解决方案可筛选恶意流量并支持端到端 TLS 加密，使容器终结点成为私有终结点并确保其安全。

请记住，出于计费目的，要求认知服务容器提交计量信息。 唯一例外是脱机容器，因为后者遵循不同的计费方法。 如果无法允许列出认知服务容器依赖的各种网络通道，则容器不能正常运行。 主机应该允许列出端口 443 和以下域：
- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

另请注意，必须在认知服务容器为 Microsoft 服务器创建的安全通道上禁用防火墙解决方案的深度数据包检查。 如果不能这样做，则容器无法正常运行。

* [了解 Azure 认知服务容器安全性](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络服务标记来定义网络安全组 (NSG) 或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

你还可以使用应用程序安全组 (ASG) 来帮助简化复杂的安全配置。 Asg 使你能够将网络安全性配置为应用程序结构的自然扩展，使你能够对虚拟机进行分组，并基于这些组定义网络安全策略。

* [虚拟网络服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [应用程序安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现与 Azure 认知服务容器结合 azure 策略的网络资源的标准安全配置。 使用 "Cognitiveservices account" 和 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 Azure 缓存的 Redis 实例的网络配置。

你还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure 基于角色的访问控制 (Azure RBAC) 和策略）来简化大规模的 Azure 部署。 轻松将蓝图应用到新的订阅和环境，并通过版本控制来微调控制措施和管理。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何创建 Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对与 Azure 认知服务容器关联的网络资源使用标记，以便以逻辑方式将它们组织到分类。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 azure 活动日志监视网络资源配置，并检测与 Azure 认知服务容器相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

* [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [如何在 Azure Monitor 中创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 在日志中维护用于 Azure 认知服务的 azure 资源（例如 Azure 认知服务）的时间源。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：启用 Azure 活动日志诊断设置，并将日志发送到 Log Aalytics 工作区、Azure 事件中心或 Azure 存储帐户进行存档。 活动日志可让你深入了解在控制平面级别对 Azure 认知服务容器执行的操作。 使用 Azure 活动日志数据，可以确定任何写入操作的 "内容、人员和时间"，这些操作 (PUT、POST、DELETE) 在 Azure Cache for Redis 实例的控制平面级别执行。

* [如何启用 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：对于控制平面审核日志记录，请启用 Azure 活动日志诊断设置，并将日志发送到 Log Aalytics 工作区、Azure 事件中心或 Azure 存储帐户进行存档。 使用 Azure 活动日志数据，可以确定在控制平面级别针对 Azure 资源执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

此外，Azure 认知服务还发送诊断事件，这些事件可收集并用于分析、警报和报告目的。 可以通过 Azure 门户配置认知服务容器的诊断设置。 可以将一个或多个诊断事件发送到存储帐户、事件中心或 Log Analytics 工作区。

* [如何启用 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [将诊断设置用于 Azure 认知服务](https://docs.microsoft.com/azure/cognitive-services/diagnostic-logging)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的合规性规则设置 Log Analytics 工作区保持期。 使用 Azure 存储帐户进行长期/存档存储。

* [如何为 Log Analytics 工作区设置日志保留参数](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区。 这些日志提供频繁生成的有关资源操作的丰富数据用于识别问题和调试。 在 Log Analytics 中执行查询，以搜索术语，确定趋势，分析模式，并根据可能已为 Azure 认知服务收集的活动日志数据提供许多其他见解。

* [如何启用 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何收集和分析 Azure Monitor 的 Log Analytics 工作区中的 Azure 活动日志](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：通过转到 Azure Monitor 中的 "警报指标" 部分，可以对 Azure 认知服务中支持的指标发出警报 &amp; 。

为认知服务容器配置诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 工作区中，配置发生一组预定义的条件时要触发的警报。 或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [使用 Azure Monitor 创建、查看和管理日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：不适用;Azure 认知服务不会处理或产生与反恶意软件相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：不适用;Azure 认知服务不会处理或产生 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Azure Active Directory (AD) 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

* [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**：控制平面访问 Azure 认知服务是通过 AZURE ACTIVE DIRECTORY (AD) 控制的。 Azure AD 没有默认密码。

通过访问密钥控制对 Azure 认知服务的数据平面访问。 这些密钥由连接到缓存的客户端使用，可以随时重新生成。

不建议在应用程序中构建默认密码。 相反，你可以将你的密码存储在 Azure Key Vault 中，然后使用 Azure Active Directory 来检索它们。

* [如何重新生成 Redis 访问密钥的 Azure 缓存](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：
- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

* [如何使用 Azure 安全中心监视标识和访问（预览）](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [如何使用 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指南**： Azure 认知服务使用访问密钥对用户进行身份验证，而不支持在数据平面级别 (SSO) 进行单一登录。 可以通过 REST API 访问 Azure 认知服务的控制平面，并支持 SSO。 若要进行身份验证，请将请求的授权标头设置为从 Azure Active Directory (AAD) 获取的 JSON Web 令牌。

* [了解 Azure 认知服务 REST API](https://docs.microsoft.com/rest/api/cognitiveservices/)

* [了解 Azure AD 的 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory (AD) 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理的建议。

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [如何在 Azure 安全中心监视标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用配置了多重身份验证 (MFA) 的特权访问工作站 (PAW) 来登录并配置 Azure 资源。

* [了解特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (AD) Privileged Identity Management (PIM) 生成日志和警报。

此外，还可使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

* [如何部署 Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [了解 Azure AD 风险检测](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：在 AZURE ACTIVE DIRECTORY (AD) 条件访问中配置命名位置，以仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问。

* [如何在 Azure 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

指南：使用 Azure Active Directory (AD) 作为中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中的数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。 如果用例支持 AD 身份验证，请使用 Azure AD 对认知服务 API 的请求进行身份验证。

目前，只有计算机视觉 API、人脸 API、文本分析 API、沉浸式读取器、窗体识别器、异常探测器和所有必应服务（必应自定义搜索使用 Azure AD 支持身份验证除外）。

* [如何对认知服务请求进行身份验证](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-azure-active-directory)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：Azure AD 提供日志来帮助发现过时的帐户。 此外，客户可以使用 Azure 标识访问评审来有效地管理组成员身份、访问企业应用程序和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

客户要维护 API 管理用户帐户的清单，请根据需要协调访问权限。 在 API 管理中，开发人员是使用 API 管理公开的 API 的用户。 默认情况下，新创建的开发人员帐户处于“活动”状态，并且与“开发人员”组相关联。 处于“活动”状态的开发人员帐户可用于访问他们具有订阅的所有 API。

* [如何在 Azure API 管理中管理用户帐户](https://docs.microsoft.com/azure/api-management/api-management-howto-create-or-invite-developers)

* [如何获取 API 管理用户的列表](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [如何使用 Azure 标识访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：你有权访问 AZURE ACTIVE DIRECTORY (AD) 登录活动、审核和风险事件日志源，从而允许你与 Azure Sentinel 或第三方 SIEM 集成。

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 可以在 Log Analytics 中配置所需的日志警报。

* [如何将 Azure 活动日志集成到 Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：对于控制平面上的帐户登录行为偏差，使用 AZURE ACTIVE DIRECTORY (AD) Identity Protection 和风险检测功能，配置对检测到的与用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 中以便进一步调查。

* [如何查看 Azure AD 风险登录](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [如何配置和启用标识保护风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：尚不可用;Azure 认知服务尚不支持客户密码箱。

* [支持客户密码箱的服务列表](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：目前不可用

**责任**：目前不可用

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实现单独的订阅和/或管理组。 资源应当按 VNet/子网进行分隔，相应地进行标记，并由 NSG 或 Azure 防火墙提供保护。 存储或处理敏感数据的资源应当充分隔离。 对于存储或处理敏感数据的虚拟机，请实施相应的策略和过程，以在不使用这些虚拟机时将其关闭。

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何创建采用安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [如何部署 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [如何通过 Azure 防火墙配置“警报”或“发出警报并拒绝”](https://docs.microsoft.com/azure/firewall/threat-intel)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：尚不可用;对于 Azure 认知服务，数据识别、分类和丢失防护功能尚不可用。

Microsoft 管理 Azure 认知服务的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：目前不可用

**责任**：目前不可用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：通过 HTTP 公开的所有认知服务终结点强制 TLS 1.2。 使用强制执行的安全协议时，尝试调用认知服务终结点的使用者应遵循以下准则：
- 客户端操作系统 (操作系统) 需要支持 TLS 1.2。
- 用于执行 HTTP 调用的语言 (和平台) 需要将 TLS 1.2 指定为请求的一部分。  (根据语言和平台，指定 TLS 是隐式还是显式完成。 ) 

* [了解 Azure 认知服务的传输层安全性](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**： Azure 认知服务尚不支持数据标识、分类和丢失防护功能。 标记包含敏感信息的实例，并根据需要实现第三方解决方案（如符合性目的）。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指南**：使用 azure RBAC) 的 azure 基于角色的访问控制来控制对 Azure 认知服务控制平面的访问 (即 Azure 门户)  (。

* [如何配置 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**：不适用；此建议适用于计算资源。

Microsoft 管理 Azure 认知服务的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：针对认知服务的静态加密依赖于所使用的特定服务。 在大多数情况下，使用 FIPS 140-2 兼容的256位 AES 加密对数据进行加密和解密。 加密和解密都是透明的，这意味着将替你管理加密和访问。 默认情况下，你的数据是安全的，无需修改你的代码或应用程序即可利用加密。

你还可以使用 Azure Key Vault 来存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。

* [加密静态信息的服务列表](https://docs.microsoft.com/azure/cognitive-services/encryption/cognitive-services-encryption-keys-portal)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 azure 活动日志结合使用，以创建有关 Azure 认知服务生产实例发生更改的警报，以及其他关键或相关资源。

* [如何针对 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**： Microsoft 对支持 Azure 认知服务的基础系统执行漏洞管理。

**Azure 安全中心监视**：目前不可用

**责任**：Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**： Microsoft 对支持 Azure 认知服务的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

* [如何使用 Azure Resource Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪 Azure Cache for Redis 实例和相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已获批 Azure 资源的清单

**指导**：不适用；此建议适用于计算资源和整个 Azure。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Resource Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

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

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

* [如何配置条件访问以阻止访问 Azure 资源管理器](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

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

**指南**：定义和实现 Azure 认知服务容器的标准安全配置（azure 策略）。 使用 "Cognitiveservices account" 命名空间中的 Azure 策略别名创建自定义策略，以便为 Redis 实例审核或强制执行 Azure 缓存配置。

* [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解 Azure Policy 效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果你使用的是 Azure 认知服务容器和相关资源的自定义 azure 策略定义或 azure 资源管理器模板，请使用 Azure Repos 安全地存储和管理你的代码。

* [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指南**：使用 "Microsoft Cache" 命名空间中的 Azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 另外，开发一个用于管理策略例外的流程和管道。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：使用 "cognitiveservices account" 命名空间中的 azure 策略别名创建自定义 Azure 策略定义，以对系统配置进行警报、审核和强制执行。 使用 Azure 策略 [audit]、[拒绝] 和 [部署（如果不存在））为 Redis 实例和相关资源自动强制执行 Azure 缓存配置。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：对于正在用于访问 Azure 认知服务 API Azure App Service 上运行的 azure 虚拟机或 web 应用程序，请将托管服务标识与 Azure Key Vault 结合使用，以简化和保护 Azure 认知服务密钥管理。 确保启用 Key Vault 软删除。

* [如何与 Azure 托管标识集成](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

* [如何创建 Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [如何对 Key Vault 进行身份验证](https://docs.microsoft.com/azure/key-vault/general/authentication)

* [如何分配 Key Vault 访问策略](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：对于正在用于访问 Azure 认知服务 API Azure App Service 上运行的 azure 虚拟机或 web 应用程序，请将托管服务标识与 Azure Key Vault 结合使用，以简化和保护 Azure 认知服务密钥管理。 确保启用 Key Vault 软删除。

使用托管标识在 Azure Active Directory 中通过自动管理的标识来提供 Azure 服务。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Azure Key Vault）进行身份验证，无需在代码中放入任何凭据。

* [如何配置托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

* [如何与 Azure 托管标识集成](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用；此建议适用于计算资源。

支持 Azure 服务的基础主机上启用了 Microsoft 反恶意软件 (例如，Azure 认知服务) ，但它不会在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：在支持 azure 服务的基础主机上启用了 Microsoft 反恶意软件 (例如，azure Cache for Redis) ，但它不会在客户内容上运行。

预扫描要上传到非计算 Azure 资源的任何内容，例如应用服务、Data Lake Storage、Blob 存储、Azure Database for PostgreSQL 等。Microsoft 无法访问这些实例中的数据。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用；此建议适用于计算资源。

支持 Azure 服务的基础主机上启用了 Microsoft 反恶意软件 (例如，Azure 认知服务) ，但它不会在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：始终自动复制 Microsoft Azure 存储帐户中的数据以确保持久性和高可用性。 Azure 存储功能会复制数据，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 可以选择在同一数据中心中、跨同一区域中的局域数据中心或跨地理上隔离的区域复制数据。

还可以使用生命周期管理功能将数据备份到存档层。 此外，为存储在存储帐户中的备份启用软删除。

* [了解 Azure 存储冗余和服务级别协议](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

* [管理 Azure Blob 存储生命周期](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts)

* [Azure 存储 Blob 的软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：使用 Azure 资源管理器部署认知服务和相关资源。 Azure 资源管理器提供导出模板的功能，这使你能够在整个开发生命周期内重新部署你的解决方案，并满怀信心地部署你的资源。 使用 Azure 自动化定期调用 Azure 资源管理器模板导出 API。 Azure Key Vault 中的备份预共享密钥。

* [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)

* [如何使用 Azure 资源管理器模板创建认知服务资源](https://docs.microsoft.com/azure/cognitive-services/resource-manager-template?tabs=portal)

* [在 Azure 门户中将单资源和多资源导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

* [资源组-导出模板](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate)

* [Azure 自动化简介](https://docs.microsoft.com/azure/automation/automation-intro)

* [如何在 Azure 中备份密钥保管库密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：如果需要，请确保定期定期将 Azure 资源管理器模板部署到隔离的订阅。 测试还原已备份的预共享密钥。

* [使用 ARM 模板和 Azure 门户部署资源](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal)

* [如何在 Azure 中还原密钥保管库密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：使用 azure DevOps 来安全地存储和管理 Azure 资源管理器模板。 若要保护在 Azure DevOps 中管理的资源，可以授予或拒绝特定用户、内置安全组或 Azure AD (Azure Active Directory 中定义的组) （如果与 Azure DevOps 集成），或 Active Directory 与 TFS 集成。  使用基于角色的访问控制来保护客户管理的密钥。 在密钥保管库中启用软删除和清除保护，以防止意外删除或恶意删除密钥。 

* [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [关于 Azure DevOps 中的权限和组](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

* [如何在密钥保管库中启用软删除和清除保护](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

* [如何在 Azure 安全中心配置工作流自动化](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [你还可以利用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，请明确标记订阅（例如 生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期练习以测试系统的事件响应能力。 识别弱点和差距，并根据需要修改计划。

* [请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南）

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。 事后审查事件，确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Sentinel。

* [如何配置连续导出](https://docs.microsoft.com/azure/security-center/continuous-export)

* [如何将警报流式传输到 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

* [如何配置工作流自动化和逻辑应用](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指南**：*[遵循 Microsoft Rules of Engagement，确保渗透测试不违反 Microsoft 政策](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [可在此处详细了解如何针对 Microsoft 托管云基础结构、服务和应用程序执行红队测试和实时站点渗透测试，以及 Microsoft 的相关策略](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
