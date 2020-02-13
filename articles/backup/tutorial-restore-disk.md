---
title: 教程 - 使用 Azure 备份还原 VM 磁盘
description: 了解如何在 Azure 中使用备份和恢复服务还原磁盘并创建恢复的 VM。
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 8a66cee7e844f0049f2d2ca2f6841943aa267f3e
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114188"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>在 Azure 中还原磁盘并创建恢复的 VM

Azure 备份可创建恢复点，这些恢复点存储在异地冗余的恢复保管库中。 从恢复点还原时，可以还原整个 VM，也可以还原单个文件。 本文介绍如何使用 CLI 还原完整的 VM。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
>
> * 列出和选择恢复点
> * 从恢复点还原磁盘
> * 从还原的磁盘创建 VM

有关使用 PowerShell 还原磁盘并创建已恢复的 VM 的信息，请参阅[使用 PowerShell 备份和还原 Azure VM](backup-azure-vms-automation.md#restore-an-azure-vm)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程需要你运行 Azure CLI 2.0.18 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>必备条件

本教程需要使用 Azure 备份所保护的 Linux VM。 若要模拟意外的 VM 删除和恢复过程，请从恢复点中的磁盘创建 VM。 如果需要使用 Azure 备份所保护的 Linux VM，请参阅[在 Azure 中使用 CLI 备份虚拟机](quick-backup-vm-cli.md)。

## <a name="backup-overview"></a>备份概述

当 Azure 启动备份时，VM 上的备份扩展将获取时点快照。 请求第一个备份时，将在 VM 上安装备份扩展。 如果进行备份时 VM 未运行，则 Azure 备份可能还需要获取基础存储的快照。

默认情况下，Azure 备份采用文件系统一致的备份。 Azure 备份获取快照后，数据将传输到恢复服务保管库。 为最大限度地提高效率，Azure 备份仅标识和传输自上次备份以后已更改的数据块。

数据传输完成后，会删除快照并创建恢复点。

## <a name="list-available-recovery-points"></a>列出可用的恢复点

若要还原磁盘，请选择恢复点作为恢复数据的源。 由于默认策略每天创建一个恢复点并保留 30 天，因此，可以保留一组恢复点，以便可以选择一个特定的时点用于恢复。

若要查看可用恢复点的列表，请使用 [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) 命令。 将使用恢复点名称  恢复磁盘。 在本教程中，我们希望最近的恢复点可用。 `--query [0].name` 参数可选择最近的恢复点名称，如下所示：

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>还原 VM 磁盘

> [!IMPORTANT]
> 强烈建议使用 Az CLI version 2.0.74 或更高版本，以享受快速还原的所有益处，包括托管磁盘还原。 用户最好始终使用最新版本。

### <a name="managed-disk-restore"></a>托管磁盘还原

如果备份 VM 具有托管磁盘，并且其目的是从恢复点还原托管磁盘，则首先需提供 Azure 存储帐户。 此存储帐户用于存储 VM 配置和部署模板，这两者稍后可用于从还原的磁盘部署 VM。 然后，还为要还原复到的托管磁盘提供一个目标资源组。

1. 若要创建存储帐户，请使用 [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) 命令。 存储帐户名称必须全部为小写，且全局唯一。 将 mystorageaccount  替换为你自己唯一的名称：

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. 使用 [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks) 从你的恢复点还原磁盘。 将 mystorageaccount  替换为你在前一个命令中创建的存储帐户的名称。 将 myRecoveryPointName 替换为你在前面的 [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) 命令输出中获得的恢复点名称  ： 还需提供将托管磁盘还原到的目标资源组。

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --target-resource-group targetRG
    ```

> [!WARNING]
> 如果未提供目标资源组，则托管磁盘将作为非托管磁盘还原到给定的存储帐户。 这会对还原时间产生重大影响，因为还原磁盘所需的时间完全取决于给定的存储帐户。

### <a name="unmanaged-disks-restore"></a>非托管磁盘还原

如果备份 VM 具有非托管磁盘，并且其目的是从恢复点还原磁盘，则首先需提供 Azure 存储帐户。 此存储帐户用于存储 VM 配置和部署模板，这两者稍后可用于从还原的磁盘部署 VM。 默认情况下，非托管磁盘将还原到其原始存储帐户。 如果用户希望将所有非托管磁盘还原到同一个位置，则还可以将给定的存储帐户也用作这些磁盘的暂存位置。

在其他步骤中，将使用还原的磁盘创建 VM。

1. 若要创建存储帐户，请使用 [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) 命令。 存储帐户名称必须全部为小写，且全局唯一。 将 mystorageaccount  替换为你自己唯一的名称：

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. 使用 [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks) 从你的恢复点还原磁盘。 将 mystorageaccount  替换为你在前一个命令中创建的存储帐户的名称。 将 myRecoveryPointName  替换为你在前面的 [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) 命令输出中获得的恢复点名称：

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

如上所述，非托管磁盘将还原到其原始存储帐户。 这可提供最佳的还原性能。 但是，如果所有非托管磁盘都需要还原到给定的存储帐户，请使用相关标志，如下所示。

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

输出类似于以下示例，该示例显示还原作业处于“正在进行”  状态：

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

如果还原作业的“状态”报告为“完成”，则系统已将必要的信息（VM 配置和部署模板）还原到存储帐户   。

## <a name="create-a-vm-from-the-restored-disk"></a>从还原的磁盘创建 VM

最后一步是从还原的磁盘创建 VM。 可以使用下载到给定存储帐户的部署模板来创建 VM。

### <a name="fetch-the-job-details"></a>提取作业详细信息

生成的作业详细信息提供可以查询和部署的模板 URI。 使用 job show 命令可获取触发的已还原作业的更多详细信息。

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

此查询的输出将提供所有详细信息，但我们只对存储帐户内容感兴趣。 可以使用 Azure CLI 的[查询功能](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)提取相关详细信息

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>提取部署模板

模板不能直接访问，因为它在客户的存储帐户和给定容器下。 需要完整的 URL（以及临时 SAS 令牌）才能访问此模板。

首先，从作业详细信息中提取模板 blob URI

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

模板 blob URI 将采用此格式并提取模板名称

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

因此，以上示例中的模板名称将是 ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json```，而容器名称为 ```myVM-daa1931199fd4a22ae601f46d8812276```

现在，请获取此容器和模板的 SAS 令牌（参阅[此处](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-cli#provide-sas-token-during-deployment)了解详细信息）

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>部署模板以创建 VM

现在部署模板来创建 VM，如[此处](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli)所述。

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

若要确认已从恢复的磁盘创建了 VM，请使用 [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) 列出资源组中的 VM，如下所示：

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>后续步骤

在本教程中，你从恢复点还原了一个磁盘，然后从该磁盘创建了一个 VM。 你已了解如何执行以下操作：

> [!div class="checklist"]
>
> * 列出和选择恢复点
> * 从恢复点还原磁盘
> * 从还原的磁盘创建 VM

请进入下一个教程了解如何从恢复点还原单个文件。

> [!div class="nextstepaction"]
> [将文件还原到 Azure 中的虚拟机](tutorial-restore-files.md)
