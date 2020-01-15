---
title: 教程 - 使用 CLI 在 Azure 上还原 SAP HANA DB
description: 在本教程中，你将了解如何使用 Azure CLI 从 Azure 备份恢复服务保管库还原 Azure VM 上运行的 SAP HANA 数据库。
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75470402"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>教程：使用 Azure CLI 还原 Azure VM 中的 SAP HANA 数据库

Azure CLI 用于从命令行或通过脚本创建和管理 Azure 资源。 本文档详细介绍了如何使用 Azure CLI 在 Azure VM 上还原已备份的 SAP HANA 数据库。 还可以使用 [Azure 门户](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)执行这些步骤。

使用 [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) 运行 CLI 命令。

本教程结束时，你将能够：

> [!div class="checklist"]
>
> * 查看已备份数据库的还原点
> * 还原数据库

本教程假设你有一个 SAP HANA 数据库在通过 Azure 备份完成备份的 Azure VM 上运行。 如果你已按照[使用 CLI 在 Azure 中备份 SAP HANA 数据库](tutorial-sap-hana-backup-cli.md)中的说明备份 SAP HANA 数据库，则要使用以下资源：

* 名为 saphanaResourceGroup 的资源组 
* 名为 saphanaVault 的保管库 
* 名为 VMAppContainer;Compute;saphanaResourceGroup;saphanaVM 的受保护容器 
* 名为 saphanadatabase;hxe;hxe 的已备份数据库/项 
* westus2 区域中的资源 

## <a name="view-restore-points-for-a-backed-up-database"></a>查看已备份数据库的还原点

要查看数据库的所有恢复点列表，请使用 [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) cmdlet，如下所示：

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

恢复点列表如下所示：

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

如你所见，上面的列表包含三个恢复点：分别用于完整备份、差异备份和日志备份。

>[!NOTE]
>你还可以使用 [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) cmdlet 查看每个连续日志备份链的起点和终点。

## <a name="prerequisites-to-restore-a-database"></a>还原数据库的先决条件

开始还原数据库前，请确保满足以下先决条件：

* 只能将数据库还原到同一区域中的 SAP HANA 实例
* 目标实例必须注册到与源相同的保管库
* Azure 备份不能识别同一 VM 上两个不同的 SAP HANA 实例。 因此，无法将数据从同一 VM 上的一个实例还原到另一个实例。

## <a name="restore-a-database"></a>还原数据库

Azure 备份可以还原在 Azure VM 上运行的 SAP HANA 数据库，如下所示：

* 使用日志备份还原到特定的日期或时间（精确到秒）。 Azure 备份可自动确定相应的完整备份、差异备份和日志链备份，这些是根据所选时间进行还原所必需的。
* 还原特定的完整备份或差异备份，这样就可以还原到特定的恢复点。

要还原数据库，请使用 [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet，该 cmdlet 需要将恢复配置对象作为其中一个输入。 此对象可使用 [az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) cmdlet 生成。 恢复配置对象包含执行还原的所有详细信息。 其中一项是还原模式：OriginalWorkloadRestore 或 AlternateWorkloadRestore   。

>[!NOTE]
> **OriginalWorkloadRestore** - 将数据库还原到与原始源相同的 SAP HANA 实例。 此选项会覆盖原始数据库。 <br>
> **AlternateWorkloadRestore** - 将数据库还原到备用位置，同时保留原始源数据库。

## <a name="restore-to-alternate-location"></a>还原到备用位置

要将数据库还原到备用位置，请使用 AlternateWorkloadRestore 作为还原模式  。 然后，必须选择还原点（可以是以前的时间点或以前的任何还原点）。

在本教程中，你将还原到以前的还原点。 [查看数据库的还原点列表](#view-restore-points-for-a-backed-up-database)并选择要还原到的点。 本教程将使用名称为 7660777527047692711 的还原点  。

使用上述还原点名称和还原模式，让我们通过 [az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) cmdlet 创建恢复配置对象。 我们来看看此 cmdlet 中其余每个参数的含义：

* **--target-item-name**：这是已还原数据库要使用的名称。 在本例中，我们使用名称 restored_database  。
* **--target-server-name**：这是成功注册到恢复服务保管库并且与要还原的数据库位于同一区域中的 SAP HANA 服务器的名称。 在本教程中，我们会将数据库还原到受保护的同一 SAP HANA 服务器，名为 hxehost  。
* **--target-server-type**：还原 SAP HANA 数据库时，必须使用 SapHanaDatabase  。

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

上一个查询的响应将是一个类似如下的恢复配置对象：

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

现在，要还原数据库，请运行 [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet。 要使用此命令，我们需输入上面的 json 输出（该输出保存到名为 recoveryconfig.json 的文件中）  。

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

输出如下所示：

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

响应将为你提供作业名。 此作业名可用于使用 [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet 来跟踪作业状态。

## <a name="restore-and-overwrite"></a>还原并覆盖

为还原到原始位置，我们将使用 OrignialWorkloadRestore 作为还原模式  。 然后，必须选择还原点（可以是以前的时间点或以前的任何还原点）。

在本教程中，我们选择还原到以前的时间点 28-11-2019-09:53:00。 此还原点可按以下格式提供：dd-mm-yyyy、dd-mm-yyyy-hh:mm:ss。 要选择要还原到的有效时间点，请使用 [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) cmdlet，此 cmdlet 会列出连续日志链备份的间隔。

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

上一个查询的响应将是一个如下所示的恢复配置对象：

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

现在，要还原数据库，请运行 [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet。 要使用此命令，我们需输入上面的 json 输出（该输出保存到名为 recoveryconfig.json 的文件中）  。

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

输出如下所示：

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

响应将为你提供作业名。 此作业名可用于使用 [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet 来跟踪作业状态。

## <a name="next-steps"></a>后续步骤

* 要了解如何使用 Azure CLI 管理备份的 SAP HANA 数据库，请继续学习教程[使用 CLI 在 Azure VM 中管理 SAP HANA 数据库](tutorial-sap-hana-backup-cli.md)

* 要了解如何使用 Azure 门户还原 Azure VM 中运行的 SAP HANA 数据库，请参阅[在 Azure VM 上还原 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
