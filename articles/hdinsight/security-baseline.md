---
title: HDInsight 的 Azure 安全基线
description: HDInsight 的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 93a5bcd77bb4f42d9099cc1ddb1b5c3130c19059
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010131"
---
# <a name="azure-security-baseline-for-hdinsight"></a>HDInsight 的 Azure 安全基线

HDInsight 的 Azure 安全基线包含可帮助您改进部署安全状态的建议。

此服务的基线取自[Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，它提供了有关如何使用最佳实践指南在 Azure 上保护云解决方案的建议。

有关详细信息，请参阅[Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

*有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虚拟网络上的网络安全组或 Azure 防火墙保护资源

**指南**：Azure HDInsight 中的外围安全是通过虚拟网络实现的。 企业管理员可以在虚拟网络内创建群集，并使用网络安全组 （NSG） 限制对虚拟网络的访问。 只有入站网络安全组规则中允许的 IP 地址才能与 Azure HDInsight 群集通信。 此配置可实现外围安全性。 部署在虚拟网络中的所有群集也将具有专用终结点，该终结点解析为虚拟网络内的专用 IP 地址，以便对群集网关进行专用 HTTP 访问。

要降低通过外渗来降低数据丢失的风险，请使用 Azure 防火墙限制 Azure HDInsight 群集的出站网络流量。

如何在虚拟网络中部署 Azure HDInsight 并借助网络安全组进行安全保护：https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

如何使用 Azure 防火墙限制 Azure HDInsight 群集的出站流量：https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 监视和记录 Vnet、子网和 NIC 的配置和流量

**指南**：使用 Azure 安全中心并修复用于保护 Azure HDInsight 群集的虚拟网络、子网和网络安全组的网络保护建议。 启用网络安全组 （NSG） 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 您还可以将 NSG 流日志发送到 Azure 日志分析工作区，并使用 Azure 流量分析提供有关 Azure 云中的流量流的见解。 Azure 流量分析的一些优点是能够可视化网络活动并识别热点、识别安全威胁、了解流量流模式和查明网络错误配置。

如何启用 NSG 流日志：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用和使用 Azure 流量分析：

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

了解 Azure 安全中心提供的网络安全：

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="13-protect-critical-web-applications"></a>1.3： 保护关键 Web 应用程序

**指导**：不适用;基准测试适用于托管 Web 应用程序的 Azure 应用服务或计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒绝与已知恶意 IP 地址的通信

**指南**：为了保护免受 DDoS 攻击，请在部署 Azure HDInsight 的虚拟网络上启用 Azure DDoS 标准保护。 使用 Azure 安全中心集成的威胁智能拒绝与已知恶意或未使用的 Internet IP 地址通信。

如何配置 DDoS 保护：

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

了解 Azure 安全中心集成威胁情报：

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5： 记录网络数据包和流日志

**指南**：为连接到用于保护 Azure HDInsight 群集的子网的 NSG 启用网络安全组 （NSG） 鞭打日志。 将 NSG 流日志记录到 Azure 存储帐户中，以生成流记录。 如果需要调查异常活动，则启用 Azure 网络观察程序数据包捕获。

如何启用 NSG 流日志：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用网络观察程序：

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6： 部署基于网络的入侵检测/入侵防御系统（IDS/IPS）

**指导**：要求可满足 Azure 安全控制 ID 1.1;将 Azure HDInsight 群集部署到虚拟网络中，并借助网络安全组 （NSG） 进行安全保护。

Azure HDInsight 有几个依赖项需要入站流量。 无法通过防火墙设备发送入站管理流量。 已知并发布了所需管理流量的源地址。 使用此信息创建网络安全组规则，以仅允许来自受信任位置的流量，从而保护群集的入站流量。

要降低通过外渗来降低数据丢失的风险，请使用 Azure 防火墙限制 Azure HDInsight 群集的出站网络流量。

如何在虚拟网络中部署 HDInsight，以及如何使用网络安全组进行安全保护：https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

了解 HDInsight 依赖项和防火墙使用情况：https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

HDInsight 管理 IP 地址：https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7： 管理 Web 应用程序的流量

**指导**：不适用;基准测试适用于托管 Web 应用程序的 Azure 应用服务或计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络服务标记定义连接到 Azure HDInsight 群集部署的子网的网络安全组 （NSG） 上的网络访问控件。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。

了解和使用 Azure HDInsight 的服务标记：

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 维护网络设备的标准安全配置

**指南**：为与 Azure HDInsight 群集相关的网络资源定义和实施标准安全配置。 在"Microsoft.HDInsight"和"Microsoft.Network"命名空间中使用 Azure 策略别名来创建自定义策略以审核或强制执行 Azure HDInsight 群集的网络配置。

您还可以使用 Azure 蓝图通过在单个蓝图定义中打包关键环境工件（如 Azure 资源管理器模板、RBAC 控件和策略）来简化大规模 Azure 部署。 轻松将蓝图应用于新订阅和环境，并通过版本控制微调控制和管理。

如何查看可用的 Azure 策略别名：

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何创建 Azure 蓝图：

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="110-document-traffic-configuration-rules"></a>1.10： 文档流量配置规则

**指南**：对网络安全组 （NSG） 以及与 Azure HDInsight 群集关联的网络安全和流量流相关的其他资源使用标记。 对于单个 NSG 规则，使用"描述"字段指定允许流量从网络传输的任何规则的业务需求和/或持续时间（等）。

使用与标记相关的任何内置 Azure 策略定义（如"要求标记及其值"）确保所有资源都使用标记创建，并通知您现有的未标记资源。

您可以使用 Azure PowerShell 或 Azure 命令行接口 （CLI） 来查找资源或根据资源标记执行操作。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何创建虚拟网络：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何创建具有安全配置的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自动工具监控网络资源配置并检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure HDInsight 部署相关的网络资源的更改。 在 Azure 监视器中创建警报，这些警报将在对关键网络资源的更改时触发。

如何查看和检索 Azure 活动日志事件：

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 监视器中创建警报：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="logging-and-monitoring"></a>日志记录和监视

*有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的时间同步源

**指南**：Microsoft 维护 Azure HDInsight 群集组件的时间源，您可以更新计算部署的时间同步。

如何为 Azure 计算资源配置时间同步：

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure 安全中心监视**：当前不可用

**责任**： 微软

### <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全日志管理

**指南**：您可以将 Azure HDInsight 群集并联到 Azure 监视器，以聚合群集生成的安全数据。 利用自定义查询来检测和响应环境中的威胁。 

如何将 Azure HDInsight 群集并联到 Azure 监视器：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

如何为 Azure HDInsight 群集创建自定义查询：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：为 HDInsight 群集启用 Azure 监视器，将其定向到日志分析工作区。 这将记录所有 Azure HDInsight 群集节点的相关群集信息和 OS 指标。

如何为 HDInsight 群集启用日志记录：

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

如何查询 HDInsight 日志：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4： 从操作系统收集安全日志

**指南**：将 Azure HDInsight 群集添加到 Azure 监视器。 确保使用的日志分析工作区已根据组织的合规性法规设置了日志保留期。

如何将 Azure HDInsight 群集并联到 Azure 监视器：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

如何配置日志分析工作区保留期：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全日志存储保留

**指南**：将 Azure HDInsight 群集添加到 Azure 监视器。 确保使用的 Azure 日志分析工作区已根据组织的合规性法规设置了日志保留期。

如何将 Azure HDInsight 群集并联到 Azure 监视器：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

如何配置日志分析工作区保留期：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="26-monitor-and-review-logs"></a>2.6： 监视和查看日志

**指南**： 使用 Azure 日志分析工作区查询查询 Azure HDInsight 日志：

如何为 Azure HDInsight 群集创建自定义查询：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 启用异常活动的警报

**指南**：使用 Azure 日志分析工作区监视和警报安全日志中与 Azure HDInsight 群集相关的异常事件。

如何管理 Azure 安全中心的警报：

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

如何对日志分析日志数据发出警报：

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反恶意软件日志记录

**指南**： Azure HDInsight 附带了为群集节点映像预安装并启用的 Clamscan，但您必须管理软件并手动聚合/监视 Clamcan 生成的任何日志。

了解夹子：

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="29-enable-dns-query-logging"></a>2.9： 启用 DNS 查询日志记录

**指南**：为 dns 日志记录实施第三方解决方案。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="210-enable-command-line-audit-logging"></a>2.10： 启用命令行审核日志记录

**指导**：根据每个节点手动配置控制台日志记录。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="identity-and-access-control"></a>标识和访问控制

*有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1： 维持管理帐户的清单

**指南**：维护在 Azure HDInsight 群集群集的群集预配期间创建的本地管理帐户的记录以及您创建的任何其他帐户。 此外，如果使用 Azure AD 集成，Azure AD 具有必须显式分配并因此可查询的内置角色。 使用 Azure AD PowerShell 模块执行临时查询以发现属于管理组成员的帐户。

此外，您可以使用 Azure 安全中心标识和访问管理建议。

如何使用 PowerShell 在 Azure AD 中获取目录角色：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 获取 Azure AD 中的目录角色成员：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

如何使用 Azure 安全中心监视标识和访问：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2： 在适用的情况下更改默认密码

**指南**：预配群集时，Azure 要求您为 Web 门户创建新密码，并保护命令程序 （SSH） 访问。 没有要更改的默认密码，但您可以为 SSH 和 Web 门户访问指定不同的密码。

在预配 Azure HDInsight 群集时如何设置密码：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3： 使用专用管理帐户

**指南**：将 Azure HDInsight 群集的身份验证与 Azure 活动目录集成。 围绕使用专用管理帐户创建政策和程序。

此外，您可以使用 Azure 安全中心标识和访问管理建议。

如何将 Azure HDInsight 身份验证与 Azure 活动目录集成：

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

如何使用 Azure 安全中心监视标识和访问：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4： 使用 Azure 活动目录的单一登录 （SSO）

**指南**：使用 Azure HDInsight ID 代理使用多重身份验证登录到企业安全包 （ESP） 群集，而无需提供任何密码。 如果您已登录到其他 Azure 服务（如 Azure 门户），则可以使用单个登录 （SSO） 体验登录到 Azure HDInsight 群集。

如何启用 Azure HDInsight ID 代理：

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure 活动目录的访问使用多重身份验证

**指南**：启用 Azure AD MFA 并遵循 Azure 安全中心标识和访问管理建议。 配置了企业安全包的 Azure HDInsight 群集可以连接到域，以便域用户可以使用其域凭据对群集进行身份验证并运行大数据作业。 启用多重身份验证 （MFA） 进行身份验证时，用户将面临提供第二个身份验证因子的挑战。

如何在 Azure 中启用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心内监视标识和访问：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用具有多重身份验证 （MFA） 的 PAW（特权访问工作站）来登录和配置 Azure HDInsight 群集和相关资源。

了解特权访问工作站：

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中启用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7： 从管理帐户记录和警报可疑活动

**指南**：配置了企业安全包的 Azure HDInsight 群集可以连接到域，以便域用户可以使用其域凭据进行身份验证。 当 AAD 环境中发生可疑或不安全活动时，可以使用 Azure 活动目录 （AAD） 安全报告生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

如何识别标记为有风险活动的 AAD 用户：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

如何监视 Azure 安全中心中的用户标识和访问活动：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从已批准的位置管理 Azure 资源

**指南**：配置了企业安全包的 Azure HDInsight 群集可以连接到域，以便域用户可以使用其域凭据进行身份验证。 使用条件访问命名位置仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

如何在 Azure 中配置命名位置：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活动目录

**指南**：使用 Azure 活动目录 （AAD） 作为中央身份验证和授权系统。 AAD 对静态和传输中的数据使用强加密来保护数据。 AAD 还对用户凭据进行盐渍、哈希和安全地存储。

配置了企业安全包 （ESP） 的 Azure HDInsight 群集可以连接到域，以便域用户可以使用其域凭据对群集进行身份验证。

如何创建和配置 AAD 实例：

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

如何在 Azure HDInsight 中使用 Azure 活动目录域服务配置企业安全包：

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期审查和协调用户访问

**指南**：使用 Azure HDInsight 群集的 Azure 活动目录 （AAD） 身份验证。 AAD 提供日志来帮助发现陈旧的帐户。 此外，使用 Azure 标识访问审核可高效地管理组成员身份、对企业应用程序的访问和角色分配。 可以定期查看用户的访问权限，以确保只有正确的用户才能继续访问。 

如何使用 Azure 标识访问审核：

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 监视访问已停用帐户的尝试

**指南**：使用 Azure 活动目录 （AAD） 登录和审核日志监视访问已停用帐户的尝试;这些日志可以集成到任何第三方 SIEM/监视工具中。

您可以通过为 AAD 用户帐户创建诊断设置、将审核日志和登录日志发送到 Azure 日志分析工作区来简化此过程。 在 Azure 日志分析工作区中配置所需的警报。

如何将 Azure 活动日志集成到 Azure 监视器中：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帐户登录行为偏差警报

**指南**：配置了企业安全包 （ESP） 的 Azure HDInsight 群集可以连接到域，以便域用户可以使用其域凭据对群集进行身份验证。  使用 Azure 活动目录 （AAD） 风险检测和身份保护功能配置自动响应，以检测与用户身份相关的可疑操作。 此外，您可以将数据引入 Azure Sentinel 以进行进一步调查。

如何查看 AAD 风险登录：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和启用身份保护风险策略：

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支持方案期间向 Microsoft 提供对相关客户数据的访问

**指导**：不可用;Azure HDInsight 尚不支持客户密码箱。

客户密码箱支持服务列表：https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="data-protection"></a>数据保护

*有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 维护敏感信息的清单

**指南**：使用与 Azure HDInsight 部署相关的资源上的标记，以帮助跟踪存储或处理敏感信息的 Azure 资源。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 Azure HDInsight 群集和任何关联的存储帐户应通过虚拟网络/子网分隔，在网络安全组 （NSG） 或 Azure 防火墙中正确标记和保护。 群集数据应包含在安全的 Azure 存储帐户或 Azure 数据湖存储（第 1 代或第 2 代）中。

为 Azure HDInsight 群集选择存储选项：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

如何保护 Azure 数据湖存储：

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

如何保护 Azure 存储帐户：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 监控和阻止未经授权传输敏感信息

**指南**：对于存储或处理敏感信息的 Azure HDInsight 群集，请使用标记将群集和相关资源标记为敏感。 要降低通过外渗来降低数据丢失的风险，请使用 Azure 防火墙限制 Azure HDInsight 群集的出站网络流量。

对于由 Microsoft 管理的基础平台，Microsoft 将所有客户内容视为敏感内容，并竭尽全力防止客户数据丢失和暴露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已经实施并维护了一套强大的数据保护控制和功能。

如何使用 Azure 防火墙限制 Azure HDInsight 群集的出站流量：

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

了解 Azure 中的客户数据保护：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：当前不可用

**责任**： 共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：加密传输中的所有敏感信息。 确保连接到 Azure HDInsight 群集或群集数据存储（Azure 存储帐户或 Azure 数据存储第 1/Gen2）的任何客户端能够协商 TLS 1.2 或更高。 默认情况下，Microsoft Azure 资源将协商 TLS 1.2。 

了解传输中的 Azure 数据湖存储加密：

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

了解传输中的 Azure 存储帐户加密：

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure 安全中心监视**：是

**责任**： 共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活动发现工具识别敏感数据

**指南**：数据标识、分类和损失预防功能尚未可用于 Azure 存储或计算资源。 如果需要符合性，则实施第三方解决方案。

对于由 Microsoft 管理的基础平台，Microsoft 将所有客户内容视为敏感内容，并竭尽全力防止客户数据丢失和暴露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已经实施并维护了一套强大的数据保护控制和功能。

了解 Azure 中的客户数据保护：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：当前不可用

**责任**： 共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制对资源的访问

**指南**：使用 Azure HDInsight 企业安全包 （ESP），您可以使用 Apache Ranger 为存储在文件、文件夹、数据库、表和行/列中的数据创建和管理细粒度的访问控制和数据模糊策略。 hadoop 管理员可以使用 Apache Ranger 中的这些插件配置基于角色的访问控制 （RBAC）， 以保护 Apache Hive、HBase、Kafka 和 Spark。

使用 Apache Ranger 配置 RBAC 策略允许您将权限与组织中的角色相关联。 此层抽象可以更轻松地确保用户仅仅有履行工作责任所需的权限。

HDInsight 中具有 Azure 活动目录域服务的企业安全包配置：

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Azure HDInsight 中的企业安全性概述：

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基于主机的数据丢失防护来实施访问控制

**指南**：对于存储或处理敏感信息的 Azure HDInsight 群集，请使用标记将群集和相关资源标记为敏感。 数据标识、分类和损失预防功能尚未可用于 Azure 存储或计算资源。 如果需要符合性，则实施第三方解决方案。

对于由 Microsoft 管理的基础平台，Microsoft 将所有客户内容视为敏感内容，并竭尽全力防止客户数据丢失和暴露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已经实施并维护了一套强大的数据保护控制和功能。

了解 Azure 中的客户数据保护：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：当前不可用

**责任**： 共享

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 静态加密敏感信息

**指南**：如果使用 Azure SQL 数据库存储 Apache Hive 和 Apache Oozie 元数据，请确保 SQL 数据始终保持加密状态。 对于 Azure 存储帐户和数据存储湖存储（第 1 代或第 2 代），建议允许 Microsoft 管理加密密钥，但是，您可以选择管理自己的密钥。

如何管理 Azure 存储帐户的加密密钥：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

如何使用客户托管加密密钥创建 Azure 数据湖存储：

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

了解 Azure SQL 数据库的加密：

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

如何使用客户托管密钥为 SQL 数据库配置透明数据加密：

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Azure 安全中心监视**：是

**责任**： 共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 对关键 Azure 资源的更改进行日志和警报

**指南**：为与 Azure HDInsight 群集关联的 Azure 存储帐户配置诊断设置，以监视和记录针对群集数据的所有 CRUD 操作。 为与 Azure HDInsight 群集关联的任何存储帐户或数据湖存储启用审核。

如何为 Azure 存储帐户启用其他日志记录/审核：

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

如何为 Azure 数据湖存储启用其他日志记录/审核：

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Azure 安全中心监视**：是

**责任**： 客户

## <a name="vulnerability-management"></a>漏洞管理

*有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1： 运行自动漏洞扫描工具

**指导**：实施第三方漏洞管理解决方案。

或者，如果您有 Rapid7、Qualys 或任何其他漏洞管理平台订阅，则可以使用脚本操作在 Azure HDInsight 群集节点上安装漏洞评估代理，并通过相应的门户管理节点。

如何手动安装 Rapid7 代理：

https://insightvm.help.rapid7.com/v1.0/docs/agent-installation-on-linux

如何手动安装 Qualys 代理：

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

如何使用脚本操作：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动化操作系统修补程序管理解决方案

**指南**：已为群集节点映像启用了自动系统更新，但必须定期重新启动群集节点以确保应用更新。

微软维护和更新基本 Azure HDInsight 节点映像。

如何为 HDInsight 群集配置操作系统修补计划：

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Azure 安全中心监视**：当前不可用

**责任**： 共享

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自动化的第三方软件修补程序管理解决方案

**指南**：使用脚本操作或其他机制来修补 Azure HDInsight 群集。 新创建的群集将始终包含最新的可用更新，其中包括最新的安全修补程序。

如何为基于 Linux 的 Azure HDInsight 群集配置操作系统修补计划：

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

如何使用脚本操作：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4： 比较背对背漏洞扫描

**指南**：实施第三方漏洞管理解决方案，该解决方案能够随着时间的推移比较漏洞扫描。 如果您有 Rapid7 或 Qualys 订阅，则可以使用该供应商的门户查看和比较背对背的漏洞扫描。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5： 使用风险评级流程确定修复已发现漏洞的优先级

**指南**：使用常见的风险评分程序（例如常见漏洞评分系统）或第三方扫描工具提供的默认风险评级。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="inventory-and-asset-management"></a>清单和资产管理

*有关详细信息，请参阅[安全控制：库存和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1： 使用 Azure 资产发现

**指南**：使用 Azure 资源图查询/发现订阅中的所有资源（如计算、存储、网络、端口和协议等），包括 Azure HDInsight 群集。  确保租户中具有适当的（读取）权限，并能够枚举订阅中的所有 Azure 订阅和资源。

尽管可以通过资源图发现经典 Azure 资源，但强烈建议今后创建和使用 Azure 资源管理器资源。

如何使用 Azure 图形创建查询：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 订阅：

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

了解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="62-maintain-asset-metadata"></a>6.2： 维护资产元数据

**指南**：将标记应用于 Azure 资源，使元数据以逻辑方式将它们组织到分类中。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3： 删除未经授权的 Azure 资源

**指南**：在适当情况下，使用标记、管理组和单独的订阅来组织和跟踪资产。 定期协调库存，确保及时从订阅中删除未经授权的资源。

如何创建其他 Azure 订阅：

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组：

https://docs.microsoft.com/azure/governance/management-groups/create

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单

**指南**：为计算资源定义已批准的 Azure 资源和已批准的软件的列表

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5： 监视未经批准的 Azure 资源

**指南**：使用 Azure 策略对客户订阅中可创建的资源类型使用以下内置策略定义进行限制：

- 不允许的资源类型

- 允许的资源类型

使用 Azure 资源图查询/发现订阅中的资源。 确保环境中的所有 Azure 资源都获得批准。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 图形创建查询：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6： 监控计算资源中未经批准的软件应用程序

**指南**：实施第三方解决方案，以监视未经批准的软件应用程序的群集节点。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7： 删除未经批准的 Azure 资源和软件应用程序

**指南**：使用 Azure 资源图查询/发现订阅中的所有资源（如计算、存储、网络、端口和协议等），包括 Azure HDInsight 群集。  删除发现的任何未经批准的 Azure 资源。 对于 Azure HDInsight 群集节点，请实施第三方解决方案，以在未经批准的软件上删除或发出警报。

如何使用 Azure 图形创建查询：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="68-use-only-approved-applications"></a>6.8： 仅使用已批准的应用程序

**指南**：对于 Azure HDInsight 群集节点，实现第三方解决方案以防止未经授权的软件执行。


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="69-use-only-approved-azure-services"></a>6.9： 仅使用已批准的 Azure 服务

**指南**：使用 Azure 策略对客户订阅中可创建的资源类型使用以下内置策略定义进行限制：

- 不允许的资源类型

- 允许的资源类型

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 策略拒绝特定资源类型：https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="610-implement-approved-application-list"></a>6.10： 实施已批准的申请列表

**指南**：对于 Azure HDInsight 群集节点，实现第三方解决方案以防止执行未经授权的文件类型。


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器交互的能力

**指南**：使用 Azure 条件访问通过为"Microsoft Azure 管理"应用配置"阻止访问"来限制用户与 Azure 资源管理器交互的能力。

如何配置条件访问以阻止对 Azure 资源管理器的访问：https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12： 限制用户在计算资源中执行脚本的能力

**指导**：不适用;这不适用于 Azure HDInsight，因为群集的用户（非管理员）不需要访问各个节点来运行作业。 群集管理员对所有群集节点具有根访问权限。

**Azure 安全中心监视**：当前不可用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13： 物理或逻辑上隔离高风险应用程序

**指导**：不适用;基准测试适用于托管 Web 应用程序的 Azure 应用服务或计算资源。

**Azure 安全中心监视**：当前不可用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

*有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：在"Microsoft.HDInsight"命名空间中使用 Azure 策略别名创建自定义策略以审核或强制执行 HDInsight 群集的网络配置。

如何查看可用的 Azure 策略别名：

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2： 建立安全的操作系统配置

**指南**： 由微软管理和维护的 Azure HDInsight 操作系统映像。 负责为群集节点的操作系统实施安全配置的客户。 


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure 策略 [拒绝] 和 [部署（如果不存在））为 Azure HDInsight 群集和相关资源强制实施安全设置。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

了解 Azure 策略效果：

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4： 维护安全的操作系统配置

**指南**： 由微软管理和维护的 Azure HDInsight 操作系统映像。 负责实现操作系统级状态配置的客户。


**Azure 安全中心监视**：当前不可用

**责任**： 共享

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存储 Azure 资源的配置

**指南**：如果使用自定义 Azure 策略定义，请使用 Azure DevOps 或 Azure 存储库安全地存储和管理代码。

如何在 Azure DevOps 中存储代码：

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 存储库文档：

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存储自定义操作系统映像

**指导**：不适用;自定义映像不适用于 Azure HDInsight。

**Azure 安全中心监视**：当前不可用

**责任**：不适用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系统配置管理工具

**指南**：使用"Microsoft.HDInsight"命名空间中的 Azure 策略别名创建自定义策略以警报、审核和强制执行系统配置。 此外，开发用于管理策略异常的流程和管道。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指南**：实现第三方解决方案，以保持群集节点操作系统所需的状态。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实现自动化配置监视

**指南**：在"Microsoft.HDInsight"命名空间中使用 Azure 策略别名创建自定义策略以审核或强制执行 HDInsight 群集的配置。

如何查看可用的 Azure 策略别名：

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 对操作系统实施自动配置监控

**指南**：实施第三方解决方案来监视群集节点操作系统的状态。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="711-manage-azure-secrets-securely"></a>7.11： 安全管理 Azure 机密

**指南**： Azure HDInsight 包括自带密钥 （BYOK） 支持 Apache Kafka。 借助此功能，你可以拥有和管理用于加密静态数据的密钥。

Azure HDInsight 中的所有托管磁盘都受 Azure 存储服务加密 （SSE） 的保护。 默认情况下，这些磁盘上的数据使用 Microsoft 托管密钥进行加密。 如果启用 BYOK，则为 Azure HDInsight 提供加密密钥，以便使用 Azure 密钥保管库对其进行使用和管理。

密钥保管库也可以与 Azure HDInsight 部署一起使用，以管理群集存储的密钥（Azure 存储帐户和 Azure 数据湖存储）

如何在 Azure HDInsight 上为 Apache Kafka 带来您自己的密钥：

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

如何管理 Azure 存储帐户的加密密钥：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12： 安全、自动地管理身份

**指南**：托管标识可用于 Azure HDInsight 中，以允许群集访问 Azure 活动目录域服务、访问 Azure 密钥保管库或访问 Azure 数据湖存储 Gen2 中的文件。

使用 Azure HDInsight 了解托管标识：

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13： 消除意外的凭据暴露

**指南**：如果使用与 Azure HDInsight 部署相关的任何代码，则可以实现凭据扫描程序来标识代码中的凭据。 凭据扫描程序还将鼓励将发现的凭据移动到更安全的位置，如 Azure 密钥保管库。 

如何设置凭据扫描程序：

https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="malware-defense"></a>恶意软件防护

*有关详细信息，请参阅[安全控制：恶意软件防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1： 使用集中管理的反恶意软件

**指南**： Azure HDInsight 附带了为群集节点映像预安装并启用的 Clamscan，但您必须管理软件并手动聚合/监视 Clamcan 生成的任何日志。

了解 Azure HDInsight 的夹子：

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：要上载到非计算 Azure 资源的预扫描文件

**指南**：在支持 Azure 服务的基础主机上启用了 Microsoft 反恶意软件，但它不在客户内容上运行。

预扫描上载到与 Azure HDInsight 群集部署相关的任何 Azure 资源的任何文件，如数据湖存储、Blob 存储等。在这些情况下，Microsoft 无法访问客户数据。

了解适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件：

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 安全中心监视**：当前不可用

**责任**： 共享

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3： 确保更新反恶意软件和签名

**指南**： Azure HDInsight 附带了为群集节点映像预安装并启用的 Clamscan。 Clamscan 将自动执行引擎和定义更新，但是，需要手动执行日志的聚合和管理。

了解 Azure Azure HDInsight 的夹子：

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="data-recovery"></a>数据恢复

*有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1： 确保定期自动备份

**指南**：在 HDInsight 群集数据存储中使用 Azure 存储帐户时，请选择适当的冗余选项（LRS、ZRS、GRS、RA-GRS）。  将 Azure SQL 数据库用于 Azure HDInsight 群集数据存储时，请配置活动异地复制。

如何为 Azure 存储帐户配置存储冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy

如何为 Azure SQL 数据库配置冗余：

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 执行完整的系统备份并备份任何客户托管密钥

**指南**：在 Azure HDInsight 群集数据存储中使用 Azure 存储帐户时，请选择适当的冗余选项（LRS、ZRS、GRS、RA-GRS）。 如果对 Azure HDInsight 部署的任何部分使用 Azure 密钥保管库，请确保备份密钥。

为 Azure HDInsight 群集选择存储选项：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

如何为 Azure 存储帐户配置存储冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy

如何在 Azure 中备份密钥保管库密钥：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户托管密钥

**指南**：如果 Azure 密钥保管库正在与 Azure HDInsight 部署一起使用，则测试备份的客户托管密钥的还原。

如何在 Azure HDInsight 上为 Apache Kafka 带来您自己的密钥：

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

如何在 Azure 中还原密钥保管库密钥：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理密钥

**指南**：如果 Azure 密钥保管库正在与 Azure HDInsight 部署一起使用，请在密钥保管库中启用软删除，以保护密钥免遭意外或恶意删除。

如何在 Azure 密钥保管库中启用软删除：

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="incident-response"></a>事件响应

*有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指南

**指导**：确保有书面的事件响应计划，定义人员的角色以及事件处理/管理的各个阶段。

如何在 Azure 安全中心内配置工作流自动化：

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2： 创建事件评分和优先级处理过程

**指南**：安全中心为警报分配严重性，以帮助您确定处理每个警报的顺序，以便在资源受到威胁时，您可以马上到达它。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="103-test-security-response-procedures"></a>10.3： 测试安全响应程序

**指导**：进行练习，以常规节奏测试系统的事件响应能力。 找出薄弱环节和差距，并根据需要修订计划。请参阅 NIST 出版物：IT 计划和功能测试、培训和锻炼计划指南：https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并配置安全事件的警报通知

**指南**：如果 Microsoft 安全响应中心 （MSRC） 发现您的数据已被非法或未经授权的方访问，Microsoft 将使用安全事件联系信息与您联系。

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

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6： 自动响应安全警报

**指南**：使用 Azure 安全中心的工作流自动化功能，通过安全警报和建议上的"逻辑应用"自动触发响应。

如何配置工作流自动化和逻辑应用：

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

*有关详细信息，请参阅[安全控制：渗透测试和红队练习](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：对 Azure 资源进行定期渗透测试，并确保在 60 天内补救所有关键安全发现

**指南**：请遵循 Microsoft 的接用规则，以确保您的渗透测试不违反 Microsoft 策略：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

您可以在此处找到有关 Microsoft 针对 Microsoft 托管云基础架构、服务和应用程序进行红色团队和实时站点渗透测试的战略和实施的详细信息：https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心监视**：不适用

**责任**： 共享

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 了解有关[Azure 安全基线的更多信息](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
