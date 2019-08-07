---
title: Azure SQL 中的单一数据库和共用数据库的 VNet 终结点和规则 | Microsoft Docs
description: 将子网标记为虚拟网络服务终结点。 然后将终结点标记为适用于 Azure SQL 数据库 ACL 的虚拟网络规则。 然后，SQL 数据库就会接受来自子网上所有虚拟机和其他节点的通信。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 03/12/2019
ms.openlocfilehash: 9b28a8efcc09954d9046ad1dda3ba5f10f45bdfa
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840471"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>为数据库服务器使用虚拟网络服务终结点和规则

*虚拟网络规则*是一种防火墙安全功能，用于控制是否允许 Azure [SQL 数据库](sql-database-technical-overview.md)中你的单一数据库和弹性池的数据库服务器或 [SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)中你的数据库的数据库服务器接受从虚拟网络中的特定子网发送的通信。 本文说明了为何有时候最好选择虚拟网络规则功能来安全地启用到 Azure SQL 数据库和 SQL 数据仓库的通信。

> [!IMPORTANT]
> 本文适用于 Azure SQL 服务器，同时也适用于在 Azure SQL 服务器中创建的 SQL 数据库和 SQL 数据仓库数据库。 为简单起见，在提到 SQL 数据库和 SQL 数据仓库时，本文统称 SQL 数据库。 本文不适用于 Azure SQL 数据库中的**托管实例**部署，因为它没有与之关联的服务终结点。

若要创建虚拟网络规则, 必须先有一个[虚拟网络服务终结点, 该终结点][vm-virtual-network-service-endpoints-overview-649d]才能引用该规则。

## <a name="how-to-create-a-virtual-network-rule"></a>如何创建虚拟网络规则

如果只创建虚拟网络规则，则可跳到[本文后面](#anchor-how-to-by-using-firewall-portal-59j)的步骤和说明。

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>术语和说明

**虚拟网络：** 可以让虚拟网络与 Azure 订阅相关联。

**子网：** 虚拟网络包含**子网**。 你所拥有的任何 Azure 虚拟机 (VM) 都会分配到子网。 一个子网可能包含多个 VM 或其他计算节点。 虚拟网络之外的计算节点不能访问虚拟网络，除非已将安全性配置为允许这样的访问。

**虚拟网络服务终结点：** [虚拟网络服务终结点][vm-virtual-network-service-endpoints-overview-649d]是一个子网，其属性值包括一个或多个正式的 Azure 服务类型名称。 本文介绍 **Microsoft.Sql** 的类型名称，即名为“SQL 数据库”的 Azure 服务。

**虚拟网络规则：** 适用于 SQL 数据库服务器的虚拟网络规则是一个子网，列在 SQL 数据库服务器的访问控制列表 (ACL) 中。 子网必须包含“Microsoft.Sql”类型名称才会将其列在 SQL 数据库的 ACL 中。

虚拟网络规则要求 SQL 数据库服务器接受来自子网上所有节点的通信。

<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>虚拟网络规则的优势

在你采取措施之前，子网上的 VM 不能与 SQL 数据库通信。 建立通信的一项操作是创建虚拟网络规则。 若要弄清楚为何选择 VNet 规则方法，必须进行一个比较和对比式的讨论，其中涉及到防火墙提供的竞争性安全选项。

### <a name="a-allow-access-to-azure-services"></a>A. 允许访问 Azure 服务

防火墙窗格有一个标记为“允许访问 Azure 服务”的“启用/禁用”按钮。 “启用”设置允许来自所有 Azure IP 地址和所有 Azure 子网的通信。 这些 Azure IP 或子网可能不是你所拥有的。 此“启用”设置可能超出你在开放方面对 SQL 数据库的需要。 虚拟网络规则功能提供精细得多的控制。

### <a name="b-ip-rules"></a>B. IP 规则

可以通过 SQL 数据库防火墙规则指定 IP 地址范围，处于该范围内的通信允许进入 SQL 数据库。 此方法适用于 Azure 专用网络外部的稳定 IP 地址。 但是，Azure 专用网络内部的许多节点都配置了动态 IP 地址。 某些情况下（例如重启 VM 时），动态 IP 地址可能会变化。 处于生产环境中时，在防火墙规则中指定一个动态 IP 地址并不明智。

可以通过获取 VM 的静态 IP 地址，对 IP 选项进行“补救”。 有关详细信息, 请参阅[使用 Azure 门户为虚拟机配置专用 IP 地址][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]。

但是，静态 IP 方法可能会变得难以管理，在规模大时操作成本高。 虚拟网络规则更易于制定和管理。

> [!NOTE]
> 目前，子网上没有 SQL 数据库。 如果 Azure SQL 数据库服务器是虚拟网络子网上的一个节点，则虚拟网络中的所有节点都可以与 SQL 数据库通信。 在这种情况下，VM 可以与 SQL 数据库通信，不需任何虚拟网络规则或 IP 规则。

但截至 2017 年 9 月，Azure SQL 数据库服务仍然无法分配给子网。

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>虚拟网络规则详细信息

此部分介绍虚拟网络规则的多项详细信息。

### <a name="only-one-geographic-region"></a>只有一个地理区域

一个虚拟网络服务终结点只能应用于一个 Azure 区域。 终结点不允许其他区域接受来自该子网的通信。

任何虚拟网络规则都只能应用于基础终结点应用到的区域。

### <a name="server-level-not-database-level"></a>服务器级而非数据库级

每个虚拟网络规则都适用于整个 Azure SQL 数据库服务器，而不仅仅是该服务器上某个特定的数据库。 换句话说，虚拟网络规则适用于服务器级而非数据库级。

- 与之不同的是，IP 规则适用于这其中的任一级别。

### <a name="security-administration-roles"></a>安全管理角色

在管理虚拟网络服务终结点时，安全角色是分开的。 下述每个角色都需要进行操作：

- **网络管理员：** &nbsp; 启用终结点。
- **数据库管理员：** &nbsp;更新访问控制列表 (ACL)，将给定的子网添加到 SQL 数据库服务器。

RBAC 备用：

网络管理员和数据库管理员角色的权限超出虚拟网络规则的管理需要， 只有部分权限是必需的。

你可以选择在 Azure 中使用[基于角色的访问控制 (RBAC)][rbac-what-is-813s]来创建只具有必要的功能子集的单个自定义角色。 在涉及到网络管理员或数据库管理员时，可以使用自定义角色来代替。与向两个主要的管理员角色添加用户相比，向自定义角色添加用户的安全风险较低。

> [!NOTE]
> 在某些情况下，Azure SQL 数据库和 VNet-子网位于不同的订阅中。 在这些情况下，必须确保以下配置：
> - 两个订阅都必须属于同一 Azure Active Directory 租户。
> - 用户具有启动操作所需的权限，例如启用服务终结点，以及向给定服务器添加 VNet-子网。
> - 两个订阅都必须注册 Microsoft.Sql 提供程序。

## <a name="limitations"></a>限制

对于 Azure SQL 数据库，虚拟网络规则功能具有以下限制：

- Web 应用可以映射到 VNet/子网中的专用 IP。 即使已从给定 VNet/子网启用服务终结点，从 Web 应用到服务器的连接也将具有 Azure 公共 IP 源，而不是 VNet/子网源。 若要启用从 Web 应用到具有 VNet 防火墙规则的服务器的连接，必须在该服务器上**允许 Azure 服务访问服务器**。

- 在 SQL 数据库的防火墙中，每个虚拟网络规则都引用一个子网。 引用的所有这些子网都必须托管在同一个托管 SQL 数据库的地理区域内。

- 对于任何给定的虚拟网络，每个 Azure SQL 数据库服务器最多可拥有 128 个 ACL 条目。

- 虚拟网络规则仅适用于 Azure 资源管理器虚拟网络;而非[经典部署模型][arm-deployment-model-568f]网络。

- 如果启用 Azure SQL 数据库的虚拟网络服务终结点，会同时启用 MySQL 和 PostgreSQL Azure 服务的终结点。 但是，启用终结点后，尝试从终结点连接到 MySQL 或 PostgreSQL 实例可能会失败。
  - 根本原因是 MySQL 和 PostgreSQL 可能没有配置虚拟网络规则。 必须为 Azure Database for MySQL 和 Azure Database for PostgreSQL 配置虚拟网络规则，连接才会成功。

- 在防火墙上，IP 地址范围适用于以下网络项，但虚拟网络规则并不适用：
  - [站点到站点 (S2S) 虚拟专用网 (VPN)][vpn-gateway-indexmd-608y]
  - 通过[ExpressRoute][expressroute-indexmd-744v]本地

### <a name="considerations-when-using-service-endpoints"></a>使用服务终结点的注意事项

在使用 Azure SQL 数据库的服务终结点时，请查看以下注意事项：

- **需要到 Azure SQL 数据库公共 IP 的出站连接**：必须为 Azure SQL 数据库 IP 启用网络安全组 (NSG) 才能进行连接。 可以使用 Azure SQL 数据库的 NSG [服务标记](../virtual-network/security-overview.md#service-tags)执行此操作。

### <a name="expressroute"></a>ExpressRoute

如果是在本地使用 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，则在进行公共对等互连或 Microsoft 对等互连时，需标识所用的 NAT IP 地址。 进行公共对等互连时，每条 ExpressRoute 线路默认情况下会使用两个 NAT IP 地址。当流量进入 Microsoft Azure 网络主干时，会向 Azure 服务流量应用这些地址。 进行 Microsoft 对等互连时，所用 NAT IP 地址由客户或服务提供商提供。 若要允许访问服务资源，必须在资源 IP 防火墙设置中允许这些公共 IP 地址。 若要查找公共对等互连 ExpressRoute 线路 IP 地址，请通过 Azure 门户[开具 ExpressRoute 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 详细了解[适用于 ExpressRoute 公共对等互连和 Microsoft 对等互连的 NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)。
  
若要允许从线路到 Azure SQL 数据库的通信，则必须为 NAT 的公共 IP 地址创建 IP 网络规则。

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-azure-services-to-access-server"></a>删除“允许 Azure 服务访问服务器”的影响

许多用户希望从其 Azure SQL Server 中删除“允许 Azure 服务访问服务器”，将其替换为 VNet 防火墙规则。
但是，删除该项会影响以下功能：

### <a name="import-export-service"></a>导入/导出服务

Azure SQL 数据库导入/导出服务在 Azure 中的 VM 上运行。 这些 VM 不在 VNet 中，因此在连接到数据库时会获取 Azure IP。 删除“允许 Azure 服务访问服务器”后，这些 VM 将无法访问数据库。
可以解决此问题。 通过 DACFx API 直接在代码中运行 BACPAC 导入或导出。 确保该项部署在 VM 中且已为其所在的 VNet-子网设置防火墙规则。

### <a name="sql-database-query-editor"></a>SQL 数据库查询编辑器

Azure SQL 数据库查询编辑器部署在 Azure 中的 VM 上。 这些 VM 不在 VNet 中。 因此，这些 VM 在连接到数据库时会获取 Azure IP。 删除“允许 Azure 服务访问服务器”后，这些 VM 将无法访问数据库。

### <a name="table-auditing"></a>表审核

目前有两种方法可以在 SQL 数据库上启用审核。 在 Azure SQL Server 上启用服务终结点后，表审核会失败。 在这里，解决办法是转为 Blob 审核。

### <a name="impact-on-data-sync"></a>对数据同步的影响

Azure SQL 数据库具有数据同步功能，可使用 Azure IP 连接到数据库。 使用服务终结点时，很可能会禁用对 SQL 数据库服务器的“允许 Azure 服务访问服务器”访问权限。 这将中断数据同步功能。

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>将 VNet 服务终结点与 Azure 存储配合使用的影响

Azure 存储已实现相同的功能，允许限制到 Azure 存储帐户的连接。 如果选择将此功能与某个 Azure 存储帐户配合使用，而该帐户正由 Azure SQL Server 使用，则可能会出现问题。 接下来会列出受此影响的 Azure SQL 数据库和 Azure SQL 数据仓库功能并对其进行讨论。

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL 数据仓库 PolyBase

PolyBase 通常用于将数据从 Azure 存储帐户加载到 Azure SQL 数据仓库中。 如果正从 Azure 存储帐户加载数据，而该帐户只允许一组 VNet-子网的访问，则会断开从 PolyBase 到该帐户的连接。 对于连接到 Azure 存储（已通过安全方式连接到 VNet）的 Azure SQL 数据仓库，若要启用 PolyBase 导入和导出方案，请执行如下所示的步骤：

#### <a name="prerequisites"></a>系统必备

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

1.  按照此[指南](https://docs.microsoft.com/powershell/azure/install-az-ps)安装 Azure PowerShell。
2.  如果有常规用途 v1 或 Blob 存储帐户，则必须先按照此[指南](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)将该帐户升级到常规用途 v2 帐户。
3.  必须在 Azure 存储帐户的“防火墙和虚拟网络”设置菜单下启用“允许受信任的 Microsoft 服务访问此存储帐户”。 有关详细信息，请参阅此[指南](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)。
 
#### <a name="steps"></a>步骤
1. 在 PowerShell 中，向 Azure Active Directory (AAD) 注册 SQL 数据库服务器：

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```
    
   1. 按照此[指南](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)创建**常规用途 v2 存储帐户**。

   > [!NOTE]
   > - 如果有常规用途 v1 或 Blob 存储帐户，则必须先按照此[指南](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)将该帐户**升级到 v2** 帐户。
   > - 若要了解 Azure Data Lake Storage Gen2 的已知问题，请参阅此[指南](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues)。
    
1. 在存储帐户下导航到“访问控制(标识和访问管理)”，然后单击“添加角色分配”。 向 SQL 数据库服务器分配“存储 Blob 数据参与者”RBAC 角色。

   > [!NOTE] 
   > 只有具有“所有者”特权的成员能够执行此步骤。 若要了解 Azure 资源的各种内置角色，请参阅此[指南](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。
  
1. **通过 Polybase 连接到 Azure 存储帐户：**

   1. 创建数据库 **[主密钥](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** （如果此前尚未创建）：
       ```SQL
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```
    
   1. 使用 **IDENTITY = '托管服务标识'** 创建数据库范围的凭据：

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - 使用 Azure 存储访问密钥时，不需指定 SECRET，因为此机制在后台使用[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
       > - 使用 Azure 存储帐户以安全方式连接到 VNet 时，IDENTITY 名称应该为 **'托管服务标识'** ，以便通过 PolyBase 进行连接。    
    
   1. 使用 abfss:// 方案创建外部数据源，以便通过 PolyBase 连接到常规用途 v2 存储帐户：

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```
       > [!NOTE] 
       > - 如果已经有外部表关联到常规用途 v1 或 Blob 存储帐户，则应先删除这些外部表，然后删除相应的外部数据源。 然后，使用 abfss:// 方案按照上面的步骤创建连接到常规用途 v2 存储帐户的外部数据源，并使用该新建的外部数据源重新创建所有外部表。 可以通过[生成和发布脚本向导](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard)为所有外部表生成 create-script，以方便使用。
       > - 有关 abfss:// 方案的详细信息，请参阅此[指南](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri)。
       > - 有关 CREATE EXTERNAL DATA SOURCE 的详细信息，请参阅此[指南](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)。
        
   1. 使用[外部表](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)进行正常查询。

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL 数据库 Blob 审核

Blob 审核将审核日志推送到你自己的存储帐户。 如果此存储帐户使用 VNet 服务终结点功能，则会断开从 Azure SQL 数据库到存储帐户的连接。

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>在未打开 VNet 服务终结点的情况下，将 VNet 防火墙规则添加到服务器

早在增强此功能以前，就要求你先打开 VNet 服务终结点，然后才能在防火墙中实施实时 VNet 规则。 这些终结点已将给定的 VNet 子网关联到 Azure SQL 数据库。 但现在从 2018 年 1 月开始，可以通过设置 **IgnoreMissingVNetServiceEndpoint** 标志来避开此要求。

仅设置防火墙规则不会帮助保护服务器。 还必须打开 VNet 服务终结点才能使安全性生效。 打开服务终结点时，VNet 子网会遇到停机，直到它完成从“关”到“开”的转换。 这在大型 VNet 的上下文中尤其如此。 可以使用 **IgnoreMissingVNetServiceEndpoint** 标志，减少或消除转换期间的停机时间。

可以使用 PowerShell 设置 **IgnoreMissingVNetServiceEndpoint** 标志。 有关详细信息, 请参阅[PowerShell 为 AZURE SQL 数据库创建虚拟网络服务终结点和规则][sql-db-vnet-service-endpoint-rule-powershell-md-52d]。

## <a name="errors-40914-and-40615"></a>错误 40914 和 40615

连接错误 40914 与虚拟网络规则（如 Azure 门户中的“防火墙”窗格所指定）相关。 错误 40615 基本相似，不同之处在于与“防火墙”上的“IP 地址规则”相关。

### <a name="error-40914"></a>错误 40914

消息文本：无法打开登录时请求的服务器‘[服务器-名称]’。 不允许客户端访问服务器。

错误说明：客户端位于包含虚拟网络服务器终结点的子网中。 不过，Azure SQL 数据库服务器没有授权子网与 SQL 数据库进行通信的虚拟网络规则。

错误解决方法：在 Azure 门户的“防火墙”窗格中，使用虚拟网络规则控件为子网[添加虚拟网络规则](#anchor-how-to-by-using-firewall-portal-59j)。

### <a name="error-40615"></a>错误 40615

消息文本：无法打开此登录请求的服务器“{0}”。 不允许 IP 地址为“{1}”的客户端访问此服务器。

错误说明：客户端尝试从未经授权连接到 Azure SQL 数据库服务器的 IP 地址进行连接。 服务器防火墙没有 IP 地址规则允许客户端从给定 IP 地址与 SQL 数据库进行通信。

错误解决方法：输入客户端 IP 地址作为 IP 规则。 为此，可以使用 Azure 门户中的“防火墙”窗格。

[此处][sql-database-develop-error-messages-419g]介绍了几个 SQL 数据库错误消息的列表。

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>门户可以创建虚拟网络规则

本部分说明如何使用[Azure 门户][http-azure-portal-link-ref-477t]在 Azure SQL 数据库中创建*虚拟网络规则*。 此规则要求 SQL 数据库接受来自特定子网的通信，该子网已被标记为“虚拟网络服务终结点”。

> [!NOTE]
> 若要向 Azure SQL 数据库服务器的 VNet 防火墙规则添加服务终结点，请确保为子网启用服务终结点。
>
> 如果没有为子网启用服务终结点，门户会要求你启用。 在添加规则的边栏选项卡上单击“启用”按钮。

## <a name="powershell-alternative"></a>PowerShell 备用

PowerShell 脚本也可创建虚拟网络规则。 重要的 cmdletNew-AzSqlServerVirtualNetworkRule。 如果感兴趣, 请参阅[PowerShell 为 AZURE SQL 数据库创建虚拟网络服务终结点和规则][sql-db-vnet-service-endpoint-rule-powershell-md-52d]。

## <a name="rest-api-alternative"></a>REST API 替代项

在内部，用于 SQL VNet 操作的 PowerShell cmdlet 调用 REST API。 可以直接调用 REST API。

- [虚拟网络规则：运算符][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>系统必备

必须有一个子网已经使用特定的虚拟网络服务终结点类型名称进行标记，且该名称必须与 Azure SQL 数据库相关。

- 相关的终结点类型名称为Microsoft.Sql。
- 如果你的子网可能未使用类型名称进行标记, 请参阅[验证子网是否是终结点][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]。

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Azure 门户步骤

1. 登录到 [Azure 门户][http-azure-portal-link-ref-477t]。

2. 然后在门户中导航到“SQL 服务”&gt;“防火墙/虚拟网络”。

3. 将“允许访问 Azure 服务”控件设置为“禁用”。

    > [!IMPORTANT]
    > 如果将此控件设置为“启用”，则 Azure SQL 数据库服务器接受来自任何子网的通信。 从安全角度来看，将此控件设置为“启用”可能会导致过度访问。 针对 SQL 数据库结合使用 Microsoft Azure 虚拟网络服务终结点功能和虚拟网络规则功能，可以降低安全风险。

4. 在“虚拟网络”部分单击“+ 添加现有项”控件。

    ![单击“添加现有项(子网终结点，充当 SQL 规则)”。][image-portal-firewall-vnet-add-existing-10-png]

5. 在新的“创建/更新”窗格的控件中填充 Azure 资源的名称。

    > [!TIP]
    > 必须包括子网的正确地址前缀。 可以在门户中找到该值。
    > 导航到“所有资源”&gt;“所有类型”&gt;“虚拟网络”。 筛选器会显示虚拟网络。 单击虚拟网络，然后单击“子网”。 “地址范围”列包含所需的地址前缀。

    ![填充新规则的字段。][image-portal-firewall-create-update-vnet-rule-20-png]

6. 单击窗格底部的“确定”按钮。

7. 查看防火墙窗格中生成的虚拟网络规则。

    ![查看防火墙窗格中的新规则。][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> 以下状态适用于这些规则：
> - **就绪：** 表示所启动的操作已成功。
> - **失败：** 表示所启动的操作已失败。
> - **已删除：** 仅适用于删除操作，表示规则已删除，不再适用。
> - **进行中：** 表示操作正在进行。 操作处于这种状态时，会应用旧规则。

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>相关文章

- [Azure 虚拟网络服务终结点][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL 数据库服务器级和数据库级防火墙规则][sql-db-firewall-rules-config-715d]

Azure SQL 数据库的虚拟网络规则功能已在 2017 年 9 月末推出。

## <a name="next-steps"></a>后续步骤

- [使用 PowerShell 创建虚拟网络服务终结点, 然后创建 Azure SQL 数据库的虚拟网络规则。][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [虚拟网络规则：REST][rest-api-virtual-network-rules-operations-862r] api 操作

<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.yml

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
