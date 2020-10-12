---
title: 教程 - 使用 CLI 在 Azure 上还原 SAP HANA DB
description: 在本教程中，你将了解如何使用 Azure CLI 从 Azure 备份恢复服务保管库还原 Azure VM 上运行的 SAP HANA 数据库。
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e524bfe090f0d67b76c13e876f44e83986aeb9e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334797"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>教程：使用 Azure CLI 还原 Azure VM 中的 SAP HANA 数据库

Azure CLI 用于从命令行或通过脚本创建和管理 Azure 资源。 本文档详细介绍了如何使用 Azure CLI 在 Azure VM 上还原已备份的 SAP HANA 数据库。 还可以使用 [Azure 门户](./sap-hana-db-restore.md)执行这些步骤。

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

要查看数据库的所有恢复点列表，请使用 [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) cmdlet，如下所示：

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
>你还可以使用 [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) cmdlet 查看每个连续日志备份链的起点和终点。

## <a name="prerequisites-to-restore-a-database"></a>还原数据库的先决条件

开始还原数据库前，请确保满足以下先决条件：

* 只能将数据库还原到同一区域中的 SAP HANA 实例
* 目标实例必须注册到与源相同的保管库
* Azure 备份不能识别同一 VM 上两个不同的 SAP HANA 实例。 因此，无法将数据从同一 VM 上的一个实例还原到另一个实例。

## <a name="restore-a-database"></a>还原数据库

Azure 备份可以还原在 Azure VM 上运行的 SAP HANA 数据库，如下所示：

* 使用日志备份还原到特定的日期或时间（精确到秒）。 Azure 备份可自动确定相应的完整备份、差异备份和日志链备份，这些是根据所选时间进行还原所必需的。
* 还原特定的完整备份或差异备份，这样就可以还原到特定的恢复点。

要还原数据库，请使用 [az restore restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) cmdlet，该 cmdlet 需要将恢复配置对象作为其中一个输入。 此对象可使用 [az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) cmdlet 生成。 恢复配置对象包含执行还原的所有详细信息。 其中一项是还原模式：OriginalWorkloadRestore 或 AlternateWorkloadRestore 。

>[!NOTE]
> **OriginalWorkloadRestore** - 将数据库还原到与原始源相同的 SAP HANA 实例。 此选项会覆盖原始数据库。 <br>
> **AlternateWorkloadRestore** - 将数据库还原到备用位置，同时保留原始源数据库。

## <a name="restore-to-alternate-location"></a>还原到备用位置

要将数据库还原到备用位置，请使用 AlternateWorkloadRestore 作为还原模式。 然后，必须选择还原点（可以是以前的时间点或以前的任何还原点）。

在本教程中，你将还原到以前的还原点。 [查看数据库的还原点列表](#view-restore-points-for-a-backed-up-database)并选择要还原到的点。 本教程将使用名称为 7660777527047692711 的还原点。

使用上述还原点名称和还原模式，让我们通过 [az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) cmdlet 创建恢复配置对象。 我们来看看此 cmdlet 中其余每个参数的含义：

* **--target-item-name**：这是已还原数据库要使用的名称。 在本例中，我们使用名称 restored_database。
* **--target-server-name**：这是成功注册到恢复服务保管库并且与要还原的数据库位于同一区域中的 SAP HANA 服务器的名称。 在本教程中，我们会将数据库还原到受保护的同一 SAP HANA 服务器，名为 hxehost。
* **--target-server-type**：若要还原 SAP HANA 数据库，必须使用 HANAInstance。

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
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

现在，要还原数据库，请运行 [az restore restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) cmdlet。 要使用此命令，我们需输入上面的 json 输出（该输出保存到名为 recoveryconfig.json 的文件中）。

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

响应将为你提供作业名。 此作业名可用于使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet 来跟踪作业状态。

## <a name="restore-and-overwrite"></a>还原并覆盖

为还原到原始位置，我们将使用 OrignialWorkloadRestore 作为还原模式。 然后，必须选择还原点（可以是以前的时间点或以前的任何还原点）。

在本教程中，我们选择还原到以前的时间点 28-11-2019-09:53:00。 此还原点可按以下格式提供：dd-mm-yyyy、dd-mm-yyyy-hh:mm:ss。 要选择要还原到的有效时间点，请使用 [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) cmdlet，此 cmdlet 会列出连续日志链备份的间隔。

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
{"restore_mode": "OriginalLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "DefaultRangeRecoveryPoint", "log_point_in_time": "28-11-2019-09:53:00", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}"
```

现在，要还原数据库，请运行 [az restore restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) cmdlet。 要使用此命令，我们需输入上面的 json 输出（该输出保存到名为 recoveryconfig.json 的文件中）。

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

响应将为你提供作业名。 此作业名可用于使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet 来跟踪作业状态。

## <a name="restore-as-files"></a>还原为文件

为将备份数据还原为文件而不是数据库，我们将使用“RestoreAsFiles”作为还原模式。 然后，选择还原点（可以是以前的时间点或以前的任何还原点）。 文件转储到指定路径后，可以将这些文件移动到要将其还原为数据库的任何 SAP HANA 计算机上。 由于可以将这些文件移动到任何计算机，你现在可以跨订阅和区域进行数据还原。

在本教程中，我们将选择还原到以前的时间点 `28-11-2019-09:53:00`，以及在同一 SAP HANA 服务器上将备份文件转储为 `/home/saphana/restoreasfiles` 的位置。 此还原点可按以下格式提供：“dd-mm-yyyy”或“dd-mm-yyyy-hh:mm:ss” 。 要选择要还原到的有效时间点，请使用 [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) cmdlet，此 cmdlet 会列出连续日志链备份的间隔。

使用上述还原点名称和还原模式，让我们通过 [az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) cmdlet 创建恢复配置对象。 我们来看看此 cmdlet 中其余每个参数的含义：

* **--target-container-name**：这是成功注册到恢复服务保管库并且与要还原的数据库位于同一区域中的 SAP HANA 服务器的名称。 在本教程中，我们会将数据库作为文件还原到受保护的同一 SAP HANA 服务器，名为 hxehost。
* **- rp - name** 对于时间点还原，还原点名称将为“DefaultRangeRecoveryPoint”

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode RestoreAsFiles \
    --log-point-in-time 28-11-2019-09:53:00 \
    --rp-name DefaultRangeRecoveryPoint \
    --target-container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --filepath /home/saphana/restoreasfiles \
    --output json
```

上一个查询的响应将是一个如下所示的恢复配置对象：

```output
{
  "alternate_directory_paths": null,
  "container_id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SAPHANA;hanamachine",
  "container_uri": "VMAppContainer;compute;saphana;hanamachine",
  "database_name": null,
  "filepath": "/home/",
  "item_type": "SAPHana",
  "item_uri": "SAPHanaDatabase;hxe;hxe",
  "log_point_in_time": "04-07-2020-09:53:00",
  "recovery_mode": "FileRecovery",
  "recovery_point_id": "DefaultRangeRecoveryPoint",
  "restore_mode": "AlternateLocation",
  "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/hanamachine"
}
```

现在，要将数据库还原为文件，请运行 [az restore restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) cmdlet。 要使用此命令，我们需输入上面的 json 输出（该输出保存到名为“recoveryconfig.json”的文件中）。

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

输出如下所示：

```output
{
  "eTag": null,
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/SAPHANARESOURCEGROUP/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupJobs/608e737e-c001-47ca-8c37-57d909c8a704",
  "location": null,
  "name": "608e737e-c001-47ca-8c37-57d909c8a704",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "7ddd3c3a-c0eb-11ea-a5f8-54ee75ec272a",
    "backupManagementType": "AzureWorkload",
    "duration": "0:00:01.781847",
    "endTime": null,
    "entityFriendlyName": "HXE [hxehost]",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Job Type": "Restore as files"
      },
      "tasksList": [
        {
          "status": "InProgress",
          "taskId": "Transfer data from vault"
        }
      ]
    },
    "jobType": "AzureWorkloadJob",
    "operation": "Restore",
    "startTime": "2020-07-08T07:20:29.336434+00:00",
    "status": "InProgress",
    "workloadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

响应将为你提供作业名。 此作业名可用于使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet 来跟踪作业状态。

转储到目标容器的文件是：

* 数据库备份文件
* 目录文件
* JSON 元数据文件（适用于所涉及的每个备份文件）

通常，如果将网络共享路径或已装载的 Azure 文件共享的路径指定为目标路径，会使同一网络中的其他计算机或其上装载的同一 Azure 文件共享更加轻松地访问这些文件。

>[!NOTE]
>要在装载在目标注册的 VM 上的 Azure 文件共享上还原数据库备份文件，请确保根帐户对 Azure 文件共享具有读/写权限。

根据所选的还原点类型（“时间点”或“完整和差异”），你将看到一个或多个在目标路径中创建的文件夹 。 名为 `Data_<date and time of restore>` 的文件夹之一包含完整备份和差异备份，名为 `Log` 的其他文件夹包含日志备份。

将这些已还原文件移动到要将其还原为数据库的 SAP HANA 服务器。 然后按照这些步骤还原数据库：

1. 使用以下命令在存储备份文件的文件夹/目录上设置权限：

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. 运行下一组命令 `<SID>adm`

    ```bash
    su - <sid>adm
    ```

1. 生成用于还原的目录文件。 从用于完整备份的 JSON 元数据文件中提取“BackupId”，稍后将在还原操作中使用它。 请确保完整备份和日志备份位于不同的文件夹中，并删除这些文件夹中的目录文件和 JSON 元数据文件。

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    在上面的命令中：

    * `<DataFileDir>` - 包含完整备份的文件夹
    * `<LogFilesDir>` - 包含日志备份的文件夹
    * `<PathToPlaceCatalogFile>` - 必须放置已生成的目录文件的文件夹

1. 通过 HANA Studio 使用新生成的目录文件进行还原或使用此新生成的目录运行 HDBSQL 还原查询。 下面列出了 HDBSQL 查询：

    * 要还原到一个时间点：

        如果要创建新的已还原数据库，请运行 HDBSQL 命令，以创建新的数据库 `<DatabaseName>`，然后停止用于还原的数据库。 但是，如果只是还原现有数据库，请运行 HDBSQL 命令以停止数据库。

        然后运行以下命令以还原数据库：

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` - 新数据库或要还原的现有数据库的名称
        * `<Timestamp>` - 时间点还原的确切时间戳
        * `<DatabaseName@HostName>` - 备份用于还原的数据库的名称，以及此数据库所在位置的“主机”/SAP HANA 服务器的名称。 `USING SOURCE <DatabaseName@HostName>` 选项指定数据备份（用于还原）是一个具有与目标 SAP HANA 计算机不同的 SID 或名称的数据库。 因此，不需要为采用备份的同一 HANA 服务器上的还原指定它。
        * `<PathToGeneratedCatalogInStep3>` - 在“步骤 3”中生成的指向目录文件的路径
        * `<DataFileDir>` - 包含完整备份的文件夹
        * `<LogFilesDir>` - 包含日志备份的文件夹
        * `<BackupIdFromJsonFile>` - 在“步骤 3”中提取的“BackupId” 

    * 要还原到特定完整备份或差异备份：

        如果要创建新的已还原数据库，请运行 HDBSQL 命令，以创建新的数据库 `<DatabaseName>`，然后停止用于还原的数据库。 但是，如果只是还原现有数据库，请运行 HDBSQL 命令来停止数据库：

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` - 新数据库或要还原的现有数据库的名称
        * `<Timestamp>` - 时间点还原的确切时间戳
        * `<DatabaseName@HostName>` - 备份用于还原的数据库的名称，以及此数据库所在位置的“主机”/SAP HANA 服务器的名称。 `USING SOURCE <DatabaseName@HostName>` 选项指定数据备份（用于还原）是一个具有与目标 SAP HANA 计算机不同的 SID 或名称的数据库。 因此，不需要为采用备份的同一 HANA 服务器上的还原指定它。
        * `<PathToGeneratedCatalogInStep3>` - 在“步骤 3”中生成的指向目录文件的路径
        * `<DataFileDir>` - 包含完整备份的文件夹
        * `<LogFilesDir>` - 包含日志备份的文件夹
        * `<BackupIdFromJsonFile>` - 在“步骤 3”中提取的“BackupId” 

## <a name="next-steps"></a>后续步骤

* 要了解如何使用 Azure CLI 管理备份的 SAP HANA 数据库，请继续学习教程[使用 CLI 在 Azure VM 中管理 SAP HANA 数据库](tutorial-sap-hana-backup-cli.md)

* 要了解如何使用 Azure 门户还原 Azure VM 中运行的 SAP HANA 数据库，请参阅[在 Azure VM 上还原 SAP HANA 数据库](./sap-hana-db-restore.md)
