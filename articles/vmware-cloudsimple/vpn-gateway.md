---
title: Azure VMware 解决方案（按云简单 ） - 设置 VPN 网关
description: 描述如何设置点对点 VPN 网关和站点到站点 VPN 网关，并在本地网络和云简单私有云之间创建连接
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279489"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>在云简单网络上设置 VPN 网关

VPN 网关允许您从本地网络和客户端计算机远程连接到 CloudSimple 网络。 本地网络和 CloudSimple 网络之间的 VPN 连接提供对私有云上的 vCenter 和工作负载的访问。 CloudSimple 支持站点到站点 VPN 和点对点 VPN 网关。

## <a name="vpn-gateway-types"></a>VPN 网关类型

* **站点到站点 VPN**连接允许您设置私有云工作负载以访问本地服务。 您还可以使用本地活动目录作为标识源，以验证您的私有云 vCenter。  目前，仅支持**基于策略的 VPN**类型。
* **点对点 VPN**连接是从计算机连接到私有云的最简单方法。 使用点对点 VPN 连接远程连接到私有云。 有关为点对点 VPN 连接安装客户端的信息，请参阅[配置到私有云的 VPN 连接](set-up-vpn.md)。

在区域中，您可以创建一个点对点 VPN 网关和一个站点到站点 VPN 网关。

## <a name="automatic-addition-of-vlansubnets"></a>自动添加 VLAN/子网

CloudSimple VPN 网关提供了将 VLAN/子网添加到 VPN 网关的策略。  策略允许您为管理 VLAN/子网和用户定义的 VLAN/子网指定不同的规则。  管理 VLAN/子网的规则适用于您创建的任何新私有云。  用户定义的 VLAN/子网规则允许您自动将任何新的 VLAN/子网添加到现有或新的私有云中，用于站点到站点 VPN 网关，您可以为每个连接定义策略。

将 VLAN/子网添加到 VPN 网关的策略适用于站点到站点 VPN 和点对点 VPN 网关。

## <a name="automatic-addition-of-users"></a>自动添加用户

指向站点的 VPN 网关允许您为新用户定义自动添加策略。 默认情况下，订阅的所有所有者和参与者都有权访问 CloudSimple 门户。  仅当首次启动云简单门户时，才会创建用户。  选择 **"自动添加**规则"使任何新用户都可以使用点对点 VPN 连接访问 CloudSimple 网络。

## <a name="set-up-a-site-to-site-vpn-gateway"></a>设置站点到站点 VPN 网关

1. [访问云简单门户](access-cloudsimple-portal.md)并选择 **"网络**"。
2. 选择**VPN 网关**。
3. 单击 **"新 VPN 网关**"。

    ![创建 VPN 网关](media/create-vpn-gateway.png)

4. 对于**网关配置**，请指定以下设置，然后单击 **"下一步**"。

    * 选择**站点到站点 VPN**作为网关类型。
    * 输入名称以标识网关。
    * 选择部署云简单服务的 Azure 位置。
    * 或者，启用高可用性。

    ![创建站点到站点 VPN 网关](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > 启用高可用性需要本地 VPN 设备支持连接到两个 IP 地址。 部署 VPN 网关后，无法禁用此选项。

5. 从本地网络创建第一个连接，然后单击 **"下一步**"。

    * 输入名称以标识连接。
    * 对于对等 IP，请输入本地 VPN 网关的公共 IP 地址。
    * 输入本地 VPN 网关的对等标识符。  对等标识符通常是本地 VPN 网关的公共 IP 地址。  如果您在网关上配置了特定标识符，请输入该标识符。
    * 复制共享密钥以从本地 VPN 网关进行连接。  要更改默认共享密钥并指定新密钥，请单击编辑图标。
    * 对于**本地前缀**，输入将访问 CloudSimple 网络的本地 CIDR 前缀。  创建连接时，可以添加多个 CIDR 前缀。

    ![创建站点到站点 VPN 网关连接](media/create-vpn-gateway-s2s-connection.png)

6. 在私有云网络上启用将从本地网络访问的 VLAN/子网，然后单击"**下一步**"。

    * 要添加管理 VLAN/子网，启用**添加私有云的 VLAN/子网**。  vMotion 和 vSAN 子网需要管理子网。
    * 要添加 vMotion 子网，启用**私有云的添加 vMotion 网络**。
    * 要添加 vSAN 子网，启用**私有云的添加 vSAN 子网**。
    * 选择或取消选择特定的 VLAN。

    ![创建连接](media/create-vpn-gateway-s2s-connection-vlans.png)

7. 查看设置并单击"**提交**"。

    ![站点到站点 VPN 网关审查和创建](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>创建点对点 VPN 网关

1. [访问云简单门户](access-cloudsimple-portal.md)并选择 **"网络**"。
2. 选择**VPN 网关**。
3. 单击 **"新 VPN 网关**"。

    ![创建 VPN 网关](media/create-vpn-gateway.png)

4. 对于**网关配置**，请指定以下设置，然后单击 **"下一步**"。

    * 选择**指向站点 VPN**作为网关类型。
    * 输入名称以标识网关。
    * 选择部署云简单服务的 Azure 位置。
    * 指定指向站点网关的客户端子网。  连接时，将从客户端子网提供 DHCP 地址。

5. 对于**连接/用户**，指定以下设置，然后单击 **"下一步**"。

    * 要自动允许所有当前和未来的用户通过指向站点网关访问私有云，请选择"**自动添加所有用户**"。 选择该选项时，将自动选择用户列表中的所有用户。 您可以通过取消列表中的单个用户来覆盖自动选项。
    * 要选择单个用户，请单击用户列表中的复选框。

6. VLAN/子网部分允许您为网关和连接指定管理和用户 VLAN/子网。

    * **"自动添加**"选项为网关设置全局策略。 这些设置适用于当前网关。 可以在 **"选择"** 区域中覆盖这些设置。
    * 选择**添加私有云的管理 VLAN/子网**。 
    * 要添加所有用户定义的 VLAN/子网，请单击"**添加用户定义的 VLAN/子网**"。
    * "**选择**"设置将覆盖 **"自动添加**"下的全局设置。

7. 单击 **"下一步**"查看设置。 单击"编辑"图标可进行任何更改。
8. 单击"**创建**"以创建 VPN 网关。

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>点对点 VPN 网关的客户端子网和协议

点对点 VPN 网关允许 TCP 和 UDP 连接。  通过选择 TCP 或 UDP 配置，选择从计算机连接时要使用的协议。

配置的客户端子网用于 TCP 和 UDP 客户端。  CIDR 前缀分为两个子网，一个用于 TCP，一个用于 UDP 客户端。 根据将同时连接的 VPN 用户数选择前缀掩码。  

下表列出了前缀掩码的并发客户端连接数。

| 前缀掩码 | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| 并发 TCP 连接数 | 124 | 60 | 28 | 12 | 4 |
| 并发 UDP 连接数 | 124 | 60 | 28 | 12 | 4 |

要使用点对点 VPN 进行连接，请参阅[使用点对点 VPN 轻松连接到云](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn)。
