---
title: Microsoft Azure 备份服务器 v3 发行说明
description: 本文介绍 Microsoft Azure 备份服务器 (MABS) v3 的已知问题和解决方法。
ms.topic: conceptual
ms.date: 06/03/2020
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 87bc415c125a387d98ac88255d77fb1867564acf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254255"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Microsoft Azure 备份服务器发行说明

本文提供了 Microsoft Azure 备份服务器 (MABS) V3 的已知的问题和解决方法。

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>群集化工作负荷的备份和恢复失败

**说明：** 将 MABS V2 升级到 MABS V3 之后，群集化数据源（例如数据库可用性组 (DAG)中的 Hyper-V 群集或 SQL 群集 (SQL Always On) 或 Exchange）的备份/还原失败。

**解决方法：** 若要防止出现此问题，请打开 SQL Server Management Studio (SSMS)) 并在 DPM DB 上运行以下 SQL 脚本：

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>升级到 MABS V3 在俄罗斯语区域设置中失败

**说明：** 从 MABS V2 升级到 MABS V3 在俄罗斯语区域设置中失败并且出现错误代码 **4387**。

**解决方法：** 执行以下步骤以使用俄罗斯语安装包升级到 MABS V3：

1. [备份](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server#SSMSProcedure)你的 SQL 数据库并卸载 MABS V2（在卸载期间选择保留受保护的数据）。
2. 在升级过程中升级到 SQL 2017（企业版）并卸载报告功能。
3. [安装](/sql/reporting-services/install-windows/install-reporting-services#install-your-report-server) SQL Server Reporting Services (SSRS)。
4. [安装](/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS)。
5. 如[使用 SQL 2017 时的 SSRS 配置](./backup-azure-microsoft-azure-backup.md#upgrade-mabs)中所述使用参数配置报告功能。
6. [安装](backup-azure-microsoft-azure-backup.md) MABS V3。
7. 使用 SSMS [还原](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms) SQL 并如[此处](/system-center/dpm/back-up-the-dpm-server#using-dpmsync)所述运行 DPM-Sync 工具。
8. 使用以下命令更新 dbo.tbl_DLS_GlobalSetting 表中的“DataBaseVersion”属性：

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. 启动 MSDPM 服务。

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>安装 UR1 后，未使用新的 RDL 文件更新 MABS 报表

**说明**：通过 UR1，已使用更新的 RDL 文件修复了 MABS 报表格式设置问题。 这些新 RDL 文件不会自动替换为现有文件。

**解决方法**：若要替换这些 RDL 文件，请按照以下步骤操作：

1. 在 MABS 计算机上，打开 SQL Reporting Services Web 门户 URL。
1. 在 Web 门户 URL 上，DPMReports 文件夹以 `DPMReports_<GUID>` 的格式存在

    >[!NOTE]
    >始终只有一个文件夹具有此命名约定。 如果 MABS 是从以前的版本升级的，则可能还存在另一个旧文件夹，但无法将其打开。

    ![DPMReports 文件夹](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. 选择并打开“`DPMReports_<GUID>`”文件夹。 将会列出各个报表文件，如下所示。

    ![各个报表文件的列表](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. 选择不以“Report”结尾的报表文件，右键单击“选项”并选择“管理”  。

    ![为报表文件选择“管理”](./media/backup-mabs-release-notes-v3/manage-files.png)

1. 在新页面上，选择“替换”选项，以将这些文件替换为最新的报表文件。

    可在路径 `<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports` 中找到最新的报表文件

    例如：`C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![将这些文件替换为最新的报表文件](./media/backup-mabs-release-notes-v3/replace-files.png)

    替换文件后，请确保“名称”和“说明”保持不变且不为空 。

1. 替换文件后，请重新启动 MABS 服务并使用这些报表文件。

## <a name="next-steps"></a>后续步骤

[MABS 中的新增功能](backup-mabs-whats-new-mabs.md)
