---
title: 与 Azure CLI 备份 Azure 文件共享
description: 了解如何使用 Azure CLI 在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844035"
---
# <a name="back-up-azure-file-shares-with-cli"></a>通过 CLI 备份 Azure 文件共享

Azure 命令行接口（CLI）提供了用于管理 Azure 资源的命令行体验。 这是一个很好的工具，用于构建自定义自动化以使用 Azure 资源。 本文详细介绍如何将 Azure 文件共享与 Azure CLI 备份。 也可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) 或 [Azure 门户](backup-afs.md)执行这些步骤。

本教程结束时，你将学习如何使用 Azure CLI 执行以下操作：

* 创建恢复服务保管库
* 为 Azure 文件共享启用备份
* 触发文件共享的按需备份

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本地安装和使用 CLI，必须运行 Azure CLI 2.0.18 或更高版本。 若要查找 CLI 版本，请 `run az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是一个实体，为你提供跨所有备份项的综合视图和管理功能。 运行受保护资源的备份作业时，该作业会在恢复服务保管库中创建一个恢复点。 然后，可以使用其中一个恢复点将数据还原到给定的时间点。

按照以下步骤创建恢复服务保管库：

1. 保管库放在资源组中。 如果没有现有的资源组，请使用[az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)创建一个新的资源组。 在本教程中，我们将在 "美国东部" 区域中创建新的资源组*azurefiles* 。

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. 使用[az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) cmdlet 创建保管库。 请为保管库指定与资源组相同的位置。

    以下示例在美国东部区域中创建名为*azurefilesvault*的恢复服务保管库。

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. 指定保管库存储使用的冗余类型。 可以使用[本地冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)或[异地冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

    下面的示例使用[az 备份保管库备份-properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) cmdlet 将*azurefilesvault*的存储冗余选项设置为**Georedundant** 。

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    若要检查是否已成功创建保管库，可以使用[az backup vault show](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) cmdlet 获取保管库的详细信息。 下面的示例显示了在上述步骤中创建的*azurefilesvault*的详细信息。

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    输出将类似于下面的响应：

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>为 Azure 文件共享启用备份

本部分假设你已有要为其配置备份的 Azure 文件共享。 如果没有，请使用[az storage share create](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create)命令创建 Azure 文件共享。

若要为文件共享启用备份，你需要创建一个保护策略来定义备份作业的运行时间以及恢复点的存储时间。 你可以使用[az backup policy create](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet 创建备份策略。

下面的示例使用[az backup protection azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) cmdlet 在*afsaccount*存储帐户中使用*计划 1*备份策略为*azurefiles*文件共享启用备份：

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

输出中的 "**名称**" 属性对应于 "**启用备份**" 操作的备份服务创建的作业名称。 若要跟踪作业的状态，请使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet。

## <a name="trigger-an-on-demand-backup-for-file-share"></a>触发文件共享的按需备份

如果要为文件共享触发按需备份，而不是等待备份策略在计划时间运行作业，请使用[az backup protection backup](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) cmdlet。

需要定义以下参数来触发按需备份：

* **--container-name**是托管文件共享的存储帐户的名称。 若要检索容器的**名称**或**友好名称**，请使用[az backup container list](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--name**是要触发按需备份的文件共享的名称。 若要检索已备份项的**名称**或**友好名称**，请使用[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。
* **--保留-直到**指定要保留恢复点的日期。 应将该值设置为 UTC 时间格式（yyyy-mm-dd）。

下面的示例为*afsaccount*存储帐户中的*azuresfiles*文件共享启用按需备份，并保留到*20-01-2020*。

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

输出中的**name**属性对应于备份服务为 "按需备份" 操作创建的作业的名称。 若要跟踪作业的状态，请使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet。

## <a name="next-steps"></a>后续步骤

* 了解如何[通过 CLI 还原 Azure 文件共享](restore-afs-cli.md)
* 了解如何[通过 CLI 管理 Azure 文件共享 ackups](manage-afs-backup-cli.md)
