---
title: Azure SQL 数据库和数据仓库的专用链接 | Microsoft Docs
description: 专用终结点功能概述
author: rohitnayakmsft
ms.author: rohitna
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 09/17/2019
ms.openlocfilehash: 3adcccde89823f865535c9e1350ac4c3e541eef6
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066255"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse-preview"></a>Azure SQL 数据库和数据仓库的专用链接（预览版）

使用专用链接可以通过**专用终结点**连接到 Azure 中的各种 PaaS 服务。 若要查看支持专用链接功能的 PaaS 服务列表，请参阅[专用链接文档](../private-link/index.yml)页。 专用终结点是特定 [VNet](../virtual-network/virtual-networks-overview.md) 和子网中的专用 IP 地址。 

> [!IMPORTANT]
> 本文适用于 Azure SQL 服务器，同时也适用于在 Azure SQL 服务器中创建的 SQL 数据库和 SQL 数据仓库数据库。 为简单起见，在提到 SQL 数据库和 SQL 数据仓库时，本文统称 SQL 数据库。 本文不适用于 Azure SQL 数据库中的**托管实例**部署。 

## <a name="data-exfiltration-prevention"></a>数据渗透防护

Azure SQL 数据库中的数据渗透是指已获授权的用户（例如数据库管理员）能够从一个系统提取数据，并将其移到组织外部的其他位置或系统。 例如，该用户将数据移到第三方拥有的存储帐户。

假设某个用户在连接到 SQL 数据库的 Azure VM 中运行 SQL Server Management Studio (SSMS)。 此 SQL 数据库位于“美国西部”数据中心。 以下示例演示如何使用网络访问控制来限制通过公共终结点对 SQL 数据库进行访问。

1. 通过将“允许 azure 服务”设置为“关闭”，禁止所有 Azure 服务流量通过公共终结点进入 SQL 数据库。  确保不要在服务器和数据库级防火墙规则中允许任何 IP 地址。 有关详细信息，请参阅 [Azure SQL 数据库和数据仓库网络访问控制](sql-database-networkaccess-overview.md)。
1. 仅允许流量使用 VM 的专用 IP 地址进入 SQL 数据库。 有关详细信息，请参阅有关[服务终结点](sql-database-vnet-service-endpoint-rule-overview.md)和 [VNet 防火墙规则](sql-database-firewall-configure.md)的文章。
1. 在 Azure VM 上，按如下所示使用[网络安全组 (NSG)](../virtual-network/manage-network-security-group.md) 和服务标记缩小传出连接的范围
    - 指定一个 NSG 规则以允许服务标记 SQL.WestUs 的流量 - 仅允许连接到“美国西部”的 SQL 数据库
    - 指定一个 NSG 规则（具有**较高的优先级**），以拒绝服务标记 SQL 的流量 - 拒绝连接到所有区域中的 SQL 数据库

完成此设置后，Azure VM 只能连接到“美国西部”区域中的 SQL 数据库。 不过，连接并不局限于单个 SQL 数据库。 VM 仍可连接到“美国西部”区域中的任何 SQL 数据库，包括不在订阅中的数据库。 尽管我们在上述场景中已将数据渗透范围缩小到了特定的区域，但我们并未完全消除这种渗透。

借助专用链接，客户现在可以设置 NSG 等网络访问控制来限制对专用终结点的访问。 然后，将单个 Azure PaaS 资源映射到特定的专用终结点。 恶意内部攻击只能访问映射的 PaaS 资源（例如 SQL 数据库），而不能访问其他资源。 

## <a name="on-premises-connectivity-over-private-peering"></a>通过专用对等互连建立本地连接

当客户从本地计算机连接到公共终结点时，需要使用[服务器级防火墙规则](sql-database-server-level-firewall-rule.md)将其 IP 地址添加到基于 IP 的防火墙。 尽管此模型非常适合用于允许对开发或测试工作负荷的单个计算机进行访问，但在生产环境中却难以管理。

借助专用链接，客户可以使用 [ExpressRoute](../expressroute/expressroute-introduction.md)、专用对等互连或 VPN 隧道实现对专用终结点的跨界访问。 然后，客户可以通过公共终结点禁用所有访问，而无需使用基于 IP 的防火墙来允许任何 IP 地址。

借助专用链接，客户可以使用 Express Route (ER) 专用对等互连或 VPN 隧道实现对专用终结点的跨界访问。然后，他们可以通过公共终结点禁用所有访问权限，而无需使用基于 IP 的防火墙。

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>如何设置 Azure SQL 数据库的专用链接 

### <a name="creation-process"></a>创建过程
可以使用门户、PowerShell 或 Azure CLI 创建专用终结点：
- [门户](../private-link/create-private-endpoint-portal.md)
- [PowerShell](../private-link/create-private-endpoint-powershell.md)
- [CLI](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>审批过程
网络管理员创建专用终结点 (PE) 后，SQL 管理员可以管理与 SQL 数据库建立的专用终结点连接 (PEC)。

1. 在 Azure 门户中导航到 SQL Server 资源。

    - (1) 在左窗格中选择“专用终结点连接”
    - (2) 显示所有专用终结点连接 (PEC) 的列表
    - (3) 创建的相应专用终结点 (PE) ![所有 PEC 的屏幕截图][3]

1. 在列表中选择单个 PEC。
![选定 PEC 的屏幕截图][6]

1. SQL 管理员可以选择批准或拒绝 PEC，并可以选择性地添加简短的文本回复。
![PEC 审批屏幕截图][4]

1. 批准或拒绝后，该列表将反映相应的状态以及回复文本。
![审批后的所有 PEC 的屏幕截图][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Azure SQL 数据库专用链接的用例 

客户端可以通过同一 VNet、同一区域中的对等互连 VNet 或者跨区域的 VNet 到 VNet 连接连接到专用终结点。 此外，客户端可以使用 ExpressRoute、专用对等互连或 VPN 隧道从本地进行连接。 以下简化示意图显示了常见用例。

 ![连接选项示意图][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>测试从同一虚拟网络 (VNet) 中的 Azure VM 连接到 SQL 数据库

此方案假设已创建一个运行 Windows Server 2016 的 Azure 虚拟机 (VM)。 

1. [启动远程桌面 (RDP) 会话并连接到虚拟机](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)。 
1. 然后，可以使用以下工具执行一些基本的连接检查，以确保 VM 通过专用终结点连接到 SQL 数据库：
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>使用 Telnet 检查连接

[Telnet 客户端](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29)是可用于测试连接的 Windows 功能。 根据 Windows OS 的版本，可能需要显式启用此功能。 

安装 Telnet 后，打开命令提示符窗口。 运行 Telnet 命令并指定 SQL 数据库的 IP 地址和专用终结点。

```
>telnet 10.1.1.5 1433
```

当 Telnet 连接成功时，命令窗口中会显示下图所示的空白屏幕：

 ![Telnet 示意图][2]

### <a name="check-connectivity-using-psping"></a>使用 Psping 检查连接

可按如下所示使用 [Psping](/sysinternals/downloads/psping) 检查专用终结点连接 (PEC) 是否正在侦听端口 1433 上的连接。

按如下所示运行 Psping，并提供 SQL 数据库服务器的 FQDN 和端口 1433：

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

输出显示 Psping 可以 ping 通与 PEC 关联的专用 IP 地址。

### <a name="check-connectivity-using-nmap"></a>使用 Nmap 检查连接

Nmap（网络映射器）是一个用于网络发现和安全审核的免费开源工具。 有关详细信息和下载链接，请访问 https://nmap.org 。可以使用此工具来确保专用终结点侦听端口 1433 上的连接。

按如下所示运行 Nmap，并提供托管专用终结点的子网的地址范围。

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

结果显示，一个对应于专用终结点 IP 地址的 IP 地址已启动。


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>使用 SQL Server Management Studio (SSMS) 检查连接

最后一步是[使用 SSMS 连接到 SQL 数据库](sql-database-connect-query-ssms.md)。 使用 SSMS 连接到 SQL 数据库后，请运行以下查询，验证是否正在从 Azure VM 的专用 IP 地址进行连接：

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````
> [!NOTE]
> 在预览版中，与专用终结点建立的连接仅支持使用“代理”作为[连接策略](sql-database-connectivity-architecture.md#connection-policy) 


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>从对等互连虚拟网络 (VNet) 中的 Azure VM 进行连接 

配置 [VNet 对等互连](../virtual-network/tutorial-connect-virtual-networks-powershell.md)，以便从对等互连 VNet 中的 Azure VM 来与 SQL 数据库建立连接。

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>从 VNet 到 VNet 环境中的 Azure VM 进行连接

配置 [VNet 到 VNet VPN 网关连接](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)，以便从另一区域或订阅中的 Azure VM 来与 SQL 数据库建立连接。

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>通过 VPN 从本地环境进行连接

若要建立从本地环境到 SQL 数据库的连接，请选择并实施以下选项之一：
- [点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [站点到站点 VPN 连接](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>使用 Polybase 从 Azure SQL 数据仓库连接到 Azure 存储

PolyBase 通常用于将数据从 Azure 存储帐户加载到 Azure SQL 数据仓库中。 如果要从中加载数据的 Azure 存储帐户仅允许通过专用终结点、服务终结点或基于 IP 的防火墙访问一组 VNet 子网，则通过 PolyBase 与该帐户建立的连接将会断开。 对于连接到 Azure 存储（已通过安全方式连接到 VNet）的 Azure SQL 数据仓库，若要启用 PolyBase 导入和导出方案，请遵循[此处](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)提供的步骤。 



## <a name="next-steps"></a>后续步骤

- 有关 Azure SQL 数据库安全概述，请参阅[保护数据库](sql-database-security-overview.md)
- 有关 Azure SQL 数据库连接的概述，请参阅 [Azure SQL 连接体系结构](sql-database-connectivity-architecture.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
