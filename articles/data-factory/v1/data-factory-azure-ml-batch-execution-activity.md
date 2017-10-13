---
title: "使用 Azure 数据工厂创建预测数据管道 | Microsoft Docs"
description: "描述如何使用 Azure 数据工厂和 Azure 机器学习创建预测管道"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 38c0798bc14b094fa788e6e414bad35b34a93d2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>使用 Azure 机器学习和 Azure 数据工厂创建预测管道

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive 活动](data-factory-hive-activity.md) 
> * [Pig 活动](data-factory-pig-activity.md)
> * [MapReduce 活动](data-factory-map-reduce.md)
> * [Hadoop 流式处理活动](data-factory-hadoop-streaming-activity.md)
> * [Spark 活动](data-factory-spark.md)
> * [机器学习批处理执行活动](data-factory-azure-ml-batch-execution-activity.md)
> * [机器学习更新资源活动](data-factory-azure-ml-update-resource-activity.md)
> * [存储过程活动](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 活动](data-factory-usql-activity.md)
> * [.NET 自定义活动](data-factory-use-custom-activities.md)

## <a name="introduction"></a>介绍

### <a name="azure-machine-learning"></a>Azure 机器学习
通过 [Azure 机器学习](https://azure.microsoft.com/documentation/services/machine-learning/)，可以生成、测试和部署预测分析解决方案。 从高层次的角度来看，这可通过三个步骤完成：

1. **创建训练实验**。 使用 Azure 机器学习工作室执行此步骤。 机器学习工作室是一个协作式可视开发环境，可用于通过定型数据来定型和测试预测分析模型。
2. **将其转换为预测实验**。 利用现有数据定型模型后，便可将其用于对新数据进行评分，为评分准备并简化实验。
3. **将其部署为 Web 服务**。 可将评分实验作为 Azure Web 服务发布。 可通过此 Web 服务终结点向模型发送数据，并从模型接收结果预测。  

### <a name="azure-data-factory"></a>Azure 数据工厂
数据工厂是一项基于云的数据集成服务，可对数据**移动**和**转换**进行安排并使其自动化。 可以使用 Azure 数据工厂创建数据集成解决方案，从各种数据存储引入数据、转换/处理数据，并将结果数据发布到数据存储。

在数据工厂服务中可以创建数据管道来移动和转换数据，并按指定的计划（每小时、每日、每周等）运行管道。 该服务还提供丰富的可视化效果来显示数据管道之间的历史记录和依赖项，用户可以通过单个统一视图监视所有数据管道，轻松查明问题和设置监视警报。

有关 Azure 数据工厂服务的快速入门，请参阅文章 [Azure 数据工厂简介](data-factory-introduction.md)和[生成第一个管道](data-factory-build-your-first-pipeline.md)。

### <a name="data-factory-and-machine-learning-together"></a>结合数据工厂和机器学习
借助 Azure 数据工厂，可轻松创建相关管道，利用已发布的 [Azure 机器学习 Web 服务进行预测分析][azure-machine-learning]。 使用 Azure 数据工厂管道中的“批处理执行活动”，可调用 Azure 机器学习 Web 服务来预测批处理中的数据。 有关详细信息，请参阅[使用批处理执行活动调用 Azure 机器学习 Web 服务](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity)部分。

随着时间推移，需要使用新的输入数据集重新定型 Azure 机器学习评分实验中的预测模型。 执行以下步骤可重新定型数据工厂管道中的 Azure 机器学习模型：

1. 将训练实验（非预测实验）作为 Web 服务发布。 在 Azure 机器学习工作室中，如同在前一方案中将预测实验作为 Web 服务公开那样执行此步骤。
2. 使用 Azure 机器学习批处理执行活动调用训练实验的 Web 服务。 基本上，可使用 Azure 机器学习批处理执行活动调用定型 Web 服务和评分 Web 服务。

完成重新定型后，使用 **Azure ML 更新资源活动**通过新的定型模型更新评分 Web 服务（作为 Web 服务公开的预测实验）。 有关详细信息，请参阅[使用更新资源活动更新模型](data-factory-azure-ml-update-resource-activity.md)一文。

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>使用批处理执行活动调用 Web 服务
使用 Azure 数据工厂来安排数据移动和处理，并使用 Azure 机器学习进行批处理执行。 下方列出了顶级步骤：

1. 创建 Azure 机器学习链接服务。 需要以下值：

   1. 用于批处理执行 API 的**请求 URI**。 在 Web 服务页中，单击“批处理执行”链接可找到此请求 URI。
   2. 用于已发布 Azure 机器学习 Web 服务的 **API 密钥**。 单击已发布的 Web 服务可找到此 API 密钥。
   3. 使用 **AzureMLBatchExecution** 活动。

      ![机器学习仪表板](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![批处理 URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>方案：使用 Web 服务输入/输出（参考 Azure Blob 存储中的数据）的实验
在本方案中，Azure 机器学习 Web 服务使用 Azure Blob 存储中某文件的数据进行预测，并将预测结果存储在 Blob 存储中。 以下 JSON 定义了包含 AzureMLBatchExecution 活动的数据工厂管道。 该活动将数据集 **DecisionTreeInputBlob** 作为输入，**DecisionTreeResultBlob** 作为输出。 通过使用 **webServiceInput** JSON 属性，将 **DecisionTreeInputBlob** 作为输入传递给 Web 服务。 通过使用 **webServiceOutputs** JSON 属性，将 **DecisionTreeResultBlob** 作为输出传递给 Web 服务。  

> [!IMPORTANT]
> 如果 Web 服务需要多个输入，可改为使用 **webServiceInputs** 属性，而不是 **webServiceInput**。 有关使用 webServiceInputs 属性的示例，请参阅 [Web 服务需要多个输入](#web-service-requires-multiple-inputs)部分。
>
> **webServiceInput**/**webServiceInputs** 和 **webServiceOutputs** 属性（位于 **typeProperties** 中）引用的数据集也必须包含在活动 **inputs** 和 **outputs** 中。
>
> 在 Azure 机器学习实验中，Web 服务输入和输出端口及全局参数具有可自定义的默认名称（“input1”、“input2”）。 用于 webServiceInputs、webServiceOutputs 和 globalParameters 设置的名称必须与实验中的名称完全匹配。 可在 Azure 机器学习终结点的“批处理执行帮助”页上查看示例请求有效负载，验证预期映射。
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }                
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> 仅 AzureMLBatchExecution 活动的输入和输出可作为参数传递给 Web 服务。 例如，在上面的 JSON 片段中，DecisionTreeInputBlob 是作为输入通过 webServiceInput 参数传递给 Web 服务的 AzureMLBatchExecution 活动的输入。   
>
>

### <a name="example"></a>示例
本示例使用 Azure 存储保留输入和输出数据。

建议先浏览[使用数据工厂生成第一个管道][adf-build-1st-pipeline]教程，再浏览本示例。 本示例使用数据工厂编辑器创建数据工厂项目（链接服务、数据集、管道）。   

1. 为 **Azure 存储**创建**链接服务**。 如果输入和输出文件位于不同的存储帐户，需使用两个链接服务。 下面是 JSON 示例：

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. 创建**输入** Azure 数据工厂**数据集**。 与某些其他数据工厂数据集不同，这些数据集必须同时包含 **folderPath** 和 **fileName** 值。 可使用分区，使每个批处理执行（每个数据切片）进行处理或生成唯一的输入和输出文件。 可能需要包括某个上游活动才能将输入转换为 CSV 文件格式，并将其置于每个切片的存储帐户中。 在这种情况下，不会包括下方示例显示的 **external** 和 **externalData** 设置，DecisionTreeInputBlob 会成为不同活动的输出数据集。

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }
    ```

    输入 csv 文件必须包含列标题行。 如果使用**复制活动**创建 csv 或将其移动到 Blob 存储，应将接收器属性 **blobWriterAddHeader** 设置为 **true**。 例如：

    ```JSON
    sink:
    {
        "type": "BlobSink",     
        "blobWriterAddHeader": true
    }
    ```

    如果 csv 文件不含标题行，可能出现以下错误：**活动中出错：读取字符串时出错。意外令牌：StartObject。路径 ''、行 1、位置 1**。
3. 创建**输出** Azure 数据工厂**数据集**。 本示例使用分区为每个切片执行创建唯一输出路径。 如不使用分区，活动将覆盖文件。

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. 创建类型为 **AzureMLLinkedService** 的**链接服务**，提供 API 密钥和模型批处理执行 URL。

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. 最后，创建包含 **AzureMLBatchExecution** 活动的管道。 在运行时，管道执行以下步骤：

   1. 从输入数据集获取输入文件的位置。
   2. 调用 Azure 机器学习批处理执行 API
   3. 将批处理执行输出复制到输出数据集中给定的 blob。

      > [!NOTE]
      > AzureMLBatchExecution 活动可包含零个或多个输入和一个或多个输出。
      >
      >

    ```JSON
    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }                
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }
    ```

      **开始**和**结束**日期/时间必须采用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2014-10-14T16:32:41Z。 **结束**时间可选。 如果未指定 **end** 属性的值，则以“**开始时间 + 48 小时**”计算 若要无限期运行管道，请指定 **9999-09-09** 作为 **end** 属性的值。 有关 JSON 属性的详细信息，请参阅 [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) （JSON 脚本参考）。

      > [!NOTE]
      > 可选择指定 AzureMLBatchExecution 活动的输入。
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>方案：使用读取器/编写器模块参考各种存储中的数据的实验
创建 Azure 机器学习实验时的另一种常见方案是使用读取器和编写器模块。 读取器模块用于将数据加载到实验，编写器模块用于保存实验中的数据。 有关读取器和编写器模块的详细信息，请参阅 MSDN 库上的[读取器](https://msdn.microsoft.com/library/azure/dn905997.aspx)和[编写器](https://msdn.microsoft.com/library/azure/dn905984.aspx)主题。     

使用读取器和编写器模块时，最好对这些读取器/编写器模块的每个属性使用 Web 服务参数。 使用这些 Web 参数，可在运行时配置值。 例如，可通过使用 Azure SQL 数据库 XXX.database.windows.net 的读取器模块创建实验。 部署 Web 服务后，需使 Web 服务使用者能够指定另一个名为 YYY.database.windows.net 的 Azure SQL Server。 Web 服务参数可用于允许配置此值。

> [!NOTE]
> Web 服务输入和输出与 Web 服务参数不同。 在第一个方案中，已了解了可为 Azure 机器学习 Web 服务指定输入和输出的方法。 在此方案中，为 Web 服务传递对应于读取器/编写器模块属性的参数。
>
>

我们来看看使用 Web 服务参数的情况。 有一个已部署的 Azure 机器学习 Web 服务，它使用读取器模块从 Azure 机器学习支持的一个数据源（例如：Azure SQL 数据库）读取数据。 完成批处理执行后，使用读取器模块（Azure SQL 数据库）写入结果。  实验中未定义任何 Web 服务输入和输出。 在此情况下，建议为读取器和编写器模块配置相关的 Web 服务参数。 此配置允许在使用 AzureMLBatchExecution 活动时配置读取器/编写器模块。 在活动 JSON 中的 **globalParameters** 部分指定 Web 服务参数，如下所示。

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

还可使用[数据工厂函数](data-factory-functions-variables.md)传递 Web 服务参数的值，如下方示例所示：

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Web 服务参数区分大小写，因此请确保活动 JSON 中指定的名称与 Web 服务公开的名称相匹配。
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>使用读取器模块读取 Azure Blob 中多个文件的数据
含 Pig 和 Hive 等活动的大数据管道可以生成无扩展名的一个或多个输出文件。 例如，指定外部 Hive 表时，可将外部 Hive 表数据存储在 Azure Blob 存储中，并命名为 000000_0。 可在实验中使用读取器模块读取多个文件，并将文件用于预测。

在 Azure 机器学习实验中使用读取器模块时，可指定 Azure Blob 作为输入。 Azure Blob 存储中的文件可能是在 HDInsight 上运行的 Pig 和 Hive 脚本生成的输出文件（示例：000000_0）。 使用读取器模块，可通过配置“容器路径、目录/blob”读取文件（无扩展名）。 **容器路径**指向容器，**目录/blob** 指向包含如下图所示文件的文件夹。 星号，即 \*) **指定容器/文件夹中的所有文件（即，data/aggregateddata/year=2014/month-6/\*）**均作为实验的一部分读取。

![Azure Blob 属性](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>示例
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>内含 AzureMLBatchExecution 活动（带 Web 服务参数）的管道

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure ML model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }              
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

在上述 JSON 示例中：

* 部署的 Azure 机器学习 Web 服务使用读取器和编写器模块从 Azure SQL 数据库读取数据，或将数据写入 Azure SQL 数据库。 此 Web 服务公开以下四个参数：数据库服务器名、数据库名、服务器用户帐户名和服务器用户帐户密码。  
* **开始**和**结束**日期/时间必须采用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2014-10-14T16:32:41Z。 **结束**时间可选。 如果未指定 **end** 属性的值，则以“**开始时间 + 48 小时**”计算 若要无限期运行管道，请指定 **9999-09-09** 作为 **end** 属性的值。 有关 JSON 属性的详细信息，请参阅 [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) （JSON 脚本参考）。

### <a name="other-scenarios"></a>其他方案
#### <a name="web-service-requires-multiple-inputs"></a>Web 服务需要多个输入
如果 Web 服务需要多个输入，可改为使用 **webServiceInputs** 属性，而不是 **webServiceInput**。 **webServiceInputs** 引用的数据集也必须包括在活动 **inputs** 中。

在 Azure 机器学习实验中，Web 服务输入和输出端口及全局参数具有可自定义的默认名称（“input1”、“input2”）。 用于 webServiceInputs、webServiceOutputs 和 globalParameters 设置的名称必须与实验中的名称完全匹配。 可在 Azure 机器学习终结点的“批处理执行帮助”页上查看示例请求有效负载，验证预期映射。

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Web 服务不需要输入
Azure 机器学习批处理执行 Web 服务可用于运行不需要任何输入的任何工作流，例如 R 或 Python 脚本。 或者，可使用不会公开任何 GlobalParameters 的读取器模块配置实验。 在这种情况下，AzureMLBatchExecution 活动配置如下：

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }              
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Web 服务不需要输入/输出
Azure 机器学习批处理执行 Web 服务可能未配置任何 Web 服务输出。 在此示例中，未配置任何 Web 服务输入、输出或 GlobalParameters。 虽然在活动本身仍配置了一个输出，但不会将其视作 webServiceOutput。

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Web 服务使用读取器和编写器，且活动仅在其他活动成功时运行
可将 Azure 机器学习 Web 服务读取器和编写器模块配置为使用/不使用任何 GlobalParameters 运行。 但是，可能需要在使用数据集依赖项的管道中嵌入服务调用才能仅在某一上游处理完成时调用服务。 还可使用此方法，在批处理执行完成后触发其他某种操作。 在这种情况下，可使用活动输入和输出表示依赖项，无需将任何依赖项命名为 Web 服务输入或输出。

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

**结论**如下：

* 如果实验终结点使用 webServiceInput：它由 blob 数据集表示并包括在活动输入和 webServiceInput 属性中。 否则，省略 webServiceInput 属性。
* 如果实验终结点使用 webServiceOutput(s)：它们由 blob 数据集表示并包括在活动输输出和 webServiceOutputs 属性中。 实验中的每个输出名称映射活动输出和 webServiceOutputs。 否则，省略 webServiceOutputs 属性。
* 如果实验终结点公开 globalParameter(s)，则在活动 globalParameters 属性中将其作为键、值对提供。 否则，省略 globalParameters 属性。 键区分大小写。 可在值中使用 [Azure 数据工厂函数](data-factory-functions-variables.md)。
* 其他数据集可能包括在活动输入和输出属性，不需在活动 typeProperties 中引用。 这些数据集使用切片依赖项控制执行，否则会被 AzureMLBatchExecution 活动忽略。


## <a name="updating-models-using-update-resource-activity"></a>使用更新资源活动更新模型
完成重新定型后，使用 **Azure ML 更新资源活动**通过新的定型模型更新评分 Web 服务（作为 Web 服务公开的预测实验）。 有关详细信息，请参阅[使用更新资源活动更新模型](data-factory-azure-ml-update-resource-activity.md)一文。

### <a name="reader-and-writer-modules"></a>读取器和编写器模块
使用 Web 服务参数的常见方案是使用 Azure SQL 读取器和编写器。 读取器模块用于将数据加载到 Azure 机器学习工作室外数据管理服务的实验中。 编写器模块用于将实验数据保存到 Azure 机器学习工作室外的数据管理服务。  

有关 Azure Blob/Azure SQL 读取器/编写器的详细信息，请参阅 MSDN 库上的[读取器](https://msdn.microsoft.com/library/azure/dn905997.aspx)和[编写器](https://msdn.microsoft.com/library/azure/dn905984.aspx)主题。 上一节中的示例使用了 Azure Blob 读取器和 Azure Blob 编写器。 本部分讨论如何使用 Azure SQL 读取器和 Azure SQL 编写器。

## <a name="frequently-asked-questions"></a>常见问题
**问：**我有多个由大数据管道生成的文件。 可以使用 AzureMLBatchExecution 活动处理所有文件吗？

**答：**可以。 有关详细信息，请参阅**使用读取器模块读取 Azure Blob 中多个文件的数据**部分。

## <a name="azure-ml-batch-scoring-activity"></a>Azure 机器学习批处理评分活动
如果使用 **AzureMLBatchScoring** 活动集成 Azure 机器学习，建议使用最新的 **AzureMLBatchExecution** 活动。

Azure SDK 和 Azure PowerShell 的 2015 年 8 月版中引入了 AzureMLBatchExecution 活动。

如果希望继续使用 AzureMLBatchScoring 活动，请继续阅读本部分。  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>将 Azure 存储用于输入/输出的 Azure 机器学习批处理评分活动

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Web 服务参数
要指定 Web 服务参数的值，请将 **typeProperties** 部分添加到管道 JSON 中的 **AzureMLBatchScoringActivty** 部分，如下方示例所示：

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
还可使用[数据工厂函数](data-factory-functions-variables.md)传递 Web 服务参数的值，如下方示例所示：

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Web 服务参数区分大小写，因此请确保活动 JSON 中指定的名称与 Web 服务公开的名称相匹配。
>
>

## <a name="see-also"></a>另请参阅
* [Azure 博客文章：Azure 数据工厂和 Azure 机器学习入门](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
