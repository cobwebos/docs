---
title: 快速入门：创建私有云
titleSuffix: Azure VMware Solutions by CloudSimple
description: 了解如何通过云简单创建和配置使用 Azure VMware 解决方案的私有云
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a2fe7daf1476f19f6a6eea83174342fa1273fe14
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867965"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>快速入门 - 配置私有云环境

在本文中，了解如何创建云简单私有云并设置私有云环境。

## <a name="before-you-begin"></a>开始之前

查看[网络先决条件](cloudsimple-network-checklist.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-a-private-cloud"></a>创建私有云

私有云是一个孤立的 VMware 堆栈，支持 ESXi 主机、vCenter、vSAN 和 NSX。

私有云通过云简单门户进行管理。 它们有自己的 vCenter 服务器。 堆栈在专用节点和隔离裸机硬件节点上运行。

1. 选择“所有服务”  。
2. 搜索**云简单服务**。
3. 选择要在其中创建私有云的云简单服务。
4. 从**概述**中，单击 **"创建私有云**"可为云简单门户打开新的浏览器选项卡。  如果出现提示，请使用 Azure 登录凭据登录。  

    ![从 Azure 创建私有云](media/create-private-cloud-from-azure.png)

5. 在云简单门户中，为私有云提供名称。
6. 选择私有云**的位置**。
7. 选择**节点类型**，与在 Azure 上预配的内容一致。
8. 指定**节点计数**。  创建私有云至少需要三个节点。

    ![创建私有云 - 基本信息](media/create-private-cloud-basic-info.png)

9. 单击 **"下一步：高级选项**"。
10. 输入 vSphere/vSAN 子网的 CIDR 范围。 确保 CIDR 范围不会与任何本地或其他 Azure 子网（虚拟网络）或网关子网重叠。

    **CIDR 范围选项**：/24、/23、/22 或 /21。 /24 CIDR 范围最多支持 26 个节点，/23 CIDR 范围最多支持 58 个节点，/22 和 /21 CIDR 范围支持 64 个节点（私有云中最大节点数）。  要了解更多信息以及 VLAN 和子网，请参阅[VLAN 和子网概述](cloudsimple-vlans-subnets.md)。

      > [!IMPORTANT]
      > vSphere/vSAN CIDR 范围内的 IP 地址保留供私有云基础设施使用。  不要在任何虚拟机上使用此范围内的 IP 地址。

11. 单击 **"下一步："查看并创建**。
12. 查看设置。 如果需要更改任何设置，请单击"**上一个**"。
13. 单击“创建”。 

私有云预配过程开始。  预配私有云最多可能需要两个小时。

## <a name="launch-cloudsimple-portal"></a>启动云简单门户

可以从 Azure 门户访问云简单门户。  CloudSimple 门户将使用单一登录 （SSO） 使用 Azure 登录凭据启动。  访问云简单门户需要您授权**云简单服务授权**应用程序。  有关授予权限的详细信息，请参阅[同意云简单服务授权应用程序](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application)。

1. 选择“所有服务”  。
2. 搜索**云简单服务**。
3. 选择要在其中创建私有云的云简单服务。
4. 从概述中，单击"**转到云简单"门户**可打开 CloudSimple 门户的新浏览器选项卡。  如果出现提示，请使用 Azure 登录凭据登录。  

    ![启动云简单门户](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>创建点对点 VPN

点对点 VPN 连接是从计算机连接到私有云的最简单方法。 如果您远程连接到私有云，请使用点对点 VPN 连接。  要快速访问私有云，请按照以下步骤操作。  可通过[站点到站点 VPN](vpn-gateway.md)或[Azure ExpressRoute](on-premises-connection.md)从本地网络访问云简单区域。

### <a name="create-gateway"></a>创建网关

1. 启动云简单门户并选择**网络**。
2. 选择**VPN 网关**。
3. 单击 **"新 VPN 网关**"。

    ![创建 VPN 网关](media/create-vpn-gateway.png)

4. 对于**网关配置**，请指定以下设置，然后单击 **"下一步**"。

    * 选择**指向站点 VPN**作为网关类型。
    * 输入名称以标识网关。
    * 选择部署云简单服务的 Azure 位置。
    * 指定指向站点网关的客户端子网。  当您连接时，将从该子网提供 DHCP 地址。

5. 对于**连接/用户**，指定以下设置，然后单击 **"下一步**"。

    * 要自动允许所有当前和未来的用户通过此指向站点网关访问私有云，请选择"**自动添加所有用户**"。 选择此选项后，"用户"列表中的所有用户将自动选择。 您可以通过取消列表中的单个用户来覆盖自动选项。
    * 要仅选择单个用户，请单击"用户"列表中的复选框。

6. VLAN/子网部分允许您为网关和连接指定管理和用户 VLAN/子网。

    * **"自动添加**"选项设置此网关的全局策略。 这些设置适用于当前网关。 可以在 **"选择"** 区域中覆盖这些设置。
    * 选择**添加私有云的管理 VLAN/子网**。
    * 要添加所有用户定义的 VLAN/子网，请单击"**添加用户定义的 VLAN/子网**"。
    * "**选择**"设置将覆盖 **"自动添加**"下的全局设置。

7. 单击 **"下一步**"查看设置。 单击"编辑"图标可进行任何更改。
8. 单击"**创建**"以创建 VPN 网关。

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>使用点对点 VPN 轻松连接到云

VPN 客户端需要从您的计算机连接到云简单。  下载适用于 MacOS 和 OS X 的 Windows 或[粘度](https://www.sparklabs.com/viscosity/download/)的[OpenVPN 客户端](https://openvpn.net/community-downloads/)。

1. 启动云简单门户并选择**网络**。
2. 选择**VPN 网关**。
3. 从 VPN 网关列表中，单击指向站点 VPN 网关。
4. 选择“用户”****。
5. 单击 **"下载我的 VPN 配置**"。

    ![下载 VPN 配置](media/download-p2s-vpn-configuration.png)

6. 在 VPN 客户端上导入配置。

    * 在[Windows 客户端上导入配置](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)的说明
    * 有关在[macOS 或 OS X 上导入配置](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)的说明

7. 连接到云简单。

## <a name="create-a-vlan-for-your-workload-vms"></a>为您的工作负载 VM 创建 VLAN

创建私有云后，请创建一个 VLAN，您可以在其中部署工作负荷/应用程序 VM。

1. 在云简单门户中，选择 **"网络**"。
2. 单击**VLAN/子网**。
3. 单击 **"创建 VLAN/子网**"。

    ![创建 VLAN/子网](media/create-new-vlan-subnet.png)

4. 为新的 VLAN/子网选择**私有云**。
5. 从列表中选择 VLAN ID。  
6. 输入子网名称以标识子网。
7. 指定子网 CIDR 范围和掩码。  此范围不得与任何现有子网重叠。
8. 单击“提交”  。

    ![创建 VLAN/子网详细信息](media/create-new-vlan-subnet-details.png)

将创建 VLAN/子网。  现在，您可以使用此 VLAN ID 在私有云 vCenter 上创建分布式端口组。

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>将环境连接到 Azure 虚拟网络

云简单为您提供了专用云的 ExpressRoute 电路。 您可以将 Azure 上的虚拟网络连接到 ExpressRoute 电路。 有关设置连接的完整详细信息，[请按照 Azure 虚拟网络连接中的步骤使用 ExpressRoute](https://docs.microsoft.com/azure/vmware-cloudsimple/cloudsimple-azure-network-connection)。

## <a name="sign-in-to-vcenter"></a>登录到 vCenter

您现在可以登录到 vCenter 来设置虚拟机和策略。

1. 要访问 vCenter，从云简单门户开始。 在主页上，在 **"常见任务**"下，单击 **"启动 vSphere 客户端**"。  选择私有云，然后单击在私有云上**启动 vSphere 客户端**。

    ![启动 vSphere 客户端](media/launch-vcenter-from-cloudsimple-portal.png)

2. 选择您首选的 vSphere 客户端以访问 vCenter，并使用用户名和密码登录。  默认值为：
    * 用户名：`CloudOwner@cloudsimple.local`
    * 密码：`CloudSimple123!`

下一个过程中的 vCenter 屏幕来自 vSphere （HTML5） 客户端。

## <a name="change-your-vcenter-password"></a>更改 vCenter 密码

CloudSimple 建议您在首次登录到 vCenter 时更改密码。  
您设置的密码必须满足以下要求：

* 最长生存期：密码必须每 365 天更改一次
* 限制重用：用户无法重复使用前五个密码中的任何一个
* 长度： 8 - 20 个字符
* 特殊字符：至少一个特殊字符
* 字母字符：至少一个大写字符 A-Z 和至少一个小写字符 a-z
* 数字：至少一个数字字符，0-9
* 最大相同的相邻字符：三个

    示例：CC 或 CCC 作为密码的一部分是可以接受的，但 CCCC 不是。

如果您设置的密码不符合要求：

* 如果您使用 vSphere 闪存客户端，它会报告错误
* 如果使用 HTML5 客户端，它不报告错误。 客户端不接受更改，旧密码继续工作。

## <a name="access-nsx-manager"></a>访问 NSX 管理器

NSX 管理器使用默认密码部署。 

* 用户名：**管理员**
* 密码：**云简单123！**

您可以在云简单门户上找到 NSX 管理器完全限定的域名 （FQDN） 和 IP 地址。

1. 启动云简单门户并选择**资源**。
2. 单击要使用的私有云。
3. 选择**vSphere 管理网络**
4. 使用**NSX 管理器**的 FQDN 或 IP 地址并使用 Web 浏览器进行连接。

    ![查找 NSX 管理器 FQDN](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>创建端口组

要在 vSphere 中创建分布式端口组，请：

1. 按照[vSphere 网络指南](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)中的"添加分布式端口组"中的说明进行操作。
2. 设置分布式端口组时，请提供[在为工作负载 VM 创建 VLAN](#create-a-vlan-for-your-workload-vms)中创建的 VLAN ID。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware VM](quickstart-create-vmware-virtual-machine.md)
* [使用 Azure 快速路由连接到本地网络](on-premises-connection.md)
* [从本地设置站点到站点 VPN](vpn-gateway.md)
