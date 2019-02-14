---
title: 有关保护和管理已迁移到 Azure 的工作负荷的最佳做法 | Microsoft Docs
description: 迁移到 Azure 后，获取有关操作、管理和保护已迁移工作负荷的最佳做法。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: eaef1c904b5404339c476d86c5b8c2a1740e5a3e
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700071"
---
# <a name="best-practices-for-securing-and-managing-workloads-migrated-to-azure"></a>有关保护和管理已迁移到 Azure 的工作负荷的最佳做法

在规划和设计迁移时，除了考虑迁移本身的问题以外，还需要考虑到迁移后 Azure 中的安全和管理模型。 本文介绍有关迁移后保护 Azure 部署的规划和最佳做法，以及让部署保持最佳运行状态所要持续执行的任务。 

> [!IMPORTANT]
> 本文介绍的最佳做法和意见以撰写本文时可用的 Azure 平台和服务功能为基础。 各种特性和功能会随时间变化。

## <a name="secure-migrated-workloads"></a>保护已迁移的工作负荷

迁移后，最关键的任务是防范迁移的工作负荷遭受内部和外部威胁。 以下最佳做法可帮助你实现该目的：

- [使用 Azure 安全中心](#best-practice-follow-azure-security-center-recommendations)：了解如何使用 Azure 安全中心提供的监视、评估和建议
- [加密数据](#best-practice-encrypt-data)：获取有关加密 Azure 中的数据的最佳做法。
- [设置反恶意软件](#best-practice-protect-vms-with-antimalware)：防范 VM 受到恶意软件的攻击。
- [保护 Web 应用](#best-practice-secure-web-apps)：使已迁移的 Web 应用中的敏感信息保持安全。
- [评审订阅](#best-practice-review-subscriptions-and-resource-permissions)：确认谁可以在迁移后访问你的 Azure 订阅和资源。
- [使用日志](#best-practice-review-audit-and-security-logs)：定期评审 Azure 审核和安全日志。
- [评审其他安全功能](#best-practice-evaluate-other-security-features)：了解和评估 Azure 提供的高级安全功能。

## <a name="best-practice-follow-azure-security-center-recommendations"></a>最佳做法：遵循 Azure 安全中心建议

Microsoft 努力确保为 Azure 租户管理员提供所需的信息，以启用可防范工作负荷遭受攻击的安全功能。  Azure 安全中心提供统一的安全管理。 在安全中心，可对各种工作负荷应用安全策略、限制威胁攻击面，以及检测和响应攻击。 安全中心分析各个 Azure 租户的资源和配置，并提供安全建议，包括：

- **集中式策略管理** - 通过集中管理所有混合云工作负荷中的安全策略，确保符合公司或法规安全要求。
- **持续安全评估** - 监视计算机、网络、存储和数据服务以及应用程序的安全状态，发现潜在的安全问题。
- **可行的建议** - 借助高优先级且可行的安全建议，在攻击者利用安全漏洞前修复这些漏洞。
- **划分警报和事件的优先级** - 通过对安全警报和事件划分优先级，首先关注最紧急的威胁。

除了评估和建议以外，安全中心还提供其他多种可为特定资源启用的安全功能。

- **实时 (JIT) 访问**：实时减小网络受攻击面，控制对 Azure VM 上的管理端口的访问。
    - 在 Internet 上打开 VM RDP 端口 3389 会让恶意行动者的持续攻击活动瞄准 VM。 Azure IP 地址是公开的，黑客会不断探测这些地址，并针对端口 3389 发起攻击。 
    - 实时保护使用网络安全组 (NSG) 和传入规则来限制特定端口的开放时间长短。
    - 启用实时保护后，安全中心将检查用户是否对 VM 拥有基于角色的访问控制 (RBAC) 写入访问权限。 此外，指定规则来限制用户连接到 VM 的方式。 如果权限适当，则批准访问请求，同时，安全中心会配置 NSG，以允许在指定的时间内将入站流量发往选定的端口。 该时间过后，NSG 将恢复前一状态。
- **自适应应用程序控制**：使用动态应用允许列表控制 VM 上可运行的应用，避免 VM 上出现不需要的软件和恶意软件。
    - 使用自适应应用程序控制可将应用加入允许列表，防止恶意用户或管理员在 VM 上安装未经批准或审核的软件应用。
    - 可以阻止运行恶意应用或针对这种企图发出警报，避免不需要的应用或恶意应用，确保符合组织的应用安全政策。
- **文件完整性监视**：确保 VM 上运行的文件的完整性。
    - 无需安装可能导致 VM 出现问题的软件。  更改系统文件也可能导致 VM 故障或性能降低。  文件完整性监视将检查系统文件和注册表设置的更改，有设置更新时会发出通知。
    - 安全中心会建议要监视的文件。


**了解更多：**

- [详细了解](https://docs.microsoft.com/azure/security-center/security-center-intro) Azure 安全中心。
- [详细了解](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)实时 VM 访问。
- [详细了解](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)如何应用自适应应用程序控制。
- [开始使用](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)文件完整性监视。


## <a name="best-practice-encrypt-data"></a>最佳做法：加密数据 

加密是 Azure 安全做法的重要部分。 确保在所有级别启用加密可帮助防止未经授权的一方获取敏感数据（包括传输中的数据和静态数据）的访问权限。 

### <a name="encryption-for-iaas"></a>IaaS 加密

- **VM**：对于 VM，可以使用 Azure 磁盘加密来加密 Windows 和 Linux IaaS VM 磁盘。
    - 磁盘加密利用 Windows 版 BitLocker 和 Linux 版 DM-Crypt 对 OS 和数据磁盘进行卷加密。
    - 可以使用 Azure 创建的加密密钥，或者提供自己的、在 Azure Key Vault 中受保护的加密密钥。 
    - 使用磁盘加密可以保护静态（磁盘中）的 IaaS VM 数据，以及在启动 VM 期间保护这些数据。 
    - 如果 VM 未加密，Azure 安全中心会发出警报。
- **存储**：保护 Azure 存储中存储的静态数据。
    - 可以使用 Microsoft 生成的、符合 FIPS 140-2 规范的 AES 密钥来加密 Azure 存储帐户中存储的数据，或者可以使用自己的密钥。
    - 针对所有新的和现有的存储帐户启用存储服务加密，并且不能禁用。


### <a name="encryption-for-paas"></a>PaaS 加密

在 IaaS 中，你需要管理自己的 VM 和基础结构，而在 PaaS 中，模型平台和基础结构由提供商管理，这样，你便可以专注于核心应用逻辑和功能。 由于 PaaS 服务的类型多种多样，出于安全目的，需要单独评估每个服务。 下面举例说明如何为 Azure SQL 数据库启用加密。

- **Always Encrypted**：使用 SQL Server Management Studio 中的 Always Encrypted 向导保护静态数据。
    - 创建 Always Encrypted 密钥来加密各项列数据。
    - 可将 Always Encrypted 密钥以加密形式存储在数据库元数据中，或者存储在 Azure Key Vault 等受信任的密钥存储中。
    - 使用此功能可能需要对应用做出更改。
- **透明数据加密 (TDE)**：使用实时加密和解密保护 Azure SQL 数据库、相关备份和静态事务日志文件。
    - 无需在应用层进行更改，即可通过 TDE 执行加密活动。
    - TDE 可以使用 Microsoft 提供的加密密钥，或者，你可以使用“创建自己的密钥”支持提供自己的密钥。


**了解更多：**
- [了解](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)适用于 IaaS VM 的 Azure 磁盘加密。
- 为 IaaS Windows VM [启用](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-windows)加密。
- [了解](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)静态数据的 Azure 存储服务加密。
- [阅读](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Always Encrypted 概述。
- [了解](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-2017)适用于 Azure SQL 数据库的 TDE。
- [了解](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql)如何配合“创建自己的密钥”使用 TDE。

## <a name="best-practice-protect-vms-with-antimalware"></a>最佳做法：使用反恶意软件保护 VM

具体而言，迁移的旧式 Azure VM 上可能未安装适当级别的反恶意软件。  Azure 提供一个免费的终结点解决方案，可帮助防范 VM 遭受病毒、间谍软件和其他恶意软件的攻击。
- 当已知恶意软件或不需要的软件试图自我安装时，适用于 Azure 的 Microsoft 反恶意软件会生成警报。
- 它是在后台运行的单代理解决方案，无需人工干预。
- 在 Azure 安全中心，可以轻松识别未运行终结点保护的 VM，并可根据需要安装 Microsoft 反恶意软件。

![适用于 VM 的反恶意软件](./media/migrate-best-practices-security-management/antimalware.png)
*适用于 VM 的反恶意软件*

**了解更多：**

- [了解](https://docs.microsoft.com/azure/security/azure-security-antimalware) Microsoft 反恶意软件

## <a name="best-practice-secure-web-apps"></a>最佳做法：保护 Web 应用

迁移的 Web 应用面临着以下几个问题：

- 大部分传统 Web 应用程序往往在配置文件中包含敏感信息。  备份应用或者将应用代码签入或签出源代码管理时，包含此类信息的文件可能造成安全问题。
- 此外，迁移 VM 中的 Web 应用时，有可能会将该计算机从本地网络和受防火墙保护的环境转移到面向 Internet 的环境。 需要确保设置一种解决方案用于执行与本地保护资源相同的工作。


Azure 提供几种解决方案：

- **Azure Key Vault**：当今的 Web 应用开发人员都在采取措施来确保这些文件中的敏感信息不会泄密。 一种保护方法是将这些信息提取出来，并将其放入 Azure Key Vault。
    - 可以使用 Key Vault 来集中存储应用机密并控制其分发。 这样就无需在应用文件中存储安全信息。
    - 应用可以使用 URI 访问保管库中的安全信息，无需用户编写自定义代码。
    - Azure Key Vault 允许通过 Azure 安全控制锁定访问权限，以及无缝实施“密钥滚动更新”。 Microsoft 看不到你的数据，也不会提取这些数据。
- **应用服务环境**：如果迁移的应用需要额外的保护，可以考虑添加应用服务环境和 Web 应用程序防火墙来保护应用资源。
    - Azure 应用服务环境提供完全隔离的专用环境，在其中，可以运行 Windows 和 Linux Web 应用、Docker 容器、移动应用和 Functions 等应用服务应用。
    - 对于规模极大、需要隔离和安全网络访问或者内存利用率较高的应用而言，此环境非常有用。
- **Web 应用程序防火墙**：应用程序网关的一项功能，可针对 Web 应用提供集中式保护。
    - 它无需对后端代码进行修改即可保护 Web 应用。
    - 它可以同时保护在应用程序网关后面的多个 Web 应用。
    - 可以使用 Azure Monitor 监视 Web 应用程序防火墙，并可将其集成到 Azure 安全中心。



![保护 Web 应用](./media/migrate-best-practices-security-management/web-apps.png)
*Azure Key Vault*

**了解更多：**

- [获取](https://docs.microsoft.com/azure/key-vault/key-vault-overview) Azure Key Vault 概述。
- [了解](https://docs.microsoft.com/azure/application-gateway/waf-overview) Web 应用程序防火墙。
- [获取](https://docs.microsoft.com/azure/app-service/environment/intro)应用服务环境简介。
- [了解](https://docs.microsoft.com/azure/key-vault/tutorial-web-application-keyvault)如何将 Web 应用配置为从 Key Vault 读取机密。
- [了解](https://docs.microsoft.com/azure/application-gateway/waf-overview) Web 应用程序防火墙

## <a name="best-practice-review-subscriptions-and-resource-permissions"></a>最佳做法：评审订阅和资源权限

迁移工作负荷后，在 Azure 中运行这些工作负荷的过程中，拥有工作负荷访问权限的人员会不断变动。 安全团队应定期评审对 Azure 租户和资源组的访问权限。 Azure 提供多种标识管理和访问控制安全性产品/服务（包括基于角色的访问控制 (RBAC)），以授权访问 Azure 资源。

- RBAC 为安全主体分配访问权限。 安全主体代表用户、组（一组用户）、服务主体（应用和服务使用的标识）和托管标识（Azure 自动管理的 Azure Active Directory 标识）。
- RBAC 可将角色分配到安全主体，例如所有者、参与者和读取者，以及用于定义相关角色可执行的操作的角色定义（权限集合）。
- RBAC 还可以设置范围，用于规定角色的权限边界。 可在多个级别（包括管理组、订阅、资源组或资源）设置范围
- 确保拥有 Azure 访问权限的管理员只能访问你要允许的资源。  如果 Azure 中预定义角色的粒度不足，你可以创建自定义角色来区分和限制访问权限。

![访问控制](./media/migrate-best-practices-security-management/subscription.png)
*访问控制 - IAM*

**了解更多：**

- [关于](https://docs.microsoft.com/azure/role-based-access-control/overview) RBAC。
- [了解](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)如何使用 RBAC 和 Azure 门户管理访问权限。
- [了解](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)自定义角色。

## <a name="best-practice-review-audit-and-security-logs"></a>最佳做法：评审审核和安全日志

Azure Active Directory (AD) 提供 Azure Monitor 中显示的活动日志。 日志捕获 Azure 租户中执行的操作、操作的执行时间和执行者。 

- 审核日志显示租户中任务的历史记录。 登录活动日志显示任务的执行者。 
- 对安全报告的访问权限取决于 Azure AD 许可证。 Free 和 Basic 版本提供有风险用户和登录活动的列表。Premium 1 和 Premium 2 版本提供基础事件信息。
- 可将活动日志路由到多个终结点进行长期保留及获取数据见解。
- 使日志评审成为一项惯例，或者集成安全信息和事件管理 (SIEM) 工具来自动检查异常情况。  如果不使用 Premium 1 或 2，则需要自行执行大量的分析，或使用自己的 SIEM 系统。  分析包括探查有风险的登录和事件，以及其他用户攻击模式。


![用户和组](./media/migrate-best-practices-security-management/azure-ad.png)
*Azure AD 用户和组*

**了解更多：**

- [了解](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) Azure Monitor 中的 Azure AD 活动日志。
- [了解](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs)如何在 Azure AD 门户中审核活动报告。

## <a name="best-practice-evaluate-other-security-features"></a>最佳做法：评估其他安全功能

Azure 中的其他许多安全功能可以提供高级安全选项。 其中的某些最佳做法需要附加产品许可证和高级选项。

- **实施 Azure AD 管理单元 (AU)**：将管理职责委托给支持人员可能比较棘手，因为这只能提供基本的 Azure 访问控制。  授权支持人员管理 Azure AD 中的所有组可能不是实现组织安全性的理想方法。  使用 AU 可将 Azure 资源划分成容器，类似于使用本地组织单位 (OU)。  若要使用 AU，AU 管理员必须具有 Premium Azure AD 许可证。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)。
- **使用多重身份验证 (MFA)**：如果有 Premium Azure AD 许可证，则可以在管理员帐户中启用并强制实施 MFA。 网络钓鱼是窃取帐户凭据的最常见手段。  一旦恶意行动者取得管理员帐户凭据，就无从阻止他们开展影响重大的行动，例如删除你的所有资源组。 可通过多种方式设置 MFA，包括电子邮件、Authenticator 应用和手机短信。 管理员可以选择干扰性最低的选项。 MFA 与威胁分析和条件访问策略集成，可以随机要求做出 MFA 质询响应。 详细了解[安全指导](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices)和[如何设置](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices) MFA。
- **实施条件访问**：在大部分中小型组织中，Azure 管理员和支持团队可能位于一个地理位置。 在这种情况下，大多数登录活动都来自同一区域。 如果这些位置的 IP 地址基本上保持静态，则最好是避免从这些区域以外进行管理员登录。 即使远程恶意行动者窃取了管理员凭据，你也可以实施安全功能（例如，条件访问与 MFA 相结合），以防止从远程位置、欺骗位置或随机 IP 地址登录。 [详细了解](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)条件访问，并查看有关 Azure AD 中的条件访问的[最佳做法](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)。
- **评审企业应用程序权限**：不时地，管理员可能会单击 Microsoft 和第三方链接，但不知道这样做对其组织造成的影响。 单击某些链接可能会显示一个要求分配 Azure 应用权限的许可屏幕，并可能允许被访问站点获取 Azure AD 数据的读取访问权限，甚至是管理整个 Azure 订阅的完全访问权限。 应该定期评审管理员和用户允许的、可访问 Azure 资源的应用。 应确保这些应用只拥有必要的权限。 此外，可每隔一个季度或半年向用户发送电子邮件并在其中包含应用页面的链接，告知他们已允许哪些应用访问其组织数据。 [详细了解](https://docs.microsoft.com/azure/active-directory/manage-apps/application-types)应用程序类型，以及[如何控制](https://docs.microsoft.com/azure/active-directory/manage-apps/remove-user-or-group-access-portal) Azure AD 中的应用分配。



## <a name="managed-migrated-workloads"></a>管理已迁移的工作负荷

本部分建议有关 Azure 管理的一些最佳做法，包括：

- [管理资源](#best-practice-name-resource-groups)：有关 Azure 资源组和资源的最佳做法，包括智能命名、防止意外删除、管理资源权限和有效的资源标记。
- [使用蓝图](#best-practice-implement-blueprints)：获取有关使用蓝图构建和管理部署环境的快速概述。
- [查看体系结构](#best-practice-review-azure-reference-architectures)：查看示例 Azure 体系结构，以便在构建迁移后的部署期间从中获得经验。
- [设置管理组](#best-practice-manage-resources-with-management-groups)：如果有多个订阅，可将其收集到管理组中，并将调控设置应用到这些组。
- [设置访问策略](#best-practice-deploy-azure-policy)：对 Azure 资源应用合规性策略。
- [实施 BCDR 策略](#best-practice-implement-a-bcdr-strategy)：制定业务连续性和灾难恢复 (BCDR) 策略，以便在发生服务中断时保持数据的安全性、环境的复原能力和资源的正常运行。
- [管理 VM](#best-practice-use-managed-disks-and-availability-sets)：将 VM 分组到可用性组，以实现复原能力和高可用性。 使用托管磁盘来简化 VM 磁盘和存储的管理。
- [监视资源使用情况](#best-practice-monitor-resource-usage-and-performance)：为 Azure 资源启用诊断日志记录，生成警报和 Playbook 进行主动故障排除，并使用 Azure 仪表板统一查看部署运行状况和状态。
- [管理支持和更新](#best-practice-manage-updates)：了解 Azure 支持计划及其实施方式，获取有关保持 VM 最新状态的最佳做法，并为变更管理制定流程。


## <a name="best-practice-name-resource-groups"></a>最佳做法：为资源组命名

确保为资源组提供有意义的名称，以方便管理员和支持团队成员可以轻松识别和导航，这样可以大幅提高生产力和效率。
- 我们建议遵循 Azure 命名约定。
- 若要使用 AD Connect 将本地 AD DS 同步到 Azure AD，请考虑使本地安全组的名称与 Azure 中资源组的名称相匹配。

![命名](./media/migrate-best-practices-security-management/naming.png)
*资源组命名*


**了解更多：**

- [了解](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)命名约定

## <a name="best-practice-implement-delete-locks-for-resource-groups"></a>最佳做法：针对资源组实施删除锁

你不希望意外删除的资源组彻底消失。 我们建议实施删除锁来防止发生这种情况。


![删除锁](./media/migrate-best-practices-security-management/locks.png)
*删除锁*

**了解更多：**

- [了解](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)如何锁定资源以防止意外更改。


## <a name="best-practice-understand-resource-access-permissions"></a>最佳做法：了解资源访问权限 

订阅所有者有权访问你的订阅中的所有资源组和资源。
- 进行这项重要分配时，请谨慎添加人员。 了解此类权限产生的后果，对于保持环境的安全稳定非常重要。
- 确保将资源放到适当的资源组中：
    - 将具有类似生命周期的资源放在一起。 理想情况下，在需要删除整个资源组时，不应需要移动某个资源。
    - 为简化管理，应将支持某项功能或工作负荷的资源放在一起。

**了解更多：**

- [了解](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/)如何组织订阅和资源组。

## <a name="best-practice-tag-resources-effectively"></a>最佳做法：有效地标记资源

仅使用与资源相关的资源组名称往往不能提供足够的元数据来有效实施机制，例如订阅中的内部计费或管理。
- 最佳做法是使用 Azure 标记添加可供查询和报告的有用元数据。 
- 利用标记可以逻辑方式对包含所定义属性的资源进行组织。  可以直接将标记应用到资源组或资源。
- 可以针对资源组或单个资源应用标记。 资源组标记不会由该组中的资源继承。
- 可以使用 Powershell 或 Azure 自动化自动应用标记，或者标记单个组和资源。 - 标记方法或自助方法。  如果你已部署请求和变更管理系统，则可以轻松利用请求中的信息来填充特定于公司的资源标记。


![标记](./media/migrate-best-practices-security-management/tagging.png)
*标记*

**了解更多：**

- [了解](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)标记和标记限制。
- [查看](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#powershell)有关设置标记，以及将资源组中的标记应用到其资源的 PowerShell 和 CLI 示例。
- [阅读](http://www.azurefieldnotes.com/2016/07/18/azure-resource-tagging-best-practices/) Azure 标记最佳做法。


## <a name="best-practice-implement-blueprints"></a>最佳做法：实施蓝图

正如工程师或建筑师使用蓝图勾勒出项目的设计参数一样，通过 Azure 蓝图，云架构师和中心 IT 小组同样可以定义一组可重复的 Azure 资源，这些资源实施并遵守组织的标准、模式和要求。 使用 Azure 蓝图，开发团队可以根据组织合规性要求快速构建新的环境，并提供一组内置组件（例如网络）来加速开发和交付过程。

- 使用蓝图可以协调资源组、Azure 资源管理器模板以及策略和角色分配的部署。
- Azure 蓝图存储在全局分布式 Azure Cosmos DB 中。 蓝图对象将复制到多个 Azure 区域。 无论蓝图将资源部署到哪个区域，复制都能提供对蓝图的低延迟、高可用性和一致访问。

**了解更多：**

- [了解](https://docs.microsoft.com/azure/governance/blueprints/overview)蓝图。
- [查看](https://azure.microsoft.com/blog/customizing-azure-blueprints-to-accelerate-ai-in-healthcare/)用于在医疗保健行业加速 AI 实施的蓝图示例。

## <a name="best-practice-review-azure-reference-architectures"></a>最佳做法：查看 Azure 参考体系结构

在 Azure 中构建安全、可缩放且易于管理的工作负荷可能颇费周折。  随着不断的变化，可能很难跟上最佳环境所需的不同功能。 设计和迁移工作负荷时，使用一个参考体系结构可能有所帮助。  Azure 和 Azure 合作伙伴为各种环境构建了多个示例参考体系结构。 这些示例旨在提供思路，让你从中学到经验或者在其基础之上构建解决方案。 

参考体系结构按方案进行组织。 它们包含建议的做法，以及有关管理、可用性、可伸缩性和安全性的建议。
Azure 应用服务环境提供完全隔离的专用环境，在其中可以运行应用服务应用，包括 Windows 和 Linux Web 应用、Docker 容器、移动应用和 Functions。 应用服务可将 Azure 的强大功能添加到应用程序，并提供安全功能、负载均衡、自动缩放和自动管理。 还可以利用其 DevOps 功能，例如来自 Azure DevOps 和 GitHub 的持续部署，以及包管理、过渡环境、自定义域和 SSL 证书。 应用服务非常适合用于需要隔离和安全网络访问的应用、使用大量内存的应用，以及需要缩放的其他资源。
**了解更多：**

- [了解](https://docs.microsoft.com/azure/architecture/reference-architectures/) Azure 参考体系结构。
- [查看](https://docs.microsoft.com/azure/architecture/example-scenario/) Azure 示例方案。

## <a name="best-practice-manage-resources-with-management-groups"></a>最佳做法：使用管理组管理资源

如果组织有多个订阅，则你需要管理这些订阅的访问、策略与合规性。 Azure 管理组提供订阅上的作用域级别。

- 可将订阅组织到名为“管理组”的容器中，并对其应用调控条件。
- 管理组中的所有订阅自动继承管理组的条件。
- 不管使用什么类型的订阅，管理组都能提供大规模的企业级管理。
- 例如，可以应用一个管理组策略用于限制可以创建 VM 的区域。 然后，将此策略应用到该管理组下的所有管理组、订阅和资源。
- 可以构建管理组和订阅的灵活层次结构，以便将资源组织成用于统一策略和访问管理的层次结构。

下图显示了使用管理组创建用于调控的层次结构的示例。

![管理组](./media/migrate-best-practices-security-management/management-groups.png)
*管理组*

**了解更多：**
- [详细了解](https://docs.microsoft.com/azure/governance/management-groups/index)如何将资源组织成管理组。

## <a name="best-practice-deploy-azure-policy"></a>最佳做法：部署 Azure Policy

Azure Policy 是 Azure 中的一项服务，可用于创建、分配和管理策略。

- 策略将在整个资源中强制实施不同的规则和效果，以便这些资源符合公司标准和服务级别协议。
- Azure Policy 将评估资源，并扫描那些不符合策略的资源。
- 例如，可以创建一个策略，以便仅允许在环境中为 VM 使用特定的 SKU 大小。 创建和更新资源以及扫描现有资源时，Azure Policy 将评估此设置。 
- Azure 提供一些可供你分配的内置策略，你也可以创建自己的策略。


![Azure Policy](./media/migrate-best-practices-security-management/policy.png)
*Azure Policy*

**了解更多：**
- [获取](https://docs.microsoft.com/azure/governance/policy/overview) Azure Policy 的概述。
- [了解](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)如何创建和管理策略以实施合规措施。


## <a name="best-practice-implement-a-bcdr-strategy"></a>最佳做法：实施 BCDR 策略

在规划迁移到 Azure 期间，规划业务连续性和灾难恢复 (BCDR) 是要完成的一项关键任务。 在法律条款中，你的合同会包括一则不可抗力条款，它为飓风或地震等不可抗力造成的失责提供了借口。 但是，在灾难来临时，你仍有责任确保服务可继续运行，并在必要的情况下予以恢复。 这种能力可能决定了公司未来的成败。

概括而言，BCDR 策略必须考虑到：
- **数据备份**：如何保持数据的安全，以便在发生服务中断时可以轻松恢复数据。
- **灾难恢复**：发生服务中断时如何保持应用的弹性和可用性。 

### <a name="azure-resiliency-features"></a>Azure 复原功能
Azure 平台提供许多复原功能。

- **区域配对**：Azure 将区域配对，以便在数据驻留边界内提供区域保护。 Azure 确保区域对之间的物理隔离性，在发生广泛的服务中断时优先恢复对中的一个区域，单独在每个区域中部署系统更新，并允许使用 Azure 异地冗余存储等功能在区域对之间进行复制。
- **可用性区域**：可用性区域在 Azure 区域中建立独立的物理局部区域，以防范整个 Azure 数据中心发生故障。 每个局部区域具有独立的电源、网络基础设施和散热机制。
- **可用性集**：可用性集可防范数据中心内部发生故障。 可将 VM 分组到可用性集中，使其保持高可用性。 在每个可用性集中，Azure 实施多个容错域，以便将共用电源和网络交换机的底层硬件分组到在一起；Azure 还会实施更新域，以便将可以同时维护或重新启动的底层硬件分组到一起。 例如，将某个工作负荷分散在不同的 Azure VM 之间时，可将每个应用层的两个或更多个 VM 放入一个集。 例如，可将前端 VM 放入一个集，将数据层 VM 放入另一个集。 由于一个集内每次只会重新启动一个更新域，因此，Azure 可确保集内的 VM 分散在不同的容错域之间，而你可以确保集内的所有 VM 不会同时发生故障。

### <a name="set-up-bcdr"></a>设置 BCDR

迁移到 Azure 时必须知道，尽管 Azure 平台提供这些内置复原功能，但你仍然需要设计好 Azure 部署，才能利用可提供高可用性、灾难恢复和备份的 Azure 功能与服务。

- BCDR 解决方案取决于公司目标，并受 Azure 部署策略的影响。 基础结构即服务 (IaaS) 和平台即服务 (PaaS) 部署为 BCDR 带来了不同的挑战。
- 部署 BCDR 解决方案后，应定期对其进行测试，以检查策略是否仍旧可行。


## <a name="best-practice-back-up-your-data"></a>最佳做法：备份数据

在大多数情况下，本地工作负荷在迁移后即遭淘汰，必须扩展或更换用于备份数据的本地策略。 如果将整个数据中心迁移到 Azure，则需要使用 Azure 技术或第三方集成解决方案来设计和实施完整的备份解决方案。 


### <a name="back-up-an-iaas-deployment"></a>备份 IaaS 部署

对于 Azure IaaS VM 上运行的工作负荷，请考虑以下备份解决方案：

- **Azure 备份**：为 Azure Windows 和 Linux VM 提供应用程序一致性备份。
- **存储快照**：创建 Blob 存储的快照。

#### <a name="azure-backup"></a>Azure 备份

Azure 备份可以备份和创建存储于 Azure 存储中的数据恢复点。 Azure 备份可以备份 Azure VM 磁盘和 Azure 文件（预览版）。 Azure 文件在云中提供可通过 SMB 访问的文件共享。
   
可以通过多种方式使用 Azure 备份来备份 VM。

- **直接从 VM 设置备份**：可以直接通过 Azure 门户中的 VM 选项使用 Azure 备份来备份 VM。 可以每天备份 VM 一次，并可根据需要还原 VM 磁盘。 Azure 备份可创建应用感知的数据快照 (VSS)，无需在 VM 上安装代理。
- **在恢复服务保管库中直接备份**：可以通过部署 Azure 备份恢复服务保管库来备份 IaaS VM。 这样可以在一个位置跟踪和管理备份，并提供精细的备份和还原选项。 每天可在文件/文件夹级别最多备份三次。 这种备份不区分应用，并且不支持 Linux。 需在要备份的每个 VM 上安装 Microsoft Azure 恢复服务 (MARS) 代理。
- **Azure 备份服务器：在 Azure 备份服务器中保护 VM**：Azure 备份免费随附 Azure 备份服务器。 将 VM 备份到本地 Azure 备份服务器存储。 然后，将 Azure 备份服务器备份到 Azure 的保管库中。 备份区分应用，可针对频繁的备份和保留期提供全面的精细控制。 可在应用级别进行备份。 例如，备份 SQL Server 或 SharePoint。

为确保安全，Azure 备份将使用 AES-256 加密传输中的数据，并通过 HTTPS 将其发送到 Azure。 Azure 中备份的静态数据将使用[存储服务加密 (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) 进行加密，传输和存储中的数据也会得到加密。


![Azure 备份](./media/migrate-best-practices-security-management/iaas-backup.png)
*Azure 备份*

**了解更多：**

- [了解](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)不同类型的备份。
- 为 Azure VM [规划备份基础结构](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)。

#### <a name="storage-snapshots"></a>存储快照

Azure VM 作为页 Blob 存储在 Azure 存储中。 

- 快照捕获特定时间点的 Blob 状态。
- 作为 Azure VM 磁盘的替代备份方法，可创建存储 Blob 的快照，然后将其复制到另一个存储帐户。 
- 可以复制整个 Blob，或使用增量快照复制以便仅复制增量更改并减少存储空间。
- 作为额外的预防措施，可以针对 Blob 存储帐户启用软删除。 启用此功能后，已删除的 Blob 将标记为已删除，但不会立即将其清除。 在过渡期，仍可以还原该 Blob。

**了解更多：**

- [了解](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) Azure Blob 存储。
- [了解](https://docs.microsoft.com/azure/storage/blobs/storage-blob-snapshots)如何创建 Blob 快照。
- [查看](https://azure.microsoft.com/blog/microsoft-azure-block-blob-storage-backup) Blob 存储备份的示例方案。
- [了解](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)软删除。
- [Azure 存储中的灾难恢复和强制故障转移（预览版）](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

#### <a name="third-party-backup"></a>第三方备份

此外，可以使用第三方解决方案将 Azure VM 和存储容器备份到本地存储或其他云提供商。 [详细了解](https://azuremarketplace.microsoft.com/marketplace/apps?search=backup&page=1) Azure 市场中的备份解决方案。 


### <a name="back-up-a-paas-deployment"></a>备份 PaaS 部署


在 IaaS 中，你需要管理自己的 VM 和基础结构，而在 PaaS 中，模型平台和基础结构由提供商管理，这样，你便可以专注于核心应用逻辑和功能。 由于 PaaS 服务的类型多种多样，出于备份目的，需要单独评估每个服务。 下面将探讨两个常见的 Azure PaaS 服务 - Azure SQL 数据库和 Azure Functions。

#### <a name="back-up-azure-sql-database"></a>备份 Azure SQL 数据库

Azure SQL 数据库是完全托管式的 PaaS 数据库引擎。 它提供许多业务连续性功能，包括自动备份。

- SQL 数据库自动执行每周完整数据库备份，每隔 12 小时执行差异备份。 每隔 5 到 10 分钟创建事务日志备份，以防止数据库中的数据丢失。
- 备份是透明的，且不会产生额外的费用。
- 备份存储在 RA-GRS 存储中以实现异地冗余，并复制到配对的地理区域。
- 备份保留期取决于购买模式。 基于 DTU 的服务层提供的保留期为 7 天（适用于基本层）到 35 天（适用于其他层）。
- 在保留期内，可将数据库还原到某个时间点。 还可以还原已删除的数据库、还原到不同的地理区域，如果数据库具有长期保留策略 (LTR)，则可以从长期备份还原。


![Azure SQL 备份](./media/migrate-best-practices-security-management/sql-backup.png)
*Azure SQL 备份*

**了解更多：**
- 适用于 SQL 数据库的[自动备份](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- 使用自动备份[恢复数据库](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups)。

 
#### <a name="back-up-azure-functions"></a>备份 Azure Functions

由于 Azure Functions 或多或少地以代码形式运行，因此，你应该使用保护代码时所用的相同方法（例如 GitHub 中的源代码管理，或 Azure DevOps 服务）对其进行备份。

**了解更多：**

- 适用于 Azure DevOps 的[数据保护](https://docs.microsoft.com/azure/devops/articles/team-services-security-whitepaper?view=vsts)。

## <a name="best-practice-set-up-disaster-recovery"></a>最佳做法：设置灾难恢复 

除了保护数据以外，BCDR 规划还必须考虑到在发生灾难时如何使应用和工作负荷保持可用。 

### <a name="set-up-disaster-recovery-for-iaas-apps"></a>为 IaaS 应用设置灾难恢复

对于 Azure IaaS VM 和 Azure 存储中运行的工作负荷，请考虑以下解决方案：

- **Azure Site Recovery**：协调从主要区域到次要区域的 Azure VM 复制。 发生服务中断时，可以从主要区域故障转移到次要区域，用户可以继续访问应用。 恢复正常后，可以故障回复到主要区域。
- **Azure 存储**：Azure 为不同类型的存储提供内置的复原能力和高可用性：

#### <a name="azure-site-recovery"></a>Azure Site Recovery 

Azure Site Recovery 是确保在发生服务中断时 Azure VM 可以联机并且 VM 应用可供使用的主要 Azure 服务。 Site Recovery 将 VM 从主要区域复制到 Azure 次要区域。 当灾难发生时，可以从主要区域故障转移 VM，并在次要区域中像平时一样访问它们。 运营恢复正常后，可将 VM 故障回复到主要区域。


![Azure Site Recovery](./media/migrate-best-practices-security-management/site-recovery.png)
*Site Recovery*

**了解更多：**
- [查看](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-disaster-recovery-guidance)适用于 Azure VM 的灾难恢复方案。
- [了解](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-replicate-after-migration)如何在迁移后为 Azure VM 设置灾难恢复。

#### <a name="azure-storage"></a>Azure 存储

为了实现内置的复原和高可用性，将会复制 Azure 存储。

-   **异地冗余存储 (GRS)**：防范区域范围的服务中断，在给定的年度提供至少 99.99999999999999%（16 个 9）的持久性目标。
    - 存储数据将复制到与主要区域配对的次要区域。
    - 如果主要区域发生故障，并且 Microsoft 发起了到次要区域的故障转移，则你对自己的数据拥有读取访问权限。
- **读取访问权限异地冗余存储 (RA-GRS)**：防范区域范围的服务中断。
    - 存储数据将复制到次要区域。
    - 不管 Microsoft 是否发起了故障转移，都保证你都对次要区域中复制的数据拥有读取访问权限。 如果同一区域中的两个或更多个数据中心出现问题，地理隔离的区域中仍会提供你的数据。
-   **区域冗余存储 (ZRS)**：防范数据中心故障。
    - ZRS 以同步方式在单个区域中的三个存储群集之间复制数据。 群集是物理分隔的，各自位于其自身的可用性区域中。
    - 如果发生灾难，你的存储仍然可用。 ZRS 应是任务关键型工作负荷的最低目标。



**了解更多：**

- [了解](https://docs.microsoft.com/azure/storage/common/storage-redundancy) Azure 存储复制。


### <a name="set-up-disaster-recovery-for-paas-workloads"></a>为 PaaS 工作负荷设置灾难恢复

让我们探讨 PaaS 工作负荷示例的灾难恢复选项。

#### <a name="disaster-recovery-of-azure-sql-server"></a>Azure SQL Server 的灾难恢复

有许多不同的选项，每个选项会影响数据是否丢失、恢复时间和成本。

可以使用故障转移组和活动异地复制来提供复原能力，以应对区域性服务中断和灾难性故障

- **活动异地复制**：部署活动异地复制可以在数据中心发生服务中断或者无法与主数据库建立连接时，快速实现灾难恢复。
    - 异地复制会在相同或不同区域中的最多四个辅助节点中持续创建数据库的可读副本。
    - 发生服务中断时，可以故障转移到某个次要区域，并使数据库恢复联机状态。
- **自动故障转移组**：自动故障转移组扩展了活动异地复制，它能够以透明方式故障转移多个数据库。
    - 自动故障转移组提供活动异地复制的强大抽象，可以实现组级数据库复制和自动故障转移。
    - 可以在创建的故障转移组中包含一个主服务器用于托管一个或多个主数据库，包含一个辅助服务器用于托管主服务器的只读副本，包含侦听器用于指向每个服务器，并包含自动故障转移策略。
    - 指定的侦听器终结点消除了在故障转移后更改 SQL 连接字符串的需要。
- **异地还原**： 
    -   使用异地还原可将数据库恢复到不同的区域。 所有 Azure 数据库的自动备份将在后台复制到次要区域。 异地还原始终从次要区域中存储的备份文件副本还原数据库。
-   **区域冗余数据库**为 Azure 可用性区域提供内置支持。
    - 在数据中心发生故障时，区域冗余数据库可以增强 Azure SQL Server 的高可用性。
    - 使用区域冗余可将冗余的数据库副本放在某个区域的不同可用性区域内。 



![异地复制](./media/migrate-best-practices-security-management/geo-replication.png)
*异地复制*

**了解更多：**
- [了解](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability) Azure SQL Server 的高可用性。
- [阅读](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)用于灾难恢复的 Azure SQL 数据库 101。
- [获取](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)活动异地复制和故障转移组的概述。
- [了解](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)灾难恢复设计。
- [获取](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)有关故障转移组的最佳做法。
- [获取](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-security-config)有关异地还原或故障转移后的安全最佳做法。
- [了解](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability#zone-redundant-configuration)区域冗余
- [了解](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-drills)如何针对 SQL 数据库执行灾难恢复演练。

### <a name="disaster-recovery-for-azure-functions"></a>Azure Functions 的灾难恢复

如果 Azure 中的计算基础结构发生故障，Azure 函数应用可能不可用。

- 为了尽量减少出现这种停机的可能性，可以使用不同区域中部署的两个函数应用。
- 可将 Azure 流量管理器配置为检测主要函数应用中的问题，并自动将流量重定向到次要区域中的函数应用。
- 将流量管理器与异地冗余存储配合使用可以在发生区域性故障时，在多个区域中获得相同的功能


![流量管理器](./media/migrate-best-practices-security-management/traffic-manager.png)
*流量管理器*

**了解更多：**

- [了解](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) Azure 应用的灾难恢复。
- [了解](https://docs.microsoft.com/azure/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution) Azure Durable Functions 的灾难恢复和异地分布。


## <a name="best-practice-use-managed-disks-and-availability-sets"></a>最佳做法：使用托管磁盘和可用性集

Azure 使用可用性集将 VM 以逻辑方式分组在一起，并使集内的 VM 与其他资源相隔离。 可用性集内的 VM 分散在多个容错域之间并具有独立的子系统，以防范局部故障；它们还分散在多个更新域之间，使集内的所有 VM 不会同时重新启动。

Azure 托管磁盘通过管理与 VM 磁盘关联的存储帐户来简化 Azure IaaS VM 的磁盘管理。

- 我们建议尽量使用托管磁盘。 只需指定所需的存储类型和磁盘大小，Azure 就会在幕后自动创建并管理磁盘。
- 可将现有磁盘转换为托管磁盘。
- 为了实现较高的恢复能力和可用性，应在可用性集内创建 VM。 发生计划性或非计划性服务中断时，可用性集可确保集内至少有一个 VM 仍可供使用。


![托管磁盘](./media/migrate-best-practices-security-management/managed-disks.png)
*托管磁盘*

**了解更多：**
- [获取](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)托管磁盘的概述。
- [了解](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)如何将磁盘转换为托管磁盘。
- [了解](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)如何在 Azure 中管理 Windows VM 的可用性。


## <a name="best-practice-monitor-resource-usage-and-performance"></a>最佳做法：监视资源使用情况和性能 

你可能已将工作负荷转移到 Azure，以利用 Azure 无限缩放功能。 但是，转移工作负荷并不意味着 Azure 会在未收到你的输入的情况下自动实施缩放。 示例：

- 如果营销组织推送了一条新的电视广告，而这条广告使流量增加了 300%，则可能会导致站点出现可用性问题。 新迁移的工作负荷可能会达到分配的限制，然后崩溃。
- 另一个示例是迁移的工作负荷遭到分布式拒绝服务 (DDoS) 攻击。 在这种情况下，你可能不希望进行缩放，而是防止攻击源访问你的资源。

这两种案例的解决方法有所不同，但都需要通过使用情况和性能监视来洞察发生的情况。

- Azure Monitor 可以帮助揭示这些指标，并通过警报、自动缩放、事件中心、逻辑应用等功能提供响应。
- 除了 Azure 监视以外，还可以集成第三方 SIEM 应用程序来监视审核和性能事件的 Azure 日志。


![Azure Monitor](./media/migrate-best-practices-security-management/monitor.png)
*Azure Monitor*

**了解更多：**

- [了解](https://docs.microsoft.com/azure/azure-monitor/overview) Azure Monitor。
- [获取](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)有关监视和诊断的最佳做法。
- [了解](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling)自动缩放。
- [了解](https://docs.microsoft.com/azure/security-center/security-center-export-data-to-siem)如何将 Azure 数据路由到 SIEM 工具。

## <a name="best-practice-enable-diagnostic-logging"></a>最佳做法：启用诊断日志记录

Azure 资源会生成相当多的日志记录指标和遥测数据。

- 默认情况下，大多数资源类型并未启用诊断日志记录。
- 为各个资源启用诊断日志记录后，可以查询日志记录数据，并基于该数据生成警报和 Playbook。
- 启用诊断日志记录时，每个资源具有一组特定的类别。 请选择一个或多个日志记录类别，以及日志数据的位置。 可将日志发送到存储帐户、事件中心或 Log Analytics。 


![诊断日志记录](./media/migrate-best-practices-security-management/diagnostics.png)
*诊断日志记录*

**了解更多：**

- [了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)如何收集和使用日志数据。
- [了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema)诊断日志记录支持的操作。


## <a name="best-practice-set-up-alerts-and-playbooks"></a>最佳做法：设置警报和 Playbook

为 Azure 资源启用诊断日志记录后，可以开始使用日志记录数据来创建自定义警报。

- 在监视数据中发现异常状况时，警报会主动予以通知。 然后，可以在系统用户注意到这些问题之前将其解决。 可以针对指标值、日志搜索查询、活动日志事件、平台运行状况和网站可用性等对象发出警报。
- 触发警报后，可以运行逻辑应用 Playbook。 Playbook 可以帮助你自动编写和协调针对特定警报的响应。 Playbook 基于 Azure 逻辑应用。 可以使用逻辑应用模板来创建 Playbook，或者可以自行创建 Playbook。
- 举一个简单的例子，你可以创建一个在对网络安全组执行端口扫描时触发的警报。  可以设置一个 Playbook 用于运行和锁定扫描源的 IP 地址。
- 另一个示例是出现内存泄漏的应用。  当内存用量达到特定的水平时，Playbook 可以回收进程。


![警报](./media/migrate-best-practices-security-management/alerts.png)
*警报*

**了解更多：**
- [了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts)警报。
- [了解](https://docs.microsoft.com/azure/security-center/security-center-playbooks)可对安全中心警报做出响应的安全 Playbook。

## <a name="best-practice-use-the-azure-dashboard"></a>最佳做法：使用 Azure 仪表板

Azure 门户是一个基于 Web 的统一控制台，用于生成、管理和监视从简单 Web 应用到复杂云应用程序的一切项目。 它包括可自定义仪表板和可访问性选项。
- 可以创建多个仪表板，并与对 Azure 订阅有访问权限的其他用户共享。
- 使用此共享模型，团队可以洞察 Azure 环境，从而在管理云中的系统时保持主动。


![Azure 仪表板](./media/migrate-best-practices-security-management/dashboard.png)
*Azure 仪表板*

**了解更多：**

- [了解](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)如何创建仪表板。
- [了解](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-structure)仪表板结构。


## <a name="best-practice-understand-support-plans"></a>最佳做法：了解支持计划

有时，你需要与内部支持人员或 Microsoft 支持人员协作。 在执行灾难恢复等方案期间，制定一套支持策略和过程至关重要。 此外，应该为管理员和支持人员提供有关实施这些策略的培训。

- 如果 Azure 服务问题影响到了工作负荷（这种情况不太可能出现），管理员应该知道如何以最适当且有效的方式向 Microsoft 提交支持票证。
- 熟悉为 Azure 提供的各种支持计划。 这些计划包括专用于开发人员实例的响应时间、在 15 分钟以内做出响应的顶级支持，等等。


![支持计划](./media/migrate-best-practices-security-management/support.png)
*支持计划*

**了解更多：**
- [获取](https://azure.microsoft.com/support/options/) Azure 支持计划的概述。
- [了解](https://azure.microsoft.com/support/legal/sla/)服务级别协议 (SLA)。

## <a name="best-practice-manage-updates"></a>最佳做法：管理更新

使用最新操作系统和软件更新保持 Azure VM 的更新状态是一个非常繁琐的任务。 如果能够发现所有 VM，确定它们所需的更新并自动推送这些更新，则此过程就会变得非常方便。

- 可以使用 Azure 自动化中的更新管理，为部署在 Azure、本地或其他云提供商中的 Windows 和 Linux 计算机管理操作系统更新。 
- 使用更新管理可以快速评估所有代理计算机上可用更新的状态，并管理更新的安装。
- 可以直接通过 Azure 自动化帐户为 VM 启用更新管理。 还可以通过 Azure 门户中的 VM 页更新单个 VM。
- 此外，可将 Azure VM 注册到 System Center Configuration Manager。 然后，可将 Configuration Manager 工作负荷迁移到 Azure，并从单个 Web 界面执行报告和软件更新。


![VM 更新](./media/migrate-best-practices-security-management/updates.png)
*更新*

**了解更多：**

- [了解](https://docs.microsoft.com/azure/automation/automation-update-management) Azure 中的更新管理。
- [了解](https://docs.microsoft.com/azure/automation/oms-solution-updatemgmt-sccmintegration)如何将 Configuration Manager 与更新管理集成。
- 有关 Azure 中的 Configuration Manager 的[常见问题解答](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure)。


## <a name="implement-a-change-management-process"></a>实施变更管理流程

与使用任何生产系统时一样，进行任何类型的更改都可能会影响你的环境。 在迁移的环境中，要求提交请求才能对生产系统做出更改的变更管理流程是很有价值的补充。

- 可以针对变更管理构建最佳做法框架，以提高管理员和支持人员的意识。
- 可以借助 Azure 自动化来对已迁移的工作流进行配置管理和更改跟踪。
- 实施变更管理流程时，可以使用审核日志将 Azure 更改日志链接到假设存在（不一定存在）的更改请求。 因此，如果你看到做出了某项更改但没有相应的更改请求，可以调查流程中发生了什么问题。

Azure 在 Azure 自动化中提供更改跟踪解决方案：

- 该解决方案跟踪对 Windows 和 Linux 软件与文件、Windows 注册表项、Windows 服务及 Linux 守护程序的更改。
- 受监视服务器上的更改将发送到云中的 Log Analytics 服务进行处理。
- 逻辑应用于接收的数据，云服务则记录数据。
- 在“更改跟踪”仪表板上，可以轻松查看服务器基础结构中所做的更改。


![变更管理](./media/migrate-best-practices-security-management/change.png)
*变更管理*

**了解更多：**

- [了解](https://docs.microsoft.com/azure/automation/automation-change-tracking)更改跟踪。
- [了解](https://docs.microsoft.com/azure/automation/automation-intro) Azure 自动化功能。




## <a name="next-steps"></a>后续步骤 

查看其他最佳做法：


- 有关迁移后的网络设置的[最佳做法](migrate-best-practices-networking.md)。
- 有关迁移后的成本管理的[最佳做法](migrate-best-practices-costs.md)。








