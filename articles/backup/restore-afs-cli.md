---
title: 与 Azure CLI 还原 Azure 文件共享
description: 了解如何使用 Azure CLI 在恢复服务保管库中还原备份的 Azure 文件共享
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 63b2be2fe24c1274ed1581b7b849de578c978842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76931037"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>与 Azure CLI 还原 Azure 文件共享

Azure CLI 提供了用于管理 Azure 资源的命令行体验。 它是构建自定义自动化以使用 Azure 资源的绝佳工具。 本文介绍如何使用 Azure CLI 从[Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)创建的还原点还原整个文件共享或特定文件。 也可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) 或 [Azure 门户](backup-afs.md)执行这些步骤。

在本文结束时，您将了解如何使用 Azure CLI 执行以下操作：

* 查看备份的 Azure 文件共享的还原点。
* 还原完整的 Azure 文件共享。
* 还原单个文件或文件夹。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本地安装和使用 CLI，必须运行 Azure CLI 2.0.18 或更高版本。 若要查找 CLI 版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites"></a>先决条件

本文假定您已经拥有由 Azure 备份备份的 Azure 文件共享。 如果没有，请参阅[使用 CLI 备份 Azure 文件共享](backup-afs-cli.md)，以配置文件共享的备份。 在本文中，您可以使用以下资源：

| 文件共享  | 存储帐户 | 区域 | 详细信息                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azure 文件*  | *阿夫斯帐户*      | EastUS | 使用 Azure 备份备份的原始源                 |
| *azure 文件1* | *帐户1*      | EastUS | 用于备用位置恢复的目标源 |

您可以为文件共享使用类似的结构来尝试本文中介绍的不同类型的还原。

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>获取 Azure 文件共享的恢复点

使用[az 备份恢复点列表](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list)cmdlet 列出备份文件共享的所有恢复点。

下面的示例获取*afs 帐户*存储帐户中*azure 文件*共享的恢复点列表。

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

还可以通过使用容器和项的友好名称运行以前的 cmdlet，提供以下两个附加参数：

* **--备份管理类型** *：azure 存储*
* **--工作负载类型** *：azure 文件共享*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

结果集是具有每个还原点时间和一致性详细信息的恢复点的列表。

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

输出中的**Name**属性对应于恢复点名称，该名称可用作恢复操作中 **--rp-name**参数的值。

## <a name="full-share-recovery-by-using-the-azure-cli"></a>使用 Azure CLI 完全恢复共享

可以使用此还原选项在原始位置或备用位置还原完整的文件共享。

定义以下参数以执行还原操作：

* **--容器名称**：承载备份的原始文件共享的存储帐户的名称。 若要检索容器的名称或友好名称，请使用[az 备份容器列表](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--项名称**：要用于还原操作的备份原始文件共享的名称。 若要检索备份项的名称或友好名称，请使用[az 备份项列表](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。

### <a name="restore-a-full-share-to-the-original-location"></a>将完整共享还原到原始位置

还原到原始位置时，不需要指定与目标相关的参数。 只能提供 **"解决冲突**"。

下面的示例使用[az 备份还原还原-azure 文件共享](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare)cmdlet，还原模式设置为*原始位置*，以还原原始位置中的*azure 文件*共享。 您可以使用恢复点 932883129628959823，在[Azure 文件共享的 Fetch 恢复点](#fetch-recovery-points-for-the-azure-file-share)中获取该恢复点：

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

输出中的**Name**属性对应于还原操作的备份服务创建的作业的名称。 要跟踪作业的状态，请使用[az 备份作业显示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)cmdlet。

### <a name="restore-a-full-share-to-an-alternate-location"></a>将完整共享还原到备用位置

可以使用此选项将文件共享还原到备用位置，并保持原始文件共享原样。 为备用位置恢复指定以下参数：

* **--目标存储帐户**：还原备份内容的存储帐户。 目标存储帐户应与保管库位于同一位置。
* **--目标文件共享**：还原备份内容的目标存储帐户中的文件共享。
* **--目标文件夹**：还原数据的文件共享下的文件夹。 如果要将备份内容还原到根文件夹，请将目标文件夹值指定为空字符串。
* **--解决冲突**：如果与还原的数据发生冲突，则指示。 接受“覆盖”或“跳过”********。

下面的示例使用[az 备份还原还原-azure 文件共享](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare)与还原模式作为*备用位置*，以将*afs 帐户*存储帐户中的*azure 文件*共享还原到 afaccount1 存储帐户中的*azurefile1"* 文件共享。 *afaccount1*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

输出中的**Name**属性对应于还原操作的备份服务创建的作业的名称。 要跟踪作业的状态，请使用[az 备份作业显示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)cmdlet。

## <a name="item-level-recovery"></a>项目级恢复

您可以使用此还原选项还原原始位置或备用位置中的单个文件或文件夹。

定义以下参数以执行还原操作：

* **--容器名称**：承载备份的原始文件共享的存储帐户的名称。 若要检索容器的名称或友好名称，请使用[az 备份容器列表](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--项名称**：要用于还原操作的备份原始文件共享的名称。 若要检索备份项的名称或友好名称，请使用[az 备份项列表](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。

为要恢复的项指定以下参数：

* **SourceFilePath**：文件的绝对路径，将在文件共享中作为字符串还原。 此路径与[az 存储文件下载](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download)中使用的路径相同，[或 az 存储文件显示](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show)CLI 命令。
* **源文件类型**：选择是选择目录还是文件。 接受“目录”或“文件”********。
* **解决冲突**：如果与还原的数据发生冲突，则说明。 接受“覆盖”或“跳过”********。

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>将单个文件或文件夹还原到原始位置

使用[az 备份还原还原-azure 文件](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles)cmdlet，还原模式设置为*原始位置*，将特定文件或文件夹还原到其原始位置。

以下示例还原*还原还原测试.txt*文件的原始位置 *：azurefile*文件共享。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

输出中的**Name**属性对应于还原操作的备份服务创建的作业的名称。 要跟踪作业的状态，请使用[az 备份作业显示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)cmdlet。

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>将单个文件或文件夹还原到备用位置

要将特定文件或文件夹还原到备用位置，请使用[az 备份还原还原-azure 文件](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles)cmdlet，还原模式设置为*备用位置*，并指定以下目标相关参数：

* **--目标存储帐户**：还原备份内容的存储帐户。 目标存储帐户应与保管库位于同一位置。
* **--目标文件共享**：还原备份内容的目标存储帐户中的文件共享。
* **--目标文件夹**：还原数据的文件共享下的文件夹。 如果要将备份的内容还原到根文件夹，请将目标文件夹的值作为空字符串。

以下示例将最初存在于*azure 文件*共享中的*还原测试.txt*文件还原到备用位置：在*afaccount1*存储帐户中托管的*azurefile1*文件共享中的*还原数据*文件夹。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

输出中的**Name**属性对应于还原操作的备份服务创建的作业的名称。 要跟踪作业的状态，请使用[az 备份作业显示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)cmdlet。

## <a name="next-steps"></a>后续步骤

了解如何使用[Azure CLI 管理 Azure 文件共享备份](manage-afs-backup-cli.md)。
