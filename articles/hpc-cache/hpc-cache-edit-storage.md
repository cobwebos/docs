---
title: 更新 Azure HPC 缓存存储目标
description: 如何编辑 Azure HPC 缓存存储目标
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/02/2020
ms.author: v-erkel
ms.openlocfilehash: f11e12c4f30977514e04b09c7e1c3012eb7888a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092450"
---
# <a name="edit-storage-targets"></a>编辑存储目标

可以从缓存的 "**存储目标**门户" 页或使用 Azure CLI 删除或修改存储目标。

> [!TIP]
> [管理 AZURE HPC 缓存视频](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)演示了如何编辑 Azure 门户中的存储目标。

## <a name="remove-a-storage-target"></a>删除存储目标

### <a name="portal"></a>[门户](#tab/azure-portal)

若要删除存储目标，请在列表中选择它，然后单击 "**删除**" 按钮。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用[az hpc-缓存存储-目标删除](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove)从缓存中删除存储目标。

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

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

在 Azure 门户中，可以通过单击存储目标名称并打开其详细信息页来查看可编辑的字段。 还可以通过 Azure CLI 修改存储目标。

![NFS 存储目标的编辑页的屏幕截图](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>更新 NFS 存储目标

对于 NFS 存储目标，可以更新多个属性。 （请参阅上面的屏幕截图，查看示例 "编辑" 页。）

* **使用情况模型**-使用模型影响缓存如何保留数据。 有关详细信息，[请参阅选择使用情况模型](hpc-cache-add-storage.md#choose-a-usage-model)。
* **虚拟命名空间路径**-客户端用于装载此存储目标的路径。 有关详细信息，请参阅[计划聚合命名空间](hpc-cache-namespace.md)。
* **NFS 导出路径**-用于此命名空间路径的存储系统导出。
* **子目录路径**-要与此命名空间路径关联的子目录（在导出下）。 如果不需要指定子目录，请将此字段留空。

每个命名空间路径都需要导出和子目录的唯一组合。 也就是说，不能向后端存储系统上的完全相同目录提供两个不同的面向客户端的路径。

### <a name="portal"></a>[Portal](#tab/azure-portal)

进行更改后，单击 **"确定"** 以更新存储目标，或单击 "**取消**" 以放弃更改。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用[az nfs-target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target)命令可以更改存储目标的使用模式、虚拟命名空间路径和 nfs 导出或子目录值。

* 若要更改使用模型，请使用 ``--nfs3-usage-model`` 选项。 示例： ``--nfs3-usage-model WRITE_WORKLOAD_15``

* 若要更改命名空间路径、导出或导出子目录，请使用 ``--junction`` 选项。

  ``--junction``参数使用以下值：

  * ``namespace-path``-面向客户端的虚拟文件路径
  * ``nfs-export``-与面向客户端的路径关联的存储系统导出
  * ``target-path``（可选）-导出的子目录（如果需要）

  示例： ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

所有更新命令都需要缓存名称、存储目标名称和资源组。

命令示例： <!-- having problem testing this -->

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
    --name rivernfs0 --resource-group doc-rg0619 \
    --nfs3-usage-model READ_HEAVY_INFREQ
```

如果缓存已停止或未处于正常状态，则在缓存运行正常后，将会应用该更新。

---

## <a name="update-an-azure-blob-storage-target"></a>更新 Azure Blob 存储目标

对于 blob 存储目标，可以修改虚拟命名空间路径。

### <a name="portal"></a>[门户](#tab/azure-portal)

使用 Blob 存储目标的详细信息页可以修改虚拟命名空间路径。

![blob 存储目标的编辑页的屏幕截图](media/hpc-cache-edit-storage-blob.png)

完成后，单击 **"确定"** 以更新存储目标，或单击 "**取消**" 以放弃更改。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用[az hpc-cache blob-存储目标更新](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update)更新目标的命名空间路径。

```azurecli
az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --storage-account "/subscriptions/<subscription_ID>/resourceGroups/erinazcli/providers/Microsoft.Storage/storageAccounts/rg"  \
    --container-name "container-name" --virtual-namespace-path "/new-path"
```

如果缓存已停止或未处于正常状态，则在缓存运行正常后，将会应用该更新。

---

## <a name="next-steps"></a>后续步骤

* 若要了解有关这些选项的详细信息，请参阅[添加存储目标](hpc-cache-add-storage.md)。
* 有关使用虚拟路径的更多提示，请参阅[计划聚合命名空间](hpc-cache-namespace.md)。
