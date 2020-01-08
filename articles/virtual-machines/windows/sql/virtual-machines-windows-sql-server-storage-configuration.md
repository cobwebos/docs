---
title: SQL Server VM 的存储配置 | Microsoft Docs
description: 本主题介绍 Azure 在预配期间如何配置 SQL Server VM 的存储（Resource Manager 部署模型）。 此外，还说明了如何为现有的 SQL Server VM 配置存储。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 231c8cb8e66d658ad49e02fd585f6c8a1593cb2d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75373975"
---
# <a name="storage-configuration-for-sql-server-vms"></a>SQL Server VM 的存储配置

在 Azure 中配置 SQL Server 虚拟机映像时，可以借助门户自动完成存储配置。 这包括将存储附加到 VM、使该存储可供 SQL Server 访问，并对其进行配置以根据特定的性能要求优化。

本主题介绍 Azure 如何在预配期间针对 SQL Server VM 以及针对现有的 VM 配置存储。 此配置基于运行 SQL Server 的 Azure VM 的[性能最佳实践](virtual-machines-windows-sql-performance.md)。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>必备组件

若要使用自动存储配置设置，虚拟机需有以下特征：

* 已使用 [SQL Server 库映像](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)预配。
* 使用 [Resource Manager 部署模型](../../../azure-resource-manager/resource-manager-deployment-model.md)。
* 使用[高级 SSD](../disks-types.md)。

## <a name="new-vms"></a>新的 VM

以下部分介绍了如何为新的 SQL Server 虚拟机配置存储。

### <a name="azure-portal"></a>Azure 门户

使用 SQL Server 库映像预配 Azure VM 时，请在 " **SQL Server 设置**" 选项卡上选择 "**更改配置**" 以打开 "性能优化存储配置" 页。 你可以将值保留为默认值，或根据工作负载修改最适合你的需求的磁盘配置类型。 

![预配期间的 SQL Server VM 存储配置](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

在 "**存储优化**" 下选择要为其部署 SQL Server 的工作负载的类型。 使用**常规**优化选项时，默认情况下，你将有一个数据磁盘的最大 IOPS 为5000，并且你的数据、事务日志和 TempDB 存储将使用此相同驱动器。 选择**事务处理**（OLTP）或**数据仓库**时，将为数据创建单独的磁盘，为事务日志创建单独的磁盘，并将本地 SSD 用于 TempDB。 **事务处理**和**数据仓库**之间没有存储差别，但它确实更改了[条带配置和跟踪标志](#workload-optimization-settings)。 选择 "高级存储" 可将数据驱动器的缓存设置为*ReadOnly* ，而对于日志驱动器，则设置为 "*无*"，因为每[SQL Server VM 性能最佳做法](virtual-machines-windows-sql-performance.md)。 

![预配期间的 SQL Server VM 存储配置](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

磁盘配置可完全自定义，因此，你可以配置 SQL Server VM 工作负荷所需的存储拓扑、磁盘类型和 IOPs。 如果 SQL Server VM 位于某个受支持区域（美国东部2、东南亚和北欧），并且已[为订阅启用了 ultra 磁盘](/azure/virtual-machines/windows/disks-enable-ultra-ssd)，则还可以使用 UltraSSD （预览）作为**磁盘类型**的选项。  

此外，还可以设置磁盘的缓存。 与[高级磁盘](/azure/virtual-machines/windows/disks-types#premium-ssd)结合使用时，Azure vm 具有称为[Blob 缓存](/azure/virtual-machines/windows/premium-storage-performance#disk-caching)的多层缓存技术。 Blob 缓存使用虚拟机 RAM 和本地 SSD 的组合进行缓存。 

高级 SSD 的磁盘缓存可以是*ReadOnly*、 *ReadWrite*或*None*。 

- 对于存储在高级存储上的 SQL Server 数据文件，*只读*缓存非常有用。 *ReadOnly*缓存会使读取延迟较低，读取率较高，吞吐量为，读取是从缓存执行的，后者是在 VM 内存和本地 SSD 中进行的。 与从 Azure blob 存储读取数据磁盘相比，这些读取速度要快得多。 高级存储不会将从缓存提供的读取次数计入磁盘 IOPS 和吞吐量。 因此，适用的可以实现更高的总 IOPS，即 ant 吞吐量。 
- 如果日志文件是按顺序写入的，则不会对承载 SQL Server 日志文件的磁盘使用 "*无*缓存" 配置，也不能从*ReadOnly*缓存中获益。 
- *Readwrite*缓存不能用于宿主 SQL Server 文件，因为 SQL Server 不支持与*ReadWrite*缓存的数据一致性。 写入*只读 blob*缓存的浪费容量，如果写入经过*只读*blob 缓存层，则延迟会略微增加。 


   > [!TIP]
   > 请确保存储配置与所选 VM 大小施加的限制相匹配。 选择超出 VM 大小的性能上限的存储参数将导致错误： `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`。 可以通过更改磁盘类型来减少 IOPs，或增加 VM 大小以提高性能上限。 


根据所做的选择，Azure 会在创建 VM 后执行以下存储配置任务：

* 创建高级 SSD 盘并将其连接到虚拟机。
* 配置 SQL Server 可访问的数据磁盘。
* 根据指定的大小和性能（IOPS 和吞吐量）要求，在存储池中配置数据磁盘。
* 将存储池与虚拟机上的新驱动器相关联。
* 根据指定的工作负荷类型（“数据仓库”、“事务处理”或“常规”）优化新驱动器。

有关 Azure 如何配置存储设置的详细信息，请参阅[存储配置部分](#storage-configuration)。 有关如何在 Azure 门户中创建 SQL Server VM 的完整演练，请参阅[预配教程](virtual-machines-windows-portal-sql-server-provision.md)。

### <a name="resource-manage-templates"></a>Resource Manager 模板

如果使用以下 Resource Manager 模板，则会默认附加两个不带存储池配置的高级数据磁盘。 但是，可以自定义这些模板，更改附加到虚拟机的高级数据磁盘的数目。

* [使用自动备份创建 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [使用自动修补创建 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [使用 AKV 集成创建 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>快速入门模板

可以使用以下快速入门模板，通过存储优化部署 SQL Server VM。 

* [创建具有存储优化的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [使用 UltraSSD 创建 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>现有 VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

对于现有的 SQL Server VM，可以在 Azure 门户中修改某些存储设置。 打开[SQL 虚拟机资源](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)，然后选择 "**概述**"。 SQL Server 概述 "页显示 VM 的当前存储使用情况。 此图显示了 VM 上存在的所有驱动器。 每个驱动器的存储空间都分四个部分显示：

* SQL 数据
* SQL 日志
* 其他（非 SQL 存储）
* 可用

若要修改存储设置，请选择 "**设置**" 下的 "**配置**"。 

![为现有 SQL Server VM 配置存储](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

您可以修改在 SQL Server VM 创建过程中配置的驱动器的磁盘设置。 选择 "**扩展驱动器**" 将打开 "驱动器修改" 页，允许您更改磁盘类型并添加更多磁盘。 

![为现有 SQL Server VM 配置存储](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>存储配置

本部分提供有关 Azure 在 Azure 门户中的 SQL VM 预配或配置期间自动执行的存储配置更改的参考。

* Azure 通过从 VM 选择的存储配置存储池。 本主题的下一部分提供了有关存储池配置的详细信息。
* 自动存储配置始终使用[高级 SSD](../disks-types.md) P30 数据磁盘。 因此，所选 TB 数目与附加到 VM 的数据磁盘数目之间存在 1:1 映射。

有关价格信息，请参阅 [磁盘存储](https://azure.microsoft.com/pricing/details/storage) 选项卡上的 **存储定价** 页。

### <a name="creation-of-the-storage-pool"></a>创建存储池

Azure 使用以下设置在 SQL Server VM 上创建存储池。

| 设置 | 值 |
| --- | --- |
| 条带大小 |256 KB（数据仓库）；64 KB（事务） |
| 磁盘大小 |每个磁盘 1 TB |
| 缓存 |读取 |
| 分配大小 |64 KB NTFS 分配单元大小 |
| 恢复 | 简单恢复（不可复原） |
| 列数 |最<sup>多8个</sup>数据磁盘的数量 |


<sup>1</sup> 创建存储池后，无法更改存储池中的列数。


## <a name="workload-optimization-settings"></a>工作负荷优化设置

下表描述了三个可用的工作负荷类型选项及其对应的优化：

| 工作负载类型 | Description | 优化 |
| --- | --- | --- |
| **常规** |支持大多数工作负荷的默认设置 |无 |
| **事务处理** |针对传统数据库 OLTP 工作负荷优化存储 |跟踪标志 1117<br/>跟踪标志 1118 |
| **数据仓库** |针对分析和报告工作负荷优化存储 |跟踪标志 610<br/>跟踪标志 1117 |

> [!NOTE]
> 只有通过在预配 SQL 虚拟机时，在存储配置步骤中进行选择，才能指定工作负荷类型。

## <a name="next-steps"></a>后续步骤

有关其他与在 Azure VM 中运行 SQL Server 相关的主题，请参阅 [SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md)（Azure 虚拟机上的 SQL Server）。
