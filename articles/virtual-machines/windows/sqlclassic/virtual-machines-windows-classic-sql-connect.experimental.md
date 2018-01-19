---
title: "连接到 Azure 上的 SQL Server 虚拟机（经典部署）| Microsoft Docs"
description: "了解如何连接到 Azure 中虚拟机上运行的 SQL Server。 本主题使用经典部署模型。 方案根据网络配置和客户端位置的不同而异。"
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 67b328cb754e49fe1dea9d57f74dd31793acd93c
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>连接到 Azure 上的 SQL Server 虚拟机（经典部署）
> [!div class="op_single_selector"]
> * [资源管理器](../sql/virtual-machines-windows-sql-connect.md)
> * [经典](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>概述
本主题介绍如何连接到 Azure 中虚拟机上运行的 SQL Server 实例。 介绍一些[常规连接方案](#connection-scenarios)，并提供[在 Azure VM 中配置 SQL Server 连接的详细步骤](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)。

> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[资源管理器和经典模型](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 如果使用的是 Resource Manager VM，请参阅[使用 Resource Manager 连接到 Azure 上的 SQL Server 虚拟机](../sql/virtual-machines-windows-sql-connect.md)。

## <a name="connection-scenarios"></a>连接方案
客户端连接虚拟机上运行的 SQL Server 的方式取决于客户端的位置与计算机/网络配置。 这些方案包括：

* [连接到同一云服务中的 SQL Server](#connect-to-sql-server-in-the-same-cloud-service)
* [通过 Internet 连接到 SQL Server](#connect-to-sql-server-over-the-internet)
* [连接到同一虚拟网络中的 SQL Server](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> 使用下列任一方法进行连接之前，必须遵循[本文中的步骤配置连接](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)。
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>连接到同一云服务中的 SQL Server
可以在同一云服务中创建多个虚拟机。 要了解此虚拟机方案，请参阅[如何将虚拟机连接到虚拟网络或云服务](../classic/connect-vms.md#connect-vms-in-a-standalone-cloud-service)。 本方案介绍一台虚拟机上的客户端尝试连接到在同一云服务中另一虚拟机上运行的 SQL Server 时的情况。

在此方案中，可使用 VM **名称**（在门户中也显示为**计算机名**或**主机名**）连接。 这是你在创建 VM 时为其提供的名称。 例如，如果将 SQL VM 命名为 **mysqlvm**，则同一云服务中的客户端 VM 可以使用以下连接字符串连接：

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>通过 Internet 连接到 SQL Server
如果想要通过 Internet 连接到 SQL Server 数据库引擎，则必须创建虚拟机终结点以进行传入 TCP 通信。 此 Azure 配置步骤将传入 TCP 端口通信定向到虚拟机可以访问的 TCP 端口。

若要通过 Internet 进行连接，必须使用 VM 的 DNS 名称和（本文中稍后配置的）VM 终结点端口号。 要查找 DNS 名称，请导航到 Azure 门户，并选择“虚拟机(经典)”。 然后选择虚拟机。 “DNS 名称”显示在“概述”部分。

例如，假设一个名为 **mysqlvm** 的经典虚拟机，其 DNS 名称为 **mysqlvm7777.cloudapp.net**，VM 终结点为 **57500**。 假设正确配置了连接性，则可从 Internet 上的任意位置使用以下连接字符串访问该虚拟机：

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

尽管此连接字符串使客户端可通过 Internet 进行连接，但这并不意味着任何人都可以连接到 SQL Server。 外部客户端必须有正确的用户名和密码。 为了提高安全性，请不要对公共虚拟机终结点使用常用的 1433 端口。 如果可能，请考虑在终结点上添加 ACL 以将流量限制为允许的客户端。 有关在终结点上使用 ACL 的说明，请参阅[管理终结点上的 ACL](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint)。

> [!NOTE]
> 使用此方法与 SQL Server 通信时，Azure 数据中心的所有传出数据都将服从[出站数据传输的定价](https://azure.microsoft.com/pricing/details/data-transfers/)。
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>连接到同一虚拟网络中的 SQL Server
[虚拟网络](../../../virtual-network/virtual-networks-overview.md)支持其他方案。 可以连接同一虚拟网络中的 VM，即使这些 VM 位于不同的云服务中。 使用[站点到站点 VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)，可以创建连接 VM 与本地网络和计算机的混合体系结构。

虚拟网络还可将 Azure VM 加入域。 加入域是结合 SQL Server 使用 Windows 身份验证的唯一方式。 其他连接方案需要使用用户名和密码进行 SQL 身份验证。

如果计划配置域环境和 Windows 身份验证，则不需要配置公共终结点或 SQL 身份验证和登录。 在此方案中，可以在连接字符串中指定 SQL Server VM 名称以连接 SQL Server 实例。 以下示例假设已配置 Windows 身份验证，并且用户已获得访问 SQL Server 实例的权限。

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>在 Azure VM 中配置 SQL Server 连接的步骤
以下步骤演示如何使用 SQL Server Management Studio (SSMS) 通过 Internet 连接到 SQL Server 实例。 但是，这些步骤同样适用于使 SQL Server 虚拟机可以通过本地和 Azure 中运行的应用程序访问。

必须先完成下列任务，才能从其他 VM 或 Internet 连接到 SQL Server 的实例：

* [为虚拟机创建 TCP 终结点](#create-a-tcp-endpoint-for-the-virtual-machine)
* [在 Windows 防火墙中打开 TCP 端口](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [将 SQL Server 配置为侦听 TCP 协议](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [配置混合模式的 SQL Server 身份验证](#configure-sql-server-for-mixed-mode-authentication)
* [创建 SQL Server 身份验证登录名](#create-sql-server-authentication-logins)
* [确定虚拟机的 DNS 名称](#determine-the-dns-name-of-the-virtual-machine)
* [从其他计算机连接到数据库引擎](#connect-to-the-database-engine-from-another-computer)

下图概述了连接路径：

![连接到 SQL Server 虚拟机](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>后续步骤
如果还打算针对高可用性和灾难恢复使用 AlwaysOn 可用性组，应该考虑实施侦听器。 数据库客户端将连接到侦听器，而不是直接连接到一个 SQL Server 实例。 侦听器将客户端路由到可用性组中的主副本。 有关详细信息，请参阅[在 Azure 中配置 AlwaysOn 可用性组的 ILB 侦听器](../classic/ps-sql-int-listener.md)。

请务必查看 Azure 虚拟机上运行的 SQL Server 的所有最佳安全性实践。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的安全注意事项](../sql/virtual-machines-windows-sql-security.md)。

[探索学习路径](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) ：Azure 虚拟机上的 SQL Server。 

有关其他与在 Azure VM 中运行 SQL Server 相关的主题，请参阅 [SQL Server on Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)（Azure 虚拟机上的 SQL Server）。

