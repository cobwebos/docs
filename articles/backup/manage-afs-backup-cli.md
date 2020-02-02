---
title: 用 Azure CLI 管理 Azure 文件共享备份
description: 了解如何使用 Azure CLI 管理和监视 Azure 备份备份的 Azure 文件共享。
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 44a49913abd99b285397b8b78ad9d4c0f9df52ea
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934877"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>用 Azure CLI 管理 Azure 文件共享备份

Azure CLI 提供了用于管理 Azure 资源的命令行体验。 这是一个很好的工具，用于构建自定义自动化以使用 Azure 资源。 本文介绍如何执行管理和监视[Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)所备份的 azure 文件共享的任务。 还可以通过[Azure 门户](https://portal.azure.com/)执行这些步骤。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本地安装和使用 CLI，必须运行 Azure CLI 2.0.18 或更高版本。 若要查找 CLI 版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites"></a>必备组件

本文假设已有一个 azure 文件共享由[Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)进行备份。 如果没有，请参阅[使用 CLI 备份 Azure 文件共享](backup-afs-cli.md)，为文件共享配置备份。 本文将使用以下资源：

* **资源组**： *azurefiles*
* **RecoveryServicesVault**： *azurefilesvault*
* **存储帐户**： *afsaccount*
* **文件共享**： *azurefiles*

## <a name="monitor-jobs"></a>监视作业

触发备份或还原操作时，备份服务会创建一个作业用于跟踪。 若要监视已完成或当前正在运行的作业，请使用[az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet。 使用 CLI，还可以[挂起当前正在运行的作业](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop)，或[等待作业完成](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)。

以下示例显示*azurefilesvault*恢复服务保管库的备份作业的状态：

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>修改策略

你可以使用[az backup item set-policy](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy)修改备份策略以更改备份频率或保持期。

若要更改策略，请定义以下参数：

* **--container-name**：承载文件共享的存储帐户的名称。 若要检索容器的**名称**或**友好名称**，请使用[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--name**：要更改策略的文件共享的名称。 若要检索已备份项的**名称**或**友好名称**，请使用[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。
* **--policy-name**：要为文件共享设置的备份策略的名称。 你可以使用[az backup policy list](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list)查看保管库的所有策略。

以下示例为*afsaccount*存储帐户中存在的*azurefiles*文件共享设置*schedule2*备份策略。

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

还可以通过使用容器和项的友好名称来运行上一个命令，方法是提供以下两个附加参数：

* **--备份管理-类型**： *azurestorage*
* **--工作负荷类型**： *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

输出中的**name**属性对应于更改策略操作的备份服务创建的作业名称。 若要跟踪作业的状态，请使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet。

## <a name="stop-protection-on-a-file-share"></a>停止对文件共享的保护

可以通过两种方法来停止保护 Azure 文件共享：

* 停止所有将来的备份作业并*删除*所有恢复点。
* 停止所有将来的备份作业，但*保留*恢复点。

由于 Azure 备份创建的底层快照将被保留，因此可能会产生与保留存储中的恢复点相关的成本。 保留恢复点的好处是稍后在需要时还原文件共享的选项。 有关保留恢复点的成本的信息，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/storage/files)。 如果你选择删除所有恢复点，则无法还原文件共享。

若要停止对文件共享的保护，请定义以下参数：

* **--container-name**：承载文件共享的存储帐户的名称。 若要检索容器的**名称**或**友好名称**，请使用[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--项-name**：要停止保护的文件共享的名称。 若要检索已备份项的**名称**或**友好名称**，请使用[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。

### <a name="stop-protection-and-retain-recovery-points"></a>停止保护并保留恢复点

若要在保留数据的同时停止保护，请使用[az backup protection disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet。

下面的示例将停止对*azurefiles*文件共享的保护，但会保留所有恢复点。

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

还可以通过使用容器的友好名称和项来运行上一个命令，方法是提供以下两个附加参数：

* **--备份管理-类型**： *azurestorage*
* **--工作负荷类型**： *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

输出中的 "**名称**" 属性对应于 "停止保护" 操作的备份服务创建的作业名称。 若要跟踪作业的状态，请使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet。

### <a name="stop-protection-without-retaining-recovery-points"></a>停止保护而不保留恢复点

若要停止保护而不保留恢复点，请在 "**删除-备份-数据**" 选项设置为**true**的情况下使用[az backup protection disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet。

以下示例停止对*azurefiles*文件共享的保护，而不保留恢复点。

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

还可以通过使用容器的友好名称和项来运行上一个命令，方法是提供以下两个附加参数：

* **--备份管理-类型**： *azurestorage*
* **--工作负荷类型**： *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>恢复对文件共享的保护

如果已停止对 Azure 文件共享的保护，但保留了恢复点，则可以在以后继续保护。 如果不保留恢复点，则无法继续保护。

若要恢复对文件共享的保护，请定义以下参数：

* **--container-name**：承载文件共享的存储帐户的名称。 若要检索容器的**名称**或**友好名称**，请使用[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--项-name**：要恢复保护的文件共享的名称。 若要检索已备份项的**名称**或**友好名称**，请使用[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。
* **--policy-name**：要为其恢复对文件共享的保护的备份策略的名称。

下面的示例使用[az backup protection resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) cmdlet 通过*schedule1*备份策略恢复对*azurefiles*文件共享的保护。

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

还可以通过使用容器的友好名称和项来运行上一个命令，方法是提供以下两个附加参数：

* **--备份管理-类型**： *azurestorage*
* **--工作负荷类型**： *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

输出中的**name**属性对应于备份服务为恢复保护操作创建的作业的名称。 若要跟踪作业的状态，请使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet。

## <a name="unregister-a-storage-account"></a>取消注册存储帐户

如果要使用不同的恢复服务保管库来保护特定存储帐户中的文件共享，请先停止保护该存储帐户中的[所有文件共享](#stop-protection-on-a-file-share)。 然后从当前用于保护的恢复服务保管库中取消注册该帐户。

需要提供容器名称来注销存储帐户。 若要检索容器的**名称**或**友好名称**，请使用[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。

下面的示例使用[az backup container](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister) afsaccount cmdlet 从*azurefilesvault*中取消注册 " " 存储帐户。

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

还可以通过提供以下附加参数，使用容器的友好名称运行以前的 cmdlet：

* **--备份管理-类型**： *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[排查 Azure 文件共享备份问题](troubleshoot-azure-files.md)。
