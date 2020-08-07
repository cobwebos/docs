---
title: 食谱：针对大数据的认知维护和认知服务
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何通过认知服务进行大数据的分布式异常检测
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: b29fa17c21b2cdb2e764c905af3819142d8c341e
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87844059"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>食谱：针对大数据的认知维护和认知服务

此食谱显示了如何在 Spark 上使用 Azure Synapse 分析和认知服务来实现 IoT 设备的预测性维护。 接下来，我们将介绍[CosmosDB 和 Synapse 链接](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples)示例。 为简单起见，在这种情况下，我们将从 CSV 文件直接读取数据，而不是通过 CosmosDB 和 Synapse 链接获取流数据。 强烈建议您查找 Synapse 链接示例。

## <a name="hypothetical-scenario"></a>假设方案

假想方案是一种电源植物，其中 IoT 设备监视[流涡轮机](https://en.wikipedia.org/wiki/Steam_turbine)。 IoTSignals 集合每分钟 (RPM) 和 Megawatts () MW 每个。 分析来自流涡轮机的信号，检测到异常信号。

数据中的离群值可能是随机的。 在这些情况下，RPM 值将会出现，而 MW 的输出将会关闭，以实现线路保护。 其思路是查看同时变化的数据，但具有不同的信号。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 使用[Spark 池](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool)配置的[Azure Synapse 工作区](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace)

## <a name="setup"></a>设置

### <a name="create-an-anomaly-detector-resource"></a>创建异常检测器资源

Azure 认知服务由你订阅的 Azure 资源表示。 使用[Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或[Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)为转换器创建资源。 你还可以：

- 查看[Azure 门户](https://portal.azure.com/)中的现有资源。

记下该资源的终结点和密钥，你将在本指南中需要它。

## <a name="enter-your-service-keys"></a>输入服务密钥

首先，让我们添加你的密钥和位置。

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>将数据读入数据帧

接下来，让我们将 IoTSignals 文件读取到数据帧中。 在 Synapse 工作区中打开一个新笔记本，并从该文件创建一个数据帧。

```python
df_device_info = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>在 Spark 上使用认知服务运行异常情况检测

目标是查找来自 IoT 设备的信号输出异常值的实例，以便我们可以看到发生错误的时间，并进行预测性维护。 为此，让我们在 Spark 上使用异常探测器：

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

让我们看一下数据：

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

此单元格应生成如下所示的结果：

| timestamp           |   值 | deviceId   | isAnomaly   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | 开发-7      | 错误       |
| 2020-05-01 18:33:52 |    2976 | 开发-7      | 错误       |
| 2020-05-01 18:33:53 |    2714 | 开发-7      | 错误       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>可视化某个设备的异常

IoTSignals.csv 包含来自多个 IoT 设备的信号。 我们将重点放在特定设备上，并直观显示设备的异常输出。

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

现在，我们已经创建了一个表示特定设备的异常的数据帧，可以可视化这些异常：

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

如果成功，您的输出将如下所示：

![异常探测器绘图](../media/anomaly-output.png)

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 认知服务、Azure Synapse Analytics 和 Azure CosmosDB 进行大规模预测性维护。 有关详细信息，请参阅[GitHub](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples/tree/master/IoT)上的完整示例。
