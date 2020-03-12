---
title: 复制活动性能疑难解答
description: 了解如何对 Azure 数据工厂中的复制活动性能进行故障排除。
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
ms.date: 03/11/2020
ms.openlocfilehash: dd0343fc2e25a50f9aa9a9fdc3ef5ebb9615bc56
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79125766"
---
# <a name="troubleshoot-copy-activity-performance"></a>复制活动性能疑难解答

本文概述了如何排查 Azure 数据工厂中的复制活动性能问题。 

运行复制活动后，可以在[复制活动监视](copy-activity-monitoring.md)视图中收集运行结果和性能统计信息。 下面是一个示例。

![监视复制活动运行详细信息](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>性能优化提示

在某些情况下，当你在数据工厂中运行复制活动时，将在顶部看到 **"性能优化提示"** ，如上面的示例所示。 提示会告诉您此特定副本运行的 ADF 所识别的瓶颈，并建议如何提高复制吞吐量。 尝试进行 recommanded 更改，然后再次运行复制。

作为参考，当前性能优化提示为以下情况提供建议：

| 类别              | 性能优化提示                                      |
| --------------------- | ------------------------------------------------------------ |
| 特定的数据存储   | 将数据加载到**Azure Synpase Analytics （以前称为 SQL DW）** ：如果未使用 POLYBASE 或 COPY 语句，建议使用该语句。 |
| &nbsp;                | 从/向**AZURE SQL 数据库**复制数据：当 DTU 的利用率较高时，建议升级到更高的层。 |
| &nbsp;                | 将数据从/复制到**Azure Cosmos DB**： ru 处于高利用率下时，建议升级到更大的 ru。 |
| &nbsp;                | 来自**Amazon Redshift**的引入数据：如果未使用，则建议使用 UNLOAD。 |
| 数据存储限制 | 如果在复制期间数据存储限制了多个读/写操作，则建议检查并增加数据存储的允许请求速率，或者减少并发工作负荷。 |
| 集成运行时  | 如果使用**自承载 Integration Runtime （IR）** 并在队列中复制活动等待时间较长，直到 ir 具有可用资源才能执行，则建议向外缩放/向上缩放 IR。 |
| &nbsp;                | 如果你使用的是在非最佳区域中导致读取/写入速度变慢的**Azure Integration Runtime** ，则建议将配置为使用另一个区域中的 IR。 |
| 容错       | 如果配置容错，并跳过不兼容的行导致性能变慢，则建议确保源和接收器数据兼容。 |
| 暂存复制           | 如果配置了暂存复制但对源接收器对不起作用，则建议删除它。 |
| 恢复                | 如果从上一故障点恢复复制活动，但在原始运行后更改了 DIU 设置，请注意，new DIU 设置不会生效。 |

## <a name="understand-copy-activity-execution-details"></a>了解复制活动执行详细信息

"复制活动监视" 视图底部的 "执行详细信息" 和 "持续时间" 描述了复制活动要经历的关键阶段（请参阅本文开头的示例），这对于排查复制性能问题尤其有用。 复制运行的瓶颈是持续时间最长的一个。 请参阅每个阶段的定义中的下表，了解如何对[Azure IR 上的复制活动进行故障排除](#troubleshoot-copy-activity-on-azure-ir)，以及如何通过此类信息[对自承载 IR 上的复制活动进行故障排除](#troubleshoot-copy-activity-on-self-hosted-ir)。

| 阶段           | 说明                                                  |
| --------------- | ------------------------------------------------------------ |
| 队列           | 在集成运行时中实际开始复制活动之前经过的时间。 |
| 复制前脚本 | 从 IR 和复制活动完成到在接收器数据存储中执行复制前脚本的复制活动之间经过的时间。 当你为数据库接收器配置预复制脚本时，例如，将数据写入 Azure SQL 数据库时，请在复制新数据之前进行清理。 |
| 转换        | 上一步结束与 IR 之间经过的时间，将所有数据从源传输到接收器。 "传输" 下的子步骤将并行运行。<br><br>- **至第一字节的时间：** 上一步骤结束与 IR 接收源数据存储中第一个字节之间经过的时间。 适用于非基于文件的源。<br>- **列出源：** 枚举源文件或数据分区所用的时间。 后者适用于为数据库源配置分区选项的情况，例如，当从 Oracle/SAP HANA/Teradata/Netezza/等数据库复制数据时。<br/>**从源中读取 -：** 从源数据存储检索数据所用的时间。<br/>- **写入接收器：** 向接收器数据存储写入数据所用的时间。 |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>对 Azure IR 上的复制活动进行故障排除

按照[性能优化步骤](copy-activity-performance.md#performance-tuning-steps)来规划和执行适用于你的方案的性能测试。 

当复制活动性能无法满足你的期望时，若要对 Azure Integration Runtime 上运行的单个复制活动进行故障排除，如果你看到 "复制监视" 视图中显示的[性能优化提示](#performance-tuning-tips)，请应用该建议，然后重试。 否则，请[了解复制活动的执行详细信息](#understand-copy-activity-execution-details)，检查哪个阶段的持续时间**最长**，并应用以下指南以提高复制性能：

- **"复制脚本" 经历了长时间：** 这意味着在接收器数据库上运行的复制前脚本需要很长时间才能完成。 优化指定的复制前脚本逻辑，提高性能。 如果需要更多帮助来改进脚本，请与数据库团队联系。

- **"传输-至第一字节的时间" 经历了长时间的工作时间**：这意味着源查询需要很长的时间来返回任何数据。 检查并优化查询或服务器。 如果需要更多帮助，请联系你的数据存储团队。

- **"传输-列出源" 的运行时间长**：这意味着枚举源文件或源数据库数据分区速度缓慢。

  - 从基于文件的源复制数据时，如果你对文件夹路径或文件名（`wildcardFolderPath` 或 `wildcardFileName`）使用**通配符筛选器**，或使用**文件 "上次修改时间" 筛选器**（`modifiedDatetimeStart` 或`modifiedDatetimeEnd`），请注意，此类筛选器会导致将指定文件夹下的所有文件的复制活动列出到 "客户端"，然后应用筛选器。 当只有少量文件符合筛选规则时，此类文件枚举就会成为瓶颈。

    - 检查是否可以[根据日期时间分区文件路径或名称复制文件](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)。 这样一来，就不会对列出源端带来负担。

    - 检查是否可以使用数据存储的本机筛选器，尤其是 Amazon S3 和 Azure Blob 的 "**前缀**"。 前缀筛选器是一个数据存储服务器端筛选器，具有更好的性能。

    - 请考虑将单个大数据集拆分为几个较小的数据集，并让这些复制作业同时运行每个处理部分的数据。 可以通过 Lookup/GetMetadata + ForEach + Copy 执行此操作。 请参阅[从多个容器复制文件或将](solution-template-copy-files-multiple-containers.md)[数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md)解决方案模板（一般示例）。

  - 检查 ADF 是否在源上报告任何限制错误，或数据存储是否处于高利用率状态。 如果是这样，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员以增加限制或可用资源。

  - 使用与源数据存储区相同或接近的 Azure IR。

- **"传输-从源读取" 的时间过长**： 

  - 采用连接器特定的数据加载最佳做法（如果适用）。 例如，从[Amazon Redshift](connector-amazon-redshift.md)复制数据时，请将配置为使用 Redshift 卸载。

  - 检查 ADF 是否在源上报告任何限制错误，或数据存储是否处于高利用率。 如果是这样，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员以增加限制或可用资源。

  - 检查复制源和接收器模式： 

    - 如果你的复制模式支持大于4个数据集成单元（DIUs）-请参阅[本节中的](copy-activity-performance.md#data-integration-units)详细信息，通常可以尝试增加 DIUs 以获得更好的性能。 

    - 否则，请考虑将单个大数据集拆分为几个较小的数据集，并让这些复制作业同时运行每个处理部分的数据。 可以通过 Lookup/GetMetadata + ForEach + Copy 执行此操作。 请参阅[从多个容器复制文件、将](solution-template-copy-files-multiple-containers.md)[数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md)或[使用控制表解决方案模板进行大容量复制](solution-template-bulk-copy-with-control-table.md)（如一般示例）。

  - 使用与源数据存储区相同或接近的 Azure IR。

- **"传输-写入接收器" 的时间过长**：

  - 采用连接器特定的数据加载最佳做法（如果适用）。 例如，将数据复制到[Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) （以前称为 SQL DW）时，请使用 POLYBASE 或 COPY 语句。 

  - 检查 ADF 是否在接收器上报告任何限制错误，或数据存储是否处于高利用率。 如果是这样，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员以增加限制或可用资源。

  - 检查复制源和接收器模式： 

    - 如果你的复制模式支持大于4个数据集成单元（DIUs）-请参阅[本节中的](copy-activity-performance.md#data-integration-units)详细信息，通常可以尝试增加 DIUs 以获得更好的性能。 

    - 否则，会逐渐调整[并行复制](copy-activity-performance-features.md)，请注意，过多的并行复制甚至可能会影响性能。

  - 使用相同或接近于接收器数据存储区的 Azure IR。

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>对自承载 IR 上的复制活动进行故障排除

按照[性能优化步骤](copy-activity-performance.md#performance-tuning-steps)来规划和执行适用于你的方案的性能测试。 

如果复制性能不符合你的期望，若要解决 Azure Integration Runtime 上运行的单个复制活动，请在 "复制监视" 视图中看到[性能优化提示](#performance-tuning-tips)，应用建议，然后重试。 否则，请[了解复制活动的执行详细信息](#understand-copy-activity-execution-details)，检查哪个阶段的持续时间**最长**，并应用以下指南以提高复制性能：

- **"队列" 的持续时间很长：** 这意味着复制活动会在队列中等待很长时间，直到你的自承载 IR 有资源可执行。 检查 IR 容量和使用情况，并根据工作负荷[增加或缩减](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

- **"传输-至第一字节的时间" 经历了长时间的工作时间**：这意味着源查询需要很长的时间来返回任何数据。 检查并优化查询或服务器。 如果需要更多帮助，请联系你的数据存储团队。

- **"传输-列出源" 的运行时间长**：这意味着枚举源文件或源数据库数据分区速度缓慢。

  - 检查自承载的 IR 计算机连接到源数据存储的延迟是否较低。 如果源位于 Azure 中，则可以使用[此工具](http://www.azurespeed.com/Azure/Latency)检查自承载 IR 计算机到 azure 区域的延迟，越好越好。

  - 从基于文件的源复制数据时，如果你对文件夹路径或文件名（`wildcardFolderPath` 或 `wildcardFileName`）使用**通配符筛选器**，或使用**文件 "上次修改时间" 筛选器**（`modifiedDatetimeStart` 或`modifiedDatetimeEnd`），请注意，此类筛选器会导致将指定文件夹下的所有文件的复制活动列出到 "客户端"，然后应用筛选器。 当只有少量文件符合筛选规则时，此类文件枚举就会成为瓶颈。

    - 检查是否可以[根据日期时间分区文件路径或名称复制文件](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)。 这样一来，就不会对列出源端带来负担。

    - 检查是否可以使用数据存储的本机筛选器，尤其是 Amazon S3 和 Azure Blob 的 "**前缀**"。 前缀筛选器是一个数据存储服务器端筛选器，具有更好的性能。

    - 请考虑将单个大数据集拆分为几个较小的数据集，并让这些复制作业同时运行每个处理部分的数据。 可以通过 Lookup/GetMetadata + ForEach + Copy 执行此操作。 请参阅[从多个容器复制文件或将](solution-template-copy-files-multiple-containers.md)[数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md)解决方案模板（一般示例）。

  - 检查 ADF 是否在源上报告任何限制错误，或数据存储是否处于高利用率状态。 如果是这样，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员以增加限制或可用资源。

- **"传输-从源读取" 的时间过长**： 

  - 检查自承载的 IR 计算机连接到源数据存储的延迟是否较低。 如果源位于 Azure 中，则可以使用[此工具](http://www.azurespeed.com/Azure/Latency)检查自承载 IR 计算机到 azure 区域的延迟，越好越好。

  - 检查自承载 IR 计算机是否具有足够的入站带宽来有效地读取和传输数据。 如果源数据存储在 Azure 中，则可以使用[此工具](https://www.azurespeed.com/Azure/Download)来检查下载速度。

  - 查看 > 数据工厂 > 概述页 Azure 门户中的自承载 IR 的 CPU 和内存使用趋势。 如果 CPU 使用率较高或可用内存不足，则考虑[增加/传出 IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 。

  - 采用连接器特定的数据加载最佳做法（如果适用）。 例如：

    - 从[Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [Teradata](connector-teradata.md#teradata-as-source)、 [SAP HANA](connector-sap-hana.md#sap-hana-as-source)、 [sap 表](connector-sap-table.md#sap-table-as-source)和[sap 开放式集线器](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)复制数据时，启用数据分区选项以并行方式复制数据。

    - 从[HDFS](connector-hdfs.md)复制数据时，请将配置为使用 DistCp。

    - 从[Amazon Redshift](connector-amazon-redshift.md)复制数据时，请将配置为使用 Redshift 卸载。

  - 检查 ADF 是否在源上报告任何限制错误，或数据存储是否处于高利用率。 如果是这样，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员以增加限制或可用资源。

  - 检查复制源和接收器模式： 

    - 如果从启用了分区选项的数据存储复制数据，请考虑逐步调整[并行副本](copy-activity-performance-features.md)，请注意，太多的并行副本甚至可能会影响性能。

    - 否则，请考虑将单个大数据集拆分为几个较小的数据集，并让这些复制作业同时运行每个处理部分的数据。 可以通过 Lookup/GetMetadata + ForEach + Copy 执行此操作。 请参阅[从多个容器复制文件、将](solution-template-copy-files-multiple-containers.md)[数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md)或[使用控制表解决方案模板进行大容量复制](solution-template-bulk-copy-with-control-table.md)（如一般示例）。

- **"传输-写入接收器" 的时间过长**：

  - 采用连接器特定的数据加载最佳做法（如果适用）。 例如，将数据复制到[Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) （以前称为 SQL DW）时，请使用 POLYBASE 或 COPY 语句。 

  - 检查自承载的 IR 计算机连接到接收器数据存储的延迟是否较低。 如果接收器位于 Azure 中，则可以使用[此工具](http://www.azurespeed.com/Azure/Latency)检查自承载 IR 计算机到 azure 区域的延迟，越好越好。

  - 检查自承载的 IR 计算机是否具有足够的出站带宽来有效地传输和写入数据。 如果接收器数据存储位于 Azure 中，则可以使用[此工具](https://www.azurespeed.com/Azure/UploadLargeFile)来检查上传速度。

  - 检查自承载 IR 的 CPU 和内存使用趋势 Azure 门户 > 你的数据工厂-> 概述 "页。 如果 CPU 使用率较高或可用内存不足，则考虑[增加/传出 IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 。

  - 检查 ADF 是否在接收器上报告任何限制错误，或数据存储是否处于高利用率。 如果是这样，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员以增加限制或可用资源。

  - 请考虑逐步优化[并行复制](copy-activity-performance-features.md)，请注意，过多的并行复制甚至可能会影响性能。

## <a name="other-references"></a>其他参考资料

下面是有关一些受支持数据存储的性能监视和优化参考：

* Azure Blob 存储： [blob 存储的可伸缩性和性能目标](../storage/blobs/scalability-targets.md)，以及[Blob 存储的性能和可伸缩性清单](../storage/blobs/storage-performance-checklist.md)。
* Azure 表存储：表存储[的可伸缩性和性能目标](../storage/tables/scalability-targets.md)，以及[表存储的性能和可伸缩性清单](../storage/tables/storage-performance-checklist.md)。
* Azure SQL 数据库：可以[监视性能](../sql-database/sql-database-single-database-monitor.md)并检查数据库事务单位（DTU）百分比。
* Azure SQL 数据仓库：其功能以数据仓库单位（Dwu）度量。 请参阅[管理 AZURE SQL 数据仓库中的计算能力（概述）](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。
* Azure Cosmos DB： [Azure Cosmos DB 中的性能级别](../cosmos-db/performance-levels.md)。
* 本地 SQL Server：[监视和优化性能](https://msdn.microsoft.com/library/ms189081.aspx)。
* 本地文件服务器：[文件服务器的性能优化](https://msdn.microsoft.com/library/dn567661.aspx)。

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能和可伸缩性指南](copy-activity-performance.md)
- [复制活动性能优化功能](copy-activity-performance-features.md)
- [使用 Azure 数据工厂将数据从 data lake 或数据仓库迁移到 Azure](data-migration-guidance-overview.md)
- [将数据从 Amazon S3 迁移到 Azure 存储](data-migration-guidance-s3-azure-storage.md)
