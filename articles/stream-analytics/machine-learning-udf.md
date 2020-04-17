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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481971"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>将 Azure 流分析与 Azure 机器学习（预览）集成

您可以在 Azure 流分析作业中实现机器学习模型作为用户定义的函数 （UDF），以便对流式输入数据进行实时评分和预测。 [Azure 机器学习](../machine-learning/overview-what-is-azure-ml.md)允许您使用任何流行的开源工具（如 Tensorflow、scikit-学习或 PyTorch）来准备、训练和部署模型。

> [!NOTE]
> 此功能处于公共预览版中。 只能通过使用[流分析门户预览链接](https://aka.ms/asaportalpreview)在 Azure 门户上访问此功能。 此功能也可在[最新版本的流分析工具为可视化工作室](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)提供。

## <a name="prerequisites"></a>先决条件

在将机器学习模型作为函数添加到流分析作业之前，请完成以下步骤：

1. 使用 Azure 机器学习[将模型部署为 Web 服务](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)。

2. 评分脚本应具有 Azure 机器学习用于生成架构规范[的示例输入和输出](../machine-learning/how-to-deploy-and-where.md#example-entry-script)。 流分析使用架构来了解 Web 服务的函数签名。

3. 请确保您的 Web 服务接受并返回 JSON 序列化数据。

4. 在 Azure[库伯奈斯服务](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target)上部署模型，用于大规模生产部署。 如果 Web 服务无法处理来自作业的请求数，则流分析作业的性能将降低，从而影响延迟。 部署在 Azure 容器实例上的模型今天不受支持，但将在未来几个月提供。

## <a name="add-a-machine-learning-model-to-your-job"></a>将机器学习模型添加到作业中

可以直接从 Azure 门户将 Azure 机器学习功能添加到流分析作业。

1. 导航到 Azure 门户中的流分析作业，然后选择**作业拓扑**下的**函数**。 然后，从 **+ 添加**下拉菜单中选择 Azure **ML 服务**。

   ![添加 Azure ML UDF](./media/machine-learning-udf/add-azureml-udf.png)

2. 使用以下属性值填写**Azure 机器学习服务函数**窗体：

   ![配置 Azure ML UDF](./media/machine-learning-udf/configure-azureml-udf.png)

下表描述了流分析中 Azure ML 服务函数的每个属性。

|properties|说明|
|--------|-----------|
|函数别名|输入名称以在查询中调用函数。|
|订阅|您的 Azure 订阅。|
|Azure ML 工作区|用于将模型部署为 Web 服务的 Azure 机器学习工作区。|
|部署|托管模型的 Web 服务。|
|函数签名|从 API 的架构规范推断出 Web 服务的签名。 如果签名无法加载，请检查您在评分脚本中提供了示例输入和输出以自动生成架构。|
|每个分区的并行请求数|这是优化大规模吞吐量的高级配置。 此数字表示从作业的每个分区发送到 Web 服务的并发请求。 具有六个流式处理单元 （SU） 和较低的作业有一个分区。 具有 12 个 S 的作业有两个分区，18 个 SUs 有三个分区，等等。<br><br> 例如，如果作业有两个分区，并且将此参数设置为 4 个，则作业将同时向 Web 服务请求八个。 在公开预览的这个时候，此值默认为 20，无法更新。|
|最大批数|这是用于优化大规模吞吐量的高级配置。 此数字表示发送到 Web 服务的单个请求中一起批处理的最大事件数。|

## <a name="supported-input-parameters"></a>支持的输入参数

当流分析查询调用 Azure 机器学习 UDF 时，作业将创建到 Web 服务的 JSON 序列化请求。 请求基于特定于模型的架构。 您必须在评分脚本中提供示例输入和输出，才能[自动生成架构](../machine-learning/how-to-deploy-and-where.md)。 该架构允许流分析为任何受支持的数据类型（如 numpy、熊猫和 PySpark）构造 JSON 序列化请求。 多个输入事件可以在单个请求中批处理在一起。

以分析查询是如何调用 Azure 机器学习 UDF 的示例：

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

流分析仅支持为 Azure 机器学习函数传递一个参数。 您可能需要先准备数据，然后再将其作为输入传递给机器学习 UDF。

## <a name="pass-multiple-input-parameters-to-the-udf"></a>将多个输入参数传递给 UDF

机器学习模型输入的最常见示例是数字数组和 DataFrame。 您可以使用 JavaScript UDF 创建数组，并使用 子`WITH`句创建 JSON 序列化的数据框架。

### <a name="create-an-input-array"></a>创建输入数组

您可以创建一个 JavaScript UDF，它接受*N*个输入数，并创建可用作 Azure 机器学习 UDF 输入的数组。

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

### <a name="create-a-pandas-or-pyspark-dataframe"></a>创建熊猫或 PySpark 数据框架

可以使用 子`WITH`句创建 JSON 序列化数据帧，该帧可以作为输入传递给 Azure 机器学习 UDF，如下所示。

以下查询通过选择必要的字段创建 DataFrame，并使用 DataFrame 作为 Azure 机器学习 UDF 的输入。

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

以下 JSON 是上一个查询的示例请求：

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

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>优化 Azure 机器学习 UF 的性能

将模型部署到 Azure 库伯奈斯服务时，可以[分析模型以确定资源利用率](../machine-learning/how-to-deploy-and-where.md#profilemodel)。 您还可以[为部署启用应用见解](../machine-learning/how-to-enable-app-insights.md)，以了解请求率、响应时间和失败率。

如果方案具有高事件吞吐量，则可能需要更改流分析中的以下参数，以便在低端到端延迟时实现最佳性能：

1. 最大批数。
2. 每个分区的并行请求数。

### <a name="determine-the-right-batch-size"></a>确定正确的批次大小

部署 Web 服务后，发送具有不同批处理大小的样本请求，从 50 开始，以数百个顺序增加。 例如，200、500、1000、2000 等。 您会注意到，在一定的批处理大小之后，响应的延迟会增加。 响应延迟增加的点应该是作业的最大批处理计数。

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>确定每个分区的并行请求数

在最佳缩放时，流分析作业应该能够向 Web 服务发送多个并行请求，并在几毫秒内获得响应。 Web 服务响应的延迟可能会直接影响流分析作业的延迟和性能。 如果从作业到 Web 服务的呼叫需要很长时间，则水印延迟可能会增加，并且可能还会看到积压的输入事件数增加。

为了防止此类延迟，请确保 Azure 库伯奈斯服务 （AKS） 群集已预配了[正确数量的节点和副本](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli)。 Web 服务的高度可用并返回成功的响应至关重要。 如果作业从 Web 服务收到不可用响应 （503），则它将不断重试，并关闭指数。 成功 （200） 和服务不可用 （503） 以外的任何响应都将导致作业进入失败状态。

## <a name="next-steps"></a>后续步骤

* [教程：Azure 流分析 JavaScript 用户定义的函数](stream-analytics-javascript-user-defined-functions.md)
* [使用 Azure 机器学习工作室（经典）功能扩展流分析作业](stream-analytics-scale-with-machine-learning-functions.md)

