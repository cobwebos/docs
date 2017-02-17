---
title: "使用脚本操作在 Hadoop 群集上安装 Spark | Microsoft Docs"
description: "了解如何使用 Spark 通过脚本操作自定义 HDInsight 群集。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 7ebf4e2b-0742-4a2f-b429-60dc30d3f905
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: c9e3c1d2a1f5b83c59fa2a22f3cb4d89df203384
ms.openlocfilehash: 4fa808b50b56122976cc176c55937f9443f31097


---
# <a name="install-and-use-spark-on-windows-based-hdinsight-clusters-using-script-action"></a>使用脚本操作在基于 Windows 的 HDInsight 群集上安装并使用 Spark

> [!IMPORTANT]
> 这篇文章现已弃用。 HDInsight 现在为基于 Linux 的群集提供属于一流群集类型的 Spark，这意味着现在可以直接创建 Spark 群集，而不必使用脚本操作修改 Hadoop 群集。 此外，本文档中的步骤仅适用于基于 Windows 的 HDInsight 群集。 Windows 上仅可使用低于 HDInsight 3.4 版本的 HDInsight。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight Deprecation on Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)（HDInsight 在 Windows 上即将弃用）。

了解如何使用脚本操作在基于 Windows 的 HDInsight 群集上安装 Spark，以及如何在 HDInsight 群集上运行 Spark 查询。

**相关文章**

* [在 HDInsight 中创建 Hadoop 群集](hdinsight-provision-clusters.md)：有关如何创建 HDInsight 群集的一般信息。
* [开始在 HDInsight 上使用 Apache Spark](hdinsight-apache-spark-jupyter-spark-sql.md)：创建 HDInsight Spark 群集。
* [使用脚本操作自定义 HDInsight 群集][hdinsight-cluster-customize]：有关使用脚本操作自定义 HDInsight 群集的一般信息。
* [为 HDInsight 开发脚本操作脚本](hdinsight-hadoop-script-actions.md)。

## <a name="what-is-spark"></a>什么是 Spark？
<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> 是一种开放源代码并行处理框架，支持内存中处理，以提升大数据分析应用程序的性能。 Spark 的内存中计算功能使其成为机器学习和图形计算中的迭代算法的最佳选择。

Spark 还可用于执行基于磁盘的传统数据处理。 Spark 改进了传统的 MapReduce 框架，因为在中间阶段不必写入到磁盘。 另外，Spark 与 Hadoop 分布式文件系统 (HDFS) 和 Azure Blob 存储兼容，以便现有的数据可通过 Spark 进行轻松处理。

本主题提供有关如何自定义 HDInsight 群集以安装 Spark 的说明。

## <a name="install-spark-using-the-azure-portal"></a>使用 Azure 门户安装 Spark
用于在 HDInsight 群集上安装 Spark 的示例脚本可通过 [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1) 上的只读 Azure 存储 blob 获得。 此脚本可安装 Spark 1.2.0 或 Spark 1.0.2，具体视创建的 HDInsight 群集的版本而定。

* 如果在创建 **HDInsight 3.2** 群集时使用此脚本，则该脚本将安装 **Spark 1.2.0**。
* 如果在创建 **HDInsight 3.1** 群集时使用此脚本，则该脚本将安装 **Spark 1.0.2**。

你可以修改此脚本，或创建自己的脚本，以安装其他版本的 Spark。

> [!NOTE]
> 该示例脚本仅适用于 HDInsight 3.1 和 3.2 群集。 有关 HDInsight 群集版本的详细信息，请参阅 [HDInsight 群集版本](hdinsight-component-versioning.md)。

1. 按照[在 HDInsight 中创建 Hadoop 群集](hdinsight-provision-clusters.md)中的说明，使用“自定义创建”选项开始创建群集。 选取群集版本，具体视以下内容而定：

   * 如果要安装 **Spark 1.2.0**，请创建 HDInsight 3.2 群集。
   * 如果要安装 **Spark 1.0.2**，请创建 HDInsight 3.1 群集。
2. 在向导的“脚本操作”页上，单击“添加脚本操作”，以提供有关脚本操作的详细信息，如下所示：

    ![使用脚本操作自定义群集](./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")

    <table border='1'>
        <tr><th>属性</th><th>值</th></tr>
        <tr><td>名称</td>
            <td>指定脚本操作的名称。 例如，<b>Install Spark</b>。</td></tr>
        <tr><td>脚本 URI</td>
            <td>指定调用以自定义群集的脚本的统一资源标识符 (URI)。 例如，<i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
        <tr><td>节点类型</td>
            <td>指定在其上运行自定义脚本的节点。 可以选择“所有节点”<b></b>、“仅限头节点”<b></b>或“仅限辅助角色节点”<b></b>。
        <tr><td>parameters</td>
            <td>根据脚本的需要，指定参数。 用于安装 Spark 的脚本不需要任何参数，因此，可以将此项保留为空。</td></tr>
    </table>

    你可以添加多个脚本操作，在群集上安装多个组件。 在添加了脚本后，单击复选标记以开始创建群集。

还可以通过 Azure PowerShell 或 HDInsight .NET SDK 使用脚本在 HDInsight 上安装 Spark。 有关这些过程的说明在本主题后面提供。

## <a name="use-spark-in-hdinsight"></a>在 HDInsight 中使用 Spark
Spark 提供用 Scala、Python 和 Java 编写的 API。 还可以使用交互式 Spark shell 来运行 Spark 查询。 本部分提供有关如何通过不同方法来使用 Spark 的说明：

* [使用 Spark shell 来运行交互式查询](#sparkshell)
* [使用 Spark shell 来运行 Spark SQL 查询](#sparksql)
* [使用独立的 Scala 程序](#standalone)

### <a name="a-namesparkshellause-the-spark-shell-to-run-interactive-queries"></a><a name="sparkshell"></a>使用 Spark shell 来运行交互式查询
执行以下步骤以从交互式 Spark shell 运行 Spark 查询。 在本部分中，我们将对默认情况下在 HDInsight 群集上可用的示例数据文件 (/example/data/gutenberg/davinci.txt) 运行 Spark 查询。

1. 在 Azure 门户中，对你创建的安装有 Spark 的群集启用远程桌面，然后远程连接到该群集。 有关说明，请参阅[使用 RDP 连接到 HDInsight 群集](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)。
2. 在远程桌面协议 (RDP) 会话中，从桌面打开 Hadoop 命令行（通过桌面快捷方式），然后导航到 Spark 的安装位置；例如，**C:\apps\dist\spark-1.2.0**。
3. 运行以下命令以启动 Spark shell：

         .\bin\spark-shell --master yarn

    在命令完成运行后，应该获取 Scala 提示符：

         scala>
4. 在 Scala 提示符中，输入如下所示的 Spark 查询。 此查询会计算每个单词在 davinci.txt 文件中出现的次数，该文件位于与群集关联的 Azure Blob 存储上的 /example/data/gutenberg/ 位置处。

        val file = sc.textFile("/example/data/gutenberg/davinci.txt")
        val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
        counts.toArray().foreach(println)
5. 输出应如下所示：

    ![在 HDInsight 群集中运行 Scala 交互式 shell 的输出](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)
6. 输入 :q，退出 Scala 提示符。

        :q

### <a name="a-namesparksqlause-the-spark-shell-to-run-spark-sql-queries"></a><a name="sparksql"></a>使用 Spark shell 来运行 Spark SQL 查询
Spark SQL 允许使用 Spark 运行以结构化查询语言 (SQL)、HiveQL 或 Scala 表达的关系查询。 本部分介绍了如何使用 Spark 对示例 Hive 表运行 Hive 查询。 在创建群集时，本部分中使用的 Hive 表（称为 **hivesampletable**）默认为可用。

> [!NOTE]
> 以下示例根据 **Spark 1.2.0** 创建，如果在创建 HDInsight 3.2 群集时运行脚本操作，则将安装该程序。
>
>

1. 在 Azure 门户中，对你创建的安装有 Spark 的群集启用远程桌面，然后远程连接到该群集。 有关说明，请参阅[使用 RDP 连接到 HDInsight 群集](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)。
2. 在 RDP 会话中，从桌面打开 Hadoop 命令行（通过桌面快捷方式），并导航到 Spark 的安装位置；例如，**C:\apps\dist\spark-1.2.0**。
3. 运行以下命令以启动 Spark shell：

         .\bin\spark-shell --master yarn

    在命令完成运行后，应该获取 Scala 提示符：

         scala>
4. 在出现 Scala 提示符时，设置 Hive 上下文。 这是通过 Spark 使用 Hive 查询所必需的。

        val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

    请注意，**sc** 是启动 Spark shell 时设置的默认 Spark 上下文。
5. 通过使用 Hive 上下文运行 Hive 查询，并将输出打印到控制台。 查询将检索特定 make 的设备上的数据，并将检索到的记录数限制为 20。

        hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)
6. 应该会显示如下输出：

    ![在 HDInsight 群集中运行 Spark SQL 的输出](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)
7. 输入 :q，退出 Scala 提示符。

        :q

### <a name="a-namestandaloneause-a-standalone-scala-program"></a><a name="standalone"></a>使用独立的 Scala 程序
在本部分中，我们将编写 Scala 应用程序，用于计算在示例数据文件 (/example/data/gutenberg/davinci.txt) 中包含字母“a”和“b”的行数。默认情况下，该文件在 HDInsight 群集上提供。 若要在使用 Spark 安装自定义的群集上编写并使用独立的 Scala 程序，必须执行以下步骤：

* 编写 Scala 程序
* 构建 Scala 程序以获取 .jar 文件
* 在群集上运行作业

#### <a name="write-a-scala-program"></a>编写 Scala 程序
在本部分中，将编写 Scala 程序，用于计算在示例数据文件中包含字母“a”和“b”的行数。

1. 打开文本编辑器，并粘贴以下代码：

        /* SimpleApp.scala */
        import org.apache.spark.SparkContext
        import org.apache.spark.SparkContext._
        import org.apache.spark.SparkConf

        object SimpleApp {
          def main(args: Array[String]) {
            val logFile = "/example/data/gutenberg/davinci.txt"            //Location of the sample data file on Azure Blob storage
            val conf = new SparkConf().setAppName("SimpleApplication")
            val sc = new SparkContext(conf)
            val logData = sc.textFile(logFile, 2).cache()
            val numAs = logData.filter(line => line.contains("a")).count()
            val numBs = logData.filter(line => line.contains("b")).count()
            println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
          }
        }

1. 使用名称 **SimpleApp.scala** 保存文件。

#### <a name="build-the-scala-program"></a>构建 Scala 程序
在本部分中，请使用<a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">简单的构建工具</a>（或 sbt）构建 Scala 程序。 sbt 需要 Java 1.6 或更高版本，因此，要确保安装有正确版本的的 Java，然后才能继续完成本部分的内容。

1. 从 http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html 安装 sbt。
2. 创建名为 **SimpleScalaApp** 的文件夹，并在此文件夹中创建名为 **simple.sbt** 的文件。 这是包含有关 Scala 版本和库依赖项等内容的信息的配置文件。将以下内容粘贴到 simple.sbt 文件中并保存它：

        name := "SimpleApp"

        version := "1.0"

        scalaVersion := "2.10.4"

        libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"

    > [!NOTE]
    > 确保在该文件中保留有空行。

1. 在 **SimpleScalaApp** 文件夹下，创建目录结构 **\src\main\scala**，并粘贴以前在 \src\main\scala 文件夹下创建的 Scala 程序 (**SimpleApp.scala**)。
2. 打开命令提示符，导航到 SimpleScalaApp 目录，然后输入以下命令：

        sbt package

    在编译了应用程序后，将在 **\target\scala-2.10** 目录下的根 SimpleScalaApp 文件夹中看到 **simpleapp_2.10-1.0.jar** 文件。

#### <a name="run-the-job-on-the-cluster"></a>在群集上运行作业
在本部分中，将远程连接到安装有 Spark 的群集，然后复制 SimpleScalaApp 项目的目标文件夹。 然后，将使用 **spark-submit** 命令在群集上提交作业。

1. 远程连接到安装有 Spark 的群集。 从编写和构建 SimpleApp.scala 程序的计算机中，复制 **SimpleScalaApp\target** 文件夹，并将其粘贴到群集上的某个位置。
2. 在 RDP 会话中，从桌面打开 Hadoop 命令行，然后导航到**目标**文件夹粘贴到的位置。
3. 输入以下命令以运行 SimpleApp.scala 程序：

        C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

1. 在程序完成运行后，输出将显示在控制台上。

        Lines with a: 21374, Lines with b: 11430

## <a name="install-spark-using-azure-powershell"></a>使用 Azure PowerShell 安装 Spark
在本部分中，使用 **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** cmdlet 通过脚本操作来调用脚本，以自定义群集。 在继续前，确保已安装并配置 Azure PowerShell。 有关配置工作站以运行用于 HDInsight 的 Azure PowerShell cmdlet 的信息，请参阅[安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

执行以下步骤：

1. 打开 Azure PowerShell 窗口，并声明以下变量：

    ```powershell
    # Provide values for these variables
    $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription
    $clusterName = "<HDInsightClusterName>"            # HDInsight cluster name
    $storageAccountName = "<StorageAccountName>"    # Azure Storage account that hosts the default container
    $storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
    $containerName = $clusterName
    $location = "<MicrosoftDataCenter>"                # Location of the HDInsight cluster. It must be in the same data center as the Storage account.
    $clusterNodes = <ClusterSizeInNumbers>            # Number of nodes in the HDInsight cluster
    $version = "<HDInsightClusterVersion>"          # For example, "3.2"
    ```

2. 指定配置值，例如群集中的节点，以及要使用的默认存储。

    ```powershell
    # Specify the configuration options
    Select-AzureSubscription $subscriptionName
    $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
    $config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
    $config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
    $config.DefaultStorageAccount.StorageContainerName=$containerName
    ```

3. 使用 **Add-AzureHDInsightScriptAction** cmdlet 将脚本操作添加到群集配置。 稍后，在创建群集时，将执行脚本操作。

    ```powershell
    # Add a script action to the cluster configuration
    $config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1
    ```

    **Add-AzureHDInsightScriptAction** cmdlet 采用以下参数：

    <table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
    <tr>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">参数</th>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">定义</th></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">配置</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">脚本操作信息添加到的配置对象。</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名称</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">脚本操作的名称。</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指定在其上运行自定义脚本的节点。 有效值是 HeadNode（在头节点上安装）或 DataNode（在所有数据节点上安装）。 你可以使用任一值或两个值。</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指定执行的脚本的 URI。</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">参数</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">脚本所需的参数。 本主题中使用的示例脚本不需要任何参数，因此，在上述代码段中看不到此参数。
    </td></tr>
    </table>
4. 最后，开始创建安装有 Spark 的自定义群集。

    ```powershell
    # Start creating a cluster with Spark installed
    New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version
    ```

出现提示时，请输入群集的凭据。 创建群集可能需要几分钟时间。

## <a name="install-spark-using-powershell"></a>使用 PowerShell 安装 Spark
请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell)。

## <a name="install-spark-using-net-sdk"></a>使用 .NET SDK 安装 Spark
请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell)。

## <a name="see-also"></a>另请参阅
* [在 HDInsight 中创建 Hadoop 群集](hdinsight-provision-clusters.md)：创建 HDInsight 群集。
* [开始在 HDInsight 上使用 Apache Spark](hdinsight-apache-spark-jupyter-spark-sql.md)：开始在 HDInsight 上使用 Spark。
* [使用脚本操作自定义 HDInsight 群集][hdinsight-cluster-customize]：使用脚本操作自定义 HDInsight 群集。
* [为 HDInsight 开发脚本操作脚本](hdinsight-hadoop-script-actions.md)：开发脚本操作脚本。
* [在 HDInsight 群集上安装 R][hdinsight-install-r]：提供有关如何使用群集自定义在 HDInsight Hadoop 群集上安装和使用 R 的说明。 R 是一种用于统计计算的开放源代码语言和环境。 它提供了数百个内置统计函数及其自己的编程语言，可结合各方面的函数编程和面向对象的编程。 它还提供了各种图形功能。
* [在 HDInsight 群集上安装 Giraph](hdinsight-hadoop-giraph-install.md)。 使用群集自定义在 HDInsight Hadoop 群集上安装 Giraph。 Giraph 可让你通过使用 Hadoop 执行图形处理，并可以在 Azure HDInsight 上使用。
* [在 HDInsight 群集上安装 Solr](hdinsight-hadoop-solr-install.md)。 使用群集自定义在 HDInsight Hadoop 群集上安装 Solr。 Solr 允许对存储的数据执行功能强大的搜索操作。

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs



<!--HONumber=Jan17_HO3-->


