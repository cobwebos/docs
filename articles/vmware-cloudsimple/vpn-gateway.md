---
title: CloudSimple-设置 VPN 网关的 Azure VMware 解决方案
description: 介绍如何设置点到站点 VPN 网关和站点到站点 VPN 网关，以及如何在本地网络与 CloudSimple 私有云之间创建连接
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845359"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>在 CloudSimple 网络上设置 VPN 网关

使用 VPN 网关，可以从本地网络和客户端计算机远程连接到 CloudSimple 网络。 本地网络与 CloudSimple 网络之间的 VPN 连接提供对私有云上的 vCenter 和工作负荷的访问权限。 CloudSimple 支持站点到站点 VPN 和点到站点 VPN 网关。

## <a name="vpn-gateway-types"></a>VPN 网关类型

* 使用**站点到站点 VPN**连接，可以设置私有云工作负荷以访问本地服务。 你还可以使用本地 Active Directory 作为标识源，以便向你的私有云 vCenter 进行身份验证。  目前仅支持**基于策略的 VPN**类型。
* **点到站点 VPN**连接是从计算机连接到私有云的最简单方法。 使用点到站点 VPN 连接远程连接到私有云。 有关为点到站点 VPN 连接安装客户端的信息，请参阅[配置到私有云的 VPN 连接](set-up-vpn.md)。

在某个区域中，你可以创建一个点到站点 VPN 网关和一个站点到站点 VPN 网关。

## <a name="automatic-addition-of-vlansubnets"></a>自动添加 VLAN/子网

CloudSimple VPN 网关提供向 VPN 网关添加 VLAN/子网的策略。  策略允许你为管理 VLAN/子网和用户定义的 VLAN/子网指定不同的规则。  管理 VLAN/子网的规则适用于你创建的任何新的私有云。  用户定义的 Vlan/子网规则允许自动向站点到站点 VPN 网关的现有或新的私有云添加任何新的 VLAN/子网，并为每个连接定义策略。

向 VPN 网关添加 Vlan/子网的策略适用于站点到站点 VPN 和点到站点 VPN 网关。

## <a name="automatic-addition-of-users"></a>自动添加用户

点到站点 VPN 网关可为新用户定义自动添加策略。 默认情况下，订阅的所有所有者和参与者都可以访问 CloudSimple 门户。  仅当 CloudSimple 门户首次启动时，才会创建用户。  选择**自动添加**规则后，任何新用户都可以使用点到站点 VPN 连接访问 CloudSimple 网络。

## <a name="set-up-a-site-to-site-vpn-gateway"></a>设置站点到站点 VPN 网关

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md)，然后选择 "**网络**"。
2. 选择**VPN 网关**。
3. 单击 "**新建 VPN 网关**"。

    ![创建 VPN 网关](media/create-vpn-gateway.png)

4. 对于 "**网关配置**"，请指定以下设置，然后单击 "**下一步**"。

    * 选择 "**站点到站点 VPN** " 作为网关类型。
    * 输入名称以标识网关。
    * 选择要在其中部署 CloudSimple 服务的 Azure 位置。
    * 还可以启用高可用性。

    ![创建站点到站点 VPN 网关](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > 启用高可用性要求你的本地 VPN 设备支持连接到两个 IP 地址。 部署 VPN 网关后，将无法禁用此选项。

5. 创建来自本地网络的第一个连接，然后单击 "**下一步**"。

    * 输入一个名称以标识该连接。
    * 对于对等 IP，请输入本地 VPN 网关的公共 IP 地址。
    * 输入本地 VPN 网关的对等标识符。  对等标识符通常是本地 VPN 网关的公共 IP 地址。  如果已在网关上配置了特定标识符，请输入标识符。
    * 复制用于从本地 VPN 网关进行连接的共享密钥。  若要更改默认共享密钥并指定新密钥，请单击 "编辑" 图标。
    * 对于**本地前缀**，请输入将访问 CloudSimple 网络的本地 CIDR 前缀。  您可以在创建连接时添加多个 CIDR 前缀。

    ![创建站点到站点 VPN 网关连接](media/create-vpn-gateway-s2s-connection.png)

6. 在私有云网络上启用将从本地网络访问的 VLAN/子网，然后单击 "**下一步**"。

    * 若要添加管理 VLAN/子网，请启用 "**添加私有云的管理 vlan/子网**"。  子网需要用于 vMotion 和 vSAN 子网。
    * 若要添加 vMotion 子网，请启用 "**添加私有云的 vmotion 网络**"。
    * 若要添加 vSAN 子网，请启用 "**添加" 私有云的 vSAN 子网**。
    * 选择或取消选择 "特定 Vlan"。

    ![创建连接](media/create-vpn-gateway-s2s-connection-vlans.png)

7. 查看设置，然后单击 "**提交**"。

    ![站点到站点 VPN 网关查看和创建](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>创建点到站点 VPN 网关

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md)，然后选择 "**网络**"。
2. 选择**VPN 网关**。
3. 单击 "**新建 VPN 网关**"。

    ![创建 VPN 网关](media/create-vpn-gateway.png)

4. 对于 "**网关配置**"，请指定以下设置，然后单击 "**下一步**"。

    * 选择**点到站点 VPN**作为网关类型。
    * 输入名称以标识网关。
    * 选择要在其中部署 CloudSimple 服务的 Azure 位置。
    * 为点到站点网关指定客户端子网。  连接时，将从客户端子网中指定 DHCP 地址。

5. 对于 "**连接/用户**"，指定以下设置，然后单击 "**下一步**"。

    * 若要自动允许当前和未来的所有用户通过点到站点网关访问私有云，请选择 "**自动添加所有用户**"。 选择该选项时，将自动选择用户列表中的所有用户。 您可以通过取消选择列表中的单个用户来覆盖 "自动" 选项。
    * 若要选择单个用户，请单击用户列表中的复选框。

6. 通过 "Vlan/子网" 部分，可以为网关和连接指定管理和用户 Vlan/子网。

    * **自动添加**选项设置网关的全局策略。 这些设置将应用于当前的网关。 这些设置可在 "**选择**" 区域中被覆盖。
    * 选择 "**添加私有云的管理 vlan/子网**"。 
    * 若要添加所有用户定义的 Vlan/子网，请单击 "**添加用户定义的 vlan/子网**"。
    * "**选择**设置" 在 "**自动添加**" 下覆盖全局设置。

7. 单击 "**下一步**" 查看设置。 单击 "编辑" 图标进行任何更改。
8. 单击 "**创建**"，创建 VPN 网关。

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>点到站点 VPN 网关的客户端子网和协议

点到站点 VPN 网关允许 TCP 和 UDP 连接。  选择 "TCP" 或 "UDP" 配置，从计算机连接时选择要使用的协议。

配置的客户端子网同时用于 TCP 和 UDP 客户端。  CIDR 前缀分为两个子网，一个用于 TCP，一个用于 UDP 客户端。 根据要并发连接的 VPN 用户数量选择前缀掩码。  

下表列出了前缀掩码的并发客户端连接数。

| 前缀掩码 | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| 并发 TCP 连接数 | 124 | 60 | 28 | 12 | 4 |
| 并发 UDP 连接数 | 124 | 60 | 28 | 12 | 4 |

若要使用点到站点 VPN 进行连接，请参阅[使用点到站点 Vpn 连接到 CloudSimple](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn)。
