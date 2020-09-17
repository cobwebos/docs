---
title: 适用于 Azure VMware 解决方案的 NetApp 文件
description: 将 Azure NetApp 文件与 Azure VMware Vm 一起使用，在本地服务器、Azure VMware 解决方案 Vm 和云基础结构之间迁移和同步数据。
ms.topic: how-to
ms.date: 09/17/2020
ms.openlocfilehash: 0adf137db358d3561365a3a1ed2def338eeeb158
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708620"
---
# <a name="netapp-files-for-azure-vmware-solution"></a>适用于 Azure VMware 解决方案的 NetApp 文件

本文介绍如何将 Azure NetApp 文件与基于 Azure VMware 解决方案的工作负荷集成。 [Azure NetApp 文件](../azure-netapp-files/azure-netapp-files-introduction.md) 是一项 azure 服务，用于在云中迁移和运行企业文件工作负荷，无任何代码更改。 它可让你轻松地跨本地和云基础结构迁移数据。 快速、安全增强的数据同步通过即时快照、还原和 Active Directory 集成等功能简化了迁移和 DevOps 方案。

## <a name="topology"></a>拓扑

在此方案中，若要测试并验证 Azure NetApp 文件池共享，请在 Azure 上配置容量池、卷池和子网。 我们使用了 NFS 协议。 Azure NetApp 文件和 Azure VMware 解决方案都是在同一个 Azure 区域（美国东部）中创建的。 通过 Azure ExpressRoute 连接到 Azure VMware 解决方案。

![适用于 Azure VMware 解决方案拓扑的 NetApp 文件。](media/net-app-files/net-app-files-topology.png)

## <a name="prerequisites"></a>先决条件 

> [!div class="checklist"]
> * Azure NetApp 文件的 azure 订阅
> * Azure NetApp 文件的子网
> * Azure VMware 解决方案上的 Linux VM
> * Azure VMware 解决方案上的 Windows VM

## <a name="verify-configuration-of-azure-netapp-files"></a>验证 Azure NetApp 文件的配置 

首先，验证在高级磁盘上的 Azure 中创建的 Azure NetApp 文件的配置。

1. 在 Azure 门户的 " **存储**" 下，选择 " **Azure NetApp 文件**"。 将显示已配置的 Azure NetApp 文件的列表。

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Azure NetApp 文件的列表。"::: 

2. 如果选择 NetApp 帐户，则可以查看各种设置。 例如，如果选择 " **anf2**"，则会看到其设置。 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="查看 NetApp 帐户的设置。"::: 

3. 选择 **容量池** 以验证配置的池。 

    :::image type="content" source="media/net-app-files/capacity-pools.png" alt-text="验证配置的池。":::

    你可以看到，将存储池配置为 4 TiB 的高级磁盘。

4. 选择 **卷** (参阅步骤 2) 中的屏幕截图，查看在容量池中创建的卷。  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Azure NetApp 文件量。":::

5. 选择要查看其配置的卷。  

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="卷的配置详细信息。":::

    你可以看到，anfpool 卷池（其中包含 200 GiB）已创建为 NetApp 文件共享。 为 Azure NetApp 文件创建了专用 IP 虚拟网络，该虚拟网络提供要在 Azure VMware 解决方案 Vm 上装载的 NFS 路径。 

## <a name="protocols-supported-by-azure-netapp-files"></a>Azure NetApp 文件支持的协议  

Azure NetApp 文件支持 (SMB) 和网络文件系统 (NFS) 共享映射的服务器消息块。 

- **Smb 共享**：若要部署 smb 卷，需要首先创建 Active Directory 连接。 若要成功连接，必须由 Azure NetApp 文件的委托子网访问指定的域控制器。 在 Azure NetApp 文件帐户内配置 Active Directory 后，在创建 SMB 卷时，它将显示为可选择的项。 

- **Nfs 共享**： Azure NetApp 文件通过使用 NFS (NFSv3 和 nfsv 4.1) 、SMBv3 或双重协议 (NFSV3 和 SMB) 来创建卷。 卷的容量消耗是依据其池的预配容量计数的。 可以通过使用命令行将 NFS 装载到 Linux 服务器。

## <a name="create-azure-netapp-files"></a>创建 Azure NetApp 文件 

1. 登录到 [Azure 门户](https://rc.portal.azure.com/#home)。 在 "Azure 服务" 下，选择 " **Azure NetApp 文件**"。 

2. 选择 " **+ 添加** " 创建新的 Azure NetApp 文件卷。 

3. 在 "名称"、"订阅"、"资源组" 和 ") 位置" (填写必填字段，然后选择 " **创建**"。 

## <a name="add-capacity-pools-into-azure-netapp-files"></a>向 Azure NetApp 文件添加容量池 

1. 在 Azure 门户中，选择 " **Azure NetApp 文件**"，选择 " **容量池** " 和 " **+ 添加池**"。 

2. 输入卷池名称、服务级别和磁盘大小所需的详细信息 (FQDN) 或 IP 和权重。 选择“添加”。

    :::image type="content" source="media/net-app-files/add-capacity-pool.png" alt-text="向 NetApp 文件添加容量池。":::

3. 若要在容量池中创建卷，请在 "搜索" 窗格中选择 " **卷** "，然后选择 " **+ 添加卷**"。 
 
4. 填写必填字段，如以下屏幕截图所示。

    :::image type="content" source="media/net-app-files/create-a-volume.png" alt-text="添加容量池下的卷。":::

5. 在下一页上，选择协议类型 "共享"。 如果版本是 NFS 共享，请选择版本，并选择 Active Directory 域（如果它是 SMB 共享）。  

6. 如果它是 NFS 共享，请添加要从中访问协议类型共享的源 IP 地址。 选择“查看 + 创建”。 

    :::image type="content" source="media/net-app-files/select-volume-details.png" alt-text="选择 "卷详细信息"。":::
 
    在 Azure 门户中的 Azure NetApp 文件下，你的 NFS 共享已准备就绪，可供使用。

    :::image type="content" source="media/net-app-files/share-ready.png" alt-text="NFS 共享已准备就绪。":::

## <a name="mount-an-nfs-file-share-on-your-azure-vmware-solution-vms"></a>在 Azure VMware 解决方案 Vm 上装载 NFS 文件共享

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-windows-vm"></a>在 Azure VMware 解决方案 Windows VM 上装载 NFS 文件共享

- 打开命令提示符并运行命令，以映射 NFS 文件共享。 以下屏幕截图显示在 Azure VMware 解决方案的 Windows VM 上映射的共享驱动器。  

    :::image type="content" source="media/net-app-files/share-mapped-to-windows-vm.png" alt-text="共享 Azure VMware 解决方案的 Windows VM 上映射的驱动器。":::
 
    :::image type="content" source="media/net-app-files/mapped-to-windows-drive.png" alt-text="共享在 Windows VM 上映射的驱动器。":::

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-linux-vm"></a>在 Azure VMware 解决方案 Linux VM 上装载 NFS 文件共享

#### <a name="setting-up-your-azure-instance"></a>设置 Azure 实例

1. 在 Azure 门户中，将 Azure 实例与卷所在的虚拟网络中定义的子网相关联。

    > [!NOTE]
    > 子网需要一个网络安全组规则，该规则允许 NFS 端口上的流量 (2049、111) 、UDP 和 TCP。

2. 打开 SSH 客户端并连接到 Azure 实例。 有关详细信息，请参阅 [如何在 Azure 上将 SSH 密钥与 Windows 配合使用](../virtual-machines/linux/ssh-from-windows.md)。

3. 在 Azure 实例上安装 NFS 客户端：
   - 在 Red Hat Enterprise Linux 或 SUSE Linux 实例上： `sudo yum install -y nfs-utils`
   - 在 Ubuntu 或 Debian 实例上： `sudo apt-get install nfs-common` 

#### <a name="mounting-your-file-system"></a>装载文件系统

1. 在 Azure 实例上创建新目录： `sudo mkdir ANFPOOL`

2. 选择装载目标 IP 地址。

3. 装载文件系统： `sudo mount -t nfs -o rw,hard,rsize=1048576,wsize=1048576,vers=3,tcp 10.22.3.4:/ANFPOOL ANFPOOL`

    :::image type="content" source="media/net-app-files/root-test.png" alt-text="根测试。":::

## <a name="create-an-active-directory-connection-for-an-smb-share"></a>为 SMB 共享创建 Active Directory 连接

1. 在 Azure 门户中选择一个 NetApp 帐户。

2. 在 "Azure NetApp 文件" 下，选择 **Active Directory 连接**"。

    :::image type="content" source="media/net-app-files/active-directory-connections.png" alt-text="Active Directory 连接。"::: 

3. 选择 " **联接** "，将 SMB 共享加入到 Active Directory。 以下屏幕截图显示 SMB 共享的域详细信息。

    :::image type="content" source="media/net-app-files/active-directory-connect-details.png" alt-text="SMB 共享的域详细信息。"::: 

    你的 Azure SMB 文件共享已准备就绪，可供使用。  

    :::image type="content" source="media/net-app-files/smb-file-share-details.png" alt-text="SMB 文件共享已准备就绪。"::: 

4. 将 SMB 共享映射到你的 Microsoft Azure VMware 解决方案 VM。 如前面的屏幕截图中所示，使用 SMB 装载路径。 有关详细信息，请参阅 [在 Windows 10 中映射网络驱动器](https://support.microsoft.com/help/4026635/windows-10-map-a-network-drive)

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure NetApp 文件的存储层次结构](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md)。
- 请参阅 [Azure VMware 解决方案 vm 的生命周期管理](lifecycle-management-of-azure-vmware-solution-vms.md)
- 请参阅 [在中心辐射型体系结构中集成 Azure VMware 解决方案](concepts-avs-hub-and-spoke-integration.md)
