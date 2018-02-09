---
title: "创建支持“恰好一次”事件处理的 Spark 流作业 - Azure HDInsight | Microsoft Docs"
description: "如何将 Spark 流设置为处理事件一次且只有一次。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ramoha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: ramoha
ms.openlocfilehash: ebab9ebc92ae1dff8902d618d0a474ce2b2a0af3
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="create-spark-streaming-jobs-with-exactly-once-event-processing"></a>创建支持“恰好一次”事件处理的 Spark 流作业

系统中发生某种故障后，流处理应用程序会采取不同的方法来应对消息的重新处理：

* 至少一次：保证处理每条消息，但消息可能会处理多次。
* 最多一次：不一定会处理每条消息。 如果处理某条消息，只会将它处理一次。
* 恰好一次：保证处理每条消息一次且只有一次。

本文介绍如何配置 Spark 流，以实现“恰好一次”处理。

## <a name="exactly-once-semantics-with-spark-streaming"></a>Spark 流的“恰好一次”语义

首先，考虑在出现问题后所有系统故障点如何重启，以及如何避免数据丢失。 Spark 流应用程序包含：

* 一个输入源
* 从输入源提取数据的一个或多个接收器进程
* 处理数据的任务
* 一个输出接收器
* 一个管理长时间运行的作业的驱动程序进程

“恰好一次”语义要求数据在任何时间点都不会丢失，并且不管故障发生在哪个位置，消息处理都可重启。

### <a name="replayable-sources"></a>可重播源

Spark 流应用程序从中读取事件的源必须可重播。 这意味着，如果已检测到消息，但在持久保存或处理该消息之前系统发生故障，则源必须再次提供同一消息。

在 Azure 中，Azure 事件中心和 HDInsight 上的 Kafka 提供可重播源。 可重播源的另一个示例是 HDFS 等容错的文件系统、Azure 存储 Blob 或 Azure Data Lake Store，其中的所有数据将永久保留，随时都可以重新读取整个数据。

### <a name="reliable-receivers"></a>可靠的接收器

在 Spark 流中，事件中心和 Kafka 等源具有可靠的接收器，其中的每个接收器可跟踪它在读取源时的进度。 可靠的接收器将其状态持久保存在以下容错存储中：ZooKeeper，或者写入 HDFS 的 Spark 流检查点。 如果此类接收器发生故障并随后重启，它可以从上次中断的位置继续拾取数据。

### <a name="use-the-write-ahead-log"></a>使用预写日志

Spark 流支持使用预写日志，其中每个收到的事件首先写入容错存储中的 Spark 检查点目录，然后存储在弹性分布式数据集 (RDD) 中。 在 Azure 中，容错存储是以 Azure 存储或 Azure Data Lake Store 为基础的 HDFS。 在 Spark 流应用程序中，可以通过将 `spark.streaming.receiver.writeAheadLog.enable` 配置设置指定为 `true`，为所有接收器启用预写日志。 预写日志针对驱动程序和执行器的故障提供容错。

对于针对事件数据运行任务的辅助角色，将会根据定义在多个辅助角色之间复制和分配每个 RDD。 如果由于运行任务的辅助角色崩溃而导致该任务失败，则会在拥有事件数据副本的另一个辅助角色上重启该任务，因此事件不会丢失。

### <a name="use-checkpoints-for-drivers"></a>使用驱动程序的检查点

作业驱动程序需要可重启。 如果运行 Spark 流应用程序的驱动程序崩溃，该驱动程序及其运行的所有接收器、任务和存储事件数据的任何 RDD 都会关闭。 在这种情况下，需要能够保存作业的进度，以便稍后可以恢复。 为此，可以在容错存储中定期创建 DStream 的有向无环图 (DAG) 检查点。 DAG 元数据包括用于创建流应用程序的配置、肜于定义应用程序的操作，以及已排队但尚未完成的任何批。 此元数据可让发生故障的驱动程序根据检查点信息重启。 驱动程序重启时，会启动新的接收器，这些接收器可自行通过预写日志将事件数据恢复到 RDD。

可以执行两个步骤在 Spark 流中启用检查点。 

1. 在 StreamingContext 对象中配置检查点的存储路径：

    val ssc = new StreamingContext(spark, Seconds(1))  ssc.checkpoint("/path/to/checkpoints")

    在 HDInsight 中，应将这些检查点保存到群集上附加的默认存储：Azure 存储或 Azure Data Lake Store。

2. 接下来，在 DStream 上指定检查点间隔（以秒为单位）。 在每个间隔内，会将派生自输入事件的状态数据持久保存到存储。 在从源事件重新生成状态时，持久保存的状态数据可以减少所需的计算。

    val lines = ssc.socketTextStream("hostname", 9999)  lines.checkpoint(30)  ssc.start()  ssc.awaitTermination()

### <a name="use-idempotent-sinks"></a>使用幂等接收器

作业将结果写入到的目标接收器必须能够处理多次提供相同结果的情况。 该接收器必须能够检测此类重复结果并将其忽略。 可以使用相同的数据多次调用幂等接收器，而不会更改状态。

若要创建幂等接收器，可以实现相应的逻辑来首先检查数据存储中是否存在传入的结果。 如果已存在该结果，则从 Spark 作业的角度看，写入操作看上去已成功，但实际上数据存储忽略了重复的数据。 如果结果不存在，则接收器应将此新结果插入到其存储中。 

例如，可以在 Azure SQL 数据库中使用存储过程，将事件插入表中。 此存储过程先根据键字段查找事件，仅当未找到匹配的事件时，才将记录插入到表中。

另一个示例是使用分区的文件系统，例如 Azure 存储 Blob 或 Azure Data Lake Store。 在这种情况下，接收器逻辑不需要检查文件是否存在。 如果表示事件的文件存在，只需使用相同的数据将其覆盖。 否则，需要在计算的路径中创建一个新文件。

## <a name="next-steps"></a>后续步骤

* [Spark 流概述](apache-spark-streaming-overview.md)
* [在 YARN 中创建高可用性 Spark 流作业](apache-spark-streaming-high-availability.md)
