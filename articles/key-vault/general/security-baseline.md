---
title: 密钥保管库的 Azure 安全基线
description: 密钥保管库的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 46fa0160dd8b37e89cdd77ba8acdae294fddbefe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616890"
---
# <a name="azure-security-baseline-for-key-vault"></a>密钥保管库的 Azure 安全基线

密钥保管库的 Azure 安全基线包含可帮助您改进部署安全状态的建议。

此服务的基线取自[Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，它提供了有关如何使用最佳实践指南在 Azure 上保护云解决方案的建议。

有关详细信息，请参阅[Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

*有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虚拟网络上的网络安全组或 Azure 防火墙保护资源

**指南**：将 Azure 密钥保管库与 Azure 专用链接集成。 

Azure 专用链接服务使您能够通过虚拟网络中的专用终结点访问 Azure 服务（例如，Azure 密钥保管库）和 Azure 托管的客户/合作伙伴服务。

Azure 专用终结点是一个网络接口，可以通过私密且安全的方式将你连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

如何将密钥保管库与 Azure 专用链接集成：

https://docs.microsoft.com/azure/key-vault/private-link-service


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 监视和记录 Vnet、子网和 NIC 的配置和流量

**指南**：使用 Azure 安全中心并遵循网络保护建议来帮助保护 Azure 中的关键保管库配置的资源。 

有关 Azure 安全中心提供的网络安全的详细信息： 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="13-protect-critical-web-applications"></a>1.3： 保护关键 Web 应用程序

**指导**：不适用;此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒绝与已知恶意 IP 地址的通信

**指南**：在与密钥保管库实例关联的 Azure 虚拟网络上启用 Azure DDoS 保护标准，以抵御分布式拒绝服务攻击。 使用 Azure 安全中心集成威胁智能拒绝与已知恶意或未使用的 Internet IP 地址的通信。

 
使用 Azure 门户管理 Azure DDoS 保护标准：https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure 安全中心中的 Azure 服务层的威胁检测：https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5： 记录网络数据包和流日志

**指南**：Azure 密钥保管库不使用网络安全组 （NSG），并且未捕获 Azure 密钥保管库的流日志。 相反，请使用 Azure 专用链接来保护 Azure 密钥保管库实例，并使诊断设置能够记录指标和审核事件。

将密钥保管库与 Azure 专用链接集成：

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure 密钥保管库日志记录：https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6： 部署基于网络的入侵检测/入侵防御系统（IDS/IPS）

**指南**：可通过为 Azure 密钥保管库配置高级威胁保护 （ATP） 来满足此要求。 ATP 提供了额外的安全智能层。 此工具检测访问或利用 Azure 密钥保管库帐户的潜在有害尝试。

当 Azure 安全中心检测到异常活动时，它会显示警报。 它还向订阅管理员发送电子邮件，提供可疑活动的详细信息以及如何调查和修复已识别的威胁的建议。

为 Azure 密钥保管库设置高级威胁保护：

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Azure 安全中心监视**：是

**责任**： 客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7： 管理 Web 应用程序的流量

**指导**：不适用;此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低网络安全规则的复杂性和管理开销

**指南**：对于需要访问 Azure 密钥保管库实例的资源，请使用 Azure 密钥保管库的 Azure 服务标记在网络安全组或 Azure 防火墙上定义网络访问控件。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。

Azure 服务标记概述：https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 维护网络设备的标准安全配置

**指南**：使用 Azure 策略为与 Azure 密钥保管库实例关联的网络资源定义和实施标准安全配置。 在"Microsoft.KeyVault"和"Microsoft.Network"命名空间中使用 Azure 策略别名来创建自定义策略以审核或强制执行 Azure 密钥保管库实例的网络配置。 您还可以使用与 Azure 密钥保管库相关的内置策略定义，例如：

Key Vault 应使用虚拟网络服务终结点

教程：创建和管理策略以强制实施合规性：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 策略示例：

https://docs.microsoft.com/azure/governance/policy/samples/#networ

快速入门：在门户中定义和分配蓝图：

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="110-document-traffic-configuration-rules"></a>1.10： 文档流量配置规则

**指南**：对 Azure 密钥保管库实例的网络安全和流量流相关的资源使用标记来提供元数据和逻辑组织。

使用与标记相关的任何内置 Azure 策略定义（如"要求标记及其值"）确保所有资源都使用标记创建，并通知您现有的未标记资源。

您可以使用 Azure PowerShell 或 Azure CLI 查找资源或根据资源标记执行操作。

使用标记来组织 Azure 资源：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自动工具监控网络资源配置并检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure 密钥保管库实例相关的网络资源的更改。 在 Azure 监视器中创建警报，这些警报将在对关键网络资源的更改时触发。

查看和检索 Azure 活动日志事件：

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

使用 Azure 监视器创建、查看和管理活动日志警报：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="logging-and-monitoring"></a>日志记录和监视

*有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的时间同步源

**指导**：不适用;Microsoft 维护用于 Azure 资源（如 Azure 密钥保管库）的时间源，用于日志中的时间戳。


**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全日志管理

**指南**：通过 Azure 监视器引入日志以聚合 Azure 密钥保管库生成的安全数据。 在 Azure 监视器中，使用 Azure 日志分析工作区来查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储。 或者，您可以将数据启用到 Azure Sentinel 或第三方 SIEM。 

Azure 密钥保管库日志记录：

https://docs.microsoft.com/azure/key-vault/key-vault-logging

快速入门：如何登上 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：在 Azure 密钥保管库实例上启用诊断设置，以访问审核、安全和诊断日志。 活动日志自动可用，包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

Azure 密钥保管库日志记录：

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4： 从操作系统收集安全日志

**指导**：不适用;此建议用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全日志存储保留

**指南**：在 Azure 监视器中，对于用于保存 Azure 密钥保管库日志的日志分析工作区，请根据组织的合规性法规设置保留期。 使用 Azure 存储帐户进行长期/存档存储。

更改数据保留期：https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="26-monitor-and-review-logs"></a>2.6： 监视和查看日志

**指南**：分析和监视日志是否存在异常行为，并定期查看 Azure 密钥保管库保护资源的结果。 使用 Azure 监视器的日志分析工作区查看日志并执行日志数据的查询。 或者，您可以将数据启用并车载到 Azure Sentinel 或第三方 SIEM。 

快速入门：板载 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

开始在 Azure 监视器中的日志分析：

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

开始在 Azure 监视器中的日志查询：

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 启用异常活动的警报

**指南**：在 Azure 安全中心中，为密钥保管库启用高级威胁保护 （ATP）。 在 Azure 密钥保管库中启用诊断设置，并将日志发送到日志分析工作区。 将日志分析工作区添加到 Azure Sentinel 中，因为它提供安全编排自动响应 （SOAR） 解决方案。 这允许创建行动手册（自动解决方案），并用于修复安全问题。

快速入门：板载 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

管理和响应 Azure 安全中心中的安全警报：

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

使用 Azure 监视器警报响应事件：

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反恶意软件日志记录

**指导**：不适用;Azure 密钥保管库不处理或生成与恶意软件相关的日志。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9： 启用 DNS 查询日志记录

**指导**：不适用;Azure 密钥保管库不处理或生成与 DNS 相关的日志。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10： 启用命令行审核日志记录

**指导**：不适用;此建议用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

*有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1： 维持管理帐户的清单

**指南**：维护 Azure 活动目录注册应用程序的清单，以及有权访问 Azure 密钥保管库密钥、机密和证书的任何用户帐户。 您可以使用 Azure 门户或 PowerShell 查询和协调密钥保管库访问权限。 要在 PowerShell 中查看访问权限，请使用以下命令：

（获取 Az 资源 - 资源 Id [密钥库资源 ID]）。属性.访问策略

将应用程序注册到 Azure 活动目录：

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

安全访问密钥保管库：

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2： 在适用的情况下更改默认密码

**指导**：不适用;Azure 密钥保管库没有默认密码的概念，因为身份验证由 Active Directory 提供，并且使用基于角色的访问控制进行保护。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="33-use-dedicated-administrative-accounts"></a>3.3： 使用专用管理帐户

**指南**：围绕使用有权访问 Azure 密钥保管库实例的专用管理帐户创建标准操作过程。 使用 Azure 安全中心标识和访问管理（当前处于预览状态）监视活动管理帐户的数量。

监视身份和访问（预览）：

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4： 使用 Azure 活动目录的单一登录 （SSO）

**指南**：将 Azure 服务主体与 AppId、租户 ID 和客户端机密结合使用，以无缝验证应用程序并检索将用于访问 Azure 密钥保管库机密的令牌。

使用 .NET 对 Azure 密钥保管库的服务到服务身份验证：

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure 活动目录的访问使用多重身份验证

**指南**：启用 Azure 活动目录多重身份验证，并遵循 Azure 安全中心标识和访问管理（当前预览版）建议，以帮助保护启用事件集线器的资源。

规划基于云的 Azure 多重身份验证部署：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

监视身份和访问（预览）：

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用特权访问工作站 （PAW） 与 Azure 多重身份验证 （MFA） 配置为登录和配置启用密钥保管库的资源。 

特权访问工作站：https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

规划基于云的 Azure 多重身份验证部署：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7： 从管理帐户记录和警报可疑活动

**指南**：当环境中发生可疑或不安全活动时，请使用 Azure 活动目录 （AAD） 特权标识管理 （PIM） 生成日志和警报。 使用 AAD 风险检测查看有关风险用户行为的警报和报告。 对于其他日志记录，请将 Azure 安全中心风险检测警报发送到 Azure 监视器，并使用操作组配置自定义警报/通知。

为 Azure 密钥保管库启用高级威胁保护 （ATP） 以生成可疑活动的警报。

部署 Azure AD 特权标识管理 （PIM）：https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

为 Azure 密钥保管库设置高级威胁保护（预览）：https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Azure 密钥保管库的警报（预览）：https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure 活动目录风险检测：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

在 Azure 门户中创建和管理操作组：https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Azure 安全中心监视**：是

**责任**： 客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从已批准的位置管理 Azure 资源

**指南**：配置条件访问策略的位置条件并管理命名位置。 使用命名位置可以创建 IP 地址范围或者国家和地区的逻辑分组。 您可以限制对敏感资源（如密钥保管库机密）的访问，以访问配置的命名位置。

Azure 活动目录条件访问中的位置条件是什么？https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活动目录

**指南**：使用 Azure 活动目录 （AAD） 作为 Azure 资源（如密钥保管库）的中央身份验证和授权系统。 这允许基于角色的访问控制 （RBAC） 管理敏感资源。

 

快速入门：在 Azure 活动目录中创建新租户：

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期审查和协调用户访问

**指导**：查看 Azure 活动目录 （AAD） 日志，以帮助发现具有 Azure 密钥保管库管理角色的陈旧帐户。 此外，使用 AAD 访问审核可有效地管理组成员身份、访问可用于访问 Azure 密钥保管库的企业应用程序以及角色分配。 应定期审查用户访问权限，例如每 90 天一次，以确保只有正确的用户才能继续访问。

Azure 活动目录报告和监视文档：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

什么是 Azure AD 访问审核？：

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 监视访问已停用帐户的尝试

**指南**：启用 Azure 密钥保管库和 Azure 活动目录的诊断设置，将所有日志发送到日志分析工作区。 在日志分析中配置所需的警报（例如尝试访问禁用的机密）。

将 Azure AD 日志与 Azure 监视器日志集成：https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

从旧的密钥保管库解决方案迁移：https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Azure 安全中心监视**：是

**责任**： 客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帐户登录行为偏差警报

**指南**：使用 Azure 活动目录的标识保护和风险检测功能，可配置自动响应，以检测与 Azure 密钥保管库保护资源相关的可疑操作。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。 

Azure 活动目录门户中的有风险登录报告：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

如何：配置和启用风险策略：https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支持方案期间向 Microsoft 提供对相关客户数据的访问

**指导**：不适用;Azure 密钥保管库不支持客户密码箱。

支持的服务和方案在一般可用性中：https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-protection"></a>数据保护

*有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 维护敏感信息的清单

**指南**：使用标记来帮助跟踪在启用 Azure 密钥保管库的资源上存储或处理敏感信息的 Azure 资源。 

使用标记来组织 Azure 资源：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔离存储或处理敏感信息的系统

**指南**：您可以使用配置为限制对特定子网的访问的虚拟网络服务终结点来保护对 Azure 密钥保管库的访问。

防火墙规则生效后，仅当请求源自允许的子网或 IP 地址范围时，才能执行 Azure 密钥保管库数据平面操作。 这也适用于 Azure 门户中的 Azure 密钥保管库访问。 尽管可以从 Azure 门户浏览到密钥保管库，但如果客户端计算机不在允许列表中，则可能无法列出密钥、机密或证书。 这也会影响 Azure 密钥保管库选取器和其他 Azure 服务。 如果防火墙规则阻止客户端计算机执行此操作，则可以查看密钥保管库的列表，但不能查看列表密钥。

配置 Azure 密钥保管库防火墙和虚拟网络：https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure 密钥保管库的虚拟网络服务终结点：https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 监控和阻止未经授权传输敏感信息

**指南**：存储在 Azure 密钥保管库中的所有数据都被视为敏感数据。 使用 Azure 密钥保管库数据平面访问控件控制对 Azure 密钥保管库机密的访问。 您还可以使用密钥保管库的内置防火墙来控制网络层的访问。 要监视对 Azure 密钥保管库的访问，请启用密钥保管库诊断设置，并将日志发送到 Azure 存储帐户或日志分析工作区。

安全访问密钥保管库：https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

配置 Azure 密钥保管库防火墙和虚拟网络：https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure 密钥保管库日志记录：https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure 安全中心监视**：是

**责任**： 客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：所有到 Azure 密钥保管库的流量进行身份验证、管理和数据平面访问，都经过加密并经过 HTTPS：端口 443。 （但是，CRL 偶尔会有 HTTP [端口 80] 流量。 

在防火墙后面访问 Azure 密钥保管库：https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活动发现工具识别敏感数据

**指导**：不适用;Azure 密钥保管库中的所有数据（机密、密钥和证书）都被视为敏感数据。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制对资源的访问

**指南**：安全访问 Azure 密钥保管库实例的管理和数据平面。

安全访问密钥保管库：

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基于主机的数据丢失防护来实施访问控制

**指南**：Microsoft 管理 Azure 密钥保管库的基础基础结构，并实施了严格的控制，以防止客户数据丢失或暴露。

什么是 Azure Key Vault？

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azure 客户数据保护：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 静态加密敏感信息

**指南**：所有托管对象（密钥、证书和机密）在 Azure 密钥保管库中静态加密。

支持文档：

- [加密模型和密钥管理表](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 对关键 Azure 资源的更改进行日志和警报

**指导**：使用 Azure 监视器中的 Azure 密钥保管库分析解决方案查看 Azure 密钥保管库审核事件日志。

Azure 监视器中的 Azure 密钥保管库分析解决方案：

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="vulnerability-management"></a>漏洞管理

*有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1： 运行自动漏洞扫描工具

**指南**：Microsoft 在支持 Azure 密钥保管库的基础系统上执行漏洞管理。


**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动化操作系统修补程序管理解决方案

**指导**：不适用;Microsoft 对支持密钥保管库的基础系统执行修补程序管理。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自动化的第三方软件修补程序管理解决方案

**指导**：不适用;此建议用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4： 比较背对背漏洞扫描

**指南**：微软对支持密钥保管库的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**： 微软

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5： 使用风险评级流程确定修复已发现漏洞的优先级

**指南**：使用 Azure 安全中心提供的默认风险评级（安全分数）。

提高 Azure 安全中心的安全分数：

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="inventory-and-asset-management"></a>清单和资产管理

*有关详细信息，请参阅[安全控制：库存和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1： 使用 Azure 资产发现

**指南**：使用 Azure 资源图查询和发现订阅中的所有资源（包括 Azure 密钥保管库实例）。 确保租户中具有适当的（读取）权限，并能够枚举订阅中的所有 Azure 订阅和资源。

快速入门：使用 Azure 资源图资源管理器运行第一个资源图查询：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

获取当前帐户可以访问的订阅：

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

什么是 Azure 资源的基于角色的访问控制 (RBAC)？

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="62-maintain-asset-metadata"></a>6.2： 维护资产元数据

**指南**：将标记应用于 Azure 密钥保管库资源，使元数据以逻辑方式将它们组织到分类中。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3： 删除未经授权的 Azure 资源

**指南**：在适当情况下，使用标记、管理组和单独的订阅来组织和跟踪 Azure 密钥保管库实例和相关资源。 定期协调库存，确保及时从订阅中删除未经授权的资源。

创建其他 Azure 订阅：

https://docs.microsoft.com/azure/billing/billing-create-subscription

为资源组织和管理创建管理组：

https://docs.microsoft.com/azure/governance/management-groups/create

使用标记来组织 Azure 资源：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单

**指南**：为计算资源定义已批准的 Azure 资源和已批准的软件的列表

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5： 监视未经批准的 Azure 资源

**指南**：使用 Azure 策略对客户订阅中可创建的资源类型使用以下内置策略定义进行限制：

- 不允许的资源类型

- 允许的资源类型

此外，使用 Azure 资源图查询/发现订阅中的资源。

教程：创建和管理策略以强制实施合规性：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

快速入门：使用 Azure 资源图资源管理器运行第一个资源图查询：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6： 监控计算资源中未经批准的软件应用程序

**指导**：不适用;此建议用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7： 删除未经批准的 Azure 资源和软件应用程序

**指导**：不适用;此建议适用于整个 Azure 以及计算资源。


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

教程：创建和管理策略以强制实施合规性：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 策略示例：https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="610-implement-approved-application-list"></a>6.10： 实施已批准的申请列表

**指导**：不适用;此建议用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11：限制用户通过脚本与 AzureResource Manager 交互的能力

**指南**：使用 Azure 条件访问通过为"Microsoft Azure 管理"应用配置"阻止访问"来限制用户与 Azure 资源管理器 （ARM） 交互的能力。 这可以防止在高安全性环境中创建和更改资源，例如具有密钥保管库配置的资源。

使用条件访问管理对 Azure 管理的访问：

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

**指南**：使用"Microsoft.KeyVault"命名空间中的 Azure 策略别名创建自定义策略以审核或强制执行 Azure 密钥保管库实例的配置。 您还可以对 Azure 密钥保管库使用内置 Azure 策略定义，例如：

应可恢复 Key Vault 对象

将 Key Vault 的诊断设置部署到 Log Analytics 工作区

应启用 Key Vault 中的诊断日志

Key Vault 应使用虚拟网络服务终结点

将 Key Vault 的诊断设置部署到事件中心

使用 Azure 安全中心的建议作为 Azure 密钥保管库实例的安全配置基线。

如何查看可用的 Azure 策略别名：

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

教程：创建和管理策略以强制实施合规性：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2： 建立安全的操作系统配置

**指导**：不适用;此建议用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 策略 [拒绝] 和 [部署（如果不存在））在启用 Azure 密钥保管库的资源中强制实施安全设置。 

教程：创建和管理策略以强制实施合规性：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
了解 Azure 策略效果： 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4： 维护安全的操作系统配置

**指导**：不适用;此建议用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存储 Azure 资源的配置

**指南**：如果对启用 Azure 密钥保管库的资源使用自定义 Azure 策略定义，请使用 Azure 存储库安全地存储和管理代码。

如何在 Azure DevOps 中存储代码： 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Azure 存储库文档： 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存储自定义操作系统映像

**指导**：不适用;此建议用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系统配置管理工具

**指南**：使用"Microsoft.KeyVault"命名空间中的 Azure 策略别名创建自定义策略以警报、审核和强制执行系统配置。 此外，开发用于管理策略异常的流程和管道。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指导**：不适用;此建议用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实现自动化配置监视

**指南**：使用 Azure 安全中心对 Azure 密钥保管库保护的资源执行基线扫描 

  

如何修复 Azure 安全中心中的建议： 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 对操作系统实施自动配置监控

**指导**：不适用;此基准测试用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11： 安全管理 Azure 机密

**指南**：将托管服务标识与 Azure 密钥保管库结合使用，以简化和保护云应用程序的秘密管理。 确保启用 Azure 密钥保管库软删除。

如何与 Azure 托管标识集成：

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何创建密钥保管库：

https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用托管标识提供密钥保管库身份验证： 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12： 安全、自动地管理身份

**指南**：将托管服务标识与 Azure 密钥保管库结合使用，以简化和保护云应用程序的秘密管理。 

  

如何与 Azure 托管标识集成： 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

如何创建密钥保管库： 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

如何使用托管标识提供密钥保管库身份验证：  
https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13： 消除意外的凭据暴露

**指南**：实现凭据扫描程序以标识代码中的凭据。 凭据扫描程序还将鼓励将发现的凭据移动到更安全的位置，如 Azure 密钥保管库。  
  
 如何设置凭据扫描程序：https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="malware-defense"></a>恶意软件防护

*有关详细信息，请参阅[安全控制：恶意软件防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1： 使用集中管理的反恶意软件

**指导**：不适用;此建议用于计算资源。 Microsoft 处理底层平台的反恶意软件。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：要上载到非计算 Azure 资源的预扫描文件

**指南**：在支持 Azure 服务的基础主机上启用 Microsoft 反恶意软件（例如，Azure 密钥保管库），但是，它不在客户内容上运行。

预扫描上载或发送到非计算 Azure 资源（如 Azure 密钥保管库）的任何内容。 在这种情况下，Microsoft 无法访问您的数据。

了解适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件：https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3： 确保更新反恶意软件和签名

**指导**：不适用;此建议用于计算资源。 Microsoft 处理底层平台的反恶意软件。


**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

*有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1： 确保定期自动备份

**指南**：使用以下 PowerShell 命令，确保定期自动备份密钥保管库证书、密钥、托管存储帐户和机密：

- 备份-AzKeyVault 证书

- 备份-阿兹基库键

- 备份-AzKeyVault 托管存储帐户

- 备份-阿兹基库特秘密

或者，您可以将密钥保管库备份存储在 Azure 备份中。

如何备份密钥保管库证书：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

如何备份密钥保管库密钥：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

如何备份密钥保管库托管存储帐户：https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

如何备份密钥保管库机密：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

如何启用 Azure 备份：https://docs.microsoft.com/azure/backup



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 执行完整的系统备份并备份任何客户托管密钥

**指南**： 使用以下 PowerShell 命令执行密钥保管库证书、密钥、托管存储帐户和机密的备份：

- 备份-AzKeyVault 证书

- 备份-阿兹基库键

- 备份-AzKeyVault 托管存储帐户

- 备份-阿兹基库特秘密

或者，您可以将密钥保管库备份存储在 Azure 备份中。

如何备份密钥保管库证书：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

如何备份密钥保管库密钥：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

如何备份密钥保管库托管存储帐户：https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

如何备份密钥保管库机密：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

如何启用 Azure 备份：https://docs.microsoft.com/azure/backup



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户托管密钥

**指南**： 定期使用以下 PowerShell 命令对密钥保管库证书、密钥、托管存储帐户和机密执行数据恢复：

- 还原-AzKeyVault 证书

- 恢复-阿兹基库键

- 还原 -AzKeyVault 托管存储帐户

- 恢复-阿兹基库秘密

如何还原密钥保管库证书：https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

如何还原密钥保管库密钥：https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

如何还原密钥保管库托管存储帐户：https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

如何恢复密钥保管库机密：https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理密钥

**指南**：确保为 Azure 密钥保管库启用软删除。 软删除允许恢复已删除的密钥保管库和保管库对象，如密钥、机密和证书。 

如何使用 Azure 密钥保管库的软删除： 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="incident-response"></a>事件响应

*有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指南

**指导**：为您的组织构建事件响应指南。 确保有书面事件响应计划，定义人员的所有角色以及事件处理/管理阶段，从检测到事件后审查。 这些进程应侧重于保护敏感系统，例如使用密钥保管库机密的系统。

如何在 Azure 安全中心内配置工作流自动化： 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

有关构建您自己的安全事件响应流程的指导：  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

微软安全响应中心的事件剖析：   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

客户还可以利用 NIST 的计算机安全事件处理指南来帮助创建他们自己的事件响应计划： 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2： 创建事件评分和优先级处理过程

**指南**：安全中心为每个警报分配严重性，以帮助您优先处理应首先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。 此外，清楚地标记订阅（对于前 生产，非 prod），并创建命名系统以明确识别和分类 Azure 资源，特别是那些处理敏感数据（如 Azure 密钥保管库机密）的资源。


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="103-test-security-response-procedures"></a>10.3： 测试安全响应程序

**指南**：执行练习，以常规节奏测试系统的事件响应功能，以帮助保护 Azure 密钥保管库实例和相关资源。 找出薄弱环节和差距，并根据需要修订计划。

请参阅 NIST 出版物：IT 计划和功能测试、培训和锻炼计划指南： 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并配置安全事件的警报通知

**指南**：如果 Microsoft 安全响应中心 （MSRC） 发现您的数据已被非法或未经授权的方访问，Microsoft 将使用安全事件联系信息与您联系。  事后查看事件，以确保问题得到解决。

如何设置 Azure 安全中心安全联系人：

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5： 将安全警报纳入事件响应系统

**指南**：使用"连续导出"功能导出 Azure 安全中心警报和建议，以帮助识别启用 Azure 密钥库的资源的风险。 "连续导出"允许您手动或持续、持续的方式导出警报和建议。  您可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure 哨兵。 

 

如何配置连续导出： 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

如何将警报流式传输到 Azure 哨兵： 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6： 自动响应安全警报

**指南**：使用 Azure 安全中心的工作流自动化功能通过安全警报和建议上的"逻辑应用"自动触发响应，以保护受 Azure 密钥保管库保护的资源。 

 

如何配置工作流自动化和逻辑应用： 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

*有关详细信息，请参阅[安全控制：渗透测试和红队练习](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：对 Azure 资源进行定期渗透测试，并确保在 60 天内补救所有关键安全发现

**指南**：您不应直接对 Azure 密钥保管库服务执行笔测试，但建议测试使用密钥保管库以确保机密安全的 Azure 资源。

您需要遵守 Microsoft 参与规则，以确保您的渗透测试不违反 Microsoft 策略：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

您可以找到有关 Microsoft 针对 Microsoft 管理的云基础架构、服务和应用程序进行红色团队和实时站点渗透测试的战略和执行的详细信息，如下所示： 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心监视**：不适用

**责任**： 共享

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 了解有关[Azure 安全基线的更多信息](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
