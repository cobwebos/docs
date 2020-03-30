---
title: 存储帐户容器恢复
description: 存储帐户容器恢复
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562269"
---
# <a name="storage-account-container-recovery"></a>存储帐户容器恢复

Azure 存储通过自动化副本提供数据复原能力。 但是，这不会防止应用程序代码或用户有意或无意损坏数据。 在遇到应用程序或用户错误时保持数据保真需要更为先进的技术，如将数据和审核日志复制到辅助存储位置。

## <a name="checking-azure-storage-account-type"></a>检查 Azure 存储帐户类型

1. 导航到[Azure 门户](https://portal.azure.com/)。

2. 找到自己的存储帐户。

3. 在“概述”**** 部分中，检查“复制”****。

   ![图像](media/storage-account-container-recovery/1.png)

4. 如果复制类型为 **GRS/RA-GRS**，那么可能可以恢复帐户容器。 对于其他所有复制类型，则不可能恢复帐户容器。

5. 收集以下信息，并向 Microsoft 支持部门提出支持请求。

   * 存储帐户名称：
   * 容器名称：
   * 删除时间：

   下表根据复制策略概述了存储帐户容器恢复的范围。

   |内容类型|LRS|ZRS|GRS|RA - GRS| 
   |---|---|---|---|---|
   |存储容器|否|否|是|是| 

   * 我们可以尝试还原存储帐户容器，但没有任何保证。 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>为了恢复成功不要做的事情

* 请不要重新创建容器（使用相同名称）。  
* 如果已经重新创建了容器，则需要在提交恢复支持请求之前删除该容器。

## <a name="steps-to-prevent-this-in-the-future"></a>防止将来出现此情况的步骤

1. 为避免将来出现此情况，最建议使用的功能是[软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)。

2. 我们还建议[快照](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)功能。
 
## <a name="next-steps"></a>后续步骤

下面是有关此功能的两个代码示例：

  * [在 .NET 中创建和管理 blob 快照](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [通过 PowerShell 使用 blob 快照](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

