---
title: 对 Azure HDInsight Hive 表中的数据采样 - Team Data Science Process
description: 使用 Hive 查询向下采样存储在 Azure HDInsight Hive 表中的数据，以将数据减至对于分析更易于管理的大小。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: df85edc3de00e2b0342bc3102fe9e85564a9835b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719987"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>对 Azure HDInsight Hive 表中的数据进行采样
本文介绍如何使用 Hive 查询向下采样存储在 Azure HDInsight Hive 表中的数据，以将其减至对于分析更易于管理的大小。 它包含三个一般使用的采样方法：

* 统一随机采样
* 按组随机采样
* 分层采样

**为什么对数据进行采样？**
如果计划要分析的数据集很大，通常最好是对数据进行向下采样，以将数据减至较小但具备代表性且更易于管理的规模。 向下采样有利于数据理解、探索和功能设计。 它在团队数据科学过程中的作用是启用数据处理功能和机器学习模型的快速原型设计。

此采样任务是[团队数据科学流程 (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) 中的一个步骤。

## <a name="how-to-submit-hive-queries"></a>如何提交 Hive 查询
可从 Hadoop 群集头节点上的 Hadoop 命令行控制台中提交 Hive 查询。  登录到 Hadoop 群集的头节点，打开 Hadoop 命令行控制台，并从那里提交 Hive 查询。 有关在 Hadoop 命令行控制台中提交 Hive 查询的说明，请参阅[如何提交 Hive 查询](move-hive-tables.md#submit)。

## <a name="uniform"></a>统一随机采样
统一随机采样意味着数据集中的每一行都具有相同的采样机会。 通过将额外字段 rand() 添加到内部“select”查询中以及以该随机字段为条件的外部“select”查询中的数据集，可实现它。

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

## <a name="group"></a>按组随机采样
采样分类数据时，建议包括或排除分类变量的某些值的所有实例。 这一类采样称为“按组采样”。 例如，如果有一个分类变量“State”，其包含值例如 NY、MA、CA、NJ 和 PA，无论是否进行采样，都希望相同州的记录在一起。

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

## <a name="stratified"></a>分层采样
如果获取的样本具有的分类值与父填充中该分类值所呈现的比率相同，则针对分类变量对随机采样进行分层。 同样使用与上述示例，假设数据具有按州分组的以下观察值：NJ 具有 100 个观察值，NY 具有 60 个观察值，WA 具有 300 个观察值。 如果指定分层采样率为 0.5，那么获取的 NJ、NY 和 WA 的样本应该分别具有大约 50、30 和 150 个观察值。

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

