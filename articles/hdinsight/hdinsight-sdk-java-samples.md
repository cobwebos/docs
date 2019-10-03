---
title: Azure HDInsight：Java 示例
description: 针对用于 Java 的 HDInsight SDK，请在 GitHub 上查找有关使用它的常见任务的 Java 示例。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 04/15/2019
ms.openlocfilehash: 4a9bfe83c9fc1ccbc496ec97822305720dc52c10
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076838"
---
# <a name="azure-hdinsight-java-samples"></a>Azure HDInsight：Java 示例

> [!div class="op_single_selector"]
> * [Java 示例](hdinsight-sdk-java-samples.md)
> * [.NET 示例](hdinsight-sdk-dotnet-samples.md)
> * [Python 示例](hdinsight-sdk-python-samples.md)
<!-- * [Go Examples](hdinsight-sdk-dotnet-samples.md)-->

本文将提供：

* 有关群集创建任务的示例链接。
* 有关其他管理任务的参考内容链接。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [用于 Java 的 Azure HDInsight SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>群集管理 - 创建

* [创建 Kafka 群集](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateKafkaClusterSample.java)
* [创建 Spark 群集](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateSparkClusterSample.java)
* [创建包含 Azure Data Lake Storage Gen2 的 Spark 群集](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateHadoopClusterWithAdlsGen2Sample.java)
* [创建包含企业安全性套餐 (ESP) 的 Spark 群集](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateEspClusterSample.java)

通过克隆 [hdinsight-java-sdk-samples](https://github.com/Azure-Samples/hdinsight-java-sdk-samples) GitHub 存储库，可获取 Java 的这些示例。

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

在[用于 Java 的 HDInsight SDK 参考文档](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-preview)中，可找到这一附加 SDK 功能的代码片段。
