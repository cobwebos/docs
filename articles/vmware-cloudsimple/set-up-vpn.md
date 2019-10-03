---
title: Azure VMware 解决方案 (按 CloudSimple)-在本地与私有云之间配置 VPN
description: 介绍如何配置本地网络与 CloudSimple 私有云之间的站点到站点或点到站点 VPN 连接
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972385"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>配置到 CloudSimple 私有云的 VPN 连接

使用 VPN 网关, 可以从本地网络和客户端计算机远程连接到 CloudSimple 网络。  本文介绍如何从 CloudSimple 门户设置 VPN 网关。  本地网络与 CloudSimple 网络之间的 VPN 连接提供对私有云上的 vCenter 和工作负荷的访问权限。 CloudSimple 支持点到站点 VPN 网关和站点到站点 VPN 网关。

## <a name="vpn-gateway-types"></a>VPN 网关类型

* **点到站点 VPN**连接是从计算机连接到私有云的最简单方法。 使用点到站点 VPN 连接来远程连接到私有云。
* 使用**站点到站点 VPN**连接, 可以设置私有云工作负荷以访问本地服务。 你还可以使用本地 Active Directory 作为标识源, 以便向你的私有云 vCenter 进行身份验证。  目前支持**基于策略的 VPN**类型。

在某个区域中, 可以创建一个站点到站点 VPN 网关和一个点到站点 VPN 网关。

## <a name="point-to-site-vpn"></a>点到站点 VPN

若要创建点到站点 VPN 网关, 请参阅[创建点到站点 vpn 网关](vpn-gateway.md#create-point-to-site-vpn-gateway)。

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>使用点到站点 VPN 连接到 CloudSimple

从计算机连接到 CloudSimple 时需要 VPN 客户端。  下载适用于 Windows 的[OpenVPN client](https://openvpn.net/community-downloads/)或用于 MACOS 和 OS X 的[Viscosity](https://www.sparklabs.com/viscosity/download/) 。

1. 启动 CloudSimple 门户, 然后选择 "**网络**"。
2. 选择**VPN 网关**。
3. 在 VPN 网关列表中, 单击 "点到站点 VPN 网关"。
4. 选择“用户”。
5. 单击 "**下载我的 VPN 配置"**

    ![下载 VPN 配置](media/download-p2s-vpn-configuration.png)

6. 导入 VPN 客户端上的配置

    * 有关[在 Windows 客户端上导入配置](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)的说明
    * 有关[在 macOS 或 OS X 上导入配置](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)的说明

7. 连接到 CloudSimple VPN 网关。

下面的示例演示如何使用**Viscosity 客户端**导入连接。

#### <a name="import-connection-on-viscosity-client"></a>在 Viscosity 客户端上导入连接

1. 从下载的 .zip 文件中提取 VPN 配置的内容。

2. 在计算机上打开 Viscosity。

3. 单击该 **+** 图标, 然后选择 "**从文件** **导入连接** > "。

    ![从文件导入 VPN 配置](media/import-p2s-vpn-config.png)

4. 选择要使用的协议的 OpenVPN 配置文件 (ovpn), 并单击 "**打开**"。

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

此时, 该连接将显示在 "Viscosity" 菜单中。

#### <a name="connect-to-the-vpn"></a>连接到 VPN

若要使用 Viscosity OpenVPN 客户端连接到 VPN, 请从菜单中选择该连接。 菜单图标将更新以指示已建立连接。

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>连接到多个私有云

点到站点 VPN 连接解析您创建的第一个私有云的 DNS 名称。 如果要访问其他私有云, 则必须更新 VPN 客户端上的 DNS 服务器。

1. 启动[CloudSimple 门户](access-cloudsimple-portal.md)。

2. 导航到 "**资源** > " "**私有云**", 然后选择要连接到的私有云。

3. 在私有云的 "**摘要**" 页上, 在 "**基本信息**" 下复制私有云 DNS 服务器 IP 地址。

    ![私有云 DNS 服务器](media/private-cloud-dns-server.png)

4. 右键单击计算机系统托盘中的 Viscosity 图标, 然后选择 "**首选项**"。

    ![VPN](media/vis00.png)

5. 选择 CloudSimple VPN 连接。

    ![VPN 连接](media/viscosity-client.png)

6. 单击 "**编辑**" 更改连接属性。

    ![编辑 VPN 连接](media/viscosity-edit-connection.png)

7. 单击 "**网络**" 选项卡, 然后输入私有云 DNS 服务器 IP 地址, 以逗号或空格分隔, 并```cloudsimple.io```输入域。  选择 "**忽略 VPN 服务器发送的 DNS 设置**"。

    ![VPN 网络](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> 若要连接到第一个私有云, 请删除这些设置, 然后连接到 VPN 服务器。

## <a name="site-to-site-vpn"></a>站点到站点 VPN

若要创建站点到站点 VPN 网关, 请参阅[创建站点到站点 vpn 网关](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。  从本地网络到私有云的站点到站点 VPN 连接提供了这些优势。  

* 本地网络中任何工作站的私有云 vCenter 的可访问性
* 使用本地 Active Directory 作为 vCenter 标识源
* 方便地将 VM 模板、Iso 和其他文件从本地资源传输到私有云 vCenter
* 从本地网络在私有云上运行的工作负荷的可访问性

若要在高可用性模式下设置本地 VPN 网关, 请参阅[配置高可用性 vpn 连接](high-availability-vpn-connection.md)。

> [!IMPORTANT]
>    1. 在 VPN 设备上将 TCP MSS 钳位设置为1200。 或者, 如果 VPN 设备不支持 MSS 钳位, 还可以改为将隧道接口上的 MTU 设置为1240字节。
> 2. 设置站点到站点 VPN 后, 将 *. cloudsimple.io 的 DNS 请求转发到私有云 DNS 服务器。  按照[本地 DNS 设置](on-premises-dns-setup.md)中的说明进行操作。
