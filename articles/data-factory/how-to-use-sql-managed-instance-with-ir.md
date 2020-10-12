---
title: 在 Azure 数据工厂中结合使用 Azure SQL 托管实例和 Azure-SQL Server Integration Services (SSIS)
description: 了解如何在 Azure 数据工厂中结合使用 Azure SQL 托管实例和 SQL Server Integration Services (SSIS)。
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: 2bdfdd31e2cc9bc964abc040d0631c4760fca283
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984881"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>在 Azure 数据工厂中结合使用 Azure SQL 托管实例和 SQL Server Integration Services (SSIS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

现在，可将 SQL Server Integration Services (SSIS) 项目、包和工作负荷移到 Azure 云。 在 Azure SQL 数据库或 SQL 托管实例上使用 SQL Server Management Studio (SSMS) 等熟悉工具来部署、运行和管理 SSIS 项目和包。 本文重点介绍了在结合使用 Azure SQL 托管实例和 Azure-SSIS Integration Runtime (IR) 时的以下特定方面：

- [为 Azure-SSIS IR 预配由 Azure SQL 托管实例托管的 SSIS 目录 (SSISDB)](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [通过 Azure SQL 托管实例代理作业执行 SSIS 包](how-to-invoke-ssis-package-managed-instance-agent.md)
- [通过 Azure SQL 托管实例代理作业清理 SSISDB 日志](#clean-up-ssisdb-logs)
- [使用 Azure SQL 托管实例进行 Azure-SSIS IR 故障转移](configure-bcdr-azure-ssis-integration-runtime.md#azure-ssis-ir-failover-with-a-sql-managed-instance)
- [使用 Azure SQL 托管实例作为数据库工作负荷目标，将本地 SSIS 工作负荷迁移到 ADF 中的 SSIS](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>为 Azure-SSIS IR 预配由 Azure SQL 托管实例托管的 SSISDB

### <a name="prerequisites"></a>先决条件

1. 选择 Azure Active Directory 身份验证时，[在 Azure SQL 托管实例上启用 Azure Active Directory (Azure AD)](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)。

1. 选择如何通过专用终结点或通过公共终结点连接 SQL 托管实例：

    - 通过专用终结点（首选）

        1. 选择 Azure-SSIS IR 要联接到的虚拟网络：
            - 在与托管实例相同的虚拟网络中，具有 **不同的子网**。
            - 在不同于托管实例的虚拟网络中，通过虚拟网络对等互连 (此限制为同一区域，因为存在全局 VNet 对等互连约束) 或从虚拟网络到虚拟网络的连接。

            有关 SQL 托管实例连接的详细信息，请参阅 [将应用程序连接到 AZURE SQL 托管实例](https://review.docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connect-app)。

        1. [配置虚拟网络](#configure-virtual-network)。

    - 通过公共终结点

        Azure SQL 托管实例可通过[公共终结点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)进行连接。 入站和出站要求需要满足才能允许 SQL 托管实例和 Azure-SSIS IR 之间的流量：

        - 当 Azure-SSIS IR 不在虚拟网络中时（首选）

            SQL 托管实例的入站要求，以允许来自 Azure-SSIS IR 的入站流量。

            | 传输协议 | 源 | 源端口范围 | 目标 | 目标端口范围 |
            |---|---|---|---|---|
            |TCP|Azure 云服务标记|*|VirtualNetwork|3342|

            有关详细信息，请参阅[允许网络安全组上的公共终结点流量](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure#allow-public-endpoint-traffic-on-the-network-security-group)。

        - 当 Azure-SSIS IR 在虚拟网络中时

            有一种特殊情况，即当 SQL 托管实例位于 Azure-SSIS IR 不支持的区域中时，由于全球 VNet 对等互连限制，Azure-SSIS IR 位于没有 VNet 对等互连的虚拟网络中。 在这种情况下，虚拟网络中的 Azure-SSIS IR 通过公共终结点连接 SQL 托管实例 。 使用下面的网络安全组 (NSG) 规则以允许 SQL 托管实例和 Azure-SSIS IR 之间的流量：

            1. SQL 托管实例的入站要求，以允许来自 Azure-SSIS IR 的入站流量。

                | 传输协议 | 源 | 源端口范围 | 目标 |目标端口范围 |
                |---|---|---|---|---|
                |TCP|Azure-SSIS IR 的静态 IP 地址 <br> 有关详细信息，请参阅[为 Azure-SSIS IR 创建自己的公共 IP](join-azure-ssis-integration-runtime-virtual-network.md#publicIP)。|*|VirtualNetwork|3342|

             1. Azure-SSIS IR 的出站要求，以允许流向 SQL 托管实例的出站流量。

                | 传输协议 | 源 | 源端口范围 | 目标 |目标端口范围 |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[SQL 托管实例公共终结点 IP 地址](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-find-management-endpoint-ip-address)|3342|

### <a name="configure-virtual-network"></a>配置虚拟网络

1. 用户权限。 创建 Azure-SSIS IR 的用户必须至少使用以下选项之一在 Azure 数据工厂资源上进行[角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope)：

    - 使用内置的“网络参与者”角色。 此角色具有 _Microsoft.Network/\*_ 权限，具有比所需作用域更大的作用域。
    - 创建仅包括必需的 _Microsoft.Network/virtualNetworks/\*/join/action_ 权限的一个自定义角色。 如果还需要在将 Azure-SSIS IR 联接到 Azure 资源管理器虚拟网络的同时为它创建自己的公共 IP 地址，还请在角色中添加 Microsoft.Network/publicIPAddresses/*/join/action 权限。

1. 虚拟网络。

    1. 请确保虚拟网络的资源组可以创建和删除特定 Azure 网络资源。

        Azure-SSIS IR 需要在与虚拟网络相同的资源组下创建某些网络资源。 这些资源包括：
        - Azure 负载均衡器，名为 \<Guid>-azurebatch-cloudserviceloadbalancer
        - 网络安全组，名为 *\<Guid>-azurebatch-cloudservicenetworksecuritygroup
        - Azure 公共 IP 地址，名为 -azurebatch-cloudservicepublicip

        这些资源在 Azure-SSIS IR 启动时创建， 并在 Azure-SSIS IR 停止时删除。 为了避免阻止 Azure-SSIS IR 停止，请不要在其他资源中重用这些网络资源。

    1. 确保虚拟网络所属的资源组/订阅中没有任何[资源锁](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)。 如果你配置了只读锁/删除锁，则无法启动和停止 Azure-SSIS IR，或者它会停止响应。

    1. 确保没有 Azure 策略阻止在虚拟网络所属的资源组/订阅下创建以下资源：
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

    1. 允许网络安全组 (NSG) 上流量的规则，以允许 SQL 托管实例和 Azure-SSIS IR 之间的流量，以及 Azure-SSIS IR 所需的流量。
        1. SQL 托管实例的入站要求，以允许来自 Azure-SSIS IR 的入站流量。

            | 传输协议 | 源 | 源端口范围 | 目标 | 目标端口范围 | 注释 |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433、11000-11999|如果 SQL 数据库服务器连接策略设置为“代理”（而不是“重定向”），那么只需要端口 1433。|

        1. Azure-SSIS IR 的出站要求，以允许流向 SQL 托管实例的出站流量，以及 Azure-SSIS IR 所需的其他流量。

        | 传输协议 | 源 | 源端口范围 | 目标 | 目标端口范围 | 注释 |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433、11000-11999 |允许流向 SQL 托管实例的出站流量。 如果连接策略设置为“代理”（而不是“重定向”），那么只需要端口 1433。 |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | 虚拟网络中的 Azure-SSIS IR 节点使用此端口来访问 Azure 服务，如 Azure 存储和 Azure 事件中心。 |
        | TCP | VirtualNetwork | * | Internet | 80 | （可选）虚拟网络中的 Azure-SSIS IR 节点使用此端口从 Internet 下载证书吊销列表。 如果阻止此流量，可能会在启动 IR 时遇到性能降级，并且无法通过检查证书吊销列表来了解证书使用情况。 若要进一步将目标范围缩小到特定 FQDN，请参阅[使用 Azure ExpressRoute 或用户定义的路由 (UDR)](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network#route)。|
        | TCP | VirtualNetwork | * | 存储 | 445 | （可选）只有当你要执行存储在 Azure 文件存储中的 SSIS 包时，才需要此规则。 |
        |||||||

        1. Azure-SSIS IR 的入站要求，以允许 Azure-SSIS IR 所需的流量。

        | 传输协议 | 源 | 源端口范围 | 目标 | 目标端口范围 | 注释 |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876、29877（如果将 IR 加入资源管理器虚拟网络） <br/><br/>10100、20100、30100（如果将 IR 加入经典虚拟网络）| 数据工厂服务使用这些端口来与虚拟网络中 Azure-SSIS IR 的节点通信。 <br/><br/> 无论是否创建子网级 NSG，数据工厂都始终会在附加到托管 Azure-SSIS IR 的虚拟机的网络接口卡 (NIC) 级别配置 NSG。 此 NIC 级别的 NSG 仅允许来自指定端口上的数据工厂 IP 地址的入站流量。 即使你在子网级别向 Internet 流量打开这些端口，来自非数据工厂 IP 地址的流量也会在 NIC 级别被阻止。 |
        | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | （可选）只有当 Microsoft 支持者要求客户打开以进行高级故障排除，并且可以在故障排除后立即关闭时，才需要此规则。 CorpNetSaw 服务标记只允许 Microsoft 企业网络中的安全访问工作站使用远程桌面。 无法在门户中选择此服务标记，只能通过 Azure PowerShell 或 Azure CLI 选择。 <br/><br/> 在 NIC 级别 NSG 处，端口 3389 是默认打开的，允许在子网级别 NSG 处控制端口 3389；但在此期间，Azure-SSIS IR 在每个 IR 节点上的 Windows 防火墙规则处默认禁止端口 3389 出站以实现保护。 |
        |||||||

    1. 有关详细信息，请参阅[虚拟网络配置](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)：
        - 如果你为 Azure-SSIS IR 创建自己的公共 IP 地址
        - 如果你使用自己的域名系统 (DNS) 服务器
        - 如果你使用 Azure ExpressRoute 或用户定义的路由 (UDR)
        - 如果你使用自定义的 Azure-SSIS IR

### <a name="provision-azure-ssis-integration-runtime"></a>预配 Azure-SSIS Integration Runtime

1. 选择 SQL 托管实例专用终结点或公共终结点。

    在 Azure 门户/ADF 应用中 [设置 Azure-SSIS IR](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) 时，请在 "sql 设置" 页上，在创建 SSIS 目录 (SSISDB) 时使用 sql 托管实例 **专用终结点** 或 **公共终结点** 。

    公共终结点主机名的格式为 <mi_name>.public.<dns_zone>.database.windows.net，用于连接的端口为 3342。  

    ![屏幕截图显示了集成运行时安装，其中选择了 "创建 S" 目录和输入的目录数据库服务器终结点。](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. 选择在应用时启用 Azure AD 身份验证。

    ![catalog-public-endpoint](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    若要详细了解如何启用 Azure AD 身份验证，请参阅[在 Azure SQL 托管实例上启用 Azure AD](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)。

1. 应用时，将 Azure-SSIS IR 联接到虚拟网络。

    在“高级设置”页上，选择要联接到的虚拟网络和子网。
    
    如果与 SQL 托管实例位于相同的虚拟网络中，请选择与 SQL 托管实例位于不同的子网中。 

    若要详细了解如何将 Azure-SSIS IR 联接到虚拟网络，请参阅[将 Azure-SSIS Integration Runtime 联接到虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)。

    ![屏幕截图显示集成运行时设置高级设置，你可以在其中选择要加入的运行时的虚拟网络。](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

有关如何创建 Azure-SSIS IR 的详细信息，请参阅[在 Azure 数据工厂中创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime)。

## <a name="clean-up-ssisdb-logs"></a>清理 SSISDB 日志

SSISDB 日志保留策略由 [catalog.catalog_properties](https://docs.microsoft.com/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database?view=sql-server-ver15) 中的以下属性定义：

- OPERATION_CLEANUP_ENABLED

    如果值为 TRUE，则会从目录中删除超过 RETENTION_WINDOW（天）的操作详细信息和操作消息。 如果值为 FALSE，则会在目录中存储所有操作详细信息和操作消息。 注意：清理操作是由 SQL Server 作业执行。

- RETENTION_WINDOW

    操作详细信息和操作消息存储在目录中的天数。 如果值为 -1，保留时段是无限的。 注意：如果无需清理，请将 OPERATION_CLEANUP_ENABLED 设置为 FALSE。

若要删除在管理员设置的保留时段之外的 SSISDB 日志，可以触发存储过程 `[internal].[cleanup_server_retention_window_exclusive]`。 还可以安排 SQL 托管实例代理作业执行，以触发此存储过程。

## <a name="next-steps"></a>后续步骤

- [通过 Azure SQL 托管实例代理作业执行 SSIS 包](how-to-invoke-ssis-package-managed-instance-agent.md)
- [设置业务连续性和灾难恢复 (BCDR)](configure-bcdr-azure-ssis-integration-runtime.md)
- [将本地 SSIS 工作负荷迁移到 ADF 中的 SSIS](scenario-ssis-migration-overview.md)
