---
title: "连接到 SQL Server 虚拟机 (Resource Manager) | Microsoft Docs"
description: "了解如何连接到 Azure 中虚拟机上运行的 SQL Server。 本主题使用经典部署模型。 方案根据网络配置和客户端位置的不同而异。"
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: jroth
ms.openlocfilehash: 7285cf47c3a5ec731cd9cfe311053e9d19886f1d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>连接到 Azure 上的 SQL Server 虚拟机

## <a name="overview"></a>概述

本主题介绍如何连接到 Azure 中虚拟机上运行的 SQL Server 实例。 涵盖一些[常规连接方案](#connection-scenarios)，并在门户中提供更改连接设置的相关[步骤](#change)。 如果需要在门户外排除连接故障或配置连接，请参阅主题末尾部分的[手动配置](#manual)。 

如需观看预配和连接的完整演练，请参阅[在 Azure 上预配 SQL Server 虚拟机](virtual-machines-windows-portal-sql-server-provision.md)。

## <a name="connection-scenarios"></a>连接方案

客户端连接虚拟机上运行的 SQL Server 的方式取决于客户端的位置与网络配置。

如果在 Azure 门户中预配 SQL Server VM，则可以选择指定“SQL 连接”的类型。

![预配期间的公共 SQL 连接选项](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

用于连接的选项包括：

| 选项 | 说明 |
|---|---|
| 公共 | 通过 Internet 连接到 SQL Server |
| 专用 | 连接到同一虚拟网络中的 SQL Server |
| 本地 | 在同一虚拟机上本地连接到 SQL Server | 

以下各节详细说明了“公共”和“专用”选项。

## <a name="connect-to-sql-server-over-the-internet"></a>通过 Internet 连接到 SQL Server

如果想要通过 Internet 连接到 SQL Server 数据库引擎，在预配过程中，在门户中为“SQL 连接”类型选择“公共”。 门户将自动执行以下步骤：

* 为 SQL Server 启用 TCP/IP 协议。
* 配置防火墙规则以打开 SQL Server TCP 端口（默认值为 1433）。
* 启用公共访问所需的 SQL Server 身份验证。
* 在 VM 上将网络安全组配置为 SQL Server 端口上的所有 TCP 流量。

> [!IMPORTANT]
> SQL Server Developer Edition 和 Express Edition 的虚拟机映像不会自动启用 TCP/IP 协议。 对于 Developer Edition 和 Express Edition，在创建 VM 后，必须使用 SQL Server 配置管理器[手动启用 TCP/IP 协议](#manualtcp) 。

任何可以访问 Internet 的客户端都可以连接到 SQL Server 实例，只需指定虚拟机的公共 IP 地址或分配到该 IP 地址的任何 DNS 标签即可。 如果 SQL Server 端口为 1433，则不需在连接字符串中进行指定。 以下连接字符串使用 SQL 身份验证（还可以使用公共 IP 地址）连接到具有 DNS 标签 `sqlvmlabel.eastus.cloudapp.azure.com` 的 SQL VM。

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

尽管客户端可通过 Internet 进行连接，但这并不意味着任何人都可以连接到 SQL Server。 外部客户端必须有正确的用户名和密码。 但是，为了提高安全性，可以不使用 1433 这个众所周知的端口。 例如，如果将 SQL Server 配置为在端口 1500 上进行侦听并制定了适当的防火墙和网络安全组规则，则可将端口号附加到服务器名称上进行连接。 下面的示例通过将自定义端口号 1500 添加到服务器名称，对前一个端口号进行了更改：

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> 在 VM 中通过 Internet 查询 SQL Server 时，Azure 数据中心的所有传出数据都将服从常规[出站数据传输的定价](https://azure.microsoft.com/pricing/details/data-transfers/)。

## <a name="connect-to-sql-server-within-a-virtual-network"></a>在虚拟网络中连接到 SQL Server

在门户中为“SQL 连接”类型选择“专用”时，Azure 会将大多数设置配置为相同的“公共”。 其中一个区别在于，不存在允许 SQL Server 端口（默认值为 1433）上的外部流量的网络安全组规则。

> [!IMPORTANT]
> SQL Server Developer Edition 和 Express Edition 的虚拟机映像不会自动启用 TCP/IP 协议。 对于 Developer Edition 和 Express Edition，在创建 VM 后，必须使用 SQL Server 配置管理器[手动启用 TCP/IP 协议](#manualtcp) 。

专用连接通常与[虚拟网络](../../../virtual-network/virtual-networks-overview.md)结合使用，从而启用多个方案。 可以连接同一虚拟网络中的 VM，即使这些 VM 位于不同的资源组中。 使用[站点到站点 VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)，可以创建连接 VM 与本地网络和计算机的混合体系结构。

虚拟网络还可将 Azure VM 加入域。 这是对 SQL Server 使用 Windows 身份验证的唯一方式。 其他连接方案需要使用用户名和密码进行 SQL 身份验证。

假设已在虚拟网络中配置 DNS，则可在连接字符串中指定 SQL Server VM 计算机名来连接 SQL Server 实例。 以下示例还假设同时已配置 Windows 身份验证，并且用户已获得访问 SQL Server 实例的权限。

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a>更改 SQL 连接设置

可以更改 Azure 门户中的 SQL Server 虚拟机的连接设置。

1. 在 Azure 门户中，选择“虚拟机”。

2. 选择 SQL Server VM。

3. 在“设置”下，单击“SQL Server 配置”。

4. 将“SQL 连接级别”更改为所需设置。 可以选择性使用此区域来更改 SQL Server 端口或 SQL 身份验证设置。

   ![更改 SQL 连接性](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. 请等待几分钟时间以完成更新。

   ![SQL VM 更新通知](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a>为 Developer Edition 和 Express Edition 启用 TCP/IP

更改 SQL Server 连接性设置时，Azure 不会为 SQL Server Developer Edition 和 Express Edition 自动启用 TCP/IP 协议。 以下步骤说明了如何手动启用 TCP/IP，以便通过 IP 地址进行远程连接。

首先，通过远程桌面连接到 SQL Server 计算机。

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

接下来，通过“SQL Server 配置管理器”启用 TCP/IP 协议。

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>使用 SSMS 进行连接

以下步骤演示如何为 Azure VM 创建可选 DNS 标签，然后与 SQL Server Management Studio (SSMS) 进行连接。

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a> 手动配置和故障排除

尽管门户提供自动配置连接的选项，但了解如何手动配置连接也非常重要。 了解相关要求也有助于进行故障排除。

下表列出了连接到在 Azure VM 中运行的 SQL Server 的要求。

| 要求 | 说明 |
|---|---|
| [启用 SQL Server 身份验证模式](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode#SSMSProcedure) | 除非已在虚拟网络上配置 Active Directory，否则需要进行 SQL Server 身份验证才能连接到远程 VM。 |
| [创建 SQL 登录名](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | 如果使用的是 SQL 身份验证，则需要提供 SQL 登录名，并且用户名和密码还有权访问目标数据库。 |
| [启用 TCP/IP 协议](#manualTCP) | SQL Server 必须允许通过 TCP 连接。 |
| [启用 SQL Server 端口的防火墙规则](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | VM 上的防火墙必须允许 SQL Server 端口（默认为 1433）上的入站流量。 |
| [创建 TCP 1433 的网络安全组规则](../../../virtual-network/virtual-networks-create-nsg-arm-pportal.md#create-rules-in-an-existing-nsg) | 如果希望通过 Internet 连接，必须允许 VM 接收 SQL Server 端口（默认为 1433）上的流量。 本地和仅虚拟网路连接对此无要求。 这是在 Azure 门户中所要求的唯一步骤。 |

> [!TIP]
> 在门户中配置连接时，已为你完成上表中的步骤。 只需使用这些步骤来确认配置或手动为 SQL Server 设置连接。

## <a name="next-steps"></a>后续步骤

若要查看预配说明以及这些连接步骤，请参阅[在 Azure 上预配 SQL Server 虚拟机](virtual-machines-windows-portal-sql-server-provision.md)。

有关其他与在 Azure VM 中运行 SQL Server 相关的主题，请参阅 [SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md)（Azure 虚拟机上的 SQL Server）。