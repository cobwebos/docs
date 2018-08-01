---
title: 添加/删除 Azure 文件同步服务器终结点 | Microsoft Docs
description: 了解规划 Azure 文件部署时应考虑的问题。
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.openlocfilehash: c8da3f501d03a05ef5490197d4fd38ada01a4997
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158431"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>添加/删除 Azure 文件同步服务器终结点
借助 Azure 文件同步，既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 它通过将 Windows Server 转换为 Azure 文件共享的快速缓存来实现这一点。 你可以使用 Windows Server 上的任意可用协议在本地访问数据（包括 SMB、NFS 和 FTPS），并且可以在世界各地获取所需的缓存数。

服务器终结点表示已注册服务器上的特定位置，例如服务器卷中的文件夹或服务器卷的根。 如果命名空间不重叠（例如 F:\sync1 和 F:\sync2），多个服务器终结点可存在于同一个卷。 可为每个服务器终结点单独配置云分层策略。 如果将带一组现有文件的服务器位置作为服务器终结点添加到同步组，则这些文件将与同步组中其他终结点上已有的任何其他文件进行合并。

若要了解如何部署端到端的 Azure 文件同步，请参阅[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

## <a name="prerequisites"></a>先决条件
若要创建服务器终结点，必须首先确保满足以下条件： 
- 服务器安装了 Azure 文件同步代理并已注册。 有关安装 Azure 文件同步代理的说明可以在[向 Azure 文件同步注册/注销服务器](storage-sync-files-server-registration.md)一文中找到。 
- 确保已部署存储同步服务。 有关如何部署存储同步服务的详细信息，请参阅[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。 
- 确保已部署同步组。 了解如何[创建同步组](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint)。
- 确保服务器已连接到 Internet，并且 Azure 可以访问。 我们使用端口 443 处理服务器和我们的服务之间的所有通信。

## <a name="add-a-server-endpoint"></a>添加服务器终结点
若要添加服务器终结点，请导航到所需同步组，然后选择“添加服务器终结点”。

![在“同步组”窗格中添加一个新的服务器终结点](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

“添加服务器终结点”下需要以下信息：

- **已注册服务器**：要在其中创建服务器终结点的服务器或群集的名称。
- **路径**：要作为同步组的一部分进行同步的 Windows Server 上的路径。
- 云分层：启用或禁用云分层的开关。 启用时，云分层会将文件分层到 Azure 文件共享。 这会将本地文件共享转换为缓存而不是数据集的完整副本，以帮助你管理服务器上的空间效率。
- **卷可用空间**：要在服务器终结点所在的卷上保留的可用空间量。 例如，如果有一个服务器终结点的卷上的卷可用空间设置为 50%，则约有一半数据会分层为 Azure 文件。 不管是否启用云分层，Azure 文件共享在同步组中始终具有完整的数据副本。

选择“创建”以添加服务器终结点。 同步组命名空间中的文件现在会保持同步。 

## <a name="remove-a-server-endpoint"></a>删除服务器终结点
如果希望停止对给定服务器终结点使用 Azure 文件同步，则可以删除该服务器终结点。 

> [!Warning]  
> 不要尝试通过删除并重新创建服务器终结点来解决同步、云分层或 Azure 文件同步的任何其他方面的问题，除非 Microsoft 工程师明确指示这样做。 删除服务器终结点是一种破坏性操作，并且在重新创建服务器终结点后，服务器终结点中的分层文件将不会“重新连接”到 Azure 文件共享上的其位置，这将导致同步错误。 另请注意，存在于服务器终结点命名空间之外的分层文件可能会永久丢失。 即使从未启用云分层，分层文件也可能存在于服务器终结点中。

若要确保在删除服务器终结点之前召回所有分层文件，请在服务器终结点上禁用云分层，然后执行以下 PowerShell cmdlet 以召回服务器终结点命名空间内的所有分层文件：

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Note]  
> 如果承载服务器的本地卷没有足够可用空间可用于召回所有分层数据，则 `Invoke-StorageSyncFileRecall` cmdlet 会失败。  

删除服务器终结点：

1. 导航到在其中注册服务器的存储同步服务。
2. 导航到所需同步组。
3. 在存储同步服务中删除同步组中所需的服务器终结点。 可以通过在“同步组”窗格中右键单击相关服务器终结点来完成。

    ![从同步组中删除服务器终结点](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>后续步骤
- [向 Azure 文件同步注册/注销服务器](storage-sync-files-server-registration.md)
- [规划 Azure 文件同步部署](storage-sync-files-planning.md)