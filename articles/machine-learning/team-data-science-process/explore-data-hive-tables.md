---
title: 使用 Hive 查询在 Hive 表中浏览数据 | Microsoft Docs
description: 使用 Hive 查询在 Hive 表中浏览数据。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: c50c6de28c60bbe6a6c0d510e62f35cd037d6e95
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443306"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>使用 Hive 查询在 Hive 表中浏览数据

本文提供了用于在 HDInsight Hadoop群集的 Hive 表中浏览数据的示例 Hive 脚本。

此任务是[团队数据科学过程](overview.md)中的一个步骤。

## <a name="prerequisites"></a>先决条件
本文假设用户具备以下条件：

* 已创建 Azure 存储帐户。 如果需要说明，请参阅[创建 Azure 存储帐户](../../storage/common/storage-quickstart-create-account.md)
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
[GitHub 存储库](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)中也提供了特定于 [NYC Taxi Trip Data](http://chriswhong.com/open-data/foil_nyc_taxi/)（纽约出租车行程数据）方案的查询示例。 这些查询已具有指定的数据架构，并准备好提交以运行。

