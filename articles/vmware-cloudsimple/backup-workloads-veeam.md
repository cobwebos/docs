---
title: Azure VMware 解决方案（按云简单 - 使用 Veeam 备份私有云上的工作负载虚拟机）
description: 描述如何使用 Veeam B&R 9.5 备份在基于 Azure 的云简单私有云中运行的虚拟机
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3262841efb9109b1de24fe501ea0a7bea0dd612d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025123"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>使用 Veeam B&R 备份云简单私有云上的工作负载 VM

本指南介绍如何使用 Veeam B&R 9.5 备份在基于 Azure 的云简单私有云中运行的虚拟机。

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>关于 Veeam 备份和恢复解决方案

Veeam 解决方案包括以下组件。

**备份服务器**

备份服务器是 Windows 服务器 （VM），用作 Veeam 的控制中心并执行以下功能： 

* 协调备份、复制、恢复验证和恢复任务
* 控制作业调度和资源分配
* 允许您设置和管理备份基础结构组件，并为备份基础结构指定全局设置

**代理服务器**

代理服务器安装在备份服务器和备份基础结构的其他组件之间。 它们管理以下功能：

* 从生产存储中检索 VM 数据
* 压缩
* 重复数据删除
* 加密
* 将数据传输到备份存储库

**备份存储库**

备份存储库是 Veeam 保存复制 VM 的备份文件、VM 副本和元数据的存储位置。  存储库可以是具有本地磁盘（或装载的 NFS/SMB）的 Windows 或 Linux 服务器，也可以是硬件存储重复数据消除设备。

### <a name="veeam-deployment-scenarios"></a>Veeam 部署方案
可以利用 Azure 提供备份存储库和存储目标，用于长期备份和存档。 私有云中的 VM 和 Azure 中的备份存储库之间的所有备份网络流量都通过高带宽、低延迟链路传输。 跨区域的复制流量通过内部 Azure 背板网络传输，从而降低用户的带宽成本。

**基本部署**

对于要备份的结核病小于 30 TB 的环境，CloudSimple 建议进行以下配置：

* 安装在私有云中同一 VM 上的 Veeam 备份服务器和代理服务器。
* Azure 中基于 Linux 的主备份存储库配置为备份作业的目标。
* `azcopy`用于将数据从主备份存储库复制到复制到另一个区域的 Azure Blob 容器。

![基本部署方案](media/veeam-basicdeployment.png)

**高级部署**

对于要备份超过 30 TB 的环境，CloudSimple 建议进行以下配置：

* 如 Veeam 建议，vSAN 群集中每个节点有一个代理服务器。
* 私有云中基于 Windows 的主备份存储库可缓存五天的数据，以便快速还原。
* Azure 中的 Linux 备份存储库作为备份副本作业的目标，具有较长的持续时间保留时间。 此存储库应配置为横向扩展备份存储库。
* `azcopy`用于将数据从主备份存储库复制到复制到另一个区域的 Azure Blob 容器。

![基本部署方案](media/veeam-advanceddeployment.png)

在上图中，请注意备份代理是一个 VM，具有对 vSAN 数据存储上的工作负载 VM 磁盘的热添加访问权限。 Veeam 使用虚拟设备备份代理传输模式进行 vSAN。

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>云简单 Veeam 解决方案的要求

Veeam 解决方案要求您执行以下操作：

* 提供您自己的 Veeam 许可证。
* 部署和管理 Veeam 以备份云简单私有云中运行的工作负载。

此解决方案为您提供了对 Veeam 备份工具的完全控制，并提供了使用本机 Veeam 接口或 Veeam vCenter 插件来管理 VM 备份作业的选择。

如果您是现有 Veeam 用户，则可以跳过 Veeam 解决方案组件部分，直接转到[Veeam 部署方案](#veeam-deployment-scenarios)。

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>在云简单私有云中安装和配置 Veeam 备份

以下各节介绍如何为云简单私有云安装和配置 Veeam 备份解决方案。

部署过程包括以下步骤：

1. [vCenter UI：在私有云中设置基础设施服务](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [云简单门户：为 Veeam 设置私有云网络](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [云简单门户：升级权限](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure 门户：将虚拟网络连接到私有云](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure 门户：在 Azure 中创建备份存储库](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure 门户：配置 Azure Blob 存储以进行长期数据保留](#configure-azure-blob-storage-for-long-term-data-retention)
7. [私有云的 vCenter UI：安装 Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam 控制台：配置 Veeam 备份&恢复软件](#veeam-console-install-veeam-backup-and-recovery-software)
9. [云简单门户：设置 Veeam 访问权限和降级权限](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>开始之前

在开始 Veeam 部署之前，需要以下操作：

* 您拥有的 Azure 订阅
* 预先创建的 Azure 资源组
* 订阅中的 Azure 虚拟网络
* 一个 Azure 存储帐户
* 使用云简单门户创建的[私有云](create-private-cloud.md)。  

在实施阶段需要以下项目：

* VMware 模板，用于安装 Veeam（如 Windows 服务器 2012 R2 - 64 位映像）
* 为备份网络标识了一个可用的 VLAN
* 要分配给备份网络的子网的 CIDR
* Veeam 9.5 u3 可安装媒体 （ISO） 上传到私有云的 vSAN 数据存储

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter UI：在私有云中设置基础设施服务

在私有云中配置基础设施服务，以便轻松管理工作负载和工具。

* 您可以添加外部标识提供程序，如["设置 vCenter 标识源"中所述，以便使用 Active Directory（](set-vcenter-identity.md)如果以下任一项适用）：

  * 您希望从私有云中的本地活动目录 （AD） 中标识用户。
  * 您希望在私有云中为所有用户设置 AD。
  * 您想要使用 Azure AD。
* 要为私有云中的工作负载提供 IP 地址查找、IP 地址管理和名称解析服务，请设置 DHCP 和 DNS 服务器，如[在云简单私有云中设置 DNS 和 DHCP 应用程序和工作负载中](dns-dhcp-setup.md)所述。

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>云简单私有云：为 Veeam 设置私有云网络

访问 CloudSimple 门户，为 Veeam 解决方案设置私有云网络。

为备份网络创建 VLAN 并为其分配子网 CIDR。 有关说明，请参阅[创建和管理 VLAN/子网](create-vlan-subnet.md)。

在管理子网和备份网络之间创建防火墙规则，以允许 Veeam 使用的端口上的网络流量。 请参阅 Veeam 主题["已使用端口](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)"。 有关防火墙规则创建的说明，请参阅[设置防火墙表和规则](firewall.md)。

下表提供了端口列表。

| 图标 | 描述 | 图标 | 描述 |
| ------------ | ------------- | ------------ | ------------- |
| 备份服务器  | vCenter  | HTTPS / TCP  | 443 |
| 备份服务器 <br> *部署 Veeam 备份&复制组件所需的* | 备份代理  | TCP/UDP  | 135、137 到 139 和 445 |
    | 备份服务器   | DNS  | UDP  | 53  | 
    | 备份服务器   | Veeam 更新通知服务器  | TCP  | 80  | 
    | 备份服务器   | Veeam 许可证更新服务器  | TCP  | 443  | 
    | 备份代理   | vCenter |   |   | 
    | 备份代理  | Linux 备份存储库   | TCP  | 22  | 
    | 备份代理  | 窗口备份存储库  | TCP  | 49152 - 65535   | 
    | 备份存储库  | 备份代理  | TCP  | 2500 -5000  | 
    | 源备份存储库<br> *用于备份复制作业*  | 目标备份存储库  | TCP  | 2500 - 5000  | 

在工作负载子网和备份网络之间创建防火墙规则，如[设置防火墙表和规则](firewall.md)中所述。  对于应用程序感知备份和还原，必须在承载特定应用程序的工作负载 VM 上打开[其他端口](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)。

默认情况下，CloudSimple 提供 1Gbps 快速路由链接。 对于较大的环境大小，可能需要更高的带宽链路。 有关高带宽链接的详细信息，请与 Azure 支持部门联系。

要继续设置，您需要授权密钥和对等电路 URI 以及访问 Azure 订阅。  此信息可在云简单门户中的虚拟网络连接页面上找到。 有关说明，请参阅[获取 Azure 虚拟网络到云简单连接的对等信息](virtual-network-connection.md)。 如果您在获取信息时遇到任何问题，[请联系支持](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)人员。

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>云简单私有云：提升云所有者的权限

默认的"云所有者"用户在私有云 vCenter 中没有足够的权限来安装 VEEAM，因此必须升级用户的 vCenter 权限。 有关详细信息，请参阅[升级权限](escalate-private-cloud-privileges.md)。

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure 门户：将虚拟网络连接到私有云

使用 ExpressRoute 按照[Azure 虚拟网络连接](azure-expressroute-connection.md)中的说明将虚拟网络连接到私有云。

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure 门户：创建备份存储库 VM

1. 创建具有（2 个 vCPU 和 8 GB 内存）的标准 D2 v3 VM。
2. 选择基于 CentOS 7.4 的图像。
3. 为 VM 配置网络安全组 （NSG）。 验证 VM 没有公共 IP 地址，并且无法从公共 Internet 进行。
4. 为新 VM 创建基于用户名和密码的用户帐户。 有关说明，请参阅[在 Azure 门户中创建 Linux 虚拟机](../virtual-machines/linux/quick-create-portal.md)。
5. 创建 1x512 GiB 标准硬盘并将其附加到存储库 VM。  有关说明，请参阅[如何将托管数据磁盘附加到 Azure 门户中的 Windows VM。](../virtual-machines/windows/attach-managed-disk-portal.md)
6. [在托管磁盘上创建 XFS 卷](https://www.digitalocean.com/docs/volumes/how-to/)。 使用前面提到的凭据登录到 VM。 执行以下脚本以创建逻辑卷、向其添加磁盘、创建 XFS 文件系统[分区](https://www.digitalocean.com/docs/volumes/how-to/partition/)以及[将分区装载](https://www.digitalocean.com/docs/volumes/how-to/mount/)到 /backup1 路径下。

    示例脚本：

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. 将 /backup1 作为 NFS 装载点公开/备份，以指向在私有云中运行的 Veeam 备份服务器。 有关说明，请参阅数字海洋文章["如何在 CentOS 6 上设置 NFS 山"。](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6) 在 Veeam 备份服务器中配置备份存储库时，使用此 NFS 共享名称。

8. 在 NSG 中为备份存储库 VM 配置筛选规则，以显式允许所有网络流量进入或从 VM 传输。

> [!NOTE]
> Veeam 备份&复制使用 SSH 协议与 Linux 备份存储库进行通信，并且需要 Linux 存储库上的 SCP 实用程序。 验证 SSH 守护进程配置正确，以及 SCP 在 Linux 主机上是否可用。

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>配置 Azure Blob 存储以进行长期数据保留

1. 创建标准类型的通用存储帐户 （GPv2） 和 Blob 容器，如 Microsoft 视频["开始使用 Azure 存储"](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)中所述。
2. 创建 Azure 存储容器，如[创建容器](https://docs.microsoft.com/rest/api/storageservices/create-container)引用中所述。
2. 从微软`azcopy`下载 Linux 的命令行实用程序。 您可以在 CentOS 7.5 中的 bash 外壳中使用以下命令。

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. 使用`azcopy`命令将备份文件复制到 blob 容器中。这些操作将备份文件复制到或从 blob 容器复制。  有关详细命令[，请参阅在 Linux 上使用 AzCopy 传输数据](../storage/common/storage-use-azcopy-linux.md)。

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>私有云的 vCenter 控制台：安装 Veeam B&R

从私有云访问 vCenter 以创建 Veeam 服务帐户，安装 Veeam B&R 9.5，并使用服务帐户配置 Veeam。

1. 创建名为"Veeam 备份角色"的新角色，并根据需要分配 Veeam 的建议权限。 有关详细信息，请参阅 Veeam 主题["必需权限](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)"。
2. 在 vCenter 中创建新的"Veeam 用户组"组，并将其分配给"Veeam 备份角色"。
3. 创建新的"Veeam 服务帐户"用户并将其添加到"Veeam 用户组"。

    ![创建 Veeam 服务帐户](media/veeam-vcenter01.png)

4. 使用备份网络 VLAN 在 vCenter 中创建分布式端口组。 有关详细信息，请查看 VMware 视频[在 vSphere Web 客户端中创建分布式端口组](https://www.youtube.com/watch?v=wpCd5ZbPOpA)。
5. 根据[Veeam 系统要求](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)，在 vCenter 中为 Veeam 备份和代理服务器创建 VM。 您可以使用 Windows 2012 R2 或 Linux。 有关详细信息，请参阅[使用 Linux 备份存储库的要求](https://www.veeam.com/kb2216)。
6. 将可安装的 Veeam ISO 作为 CDROM 设备安装在 Veeam 备份服务器 VM 中。
7. 使用 RDP 会话到 Windows 2012 R2 计算机（Veeam 安装的目标），在 Windows 2012 R2 VM 上安装[Veeam B&R 9.5u3。](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
8. 查找 Veeam 备份服务器 VM 的内部 IP 地址，并将 IP 地址配置为在 DHCP 服务器中为静态地址。 执行此操作所需的确切步骤取决于 DHCP 服务器。 例如，Netgate 文章静态<a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">DHCP 映射</a>解释了如何使用 pfSense 路由器配置 DHCP 服务器。

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam 控制台：安装 Veeam 备份和恢复软件

使用 Veeam 控制台配置 Veeam 备份和恢复软件。 有关详细信息，请参阅[Veeam 备份&复制 v9 - 安装和部署](https://www.youtube.com/watch?v=b4BqC_WXARk)。

1. 将 VMware vSphere 添加为托管服务器环境。 当出现提示时，提供您在私有云 vCenter 控制台开头创建的 Veeam 服务帐户[的凭据：安装 Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)。

    * 对负载控制和默认高级设置使用默认设置。
    * 将装载服务器位置设置为备份服务器。
    * 将 Veeam 服务器的配置备份位置更改为远程存储库。

2. 将 Azure 中的 Linux 服务器添加为备份存储库。

    * 使用默认设置进行负载控制和高级设置。 
    * 将装载服务器位置设置为备份服务器。
    * 将 Veeam 服务器的配置备份位置更改为远程存储库。

3. 使用**主>配置备份设置**启用配置备份加密。

4. 将 Windows 服务器 VM 添加为 VMware 环境的代理服务器。 使用代理的"流量规则"，通过线路加密备份数据。

5. 配置备份作业。
    * 要配置备份作业，请按照[创建备份作业](https://www.youtube.com/watch?v=YHxcUFEss4M)中的说明操作。
    * 在 **"存储">** 启用备份文件加密。

6. 配置备份复制作业。

    * 要配置备份复制作业，请按照视频中的说明[创建备份复制作业](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)。
    * 在 **"存储">** 启用备份文件加密。

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>云简单门户：设置 Veeam 访问权限和降级权限
为 Veeam 备份和恢复服务器创建公共 IP 地址。 有关说明，请参阅[分配公共 IP 地址](public-ips.md)。

创建防火墙规则，用于允许 Veeam 备份服务器创建到 Veeam 网站的出站连接，以便在 TCP 端口 80 上下载更新/修补程序。 有关说明，请参阅[设置防火墙表和规则](firewall.md)。

要取消权限，请参阅[降级特权](escalate-private-cloud-privileges.md#de-escalate-privileges)。

## <a name="references"></a>参考

### <a name="cloudsimple-references"></a>云简单引用

* [创建私有云](create-private-cloud.md)
* [创建和管理 VLAN/子网](create-vlan-subnet.md)
* [vCenter 标识源](set-vcenter-identity.md)
* [工作负载 DNS 和 DHCP 设置](dns-dhcp-setup.md)
* [提升特权](escalate-privileges.md)
* [设置防火墙表和规则](firewall.md)
* [私有云权限](learn-private-cloud-permissions.md)
* [分配公共 IP 地址](public-ips.md)

### <a name="veeam-references"></a>Veeam 参考文献

* [已使用端口](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [所需的权限](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [系统要求](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [安装 Veeam 备份&复制](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Linux 的多操作系统 FLR 和存储库支持所需的模块和权限](https://www.veeam.com/kb2216)
* [Veeam 备份&复制 v9 - 安装和部署 - 视频](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 创建备份作业 - 视频](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 创建备份复制作业 - 视频](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure 引用

* [使用 Azure 门户配置 ExpressRoute 的虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [将 VNet 连接到线路 - 不同订阅](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [在 Azure 门户中创建 Linux 虚拟机](../virtual-machines/linux/quick-create-portal.md)
* [如何在 Azure 门户中将托管数据磁盘附加到 Windows VM](../virtual-machines/windows/attach-managed-disk-portal.md)
* [开始使用 Azure 存储 - 视频](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Create Container](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [使用 AzCopy on Linux 传输数据](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>VMware 参考

* [在 vSphere Web 客户端中创建分布式端口组 - 视频](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>其他参考资料

* [在托管磁盘上创建 XFS 卷 - RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [如何在 CentOS 7 上设置 NFS 安装 - 如何锻造](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [配置 DHCP 服务器 - 网门](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
