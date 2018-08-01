---
title: 在 Azure VM 上使用 SQL Server 构建和部署机器学习模型 | Microsoft 文档
description: 高级分析流程和技术实务
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 6066b083-262c-4453-a712-a5c05acc3df8
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: deguhath
ms.openlocfilehash: dae8549cb951a463688869454e2f4c71103c1363
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226387"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>团队数据科学过程实务：使用 SQL Server
在本教程中，将逐步指导完成使用 SQL Server 和可公开取得的数据集 [NYC 出租车行程](http://www.andresmh.com/nyctaxitrips/)，构建和部署机器学习模型的过程。 该程序遵循标准数据科学工作流，包括：引入和浏览数据，设计功能以促进学习，并构建和部署模型。

## <a name="dataset"></a>NYC 出租车行程数据集介绍
NYC 出租车行程数据是大约 20 GB（未压缩时约为 48 GB）的压缩 CSV 文件，其中包含超过 1.73 亿个单独行程及每个行程支付的费用。 每个行程记录都包括上车和下车的位置和时间、匿名的出租车司机驾驶证编号和徽章（出租车的唯一 ID）编号。 数据涵盖  2013 年的所有行程，并在每个月的以下两个数据集中提供：

1. 'trip_data' CSV 包含行程的详细信息，例如乘客数、上车和下车地点、行程持续时间和行程距离。 下面是一些示例记录：
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. 'trip_fare' CSV 包含每个行程所付费用的详细信息，例如付款类型、费用金额、附加税和税金、小费和通行税，以及支付的总金额等。 下面是一些示例记录：
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

联接 trip\_data 和 trip\_fare 的唯一键由以下字段组成：medallion、hack\_licence 和 pickup\_datetime。

## <a name="mltasks"></a>预测任务示例
我们会根据 *tip\_amount* 编写三个预测问题的公式，即：

1. 二元分类：预测是否已支付某个行程的小费，即大于 $0 的 *tip\_amount* 是正例，等于 $0 的 *tip\_amount* 是反例。
2. 多元分类：预测为行程支付的小费金额范围。 我们将 *tip\_amount* 划分五个分类收纳组或类别：
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. 回归任务：预测为行程支付的小费金额。  

## <a name="setup"></a>设置 Azure 数据科学环境进行高级分析
正如你从[规划环境](plan-your-environment.md)指南中看到的那样，在 Azure 中使用 NYC 出租车行程数据集时，有几个选项可以使用：

* 使用 Azure blob 中的数据，并在 Azure 机器学习中模型化
* 将数据加载到 SQL Server 数据库，然后在 Azure 机器学习中模型化

在本教程中，我们将展示将数据并行批量导入 SQL Server、数据浏览、功能设计以及使用 SQL Server Management Studio 和 IPython Notebook 向下采样。 [示例脚本](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)和 [IPython notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) 在 GitHub 中共享。 使用 Azure blob 中数据的示例 IPython notebook 也可以在同一位置取得。

要设置 Azure 数据科学环境：

1. [创建存储帐户](../../storage/common/storage-create-storage-account.md)
2. [创建 Azure 机器学习工作区](../studio/create-workspace.md)
3. [预配数据科研虚拟机](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)，提供 SQL Server 和 IPython Notebook 服务器。
   
   > [!NOTE]
   > 在安装过程中，示例脚本和 IPython notebook 将下载到数据科学虚拟机。 VM 后续安装脚本完成后，这些示例会在虚拟机文档库中：  
   > 
   > * 示例脚本： `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * 示例 IPython Notebook：`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   其中 `<user_name>` 是 VM 的 Windows 登录名。 我们将示例文件夹称为**示例脚本**和**示例 IPython Notebook**。
   > 
   > 

根据数据集大小、数据源位置和所选的 Azure 目标环境，此应用场景类似于[应用场景 \#5：本地文件中的大型数据集、Azure VM 中的目标 SQL Server](plan-sample-scenarios.md#largelocaltodb)。

## <a name="getdata"></a>从公共源获取数据
要从 [NYC 出租车行程](http://www.andresmh.com/nyctaxitrips/)数据集的公共位置获取该数据集，可以使用[将数据从 Azure Blob 存储移入和移出](move-azure-blob.md)中所述的任意方法，将数据复制到新的虚拟机。

使用 AzCopy 复制数据：

1. 登录到虚拟机 (VM)
2. 在 VM 数据磁盘中创建一个新的目录（注意：不要使用虚拟机附带的临时磁盘作为数据磁盘）。
3. 在“命令提示符”窗口中，运行以下 Azcopy 命令行，将 < path_to_data_folder > 替换成在步骤 (2) 中创建的数据文件夹：
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    AzCopy 完成时，数据文件夹中总共应有 24 个压缩 CSV 文件（其中 12 个文件是 trip\_data，12 个文件是 trip\_fare）。
4. 解压缩下载的文件。 注意未压缩的文件所在的文件夹。 此文件夹将称为 <path\_to\_data\_files\>。

## <a name="dbload"></a>将数据批量导入 SQL Server 数据库
使用*分区表和视图*，即可提升将大量数据加载/传输到 SQL 数据库及后续查询的性能。 在本部分中，我们将按照[使用 SQL 分区表平行批量量导入数据](parallel-load-sql-partitioned-tables.md)中的说明进行操作，创建新数据库并将数据并行加载到分区表。

1. 登录到 VM 后，启动 **SQL Server Management Studio**。
2. 使用 Windows 身份验证进行连接。
   
    ![SSMS 连接][12]
3. 如果尚未更改的 SQL Server 身份验证模式并尚未创建新的 SQL 登录用户，请打开 **Sample Scripts** 文件夹中名为 **change\_auth.sql** 的脚本文件。 更改默认用户名和密码。 单击工具栏中的“**! 执行**”来运此该脚本。
   
    ![执行脚本][13]
4. 验证和/或更改 SQL Server 默认数据库和日志文件夹，以确保新创建的数据库将存储在数据磁盘中。 系统将使用数据和日志磁盘，预配置针对数据存储加载进行优化的 SQL Server VM 映像。 如果 VM 不包含数据磁盘，并且在 VM 安装过程中添加了新的虚拟硬盘，则需按照如下步骤更改默认文件夹：
   
   * 右键单击左侧面板中的 SQL Server 名称，并单击“**属性**”。
     
       ![SQL Server 属性][14]
   * 从左侧的“**选择页**”列表中选择“**数据库设置**”。
   * 验证**数据库默认位置**，并/或将其更改为所选的**数据磁盘**位置。 如果使用默认位置设置创建新的数据库，则这是新数据库所在的位置。
     
       ![SQL 数据库默认设置][15]  
5. 若要创建新数据库和一组文件组来保存已分区的表，请打开示例脚本 **create\_db\_default.sql**。 该脚本会在默认数据位置创建一个名为 **TaxiNYC** 的新数据库和 12 个文件组。 每个文件组将保存一个月内的 trip\_data 和 trip\_fare 数据。 根据需要修改数据库名称。 单击“**! 执行**”，运行此脚本。
6. 接下来，创建两个分区表，一个用于 trip\_data，另一个用于 trip\_fare。 打开示例脚本 **create\_partitioned\_table.sql**，其功能如下：
   
   * 创建分区函数，以按月拆分数据。
   * 创建分区方案，以将每个月的数据映射到不同的文件组。
   * 创建两个映射到分区方案的分区表：**nyctaxi\_trip** 将保存 trip\_data，而 **nyctaxi\_fare** 将保存 trip\_fare 数据。
     
     单击“**! 执行**”，运行该脚本并创建分区表。
7. 在“**示例脚本**”文件夹中，提供了两个示例 PowerShell 脚本，可用于演示将数据并行批量导入到 SQL Server 表的方式。
   
   * **bcp\_parallel\_generic.ps1** 是将数据并行批量导入到表的通用脚本。 修改此脚本以设置此脚本的注释行中指示的输入和目标变量。
   * **bcp\_parallel\_nyctaxi.ps1** 是通用脚本的预配置版本，可用于为 NYC 出租车行程数据同时加载这两个表。  
8. 右键单击 **bcp\_parallel\_nyctaxi.ps1** 脚本名称，然后单击“**编辑**”可在 PowerShell 中将其打开。 查看预设的变量，并根据所选的数据库名称、输入数据文件夹、目标日志文件夹和到示例格式文件 **nyctaxi_trip.xml** 和 **nyctaxi\_fare.xml**（在“**示例脚本**”文件夹中提供）的路径进行修改。
   
    ![批量导入数据][16]
   
    也可以选择身份验证模式，默认值为 Windows 身份验证。 单击工具栏中的绿色箭头运行。 该脚本将启动 24 个并行批量导入操作，每个分区表对应 12 个操作。 也可以通过打开上述步骤中设置的 SQL Server 默认数据文件夹，监测数据导入进度。
9. PowerShell 脚本将报告起始和结束时间。 所有批量导入完成时，将报告结束时间。 检查目标日志文件夹，以验证批量导入成功，即未报告目标日志文件夹存在任何错误。
10. 数据库已就绪，可以进行浏览、功能设计及需要的其他操作。 由于这些表是根据 **pickup\_datetime** 字段进行分区的，因此，分区方案将为在 **WHERE** 子句中包括 **pickup\_datetime** 条件的查询带来好处。
11. 在 **SQL Server Management Studio** 中，探索提供的示例脚本 **sample\_queries.sql**。 要运行任意示例查询，请突出显示查询行，并单击工具栏中的“**! 执行**”。
12. NYC 出租车行程数据加载到两个独立的表中。 若要改进联接操作，强烈建议为表建立索引。 示例脚本 **create\_partitioned\_index.sql** 会在复合联接键 **medallion、hack\_license 和 pickup\_datetime** 上创建分区索引。

## <a name="dbexplore"></a>SQL Server 中的数据浏览和功能设计
在此部分中，我们通过使用之前创建的 SQL Server 数据库，直接在 **SQL Server Management Studio** 中运行 SQL 查询来执行数据浏览和功能设计。 “**示例脚本**”文件夹中提供了名为 **sample\_queries.sql** 的示例脚本。 如果数据库名称不同于默认值：**TaxiNYC**，请修改此脚本以更改数据库名称。

在本练习中，我们将：

* 使用 Windows 身份验证，或 SQL 身份验证及 SQL 登录名和密码，连接到 **SQL Server Management Studio**。
* 在不同的时间范围中探索几个字段的数据分布。
* 调查经度和纬度字段的数据质量。
* 根据 **tip\_amount** 生成二元和多元分类标签。
* 生成特征，并计算/比较行程距离。
* 联接两个表，并提取用于构建模型的随机示例。

准备好进行 Azure 机器学习后，也可以：  

1. 保存最终的 SQL 查询，以提取和采样数据，并直接将查询复制和粘贴到 Azure 机器学习中的“[导入数据][import-data]”模块，或者
2. 保留计划用于在新数据库表中进行建模的抽样和工程数据，并使用 Azure 机器学习的[导入数据][import-data]模块中的新表。

在本部分中，我们将保存最终查询以提取和采样数据。 第二种方法在 [IPython Notebook 中的数据浏览和特征工程](#ipnb)部分进行了演示。

若要在之前使用并行批量导入填充的表中快速验证表的多个行和列，请采用如下方式：

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>浏览：依据徽章的行程分布
此示例标识在给定的时间段内具有 100 多个行程的徽章（出租车数）。 查询将受益于分区表访问，因为它受 **pickup\_datetime** 分区方案的限制。 查询完整数据集还将使用分区表和/或索引扫描。

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>浏览：依据徽章和 hack_license 的行程分布
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>数据质量评估：验证含有不正确经度和/或纬度的记录
此示例将调查是否有任何一个经度和/或纬度字段包含无效的值（弧度应介于 -90 到 90 之间），或者具有（0，0）坐标。

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>浏览：已付小费与未付小费的行程分布
此示例将查找给定时间段（或如果时间段为全年，则是在完整的数据集）内，已付小费与未付小费的行程的数量。 此分布反映二元标签分布，以便以后用于二元分类建模。

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>浏览：小费分类/范围分布
此示例将计算给定的时间段（或如果时间段为全年，则表示完整的数据集）内的小费范围分布。 这是标签类的分布，会在以后用于多类分类建模。

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>浏览：计算并比较行程距离
此示例将上车和下车经度和纬度转换为 SQL 地理位置点，使用 SQL 地理位置点差异计算行程距离，并返回一个随机抽样的结果进行比较。 该示例仅在使用前面介绍的数据质量评估查询，将结果限制为有效坐标。

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>SQL 查询中的功能设计
标签生成和地理转换浏览查询还可通过删除计数部分，用于生成标签/功能。 其他功能设计 SQL 示例在 [IPython Notebook 中的数据浏览和特征工程](#ipnb)部分提供。 使用可在 SQL Server 数据库实例直接运行的 SQL 查询，以更高效的方式在完整数据集或其大型子集上运行功能生成查询。 该查询可能会在 **SQL Server Management Studio**、IPython Notebook 或任何可本地或远程访问数据库的开发工具/环境中执行。

#### <a name="preparing-data-for-model-building"></a>准备建模的数据
下面的查询可联接 **nyctaxi\_trip** 和 **nyctaxi\_fare** 表，生成一个二元分类标签 **tipped**、多类分类标签 **tip\_class**，以及从完整联接的数据集中提取 1% 的随机样本。 可以复制此查询，然后将其直接粘贴到 [Azure 机器学习工作室](https://studio.azureml.net)的[导入数据][import-data]模块中，以便从 Azure 中的 SQL Server 数据库实例进行直接数据引入。 此查询将排除具有不正确（0，0）坐标的记录。

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>IPython Notebook 中的数据浏览和功能设计
在此部分中，我们会在之前创建的 SQL Server 数据库中使用 Python 和 SQL 查询，执行数据浏览和功能生成。 “**Sample IPython Notebooks**”文件夹中提供了名为 **machine-Learning-data-science-process-sql-story.ipynb** 的示例 IPython notebook。 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) 也提供此 Notebook。

以下是处理大数据时的建议顺序：

* 将小型数据示例读入到内存中的数据帧。
* 使用抽样数据执行一些可视化效果和浏览。
* 使用抽样数据进行功能设计实验。
* 对于大型数据浏览、数据操作和功能设计，请使用 Python 直接针对 Azure VM 中的 SQL Server 数据库发出 SQL 查询。
* 决定用于 Azure 机器学习建模的样本大小。

准备好继续进行 Azure 机器学习后，也可以：  

1. 保存最终的 SQL 查询，以提取和采样数据，然后直接将查询复制和粘贴到 Azure 机器学习中的“[导入数据][import-data]”模块。 此方法于[在 Azure 机器学习中建模](#mlmodel)部分进行展示。    
2. 保留计划用于在新数据库表中进行建模的抽样和工程数据，并在[导入数据][import-data]模块中使用新表。

以下是几个数据浏览、数据可视化和功能设计示例。 有关更多示例，请参阅 **Sample IPython Notebooks** 文件夹中的 SQL IPython Notebook 示例。

#### <a name="initialize-database-credentials"></a>初始化数据库凭据
使用以下变量初始化数据库连接设置：

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>创建数据库连接
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>报告表 nyctaxi_trip 中的行数和列数
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* 总行数 = 173179759  
* 总列数 = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>从 SQL Server 数据库读入小型数据样本
    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

读取示例表的时间为 6.492000 秒  
检索到的行数和列数 = (84952, 21)

#### <a name="descriptive-statistics"></a>描述性统计信息
浏览抽样数据现已就绪。 接下来，首先查看 **trip\_distance**（或任何其他）字段的描述性统计信息：

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>可视化效果：盒须图示例
接下来我们查看行程距离的盒须图，以可视化分位数

    df1.boxplot(column='trip_distance',return_type='dict')

![Plot #1][1]

#### <a name="visualization-distribution-plot-example"></a>可视化效果：分布的盒须图示例
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Plot #2][2]

#### <a name="visualization-bar-and-line-plots"></a>可视化效果：条形和折线图
在此示例中，我们可以将行程距离量化为五个分类收纳组，并将分类收纳结果可视化。

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

我们可以在条形图和折线图中绘制上述分类收纳组分布，如下所示

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Plot #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Plot #4][4]

#### <a name="visualization-scatterplot-example"></a>可视化效果：散点图示例
我们会显示在 **trip\_time\_in\_secs** 和 **trip\_distance** 之间的散点图，以查看是否存在任何关联

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Plot #6][6]

同样地，我们可以检查 **rate\_code** 和 **trip\_distance** 之间的关系。

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Plot #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>二次采样 SQL 中的数据
在 [Azure 机器学习工作室](https://studio.azureml.net)中准备建模数据时，可以决定**在“导入数据”模块中直接使用 SQL 查询**，或者将工程和抽样数据保留在新表中，这样就可以通过简单的“SELECT * FROM <your\_new\_table\_name>”在[导入数据][import-data]模块中使用。

在本部分中，我们将创建新表以保存抽样和工程数据。 [SQL Server 中的数据浏览和特征工程](#dbexplore)部分提供了可用于模型构建的直接 SQL 查询示例。

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>创建取样表，并使用 1% 已联接表的填充。 如果存在此表，请首先将其删除。
在本部分中，我们会联接表 **nyctaxi\_trip** 和 **nyctaxi\_fare**，提取 1% 的随机样本，并将抽样数据保存到名为 **nyctaxi\_one\_percent** 的新表中：

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>在 IPython Notebook 中使用 SQL 查询进行数据浏览
在本部分中，我们将使用之前创建的新表中保存的 1% 抽样数据来浏览数据分布。 请注意，如 [SQL Server 中的数据浏览和特征工程](#dbexplore)部分所示，可使用原始表或使用 **TABLESAMPLE** 执行类似浏览，以限制浏览示例，或通过使用 **pickup\_datetime** 分区，将结果限制为给定时间段。

#### <a name="exploration-daily-distribution-of-trips"></a>浏览：每日行程分布
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>浏览：按徽章的行程分布
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>在 IPython Notebook 中使用 SQL 查询进行功能设计
在本部分中，我们将使用 SQL 查询直接生成新标签和特征，以在上一部分创建的 1% 的取样表上操作。

#### <a name="label-generation-generate-class-labels"></a>标签生成：生成类标签
在下面示例中，我们会生成两组用于建模的标签：

1. 二进制类标签 **tipped**（预测是否会支付小费）
2. 多类标签 **tip\_class**（预测小费的收纳组或范围）
   
        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''
   
        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()
   
        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''
   
        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>功能设计：适用于分类列的计数功能
此示例会将分类字段替换为数字字段，方法是使用它在数据中发生的计数来替换每个类别。

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>功能设计：适用于数值列的收纳组功能
此示例会将连续的数值字段转换为预设的类别范围，即将数值字段转换为分类字段。

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>功能设计：从十进制纬度/经度提取位置功能
此示例以十进制表示的纬度和/或经度字段划分为多个不同粒度的区域字段，例如国家/地区、城市、城镇、街区等等。注意，新的地理位置字段不映射到实际位置。 有关规划地理编码位置的信息，请参 [Bing 地图 REST 服务](https://msdn.microsoft.com/library/ff701710.aspx)。

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>验证功能化表的最终形式
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

我们现已准备好在 [Azure 机器学习](https://studio.azureml.net) 中进行建模和模型部署。 数据已可用于之前识别的任意预测问题，即：

1. 二元分类：预测某个行程是否会支付小费。
2. 多类分类：根据以前定义的类，预测小费支付范围。
3. 回归任务：预测为行程支付的小费金额。  

## <a name="mlmodel"></a>在 Azure 机器学习中建模
若要开始建模练习，请登录到 Azure 机器学习工作区。 如果尚未创建机器学习工作区，请参阅[创建 Azure 机器学习工作区](../studio/create-workspace.md)。

1. 要开始使用 Azure 机器学习，请参阅[什么是 Azure 机器学习工作室？](../studio/what-is-ml-studio.md)
2. 登录 [Azure 机器学习工作室](https://studio.azureml.net)。
3. 工作室主页上提供丰富的信息、视频、教程、指向模块参考链接及其他资源。 有关 Azure 机器学习的详细信息，请参阅 [Azure 机器学习文档中心](https://azure.microsoft.com/documentation/services/machine-learning/)。

典型的训练实验由以下内容组成：

1. “**新建 +**”实验。
2. 将数据放入 Azure 机器学习。
3. 根据需要预处理、转换和操作数据。
4. 根据需要生成功能。
5. 将数据拆分为训练/验证/测试数据集（或每个类具有单独的数据集）。
6. 根据要解决的学习问题，选择一个或多个机器学习算法。 例如，二元分类、多类分类、回归。
7. 使用定型数据集训练一个或多个模型。
8. 使用定型模型对验证数据集评分。
9. 评估模型来计算针对学习问题的相关指标。
10. 微调模型，并选择最佳模型进行部署。

在此练习中，我们已经探讨和设计了 SQL Server 中的数据，并确定了要引入 Azure 机器学习中的样本大小。 若要构建一个或多个我们确定的预测模型：

1. 使用**数据输入和输出**部分的[导入数据][import-data]模块，将数据放入 Azure 机器学习。 有关详细信息，请参阅[导入数据][import-data]模块参考页。
   
    ![Azure 机器学习导入数据][17]
2. 在“**属性**”面板中，选择“**Azure SQL 数据库**”作为**数据源**。
3. 在“**数据库服务器名称**”字段中输入数据库 DNS 名称。 格式：`tcp:<your_virtual_machine_DNS_name>,1433`
4. 在相应字段中输入**数据库名称**。
5. 在\*\*服务器用户帐户名中输入 SQL 用户名，在“服务器用户帐户密码”中输入密码。
7. 在**数据库查询**编辑文本区域，粘贴提取必要数据库字段（包括任何计算的字段，例如标签）的查询，并向下采样数据至所需样本大小。

二元分类实验直接从 SQL Server 数据库读取数据，示例如下图所示。 可以针对多类分类和回归问题构建类似实验。

![Azure 机器学习训练][10]

> [!IMPORTANT]
> 在上一部分中提供的建模数据提取和采样查询示例中，**这三个建模练习的所有标签都包括在此查询中**。 每个建模练习的一个重要（必需）步骤是**排除**其他两个问题不需要的标签，以及任何其他的**目标泄漏**。 例如，使用二元分类时，使用标签 **tipped**并排除字段 **tip\_class**、**tip\_amount** 和 **total\_amount**。 后者是目标泄漏，因为它们指示支付的小费。
> 
> 为了排除不需要的列和/或目标泄漏，可以使用[选择数据集中的列][select-columns]模块或[编辑元数据][edit-metadata]。 有关详细信息，请参阅[选择数据集中的列][select-columns]和[编辑元数据][edit-metadata]参考页。
> 
> 

## <a name="mldeploy"></a>在 Azure 机器学习中部署模型
模型已就绪时，即可轻松地从实验直接将其部署为 Web 服务。 有关部署 Azure 机器学习 Web 服务的详细信息，请参阅[部署 Azure 机器学习 Web 服务](../studio/publish-a-machine-learning-web-service.md)。

要部署新 Web 服务，需要：

1. 创建评分实验。
2. 部署 Web 服务。

若要从**已完成**的训练实验中创建评分实验，请单击下方操作栏中的“**创建评分实验**”。

![Azure 评分][18]

Azure 机器学习将尝试根据训练实验的组件创建评分实验。 特别是，它将：

1. 保存训练的模型，并删除模型训练模块。
2. 标识逻辑**输入端口**，以表示预期输入数据架构。
3. 标识逻辑**输出端口**，以表示预期 Web 服务输出架构。

创建评分实验后，请检查并根据需要进行调整。 典型调整是将输入数据集和/或查询替换为排除标签字段的数据集和/或查询，因为这些数据集和/或查询在调用该服务时不可用。 如果将输入数据集和/或查询大小减少到几个记录，刚好能够表示输入架构，这也是一个非常好的做法。 对于输出端口，通常会使用[选择数据集中的列][select-columns]模块在输出中排除所有输入字段，仅包括“评分标签”和“评分概率”。

评分实验示例如下图所示。 准备部署时，请单击下方操作栏中的“**发布 WEB 服务**”按钮。

![Azure 机器学习发布][11]

概括来说，在本分步介绍的教程中，已创建了 Azure 数据科学环境，从数据采集到 Azure 机器学习 Web 服务的模型训练和部署等多项工作中，都会使用大型公共数据集。

### <a name="license-information"></a>许可证信息
此示例演练和及其附带脚本和 IPython notebook 是在 MIT 许可证下由 Microsoft 共享。 如需更多详细信息，请查看 GitHub 上示例代码目录中的 LICENSE.txt 文件。

### <a name="references"></a>参考
•    [Andrés Monroy NYC 出租车行程下载页](http://www.andresmh.com/nyctaxitrips/)  
•    [由 Chris Whong 提供的 FOILing NYC 出租车行程数据](http://chriswhong.com/open-data/foil_nyc_taxi/)   
•   [NYC 出租车和礼车委员会研究和统计信息](http://www.nyc.gov/html/tlc/html/technology/aggregated_data.shtml)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
