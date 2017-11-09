---
title: "了解 Hadoop 群集中的数据以及如何在 Azure 机器学习中创建模型 | Microsoft 文档"
description: "对于采用 HDInsight Hadoop 群集的端到端方案，使用 Team Data Science Process 来构建和部署使用公开可用数据集的模型。"
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: e9e76c91-d0f6-483d-bae7-2d3157b86aa0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: 238b7d6bb6289b5f2e8d2a20f4335724087dfd48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Team Data Science Process 的工作原理：使用 Azure HDInsight Hadoop 群集
通过本演练，我们在使用 [Azure HDInsight Hadoop 群集](https://azure.microsoft.com/services/hdinsight/)的端到端方案中借助 [Team Data Science Process (TDSP)](overview.md)，对公开发布的[纽约市出租车行程](http://www.andresmh.com/nyctaxitrips/)数据集中的数据进行存储、探索和实施特性工程，以及对该数据进行下采样。 数据模型是使用 Azure 机器学习构建的，用于处理二元分类、多类分类和回归预测任务。

针对使用 HDInsight Hadoop 群集进行数据处理的类似方案，有关介绍处理其大型 (1 TB) 数据集的演练，请参阅 [Team Data Science Process - 使用 Azure HDInsight Hadoop 群集处理 1 TB 数据集](hive-criteo-walkthrough.md)。

也可以使用 IPython Notebook 完成该具备 1 TB 数据集的演练中介绍的任务。 想要尝试此方法的用户应该咨询[使用 Hive ODBC 连接的 Criteo 演练](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb)主题。

## <a name="dataset"></a>NYC 出租车行程数据集介绍
NYC 出租车行程数据是大约 20 GB（未压缩时约为 48 GB）的压缩逗号分隔值 (CSV) 文件，其中包含超过 1.73 亿个单独行程及每个行程支付的费用。 每个行程记录都包括上车和下车的位置和时间、匿名的出租车司机驾驶证编号和徽章（出租车的唯一 ID）编号。 数据涵盖 2013 年的所有行程，并每月在以下两个数据集中提供：

1. ‘trip_data’ CSV 文件包含行程的详细信息，例如乘客数、上车和下车地点、行程持续时间和行程距离。 下面是一些示例记录：
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. “trip_fare”CSV 文件包含每个行程支付费用的详细信息，例如付款类型、费用金额、附加费和税金、小费和通行费以及支付的总金额。 下面是一些示例记录：
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

联接 trip\_data 和 trip\_fare 的唯一键由以下字段组成：medallion、hack\_licence 和 pickup\_datetime。

若要获取与特定行程相关的所有详细信息，只需联接以下三个键即可：“medallion”、“hack\_license”和“pickup\_datetime”。

我们会在稍后将数据存储到 Hive 表时，介绍该数据的更多详细信息。

## <a name="mltasks"></a>预测任务示例
着手处理数据时，根据其分析确定要进行的预测类型有助于明确需要包括在过程中的任务。
下面是本演练中讨论的三个预测问题示例，其表述基于 *tip\_amount*：

1. **二元分类**：预测是否已支付某个行程的小费，即大于 $0 的 *tip\_amount* 是正例，等于 $0 的 *tip\_amount* 是反例。
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0
2. **多类分类**：预测为行程支付的小费金额范围。 我们将 *tip\_amount* 划分五个分类收纳组或类别：
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **回归任务**：预测为行程支付的小费金额。  

## <a name="setup"></a>针对高级分析设置 HDInsight Hadoop 群集
> [!NOTE]
> 这通常是**管理员**任务。
> 
> 

可以通过三个步骤为使用 HDInsight 群集的高级分析设置 Azure 环境：

1. [创建存储帐户](../../storage/common/storage-create-storage-account.md)：此存储帐户用于在 Azure Blob 存储中存储数据。 HDInsight 群集中使用的数据也驻留在此处。
2. [为高级分析过程和技术自定义 Azure HDInsight Hadoop 群集](customize-hadoop-cluster.md)。 此步骤将创建一个在所有节点上都安装有 64 位 Anaconda Python 2.7 的 Azure HDInsight Hadoop 群集。 自定义 HDInsight 群集时需牢记两个重要步骤。
   
   * 创建 HDInsight 群集时，请记住将其与步骤 1 中创建的存储帐户相链接。 此存储帐户用于访问在该群集内处理的数据。
   * 创建群集后，启用对其头节点的远程访问。 导航到“配置”选项卡，并单击“启用远程”。 此步骤指定用于远程登录的用户凭据。
3. [创建 Azure 机器学习工作区](../studio/create-workspace.md)：此 Azure 机器学习工作区用于构建机器学习模型。 使用 HDInsight 群集完成初始数据探索并进行下采样后，此任务将得到解决。

## <a name="getdata"></a>从公共源获取数据
> [!NOTE]
> 这通常是**管理员**任务。
> 
> 

要从 [NYC 出租车行程](http://www.andresmh.com/nyctaxitrips/)数据集的公共位置获取该数据集，可以使用[将数据从 Azure Blob 存储移入和移出](move-azure-blob.md)中所述的任意方法，将数据复制到计算机。

此处介绍如何使用 AzCopy 传输包含数据的文件。 若要下载并安装 AzCopy，请按照 [AzCopy 命令行实用程序入门](../../storage/common/storage-use-azcopy.md)中的说明进行操作。

1. 在“命令提示符”窗口中，发出以下 AzCopy 命令，将 <path_to_data_folder> 替换为所需目标：

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. 复制完成后，所选数据文件夹中共有 24 个压缩文件。 将下载的文件解压缩到本地计算机上的同一目录。 记下未压缩的文件所在的文件夹。 此文件夹将称为 *<path\_to\_unzipped_data\_files\>*。

## <a name="upload"></a>将数据上传到 Azure HDInsight Hadoop 群集的默认容器
> [!NOTE]
> 这通常是**管理员**任务。
> 
> 

在以下 AzCopy 命令中，将以下参数替换为创建 Hadoop 群集和解压缩数据文件时所指定的实际值。

* ***<path_to_data_folder>*** 计算机上包含解压缩数据文件的目录（与路径）  
* ***<Hadoop 群集的存储帐户名称>*** 与 HDInsight 群集关联的存储帐户
* ***<Hadoop 群集的默认容器>*** 群集使用的默认容器。 注意，默认容器的名称通常与群集本身的名称相同。 例如，如果群集名为“abc123.azurehdinsight.net”，则默认容器为 abc123。
* ***<存储帐户密钥>*** 群集使用的存储帐户密钥

在计算机的“命令提示符”或“Windows PowerShell”窗口中，运行以下两个 AzCopy 命令。

此命令将行程数据上传到 Hadoop 群集的默认容器中的 ***nyctaxitripraw*** 目录。

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

此命令将费用数据上传到 Hadoop 群集的默认容器中的 ***nyctaxifareraw*** 目录。

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

现在，数据应在 Azure Blob 存储中，并且可以在 HDInsight 群集中使用。

## <a name="#download-hql-files"></a>登录到 Hadoop 群集的头节点，并准备进行探索数据分析
> [!NOTE]
> 这通常是**管理员**任务。
> 
> 

若要访问群集的头节点以进行探索数据分析和数据的下采样，请按照[访问 Hadoop 群集的头节点](customize-hadoop-cluster.md#headnode)中所述的过程进行操作。

在本演练中，我们主要使用 [Hive](https://hive.apache.org/)（一种类似 SQL 的查询语言）编写的查询来执行初步数据探索。 Hive 查询存储在 .hql 文件中。 然后，对此数据进行下采样，以便用于在 Azure 机器学习中构建模型。

要准备用于探索数据分析的群集，需将包含相关 Hive 脚本的 .hql 文件从 [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) 下载到头节点上的本地目录 (C:\temp)。 若要执行此操作，请从群集的头节点中打开“命令提示符”，并发出以下两个命令：

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

这两个命令会将本演练中需要的所有 .hql 文件下载到头节点中的本地目录 C:\temp&#92;。

## <a name="#hive-db-tables"></a>创建按月分区的 Hive 数据库和表
> [!NOTE]
> 这通常是**管理员**任务。
> 
> 

现在已准备就绪，可以为 NYC 出租车数据集创建 Hive 表。
在 Hadoop 群集头节点的桌面上，打开 ***Hadoop 命令行***，并通过输入命令输入 Hive 目录

    cd %hive_home%\bin

> [!NOTE]
> **从以上 Hive bin/ 目录提示符运行此演练中的所有 Hive 命令。这会自动处理任何路径问题。我们在本演练中交替使用术语“Hive 目录提示符”、“Hive bin/ 目录提示符”和“Hadoop 命令行”。**
> 
> 

从 Hive 目录提示符，在头节点的 Hadoop 命令行中输入以下命令，以提交 Hive 查询，从而创建 Hive 数据库和表：

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

下面是 ***C:\temp\sample\_hive\_create\_db\_and\_tables.hql*** 文件的内容，用于创建 Hive 数据库 ***nyctaxidb*** 以及表“行程”和“费用”。

    create database if not exists nyctaxidb;

    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

此 Hive 脚本会创建两个表：

* “行程”表包含每个行程的行程详情（司机详细信息、上车时间、行程距离和时间）
* “费用”表包含费用详细信息（费用金额、小费金额、通行费和附加费）。

如果需要有关这些过程的任何其他帮助或想要了解另外的过程，请参阅[直接从 Hadoop 命令行提交 Hive 查询](move-hive-tables.md#submit)部分。

## <a name="#load-data"></a>按分区将数据加载到 Hive 表
> [!NOTE]
> 这通常是**管理员**任务。
> 
> 

NYC 出租车数据集具有按月划分的自然分区，用于加快处理和查询时间。 以下 PowerShell 命令（使用 **Hadoop 命令行** 从 Hive 目录发出）将数据加载到按月分区的“行程”和“费用”Hive 表。

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

*sample\_hive\_load\_data\_by\_partitions.hql* 文件包含以下 **LOAD** 命令。

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

注意，探索进程中使用的多个 Hive 查询涉及查看单个分区或仅几个分区。 但是可以在整个数据中运行这些查询。

### <a name="#show-db"></a>在 HDInsight Hadoop 群集中显示数据库
若要在 Hadoop 命令行窗口中显示 HDInsight Hadoop 群集中创建的数据库，请在 Hadoop 命令行中运行以下命令：

    hive -e "show databases;"

### <a name="#show-tables"></a>显示 nyctaxidb 数据库中的 Hive 表
若要显示 nyctaxidb 数据库中的表，请在 Hadoop 命令行中运行以下命令：

    hive -e "show tables in nyctaxidb;"

可以通过发出以下命令，确认表是否分区：

    hive -e "show partitions nyctaxidb.trip;"

预期输出如下所示：

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

同样，可以通过发出以下命令确保费用表已分区：

    hive -e "show partitions nyctaxidb.fare;"

预期输出如下所示：

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>Hive 中的数据探索和功能设计
> [!NOTE]
> 这通常是**数据科学家**任务。
> 
> 

对于加载到 Hive 表中的数据的数据探索和特性工程任务，可使用 Hive 查询完成。 以下是本部分中介绍的此类任务的示例：

* 查看两个表中的前 10 条记录。
* 在不同的时间范围中探索几个字段的数据分布。
* 调查经度和纬度字段的数据质量。
* 根据 **tip\_amount** 生成二元和多元分类标签。
* 通过计算直接行程距离生成特性。

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>探索：查看“行程”表中的前 10 条记录
> [!NOTE]
> 这通常是**数据科学家**任务。
> 
> 

为了查看数据是什么样，我们会检查每个表的 10 条记录。 在 Hadoop 命令行控制台中，从 Hive 目录提示符分别运行以下两个查询来检查记录。

获取“行程”表中第一个月的前 10 条记录：

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

获取“费用”表中第一个月的前 10 条记录：

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

将记录保存到文件以方便查看通常很有用。 对上述查询进行细微更改将实现此操作：

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>探索：查看 12 个分区中每个分区的记录数
> [!NOTE]
> 这通常是**数据科学家**任务。
> 
> 

此任务关注的是在历年内行程次数如何变化。 按月分组可以让我们看到行程的分布情况。

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

这会得出以下输出：

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

此处，第一列表示月份，第二列表示该月份的行程数。

我们还可以通过在 Hive 目录提示符处发出以下命令来计算行程数据集中的记录总数。

    hive -e "select count(*) from nyctaxidb.trip;"

这会生成：

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

使用类似于对行程数据集显示的命令，可以从 Hive 目录提示符中针对费用数据集发出 Hive 查询，以便验证记录数。

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

这会得出以下输出：

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

注意，针对这两个数据集会返回完全相同的每月行程数。 这提供了数据已正确加载的第一个验证。

可从 Hive 目录提示符中使用以下命令计算费用数据集中的记录总数：

    hive -e "select count(*) from nyctaxidb.fare;"

这会生成：

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

两个表中的记录总数也是相同的。 这提供了数据已正确加载的第二个验证。

### <a name="exploration-trip-distribution-by-medallion"></a>浏览：依据徽章的行程分布
> [!NOTE]
> 这通常是**数据科学家**任务。
> 
> 

此示例标识在给定的时间段内具有 100 多个行程的徽章（出租车数）。 查询受益于分区表访问，因为它受分区变量**月份**的限制。 查询结果将写入头节点上 `C:\temp` 中的本地文件 queryoutput.tsv。

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

以下是用于检查的 *sample\_hive\_trip\_count\_by\_medallion.hql* 文件的内容。

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

NYC 出租车数据集中的牌照标识一辆唯一的出租车。 通过询问特定时间段内，哪些出租车的行程数超过了一定量，可以确定哪些车处于“忙碌”状态。 以下示例标识前三个月内行程数超过 100 的出租车，并将查询结果保存到本地文件 C:\temp\queryoutput.tsv。

以下是用于检查的 *sample\_hive\_trip\_count\_by\_medallion.hql* 文件的内容。

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

从 Hive 目录提示符中发出以下命令：

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>浏览：依据徽章和 hack_license 的行程分布
> [!NOTE]
> 这通常是**数据科学家**任务。
> 
> 

探索数据集时，我们经常想要检查值组共同出现的次数。 本部分提供如何针对出租车和司机执行此操作的示例。

*sample\_hive\_trip\_count\_by\_medallion\_license.hql* 文件将“medallion”和“hack_license”上的费用数据集分组，并返回每个组合的计数。 以下是其内容。

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

此查询返回出租车和特定司机的组合，按行程数降序排序。

从 Hive 目录提示符中，运行：

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

查询结果将写入本地文件 C:\temp\queryoutput.tsv。

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitudelatitude-records"></a>探索：通过检查无效的经度/纬度记录，评估数据质量
> [!NOTE]
> 这通常是**数据科学家**任务。
> 
> 

探索数据分析的共同目标是剔除无效或错误的记录。 本部分中的示例确定经度或纬度字段是否包含 NYC 区域外的值。 由于有可能此类记录具有错误的经纬值，因此我们希望将其从任何用于建模的数据中清除。

以下是用于检查的 *sample\_hive\_quality\_assessment.hql* 文件内容。

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


从 Hive 目录提示符中，运行：

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

此命令中包含的 *-S* 参数阻止状态屏幕打印输出 Hive Map/Reduce 作业。 这非常有用，因为它使 Hive 查询输出的屏幕打印更加易读。

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>探索：行程小费的二元类分布
> [!NOTE]
> 这通常是**数据科学家**任务。
> 
> 

对于[预测任务示例](hive-walkthrough.md#mltasks)部分中所述的二元分类问题，了解是否已付小费非常有用。 小费的分布为二元形式：

* 已付小费（类 1，tip\_amount > $0）  
* 无小费（类 0，tip\_amount = $0）。

下面显示的 *sample\_hive\_tipped\_frequencies.hql* 文件执行此操作。

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

从 Hive 目录提示符中，运行：

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>探索：多类设置中的类分布
> [!NOTE]
> 这通常是**数据科学家**任务。
> 
> 

对于[预测任务示例](hive-walkthrough.md#mltasks)部分中所述的多类分类问题，此数据集也适用于自然分类，在这种分类中可预测所付小费的金额。 我们可以使用 bin 在查询中定义小费范围。 为了获取各种小费范围的类分布，我们使用 *sample\_hive\_tip\_range\_frequencies.hql* 文件。 以下是其内容。

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

从 Hadoop 命令行控制台运行以下命令：

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-direct-distance-between-two-longitude-latitude-locations"></a>探索：计算两个经纬位置之间的直接距离
> [!NOTE]
> 这通常是**数据科学家**任务。
> 
> 

测量直接距离可得出它与实际行程距离之间的差。 我们注意到，如果乘客发现司机故意绕远路，则该乘客提供小费的可能性更低，这是此功能的着眼点。

为了查看实际行程距离与两个经纬点（“大圆”距离）之间的[半正矢距离](http://en.wikipedia.org/wiki/Haversine_formula)的比较结果，我们使用 Hive 中可用的三角函数，即：

    set R=3959;
    set pi=radians(180);

    insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

在上面的查询中，R 表示以英里为单位的地球半径，pi 转换为弧度。 注意，“筛选”了经纬点，以便删除远离 NYC 区域的值。

在此例中，我们将结果写入名为“queryoutputdir”的目录。 下面显示的命令序列先创建此输出目录，并运行 Hive 命令。

从 Hive 目录提示符中，运行：

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


查询结果将写入 Hadoop 群集的默认容器下的 9 个 Azure blob（***queryoutputdir/000000\_0*** 到 ***queryoutputdir/000008\_0***）。

为了查看各个 blob 的大小，我们从 Hive 目录提示符运行以下命令：

    hdfs dfs -ls wasb:///queryoutputdir

若要查看给定文件（如 000000\_0）的内容，可使用 Hadoop 的 `copyToLocal` 命令。

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> 对于大型文件，`copyToLocal` 可能非常慢，因此不建议将其用于此类文件。  
> 
> 

将此数据驻留在 Azure blob 中的一个主要优点是，我们可以使用[导入数据][import-data]模块在 Azure 机器学习中探索数据。

## <a name="#downsample"></a>在 Azure 机器学习中对数据进行下采样和构建模型
> [!NOTE]
> 这通常是**数据科学家**任务。
> 
> 

在探索数据分析阶段之后，便可以开始在 Azure 机器学习中对数据进行下采样，以便构建模型。 本部分演示如何使用 Hive 查询对数据进行下采样，并从 Azure 机器学习中的[导入数据][import-data]模块访问该数据。

### <a name="down-sampling-the-data"></a>对数据进行下采样
此过程包含两个步骤。 首先，在存在于所有记录中的三个键上将“nyctaxidb.trip”和“nyctaxidb.fare”表联接，这三个键是：“medallion”、“hack\_license”和“pickup\_datetime”。 然后，生成一个二元分类标签“tipped”和一个多类分类标签“tip\_class”。

为了能够直接从 Azure 机器学习中的[导入数据][import-data]模块使用已经过下采样的数据，必须将上述查询的结果存储到内部 Hive 表中。 接下来，我们将创建一个内部 Hive 表，并使用已联接且已经过下采样的数据填充其内容。

查询直接应用标准 Hive 函数，以从“pickup\_datetime”字段中生成一天的某一小时、一年的某一周和一周的某一日（1 代表星期一，7 代表星期日），以及上下车位置之间的直接距离。 用户可参阅 [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) 获取此类函数的完整列表。

然后，查询会对数据进行下采样，以便查询结果适合 Azure 机器学习工作室。 导入到该工作室中的原始数据集仅有 1 %。

下面是 *sample\_hive\_prepare\_for\_aml\_full.hql* 文件的内容，用于为在 Azure 机器学习中构建模型准备数据。

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;

        --- now insert contents of the join into the above internal table

        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

从 Hive 目录提示符中运行此查询：

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

我们现在已拥有了一个内部表“nyctaxidb.nyctaxi_downsampled_dataset”，可使用 Azure 机器学习中的[导入数据][import-data]模块访问该表。 此外，可以将此数据集用于构建机器学习模型。  

### <a name="use-the-import-data-module-in-azure-machine-learning-to-access-the-down-sampled-data"></a>使用 Azure 机器学习中的“导入数据”模块访问已经过下采样的数据
作为在 Azure 机器学习的[导入数据][import-data]模块中发出 Hive 查询的先决条件，我们需要对 Azure 机器学习工作区的访问权限和对群集凭据及其关联的存储帐户的访问权限。

有关[导入数据][import-data]模块的一些详细信息和要输入的参数：

**HCatalog 服务器 URI**：如果群集名称是“abc123”，则其 URI 即为：https://abc123.azurehdinsight.net

**Hadoop 用户帐户名称**：为群集选择的用户名（**不是**远程访问用户名）

**Hadoop 用户帐户密码**：为群集选择的密码（**不是**远程访问密码）

**输出数据的位置**：为此选项选择 Azure。

**Azure 存储帐户名称**：与群集关联的默认存储帐户的名称。

**Azure 容器名称**：这是群集的默认容器名称，通常与群集名称相同。 对于名为"abc123"的群集，默认容器名称即为 abc123。

> [!IMPORTANT]
> **我们希望使用 Azure 机器学习中的[导入数据][import-data]模块查询的任何表都必须是内部表。** 要确定数据库 D.db 中的表 T 是否是内部表，请参考如下操作提示。
> 
> 

从 Hive 目录提示符中，发出命令：

    hdfs dfs -ls wasb:///D.db/T

如果该表是内部表并且已填充，则其内容一定会在此处显示。 确定表是否是内部表的另一种方法是使用 Azure 存储资源管理器。 使用它导航到群集的默认容器名称，并按表名称进行筛选。 如果显示了表及其内容，则可确定该表为内部表。

下面是 Hive 查询和[导入数据][import-data]模块的快照：

![“导入数据”模块的 Hive 查询](./media/hive-walkthrough/1eTYf52.png)

注意，由于已经过下采样的数据驻留在默认容器中，所以从 Azure 机器学习中产生的 Hive 查询非常简单，仅为“SELECT * FROM nyctaxidb.nyctaxi\_downsampled\_data”。

现在，便可以将数据集用作构建机器学习模型的起点。

### <a name="mlmodel"></a>在 Azure 机器学习中构建模型
我们现已能在 [Azure 机器学习](https://studio.azureml.net)中进行模型构建和模型部署。 数据已可用于解决上述预测问题：

**1.二元分类**：预测某个行程是否支付小费。

**使用的学习器：**双类逻辑回归

a. 对于此问题，我们的目标（或类）标签为“tipped”。 我们的原始下采样数据集具有几个列，这些列是此分类实验的目标泄漏。 具体：tip\_class、tip\_amount 和 total\_amount 揭示有关测试时不可用的目标标签的信息。 使用[选择数据集中的列][select-columns]模块移除这些列，不将其纳入考虑。

以下快照显示预测给定行程是否支付小费的实验。

![实验快照](./media/hive-walkthrough/QGxRz5A.png)

b. 对于此实验，我们的目标标签分布大约是 1:1。

以下快照显示该二元分类问题的小费类标签的分布情况。

![提示类标签的分布](./media/hive-walkthrough/9mM4jlD.png)

最终，我们获得的 AUC 为 0.987，如下图所示。

![AUC 值](./media/hive-walkthrough/8JDT0F8.png)

**2.多类分类**：使用以前定义的类预测为行程支付的小费金额范围。

**使用的学习器：**多类逻辑回归

a. 对于此问题，我们的目标（或类）标签为“tip\_class”，其取值有五种选择（0、1、2、3、4）。 与二元分类的情况类似，我们也具有几个作为此实验的目标泄漏的列。 具体：tipped、tip\_amount 和 total\_amount 揭示有关测试时不可用的目标标签的信息。 使用[选择数据集中的列][select-columns]模块移除这些列。

以下快照显示预测小费可能归属的 bin 的实验（类 0：小费 = $0，类 1：小费 > $0 且 <= $5，类 2：小费 > $5 且 <= $10，类 3：小费 > $10 且 <= $20，类 4：小费 > $20）

![实验快照](./media/hive-walkthrough/5ztv0n0.png)

现在，我们将展示实际测试类的分布情况。 可以看到，类 0 和类 1 的情况很普遍，而其他类的情况很少。

![测试类分布](./media/hive-walkthrough/Vy1FUKa.png)

b. 对于此实验，我们使用混淆矩阵检查来预测准确性。 如下所示。

![混淆矩阵](./media/hive-walkthrough/cxFmErM.png)

注意，虽然此模型对于普遍类的预测准确性很高，但对于较少情况的类，其并未做好“学习”工作。

**3.回归任务**：预测为行程支付的小费金额。

**使用的学习器：**提升决策树

a. 对于此问题，我们的目标（或类）标签为“tip\_amount”。 这种情况下，我们的目标泄漏为：tipped、tip\_class 和 total\_amount；所有这些变量都揭示有关测试时通常不可用的小费金额的信息。 使用[选择数据集中的列][select-columns]模块移除这些列。

以下快照显示预测支付的小费金额的实验。

![实验快照](./media/hive-walkthrough/11TZWgV.png)

b. 对于回归问题，我们通过查看预测中的平方误差，决定系数等，测量预测准确性。 显示如下。

![预测统计信息](./media/hive-walkthrough/Jat9mrz.png)

我们可以看到，决定系数是 0.709，这意味着我们的模型系数解释了大约 71％ 的方差。

> [!IMPORTANT]
> 若要深入了解 Azure 机器学习以及如何访问和使用它，请参阅[什么是机器学习？](../studio/what-is-machine-learning.md)。 [Cortana Intelligence 库](https://gallery.cortanaintelligence.com/)是一个非常有用的资源，可用于在 Azure 机器学习上进行一系列机器学习实验。 该库涵盖了各类实验，并提供对 Azure 机器学习功能范围的全面介绍。
> 
> 

## <a name="license-information"></a>许可证信息
此示例演练及其附带脚本在 MIT 许可证下由 Microsoft 共享。 如需详细信息，请查看 GitHub 上的示例代码目录中的 LICENSE.txt 文件。

## <a name="references"></a>参考
•   [Andrés Monroy NYC 出租车行程下载页面](http://www.andresmh.com/nyctaxitrips/)  
•    [由 Chris Whong 提供的 FOILing NYC 出租车行程数据](http://chriswhong.com/open-data/foil_nyc_taxi/)   
•   [NYC 出租车和礼车委员会研究和统计信息](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
