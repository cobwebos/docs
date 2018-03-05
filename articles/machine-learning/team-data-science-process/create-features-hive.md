---
title: "使用 Hive 查询在 Hadoop 群集中为数据创建功能 | Microsoft Docs"
description: "在存储在 Azure HDInsight Hadoop 群集中的数据中生成功能的 Hive 查询的示例。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e8a94c71-979b-4707-b8fd-85b47d309a30
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: d72e10332263fac0b0ca0f937d394d2832d88781
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>使用 Hive 查询创建用于 Hadoop 群集中数据的功能
本文档将演示如何使用 Hive 查询创建用于 Hadoop 群集中数据的功能。 这些 Hive 查询使用嵌入式 Hive 用户的定义函数 (UDF) 以及为其提供的脚本。

创建功能所需要的操作可以是内存密集型。 在这种情况下，Hive 查询的性能将变得更加重要，可通过优化某些参数来对其进行改善。 将在最后部分中讨论这些参数的优化。

显示的查询示例特定于 [NYC Taxi Trip Data](http://chriswhong.com/open-data/foil_nyc_taxi/)（纽约出租车行程数据）方案，[GitHub 存储库](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)中也提供了这些方案。 这些查询已具有指定的数据架构，并准备好提交以运行。 最后部分也会讨论用户可对其进行优化以改善 Hive 查询性能的参数。

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

该**菜单**链接到介绍如何在各种环境中为数据创建特征的主题。 此任务是[团队数据科学过程 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中的一个步骤。

## <a name="prerequisites"></a>先决条件
本文假设用户具备以下条件：

* 已创建 Azure 存储帐户。 如果需要说明，请参阅[创建 Azure 存储帐户](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* 已预配具有 HDInsight 服务的自定义 Hadoop 群集。  如果需要说明，请参阅[为高级分析自定义 Azure HDInsight Hadoop 群集](customize-hadoop-cluster.md)。
* 数据已上传到 Azure HDInsight Hadoop 群集中的 Hive 表。 如果没有，请按照[创建并将数据上传到 Hive 表](move-hive-tables.md)，先将数据上传到 Hive 表。
* 已启用群集的远程访问权限。 如果需要说明，请参阅[访问 Hadoop 群集的头节点](customize-hadoop-cluster.md)。

## <a name="hive-featureengineering"></a>功能生成
在本部分中，介绍使用 Hive 查询可用其生成功能的方法的几个示例。 如果已生成其他功能，则可将其作为列添加到现有表，或创建具有这些其他功能和主密钥的新表，新表之后可联接到原始表。 以下是显示的示例：

1. [基于频率的功能生成](#hive-frequencyfeature)
2. [二元分类中分类变量的风险](#hive-riskfeature)
3. [从日期时间字段中提取功能](#hive-datefeatures)
4. [从文本字段中提取功能](#hive-textfeatures)
5. [计算 GPS 坐标之间的距离](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>基于频率的功能生成
通常用于计算分类变量级别的频率，或多个分类变量中某些组合级别的频率。 用户可使用以下脚本计算这些频率：

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>二元分类中分类变量的风险
在二元分类中，如果使用的模型只采用数字功能，则需要将非数字分类变量转换为数字功能。 通过使用数字风险替代非数字级别来完成此转换。 此部分将演示计算分类变量的风险值（对数几率）的某些泛型 Hive 查询。

        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

在此示例中，将变量 `smooth_param1` 和 `smooth_param2` 设置为平滑处理数据中计算的风险值。 风险值必须介于 -Inf 和 Inf 之间。 风险值 > 0，指示目标值等于 1 的概率大于 0.5。

计算风险表之后，用户可通过将其联接到风险表来将风险值分配到表。 前面部分中提供了 Hive 联接查询。

### <a name="hive-datefeatures"></a>从日期时间字段中提取功能
Hive 附带一组用于处理日期时间字段的 UDF。 在 Hive 中，默认日期时间格式为“yyyy-MM-dd 00:00:00”（例如，“1970-01-01 12:21:32”）。 本部分将演示从日期时间字段中提取某月、某月中某天的示例，以及将非默认格式的日期时间字符串转换为默认格式的日期时间字符串。

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

此 Hive 查询假定 *<datetime field>* 使用的是默认日期时间格式。

如果日期时间字段并未使用默认格式，则需要先将日期时间字段转换为 Unix 时间戳，然后将 Unix 时间戳转换为默认格式的日期时间字符串。 如果日期时间使用默认格式，那么用户可以应用嵌入的日期时间 UDF 以提取功能。

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

在此查询中，如果 *<datetime field>* 的模式类似于 *03/26/2015 12:04:39*，则 *<pattern of the datetime field>* 应为 `'MM/dd/yyyy HH:mm:ss'`。 若要对其进行测试，用户可以运行

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

预配群集时，此查询中的 *hivesampletable* 会在所有 Azure HDInsight Hadoop 群集上默认预安装。

### <a name="hive-textfeatures"></a>从文本字段中提取功能
如果 Hive 表具有包含以空格分隔的单词的字符串的文本字段，那么以下查询将提取字符串的长度和字符串中的单词数。

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>计算 GPS 坐标集之间的距离
本部分中给出的查询可直接应用于纽约出租车行程数据。 此查询旨在演示如何应用 Hive 中嵌入的数学函数来生成功能。

此查询中使用的字段为提取和减少位置的 GPS 坐标，名为*提取\_经度*、*提取\_纬度*、*减少\_经度*、*减少\_纬度*。 计算提取和减少坐标之间直接距离的查询为：

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

计算两个 GPS 坐标之间距离的数学等式可在 <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a>（可移动类型脚本）站点上找到，该站点由 Peter Lapisu 创作。 在此 Javascript 中，函数 `toRad()` 就是 lat_or_lonpi/180*，它将度数转换为弧度。 此处，*lat_or_lon* 是纬度或经度。 由于 Hive 不提供函数 `atan2`，但提供函数 `atan`，所以 `atan2` 函数通过 <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a> 中提供的定义由上述的 Hive 查询中的函数 `atan` 来实现。

![创建工作区](./media/create-features-hive/atan2new.png)

嵌入 UDF 的 Hive 的完整列表可在 <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a> 上的**内置函数**部分中找到）。  

## <a name="tuning"></a>高级主题：优化 Hive 参数以加快查询速度
Hive 群集的默认参数设置可能不适合 Hive 查询以及正在处理查询的数据。 在本部分中，讨论用户可对其进行优化以改进 Hive 查询性能的某些参数。 用户需要在查询处理数据之前，先添加优化查询参数。

1. Java 堆空间：对于涉及联接大数据集或处理长记录的查询，一个常见错误为“堆空间不足”。 可以通过将参数 mapreduce.map.java.opts 和 mapreduce.task.io.sort.mb 设置为所需的值来避免此错误。 下面是一个示例：
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    此参数会将 4GB 内存分配到 Java 堆空间，并通过为其分配更多内存来提高排序效率。 如果有任何与堆空间相关的作业失败错误，最好进行这些分配。

1. DFS 块大小：此参数设置文件系统存储的最小数据单位。 例如，如果 DFS 块的大小为 128 MB，那么任何小于等于 128 MB 的数据都存储在单个块中。 大于 128 MB 的数据会被分配到额外的块。 
2. 选择较小的块大小会导致 Hadoop 中开销变大，因为名称节点必须处理更多查找属于文件的相关块的请求。 处理千兆字节（或更大）数据的推荐设置为：

        set dfs.block.size=128m;

2. 优化 Hive 中的联接操作：映射/归约框架中的联接操作通常发生在归约阶段，有时可通过规划映射阶段（也称为“映射联接”）中的联接来实现巨大的提升。 若要指导 Hive 尽可能执行此操作，请设置：
   
       set hive.auto.convert.join=true;

3. 将映射器数指定为 Hive：虽然 Hadoop 允许用户设置归约器数量，但是映射器数量通常不由用户设置。 允许对此数量在一定程度进行控制的技巧是，选择 Hadoop 变量 mapred.min.split.size 和 mapred.max.split.size，因为每个映射任务的大小由以下内容决定：
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    通常而言：
    
    - mapred.min.split.size 的默认值为 0，
    - mapred.max.split.size 的默认值为 Long.MAX， 
    - dfs.block.size 的默认值为 64 MB。

    如我们所见，根据数据大小，通过对其进行“设置”允许对使用的映射器数量进行优化，可优化这些参数。

4. 以下是用于优化 Hive 性能的一些其他高级选项。 这些选项可用于设置分配到映射和归纳任务的内存，并可用于调节性能。 请记住，mapreduce.reduce.memory.mb 不能大于 Hadoop 群集中每个辅助角色节点的物理内存大小。
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

