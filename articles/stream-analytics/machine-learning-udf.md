---
title: 将 Azure 流分析与 Azure 机器学习集成
description: 本文介绍如何将 Azure 流分析作业与 Azure 机器学习模型相集成。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: e2277e2088a8cb386d6f19799b235d96e08959b0
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543429"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>将 Azure 流分析与 Azure 机器学习集成（预览版）

可以在 Azure 流分析作业中将机器学习模型实现为用户定义的函数 (UDF)，以便基于流输入数据执行实时评分和预测。 [Azure 机器学习](../machine-learning/overview-what-is-azure-ml.md)可让你使用任何流行的开源工具（例如 Tensorflow、scikit-learn 或 PyTorch）来准备、训练和部署模型。

## <a name="prerequisites"></a>先决条件

在将机器学习模型作为函数添加到流分析作业之前，请完成以下步骤：

1. 使用 Azure 机器学习[将模型部署为 Web 服务](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)。

2. 评分脚本应该包含由 Azure 机器学习用来生成架构规范的[示例输入和输出](../machine-learning/how-to-deploy-and-where.md)。 流分析使用该架构来理解 Web 服务的函数签名。 可以使用此[示例 swagger 定义](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/AzureML/swagger-example.json)作为参考，以确保已正确设置该定义。

3. 确保 Web 服务接受并返回 JSON 序列化的数据。

4. 在 [Azure Kubernetes 服务](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target)中部署模型，以进行大规模生产部署。 如果 Web 服务无法处理来自作业的请求数，流分析作业的性能将会下降，从而影响延迟。 仅当你使用 Azure 门户时，Azure 容器实例中部署的模型才受支持。 流分析中尚不支持使用[Azure 机器学习设计器](https://docs.microsoft.com/azure/machine-learning/concept-designer)生成的模型。

## <a name="add-a-machine-learning-model-to-your-job"></a>将机器学习模型添加到作业

可以直接从 Azure 门户将 Azure 机器学习函数添加到流分析作业。

1. 在 Azure 门户中导航到你的流分析作业，在“作业拓扑”下选择“函数”。  然后，从“+ 添加”下拉菜单中选择“Azure ML 服务”。 

   ![添加 Azure ML UDF](./media/machine-learning-udf/add-azureml-udf.png)

2. 在“Azure 机器学习服务函数”窗体中填写以下属性值：

   ![配置 Azure ML UDF](./media/machine-learning-udf/configure-azureml-udf.png)

下表描述了流分析中 Azure ML 服务函数的每个属性。

|properties|说明|
|--------|-----------|
|函数别名|输入一个名称以在查询中调用函数。|
|订阅|你的 Azure 订阅。|
|Azure ML 工作区|用于将模型部署为 Web 服务的 Azure 机器学习工作区。|
|部署|托管模型的 Web 服务。|
|函数签名|从 API 的架构规范推理出的 Web 服务签名。 如果签名无法加载，请检查是否已在评分脚本中提供了用于自动生成架构的示例输入和输出。|
|每个分区的并行请求数|这是一项高级配置，用于优化大规模吞吐量。 此数字表示从作业的每个分区发送到 Web 服务的并发请求数。 具有 6 个或更少流单元 (SU) 的作业有一个分区。 具有 12 个 SU 的作业有两个分区，具有 18 个 SU 的作业有三个分区，依此类推。<br><br> 例如，如果你的作业有两个分区，而你将此参数设置为 4，那么将会有 8 个并发请求从该作业发送到 Web 服务。 在目前的公共预览版中，此值默认为 20，且无法更新。|
|最大批数|这是一项高级配置，用于优化大规模吞吐量。 此数字表示在发送到 Web 服务的单个请求中要一起进行批处理的最大事件数。|

## <a name="supported-input-parameters"></a>支持的输入参数

当流分析查询调用某个 Azure 机器学习 UDF 时，作业将创建一个要发送到 Web 服务的 JSON 序列化请求。 该请求基于模型特定的架构。 必须在评分脚本中提供示例输入和输出，以[自动生成架构](../machine-learning/how-to-deploy-and-where.md)。 该架构使流分析能够为任何支持的数据类型（例如 numpy、pandas 和 PySpark）构造 JSON 序列化请求。 可以在单个请求中对多个输入事件一起进行批处理。

以下流分析查询示例演示如何调用 Azure 机器学习 UDF：

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

流分析仅支持为 Azure 机器学习函数传递一个参数。 在将数据作为输入传递给机器学习 UDF 之前，可能需要准备该数据。

## <a name="pass-multiple-input-parameters-to-the-udf"></a>向 UDF 传递多个输入参数

机器学习模型的最常见输入示例是 numpy 数组和数据帧。 可以使用 JavaScript UDF 创建数组，并使用 `WITH` 子句创建 JSON 序列化的数据帧。

### <a name="create-an-input-array"></a>创建输入数组

可以创建接受 *N* 个输入的 JavaScript UDF，并创建可用作 Azure 机器学习 UDF 输入的数组。

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

将 JavaScript UDF 添加到作业后，可以使用以下查询调用 Azure 机器学习 UDF：

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

以下 JSON 是一个示例请求：

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>创建 Pandas 或 PySpark 数据帧

可以使用 `WITH` 子句来创建可作为输入传递给 Azure 机器学习 UDF 的 JSON 序列化数据帧，如下所示。

以下查询通过选择所需字段创建一个数据帧，并使用该数据帧作为 Azure 机器学习 UDF 的输入。

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM input
```

以下 JSON 是来自上述查询的示例请求：

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>优化 Azure 机器学习 UDF 的性能

将模型部署到 Azure Kubernetes 服务时，可以[分析模型以确定资源利用率](../machine-learning/how-to-deploy-profile-model.md)。 还可以[为部署启用 App Insights](../machine-learning/how-to-enable-app-insights.md)，以了解请求速率、响应时间和失败率。

如果你的方案使用较高的事件吞吐量，那么你可能需要更改流分析中的以下参数，以实现最佳性能和较低的端到端延迟：

1. 最大批计数。
2. 每个分区的并行请求数。

### <a name="determine-the-right-batch-size"></a>确定适当的批大小

部署 Web 服务后，发送具有不同批大小的示例请求。批大小从 50 开始，以 100 为增量递增。 例如 200、500、1000、2000，等等。 你将发现，在达到特定的批大小后，响应延迟会增大。 该特定大小（超过该值后，响应延迟会增大）应是作业的最大批计数。

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>确定每个分区的并行请求数

经过最佳缩放后，流分析作业应该能够将多个并行请求发送到 Web 服务，并在几毫秒内收到响应。 Web 服务的响应延迟可能会直接影响流分析作业的延迟和性能。 如果从作业调用 Web 服务花费了很长时间，那么你可能会发现水印延迟增大，还可能会发现积压的输入事件数量增加。

若要防止此类延迟，请确保已为 Azure Kubernetes 服务 (AKS) 群集预配了[适当数量的节点和副本](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli)。 Web 服务高度可用并返回成功响应至关重要。 如果作业收到来自 Web 服务的“服务不可用”响应 (503)，它会使用指数退避不断重试。 除“成功”(200) 和“服务不可用”(503) 以外的任何响应都会导致作业进入失败状态。

## <a name="next-steps"></a>后续步骤

* [教程：Azure 流分析 JavaScript 用户定义的函数](stream-analytics-javascript-user-defined-functions.md)
* [使用 Azure 机器学习工作室（经典）函数缩放流分析作业](stream-analytics-scale-with-machine-learning-functions.md)

