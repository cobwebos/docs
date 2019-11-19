---
title: 更新 Azure HPC 缓存存储目标
description: 如何编辑 Azure HPC 缓存存储目标
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rohogue
ms.openlocfilehash: 115e75c0149a35104d9c3696710bf8231a98743d
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168517"
---
# <a name="edit-storage-targets"></a>编辑存储目标

可以编辑存储目标以修改其部分属性。 不同类型的存储可编辑不同的属性：

* 对于 Blob 存储目标，可以更改命名空间路径。

* 对于 NFS 存储目标，可以更改以下属性：

  * 命名空间路径
  * 使用模型
  * 导出
  * 导出子目录

不能编辑存储目标的名称、类型或后端存储系统（Blob 容器或 NFS 主机名/IP 地址）。 如果需要更改这些属性，请删除存储目标，并使用新值创建替换。

若要修改存储目标，请单击存储目标名称以打开其详细信息页。 页面中的某些字段是可编辑的。

![NFS 存储目标的编辑页的屏幕截图](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>更新 NFS 存储目标

对于 NFS 存储目标，可以更新多个属性。 （请参阅上面的屏幕截图，查看示例 "编辑" 页。）

* **使用情况模型**-使用模型影响缓存如何保留数据。 有关详细信息，[请参阅选择使用情况模型](hpc-cache-add-storage.md#choose-a-usage-model)。
* **虚拟命名空间路径**-客户端用于装载此存储目标的路径。 有关详细信息，请参阅[计划聚合命名空间](hpc-cache-namespace.md)。
* **NFS 导出路径**-用于此命名空间路径的存储系统导出。
* **子目录路径**-要与此命名空间路径关联的子目录（在导出下）。 如果不需要指定子目录，请将此字段留空。

每个命名空间路径都需要导出和子目录的唯一组合。 也就是说，不能向后端存储系统上的完全相同目录提供两个不同的面向客户端的路径。

进行更改后，单击 **"确定"** 以更新存储目标，或单击 "**取消**" 以放弃更改。

## <a name="update-an-azure-blob-storage-target"></a>更新 Azure Blob 存储目标

使用 Blob 存储目标的详细信息页可以修改虚拟命名空间路径。

![blob 存储目标的编辑页的屏幕截图](media/hpc-cache-edit-storage-blob.png)

完成后，单击 **"确定"** 以更新存储目标，或单击 "**取消**" 以放弃更改。

## <a name="next-steps"></a>后续步骤

* 若要了解有关这些选项的详细信息，请参阅[添加存储目标](hpc-cache-add-storage.md)。
* 有关使用虚拟路径的更多提示，请参阅[计划聚合命名空间](hpc-cache-namespace.md)。
