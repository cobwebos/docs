---
title: 适用于 Azure Data Box 的 Azure 安全基线
description: 适用于 Azure Data Box 的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: a13a8b2eef9fa1bd9c7281ac6b7c7dba40105428
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497318"
---
# <a name="azure-security-baseline-for-azure-data-box"></a>适用于 Azure Data Box 的 Azure 安全基线

适用于 Azure Data Box 的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全性

有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：不适用;你的 Azure Data Box 不能与虚拟网络关联。 通过 Azure 门户控制从 Data Box 到 Azure 托管存储的流量。 利用 Data Box 时，数据将通过 Azure 主干传输。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指南**：不适用;你的 Azure Data Box 不能与虚拟网络关联。 通过 Azure 门户控制从 Data Box 到 Azure 托管存储的流量。 利用 Data Box 时，数据将通过 Azure 主干传输。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：不适用;你的 Azure Data Box 不能与虚拟网络关联。 通过 Azure 门户控制从 Data Box 到 Azure 托管存储的流量。 利用 Data Box 时，数据将通过 Azure 主干传输。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：不适用;你的 Azure Data Box 不能与虚拟网络关联。 通过 Azure 门户控制从 Data Box 到 Azure 托管存储的流量。 利用 Data Box 时，数据将通过 Azure 主干传输。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：指导： Azure Data Box 所使用的终结点均由 Microsoft 管理。 你负责管理要部署到本地系统的其他所有控件。

* [了解 Azure Data Box 安全性](https://docs.microsoft.com/azure/databox/data-box-security)

* [Azure Data Box 的端口信息](https://docs.microsoft.com/azure/databox/data-box-system-requirements#port-requirements)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：不适用;你的 Azure Data Box 不能与虚拟网络关联。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：不适用;你的 Azure Data Box 不能与虚拟网络关联。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：不适用;你的 Azure Data Box 不能与虚拟网络关联。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：不适用;你的 Azure Data Box 不能与虚拟网络关联。

Azure 安全中心监视：不适用

**责任**：不适用

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护用于 Azure 资源的时间源，例如日志中的时间戳。 如果未连接到可以访问客户站点上的 NTP 服务器的网络，则 Azure Data Box 时间可能会偏差。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：对应于你的 Data Box 顺序中的每个步骤，你可以执行多个操作来控制对订单的访问、审核事件、跟踪订单，以及解释生成的各种日志。

* [了解 Azure Data Box 的跟踪和事件日志记录](https://docs.microsoft.com/azure/databox/data-box-logs)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：对应于你的 Data Box 顺序中的每个步骤，你可以执行多个操作来控制对订单的访问、审核事件、跟踪订单，以及解释生成的各种日志。

* [了解 Azure Data Box 的跟踪和事件日志记录](https://docs.microsoft.com/azure/databox/data-box-logs)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：此建议用于计算资源。 Data Box 具有包含安全日志的审核日志和支持包。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：不适用

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指南**：对应于你的 Data Box 顺序中的每个步骤，你可以执行多个操作来控制对订单的访问、审核事件、跟踪订单，以及解释生成的各种日志。

* [了解 Azure Data Box 的跟踪和事件日志记录](https://docs.microsoft.com/azure/databox/data-box-logs)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：对应于你的 Data Box 顺序中的每个步骤，你可以执行多个操作来控制对订单的访问、审核事件、跟踪订单，以及解释生成的各种日志。

* [了解 Azure Data Box 的跟踪和事件日志记录](https://docs.microsoft.com/azure/databox/data-box-logs)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：不适用;Azure Data Box 不会处理或产生与反恶意软件相关的日志。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：不适用;Azure Data Box 不会处理或产生与 DNS 相关的日志。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：维护对你的 Azure Data Box 具有管理访问权限的用户帐户的清单。 可以在 Azure 门户中为你的订阅使用“标识和访问控制(IAM)”窗格来配置基于角色的访问控制 (RBAC)。 这些角色将应用于 Active Directory 中的用户、组、服务主体和托管标识。在首次创建订单时，你可以控制谁可以访问你的订单。 在不同范围中设置 Azure 角色，以控制对 Data Box 顺序的访问。 Azure 角色确定对操作子集的访问（读写、只读、读写）的类型。

* [了解自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)

* [如何为工作簿配置 RBAC](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)

* [了解如何按顺序设置访问控制](https://docs.microsoft.com/azure/databox/data-box-logs#set-up-access-control-on-the-order)

**Azure 安全中心监视**：否

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure AD 没有默认密码。 其他需要密码的 Azure 资源会强制创建具有复杂性要求和最小密码长度的密码，该长度因服务而异。 你对可能使用默认密码的第三方应用程序和市场服务负责。

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

**指南**：不适用;对你的 Data Box 顺序的访问是通过 Azure 门户并为具有所有者或参与者的租户角色的帐户进行保留。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：不适用

**Azure 安全中心监视**：否

**责任**：不适用

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：通过启用了 Azure 多重身份验证（MFA）的特权访问工作站（PAW）登录并配置你的 Azure Data Box 订单。

* [特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [规划基于云的 Azure 多重身份验证部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指南**：不适用，Azure Data Box 没有自己的管理帐户。 可以通过 Azure 门户访问它。 但是，你可以将 Azure 订阅配置为使用 Azure Active Directory （AD） Privileged Identity Management （PIM）来生成日志，并在环境中发生可疑活动或不安全活动时发出警报。

此外，还可使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

* [如何部署 Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [了解 Azure AD 风险检测](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问 Azure 门户。

* [如何在 Azure 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：使用 AZURE ACTIVE DIRECTORY （AD）作为中心身份验证和授权系统（如果适用）。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

* [如何创建和配置 Azure AD 实例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (AD) 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

对于 Data Box 设备，不会实时支持。 可以在作业结束时查看日志。

* [了解 Azure AD 报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [如何使用 Azure 标识访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：使用 AZURE ACTIVE DIRECTORY （AD）作为中心身份验证和授权系统（如果适用）。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

你可以访问 Azure AD 登录活动、审核和风险事件日志源，以便与 Azure Sentinel 或第三方 SIEM 集成。

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 可以在 Log Analytics 中配置所需的日志警报。

Azure Data Box 服务日志不会写入 Log Analytics 工作区中。

* [如何将 Azure 活动日志集成到 Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：对于控制平面（例如 Azure 门户）中帐户登录行为的偏差，可使用 Azure AD 标识保护和风险检测功能进行配置，实现在检测到与用户标识相关的可疑操作时自动进行响应。 还可将数据引入 Azure Sentinel 以做进一步调查。

* [如何查看 Azure AD 风险登录](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [如何配置和启用标识保护风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**： Azure Data Box 当前不支持客户密码箱。

* [支持客户密码箱的服务列表](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：不适用于 Azure Data Box。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：将在订阅中预配要访问的资源所在的 Azure Data Box。 没有要保护或隔离的公共终结点。 Data Box 访问权限可用于具有订阅的所有者或参与者访问权限的用户。

在将数据上传到 Azure 的过程中，将隔离用于上传数据的 Data Box 设备和服务。

* [如何开始 Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**： Microsoft 管理 Azure Data Box 的底层基础结构，并实施了严格控制以防止客户数据丢失或泄露。 在客户站点上 Data Box 时，请遵循最佳做法，确保传输的敏感数据受到保护。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：当前不可用

**责任**：共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**： Microsoft 管理 Azure Data Box 的底层基础结构，并实施了严格控制以防止客户数据丢失或泄露。 在客户站点上 Data Box 时，请遵循最佳做法，确保传输的敏感数据受到保护。

* [了解 Azure Data Box 中的数据迁移](https://docs.microsoft.com/azure/databox/data-box-faq)

* [Data Box 安全性概述](https://docs.microsoft.com/azure/databox/data-box-security)

**Azure 安全中心监视**：不适用

责任：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：当前不可用;数据标识、分类和丢失防护功能尚不适用于 Azure Data Box。 Microsoft 管理 Azure Data Box 的底层基础结构，并已实现严格控制以防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：目前不可用

**责任**：不适用

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**：确保你有权访问订阅的所有者或参与者才能创建 Data Box 订单。 你还可以在资源级别定义 Data Box 读取器和 Data Box 参与者角色。

* [了解如何开始 Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal)

* [了解如何设置访问控制](https://docs.microsoft.com/azure/databox/data-box-logs#set-up-access-control-on-the-order)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**：不适用；此建议适用于计算资源。 Microsoft 管理 Azure Data Box 的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

* [Azure 客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**： Azure Data Box 为静态数据实现 AES 256 位加密。

Azure Data Box 为静态数据实现 AES 256 位加密。此外，Azure Data Box 通过加密密钥来保护用于锁定设备的设备解锁密钥（也称为设备密码）。 默认情况下，使用 Microsoft 管理的密钥加密 Data Box 订单的设备解锁密钥。 如需进一步控制设备解锁密钥，还可以提供客户管理的密钥。 必须在 Azure Key Vault 中创建并存储客户管理的密钥。

* [了解 Data Box 数据保护](https://docs.microsoft.com/azure/databox/data-box-security)

* [将 Azure 密钥保管库中的客户管理的密钥用于 Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-customer-managed-encryption-key-portal)

**Azure 安全中心监视**：不适用

责任：共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建 Azure Data Box 以及其他关键或相关资源发生更改的警报。

* [如何针对 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**： Microsoft 对支持 Azure Data Box 的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**：发运 Data Box 后，会随最新更新一起安装。 我们不执行字段更新。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指南**：发运 Data Box 后，会随最新更新一起安装。 我们不执行字段更新。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**： Microsoft 对支持 Azure Data Box 的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**： Microsoft 对支持 Azure Data Box 的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：不适用，没有要发现的 Data Box 资产。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：不适用，Data Box 没有资产元数据。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：不适用，Data Box 服务确保未使用未经授权的 Azure 资源。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已获批 Azure 资源的清单

**指南**：不适用;Data Box 服务级别没有任何内容。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：不适用，Data Box 服务级别无。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**：不适用，Data Box 服务级别无。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：不适用，Data Box 服务级别无。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：不适用，Data Box 服务级别无。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：不适用，Data Box 服务仅使用批准的 Azure 服务。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指南**：不适用;Data Box 服务仅使用批准的软件标题。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

* [如何配置条件访问以阻止访问 Azure 资源管理器](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指南**：不适用;Data Box 服务不支持执行脚本。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：不适用;Data Box 服务没有 Azure 应用服务上运行的 web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**： Azure Data Box 附带预配置的最佳方案安全设置。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**： Azure Data Box 附带预配置的最佳方案安全设置。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**： Azure Data Box 附带资源的预配置最佳方案安全设置，不能更改。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**： Azure Data Box 附带资源的预配置最佳方案安全设置，不能更改。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：安全存储所有 Data Box 配置。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：安全存储所有 Data Box 的操作系统映像。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指南**：不适用，Azure Data Box 配置无法更改。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指南**：不适用，Azure Data Box 配置无法更改。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：不适用，Azure Data Box 配置无法更改。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**：不适用，Azure Data Box 配置无法更改。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：必须创建客户管理的密钥并将其存储在 Azure Key Vault 中。

* [如何在 Azure Key Vault 中使用客户托管的密钥 Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-customer-managed-encryption-key-portal)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：不适用;Azure Data Box 不使用托管标识。

* [支持托管标识的 Azure 服务](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**： Microsoft 在 Data Box 代码上运行凭据扫描器。 此外，Microsoft 还会安全地保护凭据。 实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：空值

**责任**：共享

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：不适用；此项指导适用于计算资源。 Microsoft 反恶意软件已在支持 Azure 服务（例如 Azure 应用服务）的基础主机上启用，但它不会针对客户内容运行。

Azure 安全中心监视：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：在支持 azure 服务的基础主机（例如，azure 客户密码箱）上启用了 Microsoft 反恶意软件，但它不会在客户内容上运行。

你需要负责预先扫描要上传到非计算 Azure 资源的任何内容。 Microsoft 无法访问客户数据，因此无法代表你对客户内容执行反恶意软件扫描。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用；此建议适用于计算资源。 支持 Azure 服务的底层主机上启用了 Microsoft 反恶意软件，但它不会在客户内容上运行。

Azure 安全中心监视：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：不适用，Data Box 服务不需要备份。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：请确保备份任何数据和客户管理的密钥。 Data Box 不会进行任何备份。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：你应该在将数据从本地删除之前，验证你的所有数据是否在 Azure 存储帐户中。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：确保根据最佳做法保护任何备份或客户托管的密钥。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

* [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [利用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，使用标记清楚地标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

* [Azure 安全中心中的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [使用标记整理 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指南**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

* [NIST 发布 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

* [如何配置连续导出](https://docs.microsoft.com/azure/security-center/continuous-export)

* [如何将警报流式传输到 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，以保护 Azure 资源。

* [如何配置工作流自动化和逻辑应用](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指南**： Microsoft 在 Data Box 设备上进行渗透测试和漏洞扫描。 你可以执行自己的渗透测试和漏洞扫描。 如果你选择这样做，请按照 Microsoft 交往规则确保你的渗透测试不违反 Microsoft 政策。 针对 Microsoft 托管的云基础结构、服务和应用程序，使用 Microsoft 的战略和对红色组合和活动站点渗透测试的执行。

* [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

责任：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
