---
title: SQL Server FCI - Azure 虚拟机 | Microsoft 文档
description: 本文介绍如何在 Azure 虚拟机上创建 SQL Server 故障转移群集实例。
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 1a69741ba3ced91b6b0d1fc4bcd4aea887452151
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792177"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>在 Azure 虚拟机上配置 SQL Server 故障转移群集实例

本文介绍如何在 Azure 资源管理器模型中的 Azure 虚拟机上创建 SQL Server 故障转移群集实例（FCI）。 此解决方案使用[Windows Server 2016 Datacenter edition 存储空间直通](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)作为基于软件的虚拟 SAN，它在 Windows 群集中的节点（Azure vm）之间同步存储（数据磁盘）。 存储空间直通在 Windows Server 2016 中是新增的。

下图显示了 Azure 虚拟机上的完整解决方案：

![完整解决方案](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

此图显示：

- Windows Server 故障转移群集中的两个 Azure 虚拟机。 当虚拟机在故障转移群集中时，它也称为*群集节点*或*节点*。
- 每个虚拟机包含两个或更多个数据磁盘。
- 存储空间直通同步数据磁盘上的数据，并将已同步的存储显示为存储池。
- 存储池向故障转移群集提供群集共享卷（CSV）。
- SQL Server FCI 群集角色为数据驱动器使用 CSV。
- 用于保存 SQL Server FCI IP 地址的 Azure 负载均衡器。
- Azure 可用性集保存所有资源。

>[!NOTE]
>关系图中的所有 Azure 资源位于同一资源组中。

有关存储空间直通的详细信息，请参阅[Windows Server 2016 Datacenter edition 存储空间直通](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)。

存储空间直通支持两种类型的体系结构：聚合和超聚合。 本文档中所述的体系结构为超聚合。 超融合基础设施将存储放置在承载群集应用程序的相同服务器上。 在此体系结构中，存储位于每个 SQL Server FCI 节点上。

## <a name="licensing-and-pricing"></a>许可与定价

在 Azure 虚拟机上，你可以使用即用即付（PAYG）或自带许可证（BYOL） VM 映像 SQL Server 许可证。 你选择的映像类型将影响你的收费方式。

使用即用即付许可，Azure 虚拟机上 SQL Server 的故障转移群集实例（FCI）将产生 FCI （包括被动节点）的所有节点的费用。 有关详细信息，请参阅 [SQL Server Enterprise 虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)。

如果有企业协议软件保障，则可以为每个活动节点使用一个免费的被动 FCI 节点。 若要利用 Azure 中的此权益，请使用 BYOL VM 映像，并在 FCI 的主动和被动节点上使用相同的许可证。 有关详细信息，请参阅[企业协议](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)。

若要在 Azure 虚拟机上比较 SQL Server 的即用即付和 BYOL 许可，请参阅[SQL vm 入门](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)。

有关许可 SQL Server 的完整信息，请参阅[定价](https://www.microsoft.com/sql-server/sql-server-2017-pricing)。

### <a name="example-azure-template"></a>示例 Azure 模板

你可以通过模板在 Azure 中创建此完整解决方案。 GitHub [Azure 快速入门模板](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad)中提供了一个模板示例。 此示例不是针对任何特定工作负荷设计或测试的。 可以运行模板来创建 SQL Server FCI，并将存储空间直通存储连接到域。 可以评估该模板，并根据需要进行修改。

## <a name="before-you-begin"></a>开始之前

在开始之前，需要了解并准备好几个事项。

### <a name="what-to-know"></a>要了解的事项
你应该对这些技术有一个操作理解：

- [Windows 群集技术](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 故障转移群集实例](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

需要注意的一点是，在 Azure IaaS VM 来宾故障转移群集上，建议每个服务器使用单个 NIC （群集节点）和单个子网。 Azure 网络具有物理冗余，这使得 Azure IaaS VM 来宾群集上不需要其他 Nic 和子网。 群集验证报告将警告你只能在单个网络上访问节点。 可以在 Azure IaaS VM 来宾故障转移群集上忽略此警告。

还应大致了解这些技术：

- [使用 Windows Server 2016 中的存储空间直通的超聚合解决方案](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Azure 资源组](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> 目前，仅支持将 Azure 虚拟机上的故障转移群集实例 SQL Server [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)的[轻型管理模式](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)。 若要从完全扩展模式更改为轻型，请删除相应 Vm 的**Sql 虚拟机**资源，然后在轻型模式下向 sql VM 资源提供程序注册这些虚拟机资源。 使用 Azure 门户删除**SQL 虚拟机**资源时，请**清除正确虚拟机旁边的复选框**。 完整扩展支持诸如自动备份、修补和高级门户管理之类的功能。 在轻型管理模式下重新安装代理后，这些功能对 SQL Vm 不起作用。

### <a name="what-to-have"></a>准备工作

在完成本文中的步骤之前，你应该已经：

- Microsoft Azure 订阅。
- Azure 虚拟机上的 Windows 域。
- 有权在 Azure 虚拟机和 Active Directory 中创建对象的帐户。
- 具有足够 IP 地址空间的 Azure 虚拟网络和子网：
   - 两台虚拟机。
   - 故障转移群集的 IP 地址。
   - 每个 FCI 的 IP 地址。
- 在 Azure 网络上配置的、指向域控制器的 DNS。

准备好这些先决条件后，即可开始构建故障转移群集。 第一步是创建虚拟机。

## <a name="step-1-create-the-virtual-machines"></a>步骤1：创建虚拟机

1. 通过订阅登录到[Azure 门户](https://portal.azure.com)。

1. [创建 Azure 可用性集](../tutorial-availability-sets.md)。

   可用性集可将各个容错域和更新域中的虚拟机分组。 它可确保应用程序不会受到单一故障点（例如网络交换机或服务器机架的电源装置）的影响。

   如果尚未为虚拟机创建资源组，请在创建 Azure 可用性集时执行此操作。 如果使用 Azure 门户创建可用性集，请执行以下步骤：

   1. 在 Azure 门户中，选择 "**创建资源**" 以打开 Azure Marketplace。 搜索“可用性集”。
   1. 选择 "**可用性集**"。
   1. 选择**创建**。
   1. 在 "**创建可用性集**" 下，提供以下值：
      - **名称**：可用性集的名称。
      - **订阅**：Azure 订阅。
      - **资源组**：如果要使用现有组，请单击 "**选择现有**"，然后从列表中选择组。 否则，请选择 "**新建**"，然后输入组的名称。
      - **位置**：设置要在其中创建虚拟机的位置。
      - **容错域**：使用默认值（**3**）。
      - **更新域**：使用默认值（**5**）。
   1. 选择 "**创建**" 创建可用性集。

1. 在可用性集中创建虚拟机。

   在 Azure 可用性集中预配两个 SQL Server 虚拟机。 有关说明，请参阅[在 Azure 门户中预配 SQL Server 虚拟机](virtual-machines-windows-portal-sql-server-provision.md)。

   将两个虚拟机放在以下位置：

   - 在与可用性集相同的 Azure 资源组中。
   - 域控制器所在的同一个网络中。
   - 在具有足够 IP 地址空间的子网中，虚拟机和可能最终在群集上使用的所有 Fci。
   - Azure 可用性集中。

      >[!IMPORTANT]
      >创建虚拟机后无法设置或更改可用性集。

   从 Azure Marketplace 中选择一个映像。 你可以使用包含 Windows Server 和 SQL Server 的 Azure Marketplace 映像，或者使用只包含 Windows Server 的 Azure Marketplace 映像。 有关详细信息，请参阅[Azure 虚拟机上 SQL Server 的概述](virtual-machines-windows-sql-server-iaas-overview.md)。

   Azure 库中的官方 SQL Server 映像包括已安装的 SQL Server 实例、SQL Server 安装软件和所需的密钥。

   根据要支付 SQL Server 许可证的方式选择正确的映像：

   - **按使用量付费许可**。 这些映像的每秒费用包括 SQL Server 许可费：
      - **Windows Server 2016 Datacenter 上的 SQL Server 2016 企业版**
      - **Windows Server 2016 Datacenter 上的 SQL Server 2016 标准**
      - **Windows Server 2016 Datacenter 上的 SQL Server 2016 开发人员**

   - **自带许可 (BYOL)**

      - **BYOLWindows Server 2016 Datacenter 上的 SQL Server 2016 企业版**
      - **BYOLWindows Server 2016 Datacenter 上的 SQL Server 2016 标准**

   >[!IMPORTANT]
   >创建虚拟机后，删除预装的独立 SQL Server 实例。 在设置故障转移群集和存储空间直通之后，你将使用预先安装的 SQL Server 媒体来创建 SQL Server FCI。

   或者，你可以使用只包含操作系统的 Azure Marketplace 映像。 在设置故障转移群集和存储空间直通后，选择**Windows Server 2016 Datacenter**映像并安装 SQL SERVER 的 FCI。 此映像不包含 SQL Server 安装介质。 将 SQL Server 安装媒体放在一个可在其中运行每个服务器的位置。

1. Azure 创建虚拟机后，使用 RDP 连接到每个虚拟机。

   当你首次使用 RDP 连接到虚拟机时，系统将提示你是否允许在网络上发现此 PC。 请选择“是”。

1. 如果使用的是基于 SQL Server 的虚拟机映像之一，请删除 SQL Server 实例。

   1. 在 "**程序和功能**" 中，右键单击**Microsoft SQL Server 2016 （64）** "，然后选择"**卸载/更改**"。
   1. 选择“删除”。
   1. 选择默认实例。
   1. 删除“数据库引擎服务”下的所有功能。 请勿删除**共享功能**。 你将看到类似于以下屏幕截图的内容：

      ![选择功能](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. 选择 "**下一步**"，然后选择 "**删除**"。

1. <a name="ports"></a>打开防火墙端口。

   在每个虚拟机上，在 Windows 防火墙上打开以下端口：

   | 用途 | TCP 端口 | 说明
   | ------ | ------ | ------
   | SQL Server | 1433 | SQL Server 的默认实例正常使用的端口。 如果使用了库中的某个映像，此端口会自动打开。
   | 运行状况探测 | 59999 | 任何打开的 TCP 端口。 在后面的步骤中，需要将负载均衡器[运行状况探测](#probe)和群集配置为使用此端口。  

1. 将存储添加到虚拟机。 有关详细信息，请参阅[添加存储](../disks-types.md)。

   这两个虚拟机至少需要两个数据磁盘。

   附加原始磁盘，而不是 NTFS 格式的磁盘。
      >[!NOTE]
      >如果附加 NTFS 格式的磁盘，则只有在没有磁盘确认检查的情况下才能启用存储空间直通。  

   将至少两个高级 SSD 附加到每个 VM。 建议至少 P30 （1 TB）磁盘。

   将主机缓存设置为“只读”。

   在生产环境中使用的存储容量取决于工作负荷。 本文中所述的值适用于演示和测试。

1. [将虚拟机添加到现有的域](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)。

创建并配置虚拟机后，可以设置故障转移群集。

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>步骤2：配置 Windows Server 故障转移群集与存储空间直通

下一步是将故障转移群集配置存储空间直通。 在此步骤中，你将完成以下子步骤：

1. 添加 Windows Server 故障转移群集功能。
1. 验证群集。
1. 创建故障转移群集。
1. 创建云见证。
1. 添加存储。

### <a name="add-windows-server-failover-clustering"></a>添加 Windows Server 故障转移群集

1. 使用具有本地管理员的成员并且有权在 Active Directory 中创建对象的域帐户，通过 RDP 连接到第一个虚拟机。 使用此帐户完成余下的配置。

1. [将故障转移群集添加到每个虚拟机](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms)。

   若要从 UI 安装故障转移群集，请在两个虚拟机上执行以下步骤：
   1. 在**服务器管理器**中，选择 "**管理**"，然后选择 "**添加角色和功能**"。
   1. 在 "**添加角色和功能向导**" 中，选择 "**下一步**"，直到你**选择 "功能**"。
   1. 在 "**选择功能**" 中，选择 "**故障转移群集**"。 请包含所有所需的功能和管理工具。 选择 "**添加功能**"。
   1. 选择 "**下一步**"，然后选择 "**完成**" 以安装这些功能。

   若要使用 PowerShell 安装故障转移群集，请在某个虚拟机上通过管理员 PowerShell 会话运行以下脚本：

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

有关后续步骤的详细参考信息，请参阅[使用 Windows Server 2016 中的存储空间直通的超聚合解决方案](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct)的步骤3中的说明。

### <a name="validate-the-cluster"></a>验证群集

使用 PowerShell 或 PowerShell 验证群集。

若要使用 UI 验证群集，请在其中一台虚拟机上执行以下步骤：

1. 在 "**服务器管理器**" 下，选择 "**工具**"，然后选择 "**故障转移群集管理器**"。
1. 在 "**故障转移群集管理器**" 下，选择 "**操作**"，然后选择 "**验证配置**"。
1. 选择“**下一步**”。
1. 在 "**选择服务器或群集**" 下，输入这两个虚拟机的名称。
1. 在 "**测试选项**" 下，选择 "**仅运行选择的测试**"。 选择“**下一步**”。
1. 在 "**测试选择**" 下，选择 "**存储**" 以外的所有测试，如下所示：

   ![选择群集验证测试](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. 选择“**下一步**”。
1. **确认**下，选择 "**下一步**"。

"验证配置向导" 将运行验证测试。

若要使用 PowerShell 验证群集，请在某个虚拟机上通过管理员 PowerShell 会话运行以下脚本：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

验证群集后，创建故障转移群集。

### <a name="create-the-failover-cluster"></a>创建故障转移群集

若要创建故障转移群集，需要：
- 将成为群集节点的虚拟机的名称。
- 故障转移群集的名称
- 故障转移群集的 IP 地址。 你可以使用与群集节点在同一 Azure 虚拟网络和子网中未使用的 IP 地址。

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows server 2008 到 Windows Server 2016

下面的 PowerShell 脚本通过 Windows Server 2016 为 Windows Server 2008 创建一个故障转移群集。 使用 Azure 虚拟网络中节点的名称（虚拟机名称）和可用的 IP 地址更新脚本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

下面的 PowerShell 脚本将为 Windows Server 2019 创建一个故障转移群集。 有关详细信息，请参阅[故障转移群集：群集网络对象](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。 使用 Azure 虚拟网络中节点的名称（虚拟机名称）和可用的 IP 地址更新脚本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>创建云见证

Cloud 见证是存储在 Azure 存储 blob 中的一种新型群集仲裁见证。 这样就不再需要承载见证共享的独立 VM。

1. [为故障转移群集创建云见证](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)。

1. 创建 Blob 容器。

1. 保存访问密钥和容器 URL。

1. 配置故障转移群集仲裁见证。 请参阅[在用户界面中配置仲裁见证](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)。

### <a name="add-storage"></a>添加存储

存储空间直通的磁盘必须为空。 它们不能包含分区或其他数据。 若要清除磁盘，请遵循[本指南中的步骤](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives)。

1. [启用存储空间直通](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct)。

   下面的 PowerShell 脚本支持存储空间直通：  

   ```powershell
   Enable-ClusterS2D
   ```

   现在，“故障转移群集管理器”中会显示存储池。

1. [创建卷](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes)。

   启用后，存储空间直通会自动创建存储池。 你现在可以创建卷。 PowerShell cmdlet `New-Volume` 自动完成卷的创建过程。 此过程包括格式设置、将卷添加到群集，以及创建群集共享卷（CSV）。 此示例创建一个 800-千兆字节（GB）的 CSV：

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   此命令完成后，会将 800 GB 卷作为群集资源进行装载。 该卷位于 `C:\ClusterStorage\Volume1\`。

   此屏幕截图显示了存储空间直通群集共享卷：

   ![群集共享卷](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>步骤 3：测试故障转移群集故障转移

在**故障转移群集管理器**中，验证是否可将存储资源移到另一个群集节点。 如果可以使用**故障转移群集管理器**连接到故障转移群集，并将存储从一个节点移到另一个节点，则可以配置 FCI。

## <a name="step-4-create-the-sql-server-fci"></a>步骤4：创建 SQL Server FCI

配置故障转移群集和所有群集组件（包括存储）后，可以创建 SQL Server FCI。

1. 使用 RDP 连接到第一个虚拟机。

1. 在**故障转移群集管理器**中，请确保所有核心群集资源位于第一个虚拟机上。 如有必要，请将所有资源移到该虚拟机。

1. 找到安装媒体。 如果虚拟机使用某个 Azure 市场映像，该媒体将位于 `C:\SQLServer_<version number>_Full`。 选择“设置”。

1. 在**SQL Server 安装中心**中，选择 "**安装**"。

1. 选择 "**新建" SQL Server 故障转移群集安装**。 遵照向导中的说明安装 SQL Server FCI。

   FCI 数据目录需位于群集存储中。 使用存储空间直通，它不是共享磁盘，而是每个服务器上的卷的装入点。 存储空间直通在两个节点之间同步该卷。 卷作为群集共享卷提供给群集。 使用数据目录的 CSV 装入点。

   ![数据目录](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. 完成向导中的说明之后，安装程序会在第一个节点上安装 SQL Server FCI。

1. 安装程序在第一个节点上安装 FCI 后，请使用 RDP 连接到第二个节点。

1. 打开“SQL Server 安装中心”。 选择 "**安装**"。

1. 选择 "**将节点添加到 SQL Server 故障转移群集**"。 按照向导中的说明安装 SQL Server 并将服务器添加到 FCI。

   >[!NOTE]
   >如果你使用的是包含 SQL Server 的 Azure Marketplace 库映像，则 SQL Server 工具随映像一起提供。 如果未使用这些映像之一，请单独安装 SQL Server 工具。 请参阅 [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)（下载 SQL Server Management Studio (SSMS)）。

## <a name="step-5-create-the-azure-load-balancer"></a>步骤5：创建 Azure 负载均衡器

在 Azure 虚拟机上，群集使用负载均衡器来保存每次都需要位于一个群集节点上的 IP 地址。 在此解决方案中，负载均衡器保存 SQL Server FCI 的 IP 地址。

有关详细信息，请参阅[创建和配置 Azure 负载均衡器](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)。

### <a name="create-the-load-balancer-in-the-azure-portal"></a>在 Azure 门户中创建负载均衡器

若要创建负载均衡器，请执行以下操作：

1. 在 Azure 门户中，请前往包含虚拟机的资源组。

1. 选择 **添加** 。 在 Azure Marketplace 中搜索**负载均衡器**。 选择 "**负载均衡器**"。

1. 选择**创建**。

1. 为负载均衡器配置以下属性：

   - **订阅**：Azure 订阅。
   - **资源组**：包含虚拟机的资源组。
   - **名称**：标识负载均衡器的名称。
   - **区域**：包含虚拟机的 Azure 位置。
   - **类型**： public 或 private。 可以从虚拟网络中访问私有负载均衡器。 大多数 Azure 应用程序可以使用专用负载均衡器。 如果你的应用程序需要通过 internet 直接访问 SQL Server，请使用公共负载均衡器。
   - **SKU**：标准版。
   - **虚拟网络**：虚拟机所在的网络。
   - **IP 地址分配**：静态。 
   - **专用 ip 地址**：分配给 SQL Server FCI 群集网络资源的 ip 地址。

 以下屏幕截图显示了 "**创建负载均衡器**" UI：

   ![设置负载均衡器](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>配置负载均衡器后端池

1. 返回到包含虚拟机的 Azure 资源组，并找到新的负载均衡器。 可能需要刷新资源组上的视图。 选择负载均衡器。

1. 选择 "**后端池**"，然后选择 "**添加**"。

1. 将后端池与包含 VM 的可用性集相关联。

1. 在 "**目标网络 IP 配置**" 下，选择 "**虚拟机**" 并选择将作为群集节点加入的虚拟机。 请务必包括将承载 FCI 的所有虚拟机。

1. 选择 **"确定"** 以创建后端池。

### <a name="configure-a-load-balancer-health-probe"></a>配置负载均衡器运行状况探测

1. 在 "负载均衡器" 边栏选项卡中选择 "**运行状况探测**"。

1. 选择 **添加** 。

1. 在 "**添加运行状况探测**" <a name="probe"></a>边栏选项卡中，设置运行状况探测参数。

   - **名称**：运行状况探测的名称。
   - **协议**：TCP。
   - **端口**：在[此步骤](#ports)中，将设置为在防火墙中为运行状况探测创建的端口。 在本文中，此示例使用 TCP 端口 `59999`。
   - **间隔**：5 秒。
   - **不正常阈值**：2 次连续失败。

1. 选择“确定”。

### <a name="set-load-balancing-rules"></a>设置负载均衡规则

1. 在 "负载均衡器" 边栏选项卡上，选择 "**负载均衡规则**"。

1. 选择 **添加** 。

1. 设置负载均衡规则参数：

   - **名称**：负载均衡规则的名称。
   - **前端 IP 地址**： SQL Server FCI 群集网络资源的 IP 地址。
   - **端口**： SQL SERVER FCI TCP 端口。 默认实例端口为 1433。
   - **后端端口**：启用**浮动 IP （直接服务器返回）** 时，使用与**端口**值相同的端口。
   - **后端池**：你之前配置的后端池名称。
   - **运行状况探测**：你先前配置的运行状况探测。
   - **会话持久性**：无。
   - **空闲超时(分钟)** ：4。
   - **浮动 IP （直接服务器返回）** ：已启用。

1. 选择“确定”。

## <a name="step-6-configure-the-cluster-for-the-probe"></a>步骤6：为探测配置群集

在 PowerShell 中设置群集探测端口参数。

若要设置群集探测端口参数，请用您的环境中的值更新以下脚本中的变量。 从脚本中删除尖括号（`<` 和 `>`）。

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

以下列表说明了需要更新的值：

   - `<Cluster Network Name>`：网络的 Windows Server 故障转移群集名称。 在 "**故障转移群集管理器** > **网络**" 中，右键单击该网络，然后选择 "**属性**"。 正确的值位于“常规”选项卡的“名称”下。

   - `<SQL Server FCI IP Address Resource Name>`： SQL Server FCI IP 地址资源名称。 在**故障转移群集管理器** > "**角色**的 SQL Server" 服务器名称 "下，右键单击"**服务器名称**"下的" IP 地址 "资源，然后选择"**属性**"。 正确的值位于“常规”选项卡的“名称”下。 

   - `<ILBIP>`：ILB IP 地址。 在 Azure 门户中将此地址配置为 ILB 前端地址。 这也是 SQL Server FCI IP 地址。 可在“故障转移群集管理器”中找到该地址，它与 `<SQL Server FCI IP Address Resource Name>` 位于同一属性页。  

   - `<nnnnn>`：在负载平衡器运行状况探测中配置的探测端口。 任何未使用的 TCP 端口都有效。

>[!IMPORTANT]
>群集参数的子网掩码必须是 TCP IP 广播地址：`255.255.255.255`。

设置群集探测后，可以在 PowerShell 中查看所有群集参数。 运行此脚本：

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>步骤 7：测试 FCI 故障转移

测试 FCI 的故障转移以验证群集功能。 执行以下步骤：

1. 使用 RDP 连接到 SQL Server FCI 群集节点之一。

1. 打开“故障转移群集管理器”。 选择“角色”。 观察哪个节点拥有 SQL Server FCI 角色。

1. 右键单击“SQL Server FCI”角色。

1. 选择 "**移动**"，然后选择 "**最佳节点**"。

**故障转移群集管理器**显示角色，其资源处于脱机状态。 然后资源会移动，并在另一个节点上联机。

### <a name="test-connectivity"></a>测试连接

若要测试连接，请登录到同一虚拟网络中的另一台虚拟机。 打开“SQL Server Management Studio”并连接到 SQL Server FCI 名称。

>[!NOTE]
>如果需要，可以[下载 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="limitations"></a>限制

Azure 虚拟机支持在 Windows Server 2019 上使用存储在群集共享卷（CSV）和[标准负载均衡器](../../../load-balancer/load-balancer-standard-overview.md)上的 Microsoft 分布式事务处理协调器（MSDTC）。

在 Azure 虚拟机上，Windows Server 2016 或更早版本不支持 MSDTC，原因如下：

- 群集 MSDTC 资源不能配置为使用共享存储。 在 Windows Server 2016 上，如果你创建了 MSDTC 资源，则它不会显示任何可供使用的共享存储，即使存储可用也是如此。 Windows Server 2019 中已修复此问题。
- 基本负载均衡器不处理 RPC 端口。

## <a name="see-also"></a>另请参阅

[设置远程桌面存储空间直通（Azure）](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[具有存储空间直通的超聚合解决方案](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[存储空间直通概述](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[存储空间直通 SQL Server 支持](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
