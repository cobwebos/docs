---
title: 事件中心的 Azure 安全基线
description: 事件中心的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 10fc822f9f36512405dd4e3b5aba6270b53e163f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549032"
---
# <a name="azure-security-baseline-for-event-hubs"></a>事件中心的 Azure 安全基线

事件中心 Azure 安全基线包含可帮助您改进部署安全状态的建议。

此服务的基线取自[Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，它提供了有关如何使用最佳实践指南在 Azure 上保护云解决方案的建议。

有关详细信息，请参阅[Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

*有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虚拟网络上的网络安全组或 Azure 防火墙保护资源

**指南**：将事件中心与虚拟网络服务终结点集成，可以安全地访问来自工作负载（如绑定到虚拟网络的虚拟机）的消息传递功能，并且网络流量路径在两端都受到保护。

绑定到至少一个虚拟网络子网服务终结点后，相应的事件中心命名空间不再接受来自任何位置的流量，而是接受虚拟网络中的授权子网。 从虚拟网络的角度来看，将事件中心命名空间绑定到服务终结点会配置从虚拟网络子网到消息服务的隔离网络隧道。 

您还可以创建专用终结点，这是一个网络接口，通过使用 Azure 专用链接服务将您私下安全地连接到 Azure 事件中心服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 

您还可以使用防火墙保护 Azure 事件中心命名空间。 Azure 事件中心支持基于 IP 的访问控制，用于入站防火墙支持。 可以使用 Azure 门户、Azure 资源管理器模板或通过 Azure CLI 或 Azure PowerShell 设置防火墙规则。

如何使用 Azure 事件中心中的虚拟网络服务终结点：https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

有关详细信息，请参阅将 Azure 事件中心与 Azure 专用https://docs.microsoft.com/azure/event-hubs/private-link-service链接集成： 。

在事件中心命名空间上启用虚拟网络集成和防火墙：https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

如何为 Azure 事件中心命名空间配置 IP 防火墙规则：https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 监视和记录 Vnet、子网和 NIC 的配置和流量

**指南**：使用 Azure 安全中心并遵循网络保护建议来帮助保护 Azure 中的事件中心资源。 如果使用 Azure 虚拟机访问事件中心，请启用网络安全组 （NSG） 流日志并将日志发送到存储帐户以进行流量审核。

如何启用 NSG 流日志：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

了解 Azure 安全中心提供的网络安全：https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="13-protect-critical-web-applications"></a>1.3： 保护关键 Web 应用程序

**指导**：不适用;此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒绝与已知恶意 IP 地址的通信

**指南**：在与事件中心关联的虚拟网络上启用 DDoS 保护标准，以防止分布式拒绝服务 （DDoS） 攻击。 使用 Azure 安全中心集成威胁智能拒绝与已知恶意或未使用的 Internet IP 地址的通信。

如何配置 DDoS 保护：[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](/azure/virtual-network/manage-ddos-protection)

有关 Azure 安全中心集成威胁情报的详细信息：https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5： 记录网络数据包和流日志

**指南**：如果使用 Azure 虚拟机访问事件中心，请启用网络安全组 （NSG） 流日志并将日志发送到存储帐户以进行流量审核。 您还可以将 NSG 流日志发送到日志分析工作区，并使用流量分析提供有关 Azure 云中的流量流的见解。 流量分析的一些优点是能够可视化网络活动并识别热点、识别安全威胁、了解流量模式和查明网络错误配置。

如果需要调查异常活动，则启用网络观察程序数据包捕获。

如何启用 NSG 流日志：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用和使用流量分析：https://docs.microsoft.com/azure/network-watcher/traffic-analytics

如何启用网络观察程序：https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6： 部署基于网络的入侵检测/入侵防御系统（IDS/IPS）

**指南**：如果使用 Azure 虚拟机访问事件中心，请从 Azure 应用商店中选择支持具有有效负载检查功能的 IDS/IPS 功能的产品/服务。 如果组织不需要基于有效负载检查的入侵检测和/或预防，则可以使用 Azure 事件中心的内置防火墙功能。 您可以使用防火墙规则限制对有限范围 IP 地址或特定 IP 地址的活动中心命名空间的访问。

Azure 市场：

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

如何在事件中心为指定的 IP 地址添加防火墙规则：

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure 安全中心监视**：尚未可用

**责任**： 客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7： 管理 Web 应用程序的流量

**指导**：不适用;此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低网络安全规则的复杂性和管理开销

**指南**：不适用，此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 维护网络设备的标准安全配置

**指南**：使用 Azure 策略为与 Azure 事件中心命名空间关联的网络资源定义和实施标准安全配置。 在"Microsoft.EventHub"和"Microsoft.Network"命名空间中使用 Azure 策略别名来创建自定义策略以审核或强制执行事件中心命名空间的网络配置。 您还可以使用与 Azure 事件中心相关的内置策略定义，例如：

- 事件中心应使用虚拟网络服务终结点。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

事件中心命名空间的 Azure 内置策略：https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



用于网络的 Azure 策略示例：https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



如何创建 Azure 蓝图：https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="110-document-traffic-configuration-rules"></a>1.10： 文档流量配置规则

**指南**：对虚拟网络以及与事件中心关联的网络安全和流量流相关的其他资源使用标记。

如何创建和使用标记：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自动工具监控网络资源配置并检测更改

**指导**：使用 Azure 活动日志监视网络资源配置并检测与 Azure 事件中心相关的网络资源的更改。 在 Azure 监视器中创建警报，这些警报将在对关键网络资源的更改时触发。

如何查看和检索 Azure 活动日志事件：https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 监视器中创建警报：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="logging-and-monitoring"></a>日志记录和监视

*有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的时间同步源

**指导**：不适用;Microsoft 维护用于 Azure 资源（如 Azure 事件中心）的时间源，用于日志中的时间戳。

**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全日志管理

**指南**：在 Azure 监视器中，在活动日志和事件中心诊断设置中配置与事件中心相关的日志，以将日志发送到日志分析工作区以进行查询或发送到存储帐户以进行长期存档存储。

如何配置 Azure 事件中心的诊断设置：https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

了解 Azure 活动日志：https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：为 Azure 事件中心命名空间启用诊断设置。 Azure 事件中心有三类诊断设置：存档日志、操作日志和自动缩放日志。 启用操作日志来捕获有关事件中心操作期间发生的情况的信息，特别是操作类型，包括事件中心创建、使用的资源和操作的状态。

此外，您可以启用 Azure 活动日志诊断设置并将其发送到 Azure 存储帐户、事件中心或日志分析工作区。 活动日志提供对 Azure 事件中心和其他资源执行的操作的见解。 使用活动日志，可以确定 Azure 事件中心命名空间上采用的任何写入操作（PUT、POST、DELETE）的"什么、谁和时间"。

如何为 Azure 事件中心启用诊断设置：https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

如何为 Azure 活动日志启用诊断设置：https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4： 从操作系统收集安全日志

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全日志存储保留

**指南**：在 Azure 监视器中，根据组织的合规性法规设置日志分析工作区保留期，以捕获和查看事件中心相关事件。

如何为日志分析工作区设置日志保留参数：https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="26-monitor-and-review-logs"></a>2.6： 监视和查看日志

**指导**：分析和监视日志是否存在异常行为，并定期查看与事件中心相关的结果。 使用 Azure 监视器的日志分析查看日志并执行日志数据的查询。 或者，您可以将数据启用并车载到 Azure Sentinel 或第三方 SIEM。
 

有关日志分析工作区的详细信息：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 监视器中执行自定义查询：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 启用异常活动的警报

**指南**：在 Azure 监视器中，在活动日志中配置与 Azure 事件中心相关的日志，以及事件中心诊断设置，以将日志发送到日志分析工作区以进行查询或发送到存储帐户以进行长期存档存储。 使用日志分析工作区为安全日志和事件中的异常活动创建警报。

或者，您可以将数据启用和车载到 Azure Sentinel。 

了解 Azure 活动日志：https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

如何配置 Azure 事件中心的诊断设置：https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

如何对日志分析工作区日志数据发出警报：https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：尚未可用

**责任**： 客户

### <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反恶意软件日志记录

**指导**：不适用;事件中心不处理反恶意软件日志记录。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9： 启用 DNS 查询日志记录

**指导**：不适用;事件中心不处理或生成 DNS 相关日志。

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

**指导**：通过 Azure 活动目录 （AD） 控制对事件中心的控制平面访问。 Azure AD 没有默认密码的概念。

通过 Azure AD 控制对事件中心的数据平面访问，具有托管标识或应用注册以及共享访问签名。 连接到事件中心的客户端使用共享访问签名，并可以随时重新生成。

了解事件中心的共享访问签名：https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure 安全中心监视**：不适用

**责任**： 客户

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

**指南**：Microsoft Azure 基于 Azure 活动目录 （AD） 为资源和应用程序提供集成的访问控制管理。 将 Azure AD 与 Azure 事件中心配合使用的主要优势在于，不再需要将凭据存储在代码中。 可以从 Microsoft 标识平台请求 OAuth 2.0 访问令牌。 用于请求令牌的资源名称为 https://eventhubs.azure.net/。 Azure AD 对运行应用程序的安全主体（用户、组或服务主体）进行身份验证。 如果身份验证成功，Azure AD 会将访问令牌返回应用程序，应用程序可随之使用访问令牌对 Azure 事件中心资源请求授权。

如何使用 Azure AD 对应用程序进行身份验证以访问事件中心资源：https://docs.microsoft.com/azure/event-hubs/authenticate-application

使用 Azure AD 了解 SSO：https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure 活动目录的访问使用多重身份验证

**指南**：启用 Azure 活动目录多重身份验证 （MFA），并遵循 Azure 安全中心标识和访问管理建议，以帮助保护启用事件集线器的资源。

如何在 Azure 中启用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心内监视标识和访问：https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用特权访问工作站 （PAW） 配置为登录到和配置启用事件集线器的资源的多重身份验证 （MFA）。

了解特权访问工作站：https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中启用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7： 从管理帐户记录和警报可疑活动

**指南**：当环境中发生可疑或不安全活动时，请使用 Azure 活动目录 （AD） 特权标识管理 （PIM） 生成日志和警报。 使用 Azure AD 风险检测查看有关风险用户行为的警报和报告。 对于其他日志记录，请将 Azure 安全中心风险检测警报发送到 Azure 监视器，并使用操作组配置自定义警报/通知。

如何部署特权标识管理 （PIM）：https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

了解 Azure AD 风险检测：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

如何为自定义警报和通知配置操作组：https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从已批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。



如何在 Azure 中配置命名位置：https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活动目录

**指南**：使用 Azure 活动目录 （AD） 作为 Azure 资源（如事件中心）的中央身份验证和授权系统。 这允许基于角色的访问控制 （RBAC） 管理敏感资源。

 如何创建和配置 Azure AD 实例：https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

要了解 Azure 事件中心如何与 Azure 活动目录 （AAD） 集成，请参阅使用 Azure 活动目录授权访问事件中心资源：https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期审查和协调用户访问

**指南**： Azure 活动目录 （AD） 提供日志，以帮助您发现陈旧的帐户。 此外，使用 Azure 标识访问审核可高效地管理组成员身份、对企业应用程序的访问和角色分配。 可以定期查看用户访问权限，以确保只有正确的用户才能继续访问。

此外，定期轮换事件中心共享访问签名。

了解 Azure AD 报告：https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 标识访问审核：https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

了解事件中心的共享访问签名：https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 监视访问已停用帐户的尝试

**指南**：您可以访问 Azure 活动目录 （AD） 登录活动、审核和风险事件日志源，这允许您与任何 SIEM/监视工具集成。

您可以通过为 Azure AD 用户帐户创建诊断设置并将审核日志和登录日志发送到日志分析工作区来简化此过程。 您可以在日志分析中配置所需的日志警报。

如何将 Azure 活动日志集成到 Azure 监视器中：https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

使用 Azure 活动目录授权访问事件中心资源：https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帐户登录行为偏差警报

**指南**：使用 Azure 活动目录的标识保护和风险检测功能，将自动响应配置为检测到的与启用事件中心的资源相关的可疑操作。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。

如何查看 Azure AD 风险登录：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和启用身份保护风险策略：https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支持方案期间向 Microsoft 提供对相关客户数据的访问

**指南**：当前不可用;事件中心尚不支持客户密码箱。

客户密码箱支持的服务列表：https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 安全中心监视**：当前不可用

**责任**： 当前不可用

## <a name="data-protection"></a>数据保护

*有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 维护敏感信息的清单

**指南**：使用与事件中心相关的资源上的标记来帮助跟踪存储或处理敏感信息的 Azure 资源。

如何创建和使用标记：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 事件中心命名空间应由虚拟网络分隔，并启用服务终结点并正确标记。

您还可以使用防火墙保护 Azure 事件中心命名空间。 Azure 事件中心支持基于 IP 的访问控制，用于入站防火墙支持。 可以使用 Azure 门户、Azure 资源管理器模板或通过 Azure CLI 或 Azure PowerShell 设置防火墙规则。

如何创建其他 Azure 订阅：https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组：https://docs.microsoft.com/azure/governance/management-groups/create

为 Azure 事件中心命名空间配置 IP 防火墙规则：https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

如何创建和使用标记：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何创建虚拟网络：https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 监控和阻止未经授权传输敏感信息

**指南**：使用虚拟机访问事件中心时，请使用虚拟网络、服务终结点、事件中心防火墙、网络安全组和服务标记来降低数据泄露的可能性。

Microsoft 管理 Azure 事件中心的基础基础结构，并实施了严格的控制，以防止客户数据丢失或暴露。

为 Azure 事件中心命名空间配置 IP 防火墙规则：https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

使用 Azure 事件中心了解虚拟网络服务终结点：https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

将 Azure 事件中心与 Azure 专用链接集成：https://docs.microsoft.com/azure/event-hubs/private-link-service

了解网络安全组和服务标记：https://docs.microsoft.com/azure/virtual-network/security-overview

了解 Azure 中的客户数据保护：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：默认情况下，Azure 事件中心强制实施 TLS 加密通信。 目前支持 TLS 版本 1.0、1.1 和 1.2。 但是，TLS 1.0 和 TLS 1.1 即将在全行业范围内弃用，因此，请尽可能使用 TLS 1.2。

要了解事件中心的安全功能，请参阅网络安全：https://docs.microsoft.com/azure/event-hubs/network-security

**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活动发现工具识别敏感数据

**指南**：Azure 事件中心尚不可用数据标识、分类和损失预防功能。 如果需要符合性，则实施第三方解决方案。

对于由 Microsoft 管理的基础平台，Microsoft 将所有客户内容视为敏感内容，并竭尽全力防止客户数据丢失和暴露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已经实施并维护了一套强大的数据保护控制和功能。

了解 Azure 中的客户数据保护：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：当前不可用

**责任**： 共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制对资源的访问

**指南**： Azure 事件中心支持使用 Azure 活动目录 （AD） 授权对事件中心资源的请求。 可以通过 Azure AD 使用基于角色的访问控制 (RBAC) 授予对服务主体的访问权限，该服务主体可能是用户或应用程序服务主体。

了解 Azure AD RBAC 和 Azure 事件中心可用的角色：https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基于主机的数据丢失防护来实施访问控制

**指导**：不适用;本指南用于计算资源。

Microsoft 管理事件中心的基础基础结构，并实施了严格的控制，以防止客户数据丢失或暴露。

了解 Azure 中的客户数据保护：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 静态加密敏感信息

**指南**：Azure 事件中心支持使用 Microsoft 管理的密钥或客户管理的密钥加密静态数据的选项。 此功能使您能够创建、旋转、禁用和撤销对用于加密静态 Azure 事件中心数据的客户托管密钥的访问权限。

如何配置客户管理的密钥以加密 Azure 事件中心：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 对关键 Azure 资源的更改进行日志和警报

**指南**：将 Azure 监视器与 Azure 活动日志一起用于创建对 Azure 事件中心和其他关键或相关资源的生产实例进行更改时的警报。

如何为 Azure 活动日志事件创建警报：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="vulnerability-management"></a>漏洞管理

*有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1： 运行自动漏洞扫描工具

**指导**：不适用;Microsoft 在支持事件中心的基础系统上执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动化操作系统修补程序管理解决方案

**指导**：不适用;Microsoft 在支持事件中心的基础系统上执行修补程序管理。

**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自动化的第三方软件修补程序管理解决方案

**指导**：不适用;基准测试适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4： 比较背对背漏洞扫描

**指导**：不适用;Microsoft 在支持事件中心的基础系统上执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5： 使用风险评级流程确定修复已发现漏洞的优先级

**指导**：不适用;Microsoft 在支持事件中心的基础系统上执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**： 微软

## <a name="inventory-and-asset-management"></a>清单和资产管理

*有关详细信息，请参阅[安全控制：库存和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1： 使用 Azure 资产发现

**指南**：使用 Azure 资源图查询和发现订阅中的所有资源（包括 Azure 事件中心命名空间）。 确保租户中具有适当的（读取）权限，并能够枚举订阅中的所有 Azure 订阅和资源。

如何使用 Azure 图形创建查询：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

**指南**：在适当情况下，使用标记、管理组和单独的订阅来组织和跟踪 Azure 事件中心命名空间和相关资源。 定期协调库存，确保及时从订阅中删除未经授权的资源。

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

- 不允许的资源类型

- 允许的资源类型

此外，使用 Azure 资源图查询/发现订阅中的资源。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 图形创建查询：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

- 不允许的资源类型

- 允许的资源类型

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 策略拒绝特定资源类型：https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="610-implement-approved-application-list"></a>6.10： 实施已批准的申请列表

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>限制用户通过脚本与 Azure 资源管理器交互的能力</div>

**指南**：配置 Azure 条件访问，通过为"Microsoft Azure 管理"应用配置"阻止访问"来限制用户与 Azure 资源管理器交互的能力。

如何配置条件访问以阻止对 Azure 资源管理器的访问：https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

**指南**：为 Azure 事件中心部署定义和实施标准安全配置。 在"Microsoft.EventHub"命名空间中使用 Azure 策略别名创建自定义策略以审核或强制执行配置。 您还可以使用 Azure 事件中心的内置策略定义，例如：

- 应启用事件中心内的诊断日志

- 事件中心应使用虚拟网络服务终结点

事件中心命名空间的 Azure 内置策略：https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

如何查看可用的 Azure 策略别名：https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2： 建立安全的操作系统配置

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 策略 [拒绝] 和 [部署（如果不存在））在启用事件中心的资源中强制实施安全设置。 

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
有关 Azure 策略效果的详细信息：https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4： 维护安全的操作系统配置

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存储 Azure 资源的配置

**指南**：如果对事件中心或相关资源使用自定义 Azure 策略定义，请使用 Azure 存储库安全地存储和管理代码。

如何在 Azure DevOps 中存储代码：https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 存储库文档：https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存储自定义操作系统映像

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系统配置管理工具

**指南**：使用"Microsoft.EventHub"命名空间中的 Azure 策略别名创建自定义策略以警报、审核和强制执行系统配置。 此外，开发用于管理策略异常的流程和管道。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实现自动化配置监视

**指南**：使用"Microsoft.EventHub"命名空间中的 Azure 策略别名创建自定义策略以警报、审核和强制执行系统配置。 使用 Azure 策略 [审核]、[拒绝]和 [部署（如果不存在））自动强制 Azure 事件中心部署和相关资源的配置。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 对操作系统实施自动配置监控

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11： 安全管理 Azure 机密

**指南**：对于在 Azure 应用服务上运行的 Azure 虚拟机或 Web 应用程序，用于访问事件中心，请使用 Azure 密钥保管库的托管服务标识，以简化和保护 Azure 事件中心部署的共享访问签名管理。 确保启用密钥保管库软删除。

使用 Azure 活动目录对托管标识进行身份验证以访问事件中心资源：https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

为事件中心配置客户管理的密钥：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

如何与 Azure 托管标识集成：https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何创建密钥保管库：https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用托管标识提供密钥保管库身份验证：https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12： 安全、自动地管理身份

**指南**：对于在 Azure 应用服务上运行的 Azure 虚拟机或 Web 应用程序，用于访问事件中心，请使用 Azure 密钥保管库的托管服务标识来简化和保护 Azure 事件中心。 确保启用密钥保管库软删除。

使用托管标识在 Azure 活动目录 （AD） 中为 Azure 服务提供自动托管标识。 托管标识允许您对支持 Azure AD 身份验证的任何服务（包括 Azure 密钥保管库）进行身份验证，而无需在代码中进行任何凭据。

使用 Azure 活动目录对托管标识进行身份验证以访问事件中心资源：https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

为事件中心配置客户管理的密钥：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

如何配置托管标识：https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

如何与 Azure 托管标识集成：https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

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

**指南**： 预扫描上载到非计算 Azure 资源的任何内容，如 Azure 事件中心、应用服务、数据存储、Blob 存储、用于 PostgreSQL 的 Azure 数据库等。在这种情况下，Microsoft 无法访问您的数据。

在支持 Azure 服务的基础主机上启用 Microsoft 反恶意软件（例如，Redis 的 Azure 缓存），但它不在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3： 确保更新反恶意软件和签名

**指导**：不适用;此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

*有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1： 确保定期自动备份

**指南**：为 Azure 事件中心配置地理灾难恢复。 当整个 Azure 区域或数据中心（如果未使用可用性区域）遭遇停机时，在不同区域或数据中心中继续进行数据处理就显得至关重要。 因此，地质灾害恢复和异地复制是任何企业的重要功能。 Azure 事件中心支持命名空间级别的异地灾难恢复和异地复制。 

了解 Azure 事件中心的地理灾难恢复：https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 执行完整的系统备份并备份任何客户托管密钥

**指南**：Azure 事件中心使用 Azure 存储服务加密 （Azure SSE） 提供静态数据加密。 事件中心依赖于 Azure 存储来存储数据，默认情况下，与 Azure 存储一起存储的所有数据都使用 Microsoft 管理的密钥进行加密。 如果使用 Azure 密钥保管库存储客户管理的密钥，请确保定期自动备份密钥。

使用以下 PowerShell 命令确保密钥保管库机密的定期自动备份：备份-AzKeyVault秘密

如何配置客户管理的密钥以加密静态 Azure 事件中心数据：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

如何备份密钥保管库机密：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户托管密钥

**指南**：测试备份的客户托管密钥的恢复。

 

如何在 Azure 中还原密钥保管库密钥：https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理密钥

**指南**：在密钥保管库中启用软删除，以保护密钥免受意外或恶意删除。 Azure 事件中心要求客户管理的密钥必须配置"软删除"和"不清除"。

为用于捕获事件中心数据的 Azure 存储帐户配置软删除。 请注意，Azure 数据湖存储第 2 代还不支持此功能。

如何在密钥保管库中启用软删除：https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

使用密钥设置密钥保管库：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Azure 存储 blob 的软删除：https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 安全中心监视**：是

**责任**： 客户

## <a name="incident-response"></a>事件响应

*有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指南

**指导**：确保有书面的事件响应计划，定义人员的角色以及事件处理/管理的各个阶段。

如何在 Azure 安全中心内配置工作流自动化：https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2： 创建事件评分和优先级处理过程

**指南**：安全中心为警报分配严重性，以帮助您确定处理每个警报的顺序，以便在资源受到威胁时，您可以马上到达它。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="103-test-security-response-procedures"></a>10.3： 测试安全响应程序

**指导**：进行练习，以常规节奏测试系统的事件响应能力。 找出薄弱环节和差距，并根据需要修订计划。

请参阅 NIST 出版物：IT 计划和功能测试、培训和锻炼计划指南：https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并配置安全事件的警报通知

**指南**：如果 Microsoft 安全响应中心 （MSRC） 发现客户的数据已被非法或未经授权的方访问，Microsoft 将使用安全事件联系信息与您联系。  事后查看事件，以确保问题得到解决。 

如何设置 Azure 安全中心安全联系人：https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5： 将安全警报纳入事件响应系统

**指南**：使用"连续导出"功能导出 Azure 安全中心警报和建议。 "连续导出"允许您手动或持续、持续的方式导出警报和建议。 您可以使用 Azure 安全中心数据连接器来流式传输警报哨兵。

如何配置连续导出：https://docs.microsoft.com/azure/security-center/continuous-export

如何将警报流式传输到 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6： 自动响应安全警报

**指南**：使用 Azure 安全中心的工作流自动化功能，通过安全警报和建议上的"逻辑应用"自动触发响应。

如何配置工作流自动化和逻辑应用：https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

*有关详细信息，请参阅[安全控制：渗透测试和红队练习](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：对 Azure 资源进行定期渗透测试，并确保在 60 天内补救所有关键安全发现

**指南**：请遵循 Microsoft 的接用规则，以确保您的渗透测试不违反 Microsoft 策略https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1： .
您可以在此处找到有关 Microsoft 针对 Microsoft 托管云基础架构、服务和应用程序进行红色团队和实时站点渗透测试的战略和实施的详细信息：https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心监视**：是

**责任**： 客户

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 了解有关[Azure 安全基线的更多信息](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
