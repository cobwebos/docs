---
title: Azure VMware 解决方案 (通过 CloudSimple)-使用 Veeam 在私有云上备份工作负荷虚拟机
description: 介绍如何使用 Veeam B & R 9.5 备份在基于 Azure 的 CloudSimple 私有云中运行的虚拟机
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 930e482ab85113ac802932929fdbea358ee26035
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619594"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>使用 Veeam B & R 在 CloudSimple 私有云上备份工作负荷 Vm

本指南介绍如何使用 Veeam B & R 9.5, 备份在基于 Azure 的 CloudSimple 私有云中运行的虚拟机。

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>关于 Veeam 备份和恢复解决方案

Veeam 解决方案包括以下组件。

**备份服务器**

备份服务器是一种 Windows server (VM), 充当 Veeam 的控制中心并执行以下功能: 

* 协调备份、复制、恢复验证和还原任务
* 控制作业计划和资源分配
* 允许你设置和管理备份基础结构组件, 并为备份基础结构指定全局设置

**代理服务器**

在备份服务器和备份基础结构的其他组件之间安装代理服务器。 它们管理以下功能:

* 从生产存储中检索 VM 数据
* 压缩
* 重复数据删除
* 加密
* 将数据传输到备份存储库

**备份存储库**

备份存储库是 Veeam 为复制的 Vm 保留备份文件、VM 副本和元数据的存储位置。  存储库可以是具有本地磁盘的 Windows 或 Linux 服务器 (或已装载的 NFS/SMB), 也可以是硬件存储重复数据删除设备。

### <a name="veeam-deployment-scenarios"></a>Veeam 部署方案
可以利用 Azure 来提供备份存储库和存储目标, 以便进行长期备份和存档。 私有云中 Vm 与 Azure 中的备份存储库之间的所有备份网络流量均通过高带宽、低延迟链接进行。 跨区域的复制流量通过内部 Azure 底板网络传输, 从而降低了用户的带宽成本。

**基本部署**

对于备份量小于 30 TB 的环境, CloudSimple 建议采用以下配置:

* 在私有云中的同一 VM 上安装了 Veeam 备份服务器和代理服务器。
* Azure 中基于 Linux 的主备份存储库配置为备份作业的目标。
* `azcopy`用于将数据从主备份存储库复制到复制到其他区域的 Azure blob 容器。

![基本部署方案](media/veeam-basicdeployment.png)

**高级部署**

对于超过 30 TB 备份的环境, CloudSimple 建议采用以下配置:

* VSAN 群集中每个节点一个代理服务器, 如 Veeam 所建议。
* 私有云中基于 Windows 的主备份存储库, 用于缓存五天的数据进行快速还原。
* Azure 中的 Linux 备份存储库作为备份复制作业的目标, 以实现更长的持续时间保留。 此存储库应配置为扩展备份存储库。
* `azcopy`用于将数据从主备份存储库复制到复制到其他区域的 Azure blob 容器。

![基本部署方案](media/veeam-advanceddeployment.png)

在上图中, 请注意, 备份代理是一种 VM, 它可以热添加对 vSAN 数据存储上的工作负荷 VM 磁盘的访问权限。 Veeam 使用虚拟设备备份代理传输模式实现 vSAN。

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>CloudSimple 上的 Veeam 解决方案的要求

Veeam 解决方案要求你执行以下操作:

* 提供自己的 Veeam 许可证。
* 部署和管理 Veeam 以备份 CloudSimple 私有云中运行的工作负荷。

此解决方案可让你完全控制 Veeam 备份工具, 并可以选择使用本机 Veeam 接口或 Veeam vCenter 插件来管理 VM 备份作业。

如果你是现有的 Veeam 用户, 则可以跳过 Veeam 解决方案组件上的部分, 并直接转到[Veeam 部署方案](#veeam-deployment-scenarios)。

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>在 CloudSimple 私有云中安装和配置 Veeam 备份

以下部分介绍了如何为 CloudSimple 私有云安装和配置 Veeam 备份解决方案。

部署过程包括以下步骤:

1. [vCenter UI:在私有云中设置基础结构服务](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [CloudSimple 门户:为 Veeam 设置私有云网络](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [CloudSimple 门户:提升权限](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure 门户：将虚拟网络连接到私有云](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure 门户：在 Azure 中创建备份存储库](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure 门户：配置 Azure blob 存储以进行长期数据保留](#configure-azure-blob-storage-for-long-term-data-retention)
7. [私有云的 vCenter UI:安装 Veeam B & R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam 控制台:配置 Veeam Backup & 恢复软件](#veeam-console-install-veeam-backup-and-recovery-software)
9. [CloudSimple 门户:设置 Veeam 访问和取消提升权限](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>开始之前

在开始 Veeam 部署之前, 需要满足以下要求:

* 你拥有的 Azure 订阅
* 预先创建的 Azure 资源组
* 订阅中的 Azure 虚拟网络
* 一个 Azure 存储帐户
* 使用 CloudSimple 门户创建的[私有云](create-private-cloud.md)。  

实现阶段需要以下各项:

* 适用于 Windows 的 VMware 模板安装 Veeam (如 Windows Server 2012 R2-64 位映像)
* 为备份网络识别的一个可用 VLAN
* 要分配给备份网络的子网的 CIDR
* Veeam 9.5 u3 可安装媒体 (ISO) 上传到私有云的 vSAN 数据存储

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter UI:在私有云中设置基础结构服务

在私有云中配置基础结构服务, 便于管理工作负荷和工具。

* 如果满足以下任一条件, 则可以按照[将 vCenter 标识源设置为使用 Active Directory](set-vcenter-identity.md)中所述添加外部标识提供程序:

  * 需要在私有云中标识本地 Active Directory (AD) 中的用户。
  * 需要为所有用户在私有云中设置 AD。
  * 要使用 Azure AD。
* 若要为私有云中的工作负荷提供 IP 地址查找、IP 地址管理和名称解析服务, 请按照在[CloudSimple 私有云中设置 DNS 和 DHCP 应用程序和工作负载](dns-dhcp-setup.md)中所述设置 DHCP 和 DNS 服务器。

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple 私有云:为 Veeam 设置私有云网络

访问 CloudSimple 门户, 为 Veeam 解决方案设置私有云网络。

创建用于备份网络的 VLAN, 并为其分配子网 CIDR。 有关说明, 请参阅[创建和管理 vlan/子网](create-vlan-subnet.md)。

在管理子网和备份网络之间创建防火墙规则, 以允许 Veeam 使用的端口上的网络流量。 请参阅 Veeam 主题[使用的端口](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)。 有关创建防火墙规则的说明, 请参阅[设置防火墙表和规则](firewall.md)。

下表提供了端口列表。

| 图标 | 描述 | 图标 | 描述 |
| ------------ | ------------- | ------------ | ------------- |
| 备份服务器  | vCenter  | HTTPS/TCP  | 443 |
| 备份服务器 <br> *部署 Veeam 备份 & 复制组件所必需的* | 备份代理  | TCP/UDP  | 135、137到139和445 |
    | 备份服务器   | DNS  | UDP  | 53  | 
    | 备份服务器   | Veeam 更新通知服务器  | TCP  | 80  | 
    | 备份服务器   | Veeam 许可证更新服务器  | TCP  | 443  | 
    | 备份代理   | vCenter |   |   | 
    | 备份代理  | Linux 备份存储库   | TCP  | 22  | 
    | 备份代理  | Windows 备份存储库  | TCP  | 49152-65535   | 
    | 备份存储库  | 备份代理  | TCP  | 2500-5000  | 
    | 源备份存储库<br> *用于备份复制作业*  | 目标备份存储库  | TCP  | 2500-5000  | 

如[设置防火墙表和规则](firewall.md)中所述, 在工作负荷子网和备份网络之间创建防火墙规则。  对于识别应用程序的备份和还原, 必须在承载特定应用程序的工作负荷 Vm 上打开[其他端口](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)。

默认情况下, CloudSimple 提供 1Gbps ExpressRoute 链接。 对于更大的环境大小, 可能需要较高的带宽链接。 若要详细了解更多带宽链接, 请联系 Azure 支持。

若要继续安装, 需要授权密钥和对等线路 URI 以及对 Azure 订阅的访问权限。  此信息可在 CloudSimple 门户中的 "虚拟网络连接" 页面上找到。 有关说明, 请参阅[获取 Azure 虚拟网络对等互连信息以 CloudSimple 连接](virtual-network-connection.md)。 如果在获取信息时遇到任何问题, 请[联系支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple 私有云:提升 cloudowner 的权限

默认值为 "cloudowner" 的用户在私有云 vCenter 中没有足够的权限来安装 VEEAM, 因此必须升级用户的 vCenter 特权。 有关详细信息, 请参阅[提升权限](escalate-private-cloud-privileges.md)。

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure 门户：将虚拟网络连接到私有云

按照[使用 ExpressRoute 的 Azure 虚拟网络连接](azure-expressroute-connection.md)中的说明, 将虚拟网络连接到私有云。

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure 门户：创建备份存储库 VM

1. 使用 (2 个 vcpu 和 8 GB 内存) 创建标准 D2 v3 VM。
2. 选择基于 CentOS 7.4 的映像。
3. 为 VM 配置网络安全组 (NSG)。 验证 VM 没有公共 IP 地址, 并且无法从公共 internet 访问。
4. 为新 VM 创建一个基于用户名和密码的用户帐户。 有关说明, 请参阅[在 Azure 门户中创建 Linux 虚拟机](../virtual-machines/linux/quick-create-portal.md)。
5. 创建 1x512 GiB standard HDD, 并将其附加到存储库 VM。  有关说明, 请参阅[如何在 Azure 门户中将托管数据磁盘附加到 WINDOWS VM](../virtual-machines/windows/attach-managed-disk-portal.md)。
6. [在托管磁盘上创建 XFS 卷](https://www.digitalocean.com/docs/volumes/how-to/format-and-mount)。 使用前面提到的凭据登录到 VM。 执行以下脚本以创建逻辑卷, 向其中添加磁盘, 创建 XFS filesystem 分区, 并将分区装入/backup1 路径下。

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

7. 将/backup1 作为 NFS 装入点公开给在私有云中运行的 Veeam 备份服务器。 有关说明, 请参阅[如何在 CentOS 6 上设置 NFS 装载一](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6)文。 在 Veeam 备份服务器中配置备份存储库时, 请使用此 NFS 共享名称。

8. 在 NSG 中为 "备份存储库" VM 配置筛选规则, 以明确允许进出 VM 的所有网络流量。

> [!NOTE]
> Veeam 备份 & 复制使用 SSH 协议与 Linux 备份存储库进行通信, 并在 Linux 存储库上需要 SCP 实用程序。 验证 SSH 守护程序是否已正确配置, 并且 SCP 在 Linux 主机上可用。

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>配置 Azure blob 存储以进行长期数据保留

1. 按照[使用 Azure 存储](https://azure.microsoft.com/en-gb/resources/videos/get-started-with-azure-storage)的 Microsoft 视频入门中所述, 创建标准类型和 blob 容器的常规用途存储帐户 (GPv2)。
2. 创建 Azure 存储容器, 如[创建容器](https://docs.microsoft.com/rest/api/storageservices/create-container)引用中所述。
2. 从 Microsoft 下载适用于 Linux 的命令行实用工具。`azcopy` 你可以在 CentOS 7.5 的 bash shell 中使用以下命令。

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. `azcopy`使用命令将备份文件复制到 blob 容器和 blob 容器。  请参阅[在 Linux 上通过 AzCopy 传输数据](../storage/common/storage-use-azcopy-linux.md)了解详细命令。

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>私有云的 vCenter 控制台:安装 Veeam B & R

从私有云访问 vCenter 若要创建 Veeam 服务帐户, 请使用服务帐户安装 Veeam B & R 9.5, 并配置 Veeam。

1. 创建名为 "Veeam Backup Role" 的新角色, 并为其分配 Veeam 建议的必要权限。 有关详细信息, 请参阅 Veeam 主题[所需的权限](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)。
2. 在 vCenter 中创建新的 "Veeam 用户组" 组, 并为其分配 "Veeam 备份角色"。
3. 创建新的 "Veeam 服务帐户" 用户, 并将其添加到 "Veeam 用户组"。

    ![创建 Veeam 服务帐户](media/veeam-vcenter01.png)

4. 使用备份网络 VLAN 在 vCenter 中创建分布式端口组。 有关详细信息, 请查看 VMware 视频[在 VSphere Web 客户端中创建分布式端口组](https://www.youtube.com/watch?v=wpCd5ZbPOpA)。
5. 按照[Veeam 系统要求](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95), 为 vCenter 中的 Veeam 备份和代理服务器创建 vm。 可以使用 Windows 2012 R2 或 Linux。 有关详细信息, 请参阅[使用 Linux 备份存储库的要求](https://www.veeam.com/kb2216)。
6. 在 Veeam 备份服务器 VM 中装载可安装的 Veeam ISO 作为 CDROM 设备。
7. 使用 Windows 2012 R2 计算机的 RDP 会话 (Veeam 安装的目标), 在 Windows 2012 R2 VM 中[安装 Veeam B & R 9.5 u3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) 。
8. 查找 Veeam 备份服务器 VM 的内部 IP 地址, 并将该 IP 地址配置为 DHCP 服务器中的静态 IP 地址。 执行此操作所需的确切步骤取决于 DHCP 服务器。 例如, Netgate 文章<a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">静态 DHCP 映射</a>介绍了如何使用 pfSense 路由器配置 DHCP 服务器。

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam 控制台:安装 Veeam 备份和恢复软件

使用 Veeam 控制台配置 Veeam 备份和恢复软件。 有关详细信息, 请参阅[Veeam Backup & Replication v9.x-安装和部署](https://www.youtube.com/watch?v=b4BqC_WXARk)。

1. 将 VMware vSphere 作为托管服务器环境添加。 出现提示时, 提供你在私有云的[vCenter 控制台开头创建的 Veeam 服务帐户的凭据:安装 Veeam B & R](#vcenter-console-of-private-cloud-install-veeam-br)。

    * 使用负载控制和默认高级设置的默认设置。
    * 将安装服务器位置设置为备份服务器。
    * 将 Veeam 服务器的配置备份位置更改为远程存储库。

2. 将 Azure 中的 Linux 服务器添加为备份存储库。

    * 将默认设置用于负载控制和高级设置。 
    * 将安装服务器位置设置为备份服务器。
    * 将 Veeam 服务器的配置备份位置更改为远程存储库。

3. 使用**Home > 配置备份设置**启用配置备份的加密。

4. 添加 Windows server VM 作为 VMware 环境的代理服务器。 使用代理的 "流量规则", 通过网络加密备份数据。

5. 配置备份作业。
    * 若要配置备份作业, 请按照[创建备份作业](https://www.youtube.com/watch?v=YHxcUFEss4M)中的说明进行操作。
    * 启用 "**高级设置" > 存储**中的备份文件的加密。

6. 配置备份复制作业。

    * 若要配置备份复制作业, 请按照视频[创建备份复制作业](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)中的说明进行操作。
    * 启用 "**高级设置" > 存储**中的备份文件的加密。

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>CloudSimple 门户:设置 Veeam 访问和取消提升权限
为 Veeam 备份和恢复服务器创建公共 IP 地址。 有关说明, 请参阅[分配公共 IP 地址](public-ips.md)。

使用创建防火墙规则, 以允许 Veeam 备份服务器创建到 Veeam 网站的出站连接, 以下载 TCP 端口80上的更新/修补程序。 有关说明, 请参阅[设置防火墙表和规则](firewall.md)。

若要取消提升权限, 请参阅[取消提升权限](escalate-private-cloud-privileges.md#de-escalate-privileges)。

## <a name="references"></a>参考资料

### <a name="cloudsimple-references"></a>CloudSimple 引用

* [创建私有云](create-private-cloud.md)
* [创建和管理 Vlan/子网](create-vlan-subnet.md)
* [vCenter 标识源](set-vcenter-identity.md)
* [工作负荷 DNS 和 DHCP 设置](dns-dhcp-setup.md)
* [提升权限](escalate-privileges.md)
* [设置防火墙表和规则](firewall.md)
* [私有云权限](learn-private-cloud-permissions.md)
* [分配公共 IP 地址](public-ips.md)

### <a name="veeam-references"></a>Veeam 引用

* [使用的端口](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [必需的权限](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [系统要求](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [& 复制安装 Veeam 备份](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [适用于 Linux 的多 OS FLR 和存储库支持所需的模块和权限](https://www.veeam.com/kb2216)
* [Veeam 备份 & 复制 v9.x-安装和部署-视频](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [创建备份作业的 Veeam v9.x-视频](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9.x 创建备份复制作业-视频](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure 参考

* [使用 Azure 门户为 ExpressRoute 配置虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [将 VNet 连接到不同于线路的订阅](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [在 Azure 门户中创建 Linux 虚拟机](../virtual-machines/linux/quick-create-portal.md)
* [如何将托管数据磁盘附加到 Azure 门户中的 Windows VM](../virtual-machines/windows/attach-managed-disk-portal.md)
* [通过 Azure 存储入门-视频](https://azure.microsoft.com/en-gb/resources/videos/get-started-with-azure-storage)
* [创建容器](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [使用 AzCopy on Linux 传输数据](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>VMware 引用

* [在 vSphere Web 客户端中创建分布式端口组-视频](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>其他参考资料

* [在托管磁盘上创建 XFS 卷-RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [如何在 CentOS 7 上设置 NFS 装载-HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [配置 DHCP 服务器-Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
