---
title: "添加/删除 Azure 文件同步（预览版）服务器终结点 | Microsoft Docs"
description: "了解规划 Azure 文件部署时应考虑的问题。"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 92ac80953623a5a94d3104f30787c9636308c707
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="addremove-an-azure-file-sync-preview-server-endpoint"></a>添加/删除 Azure 文件同步（预览版）服务器终结点
借助 Azure 文件同步（预览版），既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 它通过将 Windows Server 转换为 Azure 文件共享的快速缓存来实现这一点。 你可以使用 Windows Server 上的任意可用协议在本地访问数据（包括 SMB、NFS 和 FTPS），并且可以在世界各地获取所需的缓存数。

服务器终结点代表已注册服务器上的特定位置，例如服务器卷中的文件夹或服务器卷的根。 如果命名空间不重叠（例如 F:\sync1 和 F:\sync2），多个服务器终结点可存在于同一个卷。 可为每个服务器终结点单独配置云分层策略。 如果将带一组现有文件的服务器位置作为服务器终结点添加到同步组，则这些文件将与同步组中其他终结点上已有的任何其他文件进行合并。

若要了解如何部署端到端的 Azure 文件同步，请参阅[如何部署 Azure 文件同步（预览版）](storage-sync-files-deployment-guide.md)。

## <a name="prerequisites"></a>先决条件
若要创建服务器终结点，必须首先确保满足以下条件： 
- 服务器安装了 Azure 文件同步代理并已注册。 有关安装 Azure 文件同步代理的说明可以在[向 Azure 文件同步（预览版）注册/注销服务器](storage-sync-files-server-registration.md)一文中找到。 
- 确保已部署存储同步服务。 有关如何部署存储同步服务的详细信息，请参阅[如何部署 Azure 文件同步（预览版）](storage-sync-files-deployment-guide.md)。 
- 确保已部署同步组。 了解如何[创建同步组](storage-sync-files-deployment-guide.md#create-a-sync-group)。
- 确保服务器已连接到 Internet，并且 Azure 可以访问。

## <a name="add-a-server-endpoint"></a>添加服务器终结点
若要添加服务器终结点，请导航到所需同步组，然后选择“添加服务器终结点”。

![在“同步组”窗格中添加一个新的服务器终结点](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

“添加服务器终结点”下需要以下信息：

- **已注册服务器**：要在其中创建服务器终结点的服务器或群集的名称。
- **路径**：要作为同步组的一部分进行同步的 Windows Server 上的路径。
- **云分层**：用于启用或禁用云分层的开关，它使很少使用或访问的文件能够分层为 Azure 文件。
- **卷可用空间**：要在服务器终结点所在的卷上保留的可用空间量。 例如，如果有一个服务器终结点的卷上的卷可用空间设置为 50%，则约有一半数据会分层为 Azure 文件。 不管是否启用云分层，Azure 文件共享在同步组中始终具有完整的数据副本。

选择“创建”以添加服务器终结点。 同步组命名空间中的文件现在会保持同步。 

## <a name="remove-a-server-endpoint"></a>删除服务器终结点
为服务器终结点启用时，云分层会将文件分层到 Azure 文件共享。 这使本地文件共享可以充当缓存（而不是数据集的完整副本），以便高效使用文件服务器上的空间。 但是，如果在分层文件仍处于服务器本地的情况下删除服务器终结点，则这些文件会不可访问。 因此，如果需要继续进行文件访问，则必须从 Azure 文件召回所有分层文件，然后再继续进行注销。 

这可以使用 PowerShell cmdlet 完成，如下所示：

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> 如果承载服务器的本地卷没有足够可用空间可用于召回所有分层数据，则 `Invoke-StorageSyncFileRecall` cmdlet 会失败。  

删除服务器终结点：

1. 导航到在其中注册服务器的存储同步服务。
2. 导航到所需同步组。
3. 在存储同步服务中删除同步组中所需的服务器终结点。 这可以通过在“同步组”窗格中右键单击相关服务器终结点来完成。

    ![从同步组中删除服务器终结点](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>后续步骤
- [向 Azure 文件同步（预览版）注册/注销服务器](storage-sync-files-server-registration.md)
- [规划 Azure 文件同步部署](storage-sync-files-planning.md)