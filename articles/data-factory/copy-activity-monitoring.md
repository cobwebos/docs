---
title: 监视复制活动
description: 了解如何监视 Azure 数据工厂中的复制活动执行情况。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: jingwang
ms.openlocfilehash: 92119709aa260f3180c503a77064f6e80dece6e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440603"
---
# <a name="monitor-copy-activity"></a>监视复制活动

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何监视 Azure 数据工厂中的复制活动执行情况。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="monitor-visually"></a>直观地监视

在 Azure 数据工厂中创建并发布管道以后，即可将其与触发器关联，或者手动启动临时运行。 可以在 Azure 数据工厂用户体验中以本机方式监视所有管道运行。 参阅[直观监视 Azure 数据工厂](monitor-visually.md)，对 Azure 数据工厂监视进行常规了解。

若要监视复制活动运行，请转到数据工厂的“创作和监视”UI。 在“监视器”选项卡上，可以看到一个管道运行列表，单击“管道名称”链接即可访问管道运行中的活动运行的列表。

![监视管道运行](./media/copy-activity-overview/monitor-pipeline-run.png)

在此级别，可以看到复制活动输入、输出和错误的链接（如果复制活动运行失败）以及持续时间/状态等统计信息。 单击复制活动名称旁边的“详细信息”  按钮（眼镜）即可详细了解复制活动执行情况。 

![监视复制活动运行](./media/copy-activity-overview/monitor-copy-activity-run.png)

在此图形监视视图中，Azure 数据工厂会提供复制活动执行信息，其中包括数据读取/写入量、从源复制到接收器的数据的文件数/行数、吞吐量、针对复制方案应用的配置、使用相应的持续时间和详细信息执行复制活动的步骤，等等。 请参阅[此表](#monitor-programmatically)，了解每个可能的指标及其详细说明。 

在某些方案中，当你运行数据工厂中的复制活动时，会在复制活动监视视图顶部看到“性能调优提示”，如示例中所示。 这些提示告知 ADF 针对此特定复制运行确定的瓶颈，并建议如何进行更改，以便提升复制吞吐量。 详细了解[自动性能优化提示](copy-activity-performance-troubleshooting.md#performance-tuning-tips)。

底部的**执行详细信息和持续时间**描述了复制活动所要经历的重要阶段，这对于排查复制性能问题特别有用。 复制运行的瓶颈就是持续时间最长的那个运行。 请参阅[排查复制活动性能问题](copy-activity-performance-troubleshooting.md)，了解每个阶段所代表的内容以及详细的故障排除指南。

**示例：从 Amazon S3 复制到 Azure Data Lake Storage Gen2**

![监视复制活动运行详细信息](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>以编程方式监视

“复制活动运行结果” > “输出”部分（用于呈现 UI 监视视图）中也会返回复制活动执行详细信息和性能特征。 下面是可能返回的属性的完整列表。 只会显示适用于你的复制方案的属性。 若要对如何以编程方式监视活动运行进行常规了解，请参阅[以编程方式监视 Azure 数据工厂](monitor-programmatically.md)。

| 属性名称  | 说明 | 输出中的单位 |
|:--- |:--- |:--- |
| dataRead | 从源读取的实际数据量。 | Int64 值，以字节为单位 |
| DataWritten | 写入/提交到接收器的数据的实际装载。 大小可能不同于 `dataRead` 大小，因为它与每个数据存储存储数据的方式相关。 | Int64 值，以字节为单位 |
| filesRead | 从基于文件的源中读取的文件数。 | Int64 值（未指定单位） |
| filesWritten | 写入/提交到基于文件的接收器的文件数。 | Int64 值（未指定单位） |
| filesSkipped | 从基于文件的源中跳过的文件数。 | Int64 值（未指定单位） |
| dataConsistencyVerification | 数据一致性验证的详细信息，可在其中查看是否已验证复制的数据在源存储和目标存储之间的一致性。 有关详细信息，请参阅[本文](copy-activity-data-consistency.md#monitoring)。 | 数组 |
| sourcePeakConnections | 复制活动运行期间与源数据存储建立的并发连接峰值数量。 | Int64 值（未指定单位） |
| sinkPeakConnections | 复制活动运行期间与接收器数据存储建立的并发连接峰值数量。 | Int64 值（未指定单位） |
| rowsRead | 从源中读取的行数。 此指标不适用于不进行分析而按原样复制文件的情况，例如，当源和接收器数据集是二进制格式类型或具有相同设置的其他格式类型时。 | Int64 值（未指定单位） |
| rowsCopied | 复制到接收器的行数。 此指标不适用于不进行分析而按原样复制文件的情况，例如，当源和接收器数据集是二进制格式类型或具有相同设置的其他格式类型时。  | Int64 值（未指定单位） |
| rowsSkipped | 跳过的不兼容行数。 可通过将 `enableSkipIncompatibleRow` 设置为 true 来跳过不兼容的行。 | Int64 值（未指定单位） |
| copyDuration | 复制运行的持续时间。 | Int32 值，以秒为单位 |
| throughput | 数据传输速率。 | 浮点数，以 KBps 为单位 |
| sourcePeakConnections | 复制活动运行期间与源数据存储建立的并发连接峰值数量。 | Int32 值（无单位） |
| sinkPeakConnections| 复制活动运行期间与接收器数据存储建立的并发连接峰值数量。| Int32 值（无单位） |
| sqlDwPolyBase | 将数据复制到 Azure Synapse Analytics（以前称为 SQL 数据仓库）时是否使用了 PolyBase。 | 布尔 |
| redshiftUnload | 从 Redshift 复制数据时是否使用了 UNLOAD。 | 布尔 |
| hdfsDistcp | 从 HDFS 复制数据时是否使用了 DistCp。 | 布尔 |
| effectiveIntegrationRuntime | 用来为活动运行提供支持的一个或多个集成运行时 (IR)，采用 `<IR name> (<region if it's Azure IR>)` 格式。 | 文本（字符串） |
| usedDataIntegrationUnits | 复制期间的有效数据集成单位。 | Int32 值 |
| usedParallelCopies | 复制期间的有效 parallelCopies。 | Int32 值 |
| logPath | Blob 存储中跳过的数据的会话日志路径。 请参阅[容错](copy-activity-overview.md#fault-tolerance)。 | 文本（字符串） |
| executionDetails | 有关复制活动经历的各个阶段、相应步骤、持续时间、配置等的更多详细信息。 不建议分析此节，因为它有可能发生更改。 若要更好地了解如何通过它来了解复制性能并排查其问题，请参阅[以视觉方式进行监视](#monitor-visually)部分。 | Array |
| perfRecommendation | 复制性能优化提示。 有关详细信息，请参阅[性能优化提示](copy-activity-performance-troubleshooting.md#performance-tuning-tips)。 | Array |
| billingReference | 给定运行的计费用量。 了解更多 [活动-运行级别的监视消耗量](plan-manage-costs.md#monitor-consumption-at-activity-run-level)。 | 对象 |
| durationInQueue | 复制活动开始执行之前的排队持续时间（秒）。 | 对象 |

**示例：**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "filesSkipped": 0,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "dataConsistencyVerification": 
    { 
        "VerificationResult": "Verified", 
        "InconsistentData": "None" 
    },
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

\-[“复制活动”概述](copy-activity-overview.md)

\- [复制活动性能](copy-activity-performance.md)