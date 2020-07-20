---
title: 适用于大数据的认知服务入门
description: 在 Azure Databricks 中使用认知服务设置 MMLSpark 管道，并运行示例。
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 7f4849c75b36b1663416ad1a97e3264fabcdaa67
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86201739"
---
# <a name="getting-started"></a>入门

为数据生成管道的第一步是设置环境。 环境准备就绪后，可以快速轻松地运行示例。

在本文中，我们将执行以下步骤帮助你入门：

1. [创建认知服务资源](#create-a-cognitive-services-resource)
1. [创建 Apache Spark 群集](#create-an-apache-spark-cluster)
1. [尝试示例](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>创建认知服务资源

要使用大数据认知服务，必须首先为工作流创建认知服务。 认知服务分为两种：托管在 Azure 中的云服务，以及由用户管理的容器化服务。 建议从较简单的基于云的认知服务开始。

### <a name="cloud-services"></a>云服务

基于云的认知服务是托管在 Azure 中的智能算法。 这些服务无需培训即可使用，只需要 Internet 连接。 可以[在 Azure 门户中创建认知服务](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows)，也可以[使用 Azure CLI 创建认知服务](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli?tabs=windows)。

### <a name="containerized-services-optional"></a>容器化服务（可选）

如果应用程序或工作负载使用非常大的数据集，需要专用网络，或者无法联系云，则与云服务通信几乎是不可能的。 在这种情况下，容器化认知服务具有以下优势：

* **低连接性**：可以在云中或云外的任何计算环境中部署容器化认知服务。 如果应用程序无法联系云，请考虑在应用程序中部署容器化认知服务。

* **低延迟**：因为容器化服务不需要与云之间的双向通信，所以会以较低的延迟来返回响应。

* **隐私和数据安全性**：可以将容器化服务部署到专用网络，以便敏感数据不离开网络。

* **高度可扩展性**：容器化服务没有“速率限制”，该服务在用户管理的计算机上运行。 因此，可以扩展认知服务而无需处理更大的工作负载。

按照[本指南](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support?tabs=luis)创建容器化认知服务。

## <a name="create-an-apache-spark-cluster"></a>创建 Apache Spark 群集

[Apache Spark&trade;](http://spark.apache.org/) 是为进行大数据数据处理而设计的分布式计算框架。 用户可以通过 Azure Databricks、Azure Synapse Analytics、HDInsight 和 Azure Kubernetes 服务等服务在 Azure 中使用 Apache Spark。 要使用大数据认知服务，必须首先创建群集。 如果已有 Spark 群集，请尝试一个示例。

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks 是一种基于 Apache Spark 的分析平台，其中包含一个一键式设置、简化的工作流和一个交互式工作区。 它通常用于在数据科学家、工程师和业务分析师之间进行协作。 要在 Azure Databricks 上使用大数据认知服务，请执行以下步骤：

1. [创建 Azure Databricks 工作区](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [在 Databricks 中创建 Spark 群集](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. 安装大数据认知服务
    * 在 Databricks 工作区中创建新库  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * 输入以下 maven 坐标的坐标值：`com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc1` 存储库：`https://mmlspark.azureedge.net/maven`  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * 将库安装到群集上  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="synapse-analytics-optional"></a>Synapse Analytics（可选）

（可选）你可以使用 Synapse Analytics 来创建 spark 群集。 Azure Synapse Analytics 将企业数据仓库和大数据分析结合在一起。 借助它可以使用无服务器的按需资源或预配资源，任意执行自己定义的大规模数据查询。 要开始使用 Synapse Analytics，请执行以下步骤：

1. [创建 Synapse 工作区（预览版）](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace)。
1. [使用 Azure 门户新建 Apache Spark 池（预览版）](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-portal)。

在 Synapse Analytics 中，会默认安装用于认知服务的大数据。

### <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

如果你使用的是容器化认知服务，则将 Spark 与容器一起部署的一个常见选项是 Azure Kubernetes 服务。

要开始使用 Azure Kubernetes 服务，请执行以下步骤：

1. [使用 Azure 门户部署 Azure Kubernetes 服务 (AKS) 群集](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
1. [安装 Apache Spark 2.4.0 helm 图表](https://hub.helm.sh/charts/microsoft/spark)
1. [使用 Helm 安装认知服务容器](https://docs.microsoft.com/azure/cognitive-services/computer-vision/deploy-computer-vision-on-premises)

## <a name="try-a-sample"></a>尝试示例

设置 Spark 群集和环境后，可以运行简短的示例。 本部分演示如何在 Azure Databricks 中使用用于认知服务的大数据。

首先，在 Azure Databricks 中创建笔记本。 对于其他 Spark 群集提供程序，请使用其笔记本或 Spark Submit。

1. 通过从“Azure Databricks”菜单中选择“新建笔记本”来创建新的 Databricks 笔记本 。

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. 在“创建笔记本”对话框中输入一个名称，选择“Python”作为语言，并选择前面创建的 Spark 群集。 

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    选择“创建”。

1. 将以下代码片段粘贴到新的笔记本中。

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. 从 Azure 门户中的文本分析仪表板上的“密钥和终结点”菜单中获取订阅密钥。
1. 将 Databricks 笔记本代码中的订阅密钥占位符替换为订阅密钥。
1. 选择笔记本单元格右上角的“播放”或“三角形”符号以运行该示例。 （可选）选择笔记本顶部的“运行所有”以运行所有单元格。 答案将显示在表中的单元格下方。

### <a name="expected-results"></a>预期结果

| text                                      |   情绪 |
|:------------------------------------------|------------:|
| 今天天气晴朗，我真高兴！           |   0.978959  |
| 交通高峰期让我很郁闷 |   0.0237956 |
| Spark 的认知服务不差  |   0.888896  |

## <a name="next-steps"></a>后续步骤

- [简短的 Python 示例](samples-python.md)
- [简短的 Scala 示例](samples-scala.md)
- [方案：预测性维护](recipes/anomaly-detection.md)
- [方案：智能艺术探索](recipes/art-explorer.md)
