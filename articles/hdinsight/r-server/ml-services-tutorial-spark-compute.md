---
title: 教程：在 Azure HDInsight 的 Spark 计算上下文中使用 R
description: 教程 - 机器学习服务中的 R 和 Spark 入门。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 244c62467f187417bbb9f0e54173aad5a7d26d0a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451738"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>教程：在 Azure HDInsight 的 Spark 计算上下文中使用 R

本教程逐步介绍如何在 Azure HDInsight 中的机器学习服务群集上运行的 Apache Spark 中使用 R 函数。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将示例数据下载到本地存储
> * 将数据复制到默认存储
> * 设置数据集
> * 创建数据源
> * 创建 Spark 的计算上下文
> * 拟合线性模型
> * 使用复合 XDF 文件
> * 将 XDF 转换为 CSV

## <a name="prerequisites"></a>先决条件

* HDInsight 上的机器学习服务群集。 参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)，并选择“机器学习服务”作为“群集类型”。  

## <a name="connect-to-rstudio-server"></a>连接到 RStudio Server

RStudio Server 在群集的边缘节点上运行。 转到以下 URL，其中 `CLUSTERNAME` 是创建的机器学习服务群集的名称：

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

首次登录时需要进行两次身份验证。 对于第一个身份验证提示，请提供群集管理员登录名和密码，默认为 `admin`。 对于第二个身份验证提示，请提供 SSH 登录名和密码，默认为 `sshuser`。 后续登录只需提供 SSH 凭据。

## <a name="download-sample-data"></a>下载示例数据

“2012 年航班准时性数据集”由 12 个逗号分隔的文件组成，这些文件包含有关 2012 年美国所有商务航班的抵达和出发详细信息。  这是一个大数据集，其中包含 600 万条以上的观测结果。

1. 初始化几个环境变量。 在 RStudio Server 控制台中输入以下代码：

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

    变量将显示在屏幕右侧的“环境”选项卡下。 

    ![RStudio](./media/ml-services-tutorial-spark-compute/rstudio.png)

2.  创建本地目录并下载示例数据。 在 RStudio 中输入以下代码：

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    下载应在大约 9 分半钟内完成。

## <a name="copy-data-to-default-storage"></a>将数据复制到默认存储

使用 `airDataDir` 变量指定 HDFS 位置。 在 RStudio 中输入以下代码：

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

该步骤在大约 10 秒内应会完成。

## <a name="set-up-data-set"></a>设置数据集

1. 创建使用默认值的文件系统对象。 在 RStudio 中输入以下代码：

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

2. 原始 CSV 文件包含的变量名称比较杂乱，因此我们提供了 `colInfo` 列表使其变得更加整齐有序。 在 RStudio 中输入以下代码：

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-source"></a>创建数据源

在 Spark 计算上下文中，可以使用以下函数创建数据源：

|函数 | 说明 |
|---------|-------------|
|`RxTextData` | 逗号分隔的文本数据源。 |
|`RxXdfData` | 采用 XDF 数据文件格式的数据。 在 RevoScaleR 中，XDF 文件格式已针对 Hadoop 进行修改，将在一组复合文件而不是单个文件中存储数据。 |
|`RxHiveData` | 生成 Hive 数据源对象。|
|`RxParquetData` | 生成 Parquet 数据源对象。|
|`RxOrcData` | 生成 Orc 数据源对象。|

使用已复制到 HDFS 的文件创建 [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) 对象。 在 RStudio 中输入以下代码：

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-compute-context-for-spark"></a>创建 Spark 的计算上下文

若要在工作器节点上加载数据和运行分析，请将脚本中的计算上下文设置为 [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark)。 在此上下文中，R 函数会自动在所有工作器节点之间分配工作负荷，不需要满足有关管理作业或队列的固有要求。 通过 `RxSpark` 或 `rxSparkConnect()` 建立 Spark 计算上下文，使用 `rxSparkDisconnect()` 返回到本地计算上下文。 在 RStudio 中输入以下代码：

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>拟合线性模型

1. 使用 [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) 函数通过 `airDS` 数据源拟合线性模型。 在 RStudio 中输入以下代码：

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    此步骤在 2 到 3 分钟内应会完成。

1. 查看结果。 在 RStudio 中输入以下代码：

    ```R
    summary(delayArr)
    ```

    应该看到以下结果：

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    请注意，结果指示我们已使用指定目录中的所有 .csv 文件处理了所有数据 - 600 万条观测结果。 另请注意，由于指定了 `cube = TRUE`，我们获得了每个星期日期的估计系数（而不是截距）。

## <a name="use-composite-xdf-files"></a>使用复合 XDF 文件

我们知道，可以直接在 Hadoop 上使用 R 分析 CSV 文件，但如果以更有效的格式存储数据，则可以更快地执行分析。 R .xdf 格式极其高效，但已针对 HDFS 进行一定程度的修改，目的是使各个文件保留在单个 HDFS 块中。 （HDFS 块大小根据安装的不同而异，但通常为 64 MB 或 128 MB。）在 Hadoop 上使用 [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) 时，请指定某个 `RxTextData` 数据源（例如 `AirDS`）作为 inData 参数，并指定某个 `RxXdfData` 数据源（其 fileSystem 设置为某个 HDFS 文件系统）作为 outFile 参数，来创建一组复合 .xdf 文件。 然后，`RxXdfData` 对象可用作后续 R 分析中的数据参数。

1. 定义 `RxXdfData` 对象。 在 RStudio 中输入以下代码：

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. 设置 250000 行的块大小，并指定我们要读取所有数据。 在 RStudio 中输入以下代码：

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. 使用 `rxImport` 导入数据。 在 RStudio 中输入以下代码：

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    此步骤在几分钟内应可完成。

1. 使用新的更快的数据源重新评估同一线性模型。 在 RStudio 中输入以下代码：

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    该步骤在一分钟内应可完成。

1. 查看结果。 结果应与 CSV 文件中的内容相同。 在 RStudio 中输入以下代码：

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>将 XDF 转换为 CSV

### <a name="in-a-spark-context"></a>在 Spark 上下文中

如果你已将 CSV 转换为 XDF 以便在运行分析时提高效率，但现在想要将数据转换回到 CSV，可以使用 [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) 来实现此目的。

若要创建 CSV 文件的文件夹，请先使用目录名称作为文件参数创建 `RxTextData` 对象；这表示要在其中创建 CSV 文件的文件夹。 运行 `rxDataStep` 时已创建此目录。 然后，在 `rxDataStep` 的 `outFile` 参数中指向此 `RxTextData` 对象。 创建的每个 CSV 根据目录名称命名，后接一个数字。

假设我们在执行逻辑回归和预测后要从 `airDataXdf` 复合 XDF 写出 HDFS 中的 CSV 文件夹，使新的 CSV 文件包含预测值和残差。 在 RStudio 中输入以下代码：

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

此步骤在大约两分半钟内应可完成。

可以看到，`rxDataStep` 为输入复合 XDF 文件中的每个 .xdfd 文件写出了一个 CSV。 这是在将计算上下文设置为 `RxSpark` 时，将复合 XDF 中的 CSV 写入 HDFS 的默认行为。

### <a name="in-a-local-context"></a>在本地上下文中

或者，在执行完分析后，可将计算上下文切换回到 `local`，并利用 `RxTextData` 中的以下两个参数，这样，在将 CSV 文件写出到 HDFS 时可以稍微提高控制度：`createFileSet` 和 `rowsPerOutFile`。 当 `createFileSet` 设置为 `TRUE` 时，CSV 文件的文件夹将写入到指定的目录。 当 `createFileSet` 设置为 `FALSE` 时，将写入单个 CSV 文件。 第二个参数 `rowsPerOutFile` 可设置为整数，指示当 `createFileSet` 为 `TRUE` 时，要将多少行写入每个 CSV 文件。

在 RStudio 中输入以下代码：

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

此步骤在大约 10 分钟内应可完成。

使用 `RxSpark` 计算上下文时，`createFileSet` 默认为 `TRUE`，`rowsPerOutFile` 不起作用。 因此，若要创建单个 CSV 或自定义每个文件的行数，必须在 `local` 计算上下文中执行 `rxDataStep`（数据仍可在 HDFS 中）。

## <a name="final-steps"></a>最后的步骤

1. 清理数据。 在 RStudio 中输入以下代码：

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. 停止远程 Spark 应用程序。 在 RStudio 中输入以下代码：

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. 退出 R 会话。 在 RStudio 中输入以下代码：

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>清理资源

完成本教程后，可以删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。

若要删除群集，请参阅[使用浏览器、PowerShell 或 Azure CLI 删除 HDInsight 群集](../hdinsight-delete-cluster.md)。

## <a name="next-steps"></a>后续步骤

本教程已介绍如何在 Azure HDInsight 中的机器学习服务群集上运行的 Apache Spark 中使用 R 函数。 有关详细信息，请参阅以下文章：

* [适用于 HDInsight 上的 ML Services 的计算上下文选项](r-server-compute-contexts.md)
* [适用于 Hadoop 上的 Spark 的 R 函数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
