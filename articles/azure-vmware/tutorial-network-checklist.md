---
title: 教程 - 网络规划清单
description: 了解网络要求先决条件以及有关 Azure VMware 解决方案的网络连接和网络端口的详细信息。
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 5ee70b8a297e1b8418049ff229b3c1869819145b
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948198"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Azure VMware 解决方案的网络规划清单 

Azure VMware 解决方案提供一个 VMware 私有云环境，用户和应用程序可以从本地和基于 Azure 的环境或资源访问该环境。 连接是通过 Azure ExpressRoute 和 VPN 连接等网络服务提供的，需要提供一些特定的网络地址范围和防火墙端口来启用服务。 本文提供正确配置网络以使用 Azure VMware 解决方案所需的信息。

本教程介绍：

> [!div class="checklist"]
> * 虚拟网络和 ExpressRoute 线路注意事项
> * 路由和子网要求
> * 与服务通信所需的网络端口
> * Azure VMware 解决方案中的 DHCP 和 DNS 注意事项

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>虚拟网络和 ExpressRoute 线路注意事项
在订阅中创建虚拟网络连接时，ExpressRoute 线路通过对等互连建立，并使用你在 Azure 门户中请求的授权密钥和对等互连 ID。 对等互连是私有云和虚拟网络之间的一种专用的一对一连接。

> [!NOTE] 
> ExpressRoute 线路不属于私有云部署。 本地 ExpressRoute 线路不在本文档的介绍范围之内。 如果你需要与私有云的本地连接，可使用一个现有的 ExpressRoute 线路或在 Azure 门户中购买一个。

部署私有云时，你将收到 vCenter 和 NSX-T Manager 的 IP 地址。 若要访问这些管理接口，需要在订阅的虚拟网络中创建其他资源。 在本教程中，可找到有关创建这些资源和建立 [ExpressRoute 专用对等互连](tutorial-expressroute-global-reach-private-cloud.md)的过程。

私有云逻辑网络随附提前预配的 NSX-T。 已为你预配了第 0 层网关和第 1 层网关。 可创建一个段，并将其附加到现有的第 1 层网关，或将其附加到所定义的新的第 1 层网关。 NSX-T 逻辑网络组件提供工作负载之间的东-西连接，并提供与 Internet 和 Azure 服务的北-南连接。

## <a name="routing-and-subnet-considerations"></a>路由和子网注意事项
使用 Azure ExpressRoute 连接将 AVS 私有云连接到 Azure 虚拟网络。 这种高带宽、低延迟的连接可让你从私有云环境访问 Azure 订阅中运行的服务。 该路由以边界网关协议 (BGP) 为基础、自动进行预配并且默认对每个私有云部署启用。 

AVS 私有云要求至少对子网使用 `/22` CIDR 网络地址块，如下所示。 此网络将补充你的本地网络。 地址块不应与订阅和本地网络中的其他虚拟网络中使用的地址块重叠。 在此地址块中，管理、配置和 VMotion 网络会自动进行预配。

>[!NOTE]
>地址块的允许范围为 RFC 1918 专用地址空间（10.0.0.0/8、172.16.0.0/12、192.168.0.0/16），172.17.0.0/16 除外。

示例 `/22` CIDR 网络地址块：`10.10.0.0/22`

子网：

| 网络用途             | 子网 | 示例          |
| ------------------------- | ------ | ---------------- |
| 私有云管理  | `/26`  | `10.10.0.0/26`   |
| vMotion 网络           | `/25`  | `10.10.1.128/25` |
| VM 工作负荷              | `/24`  | `10.10.2.0/24`   |
| ExpressRoute 对等互连      | `/29`  | `10.10.3.8/29`   |


## <a name="required-network-ports"></a>所需的网络端口

| 源 | 目标 | 协议 | 端口 | 说明  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| 私有云 DNS 服务器 | 本地 DNS 服务器 | UDP | 53 | DNS 客户端 - 为任何本地 DNS 查询转发来自私有云 vCenter 的请求（查看下面的“DNS”部分） |  
| 本地 DNS 服务器   | 私有云 DNS 服务器 | UDP | 53 | DNS 客户端 - 将来自本地服务的请求转发到私有云 DNS 服务器（查看下面的“DNS”部分） |  
| 本地网络  | 私有云 vCenter 服务器  | TCP(HTTP)  | 80 | vCenter Server 要求对直接 HTTP 连接使用端口 80。 端口 80 将请求重定向到 HTTPS 端口 443。 如果使用 `http://server` 而不是 `https://server`，则这种重定向会很有帮助。  <br><br>WS-Management（还需要打开端口 443） <br><br>如果使用自定义 Microsoft SQL 数据库而不是 vCenter Server 上捆绑的 SQL Server 2008 数据库，则 SQL Reporting Services 将使用端口 80。 安装 vCenter Server 时，安装程序会提示你更改 vCenter Server 的 HTTP 端口。 将 vCenter Server HTTP 端口更改为自定义值可确保成功安装。 Microsoft Internet Information Services (IIS) 也使用端口 80。 请参阅“vCenter Server 与 IIS 之间的端口 80 冲突”。 |  
| 私有云管理网络 | 本地 Active Directory  | TCP  | 389 | 必须在 vCenter Server 的本地实例和所有远程实例上打开此端口。 此端口是 vCenter Server 组的目录服务的 LDAP 端口号。 vCenter Server 系统需要绑定到端口 389，即使不将此 vCenter Server 实例加入链接模式组，也是如此。 如果另一个服务正在此端口上运行，则最好删除此服务，或将其端口更改为其他端口。 可以在从 1025 到 65535 的任何端口上运行 LDAP 服务。  如果此实例充当 Microsoft Windows Active Directory，请将端口号从 389 更改为 1025 到 65535 的可用端口。 此端口是可选的 - 用于将本地 AD 配置为私有云 vCenter 上的标识源。 |  
| 本地网络  | 私有云 vCenter 服务器  | TCP(HTTPS)  | 443 | 可使用此端口从本地网络访问 vCenter。 它是 vCenter Server 系统用于侦听来自 vSphere 客户端的连接的默认端口。 要使 vCenter Server 系统能够从 vSphere 客户端接收数据，请在防火墙中打开端口 443。 vCenter Server 系统还使用端口 443 监视来自 SDK 客户端的数据传输。 此端口还用于以下服务：WS-Management（还需要打开端口 80）。 vSphere 客户端对 vSphere 更新管理器的访问。 第三方网络管理客户端与 vCenter Server 的连接。 第三方网络管理客户端对主机的访问。 |  
| Web 浏览器  | 混合云管理器  | TCP(HTTPS) | 9443 | 用于配置混合云管理器系统的混合云管理器虚拟设备管理界面。 |
| 管理网络  | 混合云管理器 | SSH | 22 | 管理员对混合云管理器的 SSH 访问。 |
| HCM | 云网关 | TCP(HTTPS) | 8123 | 将基于主机的复制服务指令发送到混合云网关。 |
| HCM | 云网关 | HTTP TCP(HTTPS) | 9443 | 使用 REST API 将管理指令发送到本地混合云网关。 |
| 云网关 | L2C | TCP(HTTPS) | 443 | 当 L2C 使用与混合云网关相同的路径时，将管理指令从云网关发送到 L2C。 |
| 云网关 | ESXi 主机 | TCP | 80,902 | 管理和 OVF 部署。 |
| 云网关（本地）| 云网关（远程） | UDP | 4500 | IPSEC 需要此端口<br>   进行 Internet 密钥交换 (IKEv2) 以封装双向隧道的工作负荷。 还支持网络地址转换 (NAT-T)。 |
| 云网关（本地） | 云网关（远程）  | UDP | 500 | IPSEC 需要此端口<br> 双向隧道的 Internet 密钥交换 (ISAKMP)。 |
| 本地 vCenter 网络 | 私有云管理网络 | TCP | 8000 |  从本地 vCenter 到私有云 vCenter 的 VM vMotion   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>DHCP 和 DNS 解析注意事项
在私有云环境中运行的应用程序和工作负载需要使用名称解析和 DHCP 服务来进行查找和 IP 地址分配。 需有适当的 DHCP 和 DNS 基础结构才能提供这些服务。 在私有云环境中，可以配置一个虚拟机来提供这些服务。  

使用内置于 NSX 的 DHCP 服务，或使用私有云中的本地 DHCP 服务器，而不要通过 WAN 将广播 DHCP 流量路由回本地。


## <a name="next-steps"></a>后续步骤

本教程介绍部署 Azure VMware 解决方案私有云的注意事项和要求。 


正确设置网络后，请继续学习下一篇教程以创建 Azure VMware 解决方案私有云。

> [!div class="nextstepaction"]
> [创建 Azure VMware 解决方案私有云](tutorial-create-private-cloud.md)
