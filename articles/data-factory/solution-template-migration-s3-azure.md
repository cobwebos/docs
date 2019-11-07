---
title: 通过 Azure 数据工厂将数据从 Amazon S3 迁移到 Azure Data Lake Storage Gen2
description: 了解如何使用解决方案模板从 Amazon S3 迁移数据，使用外部控制表将 AWS S3 上的分区列表存储到 Azure 数据工厂。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/07/2019
ms.openlocfilehash: a8591762bf4e8eccd5e1b7d67538674feed720b9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684192"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>将数据从 Amazon S3 迁移到 Azure Data Lake Storage Gen2

使用模板将包含数百个数百万文件的数据量从 Amazon S3 迁移到 Azure Data Lake Storage Gen2。 

 > [!NOTE]
 > 如果你想要将小型数据卷从 AWS S3 复制到 Azure （例如，小于 10 TB），使用[Azure 数据工厂复制数据工具](copy-data-tool.md)会更高效、更轻松。 本文中所述的模板大于所需的模板。

## <a name="about-the-solution-templates"></a>关于解决方案模板

建议使用数据分区，尤其是在迁移 10 TB 以上的数据时。 若要对数据进行分区，请利用 "前缀" 设置按名称筛选 Amazon S3 上的文件夹和文件，然后每个 ADF 复制作业可以一次复制一个分区。 可以并行运行多个 ADF 复制作业，以获得更好的吞吐量。

数据迁移通常需要一次性的历史数据迁移，并定期将更改从 AWS S3 同步到 Azure。 下面是两个模板，其中一个模板涵盖一次历史数据迁移，另一个模板涉及将更改从 AWS S3 同步到 Azure。

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>用于将历史数据从 Amazon S3 迁移到 Azure Data Lake Storage Gen2 的模板

此模板（*模板名称：将 AWS S3 的历史数据迁移到 Azure Data Lake Storage Gen2*）假设已在 Azure SQL 数据库的外部控制表中编写了一个分区列表。 因此，它将使用*查找*活动从外部控制表检索分区列表，遍历每个分区，并使每个 ADF 复制作业一次复制一个分区。 完成任何复制作业后，它将使用*存储过程*活动来更新在控制表中复制每个分区的状态。

该模板包含五个活动：
- **查找**将检索尚未从外部控制表复制到 Azure Data Lake Storage Gen2 的分区。 表名为*s3_partition_control_table* ，用于从表中加载数据的查询为 *"SELECT PARTITIONPREFIX From s3_partition_control_table WHERE SuccessOrFailure = 0"* 。
- **ForEach**从*查找*活动获取分区列表，并将每个分区循环访问*TriggerCopy*活动。 可以将*batchCount*设置为同时运行多个 ADF 复制作业。 此模板中设置了2。
- **ExecutePipeline**执行*CopyFolderPartitionFromS3*管道。 由于我们创建另一个管道来使每个复制作业都复制一个分区，这是因为它可以让你轻松地重新运行失败的复制作业，以再次从 AWS S3 重新加载该特定分区。 加载其他分区的所有其他复制作业不受影响。
- **复制**将 AWS S3 中的每个分区复制到 Azure Data Lake Storage Gen2。
- **SqlServerStoredProcedure**更新控制表中每个分区的复制状态。

该模板包含两个参数：
- **AWS_S3_bucketName**是要从中迁移数据的 AWS S3 上的 bucket 名称。 如果要迁移 AWS S3 上多个存储桶中的数据，则可以在外部控制表中另外添加一个列，以存储每个分区的存储桶名称，还可以更新管道以便相应地从该列中检索数据。
- **Azure_Storage_fileSystem**是要将数据迁移到 Azure Data Lake Storage Gen2 的文件系统名称。

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>要使模板仅将更改的文件从 Amazon S3 复制到 Azure Data Lake Storage Gen2

此模板（*模板名称：将增量数据从 AWS S3 复制到 Azure Data Lake Storage Gen2*）使用每个文件的 LastModifiedTime 将新的或更新的文件从 AWS S3 复制到 Azure。 请注意，如果你的文件或文件夹已按时间间隔信息（作为 AWS S3 上的文件或文件夹名称的一部分）分区（例如，/yyyy/mm/dd/file.csv），则可以转到本[教程](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)获取更高性能的增量方法正在加载新文件。 此模板假设已在 Azure SQL 数据库的外部控制表中编写分区列表。 因此，它将使用*查找*活动从外部控制表检索分区列表，遍历每个分区，并使每个 ADF 复制作业一次复制一个分区。 当每个复制作业开始复制 AWS S3 中的文件时，它依赖于 LastModifiedTime 属性来识别并复制新的或更新的文件。 完成任何复制作业后，它将使用*存储过程*活动来更新在控制表中复制每个分区的状态。

该模板包含七个活动：
- **Lookup**从外部控制表检索分区。 表名为*s3_partition_delta_control_table* ，用于从表中加载数据的查询是 *"select distinct PartitionPrefix from s3_partition_delta_control_table"* 。
- **ForEach**从*查找*活动获取分区列表，并将每个分区循环访问*TriggerDeltaCopy*活动。 可以将*batchCount*设置为同时运行多个 ADF 复制作业。 此模板中设置了2。
- **ExecutePipeline**执行*DeltaCopyFolderPartitionFromS3*管道。 由于我们创建另一个管道来使每个复制作业都复制一个分区，这是因为它可以让你轻松地重新运行失败的复制作业，以再次从 AWS S3 重新加载该特定分区。 加载其他分区的所有其他复制作业不受影响。
- **Lookup**从外部控制表检索上次复制作业运行时间，以便可以通过 LastModifiedTime 识别新文件或更新文件。 表名称是*s3_partition_delta_control_table* ，而从表加载数据的查询是 *"Select max （JobRunTime） as LastModifiedTime From s3_partition_delta_control_table where PartitionPrefix = ' @ {管道（）. prefixStr} "和 SuccessOrFailure = 1"* 。
- 仅将 AWS S3 的每个分区的新文件或已更改的文件**复制**到 Azure Data Lake Storage Gen2。 *ModifiedDatetimeStart*的属性被设置为最后一个复制作业运行时。 *ModifiedDatetimeEnd*的属性设置为当前复制作业运行时间。 请注意，将时间应用于 UTC 时区。
- **SqlServerStoredProcedure**更新复制每个分区的状态，并在控件表中复制运行时复制成功。 SuccessOrFailure 的列设置为1。
- **SqlServerStoredProcedure**更新复制每个分区的状态并在控制表中复制运行时间失败。 SuccessOrFailure 的列设置为0。

该模板包含两个参数：
- **AWS_S3_bucketName**是要从中迁移数据的 AWS S3 上的 bucket 名称。 如果要迁移 AWS S3 上多个存储桶中的数据，则可以在外部控制表中另外添加一个列，以存储每个分区的存储桶名称，还可以更新管道以便相应地从该列中检索数据。
- **Azure_Storage_fileSystem**是要将数据迁移到 Azure Data Lake Storage Gen2 的文件系统名称。

## <a name="how-to-use-these-two-solution-templates"></a>如何使用这两个解决方案模板

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>用于将历史数据从 Amazon S3 迁移到 Azure Data Lake Storage Gen2 的模板 

1. 在 Azure SQL 数据库中创建一个控制表，用于存储 AWS S3 的分区列表。 

    > [!NOTE]
    > 表名为 s3_partition_control_table。
    > 控制表的架构是 PartitionPrefix 和 SuccessOrFailure，其中 PartitionPrefix 是 S3 中的前缀设置，用于按名称筛选 Amazon S3 中的文件夹和文件，SuccessOrFailure 是复制每个分区的状态：0表示此分区具有未复制到 Azure，1表示此分区已成功复制到 Azure。
    > 控制表中定义了5个分区，并且复制每个分区的默认状态为0。

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. 在控制表的同一个 Azure SQL 数据库中创建一个存储过程。 

    > [!NOTE]
    > 存储过程的名称为 sp_update_partition_success。 它将由 ADF 管道中的 SqlServerStoredProcedure 活动调用。

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. 请参阅将**历史数据从 AWS S3 迁移到 Azure Data Lake Storage Gen2**模板。 输入与外部控制表的连接，将 AWS S3 作为数据源存储，并 Azure Data Lake Storage Gen2 作为目标存储。 请注意，外部控制表和存储过程引用相同的连接。

    ![创建新连接](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. 选择“使用此模板”。

    ![使用此模板](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. 将看到2个管道和3个数据集已创建，如以下示例中所示：

    ![查看管道](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. 选择“调试”，输入**参数**，然后选择“完成”。

    ![单击“调试”****](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. 看到的结果类似于以下示例：

    ![查看结果](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>要使模板仅将更改的文件从 Amazon S3 复制到 Azure Data Lake Storage Gen2

1. 在 Azure SQL 数据库中创建一个控制表，用于存储 AWS S3 的分区列表。 

    > [!NOTE]
    > 表名为 s3_partition_delta_control_table。
    > 控制表的架构为 PartitionPrefix、JobRunTime 和 SuccessOrFailure，其中 PartitionPrefix 是 S3 中的前缀设置，用于按名称筛选 Amazon S3 中的文件夹和文件，JobRunTime 是复制作业运行时的日期时间值，SuccessOrFailure 是复制每个分区的状态：0表示此分区尚未复制到 Azure，1表示此分区已成功复制到 Azure。
    > 控制表中定义了5个分区。 JobRunTime 的默认值可以是一次启动一次历史数据迁移的时间。 ADF 复制活动将复制 AWS S3 上经过一段时间后最后修改的文件。 复制每个分区的默认状态为1。

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. 在控制表的同一个 Azure SQL 数据库中创建一个存储过程。 

    > [!NOTE]
    > 存储过程的名称为 sp_insert_partition_JobRunTime_success。 它将由 ADF 管道中的 SqlServerStoredProcedure 活动调用。

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. 请参阅将**增量数据从 AWS S3 复制到 Azure Data Lake Storage Gen2**模板。 输入与外部控制表的连接，将 AWS S3 作为数据源存储，并 Azure Data Lake Storage Gen2 作为目标存储。 请注意，外部控制表和存储过程引用相同的连接。

    ![创建新连接](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. 选择“使用此模板”。

    ![使用此模板](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. 将看到2个管道和3个数据集已创建，如以下示例中所示：

    ![查看管道](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. 选择“调试”，输入**参数**，然后选择“完成”。

    ![单击“调试”****](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. 看到的结果类似于以下示例：

    ![查看结果](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. 您还可以通过查询 *"select * from s3_partition_delta_control_table"* 检查控制表中的结果，您将看到类似于以下示例的输出：

    ![查看结果](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>后续步骤

- [从多个容器复制文件](solution-template-copy-files-multiple-containers.md)
- [移动文件](solution-template-move-files.md)