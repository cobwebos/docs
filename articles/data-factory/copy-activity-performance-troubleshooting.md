---
title: 排查复制活动的性能问题
description: 了解如何排查 Azure 数据工厂中复制活动的性能问题。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: d339e68dcf49c74c508029fda3e7eb548ec92588
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84770945"
---
# <a name="troubleshoot-copy-activity-performance"></a>排查复制活动的性能问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何排查 Azure 数据工厂中复制活动的性能问题。 

运行复制活动后，可以在[复制活动监视](copy-activity-monitoring.md)视图中收集运行结果和性能统计信息。 下面是一个示例。

![监视复制活动运行详细信息](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>性能优化提示

在某些情况下，当你运行数据工厂中的复制活动时，将在顶部看到“性能优化提示”，如上面的示例中所示。 这些提示告知 ADF 针对此特定复制运行识别到的瓶颈，并建议如何提升复制吞吐量。 请尝试根据建议进行更改，然后再次运行复制。

作为参考，当前性能优化提示针对以下情况提供了建议：

| Category              | 性能优化提示                                      |
| --------------------- | ------------------------------------------------------------ |
| 特定于数据存储   | 将数据载入 **Azure Synpase Analytics（前称为 SQL 数据仓库）** ：建议使用 PolyBase；如果 PolyBase 不可用，则使用 COPY 语句。 |
| &nbsp;                | 从/向 **Azure SQL 数据库**复制数据：当 DTU 的利用率较高时，建议升级到更高的层。 |
| &nbsp;                | 从/向 **Azure Cosmos DB** 复制数据：当 RU 的利用率较高时，建议升级到更大的 RU。 |
|                       | 从 SAP 表复制数据：复制大量数据时，建议利用 SAP 连接器的分区选项启用并行加载并增加最大分区数。 |
| &nbsp;                | 从 **Amazon Redshift** 引入数据：如果未使用 UNLOAD，建议使用它。 |
| 数据存储限制 | 如果在复制期间数据存储限制了一些读/写操作，则建议检查并增大数据存储允许的请求速率，或减小并发工作负荷。 |
| 集成运行时  | 如果使用了**自承载集成运行时 (IR)** ，而复制活动在队列中长时间等待，直到 IR 提供了用于执行该活动的资源，则建议横向/纵向扩展 IR。 |
| &nbsp;                | 如果使用了非最佳区域中的 **Azure Integration Runtime**，导致读/写速度缓慢，则建议配置为使用另一区域中的 IR。 |
| 容错       | 如果配置了容错并跳过不兼容的行，导致性能变慢，则建议确保源和接收器数据兼容。 |
| 暂存复制           | 如果配置了分阶段复制，但此方法对于源-接收器对不起作用，则建议删除此方法。 |
| 恢复                | 如果复制活动已从上一故障点恢复，但你在完成原始运行后正好更改了 DIU 设置，请注意，新的 DIU 设置不会生效。 |

## <a name="understand-copy-activity-execution-details"></a>了解复制活动执行详细信息

复制活动监视视图底部的执行详细信息和持续时间描述了复制活动所要经历的重要阶段（请参阅本文开头的示例），这对于排查复制性能问题特别有用。 复制运行的瓶颈就是持续时间最长的那个运行。 请参阅下表中每个阶段的定义，并了解如何使用此类信息[排查 Azure IR 中的复制活动的问题](#troubleshoot-copy-activity-on-azure-ir)和[排查自承载 IR 中的复制活动的问题](#troubleshoot-copy-activity-on-self-hosted-ir)。

| 阶段           | 说明                                                  |
| --------------- | ------------------------------------------------------------ |
| 队列           | 复制活动在集成运行时中实际启动之前所消逝的时间。 |
| 复制前脚本 | 复制活动在 IR 中启动之后、在接收器数据存储中执行完复制前脚本之前所消逝的时间。 为数据库接收器配置复制前脚本时适用，例如，将数据写入 Azure SQL 数据库会在复制新数据之前执行清理。 |
| 传输        | 完成前一步骤之后、在 IR 将所有数据从源传输到接收器之前所消逝的时间。 <br/>请注意，传输中的子步骤会并行运行，某些操作（例如，分析/生成文件格式）现在未显示。<br><br/>- **距第一字节的时间：** 在前一步骤结束之后、IR 从源数据存储收到第一个字节之前所经过的时间。 适用于不是基于文件的源。<br>- **列出源：** 枚举源文件或数据分区所花费的时间。 后者适用于为数据库源配置分区选项时，例如，从 Oracle/SAP HANA/Teradata/Netezza 等数据库复制数据时。<br/>-**从源中读取：** 从源数据存储检索数据所花费的时间。<br/>- **写入接收器：** 将数据写入接收器数据存储所花费的时间。 请注意，某些连接器目前没有此指标，包括 Azure 认知搜索、Azure 数据资源管理器、Azure 表存储、Oracle、SQL Server、Common Data Service、Dynamics 365、Dynamics CRM、Salesforce/Salesforce 服务云。 |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>排查 Azure IR 中的复制活动的问题

遵循[性能优化步骤](copy-activity-performance.md#performance-tuning-steps)为方案规划并执行性能测试。 

当复制活动性能不符合预期时，若要排查 Azure Integration Runtime 中运行的单个复制活动的问题，在看到复制监视视图中显示了[性能优化提示](#performance-tuning-tips)的情况下，请应用建议并重试。 否则，请[了解复制活动执行详细信息](#understand-copy-activity-execution-details)，检查哪个阶段的持续时间**最长**，并应用以下指导以提升复制性能：

- **“复制前脚本”的持续时间较长：** 表示接收器数据库中运行的复制前脚本花费了较长时间来完成。 优化指定的复制前脚本逻辑，以增强性能。 如果在改进脚本方面需要更多的帮助，请与数据库团队联系。

- **“传输 - 距第一字节的时间”的工作持续时间较长：** 表示源查询花费了较长时间来返回任何数据。 检查并优化查询或服务器。 如需更多帮助，请与数据存储团队联系。

- **“传输 - 列出源”的工作持续时间较长：** 表示枚举源文件或源数据库数据分区的速度缓慢。
  - 从基于文件的源复制数据时，如果对文件夹路径或文件名使用**通配符筛选器**（`wildcardFolderPath` 或 `wildcardFileName`），或使用**文件上次修改时间筛选器**（`modifiedDatetimeStart` 或 `modifiedDatetimeEnd`），请注意，此类筛选器会导致复制活动在客户端中列出指定文件夹下的所有文件，然后应用筛选器。 此类文件枚举可能会变成瓶颈，尤其是只有少量的文件符合筛选规则时。

    - 检查是否可以[基于按日期时间分区的文件路径或名称复制文件](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)。 这不会在“列出源”端带来负担。

    - 检查是否可以改用数据存储的本机筛选器，具体而言，是否可以使用 Amazon S3 和 Azure Blob 的“**前缀**”。 前缀筛选器是一个数据存储服务器端筛选器，其性能要好得多。

    - 考虑将单个大型数据集拆分为多个小型数据集，并让每个并发运行的复制作业处理一部分数据。 为此，可以使用 Lookup/GetMetadata + ForEach + Copy。 请参阅[从多个容器复制文件](solution-template-copy-files-multiple-containers.md)或[将数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md) 解决方案模板，其中提供了一般性的示例。

  - 检查 ADF 是否报告了源中的任何限制错误，或者数据存储是否处于高利用率状态。 如果是，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员来提高限制或增加可用资源。

  - 使用同一源数据存储区域或者与之靠近的区域中的 Azure IR。

- **“传输 - 从源读取”的工作持续时间较长：** 

  - 采用特定于连接器的数据加载最佳做法（如果适用）。 例如，从 [Amazon Redshift](connector-amazon-redshift.md) 复制数据时，请配置为使用 Redshift UNLOAD。

  - 检查 ADF 是否报告了源中的任何限制错误，或者数据存储是否处于高利用率状态。 如果是，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员来提高限制或增加可用资源。

  - 检查复制源和接收器模式： 

    - 如果复制模式支持 4 个以上的数据集成单位 (DIU) - 请参阅[此部分](copy-activity-performance-features.md#data-integration-units)中的详细信息，一般情况下，可以尝试增加 DIU 以获得更好的性能。 

    - 否则，请考虑将单个大型数据集拆分为多个小型数据集，并让每个并发运行的复制作业处理一部分数据。 为此，可以使用 Lookup/GetMetadata + ForEach + Copy。 请参阅[从多个容器复制文件](solution-template-copy-files-multiple-containers.md)、[将数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md) 或[使用控制表进行批量复制](solution-template-bulk-copy-with-control-table.md)解决方案模板，其中提供了一般性的示例。

  - 使用同一源数据存储区域中或者与之靠近的区域中的 Azure IR。

- **“传输 - 写入接收器”的工作持续时间较长：**

  - 采用特定于连接器的数据加载最佳做法（如果适用）。 例如，将数据复制到 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)（前称为 SQL 数据仓库）时，请使用 PolyBase 或 COPY 语句。 

  - 检查 ADF 是否报告了接收器中的任何限制错误，或者数据存储是否处于高利用率状态。 如果是，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员来提高限制或增加可用资源。

  - 检查复制源和接收器模式： 

    - 如果复制模式支持 4 个以上的数据集成单位 (DIU) - 请参阅[此部分](copy-activity-performance-features.md#data-integration-units)中的详细信息，一般情况下，可以尝试增加 DIU 以获得更好的性能。 

    - 否则，请逐步优化[并行复制](copy-activity-performance-features.md)，同时请注意，过多的并行复制可能会进一步损害性能。

  - 使用同一接收器数据存储区域中或者与之靠近的区域中的 Azure IR。

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>排查自承载 IR 中的复制活动的问题

遵循[性能优化步骤](copy-activity-performance.md#performance-tuning-steps)为方案规划并执行性能测试。 

当复制性能不符合预期时，若要排查 Azure Integration Runtime 中运行的单个复制活动的问题，在看到复制监视视图中显示了[性能优化提示](#performance-tuning-tips)的情况下，请应用建议并重试。 否则，请[了解复制活动执行详细信息](#understand-copy-activity-execution-details)，检查哪个阶段的持续时间**最长**，并应用以下指导以提升复制性能：

- **“队列”持续时间较长：** 表示复制活动在队列中长时间等待，直到自承载 IR 提供了用于执行该活动的资源。 检查 IR 容量和使用率，并根据工作负荷进行[纵向或横向扩展](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

- **“传输 - 距第一字节的时间”的工作持续时间较长：** 表示源查询花费了较长时间来返回任何数据。 检查并优化查询或服务器。 如需更多帮助，请与数据存储团队联系。

- **“传输 - 列出源”的工作持续时间较长：** 表示枚举源文件或源数据库数据分区的速度缓慢。

  - 检查自承载 IR 计算机是否以较低的延迟连接到源数据存储。 如果源位于 Azure 中，你可以使用[此工具](http://www.azurespeed.com/Azure/Latency)检查自承载 IR 计算机与 Azure 区域之间的连接延迟，延迟值越小越好。

  - 从基于文件的源复制数据时，如果对文件夹路径或文件名使用**通配符筛选器**（`wildcardFolderPath` 或 `wildcardFileName`），或使用**文件上次修改时间筛选器**（`modifiedDatetimeStart` 或 `modifiedDatetimeEnd`），请注意，此类筛选器会导致复制活动在客户端中列出指定文件夹下的所有文件，然后应用筛选器。 此类文件枚举可能会变成瓶颈，尤其是只有少量的文件符合筛选规则时。

    - 检查是否可以[基于按日期时间分区的文件路径或名称复制文件](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)。 这不会在“列出源”端带来负担。

    - 检查是否可以改用数据存储的本机筛选器，具体而言，是否可以使用 Amazon S3 和 Azure Blob 的“**前缀**”。 前缀筛选器是一个数据存储服务器端筛选器，其性能要好得多。

    - 考虑将单个大型数据集拆分为多个小型数据集，并让每个并发运行的复制作业处理一部分数据。 为此，可以使用 Lookup/GetMetadata + ForEach + Copy。 请参阅[从多个容器复制文件](solution-template-copy-files-multiple-containers.md)或[将数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md) 解决方案模板，其中提供了一般性的示例。

  - 检查 ADF 是否报告了源中的任何限制错误，或者数据存储是否处于高利用率状态。 如果是，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员来提高限制或增加可用资源。

- **“传输 - 从源读取”的工作持续时间较长：** 

  - 检查自承载 IR 计算机是否以较低的延迟连接到源数据存储。 如果源位于 Azure 中，你可以使用[此工具](http://www.azurespeed.com/Azure/Latency)检查自承载 IR 计算机与 Azure 区域之间的连接延迟，延迟值越小越好。

  - 检查自承载 IR 计算机是否具有足够的入站带宽，可以有效地读取和传输数据。 如果源数据存储位于 Azure 中，你可以使用[此工具](https://www.azurespeed.com/Azure/Download)检查下载速度。

  - 在 Azure 门户 -> 数据工厂 -> 概述页中检查自承载 IR 的 CPU 和内存使用趋势。 如果 CPU 使用率较高或可用内存不足，请考虑[纵向/横向扩展 IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

  - 采用特定于连接器的数据加载最佳做法（如果适用）。 例如：

    - 从 [Oracle](connector-oracle.md#oracle-as-source)、[Netezza](connector-netezza.md#netezza-as-source)、[Teradata](connector-teradata.md#teradata-as-source)、[SAP HANA](connector-sap-hana.md#sap-hana-as-source)、[SAP Table](connector-sap-table.md#sap-table-as-source) 和 [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) 复制数据时，请启用数据分区选项以并行复制数据。

    - 从 [HDFS](connector-hdfs.md)复制数据时，请配置为使用 DistCp。

    - 从 [Amazon Redshift](connector-amazon-redshift.md) 复制数据时，请配置为使用 Redshift UNLOAD。

  - 检查 ADF 是否报告了源中的任何限制错误，或者数据存储是否处于高利用率状态。 如果是，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员来提高限制或增加可用资源。

  - 检查复制源和接收器模式： 

    - 如果从已启用分区选项的数据存储复制数据，请考虑逐步优化[并行复制](copy-activity-performance-features.md)，同时请注意，过多的并行复制可能会进一步损害性能。

    - 否则，请考虑将单个大型数据集拆分为多个小型数据集，并让每个并发运行的复制作业处理一部分数据。 为此，可以使用 Lookup/GetMetadata + ForEach + Copy。 请参阅[从多个容器复制文件](solution-template-copy-files-multiple-containers.md)、[将数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md) 或[使用控制表进行批量复制](solution-template-bulk-copy-with-control-table.md)解决方案模板，其中提供了一般性的示例。

- **“传输 - 写入接收器”的工作持续时间较长：**

  - 采用特定于连接器的数据加载最佳做法（如果适用）。 例如，将数据复制到 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)（前称为 SQL 数据仓库）时，请使用 PolyBase 或 COPY 语句。 

  - 检查自承载 IR 计算机是否以较低的延迟连接到接收器数据存储。 如果接收器位于 Azure 中，你可以使用[此工具](http://www.azurespeed.com/Azure/Latency)检查自承载 IR 计算机与 Azure 区域之间的连接延迟，延迟值越小越好。

  - 检查自承载 IR 计算机是否具有足够的出站带宽，可以有效地传输和写入数据。 如果接收器数据存储位于 Azure 中，你可以使用[此工具](https://www.azurespeed.com/Azure/UploadLargeFile)检查上传速度。

  - 在 Azure 门户 -> 数据工厂 -> 概述页中检查自承载 IR 的 CPU 和内存使用趋势。 如果 CPU 使用率较高或可用内存不足，请考虑[纵向/横向扩展 IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

  - 检查 ADF 是否报告了接收器中的任何限制错误，或者数据存储是否处于高利用率状态。 如果是，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员来提高限制或增加可用资源。

  - 考虑逐步优化[并行复制](copy-activity-performance-features.md)，同时请注意，过多的并行复制可能会进一步损害性能。

## <a name="other-references"></a>其他参考资料

下面是有关一些受支持数据存储的性能监视和优化参考：

* Azure Blob 存储：[Blob 存储的可伸缩性和性能目标](../storage/blobs/scalability-targets.md)和 [Blob 存储的性能与可伸缩性查检表](../storage/blobs/storage-performance-checklist.md)。
* Azure 表存储：[表存储的可伸缩性和性能目标](../storage/tables/scalability-targets.md)和[表存储的性能与可伸缩性查检表](../storage/tables/storage-performance-checklist.md)。
* Azure SQL 数据库：可[监视性能](../sql-database/sql-database-single-database-monitor.md)并检查数据库事务单位 (DTU) 百分比。
* Azure SQL 数据仓库：其功能以数据仓库单位 (DWU) 衡量。 请参阅[管理 Azure SQL 数据仓库中的计算能力（概述）](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。
* Azure Cosmos DB：[Azure Cosmos DB 中的性能级别](../cosmos-db/performance-levels.md)。
* SQL Server：[性能监视和优化](https://msdn.microsoft.com/library/ms189081.aspx)。
* 本地文件服务器：[文件服务器性能优化](https://msdn.microsoft.com/library/dn567661.aspx)。

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能和可伸缩性指南](copy-activity-performance.md)
- [复制活动性能优化功能](copy-activity-performance-features.md)
- [使用 Azure 数据工厂将数据从 Data Lake 或数据仓库迁移到 Azure](data-migration-guidance-overview.md)
- [将数据从 Amazon S3 迁移到 Azure 存储](data-migration-guidance-s3-azure-storage.md)
