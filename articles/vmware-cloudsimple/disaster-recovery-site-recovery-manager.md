---
title: Azure VMware 解决方案 (通过 CloudSimple)-使用 VMware Site Recovery Manager 将私有云设置为灾难恢复站点
description: 介绍如何将 CloudSimple 私有云设置为本地 VMware 工作负荷的灾难恢复站点
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7af6abefd132df7980bdc6e485734a996761653b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972799"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>使用 VMware Site Recovery Manager 将私有云设置为灾难恢复目标

你可以使用 CloudSimple 私有云作为本地 VMware 工作负荷的灾难恢复 (DR) 站点。

DR 解决方案基于 vSphere 复制和 VMware Site Recovery Manager (SRM)。 可以遵循类似的方法, 使私有云成为受本地恢复站点保护的主站点。

CloudSimple 解决方案:

* 无需为灾难恢复设置数据中心。
* 允许你利用为全球地理复原而部署 CloudSimple 的 Azure 位置。
* 提供一个选项, 用于降低部署成本, 并提供用于建立 DR 的总拥有成本。

CloudSimple 解决方案要求你执行以下操作:

* 在私有云中安装、配置和管理 vSphere 复制和 SRM。
* 当私有云为受保护的站点时, 为 SRM 提供自己的许可证。 当 CloudSimple 站点用作恢复站点时, 不需要任何其他的 SRM 许可证。

使用此解决方案, 你可以完全控制 vSphere 复制和 SRM。 熟悉的 UI、API 和 CLI 接口允许使用现有的脚本和工具。

你可以使用与私有云和本地环境兼容的任何版本的 vRA 和 SRM。 本指南中的示例使用 vRA 6.5 和 SRM 6.5。 这些版本与 CloudSimple 支持的 vSphere 6.5 兼容。

## <a name="deploy-the-solution"></a>部署解决方案

以下部分介绍如何在私有云中使用 SRM 部署灾难恢复解决方案。

1. [验证 VMware 产品版本是否兼容](#verify-that-vmware-product-versions-are-compatible)
2. [估计 DR 环境的大小](#estimate-the-size-of-your-dr-environment)
3. [为环境创建私有云](#create-a-private-cloud-for-your-environment)
4. [为 SRM 解决方案设置私有云网络](#set-up-private-cloud-networking-for-the-srm-solution)
5. [设置本地网络与私有云之间的站点到站点 VPN 连接并打开所需的端口](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [在私有云中设置基础结构服务](#set-up-infrastructure-services-in-your-private-cloud)
7. [在本地环境中安装 vSphere 复制设备](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [在私有云环境中安装 vSphere 复制设备](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [在本地环境中安装 SRM 服务器](#install-srm-server-in-your-on-premises-environment)
10. [在私有云中安装 SRM 服务器](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>验证 VMware 产品版本是否兼容

本指南中的配置服从以下兼容性要求:

* 必须在私有云和本地环境中部署相同的 SRM 版本。
* 必须在私有云和本地环境中部署同一版本的 vSphere 复制。
* 私有云和本地环境中的平台服务控制器 (PSC) 版本必须兼容。
* 私有云中的 vCenter 版本和本地环境必须兼容。
* SRM 和 vSphere 复制的版本必须相互兼容, 并与 PSC 和 vCenter 的版本兼容。

有关 VMware 的相关文档和兼容性信息的链接, 请参阅[vmware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)文档。

若要查看私有云中的 vCenter 和 PSC 版本, 请打开 CloudSimple 门户。 中转到 "**资源**", 选择私有云, 然后单击 " **vSphere 管理网络**" 选项卡。

![vCenter & 私有云中的 PSC 版本](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>估计 DR 环境的大小

1. 验证你确定的本地配置是否在支持的限制范围内。 对于 SRM 6.5, 针对[Site Recovery Manager 6.5 的操作限制](https://kb.vmware.com/s/article/2147110), VMware 知识库文章中记录了这些限制。
2. 确保你有足够的网络带宽来满足工作负载大小和 RPO 要求。 有关[计算 VSphere 复制的带宽要求](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)的 VMware 知识库文章提供了有关带宽限制的指导。
3. 使用 CloudSimple sizer 工具估算灾难恢复站点所需的资源, 以保护本地环境。

### <a name="create-a-private-cloud-for-your-environment"></a>为环境创建私有云

按照[创建私有云](create-private-cloud.md)中的说明和大小调整建议, 从 CloudSimple 门户创建私有云。

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>为 SRM 解决方案设置私有云网络

访问 CloudSimple 门户, 为 SRM 解决方案设置私有云网络。

为 SRM 解决方案网络创建 VLAN, 并为其分配一个子网 CIDR。 有关说明, 请参阅[创建和管理 vlan/子网](create-vlan-subnet.md)。

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>设置本地网络与私有云之间的站点到站点 VPN 连接并打开所需的端口

设置本地网络和私有云之间的站点到站点连接。 有关说明, 请参阅[配置到 CloudSimple 私有云的 VPN 连接](set-up-vpn.md)。

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>在私有云中设置基础结构服务

在私有云中配置基础结构服务, 便于管理工作负荷和工具。

如果你想要执行以下任一操作, 则可以添加外部标识提供者, 如[使用 Azure AD 作为 CloudSimple 私有云上的 vCenter 的标识提供者](azure-ad.md)中所述:

* 在私有云中识别本地 Active Directory (AD) 中的用户。
* 在私有云中为所有用户设置 AD。
* 使用 Azure AD。

若要为私有云中的工作负荷提供 IP 地址查找、IP 地址管理和名称解析服务, 请按照在[CloudSimple 私有云中设置 DNS 和 DHCP 应用程序和工作负载](dns-dhcp-setup.md)中所述设置 DHCP 和 DNS 服务器。

*. Cloudsimple.io 域由专用云中的管理 Vm 和主机使用。 若要解决此域的请求, 请在 DNS 服务器上配置 DNS 转发, 如[创建条件转发器](on-premises-dns-setup.md#create-a-conditional-forwarder)中所述。

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>在本地环境中安装 vSphere 复制设备

按照 VMware 文档在本地环境中安装 vSphere 复制设备 (vRA)。 安装包括以下高级步骤:

1. 准备用于 vRA 安装的本地环境。

2. 使用 vmware.com 中的 VR ISO 中的 OVF 在本地环境中部署 vRA。 对于 vRA 6.5,[此 VMware 博客](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)包含相关信息。

3. 在本地站点上将本地 vRA 注册为 vCenter 单一登录。 有关 vSphere 复制6.5 的详细说明, 请参阅 VMware 文档[VMware vSphere 复制6.5 安装和配置](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)。

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>在私有云环境中安装 vSphere 复制设备

在开始之前, 请确认你具有以下各项:

* 从本地环境中的子网到私有云的管理子网的 IP 可访问性
* 从本地 vSphere 环境中的复制子网到私有云的 SRM 解决方案子网的 IP 可访问性

有关说明, 请参阅[配置到 CloudSimple 私有云的 VPN 连接](set-up-vpn.md)。 步骤类似于本地安装的步骤。

CloudSimple 建议在 vRA 和 SRM 安装过程中使用 Fqdn 而不是 IP 地址。 若要确定私有云中的 vCenter 和 PSC 的 FQDN, 请打开 CloudSimple 门户。 中转到 "**资源**", 选择私有云, 然后单击 " **vSphere 管理网络**" 选项卡。

![在私有云中查找 vCenter/PSC 的 FQDN](media/srm-resources.png)

CloudSimple 要求你不要使用默认的 "cloudowner" 用户安装 vRA 和 SRM, 而是创建新用户。 这样做是为了帮助确保私有云 vCenter 环境的正常运行时间和可用性。 但是, 私有云 vCenter 不中的默认 cloudowner 用户具有足够的权限, 可以创建具有管理权限的新用户。

在安装 vRA 和 SRM 之前, 必须升级 cloudowner 用户的 vCenter 权限, 然后在 vCenter SSO 域中创建具有管理权限的用户。 有关默认私有云用户和权限模型的详细信息, 请参阅[了解私有云权限模型](learn-private-cloud-permissions.md)。

安装包括以下高级步骤:

1. [提升权限](escalate-private-cloud-privileges.md)。
2. 在私有云中创建一个用户, 用于 vSphere 复制和 SRM 安装。 [VCenter UI:在私有云中创建 vRA & SRM 安装](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation)的用户。
3. 准备私有云环境以进行 vRA 安装。
4. 使用 vmware.com 中的 VR ISO 中的 OVF 在私有云中部署 vRA。 对于 vRA 6.5,[此 VMware 博客](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)包含相关信息。
5. 配置 vRA 的防火墙规则。 CloudSimple 门户中[的说明:配置 vRA](#cloudsimple-portal-configure-firewall-rules-for-vra)的防火墙规则。
6. 在私有云站点上将私有 Cloud vRA 注册为 vCenter 单一登录。
7. 在两个设备之间配置 vSphere 复制连接。 确保在防火墙中打开所需的端口。 有关 vSphere 复制6.5 必须打开的端口号的列表, 请参阅[此 VMware 知识库文章](https://kb.vmware.com/s/article/2087769)。

有关 vSphere 复制6.5 的详细安装说明, 请参阅 VMware 文档[VMware vSphere 复制6.5 安装和配置](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)。

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter UI:在私有云中创建用于 vRA 和 SRM 安装的用户

从 CloudSimple 门户提升权限后, 使用 cloudowner 用户凭据登录到 vCenter。

在 vcenter 中创建一个`srm-soln-admin`新用户, 并将其添加到 vcenter 中的 administrators 组。
以 cloudowner 用户的身份注销 vCenter, 并以*srm soln*用户身份登录。

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple 门户:配置 vRA 的防火墙规则

按照[设置防火墙表和规则](firewall.md)中所述配置防火墙规则以打开端口, 以便在之间进行通信:

* vRA 在 SRM 解决方案网络中, 并在管理网络中的 vCenter 和 ESXi 主机中进行。
* 两个站点上的 vRA 装置。

有关 vSphere 复制6.5 必须打开的端口号的列表, 请参阅此[VMware 知识库文章](https://kb.vmware.com/s/article/2087769)。

### <a name="install-srm-server-in-your-on-premises-environment"></a>在本地环境中安装 SRM 服务器

在开始之前, 请验证以下各项:

* vSphere 复制设备安装在本地和私有云环境中。
* 两个站点上的 vSphere 复制设备彼此连接。
* 你已经查看了有关先决条件和最佳实践的 VMware 信息。 对于 SRM 6.5, 你可以参阅 VMware 文档[先决条件和 srm 6.5 的最佳实践](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)。

按照 vmware 文档中的说明, 按照此[vmware 文档](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)中所述, 在部署模型 "包含每个平台服务的一个 vCenter 实例的双站点拓扑" 中执行 SRM 服务器安装。 VMware 6.5 安装说明适用于 VMware 文档[安装 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)。

### <a name="install-srm-server-in-your-private-cloud"></a>在私有云中安装 SRM 服务器

在开始之前, 请验证以下各项:

* vSphere 复制设备安装在本地和私有云环境中。
* 两个站点上的 vSphere 复制设备彼此连接。
* 你已经查看了有关先决条件和最佳实践的 VMware 信息。 对于 SRM 6.5, 可以参考[Site Recovery Manager 6.5 服务器安装的先决条件和最佳方案](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)。

以下步骤介绍了私有云 SRM 安装。

1. [vCenter UI:安装 SRM](#vcenter-ui-install-srm)
2. [CloudSimple 门户:为 SRM 配置防火墙规则](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter UI:配置 SRM](#vcenter-ui-configure-srm)
4. [CloudSimple 门户: 取消提升权限](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI:安装 SRM

使用 soln-管理员凭据登录到 vCenter 后, 请遵循 VMware 文档在部署模型 "包含一个 vCenter 实例的双站点拓扑" 下, 按此 Vmware 中的说明执行 SRM 服务器安装[文档](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)。 VMware 6.5 安装说明适用于 VMware 文档[安装 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)。

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple 门户:为 SRM 配置防火墙规则

按照[设置防火墙表和规则](firewall.md)中所述配置防火墙规则, 以允许在以下各项之间进行通信:

私有云中的 SRM 服务器和 vCenter/PSC。
两个站点上的 SRM 服务器

有关 vSphere 复制6.5 必须打开的端口号的列表, 请参阅[此 VMware 知识库文章](https://kb.vmware.com/s/article/2087769)。

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI:配置 SRM

在私有云中安装 SRM 后, 请按照 VMware Site Recovery 管理器安装和配置指南部分中所述执行以下任务。 对于 SRM 6.5,[安装 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)的 VMware 文档中提供了相关说明。

1. 将 Site Recovery Manager 服务器实例连接到受保护的和恢复站点。
2. 建立与远程 Site Recovery Manager 服务器实例的客户端连接。
3. 安装 Site Recovery Manager 许可证密钥。

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple 门户:反呈报权限

若要取消提升权限, 请参阅[取消提升权限](escalate-private-cloud-privileges.md#de-escalate-privileges)。

## <a name="ongoing-management-of-your-srm-solution"></a>对 SRM 解决方案进行持续管理

你可以完全控制私有云环境中的 vSphere 复制和 SRM 软件, 并且应执行必要的软件生命周期管理。 在更新或升级 vSphere 复制或 SRM 之前, 请确保软件的任何新版本都与私有云 vCenter 和 PSC 兼容。

> [!NOTE]
> CloudSimple 当前正在探索用于提供托管 DR 服务的选项。 

## <a name="multiple-replication-configuration"></a>多个复制配置

 [基于阵列的复制和 vSphere 复制技术可以同时与 SRM 一起使用](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)。 但是, 必须将它们应用到不同的 Vm 集 (可以通过基于阵列的复制或 vSphere 复制来保护给定 VM, 但不能同时使用两者)。 此外, CloudSimple 站点可以配置为多个受保护站点的恢复站点。 有关多站点配置的详细信息, 请参阅[SRM 多站点选项](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/)。

## <a name="references"></a>参考资料

* [VMware Site Recovery Manager 文档](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Site Recovery Manager 6.5 的操作限制](https://kb.vmware.com/s/article/2147110)
* [计算 vSphere 复制的带宽要求](https://kb.vmware.com/s/article/2037268)
* [部署 vSphere 复制时的 OVF 选择6。5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere 复制6.5 安装和配置](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [SRM 6.5 的先决条件和最佳实践](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [具有每个平台服务控制器一个 vCenter Server 实例的两站点拓扑中的 Site Recovery 管理器](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Site Recovery Manager 6.5 安装和配置指南](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [基于阵列的复制与 vSphere 复制的 SRM 博客](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [有关 SRM 多站点选项的 VMware 博客](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [必须为 vSphere 复制5.8、1.x 和8打开的端口号](https://kb.vmware.com/s/article/2147112)
