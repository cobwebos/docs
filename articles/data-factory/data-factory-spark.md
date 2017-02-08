---
title: "从 Azure 数据工厂调用 Spark 程序"
description: "了解如何使用 MapReduce 活动从 Azure 数据工厂调用 Spark 程序。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: b3895dbf56c58e8e10e6af84afc520e5aef6e01e
ms.openlocfilehash: c2eb07b83f3096f86772f9af63a48da79ce0fd2e


---
# <a name="invoke-spark-programs-from-data-factory"></a>从数据工厂调用 Spark 程序
## <a name="introduction"></a>介绍
可以使用数据工厂管道中的 MapReduce 活动在 HDInsight Spark 群集上运行 Spark 程序。 阅读本文之前，请先参阅 [MapReduce 活动](data-factory-map-reduce.md)一文以了解使用活动的详细信息。 

## <a name="spark-sample-on-github"></a>GitHub 上的 Spark 示例
[Spark - GitHub 上的数据工厂示例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark)演示如何使用 MapReduce 活动调用 Spark 程序。 Spark 程序只是将数据从一个 Azure Blob 容器复制到其他的 Azure Blob容器。 

## <a name="data-factory-entities"></a>数据工厂实体
**Spark-ADF/src/ADFJsons** 文件夹包含数据工厂实体（链接服务、数据集、管道）的文件。  

此示例中有两个**链接服务**：Azure 存储和 Azure HDInsight。 指定 **StorageLinkedService.json** 中的 Azure 存储名称和密钥值以及 **HDInsightLinkedService.json** 中的 clusterUri、userName 和密码。

此示例中有两个**数据集**：**input.json** 和 **output.json**。 这些文件位于**数据集**文件夹中。  这些文件表示 MapReduce 活动的输入和输出数据集

在 **ADFJsons/Pipeline** 文件夹中查找示例管道。 查看管道以了解如何使用 MapReduce 活动调用 Spark 程序。 

MapReduce 活动配置为在 Azure 存储（在 StorageLinkedService.json 中指定）中的 **adflibs** 容器中调用 **com.adf.sparklauncher.jar**。 此程序的源代码位于 Spark-ADF/src/main/java/com/adf/ 文件夹中，它调用 spark-submit 并运行 Spark 作业。 

> [!IMPORTANT]
> 使用示例之前，请阅读 [README.md ](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.md) 获取最新信息和其他信息。 
> 
> 将此方法与自己的 HDInsight Spark 群集相结合，以使用 MapReduce 活动调用 Spark 程序。 不支持使用按需 HDInsight 群集。   
> 
> 

## <a name="see-also"></a>另请参阅
* [Hive 活动](data-factory-hive-activity.md)
* [Pig 活动](data-factory-pig-activity.md)
* [MapReduce 活动](data-factory-map-reduce.md)
* [Hadoop 流式处理活动](data-factory-hadoop-streaming-activity.md)
* [调用 R 脚本](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)




<!--HONumber=Nov16_HO3-->


