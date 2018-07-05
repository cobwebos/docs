---
title: 适用于 ML Services on HDInsight 的 Azure 存储解决方案 - Azure | Microsoft Docs
description: 了解 ML Services on HDInsight 所提供的不同存储选项
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: f5b9b180f8a6f825e4d91850ee72af19e6d09a4c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052957"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>适用于 ML Services on HDInsight 的 Azure 存储解决方案

ML Services on HDInsight 可使用各种存储解决方案来保存数据、代码或包含分析结果的对象。 这包括以下选项：

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake 存储](https://azure.microsoft.com/services/data-lake-store/)
- [Azure 文件存储](https://azure.microsoft.com/services/storage/files/)

还可以使用 HDInsight 群集访问多个 Azure 存储帐户或容器。 Azure 文件存储是可在边缘节点上使用的一个方便的数据存储选项，通过它可将 Azure 存储文件共享装载到其他位置，例如 Linux 文件系统。 任何具有受支持操作系统（如 Windows 或 Linux）的系统，都可以装载和使用 Azure 文件共享。 

在 HDInsight 中创建 Hadoop 群集时，将指定 Azure 存储帐户或 Data Lake Store。 该帐户中的某个特定存储容器可为你所创建的群集保存文件系统（例如 Hadoop 分布式文件系统）。 有关详细信息及指南，请参阅：

- [配合使用 Azure 存储与 HDInsight ](../hdinsight-hadoop-use-blob-storage.md)
- [配合使用 Data Lake Store 和 Azure HDInsight 群集](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>将 Azure Blob 存储帐户用于 ML Services 群集

如果在创建 ML Services 群集时指定了多个存储帐户，以下说明介绍如何使用辅助帐户在 ML Services 群集上访问数据和执行操作。 假定为以下存储帐户和容器：storage1 和名为 container1 的一个默认容器以及包含 **container2** 的 storage2。

> [!WARNING]
> 出于性能目的，HDInsight 群集会在与你指定的主存储帐户相同的数据中心内创建。 不支持在 HDInsight 群集之外的其他位置使用存储帐户。

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>将默认存储用于 ML Services on HDInsight

1. 使用 SSH 客户端，连接到群集的边缘节点。 有关将 SSH 与 HDInsight 群集配合使用的信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。
  
2. 将示例文件 mysamplefile.csv 复制到 /share 目录中。 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

3. 切换到 R Studio 或其他 R 控制台，写入 R 代码，将名称节点设置为默认和要访问的文件的位置。  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

所有目录和文件引用都指向存储帐户 `wasb://container1@storage1.blob.core.windows.net`。 这是与 HDInsight 群集关联的**默认存储帐户**。

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>将其他存储用于 ML Services on HDInsight

现在，假设要处理名为 mysamplefile1.csv 的文件，该文件位于 **storage2** 的 **container2** 中的 /private 目录。

在 R 代码中，将名称节点引用指向 **storage2** 存储帐户。

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

    #Location of the data:
    bigDataDirRoot <- "/private"

    #Define Spark compute context:
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define HDFS file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")

所有目录和文件引用现在都指向存储帐户 `wasb://container2@storage2.blob.core.windows.net`。 这是已指定的**名称节点**。

必须在 storage2 上配置 /user/RevoShare/<SSH username> 目录，如下所示：


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-an-azure-data-lake-store-with-ml-services-cluster"></a>将 Azure Data Lake Store 与 ML Services 群集配合使用 

若要将 Data Lake Store 与 HDInsight 群集配合使用，必须允许群集访问你要使用的每个 Azure Data Lake Store。 有关如何使用 Azure 门户创建 HDInsight 群集，并将 Azure Data Lake Store 帐户作为默认存储或附加存储的说明，请参阅[使用 Azure 门户创建带 Data Lake Store 的 HDInsight 群集](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)。

在 R 脚本中使用该存储的方式与使用辅助 Azure 存储帐户的方式（如上一过程中所述）非常类似。

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>为群集添加 Azure Data Lake Store 访问权限
可以使用与 HDInsight 群集关联的 Azure Active Directory (Azure AD) 服务主体来访问 Data Lake Store。

1. 创建 HDInsight 群集时，请在“数据源”选项卡中选择“群集 AAD 标识”。

2. 在“群集 AAD 标识”对话框中的“选择 AD 服务主体”下面，选择“新建”。

为服务主体命名并创建密码后，单击“管理 ADLS 访问”将该服务主体与 Data Lake Store 相关联。

创建群集后，还可向一个或多个 Data Lake store 帐户添加群集访问权限。 打开 Data Lake store 的 Azure 门户条目，转至“数据资源管理器”>“访问权限”>“添加”。 

### <a name="how-to-access-the-data-lake-store-from-ml-services-on-hdinsight"></a>如何从 ML Services on HDInsight 访问 Data Lake Store

获得 Data Lake Store 访问权限后，便可以在 HDInsight 上的 ML Services 群集中使用该存储，其使用方式与使用辅助 Azure 存储帐户类似。 唯一的差别在于，前缀 **wasb://** 需更改为 **adl://**，如下所示：


    # Point to the ADL store (e.g. ADLtest)
    myNameNode <- "adl://rkadl1.azuredatalakestore.net"
    myPort <- 0

    # Location of the data (assumes a /share directory on the ADL account)
    bigDataDirRoot <- "/share"  

    # Define Spark compute context
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    # Set compute context
    rxSetComputeContext(mySparkCluster)

    # Define HDFS file system
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    # Specify the input file in HDFS to analyze
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

以下命令用于为 Data Lake Store 帐户配置 RevoShare 目录，并添加前一示例中的示例 .csv 文件：


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>将 Azure 文件存储用于 ML Services on HDInsight

还有一个可在边缘节点上使用的便利数据存储选项，我们称之为 [Azure 文件]((https://azure.microsoft.com/services/storage/files/)。 使用该选项可将 Azure 存储的文件共享装载到 Linux 文件系统。 对比 HDFS，如果可以在边缘节点上使用本机文件系统，则存储数据文件、R 脚本以及随后可能需要的结果对象将更方便。 

使用 Azure 文件的主要好处之一是，装有受支持 OS（例如 Windows 或 Linux）的系统都可以装载和使用文件共享。 例如，自己或者团队成员拥有的另一个 HDInsight 群集、Azure VM 甚至本地系统均可使用 Azure 文件。 有关详细信息，请参阅：

- [如何配合使用 Azure 文件存储与 Linux ](../../storage/files/storage-how-to-use-files-linux.md)
- [如何配合使用 Azure 文件存储与 Windows ](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>后续步骤

* [HDInsight 上的 ML Services 群集概述](r-server-overview.md)
* [Hadoop 上的 ML Services 群集入门](r-server-get-started.md)
* [适用于 HDInsight 上的 ML Services 群集的计算上下文选项](r-server-compute-contexts.md)

