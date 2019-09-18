---
title: 快速入门：使用 R 控制台在 ML 服务上执行 R 脚本 - Azure HDInsight
description: 在本快速入门中，将使用 R 控制台在 Azure HDInsight 中的 ML Services 群集上执行 R 脚本。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 44bc90a4c556108b4b6874a2d1297a6467413824
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736365"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-r-console"></a>快速入门：使用 R 控制台在 Azure HDInsight 中的 ML Services 群集上执行 R 脚本

Azure HDInsight 上的 ML Services 允许 R 脚本使用 Apache Spark 和 Apache Hadoop MapReduce 运行分布式计算。 ML Services 可设置计算上下文，从而控制执行调用的方式。 群集的边缘节点为连接到群集和运行 R 脚本提供了便捷的位置。 使用边缘节点，可以选择跨边缘节点服务器的各个核心上运行 RevoScaleR 的并行化分布式函数。 还可以通过使用 RevoScaleR 的 Hadoop Map Reduce 或 Apache Spark 计算上下文在群集的各个节点上运行这些函数。

本快速入门介绍如何使用 R 控制台运行 R 脚本，以演示如何使用 Spark 进行分布式 R 计算。 你将定义计算上下文以在边缘节点上本地执行计算，然后重新分布在 HDInsight 群集中的节点上。

## <a name="prerequisites"></a>先决条件

* HDInsight 上的机器学习服务群集。 请参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)，并选择 **ML Services** 作为**群集类型**。

* SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。


## <a name="connect-to-r-console"></a>连接到 R 控制台

1. 使用 SSH 连接到 ML Services HDInsight 群集的边缘节点。 编辑以下命令，将 `CLUSTERNAME` 替换为群集的名称，然后输入该命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

1. 在 SSH 会话中，使用以下命令启动 R 控制台：

    ```
    R
    ```

    此时应会显示包含 ML Server 版本以及其他信息的输出。


## <a name="use-a-compute-context"></a>使用计算上下文

1. 可以通过 `>` 提示符输入 R 代码。 使用以下代码将示例数据加载到 HDInsight 的默认存储中：

    ```R
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    此步骤可能需要大约 10 分钟才能完成。

1. 创建一些数据信息并定义两个数据源。 在 R 控制台中输入以下代码：

    ```R
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. 使用**本地**计算上下文对数据运行逻辑回归。 在 R 控制台中输入以下代码：

    ```R
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    计算应该在大约 7 分钟内完成。 应会看到以类似于以下代码段的行结尾的输出：

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. 使用 **Spark** 上下文运行相同的逻辑回归。 Spark 上下文将处理进程分布到 HDInsight 群集的所有辅助角色节点上。 在 R 控制台中输入以下代码：

    ```R
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    计算应该在大约 5 分钟内完成。

1. 若要退出 R 控制台，请使用以下命令：

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，可以删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。

若要删除群集，请参阅[使用浏览器、PowerShell 或 Azure CLI 删除 HDInsight 群集](../hdinsight-delete-cluster.md)。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 R 控制台运行 R 脚本，以演示如何使用 Spark 进行分布式 R 计算。  继续阅读下一篇文章，了解可用于指定是否以及如何跨边缘节点或 HDInsight 群集的核心并行执行的选项。

> [!div class="nextstepaction"]
>[适用于 HDInsight 上的 ML Services 的计算上下文选项](./r-server-compute-contexts.md)