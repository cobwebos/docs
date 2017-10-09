---
title: "Azure 数据工厂中的 Execute Pipeline 活动 | Microsoft Docs"
description: "了解如何使用 Execute Pipeline 活动从一个数据工厂管道调用另一个数据工厂管道。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 81d631291c5b375cf7425b0089caf6a97ec3b598
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Execute Pipeline 活动
Execute Pipeline 活动允许一个数据工厂管道调用另一个管道。

> [!NOTE]
> 本文适用于目前处于预览状态的版本 2 数据工厂。 如果使用数据工厂服务版本 1（即正式版 (GA），请参阅[数据工厂 V1 文档](v1/data-factory-introduction.md)。

## <a name="syntax"></a>语法

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Type 属性
属性 | 说明 | 允许的值 | 必选
-------- | ----------- | -------------- | --------
name | Execute Pipeline 活动的名称。 | String | 是
type | 必须设置为：**ExecutePipeline**。 | String | 是
管道 | 管道引用此管道调用的依赖管道。 管道引用对象具有两个属性：**referenceName** 和 **type**。 referenceName 属性指定引用管道的名称。 type 属性必须设置为 PipelineReference。 | PipelineReference | 是
parameters | 传递给已调用管道的参数 | 将参数名映射为自变量值的 JSON 对象 | 否
waitOnCompletion | 定义活动执行是否等待从属管道执行完成。 | 默认值为 false。 | 布尔 | 否

## <a name="sample"></a>示例
此方案具有两个管道：

- **主管道** - 此管道具有调用已调用管道的 Execute Pipeline 活动。 主管道采用两个参数：`masterSourceBlobContainer` 和 `masterSinkBlobContainer`。
- **已调用管道** - 此管道具有一个 Copy 活动，该活动将数据从 Azure Blob 源复制到 Azure Blob 接收器。 已调用管道采用两个参数：`sourceBlobContainer` 和 `sinkBlobContainer`。

### <a name="master-pipeline-definition"></a>主管道定义

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobCountainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "datasets": [],
    "linkedServices": [],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>已调用管道定义

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "datasets": [
      {
        "name": "SourceBlobDataset",
        "properties": {
          "type": "AzureBlob",
          "typeProperties": {
            "folderPath": {
              "value": "@pipeline().parameters.sourceBlobContainer",
              "type": "Expression"
            },
            "fileName": "salesforce.txt"
          },
          "linkedServiceName": {
            "referenceName": "BlobStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      },
      {
        "name": "sinkBlobDataset",
        "properties": {
          "type": "AzureBlob",
          "typeProperties": {
            "folderPath": {
              "value": "@pipeline().parameters.sinkBlobContainer",
              "type": "Expression"
            }
          },
          "linkedServiceName": {
            "referenceName": "BlobStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ],
    "linkedServices": [
      {
        "name": "BlobStorageLinkedService",
        "properties": {
          "type": "AzureStorage",
          "typeProperties": {
            "connectionString": {
              "value": "DefaultEndpointsProtocol=https;AccountName=*****",
              "type": "SecureString"
            }
          }
        }
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="running-the-pipeline"></a>运行管道

若要运行该示例中的主管道，为 masterSourceBlobContainer 和 masterSinkBlobContainer 参数传递以下值： 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

主管道将这些值传递给已调用管道，如以下示例中所示： 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobCountainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动： 

- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
