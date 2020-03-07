---
title: 教程 - 使用 CLI 在 Azure 上进行 SAP HANA 数据库备份
description: 在本教程中，了解如何使用 Azure CLI 将 Azure VM 上运行的 SAP HANA 数据库备份到 Azure 备份恢复服务保管库。
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: cdc8a8fb09a086a2b9212c21d071f267991fa275
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206616"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>教程：使用 Azure CLI 备份 Azure VM 中的 SAP HANA 数据库

Azure CLI 用于从命令行或通过脚本创建和管理 Azure 资源。 本文档详细介绍了如何备份 SAP HANA 数据库并触发按需备份 - 所有这些操作都使用 Azure CLI。 还可以使用 [Azure 门户](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)执行这些步骤。

本文档假定你已在 Azure VM 上安装了 SAP HANA 数据库。 （也可以[使用 Azure CLI 创建 VM](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)）。 完成本教程后，可执行以下操作：

> [!div class="checklist"]
>
> * 创建恢复服务保管库
> * 注册 SAP HANA 实例并发现该实例上的数据库
> * 在 SAP HANA 数据库上启用备份
> * 触发按需备份

查看[目前针对 SAP HANA 提供支持的场景](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本地安装和使用 CLI，必须运行 Azure CLI 版本 xx.xxx.x 或更高版本。 若要查找 CLI 版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是一个逻辑容器，用于存储每个受保护资源（例如 Azure VM）或在 Azure VM 上运行的工作负载（例如 SQL 或 HANA 数据库）的备份数据。 运行受保护资源的备份作业时，该作业会在恢复服务保管库中创建一个恢复点。 然后，可以使用其中一个恢复点将数据还原到给定的时间点。

使用 [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create) 创建恢复服务保管库。 指定与想要保护的 VM 相同的资源组和位置。 了解如何通过此 [VM 快速入门](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)使用 Azure CLI 创建 VM。

本教程将使用以下内容：

* 名为 saphanaResourceGroup 的资源组 
* 名为 saphanaVM 的 VM 
* westus2 位置中的资源  。

我们将创建一个名为 saphanaVault 的保管库  。

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

默认情况下，恢复服务保管库是针对异地冗余存储设置的。 异地冗余存储可确保将备份数据复制到距主区域数百英里以外的辅助 Azure 区域。 如果存储冗余设置需要修改，请使用 [az backup vault backup-properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) cmdlet。

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

若要查看是否已成功创建保管库，请使用 [az backup vault list](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-list) cmdlet。 你将看到以下响应：

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>注册并保护 SAP HANA 实例

对于要由 Azure 服务发现的 SAP HANA 实例（该实例上安装有包含 SAP HANA 的 VM），必须在 SAP HANA 计算机上运行[预注册脚本](https://aka.ms/scriptforpermsonhana)。 运行脚本之前，请确保满足所有[先决条件](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#prerequisites)。 若要了解脚本的更多功能，请参阅[预注册脚本的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分。

脚本运行后，可通过之前创建的恢复服务保管库注册 SAP HANA 实例。 若要注册实例，请使用 [az backup container register](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-register) cmdlet。 VMResourceId 是你创建的用于安装 SAP HANA 的 VM 资源 ID  。

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>如果 VM 与保管库不在同一个资源组中，则 saphanaResourceGroup 引用创建保管库所在的资源组  。

注册 SAP HANA 实例会自动发现其所有当前数据库。 但是，若要发现将来可能添加的任何新数据库，请参考[发现已添加到已注册 SAP HANA 的新数据库](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance)实例部分。

若要检查 SAP HANA 实例是否已成功注册到保管库，请使用 [az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) cmdlet。 你将看到以下响应：

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> 以上输出中的“名称”列是指容器名。 以下部分将使用此容器名，以启用备份并触发它们。 在本例中，为 VMAppContainer;Compute;saphanaResourceGroup;saphanaVM  。

## <a name="enable-backup-on-sap-hana-database"></a>在 SAP HANA 数据库上启用备份

[az backup protectable-item list](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) cmdlet 列出了在上一步中注册的 SAP HANA 实例上发现的所有数据库。

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

应在此列表中找到要备份的数据库，如下所示：

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

从上面的输出中可以看出，SAP HANA 系统的 SID 是 HXE。 在本教程中，我们将为 hxehost 服务器上的 saphanadatabase;hxe;hxe 数据库配置备份   。

若要在数据库上一次保护和配置一个备份，请使用 [az backup protection enable-for-azurewl](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurewl) cmdlet。 提供要使用的策略名。 若要使用 CLI 创建策略，请使用 [az backup policy create](https://docs.microsoft.com//cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet。 本教程将使用 sapahanaPolicy 策略  。

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

可以使用 [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet 检查以上备份配置是否已完成。 输出将如下所示：

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

[az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet 列出了在受保护数据库上已运行或当前正在运行的所有（计划或按需）备份作业，以及注册、配置备份、删除备份数据等其他操作。

## <a name="trigger-an-on-demand-backup"></a>触发按需备份

尽管上述部分详细介绍了如何配置计划的备份，但本部分介绍如何触发按需备份。 为此，我们现在使用 [az backup protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now) cmdlet。

>[!NOTE]
> 按需备份的保留策略由数据库的基础保留策略决定。

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

输出将如下所示：

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

响应将为你提供作业名。 此作业名可用于使用 [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet 来跟踪作业状态。

>[!NOTE]
>除了安排完整备份或差异备份，当前还可以手动触发它们。 日志备份由 SAP HANA 内部自动触发和管理。
>
> Azure 备份目前不支持增量备份。

## <a name="next-steps"></a>后续步骤

* 若要了解如何使用 CLI 在 Azure VM 中还原 SAP HANA 数据库，请继续学习教程 - [使用 CLI 在 Azure VM 中还原 SAP HANA 数据库](tutorial-sap-hana-restore-cli.md)

* 若要了解如何使用 Azure 门户备份 Azure VM 中运行的 SAP HANA 数据库，请参阅[在 Azure VM 上备份 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
