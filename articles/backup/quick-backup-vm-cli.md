---
title: "Azure 快速入门 - 使用 Azure CLI 备份 VM | Microsoft Docs"
description: "了解如何使用 Azure CLI 备份虚拟机"
services: virtual-machines-linux, azure-backup
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: virtual-machines-linux, azure-backup
ms.devlang: azurecli
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/18/2017
ms.author: iainfou
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: b01916516d15ffee46f135e175ee4136f79acbe5
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="back-up-a-virtual-machine-in-azure-with-the-cli"></a>使用 CLI 在 Azure 中备份虚拟机
Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 可以通过定期创建备份来保护数据。 Azure 备份可创建恢复点，这些恢复点可存储在异地冗余的恢复保管库中。 本文详细介绍如何使用 Azure CLI 在 Azure 中备份虚拟机 (VM)。 也可以使用 [Azure PowerShell](quick-backup-vm-powershell.md) 或 [Azure 门户](quick-backup-vm-portal.md)执行这些步骤。

参考本快速入门可在现有的 Azure VM 上备份。 如果需要创建 VM，可以[使用 Azure CLI 创建 VM](../virtual-machines/linux/quick-create-cli.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门教程要求运行 Azure CLI 2.0.18 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 


## <a name="register-the-azure-backup-resource-provider"></a>注册 Azure 备份资源提供程序
首次使用 Azure 备份时，必须使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az_provider_register) 在订阅中注册 Azure 恢复服务提供程序。

```azurecli-interactive
az provider register --namespace Microsoft.RecoveryServices
```


## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库
恢复服务保管库是一个逻辑容器，用于存储每个受保护资源（例如 Azure VM）的备份数据。 运行受保护资源的备份作业时，该作业会在恢复服务保管库中创建一个恢复点。 然后，可以使用其中一个恢复点将数据还原到给定的时间点。

使用 **az backup vault create** 创建恢复服务保管库。 指定与想要保护的 VM 相同的资源组和位置。 如果使用了 [VM 快速入门](../virtual-machines/linux/quick-create-cli.md)，则资源组名为 *myResourceGroup*，VM 名为 *myVM*，资源位于 *eastus* 位置。

```azurecli-interactive 
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

默认情况下，保管库是针对异地冗余存储设置的。 为了进一步保护数据，此存储冗余级别可确保将备份数据复制到距离主要区域数百英里以外的 Azure 次要区域。


## <a name="enable-backup-for-an-azure-vm"></a>为 Azure VM 启用备份
创建并使用策略来定义备份作业的运行时间以及恢复点的存储期限。 默认保护策略每天运行备份作业，并将恢复点保留 30 天。 可以使用这些默认策略值来快速保护 VM。 若要为 VM 启用备份保护，请使用 **az backup protection enable-for-vm**。 指定要保护的资源组和 VM，再指定要使用的策略：

```azurecli-interactive 
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```


## <a name="start-a-backup-job"></a>启动备份作业
若要立即开始备份而不是等待默认策略根据计划的时间运行作业，请使用 **az backup protection backup-now**。 这第一个备份作业会创建完整恢复点。 此初始备份后的每个备份作业会创建增量恢复点。 增量恢复点有利于存储并具有时效性，因为它们仅传输自上次备份以来所做的更改。

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

由于这第一个备份作业需要创建完整恢复点，因此，该过程最长可能需要花费 20 分钟。


## <a name="monitor-the-backup-job"></a>监视备份作业
若要监视备份作业的状态，请使用 **az backup job list**：

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

输出如以下示例所示，其中显示备份作业的状态为“正在进行”：

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

当备份作业的“状态”报告为“已完成”时，表示 VM 已受恢复服务的保护，并且已存储完整的恢复点。


## <a name="clean-up-deployment"></a>清理部署
如果不再需要，可以在 VM 上禁用保护，删除还原点和恢复服务保管库，然后删除资源组和关联的 VM 资源。 如果使用了现有的 VM，可以跳过最后一个 [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete) 命令，以保留资源组和 VM。

如果想要继续学习有关如何还原 VM 数据的备份教程，请跳过本部分中的步骤，并转到[后续步骤](#next-steps)。 

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
在本快速入门中，我们创建了恢复服务保管库，在 VM 上启用了保护，并创建了初始恢复点。 若要详细了解 Azure 备份和恢复服务，请继续学习其他教程。

> [!div class="nextstepaction"]
> [备份多个 Azure VM](./tutorial-backup-vm-at-scale.md)

