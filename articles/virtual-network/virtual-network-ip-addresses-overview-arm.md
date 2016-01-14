<properties
   pageTitle="在 Azure 资源管理器中实施公共 IP 和专用 IP 寻址 | Microsoft Azure"
   description="了解如何在 Azure 资源管理器中实施公共 IP 和专用 IP 寻址"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
	ms.service="virtual-network"
	ms.date="12/14/2015"
	wacn.date=""/>

# Azure 资源管理器中的 IP 地址
可以将 IP 地址分配给与其他 Azure 资源通信的 Azure 资源，也可以将其分配给本地网络和 Internet。可以在 Azure 中使用两种类型的 IP 地址：公共地址和专用地址。

公共 IP 地址用于与 Internet 通信，这些通信中包括面向公众的 Azure 服务。

专用 IP 地址用于在 Azure 虚拟网络 (VNet) 中通信，以及在本地网络中通信（当你使用 ExpressRoute 线路将网络扩展到 Azure 时）。

[AZURE.INCLUDE [azure-arm-classic-important-include](../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](/documentation/articles/virtual-network-ip-addresses-overview-classic)。

## 公共 IP 地址
分配公共 IP 地址可以让 Azure 资源与 Internet 以及面向公众的 Azure 服务（例如 [Azure Redis 缓存](/home/features/cache)、[Azure 事件中心](/home/features/event-hubs)、[SQL 数据库](/documentation/articles/sql-database-technical-overview)和 [Azure 存储空间](/documentation/articles/storage-introduction)）通信。

在 Azure 资源管理器中，[公共 IP](/documentation/articles/resource-groups-networking#public-ip-address) 地址是具有其自身属性的资源，并且可以与任意下述资源相关联：

- VM
- 面向 Internet 的负载平衡器
- 应用程序网关数

### 分配方法
你可以使用*动态* 或*静态* 公共 IP 地址。在默认的属于*动态* 的分配方法中，IP 地址**不**是在创建过程中分配给公共 IP 资源的。与之相反，公共 IP 资源会在创建或启动与公共 IP 关联的资源时根据所属的 [Azure 位置](https://www.microsoft.com/download/details.aspx?id=41653)来分配 IP 地址。另外，公共 IP 所使用的实际 IP 地址可能会在删除或停止关联的资源时变化。

如果你想确保资源的 IP 地址保持不变，则需将分配方法更改为*静态*。以下情况通常使用静态公共 IP 地址：

- 使用链接到 IP 地址的 SSL 证书。
- 相关资源需要针对外部 Azure 设置防火墙规则。
- 相关资源依赖外部 DNS 名称解析，其中的动态 IP 需要更新 A 记录。
- 相关资源运行其他应用通过 IP 地址访问的服务。

>[AZURE.NOTE]即使将公共 IP 配置为使用静态 IP 地址，也无法指定资源所使用的实际 IP 地址。你的资源将根据其创建时所在的 [Azure 位置](https://www.microsoft.com/download/details.aspx?id=41653)获取 IP 地址。

### DNS 主机名解析
你可以将公共 IP 与 DNS 域名标签相关联，以便在 Azure DNS 服务器中创建相应的 DNS 条目。相应的 FQDN 将采用 *domainnamelabel*.*location*.chinacloudapp.cn 格式，并将关联到与你的资源链接的公共 IP。例如，如果你在名为*中国北方* 的 Azure 区域中创建 *domainnamelabel* 为 **azuretest** 的公共 IP，则关联到该公共 IP 的资源的 FQDN 将为 **azuretest.chinanorth.chinacloudapp.cn**。

>[AZURE.IMPORTANT]所创建的每个域名标签在其 Azure 位置中必须是唯一的。

你可以稍后使用自己的自定义域名（指向 Azure 中使用的 FQDN）创建 CNAME 记录。

### VM
你**不能**直接将公共 IP 分配给 [VM](/documentation/articles/virtual-machines-about)，而只能先创建一个 NIC，然后将公共 IP 关联到该 NIC，再将该 NIC 关联到 VM。虽然可以为 VM 分配多个 NIC，但只有**主** NIC 可以有关联的公共 IP。

你可以向 VM 的主 NIC 分配动态或静态公共 IP 地址。

### 面向 Internet 的负载平衡器
你可以使用面向 Internet 的[负载平衡器](/documentation/articles/load-balancer-overview)，通过单个公共 IP 地址跨多个 VM 来平衡应用程序的负载，同时还可以使用可供普通的第 4 级负载平衡器使用的其他功能。

你可以将公共 IP 地址分配到面向 Internet 的负载平衡器的前端。你可以向面向 Internet 的负载平衡器分配动态或静态公共 IP 地址。

### 应用程序网关数
你可以使用[应用程序网关](/documentation/articles/application-gateway-introduction)通过单个公共 IP 地址跨多个 VM 来平衡应用程序的负载，所用方式与负载平衡器的使用方式类似。但是，应用程序网关也允许你创建路由规则，以便根据 URL 用户请求进一步地跨 VM 分配流量，同时还允许你使用常规的第 7 级负载平衡器所提供的其他功能。

可以将公共 IP 分配到应用程序网关的前端。

### 概览

下表显示哪些资源可以使用动态或静态公共 IP 地址，以及哪些资源可以使用多个公用 IP 地址。

|资源|动态|静态|多个 IP 地址|
|---|---|---|---|
|虚拟机 (VM)/网络接口卡 (NIC)|是|是|否|
|负载平衡器前端|是|是|是|
|应用程序网关前端|是|否|否|

## 专用 IP 地址
分配专用 IP 地址即可让 Azure 资源与 [VNet](/documentation/articles/virtual-networks-overview) 中的其他资源通信，或者通过 ExpressRoute 线路与本地网络通信。你创建的每个 VNet 将使用一系列你所指定的专用 IP 地址。VNet 中的资源必须使用该范围内的专用 IP 地址。

在 Azure 资源管理器中，专用 IP 地址是可以在不同资源中使用的属性。

- 通过网络接口卡 (NIC) 使用的 VM
- 面向 Internet 的负载平衡器
- 内部负载平衡器 (ILB)
- 应用程序网关数 

### 分配方法
你可以使用*动态* 或*静态* 专用 IP 地址。在属于*动态* 方法的默认分配方法中，IP 地址会根据资源所属的子网自动分配给资源。但是，资源所用的 IP 地址可能会在停止和重新启动该资源时发生变化。

如果你想确保资源的 IP 地址不变，则需将分配方法更改为*静态*，并指定一个分配给资源所属子网的地址范围内的有效 IP 地址。

静态专用 IP 地址通常用于：

- 充当 DNS 服务器的 VM。
- 充当域控制器的 VM。
- 需要使用 IP 地址的防火墙规则的 VM。
- 运行其他应用通过 IP 地址进行访问的服务的 VM。

### 内部 DNS 主机名解析
在 Azure 资源之间进行的大多数通信会使用可以人工读取的名称而非 IP 地址来代表资源。该名称称为*主机名*，这是一个网络专业人员通常都会懂的术语。当某个资源尝试使用主机名访问其他资源时，必须将该主机名解析成 IP 地址。此工作通常由 [DNS 服务器](https://technet.microsoft.com/magazine/2005.01.howitworksdns.aspx)来完成。

所有 Azure VM 都使用 [Azure 托管的 DNS 服务器](/documentation/articles/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution)，除非你创建自己的 DNS 服务器并将 VM 配置为使用该服务器。使用 Azure 托管的 DNS 服务器时，将自动创建 DNS 记录，以便将 VM 的主机名解析成 VM 的专用 IP 地址。使用多 NIC VM 时，主机名将解析成主 NIC 的专用 IP 地址。

### VM
你不能直接将专用 IP 分配给 [VM](/documentation/articles/virtual-machines-about)，而只能先创建一个 NIC，然后将该 NIC 关联到 VM。每个 NIC 都有自己的 **privateIPAddress** 属性，该属性必须为 NIC 所分配到的子网设置有效的 IP 地址。VM 可以有[多个 NIC](/documentation/articles/virtual-networks-multiple-nics)。

你可以将动态或静态 IP 地址分配给 VM 所使用的 NIC。

### 面向 Internet 的负载平衡器
你可以使用面向 Internet 的[负载平衡器](/documentation/articles/load-balancer-overview)，通过单个公共 IP 地址跨多个 VM 来平衡应用程序的负载，同时还可以使用可供普通的第 4 级负载平衡器使用的其他功能。

你可以将分配给 VM 的 NIC 关联到负载平衡器的后端池，以便将 VM 用作进行过负载平衡处理的流量的目标。你还可以将 NIC 关联到负载平衡器的入站 NAT 规则，使得所提供的 NAT 规则允许直接访问负载平衡器后面的 VM。

当你将 NIC 关联到面向 Internet 的负载平衡器时，系统会将适用于 NAT 规则或负载平衡器中的负载平衡规则的流量转发到相应 NIC 的专用 IP 地址。

你可以向 NIC（已关联到面向 Internet 的负载平衡器）分配动态或静态 IP 地址。

###内部负载平衡器 (ILB)
你可以使用[内部负载平衡器](/documentation/articles/load-balancer-internal-overview)通过子网中的单个专用 IP 地址跨多个 VM 来平衡应用程序的负载。例如，你可以让多个 VM 托管同一数据库，然后再让来自 VNet 的数据库连接传播到这些 VM。你可以使用内部负载平衡器来执行此操作。

你可以将分配给 VM 的 NIC 关联到负载平衡器的后端池，以便将 VM 用作进行过负载平衡处理的流量的目标，这一点与面向公众的负载平衡器类似。你还可以将 NIC 关联到负载平衡器的入站 NAT 规则，使得所提供的 NAT 规则允许直接访问负载平衡器后面的 VM。你还可以将专用 IP 地址关联到 ILB 的前端。

你可以将动态或静态专用 IP 地址分配到内部负载平衡器的前端和后端 NIC。

###应用程序网关
你可以使用[应用程序网关](/documentation/articles/application-gateway-introduction)通过单个专用 IP 地址跨多个 VM 来平衡应用程序的负载，从而创建一个内部应用程序网关。

你可以将分配给 VM 的 NIC 关联到应用程序网关的后端地址池，以便将 VM 用作进行过负载平衡处理的流量的目标。你可以将动态或静态专用 IP 地址分配给 NIC。

你可以将专用 IP 地址关联到应用程序网关的前端。你可以将动态或静态专用 IP 地址分配给应用程序网关。

### 概览
下表显示哪些资源可以使用动态或静态专用 IP 地址，以及哪些资源可以使用多个专用 IP 地址。

|资源|静态|动态|多个 IP 地址|
|---|---|---|---|
|虚拟机 (VM)/网络接口卡 (NIC)|是|是|是|
|内部负载平衡器前端|是|是|是|
|应用程序网关前端|是|是|是|

## 资源管理器与经典部署之间的差异
如果你熟悉经典部署模型但对资源管理器很陌生，则可查看下面列出的差异，从而快速了解资源管理器中所做的更改。

###专用 IP 地址

|资源|经典|资源管理器|
|---|---|---|
|VM|称为 DIP|称为专用 IP 地址|
||分配给 VM|分配给 NIC|
|ILB|分配给后端地址池|分配给属于后端地址池的 NIC|
|面向 Internet 的负载平衡器|分配给云服务中的 VM 或角色实例|分配给属于后端地址池的 NIC|

###公共 IP 地址

|资源|经典|资源管理器|
|---|---|---|
|VM|称为 ILPIP|称为公共 IP|
||分配给 VM|分配给 NIC|
||只能为动态|可以为静态或动态|
|面向 Internet 的负载平衡器|称为 VIP（动态）或保留 IP（静态）|分配给前端 IP 配置|
||分配给云服务|分配给前端 IP 配置|

## 后续步骤

- [使用静态公共 IP 部署 VM](/documentation/articles/virtual-network-deploy-static-pip-arm-template)
- [使用 Azure CLI 为面向 Internet 的负载平衡器创建公共 IP 地址](/documentation/articles/load-balancer-get-started-internet-arm-cli#create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool)
- [使用 PowerShell 为应用程序网关创建公共 IP 地址](/documentation/articles/application-gateway-create-gateway-arm#create-public-ip-address-for-front-end-configuration)
- [使用静态专用 IP 地址部署 VM](/documentation/articles/virtual-networks-static-private-ip-arm-pportal)
- [使用 PowerShell 为内部负载平衡器创建前端静态专用 IP 地址](/documentation/articles/load-balancer-get-started-ilb-arm-ps#create-front-end-ip-pool-and-backend-address-pool)
- [使用 PowerShell 通过静态专用 IP 地址为应用程序网关创建后端池](/documentation/articles/application-gateway-create-gateway-arm#create-an-application-gateway-configuration-object)

<!---HONumber=Mooncake_0104_2016-->