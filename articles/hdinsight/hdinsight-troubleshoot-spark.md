---
title: "使用 Azure HDInsight 对 Spark 进行故障排除 | Microsoft Docs"
description: "获取有关使用 Apache Spark 和 Azure HDInsight 的常见问题的解答。"
keywords: "Azure HDInsight, Spark, 常见问题解答, 故障排除指南, 常见问题, 应用程序配置, Ambari"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/31/2017
ms.author: arijitt
ms.openlocfilehash: 84b78fe4eb60162f3cad0ae4ae7f98864d5bbf2b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-spark-by-using-azure-hdinsight"></a>使用 Azure HDInsight 对 Spark 进行故障排除

了解处理 Apache Ambari 中的 Apache Spark 有效负载时的最常见问题及其解决方法。

## <a name="how-do-i-configure-a-spark-application-by-using-ambari-on-clusters"></a>如何在群集上使用 Ambari 配置 Spark 应用程序

### <a name="resolution-steps"></a>解决步骤

事先在 HDInsight 中设置此过程的配置值。 若要确定需要设置哪些 Spark 配置以及配置值是什么，请参阅 [Spark 应用程序为何失败并出现 OutofMemoryError 异常](#what-causes-a-spark-application-outofmemoryerror-exception)。 

1. 在群集列表中选择“Spark2”。

    ![从列表中选择群集](media/hdinsight-troubleshoot-spark/update-config-1.png)

2. 选择“配置”选项卡。

    ![选择“配置”选项卡](media/hdinsight-troubleshoot-spark/update-config-2.png)

3. 在配置列表中，选择“Custom-spark2-defaults”。

    ![选择 custom-spark-defaults](media/hdinsight-troubleshoot-spark/update-config-3.png)

4. 找到需要调整的值设置，例如 **spark.executor.memory**。 在本例中，值 **4608m** 太大。

    ![选择 spark.executor.memory 字段](media/hdinsight-troubleshoot-spark/update-config-4.png)

5. 将值设置为建议的设置。 建议为此设置使用值 **2048m**。

    ![将值更改为 2048m](media/hdinsight-troubleshoot-spark/update-config-5.png)

6. 保存值，并保存配置。 在工具栏上选择“保存”。

    ![保存设置和配置](media/hdinsight-troubleshoot-spark/update-config-6a.png)

    如果有任何配置需要引以注意，系统会发出通知。 记下这些项，并选择“仍然继续”。 

    ![选择“仍然继续”](media/hdinsight-troubleshoot-spark/update-config-6b.png)

    编写有关配置更改的注释，并选择“保存”。

    ![输入有关所做更改的注释](media/hdinsight-troubleshoot-spark/update-config-6c.png)

7. 每次保存配置时，系统都会提示重启服务。 选择“重启”。

    ![选择“重启”](media/hdinsight-troubleshoot-spark/update-config-7a.png)

    确认重启。

    ![选择“确认全部重启”](media/hdinsight-troubleshoot-spark/update-config-7b.png)

    可以查看正在运行的进程。

    ![查看正在运行的进程](media/hdinsight-troubleshoot-spark/update-config-7c.png)

8. 可以添加配置。 在配置列表中，依次选择“Custom-spark2-defaults”、“添加属性”。

    ![选择“添加属性”](media/hdinsight-troubleshoot-spark/update-config-8.png)

9. 定义新属性。 可以使用对话框为特定的设置（例如数据类型）定义单个属性。 或者，可以定义多个属性（每行定义一个属性）。 

    在本示例中，使用值 **4g** 定义了 **spark.driver.memory** 属性。

    ![定义新属性](media/hdinsight-troubleshoot-spark/update-config-9.png)

10. 根据步骤 6 和 7 中所述保存配置并重启服务。

这些更改会应用到整个群集，但可以在提交 Spark 作业时将其覆盖。

### <a name="additional-reading"></a>其他阅读材料

[在 HDInsight 群集上提交 Spark 作业](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>如何在群集上使用 Jupyter Notebook 配置 Spark 应用程序

### <a name="resolution-steps"></a>解决步骤

1. 若要确定需要设置哪些 Spark 配置以及配置值是什么，请参阅 [Spark 应用程序为何失败并出现 OutofMemoryError 异常](#what-causes-a-spark-application-outofmemoryerror-exception)。

2. 在 Jupyter Notebook 的第一个单元格中的 **%%configure** 指令后面，使用有效的 JSON 格式指定 Spark 配置。 根据需要更改实际值：

    ![添加配置](media/hdinsight-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>其他阅读材料

[在 HDInsight 群集上提交 Spark 作业](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-livy-on-clusters"></a>如何在群集上使用 Livy 配置 Spark 应用程序

### <a name="resolution-steps"></a>解决步骤

1. 若要确定需要设置哪些 Spark 配置以及配置值是什么，请参阅 [Spark 应用程序为何失败并出现 OutofMemoryError 异常](#what-causes-a-spark-application-outofmemoryerror-exception)。 

2. 使用 cURL 等 REST 客户端将 Spark 应用程序提交到 Livy。 使用如下所示的命令。 根据需要更改实际值：

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>其他阅读材料

[在 HDInsight 群集上提交 Spark 作业](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters"></a>如何在群集上使用 spark-submit 配置 Spark 应用程序

### <a name="resolution-steps"></a>解决步骤

1. 若要确定需要设置哪些 Spark 配置以及配置值是什么，请参阅 [Spark 应用程序为何失败并出现 OutofMemoryError 异常](#what-causes-a-spark-application-outofmemoryerror-exception)。

2. 使用如下所示的命令启动 spark-shell。 根据需要更改实际配置值： 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>其他阅读材料

[在 HDInsight 群集上提交 Spark 作业](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>哪些因素会导致 Spark 应用程序出现 OutofMemoryError 异常

### <a name="detailed-description"></a>详细说明

Spark 应用程序失败并出现以下类型的未捕获异常：

```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>可能的原因

此异常的最可能原因是未将足够的堆内存分配给 Java 虚拟机 (JVM)。 这些 JVM 作为 Spark 应用程序的执行程序或驱动程序启动。 

### <a name="resolution-steps"></a>解决步骤

1. 确定 Spark 应用程序要处理的数据大小上限。 可以根据输入数据的最大大小、转换输入数据时生成的中间数据，以及应用程序进一步转换中间数据时生成的输出数据来做出推测。 如果无法做出正式的初始推测，此过程也可能是迭代性的。 

2. 确保要使用的 HDInsight 群集具有足够的内存和核心资源，以便能够适应 Spark 应用程序。 若要确定资源是否足够，可以在 YARN UI 的“群集指标”部分中查看“已用内存与内存总计”以及“已用 VCore 与 VCore 总计”的值。

3. 将以下 Spark 配置设置为不超过可用内存和核心数 90% 的适当值。 这些值应仍在 Spark 应用程序的内存要求范围内。 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    计算所有执行程序使用的内存总量： 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   计算驱动程序使用的内存总量：
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>其他阅读材料

- [Spark 内存管理概述](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [在 HDInsight 群集上调试 Spark 应用程序](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)

