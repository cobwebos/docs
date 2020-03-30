---
title: 监视复制活动
description: 了解如何在 Azure 数据工厂中监视复制活动执行。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79125753"
---
# <a name="monitor-copy-activity"></a>监视复制活动

本文概述了如何在 Azure 数据工厂中监视复制活动执行。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="monitor-visually"></a>直观地监视

在 Azure 数据工厂中创建并发布管道以后，即可将其与触发器关联，或者手动启动临时运行。 可以在 Azure 数据工厂用户体验中以本机方式监视所有管道运行。 从[可视化监视 Azure 数据工厂](monitor-visually.md)了解 Azure 数据工厂监视。

要监视"复制"活动运行，请转到数据工厂 **"作者&监视器**UI"。 在 **"监视器"** 选项卡上，您将看到管道运行的列表，单击**管道名称**链接以访问管道运行中运行的活动列表。

![监视复制活动运行](./media/copy-activity-overview/monitor-pipeline-run.png)

在此级别，您可以看到用于复制活动输入、输出和错误（如果复制活动运行失败）以及持续时间/状态等统计信息的链接。 单击复制活动名称旁边的 **"详细信息**"按钮（眼镜）将为您提供有关复制活动执行的深层详细信息。 

![监视复制活动运行](./media/copy-activity-overview/monitor-copy-activity-run.png)

在此图形监视视图中，Azure 数据工厂会显示复制活动执行信息，包括数据读取/写入卷、从源复制到接收器的文件/行数据数、吞吐量、应用于复制方案的配置、复制活动经历的步骤以及相应的持续时间和详细信息等。 有关每个可能的指标及其详细说明，请参阅[此表](#monitor-programmatically)。 

在某些情况下，在数据工厂中运行复制活动时，您将在复制活动监视视图的顶部看到 **"性能调优提示"，** 如示例所示。 这些提示告诉您 ADF 为特定复制运行确定的瓶颈，以及有关要更改哪些内容以提高复制吞吐量的建议。 了解有关[自动性能调优提示](copy-activity-performance-troubleshooting.md#performance-tuning-tips)的更多。

底部**执行详细信息和持续时间**描述了复制活动执行的关键步骤，这对于解决复制性能的疑难问题特别有用。 复制运行的瓶颈是持续时间最长的瓶颈。 有关每个阶段表示的内容和详细的故障排除指南，请参阅["疑难解答"复制活动性能](copy-activity-performance-troubleshooting.md)。

**示例：从 Amazon S3 复制到 Azure 数据存储单元 2**

![监视复制活动运行详细信息](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>以编程方式监视

复制活动执行详细信息和性能特征也会在 **"复制活动运行结果** > **输出"** 部分中返回，该部分用于呈现 UI 监视视图。 下面是可能返回的属性的完整列表。 只会显示适用于你的复制方案的属性。 有关如何监视活动通常以编程方式运行的信息，请参阅[以编程方式监视 Azure 数据工厂](monitor-programmatically.md)。

| 属性名称  | 描述 | 输出单位 |
|:--- |:--- |:--- |
| dataRead | 从源读取的实际数据量。 | Int64 值，以字节为单位 |
| DataWritten | 写入/提交到接收器上的数据的实际装载量。 大小可能与`dataRead`大小不同，因为它涉及每个数据存储存储数据的方式。 | Int64 值，以字节为单位 |
| filesRead | 从基于文件的源读取的文件数。 | Int64 值（未指定单位） |
| filesWritten | 写入/提交到基于文件的接收器的文件数。 | Int64 值（未指定单位） |
| sourcePeakConnections | 复制活动运行期间与源数据存储建立的并发连接峰值数量。 | Int64 值（未指定单位） |
| sinkPeakConnections | 复制活动运行期间与接收器数据存储建立的并发连接峰值数量。 | Int64 值（未指定单位） |
| rowsRead | 从源读取的行数（不适用于二进制副本）。 | Int64 值（未指定单位） |
| rowsCopied | 复制到接收器的行数（不适用于二进制副本）。 | Int64 值（未指定单位） |
| rowsSkipped | 跳过的不兼容行数。 可通过将 `enableSkipIncompatibleRow` 设置为 true 来跳过不兼容的行。 | Int64 值（未指定单位） |
| copyDuration | 复制运行的持续时间。 | Int32 值，以秒为单位 |
| throughput | 数据传输速率。 | 浮点数，以 KBps 为单位 |
| sourcePeakConnections | 复制活动运行期间与源数据存储建立的并发连接峰值数量。 | Int32 值（无单位） |
| sinkPeakConnections| 复制活动运行期间与接收器数据存储建立的并发连接峰值数量。| Int32 值（无单位） |
| sqlDwPolyBase | 将数据复制到 SQL 数据仓库时是否使用了 PolyBase。 | Boolean |
| redshiftUnload | 从 Redshift 复制数据时是否使用了 UNLOAD。 | Boolean |
| hdfsDistcp | 从 HDFS 复制数据时是否使用了 DistCp。 | Boolean |
| effectiveIntegrationRuntime | 用来为活动运行提供支持的一个或多个集成运行时 (IR)，采用 `<IR name> (<region if it's Azure IR>)` 格式。 | 文本（字符串） |
| usedDataIntegrationUnits | 复制期间的有效数据集成单位。 | Int32 值 |
| usedParallelCopies | 复制期间的有效 parallelCopies。 | Int32 值 |
| redirectRowPath | 在 `redirectIncompatibleRowSettings` 中配置的 Blob 存储中已跳过的不兼容行的日志路径。 请参阅[容错](copy-activity-overview.md#fault-tolerance)。 | 文本（字符串） |
| executionDetails | 有关复制活动经历的各个阶段、相应步骤、持续时间、配置等的更多详细信息。 不建议分析此节，因为它有可能发生更改。 要更好地了解它如何帮助您理解和排除复制性能，请参阅[Monitor 视觉](#monitor-visually)部分。 | Array |
| perfRecommendation | 复制性能优化提示。 有关详细信息，请参阅[性能调优提示](copy-activity-performance-troubleshooting.md#performance-tuning-tips)。 | Array |

**例子：**

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