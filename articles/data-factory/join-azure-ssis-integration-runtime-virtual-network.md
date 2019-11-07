---
title: 将 Azure-SSIS 集成运行时加入虚拟网络
description: 了解如何将 Azure SSIS 集成运行时加入 Azure 虚拟网络。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d36900a1ce05eaf022637a6ef6b866fe0d190b17
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672735"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>将 Azure-SSIS 集成运行时加入虚拟网络
在 Azure 数据工厂中使用 SQL Server Integration Services (SSIS) 时，对于以下情况，应将 Azure SSIS 集成运行时 (IR) 加入 Azure 虚拟网络： 

- 你希望在不配置或管理用作代理的自承载 IR 的情况下，从 Azure-SSIS IR 中运行的 SSIS 包连接到本地数据存储。 

- 你想要从 Azure-SSIS IR 中运行的 SSIS 包连接到虚拟网络服务终结点支持的 Azure 服务资源。

- 要在 Azure SQL 数据库中托管 SSIS 目录数据库 (SSISDB)，该数据库包含虚拟网络中的虚拟网络服务终结点或托管实例。 

使用数据工厂可将 Azure-SSIS IR 加入通过经典部署模型或 Azure 资源管理器部署模型创建的虚拟网络。 

> [!IMPORTANT]
> 经典虚拟网络即将弃用，请改用 Azure 资源管理器虚拟网络。  如果已使用经典虚拟网络，请尽快切换到 Azure 资源管理器虚拟网络。

## <a name="access-to-on-premises-data-stores"></a>访问本地数据存储
如果 SSIS 包访问本地数据存储，则你可以将 Azure-SSIS IR 加入已连接到本地网络的虚拟网络。 也可以配置或管理自承载 IR 作为 Azure-SSIS IR 的代理。 有关详细信息，请参阅[将自承载 IR 配置为 Azure-SSIS IR 的代理](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。 

将 Azure-SSIS IR 加入到虚拟网络时，请记住以下要点： 

- 如果没有任何虚拟网络连接到本地网络，请先创建 Azure-SSIS IR 要加入到的 [Azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md#create-a-virtual-network)。 然后，配置从该虚拟网络到本地网络的站点到站点 [VPN 网关连接](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)或 [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 连接。 

- 如果已有一个 Azure 资源管理器虚拟网络连接到 Azure-SSIS 集成运行时所在同一位置中的本地网络，可将 IR 加入该虚拟网络。 

- 如果已有一个经典虚拟网络连接到与 Azure-SSIS IR 所在位置不同的位置中的本地网络，可以创建 Azure-SSIS IR 要加入到的 [Azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md#create-a-virtual-network)。 然后，配置[经典到 Azure 资源管理器虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)连接。 
 
- 如果已有一个 Azure 资源管理器虚拟网络连接到与 Azure-SSIS IR 所在位置不同的位置中的本地网络，可以先创建 Azure-SSIS IR 要加入到的 [Azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md##create-a-virtual-network)。 然后，配置 Azure 资源管理器到 Azure 资源管理器虚拟网络连接。 

## <a name="access-to-azure-services"></a>访问 Azure 服务
如果 SSIS 包访问[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)支持的 Azure 服务资源，并且你希望在 Azure-SSIS IR 中保护这些资源，则可以将 Azure-SSIS IR 加入到配置了虚拟网络服务终结点的虚拟网络子网。 同时，请将虚拟网络规则添加到 Azure 服务资源，以允许来自同一子网的访问。

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>在 SQL 数据库中承载 SSIS 目录
如果将 SSIS 目录承载在具有虚拟网络服务终结点的 Azure SQL 数据库中，请确保将 Azure-SSIS IR 加入到同一虚拟网络和子网中。

若要将 Azure-SSIS IR 加入与托管实例相同的虚拟网络，请确保 Azure-SSIS IR 位于与托管实例不同的子网中。 若要将 Azure-SSIS IR 加入与托管实例不同的虚拟网络，我们建议建立虚拟网络对等互连（限于相同的区域）或者虚拟网络之间的连接。 有关详细信息，请参阅[将应用程序连接到 Azure SQL 数据库托管实例](../sql-database/sql-database-managed-instance-connect-app.md)。

在所有情况下，都只能通过 Azure 资源管理器部署模型部署虚拟网络。

以下部分提供了更多详细信息。 

## <a name="virtual-network-configuration"></a>虚拟网络配置

根据以下要求设置虚拟网络： 

-   确保 `Microsoft.Batch` 是承载 Azure-SSIS IR 的虚拟网络子网订阅下的已注册提供程序。 如果使用的是经典虚拟网络，请同时将 `MicrosoftAzureBatch` 加入到该虚拟网络的经典虚拟机参与者角色。 

-   请确保具有所需的权限。 有关详细信息，请参阅[设置权限](#perms)。

-   选择合适的子网，承载 Azure-SSIS IR。 有关详细信息，请参阅[选择子网](#subnet)。 

-   如果在虚拟网络中使用你自己的域名系统 (DNS) 服务器，请参阅[设置 DNS 服务器](#dns_server)。 

-   如果在子网中使用网络安全组 (NSG)，请参阅[设置 NSG](#nsg)。 

-   如果使用 Azure ExpressRoute 或用户定义的路由 (UDR)，请参阅[使用 Azure ExpressRoute 或 UDR](#route)。 

-   确保虚拟网络的资源组可以创建和删除特定的 Azure 网络资源。 有关详细信息，请参阅[设置资源组](#resource-group)。 

-   如果根据 [Azure-SSIS IR 的自定义设置](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)中所述自定义 Azure-SSIS IR，则 Azure-SSIS IR 节点将从预定义的范围 172.16.0.0 到 172.31.255.255 中获取专用 IP 地址。 因此，请确保虚拟网络或本地网络的专用 IP 地址范围不会与此范围冲突。

下图显示了 Azure-SSIS IR 所需的连接：

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a> 设置权限

创建 Azure-SSIS IR 的用户必须拥有以下权限：

- 如果要将 SSIS IR 加入 Azure 资源管理器虚拟网络，则有两种选择：

  - 使用内置的“网络参与者”角色。 此角色具有 _Microsoft.Network/\*_ 权限，具有比所需作用域更大的作用域。

  - 创建仅包括必需的 _Microsoft.Network/virtualNetworks/\*/join/action_ 权限的一个自定义角色。 

- 如果要将 SSIS IR 加入经典虚拟网络，我们建议使用内置的“经典虚拟机参与者”角色。 否则，你必须定义包含加入虚拟网络权限的自定义角色。

### <a name="subnet"></a>选择子网

选择子网时： 

-   不要选择 GatewaySubnet 来部署 Azure-SSIS IR。 GatewaySubnet 专用于虚拟网络网关。 

-   确保选择的子网具有足够的可用地址空间以供 Azure-SSIS IR 使用。 将可用 IP 地址数保持为 IR 节点数的至少两倍。 Azure 会保留每个子网中的某些 IP 地址。 不能使用这些地址。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   不要使用其他 Azure 服务（例如，SQL 数据库托管实例、应用服务等）以独占方式占用的子网。 

### <a name="dns_server"></a> 设置 DNS 服务器 
如果需要在由 Azure-SSIS IR 联接的虚拟网络中使用自己的 DNS 服务器，请确保它可以解析全局 Azure 主机名（例如，名为 `<your storage account>.blob.core.windows.net`的 Azure 存储 blob）。 

建议执行以下步骤： 

-   配置自定义 DNS，以将请求转发到 Azure DNS。 可以在你自己的 DNS 服务器上将未解析的 DNS 记录转发到 Azure 递归解析程序 (168.63.129.16) 的 IP 地址。 

-   将自定义 DNS 设置为虚拟网络的主要 DNS 服务器。 将 Azure DNS 设置为辅助 DNS 服务器。 将 Azure 递归解析程序 (168.63.129.16) 的 IP 地址注册为辅助 DNS 服务器，以防自己的 DNS 服务器不可用。 

有关详细信息，请参阅[使用自己的 DNS 服务器的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。 

### <a name="nsg"></a> 设置 NSG
如果需要为 Azure-SSIS IR 使用的子网实施 NSG，请允许入站和出站流量通过以下端口： 

| Direction | 传输协议 | 源 | 源端口范围 | 目标 | 目标端口范围 | 注释 |
|---|---|---|---|---|---|---|
| 入站 | TCP | BatchNodeManagement | * | VirtualNetwork | 29876、29877（如果将 IR 加入资源管理器虚拟网络） <br/><br/>10100、20100、30100（如果将 IR 加入经典虚拟网络）| 数据工厂服务使用这些端口来与虚拟网络中 Azure-SSIS IR 的节点通信。 <br/><br/> 无论是否创建子网级 NSG，数据工厂都始终会在附加到托管 Azure-SSIS IR 的虚拟机的网络接口卡 (NIC) 级别配置 NSG。 此 NIC 级别的 NSG 仅允许来自指定端口上的数据工厂 IP 地址的入站流量。 即使在子网级别为 Internet 流量打开这些端口，来自 IP 地址（非数据工厂 IP 地址）的流量也会在 NIC 级别被阻止。 |
| 出站 | TCP | VirtualNetwork | * | AzureCloud | 443 | 虚拟网络中 Azure-SSIS IR 的节点使用此端口来访问 Azure 服务，例如 Azure 存储和 Azure 事件中心。 |
| 出站 | TCP | VirtualNetwork | * | Internet | 80 | 虚拟网络中的 Azure-SSIS IR 节点使用此端口从 Internet 下载证书吊销列表。 |
| 出站 | TCP | VirtualNetwork | * | Sql | 1433、11000-11999 | 虚拟网络中 Azure-SSIS IR 的节点使用这些端口来访问 SQL 数据库服务器承载的 SSISDB。 如果 SQL 数据库服务器连接策略设置为“中介”而不是“重定向”，则只需使用端口 1433。 此出站安全规则不适用于虚拟网络中托管实例承载的 SSISDB。 |
||||||||

### <a name="route"></a> 使用 Azure ExpressRoute 或 UDR
将 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 线路连接到虚拟网络基础结构以将本地网络扩展到 Azure 时，常见的配置将使用强制隧道（将 BGP 路由从 0.0.0.0/0 播发到虚拟网络）。 此隧道将强制虚拟网络的出站 Internet 流量流向本地网络设备，以进行检查和日志记录。 
 
或者可以定义 [UDR](../virtual-network/virtual-networks-udr-overview.md)，强制承载 Azure-SSIS IR 的子网的出站 Internet 流量流向承载网络虚拟设备 (NVA)（用作防火墙或 Azure 防火墙）的另一个子网，以进行检查和日志记录。 

在这两种情况下，流量路由将会中断从相关 Azure 数据工厂服务（具体而言，是 Azure Batch 管理服务）到虚拟网络中 Azure-SSIS IR 的所需入站连接。 为了避免此问题，请在包含 Azure-SSIS IR 的子网上定义一个或多个 UDR。 

在 Azure ExpressRoute 方案中，可以在承载 Azure-SSIS IR 的子网上应用下一跃点类型为“Internet”的 0.0.0.0/0 路由。 或者，可以在 NVA 方案中将现有 0.0.0.0/0 路由的下一跃点类型从“虚拟设备”修改为“Internet”。

![添加路由](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

如果你担心无法检查来自该子网的出站 Internet 流量，可以定义特定的 UDR，以便仅路由 Azure Batch 管理服务与下一跃点类型为“Internet”的 Azure-SSIS IR 之间的流量。

例如，如果你的 Azure-SSIS IR 位于 `UK South`，你可以从 "[服务标记 ip 范围" 下载链接](https://www.microsoft.com/en-us/download/details.aspx?id=56519)或通过[服务标记发现 API](https://aka.ms/discoveryapi)获取服务 `BatchNodeManagement.UKSouth` 标记的 IP 范围列表。 然后，将以下 Udr 的相关 IP 范围路由和下一跃点类型应用为**Internet**。

![Azure Batch UDR 设置](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> 此方法会产生额外的维护成本。 定期检查 IP 范围，并在 UDR 中添加新的 IP 范围，以免中断 Azure-SSIS IR。 建议每月检查 IP 范围，因为当新 IP 出现在服务标记中时，该 IP 需要再等一个月才能生效。 

### <a name="resource-group"></a> 设置资源组
Azure-SSIS IR 需要在与虚拟网络相同的资源组下创建某些网络资源。 这些资源包括：
   -   Azure 负载均衡器，名为 *Guid>-azurebatch-cloudserviceloadbalancer\<* 。
   -   Azure 公共 IP 地址，名为 *Guid>-azurebatch-cloudservicepublicip\<* 。
   -   网络工作安全组，名为 *Guid>-azurebatch-cloudservicenetworksecuritygroup\<* 。 

IR 启动时，将创建这些资源。 IR 停止时，将删除这些资源。 为了避免 IR 停止受阻，请不要在其他资源中重用这些网络资源。 

确保虚拟网络所属的资源组或订阅中没有任何资源锁。 如果配置只读锁或删除锁，则启动和停止 IR 可能会失败，或者 IR 可能会停止响应。 

请确保没有任何 Azure Policy 会阻止在虚拟网络所属的资源组或订阅下创建以下资源： 
   -   Microsoft.Network/LoadBalancers 
   -   Microsoft.Network/NetworkSecurityGroups 
   -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a> FAQ

- 如何保护用于入站连接的 Azure-SSIS IR 上公开的公共 IP 地址？ 是否可以删除公共 IP 地址？
 
    目前，当 Azure-SSIS IR 加入虚拟网络时，会自动创建一个公共 IP 地址。 我们确实有一个 NIC 级别的 NSG，它只允许 Azure Batch 管理服务入站连接到 Azure-SSIS IR。 你也可以指定子网级别的 NSG 进行入站保护。

    如果你不希望公开公共 IP 地址，请考虑将[自承载 IR 配置为 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)而不是虚拟网络的代理（如果这适用于你的方案）。
 
- 是否可将 Azure-SSIS IR 的静态 IP 地址添加到数据源的防火墙允许列表？
 
    - 如果数据源位于本地，在将虚拟网络连接到本地网络并将 Azure-SSIS IR 加入虚拟网络子网后，可将该子网的 IP 范围添加到允许列表。
    - 如果数据源是虚拟网络服务终结点支持的 Azure 服务，则你可以在虚拟网络中配置一个虚拟网络服务点，并将 Azure-SSIS IR 加入该虚拟网络子网。 然后，可以使用 Azure 服务的虚拟网络规则而不是 IP 范围来允许访问。
    - 如果数据源是不同类型的云数据源，则你可以使用 UDR 通过静态公共 IP 地址将出站流量从 Azure-SSIS IR 路由到 NVA 或 Azure 防火墙。 可将 NVA 或 Azure 防火墙的公共 IP 地址添加到允许列表。
    - 如果以前的答案无法满足你的需求，请考虑通过将[自承载 IR 配置为 Azure-SSIS IR 的代理](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)来提供数据源访问。 然后，可以将承载自承载 IR 的计算机的 IP 地址添加到允许列表中，而不是将 Azure-SSIS IR 加入虚拟网络。

## <a name="azure-portal-data-factory-ui"></a>Azure 门户（数据工厂 UI）
本部分介绍如何使用 Azure 门户和数据工厂 UI 如何将现有的 Azure SSIS IR 加入虚拟网络（经典或 Azure 资源管理器）。 

在将 Azure-SSIS IR 加入虚拟网络之前，需要正确配置虚拟网络。 请遵循适用于你的虚拟网络类型（经典或 Azure 资源管理器）的部分中的步骤操作。 然后，遵循第三部分中的步骤将 Azure SSIS IR 加入虚拟网络。 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>配置 Azure 资源管理器虚拟网络

使用门户配置一个 Azure 资源管理器虚拟网络，然后尝试将 Azure-SSIS IR 加入其中。

1. 启动 Microsoft Edge 或 Google Chrome。 目前只有这些 Web 浏览器支持数据工厂 UI。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 

1. 选择“更多服务”。 筛选并选择“虚拟网络”。 

1. 在列表中筛选并选择自己的虚拟网络。 

1. 在“虚拟网络”页中选择“属性”。 

1. 选择“资源 ID”对应的复制按钮，将虚拟网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。 

1. 在左侧菜单中选择“子网”。 确保可用地址数大于 Azure-SSIS IR 中的节点数。 

1. 验证是否已将 Azure Batch 提供程序注册到包含虚拟网络的 Azure 订阅中。 或者注册 Azure Batch 提供程序。 如果订阅中已包含 Azure Batch 帐户，则已经为 Azure Batch 注册了订阅。 （如果在数据工厂门户中创建 Azure-SSIS IR，将自动注册 Azure Batch 提供程序。） 

   a. 在 Azure 门户的左侧菜单中选择“订阅”。 

   b. 选择订阅。 

   c. 在左侧选择“资源提供程序”，确认 **Microsoft.Batch** 是注册的提供程序。 

   ![确认“已注册”状态](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果列表中未出现 **Microsoft.Batch**，若要注册该提供程序，请在订阅中[创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。 

### <a name="configure-a-classic-virtual-network"></a>配置经典虚拟网络
使用门户配置一个经典虚拟网络，然后尝试将 Azure-SSIS IR 加入其中。 

1. 启动 Microsoft Edge 或 Google Chrome。 目前只有这些 Web 浏览器支持数据工厂 UI。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 

1. 选择“更多服务”。 筛选并选择“虚拟网络(经典)”。 

1. 在列表中筛选并选择自己的虚拟网络。 

1. 在“虚拟网络(经典)”页中选择“属性”。 

   ![经典虚拟网络资源 ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. 选择“资源 ID”对应的复制按钮，将经典网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。 

1. 在左侧菜单中选择“子网”。 确保可用地址数大于 Azure-SSIS IR 中的节点数。 

   ![虚拟网络中的可用地址数](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. 将 **MicrosoftAzureBatch** 加入虚拟网络的“经典虚拟机参与者”角色。 

    a. 在左侧菜单中选择“访问控制(IAM)”，然后选择“角色分配”选项卡。 

    ![“访问控制”和“添加”按钮](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. 选择“添加角色分配”。

    c. 在“添加角色分配”页中，为“角色”选择“经典虚拟机参与者”。 在“选择”框中粘贴 **ddbf3205-c6bd-46ae-8127-60eb93363864**，然后从搜索结果列表中选择“Microsoft Azure Batch”。 

    ![在“添加角色分配”页上搜索结果](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. 选择“保存”以保存设置并关闭页面。 

    ![保存访问设置](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. 确认参与者列表中出现了“Microsoft Azure Batch”。 

    ![确认 Azure Batch 访问权限](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. 验证是否已将 Azure Batch 提供程序注册到包含虚拟网络的 Azure 订阅中。 或者注册 Azure Batch 提供程序。 如果订阅中已包含 Azure Batch 帐户，则已经为 Azure Batch 注册了订阅。 （如果在数据工厂门户中创建 Azure-SSIS IR，将自动注册 Azure Batch 提供程序。） 

   a. 在 Azure 门户的左侧菜单中选择“订阅”。 

   b. 选择订阅。 

   c. 在左侧选择“资源提供程序”，确认 **Microsoft.Batch** 是注册的提供程序。 

   ![确认“已注册”状态](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果列表中未出现 **Microsoft.Batch**，若要注册该提供程序，请在订阅中[创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>将 Azure-SSIS IR 加入虚拟网络

配置 Azure 资源管理器虚拟网络或经典虚拟网络后，可将 Azure-SSIS IR 加入该虚拟网络：

1. 启动 Microsoft Edge 或 Google Chrome。 目前只有这些 Web 浏览器支持数据工厂 UI。 

1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单中选择“数据工厂”。 如果菜单中未显示“数据工厂”，请选择“更多服务”，然后在“智能 + 分析”部分选择“数据工厂”。 

   ![数据工厂列表](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 在列表中选择包含 Azure-SSIS IR 的数据工厂。 随后会显示该数据工厂的主页。 选择“创作和部署”磁贴。 单独的选项卡中会显示数据工厂 UI。 

   ![数据工厂主页](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 在数据工厂 UI 中切换到“编辑”选项卡，选择“连接”，然后切换到“集成运行时”选项卡。 

   ![“集成运行时”选项卡](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. 如果 Azure SSIS IR 正在运行，请在“集成运行时”列表中的“操作”列内，选择 Azure SSIS IR 对应的“停止”按钮。 只有先停止 IR 才能对其进行编辑。 

   ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 在“集成运行时”列表中的“操作”列内，选择 Azure SSIS IR 对应的“编辑”按钮。 

   ![编辑集成运行时](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 在“集成运行时设置”面板中，选择“下一步”按钮进入“常规设置”和“SQL 设置”页。 

1. 在“高级设置”页上： 

   a. 选中“选择 VNet”旁边的复选框。 

   b. 对于“订阅”，请选择你的 Azure 订阅。 在订阅下，可以选择现有的虚拟网络。 
  
   c. 对于“VNet 名称”，请选择自己的虚拟网络。 

   d. 对于“子网名称”，请选择虚拟网络中的子网。 

   e. 如果还想要配置或管理自承载 IR 作为 Azure-SSIS IR 的代理，请选中 "**设置自承载**" 复选框。 有关详细信息，请参阅[将自承载 IR 配置为 Azure-SSIS IR 的代理](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。

   f. 选择“VNet 验证”按钮。 如果验证成功，请选择“下一步”按钮。 

   ![IR 安装的高级设置](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. 在“摘要”页上，复查 Azure-SSIS IR 的所有设置。 然后选择“更新”按钮。

1. 在 Azure-SSIS IR 的“操作”列中选择“启动”按钮以启动 Azure-SSIS IR。 启动要加入虚拟网络的 Azure-SSIS IR 需要花费大约 20 到 30 分钟。 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>配置虚拟网络
在将 Azure-SSIS IR 加入虚拟网络之前，需要先配置该虚拟网络。 若要自动配置需加入虚拟网络的 Azure-SSIS IR 的权限和设置，请添加以下脚本：

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>创建 Azure-SSIS IR 并将其加入虚拟网络
可以创建 Azure-SSIS IR，并将其加入虚拟网络。 有关完整的脚本和说明，请参阅[创建 Azure-SSIS IR](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)。

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>将现有 Azure-SSIS IR 加入虚拟网络
[创建 Azure-SSIS IR](create-azure-ssis-integration-runtime.md) 一文介绍了如何在同一个脚本中创建 Azure-SSIS IR 并将其加入虚拟网络。 如果你已有一个 Azure-SSIS IR，请执行以下步骤将其加入虚拟网络： 
1. 停止 Azure-SSIS IR。 
1. 将 Azure-SSIS IR 配置为加入虚拟网络。 
1. 启动 Azure-SSIS IR。 

### <a name="define-the-variables"></a>定义变量
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>停止 Azure-SSIS IR
必须先停止 Azure-SSIS IR，然后才能将它加入虚拟网络。 此命令释放该运行时的所有节点并停止计费：

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>为要加入的 Azure-SSIS IR 配置虚拟网络设置

若要配置 Azure-SSIS 要加入到的虚拟网络的设置，请使用此脚本： 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>配置 Azure-SSIS IR
若要将 Azure-SSIS IR 配置为加入虚拟网络，请运行 `Set-AzDataFactoryV2IntegrationRuntime` 命令： 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>启动 Azure-SSIS IR
若要启动 Azure-SSIS IR，请运行以下命令： 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

此命令需要 20 到 30 分钟才能完成。

## <a name="next-steps"></a>后续步骤
有关 Azure-SSIS IR 的详细信息，请参阅以下文章： 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关 IR（包括 Azure-SSIS IR）的一般概念性信息。 
- [教程：将 SSIS 包部署到 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 此教程提供有关创建 Azure-SSIS IR 的分步说明。 它使用 Azure SQL 数据库来托管 SSIS 目录。 
- [创建 Azure-SSIS IR](create-azure-ssis-integration-runtime.md)。 此文对本教程的内容做了扩充。 其中说明了如何使用虚拟网络中包含虚拟网络服务终结点或托管实例的 Azure SQL 数据库来承载 SSIS 目录。 此外，它介绍了如何将 Azure-SSIS IR 加入虚拟网络。 
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何获取有关 Azure-SSIS IR 的信息。 其中提供了返回的信息的状态说明。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 此文介绍如何停止、启动或删除 Azure-SSIS IR。 此外，介绍如何通过添加节点来扩展 Azure-SSIS IR。
