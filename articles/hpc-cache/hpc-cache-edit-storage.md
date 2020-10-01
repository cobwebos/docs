---
title: 更新 Azure HPC 缓存存储目标
description: 如何编辑 Azure HPC 缓存存储目标
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 70f350204796099e02f7afe829a6e2e1fdf653c8
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613081"
---
# <a name="edit-storage-targets"></a>编辑存储目标

您可以使用 Azure 门户或 Azure CLI 来删除或修改存储目标。

根据存储类型，可以修改以下存储目标值：

* 对于 Blob 存储目标，可以更改命名空间路径。

* 对于 NFS 存储目标，可以更改以下值：

  * 命名空间路径
  * 与命名空间路径关联的存储导出或导出子目录
  * 使用模型

不能 (Blob 容器或 NFS 主机名/IP 地址) 编辑存储目标的名称、类型或后端存储系统。 如果需要更改这些属性，请删除存储目标，并使用新值创建替换。

> [!TIP]
> [管理 AZURE HPC 缓存视频](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)演示了如何编辑 Azure 门户中的存储目标。

## <a name="remove-a-storage-target"></a>删除存储目标

### <a name="portal"></a>[门户](#tab/azure-portal)

若要删除存储目标，请打开 " **存储目标** " 页。 从列表中选择存储目标，然后单击 " **删除** " 按钮。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用 [az hpc-缓存存储-目标删除](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) 从缓存中删除存储目标。

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

删除存储目标将删除存储系统与此 Azure HPC 缓存系统的关联，但不会更改后端存储系统。 例如，如果你使用的是 Azure Blob 存储容器，则在从缓存中删除容器及其内容后仍然存在该容器。 可以将容器添加到其他 Azure HPC 缓存，将其重新添加到此缓存，或将其删除 Azure 门户。

存储在缓存中的任何文件更改都将写入到后端存储系统中，然后再删除存储目标。 如果缓存中有大量更改的数据，此过程可能需要一小时或更长时间。

## <a name="change-a-blob-storage-targets-namespace-path"></a>更改 blob 存储目标的命名空间路径

命名空间路径是客户端用于装载此存储目标的路径。  (若要了解详细信息，请参阅 [规划聚合命名空间](hpc-cache-namespace.md) 并 [设置聚合命名空间](add-namespace-paths.md)) 。

命名空间路径是你可以在 Azure Blob 存储目标上进行的唯一更新。 使用 Azure 门户或 Azure CLI 来更改它。

### <a name="portal"></a>[门户](#tab/azure-portal)

使用 Azure HPC 缓存的 " **命名空间** " 页。 [设置聚合命名空间一](add-namespace-paths.md)文中更详细地介绍了 "命名空间" 页。

单击要更改的路径名称，然后在显示的编辑窗口中创建新路径。

!["命名空间" 页的屏幕截图单击 Blob 命名空间路径后，编辑字段会显示在右侧的窗格中](media/edit-namespace-blob.png)

进行更改后，单击 **"确定"** 以更新存储目标，或单击 " **取消** " 以放弃更改。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

若要使用 Azure CLI 更改 blob 存储目标的命名空间，请使用命令 " [az hpc-缓存 blob-存储-目标更新](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update)"。 只能 `--virtual-namespace-path` 更改该值。

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>更新 NFS 存储目标

对于 NFS 存储目标，可以更改或添加虚拟命名空间路径，更改命名空间路径所指向的 NFS 导出或子目录值，并更改使用模型。

详细信息如下：

*  (虚拟命名空间路径、导出和导出子目录[更改聚合的命名空间值](#change-aggregated-namespace-values)) 
* [更改使用模式](#change-the-usage-model)

### <a name="change-aggregated-namespace-values"></a>更改聚合的命名空间值

如果使用) ，则可以使用 Azure 门户或 Azure CLI 更改面向客户端的命名空间路径、存储导出和导出子目录 (。

如果需要提醒如何在一个存储目标上创建多个有效路径，请阅读 [添加 NFS 命名空间路径](add-namespace-paths.md#nfs-namespace-paths) 中的准则。

### <a name="portal"></a>[门户](#tab/azure-portal)

使用 Azure HPC 缓存的 " **命名空间** " 页可以更新命名空间值。 有关详细信息，请参阅 [设置聚合命名空间一](add-namespace-paths.md)文。

![在右侧打开 "NFS 更新" 页的 "门户命名空间" 页的屏幕截图](media/update-namespace-nfs.png)

1. 单击要更改的路径的名称。
1. 使用 "编辑" 窗口键入新的虚拟路径、导出或子目录值。
1. 进行更改后，单击 **"确定"** 以更新存储目标，或单击 " **取消** " 以放弃更改。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用 ``--junction`` [az hpc-cache nfs 存储更新](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) 命令中的选项来更改命名空间路径、nfs 导出或导出子目录。

``--junction``参数使用以下值：

* ``namespace-path`` -面向客户端的虚拟文件路径
* ``nfs-export`` -与面向客户端的路径关联的存储系统导出
* ``target-path`` (可选) -导出的子目录（如果需要）

示例：``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

您必须为语句中的每个路径提供所有三个值 ``--junction`` 。 对于不想更改的任何值，请使用现有值。

所有更新命令中也需要缓存名称、存储目标名称和资源组。

示例命令：

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>更改使用模式

使用模型影响缓存如何保留数据。 有关详细信息， [请参阅选择使用情况模型](hpc-cache-add-storage.md#choose-a-usage-model) 。

若要更改 NFS 存储目标的使用模式，请使用这些方法之一。

### <a name="portal"></a>[门户](#tab/azure-portal)

在 Azure 门户的 " **存储目标** " 页中更改使用情况模型。 单击要更改的存储目标的名称。

![NFS 存储目标的编辑页的屏幕截图](media/edit-storage-nfs.png)

使用下拉选择器选择新的使用模型。 单击 **"确定"** 以更新存储目标，或单击 " **取消** " 以放弃更改。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用 [az hpc-cache nfs 存储更新](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target?view=azure-cli-latest#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) 命令。

Update 命令与用于添加 NFS 存储目标的命令几乎完全相同。 有关详细信息和示例，请参阅 [创建 NFS 存储目标](hpc-cache-add-storage.md#create-an-nfs-storage-target) 。

若要更改使用模型，请更新 ``--nfs3-usage-model`` 选项。 示例：``--nfs3-usage-model WRITE_WORKLOAD_15``

还需要缓存名称、存储目标名称和资源组值。

如果要验证使用模型的名称，请使用命令 " [az hpc-缓存使用情况-模型列表](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list)"。

如果缓存已停止或未处于正常状态，则在缓存运行正常后，将会应用该更新。

---

## <a name="next-steps"></a>后续步骤

* 若要了解有关这些选项的详细信息，请参阅 [添加存储目标](hpc-cache-add-storage.md) 。
* 有关使用虚拟路径的更多提示，请参阅 [计划聚合命名空间](hpc-cache-namespace.md) 。
