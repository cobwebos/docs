---
title: SQL Server 具有高级文件共享的 FCI-Azure 虚拟机
description: 本文介绍如何使用 Azure 虚拟机上的高级文件共享创建 SQL Server 故障转移群集实例。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: cba7102c39f9e5231a3fe726f2e4c74e814109f1
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597983"
---
# <a name="configure-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>在 Azure 虚拟机上配置具有高级文件共享的 SQL Server 故障转移群集实例

本文介绍如何使用[高级文件共享](../../../storage/files/storage-how-to-create-premium-fileshare.md)在 Azure 虚拟机上创建 SQL Server 故障转移群集实例（FCI）。 

高级文件共享是支持 SSD 的稳定延迟文件共享，它们完全支持在 Windows Server 2012 和更高版本上与故障转移群集实例一起用于 SQL Server 2012 和更高版本。 高级文件共享为您带来了更大的灵活性，使您可以在不停机的情况下调整文件共享大小并进行缩放。 


## <a name="before-you-begin"></a>开始之前

在继续下一步之前，需要掌握一些知识并做好一些准备工作。

应该对以下技术有实际的了解：

- [Windows 群集技术](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 故障转移群集实例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

一个重要区别是，在 Azure IaaS VM 故障转移群集上，建议每个服务器使用单个 NIC （群集节点）和一个子网。 Azure 网络具有物理冗余，在 Azure IaaS VM 来宾群集上无需额外的 Nic 和子网。 尽管群集验证报告将发出一条警告，指出节点只能在单个网络上访问，但可以在 Azure IaaS VM 故障转移群集上放心地忽略此警告。 

另外，应该对以下技术有大致的了解：

- [Azure 高级文件共享](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure 资源组](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> 目前，仅支持将 Azure 虚拟机上的故障转移群集实例 SQL Server [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)的[轻型](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider)管理模式。 若要从完全扩展模式更改为轻型，请删除相应 Vm 的**Sql 虚拟机**资源，然后在[轻型](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider)模式下向 sql VM 资源提供程序注册这些虚拟机资源。 使用 Azure 门户删除**SQL 虚拟机**资源时，请清除正确虚拟机旁边的复选框。 完整扩展支持诸如自动备份、修补和高级门户管理之类的功能。 在[轻型](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider)管理模式下重新安装代理后，这些功能对 SQL vm 不起作用。

### <a name="workload-consideration"></a>工作负荷注意事项

高级文件共享提供了 IOPS，并可提供满足多个工作负荷需求的所有容量。 但是，对于 IO 密集型工作负荷，请考虑根据托管高级磁盘或超磁盘[存储空间直通 SQL SERVER FCI](virtual-machines-windows-portal-sql-create-failover-cluster.md) 。  

检查当前环境的 IOPS 活动，并验证高级文件在开始部署或迁移之前是否会提供所需的 IOPS。 使用 Windows 性能监视器磁盘计数器和监视 SQL Server 数据、日志和临时数据库文件所需的总 IOPS （每秒磁盘传输数）和吞吐量（磁盘字节数/秒）。 许多工作负荷都有突发 IO，因此最好在繁重的使用时间段内检查，并记下最大 IOPS 和平均 IOPS。 高级文件共享提供基于共享大小的 IOPS。 高级文件还提供了可免费突发的突发，在这种情况下，你可以将 IO 突发为三倍于基准量最多一小时。 

有关高级文件共享性能的详细信息，请参阅[文件共享性能层](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-planning#file-share-performance-tiers)。 

### <a name="licensing-and-pricing"></a>许可与定价

在 Azure 虚拟机上，你可以使用即用即付（PAYG）或自带许可证（BYOL） VM 映像 SQL Server 许可证。 选择的映像类型会影响收费方式。

使用 PAYG 许可，Azure 虚拟机上的 SQL Server 的故障转移群集实例 (FCI) 会对 FCI 的所有节点（包括被动节点）收取费用。 有关详细信息，请参阅 [SQL Server Enterprise 虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)。 

与软件保障达成企业协议的客户有权为每个活动节点使用一个免费的被动 FCI 节点。 若要利用 Azure 中的此权益，请使用 BYOL VM 映像，然后在 FCI 的主动和被动节点上使用相同的许可证。 有关详细信息，请参阅[企业协议](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)。

如需比较 Azure 虚拟机中 SQL Server 的 PAYG 和 BYOL 许可，请参阅 [SQL VM 入门](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)。

有关许可 SQL Server 的完整信息，请参阅[定价](https://www.microsoft.com/sql-server/sql-server-2017-pricing)。

### <a name="limitations"></a>限制

- 具有高级文件共享的故障转移群集不支持 Filestream。 若要使用 filestream，请使用[存储空间直通](virtual-machines-windows-portal-sql-create-failover-cluster.md)部署群集。 

## <a name="prerequisites"></a>必备组件 

在遵循本文中的说明之前，应事先做好以下准备：

- Microsoft Azure 订阅。
- Azure 虚拟机上的 Windows 域。
- 有权在 Azure 虚拟机中创建对象的帐户。
- 能够为以下组件提供足够 IP 地址空间的 Azure 虚拟网络和子网：
   - 两台虚拟机。
   - 故障转移群集的 IP 地址。
   - 每个 FCI 的 IP 地址。
- 在 Azure 网络中配置的、指向域控制器的 DNS。
- 基于你的数据文件的数据库存储配额的[高级文件共享](../../../storage/files/storage-how-to-create-premium-fileshare.md)。 
- 与用于数据文件的高级文件共享不同的备份的文件共享。 此文件共享可以是标准或高级。 

满足这些先决条件后，可继续构建故障转移群集。 第一步是创建虚拟机。

## <a name="step-1-create-virtual-machines"></a>步骤 1：创建虚拟机

1. 使用订阅登录到 [Azure 门户](https://portal.azure.com)。

1. [创建 Azure 可用性集](../tutorial-availability-sets.md)。

   可用性集可将各个容错域和更新域中的虚拟机分组。 可用性集确保应用程序不会受到单一故障点（例如网络交换机或服务器机架电源装置）的影响。

   如果尚未为虚拟机创建资源组，请在创建 Azure 可用性集时执行该操作。 若要使用 Azure 门户创建可用性集，请执行以下步骤：

   - 在 Azure 门户中，单击 **+** 打开 Azure 市场。 搜索“可用性集”。
   - 单击“可用性集”。
   - 单击“创建”。
   - 在“创建可用性集”边栏选项卡中设置以下值：
      - **名称**：可用性集的名称。
      - **订阅**：Azure 订阅。
      - **资源组**：如果想要使用现有的组，请单击“使用现有项”并从下拉列表中选择该组。 否则，请选择“新建”并键入组的名称。
      - **位置**：设置要在其中创建虚拟机的位置。
      - **容错域**：使用默认值 (3)。
      - **更新域**：使用默认值 (5)。
   - 单击“创建”创建可用性集。

1. 在可用性集中创建虚拟机。

   在 Azure 可用性集中预配两个 SQL Server 虚拟机。 有关说明，请参阅[在 Azure 门户中预配 SQL Server 虚拟机](virtual-machines-windows-portal-sql-server-provision.md)。

   将两个虚拟机放在以下位置：

   - 可用性集所在的同一个 Azure 资源组中。
   - 域控制器所在的同一个网络中。
   - 能够为两个虚拟机提供足够 IP 地址空间的子网中，以及最终可能要在此群集上使用的所有 FCI 中。
   - Azure 可用性集中。   

      >[!IMPORTANT]
      >创建虚拟机后无法设置或更改可用性集。

   从 Azure 市场中选择一个映像。 可以使用同时包含 Windows Server 和 SQL Server 或者只包含 Windows Server 的市场映像。 有关详细信息，请参阅 [Azure 虚拟机上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)

   Azure 库中的正式 SQL Server 映像包含已安装的 SQL Server 实例，以及 SQL Server 安装软件和所需的密钥。

   >[!IMPORTANT]
   > 创建虚拟机后，删除预装的独立 SQL Server 实例。 将故障转移群集和高级文件共享配置为存储后，将使用预装 SQL Server 媒体创建 SQL Server FCI。 

   或者，可以使用只包含操作系统的 Azure 市场映像。 选择**Windows Server 2016 Datacenter**映像，并在将故障转移群集和高级文件共享配置为存储后安装 SQL Server FCI。 此映像不包含 SQL Server 安装媒体。 将安装媒体放在可以针对每个服务器运行 SQL Server 安装的位置。 

1. Azure 在创建虚拟机之后，将使用 RDP 连接到每个虚拟机。

   首次使用 RDP 连接到虚拟机时，计算机将询问是否允许在网络上发现此 PC。 单击 **“是”** 。

1. 如果使用某个基于 SQL Server 的虚拟机映像，请删除 SQL Server 实例。

   - 在 "**程序和功能**" 中，右键单击**Microsoft SQL Server 201_ （64位）** "，然后单击"**卸载/更改**"。
   - 单击“删除”。
   - 选择默认实例。
   - 删除“数据库引擎服务”下的所有功能。 不要删除“共享功能”。 参阅下图：

      ![删除功能](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - 单击“下一步”，并单击“删除”。

1. <a name="ports"></a>打开防火墙端口。

   在每个虚拟机上的 Windows 防火墙中打开以下端口。

   | 用途 | TCP 端口 | 说明
   | ------ | ------ | ------
   | SQL Server | 1433 | SQL Server 的默认实例正常使用的端口。 如果使用了库中的某个映像，此端口会自动打开。
   | 运行状况探测 | 59999 | 任何打开的 TCP 端口。 在后面的步骤中，需要将负载均衡器[运行状况探测](#probe)和群集配置为使用此端口。   
   | 文件共享 | 445 | 文件共享服务使用的端口。 

1. [将虚拟机添加到现有的域](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)。

创建并配置虚拟机后，可以配置高级文件共享。

## <a name="step-2-mount-premium-file-share"></a>步骤2：装载高级文件共享

1. 登录到[Azure 门户](https://portal.azure.com)并中转到你的存储帐户。
1. 在 "**文件服务**" 下，单击 "**文件共享**"，然后选择要用于 SQL 存储的高级文件共享。 
1. 选择 "**连接**" 以打开文件共享的连接字符串。 
1. 从下拉端中选择要使用的驱动器号，然后将这两个代码块复制到记事本中。

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/premium-file-storage-commands.png" alt-text="从文件共享 connect 门户复制这两个 PowerShell 命令":::

1. 使用 SQL Server FCI 将用于服务帐户的帐户登录到 SQL Server VM。 
1. 启动管理 PowerShell 命令控制台。 
1. 运行之前保存的门户中的命令。 
1. 使用 "文件资源管理器" 或 "**运行**" 对话框（Windows 键 + r）通过网络路径 `\\storageaccountname.file.core.windows.net\filesharename` 导航到该共享。 示例： `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. 至少在新连接的文件共享上创建一个文件夹，将 SQL 数据文件放置到其中。 
1. 在将参与群集的每个 SQL Server VM 上重复这些步骤。 

  > [!IMPORTANT]
  > 请考虑对备份文件使用单独的文件共享，以便为数据和日志文件保存此共享的 IOPS 和大小。 你可以为备份文件使用高级或标准文件共享

## <a name="step-3-configure-failover-cluster-with-file-share"></a>步骤3：配置具有文件共享的故障转移群集 

下一步是配置故障转移群集。 此步骤包括以下子步骤：

1. 添加 Windows 故障转移群集功能
1. 验证群集
1. 创建故障转移群集
1. 创建云见证


### <a name="add-windows-failover-clustering-feature"></a>添加 Windows 故障转移群集功能

1. 若要开始，请使用属于本地管理员的成员并且有权在 Active Directory 中创建对象的域帐户，通过 RDP 连接到第一个虚拟机。 使用此帐户完成余下的配置。

1. [将故障转移群集功能添加到每个虚拟机](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms)。

   若要通过 UI 安装故障转移群集功能，请在两个虚拟机上执行以下步骤。
   - 单击“服务器管理器”中单击“管理”，并单击“添加角色和功能”。
   - 在“添加角色和功能向导”中，单击“下一步”，直到出现“选择功能”。
   - 在“选择功能”中，单击“故障转移群集”。 请包含所有所需的功能和管理工具。 单击“添加功能”。
   - 单击“下一步”，然后单击“完成”安装这些功能。

   若要使用 PowerShell 安装故障转移群集功能，请在某个虚拟机上通过管理员 PowerShell 会话运行以下脚本。

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>验证群集

本指南参考了[验证群集](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation)中的说明。

使用 UI 或 PowerShell 验证群集。

若要使用 UI 验证群集，请在某个虚拟机中执行以下步骤。

1. 在“服务器管理器”中单击“工具”，并单击“故障转移群集管理器”。
1. 在“故障转移群集管理器”中单击“操作”，并单击“验证配置...”。
1. 单击“下一步”。
1. 在“选择服务器或群集”中，键入两个虚拟机的名称。
1. 在“测试选项”中，选择“仅运行选择的测试”。 单击“下一步”。
1. 在**测试选择**时，包含除**存储**和**存储空间直通**以外的所有测试。 参阅下图：

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/cluster-validation.png" alt-text="群集验证测试":::

1. 单击“下一步”。
1. 在“确认”中，单击“下一步”。

“验证配置向导”将运行验证测试。

若要使用 PowerShell 验证群集，请在某个虚拟机上通过管理员 PowerShell 会话运行以下脚本。

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

验证群集后，创建故障转移群集。

### <a name="create-the-failover-cluster"></a>创建故障转移群集


若要创建故障转移群集，需要：
- 成为群集节点的虚拟机的名称。
- 故障转移群集的名称
- 故障转移群集的 IP 地址。 可以使用群集节点所在的同一 Azure 虚拟网络和子网中未使用的 IP 地址。

#### <a name="windows-server-2012-2016"></a>Windows Server 2012-2016

以下 PowerShell 为**Windows Server 2012-2016**创建一个故障转移群集。 使用节点的名称（虚拟机名称）以及 Azure VNET 中可用的 IP 地址更新脚本：

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

以下 PowerShell 为 Windows Server 2019 创建一个故障转移群集。  有关详细信息，请参阅博客[故障转移群集：群集网络对象](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。  使用节点的名称（虚拟机名称）以及 Azure VNET 中可用的 IP 地址更新脚本：

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>创建云见证

云见证是 Azure 存储 Blob 中存储的新型群集仲裁见证。 这样就不再需要承载见证共享的独立 VM。

1. [为故障转移群集创建云见证](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)。

1. 创建 Blob 容器。

1. 保存访问密钥和容器 URL。

1. 配置故障转移群集仲裁见证。 请参阅[在用户界面中配置仲裁见证](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)。


## <a name="step-4-test-cluster-failover"></a>步骤4：测试群集故障转移

测试群集的故障转移。 在故障转移群集管理器中，右键单击群集 > "**更多操作**"  >  "**移动核心群集资源**"  > **选择节点**，然后选择群集的其他节点。 将核心群集资源移到群集的每个节点，然后将其移回主节点。 如果能够成功地将群集移到每个节点，则可以安装 SQL Server。  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/test-cluster-failover.png" alt-text="通过将核心资源移到其他节点来测试群集故障转移":::

## <a name="step-5-create-sql-server-fci"></a>步骤5：创建 SQL Server FCI

配置故障转移群集后，可以创建 SQL Server FCI。

1. 使用 RDP 连接到第一个虚拟机。

1. 在“故障转移群集管理器”中，确保所有群集核心资源位于第一个虚拟机上。 如有必要，请将所有资源移到此虚拟机。

1. 找到安装媒体。 如果虚拟机使用某个 Azure 市场映像，该媒体将位于 `C:\SQLServer_<version number>_Full`。 单击“设置”。

1. 在“SQL Server 安装中心”中单击“安装”。

1. 单击“新建 SQL Server 故障转移群集安装”。 遵照向导中的说明安装 SQL Server FCI。

   FCI 数据目录需要位于高级文件共享上。 键入共享的完整路径，格式为 `\\storageaccountname.file.core.windows.net\filesharename\foldername`。 将出现一条警告，通知您已指定文件服务器作为数据目录。 这是正常情况。 确保保存文件共享的帐户与 SQL Server 服务用来避免可能出现的故障的帐户相同。 

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/use-file-share-as-data-directories.png" alt-text="使用文件共享作为 SQL 数据目录":::

1. 完成向导中的操作后，安装程序会在第一个节点上安装 SQL Server FCI。

1. 安装程序在第一个节点上成功安装 FCI 后，请使用 RDP 连接到第二个节点。

1. 打开“SQL Server 安装中心”。 单击“安装”。

1. 单击“将节点添加到 SQL Server 故障转移群集”。 遵照向导中的说明安装 SQL Server 并将此服务器添加到 FCI。

   >[!NOTE]
   >如果使用了包含 SQL Server 的 Azure 市场库映像，该映像已随附 SQL Server 工具。 如果未使用此映像，需单独安装 SQL Server 工具。 请参阅 [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)（下载 SQL Server Management Studio (SSMS)）。

## <a name="step-6-create-azure-load-balancer"></a>步骤6：创建 Azure 负载均衡器

在 Azure 虚拟机上，群集使用负载均衡器来保存每次都需要位于一个群集节点上的 IP 地址。 在此解决方案中，负载均衡器保存 SQL Server FCI 的 IP 地址。

[创建并配置 Azure 负载均衡器](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)

### <a name="create-the-load-balancer-in-the-azure-portal"></a>在 Azure 门户中创建负载均衡器

若要创建负载均衡器，请执行以下操作：

1. 在 Azure 门户中，转到虚拟机所在的资源组。

1. 单击“+ 添加”。 在市场中搜索“负载均衡器”。 单击“负载均衡器”。

1. 单击“创建”。

1. 为负载均衡器配置以下属性：

   - **订阅**：Azure 订阅。
   - **资源组**：使用虚拟机所在的同一资源组。
   - **名称**：标识负载均衡器的名称。
   - **区域**：使用与虚拟机相同的 Azure 位置。
   - **类型**：负载均衡器可以是公共或专用的。 专用负载均衡器可从同一 VNET 内部访问。 大多数 Azure 应用程序可以使用专用负载均衡器。 如果应用程序需要通过 Internet 直接访问 SQL Server，请使用公共负载均衡器。
   - **Sku**：负载均衡器的 sku 应该是标准的。 
   - **虚拟网络**：虚拟机所在的同一网络。
   - **Ip 地址分配**： ip 地址分配应为静态。 
   - **专用 IP 地址**：分配给 SQL Server FCI 群集网络资源的同一 IP 地址。
   参阅下图：

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>配置负载均衡器后端池

1. 返回到虚拟机所在的 Azure 资源组，找到新的负载均衡器。 可能需要在资源组中刷新视图。 单击该负载均衡器。

1. 单击“后端池”并单击“+ 添加”来添加后端池。

1. 将后端池与包含 VM 的可用性集相关联。

1. 在“目标网络 IP 配置”下，选中“虚拟机”并选择将作为群集节点参与操作的虚拟机。 请务必包括将承载 FCI 的所有虚拟机。 

1. 单击“确定”创建后端池。

### <a name="configure-a-load-balancer-health-probe"></a>配置负载均衡器运行状况探测

1. 在负载均衡器边栏选项卡中，单击“运行状况探测”。

1. 单击“+ 添加”。

1. 在“添加运行状况探测”边栏选项卡中，<a name="probe"></a>设置运行状况探测参数：

   - **名称**：运行状况探测的名称。
   - **协议**：TCP。
   - **端口**：在[此步骤](#ports)中，将设置为在防火墙中为运行状况探测创建的端口。 在本文中，此示例使用 TCP 端口 `59999`。
   - **间隔**：5 秒。
   - **不正常阈值**：2 次连续失败。

1. 单击“确定”。

### <a name="set-load-balancing-rules"></a>设置负载均衡规则

1. 在 "负载均衡器" 边栏选项卡上，单击 "**负载均衡规则**"。

1. 单击“+ 添加”。

1. 设置负载均衡规则参数：

   - **名称**：负载均衡规则的名称。
   - **前端 IP 地址**：使用 SQL Server FCI 群集网络资源的 IP 地址。
   - **端口**：设置为 SQL Server FCI TCP 端口。 默认实例端口为 1433。
   - **后端端口**：此值使用的端口与启用“浮动 IP (直接服务器返回)”时使用的“端口”值相同。
   - **后端池**：使用前面配置的后端池名称。
   - **运行状况探测**：使用前面配置的运行状况探测。
   - **会话持久性**：无。
   - **空闲超时(分钟)** ：4。
   - **浮动 IP (直接服务器返回)** ：已启用

1. 单击 **“确定”** 。

## <a name="step-7-configure-cluster-for-probe"></a>步骤7：为探测配置群集

在 PowerShell 中设置群集探测端口参数。

若要设置群集探测端口参数，请使用环境中的值更新以下脚本中的变量。 从脚本中删除尖括号 `<>`。 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

在前面的脚本中，设置环境的值。 以下列表对这些值进行了说明：

   - `<Cluster Network Name>`：Windows Server 故障转移群集的网络名称。 在“故障转移群集管理器” > “网络”中，右键单击网络，然后单击“属性”。 正确的值位于“常规”选项卡的“名称”下。 

   - `<SQL Server FCI IP Address Resource Name>`：SQL Server FCI IP 地址资源名称。 在**故障转移群集管理器** >  SQL Server "**角色**" 下的 "服务器名称" 下，右键单击 "**服务器名称**" 下的 "IP 地址" 资源，然后单击 "**属性**"。 正确的值位于“常规”选项卡的“名称”下。 

   - `<ILBIP>`：ILB IP 地址。 在 Azure 门户中将此地址配置为 ILB 前端地址。 这也是 SQL Server FCI IP 地址。 可在“故障转移群集管理器”中找到该地址，它与 `<SQL Server FCI IP Address Resource Name>` 位于同一属性页。  

   - `<nnnnn>`：这是在负载均衡器运行状况探测中配置的探测端口。 任何未使用的 TCP 端口都有效。 

>[!IMPORTANT]
>群集参数的子网掩码必须是 TCP IP 广播地址：`255.255.255.255`。

设置群集探测后，可以在 PowerShell 中查看所有群集参数。 运行以下脚本：

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-8-test-fci-failover"></a>步骤8：测试 FCI 故障转移

测试 FCI 的故障转移以验证群集功能。 请执行以下步骤：

1. 使用 RDP 连接到 SQL Server FCI 群集节点之一。

1. 打开“故障转移群集管理器”。 单击“角色”。 观察哪个节点拥有 SQL Server FCI 角色。

1. 右键单击“SQL Server FCI”角色。

1. 单击“移动”，并单击“最佳节点”。

“故障转移群集管理器”会显示该角色及其资源已脱机。 然后资源会移动，并在另一个节点上联机。

### <a name="test-connectivity"></a>测试连接

若要测试连接，请登录到同一虚拟网络中的另一个虚拟机。 打开“SQL Server Management Studio”并连接到 SQL Server FCI 名称。

>[!NOTE]
>如果需要，可以[下载 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="limitations"></a>限制

Azure 虚拟机支持 Windows Server 2019 上的 Microsoft 分布式事务处理协调器 (MSDTC)，其中存储位于群集共享卷 (CSV) 和[标准负载均衡器](../../../load-balancer/load-balancer-standard-overview.md)上。

在 Azure 虚拟机上，Windows Server 2016 及更早版本不支持 MSDTC，因为：

- 无法将群集 MSDTC 资源配置为使用共享存储。 对于 Windows Server 2016，如果创建 MSDTC 资源，即使存储存在，也不会显示任何可用的共享存储。 Windows Server 2019 中已修复此问题。
- 基本负载均衡器不处理 RPC 端口。

## <a name="see-also"></a>另请参阅

- [Windows 群集技术](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 故障转移群集实例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
