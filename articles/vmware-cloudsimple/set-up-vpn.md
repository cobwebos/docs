---
title: Azure VMware 解决方案（按云简单 ） - 在本地云和私有云之间配置 VPN
description: 描述如何配置本地网络和云简单私有云之间的站点到站点或点对点 VPN 连接
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087124"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>配置与云简单私有云的 VPN 连接

VPN 网关允许您从本地网络和客户端计算机远程连接到 CloudSimple 网络。  在本文中，您可以找到有关从 CloudSimple 门户设置 VPN 网关的信息。  本地网络和 CloudSimple 网络之间的 VPN 连接提供对私有云上的 vCenter 和工作负载的访问。 CloudSimple 同时支持点对点 VPN 和站点到站点 VPN 网关。

## <a name="vpn-gateway-types"></a>VPN 网关类型

* **点对点 VPN**连接是从计算机连接到私有云的最简单方法。 使用点对点 VPN 连接远程连接到私有云。
* **站点到站点 VPN**连接允许您设置私有云工作负载以访问本地服务。 您还可以使用本地活动目录作为标识源，以验证您的私有云 vCenter。  目前，支持**基于策略的 VPN**类型。

在区域中，您可以创建一个站点到站点 VPN 网关和一个点对点 VPN 网关。

## <a name="point-to-site-vpn"></a>点到站点 VPN

要创建指向站点的 VPN 网关，请参阅[创建指向站点 VPN 网关](vpn-gateway.md#create-point-to-site-vpn-gateway)。

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>使用点对点 VPN 轻松连接到云

VPN 客户端需要从您的计算机连接到云简单。  下载适用于 MacOS 和 OS X 的 Windows 或[粘度](https://www.sparklabs.com/viscosity/download/)的[OpenVPN 客户端](https://openvpn.net/community-downloads/)。

1. 启动云简单门户并选择**网络**。
2. 选择**VPN 网关**。
3. 从 VPN 网关列表中，单击指向站点 VPN 网关。
4. 选择“用户”****。
5. 点击**下载我的 VPN 配置**

    ![下载 VPN 配置](media/download-p2s-vpn-configuration.png)

6. 在 VPN 客户端上导入配置

    * 在[Windows 客户端上导入配置](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)的说明
    * 有关在[macOS 或 OS X 上导入配置](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)的说明

7. 连接到云简单 VPN 网关。

下面的示例显示了使用**粘度客户端**导入连接。

#### <a name="import-connection-on-viscosity-client"></a>粘度客户端上的导入连接

1. 从下载的 .zip 文件中提取 VPN 配置的内容。

2. 打开计算机上的粘度。

3. 单击该**+** 图标并选择 **"从文件****导入连接** > "。

    ![从文件导入 VPN 配置](media/import-p2s-vpn-config.png)

4. 为要使用的协议选择 OpenVPN 配置文件 （.ovpn），然后单击"**打开**"。

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

连接现在显示在"粘度"菜单中。

#### <a name="connect-to-the-vpn"></a>连接到 VPN

要使用粘度 OpenVPN 客户端连接到 VPN，请从菜单中选择连接。 菜单图标将更新以指示已建立连接。

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>连接到多个私有云

点对点 VPN 连接解析您创建的第一个私有云的 DNS 名称。 当您要访问其他私有云时，必须更新 VPN 客户端上的 DNS 服务器。

1. 启动[云简单门户](access-cloudsimple-portal.md)。

2. 导航到**资源** > **私有云**，然后选择要连接到的私有云。

3. 在私有云的**摘要**页面上，在 **"基本信息**"下复制私有云 DNS 服务器 IP 地址。

    ![私有云 DNS 服务器](media/private-cloud-dns-server.png)

4. 右键单击计算机系统托盘中的粘度图标，然后选择 **"首选项**"。

    ![VPN](media/vis00.png)

5. 选择云简单 VPN 连接。

    ![VPN 连接](media/viscosity-client.png)

6. 单击 **"编辑"** 可更改连接属性。

    ![编辑 VPN 连接](media/viscosity-edit-connection.png)

7. 单击 **"网络**"选项卡并输入私有云 DNS 服务器 IP 地址，该地址由逗号或空格```cloudsimple.io```分隔，域为 。  选择 **"忽略 VPN 服务器发送的 DNS 设置**"。

    ![VPN 网络](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> 要连接到第一个私有云，请删除这些设置并连接到 VPN 服务器。

## <a name="site-to-site-vpn"></a>站点到站点 VPN

要创建站点到站点 VPN 网关，请参阅[创建站点到站点 VPN 网关](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。  从本地网络到私有云的站点到站点 VPN 连接提供了这些好处。  

* 从本地网络中的任何工作站访问私有云 vCenter
* 将本地活动目录用作 vCenter 标识源
* 方便地将 VM 模板、ISO 和其他文件从本地资源传输到私有云 vCenter
* 从本地网络访问在私有云上运行的工作负载

要在高可用性模式下设置本地 VPN 网关，请参阅[配置高可用性 VPN 连接](high-availability-vpn-connection.md)。

> [!IMPORTANT]
>    1. 在 VPN 设备上将 TCP MSS 夹紧设置为 1200。 或者，如果您的 VPN 设备不支持 MSS 夹紧，则可以将隧道接口上的 MTU 设置为 1240 字节。
> 2. 设置站点到站点 VPN 后，将 _.cloudsimple.io 的 DNS 请求转发到私有云 DNS 服务器。  按照本地 DNS[设置](on-premises-dns-setup.md)中的说明操作。
