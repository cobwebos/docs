---
title: "管理 Azure 堆栈中的存储容量 |Microsoft 文档"
description: "监视和管理 Azure 堆栈可用存储空间。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: b0e694e4-3575-424c-afda-7d48c2025a62
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: dce4252846732ca5161018103438df1f9ff6146d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2018
---
# <a name="manage-storage-capacity-for-azure-stack"></a>管理 Azure 堆栈的存储容量

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

这篇文章中的信息可帮助 Azure 堆栈云运算符监视器和管理其 Azure 堆栈部署的存储容量。 Azure 堆栈存储基础结构分配的 Azure 堆栈部署的总存储容量以用于子集**存储服务**。 存储服务在与部署的节点相对应的卷上的共享存储租户的数据。

作为一个云运算符，您可以将有限的要使用的存储量。 由你实现解决方案定义的存储量。 通过你 OEM 供应商联系，当你使用的多节点解决方案，或在其安装 Azure 堆栈开发工具包的硬件，被提供你的解决方案。

因为 Azure 堆栈不支持的存储容量的扩展，它是对重要[监视器](#monitor-shares)可用的存储空间来确保高效的操作，将保留。  

共享的剩余的可用容量有限时，要[管理空间](#manage-available-space)以防共享容量不足。

若要管理容量的选项包括：
- 回收容量
- 迁移容器

共享时使用，100%的存储服务不再对该共享的函数。 若要获取正在还原为共享的操作的帮助，请联系 Microsoft 支持部门。

## <a name="understand-volumes-and-shares-containers-and-disks"></a>了解卷和共享、 容器和磁盘
### <a name="volumes-and-shares"></a>卷和共享
*存储服务*分区到单独且等于分配来保存租户数据的卷的可用存储。 卷的数量等于 Azure 堆栈部署中的节点数：

- 在四个节点部署中，有四个卷。 每个卷具有单个共享。 在多节点部署，共享数不减少节点是否已删除或运行不正常。
- 如果你使用 Azure 堆栈开发人员工具包，则拥有单个共享单个卷。

由于存储服务共享以供独占使用的存储服务，你必须不是直接修改、 添加或删除任何文件共享上。 仅存储服务应处理存储在这些卷中的文件。

卷上的共享包含租户数据。 租户数据包括页 blob，块 blob、 追加 blob、 表、 队列、 数据库和相关元数据存储区。 存储对象 （blob，等等） 单独包含在单个共享数据库，因为每个对象的最大大小不能超过共享的大小。 新对象的最大大小取决于保持在共享中是未使用的空间时创建该新对象的容量。

共享较低时可用空间和操作与[回收](#reclaim-capacity)空间不成功，则可用，Azure 堆栈云操作员可以[迁移](#migrate-a-container-between)从一个共享到另一个 blob 容器。

- 有关容器和 blob 的详细信息，请参阅[Blob 存储](azure-stack-key-features.md#blob-storage)中主要功能和 Azure 堆栈中的概念。
- 有关租户用户使用 Azure 堆栈中的 blob 存储的工作原理的信息，请参阅[Azure 堆栈存储服务](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services)。


### <a name="containers"></a>容器
租户用户创建用于存储 blob 数据的容器。 虽然用户决定在将 blob 的容器，存储服务使用算法来确定哪些卷将容器上。 该算法通常选择具有最多可用空间的卷。  

Blob 放入容器后，可以增大该 blob 以使用更多空间。 在添加新的 blob 和现有 blob 增长，保存该容器收缩卷中的可用空间。  

容器并不局限于单个共享。 当容器中的混合的 blob 数据增长后使用 80%或更多可用空间时，容器将进入*溢出*模式。 在溢出模式下，在该容器中创建的任何新 blob 被分配到具有足够空间的其他卷。 随着时间推移，溢出模式中的容器可以分布在多个卷的 blob。

当使用 80%，然后选择在卷中的可用空间的 90%时，系统会发出 Azure 堆栈管理员门户中的警报。 云操作员应查看可用的存储容量，并打算重新平衡内容。 存储服务停止工作时磁盘为 100%使用，而没有其他警报。

### <a name="disks"></a>磁盘
VM 磁盘通过租户添加到容器，并且包括操作系统磁盘。 Vm 还可以具有一个或多个数据磁盘。 这两种类型的磁盘存储为页 blob。 向租户提供指导是将每个磁盘放入单独的容器来提高虚拟机的性能。
- 每个容器，用于保存从 VM 的磁盘 (页 blob) 被视为拥有磁盘的虚拟机附加的容器。
- 不保存任何磁盘与 VM 的容器被视为可用容器。

以释放空间对附加的容器的选项[限制](#move-vm-disks)。
> [!TIP]  
> 云操作员不直接管理连接到租户可能将添加到容器的虚拟机 (Vm) 的磁盘。 但是，当规划来管理存储共享上的空间，它可以是用于了解磁盘与容器和共享。

## <a name="monitor-shares"></a>监视器共享
使用 PowerShell 或管理门户来监视共享，这样你可以了解当可用空间是有限。 当你使用门户时，你将收到有关空间较低的共享警报。    

### <a name="use-powershell"></a>使用 PowerShell
作为云操作员，你可以监视使用 PowerShell 的共享的存储容量**Get AzsStorageShare** cmdlet。 Get AzsStorageShare cmdlet 在每个共享上，以字节为单位返回总计、 已分配，和可用空间。   
![示例： 返回共享的可用空间](media/azure-stack-manage-storage-shares/free-space.png)

- **总容量**是以字节为单位的共享上可用的总空间。 此空间用于数据和元数据由存储服务维护。
- **已使用容量**是由存储租户数据和关联的元数据文件中的所有范围以字节为单位的数据量。

### <a name="use-the-administrator-portal"></a>使用管理员门户
作为云操作员，你可以使用管理门户以查看所有共享的存储容量。 **转到存储** > **文件共享**打开可在其中查看使用情况信息的文件共享列表。
![示例： 存储文件共享](media/azure-stack-manage-storage-shares/storage-file-shares.png)
- **总**是以字节为单位的共享上可用的总空间。 此空间用于数据和元数据由存储服务维护。
- **使用**是由存储租户数据和关联的元数据文件中的所有范围以字节为单位的数据量。

### <a name="storage-space-alerts"></a>存储空间警报
当你使用管理门户时，你将收到有关空间较低的共享警报。

> [!IMPORTANT]
> 作为云操作员，到达完全利用保留共享。 共享时使用，100%的存储服务不再对该共享的函数。 若要恢复的可用空间，并还原为使用 100%的共享上的操作，您必须联系 Microsoft 支持。

**警告**： 时的文件共享超过 80%的利用，会出现*警告*警报在管理门户中：![示例： 警告性警报](media/azure-stack-manage-storage-shares/alert-warning.png)


**关键**： 时的文件共享超过 90%的利用，会出现*关键*警报在管理门户中：![示例： 严重警报](media/azure-stack-manage-storage-shares/alert-critical.png)

**查看详细信息**： 管理门户中，你可以打开警报以查看缓解选项的详细信息：![示例： 查看警报详细信息](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>管理可用空间
需要到共享上的可用空间时，请首先使用至少侵入性的方法。 例如，尝试在选择要迁移容器之前回收空间。  

### <a name="reclaim-capacity"></a>回收容量
*此选项适用于多节点部署和 Azure 堆栈开发工具包。*

你可以回收已删除的租户帐户使用的容量。 此容量，则自动回收时数据[保持期](azure-stack-manage-storage-accounts.md#set-the-retention-period)为止，或可以立即回收。

有关详细信息，请参阅[回收容量](azure-stack-manage-storage-accounts.md#reclaim)管理存储资源中。

### <a name="migrate-a-container-between-volumes"></a>迁移之间卷容器
*此选项仅适用于多节点部署。*

由于租户使用情况模式中，一些租户共享使用比其他更多空间。 结果可以是相对较未使用其他共享之前的空间不足的共享。

你可以尝试通过手动将某些 blob 容器迁移到其他共享释放一些空间使用过度的共享。 你可以迁移到具有容量来容纳所有的单个共享几个较小的容器。 你可以使用迁移来移动*免费*容器。 可用的容器是为 VM 不包含磁盘的容器。   

迁移将合并新的共享上的所有容器 blob。

- 如果容器已进入溢出模式，并且已在其他卷上放置 blob，新的共享必须具有足够的容量来容纳所有迁移的容器的 blob。 这包括位于其他共享的 blob。

- PowerShell cmdlet *Get AzsStorageContainer*标识仅在容器上的初始卷使用的空间。 该 cmdlet 不会确定 blob 放在其他卷上使用的空间。 因此，容器的完整大小可能不是显而易见的。 有可能，合并的新的共享上的一个容器可以发送该新共享到溢出条件放置到更多的共享的数据。 因此，你可能需要再次重新平衡共享。

- 如果你缺少到资源组的权限，并能使用 PowerShell 来查询溢出数据的其他卷，使用这些资源组和容器以了解数据的总大小在迁移该数据之前迁移的所有者。  

> [!IMPORTANT]
> 容器的 blob 的迁移是脱机操作需要的 PowerShell 使用。 迁移完成之前，你要迁移的容器的所有 blob 保持脱机状态，并且不能使用。

#### <a name="to-migrate-containers-using-powershell"></a>若要迁移容器使用 PowerShell
1. 确认你拥有[Azure PowerShell 安装和配置](http://azure.microsoft.com/documentation/articles/powershell-install-configure/)。 有关详细信息，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](http://go.microsoft.com/fwlink/?LinkId=394767)。
2.  检查要了解哪些数据是你计划迁移的共享上的容器。 若要标识在卷中的迁移的最佳候选容器，请使用**Get AzsStorageContainer** cmdlet:

    ```
    $shares = Get-AzsStorageShare
    $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -Intent Migration
    ```
    然后检查 $containers:
    ```
    $containers
    ```
    ![示例： $Containers](media/azure-stack-manage-storage-shares/containers.png)

3.  确定最佳的目标共享，以保存你所迁移的容器：
    ```
    $destinationshares = Get-AzsStorageShare -SourceShareName
    $shares[0].ShareName -Intent ContainerMigration
    ```
    然后检查 $destinationshares:
    ```
    $destinationshares
    ```    
    ![示例： $destination 共享](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. 启动容器的迁移。 迁移是异步的。 如果你在开始迁移其他容器的第一次迁移完成之前，，使用作业 id 来跟踪每个状态。
  ```
  $jobId = Start-AzsStorageContainerMigration -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares[0].UncPath
  ```
  然后检查 $jobId。 在以下示例中，将*d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0*与你想要检查的作业 id:
  ```
  $jobId
  d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
  ```
5. 使用作业 id 检查迁移作业的状态。 容器迁移完成后， **MigrationStatus**设置为**完成**。
  ```
  Get-AzsStorageContainerMigrationStatus -JobId $jobId
  ```
  ![示例： 迁移状态](media/azure-stack-manage-storage-shares/migration-status1.png)

6.  你可以取消正在进行的迁移作业。 取消的迁移作业的异步处理。 你可以通过使用 $jobid 跟踪取消：

  ```
  Stop-AzsStorageContainerMigration -JobId $jobId
  ```
  ![示例： 回滚状态](media/azure-stack-manage-storage-shares/rollback.png)

7. 你可以运行该命令步骤 6 中再次，直到状态确认迁移作业是**已取消**:  
    ![示例： 已取消的状态](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>移动 VM 磁盘
*此选项仅适用于多节点部署。*

管理空间最极端方法涉及移动虚拟机磁盘。 由于移动 （一个包含的 VM 磁盘） 的附加的容器很复杂，请与 Microsoft 支持，以完成此操作。

## <a name="next-steps"></a>后续步骤
详细了解[向用户提供虚拟机](azure-stack-tutorial-tenant-vm.md)。
