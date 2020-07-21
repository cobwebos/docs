---
title: 创建预测数据管道
description: 了解如何在 Azure 数据工厂中使用 Azure 机器学习 Studio （经典）批处理执行活动创建预测管道。
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/16/2020
ms.openlocfilehash: dabb7b8cd8023fe88a8c8d6dc507a09623bd11dd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537674"
---
# <a name="create-a-predictive-pipeline-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>使用 Azure 机器学习 Studio （经典）和 Azure 数据工厂创建预测管道

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [当前版本](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

通过[Azure 机器学习 Studio （经典）](https://azure.microsoft.com/documentation/services/machine-learning/) ，可生成、测试和部署预测分析解决方案。 从高层次的角度来看，这可通过三个步骤完成：

1. **创建训练实验**。 可以通过使用 Azure 机器学习 Studio （经典）来执行此步骤。 Azure 机器学习 Studio （经典）是一个协作式可视开发环境，可用于使用定型数据来定型和测试预测分析模型。
2. **将其转换为预测实验**。 利用现有数据定型模型后，便可将其用于对新数据进行评分，为评分准备并简化实验。
3. **将其部署为 Web 服务**。 可将评分实验作为 Azure Web 服务发布。 可通过此 Web 服务终结点向模型发送数据，并从模型接收结果预测。

### <a name="data-factory-and-azure-machine-learning-studio-classic-together"></a>数据工厂和 Azure 机器学习 Studio （经典）结合在一起
使用 Azure 数据工厂，可轻松创建使用已发布的[Azure 机器学习 Studio （经典）](https://azure.microsoft.com/documentation/services/machine-learning) web 服务进行预测分析的管道。 使用 Azure 数据工厂管道中的**批处理执行活动**，可以调用 Azure 机器学习 Studio （经典） web 服务来预测批处理中的数据。

随着时间的推移，需要使用新的输入数据集重新训练 Azure 机器学习 Studio （经典）评分实验中的预测模型。 可以通过执行以下步骤来重新训练数据工厂管道中的模型：

1. 将训练实验（非预测实验）作为 Web 服务发布。 在 Azure 机器学习 Studio （经典）中执行此步骤，就像在前面的方案中将预测实验作为 web 服务公开。
2. 使用 Azure 机器学习 Studio （经典） "批处理执行" 活动为训练试验调用 web 服务。 基本上，可以使用 Azure 机器学习 Studio （经典） "批处理执行" 活动调用定型 web 服务和评分 web 服务。

完成重新训练后，使用 " **Azure 机器学习 Studio （经典）" 更新资源活动**，通过新的训练模型更新评分 web 服务（作为 web 服务公开的预测实验）。 有关详细信息，请参阅[使用更新资源活动更新模型](update-machine-learning-models.md)一文。

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Azure 机器学习 Studio （经典）链接服务

创建**Azure 机器学习 studio （经典）** 链接服务，将 Azure 机器学习 Studio （经典） Web 服务链接到 Azure 数据工厂。 Azure 机器学习 Studio （经典） "批处理执行" 活动和 "[更新资源" 活动](update-machine-learning-models.md)使用链接服务。

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

请参阅[计算链接服务](compute-linked-services.md)一文，获取有关 JSON 定义中的属性的说明。

Azure 机器学习 Studio （经典）支持经典 Web 服务和新的 Web 服务，用于预测试验。 可以从数据工厂选择要使用的合适 Web 服务。 若要获取创建 Azure 机器学习 Studio （经典）链接服务所需的信息，请转到 https://services.azureml.net ，其中列出了所有（新的） Web 服务和经典 Web 服务。 单击要访问的 Web 服务，然后单击“使用”**** 页。 复制**主密钥**作为 **apiKey** 属性，复制**批处理请求**作为 **mlEndpoint** 属性。

![Azure 机器学习 Studio （经典） Web 服务](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Azure 机器学习 Studio （经典）批处理执行活动

下面的 JSON 代码段定义了一个 Azure 机器学习 Studio （经典） "批处理执行" 活动。 活动定义具有对之前创建的 Azure 机器学习 Studio （经典）链接服务的引用。

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| properties          | 说明                              | 必需 |
| :---------------- | :--------------------------------------- | :------- |
| name              | 管道中活动的名称     | 是      |
| description       | 描述活动用途的文本。  | 否       |
| type              | 对于 Data Lake Analytics U-SQL 活动，活动类型为**AzureMLBatchExecution**。 | 是      |
| linkedServiceName | 将服务链接到 Azure 机器学习 Studio （经典）链接服务。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。 | 是      |
| webServiceInputs  | 键、值对，用于映射 Azure 机器学习 Studio （经典） Web 服务输入的名称。 密钥必须与已发布的 Azure 机器学习 Studio （经典） Web 服务中定义的输入参数相匹配。 值是 Azure 存储链接服务和指定输入 Blob 位置的 FilePath 属性对。 | 否       |
| webServiceOutputs | 键、值对，用于映射 Azure 机器学习 Studio （经典） Web 服务输出的名称。 密钥必须与已发布的 Azure 机器学习 Studio （经典） Web 服务中定义的输出参数匹配。 值是 Azure 存储链接服务和指定输出 Blob 位置的 FilePath 属性对。 | 否       |
| globalParameters  | 要传递到 Azure 机器学习 Studio （经典）批处理执行服务终结点的键、值对。 密钥必须与已发布的 Azure 机器学习 Studio （经典） web 服务中定义的 web 服务参数的名称匹配。 在 Azure 机器学习 Studio （经典）批处理执行请求的 GlobalParameters 属性中传递值 | 否       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>方案 1：使用 Web 服务输入/输出（引用 Azure Blob 存储中的数据）的试验

在此方案中，Azure 机器学习 Studio （经典） Web 服务使用 Azure blob 存储中的文件中的数据进行预测，并将预测结果存储在 blob 存储中。 以下 JSON 定义了包含 AzureMLBatchExecution 活动的数据工厂管道。 使用 LinkedName 和 FilePath 对引用 Azure Blog 存储中的输入和输出数据。 在输入和输出的示例链接服务中，可以对每个输入/输出使用不同的链接服务，以便数据工厂能够选取正确的文件并发送到 Azure 机器学习 Studio （经典） Web 服务。

> [!IMPORTANT]
> 在 Azure 机器学习 Studio （经典）试验中，web 服务输入和输出端口及全局参数具有可自定义的默认名称（"input1" 和 "input2"）。 用于 webServiceInputs、webServiceOutputs 和 globalParameters 设置的名称必须与实验中的名称完全匹配。 您可以在 "Azure 机器学习 Studio （经典）" 终结点的 "批处理执行帮助" 页上查看示例请求负载，以验证所需的映射。


```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>方案 2：使用读取器/写入器模块引用各种存储中的数据的试验
创建 Azure 机器学习 Studio （经典）试验的另一种常见方案是使用 "导入数据" 和 "输出数据" 模块。 “导入数据”模块用于将数据加载到试验，“输出数据”模块用于保存试验中的数据。 有关“导入数据”和“输出数据”模块的详细信息，请参阅 MSDN 库中的[导入数据](https://msdn.microsoft.com/library/azure/dn905997.aspx)和[输出数据](https://msdn.microsoft.com/library/azure/dn905984.aspx)主题。

使用“导入数据”和“输出数据”模块时，最好对这些模块的每个属性使用 Web 服务参数。 使用这些 Web 参数，可在运行时配置值。 例如，可通过使用 Azure SQL 数据库 XXX.database.windows.net 的“导入数据”模块创建试验。 部署 web 服务后，你想要使 web 服务的使用者可以指定另一个名为的逻辑 SQL server `YYY.database.windows.net` 。 Web 服务参数可用于允许配置此值。

> [!NOTE]
> Web 服务输入和输出与 Web 服务参数不同。 在第一个方案中，你已了解如何为 Azure 机器学习 Studio （经典） Web 服务指定输入和输出。 在此方案中，为 Web 服务传递对应于“导入数据”/“输出数据”模块属性的参数。

我们来看看使用 Web 服务参数的情况。 已部署 Azure 机器学习 Studio （经典） web 服务，该服务使用 "读取器" 模块从 Azure 机器学习 Studio （经典）支持的某个数据源中读取数据（例如： Azure SQL 数据库）。 完成批处理执行后，使用读取器模块（Azure SQL 数据库）写入结果。  实验中未定义任何 Web 服务输入和输出。 在此情况下，建议为读取器和编写器模块配置相关的 Web 服务参数。 此配置允许在使用 AzureMLBatchExecution 活动时配置读取器/编写器模块。 在活动 JSON 中的 **globalParameters** 部分指定 Web 服务参数，如下所示。

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> Web 服务参数区分大小写，因此请确保活动 JSON 中指定的名称与 Web 服务公开的名称相匹配。

完成重新训练后，使用 " **Azure 机器学习 Studio （经典）" 更新资源活动**，通过新的训练模型更新评分 web 服务（作为 web 服务公开的预测实验）。 有关详细信息，请参阅[使用更新资源活动更新模型](update-machine-learning-models.md)一文。

## <a name="next-steps"></a>后续步骤
参阅以下文章了解如何以其他方式转换数据：

* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Hive 活动](transform-data-using-hadoop-hive.md)
* [Pig 活动](transform-data-using-hadoop-pig.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 流式处理活动](transform-data-using-hadoop-streaming.md)
* [Spark 活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [存储过程活动](transform-data-using-stored-procedure.md)
