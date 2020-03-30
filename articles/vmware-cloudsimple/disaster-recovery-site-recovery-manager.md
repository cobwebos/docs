---
title: Azure VMware 解决方案（按云简单 - 使用 VMware 站点恢复管理器将私有云设置为灾难恢复站点）
description: 描述如何将云简单私有云设置为本地 VMware 工作负载的灾难恢复站点
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa8b2da683d68a337df38e13726f22c5af43540a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565921"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>使用 VMware 站点恢复管理器将私有云设置为灾难恢复目标

您可以将云简单私有云用作本地 VMware 工作负载的灾难恢复 （DR） 站点。

DR 解决方案基于 vSphere 复制和 VMware 站点恢复管理器 （SRM）。 可以采用类似方法，使私有云成为受本地恢复站点保护的主站点。

云简单解决方案：

* 无需专门为 DR 设置数据中心。
* 允许您利用部署 CloudSimple 的 Azure 位置，实现全球地理恢复能力。
* 为您提供了降低部署成本和建立 DR 的总拥有成本的选项。

CloudSimple 解决方案要求您执行以下操作：

* 在私有云中安装、配置和管理 vSphere 复制和 SRM。
* 当私有云是受保护的站点时，为 SRM 提供您自己的许可证。 当 CloudSimple 站点用作恢复站点时，不需要任何其他 SRM 许可证。

使用此解决方案，您可以完全控制 vSphere 复制和 SRM。 熟悉的 UI、API 和 CLI 接口支持使用现有脚本和工具。

![站点恢复管理器部署](media/srm-deployment.png)

您可以使用与您的私有云和本地环境兼容的任何版本的 vRA 和 SRM。 本指南中的示例使用 vRA 6.5 和 SRM 6.5。 这些版本与 vSphere 6.5 兼容，云简单支持 vSphere 6.5。

## <a name="deploy-the-solution"></a>部署解决方案

以下各节介绍如何在私有云中使用 SRM 部署 DR 解决方案。

1. [验证 VMware 产品版本是否兼容](#verify-that-vmware-product-versions-are-compatible)
2. [估计 DR 环境的大小](#estimate-the-size-of-your-dr-environment)
3. [为您的环境创建私有云](#create-a-private-cloud-for-your-environment)
4. [为 SRM 解决方案设置私有云网络](#set-up-private-cloud-networking-for-the-srm-solution)
5. [在本地网络和私有云之间设置站点到站点 VPN 连接，并打开所需的端口](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [在私有云中设置基础设施服务](#set-up-infrastructure-services-in-your-private-cloud)
7. [在本地环境中安装 vSphere 复制设备](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [在私有云环境中安装 vSphere 复制设备](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [在本地环境中安装 SRM 服务器](#install-srm-server-in-your-on-premises-environment)
10. [在私有云中安装 SRM 服务器](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>验证 VMware 产品版本是否兼容

本指南中的配置受以下兼容性要求的约束：

* 必须在私有云和本地环境中部署同一版本的 SRM。
* 必须在私有云和本地环境中部署相同版本的 vSphere 复制。
* 私有云中的平台服务控制器 （PSC） 版本和本地环境必须兼容。
* 私有云中的 vCenter 版本和本地环境中必须兼容。
* SRM 和 vSphere 复制的版本必须彼此兼容，并且与 PSC 和 vCenter 的版本兼容。

有关相关 VMware 文档和兼容性信息的链接，请访问[VMware 站点恢复管理器](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)文档。

要了解私有云中的 vCenter 和 PSC 版本，请打开云简单门户。 转到 **"资源**"，选择您的私有云，然后单击**vSphere 管理网络**选项卡。

![私有云中的 vCenter & PSC 版本](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>估计 DR 环境的大小

1. 验证已识别的本地配置是否在受支持的限制范围内。 对于 SRM 6.5，限制记录在 VMware 知识库文章中，该文章介绍了[站点恢复管理器 6.5 的操作限制](https://kb.vmware.com/s/article/2147110)。
2. 确保您有足够的网络带宽来满足工作负载大小和 RPO 要求。 VMware 知识库文章，介绍[计算 vSphere 复制的带宽要求](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)，提供有关带宽限制的指导。
3. 使用 CloudSimple Sizer 工具估计 DR 站点中保护本地环境所需的资源。

### <a name="create-a-private-cloud-for-your-environment"></a>为您的环境创建私有云

通过按照["创建私有云](create-private-cloud.md)"中的说明和大小调整建议，从 CloudSimple 门户创建私有云。

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>为 SRM 解决方案设置私有云网络

访问 CloudSimple 门户，为 SRM 解决方案设置私有云网络。

为 SRM 解决方案网络创建 VLAN 并为其分配子网 CIDR。 有关说明，请参阅[创建和管理 VLAN/子网](create-vlan-subnet.md)。

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>在本地网络和私有云之间设置站点到站点 VPN 连接，并打开所需的端口

在本地网络和私有云之间建立站点到站点的连接。 有关说明，请参阅[配置到云简单私有云的 VPN 连接](set-up-vpn.md)。

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>在私有云中设置基础设施服务

在私有云中配置基础设施服务，以便轻松管理工作负载和工具。

如果要执行以下任一操作，可以添加外部标识提供程序，如[使用 Azure AD 作为云简单私有云上 vCenter 的标识提供程序](azure-ad.md)：

* 从私有云中的本地活动目录 （AD） 中标识用户。
* 在私有云中为所有用户设置 AD。
* 使用 Azure AD。

要为私有云中的工作负载提供 IP 地址查找、IP 地址管理和名称解析服务，请设置 DHCP 和 DNS 服务器，如[在云简单私有云中设置 DNS 和 DHCP 应用程序和工作负载中](dns-dhcp-setup.md)所述。

*.cloudsimple.io域由私有云中的管理 VM 和主机使用。 要解析对此域的请求，请按照[创建条件转发器](on-premises-dns-setup.md#create-a-conditional-forwarder)中所述，在 DNS 服务器上配置 DNS 转发。

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>在本地环境中安装 vSphere 复制设备

通过遵循 VMware 文档，在本地环境中安装 vSphere 复制设备 （vRA）。 安装由以下高级步骤组成：

1. 为 vRA 安装准备本地环境。

2. 使用 VR ISO 中的 OVF 从 vmware.com 在本地环境中部署 vRA。 对于 vRA 6.5，[此 VMware 博客](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)具有相关信息。

3. 在本地站点使用 vCenter 单点登录注册本地 vRA。 有关 vSphere 复制 6.5 的详细说明，请参阅 VMware 文档[VMware vSphere 复制 6.5 安装和配置](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)。

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>在私有云环境中安装 vSphere 复制设备

在开始之前，请验证您具有以下各项：

* 从本地环境中的子网到私有云的管理子网的 IP 可访问性
* 从本地 vSphere 环境中的复制子网到私有云的 SRM 解决方案子网的 IP 可访问性

有关说明，请参阅[配置到云简单私有云的 VPN 连接](set-up-vpn.md)。 这些步骤与本地安装的步骤类似。

CloudSimple 建议在 vRA 和 SRM 安装期间使用 FQDN 而不是 IP 地址。 要了解私有云中的 vCenter 和 PSC 的 FQDN，请打开云简单门户。 转到 **"资源**"，选择您的私有云，然后单击**vSphere 管理网络**选项卡。

![在私有云中查找 vCenter/PSC 的 FQDN](media/srm-resources.png)

CloudSimple 要求您不使用默认的"云所有者"用户安装 vRA 和 SRM，而是创建新用户。 这样做是为了帮助确保私有云 vCenter 环境的高停机时间和可用性。 但是，私有云 vCenter 中的默认云所有者用户没有足够的权限来创建具有管理权限的新用户。

在安装 vRA 和 SRM 之前，必须升级云所有者用户的 vCenter 权限，然后在 vCenter SSO 域中创建具有管理权限的用户。 有关默认私有云用户和权限模型的详细信息，请参阅[了解私有云权限模型](learn-private-cloud-permissions.md)。

安装由以下高级步骤组成：

1. [升级权限](escalate-private-cloud-privileges.md)。
2. 在私有云中创建用户，用于 vSphere 复制和 SRM 安装。 下面在[vCenter UI 中解释：在私有云中创建用户，用于 vRA & SRM 安装](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation)。
3. 为 vRA 安装准备私有云环境。
4. 使用 vr ISO 中的 OVF 从 vmware.com 在私有云中部署 vRA。 对于 vRA 6.5，[此 VMware 博客](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)具有相关信息。
5. 为 vRA 配置防火墙规则。 下面在[云简单门户中解释：为 vRA 配置防火墙规则](#cloudsimple-portal-configure-firewall-rules-for-vra)。
6. 在私有云站点使用 vCenter 单一登录注册私有云 vRA。
7. 配置两个设备之间的 vSphere 复制连接。 确保在整个防火墙上打开所需的端口。 有关 vSphere 复制 6.5 必须打开的端口号列表，请参阅[此 VMware 知识库文章](https://kb.vmware.com/s/article/2087769)。

有关 vSphere 复制 6.5 的详细安装说明，请参阅 VMware 文档[VMware vSphere 复制 6.5 安装和配置](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)。

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter UI：在私有云中创建用户，用于 vRA 和 SRM 安装

在从 CloudSimple 门户升级权限后，使用云所有者用户凭据登录到 vCenter。

在 vCenter`srm-soln-admin`中创建新用户 ，并将其添加到 vCenter 中的管理员组。
注销 vCenter 作为云所有者用户，并作为*srm-soln 管理员*用户登录。

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>云简单门户：为 vRA 配置防火墙规则

将防火墙规则配置为打开端口[Set up firewall tables and rules](firewall.md)，以便在以下之间通信：

* vRA 在 SRM 解决方案网络中，vCenter 和 ESXi 托管在管理网络中。
* 两个地点的 vRA 设备。

有关 vSphere 复制 6.5 必须打开的端口号列表，请参阅此[VMware 知识库文章](https://kb.vmware.com/s/article/2087769)。

### <a name="install-srm-server-in-your-on-premises-environment"></a>在本地环境中安装 SRM 服务器

开始之前，请验证以下内容：

* vSphere 复制设备安装在本地和私有云环境中。
* 两个站点的 vSphere 复制设备相互连接。
* 您已经查看了有关先决条件和最佳实践的 VMware 信息。 对于 SRM 6.5，您可以参考[SRM 6.5 的](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)VMware 文档先决条件和最佳实践。

按照 VMware 文档执行 SRM 服务器安装的部署模型"每个平台服务控制器具有一个 vCenter 实例的双站点拓扑"，如本[VMWare 文档中](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)所述。 SRM 6.5 的安装说明在 VMware 文档[安装站点恢复管理器](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)中提供。

### <a name="install-srm-server-in-your-private-cloud"></a>在私有云中安装 SRM 服务器

开始之前，请验证以下内容：

* vSphere 复制设备安装在本地和私有云环境中。
* 两个站点的 vSphere 复制设备相互连接。
* 您已经查看了有关先决条件和最佳实践的 VMware 信息。 对于 SRM 6.5，您可以参考[站点恢复管理器 6.5 服务器安装的先决条件和最佳实践](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)。

以下步骤描述了私有云 SRM 安装。

1. [vCenter UI：安装 SRM](#vcenter-ui-install-srm)
2. [云简单门户：为 SRM 配置防火墙规则](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter UI：配置 SRM](#vcenter-ui-configure-srm)
4. [云简单门户：降级权限](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI：安装 SRM

使用 srm-soln-admin 凭据登录到 vCenter 后，请按照 VMware 文档执行部署模型中的 SRM 服务器安装"每个平台服务控制器具有一个 vCenter 实例的双站点拓扑"，如本[VMWare 文档中](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)所述。 SRM 6.5 的安装说明在 VMware 文档[安装站点恢复管理器](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)中提供。

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>云简单门户：为 SRM 配置防火墙规则

按照[设置防火墙表和规则](firewall.md)中所述配置防火墙规则，以允许在以下之间通信：

私有云中的 SRM 服务器和 vCenter / PSC。
两个站点的 SRM 服务器

有关 vSphere 复制 6.5 必须打开的端口号列表，请参阅[此 VMware 知识库文章](https://kb.vmware.com/s/article/2087769)。

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI：配置 SRM

在私有云中安装 SRM 后，请执行 VMware 站点恢复管理器安装和配置指南部分中所述的以下任务。 对于 SRM 6.5，这些说明可在 VMware 文档[安装站点恢复管理器](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)中提供。

1. 在受保护和恢复站点上连接站点恢复管理器服务器实例。
2. 建立与远程站点恢复管理器服务器实例的客户端连接。
3. 安装站点恢复管理器许可证密钥。

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>云简单门户：降级权限

要取消权限，请参阅[降级特权](escalate-private-cloud-privileges.md#de-escalate-privileges)。

## <a name="ongoing-management-of-your-srm-solution"></a>持续管理您的 SRM 解决方案

您可以完全控制私有云环境中的 vSphere 复制和 SRM 软件，并应执行必要的软件生命周期管理。 在更新或升级 vSphere 复制或 SRM 之前，请确保任何新版本的软件都与私有云 vCenter 和 PSC 兼容。

> [!NOTE]
> CloudSimple 目前正在探索提供托管 DR 服务的选项。 

## <a name="multiple-replication-configuration"></a>多重复制配置

 [基于阵列的复制和 vSphere 复制技术都可以与 SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)同时使用。 但是，它们必须应用于单独的 VM 集（给定 VM 可以通过基于阵列的复制或 vSphere 复制进行保护，但不能同时保护这两者）。 此外，CloudSimple 网站可以配置为多个受保护站点的恢复站点。 有关多站点配置的信息，请参阅[SRM 多站点选项](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/)。

## <a name="references"></a>参考

* [VMware 站点恢复管理器文档](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [站点恢复管理器 6.5 的操作限制](https://kb.vmware.com/s/article/2147110)
* [计算 vSphere 复制的带宽要求](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [部署 vSphere 复制 6.5 时的 OVF 选择](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere 复制 6.5 安装和配置](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [SRM 6.5 的先决条件和最佳实践](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [双站点拓扑中的站点恢复管理器，每个平台服务控制器具有一个 vCenter 服务器实例](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware 站点恢复管理器 6.5 安装和配置指南](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [VMware 博客上基于阵列的复制与 vSphere 复制](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [VMware 博客关于 SRM 多站点选项](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [vSphere 复制 5.8.x、6.x 和 8 必须打开的端口号](https://kb.vmware.com/s/article/2147112)
