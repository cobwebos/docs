---
title: 使用 Azure 备份还原 VM 磁盘
description: 了解如何在 Azure 中使用备份和恢复服务还原磁盘并创建恢复的 VM。
services: backup
author: markgalioto
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: tutorial
ms.date: 4/17/2018
ms.author: markgal
ms.custom: mvc
ms.openlocfilehash: 4a122aebd149131e97be5c593a51871b1a943577
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427395"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>在 Azure 中还原磁盘并创建恢复的 VM
Azure 备份可创建恢复点，这些恢复点存储在异地冗余的恢复保管库中。 从恢复点还原时，可以还原整个 VM，也可以还原单个文件。 本文介绍如何使用 CLI 还原完整的 VM。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 列出和选择恢复点
> * 从恢复点还原磁盘
> * 从还原的磁盘创建 VM

有关使用 PowerShell 还原磁盘并创建已恢复的 VM 的信息，请参阅[使用 PowerShell 备份和还原 Azure VM](backup-azure-vms-automation.md#restore-an-azure-vm)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程需要你运行 Azure CLI 2.0.18 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 


## <a name="prerequisites"></a>先决条件
本教程需要使用 Azure 备份所保护的 Linux VM。 若要模拟意外的 VM 删除和恢复过程，请从恢复点中的磁盘创建 VM。 如果需要使用 Azure 备份所保护的 Linux VM，请参阅[在 Azure 中使用 CLI 备份虚拟机](quick-backup-vm-cli.md)。


## <a name="backup-overview"></a>备份概述
当 Azure 启动备份时，VM 上的备份扩展将获取时点快照。 请求第一个备份时，将在 VM 上安装备份扩展。 如果进行备份时 VM 未运行，则 Azure 备份可能还需要获取基础存储的快照。

默认情况下，Azure 备份采用文件系统一致的备份。 Azure 备份获取快照后，数据将传输到恢复服务保管库。 为最大限度地提高效率，Azure 备份仅标识和传输自上次备份以后已更改的数据块。

数据传输完成后，会删除快照并创建恢复点。


## <a name="list-available-recovery-points"></a>列出可用的恢复点
若要还原磁盘，请选择恢复点作为恢复数据的源。 由于默认策略每天创建一个恢复点并保留 30 天，因此，可以保留一组恢复点，以便可以选择一个特定的时点用于恢复。 

若要查看可用恢复点的列表，请使用 [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) 命令。 将使用恢复点名称恢复磁盘。 在本教程中，我们希望最近的恢复点可用。 `--query [0].name` 参数可选择最近的恢复点名称，如下所示：

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>还原 VM 磁盘
若要从恢复点恢复磁盘，请先创建 Azure 存储帐户。 此存储帐户用于存储还原的磁盘。 在其他步骤中，将使用还原的磁盘创建 VM。

1. 若要创建存储帐户，请使用 [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) 命令。 存储帐户名称必须全部为小写，且全局唯一。 将 mystorageaccount 替换为你自己唯一的名称：

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. 使用 [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks) 从你的恢复点还原磁盘。 将 mystorageaccount 替换为你在前一个命令中创建的存储帐户的名称。 将 myRecoveryPointName 替换为你在前面的 [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) 命令输出中获得的恢复点名称：

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>监视还原作业
若要监视还原作业的状态，请使用 [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list)：

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

输出类似于以下示例，该示例显示还原作业处于“正在进行”状态：

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

当还原作业的“状态”报告“已完成”时，磁盘已还原到存储帐户。


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>将还原的磁盘转换为托管磁盘
还原作业将创建一个非托管磁盘。 若要从磁盘创建 VM，必须首先将该磁盘转换为托管磁盘。

1. 使用 [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-show-connection-string) 命令获取存储帐户的连接信息。 将 mystorageaccount 替换为你的存储名称帐户，如下所示：
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. 非托管磁盘被保护在存储帐户中。 以下命令可以获取有关非托管磁盘的信息，并创建名为 uri 的变量，此变量会在下一步创建托管磁盘时用到。

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. 现在，可以使用 [az disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-create) 从恢复的磁盘创建托管磁盘。 使用上一步创建的 uri 变量作为托管磁盘的源。

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. 由于你现在已从还原磁盘获得托管磁盘，请使用 [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-delete) 命令清理非托管磁盘和存储帐户。 将 mystorageaccount 替换为你的存储名称帐户，如下所示：

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>从还原的磁盘创建 VM
最后一步是从托管磁盘创建 VM。

1. 使用 [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) 从托管磁盘创建 VM，如下所示：

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. 若要确认已从恢复的磁盘创建了 VM，请使用 [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) 列出资源组中的 VM，如下所示：

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>后续步骤
在本教程中，你从恢复点还原了一个磁盘，然后从该磁盘创建了一个 VM。 你已了解如何：

> [!div class="checklist"]
> * 列出和选择恢复点
> * 从恢复点还原磁盘
> * 从还原的磁盘创建 VM

请进入下一个教程了解如何从恢复点还原单个文件。

> [!div class="nextstepaction"]
> [将文件还原到 Azure 中的虚拟机](tutorial-restore-files.md)

