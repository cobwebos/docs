---
title: 将 Azure SSIS 集成运行时加入虚拟网络 |Microsoft Docs
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
ms.openlocfilehash: 065f69cc98f05fcb19648f190a7dba4b43da1a9a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326620"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>将 Azure-SSIS 集成运行时加入虚拟网络
使用 Azure 数据工厂中的 SQL Server Integration Services （SSIS）时，应在以下情况下将 Azure SSIS 集成运行时（IR）加入 Azure 虚拟网络： 

- 要从运行在 Azure-SSIS IR 上的 SSIS 包连接到本地数据存储，而无需将自承载 IR 作为代理进行配置或管理。 

- 要从在 Azure-SSIS IR 上运行的 SSIS 包连接到支持虚拟网络服务终结点的 Azure 服务资源。

- 你将在 Azure SQL 数据库中承载包含虚拟网络服务终结点或虚拟网络中的托管实例的 SSIS 目录数据库（SSISDB）。 

利用数据工厂，你可以将 Azure-SSIS IR 加入通过经典部署模型或 Azure 资源管理器部署模型创建的虚拟网络。 

> [!IMPORTANT]
> 经典虚拟网络已被弃用，因此请改用 Azure 资源管理器虚拟网络。  如果已使用经典虚拟网络，请尽快切换到 Azure 资源管理器虚拟网络。

## <a name="access-to-on-premises-data-stores"></a>访问本地数据存储
如果 SSIS 包访问本地数据存储，可以将 Azure-SSIS IR 加入到连接到本地网络的虚拟网络中。 也可以配置或管理自承载 IR 作为 Azure-SSIS IR 的代理。 有关详细信息，请参阅[将自承载 IR 配置为 Azure-SSIS IR 的代理](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。 

将 Azure-SSIS IR 加入到虚拟网络时，请记住以下要点： 

- 如果没有虚拟网络连接到本地网络，请先创建[Azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md#create-a-virtual-network)，以便 Azure-SSIS IR 加入。 然后，配置从该虚拟网络到本地网络的站点到站点[VPN 网关连接](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)或[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md)连接。 

- 如果 Azure 资源管理器虚拟网络已连接到 Azure-SSIS IR 所在位置的本地网络，则可以将 IR 加入该虚拟网络。 

- 如果经典虚拟网络已从 Azure-SSIS IR 的不同位置连接到你的本地网络，则可以创建[Azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md#create-a-virtual-network)，以便 Azure-SSIS IR 加入。 然后，配置[经典到 Azure 资源管理器虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)连接。 
 
- 如果 Azure 资源管理器虚拟网络已连接到 Azure-SSIS IR 位于不同位置的本地网络，则可以先创建一个[azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md##create-a-virtual-network)，以便 Azure-SSIS IR 加入。 然后配置 Azure 资源管理器到 Azure 资源管理器虚拟网络连接。 

## <a name="access-to-azure-services"></a>对 Azure 服务的访问权限
如果你的 SSIS 包访问了[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)支持的 Azure 服务资源，并且你想要 Azure-SSIS IR 保护这些资源，则可以将 Azure-SSIS IR 加入到配置了虚拟网络的虚拟网络子网服务终结点。 同时，将虚拟网络规则添加到 Azure 服务资源，以允许来自同一子网的访问。

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>在 SQL 数据库中承载 SSIS 目录
如果将 SSIS 目录承载在具有虚拟网络服务终结点的 Azure SQL 数据库中，请确保将 Azure-SSIS IR 加入到同一虚拟网络和子网中。

若要将 Azure-SSIS IR 加入到托管实例所在的同一虚拟网络中，请确保 Azure-SSIS IR 与托管实例位于不同的子网中。 若要将 Azure-SSIS IR 加入到与托管实例不同的虚拟网络，我们建议将虚拟网络对等互连（限制为同一区域）或从虚拟网络到虚拟网络的连接。 有关详细信息，请参阅[将应用程序连接到 AZURE SQL 数据库托管实例](../sql-database/sql-database-managed-instance-connect-app.md)。

在所有情况下，只能通过 Azure 资源管理器部署模型部署虚拟网络。

以下部分提供了更多详细信息。 

## <a name="virtual-network-configuration"></a>虚拟网络配置

设置虚拟网络以满足以下要求： 

-   请确保 `Microsoft.Batch` 是托管 Azure-SSIS IR 的虚拟网络子网的订阅下的已注册提供程序。 如果使用经典虚拟网络，还应将 `MicrosoftAzureBatch` 加入到该虚拟网络的经典虚拟机参与者角色。 

-   请确保具有所需的权限。 有关详细信息，请参阅[设置权限](#perms)。

-   选择合适的子网，承载 Azure-SSIS IR。 有关详细信息，请参阅[选择子网](#subnet)。 

-   如果在虚拟网络上使用自己的域名系统（DNS）服务器，请参阅[设置 DNS 服务器](#dns_server)。 

-   如果在子网上使用网络安全组（NSG），请参阅[设置 NSG](#nsg)。 

-   如果使用 Azure ExpressRoute 或用户定义的路由（UDR），请参阅[使用 Azure expressroute 或 UDR](#route)。 

-   请确保虚拟网络的资源组可以创建和删除某些 Azure 网络资源。 有关详细信息，请参阅[设置资源组](#resource-group)。 

-   如果自定义[Azure-SSIS IR 的自定义安装](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)中所述的 Azure-SSIS IR，Azure-SSIS IR 节点将从172.31.255.255 的预定义范围中获取专用 IP 地址。 因此，请确保虚拟网络或本地网络的专用 IP 地址范围不会与此范围发生冲突。

下图显示了 Azure-SSIS IR 所需的连接：

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>设置权限

创建 Azure-SSIS IR 的用户必须具有以下权限：

- 如果要将 SSIS IR 加入 Azure 资源管理器虚拟网络，则有两种选择：

  - 使用内置的 "网络参与者" 角色。 此角色具有 _Microsoft.Network/\*_ 权限，具有比所需作用域更大的作用域。

  - 创建仅包括必需的 _Microsoft.Network/virtualNetworks/\*/join/action_ 权限的一个自定义角色。 

- 如果要将 SSIS IR 加入经典虚拟网络，我们建议使用内置的经典虚拟机参与者角色。 否则，你必须定义包含加入虚拟网络权限的自定义角色。

### <a name="subnet"></a>选择子网

选择子网时： 

-   不要选择 GatewaySubnet 来部署 Azure-SSIS IR。 它专用于虚拟网络网关。 

-   确保所选的子网具有足够的可用地址空间，以便 Azure-SSIS IR 使用。 将可用 IP 地址保留为 IR 节点编号的至少两倍。 Azure 会保留每个子网中的某些 IP 地址。 不能使用这些地址。 子网的第一个和最后一个 IP 地址是为协议一致性而保留的，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   请勿使用由其他 Azure 服务独占使用的子网（例如，SQL 数据库托管实例、应用服务等）。 

### <a name="dns_server"></a>设置 DNS 服务器 
如果需要在由 Azure-SSIS IR 联接的虚拟网络中使用自己的 DNS 服务器，请确保它可以解析全局 Azure 主机名（例如，名为 @no__t 的 Azure 存储 blob）。 

建议执行以下步骤： 

-   配置自定义 DNS 以将请求转发到 Azure DNS。 可以在自己的 DNS 服务器上将未解析的 DNS 记录转发到 Azure 递归解析程序（168.63.129.16）的 IP 地址。 

-   将自定义 DNS 设置为虚拟网络的主 DNS 服务器。 将 Azure DNS 设置为辅助 DNS 服务器。 将 Azure 递归解析程序（168.63.129.16）的 IP 地址注册为辅助 DNS 服务器，以防你自己的 DNS 服务器不可用。 

有关详细信息，请参阅[使用自己的 DNS 服务器的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。 

### <a name="nsg"></a>设置 NSG
如果需要为 Azure-SSIS IR 所使用的子网实现 NSG，请通过以下端口允许入站和出站流量： 

| Direction | 传输协议 | Source | 源端口范围 | 目标 | 目标端口范围 | 注释 |
|---|---|---|---|---|---|---|
| 入站 | TCP | BatchNodeManagement | * | VirtualNetwork | 29876、29877（如果将 IR 加入资源管理器虚拟网络） <br/><br/>10100、20100、30100（如果将 IR 加入经典虚拟网络）| 数据工厂服务使用这些端口与虚拟网络中 Azure-SSIS IR 的节点进行通信。 <br/><br/> 无论是否创建子网级 NSG，数据工厂始终在连接到承载 Azure-SSIS IR 的虚拟机的网络接口卡（Nic）级别上配置 NSG。 此 NIC 级别的 NSG 仅允许来自指定端口上的数据工厂 IP 地址的入站流量。 即使您在子网级别为 internet 流量打开这些端口，来自不是数据工厂 IP 地址的 IP 地址的流量也会在 NIC 级别被阻止。 |
| 出站 | TCP | VirtualNetwork | * | AzureCloud | 443 | 虚拟网络中 Azure-SSIS IR 的节点使用此端口访问 Azure 服务，例如 Azure 存储和 Azure 事件中心。 |
| 出站 | TCP | VirtualNetwork | * | Internet | 80 | 虚拟网络中 Azure-SSIS IR 的节点使用此端口从 internet 下载证书吊销列表。 |
| 出站 | TCP | VirtualNetwork | * | Sql | 1433、11000-11999 | 虚拟网络中 Azure-SSIS IR 的节点使用这些端口来访问 SQL 数据库服务器托管的 SSISDB。 如果 SQL 数据库服务器连接策略设置为 "**代理**" 而不是 "**重定向**"，则只需要端口1433。 此出站安全规则不适用于虚拟网络中托管实例托管的 SSISDB。 |
||||||||

### <a name="route"></a>使用 Azure ExpressRoute 或 UDR
当你将[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)线路连接到虚拟网络基础结构以将本地网络扩展到 Azure 时，通用配置使用强制隧道（将 BGP 路由（0.0.0.0/0）播发到虚拟网络。 此隧道会强制将来自虚拟网络的出站 internet 流量流向本地网络设备，以便进行检查和日志记录。 
 
或者，你可以定义[udr](../virtual-network/virtual-networks-udr-overview.md) ，以强制将托管 Azure-SSIS IR 的子网中的出站 internet 流量作为防火墙或 Azure 防火墙托管网络虚拟设备（NVA），以便进行检查和日志记录。 

在这两种情况下，流量路由都将从相关的 Azure 数据工厂服务（尤其是 Azure Batch 管理服务）中断到虚拟网络中的 Azure-SSIS IR 所需的入站连接。 若要避免此情况，请在包含 Azure-SSIS IR 的子网上定义一个或多个 Udr。 

可以在 Azure ExpressRoute 方案中承载 Azure-SSIS IR 的子网上，使用下一跃点类型作为**Internet**应用 0.0.0.0/0 路由。 也可以在 NVA 方案中，将现有的 0.0.0.0/0 路由从 "**虚拟设备**" 作为 "虚拟设备" 更改为 " **Internet** "。

![添加路由](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

如果你担心无法检查来自该子网的出站 internet 流量，则可以定义特定的 Udr，以便仅在 Azure Batch 管理服务和 Azure-SSIS IR 下的下一跃点类型为**internet**时路由流量。

例如，如果 Azure-SSIS IR 位于 `UK South`，则会从[服务标记 ip 范围下载链接](https://www.microsoft.com/en-us/download/details.aspx?id=56519)或通过[服务标记发现 API](https://aka.ms/discoveryapi)获取服务标记的 IP 范围列表 `BatchNodeManagement.UKSouth`。 然后，将以下 Udr 的相关 IP 范围路由和下一跃点类型应用为**Internet**。

![Azure Batch UDR 设置](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> 此方法会产生额外的维护成本。 定期检查 IP 范围，并在 UDR 中添加新的 IP 范围，以避免破坏 Azure-SSIS IR。 建议每月检查 IP 范围，因为当新 IP 出现在服务标记中时，IP 将需要另一个月才能生效。 

### <a name="resource-group"></a>设置资源组
Azure-SSIS IR 需要在与虚拟网络相同的资源组下创建某些网络资源。 这些资源包括：
   -   一个 Azure 负载均衡器，其名称为 *\<Guid >-azurebatch-cloudserviceloadbalancer*。
   -   一个 Azure 公共 IP 地址，名称为 *\<Guid >-azurebatch-cloudservicepublicip*。
   -   网络工作安全组，名称为 *\<Guid >-azurebatch-cloudservicenetworksecuritygroup*。 

当 IR 启动时，将创建这些资源。 当 IR 停止时，它们将被删除。 若要避免阻止 IR 停止，请不要在其他资源中重复使用这些网络资源。 

请确保在虚拟网络所属的资源组或订阅上没有资源锁。 如果配置只读锁定或删除锁定，则启动和停止 IR 可能会失败，否则 IR 可能会停止响应。 

请确保没有 Azure 策略阻止在虚拟网络所属的资源组或订阅下创建以下资源： 
   -   Microsoft.Network/LoadBalancers 
   -   Microsoft.Network/NetworkSecurityGroups 
   -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a> FAQ

- 如何保护对入站连接 Azure-SSIS IR 公开的公共 IP 地址？ 是否可以删除公共 IP 地址？
 
    现在，在 Azure-SSIS IR 加入虚拟网络时，会自动创建一个公共 IP 地址。 我们有一个 NIC 级 NSG，只允许 Azure Batch 管理服务入站连接到 Azure-SSIS IR。 还可以为入站保护指定子网级别的 NSG。

    如果你不希望公开公共 IP 地址，请考虑将[自承载 IR 配置为 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)而不是虚拟网络的代理（如果这适用于你的方案）。
 
- 是否可以将 Azure-SSIS IR 的静态 IP 地址添加到数据源的防火墙允许列表中？
 
    - 如果数据源位于本地，则将虚拟网络连接到本地网络并将 Azure-SSIS IR 加入虚拟网络子网后，即可将该子网的 IP 范围添加到允许列表中。
    - 如果你的数据源是支持虚拟网络服务终结点的 Azure 服务，则可以在虚拟网络上配置虚拟网络服务点，并将 Azure-SSIS IR 加入到该虚拟网络子网中。 然后，你可以使用 Azure 服务的虚拟网络规则而不是 IP 范围来允许访问。
    - 如果你的数据源是不同类型的云数据源，则可以使用 UDR 将出站流量从 Azure-SSIS IR 路由到 NVA 或 Azure 防火墙，方法是使用静态公共 IP 地址。 可以将 NVA 或 Azure 防火墙的公共 IP 地址添加到允许列表。
    - 如果以前的答案无法满足你的需求，请考虑通过将[自承载 IR 配置为 Azure-SSIS IR 的代理](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)来提供数据源访问。 然后，可以将承载自承载 IR 的计算机的 IP 地址添加到允许列表中，而不是将 Azure-SSIS IR 加入虚拟网络。

## <a name="azure-portal-data-factory-ui"></a>Azure 门户（数据工厂 UI）
本部分说明如何使用 Azure 门户和数据工厂 UI 将现有 Azure-SSIS IR 加入虚拟网络（经典或 Azure 资源管理器）。 

将 Azure-SSIS IR 加入虚拟网络之前，需要正确配置虚拟网络。 按照适用于你的虚拟网络（经典或 Azure 资源管理器）类型的部分中的步骤进行操作。 然后按照第三部分中的步骤，将 Azure-SSIS IR 加入虚拟网络。 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>配置 Azure 资源管理器虚拟网络

使用门户配置 Azure 资源管理器虚拟网络，然后再尝试将 Azure-SSIS IR 加入到该网络。

1. 启动 Microsoft Edge 或 Google Chrome。 目前，只有这些 web 浏览器支持数据工厂 UI。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 

1. 选择“更多服务”。 筛选并选择“虚拟网络”。 

1. 在列表中筛选并选择自己的虚拟网络。 

1. 在“虚拟网络”页中选择“属性”。 

1. 选择“资源 ID”对应的复制按钮，将虚拟网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。 

1. 在左侧菜单中，选择 "**子网**"。 确保可用地址的数量大于 Azure-SSIS IR 中的节点数。 

1. 验证是否已将 Azure Batch 提供程序注册到包含虚拟网络的 Azure 订阅中。 或注册 Azure Batch 提供程序。 如果订阅中已有 Azure Batch 帐户，则会为 Azure Batch 注册订阅。 （如果在数据工厂门户中创建 Azure-SSIS IR，将自动注册 Azure Batch 提供程序。） 

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在 Azure 门户的左侧菜单中，选择 "**订阅**"。 

   b. 选择订阅。 

   c. 在左侧，选择 "**资源提供程序**"，并确认 " **Microsoft Batch** " 为已注册的提供程序。 

   ![确认“已注册”状态](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果列表中未出现 **Microsoft.Batch**，若要注册该提供程序，请在订阅中[创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。 

### <a name="configure-a-classic-virtual-network"></a>配置经典虚拟网络
使用门户配置经典虚拟网络，然后再尝试将 Azure-SSIS IR 加入到其中。 

1. 启动 Microsoft Edge 或 Google Chrome。 目前，只有这些 web 浏览器支持数据工厂 UI。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 

1. 选择“更多服务”。 筛选并选择“虚拟网络(经典)”。 

1. 在列表中筛选并选择自己的虚拟网络。 

1. 在“虚拟网络(经典)”页中选择“属性”。 

   ![经典虚拟网络资源 ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. 选择“资源 ID”对应的复制按钮，将经典网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。 

1. 在左侧菜单中，选择 "**子网**"。 确保可用地址的数量大于 Azure-SSIS IR 中的节点数。 

   ![虚拟网络中的可用地址数](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. 将 **MicrosoftAzureBatch** 加入虚拟网络的“经典虚拟机参与者”角色。 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在左侧菜单中，选择 "**访问控制（IAM）** "，然后选择 "**角色分配**" 选项卡。 

    ![“访问控制”和“添加”按钮](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. 选择“添加角色分配”。

    c. 在 "**添加角色分配**" 页上，为 "**角色**" 选择 "**经典虚拟机参与者**"。 在 "**选择**" 框中，粘贴 " **ddbf3205-c6bd-46ae-8127-60eb93363864**"，然后从搜索结果列表中选择 " **Microsoft Azure Batch** "。 

    !["添加角色分配" 页上的搜索结果](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d.单击“下一步”。 选择 "**保存**" 以保存设置并关闭页面。 

    ![保存访问设置](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. 确认参与者列表中出现了“Microsoft Azure Batch”。 

    ![确认 Azure Batch 访问权限](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. 验证是否已将 Azure Batch 提供程序注册到包含虚拟网络的 Azure 订阅中。 或注册 Azure Batch 提供程序。 如果订阅中已有 Azure Batch 帐户，则会为 Azure Batch 注册订阅。 （如果在数据工厂门户中创建 Azure-SSIS IR，将自动注册 Azure Batch 提供程序。） 

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在 Azure 门户的左侧菜单中，选择 "**订阅**"。 

   b. 选择订阅。 

   c. 在左侧，选择 "**资源提供程序**"，并确认 " **Microsoft Batch** " 为已注册的提供程序。 

   ![确认“已注册”状态](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果列表中未出现 **Microsoft.Batch**，若要注册该提供程序，请在订阅中[创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>将 Azure-SSIS IR 加入虚拟网络

配置 Azure 资源管理器虚拟网络或经典虚拟网络后，可以将 Azure-SSIS IR 加入到虚拟网络：

1. 启动 Microsoft Edge 或 Google Chrome。 目前，只有这些 web 浏览器支持数据工厂 UI。 

1. 在[Azure 门户](https://portal.azure.com)的左侧菜单中，选择 "**数据工厂**"。 如果菜单中未显示 "**数据工厂**"，请选择 "**更多服务**"，然后在 "**智能 + 分析**" 部分选择 "**数据工厂**"。 

   ![数据工厂列表](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 在列表中选择包含 Azure-SSIS IR 的数据工厂。 随后会显示该数据工厂的主页。 选择“创作和部署”磁贴。 单独的选项卡中会显示数据工厂 UI。 

   ![数据工厂主页](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 在数据工厂 UI 中切换到“编辑”选项卡，选择“连接”，然后切换到“集成运行时”选项卡。 

   ![“集成运行时”选项卡](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. 如果 Azure-SSIS IR 正在运行，请在 " **Integration** runtime" 列表的 "**操作**" 列中，选择 Azure-SSIS IR 的 "**停止**" 按钮。 在停止 IR 之前，不能对其进行编辑。 

   ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 在**集成运行时**列表的 "**操作**" 列中，选择 Azure-SSIS IR 的 "**编辑**" 按钮。 

   ![编辑集成运行时](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 在 " **Integration Runtime 设置**" 面板上，通过选择 "**下一步**" 按钮前进到 "**常规设置**" 和 " **SQL 设置**" 页。 

1. 在 "**高级设置**" 页上： 

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选中 "**选择 VNet**" 旁边的复选框。 

   b. 对于“订阅”，请选择你的 Azure 订阅。 在订阅下，可以选择现有虚拟网络。 
  
   c. 对于“VNet 名称”，请选择自己的虚拟网络。 

   d.单击“下一步”。 对于“子网名称”，请选择虚拟网络中的子网。 

   e. 如果还想要配置或管理自承载 IR 作为 Azure-SSIS IR 的代理，请选中 "**设置自承载**" 复选框。 有关详细信息，请参阅[将自承载 IR 配置为 Azure-SSIS IR 的代理](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。

   f. 选择 " **VNet 验证**" 按钮。 如果验证成功，请选择 "**下一步**" 按钮。 

   ![IR 安装的高级设置](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. 在 "**摘要**" 页上，查看 Azure-SSIS IR 的所有设置。 然后选择 "**更新**" 按钮。

1. 通过选择 Azure-SSIS IR 的 "**操作**" 列中的 "**启动**" 按钮来启动 Azure-SSIS IR。 启动加入虚拟网络的 Azure-SSIS IR 需要大约20到30分钟的时间。 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>配置虚拟网络
需要先配置虚拟网络，然后才能将 Azure-SSIS IR 加入虚拟网络。 若要为你的 Azure-SSIS IR 自动配置虚拟网络权限和设置以加入虚拟网络，请添加以下脚本：

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
[创建 Azure-SSIS IR](create-azure-ssis-integration-runtime.md)一文介绍了如何创建 Azure-SSIS IR，并在同一脚本中将其加入到虚拟网络。 如果你已经有 Azure-SSIS IR，请按照以下步骤将其加入到虚拟网络： 
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
必须先停止 Azure-SSIS IR，然后才能将其加入虚拟网络。 此命令释放该运行时的所有节点并停止计费：

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>为要加入的 Azure-SSIS IR 配置虚拟网络设置

若要配置 Azure SSIS 将连接的虚拟网络的设置，请使用以下脚本： 

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
若要配置 Azure-SSIS IR 以加入虚拟网络，请运行 `Set-AzDataFactoryV2IntegrationRuntime` 命令： 

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
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供了有关 IRs 的一般概念性信息，其中包括 Azure-SSIS IR。 
- [教程：将 SSIS 包部署到 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本教程提供了创建 Azure-SSIS IR 的分步说明。 它使用 Azure SQL 数据库来托管 SSIS 目录。 
- [创建 Azure-SSIS IR](create-azure-ssis-integration-runtime.md)。 本文将展开教程。 它提供有关在虚拟网络中将 Azure SQL 数据库用于虚拟网络服务终结点或托管实例以托管 SSIS 目录的说明。 其中介绍了如何将 Azure-SSIS IR 加入虚拟网络。 
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 本文介绍如何获取有关 Azure-SSIS IR 的信息。 它提供返回信息的状态说明。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 本文介绍如何停止、启动或删除 Azure-SSIS IR。 此外，介绍如何通过添加节点来扩展 Azure-SSIS IR。
