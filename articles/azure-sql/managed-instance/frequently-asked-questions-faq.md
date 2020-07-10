---
title: 常见问题 (FAQ)
titleSuffix: Azure SQL Managed Instance
description: 'Azure SQL 托管实例常见问题解答 (常见问题) '
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: d2e4b07c97e09fce5cdaa034e2fe67a18ef0d7f1
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171153"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Azure SQL 托管实例常见问题解答 (常见问题) 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文包含有关[AZURE SQL 托管实例](sql-managed-instance-paas-overview.md)的最常见问题。

## <a name="supported-features"></a>支持的功能

**在哪里可以找到 SQL 托管实例上支持的功能列表？**

有关 SQL 托管实例中支持的功能的列表，请参阅[AZURE sql 托管实例功能](../database/features-comparison.md)。

有关 Azure SQL 托管实例和 SQL Server 之间的语法和行为的差异，请参阅[与 SQL Server 的 t-sql 差异](transact-sql-tsql-differences-sql-server.md)。


## <a name="technical-specification-resource-limits-and-other-limitations"></a>技术规范、资源限制和其他限制
 
**在哪里可以找到 SQL 托管实例的技术特性和资源限制？**

有关可用的硬件生成特性，请参阅[硬件生成中的技术差异](resource-limits.md#hardware-generation-characteristics)。
有关可用的服务层及其特征，请参阅[服务层之间的技术差异](resource-limits.md#service-tier-characteristics)。

**我有资格获得哪些服务层？**

任何客户都有资格获得任何服务层。 但是，如果你想要通过使用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)在 Azure SQL 托管实例上交换现有的折扣率，请记住，具有软件保障的 SQL Server Enterprise Edition 客户有资格使用[常规用途](../database/service-tier-general-purpose.md)或[业务关键](../database/service-tier-business-critical.md)的性能层，并且具有软件保障的 SQL Server Standard Edition 客户仅适用于常规用途性能层。 有关更多详细信息，请参阅[AHB 的特定权限](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server)。

**SQL 托管实例支持哪些订阅类型？**

有关支持的订阅类型的列表，请参阅[支持的订阅类型](resource-limits.md#supported-subscription-types)。 

**支持哪些 Azure 区域？**

可以在大多数 Azure 区域中创建托管实例;请参阅[SQL 托管实例支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)。 如果你需要当前不受支持的区域中的托管实例，请[通过 Azure 门户发送支持请求](../database/quota-increase-request.md)。

**SQL 托管实例部署是否有任何配额限制？**

托管实例有两个默认限制：限制你可以使用的子网数量，以及可预配的 Vcore 数的限制。 各个订阅类型和区域的限制各不相同。 有关按订阅类型列出的区域资源限制列表，请参阅[区域资源限制](resource-limits.md#regional-resource-limitations)中的表。 这些是可以按需增加的软限制。 如果需要在当前区域中预配更多托管实例，则发送支持请求以使用 Azure 门户增加配额。 有关详细信息，请参阅[AZURE SQL 数据库的请求配额增加](../database/quota-increase-request.md)。

**我是否可以根据需要增加托管实例上的数据库数限制 (100) ？**

否，当前没有已提交的计划来增加 SQL 托管实例上的数据库数。 

**如果我有超过8TB 的数据，可以迁移到何处？**
可以考虑迁移到符合工作负荷的其他 Azure 风格： [AZURE SQL 数据库超大规模](../database/service-tier-hyperscale.md)或[azure 虚拟机上的 SQL Server](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)。

**如果我有特定的硬件要求（如更大的 RAM）到 vCore 或更多 Cpu，可以迁移到何处？**
可以考虑迁移到 Azure 虚拟机或[AZURE SQL 数据库](../database/sql-database-paas-overview.md)内存/cpu[上的 SQL Server](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) 。

## <a name="known-issues--bugs"></a>已知问题和 bug

**在何处可以找到已知问题和 bug？**

有关错误和已知问题，请参阅[已知问题](../database/doc-changes-updates-release-notes.md#known-issues)。

## <a name="new-features"></a>新增功能

**在何处可以找到最新功能和公共预览版功能？**

对于新功能和预览功能，请参阅[发行说明](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)。

## <a name="create-update-delete-or-move-sql-managed-instance"></a>创建、更新、删除或移动 SQL 托管实例

**如何预配 SQL 托管实例？**

可以通过[Azure 门户](instance-create-quickstart.md)、 [PowerShell](scripts/create-configure-managed-instance-powershell.md)、 [Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281)和[ARM 模板](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates)设置实例。

**能否在现有订阅中预配托管实例？**

是的，如果订阅属于所[支持的订阅类型](resource-limits.md#supported-subscription-types)，则可以在现有订阅中设置托管实例。

**为什么无法在子网中设置名称以数字开头的托管实例？**

这是对基于 regex ^ [a zA-Z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (？ <！ \. 的基本组件的当前限制\s] ) $。 所有通过 regex 的名称和都是有效的子网名称。

**如何缩放托管实例？**

可以通过[Azure 门户](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation)、 [PowerShell](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell)、 [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-update)或[ARM 模板](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates)缩放托管实例。

**是否可以将托管实例从一个区域移到另一个区域？**

可以。 有关说明，请参阅[跨区域移动资源](../database/move-resources-across-regions.md)。

**如何删除托管实例？**

可以通过 Azure 门户、 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance?view=azps-4.3.0)、 [AZURE CLI](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-delete)或[资源管理器 REST api](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)删除托管实例。

**创建或更新实例或还原数据库需要多长时间？**

需要创建新的托管实例或更改服务层 (Vcore，存储) 的时间取决于多个因素。 请参阅[管理操作](sql-managed-instance-paas-overview.md#management-operations)。
 
## <a name="naming-conventions"></a>命名约定

**托管实例是否可以与 SQL Server 本地实例同名？**

不支持更改托管实例的名称。

**能否更改 DNS 区域前缀？**

是，托管实例默认 DNS 区域 *。 database.windows.net*可以更改。 

若要使用其他 DNS 区域而不是默认区域（例如“.contoso.com”**），请执行以下操作： 
- 使用 CliConfig 定义别名。 该工具只是一个注册表设置包装，因此也可以使用组策略或脚本完成此操作。
- 将*CNAME*与*TrustServerCertificate = true*选项一起使用。

## <a name="move-a-database-from-sql-managed-instance"></a>将数据库从 SQL 托管实例 

**如何将数据库从 SQL 托管实例移回 SQL Server 或 Azure SQL 数据库？**

可以将[数据库导出到 bacpac](../database/database-export.md) ，然后[导入 bacpac 文件](../database/database-import.md)。 如果数据库小于 100 GB，我们建议使用此方法。

如果数据库中的所有表具有主键，则可以使用事务复制。

`COPY_ONLY`无法将从 sql 托管实例获取的本机备份还原到 SQL Server，因为 sql 托管实例的数据库版本比 SQL Server 更高。

## <a name="migrate-an-instance-database"></a>迁移实例数据库

**如何将实例数据库迁移到 Azure SQL 数据库？**

一种做法是[将数据库导出到 BACPAC](../database/database-export.md)，然后[导入 BACPAC 文件](../database/database-import.md)。 

如果数据库小于 100 GB，我们建议使用此方法。 如果数据库中的所有表具有主键，则可以使用事务复制。

## <a name="switch-hardware-generation"></a>切换硬件代系 

**能否在第4代和第5代联机之间切换 SQL 托管实例硬件生成？**

如果在预配 SQL 托管实例的区域中同时提供两个硬件，则可以在硬件生成之间进行自动联机切换。 在这种情况下，可以查看 " [vCore 模型概述" 页](../database/service-tiers-vcore.md)，其中说明了如何在硬件生成之间切换。

这是一个长时间运行的操作，因为新的托管实例将在后台预配，在新实例与新实例之间自动传输，并在进程结束时进行快速故障转移。 

**如果同一区域中不支持两个硬件代，会怎么样？**

如果同一区域不能同时支持这两种硬件代系，可以更改硬件代系，但必须手动完成该操作。 这要求你在需要提供硬件的区域中预配新的实例，并在新旧实例之间手动备份和还原数据。

**如果没有足够的 IP 地址来执行更新操作，该怎么办？**

如果在预配托管实例的子网中没有足够的 IP 地址，则必须在其中创建新的子网和新的托管实例。 此外，我们还建议创建新的子网，并分配更多的 IP 地址，以便将来的更新操作将避免类似情况。  (为适当的子网大小，请查看[如何确定 VNet 子网的大小](vnet-subnet-determine-size.md)。 ) 预配新实例后，可以在旧实例与新实例之间手动备份和还原数据，或执行跨实例[时间点还原](point-in-time-restore.md?tabs=azure-powershell)。 


## <a name="tune-performance"></a>调整性能

**如何实现 SQL 托管实例的优化性能？**

常规用途层中的 SQL 托管实例使用远程存储，因此，数据和日志文件的大小对性能很重要。 有关详细信息，请参阅[常规用途 SQL 托管实例性能上的日志文件大小的影响](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)。

如果工作负荷包含大量小型事务，请考虑将连接类型从代理切换为重定向模式。

## <a name="maximum-storage-size"></a>最大存储大小

**SQL 托管实例的最大存储大小是多少？**

SQL 托管实例的存储大小取决于所选的服务层 (常规用途或业务关键) 。 有关这些服务层的存储限制，请参阅[服务层特性](../database/service-tiers-general-purpose-business-critical.md)。

  
## <a name="networking-requirements"></a>网络要求 

**托管实例子网上的当前入站/出站 NSG 约束是什么？**

所需的 NSG 和 UDR 规则记录在[此处](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)，并由服务自动设置。
请记住，这些规则只是维护服务所需的规则。 若要连接到托管实例并使用不同的功能，您将需要设置需要维护的附加功能特定规则。

**如何针对管理端口设置入站 NSG 规则？**

SQL 托管实例负责设置管理端口的规则。 这是通过名为[服务辅助子网配置](connectivity-architecture-overview.md#service-aided-subnet-configuration)的功能来实现的。
这是为了确保不中断管理流量的流动，以便满足 SLA。

**能否获取用于入站管理流量的源 IP 范围？**

正确。 可以通过[配置网络观察程序流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#analyze-traffic-to-or-from-a-network-security-group)来分析通过网络安全组传入的流量。

**是否可以将 NSG 设置为控制对 (端口 1433) 的数据终结点的访问？**

正确。 设置托管实例后，可以设置控制对端口1433的入站访问的 NSG。 建议尽可能缩小其 IP 范围。

**是否可以设置 NVA 或本地防火墙以根据 Fqdn 筛选出站管理流量？**

错误。 由于以下几个原因，不支持此操作：
-   表示对入站管理请求的响应的路由流量将是非对称的，因此无法运行。
-   转到存储的路由流量将受到吞吐量限制和延迟的影响，因此，我们无法提供预期的服务质量和可用性。
-   根据经验，这些配置容易出错并且不受支持。

**能否为出站非管理流量设置 NVA 或防火墙？**

正确。 实现此目的的最简单方法是将0/0 规则添加到与托管实例子网关联的 UDR，以通过 NVA 路由流量。
 
**托管实例需要多少 IP 地址？**

子网必须有足够数量的可用 [IP 地址](connectivity-architecture-overview.md#network-requirements)。 若要确定 SQL 托管实例的 VNet 子网大小，请参阅[确定托管实例所需的子网大小和范围](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet)。 

**如果没有足够的 IP 地址来执行实例更新操作，该怎么办？**

如果在预配托管实例的子网中没有足够的[IP 地址](connectivity-architecture-overview.md#network-requirements)，则必须在其中创建新的子网和新的托管实例。 此外，我们还建议创建新的子网，并分配更多的 IP 地址，以便将来的更新操作将避免类似情况。 预配新实例后，可以在新实例和新实例之间手动备份和还原数据，或执行跨实例[时间点还原](point-in-time-restore.md?tabs=azure-powershell)。

**是否需要空的子网来创建托管实例？**

错误。 你可以使用已包含托管实例 (s) 的空子网或子网。 

**能否更改子网地址范围？**

如果中有托管实例，则不能。 这是一种 Azure 网络基础结构的限制。 只允许[将其他地址空间添加到空白子网](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet#change-subnet-settings)。 

**是否可以将托管实例移到另一个子网？**

错误。 这是当前托管实例的设计限制。 但是，你可以在另一个子网中预配新的实例，并在旧实例与新实例之间手动备份和还原数据，或执行跨实例[时间点还原](point-in-time-restore.md?tabs=azure-powershell)。

**是否需要空虚拟网络来创建托管实例？**

这不是必需的。 可以[为 AZURE sql 托管实例创建虚拟网络](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-vnet-subnet)，也可以[为 Azure sql 托管实例配置现有虚拟网络](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet)。

**是否可以将托管实例与其他服务一起放入子网？**

错误。 目前，我们不支持将托管实例放置在已包含其他资源类型的子网中。


## <a name="mitigate-data-exfiltration-risks"></a>缓解数据透露风险  

**如何缓解数据透露风险？**

为了缓解任何数据透露风险，我们建议客户应用一组安全设置和控制：

- 针对所有数据库启用[透明数据加密 (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql)。
- 禁用公共语言运行时 (CLR)。 也建议在本地禁用 CLR。
- 使用 Azure Active Directory (仅 Azure AD) 身份验证。
- 使用低权限 DBA 帐户访问实例。
- 为 sysadmin 帐户配置 JIT jumpbox 访问权限。
- 启用 [SQL 审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)，并将其与警报机制相集成。
- 从[高级数据安全 (ADS) ](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)套件中打开[威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)。


## <a name="cost-saving-use-cases"></a>节约成本的用例

**在哪里可以找到用例以及通过 SQL 托管实例节省的成本？**

SQL 托管实例案例研究：

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

为了更好地了解与部署 Azure SQL 托管实例相关的优势、成本和风险，还有一个 Forrester 研究： [Microsoft Azure SQL 数据库托管实例的总体经济影响](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。


## <a name="dns"></a>DNS

**能否为 SQL 托管实例配置自定义 DNS？**

正确。 请参阅[如何为 AZURE SQL 托管实例配置自定义 DNS](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。

**是否可以执行 DNS 刷新？**

目前，我们不提供刷新 SQL 托管实例的 DNS 服务器配置的功能。

DNS 配置最终会刷新：

- 当 DHCP 租约过期时。
- 平台升级时。

一种解决方法是，将 SQL 托管实例降级到 4 Vcore 并在以后再升级。 这样刷新 DNS 配置会产生一种负面影响。


## <a name="ip-address"></a>IP 地址

**能否使用 IP 地址连接到 SQL 托管实例？**

不支持使用 IP 地址连接到 SQL 托管实例。 SQL 托管实例主机名映射到 SQL 托管实例虚拟群集前面的负载均衡器。 由于一个虚拟群集可以托管多个托管实例，因此无法将连接路由到适当的托管实例，无需显式指定名称。

有关 SQL 托管实例虚拟群集体系结构的详细信息，请参阅[虚拟群集连接体系结构](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)。

**SQL 托管实例是否可以有静态 IP 地址？**

在极少数情况下，可能需要将 SQL 托管实例联机迁移到新的虚拟群集。 需要进行这种迁移的原因是，我们的技术堆栈发生了变化，旨在提高服务的安全性和可靠性。 迁移到新的虚拟群集会导致更改映射到 SQL 托管实例主机名的 IP 地址。 SQL 托管实例服务不会声明静态 IP 地址支持，并保留在不另行通知的情况下将其更改为定期维护周期的权限。

出于此原因，我们强烈反对依赖于 IP 地址的不可变性，因为这可能会导致不必要的停机时间。

## <a name="change-time-zone"></a>更改时区

**是否可以更改现有托管实例的时区？**

首次预配托管实例时可以设置时区配置。 不支持更改现有托管实例的时区。 有关详细信息，请参阅时区[限制](timezones-overview.md#limitations)。

解决方法包括使用适当的时区创建新的托管实例，然后执行手动备份和还原，或执行 "[跨实例时间点还原](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)"。


## <a name="security-and-database-encryption"></a>安全性和数据库加密

**Sysadmin 服务器角色是否可用于 SQL 托管实例？**

是，客户可以创建作为 sysadmin 角色成员的登录名。  假定 sysadmin 权限的客户也被认为是操作实例的责任，这会对 SLA 承诺产生负面影响。 若要将登录名添加到 sysadmin 服务器角色，请参阅[Azure AD authentication](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-aad-security-tutorial#azure-ad-authentication)。

**SQL 托管实例是否支持透明数据加密？**

是的，SQL 托管实例支持透明数据加密。 有关详细信息，请参阅[SQL 托管实例透明数据加密](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal)。

**能否对 TDE 使用 "自带密钥" 模型？**

是的，适用于 BYOK 方案 Azure Key Vault 适用于 Azure SQL 托管实例。 有关详细信息，请参阅[与客户托管的密钥透明数据加密](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-ver15&tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)。

**是否可以迁移加密的 SQL Server 数据库？**

可以。 要迁移加密的 SQL Server 数据库，需要将现有证书导出并导入到托管实例中，然后执行完整数据库备份并将其还原到托管实例中。 

你还可以使用[Azure 数据库迁移服务](https://azure.microsoft.com/services/database-migration/)迁移 TDE 加密数据库。

**如何为 SQL 托管实例配置 TDE 保护程序轮换？**

您可以使用 Azure Cloud Shell 来旋转托管实例的 TDE 保护程序。 有关说明，请参阅[SQL 托管实例中的透明数据加密 Azure Key Vault 使用你自己的密钥](scripts/transparent-data-encryption-byok-powershell.md)。

**是否可以将加密的数据库还原到 SQL 托管实例？**

是的，无需解密数据库即可将其还原到 SQL 托管实例。 你确实需要提供一个证书/密钥，将其用作源系统上的加密密钥保护程序托管实例，以便能够从加密的备份文件中读取数据。 要运行此操作有两个可行的方式：

- *将证书保护程序上载到 SQL 托管实例*。 只能使用 PowerShell 执行此操作。 [示例脚本](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate)描述了整个过程。
- 将*非对称密钥保护程序上载到 Azure Key Vault 并将 SQL 托管实例点到它*。 此方法类似于自带密钥 (BYOK) TDE 用例，该用例也使用 Key Vault 集成来存储加密密钥。 如果你不想使用密钥作为加密密钥保护程序，只想让 SQL 托管实例使用该密钥来还原加密数据库 (s) ，请按照[设置 BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)的说明进行操作，并不要选中 "**将所选密钥设为默认 TDE 保护**程序" 复选框。

使加密保护程序可供 SQL 托管实例使用后，可以继续执行标准数据库还原过程。

## <a name="purchasing-models-and-benefits"></a>购买模型和权益

**SQL 托管实例有哪些购买模型？**

SQL 托管实例提供[基于 vCore 的购买模型](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)。

**SQL 托管实例有哪些成本优势？**

可以通过以下方式节省 Azure SQL 权益的成本：
-   最大限度地提高本地许可证的现有投资，并通过[Azure 混合权益](https://docs.microsoft.com/azure/azure-sql/azure-hybrid-benefit?tabs=azure-powershell)节省高达55%。 
-   提交给计算资源的预订，并使用[保留实例权益](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity)节省高达33%。 结合 Azure 混合权益，节省高达82%。 
-   利用[Azure 开发/测试定价权益](https://azure.microsoft.com/pricing/dev-test/)，为正在进行的开发和测试工作负荷提供折扣率，可节省高达55% 与标价。

**谁有资格获得预订实例权益？**

若要获得预订实例权益的资格，订阅类型必须是企业协议 (产品/服务（BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p) ）或使用即用即付定价 (产品/服务的个人协议产品/服务) 。 有关预订的详细信息，请参阅[保留实例权益](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity)。 

**是否可以取消、交换或退款预留？**

您可以取消、交换或退款保留，但有一些限制。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)。

## <a name="billing-for-managed-instance-and-backup-storage"></a>托管实例和备份存储计费

**什么是 SQL 托管实例定价选项？**

若要浏览托管实例定价选项，请参阅[定价页](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)。

**如何跟踪托管实例的计费成本？**

可以使用[Azure 成本管理解决方案](https://docs.microsoft.com/azure/cost-management-billing/)实现此目的。 导航到[Azure 门户](https://portal.azure.com)中的 "**订阅**"，然后选择 "**成本分析**"。 

使用**累积成本**选项，并按**资源类型**筛选 `microsoft.sql/managedinstances` 。

**自动备份的成本是多少？**

不管备份保持期设置如何，都可以获得与购买的保留数据存储空间相同的可用备份存储空间量。 如果备份存储消耗在已分配的可用备份存储空间内，则托管实例上的自动备份将不会产生额外的费用，因此将不收取任何费用。 超过可用空间的情况下，如果超过可用空间，将会产生大约 $0.20-$0.24 美元/月的费用，或者查看定价页以了解你所在区域的详细信息。 有关更多详细信息，请参阅[备份存储消耗说明](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)。

**如何监视备份存储消耗的计费成本？**

可以通过 Azure 门户监视备份存储的成本。 有关说明，请参阅[监视自动备份的成本](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=managed-instance#monitor-costs)。 

**如何优化托管实例上的备份存储成本？**

若要优化备份存储成本，请参阅[SQL 托管实例上的精细备份优化](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935)。

## <a name="password-policy"></a>密码策略 

**适用于 SQL 托管实例 SQL 登录名的密码策略是什么？**

Sql 托管实例用于 SQL 登录名的密码策略继承应用于构成托管实例的虚拟群集的虚拟机的 Azure 平台策略。 目前不可能更改这些设置，因为这些设置由 Azure 定义并由托管实例继承。

 > [!IMPORTANT]
 > Azure 平台可以更改策略要求，而不会通知服务依赖于该策略。

**当前的 Azure 平台策略是什么？**

每个登录名必须在登录时设置其密码，并在达到最长期限后更改其密码。

| **策略** | **安全设置** |
| --- | --- |
| 最长密码期限 | 42天 |
| 最短密码期限 | 1 天 |
| 最短密码长度 | 10个字符 |
| 密码必须符合复杂性要求 | Enabled |

**是否可以在登录级别禁用 SQL 托管实例中的密码复杂性和过期？**

是的，可以控制登录级别 CHECK_POLICY 和 CHECK_EXPIRATION 字段。 可以通过执行以下 T-sql 命令来检查当前设置：

```sql
SELECT *
FROM sys.sql_logins
```

之后，你可以通过执行以下操作来修改指定的登录设置：

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

 (将 "test" 替换为所需的登录名，并调整策略和过期值) 

## <a name="azure-feedback-and-support"></a>Azure 反馈和支持

**在哪里可以保留我对 SQL 托管实例改进的看法？**

你可以为新的托管实例功能投票，或对[SQL 托管实例反馈论坛](https://feedback.azure.com/forums/915676-sql-managed-instance)上的投票提供新的改进建议。 这样一来，您就可以对产品开发做出贡献，并帮助我们确定潜在改进的优先级。

**如何创建 Azure 支持请求？**

若要了解如何创建 Azure 支持请求，请参阅[如何创建 azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。

