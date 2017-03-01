---
title: "Azure Data Lake 中可缩放的数据科研：端到端演练 | Microsoft 文档"
description: "如何使用 Azure Data Lake 对数据集进行数据浏览和二进制分类任务。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 91a8207f-1e57-4570-b7fc-7c5fa858ffeb
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: bradsev;weig
translationtype: Human Translation
ms.sourcegitcommit: 34441f27e842214d009d64fbc658ff5b7c05df5d
ms.openlocfilehash: e2aab1363c6a2ffef529f0708cb3bec9c095cf59


---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Azure Data Lake 中可缩放的数据科研：端到端演练
此演练介绍如何使用 Azure Data Lake 对 NYC 出租车行程和车费数据集的示例进行数据浏览和二进制分类任务，以预测小费是否是按车费所支付的。 它将以端到端的方式指导用户完成从数据采集到模型定型，再到部署发布模型的 Web 服务的 [Team Data Science Process](http://aka.ms/datascienceprocess)。

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) 具有数据科学家所需的所有功能，让他们可以轻松存储任何大小、形状和速度的数据，并且可以以经济高效的方式执行数据处理、高级分析以及具有高扩展性的机器学习建模。   按每个作业付费，只有实际处理数据时才会产生费用。 Azure Data Lake Analytics 包括一种名为 U-SQL 的语言，它将 SQL 的声明性本质与 C# 的表达能力很好地加以结合，借此提供可扩展的分布式查询功能。 它通过读取应用构架、插入自定义逻辑和用户定义函数 (UDF) 使用户能够处理非结构化数据，同时包括了可扩展性以实现大规模精细化控制。 若要深入了解 U-SQL 的设计理念，请参阅 [Visual Studio 博客文章](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)。

Data Lake Analytics 也是 Cortana Analytics 套件的重要部分，可与 Azure SQL 数据仓库、Power BI 以及数据工厂协同工作。 这为用户提供了完整的云大数据和高级分析平台。

本演练首先介绍了使用 Data Lake Analytics 完成任务所需的先决条件和资源，这些任务将形成数据科学过程，同时介绍了如何安装它们。 然后概述了使用 U-SQL 进行数据处理的步骤，最后介绍了如何将 Python 和 Hive 与 Azure 机器学习工作室配合使用以构建和部署预测模型。 

### <a name="u-sql-and-visual-studio"></a>U-SQL 和 Visual Studio
本演练建议使用 Visual Studio 编辑 U-SQL 脚本来处理数据集。 此处所述的 U-SQL 脚本将提供在单独的文件中。 此过程包括数据的引入、浏览和采样。 它还介绍了如何从 Azure 门户运行 U-SQL 脚本化作业。 为关联的 HDInsight 群集中的数据创建了 Hive 表，便于在 Azure 机器学习工作室中构建和部署二进制分类模型。  

### <a name="python"></a>Python
本演练中还包括了一部分，其中介绍了如何将 Azure 机器学习工作室与 Python 配合使用以构建和部署预测模型。  针对此过程中的步骤，我们提供了包含 Python 脚本的 Jupyter Notebook。 此 Notebook 包括适用于其他功能设计步骤和模型构建的代码，例如，多类分类建模和回归建模，以及此处提到的二进制分类模型。 回归任务用于根据其他小费特征预测小费的金额。 

### <a name="azure-machine-learning"></a>Azure 机器学习
Azure 机器学习工作室用于生成和部署预测模型。 有两种方法可完成此操作：在 HDInsight (Hadoop) 群集上使用 Python 脚本或使用 Hive 表。

### <a name="scripts"></a>脚本
本演练中仅概述了主要步骤。 可从 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) 下载完整的 **U-SQL 脚本**和 **Jupyter Notebook**。

## <a name="prerequisites"></a>先决条件
在开始阅读这些主题前，必须具有：

* Azure 订阅。 如果还没有 Azure 订阅，请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* [推荐] Visual Studio 2013 或 2015。 如果尚未安装这些版本之一，可从[此处](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)下载免费的 Community 版本。 在 Visual Studio 部分下，单击“下载 Community 2015”按钮。 

> [!NOTE]
> 除了 Visual Studio，还可以使用 Azure 门户提交 Azure Data Lake 查询。 我们将在标题为**使用 U-SQL 处理数据**的部分中提供相关说明，其中将介绍如何使用 Visual Studio 和 Azure 门户完成此操作。 
> 
> 


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>为 Azure Data Lake 准备数据科学环境
若要为此演练准备数据科学环境，请创建以下资源：

* Azure Data Lake Store (ADLS) 
* Azure Data Lake Analytics (ADLA)
* Azure Blob 存储帐户
* Azure 机器学习工作室帐户
* 用于 Visual Studio 的 Azure Data Lake 工具（推荐）

本部分说明了每种资源的创建方法。 如果选择将 Azure 机器学习与 Hive 表配合使用以构建模型，而不使用 Python，那么还需预配 HDInsight (Hadoop) 群集。 在以下相应部分中会对此备选过程进行描述。


> [!NOTE]
> **Azure Data Lake Store** 可创建为默认存储，它可单独创建，也可在创建 **Azure Data Lake Analytics** 时创建。 单独创建每种资源的方法将在下面进行说明，但是不需要单独创建 Data Lake 存储帐户。
>
> 

### <a name="create-an-azure-data-lake-store"></a>创建 Azure Data Lake Store


从 [Azure 门户](http://portal.azure.com)创建 ADLS。 有关详细信息，请参阅[使用 Azure 门户创建包含 Data Lake Store 的 HDInsight 群集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)。 请务必在此处所述的“可选配置”边栏选项卡的“DataSource”边栏选项卡中设置群集 AAD 标识。 

 ![3](./media/machine-learning-data-science-process-data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>创建 Azure Data Lake Analytics 帐户
从 [Azure 门户](http://portal.azure.com)创建 ADLA 帐户。 有关详细信息，请参阅[教程：通过 Azure 门户开始使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)。 

 ![4](./media/machine-learning-data-science-process-data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>创建 Azure Blob 存储帐户
从 [Azure 门户](http://portal.azure.com)创建 Azure Blob 存储帐户。 有关详细信息，请参阅[关于 Azure 存储帐户](../storage/storage-create-storage-account.md)中的“创建存储帐户”部分。

 ![5](./media/machine-learning-data-science-process-data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-account"></a>设置 Azure 机器学习工作室帐户
从 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)页面注册/登录到 Azure 机器学习工作室。 单击“立即开始”按钮，然后选择“免费工作区”或“标准工作区”。 完成此操作后，就能够在 Azure 机器学习工作室中创建试验。  

### <a name="install-azure-data-lake-tools-recommended"></a>安装 Azure Data Lake 工具 [推荐]
从 [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)（用于 Visual Studio 的 Azure Data Lake 工具）中，为你的 Visual Studio 版本安装 Azure Data Lake 工具。

 ![6](./media/machine-learning-data-science-process-data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

成功完成安装后，打开 Visual Studio。 顶部菜单上应显示“Data Lake”选项卡。 登录 Azure 帐户之后，左侧面板中应显示你的 Azure 资源。

 ![7](./media/machine-learning-data-science-process-data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>NYC 出租车行程数据集
此处我们使用的示例为公开发布的数据集 - [NYC 出租车行程数据集](http://www.andresmh.com/nyctaxitrips/)。 NYC 出租车车程数据包含大约 20 GB（未压缩约为  48 GB）的压缩 CSV 文件，记录了超过 1.73 亿个单独车程及每个车程支付的费用。 每个车程记录都包括上车和下车的位置和时间、匿名出租车司机的驾驶证编号和出租车（出租车的唯一 ID）编号。 数据涵盖 2013 年的所有行程，并每月在以下两个数据集中提供：

* 'trip_data' CSV 包含行程的详细信息，例如乘客数、上车和下车地点、行程持续时间和行程距离。 下面是一些示例记录：
  
       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count, trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
* 'trip_fare' CSV 包含每个行程所付费用的详细信息，例如付款类型、费用金额、附加税和税金、小费和通行税，以及支付的总金额等。 下面是一些示例记录：
  
       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

联接 trip\_data 和 trip\_fare 的唯一键由以下三个字段组成：medallion、hack\_licence 和 pickup\_datetime。 可从公共 Azure 存储 blob访问原始 CSV 文件。 此联接的 U-SQL 脚本位于[联接行程和费用表](#join)部分中。

## <a name="process-data-with-u-sql"></a>使用 U-SQL 处理数据
本部分中列举的数据处理任务包括数据的引入、质量检查、浏览和采样。 此外，还将介绍如何联接行程和费用表。 最后一部分介绍如何从 Azure 门户运行 U-SQL 脚本化作业。 下面是每个小节的链接：

* [数据引入：从公共 blob 读入数据](#ingest)
* [数据质量检查](#quality)
* [数据浏览](#explore)
* [联接行程和费用表](#join)
* [数据采样](#sample)
* [运行 U-SQL 作业](#run)

此处所述的 U-SQL 脚本将提供在单独的文件中。 可从 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) 下载完整的 **U-SQL 脚本**。

若要执行 U-SQL，请打开 Visual Studio，单击“文件”-->“新建”-->“项目”，选择“U-SQL 项目”，然后进行命名并将其保存到文件夹。

![8](./media/machine-learning-data-science-process-data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> 可以使用 Azure 门户来执行 U-SQL，而不使用 Visual Studio。 可在门户中导航到 Azure Data Lake Analytics 资源，并直接提交查询，如下图所述。
> 
> 

![9](./media/machine-learning-data-science-process-data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="a-nameingestadata-ingestion-read-in-data-from-public-blob"></a><a name="ingest"></a>数据引入：从公共 blob 读入数据
将 Azure blob 中数据的位置引用为 **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**，且可以使用 **Extractors.Csv()** 进行提取。 将以下脚本中的 wasb 地址 container_name@blob_storage_account_name 替换为自己的容器名和存储帐户名。 由于文件名的格式都相同，因此可使用 **trip\_data_{\*\}.csv** 读入所有的 12 个行程文件。 

    ///Read in Trip data
    @trip0 =
        EXTRACT 
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

因为第一行中存在标题，因此请移除标题，并将列类型更改为合适的类型。 可使用 **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ 将已处理的数据保存到 Azure Data Lake 存储，或使用 **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** 将已处理的数据保存到 Azure Blob 存储帐户。 

    // change data types
    @trip =
        SELECT 
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv(); 

    ////Output data to blob
    OUTPUT @trip   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();  

同样，我们可以读入费用数据集。 右键单击 Azure Data Lake Store，可在“Azure 门户”-->“数据资源管理器”或 Visual Studio 中的“文件资源管理器”中选择查看数据。 

 ![10](./media/machine-learning-data-science-process-data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/machine-learning-data-science-process-data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="a-namequalityadata-quality-checks"></a><a name="quality"></a>数据质量检查
读入行程和费用表之后，可按以下方式完成数据质量检查。 可将生成的 CSV 文件输出到 Azure Blob 存储或 Azure Data Lake Store。 

查找徽章的数量以及唯一的徽章数量：

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;

    @ex_1 =
        SELECT
        pickup_month, 
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv(); 

查找具有超过 100 次行程的徽章：

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv(); 

查找 pickup_longitude 方面的无效记录：

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv(); 

查找某些变量缺少的值：

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;

    @trip_summary6 =
        SELECT 
            vendor_id,
        SUM(missing_medallion) AS medallion_empty, 
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique  
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="a-nameexploreadata-exploration"></a><a name="explore"></a>数据浏览
对数据进行浏览可更好地了解数据。

了解已付小费和未付小费的行程分布：

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;

    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv(); 

了解具有临界值（0、5、10 和 20 美元）的小费金额分布。

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv(); 

了解行程距离的基本统计数据：

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT 
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance 
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

了解行程距离的百分点值：

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv(); 


### <a name="a-namejoinajoin-trip-and-fare-tables"></a><a name="join"></a>联接行程和费用表
可通过徽章、hack_license 和 pickup_time 联接行程和费用表。

    //join trip and fare table

    @model_data_full =
    SELECT t.*, 
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 

    ////output data to ADL
    OUTPUT @model_data_full   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 


对于每个级别的乘客数，请计算记录数、平均小费金额、小费金额的差异以及已付小费行程的百分比。

    // contigency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="a-namesampleadata-sampling"></a><a name="sample"></a>数据采样
首先随机从联接表中选择 0.1% 的数据：

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;

    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;

    OUTPUT @model_data_random_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv(); 

然后通过二进制变量 tip_class 执行分层采样：

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;

    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 


### <a name="a-namerunarun-u-sql-jobs"></a><a name="run"></a>运行 U-SQL 作业
完成 U-SQL 脚本编辑后，可使用 Azure Data Lake Analytics 帐户将其提交到服务器。 依次单击“Data Lake”、“提交作业”，选择你的“Analytics 帐户”，然后选择“并行度”，最后单击“提交”按钮。  

 ![12](./media/machine-learning-data-science-process-data-lake-walkthrough/12-submit-USQL.PNG)

成功编译作业之后，Visual Studio 中将显示作业的状态，以便对作业进行监视。 作业完成运行之后，甚至可以重播作业执行过程，并找到瓶颈步骤以提高作业效率。 也可转到 Azure 门户来检查 U-SQL 作业的状态。

 ![13](./media/machine-learning-data-science-process-data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/machine-learning-data-science-process-data-lake-walkthrough/14-USQL-jobs-portal.PNG)

现在可在 Azure Blob 存储或 Azure 门户中检查输出文件。 下一步中，我们将使用分层采样数据进行建模。

 ![15](./media/machine-learning-data-science-process-data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/machine-learning-data-science-process-data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>在 Azure 机器学习中生成和部署模型
我们将演示两个可用于将数据拉取到 Azure 机器学习中进行构建的选项 

* 在第一个选项中，使用已写入到 Azure Blob 的抽样数据（在上述**数据采样**步骤中），并使用 Python 从 Azure 机器学习构建和部署模型。 
* 在第二个选项中，使用 Hive 查询直接在 Azure Data Lake 中查询数据。 此选项要求创建新的 HDInsight 群集，或使用现有的 HDInsight 群集，其中 Hive 表指向 Azure Data Lake 存储中的 NY 出租车数据。  下面将讨论这两个选项。 

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>选项 1：使用 Python 生成和部署机器学习模型
若要使用 Python 生成和部署机器学习模型，请在本地计算机上或 Azure 机器学习工作室中创建 Jupyter Notebook。 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) 中提供的 Jupyter Notebook 包含完整代码，可用于浏览、可视化数据、功能设计、建模和部署。 本文只介绍建模和部署。 

### <a name="import-python-libraries"></a>导入 Python 库
若要运行示例 Jupyter Notebook 或 Python 脚本文件，需要使用以下 Python 包。 如果要使用 AzureML Notebook 服务，请提前安装这些包。

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>从 blob 读入数据
* 连接字符串   
  
        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* 读入为文本
  
        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  
  ![17](./media/machine-learning-data-science-process-data-lake-walkthrough/17-python_readin_csv.PNG)    
* 添加列名称和分隔列
  
        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* 将某些列更改为数值列
  
        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>生成机器学习模型
在这里，我们将生成二进制分类模型以预测是否为行程支付了小费。 在 Jupyter Notebook 中，可找到另外两种模型：多类分类和回归模型。

* 首先需要创建 scikit-learn 模型中可用的虚拟变量
  
        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* 创建用于建模的数据帧
  
        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
  
        X = data.iloc[:,1:]
        Y = data.tipped
* 定型和测试 60-40 split
  
        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* 定型集中的逻辑回归
  
        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)
  
       ![c1](./media/machine-learning-data-science-process-data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* 对测试数据集进行评分
  
        Y_test_pred = logit_fit.predict(X_test)
* 计算评估指标
  
        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train
  
        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred) 
        print fpr_test, tpr_test, thresholds_test
  
        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)
  
        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)
  
       ![c2](./media/machine-learning-data-science-process-data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>生成 Web 服务 API 并在 Python 中使用
生成完成后，需要使机器学习模型可操作化。 此处我们以二进制逻辑模型为例。 请确保本地计算机中的 scikit-learn 版本为 0.15.1。 如果使用的是 Azure 机器学习工作室，则无需担心这点。

* 在 Azure 机器学习工作室设置中查找你的工作区凭据。 在 Azure 机器学习工作室中，单击“设置” --> “名称” --> “授权令牌”。 
  
    ![c3](./media/machine-learning-data-science-process-data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* 创建 Web 服务
  
        @services.publish(workspaceid, auth_token) 
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* 获取 Web 服务凭据
  
        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key
  
        print url
        print api_key
  
        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* 调用 Web 服务 API。 完成上一步操作后，请等待 5-10 秒。
  
        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  
       ![c4](./media/machine-learning-data-science-process-data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>选项 2：直接在 Azure 机器学习中创建和部署模型
Azure 机器学习工作室可直接从 Azure Data Lake Store 中读取数据，然后将其用于创建和部署模型。 此方法使用指向 Azure Data Lake Store 的 Hive 表。 使用此方法需要预配单独的 Azure HDInsight 群集，并在其中创建 Hive 表。 以下部分介绍了如何执行该操作。 

### <a name="create-an-hdinsight-linux-cluster"></a>创建 HDInsight Linux 群集
从 [Azure 门户](http://portal.azure.com)创建 HDInsight 群集 (Linux)。有关详细信息，请参阅[使用 Azure 门户创建包含 Data Lake Store 的 HDInsight 群集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)中的**创建具有 Azure Data Lake Store 访问权限的 HDInsight 群集**部分。

 ![18](./media/machine-learning-data-science-process-data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>在 HDInsight 中创建 Hive 表
现在，我们将使用上一步中存储在 Azure Data Lake Store 中的数据来创建 Hive 表，该表将在 HDInsight 群集中的 Azure 机器学习工作室中使用。 请转到刚创建的 HDInsight 群集。 单击“设置” --> “属性” --> “群集 AAD 标识” --> “ADLS 访问”，请确保已将你的 Azure Data Lake Store 帐户添加到具有读取、写入和执行权限的列表中。 

 ![19](./media/machine-learning-data-science-process-data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

然后单击“设置”按钮旁的“仪表板”，接着会弹出一个窗口。 在页面的右上角，单击“Hive 视图”，然后将看到“查询编辑器”。

 ![20](./media/machine-learning-data-science-process-data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/machine-learning-data-science-process-data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

粘贴下面的 Hive 脚本以创建表。 数据源的位置以下面的方式保存在 Azure Data Lake Store 引用中：**adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**。

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


查询运行完成后，将看到类似下面的结果：

 ![22](./media/machine-learning-data-science-process-data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>在 Azure 机器学习工作室中生成和部署模型
我们现在可以通过 Azure 机器学习生成和部署预测是否为行程支付小费的模型。 分层采样数据可在二进制分类（是否支付小费）问题中使用。 可通过 Azure 机器学习工作室生成和部署使用多类分类 (tip_class) 和回归 (tip_amount) 的预测模型，但是此处只介绍如何处理使用二进制分类模型的内容。

1. 使用**数据输入和输出**部分的**导入数据**模块，将数据放入 Azure 机器学习。 有关详细信息，请参阅[导入数据模块](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)参考页。
2. 在“属性”面板中，选择“Hive 查询”作为**数据源**。
3. 将以下 Hive 脚本粘贴到“Hive 数据库查询”编辑器
   
        select * from nyc_stratified_sample;
4. 输入 HDInsight 群集的 URI（可在 Azure 门户中找到此 URI）、Hadoop 凭据、输出数据的位置，以及 Azure 存储帐户名称/密钥/容器名称。
   
   ![23](./media/machine-learning-data-science-process-data-lake-walkthrough/23-reader-module-v3.PNG)  

下图中显示的是从 Hive 表读取数据的二进制分类试验的示例。

 ![24](./media/machine-learning-data-science-process-data-lake-walkthrough/24-AML-exp.PNG)

创建此试验后，请单击“设置 Web 服务” --> “预测 Web 服务”

 ![25](./media/machine-learning-data-science-process-data-lake-walkthrough/25-AML-exp-deploy.PNG)

运行自动创建的评分实验，完成后，请单击“部署 Web 服务”

 ![26](./media/machine-learning-data-science-process-data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

很快将显示 Web 服务仪表板：

 ![27](./media/machine-learning-data-science-process-data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>摘要
当完成此演练时，就已在 Azure Data Lake 中创建了用于生成可缩放的端到端解决方案的数据科学环境。 此环境用于分析大型公共数据集，可在从数据采集到模型定型，再到将模型部署为 Web 服务的 Data Science Process 的规范步骤中使用。 U-SQL 可用于数据的处理、浏览和采样。 将 Python 和 Hive 与 Azure 机器学习工作室配合使用，可生成和部署预测模型。

## <a name="whats-next"></a>后续步骤
[Team Data Science Process (TDSP)](http://aka.ms/datascienceprocess) 的学习路径提供了主题的链接，这些主题描述了高级分析过程中的每个步骤。 [Team Data Science Process 演练](data-science-process-walkthroughs.md)页中详细列举了一系列演练，演示如何在各种预测分析方案中使用资源和服务：

* [运行中的 Team Data Science Process：使用 SQL 数据仓库 ](machine-learning-data-science-process-sqldw-walkthrough.md)
* [运行中的 Team Data Science Process：使用 HDInsight Hadoop 群集](machine-learning-data-science-process-hive-walkthrough.md)
* [Team Data Science Process：使用 SQL Server](machine-learning-data-science-process-sql-walkthrough.md)
* [有关使用 Spark on Azure HDInsight 的 Data Science Process 概述](machine-learning-data-science-spark-overview.md)




<!--HONumber=Jan17_HO5-->


