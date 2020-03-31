---
title: 使用 Azure CLI 备份 Azure 文件共享
description: 了解如何使用 Azure CLI 在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844035"
---
# <a name="back-up-azure-file-shares-with-cli"></a>使用 CLI 备份 Azure 文件共享

Azure 命令行接口 （CLI） 提供了用于管理 Azure 资源的命令行体验。 它是构建自定义自动化以使用 Azure 资源的绝佳工具。 本文详细介绍了如何使用 Azure CLI 备份 Azure 文件共享。 也可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) 或 [Azure 门户](backup-afs.md)执行这些步骤。

在本教程结束时，您将学习如何使用 Azure CLI 执行以下操作：

* 创建恢复服务保管库
* 为 Azure 文件共享启用备份
* 触发文件共享的按需备份

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本地安装和使用 CLI，必须运行 Azure CLI 2.0.18 或更高版本。 要查找 CLI 版本`run az --version`，应查找 。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是一个实体，它为您提供跨所有备份项的整合视图和管理功能。 运行受保护资源的备份作业时，该作业会在恢复服务保管库中创建一个恢复点。 然后，可以使用其中一个恢复点将数据还原到给定的时间点。

按照以下步骤创建恢复服务保管库：

1. 保管库放在资源组中。 如果没有现有资源组，请创建一个使用 az 组创建 的新[资源组](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)。 在本教程中，我们在美国东部区域创建新的资源组*azure 文件*。

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. 使用[az 备份保管库创建](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create)cmdlet 创建保管库。 请为保管库指定与资源组相同的位置。

    下面的示例在美国东部区域创建名为*azurefilevault*的恢复服务保管库。

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. 指定保管库存储使用的冗余类型。 可以使用[本地冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)或[异地冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

    下面的示例使用[az 备份保管库备份属性 set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) cmdlet 将*azurefilevault*的存储冗余选项设置为**异地冗余**。

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    要检查保管库是否成功创建，可以使用[az 备份保管库显示](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show)cmdlet 来获取保管库的详细信息。 下面的示例显示我们在上述步骤中创建的*azure 文件库*的详细信息。

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    输出将类似于以下响应：

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>为 Azure 文件共享启用备份

本节假定您已经拥有要为其配置备份的 Azure 文件共享。 如果没有，请使用[az 存储共享创建](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create)命令创建 Azure 文件共享。

要启用文件共享的备份，您需要创建一个保护策略，该策略定义备份作业何时运行以及存储恢复点的时间。 您可以使用[az 备份策略创建](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create)cmdlet 创建备份策略。

下面的示例使用[az 备份保护启用 azure 文件共享](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare)cmdlet 启用使用*计划 1*备份策略在*afs 帐户*存储帐户中的 azure*文件*共享的备份：

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

输出中的**Name**属性对应于**为启用备份**操作的备份服务创建的作业的名称。 要跟踪作业的状态，请使用 az[备份作业显示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)cmdlet。

## <a name="trigger-an-on-demand-backup-for-file-share"></a>触发文件共享的按需备份

如果要为文件共享触发按需备份，而不是等待备份策略在计划的时间运行作业，请使用[az 备份保护立即备份](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now)cmdlet。

您需要定义以下参数才能触发按需备份：

* **--容器名称**是托管文件共享的存储帐户的名称。 若要检索容器**的名称**或**友好名称**，请使用[az 备份容器列表](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--项名称**是要为其触发按需备份的文件共享的名称。 若要检索备份项**的名称**或**友好名称**，请使用[az 备份项列表](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。
* **--保留 -直到**指定日期，直到您想要保留恢复点。 该值应设置为 UTC 时间格式 （dd-mm-yyyyy）。

下面的示例触发*afs 帐户*存储帐户中*Azuresfile*文件共享的按需备份，保留期至*20-01-2020*。

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

输出中的**Name**属性对应于备份服务为"按需备份"操作创建的作业的名称。 要跟踪作业的状态，请使用[az 备份作业显示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)cmdlet。

## <a name="next-steps"></a>后续步骤

* 了解如何使用[CLI 还原 Azure 文件共享](restore-afs-cli.md)
* 了解如何使用[CLI 管理 Azure 文件共享图](manage-afs-backup-cli.md)
