---
title: 将 Azure 流分析与 Azure 机器学习集成
description: 本文介绍如何将 Azure 流分析作业与 Azure 机器学习模型集成。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 07fa72f086b676723279ee4b8efd927beb2692f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81481971"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>将 Azure 流分析与 Azure 机器学习（预览版）集成

你可以在 Azure 流分析作业中将机器学习模型作为用户定义的函数（UDF）来实现，以便对流式处理输入数据进行实时评分和预测。 [Azure 机器学习](../machine-learning/overview-what-is-azure-ml.md)允许你使用任何常用的开源工具，如 Tensorflow、Scikit-learn 或 PyTorch，以便准备、定型和部署模型。

> [!NOTE]
> 此功能处于公共预览版中。 仅可通过使用[流分析门户预览链接](https://aka.ms/asaportalpreview)来访问 Azure 门户上的此功能。 最新版本的适用于[Visual Studio 的流分析工具](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)也提供此功能。

## <a name="prerequisites"></a>必备条件

在将机器学习模型作为函数添加到流分析作业之前，请完成以下步骤：

1. 使用 Azure 机器学习将[模型部署为 web 服务](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)。

2. 评分脚本应具有 Azure 机器学习用来生成架构规范的[示例输入和输出](../machine-learning/how-to-deploy-and-where.md#example-entry-script)。 流分析使用该架构来了解 web 服务的函数签名。

3. 请确保 web 服务接受并返回 JSON 序列化数据。

4. 在[Azure Kubernetes Service](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target)上部署模型，以便进行大规模的生产部署。 如果 web 服务无法处理来自你的作业的请求数，则流分析作业的性能将降级，这会影响延迟。 目前尚不支持在 Azure 容器实例上部署的模型，但将在未来几个月内推出。

## <a name="add-a-machine-learning-model-to-your-job"></a>将机器学习模型添加到作业

可以直接从 Azure 门户将 Azure 机器学习函数添加到流分析作业。

1. 导航到 Azure 门户中的流分析作业，然后选择 "**作业拓扑**" 下的 "**函数**"。 然后，从 " **+ 添加**" 下拉菜单中选择 " **Azure ML 服务**"。

   ![添加 Azure ML UDF](./media/machine-learning-udf/add-azureml-udf.png)

2. 在**Azure 机器学习服务函数**窗体中填写以下属性值：

   ![配置 Azure ML UDF](./media/machine-learning-udf/configure-azureml-udf.png)

下表介绍了流分析中的 Azure ML 服务函数的每个属性。

|属性|说明|
|--------|-----------|
|函数别名|输入名称以在查询中调用函数。|
|订阅|你的 Azure 订阅。|
|Azure ML 工作区|用于将模型部署为 web 服务的 Azure 机器学习工作区。|
|部署|托管模型的 web 服务。|
|函数签名|从 API 的架构规范中推断出的 web 服务的签名。 如果签名加载失败，请检查是否已在评分脚本中提供了示例输入和输出，以自动生成架构。|
|每个分区的并行请求数|这是一项高级配置，用于优化大规模吞吐量。 此数字表示从作业的每个分区发送到 web 服务的并发请求数。 具有六个流式处理单位（SU）和更低版本的作业具有一个分区。 具有12个 SUs 的作业有两个分区，18个分区有三个分区，依此类推。<br><br> 例如，如果你的作业有两个分区，并且你将此参数设置为4，则将从你的作业向你的 web 服务发出8个并发请求。 在此公共预览版中，此值默认为20，无法更新。|
|最大批数|这是一个用于优化大规模吞吐量的高级配置。 此数字表示在发送到 web 服务的单个请求中将最大数目的事件进行批处理。|

## <a name="supported-input-parameters"></a>支持的输入参数

当流分析查询调用 Azure 机器学习 UDF 时，该作业将为 web 服务创建 JSON 序列化请求。 请求基于特定于模型的架构。 必须在评分脚本中提供示例输入和输出，才能[自动生成架构](../machine-learning/how-to-deploy-and-where.md)。 此架构允许流分析为任何支持的数据类型（如 numpy、pandas 和 PySpark）构造 JSON 序列化请求。 多个输入事件可以在单个请求中一起进行批处理。

以下流分析查询是如何调用 Azure 机器学习 UDF 的示例：

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

流分析仅支持为 Azure 机器学习函数传递一个参数。 在将数据作为输入传递给机器学习 UDF 之前，您可能需要准备数据。

## <a name="pass-multiple-input-parameters-to-the-udf"></a>向 UDF 传递多个输入参数

最常见的机器学习模型输入示例包括 numpy 数组和 DataFrames。 可以使用 JavaScript UDF 创建数组，并使用`WITH`子句创建 JSON 序列化的数据帧。

### <a name="create-an-input-array"></a>创建输入数组

你可以创建接受*N*个输入数量的 JavaScript UDF，并创建可用作 Azure 机器学习 UDF 输入的数组。

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

可以使用`WITH`子句创建 JSON 序列化的数据帧，可将其作为输入传递到 Azure 机器学习 UDF，如下所示。

下面的查询通过选择所需字段并使用数据帧作为 Azure 机器学习 UDF 的输入来创建一个数据帧。

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

以下 JSON 是来自前一个查询的示例请求：

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

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>优化 Azure 机器学习 Udf 的性能

将模型部署到 Azure Kubernetes 服务时，可以[分析模型来确定资源利用率](../machine-learning/how-to-deploy-and-where.md#profilemodel)。 你还可以[为你的部署启用 App Insights](../machine-learning/how-to-enable-app-insights.md) ，以便了解请求速率、响应时间和失败率。

如果有高事件吞吐量的方案，可能需要在流分析中更改以下参数，以在端到端延迟的情况下实现最佳性能：

1. 最大批处理计数。
2. 每个分区的并行请求数。

### <a name="determine-the-right-batch-size"></a>确定正确的批大小

部署 web 服务后，将从50开始发送包含不同批大小的示例请求，并按数百个顺序递增。 例如，200、500、1000、2000等。 你会注意到，在某个批大小之后，响应的延迟会增加。 响应增长延迟的时间点应为作业的最大批处理计数。

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>确定每个分区的并行请求数

最佳缩放后，流分析作业应能够将多个并行请求发送到 web 服务，并在几毫秒内收到响应。 Web 服务响应的延迟会直接影响流分析作业的滞后时间和性能。 如果从作业到 web 服务的调用花费了很长时间，则可能会出现水位线延迟的增加，还可能会显示囤积输入事件的数量增加。

若要防止此类延迟，请确保已为 Azure Kubernetes 服务（AKS）群集设置了[适当数量的节点和副本](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli)。 Web 服务高度可用并返回成功的响应，这一点很重要。 如果你的作业收到来自你的 web 服务的服务不可用响应（503），则它将以指数方式继续重试。 除 "成功（200）" 和 "服务不可用" （503）以外的任何响应都将导致作业失败。

## <a name="next-steps"></a>后续步骤

* [教程：Azure 流分析 JavaScript 用户定义的函数](stream-analytics-javascript-user-defined-functions.md)
* [通过 Azure 机器学习 Studio （经典）函数缩放流分析作业](stream-analytics-scale-with-machine-learning-functions.md)

