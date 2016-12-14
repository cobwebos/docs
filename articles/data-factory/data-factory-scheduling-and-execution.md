---
title: "使用数据工厂计划和执行 | Microsoft Docs"
description: "了解 Azure 数据工厂应用程序模型的计划和执行方面。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: febc8fef864f88fa07accf91efc9b87727a48b32
ms.openlocfilehash: 8b1029075178fbc591645a5fd6a112ad0a7f8b86


---
# <a name="data-factory-scheduling-and-execution"></a>数据工厂计划和执行
本文介绍 Azure 数据工厂应用程序模型的计划和执行方面。 

## <a name="prerequisites"></a>先决条件
本文假定用户了解数据工厂应用程序模型概念的基础知识（包括活动、管道、链接服务和数据集）。 有关的 Azure 数据工厂的基本概念，请参阅以下文章：

* [数据工厂简介](data-factory-introduction.md)
* [管道](data-factory-create-pipelines.md)
* [数据集](data-factory-create-datasets.md) 

## <a name="schedule-an-activity"></a>计划活动
可借助活动 JSON 的计划程序部分指定活动的定期计划。 例如，可将活动计划为每小时进行一次，如下所示：

    "scheduler": {
        "frequency": "Hour",
        "interval": 1
    },  

![计划程序示例](./media/data-factory-scheduling-and-execution/scheduler-example.png)

如关系图中所示，为活动指定计划将创建一系列翻转窗口。 翻转时段是一系列固定大小、非重叠、连续的时间间隔。 活动的这些逻辑翻转时段称为“活动时段”。

对于当前正在执行的活动时段，可通过活动 JSON 中的 [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) 和 [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) 系统变量访问与活动时段关联的时间间隔。 可在活动 JSON 中将这些变量用于不同目的。 例如，可用于从表示时序数据的输入和输出数据集中选择数据。

**scheduler** 属性支持与数据集中的 **availability** 属性相同的子属性。 有关详细信息，请参阅[数据集可用性](data-factory-create-datasets.md#Availability)。 示例：按特定时间偏移量计划，或将模式设置为在活动时段间隔的开始或结束时对齐处理。

可以指定活动的 **scheduler** 属性，但它是**可选**属性。 如果要指定一个属性，它必须与在输出数据集定义中指定的频率匹配。 当前，输出数据集驱动计划，因此即使活动并未生成任何输出，也必须创建输出数据集。 如果活动没有任何输入，可以跳过创建输入数据集。

## <a name="time-series-datasets-and-data-slices"></a>时间序列数据集和数据切片
时序数据是连续的数据点序列，通常包括对一段时间间隔所做的连续测量。 时序数据的常见示例包括传感器数据和应用程序遥测数据。

使用数据工厂可以通过活动运行对时间序列数据进行批处理。 通常在输入数据到达和需生成输出数据时存在定期频率。 可通过指定数据集中的 **availability** 对此频率建模，如下所示：

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

活动运行使用和生成的每个数据单元称为数据切片。 下图显示了具有 1 个输入数据集和 1 个输出数据集的活动示例。 这些数据集的 **availability** 频率设置为每隔一小时。

![可用性计划程序](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

上图中显示了输入和输出数据集的每小时数据切片。 图中还显示已准备好进行处理的三个输入切片。 “上午 10-11 点”活动正在进行，生成“上午 10-11 点”输出切片。

可通过变量 [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) 和 [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables) 访问与正在数据集 JSON 中生成的当前切片关联的时间间隔。

目前，数据工厂要求活动中指定的计划与输出数据集的“可用性”中指定的计划完全匹配。 因此，**WindowStart**、**WindowEnd**、**SliceStart** 和 **SliceEnd** 始终映射到同一时间段和单个输出切片。

有关可用于可用性部分的不同属性的详细信息，请参阅[创建数据集](data-factory-create-datasets.md)。

## <a name="move-data-from-sql-database-to-blob-storage"></a>将数据从 SQL 数据库移到 Blob 存储
现在把一些操作结合到一起执行，方法是创建管道，此管道每小时将 Azure SQL 数据库表中的数据复制到 Azure Blob 存储。

**输入：Azure SQL 数据库数据集**

    {
        "name": "AzureSqlInput",
        "properties": {
            "published": false,
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }


在可用性部分中，**Frequency** 设置为 **Hour**，**interval** 设置为 **1**。

**输出：Azure Blob 存储数据集**

    {
        "name": "AzureBlobOutput",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
                "format": {
                    "type": "TextFormat"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%M"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%d"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%H"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


在可用性部分中，**Frequency** 设置为 **Hour**，**interval** 设置为 **1**。

**活动：复制活动**

    {
        "name": "SamplePipeline",
        "properties": {
            "description": "copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "name": "AzureSQLtoBlob",
                    "description": "copy activity",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 100000,
                            "writeBatchTimeout": "00:05:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureSQLInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
            ],
            "start": "2015-01-01T08:00:00Z",
            "end": "2015-01-01T11:00:00Z"
        }
    }


此示例演示频率设置为每小时的活动计划和数据集可用性部分。 此示例演示如何使用 **WindowStart** 和 **WindowEnd** 选择活动运行的相关数据，并使用相应 **folderPath** 将其复制到 blob。 **FolderPath** 参数化为每小时具有一个单独文件夹。

当执行“上午 8-11 点”之间的三个切片时，Azure SQL 数据库中的数据如下所示：

![示例输入](./media/data-factory-scheduling-and-execution/sample-input-data.png)

管道部署后，Azure blob 中的填充如下所示：

* 文件 mypath/2015/1/1/8/Data.&lt;Guid&gt;.txt，包含数据
  
         10002345,334,2,2015-01-01 08:24:00.3130000
         10002345,347,15,2015-01-01 08:24:00.6570000
         10991568,2,7,2015-01-01 08:56:34.5300000
  
  > [!NOTE]
  > &lt;Guid&gt; 将替换为实际 guid。 示例文件名称：Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
  > 
  > 
* 文件 mypath/2015/1/1/9/Data.&lt;Guid&gt;.txt，包含数据：
  
         10002345,334,1,2015-01-01 09:13:00.3900000
         24379245,569,23,2015-01-01 09:25:00.3130000
         16777799,21,115,2015-01-01 09:47:34.3130000
* 文件 mypath/2015/1/1/10/Data.&lt;Guid&gt;.txt，包含数据。

## <a name="active-period-for-pipeline"></a>管道的活动周期
[创建管道](data-factory-create-pipelines.md)介绍了通过设置 **start** 和 **end** 属性指定的管道的活动周期概念。

可设置过去管道活动周期的开始日期。 数据工厂将自动计算（回填）过去的所有数据切片，并开始处理。

## <a name="parallel-processing-of-data-slices"></a>并行处理数据切片
通过在活动 JSON 的策略部分中设置**并发**属性，用户可将回填数据切片配置为并行运行。 有关此属性的详细信息，请参阅[创建管道](data-factory-create-pipelines.md)。

## <a name="rerun-a-failed-data-slice"></a>重新运行失败的数据切片
可以以丰富的可视化方式监视切片的执行情况。 有关详细信息，请参阅[使用 Azure 门户边栏选项卡监视和管理管道](data-factory-monitor-manage-pipelines.md)或[监视和管理应用](data-factory-monitor-manage-app.md)。

请参考以下示例，其中演示了两个活动。 Activity1 将具有切片的时序数据集生成为输出，Activity2 将此输出用作输入来生成最终输出时序数据集。

![失败的切片](./media/data-factory-scheduling-and-execution/failed-slice.png)

该图显示在最近的三个片段中，为 Dataset2 生成“上午 9-10 点”切片时失败。 数据工厂自动跟踪时序数据集的依赖项。 因此，未启动“上午 9-10 点”下游切片的活动运行。

使用数据工厂监视和管理工具可以深入查看失败切片的诊断日志，从而轻松找到问题的根本原因并进行修复。 修复问题后，便可轻松地启动活动运行以生成失败切片。 有关如何重新运行和了解数据切片的状态转换的详细信息，请参阅[使用 Azure 门户边栏选项卡监视和管理管道](data-factory-monitor-manage-pipelines.md)或[监视和管理应用](data-factory-monitor-manage-app.md)。

重新运行“Dataset2”的“上午 9-10 点”切片后，数据工厂会在最终数据集上启动运行“上午 9-10 点”依赖切片。

![重新运行失败的切片](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="run-activities-in-a-sequence"></a>在序列中运行活动
通过将一个活动的输出数据集设置为另一个活动的输入数据集，可链接两个活动（两个活动先后运行）。 这两个活动可在相同管道中，也可在不同管道中。 仅当第一个活动成功完成后第二个活动才能执行。

例如，考虑以下情况：

1. 管道 P1 具有活动 A1（需要外部输入数据集 D1），并生成 输出数据集 D2。
2. 管道 P2 活具有动 A2（需要来自数据集 D2 的输入），并生成输出数据集 D3。

在此示例中，活动 A1 和 A2 位于不同管道。 当外部数据可用且达到计划可用性频率时，运行活动 A1。 来自 D2 的计划切片可用且达到计划可用性频率时，运行活动 A2。 如果数据集 D2 中的某个切片发生错误，则在该切片可用之前，A2 不会运行该切片。

“关系图”视图如下图所示：

![链接两个管道中的活动](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

如前文所述，活动可以位于相同管道。 两个活动位于相同管道的“关系图”视图如下图所示：

![链接相同管道中的活动](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### <a name="copy-sequentially"></a>按顺序复制
可以按顺序或以有序的方式依次运行多个复制操作。 例如，管道中的两个复制活动（CopyActivity1 和 CopyActivity2）可能具有以下输入数据输出数据集：   

CopyActivity1

输入：Dataset1。 输出：Dataset2。

CopyActivity2

输入：Dataset2。  输出：Dataset3。

仅当 CopyActivity1 已成功运行且 Dataset2 可用时，CopyActivity2 才会运行。

以下是示例管道 JSON：

    {
        "name": "ChainActivities",
        "properties": {
            "description": "Run activities in sequence",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "copyBehavior": "PreserveHierarchy",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset1"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob1ToBlob2",
                    "description": "Copy data from a blob to another"
                },
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset3"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob2ToBlob3",
                    "description": "Copy data from a blob to another"
                }
            ],
            "start": "2016-08-25T01:00:00Z",
            "end": "2016-08-25T01:00:00Z",
            "isPaused": false
        }
    }

请注意，示例中将第一个复制活动 (Dataset2) 的输出数据集指定为第二个活动的输入。 因此，仅当第一个活动的输出数据集准备就绪后，第二个活动才会运行。  

在示例中，CopyActivity2 可以具有不同的输入，如 Dataset3，但由于将 Dataset2 指定为 CopyActivity2 的输入，因此该活动仅在 CopyActivity1 完成后才可运行。 例如：

CopyActivity1

输入：Dataset1。 输出：Dataset2。

CopyActivity2

输入：Dataset3、Dataset2。 输出：Dataset4。

    {
        "name": "ChainActivities",
        "properties": {
            "description": "Run activities in sequence",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "copyBehavior": "PreserveHierarchy",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset1"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlobToBlob",
                    "description": "Copy data from a blob to another"
                },
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset3"
                        },
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset4"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob3ToBlob4",
                    "description": "Copy data from a blob to another"
                }
            ],
            "start": "2017-04-25T01:00:00Z",
            "end": "2017-04-25T01:00:00Z",
            "isPaused": false
        }
    }


请注意，示例中为第二个复制活动指定了两个输入数据集。 如果指定了多个输入，则仅将第一个输入数据集用于复制数据，其他数据集用作依赖项。 仅当满足以下条件后 CopyActivity2 才会启动：

* CopyActivity1 已成功完成且 Dataset2 可用。 将数据复制到 Dataset4 时不会使用此数据集。 它仅可充当 CopyActivity2 的计划依赖项。   
* Dataset3 可用。 此数据集表示复制到目标的数据。  

## <a name="model-datasets-with-different-frequencies"></a>具有不同频率的模型数据集
在这些示例中，输入和输出数据集的频率与活动计划时段相同。 某些方案需要以某一频率生成输出的功能，其中此频率不同于一个或多个输入的频率。 数据工厂支持对这些方案进行建模。

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>示例 1：为每小时可用的输入数据生成每日输出报告
请考虑这样一种方案，方案中具有来自 Azure Blob 存储中每小时可用的传感器的输入测量数据。 希望使用[数据工厂 hive 活动](data-factory-hive-activity.md)生成包含当天平均值、最大值和最小值等统计数据的每日汇总报告。

下面介绍如何使用数据工厂对这种方案建模：

**输入数据集**

删除文件夹中某给定日期的每小时输入文件。 输入的 Availability 设置为 **Hour** (frequency: Hour, interval: 1)。

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**输出数据集**

每天在当天的文件夹中创建一个输出文件。 输出的 Availability 设置为 **Day** (frequency: Day and interval: 1)。

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**活动：管道中的 hive 活动**

Hive 脚本接收相应的 *DateTime* 信息作为参数，这些参数使用 **WindowStart** 变量，如以下代码段中所示。 hive 脚本使用此变量从正确的文件夹加载当天数据，并运行聚合以生成输出。

        {  
            "name":"SamplePipeline",
            "properties":{  
            "start":"2015-01-01T08:00:00",
            "end":"2015-01-01T11:00:00",
            "description":"hive activity",
            "activities": [
                {
                    "name": "SampleHiveActivity",
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adftutorial\\hivequery.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                            "Month": "$$Text.Format('{0:%M}',WindowStart)",
                            "Day": "$$Text.Format('{0:%d}',WindowStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },            
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 2,
                        "timeout": "01:00:00"
                    }
                 }
             ]
           }
        }

下图从数据依赖项的角度演示该方案。

![数据依赖项](./media/data-factory-scheduling-and-execution/data-dependency.png)

每天的输出切片依赖于输入数据集的 24 小时切片。 数据工厂将自动计算这些依赖项，方法是计算在同一时间段内作为待生成输出切片的输入数据切片。 如果 24 个输入切片均不可用，数据工厂将等待输入切片准备就绪，然后启动每日活动运行。

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>示例 2：使用表达式和数据工厂函数指定依赖项
现在考虑下另一种方案。 假设某个 hive 活动处理两个输入数据集。 其中一个每日获取新数据，但另一个每周获取新数据。 假设希望联接两个输入并每天生成输出。

数据工厂通过对齐到输出数据切片时间段来自动计算相应的输入切片，这种简单方法不起作用。

必须为每个活动运行指定：数据工厂应对每周输入数据集使用上一周的数据切片。 使用以下代码段中所示的 Azure 数据工厂函数实现此行为。

**Input1：Azure blob**

第一个输入是每日更新的 Azure blob。

    {
      "name": "AzureBlobInputDaily",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Input2：Azure blob**

Input2 是每周更新的 Azure blob。

    {
      "name": "AzureBlobInputWeekly",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 7
        }
      }
    }

**输出：Azure blob**

每天在当天的文件夹中创建一个输出文件。 输出的 Availability 设置为 **day** (frequency: Day, interval: 1)。

    {
      "name": "AzureBlobOutputDaily",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**活动：管道中的 hive 活动**

hive 活动每天采用两个输入并且生成输出切片。 可将每天的输出切片指定为依赖每周输入的上一周输入切片，如下所示。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-01-01T08:00:00",
        "end":"2015-01-01T11:00:00",
        "description":"hive activity",
        "activities": [
          {
            "name": "SampleHiveActivity",
            "inputs": [
              {
                "name": "AzureBlobInputDaily"
              },
              {
                "name": "AzureBlobInputWeekly",
                "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
                "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutputDaily"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
              "scriptPath": "adftutorial\\hivequery.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                "Month": "$$Text.Format('{0:%M}',WindowStart)",
                "Day": "$$Text.Format('{0:%d}',WindowStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            },            
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 2,  
              "timeout": "01:00:00"
            }
           }
         ]
       }
    }


## <a name="data-factory-functions-and-system-variables"></a>数据工厂函数和系统变量
有关数据工厂支持的函数和系统变量列表，请参阅[数据工厂的函数和系统变量](data-factory-functions-variables.md)。

## <a name="data-dependency-deep-dive"></a>数据依赖项深入介绍
若要由活动运行生成数据集切片，数据工厂需使用以下依赖模型确定活动使用和生成的数据集之间的关系。

生成输出数据集切片所需的输入数据集的时间范围称为“依赖周期”。

仅当依赖周期内输入数据集中的数据切片可用后，活动运行才能生成数据集切片。 换言之，包含依赖周期的所有输入切片必须处于“就绪” 状态后，活动运行才能生成输出数据集切片。

若要生成数据集切片 [**start**, **end**]，函数必须将数据集切片映射到其依赖周期。 此函数实质上是公式，它将数据集切片的开始和结束转换为依赖时段的开始和结束时间。 更准确地讲：

    DatasetSlice = [start, end]
    DependecyPeriod = [f(start, end), g(start, end)]

**F** 和 **g** 是映射函数，用于计算每个活动输入的依赖周期的开始和结束时间。

如示例所示，依赖周期与生成的数据切片的周期相同。 在这些情况下，数据工厂自动计算依赖周期内的输入切片。  

例如，在聚合示例中，输出为每天生成，输入数据为每小时可用，数据切片周期为 24 小时。 数据工厂查找此周期的相关每小时输入，并使输出切片依赖于输入切片。

还可为依赖周期提供自己的映射，如示例所示，其中一个输入是每周执行，输入切片是每天生成。

## <a name="data-dependency-and-validation"></a>数据依赖项和验证
数据集可以具有定义的验证策略，该策略指定切片执行生成的数据在准备好进行使用之前应如何验证。 有关详细信息，请参阅[创建数据集](data-factory-create-datasets.md)。

在这种情况下，切片执行完成后，输出切片状态将变为“等待”且子状态为“验证”。 切片验证后，切片状态将更改为“就绪”。

如果数据切片已生成但没有通过验证，因此将不会处理依赖于此切片的下游切片的活动运行。

[监视和管理管道](data-factory-monitor-manage-pipelines.md)介绍数据工厂中的数据切片的各种状态。

## <a name="external-data"></a>外部数据
可将数据集标记为“外部”（如以下 JSON 代码段中所示），表示它不是由数据工厂生成。 在这种情况下，数据集策略可能具有描述验证的额外参数集，并为数据集重试策略。 请参阅[创建管道](data-factory-create-pipelines.md)了解所有属性的说明。

与数据工厂生成的数据集类似，外部数据的数据切片需准备就绪后才能处理依赖项切片。

    {
        "name": "AzureSqlInput",
        "properties":
        {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties":
            {
                "tableName": "MyTable"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1     
            },
            "external": true,
            "policy":
            {
                "externalData":
                {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }  
        }
    }


## <a name="onetime-pipeline"></a>一次性管道
在管道定义中指定的开始和结束时间内，可创建一个管道并计划为定期运行（例如：每小时或每天）。 请参阅[计划活动](#scheduling-and-execution)了解详细信息。 还可以创建只运行一次的管道。 若要执行此操作，请将管道定义中的 **pipelineMode** 属性设置为 **onetime**，如以下 JSON 示例所示。 此属性的默认值是 **scheduled**。

    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": false
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "InputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ]
                    "name": "CopyActivity-0"
                }
            ]
            "pipelineMode": "OneTime"
        }
    }

注意以下事项：

* 不指定管道的**开始**和**结束**时间。
* 指定输入和输出数据集的 **Availability**（**frequency** 和 **interval**），尽管数据工厂不使用该值。  
* “关系图”视图不显示一次性管道。 这是设计的行为。
* 一次性管道无法更新。 可对一次性管道执行克隆、重命名、更新属性，还可以对其部署以生成另外一个一次性管道。




<!--HONumber=Nov16_HO3-->


