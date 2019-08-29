---
title: Azure 数据工厂中的 ForEach 活动 | Microsoft Docs
description: ForEach 活动在管道中定义重复的控制流。 它用于循环访问集合并执行指定的活动。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: 319f4e722184ce840d43b8f23e61711851a6d4a0
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142465"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Azure 数据工厂中的 ForEach 活动
ForEach 活动在管道中定义重复的控制流。 此活动用于循环访问集合，并在循环中执行指定的活动。 此活动的循环实现类似于采用编程语言的 Foreach 循环结构。

## <a name="syntax"></a>语法
此属性在本文后面介绍。 项属性是集合，通过 `@item()` 引用集合中的每个项目，如以下语法中所示：  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 允许的值 | 必填
-------- | ----------- | -------------- | --------
name | For-Each 活动的名称。 | String | 是
type | 必须设置为 **ForEach** | String | 是
isSequential | 指定是否应按顺序或并行执行循环。  一次最多可以并行执行 20 个循环迭代。 例如，如果你有 ForEach 活动，在 **isSequential** 设置为 False 的情况下循环访问含有 10 个不同源和接收器数据集的复制活动，所有副本都执行一次。 默认值为 false。 <br/><br/> 如果“isSequential”被设置为 False，则确保有运行多个可执行文件的正确配置。 否则，应谨慎使用此属性，以避免产生写入冲突。 有关详细信息，请参阅[并行执行](#parallel-execution)部分。 | Boolean | 否。 默认值为 false。
batchCount | 要用于控制并行执行数的批计数（当 isSequential 设为 false 时）。 | 整数（最大值为 50） | 否。 默认值为 20。
项 | 返回要循环访问的 JSON 数组的表达式。 | 表达式（返回 JSON 数组） | 是
activities | 要执行的活动。 | 活动列表 | 是

## <a name="parallel-execution"></a>并行执行
如果 **isSequential** 被设置为 False，则活动以并行方式迭代，最多包含 20 个并发迭代。 应谨慎使用此设置。 如果并发迭代写入同一文件夹中的不同文件，此方法仍然适用。 如果并发迭代以并发的方式写入同一文件，则此方法很有可能出错。 

## <a name="iteration-expression-language"></a>迭代表达式语言
在 ForEach 活动中，为属性 **items** 提供要循环访问的数组。 使用 `@item()` 循环访问 ForEach 活动中的单个枚举。 例如，如果 **items** 是数组：[1, 2, 3]，则 `@item()` 在第一次迭代中返回 1，在第二次迭代中返回 2，在第三次迭代中返回 3。

## <a name="iterating-over-a-single-activity"></a>循环访问单个活动
**场景：** 从 Azure Blob 中的同一源文件复制到 Azure Blob 中的多个目标文件。

### <a name="pipeline-definition"></a>管道定义

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Blob 数据集定义

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>运行参数值

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>循环访问多个活动
可以循环访问 ForEach 活动中的多个活动（例如：复制和 Web 活动）。 在此方案中，我们建议将多个活动摘录到单独的管道。 然后，可以将管道中的 [ExecutePipeline 活动](control-flow-execute-pipeline-activity.md)与 ForEach 活动结合使用，以调用含有多个活动的单独管道。 


### <a name="syntax"></a>语法

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>示例
**场景：** 在包含执行管道活动的 ForEach 活动中循环访问 InnerPipeline。 内部管道使用参数化的架构定义进行复制。

#### <a name="master-pipeline-definition"></a>主管道定义

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>内部管道定义

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>源数据集定义

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>接收器数据集定义

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>主管道参数
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>聚合输出

若要聚合 foreach 活动的输出，请使用 Variables 和 Append Variable 活动。

首先，在管道中声明 `array` 变量。 然后，在每个 foreach 循环内调用追加变量活动。 随后，你可以从数组中检索聚合。

## <a name="limitations-and-workarounds"></a>限制和解决方法

以下是 ForEach 活动的一些限制以及建议的解决方法。

| 限制 | 解决方法 |
|---|---|
| 不能将 ForEach 循环嵌套在另一个 ForEach 循环（或 Until 循环）中。 | 设计一个两级管道，其中具有外部 ForEach 循环的外部管道使用嵌套循环对内部管道进行迭代。 |
| 对于并行处理，ForEach 活动的最大 `batchCount` 为 50，最大项数为 100,000 个。 | 设计一个两级管道，其中具有 ForEach 活动的外部管道对内部管道进行迭代。 |
| | |

## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动： 

- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
