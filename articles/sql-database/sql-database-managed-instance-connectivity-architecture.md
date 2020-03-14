---
title: 托管实例的连接体系结构
description: 了解 Azure SQL 数据库托管实例通信和连接体系结构，以及组件如何将流量定向到托管实例。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 04/16/2019
ms.openlocfilehash: ec1430e7dd79378473cce9dbb77bedecd14600c8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256778"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Azure SQL 数据库中的托管实例的连接体系结构

本文介绍 Azure SQL 数据库托管实例中的通信。 它还描述了连接体系结构，以及组件如何将流量定向到托管实例。  

SQL 数据库托管实例位于 Azure 虚拟网络中，而专用于托管实例的子网中。 此部署提供：

- 安全的专用 IP 地址。
- 将本地网络连接到托管实例的功能。
- 将托管实例连接到链接服务器或其他本地数据存储区的能力。
- 将托管实例连接到 Azure 资源的能力。

## <a name="communication-overview"></a>通信概述

下图显示了连接到托管实例的实体。 它还显示了需要与托管实例通信的资源。 关系图底部的通信过程表示作为数据源连接到托管实例的客户应用程序和工具。  

![连接体系结构中的实体](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

托管实例是一种平台即服务（PaaS）产品。 Microsoft 使用自动代理（管理、部署和维护）来管理基于遥测数据流的此服务。 由于 Microsoft 负责管理，客户无法通过远程桌面协议（RDP）访问托管实例虚拟群集计算机。

最终用户或应用程序启动的某些 SQL Server 操作可能需要托管实例与平台交互。 一种情况是创建托管实例数据库。 此资源通过 Azure 门户、PowerShell、Azure CLI 和 REST API 公开。

托管实例依赖于 azure 服务（如用于备份的 azure 存储）、用于遥测的 Azure 事件中心、Azure Active Directory 进行身份验证、Azure Key Vault 透明数据加密（TDE）和几个提供的 Azure 平台服务安全和支持功能。 托管实例与这些服务建立连接。

所有通信均使用证书进行加密和签名。 为了检查通信方的可信度，托管实例通过证书吊销列表持续验证这些证书。 如果证书被吊销，则托管实例会关闭连接以保护数据。

## <a name="high-level-connectivity-architecture"></a>高级连接体系结构

在较高级别，托管实例是一组服务组件。 这些组件承载于在客户的虚拟网络子网中运行的一组专用的独立虚拟机上。 这些计算机构成虚拟群集。

虚拟群集可以托管多个托管实例。 如果需要，当客户更改子网中预配实例的数目时，群集会自动扩展或收缩。

客户应用程序可以连接到托管实例，并可以在虚拟网络、对等互连虚拟网络或通过 VPN 或 Azure ExpressRoute 连接的网络中查询和更新数据库。 此网络必须使用终结点和专用 IP 地址。  

![连接体系结构关系图](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft 管理和部署服务在虚拟网络外部运行。 托管实例和 Microsoft 服务通过具有公共 IP 地址的终结点进行连接。 当托管实例创建出站连接时，在接收端网络地址转换（NAT）上进行的连接看起来就像来自此公共 IP 地址的连接。

管理流量在客户的虚拟网络上流动。 这意味着虚拟网络基础结构的元素可以通过使实例失败并变为不可用来损害管理流量。

> [!IMPORTANT]
> 为了改善客户体验和服务可用性，Microsoft 在 Azure 虚拟网络基础结构元素上应用了网络意向策略。 此策略可能会影响托管实例的工作方式。 此平台机制会以透明方式向用户传达网络要求。 策略的主要目标是防止网络配置错误，并确保正常的托管实例操作。 删除托管实例时，也会删除网络意向策略。

## <a name="virtual-cluster-connectivity-architecture"></a>虚拟群集连接体系结构

让我们更深入地探讨托管实例的连接体系结构。 以下关系图演示了虚拟群集的概念布局。

![虚拟群集的连接体系结构](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

客户端使用格式 `<mi_name>.<dns_zone>.database.windows.net`的主机名连接到托管实例。 虽然此主机名在公共域名系统（DNS）区域中注册并可公开解析，但它会解析为专用 IP 地址。 创建群集时，会自动生成 `zone-id`。 如果新创建的群集托管辅助托管实例，则它会与主群集共享其区域 ID。 有关详细信息，请参阅[使用自动故障转移组启用多个数据库的透明和协调故障转移](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)。

此专用 IP 地址属于托管实例的内部负载均衡器。 负载均衡器会将流量定向到托管实例的网关。 由于可以在同一群集中运行多个托管实例，因此网关使用托管实例的主机名将流量重定向到正确的 SQL 引擎服务。

管理服务和部署服务使用映射到外部负载平衡器的[管理终结点](#management-endpoint)连接到托管实例。 仅当流量在一组预定义的端口上收到时，才会将流量路由到这些节点，这些端口只有托管实例的管理组件使用。 节点上的内置防火墙设置为仅允许来自 Microsoft IP 范围的流量。 证书相互验证管理组件和管理平面之间的所有通信。

## <a name="management-endpoint"></a>管理终结点

Microsoft 使用管理终结点管理托管实例。 此终结点位于实例的虚拟群集内。 管理终结点受网络级别的内置防火墙的保护。 在应用程序级别上，由相互证书验证进行保护。 若要查找终结点的 IP 地址，请参阅[确定管理终结点的 ip 地址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。

当连接在托管实例中启动时（与备份和审核日志一样），流量看起来从管理终结点的公共 IP 地址开始。 您可以通过将防火墙规则设置为仅允许托管实例的 IP 地址，限制对托管实例的公共服务的访问。 有关详细信息，请参阅[验证托管实例的内置防火墙](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)。

> [!NOTE]
> 转到托管实例区域内的 Azure 服务的流量经过优化，因此无法/Nsg/acl 托管实例管理终结点公共 IP 地址。 出于此原因，如果需要使用基于 IP 的防火墙规则，最常见的是存储，则服务需要与托管实例位于不同的区域中。

## <a name="service-aided-subnet-configuration"></a>服务辅助子网配置

为了满足客户的安全和可管理性要求托管实例从手动转换到服务辅助子网配置。

使用服务辅助子网配置用户可以完全控制数据（TDS）流量，同时托管实例负责确保管理流量不间断流动，以便满足 SLA。

### <a name="network-requirements"></a>网络要求 

在虚拟网络中的专用子网中部署托管实例。 子网必须具有以下特征：

- **专用子网：** 托管实例的子网不能包含与其关联的任何其他云服务，也不能是网关子网。 子网不能包含任何资源，而不能包含托管实例，你稍后无法在子网中添加其他类型的资源。
- **子网委托：** 需要将托管实例的子网委托给 `Microsoft.Sql/managedInstances` 资源提供程序。
- **网络安全组（NSG）：** NSG 需要与托管实例的子网相关联。 可以使用 NSG 通过筛选端口1433和端口11000-11999 上的流量来控制对托管实例的数据终结点的访问。 服务将自动添加允许不间断的管理流量流所需的[规则](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)。
- **用户定义的路由（UDR）表：** UDR 表需要与托管实例的子网相关联。 你可以向路由表添加条目，以通过虚拟网络网关或虚拟网络设备（NVA）将具有本地专用 IP 范围的流量路由为目标。 服务将自动添加允许中断管理流量所需的[条目](#user-defined-routes-with-service-aided-subnet-configuration)。
- **服务终结点：** 服务终结点可用于配置保存备份/审核日志的存储帐户的虚拟网络规则。
- **足够的 IP 地址：** 托管实例子网必须具有至少16个 IP 地址。 建议的最小值为 32 IP 地址。 有关详细信息，请参阅[确定托管实例的子网大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。 您可以在配置[现有网络](sql-database-managed-instance-configure-vnet-subnet.md)中的托管实例后，将其部署为满足[托管实例的网络要求](#network-requirements)。 否则，请创建[新的网络和子网](sql-database-managed-instance-create-vnet-subnet.md)。

> [!IMPORTANT]
> 创建托管实例时，会在子网上应用网络意向策略，以防止对网络设置的不符合要求的更改。 从子网中删除最后一个实例后，也会删除网络意向策略。

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>具有服务辅助子网配置的强制入站安全规则 

| 名称       |端口                        |协议|源           |目标|操作|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|TCP     |SqlManagement    |MI SUBNET  |Allow |
|            |9000、9003                  |TCP     |CorpnetSaw       |MI SUBNET  |Allow |
|            |9000、9003                  |TCP     |65.55.188.0/24、167.220.0.0/16、131.107.0.0/16、94.245.87.0/24|MI SUBNET  |Allow |
|mi_subnet   |Any                         |Any     |MI SUBNET        |MI SUBNET  |Allow |
|health_probe|Any                         |Any     |AzureLoadBalancer|MI SUBNET  |Allow |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>具有服务辅助子网配置的强制出站安全规则 

| 名称       |端口          |协议|源           |目标|操作|
|------------|--------------|--------|-----------------|-----------|------|
|管理  |443、12000    |TCP     |MI SUBNET        |AzureCloud |Allow |
|mi_subnet   |Any           |Any     |MI SUBNET        |MI SUBNET  |Allow |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>具有服务辅助子网配置的用户定义路由 

|名称|地址前缀|下一个跃点|
|----|--------------|-------|
|vnetlocal 的子网|MI SUBNET|虚拟网络|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-12-15-nexthop-internet|51.12.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|Internet|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
||||

\* MI 子网是指采用 x.x.x.x/y 格式的子网的 IP 地址范围。 可以在 Azure 门户的 "子网属性" 中找到此信息。

此外，还可以向路由表添加条目，以通过虚拟网络网关或虚拟网络设备（NVA）将具有本地专用 IP 范围的流量路由为目标。

如果虚拟网络包括自定义 DNS，则自定义 DNS 服务器必须能够解析公共 dns 记录。 使用其他功能（如 Azure AD 身份验证）可能需要解析其他 Fqdn。 有关详细信息，请参阅[设置自定义 DNS](sql-database-managed-instance-custom-dns.md)。

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>弃用无服务辅助子网配置的网络要求

在虚拟网络中的专用子网中部署托管实例。 子网必须具有以下特征：

- **专用子网：** 托管实例的子网不能包含与其关联的任何其他云服务，也不能是网关子网。 子网不能包含任何资源，而不能包含托管实例，你稍后无法在子网中添加其他类型的资源。
- **网络安全组（NSG）：** 与虚拟网络关联的 NSG 必须在任何其他规则之前定义[入站安全规则](#mandatory-inbound-security-rules)和[出站安全规则](#mandatory-outbound-security-rules)。 可以使用 NSG 通过筛选端口1433和端口11000-11999 上的流量来控制对托管实例的数据终结点的访问。
- **用户定义的路由（UDR）表：** 与虚拟网络关联的 UDR 表必须包含特定[项](#user-defined-routes)。
- **无服务终结点：** 不应将服务终结点与托管实例的子网相关联。 请确保在创建虚拟网络时禁用了 "服务终结点" 选项。
- **足够的 IP 地址：** 托管实例子网必须具有至少16个 IP 地址。 建议的最小值为 32 IP 地址。 有关详细信息，请参阅[确定托管实例的子网大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。 您可以在配置[现有网络](sql-database-managed-instance-configure-vnet-subnet.md)中的托管实例后，将其部署为满足[托管实例的网络要求](#network-requirements)。 否则，请创建[新的网络和子网](sql-database-managed-instance-create-vnet-subnet.md)。

> [!IMPORTANT]
> 如果目标子网缺少这些特征，则无法部署新的托管实例。 创建托管实例时，会在子网上应用网络意向策略，以防止对网络设置的不符合要求的更改。 从子网中删除最后一个实例后，也会删除网络意向策略。

### <a name="mandatory-inbound-security-rules"></a>强制性入站安全规则

| 名称       |端口                        |协议|源           |目标|操作|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|TCP     |Any              |MI SUBNET  |Allow |
|mi_subnet   |Any                         |Any     |MI SUBNET        |MI SUBNET  |Allow |
|health_probe|Any                         |Any     |AzureLoadBalancer|MI SUBNET  |Allow |

### <a name="mandatory-outbound-security-rules"></a>强制性出站安全规则

| 名称       |端口          |协议|源           |目标|操作|
|------------|--------------|--------|-----------------|-----------|------|
|管理  |443、12000    |TCP     |MI SUBNET        |AzureCloud |Allow |
|mi_subnet   |Any           |Any     |MI SUBNET        |MI SUBNET  |Allow |

> [!IMPORTANT]
> 确保端口9000、9003、1438、1440、1452和端口 "443" 的出站规则只有一个入站规则。 如果为每个端口单独配置入站和出站规则，则通过 Azure 资源管理器部署托管实例预配会失败。 如果这些端口在不同的规则中，部署将失败，并出现错误代码 `VnetSubnetConflictWithIntendedPolicy`

\* MI 子网是指采用 x.x.x.x/y 格式的子网的 IP 地址范围。 可以在 Azure 门户的 "子网属性" 中找到此信息。

> [!IMPORTANT]
> 虽然必需的入站安全规则允许来自端口9000、9003、1438、1440和1452的_任何_源的流量，但这些端口受内置防火墙保护。 有关详细信息，请参阅[确定管理终结点地址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。
> [!NOTE]
> 如果在托管实例中使用事务复制，且使用任何实例数据库作为发布服务器或分发服务器，请在子网的安全规则中打开端口445（TCP 出站）。 此端口将允许访问 Azure 文件共享。

### <a name="user-defined-routes"></a>用户定义的路由

|名称|地址前缀|下一个跃点|
|----|--------------|-------|
|subnet_to_vnetlocal|MI SUBNET|虚拟网络|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-96-13-nexthop-internet|13.96.0.0/13|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-8-nexthop-internet|20.0.0.0/8|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-8-nexthop-internet|51.0.0.0/8|Internet|
|mi-52-8-nexthop-internet|52.0.0.0/8|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-22-nexthop-internet|103.25.156.0/22|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-253-16-nexthop-internet|131.253.0.0/16|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|Internet|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
||||

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅 [SQL 数据库高级数据安全性](sql-database-managed-instance.md)。
- 了解如何[设置新的 azure 虚拟网络](sql-database-managed-instance-create-vnet-subnet.md)或[现有的 azure 虚拟网络](sql-database-managed-instance-configure-vnet-subnet.md)，你可以在其中部署托管实例。
- 计算要在其中部署托管实例的[子网的大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。
- 了解如何创建托管实例：
  - 通过 [Azure 门户](sql-database-managed-instance-get-started.md)。
  - 使用[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)。
  - 使用[Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)。
  - 使用[Azure 资源管理器模板（使用 JumpBox，其中包含 SSMS）](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/)。 
