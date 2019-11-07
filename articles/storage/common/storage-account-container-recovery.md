---
title: 存储帐户容器恢复
description: 存储帐户容器恢复
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
ms.openlocfilehash: f095bdfe7bbb5777a2ad2aabb3bda92d0974457d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693574"
---
# <a name="storage-account-container-recovery"></a>存储帐户容器恢复

Azure 存储通过自动化副本提供数据复原能力。 但是，这不会防止应用程序代码或用户有意或无意损坏数据。 在遇到应用程序或用户错误时保持数据保真需要更为先进的技术，如将数据和审核日志复制到辅助存储位置。

## <a name="checking-azure-storage-account-type"></a>正在检查 Azure 存储帐户类型

1. 导航到 [Azure 门户](https://portal.azure.com/)。

2. 找到自己的存储帐户。

3. 在 "**概述**" 部分中，检查**复制**。

   ![映像](media/storage-account-container-recovery/1.png)

4. 如果复制类型为**GRS/GRS**，则可以不保证帐户容器恢复。 对于所有其他复制类型，这是不可能的。

5. 收集以下信息，并向 Microsoft 支持部门提供支持请求。

   * 存储帐户名称：
   * 容器名称：
   * 删除时间：

   下表概述了存储帐户容器恢复的范围，具体取决于复制策略。

   |内容类型|LRS|ZRS|GRS|RA-GRS| 
   |---|---|---|---|---|
   |存储容器|否|否|是|是| 

   * 我们可以尝试还原存储帐户容器，但没有任何保证。 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>不是为了成功进行恢复

* 请不要重新创建具有相同名称的容器。  
* 如果你已经重新创建了容器，则需要先删除该容器，然后才能为恢复提供支持请求。

## <a name="steps-to-prevent-this-in-the-future"></a>防止将来出现此情况的步骤

1. 为了避免将来出现这种情况，最建议使用的功能是[软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)。

2. 我们还建议了[快照](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)功能。
 
## <a name="next-steps"></a>后续步骤

下面是有关此功能的两个示例代码：

  * [在 .NET 中创建和管理 blob 快照](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [通过 PowerShell 使用 blob 快照](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

