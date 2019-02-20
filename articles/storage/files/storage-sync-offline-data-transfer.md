---
title: 为向 Azure 文件同步进行脱机引入使用 Data Box 及其他方法。
description: 启用同步兼容批量迁移支持的进程和最佳做法。
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.component: files
ms.openlocfilehash: a184e0563d1ad26671c38cabe07f42d97cbe2885
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212333"
---
# <a name="migrate-to-azure-file-sync"></a>迁移到 Azure 文件同步
可通过以下几个选项迁移到 Azure 文件同步：

### <a name="uploading-files-via-azure-file-sync"></a>通过 Azure 文件同步上传文件
最简单的选项是将文件本地移动到 Windows Server 2012 R2 或更高版本，然后安装 Azure 文件同步代理。 配置同步后，文件将从服务器上传。 目前所有客户的平均上传速度为每 2 天大约 1 TB。
请考虑通过[带宽限制计划](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)确保服务器在数据中心内的状态良好。

### <a name="offline-bulk-transfer"></a>脱机批量传输
上传肯定是最简单的选择，而如果可用带宽无法在合理的时间内将文件同步到 Azure，它可能就无效。 这里要克服的难题是整个文件集的初始同步。 随后，Azure 文件同步将仅在命名空间中发生更改时迁移这些更改，占用的带宽就低得多。
使用脱机批量迁移工具（例如 Azure [Data Box 系列](https://azure.microsoft.com/services/storage/databox)工具）可解决初始上传这一项难题。 文本接下来重点介绍以兼容 Azure 文件同步的方式执行脱机迁移的进程。 还将介绍有助于避免文件冲突并在启用同步后保留文件和文件夹 ACL 及时间戳的最佳做法。

### <a name="online-migration-tools"></a>联机迁移工具
下面概述的过程并非只适用于 Data Box。 它适用于任何脱机迁移工具（如 Data Box）或联机工具（如 AzCopy、Robocopy 或第三方工具和服务）。 无论采用何种方法来解决初始上传这一个难题，按照下面列出的步骤以兼容同步的方式使用这些工具都很重要。


## <a name="offline-data-transfer-benefits"></a>脱机数据传输的好处
使用 Data Box 进行脱机迁移的主要益处如下：

- 通过脱机批量传输过程（例如 Data Box）将文件迁移到 Azure 时，你不必通过网络从服务器上传所有文件。 对于大命名空间，这可能意味着可以节省大量网络带宽和时间。
- 使用 Azure 文件同步时，无论使用何种传输方式（例如 Data Box、Azure 导入），实时服务器均只上载将数据传送到 Azure 后已更改的文件。
- Azure 文件同步确保也同步了文件和文件夹 ACL，即使脱机批量迁移产品不传输 ACL。
- 使用 Azure Data Box 和 Azure 文件同步时，故障时间为零。 使用 Data Box 将数据传输到 Azure 可有效利用网络带宽，同时确保文件保真。 只上传发送 Data Box 以来已更改的文件还可以让命名空间保持最新。

## <a name="plan-your-offline-data-transfer"></a>计划脱机数据传输
在开始之前，请查看以下信息：

- 先完成向一个或多个 Azure 文件共享的批量迁移，再启用与 Azure 文件同步的同步。
- 如果你打算使用 Data Box 进行批量迁移：请查看 [Data Box 的部署先决条件](../../databox/data-box-deploy-ordered.md#prerequisites)。
- 规划最终的 Azure 文件同步拓扑：[规划 Azure 文件同步部署](storage-sync-files-planning.md)
- 选择将保存要与其同步的文件共享的 Azure 存储帐户。 确保批量迁移发生在同一存储帐户中的临时暂存共享中。 批量迁移只能通过使用驻留在同一存储帐户中的最终共享和暂存共享启用。
- 仅在创建与服务器位置的新同步关系时，才能使用批量迁移。 无法使用现有同步关系启用批量迁移。

## <a name="offline-data-transfer-process"></a>脱机数据传输过程
本部分介绍以与 Azure Data Box 等批量迁移工具兼容的方式设置 Azure 文件同步的过程。

![该过程的直观步骤（下一段中也对这些步骤作出了详细说明）](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 步骤 | 详细信息 |
|---|---------------------------------------------------------------------------------------|
| ![过程步骤 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [订购 Data Box](../../databox/data-box-deploy-ordered.md)。 [Data Box 系列中有几个产品/服务](https://azure.microsoft.com/services/storage/databox/data)可满足你的需求。 接收 Data Box 并按照 Data Box [文档复制数据](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box)。 请确保将数据复制到 Data Box 上的此 UNC 路径：`\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>`其中 `ShareName` 是暂存共享的名称。 将 Data Box 发送回 Azure。 |
| ![过程步骤 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 等待文件显示在指定为临时暂存共享的 Azure 文件共享中。 切勿启用向这些共享同步！ |
| ![过程步骤 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | 为 Data Box 为你创建的每个文件共享创建一个空的新共享。 确保此新共享与 Data Box 共享位于同一存储帐户中。 [如何创建新的 Azure 文件共享](storage-how-to-create-file-share.md)。 |
| ![过程步骤 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | 在存储同步服务中[创建同步组](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint)，并将空共享作为云终结点引用。 对每个 Data Box 文件共享重复此步骤。 查看[部署 Azure 文件同步](storage-sync-files-deployment-guide.md)指南，并按照设置 Azure 文件同步所需的步骤进行操作。 |
| ![过程步骤 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [将实时服务器目录添加为服务器终结点](storage-sync-files-deployment-guide.md#create-a-server-endpoint)。 在此过程中指定已将文件移动到 Azure 并引用暂存共享。 可以根据需要启用或禁用云分层。 在实时服务器上创建服务器终结点时，需要引用暂存共享。 在新服务器终结点边栏选项卡中启用“脱机数据传输”（见下图），并引用必须与云终结点位于同一存储帐户中的暂存共享。 可用共享列表按存储帐户和尚未同步的共享进行筛选。 |

![直观呈现创建新服务器终结点时启用脱机数据传输的 Azure 门户用户界面。](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>同步共享
同步在创建服务器终结点后开始。 对于服务器上具有的每个文件，同步将确定它是否也在 Data Box 存放文件的暂存共享中。如果是，同步将从暂存共享复制该文件，而不是从服务器上传该文件。 如果暂存共享中没有该文件，或者本地服务器上有较新版本，则同步将从本地服务器上传该文件。

> [!IMPORTANT]
> 只能在创建服务器终结点期间启用批量迁移模式。 一旦建立了服务器终结点，目前就无法将批量迁移的数据从已在同步的服务器集成到命名空间中。

## <a name="acls-and-timestamps-on-files-and-folders"></a>文件和文件夹上的 ACL 和时间戳
Azure 文件同步将确保从实时服务器同步文件和文件夹 ACL，即使所用的批量迁移工具最初未传输 ACL。 也就是说，暂存共享可以不包含文件和文件夹上的任何 ACL。 如果在创建新服务器终结点时启用脱机数据迁移功能，将在此时为服务器上的所有文件同步 ACL。 也将同步创建时间和修改时间这两个时间戳。

## <a name="shape-of-the-namespace"></a>命名空间的形状
命名空间的形状取决于启用同步时服务器上的内容。 如果于 Data Box 执行快照和迁移之后在本地服务器上删除了文件，则不会将这些文件放入实时同步命名空间。 它们仍在暂存共享中，但不会复制它们。 同步根据实时服务器保留命名空间时，这就是预期的行为。 Data Box 快照只是高效复制文件的基础，不确保实时命名空间的形状。

## <a name="finishing-bulk-migration-and-clean-up"></a>完成批量迁移和清理
下面的屏幕截图显示了 Azure 门户中的服务器终结点属性边栏选项卡。 可在脱机数据传输部分中查看进程的状态。 它将显示“正在进行中”或“已完成”。

服务器在完成整个命名空间的初始同步后，对含 Data Box 批量迁移文件的暂存文件共享的使用就已经完成了。 观察状态更改为“完成”的脱机数据传输的服务器终结点属性。 此时，你可以清理暂存共享以节省成本：

1. 状态为“已完成”时，点击服务器终结点属性中的“禁用脱机数据传输”。
2. 请考虑删除暂存共享以节省成本。 暂存共享不太可能包含文件和文件夹 ACL，因此其使用受限。 若要即时备份，则请创建[正在同步的 Azure 文件共享的真实快照](storage-snapshots-files.md)。 你可以[启用 Azure 备份以按计划拍摄快照]( ../../backup/backup-azure-files.md)。

![直观显示脱机数据传输状态和禁用控件所在的服务终结点属性的 Azure 门户用户界面。](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

应仅在状态为“已完成”或者因配置错误而确实希望终止时禁用此模式。 如果在合法部署过程中禁用该模式，即使暂存共享仍然可用，系统也将开始从服务器上传文件。

> [!IMPORTANT]
> 禁用脱机数据传输后，即使批量迁移中的暂存共享仍可用，也无法再次启用它。

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件同步部署](storage-sync-files-planning.md)
- [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
