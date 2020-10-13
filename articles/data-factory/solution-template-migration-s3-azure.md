---
title: 将数据从 Amazon S3 迁移到 Azure Data Lake Storage Gen2
description: 了解如何使用解决方案模板通过外部控制表从 Amazon S3 迁移数据，以使用 Azure 数据工厂在 AWS S3 上存储分区列表。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: e25299c2ce5d31da8f3caa5b02ab8def816b31ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91398214"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>将数据从 Amazon S3 迁移到 Azure Data Lake Storage Gen2

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

使用模板将数亿个文件构成的 PB 量级的数据从 Amazon S3 迁移到 Azure Data Lake Storage Gen2。 

 > [!NOTE]
 > 若要将少量数据（例如，少于 10 TB）从 AWS S3 复制到 Azure，更简单高效的方法是使用 [Azure 数据工厂复制数据工具](copy-data-tool.md)。 本文中所述模板的作用不仅仅是迁移数据。

## <a name="about-the-solution-templates"></a>关于解决方案模板

建议使用数据分区，尤其是在迁移 10 TB 以上的数据时。 若要将数据分区，请利用“前缀”设置按名称筛选 Amazon S3 中的文件夹和文件，然后，每个 ADF 复制作业一次可以复制一个分区。 可以并行运行多个 ADF 复制作业，以获得更好的吞吐量。

数据迁移通常需要进行一次性的历史数据迁移，并定期将 AWS S3 中的更改同步到 Azure。 下面提供了两个模板，其中一个模板执行一次性的历史数据迁移，另一个模板将 AWS S3 中的更改同步到 Azure。

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>用于将历史数据从 Amazon S3 迁移到 Azure Data Lake Storage Gen2 的模板

此模板（模板名称：将历史数据从 AWS S3 迁移到 Azure Data Lake Storage Gen2）假设已在 Azure SQL 数据库中的某个外部控制表内编写了分区列表。  因此，它将使用 *Lookup* 活动从外部控制表检索分区列表，遍历每个分区，并使每个 ADF 复制作业一次复制一个分区。 完成任一复制作业后，它将使用 *Stored Procedure* 活动来更新在控制表中复制每个分区的操作状态。

该模板包含五个活动：
- **Lookup** 从外部控制表中检索尚未复制到 Azure Data Lake Storage Gen2 的分区。 表名称为 *s3_partition_control_table*，用于从表中加载数据的查询为 *"SELECT PartitionPrefix FROM s3_partition_control_table WHERE SuccessOrFailure = 0"* 。
- **ForEach** 获取 *Lookup* 活动检索到的分区列表，然后将每个分区迭代到 *TriggerCopy* 活动。 可以设置 *batchCount* 以同时运行多个 ADF 复制作业。 我们在此模板中设置了 2。
- **ExecutePipeline** 执行 *CopyFolderPartitionFromS3* 管道。 我们之所以创建另一个管道来使每个复制作业复制一个分区，是因为这样可以轻松地重新运行失败的复制作业，以便再次从 AWS S3 中重新加载该特定分区。 加载其他分区的所有其他复制作业不受影响。
- **Copy** 将 AWS S3 中的每个分区复制到 Azure Data Lake Storage Gen2。
- **SqlServerStoredProcedure** 更新在控制表中复制每个分区的操作状态。

该模板包含两个参数：
- **AWS_S3_bucketName** 是 AWS S3 上要从中迁移数据的桶名称。 若要从 AWS S3 上的多个桶迁移数据，可以在外部控制表中额外添加一个列用于存储每个分区的桶名称，并更新管道以相应地从该列检索数据。
- **Azure_Storage_fileSystem** 是 Azure Data Lake Storage Gen2 上要将数据迁移到的文件系统名称。

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>用于仅将已更改的文件从 Amazon S3 复制到 Azure Data Lake Storage Gen2 的模板

此模板（模板名称：将增量数据从 AWS S3 复制到 Azure Data Lake Storage Gen2）使用每个文件的 LastModifiedTime，仅将新的或已更新的文件从 AWS S3 复制到 Azure。  请注意，如果 AWS S3 上的文件或文件夹已经过时间分区并在文件或文件夹名称中包含时间切片信息（例如 /yyyy/mm/dd/file.csv），可以转到[此教程](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)获取增量加载新文件的更高效方法。 此模板假设已在 Azure SQL 数据库中的外部控制表内编写了分区列表。 因此，它将使用 *Lookup* 活动从外部控制表检索分区列表，遍历每个分区，并使每个 ADF 复制作业一次复制一个分区。 当每个复制作业开始从 AWS S3 复制文件时，它依赖于使用 LastModifiedTime 属性来识别并仅复制新的或已更新的文件。 完成任一复制作业后，它将使用 *Stored Procedure* 活动来更新在控制表中复制每个分区的操作状态。

该模板包含七个活动：
- **Lookup** 从外部控制表检索分区。 表名称为 *s3_partition_delta_control_table*，用于从表中加载数据的查询为 *"select distinct PartitionPrefix from s3_partition_delta_control_table"* 。
- **ForEach** 获取 *Lookup* 活动检索到的分区列表，然后将每个分区迭代到 *TriggerDeltaCopy* 活动。 可以设置 *batchCount* 以同时运行多个 ADF 复制作业。 我们在此模板中设置了 2。
- **ExecutePipeline** 执行 *DeltaCopyFolderPartitionFromS3* 管道。 我们之所以创建另一个管道来使每个复制作业复制一个分区，是因为这样可以轻松地重新运行失败的复制作业，以便再次从 AWS S3 中重新加载该特定分区。 加载其他分区的所有其他复制作业不受影响。
- **Lookup** 从外部控制表检索上次复制作业的运行时间，以便可以通过 LastModifiedTime 识别新的或已更新的文件。 表名称为 *s3_partition_delta_control_table*，用于从表中加载数据的查询为 *"select max(JobRunTime) as LastModifiedTime from s3_partition_delta_control_table where PartitionPrefix = '@{pipeline().parameters.prefixStr}' and SuccessOrFailure = 1"* 。
- **Copy** 仅将 AWS S3 中每个分区的新文件或已更改的文件复制到 Azure Data Lake Storage Gen2。 *modifiedDatetimeStart* 的属性设置为上次复制作业的运行时间。 *modifiedDatetimeEnd* 的属性设置为当前复制作业的运行时间。 请注意，该时间采用 UTC 时区。
- **SqlServerStoredProcedure** 更新在控制表中复制每个分区的操作状态以及复制运行时间（如果操作成功）。 SuccessOrFailure 的列设置为 1。
- **SqlServerStoredProcedure** 更新在控制表中复制每个分区的操作状态以及复制运行时间（如果操作失败）。 SuccessOrFailure 的列设置为 0。

该模板包含两个参数：
- **AWS_S3_bucketName** 是 AWS S3 上要从中迁移数据的桶名称。 若要从 AWS S3 上的多个桶迁移数据，可以在外部控制表中额外添加一个列用于存储每个分区的桶名称，并更新管道以相应地从该列检索数据。
- **Azure_Storage_fileSystem** 是 Azure Data Lake Storage Gen2 上要将数据迁移到的文件系统名称。

## <a name="how-to-use-these-two-solution-templates"></a>如何使用这两个解决方案模板

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>用于将历史数据从 Amazon S3 迁移到 Azure Data Lake Storage Gen2 的模板 

1. 在 Azure SQL 数据库中创建一个用于存储 AWS S3 分区列表的控制表。 

    > [!NOTE]
    > 表名称为 s3_partition_control_table。
    > 控制表的架构为 PartitionPrefix 和 SuccessOrFailure，其中，PartitionPrefix 是 S3 中的前缀设置，用于按名称筛选 Amazon S3 中的文件夹和文件；SuccessOrFailure 是复制每个分区的操作状态：0 表示此分区尚未复制到 Azure，1 表示此分区已成功复制到 Azure。
    > 控制表中定义了 5 个分区，复制每个分区的默认操作状态为 0。

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

2. 在同一个 Azure SQL 数据库中为控制表创建一个存储过程。 

    > [!NOTE]
    > 该存储过程的名称为 sp_update_partition_success。 该存储过程将由 ADF 管道中的 SqlServerStoredProcedure 活动调用。

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. 转到“将历史数据从 AWS S3 迁移到 Azure Data Lake Storage Gen2”模板。  输入与外部控制表的连接，并输入 AWS S3 作为数据源存储，输入 Azure Data Lake Storage Gen2 作为目标存储。 请注意，外部控制表和存储过程引用同一连接。

    ![显示将历史数据从 AWS S3 迁移到 Azure Data Lake Storage Gen2 模板的屏幕截图。](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. 选择“使用此模板”  。

    ![突出显示 "使用此模板" 按钮的屏幕截图。](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. 将会看到已创建 2 个管道和 3 个数据集，如以下示例中所示：

    ![显示两个管道和三个使用该模板创建的数据集的屏幕截图。](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. 选择“调试”，输入**参数**，然后选择“完成”。  

    ![屏幕截图，显示选择 "调试" 的位置并输入参数，然后选择 "完成"。](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. 看到的结果类似于以下示例：

    ![显示返回的结果的屏幕截图。](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>用于仅将已更改的文件从 Amazon S3 复制到 Azure Data Lake Storage Gen2 的模板

1. 在 Azure SQL 数据库中创建一个用于存储 AWS S3 分区列表的控制表。 

    > [!NOTE]
    > 表名称为 s3_partition_delta_control_table。
    > 控制表的架构为 PartitionPrefix、JobRunTime SuccessOrFailure，其中，PartitionPrefix 是 S3 中的前缀设置，用于按名称筛选 Amazon S3 中的文件夹和文件；JobRunTime 是运行复制作业时的日期时间值；SuccessOrFailure 是复制每个分区的操作状态：0 表示此分区尚未复制到 Azure，1 表示此分区已成功复制到 Azure。
    > 控制表中定义了 5 个分区。 JobRunTime 的默认值可以是启动一次性历史数据迁移时的时间。 ADF 复制活动将复制 AWS S3 上的、在该时间后已修改过的文件。 复制每个分区的默认操作状态为 1。

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

2. 在同一个 Azure SQL 数据库中为控制表创建一个存储过程。 

    > [!NOTE]
    > 该存储过程的名称为 sp_insert_partition_JobRunTime_success。 该存储过程将由 ADF 管道中的 SqlServerStoredProcedure 活动调用。

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


3. 转到“将增量数据从 AWS S3 复制到 Azure Data Lake Storage Gen2”模板。  输入与外部控制表的连接，并输入 AWS S3 作为数据源存储，输入 Azure Data Lake Storage Gen2 作为目标存储。 请注意，外部控制表和存储过程引用同一连接。

    ![创建新连接](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. 选择“使用此模板”  。

    ![使用此模板](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. 将会看到已创建 2 个管道和 3 个数据集，如以下示例中所示：

    ![查看管道](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. 选择“调试”，输入**参数**，然后选择“完成”。  

    ![单击“调试”****](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. 看到的结果类似于以下示例：

    ![查看结果](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. 还可以通过查询 *"select * from s3_partition_delta_control_table"* 来检查控制表中的结果，将看到类似于以下示例的输出：

    ![在运行查询后显示来自控制表的结果的屏幕截图。](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>后续步骤

- [从多个容器复制文件](solution-template-copy-files-multiple-containers.md)
- [移动文件](solution-template-move-files.md)