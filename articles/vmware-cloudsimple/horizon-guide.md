---
title: Azure VMware 解决方案（按云简单 - 使用私有云网站使用 VMware Horizon 托管虚拟桌面基础结构）
description: 描述如何使用云简单私有云网站使用 VMware Horizon 托管虚拟桌面基础架构
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025242"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>使用云简单私有云网站使用 VMware Horizon 托管虚拟桌面基础架构

您可以使用云简单私有云网站使用 VMware Horizon 7.x 托管虚拟桌面基础设施 （VDI）。 下图显示了 VDI 的逻辑解决方案体系结构。

![地平线部署](media/horizon-deployment.png)

使用此解决方案，您可以完全控制地平线视图管理器和应用卷。 熟悉的 UI、API 和 CLI 接口支持使用现有脚本和工具。

CloudSimple 解决方案要求您执行以下操作：

* 在私有云中安装、配置和管理 VMware Horizon 7.x。
* 提供您自己的 Horizon 许可证。

## <a name="deploy-the-solution"></a>部署解决方案

以下各节介绍如何在私有云中使用 Horizon 部署 VDI 解决方案。

1. [验证 VMware 产品版本是否兼容](#verify-that-vmware-product-versions-are-compatible)
2. [估计桌面环境的大小](#estimate-the-size-of-your-desktop-environment)
3. [为您的环境创建私有云](#create-a-private-cloud-for-your-environment)
4. [在私有云中安装 VMware 地平线](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>验证 VMware 产品版本是否兼容

* 验证您当前和计划版本的 Horizon、应用卷、统一访问网关和用户环境管理器是否相互兼容，并与私有云中的 vCenter 和 PSC 兼容。 有关兼容性信息，请参阅[Horizon 7.5 的 VMware 兼容性矩阵](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=)。
* 要查找私有云中的 vCenter 和 PSC 的当前版本，请转到[云简单门户](access-cloudsimple-portal.md)中的 **"资源"，** 选择您的私有云，然后单击**vSphere 管理网络**选项卡。

![vCenter 和 PSC 版本](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>估计桌面环境的大小

* 验证您标识的配置是否在 VMware 操作限制范围内。
* 估计所有桌面和 Horizon 管理组件所需的资源。

### <a name="create-a-private-cloud-for-your-environment"></a>为您的环境创建私有云

1. 按照[配置私有云环境](quickstart-create-private-cloud.md)中的说明，从云简单门户创建私有云。  CloudSimple 在每个新创建的私有云中创建名为"云所有者"的默认 vCenter 用户。 有关默认私有云用户和权限模型的详细信息，请参阅[了解私有云权限模型](learn-private-cloud-permissions.md)。
2. 在私有云中为 Horizon 管理平面创建 VLAN，并为其分配子网 CIDR。 有关说明，请参阅[创建和管理 VLAN/子网](create-vlan-subnet.md)。 这是将安装所有解决方案组件（统一访问网关、连接服务器、应用卷服务器和用户环境管理器服务器）的网络。
3. 决定是否要将外部标识提供商与私有云 vCenter 一起使用。 如果是，请选择以下选项之一：
    * 使用本地活动目录作为外部标识提供程序。 有关说明，请参阅[vCenter 标识源](set-vcenter-identity.md)。
    * 在 Horizon 管理平面 VLAN 中的私有云中设置活动目录服务器，以用作外部标识提供程序。 有关说明，请参阅[vCenter 标识源](set-vcenter-identity.md)。
    * 在私有云中的 Horizon 管理平面 VLAN 中设置 DHCP 和 DNS 服务器。 有关说明，请参阅[在云简单私有云中设置 DNS 和 DHCP 应用程序和工作负载](dns-dhcp-setup.md)。
4. 在私有云中安装的 DNS 服务器上配置 DNS 转发。 有关说明，请参阅[创建条件转发器](on-premises-dns-setup.md#create-a-conditional-forwarder)。

### <a name="install-vmware-horizon-in-your-private-cloud"></a>在私有云中安装 VMware 地平线

以下部署图描述了部署在私有云中的 Horizon 解决方案。 统一访问网关、AD/DC、视图和应用卷服务器安装在用户创建的 VLAN 234 中。 统一访问网关具有可从 Internet 访问的指定公共 IP 地址。 Horizon 桌面池 VM 部署在 VLAN 235 中，以提供额外的隔离和安全性。

![私有云中的地平线部署](media/horizon-private-cloud.png)

以下各节概述了设置类似于图中所示的部署的说明。 在开始之前，请验证您具有以下各项：

* 使用 CloudSimple 门户创建的私有云，具有运行桌面池的足够容量。
* 本地环境和私有云环境之间有足够的带宽，以支持桌面的网络流量。
* 在本地数据中心和私有云之间设置的站点到站点 VPN 隧道。
* 从本地环境中的最终用户子网到云简单私有云子网的 IP 可访问性。
* 为您的私有云安装 AD/DHCP/DNS。

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>云简单门户：为桌面池创建专用 VLAN/子网

为 Horizon 桌面池创建 VLAN 并为其分配子网 CIDR。 有关说明，请参阅[创建和管理 VLAN/子网](create-vlan-subnet.md)。 这是所有桌面虚拟机都将运行的网络。

遵循标准安全最佳实践来保护 Horizon 部署：

* 仅允许桌面 RDP 流量/SSH 流量到桌面 VM。
* 只允许 Horizon 管理平面 VLAN 和桌面池 VLAN 之间的管理流量。
* 只允许来自本地网络的管理流量。

您可以通过从 CloudSimple 门户配置[防火墙规则](firewall.md)来强制实施这些最佳实践。

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>云简单门户：配置防火墙规则以保护 Horizon 管理平面

在 CloudSimple 门户中设置以下规则。 有关说明，请参阅[设置防火墙表和规则](firewall.md)。

1. 在 CloudSimple N-S 防火墙中配置防火墙规则，以允许本地子网和 Horizon 管理 VLAN 之间的通信，以便仅允许 VMware 文档[Horizon 端口列表中](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html)列出的网络端口。

2. 在私有云中的 Horizon 管理 VLAN 和桌面池 VLAN 之间创建 E-W 防火墙规则。

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>云简单门户：为统一访问网关创建公共 IP 地址

为统一接入网关设备创建公共 IP 地址，以启用来自 Internet 的桌面客户端连接。 有关说明，请参阅[分配公共 IP 地址](public-ips.md)。

设置完成后，公共 IP 地址将分配并在公共 IP 页上列出。

#### <a name="cloudsimple-portal-escalate-privileges"></a>云简单门户：升级权限

默认的"云所有者"用户在私有云 vCenter 中没有足够的权限来安装 Horizon，因此必须升级用户的 vCenter 权限。 有关详细信息，请参阅[升级权限](escalate-private-cloud-privileges.md)。

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter UI：在私有云中创建用户，用于地平线安装

1. 使用"云所有者"用户凭据登录到 vCenter。
2. 在 vCenter 中创建新用户"horizon-soln-admin"，并将该用户添加到 vCenter 中的管理员组。
3. 注销 vCenter 作为"云所有者"用户，并登录为"horizon-soln-admin"用户。

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI：安装 VMware 地平线

如前面逻辑体系结构部分所述，Horizon 解决方案具有以下组件：

* VMware 地平线视图
* VMware 统一接入网关
* VMware 应用程序卷管理器
* VMware 用户环境管理器

安装组件如下：

1. 安装和配置统一访问网关，按照 VMware 文档中提供的说明[部署和配置 VMware 统一访问网关](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)。

2. 按照[视图安装指南](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)中的说明在私有云中安装地平线视图。

3. 按照[安装和配置 VMware 应用卷](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)中的说明安装应用卷管理器。

4. 按照[有关安装和配置 VMware 用户环境管理器中的说明来安装和配置用户环境管理器](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)。

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>提交支持请求以上传 VMware Horizon 预打包的应用程序卷

作为安装过程的一部分，应用卷管理器使用预打包的卷来预配应用堆栈和可写卷。 这些卷用作应用堆栈和可写卷的模板。

将卷上载到私有云数据存储需要 ESXi 根密码。 为提供帮助，请提交[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 附加 App卷安装程序捆绑包，以便 CloudSimple 支持人员可以将模板上载到您的私有云环境。

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>云简单门户：降级权限

您现在可以[取消"](escalate-private-cloud-privileges.md#de-escalate-privileges)云所有者"用户的权限。

## <a name="ongoing-management-of-your-horizon-solution"></a>持续管理您的 Horizon 解决方案

您可以完全控制私有云环境中的 Horizon 和 App 卷管理器软件，并应执行必要的软件生命周期管理。 在更新或升级 Horizon 或应用卷之前，请确保任何新版本的软件都与私有云 vCenter 和 PSC 兼容。
