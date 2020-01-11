---
title: 更新 Azure HPC 缓存存储目标
description: 如何编辑 Azure HPC 缓存存储目标
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: 5635bfc6ea5faea41b125037c76c0b8635e0f528
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867007"
---
# <a name="edit-storage-targets"></a>编辑存储目标

你可以从缓存的 "**存储目标**" 页中删除或修改存储目标。

## <a name="remove-a-storage-target"></a>删除存储目标

若要删除存储目标，请在列表中选择它，然后单击 "**删除**" 按钮。

此操作将删除与此 Azure HPC 缓存系统的存储目标关联，但不会更改后端存储系统。 例如，如果你使用的是 Azure Blob 存储容器，则在从缓存中删除容器及其内容后仍然存在该容器。 可以将容器添加到其他 Azure HPC 缓存，将其重新添加到此缓存，或将其删除 Azure 门户。

存储在缓存中的任何文件更改都将写入到后端存储系统中，然后再删除存储目标。 如果缓存中有大量更改的数据，此过程可能需要一小时或更长时间。

## <a name="update-storage-targets"></a>更新存储目标

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
