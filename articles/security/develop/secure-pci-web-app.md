---
title: 保护 PCI DSS 的 web 应用 |Microsoft Docs
description: 当你在 Azure 上进行开发时, 此示例应用可实现安全最佳实践, 从而提高应用程序和组织的安全状况。
keywords: '\n\n'
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
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992609"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>为 PCI 应用程序开发安全基础结构

## <a name="overview"></a>概述

此适用于支付卡行业 (PCI) 应用的安全基础结构提供了部署支付卡行业平台即服务 (PaaS) 环境的指导, 该环境适合于收集、存储和检索持卡人数据。 此解决方案自动完成适用于常用参考体系结构的 Azure 资源的部署和配置，演示了客户如何通过多种方式达到特定的安全和符合性要求，是客户在 Azure 上生成和配置其解决方案的基础。 解决方案实现类似于支付卡行业数据安全标准 (PCI DSS 3.2) 的一小部分需求。

此示例使用预先配置的安全控制自动部署 PaaS web 应用程序参考体系结构, 以帮助客户达到与 PCI DSS 3.2 要求类似的符合性。 此解决方案包含 Azure 资源管理器模板和 PowerShell 脚本，指导用户进行资源部署和配置。

应按照本文中所述的步骤进行操作, 以确保正确配置应用程序组件。 数据库、Azure App Service、Azure Key Vault 实例和 Azure 应用程序网关实例彼此依赖。

部署脚本设置基础结构。 运行部署脚本后, 需要在 Azure 门户中进行一些手动配置, 以将组件和服务链接在一起。

此示例面向 Azure 上的经验丰富的开发人员, 这些开发人员在零售行业中工作, 并希望使用安全的 Azure 基础结构构建一个零售应用。

> [!NOTE]
> 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。

未经修改就向此环境部署应用程序并不足以完全满足 PCI DSS 3.2 所设定的要求。 请注意以下事项：

- 本体系结构提供一个基线，帮助客户按符合 PCI DSS 3.2 要求的方式使用 Azure。
- 客户负责针对使用本体系结构构建的任何解决方案开展相应的安全与合规评估；具体要求根据客户的每种实施方式具体情况而异。

在开发和部署此应用中, 你将了解如何执行以下操作:

- 创建 Azure Key Vault 实例, 并存储和检索其中的机密。
- 部署 azure SQL azure 数据库, 设置安全数据, 并授予对其的访问权限。
- 使用应用服务环境部署 Azure web 应用, 该环境是独立于前端防火墙 aEcess 的专用。
- 使用[OWASP Top 10 规则集](https://coreruleset.org/)的防火墙创建和配置 Azure 应用程序网关实例。
- 使用 Azure 服务对传输中的数据和静态数据启用加密。
- 设置 Azure 策略和蓝色打印以评估符合性要求

开发和部署此应用后, 你将设置以下示例 web 应用以及所述的配置和安全措施。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件
应用是具有三个级别的典型 n 层应用程序。 集成的前端、后端和数据库层以及集成的管理组件如下所示:

![应用体系结构](./media/secure-pci-web-app/architecture.png)

该体系结构包括以下组件:

- [应用服务环境 v2](https://docs.microsoft.com/azure/app-service/environment/intro/)。 提供应用程序体系结构的应用服务环境和负载均衡器。
- [Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/)。 为应用程序体系结构提供网关和防火墙。
- [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)。 在多个平台上提供可扩展的应用程序性能管理 (APM) 服务。
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)。 存储并加密应用的机密, 并管理其周围的访问策略创建。
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/)。提供基于云的标识和访问管理服务、登录和访问资源。
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)。 提供服务以托管域。
- [Azure 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/)。 缩放应用程序并为服务创建高可用性
- [Azure 存储](https://docs.microsoft.com/azure/storage/common/storage-introduction/)。 提供针对新式数据存储方案的解决方案。
- [Azure Web 应用](https://docs.microsoft.com/azure/app-service/overview/)。  提供基于 HTTP 的服务, 用于承载 web 应用程序。
- [用于 AzureSQL 的 Azure 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/)。 安全地存储应用的数据。
- [Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/overview/)。 提供规范并符合某些标准和要求。
- [Azure 安全中心](https://docs.microsoft.com/azure/security-center/)
- [Azure 策略](https://docs.microsoft.com/azure/governance/policy/overview)。 为不符合已分配策略的资源评估资源。

## <a name="threat-model"></a>威胁模型
威胁建模是识别业务和应用程序的潜在安全威胁的过程, 然后确保适当的缓解计划已准备就绪。

此示例使用[Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)实现安全示例应用的威胁建模。 通过绘制组件和数据流的关系图, 你可以在开发过程的早期确定问题和威胁。 这会在以后节省时间和资金。

这是示例应用的威胁模型:

![威胁模型](./media/secure-pci-web-app/threat-model.png)

下面的屏幕截图显示了威胁建模工具生成的一些示例威胁和潜在漏洞。 威胁模型概述了公开的攻击面, 并提示开发人员考虑如何缓解这些问题。

![威胁模型输出](./media/secure-web-app/threat-model-output.png)

例如, 通过净化用户输入以及在 Azure Database for PostgreSQL 中使用存储函数, 可减轻前面的威胁模型输出中的 SQL 注入。 此缓解措施可防止在数据读取和写入过程中执行查询。

开发人员通过缓解威胁模型输出中的每个威胁, 提高系统的整体安全性。

## <a name="deployment"></a>部署
### <a name="prerequisites"></a>先决条件
若要启动并运行应用程序, 需要安装以下工具:

- 用于修改和查看应用程序代码的代码编辑器。 [Visual Studio Code](https://code.visualstudio.com/)为开源选项。
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest)开发计算机上。
- 系统中的[Git](https://git-scm.com/) 。 Git 用于本地克隆源代码。
- [jq](https://stedolan.github.io/jq/), 一种以用户友好的方式查询 JSON 的 UNIX 工具。

此示例由 Azure 资源管理器的 API 服务处理的 JSON 配置文件和 PowerShell 脚本组成, 可用于在 Azure 中部署资源。 [此处](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM)提供了详细的部署说明。

#### <a name="quickstart"></a>快速入门

1.  将[此](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) GitHub 存储库克隆或下载到本地工作站。
2.  查看 0-Setup-AdministrativeAccountAndPermission.md 并运行所提供的命令。
3.  使用 Contoso 示例数据部署测试解决方案，或试用初始生产环境。

    此脚本使用 Contoso 示例数据通过部署 Azure 资源来演示 web 商店。

在此示例中, 运行部署脚本, 将 web 应用部署到应用服务, 并创建资源。

有多种方法可在 Azure 上部署应用, 包括:

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
网络安全组 (https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 包含允许或拒绝虚拟网络中的流量的访问控制列表 (acl)。 网络安全组可用于在子网或单个 VM 级别保护流量。 存在以下网络安全组：

- 应用程序网关有 1 个网络安全组
- 应用服务环境有 1 个网络安全组
- Azure SQL 数据库有 1 个网络安全组
- 守护主机有 1 个网络安全组

每个网络安全组都打开了特定的端口和协议, 以便解决方案能够安全正确地工作。 此外，为每个网络安全组启用了以下配置：

- 诊断日志和事件 (https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) 在存储帐户中启用并存储
- Azure Monitor 日志连接到网络安全组的诊断 (https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG 配置
应按下图所示配置应用服务环境的 NSG config。

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

每个子网与其对应的网络安全组相关联。

### <a name="config"></a>配置
子网按下图所示配置。
 ![Config.xml](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
域名系统 (DNS) 负责将网站或服务名称转换 (或解析) 为它的 IP 地址。 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) 是 DNS 域的托管服务，它使用 Azure 基础结构提供名称解析。 通过在 Azure 中托管域，用户可以使用与其他 Azure 服务相同的凭据、API、工具和账单来管理 DNS 记录。 Azure DNS 还支持 DNS 专用域。

### <a name="protect-data"></a>保护数据
为了帮助保护云中的数据，需要考虑数据可能出现的状态以及可用于该状态的控件。 Azure 数据安全与加密的最佳做法与以下数据状态相关：

- 静态：包括物理媒体（磁盘或光盘）上以静态方式存在的所有信息存储对象、容器和类型。
- 传输中：在各组件、位置或程序间传输数据时，数据处于“传输中”状态。 例如通过网络、通过服务总线（从本地到云，反之亦然，包括诸如 ExpressRoute 的混合连接）进行传输，或在输入/输出过程。

### <a name="azure-storage"></a>Azure 存储
为了满足 rest 要求的加密数据, 所有[Azure 存储](https://azure.microsoft.com/services/storage/)使用 Azure Key Vault 来控制访问和加密数据的密钥。 这有助于保护持卡人数据，以支持 PCI DSS 3.2 定义的组织安全承诺和符合性要求。

### <a name="azure-disk-encryption"></a>Azure 磁盘加密
Azure 磁盘加密利用 Windows 的 BitLocker 功能为数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

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

- Azure Active Directory 是 Microsoft 的基于云的多租户目录和标识管理服务。 解决方案的所有用户（包括访问 Azure SQL 数据库的用户）都在 Azure Active Directory 中创建。
- 使用 Azure Active Directory 对应用程序执行身份验证。 有关详细信息，请参阅[将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration)。 此外，数据库列加密使用 Azure Active Directory 对访问 Azure SQL 数据库的应用程序进行身份验证。 有关详细信息, 请参阅[如何保护 AZURE SQL 数据库中的敏感数据](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- Azure 基于角色的访问控制允许管理员定义细化访问权限, 以仅授予用户执行其作业所需的访问权限量。 无需向每个用户授予 Azure 资源的不受限权限，管理员可以只允许使用特定的操作来访问持卡人数据。 订阅访问仅限于订阅管理员。
- Azure Active Directory Privileged Identity Management 使客户能够最大程度地减少有权访问某些信息 (例如持卡人数据) 的用户的数量。 管理员可以使用 Azure Active Directory Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 还可以根据需要，使用此功能来实施按需、实时的管理访问。
- Azure Active Directory Identity Protection 检测影响组织标识的潜在漏洞, 配置对检测到的与组织标识相关的可疑操作的自动响应, 并调查可疑事件以采取适当的措施来解决问题。

### <a name="secrets-management"></a>机密管理
解决方案使用 Azure Key Vault 来管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Azure Key Vault 功能可帮助客户保护和访问此类数据：

- 根据需要配置高级访问权限策略。
- 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
- Key Vault 中的所有密钥和机密都有过期日期。
- Key Vault 中的所有密钥受专用硬件安全模块的保护。 密钥类型为受 HSM 保护的 2048 位 RSA 密钥。
- 通过 Key Vault, 可以加密密钥和机密 (例如身份验证密钥、存储帐户密钥、数据加密密钥)。PFX 文件和密码), 方法是使用受硬件安全模块 (Hsm) 保护的密钥
- 使用 RBAC 将权限分配给特定范围内的用户、组和应用程序。
- 使用 Key Vault 通过自动续订来管理 TLS 证书。
- Key Vault 的诊断日志已启用，其保留期至少为 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作。

### <a name="azure-security-center"></a>Azure 安全中心
使用 Azure 安全中心, 客户可以跨工作负荷集中应用和管理安全策略、限制对威胁的暴露以及检测和响应攻击。 此外，Azure 安全中心会访问 Azure 服务的现有配置，以提供配置与服务建议来帮助改善安全状况和保护数据。

Azure 安全中心使用各种检测功能，提醒客户针对其环境的潜在攻击。 这些警报包含有关触发警报的内容、目标资源以及攻击源的重要信息。 Azure 安全中心包含一组预定义的安全警报, 在发生威胁或可疑活动时触发。 Azure 安全中心的自定义警报规则允许客户根据已从环境中收集的数据定义新的安全警报。

Azure 安全中心提供区分优先级的安全警报和事件，让客户更轻松地发现和解决潜在安全问题。 为每个检测到的威胁生成威胁情报报表, 以帮助事件响应团队调查和修正威胁。

### <a name="azure-application-gateway"></a>Azure 应用程序网关
体系结构使用配置了 Web 应用程序防火墙并启用了 OWASP 规则集的应用程序网关，来降低安全漏洞风险。 其他功能包括：

- 端对端 SSL
- 禁用 TLS 1.0 版和1.1 版
- 启用 Tlsv1.1 1。2
- Web 应用程序防火墙 (防护模式)
- OWASP 3.0 规则集的防护模式
- 启用诊断日志记录
- 自定义运行状况探测
- Azure 安全中心和 Azure 顾问提供额外的保护和通知。 Azure 安全中心还提供信誉系统。

### <a name="logging-and-auditing"></a>日志记录和审核
Azure 服务广泛记录系统和用户活动以及系统运行状况：

- [活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供对订阅中资源执行的操作的深入信息。 活动日志可帮助确定操作的发起方、发生的时间和状态。
- [诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、Azure 存储日志、Key Vault 审核日志、应用程序网关访问和防火墙日志。 所有诊断日志都将写入到集中式加密 Azure 存储帐户以进行存档。 保留期是允许用户配置的，最长为 730 天，具体取决于组织的保留期要求。

### <a name="azure-monitor-logs"></a>Azure Monitor 日志
这些日志合并到[Azure Monitor 日志](https://azure.microsoft.com/services/log-analytics/)中, 以便进行处理、存储和仪表板报告。 收集后，数据在 Log Analytics 工作区内按数据类型整理到不同的表中，这样即可不考虑最初来源而集中分析所有数据。 此外, Azure 安全中心与 Azure Monitor 日志集成, 使客户可以使用 Kusto 查询访问其安全事件数据, 并将其与其他服务中的数据合并。

以下 Azure[监视解决方案](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)包括在此体系结构中:

- [Active Directory 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：Active Directory 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并且提供特定于部署的服务器基础结构的优先建议列表。
- [SQL 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)：SQL 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并为客户提供特定于部署的服务器基础结构的优先建议列表。
- [代理运行状况](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：代理运行状况解决方案报告部署了多少代理及其地理分布, 以及有多少代理无响应, 以及正在提交操作数据的代理数。
- [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：Activity Log Analytics 解决方案可帮助分析客户的所有 Azure 订阅的 Azure 活动日志。

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) 通过使组织能够审核、创建警报和存档数据（包括在用户的 Azure 资源中跟踪 API 调用），帮助用户跟踪性能、维护安全性和确定趋势。

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理服务。 Application Insights 可检测性能异常，客户可以使用它来监视实时 Web 应用程序。 它包含强大的分析工具来帮助客户诊断问题，了解用户在应用中实际执行了哪些操作。 它旨在帮助客户持续提高性能和可用性。

### <a name="azure-key-vault"></a>Azure Key Vault
为组织创建保管库, 以便在其中存储密钥, 并为如下操作任务维护责任:

- Key Vault 中存储的数据包括：

   - Application Insights 密钥
   - 数据存储访问密钥
   - 连接字符串
   - 数据表名称
   - 用户凭据

- 根据需要配置高级访问权限策略
- 在定义 Key Vault 访问策略时，尽量将访问密钥和机密的必需权限降至最低
- Key Vault 中的所有密钥和机密都有过期日期
- Key Vault 中的所有密钥都受到 HSM 的保护 [密钥类型 = 受 HSM 保护的2048位 RSA 密钥]
- 使用基于角色的访问控制 (RBAC) 向所有用户/标识授予最低要求的权限
- 应用程序共享 Key Vault 的前提是彼此信任且需要在运行时访问相同的机密
- Key Vault 的诊断日志已启用，其保留期至少为 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作

### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
需要配置安全 VPN 隧道或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)，以安全地建立与作为此 PaaS Web 应用程序参考体系结构的一部分部署的资源的连接。 通过适当设置 VPN 或 ExpressRoute，客户可以为传输中的数据添加一层保护。

在 Azure 中实施安全 VPN 隧道，可在本地网络与 Azure 虚拟网络之间创建虚拟专用连接。 此连接通过 Internet 进行, 并允许客户在客户的网络与 Azure 之间的加密链接内安全 "隧道" 信息。 站点到站点 VPN 是安全且成熟的技术，各种规模的企业已部署该技术数十年。 IPsec 隧道模式作为加密机制在此选项中使用。

由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，Microsoft 提供了另一个更安全的连接选项。 Azure ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间专用的 WAN 链接。 ExpressRoute 连接并不绕过 Internet，并且与通过 Internet 的典型连接相比，这些连接可靠性更高、速度更快、延迟时间更短且安全性更高。 此外，由于使用的是客户电信提供商的直接连接，数据不会通过 Internet 遍历，因此不会在 Internet 上公开。

我们编写了有关如何实施安全混合网络，以便将本地网络扩展到 Azure 的[最佳做法](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="cost-considerations"></a>成本注意事项
如果还没有 Azure 帐户, 可以创建一个免费帐户。 转到[免费帐户页](https://azure.microsoft.com/free/)开始使用免费的 Azure 帐户, 并了解12个月免费购买哪些产品。

若要在包含安全功能的示例应用程序中部署资源, 需要为一些高级功能付费。 随着应用规模和 Azure 提供的免费层和试用版的升级, 你的成本可能会增加。 使用 Azure[定价计算器](https://azure.microsoft.com/pricing/calculator/)估算成本。

## <a name="next-steps"></a>后续步骤
以下文章可帮助你设计、开发和部署安全的应用程序。

- [设计](secure-design.md)
- [开发](secure-develop.md)
- [部署](secure-deploy.md)
