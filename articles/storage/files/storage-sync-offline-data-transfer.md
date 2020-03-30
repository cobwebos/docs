---
title: 将数据迁移到 Azure 文件与 Azure 数据框同步
description: 以与 Azure 文件同步兼容的方式迁移批量数据。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9398aceeb7465392e82aeaa5760f6c0504f8e33d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159517"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>将批量数据迁移到 Azure 文件与 Azure 数据框同步
您可以通过两种方式将批量数据迁移到 Azure 文件同步：

* **使用 Azure 文件同步上传文件。** 这是最简单的方法。 将文件本地移动到 Windows Server 2012 R2 或更高版本，然后安装 Azure 文件同步代理。 设置同步后，您的文件将从服务器上载。 （我们的客户目前平均上传速度约为每两天 1 TiB。为了确保服务器不会为数据中心使用过多的带宽，您可能需要设置[带宽限制计划](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)。
* **脱机传输文件。** 如果没有足够的带宽，可能无法在合理的时间内将文件上载到 Azure。 挑战在于整个文件集的初始同步。 要克服此挑战，请使用脱机批量迁移工具，如[Azure 数据框系列](https://azure.microsoft.com/services/storage/databox)。 

本文介绍如何以与 Azure 文件同步兼容的方式脱机迁移文件。请按照这些说明操作，以避免文件冲突，并在启用同步后保留文件和文件夹访问控制列表 （ACL） 和时间戳。

## <a name="migration-tools"></a>迁移工具
本文中介绍的过程不仅适用于数据框，也适用于其他脱机迁移工具。 它还适用于阿兹贝贝、Robocopy 等工具，或直接在互联网上工作的合作伙伴工具和服务。 但是，为了克服初始上传难题，请按照本文中的步骤以与 Azure 文件同步兼容的方式使用这些工具。

在某些情况下，在采用 Azure 文件同步之前，需要从一个 Windows 服务器移动到另一个 Windows 服务器。[存储迁移服务](https://aka.ms/storagemigrationservice)（SMS） 可以提供帮助。 无论您是需要迁移到 Azure 文件同步支持的服务器操作系统版本（Windows Server 2012R2 及更高版本），还是由于您为 Azure 文件同步购买了新系统而只需迁移，SMS 具有许多功能和优点，可帮助获取迁移工作进展顺利。

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>使用工具脱机传输数据的好处
以下是使用传输工具（如数据框）进行脱机迁移的主要好处：

- 您不必通过网络上载所有文件。 对于大型命名空间，此工具可以节省大量的网络带宽和时间。
- 使用 Azure 文件同步时，无论您使用哪种传输工具（数据框、Azure 导入/导出服务等），实时服务器仅上载将数据移动到 Azure 后更改的文件。
- Azure 文件同步同步文件和文件夹 ACL，即使脱机批量迁移工具不传输 ACL 也是如此。
- 数据框和 Azure 文件同步不需要停机。 使用数据框将数据传输到 Azure 时，可以有效地使用网络带宽并保留文件保真度。 通过仅上传将数据移动到 Azure 后更改的文件，还可以使命名空间保持最新。

## <a name="prerequisites-for-the-offline-data-transfer"></a>脱机数据传输的先决条件
在完成脱机数据传输之前，不应在要迁移的服务器上启用同步。 开始之前需要考虑的其他事项如下：

- 如果您计划使用数据框进行批量迁移：查看[数据框的部署先决条件](../../databox/data-box-deploy-ordered.md#prerequisites)。
- 规划最终 Azure 文件同步拓扑：[规划 Azure 文件同步部署](storage-sync-files-planning.md)
- 选择将保存要与其同步的文件共享的 Azure 存储帐户。 确保批量迁移发生在同一存储帐户中的临时暂存共享中。 批量迁移只能通过使用驻留在同一存储帐户中的最终共享和暂存共享启用。
- 仅在创建与服务器位置的新同步关系时，才能使用批量迁移。 无法使用现有同步关系启用批量迁移。


## <a name="process-for-offline-data-transfer"></a>脱机数据传输流程
下面了解如何以与批量迁移工具（如 Azure 数据框）兼容的方式设置 Azure 文件同步：

![演示如何设置 Azure 文件同步的图表](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 步骤 | 详细信息 |
|---|---------------------------------------------------------------------------------------|
| ![步骤 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [订购 Data Box](../../databox/data-box-deploy-ordered.md)。 数据盒系列提供[多种产品](https://azure.microsoft.com/services/storage/databox/data)，以满足您的需求。 收到数据框时，请按照其[文档将数据复制到](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box)数据框中的此 UNC 路径*\\\>\<：<设备\>\<ipAddreStorageAccountName_AzFile共享名称\>*。 此处 *，ShareName*是暂存共享的名称。 将 Data Box 发送回 Azure。 |
| ![步骤 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 等待文件显示在您选择的临时暂存共享的 Azure 文件共享中。 *不要启用同步到这些共享。* |
| ![步骤 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | 为数据框为您创建的每个文件共享创建新的空共享。 此新共享应与数据盒共享位于同一存储帐户中。 [如何创建新的 Azure 文件共享](storage-how-to-create-file-share.md)。 |
| ![步骤 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [在存储同步服务中创建同步组](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint)。 将空共享作为云终结点引用。 对每个 Data Box 文件共享重复此步骤。 [设置 Azure 文件同步](storage-sync-files-deployment-guide.md)。 |
| ![步骤 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [将实时服务器目录添加为服务器终结点](storage-sync-files-deployment-guide.md#create-a-server-endpoint)。 在此过程中，指定将文件移动到 Azure，并引用暂存共享。 您可以根据需要启用或禁用云分层。 在实时服务器上创建服务器终结点时，引用暂存共享。 在 **"添加服务器终结点**"边栏选项卡上，在 **"脱机数据传输** **"下**选择 启用 ，然后选择必须位于与云终结点相同的存储帐户中的暂存共享。 在这里，可用共享列表由存储帐户和尚未同步的共享进行筛选。 |

![Azure 门户用户界面的屏幕截图，演示如何在创建新服务器终结点时启用脱机数据传输](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>同步共享
创建服务器终结点后，同步将启动。 同步过程确定服务器上的每个文件是否也存在于数据盒存放文件的暂存共享中。 如果文件存在，同步进程将从暂存共享复制该文件，而不是从服务器上载该文件。 如果文件不存在在暂存共享中，或者如果本地服务器上有较新版本可用，同步过程将从本地服务器上载该文件。

> [!IMPORTANT]
> 只能在创建服务器终结点时启用批量迁移模式。 建立服务器终结点后，无法将来自已同步服务器的大宗迁移数据集成到命名空间中。

## <a name="acls-and-timestamps-on-files-and-folders"></a>文件和文件夹上的 ACL 和时间戳
Azure 文件同步可确保文件和文件夹 ACL 从实时服务器同步，即使您使用的批量迁移工具最初未传输 ACL 也是如此。 因此，暂存共享不需要包含文件和文件夹的任何 ACL。 创建新服务器终结点时启用脱机数据迁移功能时，所有文件 ACL 都在服务器上同步。 新创建和修改的时间戳也会同步。

## <a name="shape-of-the-namespace"></a>命名空间的形状
启用同步时，服务器的内容将确定命名空间的形状。 如果在 Data Box 快照和迁移完成后从本地服务器中删除文件，这些文件不会移动到实时同步命名空间中。 它们保留在暂存共享中，但不会复制它们。 这是必需的，因为同步会根据实时服务器保留命名空间。 数据框*快照*只是高效文件副本的暂存地。 它不是实时命名空间形状的权威。

## <a name="cleaning-up-after-bulk-migration"></a>批量迁移后清理 
当服务器完成命名空间的初始同步时，数据箱大迁移文件将使用暂存文件共享。 在 Azure 门户中的 **"服务器终结点属性**"边栏选项卡上，在 **"脱机数据传输**"部分中，状态从 **"正在进行"** 更改为 **"已完成**"。 

![服务器终结点属性边栏选项卡的屏幕截图，其中状态和禁用脱机数据传输控件的位置](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

现在，您可以清理暂存共享以节省成本：

1. 在 **"服务器终结点属性**"边栏选项卡上，当状态**完成**时，选择**禁用脱机数据传输**。
2. 请考虑删除暂存共享以节省成本。 暂存共享可能不包含文件和文件夹 ACL，因此不太可能有用。 出于备份时间点的目的，请创建同步 Azure[文件共享的真实快照](storage-snapshots-files.md)。 可以[设置 Azure 备份以按计划拍摄快照]( ../../backup/backup-afs.md)。

仅当状态**已完成**或由于配置错误而要取消时，才禁用脱机数据传输模式。 如果在部署期间禁用该模式，文件将开始从服务器上载，即使您的过渡共享仍然可用。

> [!IMPORTANT]
> 禁用脱机数据传输模式后，即使批量迁移的暂存共享仍然可用，也不能再次启用它。

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件同步部署](storage-sync-files-planning.md)
- [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
