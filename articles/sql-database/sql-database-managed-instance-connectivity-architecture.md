---
title: Azure SQL 数据库托管实例连接体系结构 | Microsoft Docs
description: 本文简要介绍了 Azure SQL 数据库托管实例通信，讲解了连接体系结构，还阐述了不同的组件函数如何将流量定向到托管实例。
keywords: ''
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.subservice: managed instance
ms.custom: ''
ms.date: 08/16/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.topic: conceptual
ms.openlocfilehash: 54917c6548c7f0bfacad6408732c5619e6346683
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2018
ms.locfileid: "40177444"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Azure SQL 数据库托管实例连接体系结构 

本文简要介绍了 Azure SQL 数据库托管实例通信，讲解了连接体系结构，还阐述了不同的组件函数如何将流量定向到托管实例。  

## <a name="communication-overview"></a>通信概述 

以下关系图演示了连接到托管实例的实体以及托管实例为正常工作而必须传输出的资源。 

![连接体系结构实体](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

关系图底部显示的通信表示作为数据源连接到托管实例的客户应用程序和工具。  

由于托管实例是一种服务即平台 (PaaS)，因此 Microsoft 基于遥测数据流使用自动化代理来管理该服务（管理、部署和维护）。 由于托管实例管理完全由 Microsoft 负责，客户将无法通过 RDP 访问托管实例虚拟群集机计算机。 

某些由最终用户或应用程序启动的 SQL Server 操作可能需要托管实例来与平台进行交互。 一种情况是创建托管实例数据库，这是一种通过门户、PowerShell 和 Azure CLI 公开的资源。 

托管实例依赖其他 Azure 服务才能正常运行（例如使用 Azure 存储进行备份、使用 Azure 服务总线进行遥测、使用 Azure AD 进行身份验证、使用 Azure Key Vault 进行 TDE），并相应地启动到这些服务的连接。 

上述的所有通信都使用证书进行加密和签名。 要确保通信方受信任，托管实例需不断地通过联系证书颁发机构来验证这些证书。 如果证书被撤销或托管实例无法验证它们，则会关闭连接以保护数据。 

## <a name="high-level-connectivity-architecture"></a>高级连接体系结构 

在较高级别，托管实例是一组服务组件，托管在一组专用的、在客户虚拟网络子网中运行并形成虚拟集群的独立虚拟机上。 

多个托管示例可托管在单个虚拟群集中。 当客户更改子网中已预配的实例数时，群集可根据需要自动增加或减少。 

客户应用程序只有使用具有专用 IP 地址的终结点在虚拟网络、对等虚拟网络或连接了 VPN/快速路由的网络中运行时，才能连接到托管实例、执行查询和更新数据库。  

![连接体系结构关系图](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft 管理和部署服务在虚拟网络外部运行，因此托管实例和 Microsoft 服务之间的连接都要通过使用公共 IP 地址的终结点。 当托管实例创建出站连接时，由于网络地址转换 (NAT)，该实例在接收端看起来似乎来自这个公共 IP。 

管理流量流经客户虚拟网络。 这意味着虚拟网络基础结构的元素会影响并可能损害管理流量，从而导致实例进入错误状态并变得不可用。 

> [!IMPORTANT]
> 为改善客户体验和服务可用性，Microsoft 向可能会影响托管实例正常运行的 Azure 虚拟网络基础结构元素应用网络意向策略。 这是一种向最终用户透明地传递网络需求的平台机制，主要目的是防止网络配置错误并确保托管实例正常操作。 托管实例删除时一并删除网络意向策略。 

## <a name="virtual-cluster-connectivity-architecture"></a>虚拟群集连接体系结构 

让我们更深入地了解托管实例连接体系结构。 以下关系图演示了虚拟群集的概念布局。 

![连接体系结构关系图虚拟群集](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

客户端通过采用 <mi_name>.<clusterid>.database.windows.net 形式的主机名连接到托管实例。 尽管此主机名在公用 DNS 区域中注册且可公开解析，但它仍解析为专用 IP 地址。 

此专用 IP 地址属于将流量定向到托管实例网关 (GW) 的托管实例内部负载均衡器 (ILB)。 由于多个托管实例可能在同一集群中运行，因此 GW 使用托管实例主机名来将流量重新定向到正确的 SQL 引擎服务。 

管理和部署服务使用映射到外部负载均衡器的公共终结点连接到托管实例。 只有在一组预定义的专用于托管实例管理组件的端口上进行接收时，才将流量路由到节点。 管理组件与管理平面之间的所有通信都是相互认证的。 

## <a name="next-steps"></a>后续步骤 

- 相关概述，请参阅 [什么是托管实例](sql-database-managed-instance.md) 
- 有关 VNet 配置的详细信息，请参阅 [托管实例 VNet 配置](sql-database-managed-instance-vnet-configuration.md)。 
- 查看快速入门，了解如何使用以下项创建托管实例： 
  - 通过 [Azure 门户](sql-database-managed-instance-create-tutorial-portal.md) 
  - 使用 [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) 
  - 使用 [Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/) 
  - 使用 [Azure 资源管理器模板（包含 SSMS 的 jumpbox）](https://portal.azure.com/) 

