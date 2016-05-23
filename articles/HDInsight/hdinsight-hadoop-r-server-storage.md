<properties
   pageTitle="用于 HDInsight（预览版）上的 R Server 的 Azure 存储选项 | Azure"
   description="了解用户可用于 HDInsight（预览版）上的 R Server 的不同存储选项"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
	ms.service="HDInsight"
	ms.date="03/28/2016"
	wacn.date=""/>

# 用于 HDInsight（预览版）上的 R Server 的 Azure 存储选项

HDInsight（预览版）上的 R 服务器有权访问 Azure Blob，并且即将有权访问 Azure 数据湖存储，作为保持分析中的数据、代码、结果对象等的方式。

在 HDInsight 中创建 Hadoop 群集时，将指定 Azure 存储帐户。将该帐户的一个特定 Blob 存储容器指定为对你所创建的群集保存文件系统，即 Hadoop 分布式文件系统 (HDFS)。出于性能目的，HDInsight 群集将在与你指定的主存储帐户相同的数据中心内创建。有关详细信息，请参阅[将 Azure Blob 存储与 HDInsight 配合使用](/documentation/articles/hdinsight-hadoop-use-blob-storage "将 Azure Blob 存储与 HDInsight 配合使用")。


## 使用多个 Azure Blob 存储帐户

如果需要，可以使用 HDI 群集访问多个 Azure 存储帐户或容器。为此，需要在创建群集时在用户界面中指定其他存储帐户，并按照下列步骤进行操作，以在 R 中使用它们。

1.	假设使用存储帐户名“storage1”并使用默认容器“container1”创建 HDInsight 群集。你还指定了其他存储帐户“storage2”。  
2.	现在，将文件“mycsv.csv”复制到目录“/share”，并且想要对该文件执行分析。  

    hadoop fs -mkdir /share
    hadoop fs -copyFromLocal myscsv.scv /share

3.	在 R 代码中，将名称节点设置为“default”，并将你的目录和文件设置为处理

    myNameNode <- "default"
    myPort <- 0

  数据的位置

    bigDataDirRoot <- "/share"  

  定义 Spark 计算上下文

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  设置计算上下文

    rxSetComputeContext(mySparkCluster)

  定义 HDFS 文件系统

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  指定 HDFS 中要分析的输入文件

    inputFile <-file.path(bigDataDirRoot,"mycsv.csv")
 
所有目录和文件引用均指向存储帐户，wasb://container1@storage1.blob.core.chinacloudapi.cn因为这是与 HDInsight 群集相关联的**默认存储帐户**。

现在假设你想要处理位于存储帐户名“storage2”上容器“container2”中的目录“/private”中的名为“mySpecial.csv”的文件。

在 R 代码中，将名称节点引用更改为“storage2”存储帐户。

    myNameNode <- "wasb://container2@storage2.blob.core.chinacloudapi.cn"
    myPort <- 0

  数据的位置

    bigDataDirRoot <- "/private"

  定义 Spark 计算上下文

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  设置计算上下文

    rxSetComputeContext(mySparkCluster)

  定义 HDFS 文件系统

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  指定 HDFS 中要分析的输入文件

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")
 
所有目录和文件引用现在均指向存储帐户，wasb://container2@storage2.blob.core.chinacloudapi.cn因为这是你已指定的**名称节点**。

请注意，将必须配置存储帐户“storage2”上的 /user/RevoShare/<SSH username> 目录：

    hadoop fs -mkdir wasb://container2@storage2.blob.core.chinacloudapi.cn/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.chinacloudapi.cn/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.chinacloudapi.cn/user/RevoShare/<RDP username>


## 在边缘节点上使用 Azure 文件 

还可以选择在边缘节点上使用名为 [Azure 文件](/documentation/articles/storage-how-to-use-files-linux "Azure 文件")的方便数据存储，允许你装载共享到 Linux 文件系统的 Azure 存储文件。对比 HDFS，如果可以在边缘节点上使用本机文件系统，则存储数据文件、R 脚本以及随后可能需要的结果对象将更方便。使用 Azure 文件的最大优点是通过支持的 OS (Win，Linux) 任何系统均可装载并使用文件共享，例如，你或者团队中的其他人拥有的另一个 HDInsight 群集、Azure VM 或者甚至是本地系统。


## 后续步骤

现在，你已了解如何创建包括 R Server 的新 HDInsight 群集，以及从 SSH 会话使用 R 控制台的基础知识，请使用以下资源发现使用 HDInsight 上的 R Server 的其他方法。

- [Overview of R Server on Hadoop（Hadoop 上的 R Server 概述）](/documentation/articles/hdinsight-hadoop-r-server-overview)
- [Get started with R server on Hadoop（Hadoop 上的 R Server 入门）](/documentation/articles/hdinsight-hadoop-r-server-get-started)
- [Add RStudio Server to HDInsight premium（将 RStudio Server 添加到 HDInsight 高级版）](/documentation/articles/hdinsight-hadoop-r-server-install-r-studio)
- [Compute context options for R Server on HDInsight premium（用于 HDInsight 高级版上的 R Server 的计算上下文选项）](/documentation/articles/hdinsight-hadoop-r-server-compute-contexts)

<!---HONumber=Mooncake_0516_2016-->