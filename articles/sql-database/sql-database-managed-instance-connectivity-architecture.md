---
title: 对于 Azure SQL 数据库中的托管实例的连接体系结构 |Microsoft Docs
description: 了解 Azure SQL 数据库托管实例的通信和连接体系结构如何组件将流量定向到托管实例。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: 801294241f399097d363dd8dc2682f158c0bf2cc
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358286"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>对于 Azure SQL 数据库中的托管实例的连接体系结构

本文介绍 Azure SQL 数据库托管实例中的通信。 它还介绍了连接体系结构以及如何组件将流量定向到托管实例。  

SQL 数据库托管的实例置于 Azure 虚拟网络和专用于托管实例的子网。 此部署提供：

- 安全的专用 IP 地址。
- 能够在本地网络连接到的托管实例。
- 连接到链接的服务器或其他托管的实例的功能的本地数据存储区。
- 能够连接到 Azure 资源的托管的实例。

## <a name="communication-overview"></a>通信概述

下图显示了连接到托管实例的实体。 它还显示与托管实例进行通信所需的资源。 在关系图的底部通信过程表示客户应用程序和数据源以连接到托管实例的工具。  

![连接体系结构中的实体](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

托管的实例是平台即服务 (PaaS) 产品/服务。 Microsoft 使用自动化的代理 （管理、 部署和维护） 来管理此服务基于遥测数据流。 由于 Microsoft 负责管理，客户不能通过远程桌面协议 (RDP) 访问托管的实例群集虚拟机。

由最终用户或应用程序启动的操作可能需要某些 SQL Server 托管实例与平台进行交互。 一种情况下都是托管的实例数据库的创建。 通过 Azure 门户、 PowerShell、 Azure CLI 和 REST API 公开此资源。

托管的实例的透明数据加密 (TDE) 依赖于如备份的 Azure 存储、 遥测数据的 Azure 服务总线、 Azure Active Directory 身份验证和 Azure 密钥保管库的 Azure 服务。 托管的实例建立连接到这些服务。

所有通信都使用证书进行加密和签名。 若要检查的通信方，管理可信度实例将不断地通过联系证书颁发机构验证这些证书。 如果证书被吊销或者无法验证，托管的实例将关闭连接后，若要保护的数据。

## <a name="high-level-connectivity-architecture"></a>高级连接体系结构

在高级别中，托管的实例是一组服务组件。 这些组件托管在一组专用的独立虚拟机的客户的虚拟网络子网内运行。 这些计算机构成虚拟群集。

虚拟群集可以托管多个托管的实例。 如果需要群集将自动会扩大或缩小时客户的子网中预配的实例数更改。

客户应用程序可以连接到托管实例和可以查询和更新数据库仅当它们运行在虚拟网络内对等互连虚拟网络或通过 VPN 或 Azure ExpressRoute 连接的网络。 此网络必须使用一个终结点和一个专用 IP 地址。  

![连接体系结构关系图](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

外部虚拟网络中运行 Microsoft 管理和部署服务。 通过具有公共 IP 地址的终结点连接的托管的实例和 Microsoft 服务。 托管的实例创建时的出站连接，接收端网络地址转换 (NAT) 使连接看起来像它来自此公共 IP 地址。

管理流量流经客户的虚拟网络。 这意味着虚拟网络的基础结构的元素可以实例失败，并且变得不可用，从而损害管理流量。

> [!IMPORTANT]
> 若要改善客户体验和服务可用性，Microsoft 将网络意向策略应用对 Azure 虚拟网络基础结构元素中。 该策略会影响托管的实例的工作原理。 此平台机制以透明方式进行通信，向用户的网络要求。 策略的主要目标是为了防止网络配置不正确，并确保正常的托管的实例操作。 当删除托管的实例时，也会删除网络意向策略。

## <a name="virtual-cluster-connectivity-architecture"></a>虚拟群集连接体系结构

让我们考虑的托管实例的连接体系结构更深入的了解。 以下关系图演示了虚拟群集的概念布局。

![虚拟群集的连接体系结构](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

客户端连接到托管实例使用主机名具有窗体`<mi_name>.<dns_zone>.database.windows.net`。 此主机名解析为专用 IP 地址，尽管它在公共域名系统 (DNS) 区域中注册并且是可公开解析。 `zone-id`在创建群集时自动生成。 如果新创建的群集承载第二个托管的实例，它与主群集共享其区域 ID。 有关详细信息，请参阅[使用自动故障转移组来启用多个数据库的透明和协调故障转移](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)。

此专用 IP 地址属于托管的实例的内部负载均衡器。 负载均衡器将流量定向到托管的实例的网关。 由于多个托管的实例可以运行在同一群集中，网关将使用的托管的实例的主机名来将流量重定向到正确的 SQL 引擎服务。

管理和部署服务通过连接到托管实例[管理终结点](#management-endpoint)映射到一个外部负载均衡器。 仅当一组预定义仅托管的实例的管理组件使用的端口上接收流量路由到的节点。 在节点上内置的防火墙设置以允许仅从 Microsoft IP 范围的流量。 证书相互进行身份验证管理组件和管理平面之间的所有通信。

## <a name="management-endpoint"></a>管理终结点

Microsoft 管理的托管的实例使用的管理终结点。 此终结点是该实例的虚拟群集内。 管理终结点保护的网络级别上的内置防火墙。 在应用程序级别上受到由使用证书进行相互验证。 若要查找终结点的 IP 地址，请参阅[确定管理终结点的 IP 地址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。

当连接开始执行的托管实例中 （如备份和审核日志），流量会显示，若要从管理终结点的公共 IP 地址开始。 您可以设置防火墙规则以允许仅限托管的实例的 IP 地址访问限制的托管实例从公共服务。 有关详细信息，请参阅[验证托管的实例的内置防火墙](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)。

> [!NOTE]
> 在防火墙中为托管实例中启动的连接，与托管的实例的区域内的 Azure 服务具有适用于将这些服务之间的流量的防火墙。

## <a name="network-requirements"></a>网络要求

部署虚拟网络中的专用子网中的托管的实例。 子网必须具有以下特征：

- **专用子网：** 托管的实例子网不能包含具有与其关联的任何其他云服务并且它不能是网关子网。 子网不能包含任何资源，但该托管的实例和更高版本不能将资源添加子网中。
- **网络安全组 (NSG)**：必须定义与虚拟网络关联的 NSG[入站安全规则](#mandatory-inbound-security-rules)并[出站安全规则](#mandatory-outbound-security-rules)之前的任何其他规则。 可以使用 NSG 来控制对该托管的实例的数据终结点访问端口 1433年上筛选流量。
- **用户定义的路由 (UDR) 表：** 与虚拟网络关联的 UDR 表必须包含特定[条目](#user-defined-routes)。
- **服务终结点：** 没有服务终结点应与托管的实例子网相关联。 请确保在创建虚拟网络时，禁用服务终结点选项。
- **足够的 IP 地址：** 托管的实例子网必须具有至少 16 个 IP 地址。 建议的最小为 32 个 IP 地址。 有关详细信息，请参阅[确定托管实例的子网的大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。 你可以部署在托管的实例[现有的网络](sql-database-managed-instance-configure-vnet-subnet.md)配置它以满足之后[的托管实例的网络要求](#network-requirements)。 否则，创建[新的网络和子网](sql-database-managed-instance-create-vnet-subnet.md)。

> [!IMPORTANT]
> 如果目标子网缺少这些特征，则无法部署新的托管的实例。 创建托管的实例时，网络意向策略应用上的子网，以便防止对网络的安装程序不符合要求的更改。 从子网中删除最后一个实例之后，也被删除网络意向策略。

### <a name="mandatory-inbound-security-rules"></a>强制性入站安全规则

| 名称       |端口                        |协议|源           |目标|操作|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|TCP     |任意              |任意        |允许 |
|mi_subnet   |任意                         |任意     |MI SUBNET        |任意        |允许 |
|health_probe|任意                         |任意     |AzureLoadBalancer|任意        |允许 |

### <a name="mandatory-outbound-security-rules"></a>强制性出站安全规则

| 名称       |端口          |协议|源           |目标|操作|
|------------|--------------|--------|-----------------|-----------|------|
|管理  |80、443、12000|TCP     |任意              |AzureCloud  |允许 |
|mi_subnet   |任意           |任意     |任意              |MI SUBNET*  |允许 |

> [!IMPORTANT]
> 确保没有为端口 9000，只有一个入站的规则 9003，为端口 80、 443、 12000 1438年、 1440年、 1452年和一个出站规则。 如果为每个端口分别配置入站和输出的规则，托管实例预配通过 ARM 部署将失败。 如果这些端口是在单独的规则，则部署将失败，错误代码 `VnetSubnetConflictWithIntendedPolicy`

\* MI 子网是指在窗体 10.x.x.x/y 中的子网的 IP 地址范围。 您可以在 Azure 门户中，子网属性中找到此信息。

> [!IMPORTANT]
> 尽管所需的入站的安全规则允许来自的流量_任何_源上的端口 9000，这些端口 9003、 1438年、 1440 和 1452，受内置防火墙。 有关详细信息，请参阅[确定的管理终结点地址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。
> [!NOTE]
> 如果在托管实例中，使用事务复制，并且您使用任何实例数据库作为发布服务器或分发服务器，请在子网的安全规则中打开端口 445(tcp 出站)。 此端口将允许访问 Azure 文件共享。

### <a name="user-defined-routes"></a>用户定义的路由

|名称|地址前缀|下一跃点|
|----|--------------|-------|
|subnet_to_vnetlocal|[mi_subnet]|虚拟网络|
|mi-0-5-next-hop-internet|0.0.0.0/5|Internet|
|mi-11-8-nexthop-internet|11.0.0.0/8|Internet|
|mi-12-6-nexthop-internet|12.0.0.0/6|Internet|
|mi-128-3-nexthop-internet|128.0.0.0/3|Internet|
|mi-16-4-nexthop-internet|16.0.0.0/4|Internet|
|mi-160-5-nexthop-internet|160.0.0.0/5|Internet|
|mi-168-6-nexthop-internet|168.0.0.0/6|Internet|
|mi-172-12-nexthop-internet|172.0.0.0/12|Internet|
|mi-172-128-9-nexthop-internet|172.128.0.0/9|Internet|
|mi-172-32-11-nexthop-internet|172.32.0.0/11|Internet|
|mi-172-64-10-nexthop-internet|172.64.0.0/10|Internet|
|mi-173-8-nexthop-internet|173.0.0.0/8|Internet|
|mi-174-7-nexthop-internet|174.0.0.0/7|Internet|
|mi-176-4-nexthop-internet|176.0.0.0/4|Internet|
|mi-192-128-11-nexthop-internet|192.128.0.0/11|Internet|
|mi-192-160-13-nexthop-internet|192.160.0.0/13|Internet|
|mi-192-169-16-nexthop-internet|192.169.0.0/16|Internet|
|mi-192-170-15-nexthop-internet|192.170.0.0/15|Internet|
|mi-192-172-14-nexthop-internet|192.172.0.0/14|Internet|
|mi-192-176-12-nexthop-internet|192.176.0.0/12|Internet|
|mi-192-192-10-nexthop-internet|192.192.0.0/10|Internet|
|mi-192-9-nexthop-internet|192.0.0.0/9|Internet|
|mi-193-8-nexthop-internet|193.0.0.0/8|Internet|
|mi-194-7-nexthop-internet|194.0.0.0/7|Internet|
|mi-196-6-nexthop-internet|196.0.0.0/6|Internet|
|mi-200-5-nexthop-internet|200.0.0.0/5|Internet|
|mi-208-4-nexthop-internet|208.0.0.0/4|Internet|
|mi-224-3-nexthop-internet|224.0.0.0/3|Internet|
|mi-32-3-nexthop-internet|32.0.0.0/3|Internet|
|mi-64-2-nexthop-internet|64.0.0.0/2|Internet|
|mi-8-7-nexthop-internet|8.0.0.0/7|Internet|
||||

此外，可以将条目添加到路由表将为通过虚拟网络网关或虚拟网络设备 (NVA) 目标已在本地专用 IP 范围的流量路由。

如果虚拟网络中包含的自定义 DNS，添加 Azure 递归解析程序 IP 地址 （例如 168.63.129.16) 的条目。 有关详细信息，请参阅[设置的自定义 DNS](sql-database-managed-instance-custom-dns.md)。 自定义 DNS 服务器必须能够解析这些域和其子域中的主机名称： *microsoft.com*， *windows.net*， *windows.com*， *msocsp.com*， *digicert.com*， *live.com*， *microsoftonline.com*，和*microsoftonline p.com*.

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅 [SQL 数据库内高级数据安全性](sql-database-managed-instance.md)。
- 了解如何[设置新的 Azure 虚拟网络](sql-database-managed-instance-create-vnet-subnet.md)或[现有 Azure 虚拟网络](sql-database-managed-instance-configure-vnet-subnet.md)可在其中部署托管的实例。
- [计算子网的大小](sql-database-managed-instance-determine-size-vnet-subnet.md)你想要部署托管的实例。
- 了解如何创建托管的实例：
  - 通过 [Azure 门户](sql-database-managed-instance-get-started.md)。
  - 通过使用[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)。
  - 通过使用[的 Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)。
  - 通过使用[（使用包含的 SSMS 中使用 JumpBox，） 的 Azure 资源管理器模板](https://portal.azure.com/)。
