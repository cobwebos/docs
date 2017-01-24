---
title: "对 Azure HDInsight Hive 表中的数据采样 | Microsoft Docs"
description: "Azure HDInsight  (Hadopop) Hive 表中的向下采样数据"
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: f31e8d01-0fd4-4a10-b1a7-35de3c327521
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: hangzh;bradsev
translationtype: Human Translation
ms.sourcegitcommit: a6bc79b2cb5b73109cddd6cf57caeba754b52e2e
ms.openlocfilehash: 0f3264abf6216270aa9cdd62ce3acf8640e2375a


---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>对 Azure HDInsight Hive 表中的数据进行采样
本文将介绍使用 Hive 查询向下采样 Azure HDInsight Hive 表中存储的数据。 将介绍三种常用的采样方法：

* 统一随机采样
* 按组随机采样
* 分层采样

以下**菜单**所链接到的主题将描述如何从不同的存储环境采样数据。

[!INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

**为什么对数据进行采样？**
如果计划要分析的数据集很大，通常最好是对数据进行向下采样，以将数据减至较小但具备代表性且更易于管理的规模。 这有利于数据了解、探索和功能设计。 它在团队数据科学过程中的作用是启用数据处理功能和机器学习模型的快速原型设计。

此采样任务是[团队数据科学流程 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中的一个步骤。

## <a name="how-to-submit-hive-queries"></a>如何提交 Hive 查询
可从 Hadoop 群集头节点上的 Hadoop 命令行控制台中提交 Hive 查询。 若要执行此操作，可登录到 Hadoop 群集的头节点，打开 Hadoop 命令行控制台，然后从那里提交 Hive 查询。 有关在 Hadoop 命令行控制台中提交 Hive 查询的说明，请参阅[如何提交 Hive 查询](machine-learning-data-science-move-hive-tables.md#submit)。

## <a name="a-nameuniforma-uniform-random-sampling"></a><a name="uniform"></a>统一随机采样
统一随机采样意味着数据集中的每一行都具有相同的采样机会。 通过将额外字段 rand() 添加到内部“选择”查询中以及处于该随机字段上的外部“选择”查询中的数据集，可实现它。

下面是一个示例查询：

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

此处，`<sample rate, 0-1>` 指定用户要采样的记录部分。

## <a name="a-namegroupa-random-sampling-by-groups"></a><a name="group"></a>按组随机采样
采样分类数据时，可能想要包括或排除分类变量的某些特定值的所有实例。 这是“按组采样”的含义。
例如，如果有一个分类变量“State”，其包含值 NY、MA、CA、NJ、PA 等，无论是否进行采样，都希望相同州的记录始终在一起。

下面是按组采样的示例查询：

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="a-namestratifiedastratified-sampling"></a><a name="stratified"></a>分层采样
如果获取的样本具有的该分类值与从中获取样本的父填充中该分类值所占比率相同，则针对分类变量对随机采样进行分层。 使用与上述相同的示例，假设数据具有按州分组的子填充，例如，NJ 具有 100 个观察值，NY 具有 60 个观察值，WA 具有 300 个观察值。 如果指定分层采样率为 0.5，那么获取的 NJ、NY 和 WA 的样本应该分别具有大约 50、30 和 150 个观察值。

下面是一个示例查询：

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


有关 Hive 中可用的更多高级采样方法的信息，请参阅 [LanguageManual Sampling](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling)（LanguageManual 采样）。




<!--HONumber=Dec16_HO3-->


