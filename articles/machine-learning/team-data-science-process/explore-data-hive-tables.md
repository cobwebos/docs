---
title: 使用 Hive 查询在 Hive 表中浏览数据 - Team Data Science Process
description: 使用用于在 HDInsight Hadoop 群集的 Hive 表中浏览数据的示例 Hive 脚本。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3da4f273ed1eeac88e19452c497b99c074d7b148
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981982"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>使用 Hive 查询在 Hive 表中浏览数据

本文提供了用于在 HDInsight Hadoop群集的 Hive 表中浏览数据的示例 Hive 脚本。

此任务是[团队数据科学过程](overview.md)中的一个步骤。

## <a name="prerequisites"></a>必备组件
本文假设用户具备以下条件：

* 已创建 Azure 存储帐户。 如果需要说明，请参阅[创建 Azure 存储帐户](../../storage/common/storage-account-create.md)
* 已预配具有 HDInsight 服务的自定义 Hadoop 群集。 如果需要说明，请参阅[为高级分析自定义 Azure HDInsight Hadoop 群集](customize-hadoop-cluster.md)。
* 数据已上传到 Azure HDInsight Hadoop 群集中的 Hive 表。 如果没有，请按照[创建并将数据上传到 Hive 表](move-hive-tables.md)中的说明，先将数据上传到 Hive 表。
* 已启用群集的远程访问权限。 如果需要说明，请参阅[访问 Hadoop 群集的头节点](customize-hadoop-cluster.md)。
* 如果需要有关如何提交 Hive 查询的说明，请参阅[如何提交 Hive 查询](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>数据浏览的示例 Hive 查询脚本
1. 获取每个分区的观测值计数 `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. 获取每一天的观测值计数 `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. 获取某分类列中的级别  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. 获取两个分类列组合中的级别数 `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. 获取数字列的分布  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. 从连接的两个表中提取记录
   
        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>用于出租车行程数据方案的其他查询脚本
[GitHub 存储库](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)中也提供了特定于 [NYC Taxi Trip Data](https://chriswhong.com/open-data/foil_nyc_taxi/)（纽约出租车行程数据）方案的查询示例。 这些查询已具有指定的数据架构，并准备好提交以运行。

