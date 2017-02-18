---
title: "通过堆转储调试和分析 Hadoop 服务 | Microsoft Docs"
description: "自动收集 Hadoop 服务的堆转储并将其放置在用于调试和分析的 Azure Blob 存储帐户内。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e4ec4ebb-fd32-4668-8382-f956581485c4
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 6407c371bc51461a05429fabaf38d3f9bc80d32c
ms.openlocfilehash: b6830f422b914722b8381a69c73cf8b02e107115


---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>将堆转储收集在 Blob 存储中，以便调试和分析 Hadoop 服务
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

堆转储包含应用程序的内存快照，其中包括创建转储时各变量的值。 因此，它们在诊断发生在运行时的问题时很有用。 可以自动收集 Hadoop 服务的堆转储，并将其放置在用户 Azure Blob 存储帐户中的 HDInsightHeapDumps/ 下。 

必须为各个群集上的各个服务启用堆转储收集。 默认情况下，为群集关闭了此功能。 这些堆转储可能很大，因此，在启用收集后，建议你监视保存这些转储的 Blob 存储帐户。

> [!IMPORTANT]
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight Deprecation on Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)（HDInsight 在 Windows 上即将弃用）。 本文中的信息仅适用于基于 Windows 的 HDInsight。 有关基于 Linux 的 HDInsight 的信息，请参阅[为基于 Linux 的 HDInsight 上的 Hadoop 服务启用堆转储](hdinsight-hadoop-collect-debug-heap-dump-linux.md)


## <a name="eligible-services-for-heap-dumps"></a>符合堆转储条件的服务
你可以启用以下服务的堆转储：

* **hcatalog** - tempelton
* **hive** - hiveserver2、metastore、derbyserver
* **mapreduce** - jobhistoryserver
* **yarn** - resourcemanager、nodemanager、timelineserver
* **hdfs** - datanode、secondarynamenode、namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>用于启用堆转储的配置元素
若要为服务启用堆转储，需要在该服务的节（由 **service_name** 指定）中设置相应的配置元素。

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

**service_name** 的值可以是上面列出的任何服务：tempelton、hiveserver2、metastore、derbyserver、jobhistoryserver、resourcemanager、nodemanager、timelineserver、datanode、secondarynamenode 或 namenode。

## <a name="enable-using-azure-powershell"></a>使用 Azure PowerShell 启用
例如，若要使用 Azure PowerShell 为 jobhistoryserver 启用堆转储，请执行以下操作：

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>使用 .NET SDK 启用
例如，若要使用 Azure HDInsight .NET SDK 为 jobhistoryserver 启用堆转储，请执行以下操作：

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));



<!--HONumber=Feb17_HO1-->


