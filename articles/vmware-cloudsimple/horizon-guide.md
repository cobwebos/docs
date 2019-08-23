---
title: Azure VMware 解决方案 (按 CloudSimple)-使用私有云网站使用 VMware 横向托管虚拟桌面基础结构
description: 介绍如何使用 CloudSimple 私有云网站来托管使用 VMware 范围的虚拟桌面基础结构
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8e5aeb63c54bd9ad71d5eb179fb93972468af4c0
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972755"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>使用 CloudSimple 私有云网站使用 VMware 范围托管虚拟桌面基础结构

你可以使用 CloudSimple 私有云网站来托管使用 VMware 地平线7、windows 的虚拟桌面基础结构 (VDI)。 下图显示了 VDI 的逻辑解决方案体系结构。

![水平部署](media/horizon-deployment.png)

使用此解决方案, 你可以完全控制范围视图管理器和应用卷。 熟悉的 UI、API 和 CLI 接口允许使用现有的脚本和工具。

CloudSimple 解决方案要求你执行以下操作:

* 在私有云中安装、配置和管理 VMware 地平线1.x。
* 提供您自己的水平许可证。

## <a name="deploy-the-solution"></a>部署解决方案

以下部分介绍如何在私有云中使用地平线部署 VDI 解决方案。

1. [验证 VMware 产品版本是否兼容](#verify-that-vmware-product-versions-are-compatible)
2. [估计桌面环境大小](#estimate-the-size-of-your-desktop-environment)
3. [为环境创建私有云](#create-a-private-cloud-for-your-environment)
4. [在私有云中安装 VMware 地平线](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>验证 VMware 产品版本是否兼容

* 验证你当前和计划内版本的水平、应用卷、统一访问网关和用户环境管理器是否相互兼容, 以及私有云中的 vCenter 和 PSC。 有关兼容性信息, 请参阅[适用于地平线7.5 的 VMware 兼容性矩阵](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=)。
* 若要在私有云中了解 vCenter 和 PSC 的当前版本, 请在[CloudSimple 门户](access-cloudsimple-portal.md)中, 选择 "**资源**", 选择私有云, 然后单击 " **vSphere 管理网络**" 选项卡。

![vCenter 和 PSC 版本](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>估计桌面环境大小

* 验证你确定的配置是否处于 VMware 运营限制内。
* 估计灾难恢复站点所需的资源, 以保护本地环境。

### <a name="create-a-private-cloud-for-your-environment"></a>为环境创建私有云

1. 按照[配置私有云环境](quickstart-create-private-cloud.md)中的说明, 从 CloudSimple 门户创建私有云。  CloudSimple 在每个新创建的私有云中创建一个名为 "cloudowner" 的默认 vCenter 用户。 有关默认私有云用户和权限模型的详细信息, 请参阅[了解私有云权限模型](learn-private-cloud-permissions.md)。
2. 在私有云中为地平线管理平面创建 VLAN, 并为其分配一个子网 CIDR。 有关说明, 请参阅[创建和管理 vlan/子网](create-vlan-subnet.md)。 这是将安装所有解决方案组件 (统一访问网关、连接服务器、应用卷服务器和用户环境管理器服务器) 的网络。
3. 决定是否要将外部标识提供者与私有云 vCenter 一起使用。 如果是, 请选择下列选项之一:
    * 使用本地 Active Directory 作为外部标识提供者。 有关说明, 请参阅[VCenter 标识源](set-vcenter-identity.md)。
    * 在 "水平" 管理平面 VLAN 中的私有云中设置 Active Directory 服务器, 以用作外部标识提供者。 有关说明, 请参阅[VCenter 标识源](set-vcenter-identity.md)。
    * 在私有云中的水平管理平面 VLAN 中设置 DHCP 和 DNS 服务器。 有关说明, 请参阅[在 CloudSimple 私有云中设置 DNS 和 DHCP 应用程序和工作负荷](dns-dhcp-setup.md)。
4. 在安装在私有云中的 DNS 服务器上配置 DNS 转发。 有关说明, 请参阅[创建条件转发器](on-premises-dns-setup.md#create-a-conditional-forwarder)。

### <a name="install-vmware-horizon-in-your-private-cloud"></a>在私有云中安装 VMware 地平线

以下部署关系图描述了在私有云中部署的水平解决方案。 统一访问网关、AD/DC、视图和应用卷服务器安装在用户创建的 VLAN 234 中。 统一访问网关具有可从 Internet 访问的分配的公共 IP 地址。 在 VLAN 235 中部署了地平线桌面池 Vm, 以提供更多的隔离和安全性。

![私有云中的范围部署](media/horizon-private-cloud.png)

以下各节概括说明了如何设置与图中描述的部署类似的部署。 在开始之前, 请确认你具有以下各项:

* 使用 CloudSimple 门户创建的私有云, 具有足够的容量来运行桌面池。
* 在本地环境和私有云环境之间提供足够的带宽, 以支持桌面的网络流量。
* 在本地数据中心和私有云之间设置站点到站点 VPN 隧道。
* 从本地环境中的最终用户子网到 CloudSimple 私有云子网的 IP 可访问性。
* 为私有云安装 AD/DHCP/DNS。

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple 门户:为桌面池创建专用 VLAN/子网

为地平线桌面池创建 VLAN, 并为其分配一个子网 CIDR。 有关说明, 请参阅[创建和管理 vlan/子网](create-vlan-subnet.md)。 这是将运行所有桌面虚拟机的网络。

遵循标准的安全最佳做法来保护您的水平部署:

* 仅允许桌面 RDP 流量/SSH 流量发送到桌面 Vm。
* 仅允许在地平线管理平面 VLAN 和桌面池 VLAN 之间进行管理流量。
* 仅允许来自本地网络的管理流量。

可以通过从 CloudSimple 门户配置[防火墙规则](firewall.md)来强制实施这些最佳实践。

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>CloudSimple 门户:将防火墙规则配置为保护地平线管理平面

在 CloudSimple 门户中设置以下规则。 有关说明, 请参阅[设置防火墙表和规则](firewall.md)。

1. 在 CloudSimple N-S 防火墙中配置防火墙规则, 以允许在本地子网和横向管理 VLAN 之间进行通信, 以便只允许在 VMware 文档[范围端口列表](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html)中列出的网络端口。

2. 在私有云中的水平管理 VLAN 和桌面池 VLAN 之间创建电子 W 防火墙规则。

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>CloudSimple 门户:为统一访问网关创建公共 IP 地址

为统一访问网关设备创建公共 IP 地址, 以启用来自 internet 的桌面客户端连接。 有关说明, 请参阅[分配公共 IP 地址](public-ips.md)。

安装完成后, 公共 IP 地址被分配并在公共 Ip 页面上列出。

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple 门户:提升特权

默认值为 "cloudowner" 的用户在私有云 vCenter 中没有足够的权限来安装, 因此必须升级用户的 vCenter 特权。 有关详细信息, 请参阅[提升权限](escalate-private-cloud-privileges.md)。

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter UI:在私有云中创建用于地平线安装的用户

1. 使用 "cloudowner" 用户凭据登录到 vCenter。
2. 在 vCenter 中创建新用户 "soln-管理员", 并将该用户添加到 vCenter 中的 administrators 组。
3. 以 "cloudowner" 用户身份注销 vCenter, 并以 "soln-admin" 用户身份登录。

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI:安装 VMware 地平线

如前面的逻辑体系结构部分所述, 地平线解决方案包含以下组件:

* VMware 地平线视图
* VMware 统一访问网关
* VMware 应用卷管理器
* VMware 用户环境管理器

按如下所示安装组件:

1. 按照 VMware 文档[部署和配置 Vmware 统一访问网关](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)中提供的说明安装和配置统一访问网关。

2. 按照[查看安装指南](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)中的说明, 在私有云中安装范围视图。

3. 按照[安装和配置 VMware 应用卷](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)中的说明安装应用卷管理器。

4. 按照[关于安装和配置 VMware 用户环境管理器](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)中的说明安装和配置用户环境管理器。

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>为上载 VMware 地平线预打包应用程序卷提供支持请求

作为安装过程的一部分, 应用卷管理器使用预打包的卷来预配应用堆栈和可写卷。 这些卷用作应用堆栈和可写卷的模板。

将卷上传到私有云数据存储需要 ESXi root 密码。 若要获得帮助, 请提交[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 附加 AppVolumes 安装程序捆绑包, 以便 CloudSimple 支持人员可以将模板上传到私有云环境。

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple 门户:反呈报权限

你现在可以[取消升级](escalate-private-cloud-privileges.md#de-escalate-privileges)"cloudowner" 用户的权限。

## <a name="ongoing-management-of-your-horizon-solution"></a>正在进行的水平解决方案管理

你可以完全控制私有云环境中的地平线和应用卷管理器软件, 并且应执行必要的软件生命周期管理。 在更新或升级范围或应用程序卷之前, 请确保软件的任何新版本都与私有云 vCenter 和 PSC 兼容。
