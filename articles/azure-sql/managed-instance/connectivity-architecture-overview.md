---
title: 连接体系结构
titleSuffix: Azure SQL Managed Instance
description: 了解 Azure SQL 托管实例的通信和连接体系结构，同时了解组件如何将流量定向到托管实例。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 03/17/2020
ms.openlocfilehash: 81d0731f6ea77325b3f33f91bf8d5d1386dab2fb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91283371"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Azure SQL 托管实例的连接体系结构
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍 Azure SQL 托管实例中的通信。 此外还将介绍连接体系结构，以及组件将流量定向到托管实例的方式。  

SQL 托管实例位于 Azure 虚拟网络和托管实例专用子网中。 此部署提供：

- 安全的专用 IP 地址。
- 将本地网络连接到 SQL 托管实例的能力。
- 将 SQL 托管实例连接到链接服务器或其他本地数据存储的能力。
- 将 SQL 托管实例连接到 Azure 资源的能力。

## <a name="communication-overview"></a>通信概述

下图显示了连接到 SQL 托管实例的实体。 它还显示了需要与托管实例通信的资源。 关系图底部的通信过程表示作为数据源连接到 SQL 托管实例的客户应用程序和工具。  

![连接体系结构中的实体](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL 托管实例是一种平台即服务 (PaaS) 产品。 Azure 使用自动化代理（管理、部署和维护）基于遥测数据流管理此服务。 由于管理工作由 Azure 负责，客户无法通过远程桌面协议 (RDP) 访问 SQL 托管实例的虚拟群集计算机。

某些由最终用户或应用程序发起的操作可能需要 SQL 托管实例来与平台进行交互。 一种情况是创建 SQL 托管实例数据库。 此资源是通过 Azure 门户、PowerShell、Azure CLI 和 REST API 公开的。

SQL 托管实例依赖于 Azure 服务，例如用于备份的 Azure 存储、用于遥测的 Azure 事件中心、用于身份验证的 Azure Active Directory (Azure AD)、用于透明数据加密 (TDE) 的 Azure Key Vault，以及提供安全性和可支持性功能的几项 Azure 平台服务。 SQL 托管实例与这些服务建立连接。

所有通信都使用证书进行加密和签名。 为了检查通信方的可信度，SQL 托管实例会通过证书吊销列表不断验证这些证书。 如果证书被吊销，SQL 托管实例将关闭连接以保护数据。

## <a name="high-level-connectivity-architecture"></a>高级连接体系结构

在较高级别，SQL 托管实例是一组服务组件。 这些组件托管在客户虚拟网络子网中运行的一组专用隔离虚拟机上。 这些计算机构成了虚拟群集。

一个虚拟群集可以承载多个托管实例。 当客户更改子网中预配的实例数时，群集可根据需要自动扩展或收缩。

客户应用程序可连接到 SQL 托管实例，还可查询和更新虚拟网络、对等虚拟网络或通过 VPN 或 Azure ExpressRoute 连接的网络中的数据库。 此网络必须使用一个终结点和一个专用 IP 地址。  

![连接体系结构示意图](./media/connectivity-architecture-overview/connectivityarch002.png)

Azure 管理和部署服务在虚拟网络外部运行。 SQL 托管实例和 Azure 服务通过采用公共 IP 地址的终结点进行连接。 当 SQL 托管实例创建出站连接时，在接收端，网络地址转换 (NAT) 使该连接看起来像来自此公共 IP 地址的连接。

管理流量通过客户的虚拟网络传送。 这意味着，虚拟网络基础结构的要素可能会使实例发生故障并变得不可用，从而对管理流量造成不利影响。

> [!IMPORTANT]
> 为改善客户体验和服务可用性，Azure 会针对 Azure 虚拟网络基础结构要素应用网络意向策略。 该策略可影响 SQL 托管实例的工作方式。 此平台机制以透明方式向用户传达网络要求。 该策略的主要目的是防止网络配置不当，并确保 SQL 托管实例正常运行。 删除某个托管实例时，会一并删除网络意向策略。

## <a name="virtual-cluster-connectivity-architecture"></a>虚拟群集连接体系结构

让我们更深入地了解 SQL 托管实例的连接体系结构。 以下关系图演示了虚拟群集的概念布局。

![虚拟群集的连接体系结构](./media/connectivity-architecture-overview/connectivityarch003.png)

客户端使用 `<mi_name>.<dns_zone>.database.windows.net` 格式的主机名连接到 SQL 托管实例。 此主机名将解析为专用 IP 地址，不过它将在公共域名系统 (DNS) 区域中注册，且可公开解析。 `zone-id` 是创建群集时自动生成的。 如果新创建的群集托管辅助托管实例，它会将其区域 ID 与主群集共享。 有关详细信息，请参阅[使用自动故障转移组可以实现多个数据库的透明、协调式故障转移](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets)。

此专用 IP 地址属于 SQL 托管实例的内部负载均衡器。 该负载均衡器将流量定向到 SQL 托管实例网关。 由于多个托管实例可在同一群集中运行，因此网关使用 SQL 托管实例主机名来将流量重新定向到正确的 SQL 引擎服务。

管理和部署服务使用映射到外部负载均衡器的[管理终结点](#management-endpoint)连接到 SQL 托管实例。 只有在仅由 SQL 托管实例的管理组件使用的预定义端口上接收到流量时，才会将流量路由到节点。 节点上的内置防火墙设置为只允许来自 Microsoft IP 范围的流量。 证书将对管理组件与管理平面之间的所有通信进行相互身份验证。

## <a name="management-endpoint"></a>管理终结点

Azure 使用一个管理终结点来管理 SQL 托管实例。 此终结点位于该实例的虚拟群集内部。 管理终结点在网络级别受到内置防火墙的保护。 在应用程序级别，管理终结点受到证书相互验证的保护。 若要查找终结点的 IP 地址，请参阅[确定管理终结点的 IP 地址](management-endpoint-find-ip-address.md)。

在 SQL 托管实例内开始连接时（与备份和审核日志一样），流量似乎从管理终结点的公共 IP 地址开始。 可通过将防火墙规则设置为只允许 SQL 托管实例的 IP 地址，来限制从 SQL 托管实例访问公共服务。 有关详细信息，请参阅[验证 SQL 托管实例的内置防火墙](management-endpoint-verify-built-in-firewall.md)。

> [!NOTE]
> 发往 SQL 托管实例所在区域中的 Azure 服务的流量经过优化，因此不会通过 NAT 转换为管理终结点公共 IP 地址。 因此，如果需要使用基于 IP 的防火墙规则（往往用于存储），服务需要与 SQL 托管实例位于不同的区域中。

## <a name="service-aided-subnet-configuration"></a>服务辅助的子网配置

为了满足客户的安全性和管理性要求，SQL 托管实例即将从手动配置转换为服务辅助的子网配置。

使用服务辅助的子网配置时，用户可完全控制数据 (TDS) 流量，而 SQL 托管实例将负责确保管理流量不间断流动，以满足 SLA 的规定。

服务辅助子网配置以虚拟网络[子网委派](../../virtual-network/subnet-delegation-overview.md)功能为基础，可提供自动网络配置管理和启用服务终结点。 

服务终结点可用于对保存备份和审核日志的存储帐户配置虚拟网络防火墙规则。 即使已启用服务终结点，也鼓励客户使用通过服务终结点提供附加安全的 [专用链接](../../private-link/private-link-overview.md) 。

> [!IMPORTANT]
> 由于控制平面配置 specificities，服务辅助子网配置不会在国家/地区云中启用服务终结点。 

### <a name="network-requirements"></a>网络要求

在虚拟网络中的专用子网内部署 SQL 托管实例。 该子网必须具有以下特征：

- **专用子网：** SQL 托管实例的子网不能包含其他任何关联的云服务，且不能是网关子网。 该子网不能包含除该 SQL 托管实例以外的其他任何资源，你之后也无法在该子网中添加其他类型的资源。
- **子网委派**：需要将 SQL 托管实例的子网委托给 `Microsoft.Sql/managedInstances` 资源提供程序。
- **网络安全组 (NSG)** ：NSG 需与 SQL 托管实例的子网相关联。 当 SQL 托管实例配置为使用重定向连接时，可使用某个 NSG 通过筛选端口 1433 和端口 11000-11999 上的流量，来控制对 SQL 托管实例数据终结点的访问。 该服务会自动预配并保留当前的[规则](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)，使管理流量能够不间断地流动。
- **用户定义的路由 (UDR) 表：** UDR 表需与 SQL 托管实例的子网相关联。 可将条目添加到路由表，以通过虚拟网络网关或虚拟网络设备 (NVA) 路由发往本地专用 IP 范围的流量。 服务会自动预配并保留当前的[条目](#user-defined-routes-with-service-aided-subnet-configuration)，使管理流量能够不间断地流动。
- **足够的 IP 地址：** SQL 托管实例子网必须至少有 16 个 IP 地址。 建议的最少数目为 32 个 IP 地址。 有关详细信息，请参阅[确定 SQL 托管实例的子网大小](vnet-subnet-determine-size.md)。 根据 [SQL 托管实例的网络要求](#network-requirements)配置托管实例后，可将其部署在[现有网络](vnet-existing-add-subnet.md)中。 否则，请创建[新的网络和子网](virtual-network-subnet-create-arm-template.md)。

> [!IMPORTANT]
> 创建托管实例时，将会针对子网应用网络意向策略，以防止对网络设置进行不合规的更改。 从子网中删除最后一个实例后，网络意向策略也会一并删除。

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>采用服务辅助子网配置的必需入站安全规则

| 名称       |端口                        |协议|源           |目标|操作|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|TCP     |SqlManagement    |MI SUBNET  |允许 |
|            |9000、9003                  |TCP     |CorpnetSaw       |MI SUBNET  |允许 |
|            |9000、9003                  |TCP     |CorpnetPublic    |MI SUBNET  |允许 |
|mi_subnet   |任意                         |任意     |MI SUBNET        |MI SUBNET  |允许 |
|health_probe|任意                         |任意     |AzureLoadBalancer|MI SUBNET  |允许 |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>采用服务辅助子网配置的必需出站安全规则

| 名称       |端口          |协议|源           |目标|操作|
|------------|--------------|--------|-----------------|-----------|------|
|管理  |443、12000    |TCP     |MI SUBNET        |AzureCloud |允许 |
|mi_subnet   |任意           |任意     |MI SUBNET        |MI SUBNET  |允许 |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>采用服务辅助子网配置的用户定义的路由

|名称|地址前缀|下一跃点|
|----|--------------|-------|
|subnet-to-vnetlocal|MI SUBNET|虚拟网络|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
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
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
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
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
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
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

\* MI SUBNET 是指子网的 IP 地址范围，采用 x.x.x.x/y 格式。 可以在 Azure 门户的“子网属性”中找到此信息。

此外，还可以将条目添加到路由表，以通过虚拟网络网关或虚拟网络设备 (NVA) 路由发往本地专用 IP 范围的流量。

如果虚拟网络包含自定义 DNS，则自定义 DNS 服务器必须能够解析公共 DNS 记录。 使用其他功能（例如 Azure AD 身份验证）可能需要解析其他 FQDN。 有关详细信息，请参阅[设置自定义 DNS](custom-dns-configure.md)。

### <a name="networking-constraints"></a>网络约束

**出站连接上会强制实施 TLS 1.2**：2020 年 1 月，Microsoft 对所有 Azure 服务中的服务内流量强制实施了 TLS 1.2。 对于 Azure SQL 托管实例，这导致在用于复制的出站连接上和到 SQL Server 的链接服务器连接上强制实施了 TLS 1.2。 如果对 SQL 托管实例使用低于 2016 版的 SQL Server，请确保已应用[特定于 TLS 1.2 的更新](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)。

SQL 托管实例当前不支持以下虚拟网络功能：

- **Microsoft 对等互连**：如果在与 SQL 托管实例所在的虚拟网络直接或暂时对等互连的 ExpressRoute 线路上启用 [Microsoft 对等互连](../../expressroute/expressroute-faqs.md#microsoft-peering)，会影响虚拟网络内的 SQL 托管实例组件与它依赖的服务之间的流量，从而导致可用性问题。 向已启用 Microsoft 对等互连的虚拟网络部署 SQL 托管实例预计会失败。
- **全局虚拟网络对等互连**：由于[所记录的负载均衡器约束](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)，跨 Azure 区域的[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)连接对 SQL 托管实例不起作用。
- **AzurePlatformDNS**：使用 AzurePlatformDNS [服务标记](../../virtual-network/service-tags-overview.md)阻止平台 DNS 解析会导致 SQL 托管实例不可用。 尽管 SQL 托管实例支持将客户定义的 DNS 用于引擎内的 DNS 解析，但平台操作依赖于平台 DNS。
- **NAT 网关**：使用 [AZURE 虚拟网络 NAT](../../virtual-network/nat-overview.md) 控制与特定公共 IP 地址的出站连接将导致 SQL 托管实例不可用。 SQL 托管实例服务当前仅限于使用基本负载均衡器，该负载均衡器不会通过虚拟网络 NAT 提供入站和出站流的共存。

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>[已弃用] 不采用服务辅助子网配置时的网络要求

在虚拟网络中的专用子网内部署 SQL 托管实例。 该子网必须具有以下特征：

- **专用子网：** SQL 托管实例的子网不能包含其他任何关联的云服务，且不能是网关子网。 该子网不能包含除该 SQL 托管实例以外的其他任何资源，你之后也无法在该子网中添加其他类型的资源。
- **网络安全组 (NSG)** ：与虚拟网络关联的 NSG 必须在其他任何规则的前面定义[入站安全规则](#mandatory-inbound-security-rules)和[出站安全规则](#mandatory-outbound-security-rules)。 当 SQL 托管实例配置为使用重定向连接时，可使用某个 NSG 通过筛选端口 1433 和端口 11000-11999 上的流量，来控制对 SQL 托管实例数据终结点的访问。
- **用户定义的路由 (UDR) 表：** 与虚拟网络关联的 UDR 表必须包含特定的[条目](#user-defined-routes)。
- **没有服务终结点：** 不得将任何服务终结点与 SQL 托管实例的子网相关联。 创建虚拟网络时，请务必禁用“服务终结点”选项。
- **足够的 IP 地址：** SQL 托管实例子网必须至少有 16 个 IP 地址。 建议的最少数目为 32 个 IP 地址。 有关详细信息，请参阅[确定 SQL 托管实例的子网大小](vnet-subnet-determine-size.md)。 根据 [SQL 托管实例的网络要求](#network-requirements)配置托管实例后，可将其部署在[现有网络](vnet-existing-add-subnet.md)中。 否则，请创建[新的网络和子网](virtual-network-subnet-create-arm-template.md)。

> [!IMPORTANT]
> 如果目标子网缺少这些特征，则无法部署新的托管实例。 创建托管实例时，将会针对子网应用网络意向策略，以防止对网络设置进行不合规的更改。 从子网中删除最后一个实例后，网络意向策略也会一并删除。

### <a name="mandatory-inbound-security-rules"></a>强制性入站安全规则

| 名称       |端口                        |协议|源           |目标|操作|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|TCP     |任意              |MI SUBNET  |允许 |
|mi_subnet   |任意                         |任意     |MI SUBNET        |MI SUBNET  |允许 |
|health_probe|任意                         |任意     |AzureLoadBalancer|MI SUBNET  |允许 |

### <a name="mandatory-outbound-security-rules"></a>强制性出站安全规则

| 名称       |端口          |协议|源           |目标|操作|
|------------|--------------|--------|-----------------|-----------|------|
|管理  |443、12000    |TCP     |MI SUBNET        |AzureCloud |允许 |
|mi_subnet   |任意           |任意     |MI SUBNET        |MI SUBNET  |允许 |

> [!IMPORTANT]
> 确保端口 9000、9003、1438、1440 和 1452 只有一个入站规则，端口 443 和 12000 只有一个出站规则。 如果单独为每个端口配置入站和出站规则，则无法通过 Azure 资源管理器部署预配 SQL 托管实例。 如果这些端口位于不同的规则中，则部署将失败，错误代码为 `VnetSubnetConflictWithIntendedPolicy`。

\* MI SUBNET 是指子网的 IP 地址范围，采用 x.x.x.x/y 格式。 可以在 Azure 门户的“子网属性”中找到此信息。

> [!IMPORTANT]
> 尽管所需的入站安全规则允许来自端口 9000、9003、1438、1440 和 1452 上的任意资源的流量，但这些端口受内置防火墙的保护。 有关详细信息，请参阅[确定管理终结点地址](management-endpoint-find-ip-address.md)。

> [!NOTE]
> 如果在 SQL 托管实例中使用事务复制，并且将任何实例数据库用作发布服务器或分发服务器，请在子网的安全规则中打开端口 445（TCP 出站）。 此端口允许访问 Azure 文件共享。

### <a name="user-defined-routes"></a>用户定义的路由

|名称|地址前缀|下一跃点|
|----|--------------|-------|
|subnet_to_vnetlocal|MI SUBNET|虚拟网络|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
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
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
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
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
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
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅 [什么是 Azure SQL 托管实例？](sql-managed-instance-paas-overview.md)。
- 了解如何设置可用于部署 SQL 托管实例的[新的 Azure 虚拟网络](virtual-network-subnet-create-arm-template.md)或[现有 Azure 虚拟网络](vnet-existing-add-subnet.md)。
- 在要部署 SQL 托管实例的位置[计算子网的大小](vnet-subnet-determine-size.md)。
- 了解如何通过以下方式创建托管实例：
  - 通过 [Azure 门户](instance-create-quickstart.md)。
  - 使用 [PowerShell](scripts/create-configure-managed-instance-powershell.md)。
  - 使用 [Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)。
  - 使用 [Azure 资源管理器模板（使用包含 SSMS 的 JumpBox）](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/)。
