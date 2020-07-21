---
title: 还原 Azure VM 上的 SAP HANA 数据库
description: 本文介绍如何还原在 Azure 虚拟机上运行的 SAP HANA 数据库。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: c62ea68683355fc703a5258e6e5fa0f3795f7e34
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86503585"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>还原 Azure VM 上的 SAP HANA 数据库

本文介绍如何还原 azure 备份服务已备份到恢复服务保管库的 Azure 虚拟机（VM）上运行的 SAP HANA 数据库。 可以通过还原来为开发/测试方案创建数据的副本，或返回到以前的状态。

有关如何备份 SAP HANA 数据库的详细信息，请参阅[备份 Azure VM 上的 SAP HANA 数据库](./backup-azure-sap-hana-database.md)。

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>还原到某个时间点或恢复点

Azure 备份可以还原在 Azure VM 上运行的 SAP HANA 数据库，如下所示：

* 使用日志备份还原到特定的日期或时间（精确到秒）。 Azure 备份可自动确定相应的完整备份、差异备份和日志链备份，这些是根据所选时间进行还原所必需的。

* 还原特定的完整备份或差异备份，这样就可以还原到特定的恢复点。

## <a name="prerequisites"></a>先决条件

在还原数据库之前，请注意以下事项：

* 只能将数据库还原到同一区域中的 SAP HANA 实例

* 目标实例必须注册到与源相同的保管库

* Azure 备份不能识别同一 VM 上两个不同的 SAP HANA 实例。 因此，不能将数据从同一 VM 上的一个实例还原到另一个实例。

* 若要确保目标 SAP HANA 实例已准备好进行还原，请检查其**备份就绪情况**状态：

  1. 打开在其中注册目标 SAP HANA 实例的保管库

  1. 在保管库仪表板上，在“开始使用”  下选择“备份” 

      ![保管库仪表板中的备份](media/sap-hana-db-restore/getting-started-backup.png)

  1. 在“备份”  中，在“你希望备份什么?”  下选择“Azure VM 中的 SAP HANA” 

      ![选择“Azure VM 中的 SAP HANA”](media/sap-hana-db-restore/sap-hana-backup.png)

  1. 在 "**发现 vm 中的 db**" 下，单击 "**查看详细信息**"

      ![查看详细信息](media/sap-hana-db-restore/view-details.png)

  1. 查看目标 VM 的**备份就绪情况**

      ![受保护的服务器](media/sap-hana-db-restore/protected-servers.png)

* 若要详细了解 SAP HANA 支持的还原类型，请参阅 SAP HANA 说明 [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>还原数据库

若要进行还原，需要以下权限：

* 在其中执行还原的保管库中的“备份操作员”权限。****
* 对已备份的源 VM 的**参与者（写入）** 访问权限。
* 针对目标 VM 的**参与者（写入**）访问权限：
  * 若要还原到同一 VM，则此项将是源 VM。
  * 若要还原到备用位置，则此项将是新的目标 VM。

1. 打开要在其中还原 SAP HANA 数据库的保管库

1. 在保管库仪表板上的 "**受保护项**" 下，选择 "**备份项**"

    ![备份项](media/sap-hana-db-restore/backup-items.png)

1. 在 "备份**项**" 下的 "**备份管理类型**" 下，选择**Azure VM 中的 SAP HANA**

    ![备份管理类型](media/sap-hana-db-restore/backup-management-type.png)

1. 选择要还原的数据库

    ![要还原的数据库](media/sap-hana-db-restore/database-to-restore.png)

1. 查看数据库菜单。 它提供有关数据库备份的信息，包括：

    * 最早和最新的还原点

    * 数据库的过去24和72小时的日志备份状态

    ![数据库菜单](media/sap-hana-db-restore/database-menu.png)

1. 选择**还原数据库**

1. 在 "**还原配置**" 下，指定还原数据的位置（或方法）：

    * **备用位置**：将数据库还原到备用位置，并保留原始源数据库。

    * **覆盖数据库**：将数据还原到与原始源相同的 SAP HANA 实例。 此选项会覆盖原始数据库。

      ![还原配置](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>还原到备用位置

1. 在 "**还原配置**" 菜单中的 "**还原位置**" 下，选择 "**备用位置**"。

    ![还原到备用位置](media/sap-hana-db-restore/restore-alternate-location.png)

1. 选择要将数据库还原到的 SAP HANA 主机名和实例名。
1. 检查目标 SAP HANA 实例是否已准备好进行还原，方法是确保其**备份就绪状态。** 有关更多详细信息，请参阅[先决条件部分](#prerequisites)。
1. 在“还原数据库名称”框中，输入目标数据库的名称。****

    > [!NOTE]
    > 单一数据库容器（SDC）还原必须遵循这些[检查](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)。

1. 如果适用，**如果所选 HANA 实例上已存在具有相同名称的数据库，请选择 "覆盖"**。
1. 选择“确定”。

    ![还原配置-最终屏幕](media/sap-hana-db-restore/restore-configuration-last.png)

1. 在“选择还原点”中选择“日志(时间点)”，以[还原到特定的时间点](#restore-to-a-specific-point-in-time)。******** 或者选择 "**完全 & 差异**" 以[还原到特定恢复点](#restore-to-a-specific-recovery-point)。

### <a name="restore-and-overwrite"></a>还原并覆盖

1. 在 "**还原配置**" 菜单中的 "**还原位置**" 下，选择 "**覆盖 DB**  >  **"**。

    ![覆盖 DB](media/sap-hana-db-restore/overwrite-db.png)

1. 在“选择还原点”中选择“日志(时间点)”，以[还原到特定的时间点](#restore-to-a-specific-point-in-time)。******** 或者选择 "**完全 & 差异**" 以[还原到特定恢复点](#restore-to-a-specific-recovery-point)。

### <a name="restore-as-files"></a>还原为文件

若要将备份数据还原为文件而不是数据库，请选择 "**还原为文件**"。 文件转储到指定路径后，可以将这些文件移动到要将其还原为数据库的任何 SAP HANA 计算机上。 由于可以将这些文件移动到任何计算机，你现在可以跨订阅和区域进行数据还原。

1. 在 "**还原配置**" 菜单中的 "**在何处和如何还原**" 下，选择 "**还原为文件**"。
1. 选择要将备份文件还原到的**主机**/HANA 服务器名称。
1. 在**服务器上的 "目标路径**" 中，输入在步骤2中选择的服务器上的文件夹路径。 此位置是服务要将全部所需备份文件转储到的位置。

    转储的文件包括：

    * 数据库备份文件
    * 目录文件
    * JSON 元数据文件（适用于所涉及的每个备份文件）

    通常，如果将网络共享路径或已装载的 Azure 文件共享的路径指定为目标路径，会使同一网络中的其他计算机或其上装载的同一 Azure 文件共享更加轻松地访问这些文件。

    >[!NOTE]
    >要在装载在目标注册的 VM 上的 Azure 文件共享上还原数据库备份文件，请确保根帐户对 Azure 文件共享具有读/写权限。

    ![选择目标路径](media/sap-hana-db-restore/restore-as-files.png)

1. 选择要将所有备份文件和文件夹还原到的**还原点**。

    ![选择还原点](media/sap-hana-db-restore/select-restore-point.png)

1. 与所选还原点关联的所有备份文件都将转储到目标路径中。
1. 根据所选的还原点类型（“时间点”或“完整和差异”），你将看到一个或多个在目标路径中创建的文件夹 。 名为 `Data_<date and time of restore>` 的文件夹之一包含完整备份和差异备份，名为 `Log` 的其他文件夹包含日志备份。
1. 将这些已还原文件移动到要将其还原为数据库的 SAP HANA 服务器。
1. 然后，执行以下步骤：
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
        * `<DatabaseName@HostName>` - 备份用于还原的数据库的名称，以及此数据库所在位置的“主机”/SAP HANA 服务器的名称。 `USING SOURCE <DatabaseName@HostName>` 选项指定数据备份（用于还原）是一个具有与目标 SAP HANA 计算机不同的 SID 或名称的数据库。 因此，不需要为从其创建备份的同一 HANA 服务器上执行的还原指定。
        * `<PathToGeneratedCatalogInStep3>`-在**步骤 C**中生成的目录文件的路径
        * `<DataFileDir>` - 包含完整备份的文件夹
        * `<LogFilesDir>` - 包含日志备份的文件夹
        * `<BackupIdFromJsonFile>`-在**步骤 C**中提取的**BackupId**

    * 要还原到特定完整备份或差异备份：

        如果要创建新的已还原数据库，请运行 HDBSQL 命令，以创建新的数据库 `<DatabaseName>`，然后停止用于还原的数据库。 但是，如果只是还原现有数据库，请运行 HDBSQL 命令来停止数据库：

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` - 新数据库或要还原的现有数据库的名称
        * `<Timestamp>` - 时间点还原的确切时间戳
        * `<DatabaseName@HostName>` - 备份用于还原的数据库的名称，以及此数据库所在位置的“主机”/SAP HANA 服务器的名称。 `USING SOURCE <DatabaseName@HostName>` 选项指定数据备份（用于还原）是一个具有与目标 SAP HANA 计算机不同的 SID 或名称的数据库。 因此，不需要为采用备份的同一 HANA 服务器上的还原指定它。
        * `<PathToGeneratedCatalogInStep3>`-在**步骤 C**中生成的目录文件的路径
        * `<DataFileDir>` - 包含完整备份的文件夹
        * `<LogFilesDir>` - 包含日志备份的文件夹
        * `<BackupIdFromJsonFile>`-在**步骤 C**中提取的**BackupId**

### <a name="restore-to-a-specific-point-in-time"></a>还原到特定时间点

如果已选择“日志(时间点)”作为还原类型，请执行以下操作：****

1. 从日志图中选择一个恢复点，然后选择 **"确定"** 以选择还原的时间点。

    ![还原点](media/sap-hana-db-restore/restore-point.png)

1. 在“还原”菜单中，选择“还原”启动还原作业。********

    ![选择还原](media/sap-hana-db-restore/restore-restore.png)

1. 通过选择 "数据库" 菜单上的 "**还原作业**"，在 "**通知**" 区域中跟踪还原进度或跟踪进度。

    ![已成功触发还原](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>还原到特定的恢复点

如果已选择“完整和差异”作为还原类型，请执行以下操作：****

1. 从列表中选择一个恢复点，然后选择 **"确定"** 以选择还原的时间点。

    ![还原特定恢复点](media/sap-hana-db-restore/specific-recovery-point.png)

1. 在“还原”菜单中，选择“还原”启动还原作业。********

    ![启动还原作业](media/sap-hana-db-restore/restore-specific.png)

1. 通过选择 "数据库" 菜单上的 "**还原作业**"，在 "**通知**" 区域中跟踪还原进度或跟踪进度。

    ![还原进度](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > 在将系统数据库还原到目标实例之后，多数据库容器（MDC）还原时，需要再次运行预注册脚本。 只有这样，随后的租户 DB 还原才会成功。 若要了解详细信息，请参阅[故障排除– MDC 还原](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)。

## <a name="next-steps"></a>后续步骤

* [了解如何](sap-hana-db-manage.md)使用 Azure 备份管理备份 SAP HANA 数据库
