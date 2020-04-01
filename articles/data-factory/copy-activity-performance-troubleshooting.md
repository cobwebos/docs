---
title: 排除复制活动性能的故障
description: 了解如何在 Azure 数据工厂中排除复制活动性能的疑难解答。
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
ms.openlocfilehash: a14f4d548053fb7aaf6f450176fdc49bc7b119bf
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421043"
---
# <a name="troubleshoot-copy-activity-performance"></a>排除复制活动性能的故障

本文概述了如何在 Azure 数据工厂中解决复制活动性能问题。 

运行复制活动后，可以在[复制活动监视](copy-activity-monitoring.md)视图中收集运行结果和性能统计信息。 下面是一个示例。

![监视复制活动运行详细信息](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>性能优化提示

在某些情况下，在数据工厂中运行复制活动时，您将在顶部看到 **"性能调优提示"，** 如上例所示。 这些提示告诉您 ADF 为此特定副本运行确定的瓶颈，以及如何提高复制吞吐量的建议。 尝试进行重新命令的更改，然后再次运行副本。

作为参考，当前性能调优提示为以下情况提供建议：

| 类别              | 性能优化提示                                      |
| --------------------- | ------------------------------------------------------------ |
| 数据存储特定   | 将数据加载到**Azure Synpase 分析（以前 SQL DW）** 中：建议在不使用 PolyBase 或 COPY 语句时使用它。 |
| &nbsp;                | 将数据从/复制到**Azure SQL 数据库**：当 DTU 处于高利用率时，建议升级到更高层。 |
| &nbsp;                | 将数据从/复制到**Azure Cosmos DB：** 当 RU 被高度利用时，建议升级到更大的 RU。 |
| &nbsp;                | 从**亚马逊红移**引入数据：建议使用UNLOAD，如果它不使用。 |
| 数据存储限制 | 如果在复制过程中数据存储限制了多个读/写操作，建议检查和提高数据存储的允许请求速率，或减少并发工作负载。 |
| 集成运行时  | 如果使用**自托管集成运行时 （IR）** 并在队列中等待很长时间，直到 IR 具有可用资源执行，建议向外/上扩展 IR。 |
| &nbsp;                | 如果使用的**Azure 集成运行时**位于不理想区域中，导致读取/写入速度慢，建议配置以在另一个区域中使用 IR。 |
| 容错       | 如果配置容错和跳过不兼容行会导致性能降低，建议确保源和接收器数据兼容。 |
| 暂存复制           | 如果已配置暂存副本，但对源接收器对没有帮助，建议将其删除。 |
| 恢复                | 当复制活动从上次失败点恢复，但您碰巧在原始运行后更改 DIU 设置时，请注意新的 DIU 设置不起作用。 |

## <a name="understand-copy-activity-execution-details"></a>了解复制活动执行详细信息

复制活动监视视图底部的执行详细信息和持续时间描述了复制活动经历的关键阶段（请参阅本文开头的示例），这对于解决复制性能的疑难问题特别有用。 复制运行的瓶颈是持续时间最长的瓶颈。 请参阅每个阶段定义的下表，了解如何[对 Azure IR 上的复制活动进行故障排除](#troubleshoot-copy-activity-on-azure-ir)，以及如何使用此类信息[对自托管 IR 上的复制活动进行故障排除](#troubleshoot-copy-activity-on-self-hosted-ir)。

| 阶段           | 说明                                                  |
| --------------- | ------------------------------------------------------------ |
| 队列           | 在集成运行时实际启动复制活动之前已过的时间。 |
| 预拷贝脚本 | 从 IR 开始开始复制活动到在接收器数据存储中完成执行预复制脚本的复制活动之间的运行时间。 在为数据库接收器配置预复制脚本时应用，例如将数据写入 Azure SQL 数据库时，在复制新数据之前请清理。 |
| 传输        | 上一步骤结束和红外从源传输到接收器之间的运行时间。 "传输"下的子步骤并行运行。<br><br>- **第一个字节的时间：** 从上一步骤的结束到 IR 从源数据存储接收第一个字节的时间之间的时间。 适用于不是基于文件的源。<br>- **列表来源：** 枚举源文件或数据分区所花费的时间量。 当您为数据库源配置分区选项时，例如从 Oracle/SAP HANA/Teradata/Netezza/等数据库复制数据时，则适用后者。<br/>-**从源读取：** 从源数据存储检索数据所花费的时间量。<br/>- **写入接收器：** 用于将数据写入接收器数据存储所花费的时间。 |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>在 Azure IR 上排除复制活动故障

按照[性能调优步骤](copy-activity-performance.md#performance-tuning-steps)为方案规划和执行性能测试。 

当复制活动性能不符合预期时，要对 Azure 集成运行时上运行的单个复制活动进行故障排除，如果看到复制监视视图中显示[的性能调优提示](#performance-tuning-tips)，请应用建议，然后重试。 否则，[了解复制活动执行详细信息](#understand-copy-activity-execution-details)，检查哪个阶段持续时间**最长**，并应用以下指南以提高复制性能：

- **"预复制脚本"经历了长时间的运行：** 这意味着在接收器数据库上运行的预复制脚本需要很长时间才能完成。 调整指定的预复制脚本逻辑以提高性能。 如果您需要改进脚本的进一步帮助，请与数据库团队联系。

- **"传输 - 第一字节的时间"经历了很长的工作持续时间**：这意味着源查询返回任何数据需要很长时间。 检查并优化查询或服务器。 如果您需要进一步帮助，请联系数据存储团队。

- **"传输 - 列表源"经历了长时间的工作**：这意味着枚举源文件或源数据库数据分区很慢。

  - 从基于文件的源复制数据时，如果在文件夹路径或文件名 （`wildcardFolderPath``wildcardFileName`或 ） 上使用**通配符筛选器**，或使用**文件上次修改的时间筛选器**（`modifiedDatetimeStart`或`modifiedDatetimeEnd`），请注意，此类筛选器将导致将指定文件夹下的所有文件列表的副本活动添加到客户端，然后应用筛选器。 此类文件枚举可能成为瓶颈，尤其是当只有少量文件满足筛选器规则时。

    - 检查是否可以[根据日期时间分区的文件路径或名称复制文件](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)。 这种方式不会给上市来源带来负担。

    - 检查是否可以使用数据存储的本机筛选器，特别是 Amazon S3 和 Azure Blob 的"**前缀**"。 前缀筛选器是数据存储服务器端筛选器，性能会好得多。

    - 请考虑将单个大型数据集拆分为多个较小的数据集，并让这些复制作业同时运行，每个数据处理部分。 您可以使用查找/获取元数据 = 每个内容 = 复制来执行此操作。 请参阅[从多个容器复制文件](solution-template-copy-files-multiple-containers.md)或[将数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md)解决方案模板作为一般示例。

  - 检查 ADF 是否报告源上有任何限制错误，或者数据存储是否处于高利用率状态。 如果是这样，请减少数据存储上的工作负载，或者尝试联系数据存储管理员以增加限制限制或可用资源。

  - 在源数据存储区域的相同或附近使用 Azure IR。

- **"从源转移阅读"经历了长时间的工作：** 

  - 如果适用，采用特定于连接器的数据加载最佳做法。 例如，从[Amazon Redshift](connector-amazon-redshift.md)复制数据时，配置为使用红移 UNLOAD。

  - 检查 ADF 是否报告源上有任何限制错误，或者您的数据存储是否被高利用率。 如果是这样，请减少数据存储上的工作负载，或者尝试联系数据存储管理员以增加限制限制或可用资源。

  - 检查复制源和接收器模式： 

    - 如果您的复制模式支持大于 4 个数据集成单元 （DIUs） - 请参阅[本节](copy-activity-performance-features.md#data-integration-units)的详细信息，通常您可以尝试增加 DIA 以获得更好的性能。 

    - 否则，请考虑将单个大型数据集拆分为多个较小的数据集，并让这些复制作业同时运行，每个数据处理部分。 您可以使用查找/获取元数据 = 每个内容 = 复制来执行此操作。 请参阅[从多个容器复制文件](solution-template-copy-files-multiple-containers.md)、[将数据从 Amazon S3 迁移到 ADLS Gen2，](solution-template-migration-s3-azure.md)或[使用控制表](solution-template-bulk-copy-with-control-table.md)解决方案模板进行批量复制作为一般示例。

  - 在源数据存储区域的相同或附近使用 Azure IR。

- **"转移-写作到水槽"经历了漫长的工作时间**：

  - 如果适用，采用特定于连接器的数据加载最佳做法。 例如，将数据复制到[Azure 突触分析](connector-azure-sql-data-warehouse.md)（以前的 SQL DW）时，请使用 PolyBase 或 COPY 语句。 

  - 检查 ADF 是否报告接收器上有任何限制错误，或者您的数据存储是否被高利用率。 如果是这样，请减少数据存储上的工作负载，或者尝试联系数据存储管理员以增加限制限制或可用资源。

  - 检查复制源和接收器模式： 

    - 如果您的复制模式支持大于 4 个数据集成单元 （DIUs） - 请参阅[本节](copy-activity-performance-features.md#data-integration-units)的详细信息，通常您可以尝试增加 DIA 以获得更好的性能。 

    - 否则，逐渐调整[并行副本](copy-activity-performance-features.md)，注意太多并行副本甚至可能损害性能。

  - 在同一或靠近接收器数据存储区域时使用 Azure IR。

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>在自托管 IR 上排除复制活动故障

按照[性能调优步骤](copy-activity-performance.md#performance-tuning-steps)为方案规划和执行性能测试。 

当复制性能不符合预期时，要对 Azure 集成运行时上运行的单个复制活动进行故障排除，如果看到复制监视视图中显示[的性能调优提示](#performance-tuning-tips)，请应用建议，然后重试。 否则，[了解复制活动执行详细信息](#understand-copy-activity-execution-details)，检查哪个阶段持续时间**最长**，并应用以下指南以提高复制性能：

- **"队列"经历了长时间的持续时间：** 这意味着复制活动在队列中等待很长时间，直到您的自托管 IR 具有要执行的资源。 检查红外容量和使用情况，并根据工作负载[向上或扩展](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

- **"传输 - 第一字节的时间"经历了很长的工作持续时间**：这意味着源查询返回任何数据需要很长时间。 检查并优化查询或服务器。 如果您需要进一步帮助，请联系数据存储团队。

- **"传输 - 列表源"经历了长时间的工作**：这意味着枚举源文件或源数据库数据分区很慢。

  - 检查自承载的 IR 计算机是否具有连接到源数据存储的低延迟。 如果源位于 Azure 中，则可以使用[此工具](http://www.azurespeed.com/Azure/Latency)检查从自托管 IR 计算机到 Azure 区域的延迟，越少越好。

  - 从基于文件的源复制数据时，如果在文件夹路径或文件名 （`wildcardFolderPath``wildcardFileName`或 ） 上使用**通配符筛选器**，或使用**文件上次修改的时间筛选器**（`modifiedDatetimeStart`或`modifiedDatetimeEnd`），请注意，此类筛选器将导致将指定文件夹下的所有文件列表的副本活动添加到客户端，然后应用筛选器。 此类文件枚举可能成为瓶颈，尤其是当只有少量文件满足筛选器规则时。

    - 检查是否可以[根据日期时间分区的文件路径或名称复制文件](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)。 这种方式不会给上市来源带来负担。

    - 检查是否可以使用数据存储的本机筛选器，特别是 Amazon S3 和 Azure Blob 的"**前缀**"。 前缀筛选器是数据存储服务器端筛选器，性能会好得多。

    - 请考虑将单个大型数据集拆分为多个较小的数据集，并让这些复制作业同时运行，每个数据处理部分。 您可以使用查找/获取元数据 = 每个内容 = 复制来执行此操作。 请参阅[从多个容器复制文件](solution-template-copy-files-multiple-containers.md)或[将数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md)解决方案模板作为一般示例。

  - 检查 ADF 是否报告源上有任何限制错误，或者数据存储是否处于高利用率状态。 如果是这样，请减少数据存储上的工作负载，或者尝试联系数据存储管理员以增加限制限制或可用资源。

- **"从源转移阅读"经历了长时间的工作：** 

  - 检查自承载的 IR 计算机是否具有连接到源数据存储的低延迟。 如果源位于 Azure 中，则可以使用[此工具](http://www.azurespeed.com/Azure/Latency)检查从自托管 IR 计算机到 Azure 区域的延迟，越少越好。

  - 检查自承载的 IR 计算机是否有足够的入站带宽来高效地读取和传输数据。 如果源数据存储位于 Azure 中，则可以使用[此工具](https://www.azurespeed.com/Azure/Download)检查下载速度。

  - 在 Azure 门户中检查自托管 IR 的 CPU 和内存使用趋势 ->数据工厂 ->概述页。 如果 CPU 使用率高或可用内存不足，请考虑[向上/缩小 IR。](create-self-hosted-integration-runtime.md#high-availability-and-scalability)

  - 如果适用，采用特定于连接器的数据加载最佳做法。 例如：

    - 从[Oracle](connector-oracle.md#oracle-as-source)Oracle、Netezza、Teradata、SAP [Netezza](connector-netezza.md#netezza-as-source) [HANA、SAP](connector-sap-hana.md#sap-hana-as-source)[表](connector-sap-table.md#sap-table-as-source)和[SAP 开放集线器](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)复制数据时，启用数据分区选项以并行复制数据。 [Teradata](connector-teradata.md#teradata-as-source)

    - 从[HDFS](connector-hdfs.md)复制数据时 ，配置为使用 DistCp。

    - 从[亚马逊红移](connector-amazon-redshift.md)复制数据时，配置为使用红移卸载。

  - 检查 ADF 是否报告源上有任何限制错误，或者您的数据存储是否被高利用率。 如果是这样，请减少数据存储上的工作负载，或者尝试联系数据存储管理员以增加限制限制或可用资源。

  - 检查复制源和接收器模式： 

    - 如果从支持分区选项的数据存储复制数据，请考虑逐步调整[并行副本](copy-activity-performance-features.md)，请注意，太多的并行副本甚至可能损害性能。

    - 否则，请考虑将单个大型数据集拆分为多个较小的数据集，并让这些复制作业同时运行，每个数据处理部分。 您可以使用查找/获取元数据 = 每个内容 = 复制来执行此操作。 请参阅[从多个容器复制文件](solution-template-copy-files-multiple-containers.md)、[将数据从 Amazon S3 迁移到 ADLS Gen2，](solution-template-migration-s3-azure.md)或[使用控制表](solution-template-bulk-copy-with-control-table.md)解决方案模板进行批量复制作为一般示例。

- **"转移-写作到水槽"经历了漫长的工作时间**：

  - 如果适用，采用特定于连接器的数据加载最佳做法。 例如，将数据复制到[Azure 突触分析](connector-azure-sql-data-warehouse.md)（以前的 SQL DW）时，请使用 PolyBase 或 COPY 语句。 

  - 检查自承载的 IR 计算机是否具有连接到接收器数据存储的低延迟。 如果接收器位于 Azure 中，则可以使用[此工具](http://www.azurespeed.com/Azure/Latency)检查从自承载的 IR 计算机到 Azure 区域的延迟，越少越好。

  - 检查自承载的 IR 计算机是否有足够的出站带宽来高效地传输和写入数据。 如果接收器数据存储在 Azure 中，则可以使用[此工具](https://www.azurespeed.com/Azure/UploadLargeFile)检查上载速度。

  - 检查 Azure 门户中的自托管 IR 的 CPU 和内存使用趋势 ->数据工厂 -> 概览页。 如果 CPU 使用率高或可用内存不足，请考虑[向上/缩小 IR。](create-self-hosted-integration-runtime.md#high-availability-and-scalability)

  - 检查 ADF 是否报告接收器上有任何限制错误，或者您的数据存储是否被高利用率。 如果是这样，请减少数据存储上的工作负载，或者尝试联系数据存储管理员以增加限制限制或可用资源。

  - 考虑逐渐调整[并行副本](copy-activity-performance-features.md)，请注意，太多的并行副本甚至可能损害性能。

## <a name="other-references"></a>其他参考资料

下面是有关一些受支持数据存储的性能监视和优化参考：

* Azure Blob 存储[：Blob 存储的可伸缩性和性能目标](../storage/blobs/scalability-targets.md)，[以及 Blob 存储的性能以及可伸缩性检查表](../storage/blobs/storage-performance-checklist.md)。
* Azure 表存储：[表存储的可伸缩性和性能目标](../storage/tables/scalability-targets.md)表[存储的性能以及可伸缩性检查表](../storage/tables/storage-performance-checklist.md)。
* Azure SQL 数据库：您可以[监视性能](../sql-database/sql-database-single-database-monitor.md)并检查数据库事务单元 （DTU） 百分比。
* Azure SQL 数据仓库：其功能以数据仓库单位 （DWU） 度量。 请参阅[管理 Azure SQL 数据仓库中的计算能力（概述）](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。
* Azure 宇宙[DB：Azure 宇宙 DB 中的性能级别](../cosmos-db/performance-levels.md)。
* 本地 SQL 服务器：[监视和调整性能](https://msdn.microsoft.com/library/ms189081.aspx)。
* 本地文件服务器：[文件服务器的性能调优](https://msdn.microsoft.com/library/dn567661.aspx)。

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能和可伸缩性指南](copy-activity-performance.md)
- [复制活动性能优化功能](copy-activity-performance-features.md)
- [使用 Azure 数据工厂将数据从 Data Lake 或数据仓库迁移到 Azure](data-migration-guidance-overview.md)
- [将数据从 Amazon S3 迁移到 Azure 存储](data-migration-guidance-s3-azure-storage.md)
