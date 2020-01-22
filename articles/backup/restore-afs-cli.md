---
title: 将 Azure 文件共享还原 Azure CLI
description: 了解如何使用 Azure CLI 在恢复服务保管库中还原已备份的 Azure 文件共享
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 8d91031ff9ee0d4f32cc74ff7031d39ddab3e2b4
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294404"
---
# <a name="restore-azure-file-shares-with-cli"></a>通过 CLI 还原 Azure 文件共享

Azure 命令行接口（CLI）提供了用于管理 Azure 资源的命令行体验。 这是一个很好的工具，用于构建自定义自动化以使用 Azure 资源。 本文介绍如何使用 Azure CLI 从[Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)服务创建的还原点还原整个文件共享或特定文件。 也可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) 或 [Azure 门户](backup-afs.md)执行这些步骤。

本教程结束时，你将了解如何使用 Azure CLI 执行以下操作：

* 查看已备份 Azure 文件共享的还原点
* 还原完整的 Azure 文件共享
* 还原单个文件或文件夹

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本地安装和使用 CLI，必须运行 Azure CLI 2.0.18 或更高版本。 若要查找 CLI 版本，请 `run az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites"></a>必备组件

本文假定你已有一个 azure 备份服务备份的 Azure 文件共享。 如果没有，请参阅[使用 CLI 备份 Azure 文件共享](backup-afs-cli.md)，为文件共享配置备份。 对于本文，我们将使用以下资源：

| 文件共享  | 存储器帐户 | 地区 | 详细信息                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azurefiles*  | *afsaccount*      | EastUS | 使用 Azure 备份备份原始源                 |
| *azurefiles1* | *afaccount1*      | EastUS | 用于备用位置恢复的目标源 |

你可以为文件共享使用类似的结构，尝试使用本文档中所述的不同类型的还原。

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>提取 Azure 文件共享的恢复点

使用[az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) cmdlet 列出已备份文件共享的所有恢复点。

以下示例在*afsaccount*存储帐户中获取*azurefiles*文件共享的恢复点列表。

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

你还可以使用容器和项的 "友好名称" 执行上述 cmdlet，方法是提供以下两个附加参数：

* **--备份管理-类型**： *azurestorage*
* **--工作负荷类型**： *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

结果集将是恢复点的列表，其中包含每个还原点的时间和一致性详细信息。

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

输出中的**name**属性对应于可用作恢复操作中的 **--rp name**参数的值的恢复点名称。

## <a name="full-share-recovery-using-azure-cli"></a>使用 Azure CLI 完全共享恢复

您可以使用此还原选项在原始或备用位置还原完整的文件共享。

定义以下参数以执行还原操作：

* **--container-name**是托管已备份原始文件共享的存储帐户的名称。 若要检索容器的**名称**或**友好名称**，请使用[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--name**是要用于还原操作的已备份原始文件共享的名称。 若要检索已备份项的**名称**或**友好名称**，请使用[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。

### <a name="restore-full-share-to-the-original-location"></a>将完全共享还原到原始位置

还原到原始位置时，无需指定与目标相关的参数。 必须仅提供 "**解决冲突**"。

下面的示例使用 azurefileshare [cmdlet，](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare)将还原模式设置为 " *originallocation* "，以使用在[Azure 文件共享的提取恢复点](#fetch-recovery-points-for-the-azure-file-share)获取的恢复点932883129628959823还原原始位置中的*azurefiles*文件共享：

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

输出中的**name**属性对应于备份服务为 "还原" 操作创建的作业的名称。 若要跟踪作业的状态，请使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet。

### <a name="restore-full-share-to-an-alternate-location"></a>将完全共享还原到备用位置

可以使用此选项将文件共享还原到备用位置，并使原始文件共享保持原样。 为备用位置恢复指定以下参数：

* **--目标存储帐户**：备份的内容要还原到的存储帐户。 目标存储帐户应与保管库位于同一位置。
* **--文件共享**：要将备份内容还原到的目标存储帐户中的文件共享。
* **--folder**：要将数据还原到的文件共享下的文件夹。 如果要将备份内容还原到根文件夹，请将目标文件夹值指定为空字符串。
* **--resolve-冲突**：如果与还原的数据发生冲突，则为指令。 接受“覆盖”或“跳过”。

下面的示例使用[az backup restore azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) with restore mode as *alternatelocation*将*afsaccount*存储帐户中的*azurefiles*文件共享还原到*azurefiles1*存储帐户中的*afaccount1 "* 文件共享。

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

输出中的**name**属性对应于备份服务为 "还原" 操作创建的作业的名称。 若要跟踪作业的状态，请使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet。

## <a name="item-level-recovery"></a>项目级恢复

您可以使用此还原选项还原原始或备用位置中的单个文件或文件夹。

定义以下参数以执行还原操作：

* **--container-name**是托管已备份原始文件共享的存储帐户的名称。 若要检索容器的**名称**或**友好名称**，请使用[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--name**是要用于还原操作的已备份原始文件共享的名称。 若要检索已备份项的**名称**或**友好名称**，请使用[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。

为要恢复的项目指定以下参数：

* **SourceFilePath**：文件共享中要还原的文件的绝对路径（以字符串形式）。 此路径与[az storage file 下载](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download)或[az storage file show](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) CLI 命令中使用的路径相同。
* **SourceFileType**：选择是否选择目录或文件。 接受“目录”或“文件”。
* **ResolveConflict**：如果与还原的数据发生冲突，则为指令。 接受“覆盖”或“跳过”。

### <a name="restore-individual-filesfolders-to-the-original-location"></a>将单个文件/文件夹还原到原始位置

使用[az backup restore azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) cmdlet，并将还原模式设置为*originallocation* ，以将特定文件/文件夹还原到其原始位置。

下面的示例将 *"RestoreTest"* 文件还原到原始位置： " *azurefiles*文件共享"。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

输出中的**name**属性对应于备份服务为 "还原" 操作创建的作业的名称。 若要跟踪作业的状态，请使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet。

### <a name="restore-individual-filesfolders-to-an-alternate-location"></a>将单个文件/文件夹还原到备用位置

若要将特定文件/文件夹还原到备用位置，请使用[az backup restore azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) cmdlet，并将还原模式设置为*alternatelocation* ，并指定下列与目标相关的参数：

* **--目标存储帐户**：备份的内容要还原到的存储帐户。 目标存储帐户应与保管库位于同一位置。
* **--文件共享**：要将备份内容还原到的目标存储帐户中的文件共享。
* **--folder**：要将数据还原到的文件共享下的文件夹。 如果将备份的内容还原到根文件夹，则将目标文件夹的值指定为空字符串。

下面的示例将*azurefiles*文件中最初显示的*RestoreTest*文件还原到另一个位置： *afaccount1*存储帐户中托管的*azurefiles1*文件共享中的*restoredata*文件夹。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

输出中的**name**属性对应于备份服务为 "还原" 操作创建的作业的名称。 若要跟踪作业的状态，请使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet。

## <a name="next-steps"></a>后续步骤

了解如何[通过 Azure CLI 管理 Azure 文件共享备份](manage-afs-backup-cli.md)
