---
title: PCI DSS 的安全 Web 应用 |微软文档
description: 此示例应用实现了安全最佳实践，在 Azure 上开发时可改进应用程序和组织的安全状况。
keywords: na
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4fe612db65d985be2f1f1c81d03c3ee735c03889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992609"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>为 PCI 应用开发安全基础结构

## <a name="overview"></a>概述

此支付卡行业 （PCI） 应用的安全基础结构为部署支付卡行业平台提供了指导，即适合收集、存储和检索持卡人数据的服务 （PaaS） 环境。 此解决方案自动完成适用于常用参考体系结构的 Azure 资源的部署和配置，演示了客户如何通过多种方式达到特定的安全和符合性要求，是客户在 Azure 上生成和配置其解决方案的基础。 该解决方案实现了类似于支付卡行业数据安全标准 （PCI DSS 3.2） 的要求子集。

此示例自动部署带有预配置安全控制的 PaaS Web 应用程序参考体系结构，帮助客户实现类似于 PCI DSS 3.2 要求的合规性。 此解决方案包含 Azure 资源管理器模板和 PowerShell 脚本，指导用户进行资源部署和配置。

您应该按顺序按照本文中描述的步骤操作，以确保正确配置应用程序组件。 数据库、Azure 应用服务、Azure 密钥保管库实例和 Azure 应用程序网关实例彼此依赖。

部署脚本设置基础结构。 运行部署脚本后，需要在 Azure 门户中执行一些手动配置，以将组件和服务链接在一起。

此示例面向 Azure 上经验丰富的开发人员，他们在零售行业中工作，并希望使用安全的 Azure 基础结构构建零售应用。

> [!NOTE]
> 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。

未经修改就向此环境部署应用程序并不足以完全满足 PCI DSS 3.2 所设定的要求。 注意以下事项：

- 本体系结构提供一个基线，帮助客户按符合 PCI DSS 3.2 要求的方式使用 Azure。
- 客户负责针对使用本体系结构构建的任何解决方案开展相应的安全与合规评估；具体要求根据客户的每种实施方式具体情况而异。

在开发和部署此应用程序时，您将了解如何：

- 创建 Azure 密钥保管库实例并从中存储和检索机密。
- 为 Azure SQL 部署 Azure 数据库，设置安全数据，并授权访问它。
- 使用应用服务环境部署 Azure Web 应用，该环境与前端防火墙 aEcess 是专用隔离的。
- 使用使用[OWASP 前 10 个规则集](https://coreruleset.org/)的防火墙创建和配置 Azure 应用程序网关实例。
- 使用 Azure 服务对传输中和静态的数据进行加密。
- 设置 Azure 策略和蓝色打印件以评估合规性

开发和部署此应用后，您将设置以下示例 Web 应用以及描述的配置和安全措施。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件
该应用程序是一个典型的n层应用程序，具有三层。 此处显示了集成了监视和密钥管理组件的前端、后端和数据库层：

![应用体系结构](./media/secure-pci-web-app/architecture.png)

该体系结构由以下组件组成：

- [应用服务环境 v2](https://docs.microsoft.com/azure/app-service/environment/intro/). 为我们的应用程序体系结构提供应用服务环境和负载均衡器。
- [Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/)。 为我们的应用程序体系结构提供网关和防火墙。
- [应用程序见解](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)。 在多个平台上提供可扩展的应用程序性能管理 （APM） 服务。
- [Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault/)。 存储和加密应用的机密，并管理围绕其创建访问策略。
- [Azure 活动目录](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/)。提供基于云的标识和访问管理服务、登录和访问资源。
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)。 提供托管域的服务。
- [Azure 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/)。 扩展应用程序并为服务创建高可用性
- [Azure 存储空间](https://docs.microsoft.com/azure/storage/common/storage-introduction/)。 为现代数据存储方案提供解决方案。
- [Azure Web 应用程序](https://docs.microsoft.com/azure/app-service/overview/)。  提供基于 HTTP 的服务来托管 Web 应用程序。
- [Azure 数据库为 AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/)。 安全地存储我们应用的数据。
- [Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/overview/)。 提供规范和符合某些标准和要求。
- [Azure 安全中心](https://docs.microsoft.com/azure/security-center/)
- [Azure 策略](https://docs.microsoft.com/azure/governance/policy/overview). 评估资源是否符合分配的策略。

## <a name="threat-model"></a>威胁模型
威胁建模是识别业务和应用程序的潜在安全威胁，然后确保制定适当的缓解计划的过程。

此示例使用[Microsoft 威胁建模工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)为安全示例应用实现威胁建模。 通过绘制组件和数据流图，可以在开发过程的早期识别问题和威胁。 这样可以节省时间和金钱。

这是示例应用的威胁模型：

![威胁模型](./media/secure-pci-web-app/threat-model.png)

以下屏幕截图中显示了威胁建模工具生成的一些示例威胁和潜在漏洞。 威胁模型概述了暴露的攻击面，并提示开发人员考虑如何缓解问题。

![威胁模型输出](./media/secure-web-app/threat-model-output.png)

例如，通过清理用户输入和使用 Azure 数据库中的存储函数进行 PostgreSQL 来缓解上述威胁模型输出中的 SQL 注入。 此缓解措施可防止在数据读取和写入期间任意执行查询。

开发人员通过缓解威胁模型输出中的每个威胁来提高系统的整体安全性。

## <a name="deployment"></a>部署
### <a name="prerequisites"></a>先决条件
要启动和运行应用程序，您需要安装这些工具：

- 用于修改和查看应用程序代码的代码编辑器。 [可视化工作室代码](https://code.visualstudio.com/)是一个开源选项。
- [开发计算机上的 Azure CLI。](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest)
- [Git](https://git-scm.com/)在你的系统上。 Git 用于在本地克隆源代码。
- [jq，](https://stedolan.github.io/jq/)一种以用户友好方式查询JSON的UNIX工具。

此示例由 JSON 配置文件和 PowerShell 脚本组成，这些脚本由 Azure 资源管理器的 API 服务处理，用于在 Azure 中部署资源。 [此处](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM)提供了详细的部署说明。

#### <a name="quickstart"></a>快速入门

1.  将[此](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) GitHub 存储库克隆或下载到本地工作站。
2.  查看 0-Setup-AdministrativeAccountAndPermission.md 并运行所提供的命令。
3.  使用 Contoso 示例数据部署测试解决方案，或试用初始生产环境。

    此脚本部署 Azure 资源，以便使用 Contoso 示例数据演示 Web 存储。

在此示例中，您将运行将 Web 应用部署到应用服务并创建资源的部署脚本。

在 Azure 上部署应用的方法有很多种，包括：

- Azure 资源管理器模板
- PowerShell
- Azure CLI
- Azure 门户
- Azure DevOps

## <a name="guidance-and-recommendations"></a>指导和建议

### <a name="network"></a>网络
体系结构定义了一个地址空间为 10.200.0.0/16 的专用虚拟网络。
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>网络安全组
网络安全组（https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)包含允许或拒绝虚拟网络中的流量的访问控制列表 （ACL）。 网络安全组可用于在子网或单个 VM 级别保护流量。 存在以下网络安全组：

- 应用程序网关有 1 个网络安全组
- 应用服务环境有 1 个网络安全组
- Azure SQL 数据库有 1 个网络安全组
- 守护主机有 1 个网络安全组

每个网络安全组都打开特定的端口和协议，以便解决方案可以安全、正确地工作。 此外，为每个网络安全组启用了以下配置：

- 诊断日志和事件（https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)已启用并存储在存储帐户中）
- Azure 监视器日志已连接到网络安全组的诊断程序（https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG 配置
应用服务环境的 NSG 配置应配置为下图所示。

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

每个子网与其对应的网络安全组相关联。

### <a name="config"></a>Config
子网配置如下图所示。
 ![Config](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
域名系统 （DNS） 负责将网站或服务名称翻译（或解析）到其 IP 地址。 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) 是 DNS 域的托管服务，它使用 Azure 基础结构提供名称解析。 通过在 Azure 中托管域，用户可以使用与其他 Azure 服务相同的凭据、API、工具和账单来管理 DNS 记录。 Azure DNS 还支持 DNS 专用域。

### <a name="protect-data"></a>保护数据
为了帮助保护云中的数据，需要考虑数据可能出现的状态以及可用于该状态的控件。 Azure 数据安全与加密的最佳做法与以下数据状态相关：

- 静态：包括物理媒体（磁盘或光盘）上以静态方式存在的所有信息存储对象、容器和类型。
- 传输中：在各组件、位置或程序间传输数据时，数据处于“传输中”状态。 例如通过网络、通过服务总线（从本地到云，反之亦然，包括诸如 ExpressRoute 的混合连接）进行传输，或在输入/输出过程。

### <a name="azure-storage"></a>Azure 存储
为了满足静态的加密数据要求，所有[Azure 存储](https://azure.microsoft.com/services/storage/)都使用 Azure 密钥保管库来维护对访问和加密数据的密钥的控制。 这有助于保护持卡人数据，以支持 PCI DSS 3.2 定义的组织安全承诺和符合性要求。

### <a name="azure-disk-encryption"></a>Azure 磁盘加密
Azure 磁盘加密利用 Windows 的 BitLocker 功能，为数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

### <a name="azure-sql-database"></a>Azure SQL 数据库
Azure SQL 数据库实例使用以下数据库安全措施：

- 使用 [Active Directory 身份验证和授权](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)可在一个中心位置集中管理数据库用户和其他 Microsoft 服务的标识。
- [SQL 数据库审核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/)跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。
- Azure SQL 数据库配置为使用[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)，它对数据库、相关备份和事务日志文件进行实时加密和解密，以保护静态信息。 透明数据加密可确保存储的数据免遭他人未经授权的访问。
- 在授予相应的权限前，[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)会阻止对数据库服务器的所有访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。
- [SQL 威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)可以通过为可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问模式提供安全警报，在发生潜在威胁时进行检测和响应。
- [加密列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)可以确保敏感数据永远不会在数据库系统中以明文形式显示。 启用数据加密后，只有具有密钥访问权限的客户端应用程序或应用程序服务器才能访问明文数据。
- [SQL 数据库动态数据掩码](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)通过对非特权用户或应用程序模糊化敏感数据来限制此类数据的泄漏。 动态数据掩码可以自动发现潜在的敏感数据，并建议应用合适的掩码。 这有助于识别和减少数据访问，避免通过未经授权的访问退出数据库。 客户需要负责调整动态数据掩码设置，使之遵循其数据库架构。

### <a name="identity-management"></a>身份管理
以下技术在 Azure 环境中提供用于管理持卡人数据访问的功能：

- Azure Active Directory 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 解决方案的所有用户（包括访问 Azure SQL 数据库的用户）都在 Azure Active Directory 中创建。
- 使用 Azure Active Directory 对应用程序执行身份验证。 有关详细信息，请参阅[将应用程序与 Azure 活动目录集成](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration)。 此外，数据库列加密使用 Azure Active Directory 对访问 Azure SQL 数据库的应用程序进行身份验证。 有关详细信息，请参阅[如何保护 Azure SQL 数据库中的敏感数据](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- Azure 基于角色的访问控制使管理员能够定义细粒度的访问权限，以仅授予用户执行作业所需的访问量。 无需向每个用户授予 Azure 资源的不受限权限，管理员可以只允许使用特定的操作来访问持卡人数据。 订阅访问仅限于订阅管理员。
- Azure Active Directory Privileged Identity Management 使客户能够最大限度地减少有权访问持卡人数据等特定信息的用户数量。 管理员可以使用 Azure Active Directory Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 还可以根据需要，使用此功能来实施按需、实时的管理访问。
- Azure 活动目录标识保护检测影响组织身份的潜在漏洞，配置自动响应，以检测到与组织标识相关的可疑操作，并调查可疑事件采取适当的措施来解决它们。

### <a name="secrets-management"></a>机密管理
此解决方案使用 Azure Key Vault 管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Azure Key Vault 功能可帮助客户保护和访问此类数据：

- 根据需要配置高级访问权限策略。
- 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
- Key Vault 中的所有密钥和机密都有过期日期。
- Key Vault 中的所有密钥受专用硬件安全模块的保护。 密钥类型为受 HSM 保护的 2048 位 RSA 密钥。
- 使用密钥保管库，您可以加密密钥和机密（如身份验证密钥、存储帐户密钥、数据加密密钥、通过使用受硬件安全模块 （HSM） 保护的密钥，使用 PFX 文件和密码
- 使用 RBAC 将权限分配给特定范围内的用户、组和应用程序。
- 使用密钥保管库通过自动续订管理 TLS 证书。
- Key Vault 的诊断日志已启用，其保留期至少为 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作。

### <a name="azure-security-center"></a>Azure 安全中心
借助 Azure 安全中心，客户可在工作负载中集中应用和管理安全策略、限制威胁暴露，以及检测和应对攻击。 此外，Azure 安全中心会访问 Azure 服务的现有配置，以提供配置与服务建议来帮助改善安全状况和保护数据。

Azure 安全中心使用各种检测功能，提醒客户针对其环境的潜在攻击。 这些警报包含有关触发警报的内容、目标资源以及攻击源的重要信息。 Azure 安全中心有一组预定义的安全警报，这些警报在出现威胁或可疑活动时触发。 客户可以使用 Azure 安全中心的自定义警报规则，根据从环境中收集到的数据定义新的安全警报。

Azure 安全中心提供区分优先级的安全警报和事件，让客户更轻松地发现和解决潜在安全问题。 针对检测到的每种威胁生成威胁智能报告，帮助事件响应团队调查和解决威胁。

### <a name="azure-application-gateway"></a>Azure 应用程序网关
体系结构使用配置了 Web 应用程序防火墙并启用了 OWASP 规则集的应用程序网关，来降低安全漏洞风险。 其他功能包括：

- 端到端 SSL
- 禁用 TLS v1.0 和 v1.1
- 启用 TLSv1.2
- Web 应用程序防火墙（预防模式）
- 结合 OWASP 3.0 规则集的保护模式
- 启用诊断日志记录
- 自定义运行状况探测
- Azure 安全中心和 Azure 顾问提供额外的保护和通知。 Azure 安全中心还提供信誉系统。

### <a name="logging-and-auditing"></a>日志记录和审核
Azure 服务广泛记录系统和用户活动以及系统运行状况：

- [活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供对订阅中资源执行的操作的深入信息。 活动日志可帮助确定操作的发起方、发生的时间和状态。
- [诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、Azure 存储日志、密钥保管库审核日志以及应用程序网关访问和防火墙日志。 所有诊断日志都将写入到集中式加密 Azure 存储帐户以进行存档。 保留期是允许用户配置的，最长为 730 天，具体取决于组织的保留期要求。

### <a name="azure-monitor-logs"></a>Azure Monitor 日志
这些日志合并在[Azure 监视器日志](https://azure.microsoft.com/services/log-analytics/)中，用于处理、存储和仪表板报告。 收集后，数据在 Log Analytics 工作区内按数据类型整理到不同的表中，这样即可不考虑最初来源而集中分析所有数据。 此外，Azure 安全中心与 Azure 监视器日志集成，允许客户使用 Kusto 查询访问其安全事件数据并将其与其他服务的数据合并。

以下 Azure[监视解决方案](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)作为此体系结构的一部分包含在内：

- [活动目录评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：活动目录运行状况检查解决方案在常规时间间隔内评估服务器环境的风险和运行状况，并提供特定于已部署服务器基础结构的建议的优先级列表。
- [SQL 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)：SQL 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并为客户提供特定于部署服务器基础结构的优先建议列表。
- [代理运行状况](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：代理运行状况解决方案报告部署的代理数量及其地理分布，以及响应的代理数以及提交操作数据的代理数。
- [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：Activity Log Analytics 解决方案可帮助分析客户所有 Azure 订阅的 Azure 活动日志。

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) 通过使组织能够审核、创建警报和存档数据（包括在用户的 Azure 资源中跟踪 API 调用），帮助用户跟踪性能、维护安全性和确定趋势。

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理服务。 Application Insights 可检测性能异常，客户可以使用它来监视实时 Web 应用程序。 它包含强大的分析工具来帮助客户诊断问题，了解用户在应用中实际执行了哪些操作。 它旨在帮助客户持续提高性能和可用性。

### <a name="azure-key-vault"></a>Azure Key Vault
为存储密钥的组织创建保管库，并维护操作任务的问责制，如下所示：

- Key Vault 中存储的数据包括：

   - Application Insights 密钥
   - 数据存储访问密钥
   - 连接字符串
   - 数据表名
   - 用户凭据

- 根据需要配置高级访问权限策略
- 在定义 Key Vault 访问策略时，尽量将访问密钥和机密的必需权限降至最低
- Key Vault 中的所有密钥和机密都有过期日期
- 密钥保管库中的所有密钥都受 HSM [密钥类型 + HSM 保护 2048 位 RSA 密钥] 保护。
- 使用基于角色的访问控制 （RBAC） 授予所有用户/标识所需的最低权限
- 应用程序共享 Key Vault 的前提是彼此信任且需要在运行时访问相同的机密
- Key Vault 的诊断日志已启用，其保留期至少为 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作

### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
需要配置安全 VPN 隧道或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)，以安全地建立与作为此 PaaS Web 应用程序参考体系结构的一部分部署的资源的连接。 通过适当设置 VPN 或 ExpressRoute，客户可以为传输中的数据添加一层保护。

在 Azure 中实施安全 VPN 隧道，可在本地网络与 Azure 虚拟网络之间创建虚拟专用连接。 此连接通过 Internet 进行，并允许客户安全地在客户网络和 Azure 之间的加密链路内"隧道"信息。 站点到站点 VPN 是安全且成熟的技术，各种规模的企业已部署该技术数十年。 此选项使用 IPsec 隧道模式作为加密机制。

由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，Microsoft 提供了另一个更安全的连接选项。 Azure ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间专用的 WAN 链接。 ExpressRoute 连接并不绕过 Internet，并且与通过 Internet 的典型连接相比，这些连接可靠性更高、速度更快、延迟时间更短且安全性更高。 此外，由于使用的是客户电信提供商的直接连接，数据不会通过 Internet 遍历，因此不会在 Internet 上公开。

我们编写了有关如何实施安全混合网络，以便将本地网络扩展到 Azure 的[最佳做法](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="cost-considerations"></a>成本注意事项
如果还没有 Azure 帐户，则可以创建一个免费帐户。 转到[免费帐户页面](https://azure.microsoft.com/free/)即可开始，了解可以使用免费 Azure 帐户做什么，并了解哪些产品在 12 个月内是免费的。

要使用安全功能部署示例应用中的资源，您需要为某些高级功能付费。 随着应用的扩展以及 Azure 提供的免费层和试用版需要升级以满足应用程序要求，您的成本可能会增加。 使用 Azure[定价计算器](https://azure.microsoft.com/pricing/calculator/)估计成本。

## <a name="next-steps"></a>后续步骤
以下文章可帮助您设计、开发和部署安全应用程序。

- [设计](secure-design.md)
- [发展](secure-develop.md)
- [部署](secure-deploy.md)
