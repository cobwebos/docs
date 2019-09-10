---
title: Azure VMware 解决方案（按 CloudSimple 快速入门）-创建私有云
description: 了解如何通过 CloudSimple 使用 Azure VMware 解决方案创建和配置私有云
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bbb5b90ffac4a89c14a4a6df51022bb61b10fbb0
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845499"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>快速入门-配置私有云环境

本文介绍如何创建 CloudSimple 私有云并设置私有云环境。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-a-private-cloud"></a>创建私有云

私有云是支持 ESXi 主机、vCenter、vSAN 和 NSX 的独立 VMware 堆栈。

私有云通过 CloudSimple 门户进行管理。 它们在自己的管理域中具有自己的 vCenter 服务器。 堆栈在专用节点和隔离的裸机硬件节点上运行。

1. 选择“所有服务”。
2. 搜索 " **CloudSimple Services**"。
3. 选择要在其上创建私有云的 CloudSimple 服务。
4. 从 "**概述**" 中，单击 "**创建私有云**" 以打开 CloudSimple 门户的新浏览器选项卡。  如果系统提示，请用 Azure 登录凭据登录。  

    ![从 Azure 创建私有云](media/create-private-cloud-from-azure.png)

5. 在 CloudSimple 门户中，提供私有云的名称。
6. 选择私有云的**位置**。
7. 选择 "**节点类型**"，与 Azure 上购买的内容一致。 你可以选择[CS28 或 CS36 选项](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku)。 后一种方法包括最大计算和内存容量。
8. 指定**节点计数**。  至少需要三个节点才能创建私有云。

    ![创建私有云-基本信息](media/create-private-cloud-basic-info.png)

9. 单击“下一步:**高级选项**。
10. 输入 vSphere/vSAN 子网的 CIDR 范围。 请确保 CIDR 范围不与任何本地或其他 Azure 子网（虚拟网络）或网关子网重叠。

    **CIDR 范围选项：** /24、/23、/22 或/21。 A/24 CIDR 范围最多支持26个节点，/23 个 CIDR 范围最多支持58个节点，并且/22 和/21 CIDR 范围支持64节点（私有云中的最大节点数）。  若要了解详细信息、Vlan 和子网，请参阅[vlan 和子网概述](cloudsimple-vlans-subnets.md)。

      > [!IMPORTANT]
      > VSphere/vSAN CIDR 范围中的 IP 地址保留供私有云基础结构使用。  请勿在任何虚拟机上使用此范围内的 IP 地址。

11. 单击“下一步:**查看和创建**。
12. 查看设置。 如果需要更改任何设置，请单击 "**上一步**"。
13. 单击“创建”。

私有云预配过程开始。  预配私有云可能需要长达两个小时。

## <a name="launch-cloudsimple-portal"></a>启动 CloudSimple 门户

可以从 Azure 门户访问 CloudSimple 门户。  将使用 Azure 登录凭据通过单一登录（SSO）启动 CloudSimple 门户。  若要访问 CloudSimple 门户，需要授权**CloudSimple Service 授权**应用程序。  有关授予权限的详细信息，请参阅[同意 CloudSimple 服务授权应用程序](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application)。

1. 选择“所有服务”。
2. 搜索 " **CloudSimple Services**"。
3. 选择要在其上创建私有云的 CloudSimple 服务。
4. 从 "概述" 中，单击 **"前往 CloudSimple 门户"** ，打开 CloudSimple 门户的新浏览器选项卡。  如果系统提示，请用 Azure 登录凭据登录。  

    ![启动 CloudSimple 门户](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>创建点到站点 VPN

点到站点 VPN 连接是从计算机连接到私有云的最简单方法。 如果要远程连接到私有云，请使用点到站点 VPN 连接。  若要快速访问私有云，请遵循以下步骤。  可以使用[站点到站点 VPN](vpn-gateway.md)或[Azure ExpressRoute](on-premises-connection.md)来访问本地网络中的 CloudSimple 区域。

### <a name="create-gateway"></a>创建网关

1. 启动 CloudSimple 门户，然后选择 "**网络**"。
2. 选择**VPN 网关**。
3. 单击 "**新建 VPN 网关**"。

    ![创建 VPN 网关](media/create-vpn-gateway.png)

4. 对于 "**网关配置**"，请指定以下设置，然后单击 "**下一步**"。

    * 选择**点到站点 VPN**作为网关类型。
    * 输入名称以标识网关。
    * 选择要在其中部署 CloudSimple 服务的 Azure 位置。
    * 为点到站点网关指定客户端子网。  当你连接时，将从此子网中指定 DHCP 地址。

5. 对于 "**连接/用户**"，指定以下设置，然后单击 "**下一步**"。

    * 若要自动允许当前和未来的所有用户通过此点到站点网关访问私有云，请选择 "**自动添加所有用户**"。 如果选择此选项，则会自动选择 "用户" 列表中的所有用户。 您可以通过取消选择列表中的单个用户来覆盖 "自动" 选项。
    * 若要仅选择单个用户，请单击 "用户" 列表中的复选框。

6. 通过 "Vlan/子网" 部分，可以为网关和连接指定管理和用户 Vlan/子网。

    * **自动添加**选项设置此网关的全局策略。 这些设置将应用于当前的网关。 这些设置可在 "**选择**" 区域中被覆盖。
    * 选择 "**添加私有云的管理 vlan/子网**"。
    * 若要添加所有用户定义的 Vlan/子网，请单击 "**添加用户定义的 vlan/子网**"。
    * "**选择**设置" 在 "**自动添加**" 下覆盖全局设置。

7. 单击 "**下一步**" 查看设置。 单击 "编辑" 图标进行任何更改。
8. 单击 "**创建**"，创建 VPN 网关。

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>使用点到站点 VPN 连接到 CloudSimple

从计算机连接到 CloudSimple 时需要 VPN 客户端。  下载适用于 Windows 的[OpenVPN client](https://openvpn.net/community-downloads/)或用于 MACOS 和 OS X 的[Viscosity](https://www.sparklabs.com/viscosity/download/) 。

1. 启动 CloudSimple 门户，然后选择 "**网络**"。
2. 选择**VPN 网关**。
3. 在 VPN 网关列表中，单击 "点到站点 VPN 网关"。
4. 选择“用户”。
5. 单击 "**下载我的 VPN 配置"** 。

    ![下载 VPN 配置](media/download-p2s-vpn-configuration.png)

6. 导入 VPN 客户端上的配置。

    * 有关[在 Windows 客户端上导入配置](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)的说明
    * 有关[在 macOS 或 OS X 上导入配置](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)的说明

7. 连接到 CloudSimple。

## <a name="create-a-vlan-for-your-workload-vms"></a>为工作负荷 Vm 创建 VLAN

创建私有云之后，创建一个 VLAN，你将在其中部署工作负荷/应用程序 Vm。

1. 在 CloudSimple 门户中，选择 "**网络**"。
2. 单击 " **VLAN/子网**"。
3. 单击 "**创建 VLAN/子网**"。

    ![创建 VLAN/子网](media/create-new-vlan-subnet.png)

4. 选择新 VLAN/子网的**私有云**。
5. 从列表中选择一个 VLAN ID。  
6. 输入子网名称以标识子网。
7. 指定子网 CIDR 范围和掩码。  此范围不得与任何现有子网重叠。
8. 单击“提交”。

    ![创建 VLAN/子网详细信息](media/create-new-vlan-subnet-details.png)

将创建 VLAN/子网。  你现在可以使用此 VLAN ID 在私有云 vCenter 上创建分布式端口组。

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>将环境连接到 Azure 虚拟网络

CloudSimple 为你的私有云提供了 ExpressRoute 线路。 可以将 Azure 上的虚拟网络连接到 ExpressRoute 线路。 有关设置连接的完整详细信息，请遵循[使用 ExpressRoute 的 Azure 虚拟网络连接](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)中的步骤。

## <a name="sign-in-to-vcenter"></a>登录到 vCenter

你现在可以登录到 vCenter 来设置虚拟机和策略。

1. 若要访问 vCenter，请从 CloudSimple 门户启动。 在主页上的 "**常见任务**" 下，单击 "**启动 vSphere 客户端**"。  选择私有云，然后单击 "在私有云上**启动 VSphere 客户端**"。

    ![启动 vSphere 客户端](media/launch-vcenter-from-cloudsimple-portal.png)

2. 选择首选的 vSphere 客户端以访问 vCenter，并使用用户名和密码进行登录。  默认值为：
    * 用户名： **CloudOwner@cloudsimple.local**
    * 密码：**CloudSimple123!**  

下一过程中的 vCenter 屏幕来自 vSphere （HTML5）客户端。

## <a name="change-your-vcenter-password"></a>更改你的 vCenter 密码

CloudSimple 建议你在首次登录到 vCenter 时更改密码。  
设置的密码必须满足以下要求：

* 最长生存期：密码必须每365天更改一次
* 限制重复使用：用户无法重用前面的五个密码
* 时长：8-20 个字符
* 特殊字符：至少一个特殊字符
* 字母字符：至少一个大写字符、a-z 和至少一个小写字符 a-z
* 小数至少一个数字字符，0-9
* 最大相同相邻字符：三

    例如：CC 或 CCC 可作为密码的一部分接受，但 CCCC 不能。

如果设置的密码不符合要求：

* 如果使用 vSphere Flash 客户端，则会报告错误
* 如果使用 HTML5 客户端，则不会报告错误。 客户端不接受更改，旧密码将继续工作。

## <a name="change-nsx-administrator-password"></a>更改 NSX 管理员密码

使用默认密码部署了 NSX 管理器。  建议在创建私有云后更改密码。

* 用户名：**管理员**
* 密码：**CloudSimple123!**

可以在 CloudSimple 门户中找到 NSX 管理器的完全限定的域名（FQDN）和 IP 地址。

1. 启动 CloudSimple 门户并选择 "**资源**"。
2. 单击要使用的私有云。
3. 选择**vSphere 管理网络**
4. 使用**NSX Manager**的 FQDN 或 IP 地址，并使用 web 浏览器进行连接。

    ![查找 NSX Manager FQDN](media/private-cloud-nsx-manager-fqdn.png)

若要更改密码，请按照[NSX Manager 安装](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.2/com.vmware.nsxt.install.doc/GUID-A65FE3DD-C4F1-47EC-B952-DEDF1A3DD0CF.html)中的说明进行操作。

## <a name="create-a-port-group"></a>创建端口组

在 vSphere 中创建分布式端口组：

1. 按照[VSphere 网络指南](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)中的 "添加分布式端口组" 中的说明进行操作。
2. 设置分布式端口组时，请提供在[为工作负荷 Vm 创建 vlan](#create-a-vlan-for-your-workload-vms)中创建的 vlan ID。

## <a name="next-steps"></a>后续步骤

* [在 Azure 上使用 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* [使用 Azure ExpressRoute 连接到本地网络](on-premises-connection.md)
* [从本地设置站点到站点 VPN](vpn-gateway.md)
