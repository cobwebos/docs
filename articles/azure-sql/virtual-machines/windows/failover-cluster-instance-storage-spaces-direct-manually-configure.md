---
title: 使用存储空间直通创建 FCI
description: 使用存储空间直通在 Azure 虚拟机上创建 (FCI) 与 SQL Server 的故障转移群集实例。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: 3cc579615a69b659bc1a4736984f0b3dcd6edb6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272517"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>在 Azure Vm 上使用存储空间直通 (SQL Server 创建 FCI) 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何在 Azure 虚拟机 (Vm) 中使用 [存储空间直通](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 和 SQL Server 来创建 (FCI) 的故障转移群集实例。 存储空间直通充当基于软件的虚拟存储区域网络 (VSAN) ，它在 Windows 群集) Azure Vm (节点之间同步存储 (数据磁盘) 。 

若要了解详细信息，请参阅 [有关 Azure vm 的 SQL Server FCI](failover-cluster-instance-overview.md) 和 [群集最佳实践](hadr-cluster-best-practices.md)的概述。 


## <a name="overview"></a>概述 

[存储空间直通 (S2D) ](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 支持两种类型的体系结构：聚合和超聚合。 超聚合基础结构将存储放置在托管群集应用程序的相同服务器上，以便存储位于每个 SQL Server FCI "节点上。 

下图显示了在 Azure Vm 上使用超聚合存储空间直通与 SQL Server 的完整解决方案： 

![使用超聚合存储空间直通的完整解决方案示意图](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

上图显示了同一资源组中的以下资源：

- Windows Server 故障转移群集中的两个虚拟机。 位于故障转移群集中的虚拟机也称为“群集节点”或“节点”。 
- 每个虚拟机包含两个或更多个数据磁盘。
- 存储空间直通同步数据磁盘上的数据，并以存储池的形式提供同步的存储。
- 存储池向故障转移群集提供群集共享卷 (CSV)。
- SQL Server FCI 群集角色为数据驱动器使用 CSV。
- 用于保存 SQL Server FCI IP 地址的 Azure 负载均衡器。
- Azure 可用性集保存所有资源。

   > [!NOTE]
   > 可以在 Azure 中基于模板创建整个解决方案。 GitHub [Azure 快速入门模板](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) 页上提供了一个模板示例。 此示例不是针对任何特定工作负荷设计的，也没有针对任何特定工作负荷进行测试。 运行该模板可以使用与域连接的存储空间直通存储创建 SQL Server FCI。 可以评估该模板，并根据用途对其进行修改。


## <a name="prerequisites"></a>必备条件

在完成本文中的说明之前，你应该已经：

- Azure 订阅。 [免费试用](https://azure.microsoft.com/free/)。 
- [可用性集中](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)的[两个或更多个已准备的 Windows Azure 虚拟机](failover-cluster-instance-prepare-vm.md)。
- 有权限在 Azure 虚拟机和 Active Directory 中创建对象的帐户。
- 最新版本的 [PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0)。 


## <a name="add-the-windows-cluster-feature"></a>添加 Windows 群集功能

1. 使用远程桌面协议 (RDP) 连接到第一个虚拟机，该帐户是本地管理员的成员并且有权在 Active Directory 中创建对象的域帐户。 使用此帐户完成余下的配置。

1. 将故障转移群集添加到每个虚拟机。

   若要从 UI 安装故障转移群集，请在两个虚拟机上执行以下操作：

   1. 在“服务器管理器”中选择“管理”，然后选择“添加角色和功能”。  
   1. 在 " **添加角色和功能** 向导" 中，选择 " **下一步** "，直到你 **选择 "功能**"。
   1. 在“选择功能”中，选择“故障转移群集”。  请包含所有所需的功能和管理工具。 
   1. 选择“添加功能”。
   1. 选择“下一步”，然后选择“完成”安装这些功能。 

   若要使用 PowerShell 安装故障转移群集，请在某个虚拟机上通过管理员 PowerShell 会话运行以下脚本：

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

有关后续步骤的详细信息，请参阅 [使用 Windows Server 2016 中的存储空间直通超聚合解决方案](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct)的 "步骤3：配置存储空间直通" 部分中的说明。


## <a name="validate-the-cluster"></a>验证群集

使用 UI 或 PowerShell 验证群集。

若要使用 UI 验证群集，请在其中一台虚拟机上执行以下操作：

1. 在“服务器管理器”下，依次选择“工具”、“故障转移群集管理器”。  
1. 在“故障转移群集管理器”下，依次选择“操作”、“验证配置”。  
1. 选择“**下一页**”。
1. 在“选择服务器或群集”下，输入两个虚拟机的名称。
1. 在“测试选项”下，选择“仅运行选择的测试”。  
1. 选择“**下一页**”。
1. 在“测试选择”下，选择除“存储”以外的所有测试，如下所示： 

   ![选择群集验证测试](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. 选择“**下一页**”。
1. 在“确认”下，选择“下一步”。 

    " **验证配置** 向导" 将运行验证测试。

若要使用 PowerShell 验证群集，请在某个虚拟机上通过管理员 PowerShell 会话运行以下脚本：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

验证群集后，创建故障转移群集。


## <a name="create-failover-cluster"></a>创建故障转移群集

若要创建故障转移群集，需要：

- 将成为群集节点的虚拟机的名称。
- 故障转移群集的名称。
- 故障转移群集的 IP 地址。 可以使用群集节点所在的同一 Azure 虚拟网络和子网中未使用的 IP 地址。


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 - 2016](#tab/windows2012)

对于 Windows server 2012 到 Windows Server 2016，可通过以下 PowerShell 脚本创建故障转移群集。 使用节点的名称（虚拟机名称）以及 Azure 虚拟网络中可用的 IP 地址更新脚本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

以下 PowerShell 脚本为 Windows Server 2019 创建故障转移群集。  使用节点的名称（虚拟机名称）以及 Azure 虚拟网络中可用的 IP 地址更新脚本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

有关详细信息，请参阅[故障转移群集：群集网络对象](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。

---


## <a name="configure-quorum"></a>配置仲裁

配置最适合你的业务需求的仲裁解决方案。 可以配置 [磁盘见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)、 [云见证](/windows-server/failover-clustering/deploy-cloud-witness)或 [文件共享见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)。 有关详细信息，请参阅 [SQL Server vm 的仲裁](hadr-cluster-best-practices.md#quorum)。 

## <a name="add-storage"></a>添加存储

存储空间直通的磁盘需是空的。 它们不能包含分区或其他数据。 若要清除磁盘，请按照 [部署存储空间直通](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives)中的说明进行操作。

1. [启用存储空间直通](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct)。

   以下 PowerShell 脚本启用存储空间直通：  

   ```powershell
   Enable-ClusterS2D
   ```

   现在，“故障转移群集管理器”中会显示存储池。

1. [创建卷](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes)。

   启用存储空间直通后，它会自动创建存储池。 接下来，可以创建卷。 PowerShell cmdlet `New-Volume` 自动完成卷的创建过程。 此过程包括格式化、将卷添加到群集，以及创建 CSV。 此示例创建一个 800 千兆字节 (GB) 的 CSV：

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   运行上述命令后，会将 800 GB 卷作为群集资源进行装载。 该卷位于 `C:\ClusterStorage\Volume1\`。

   此屏幕截图显示了存储空间直通的 CSV：

   ![具有存储空间直通群集共享卷的屏幕截图](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>测试群集故障转移

测试群集的故障转移。 在**故障转移群集管理器**中，右键单击群集，选择 "**更多操作**" "  >  **移动核心群集资源**  >  " "**选择节点**"，然后选择群集的其他节点。 将核心群集资源移到群集的每个节点，再将它移回主节点。 如果可以成功将群集移到每个节点，则表示你已为安装 SQL Server 做好了准备。  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="通过将核心资源移到其他节点来测试群集故障转移":::

## <a name="create-sql-server-fci"></a>创建 SQL Server FCI

配置故障转移群集和所有群集组件（包括存储）后，可以创建 SQL Server FCI。

1. 使用 RDP 连接到第一个虚拟机。

1. 在 **故障转移群集管理器**中，请确保所有核心群集资源位于第一个虚拟机上。 如有必要，请将所有资源移到该虚拟机。

1. 找到安装媒体。 如果虚拟机使用某个 Azure 市场映像，该媒体将位于 `C:\SQLServer_<version number>_Full`。 选择“设置”。

1. 在“SQL Server 安装中心”中选择“安装”。 

1. 选择“新建 SQL Server 故障转移群集安装”。 遵照向导中的说明安装 SQL Server FCI。

   FCI 数据目录需位于群集存储中。 使用存储空间直通，它不是共享磁盘，而是每个服务器上的卷的装入点。 存储空间直通在两个节点之间同步该卷。 卷作为 CSV 提供给群集。 使用数据目录的 CSV 装入点。

   ![数据目录](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. 完成向导中的说明之后，安装程序会在第一个节点上安装 SQL Server FCI。

1. 安装程序在第一个节点上安装 FCI 后，请使用 RDP 连接到第二个节点。

1. 打开“SQL Server 安装中心”。 选择“安装”。

1. 选择“将节点添加到 SQL Server 故障转移群集”。 按照向导中的说明安装 SQL Server 并将此服务器添加到 FCI。

   >[!NOTE]
   >如果使用了包含 SQL Server 的 Azure 市场库映像，该映像已随附 SQL Server 工具。 如果未使用其中的某个映像，请单独安装 SQL Server 工具。 有关详细信息，请参阅 [下载 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)。
   >


## <a name="register-with-the-sql-vm-rp"></a>向 SQL VM RP 注册

若要从门户管理你的 SQL Server VM，请将其注册到 [轻型管理模式](sql-vm-resource-provider-register.md#lightweight-management-mode)下的 SQL VM 资源提供程序 (RP) ，这是目前在 Azure VM 上 FCI 和 SQL Server 支持的唯一模式。 


使用 PowerShell 在轻型模式下注册 SQL Server VM：  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>配置连接 

若要将流量正确路由到当前主节点，请配置适用于你的环境的连接选项。 你可以创建 [Azure 负载均衡器](hadr-vnn-azure-load-balancer-configure.md) ，或者，如果你使用 SQL Server 2019 和 Windows Server 2016 (或更高版本) ，则可以改为预览 [分布式网络名称](hadr-distributed-network-name-dnn-configure.md) 功能。 

## <a name="limitations"></a>限制

- Azure 虚拟机支持 Windows Server 2019 上的 Microsoft 分布式事务处理协调器 (MSDTC) ，以及 Csv 上的存储和 [标准负载均衡器](../../../load-balancer/load-balancer-standard-overview.md)。
- 仅当在将存储添加到群集时，如果未选中或未选中，则已作为 NTFS 格式的磁盘附加的磁盘才能与存储空间直通一起使用。 
- 仅支持在 [轻型管理模式下](sql-vm-resource-provider-register.md#management-modes) 注册 SQL VM 资源提供程序。

## <a name="next-steps"></a>后续步骤

如果尚未执行此操作，请使用 [虚拟网络名称、Azure 负载均衡器](hadr-vnn-azure-load-balancer-configure.md) 或 [ (DNN) 的分布式网络名称 ](hadr-distributed-network-name-dnn-configure.md)配置到 FCI 的连接。 

如果存储空间直通不是合适的 FCI 存储解决方案，请考虑使用 [Azure 共享磁盘](failover-cluster-instance-azure-shared-disks-manually-configure.md) 或 [高级文件共享](failover-cluster-instance-premium-file-share-manually-configure.md) 来创建 FCI。 

若要了解详细信息，请参阅 [有关 Azure vm 的 SQL Server FCI](failover-cluster-instance-overview.md) 和 [群集配置最佳实践](hadr-cluster-best-practices.md)的概述。 

有关其他信息，请参阅： 
- [Windows 群集技术](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 故障转移群集实例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
