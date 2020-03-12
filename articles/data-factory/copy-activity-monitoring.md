---
title: 监视复制活动
description: 了解如何监视 Azure 数据工厂中的复制活动执行。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 6494352bf957af83b45488493bf12a094c730c09
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79125753"
---
# <a name="monitor-copy-activity"></a>监视复制活动

本文概述如何监视 Azure 数据工厂中的复制活动执行。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="monitor-visually"></a>直观地监视

在 Azure 数据工厂中创建并发布管道后，可以将其与触发器相关联，或手动启动即席运行。 可以在 Azure 数据工厂用户体验中以本机方式监视所有管道运行情况。 了解 Azure 数据工厂监视，一般情况是通过[可视化方式监视 Azure 数据工厂](monitor-visually.md)。

若要监视复制活动运行，请 **& 监视器**UI 中转到数据工厂作者。 在 "**监视器**" 选项卡上，可以看到一个管道运行列表，单击 "**管道名称**" 链接可访问管道运行中活动运行的列表。

![监视复制活动运行](./media/copy-activity-overview/monitor-pipeline-run.png)

在此级别上，你可以看到复制活动输入、输出和错误的链接（如果复制活动运行失败）以及持续时间/状态等统计信息。 单击 "复制" 活动名称旁边的 "**详细信息**" 按钮（眼镜），可获得有关复制活动执行的详细信息。 

![监视复制活动运行](./media/copy-activity-overview/monitor-copy-activity-run.png)

在此图形监视视图中，Azure 数据工厂会提供复制活动执行信息，其中包括数据读取/写入量、从源到接收器复制的数据的文件/行的数量、吞吐量、为复制方案所应用的配置、使用相应的持续时间和详细信息执行复制活动的步骤等。 请参阅[此表](#monitor-programmatically)以了解每个可能的指标及其详细说明。 

在某些情况下，当你在数据工厂中运行复制活动时，将在复制活动监视视图的顶部看到 **"性能优化提示"** ，如示例中所示。 提示会告诉你 ADF 为特定副本运行所标识的瓶颈，并建议更改哪些内容来提高复制吞吐量。 详细了解[自动性能优化提示](copy-activity-performance-troubleshooting.md#performance-tuning-tips)。

底部**执行详细信息和持续**时间介绍复制活动所经历的关键步骤，这对于排查复制性能问题尤其有用。 复制运行的瓶颈是持续时间最长的一个。 有关每个阶段所代表的内容以及详细的故障排除指南，请参阅对[复制活动性能进行故障排除](copy-activity-performance-troubleshooting.md)。

**示例：从 Amazon S3 复制到 Azure Data Lake Storage Gen2**

![监视复制活动运行详细信息](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>以编程方式监视

复制活动执行详细信息和性能特征也会在 "**复制活动运行结果** > **输出**" 部分返回，此部分用于呈现 UI 监视视图。 下面是可能返回的属性的完整列表。 你将仅看到适用于你的复制方案的属性。 有关如何以编程方式监视活动运行的详细信息，请参阅[以编程方式监视 Azure 数据工厂](monitor-programmatically.md)。

| 属性名称  | 说明 | 输出单元 |
|:--- |:--- |:--- |
| dataRead | 从源中读取的实际数据量。 | Int64 值（以字节为单位） |
| DataWritten | 写入或提交到接收器的数据的实际装载。 大小可能不同于 `dataRead` 大小，因为它与每个数据存储存储数据的方式相关。 | Int64 值（以字节为单位） |
| filesRead | 从基于文件的源中读取的文件数。 | Int64 值（未指定单位） |
| filesWritten | 写入/提交到基于文件的接收器的文件数。 | Int64 值（未指定单位） |
| sourcePeakConnections | 复制活动运行期间为源数据存储建立的并发连接的峰值数量。 | Int64 值（未指定单位） |
| sinkPeakConnections | 复制活动运行期间，建立到接收器数据存储的并发连接的峰值数量。 | Int64 值（未指定单位） |
| rowsRead | 从源中读取的行数（不适用于二进制副本）。 | Int64 值（未指定单位） |
| rowsCopied | 复制到接收器的行数（不适用于二进制副本）。 | Int64 值（未指定单位） |
| rowsSkipped | 跳过的不兼容行的数目。 可以通过将 `enableSkipIncompatibleRow` 设置为 true，来跳过不兼容的行。 | Int64 值（未指定单位） |
| copyDuration | 复制运行的持续时间。 | Int32 值（秒） |
| throughput | 数据传输速率。 | 浮点数（以 KBps 为单位） |
| sourcePeakConnections | 复制活动运行期间为源数据存储建立的并发连接的峰值数量。 | Int32 值（无单位） |
| sinkPeakConnections| 复制活动运行期间，建立到接收器数据存储的并发连接的峰值数量。| Int32 值（无单位） |
| sqlDwPolyBase | 将数据复制到 SQL 数据仓库时是否使用 PolyBase。 | Boolean |
| redshiftUnload | 从 Redshift 复制数据时是否使用 UNLOAD。 | Boolean |
| hdfsDistcp | 从 HDFS 复制数据时是否使用 DistCp。 | Boolean |
| effectiveIntegrationRuntime | 用于使活动运行通电的集成运行时（IR）或运行时，格式为 `<IR name> (<region if it's Azure IR>)`。 | 文本（字符串） |
| usedDataIntegrationUnits | 复制期间的有效数据集成单位。 | Int32 值 |
| usedParallelCopies | 复制期间的有效 parallelCopies。 | Int32 值 |
| redirectRowPath | 在 `redirectIncompatibleRowSettings` 属性中配置的 blob 存储中跳过不兼容行的日志路径。 请参阅[容错](copy-activity-overview.md#fault-tolerance)。 | 文本（字符串） |
| executionDetails | 有关复制活动所经历的阶段的详细信息以及相应的步骤、持续时间、配置等。 我们不建议你分析此部分，因为它可能会更改。 为了更好地了解它如何帮助你了解和排查复制性能问题，请参阅 "[直观监视](#monitor-visually)" 部分。 | Array |
| perfRecommendation | 复制性能优化提示。 有关详细信息，请参阅[性能优化提示](copy-activity-performance-troubleshooting.md#performance-tuning-tips)。 | Array |

**示例：**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

\-[复制活动概述](copy-activity-overview.md)

\-[复制活动性能](copy-activity-performance.md)