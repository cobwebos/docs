---
title: 使用高级文件共享创建 FCI
description: 使用高级文件共享（PFS）在 Azure 虚拟机上创建具有 SQL Server 的故障转移群集实例（FCI）。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: befab4dfb8d414743b70c535d041112bd9ccb700
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964122"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>使用高级文件共享（在 Azure Vm 上 SQL Server）创建 FCI
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何使用[高级文件共享](../../../storage/files/storage-how-to-create-premium-fileshare.md)在 Azure 虚拟机（vm）上创建带 SQL Server 的故障转移群集实例（FCI）。

高级文件共享是在 Windows Server 2012 或更高版本上存储空间直通（SSD）支持的、一致的低延迟文件共享，与故障转移群集实例一起用于 SQL Server 2012 或更高版本。 高级文件共享提供更高的灵活性，使你能在不停机的情况下对文件共享进行大小调整和缩放。

若要了解详细信息，请参阅[有关 Azure vm 的 SQL Server FCI](failover-cluster-instance-overview.md)和[群集最佳实践](hadr-cluster-best-practices.md)的概述。 

## <a name="prerequisites"></a>先决条件

在完成本文中的说明之前，你应该已经：

- Azure 订阅。
- 有权限在 Azure 虚拟机和 Active Directory 中创建对象的帐户。
- [可用性集中](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)的[两个或更多个已准备的 Windows Azure 虚拟机](failover-cluster-instance-prepare-vm.md)或不同的[可用性区域](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)。
- 要基于数据库中数据文件的存储配额，用作群集驱动器的[高级文件共享](../../../storage/files/storage-how-to-create-premium-fileshare.md)。
- 最新版本的[PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0)。 

## <a name="mount-premium-file-share"></a>装载高级文件共享

1. 登录到 [Azure 门户](https://portal.azure.com)。 并中转到你的存储帐户。
1. 在 "**文件服务**" 下，单击 "**文件共享**"，然后选择要用于 SQL 存储的高级文件共享。
1. 选择“连接”使系统显示文件共享的连接字符串。
1. 在下拉列表中，选择要使用的驱动器号，然后将这两个代码块复制到记事本。

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="从文件共享连接门户复制两个 PowerShell 命令":::

1. 使用远程桌面协议（RDP）通过 SQL Server FCI 将用于服务帐户的帐户连接到 SQL Server VM。
1. 打开 PowerShell 命令管理控制台。
1. 运行你早些时候在门户中工作时保存的命令。
1. 使用文件资源管理器或 "**运行**" 对话框（选择 Windows + R）来切换到共享。 使用网络路径 `\\storageaccountname.file.core.windows.net\filesharename`。 例如： `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. 至少在新连接的文件共享上创建一个文件夹，将 SQL 数据文件放置到其中。
1. 在将加入群集的每个 SQL Server VM 上重复这些步骤。

  > [!IMPORTANT]
  > - 请考虑对备份文件使用单独的文件共享，以便为数据和日志文件保存此共享的每秒输入/输出操作数（IOPS）和空间容量。 你可以为备份文件使用高级或标准文件共享。
  > - 如果使用的是 Windows 2012 R2 或更早版本，请执行相同的步骤来装载要用作文件共享见证的文件共享。 
  > 


## <a name="add-windows-cluster-feature"></a>添加 Windows 群集功能

1. 使用属于本地管理员的成员并且有权在 Active Directory 中创建对象的域帐户，通过 RDP 连接到第一个虚拟机。 使用此帐户完成余下的配置。

1. [将故障转移群集添加到每个虚拟机](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)。

   若要从 UI 安装故障转移群集，请在两个虚拟机上执行以下操作：
   1. 在服务器管理器中选择“管理”，然后选择“添加角色和功能”  。
   1. 在 "**添加角色和功能**向导" 中，选择 "**下一步**"，直到你**选择 "功能**"。
   1. 在“选择功能”中，选择“故障转移群集” 。 请包含所有所需的功能和管理工具。 
   1. 选择“添加功能”。
   1. 选择“下一步”，然后选择“完成”以安装这些功能 。

   若要使用 PowerShell 安装故障转移群集，请在某个虚拟机上通过管理员 PowerShell 会话运行以下脚本：

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

## <a name="validate-cluster"></a>验证群集

使用 UI 或 PowerShell 验证群集。

若要使用 UI 验证群集，请在其中一台虚拟机上执行以下操作：

1. 在“服务器管理器”下选择“工具”，然后选择“故障转移群集管理器”  。
1. 在“故障转移群集管理器”下选择“操作”，然后选择“验证配置”  。
1. 选择“下一步”。
1. 在“选择服务器或群集”下，输入两个虚拟机的名称。
1. 在“测试选项”下，选择“仅运行选择的测试” 。 
1. 选择“**下一页**”。
1. 在“测试选择”下，选择除“存储”和“存储空间直通”以外的所有测试，如下所示  ：

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="选择群集验证测试":::

1. 选择“**下一步**”。
1. 在“确认”下，选择“下一步” 。

"**验证配置**向导" 将运行验证测试。

若要使用 PowerShell 验证群集，请在某个虚拟机上通过管理员 PowerShell 会话运行以下脚本：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

验证群集后，创建故障转移群集。


## <a name="create-failover-cluster"></a>创建故障转移群集

若要创建故障转移群集，需要：

- 将成为群集节点的虚拟机的名称。
- 故障转移群集的名称。
- 故障转移群集的 IP 地址。 你可以使用未在群集节点所在的 Azure 虚拟网络和子网上使用的 IP 地址。


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 - 2016](#tab/windows2012)

对于 Windows server 2012 到 Windows Server 2016，可通过以下 PowerShell 脚本创建故障转移群集。 使用节点的名称（即虚拟机名称）以及 Azure 虚拟网络中可用的 IP 地址更新脚本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

对于 Windows Server 2019，可通过以下 PowerShell 脚本创建故障转移群集。  使用节点的名称（即虚拟机名称）以及 Azure 虚拟网络中可用的 IP 地址更新脚本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

有关详细信息，请参阅[故障转移群集：群集网络对象](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>配置仲裁

配置最适合你的业务需求的仲裁解决方案。 可以配置[磁盘见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)、[云见证](/windows-server/failover-clustering/deploy-cloud-witness)或[文件共享见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)。 有关详细信息，请参阅[SQL Server vm 的仲裁](hadr-cluster-best-practices.md#quorum)。 


## <a name="test-cluster-failover"></a>测试群集故障转移

测试群集的故障转移。 在**故障转移群集管理器**中，右键单击群集，选择 "**更多操作**" "  >  **移动核心群集资源**  >  " "**选择节点**"，然后选择群集的其他节点。 将核心群集资源移到群集的每个节点，再将它移回主节点。 如果可以成功将群集移到每个节点，则表示你已为安装 SQL Server 做好了准备。  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="通过将核心资源移到其他节点来测试群集故障转移":::


## <a name="create-sql-server-fci"></a>创建 SQL Server FCI

配置故障转移群集后，可以创建 SQL Server FCI。

1. 使用 RDP 连接到第一个虚拟机。

1. 在**故障转移群集管理器**中，请确保所有核心群集资源位于第一个虚拟机上。 如有必要，请将所有资源移到此虚拟机。

1. 找到安装媒体。 如果虚拟机使用某个 Azure 市场映像，该媒体将位于 `C:\SQLServer_<version number>_Full`。 

1. 选择“设置”。

1. 在“SQL Server 安装中心”中选择“安装” 。

1. 选择 "新建" " **SQL Server 故障转移群集安装**"，然后按照向导中的说明安装 SQL Server FCI。

   FCI 数据目录需位于高级文件共享上。 按以下格式输入共享的完整路径： `\\storageaccountname.file.core.windows.net\filesharename\foldername` 。 系统将显示一条警告，提示你已将文件服务器指定为数据目录。 预计会出现此警告。 确保保存文件共享时用于通过 RDP 访问 VM 的用户帐户与 SQL Server 服务用来避免可能出现的故障的帐户相同。

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="将文件共享用作 SQL 数据目录":::

1. 完成向导中的步骤后，安装程序会在第一个节点上安装 SQL Server FCI。

1. 在这之后，请使用 RDP 连接到第二个节点。

1. 打开**SQL Server 安装中心**，然后选择 "**安装**"。

1. 选择“向 SQL Server 故障转移群集添加节点”。 按照向导中的说明安装 SQL Server 并将此服务器添加到 FCI。

   >[!NOTE]
   >如果使用了包含 SQL Server 的 Azure 市场库映像，该映像已随附 SQL Server 工具。 如果未使用这些映像之一，请单独安装 SQL Server 工具。 有关详细信息，请参阅 [下载 SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)。

1. 在要添加到 SQL Server 故障转移群集实例的任何其他节点上重复这些步骤。 

## <a name="register-with-the-sql-vm-rp"></a>向 SQL VM RP 注册

若要从门户管理你的 SQL Server VM，请在[轻型管理模式](sql-vm-resource-provider-register.md#lightweight-management-mode)下将其注册到 SQL VM 资源提供程序（RP），当前只有 Azure vm 上的 FCI 和 SQL Server 支持的模式。 

使用 PowerShell 在轻型模式下注册 SQL Server VM （-LicenseType 可以是 `PAYG` 或 `AHUB` ）：

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>配置连接 

若要将流量正确路由到当前主节点，请配置适用于你的环境的连接选项。 你可以创建[Azure 负载均衡器](hadr-vnn-azure-load-balancer-configure.md)，或者，如果你使用 SQL Server 2019 和 Windows Server 2019，则可以改为预览[分布式网络名称](hadr-distributed-network-name-dnn-configure.md)功能。 

## <a name="limitations"></a>限制

- Windows Server 2016 及更早版本不支持 Microsoft 分布式事务处理协调器（MSDTC）。 
- 使用高级文件共享的故障转移群集不支持文件流。 若要使用 filestream，请改为使用[存储空间直通](failover-cluster-instance-storage-spaces-direct-manually-configure.md)或[Azure 共享磁盘](failover-cluster-instance-azure-shared-disks-manually-configure.md)部署群集。
- 仅支持在[轻型管理模式下](sql-vm-resource-provider-register.md#management-modes)注册 SQL VM 资源提供程序。 

## <a name="next-steps"></a>后续步骤

如果尚未执行此操作，请使用[虚拟网络名称、Azure 负载均衡器](hadr-vnn-azure-load-balancer-configure.md)或[分布式网络名称（DNN）](hadr-distributed-network-name-dnn-configure.md)配置到 FCI 的连接。 

如果高级文件共享不是合适的 FCI 存储解决方案，请考虑使用[Azure 共享磁盘](failover-cluster-instance-azure-shared-disks-manually-configure.md)或[存储空间直通](failover-cluster-instance-storage-spaces-direct-manually-configure.md)来创建 FCI。 

若要了解详细信息，请参阅[有关 Azure vm 的 SQL Server FCI](failover-cluster-instance-overview.md)和[群集配置最佳实践](hadr-cluster-best-practices.md)的概述。 

有关详情，请参阅： 
- [Windows 群集技术](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 故障转移群集实例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
