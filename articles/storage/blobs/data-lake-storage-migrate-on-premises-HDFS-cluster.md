---
title: 使用 Azure Data Box 将数据从本地 HDFS 存储到 Azure 存储
description: 将数据从本地 HDFS 存储迁移到 Azure 存储
services: storage
author: normesta
ms.service: storage
ms.date: 06/05/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 9a42135df38cde91cc6626a3f7d0328334af0a5d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729045"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>使用 Azure Data Box 将数据从本地 HDFS 存储迁移到 Azure 存储

通过使用 Data Box 设备，可以从本地 HDFS 存储到 Azure 存储 （blob 存储或数据湖存储第 2 代） Hadoop 群集的迁移数据。 您可以选择从 80 TB 的数据框或 770 TB 数据框过大。

本文可帮助你完成这些任务：

:heavy_check_mark:将数据复制到数据框或数据框大量的设备。

:heavy_check_mark:将设备发回 Microsoft。

:heavy_check_mark:将移动到你的数据湖存储第 2 代的存储帐户的数据。

## <a name="prerequisites"></a>必备组件

您需要以下操作来完成迁移。

* Azure 存储帐户**不**具有分层命名空间对其启用。

* 如果你想要使用 Azure 数据湖存储第 2 代帐户 (存储帐户**does**具有分层命名空间在其上启用)，则可能需要在此时创建。

* 包含源数据的本地 Hadoop 群集。

* [Azure Data Box 设备](https://azure.microsoft.com/services/storage/databox/)。

    - [订购 Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered)或[数据框繁重](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered)。 时排序你的设备，请务必选择存储帐户**不**具有分层命名空间对其启用。 这是因为 Data Box 设备尚不支持直接引入到 Azure 数据湖存储第 2 代。 你将需要复制到存储帐户，然后执行到 ADLS 第 2 代帐户的另一个副本。 下面的步骤中提供相关说明。
    - 连接电缆并连接你[Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)或[数据框大量](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up)到本地网络。

如果一切就绪，让我们开始。

## <a name="copy-your-data-to-a-data-box-device"></a>将数据复制到 Data Box 设备

要将数据从本地 HDFS 存储复制到 Data Box 设备，将进行一些设置，并使用[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)工具。

如果要复制的数据量不只是单个数据框的容量或单个节点上的数据框繁重，分解到大小适合你的设备数据集。

请按照下列步骤将通过 REST Api 的 Blob/对象存储的数据复制到 Data Box 设备。 REST API 接口将使设备出现作为 HDFS 存储到群集。 


1. 通过 REST 将数据复制之前，标识连接到数据框或数据框高上的 REST 接口的安全性和连接基元。 登录到本地 web UI 的数据框中，并转到**连接和复制**页。 针对 Azure 存储帐户为你的设备，在**访问设置**、 查找和选择**REST**。

    !["连接并复制"页](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 在访问存储帐户和上传数据对话框中，复制**Blob 服务终结点**并**存储帐户密钥**。 从 blob 服务终结点，省略`https://`和尾部反斜杠。

    在这种情况下，终结点是： `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`。 将使用的 URI 的主机部分是： `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`。 有关示例，请参阅如何[连接到基于 http 的 REST](/azure/databox/data-box-deploy-copy-data-via-rest)。 

     !["访问存储帐户和将数据上传"对话框](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 添加终结点和到数据框或数据框大量的节点 IP 地址`/etc/hosts`每个节点上。

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    如果为 DNS 使用其他一些机制，则应确保可以解析终结点数据。
    
4. 设置 shell 变量`azjars`以指向`hadoop-azure`和`microsoft-windowsazure-storage-sdk`jar 文件。 这些文件是 Hadoop 安装目录下 (您可以检查这些文件是否存在使用此命令`ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`其中`<hadoop_install_dir>`是 Hadoop 安装的目录) 使用的完整路径。 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

5. 创建想要用于数据复制的存储容器。 此命令的一部分，你还应指定目标文件夹。 此时，这可能是虚拟的目标文件夹。

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -mkdir -p  wasb://[container_name]@[blob_service_endpoint]/[destination_folder]
    ```

6. 运行 list 命令以确保已创建了容器和文件夹。

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -ls -R  wasb://[container_name]@[blob_service_endpoint]/
    ```

7. 到前面创建的容器，可将数据 Hadoop HDFS 复制到数据框 Blob 存储。 如果找不到要复制到的文件夹，该命令会自动创建它。

    ```
    # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -strategy dynamic -m 4 -update \
    [source_directory] \
           wasb://[container_name]@[blob_service_endpoint]/[destination_folder]       
    ```
   `-libjars`选项用于使`hadoop-azure*.jar`和依赖项`azure-storage*.jar`文件可供`distcp`。 某些群集已发生此问题。
   
   下面的示例演示如何将`distcp`命令用于将数据复制。
   
   ```
   # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -strategy dynamic -m 4 -update \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/testfiles
   ```
  
若要提高复制速度：
- 请尝试更改映射器数。 (上面的示例使用`m`= 4 的映射器。)
- 请尝试在运行多个`distcp`并行。
- 请记住较大的文件比小型文件的更好地执行。
    
## <a name="ship-the-data-box-to-microsoft"></a>寄送到 Microsoft 的数据框

按照以下步骤来准备并寄回给 Microsoft Data Box 设备。

1. 数据复制完成后，运行：
    
    - [准备将传送你的数据框或数据框大量](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)。
    - 设备准备完成后，下载物料清单文件。 将使用这些 BOM 或清单文件更高版本以验证数据上传到 Azure。 
    - 关闭设备，请拔下电缆。
2.  安排 UPS 取件。 遵照说明操作：

    - [提供你的数据框](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up) 
    - [提供在数据框繁重](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)。
3.  Microsoft 会收到你的设备、 连接到数据中心网络并将数据上载到 （与层次结构禁用的命名空间） 指定的存储帐户之后当您放置设备顺序。 验证对物料清单文件将数据上载到 Azure。 现在可以将这些数据移到数据湖存储第 2 代存储帐户。


## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>移动到你的数据湖存储第 2 代的存储帐户数据

如果使用 Azure 数据湖存储第 2 代作为数据存储，则需要此步骤。 如果使用的只是 blob 存储帐户层次结构命名空间不作为数据存储区，您不需要执行此步骤。

可以通过两种方式来执行此操作。

- 使用[Azure 数据工厂将数据移到 ADLS 第 2 代](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2)。 您必须指定**Azure Blob 存储**作为源。

- 使用基于 Azure 的 Hadoop 群集。 可以运行此 DistCp 命令：

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

此命令从你的存储帐户复制数据和元数据到数据湖存储第 2 代存储帐户。

## <a name="next-steps"></a>后续步骤

了解 HDInsight 群集时，数据湖存储第 2 代的工作原理。 请参阅[配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)。
