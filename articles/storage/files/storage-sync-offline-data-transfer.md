---
title: 使用 Azure Data Box 和其他方法将数据迁移到 Azure 文件同步
description: 将 Azure 文件同步与兼容的方式大容量数据迁移。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d1ec5168b898d0aa75c12e6eb435e20c09de1929
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64700277"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>将大容量数据迁移到 Azure 文件同步
两种方式，可以将大容量数据迁移到 Azure 文件同步：

* **使用 Azure 文件同步上传文件。** 这是最简单方法。 将文件本地到 Windows Server 2012 R2 或更高版本，移动并安装 Azure 文件同步代理。 设置在同步后，将从服务器上传文件。 （我们的客户当前遇到有关每隔两天的 1 TiB 平均上传的速度。）若要确保你的服务器不会使用太多带宽为你的数据中心，你可能想要将设置[带宽限制计划](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)。
* **传输文件脱机。** 如果你没有足够的带宽，可能无法在合理的时间内将文件上载到 Azure。 面临的挑战是整个组文件的初始同步。 若要解决这一挑战，使用脱机批量迁移工具如[Azure Data Box 系列](https://azure.microsoft.com/services/storage/databox)。 

本文介绍如何将迁移脱机文件是 Azure 文件同步与兼容的方式。按照这些说明进行操作以避免文件冲突并保留文件和文件夹的访问控制列表 (Acl) 和时间戳后启用同步。

## <a name="online-migration-tools"></a>联机迁移工具
不仅为数据框中，还为其他脱机迁移工具在此文章的工作原理中介绍的过程。 它还适用于 AzCopy、 Robocopy 或合作伙伴工具和服务等联机工具。 但是克服初始上传的挑战，请按照本文适用于 Azure 文件同步的方式使用这些工具中的步骤。


## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>使用一种工具来将脱机数据传输的优点
下面是使用传输工具如 Data Box 进行脱机迁移的主要优势：

- 您无需通过网络上载的所有文件。 对于大命名空间，此工具可以节省大量的网络带宽和时间。
- 当你使用 Azure 文件同步时，无论哪种传输工具使用 （Data Box，Azure 导入/导出服务等），实时服务器上载更改后将数据移到 Azure 的文件。
- Azure 文件同步同步文件和文件夹的 Acl，即使脱机批量迁移工具不会传输 Acl。
- 数据框和 Azure 文件同步要求没有停机时间。 当您使用 Data Box 将数据传输到 Azure 时，您有效地使用网络带宽和保留的文件保真度。 也将保留你的命名空间保持最新的上传文件的更改后将数据移到 Azure。

## <a name="prerequisites-for-the-offline-data-transfer"></a>脱机数据传输的先决条件
不应启用迁移之前完成脱机数据传输的服务器上的同步。 要在开始之前，请考虑其他事项包括，如下所示：

- 如果你打算使用 Data Box 进行批量迁移：请查看 [Data Box 的部署先决条件](../../databox/data-box-deploy-ordered.md#prerequisites)。
- 规划最终 Azure 文件同步拓扑：[规划 Azure 文件同步部署](storage-sync-files-planning.md)
- 选择将保存要与其同步的文件共享的 Azure 存储帐户。 确保批量迁移发生在同一存储帐户中的临时暂存共享中。 批量迁移只能通过使用驻留在同一存储帐户中的最终共享和暂存共享启用。
- 仅在创建与服务器位置的新同步关系时，才能使用批量迁移。 无法使用现有同步关系启用批量迁移。


## <a name="process-for-offline-data-transfer"></a>脱机数据传输的过程
下面介绍了如何设置 Azure 文件同步与 Azure Data Box 等的大容量迁移工具兼容的方式：

![关系图显示了如何设置 Azure 文件同步](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 步骤 | 详细信息 |
|---|---------------------------------------------------------------------------------------|
| ![步骤 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [订购 Data Box](../../databox/data-box-deploy-ordered.md)。 Data Box 系列产品/服务[多个产品](https://azure.microsoft.com/services/storage/databox/data)以满足你的需求。 在收到数据框中，请遵循其[文档，以将数据复制](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box)到数据对话框上此 UNC 路径：  *\\< DeviceIPAddres\>\<StorageAccountName_AzFile\>\<ShareName\>*。 在这里， *ShareName*是临时共享的名称。 将 Data Box 发送回 Azure。 |
| ![步骤 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 等待，直到你的文件显示在您选择作为临时过渡共享的 Azure 文件共享中。 *不要启用同步到这些共享。* |
| ![步骤 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | 创建新的空共享的数据框为你创建的每个文件共享。 Data Box 共享同一个存储帐户应为此新共享。 [如何创建新的 Azure 文件共享](storage-how-to-create-file-share.md)。 |
| ![步骤 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [创建同步组](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint)存储同步服务中。 作为云终结点引用的空共享。 对每个 Data Box 文件共享重复此步骤。 [设置 Azure 文件同步](storage-sync-files-deployment-guide.md)。 |
| ![步骤 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [将实时服务器目录添加为服务器终结点](storage-sync-files-deployment-guide.md#create-a-server-endpoint)。 在过程中，指定文件移动到 Azure，并引用临时共享。 可以启用或禁用云分层根据需要。 在实时服务器上创建服务器终结点，来引用该临时共享。 上**添加服务器终结点**边栏选项卡下**脱机数据传输**，选择**已启用**，然后选择必须与在云中相同的存储帐户中的临时共享终结点。 在这里，由存储帐户并不已同步的共享筛选的可用共享列表。 |

![显示如何创建新的服务器终结点时启用脱机数据传输的 Azure 门户用户界面的屏幕截图](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>同步共享
创建服务器终结点后，将启动同步。 同步过程确定是否在服务器上的每个文件也存在于其中 Data Box 存入文件的临时共享。 如果文件不存在同步过程将从临时共享，而无需将其上传从服务器复制文件。 如果文件不存在的临时共享中，或本地服务器上可用的较新版本，同步过程将从本地服务器将文件上传。

> [!IMPORTANT]
> 仅当要创建服务器终结点时，可以启用大容量迁移模式。 建立服务器终结点后，您不能将大容量迁移将数据从已同步服务器集成到命名空间。

## <a name="acls-and-timestamps-on-files-and-folders"></a>文件和文件夹上的 ACL 和时间戳
Azure 文件同步可确保即使在您使用的大容量迁移工具最初未传输 Acl，从实时服务器同步文件和文件夹的 Acl。 正因为如此，暂存共享不需要包含任何文件和文件夹的 Acl。 创建新的服务器终结点启用了脱机数据迁移功能，在服务器上同步所有文件 Acl。 创建新的和修改时间戳还会同步。

## <a name="shape-of-the-namespace"></a>命名空间的形状
启用同步时，服务器的内容确定命名空间的形状。 如果 Data Box 快照和迁移完成后，将从本地服务器中删除文件，这些文件不将移到实时、 可同步命名空间。 它们都保留在暂存共享，但它们不会复制。 这是必需的因为同步将保持活动状态的服务器根据命名空间。 Data Box*快照*是只是临时地高效的文件复制。 它不是实时的命名空间的形状的颁发机构。

## <a name="cleaning-up-after-bulk-migration"></a>批量迁移后进行清理 
当服务器完成它的命名空间的初始同步时，Data Box 批量迁移文件使用暂存文件共享。 上**服务器终结点属性**边栏选项卡在 Azure 门户中，在**脱机数据传输**部分中，状态将更改从**正在进行中**到**已完成**. 

![服务器终结点属性边栏选项卡中的脱机数据传输的状态和禁用控件的位置的屏幕截图](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

现在，你可以清理临时共享以节约成本：

1. 上**服务器终结点属性**边栏选项卡上，当状态为**Completed**，选择**禁用脱机数据传输**。
2. 请考虑删除暂存的共享来节省成本。 暂存共享可能不包含文件和文件夹的 Acl，因此它不是很有用。 用于备份的时间点目的创建真正[同步的 Azure 文件共享的快照](storage-snapshots-files.md)。 你可以[设置 Azure 备份来创建快照]( ../../backup/backup-azure-files.md)按计划。

仅当状态时禁用脱机数据传输模式**已完成**或当你想要取消由于配置不正确。 如果在部署过程中禁用模式，将开始即使暂存共享仍可从服务器上传文件。

> [!IMPORTANT]
> 禁用脱机数据传输模式后，您不能再次启用它，即使在大容量迁移的临时共享仍可用。

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件同步部署](storage-sync-files-planning.md)
- [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
