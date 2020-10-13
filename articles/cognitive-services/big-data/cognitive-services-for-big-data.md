---
title: 适用于大数据的认知服务
description: 了解如何使用 Python、Java 和 Scala 将 Azure 认知服务用于大型数据集。 借助适用于大数据的认知服务，可以将不断改进的智能模型直接嵌入 Apache Spark&trade; 和 SQL 计算中。
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 07/09/2020
ms.author: marhamil
ms.openlocfilehash: 030fb4b4e5fa10304167f8d6bc2307c5b93c6d58
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776623"
---
# <a name="azure-cognitive-services-for-big-data"></a>适用于大数据的 Azure 认知服务

![适用于大数据的 Azure 认知服务](media/cognitive-services-big-data-overview.svg)

适用于大数据的 Azure 认知服务使用户使用 [Apache Spark&trade;](https://docs.microsoft.com/dotnet/spark/what-is-spark) 通过认知服务传输 TB 级数据。 使用适用于大数据的认知服务，可以轻松地使用借助任何数据存储创建大规模的智能应用程序。

借助适用于大数据的认知服务，可以将不断改进的智能模型直接嵌入 Apache Spark&trade; 和 SQL 计算中。 这些工具将开发人员从低级网络细节中解放出来，使他们可以专注于创建智能的分布式应用程序。

## <a name="features-and-benefits"></a>功能和优势

适用于大数据的认知服务可以使用世界任何区域中的服务，还可使用[容器化认知服务](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support)。 容器支持低或不具有超低延迟响应的连接部署。 容器化认知服务可以在本地运行，可以直接在 Spark 群集的工作器节点上运行，也可以在 Kubernetes 等外部业务流程协调程序上运行。

## <a name="supported-services"></a>支持的服务

[认知服务](https://docs.microsoft.com/azure/cognitive-services/)（可通过 API 和 SDK 访问）可帮助开发人员生成智能应用程序，而无需具备 AI 或数据科学技能。 借助认知服务，你可以让应用程序看、听、说、理解和推理。 若要使用认知服务，应用程序必须通过网络将数据发送给服务。 收到后，服务会进而发送智能响应。 以下服务可用于大数据工作负载：

### <a name="vision"></a>影像

|服务名称|服务说明|
|:-----------|:------------------|
|[计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "计算机视觉")| 使用计算机视觉服务，你可以访问用于处理图像并返回信息的高级算法。 |
|[人脸](https://docs.microsoft.com/azure/cognitive-services/face/ "人脸")| 使用人脸服务可访问高级人脸算法，从而实现人脸属性检测和识别。 |

### <a name="speech"></a>语音

|服务名称|服务说明|
|:-----------|:------------------|
|[语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "语音服务")|语音服务提供对语音识别、语音合成、语音翻译以及说话人验证和身份验证等功能的访问。|

### <a name="decision"></a>决策

|服务名称|服务说明|
|:-----------|:------------------|
|[异常检测器](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "异常检测器") | 使用异常检测器（预览版）服务可以监视并检测时序数据中的异常。|

### <a name="language"></a>语言

|服务名称|服务说明|
|:-----------|:------------------|
|[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "文本分析")| 文本分析服务提供对原始文本的自然语言处理，用于情绪分析、关键短语提取和语言检测。|

### <a name="search"></a>搜索

|服务名称|服务说明|
|:-----------|:------------------|
|[必应图像搜索](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "必应图像搜索")|必应图像搜索服务返回确定与用户查询相关的图像显示。|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>适用于大数据的认知服务的支持的编程语言

适用于大数据的认知服务是在 Apache Spark 上生成的。 Apache Spark 是一个分布式计算库，支持 Java、Scala、Python、R 和许多其他语言。 当前支持以下语言。

### <a name="python"></a>Python

我们在 [Microsoft ML for Apache Spark](https://aka.ms/spark) 的命名空间 `mmlspark.cognitive` 中提供 PySpark API。 有关详细信息，请参阅 [Python 开发人员 API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html)。 有关使用示例，请参阅 [Python 示例](samples-python.md)。

### <a name="scala-and-java"></a>Scala 和 Java

我们在 [Microsoft ML for Apache Spark](https://aka.ms/spark) 的命名空间 `com.microsoft.ml.spark.cognitive` 中提供基于 Scala 和 Java 的 Spark API。 有关详细信息，请参阅 [Scala 开发人员 API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package)。 有关使用示例，请参阅 [Scala 示例](samples-scala.md)。

## <a name="supported-platforms-and-connectors"></a>支持的平台和连接器

适用于大数据的认知服务需要 Apache Spark。 有几个 Apache Spark 平台支持适用于大数据的认知服务。

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) 是基于 Apache Spark 的分析平台，已针对 Microsoft Azure 云服务平台进行优化。 它提供了一键式安装、简化的工作流程以及支持数据科学家、数据工程师和业务分析师之间协作的交互式工作区。

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[Azure Synapse Analytics](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/synapse-analytics)（以前称为 SQL 数据仓库）是使用大规模并行处理的企业数据仓库。 借助 Synapse Analytics，可以跨 PB 级数据快速运行复杂查询。 Azure Synapse Analytics 提供托管的 Spark 池以通过直观的 Jupyter Notebook 接口运行 Spark 作业。

### <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/) 大规模编排 Docker 容器和分布式应用程序。 AKS 是托管式 Kubernetes 产品，可简化 Azure 中 Kubernetes 的使用。 Kubernetes 可以对认知服务的规模、延迟和网络进行精细控制。 但是，如果不熟悉 Apache Spark，建议使用 Azure Databricks 分析或 Synapse Analytics。

### <a name="data-connectors"></a>数据连接器

获得 Spark 群集后，下一步是连接到数据。 Apache Spark 有大量数据库连接器。 这些连接器使应用程序无论大型数据集存储在何处都可以使用这些数据集。 有关支持的数据库和连接器的详细信息，请参阅 [Azure Databricks 支持的数据源列表](https://docs.microsoft.com/azure/databricks/data/data-sources/)。

## <a name="concepts"></a>概念

### <a name="spark"></a>Spark

[Apache Spark&trade;](http://spark.apache.org/) 是用于大规模数据处理的统一分析引擎。 它的并行处理框架提高了大数据和分析应用程序的性能。 无需更改核心应用程序代码，Spark 可以同时作为批处理系统和流处理系统运行。

Spark 的基础是数据帧：数据帧是分布在 Apache Spark 工作器节点上的表格式数据集合。 Spark 数据帧就像关系数据库中的表或 R/Python 中的数据帧一样，但具有无限规模。 可以从多个源构造数据帧，例如：结构化数据文件、Hive 中的表或外部数据库。 将数据置于 Spark 数据帧后，可以：

   - 执行 SQL 样式的计算，如联接和筛选表。
   - 使用 MapReduce 样式并行将函数应用到大型数据集。
   - 使用适用于 Apache Spark 的 Microsoft 机器学习应用分布式机器学习。
   - 使用适用于大数据的认知服务，通过即用型智能服务丰富数据。

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>用于 Apache Spark 的 Microsoft 机器学习 (MMLSpark)

[用于 Apache Spark 的 Microsoft 机器学习](https://mmlspark.blob.core.windows.net/website/index.html#install) (MMLSpark) 是基于 Apache Spark 构建的开源分布式机器学习库 (ML)。 此软件包中包含适用于大数据的认知服务。 此外，MMLSpark 还包含几个其他用于 Apache Spark 的 ML 工具，例如 LightGBM、Vowpal Wabbit、OpenCV、LIME 等。 借助 MMLSpark，你可以从任何 Spark 数据源构建功能强大的预测模型和分析模型。

### <a name="http-on-spark"></a>Spark 上的 HTTP

适用于大数据的认知服务是我们如何将智能 Web 服务与大数据集成的示例。 Web 服务为全球许多应用程序提供支持，大多数服务通过超文本传输协议 (HTTP) 进行通信。 为了大规模使用任意 Web 服务，我们在 Spark 上提供 HTTP。 在 Spark 上使用 HTTP 时，可以通过任何 Web 服务传递 TB 级的数据。 实际上，我们使用这项技术来支持适用于大数据的认知服务。

## <a name="developer-samples"></a>开发人员示例

- [方案：预测性维护](recipes/anomaly-detection.md)
- [方案：智能艺术探索](recipes/art-explorer.md)

## <a name="blog-posts"></a>博客文章

- [详细了解认知服务如何在 Apache Spark 上运行&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [通过深入学习和计算机视觉在 Spark 上保存 Snow Leopards](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [ Microsoft Research 播客：MMLSpark，支持 AI for Good（由 Mark Hamilton 提供）](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)
- [学术白皮书：大规模智能微服务](https://arxiv.org/abs/2009.08044)

## <a name="webinars-and-videos"></a>网络研讨会和视频

- [Spark 上的 Azure 认知服务：具有嵌入式智能服务的群集](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Spark 峰会主题演讲：可缩放的 AI for Good](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Cosmos DB 中适用于大数据的认知服务](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)
- [关于大规模智能微服务的闪电式演讲](https://www.youtube.com/watch?v=BtuhmdIy9Fk&t=6s)

## <a name="next-steps"></a>后续步骤

- [适用于大数据的认知服务入门](getting-started.md)
- [简单的 Python 示例](samples-python.md)
- [简单的 Scala 示例](samples-scala.md)
