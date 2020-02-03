---
title: 将 Azure-SSIS 集成运行时加入虚拟网络
description: 了解如何将 Azure SSIS 集成运行时加入 Azure 虚拟网络。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 7e8a1793a329a863c9df97ae5ddcbee6cef10e8e
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964276"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>将 Azure-SSIS 集成运行时加入虚拟网络

使用 Azure 数据工厂中的 SQL Server Integration Services （SSIS）时，应在以下情况下将 Azure SSIS 集成运行时（IR）加入 Azure 虚拟网络：

- 要从运行在 Azure-SSIS IR 上的 SSIS 包连接到本地数据存储，而无需配置或管理自承载 IR 作为代理。 

- 你想要在 Azure SQL 数据库中托管使用 IP 防火墙规则/虚拟网络服务终结点的 SSIS 目录数据库（SSISDB）或使用专用终结点的托管实例。 

- 要从在 Azure-SSIS IR 上运行的 SSIS 包中连接到配置了虚拟网络服务终结点的 Azure 资源。

- 需要连接到在 Azure-SSIS IR 上运行的 SSIS 包中配置了 IP 防火墙规则的数据存储/资源。

利用数据工厂，你可以将 Azure-SSIS IR 加入通过经典部署模型或 Azure 资源管理器部署模型创建的虚拟网络。

> [!IMPORTANT]
> 经典虚拟网络已被弃用，因此请改用 Azure 资源管理器虚拟网络。  如果已使用经典虚拟网络，请尽快切换到 Azure 资源管理器虚拟网络。

[配置 Azure SQL Server Integration Services （SSIS）集成运行时（IR）以加入虚拟网络](tutorial-deploy-ssis-virtual-network.md)教程介绍了通过 Azure 门户的最少步骤。 本文扩展了本教程，并介绍了所有可选任务：

- 如果你使用的是虚拟网络（经典）。
- 如果为 Azure-SSIS IR 提供了自己的公共 IP 地址。
- 如果你使用自己的域名系统（DNS）服务器。
- 如果在子网上使用网络安全组（NSG）。
- 如果使用 Azure ExpressRoute 或用户定义的路由（UDR）。
- 如果使用自定义 Azure-SSIS IR。
- 如果使用 Azure Powershell 预配。

## <a name="access-to-on-premises-data-stores"></a>访问本地数据存储

如果 SSIS 包访问本地数据存储，可以将 Azure-SSIS IR 加入到连接到本地网络的虚拟网络中。 也可以配置和管理 Azure-SSIS IR 的自承载 IR 作为代理。 有关详细信息，请参阅[将自承载 IR 配置为 Azure-SSIS IR 的代理](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。 

将 Azure-SSIS IR 加入到虚拟网络时，请记住以下要点： 

- 如果没有虚拟网络连接到本地网络，请先创建[Azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md#create-a-virtual-network)，以便 Azure-SSIS IR 加入。 然后，配置从该虚拟网络到本地网络的站点到站点[VPN 网关连接](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)或[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md)连接。 

- 如果 Azure 资源管理器虚拟网络已连接到 Azure-SSIS IR 所在位置的本地网络，则可以将 IR 加入该虚拟网络。 

- 如果经典虚拟网络已从 Azure-SSIS IR 的不同位置连接到你的本地网络，则可以创建[Azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md#create-a-virtual-network)，以便 Azure-SSIS IR 加入。 然后，配置[经典到 Azure 资源管理器虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)连接。 
 
- 如果 Azure 资源管理器虚拟网络已连接到 Azure-SSIS IR 位于不同位置的本地网络，则可以先创建一个[azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md#create-a-virtual-network)，以便 Azure-SSIS IR 加入。 然后配置 Azure 资源管理器到 Azure 资源管理器虚拟网络连接。 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>在 SQL 数据库中承载 SSIS 目录

如果在包含虚拟网络服务终结点的 Azure SQL 数据库中托管 SSIS 目录，请确保将 Azure-SSIS IR 加入同一虚拟网络和子网。

如果在具有专用终结点的托管实例中托管 SSIS 目录，请确保将 Azure-SSIS IR 加入同一虚拟网络，但在不同于托管实例的子网中。 若要将 Azure-SSIS IR 加入到与托管实例不同的虚拟网络，我们建议将虚拟网络对等互连（限制为同一区域）或从虚拟网络到虚拟网络的连接。 有关详细信息，请参阅[将应用程序连接到 AZURE SQL 数据库托管实例](../sql-database/sql-database-managed-instance-connect-app.md)。

## <a name="access-to-azure-services"></a>对 Azure 服务的访问权限

如果 SSIS 包访问支持[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)的 Azure 资源，并且你想要从 Azure-SSIS IR 保护对这些资源的访问，则可以将 Azure-SSIS IR 加入为虚拟网络服务终结点配置的虚拟网络子网，然后将虚拟网络规则添加到相关的 Azure 资源，以允许从同一子网进行访问。

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>对受 IP 防火墙规则保护的数据源的访问权限

如果 SSIS 包访问的数据存储区/资源只允许特定的静态公共 IP 地址，并且你想要从 Azure-SSIS IR 保护对这些资源的访问，则你可以将自己 Azure-SSIS IR 的[公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)加入到虚拟网络，然后将 ip 防火墙规则添加到相关资源，以允许来自这些 IP 地址的访问。

在所有情况下，只能通过 Azure 资源管理器部署模型部署虚拟网络。

以下部分提供了更多详细信息。 

## <a name="virtual-network-configuration"></a>虚拟网络配置

设置虚拟网络以满足以下要求： 

- 请确保 `Microsoft.Batch` 是托管 Azure-SSIS IR 的虚拟网络子网订阅下的已注册提供程序。 如果使用经典虚拟网络，还应将 `MicrosoftAzureBatch` 加入到该虚拟网络的经典虚拟机参与者角色。 

- 请确保具有所需的权限。 有关详细信息，请参阅[设置权限](#perms)。

- 选择合适的子网，承载 Azure-SSIS IR。 有关详细信息，请参阅[选择子网](#subnet)。 

- 如果为 Azure-SSIS IR 提供了自己的公共 IP 地址，请参阅[选择静态公共 ip 地址](#publicIP)

- 如果在虚拟网络上使用自己的域名系统（DNS）服务器，请参阅[设置 DNS 服务器](#dns_server)。 

- 如果在子网上使用网络安全组（NSG），请参阅[设置 NSG](#nsg)。 

- 如果使用 Azure ExpressRoute 或用户定义的路由（UDR），请参阅[使用 Azure expressroute 或 UDR](#route)。 

- 如果你引入自己的公共 IP 地址，请确保虚拟网络的资源组（或公共 IP 地址的资源组）可以创建和删除某些 Azure 网络资源。 有关详细信息，请参阅[设置资源组](#resource-group)。 

- 如果自定义[Azure-SSIS IR 的自定义安装](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)中所述的 Azure-SSIS IR，Azure-SSIS IR 节点将从172.31.255.255 的预定义范围中获取专用 IP 地址。 因此，请确保虚拟网络或本地网络的专用 IP 地址范围不会与此范围发生冲突。

下图显示了 Azure-SSIS IR 所需的连接：

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>设置权限

创建 Azure-SSIS IR 的用户必须具有以下权限：

- 如果要将 SSIS IR 加入 Azure 资源管理器虚拟网络，则有两种选择：

  - 使用内置的 "网络参与者" 角色。 此角色具有 _Microsoft.Network/\*_ 权限，具有比所需作用域更大的作用域。

  - 创建仅包括必需的 _Microsoft.Network/virtualNetworks/\*/join/action_ 权限的一个自定义角色。 如果还想要为 Azure-SSIS IR 提供自己的公共 IP 地址，同时将它加入 Azure 资源管理器虚拟网络，请在角色中加入_publicIPAddresses/*/join/action_权限。

- 如果要将 SSIS IR 加入经典虚拟网络，我们建议使用内置的经典虚拟机参与者角色。 否则，你必须定义包含加入虚拟网络权限的自定义角色。

### <a name="subnet"></a>选择子网

选择子网时： 

- 不要选择 GatewaySubnet 来部署 Azure-SSIS IR。 它专用于虚拟网络网关。 

- 确保所选的子网具有足够的可用地址空间，以便 Azure-SSIS IR 使用。 将可用 IP 地址保留为 IR 节点编号的至少两倍。 Azure 会保留每个子网中的某些 IP 地址。 不能使用这些地址。 子网的第一个和最后一个 IP 地址是为协议一致性而保留的，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- 请勿使用由其他 Azure 服务独占使用的子网（例如，SQL 数据库托管实例、应用服务等）。 

### <a name="publicIP"></a>选择静态公共 IP 地址

如果要在将静态公共 IP 地址加入虚拟网络的同时，为其提供 Azure-SSIS IR，请确保它们满足以下要求：

- 应该仅提供未与其他 Azure 资源关联的两个未使用的文件。 当我们定期升级你的 Azure-SSIS IR 时，将使用额外的帐户。

- 它们应为标准类型的静态类型。 有关更多详细信息，请参阅[公共 IP 地址的 sku](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) 。

- 它们应该都有 DNS 名称。 如果在创建 DNS 名称时未提供 DNS 名称，则可以在 Azure 门户上执行此操作。

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- 它们和虚拟网络应位于相同的订阅和同一区域中。

### <a name="dns_server"></a>设置 DNS 服务器 
如果你需要在由 Azure-SSIS IR 联接的虚拟网络中使用你自己的 DNS 服务器来解析你的专用主机名，请确保它还可以解析全局 Azure 主机名（例如，名为 `<your storage account>.blob.core.windows.net`的 Azure 存储 blob）。 

下面是一种建议的方法： 

-   配置自定义 DNS 以将请求转发到 Azure DNS。 可以在自己的 DNS 服务器上将未解析的 DNS 记录转发到 Azure 递归解析程序（168.63.129.16）的 IP 地址。 

有关详细信息，请参阅[使用自己的 DNS 服务器的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。 

> [!NOTE]
> 请使用完全限定的域名（FQDN）作为专用主机名称，例如，使用 `<your_private_server>.contoso.com` 而不是 `<your_private_server>`，因为 Azure-SSIS IR 不会自动追加你自己的 DNS 后缀。

### <a name="nsg"></a>设置 NSG
如果需要为 Azure-SSIS IR 所使用的子网实现 NSG，请通过以下端口允许入站和出站流量： 

-   **Azure-SSIS IR 的入站要求**

| 方向 | 传输协议 | 源 | 源端口范围 | 目标 | Destination port range | 注释 |
|---|---|---|---|---|---|---|
| 入站 | TCP | BatchNodeManagement | * | VirtualNetwork | 29876、29877（如果将 IR 加入资源管理器虚拟网络） <br/><br/>10100、20100、30100（如果将 IR 加入经典虚拟网络）| 数据工厂服务使用这些端口与虚拟网络中 Azure-SSIS IR 的节点进行通信。 <br/><br/> 无论是否创建子网级 NSG，数据工厂始终在连接到承载 Azure-SSIS IR 的虚拟机的网络接口卡（Nic）级别上配置 NSG。 此 NIC 级别的 NSG 仅允许来自指定端口上的数据工厂 IP 地址的入站流量。 即使您在子网级别为 internet 流量打开这些端口，来自不是数据工厂 IP 地址的 IP 地址的流量也会在 NIC 级别被阻止。 |
| 入站 | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | 可有可无仅当 Microsoft 支持者要求客户打开以进行高级故障排除时，才需要此规则，故障排除后即可直接关闭。 **CorpNetSaw**服务标记仅允许 Microsoft 企业网络中的安全访问工作站使用远程桌面。 此服务标记无法从门户中选择，只能通过 Azure PowerShell 或 Azure CLI 使用。 <br/><br/> 在 NIC level NSG 上，端口3389在默认情况下处于打开状态，我们允许在子网级别 NSG 控制端口3389，同时 Azure-SSIS IR 在每个 IR 节点上的 windows 防火墙规则中默认禁用端口3389出站。 |
||||||||

-   **Azure-SSIS IR 的出站要求**

| 方向 | 传输协议 | 源 | 源端口范围 | 目标 | Destination port range | 注释 |
|---|---|---|---|---|---|---|
| 出站 | TCP | VirtualNetwork | * | AzureCloud | 443 | 虚拟网络中 Azure-SSIS IR 的节点使用此端口访问 Azure 服务，例如 Azure 存储和 Azure 事件中心。 |
| 出站 | TCP | VirtualNetwork | * | Internet | 80 | 可有可无虚拟网络中 Azure-SSIS IR 的节点使用此端口从 internet 下载证书吊销列表。 如果阻止此流量，则在启动 IR 时可能会遇到性能降级，并且无法检查证书吊销列表的证书使用情况。 如果要进一步缩小目标位置，请参阅**使用 Azure ExpressRoute 或 UDR**部分|
| 出站 | TCP | VirtualNetwork | * | Sql | 1433、11000-11999 | 可有可无仅当虚拟网络中 Azure-SSIS IR 的节点访问 SQL 数据库服务器托管的 SSISDB 时，才需要此规则。 如果 SQL 数据库服务器连接策略设置为 "**代理**" 而不是 "**重定向**"，则只需要端口1433。 <br/><br/> 此出站安全规则不适用于通过专用终结点配置的虚拟网络或 Azure 数据库服务器中的托管实例所托管的 SSISDB。 |
| 出站 | TCP | VirtualNetwork | * | VirtualNetwork | 1433、11000-11999 | 可有可无仅当虚拟网络中 Azure-SSIS IR 的节点访问通过专用终结点配置的虚拟网络或 Azure 数据库服务器中托管实例承载的 SSISDB 时，才需要此规则。 如果 SQL 数据库服务器连接策略设置为 "**代理**" 而不是 "**重定向**"，则只需要端口1433。 |
| 出站 | TCP | VirtualNetwork | * | 存储空间 | 445 | 可有可无仅当要执行存储在 Azure 文件中的 SSIS 包时，才需要此规则。 |
||||||||

### <a name="route"></a>使用 Azure ExpressRoute 或 UDR
如果要检查来自 Azure-SSIS IR 的出站流量，可以通过[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)强制隧道（将 BGP 路由、0.0.0.0/0 播发到虚拟网络）或将网络虚拟设备（NVA [）通过](https://docs.microsoft.com/azure/firewall/) [Udr](../virtual-network/virtual-networks-udr-overview.md)Azure-SSIS IR 路由到网络虚拟设备（）。 

![Azure-SSIS IR 的 NVA 方案](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

你需要执行以下操作才能完成整个方案
   -   Azure Batch 管理服务与 Azure-SSIS IR 之间的入站流量不能通过防火墙设备进行路由。
   -   防火墙设备应该允许 Azure-SSIS IR 所需的出站流量。

Azure Batch 管理服务与 Azure-SSIS IR 之间的入站流量不能路由到防火墙设备，否则流量将因非对称路由问题而中断。 必须为入站流量定义路由，以便流量回复的方式与其传入的方式相同。 你可以定义特定 Udr，以在 Azure Batch 管理服务和 Azure-SSIS IR 的下一跃点类型为**Internet**之间路由流量。

例如，如果你的 Azure-SSIS IR 位于 `UK South` 上，并且你想要通过 Azure 防火墙检查出站流量，则首先从[服务标记 ip 范围下载链接](https://www.microsoft.com/download/details.aspx?id=56519)或通过[服务标记发现 API](https://aka.ms/discoveryapi)获取服务标记 `BatchNodeManagement.UKSouth` 的 IP 范围列表。 然后，将以下 Udr 的相关 IP 范围路由的下一跃点类型作为**Internet** ，并将下一跃点类型作为**虚拟设备**应用于 0.0.0.0/0 路由。

![Azure Batch UDR 设置](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> 此方法会产生额外的维护成本。 定期检查 IP 范围，并在 UDR 中添加新的 IP 范围，以避免破坏 Azure-SSIS IR。 建议每月检查 IP 范围，因为当新 IP 出现在服务标记中时，IP 将需要另一个月才能生效。 

要使防火墙设备允许出站流量，你需要允许与 NSG 出站规则中的要求相同的出站端口。
-   端口443，目标作为 Azure 云服务。

    如果你使用 Azure 防火墙，则可以使用 AzureCloud 服务标记指定网络规则，否则，你可能会在防火墙设备中允许使用目标。

-   端口80，其目标为 CRL 下载站点。

    你应允许低于 Fqdn （证书吊销列表）下载证书站点的 Fqdn （用于 Azure-SSIS IR 管理目的）：
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    如果你使用的证书具有不同的 CRL，则建议你同时包含它们。 你可以阅读此信息以了解有关[证书吊销列表](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)的详细信息。

    如果你不想使用此流量，则在开始 Azure-SSIS IR 时可能会遇到性能降级，并且无法检查证书吊销列表的证书使用情况（从安全角度来看，不建议这样做）。

-   端口1433，11000-11999，目标为 Azure SQL （仅当虚拟网络中 Azure-SSIS IR 的节点访问 SQL 数据库服务器托管的 SSISDB 时才需要）。

    如果使用 Azure 防火墙，则可以使用 Azure SQL 服务标记指定网络规则，否则可能会允许目标作为防火墙设备中的特定 Azure SQL url。

-   目标为 Azure 存储的端口445（仅当执行存储在 Azure 文件中的 SSIS 包时需要）。

    如果使用 Azure 防火墙，则可以使用存储服务标记指定网络规则，否则，可能会在防火墙设备中允许目标作为特定的 Azure 文件存储 url。

> [!NOTE]
> 对于 Azure SQL 和存储，如果在子网上配置虚拟网络服务终结点，则同一区域或配对区域中的 Azure-SSIS IR 与 Azure SQL 之间的流量将直接路由到 Microsoft Azure 主干网络而不是防火墙设备。

如果不需要检查 Azure-SSIS IR 的出站流量，可以直接应用路由，强制所有流量到下一跃点类型**Internet**：

-   在 Azure ExpressRoute 方案中，你可以在承载 Azure-SSIS IR 的子网上，使用下一跃点类型作为**Internet**应用 0.0.0.0/0 路由。 
-   在 NVA 方案中，可以修改在子网中应用的现有 0.0.0.0/0 路由 Azure-SSIS IR，该路由将从下一跃点类型作为**虚拟设备**连接到**Internet**。

![添加路由](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> 指定下一跃点类型的路由未表示所有流量都将**通过 internet。** 只要目标地址适用于 Azure 的一个服务，Azure 就会通过 Azure 的主干网络直接将流量路由到该服务，而不是将流量路由到 Internet。

### <a name="resource-group"></a>设置资源组

Azure-SSIS IR 需要在与虚拟网络相同的资源组下创建某些网络资源。 这些资源包括：
- Azure 负载均衡器，其名称 *\<Guid >-azurebatch-cloudserviceloadbalancer*。
- 一个 Azure 公共 IP 地址，名称 *\<Guid >-azurebatch-cloudservicepublicip*。
- 网络工作安全组，名称 *\<Guid >-cloudservicenetworksecuritygroup*。 

> [!NOTE]
> 你现在可以为 Azure-SSIS IR 提供自己的静态公共 IP 地址。 在此方案中，我们将只在与静态公共 IP 地址相同的资源组下创建 Azure 负载均衡器和网络安全组，而不是在虚拟网络中创建。

Azure-SSIS IR 启动时，将创建这些资源。 当你的 Azure-SSIS IR 停止时，它们将被删除。 如果为 Azure-SSIS IR 提供自己的静态公共 IP 地址，则在 Azure-SSIS IR 停止时不会将其删除。 若要避免阻止 Azure-SSIS IR 停止，请不要在其他资源中重复使用这些网络资源。 

请确保虚拟网络/静态公共 IP 地址所属的资源组/订阅上没有资源锁。 如果配置了只读/删除锁定，则启动和停止 Azure-SSIS IR 将失败，否则将停止响应。

请确保没有 Azure 策略阻止在虚拟网络/静态公共 IP 地址所属的资源组/订阅下创建以下资源： 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a> FAQ

- 如何在用于入站连接的 Azure-SSIS IR 上保护公开的公共 IP 地址？ 是否可以删除公共 IP 地址？
 
  现在，在 Azure-SSIS IR 加入虚拟网络时，会自动创建一个公共 IP 地址。 我们有一个 NIC 级 NSG，只允许 Azure Batch 管理服务入站连接到 Azure-SSIS IR。 还可以为入站保护指定子网级别的 NSG。

  如果你不希望公开任何公共 IP 地址，请考虑为[你的 Azure-SSIS IR 配置自承载 IR 作为代理](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)，而不是将 Azure-SSIS IR 加入虚拟网络（如果这适用于你的方案）。
 
- 我是否可以将我的 Azure-SSIS IR 的公共 IP 地址添加到我的数据源的防火墙允许列表中？

  你现在可以为 Azure-SSIS IR 提供自己的静态公共 IP 地址。 在这种情况下，你可以将 IP 地址添加到你的数据源的防火墙允许列表中。 你还可以考虑下面的其他选项，以根据你的情况从 Azure-SSIS IR 中保护数据访问：

  - 如果数据源位于本地，则在将虚拟网络连接到本地网络并将 Azure-SSIS IR 加入虚拟网络子网之后，可将该子网的专用 IP 地址范围添加到数据源的防火墙允许列表中.
  - 如果数据源是支持虚拟网络服务终结点的 Azure 服务，则可以在虚拟网络子网上配置虚拟网络服务终结点，并将 Azure-SSIS IR 加入该子网。 然后，你可以将此子网的虚拟网络规则添加到数据源的防火墙。
  - 如果你的数据源是非 Azure 云服务，则可以使用 UDR 通过静态公共 IP 地址将 Azure-SSIS IR 中的出站流量路由到 NVA/Azure 防火墙。 然后，你可以将 NVA/Azure 防火墙的静态公共 IP 地址添加到你的数据源的防火墙允许列表中。
  - 如果上述任何选项都不能满足你的需求，请考虑将[自承载 IR 配置为你的 Azure-SSIS IR 的代理](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。 然后，你可以将承载自承载 IR 的计算机的静态公共 IP 地址添加到你的数据源的防火墙允许列表中。

- 如果我想要为 Azure-SSIS IR 提供两个静态公用地址，为什么需要提供两个：

  Azure-SSIS IR 会定期自动更新。 新节点是在升级期间创建的，旧节点会被删除。 但是，为了避免停机，在新节点准备就绪之前，不会删除旧节点。 因此，旧节点使用的第一个静态公共 IP 地址无法立即释放，我们需要第二个静态公共 IP 地址来创建新节点。

- 我已经为 Azure-SSIS IR 提供了自己的静态公共 IP 地址，但为何仍无法访问我的数据源？

  - 确认两个静态公共 IP 地址都已添加到数据源的防火墙允许列表中。 每次升级你的 Azure-SSIS IR 时，其静态公共 IP 地址将在你所携带的两者之间进行切换。 如果只将其中一个添加到允许列表中，则在升级后，Azure-SSIS IR 的数据访问将会断开。
  - 如果数据源是 Azure 服务，请检查是否已使用虚拟网络服务终结点对其进行配置。 如果是这种情况，则从 Azure-SSIS IR 到数据源的流量将切换为使用由 Azure 服务管理的专用 IP 地址，并将您自己的静态公共 IP 地址添加到您的数据源的防火墙的允许列表中将不会生效。

## <a name="azure-portal-data-factory-ui"></a>Azure 门户（数据工厂 UI）

本部分说明如何使用 Azure 门户和数据工厂 UI 将现有 Azure-SSIS IR 加入虚拟网络（经典或 Azure 资源管理器）。 

将 Azure-SSIS IR 加入虚拟网络之前，需要正确配置虚拟网络。 按照适用于你的虚拟网络（经典或 Azure 资源管理器）类型的部分中的步骤进行操作。 然后按照第三部分中的步骤，将 Azure-SSIS IR 加入虚拟网络。 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>配置 Azure 资源管理器虚拟网络

使用门户配置 Azure 资源管理器虚拟网络，然后再尝试将 Azure-SSIS IR 加入到该网络。

1. 启动 Microsoft Edge 或 Google Chrome。 目前，只有这些 web 浏览器支持数据工厂 UI。 

1. 登录 [Azure 门户](https://portal.azure.com)。 

1. 选择“更多服务”。 筛选并选择“虚拟网络”。 

1. 在列表中筛选并选择自己的虚拟网络。 

1. 在“虚拟网络”页中选择“属性”。 

1. 选择“资源 ID”对应的复制按钮，将虚拟网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。 

1. 在左侧菜单中，选择 "**子网**"。 确保可用地址的数量大于 Azure-SSIS IR 中的节点数。 

1. 验证是否已将 Azure Batch 提供程序注册到包含虚拟网络的 Azure 订阅中。 或注册 Azure Batch 提供程序。 如果订阅中已有 Azure Batch 帐户，则会为 Azure Batch 注册订阅。 （如果在数据工厂门户中创建 Azure-SSIS IR，将自动注册 Azure Batch 提供程序。） 

   1. 在 Azure 门户的左侧菜单中，选择 "**订阅**"。 

   1. 选择订阅。 

   1. 在左侧，选择 "**资源提供程序**"，并确认 " **Microsoft Batch** " 为已注册的提供程序。 

   ![确认“已注册”状态](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果列表中未出现 **Microsoft.Batch**，若要注册该提供程序，请在订阅中[创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。 

### <a name="configure-a-classic-virtual-network"></a>配置经典虚拟网络

使用门户配置经典虚拟网络，然后再尝试将 Azure-SSIS IR 加入到其中。 

1. 启动 Microsoft Edge 或 Google Chrome。 目前，只有这些 web 浏览器支持数据工厂 UI。 

1. 登录 [Azure 门户](https://portal.azure.com)。 

1. 选择“更多服务”。 筛选并选择“虚拟网络(经典)”。 

1. 在列表中筛选并选择自己的虚拟网络。 

1. 在“虚拟网络(经典)”页中选择“属性”。 

   ![经典虚拟网络资源 ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. 选择“资源 ID”对应的复制按钮，将经典网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。 

1. 在左侧菜单中，选择 "**子网**"。 确保可用地址的数量大于 Azure-SSIS IR 中的节点数。 

   ![虚拟网络中的可用地址数](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. 将 **MicrosoftAzureBatch** 加入虚拟网络的“经典虚拟机参与者”角色。 

   1. 在左侧菜单中，选择 "**访问控制（IAM）** "，然后选择 "**角色分配**" 选项卡。 

       ![“访问控制”和“添加”按钮](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. 选择“添加角色分配”。

   1. 在 "**添加角色分配**" 页上，为 "**角色**" 选择 "**经典虚拟机参与者**"。 在 "**选择**" 框中，粘贴 " **ddbf3205-c6bd-46ae-8127-60eb93363864**"，然后从搜索结果列表中选择 " **Microsoft Azure Batch** "。 

       !["添加角色分配" 页上的搜索结果](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. 选择 "**保存**" 以保存设置并关闭页面。 

       ![保存访问设置](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. 确认参与者列表中出现了“Microsoft Azure Batch”。 

       ![确认 Azure Batch 访问权限](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. 验证是否已将 Azure Batch 提供程序注册到包含虚拟网络的 Azure 订阅中。 或注册 Azure Batch 提供程序。 如果订阅中已有 Azure Batch 帐户，则会为 Azure Batch 注册订阅。 （如果在数据工厂门户中创建 Azure-SSIS IR，将自动注册 Azure Batch 提供程序。） 

   1. 在 Azure 门户的左侧菜单中，选择 "**订阅**"。 

   1. 选择订阅。 

   1. 在左侧，选择 "**资源提供程序**"，并确认 " **Microsoft Batch** " 为已注册的提供程序。 

   ![确认“已注册”状态](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果列表中未出现 **Microsoft.Batch**，若要注册该提供程序，请在订阅中[创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>将 Azure-SSIS IR 加入虚拟网络

配置 Azure 资源管理器虚拟网络或经典虚拟网络后，可以将 Azure-SSIS IR 加入到虚拟网络：

1. 启动 Microsoft Edge 或 Google Chrome。 目前，只有这些 web 浏览器支持数据工厂 UI。 

1. 在[Azure 门户](https://portal.azure.com)的左侧菜单中，选择 "**数据工厂**"。 如果菜单中未显示 "**数据工厂**"，请选择 "**更多服务**"，然后在 "**智能 + 分析**" 部分选择 "**数据工厂**"。 

   ![数据工厂列表](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 在列表中选择包含 Azure-SSIS IR 的数据工厂。 随后会显示该数据工厂的主页。 选择 "**创作 & 监视器**" 磁贴。 单独的选项卡中会显示数据工厂 UI。 

   ![数据工厂主页](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 在数据工厂 UI 中切换到“编辑”选项卡，选择“连接”，然后切换到“集成运行时”选项卡。 

   ![“集成运行时”选项卡](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. 如果 Azure-SSIS IR 正在运行，请在 " **Integration** runtime" 列表的 "**操作**" 列中，选择 Azure-SSIS IR 的 "**停止**" 按钮。 你无法编辑 Azure-SSIS IR，直到你将其停止。 

   ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 在**集成运行时**列表的 "**操作**" 列中，选择 Azure-SSIS IR 的 "**编辑**" 按钮。 

   ![编辑集成运行时](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 在集成运行时设置面板上，通过选择 "**下一步**" 按钮前进到 "**常规设置**" 和 " **SQL 设置**" 部分。 

1. 在 "**高级设置**" 部分中： 

   1. 选择 "为**要加入的 Azure-SSIS Integration Runtime 选择 VNet"，允许 ADF 创建特定的网络资源，并选择 "自带静态公共 IP 地址**" 复选框。 

   1. 对于“订阅”，请选择包含你的虚拟网络的 Azure 订阅。

   1. 对于“位置”，系统已选择集成运行时所在的位置。

   1. 对于 "**类型**"，请选择虚拟网络的类型：经典或 Azure 资源管理器。 建议选择 Azure 资源管理器虚拟网络，因为即将弃用经典虚拟网络。

   1. 对于“VNet 名称”，请选择虚拟网络的名称。 它应与包含虚拟网络服务终结点的 Azure SQL 数据库服务器或具有专用终结点的托管实例一起用于托管 SSISDB。 或者，它应与连接到本地网络的计算机相同。 否则，可以通过任何虚拟网络为 Azure-SSIS IR 提供自己的静态公共 IP 地址。

   1. 对于“子网名称”，请选择虚拟网络的子网名称。 它应该与用于具有虚拟网络服务终结点的 Azure SQL 数据库服务器托管 SSISDB 的服务器相同。 它应与用于托管实例的托管实例的子网不同，后者用于托管 SSISDB。 否则，它可以是任何子网，为 Azure-SSIS IR 提供自己的静态公共 IP 地址。

   1. 选中 "**为您的 Azure-SSIS Integration Runtime 提供静态公共 ip 地址**" 复选框，以选择是否要为 Azure-SSIS IR 提供自己的静态公共 ip 地址，以便您可以在防火墙上为数据源启用它们。

      如果选中该复选框，请完成以下步骤。

      1. 对于 "**第一个静态公共 ip 地址**"，请选择[符合 Azure-SSIS IR 要求](#publicIP)的第一个静态公共 ip 地址。 如果没有任何设置 **，请单击 "新建链接"** ，在 Azure 门户上创建静态公共 IP 地址，然后单击 "刷新" 按钮，以便可以选择它们。
      
      1. 对于 "**第二个静态公共 ip 地址**"，请选择符合 Azure-SSIS IR[要求](#publicIP)的第二个静态公共 ip 地址。 如果没有任何设置 **，请单击 "新建链接"** ，在 Azure 门户上创建静态公共 IP 地址，然后单击 "刷新" 按钮，以便可以选择它们。

   1. 选择 " **VNet 验证**"。 如果验证成功，请选择 "**继续**"。 

   ![使用虚拟网络的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. 在 "**摘要**" 部分中，查看 Azure-SSIS IR 的所有设置。 然后选择 "**更新**"。

1. 通过选择 Azure-SSIS IR 的 "**操作**" 列中的 "**启动**" 按钮来启动 Azure-SSIS IR。 启动加入虚拟网络的 Azure-SSIS IR 需要大约20到30分钟的时间。 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>定义变量

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

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

若要将 Azure-SSIS IR 加入虚拟网络，请运行 `Set-AzDataFactoryV2IntegrationRuntime` 命令： 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
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