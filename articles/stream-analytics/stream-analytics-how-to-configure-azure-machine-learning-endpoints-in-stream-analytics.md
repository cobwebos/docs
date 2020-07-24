---
title: 在 Azure 流分析中使用机器学习终结点
description: 本文介绍如何在 Azure 流分析中使用机器语言用户定义的函数。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/11/2019
ms.openlocfilehash: 33d1e73523ca85a21c032729b6537ee3297ea23e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87020825"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>流分析中的 Azure 机器学习工作室（经典）集成（预览）
流分析支持调用 Azure 机器学习工作室（经典）终结点的用户定义函数。 [流分析 REST API 库](https://msdn.microsoft.com/library/azure/dn835031.aspx)中详细介绍了对此功能的 REST API 支持。 本文提供在流分析中成功实现此功能所需的补充信息。 也可在 [此处](stream-analytics-machine-learning-integration-tutorial.md)获取已发布的教程。

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>概述：Azure 机器学习工作室（经典）术语
Microsoft Azure 机器学习工作室（经典）提供一个协作型拖放式工具，可用于根据数据生成、测试和部署预测分析解决方案。 此工具称为“Azure 机器学习工作室（经典）”。 该工作室用于与机器学习资源交互，能够轻松地生成、测试和反复调整设计。 这些资源及其定义如下：

* **工作区**：工作区是保存所有其他机器学习资源以进行管理和控制的容器。
* **试验**：试验由数据科学家创建，以便利用数据集和定型机器学习模型。
* **终结点**：终结点是 Azure 机器学习工作室（经典）对象，用于以特征作为输入、应用指定的机器学习模型并返回已评分输出。
* **评分 Web 服务**：评分 Web 服务是终结点的集合，如上所述。

每个终结点都具有适用于批处理执行和同步执行的 API。 流分析使用同步执行。 在 Azure 机器学习工作室（经典）中，将特定的服务命名为[请求/响应服务](../machine-learning/studio/consume-web-services.md)。

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>流分析作业所需的机器学习资源
若要处理流分析作业，必须具有请求/响应终结点、 [apikey](https://docs.microsoft.com/azure/machine-learning/studio/consume-web-services)和 swagger 定义才能成功执行。 流分析拥有其他终结点，可构造 swagger 终结点的 URL、查找接口以及向用户返回默认 UDF 定义。

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>通过 REST API 配置流分析和机器学习 UDF
使用 REST API，可配置作业来调用 Azure 机器语言函数。 步骤如下：

1. 创建流分析作业
2. 定义输入
3. 定义输出
4. 创建用户定义函数 (UDF)
5. 编写调用 UDF 的流分析转换
6. 启动作业

## <a name="creating-a-udf-with-basic-properties"></a>使用基本属性创建 UDF
例如，以下示例代码创建名为 newudf 的标量 UDF，该 UDF 绑定到 Azure 机器学习工作室（经典）终结点。 请注意，*终结点*（服务 URI）可以在所选服务的 API 帮助页上找到，而 *apiKey* 可以在服务主页上找到。

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

请求正文示例：

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>调用默认 UDF 的 RetrieveDefaultDefinition 终结点
创建主干 UDF 后，需要 UDF 的完整定义。 RetreiveDefaultDefinition 终结点可帮助获取绑定到 Azure 机器学习工作室（经典）终结点的标量函数的默认定义。 下面的有效负载要求获取绑定到 Azure 机器学习终结点的标量函数的默认 UDF 定义。 它不指定实际的终结点，因为已在 PUT 请求期间提供。 流分析会调用请求中提供的终结点（如果已显式提供。 否则，会使用原来引用的终结点。 此处 UDF 采用单个字符串参数（一个句子），并返回类型字符串的单个输出以指示该句子的“情绪”标签。

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

请求正文示例：

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

其示例输出类似如下所示。

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>使用响应修补 UDF
现在，必须使用之前的响应修补 UDF，如下所示。

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

请求正文（来自 RetrieveDefaultDefinition 的输出）：

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>实现流分析转换以调用 UDF
现在，针对每个输入事件查询 UDF（此处名为 scoreTweet）并将该事件的响应写入到输入。

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>获取帮助
如需进一步的帮助，请参阅[有关 Azure 流分析的 Microsoft 问答页](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
