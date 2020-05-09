---
title: 灾难恢复和存储帐户故障转移
titleSuffix: Azure Storage
description: Azure 存储支持异地冗余存储帐户的帐户故障转移。 通过帐户故障转移，可以在主终结点不可用时为存储帐户启动故障转移过程。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 6534e7d3a05434855503a9cbf1e675aa11799984
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857776"
---
# <a name="disaster-recovery-and-storage-account-failover"></a>灾难恢复和存储帐户故障转移

Microsoft 致力于确保 Azure 服务一直可用。 不过，可能会发生计划外服务中断。 如果你的应用程序需要复原，Microsoft 建议使用异地冗余存储，以便将你的数据复制到第二个区域。 此外，客户还应制定用于处理区域服务中断的灾难恢复计划。 灾难恢复计划的一个重要组成部分是，准备在主终结点不可用时将故障转移到辅助终结点。

Azure 存储支持异地冗余存储帐户的帐户故障转移。 通过帐户故障转移，可以在主终结点不可用时为存储帐户启动故障转移过程。 故障转移将辅助终结点更新为，存储帐户的主终结点。 在故障转移完成后，客户端便可以开始对新的主终结点执行写入操作。

帐户故障转移适用于常规用途 v1、常规用途 v2 以及使用 Azure 资源管理器部署的 Blob 存储帐户类型。 所有公共区域都支持帐户故障转移，但目前在主权或国内云中不可用。

本文介绍了帐户故障转移所涉及的概念和过程，以及如何让存储帐户做好恢复准备，且造成的客户影响最小。 若要了解如何在 Azure 门户或 PowerShell 中启动帐户故障转移，请参阅[启动帐户故障转移](storage-initiate-account-failover.md)。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>选择正确的冗余选项

Azure 存储将维护存储帐户的多个副本，以确保持续性和高可用性。 为帐户选择哪个冗余选项取决于所需的复原能力水平。 若要防范区域性服务中断，请为异地冗余存储配置你的帐户，但不需要从次要区域读取访问权限：  

**异地冗余存储（GRS）或异地冗余存储（GZRS）** 会以异步方式将数据复制到介于数百英里之外的两个地理区域。 如果主要区域遭遇服务中断，次要区域便会成为数据的冗余源。 可以通过启动故障转移，将辅助终结点转换为主终结点。

**读取访问异地冗余存储（GRS）或读取访问地域冗余存储（GZRS）** 提供了异地冗余存储，并具有对辅助终结点的读取访问权限。 如果主终结点发生服务中断，则配置为对辅助终结点的读访问权限并为实现高可用性而设计的应用程序可以继续从辅助终结点读取。 Microsoft 建议 GZRS 以实现应用程序的最高可用性和持久性。

有关 Azure 存储中冗余的详细信息，请参阅 [Azure 存储冗余](storage-redundancy.md)。

> [!WARNING]
> 异地冗余存储有数据丢失风险。 数据是异步复制到次要区域，这意味着数据写入主要区域与数据写入次要区域之间存在延迟。 发生服务中断时，尚未复制到辅助终结点的对主终结点的写入操作将丢失。

## <a name="design-for-high-availability"></a>旨在实现高可用性

请务必从一开始就设计高可用性应用程序。 有关设计应用程序和计划灾难恢复方面的指导，请参阅以下 Azure 资源：

- [设计适用于 azure 的弹性应用程序](/azure/architecture/framework/resiliency/app-design)：概述在 azure 中构建高度可用的应用程序的关键概念。
- [复原清单](/azure/architecture/checklist/resiliency-per-service)：一个核对清单，用于验证应用程序是否实现了高可用性最佳设计方案。
- [使用异地冗余设计高度可用的应用程序](geo-redundant-design.md)：用于构建应用程序以利用异地冗余存储的设计指南。
- [教程：使用 Blob 存储构建高度可用的应用程序](../blobs/storage-create-geo-redundant-storage.md)：演示如何构建高度可用的应用程序，该应用程序可在发生故障时自动切换到终结点，并模拟恢复。 

此外，还请注意下面这些可保持 Azure 存储数据高可用性的最佳做法：

- **磁盘：** 使用[Azure 备份](https://azure.microsoft.com/services/backup/)来备份 Azure 虚拟机所用的 VM 磁盘。 还建议在发生区域灾难时使用 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 保护 VM。
- **块 blob：** 启用[软删除](../blobs/storage-blob-soft-delete.md)，通过[AzCopy](storage-use-azcopy.md)、 [Azure PowerShell](/powershell/module/az.storage/)或[Azure 数据移动库](storage-use-data-movement-library.md)，防止对象级删除和覆盖，或将块 blob 复制到其他区域中的其他存储帐户。
- **文件：** 使用[AzCopy](storage-use-azcopy.md)或[Azure PowerShell](/powershell/module/az.storage/)将文件复制到不同区域中的其他存储帐户。
- **表：** 使用 [AzCopy](storage-use-azcopy.md) 将表数据导出到其他区域中的另一个存储帐户内。

## <a name="track-outages"></a>跟踪服务中断

客户可以订阅 [Azure 服务运行状况仪表板](https://azure.microsoft.com/status/)，以跟踪 Azure 存储和其他 Azure 服务的运行状况和状态。

Microsoft 还建议将应用程序设计为，可以应对可能出现的写入故障。 应用程序应公开写入故障，以提醒你主要区域可能存在服务中断。

## <a name="understand-the-account-failover-process"></a>了解帐户故障转移过程

如果主要副本由于任何原因而不可用，则客户管理的帐户故障转移使你能够将整个存储帐户故障转移到次要区域。 如果你强制故障转移到次要区域，客户端可以在故障转移完成后开始向辅助终结点写入数据。 故障转移通常需要大约一小时才能完成。

### <a name="how-an-account-failover-works"></a>帐户故障转移的工作原理

在正常情况下，客户端会将数据写入主要区域中的 Azure 存储帐户，并将数据异步复制到次要区域。 下图展示了主要区域可用时的场景：

![客户端将数据写入主要区域中的存储帐户](media/storage-disaster-recovery-guidance/primary-available.png)

如果主终结点因任何原因而不可用，客户端无法再向存储帐户写入数据。 下图展示了主终结点不可用、但尚未执行恢复时的场景：

![主终结点不可用，因此客户端无法写入数据](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

客户启动帐户故障转移到辅助终结点。 故障转移过程更新 Azure 存储提供的 DNS 条目，这样辅助终结点就会成为存储帐户的新主终结点，如下图所示：

![客户启动帐户故障转移到辅助终结点](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

一旦 DNS 条目已更新并且请求被定向到新的主终结点，则会为异地冗余帐户还原写入访问权限。 在故障转移完成后，用于 blob、表、队列和文件的现有存储服务终结点保持不变。

> [!IMPORTANT]
> 在故障转移完成后，存储帐户被配置为在新的主终结点中本地冗余。 若要恢复复制到新的辅助数据库，请重新配置该帐户以进行异地冗余。
>
> 请记住，转换 LRS 帐户以使用异地冗余会产生费用。 此成本适用于在故障转移后更新新的主要区域中的存储帐户。  

### <a name="anticipate-data-loss"></a>预测数据丢失

> [!CAUTION]
> 帐户故障转移通常涉及一些数据丢失。 请务必了解启动帐户故障转移的影响。  

由于将数据从主要区域异步写入次要区域，因此在将主要区域写入到次要区域之前，总会有一段延迟。 如果主要区域变得不可用，则最近的写入可能尚未复制到次要区域。

如果强制执行故障转移，主要区域中的所有数据就会在次要区域成为新的主要区域且存储帐户配置为本地冗余时丢失。 发生故障转移时，已复制到辅助副本的所有数据都将保留。 但是，写入到未复制到辅助副本的主副本的任何数据都将永久丢失。

“上次同步时间”**** 属性表示，最近一次保证已将主要区域中的数据写入次要区域的时间。 上次同步时间之前写入的所有数据都已复制到次要区域中，而在上次同步时间之后写入的数据则可能尚未写入次要区域并发生丢失。 在发生服务中断时，使用此属性可估计启动帐户故障转移可能会造成的数据丢失量。

最佳做法是，将应用程序设计为，可以使用上次同步时间来评估预期数据丢失。 例如，若要记录所有写入操作，可以比较上次写入操作时间与上次同步时间，以确定哪些写入操作尚未同步到次要区域。

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>谨慎故障回复到原始主要区域

从主要区域故障转移到次要区域后，存储帐户被配置为在新的主要区域中本地冗余。 然后，你可以重新配置该帐户以进行异地冗余。 如果在故障转移后再次为该帐户配置了异地冗余，则新的主要区域会立即开始将数据复制到新的次要区域，这是原始故障转移前的主区域。 但是，在主数据库中的现有数据完全复制到新的辅助数据库之前，可能需要一段时间。

为存储帐户重新配置异地冗余后，可以启动另一个故障转移，从新的主要区域故障回复到新的次要区域。 在这种情况下，故障转移发生前的原始主要区域重新成为主要区域，并配置为本地冗余。 故障转移完成后的主要区域（即原始次要区域）中的所有数据都会丢失。 如果在故障回复之前，存储帐户中的大部分数据尚未复制到新的辅助数据库，则可能会丢失重大数据。

为了避免大量数据丢失，请在故障回复前检查“上次同步时间”**** 属性的值。 若要评估预期数据丢失，请比较上次同步时间与数据上次写入新的主要区域时间。 

## <a name="initiate-an-account-failover"></a>启动帐户故障转移

可以从 Azure 门户、PowerShell、Azure CLI 或 Azure 存储资源提供程序 API 启动帐户故障转移。 有关如何启动故障转移的详细信息，请参阅[启动帐户故障转移](storage-initiate-account-failover.md)。

## <a name="additional-considerations"></a>其他注意事项

查看本部分中所述的其他注意事项，以了解在强制故障转移时，应用程序和服务可能受到哪些影响。

### <a name="storage-account-containing-archived-blobs"></a>包含存档 blob 的存储帐户

包含存档 blob 的存储帐户支持帐户故障转移。 故障转移完成后，所有已存档的 blob 都需要解除冻结到联机层，然后才能配置帐户以实现异地冗余。

### <a name="storage-resource-provider"></a>存储资源提供程序

故障转移完成后，客户端可再次读取并写入新的主要区域中的 Azure 存储数据。 但是，Azure 存储资源提供程序不会进行故障转移，因此资源管理操作仍必须在主要区域中发生。 如果主要区域不可用，将无法对存储帐户执行管理操作。

由于 Azure 存储资源提供程序不进行故障转移，因此在故障转移完成后， [Location](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location)属性将返回原始主位置。

### <a name="azure-virtual-machines"></a>Azure 虚拟机

Azure 虚拟机 (VM) 不会在帐户故障转移过程中进行故障转移。 如果主要区域不可用且你故障转移到次要区域，那么就需要在故障转移完成后重新创建所有 VM。 此外，还可能会丢失与帐户故障转移相关联的数据。 Microsoft 建议特定于 Azure 中的虚拟机的以下[高可用性](../../virtual-machines/windows/manage-availability.md)和[灾难恢复](../../virtual-machines/virtual-machines-disaster-recovery-guidance.md)指南。

### <a name="azure-unmanaged-disks"></a>Azure 非托管磁盘

根据最佳做法，Microsoft 建议将非托管磁盘转换为托管磁盘。 不过，如果需要对包含附加到 Azure VM 的非托管磁盘的帐户进行故障转移，必须在启动故障转移前关闭 VM。

非托管磁盘在 Azure 存储中存储为页 blob。 如果 VM 在 Azure 中运行，任何附加到 VM 的非托管磁盘都会被租用。 如果 blob 上有租用，便无法继续帐户故障转移。 若要执行故障转移，请按照以下步骤操作：

1. 开始前，先记下所有非托管磁盘的名称、逻辑单元号 (LUN) 和附加到的 VM。 此操作可便于在故障转移完成后更轻松地重新附加磁盘。
2. 关闭 VM。
3. 删除 VM，但保留非托管磁盘的 VHD 文件。 记下 VM 删除时间。
4. 等到“上次同步时间”**** 已更新且晚于 VM 删除时间。 这一步很重要，因为如果在故障转移发生时辅助终结点尚未使用 VHD 文件完全更新，那么 VM 可能无法在新的主要区域中正常运行。
5. 启动帐户故障转移。
6. 等到帐户故障转移完成，且次要区域已成为新的主要区域。
7. 在新的主要区域中创建 VM，并重新附加 VHD。
8. 启动新 VM。

请注意，当 VM 关闭时，临时磁盘中存储的任何数据都会丢失。

## <a name="unsupported-features-and-services"></a>不支持的功能和服务

帐户故障转移不支持以下功能和服务：

- Azure 文件同步不支持存储帐户故障转移。 不得对包含 Azure 文件共享且用作 Azure 文件同步中云终结点的存储帐户执行故障转移。 否则，将会导致同步停止，并且可能还会在有新分层文件的情况下导致意外数据丢失。
- 此时不支持 ADLS Gen2 存储帐户（已启用分层命名空间的帐户）。
- 无法对包含高级块 blob 的存储帐户执行故障转移。 支持高级块 blob 的存储帐户暂不支持异地冗余。
- 不能对包含任何启用了[蠕虫永久性策略](../blobs/storage-blob-immutable-storage.md)的容器的存储帐户进行故障转移。 已解锁/锁定的基于时间的保留或法律封存策略阻止故障转移，以便保持符合性。

## <a name="copying-data-as-an-alternative-to-failover"></a>除了故障转移外，还可以复制数据

如果将存储帐户配置为对辅助副本进行读访问，则可以将应用程序设计为从辅助终结点读取。 如果不希望在主要区域发生服务中断时进行故障转移，可使用 [AzCopy](storage-use-azcopy.md)、[Azure PowerShell](/powershell/module/az.storage/) 或 [Azure 数据移动库](../common/storage-use-data-movement-library.md)等工具，将数据从次要区域中的存储帐户复制到未受影响区域中的另一个存储帐户。 然后，可以将应用程序指向此存储帐户，以进行读写操作。

> [!CAUTION]
> 不应在数据迁移策略中使用帐户故障转移。

## <a name="microsoft-managed-failover"></a>Microsoft 托管的故障转移

在由于重大灾难而导致区域丢失的极端情况下，Microsoft 可能会启动区域故障转移。 在此情况下，不需要采取任何操作。 在 Microsoft 托管的故障转移完成之前，你对存储帐户不拥有写入访问权限。 如果你的存储帐户已配置为 GRS 或 GZRS，则你的应用程序可以从次要区域读取。

## <a name="see-also"></a>另请参阅

- [使用异地冗余设计高度可用的应用程序](geo-redundant-design.md)
- [启动帐户故障转移](storage-initiate-account-failover.md)
- [教程：使用 Blob 存储构建高度可用的应用程序](../blobs/storage-create-geo-redundant-storage.md)
