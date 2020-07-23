---
title: 快速入门 - 使用 Azure CLI 备份 VM
description: 在本快速入门中，你将了解如何创建恢复服务保管库，如何在 VM 上启用保护，以及如何使用 Azure CLI 创建初始恢复点。
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: a359e47a70f6a1a9e0957b4e1c3965c8db12339a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "74171992"
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-cli"></a>使用 CLI 在 Azure 中备份虚拟机

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 可以通过定期创建备份来保护数据。 Azure 备份可创建恢复点，这些恢复点可存储在异地冗余的恢复保管库中。 本文详细介绍如何使用 Azure CLI 在 Azure 中备份虚拟机 (VM)。 也可以使用 [Azure PowerShell](quick-backup-vm-powershell.md) 或 [Azure 门户](quick-backup-vm-portal.md)执行这些步骤。

参考本快速入门可在现有的 Azure VM 上备份。 如果需要创建 VM，可以[使用 Azure CLI 创建 VM](../virtual-machines/linux/quick-create-cli.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本地安装和使用 CLI，必须运行 Azure CLI 2.0.18 或更高版本。 若要查找 CLI 版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是一个逻辑容器，用于存储每个受保护资源（例如 Azure VM）的备份数据。 运行受保护资源的备份作业时，该作业会在恢复服务保管库中创建一个恢复点。 然后，可以使用其中一个恢复点将数据还原到给定的时间点。

使用 [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create) 创建恢复服务保管库。 指定与想要保护的 VM 相同的资源组和位置。 如果使用过 [VM 快速入门](../virtual-machines/linux/quick-create-cli.md)，则已创建：

- 名为 myResourceGroup 的资源组； 
- 名为 myVM 的 VM  ；
- “eastus”位置的资源。 

```azurecli-interactive
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

默认情况下，恢复服务保管库是针对异地冗余存储设置的。 异地冗余存储可确保将备份数据复制到距主区域数百英里以外的辅助 Azure 区域。 如果存储冗余设置需要修改，请使用 [az backup vault backup-properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) cmdlet。

```azurecli
az backup vault backup-properties set \
    --name myRecoveryServicesVault  \
    --resource-group myResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

## <a name="enable-backup-for-an-azure-vm"></a>为 Azure VM 启用备份

创建一个保护策略，用于定义：备份作业的运行时间以及恢复点的存储期限。 默认保护策略每天运行一个备份作业，并将恢复点保留 30 天。 可以使用这些默认策略值来快速保护 VM。 若要为 VM 启用备份保护，请使用 [az backup protection enable-for-vm](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-enable-for-vm)。 指定要保护的资源组和 VM，再指定要使用的策略：

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

> [!NOTE]
> 如果 VM 与保管库不在同一个资源组中，则 myResourceGroup 引用创建保管库所在的资源组。 如下所示，请提供 VM ID 而不是 VM 名称。

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') \
    --policy-name DefaultPolicy
```

> [!IMPORTANT]
> 使用 CLI 一次为多个 VM 启用备份时，请确保单个策略关联的 VM 不超过 100 个。 这是[建议的最佳做法](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy)。 目前，如果有超过 100 个 VM，PS 客户端不会显式阻止，但计划在将来添加检查。

## <a name="start-a-backup-job"></a>启动备份作业

若要立即开始备份而不是等待默认策略根据计划的时间运行作业，请使用 [az backup protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now)。 第一个备份作业会创建一个完整恢复点。 初始备份后的每个备份作业都会创建增量恢复点。 增量恢复点有利于存储并具有时效性，因为它们仅传输自上次备份以来所做的更改。

以下参数用于备份 VM：

- `--container-name` 是 VM 的名称
- `--item-name` 是 VM 的名称
- 应以 UTC 时间格式 (**dd-mm-yyyy**) 将 `--retain-until` 值设置为希望恢复点可用的最后一个日期

以下示例备份名为 *myVM* 的 VM，并将恢复点的过期时间设置为 2017 年 10 月 18 日：

```azurecli-interactive
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```

## <a name="monitor-the-backup-job"></a>监视备份作业

若要监视备份作业的状态，请使用 [az backup job list](https://docs.microsoft.com/cli/azure/backup/job#az-backup-job-list)：

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

输出如以下示例所示，其中显示备份作业的状态为“正在进行”： 

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

当备份作业的“状态”报告为“已完成”时，表示 VM 已受恢复服务的保护，并且已存储完整的恢复点。  

## <a name="clean-up-deployment"></a>清理部署

如果不再需要，可以在 VM 上禁用保护，删除还原点和恢复服务保管库，然后删除资源组和关联的 VM 资源。 如果使用了现有的 VM，可以跳过最后一个 [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令，以保留资源组和 VM。

若要尝试备份教程，了解如何还原 VM 的数据，请转到[后续步骤](#next-steps)。

```azurecli-interactive
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建恢复服务保管库，如何在 VM 上启用保护，以及如何创建初始恢复点。 若要详细了解 Azure 备份和恢复服务，请继续学习其他教程。

> [!div class="nextstepaction"]
> [备份多个 Azure VM](./tutorial-backup-vm-at-scale.md)
