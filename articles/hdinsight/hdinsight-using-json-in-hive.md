---
title: "使用 HDInsight 中的 Hive 分析和处理 JSON 文档 | Microsoft Docs"
description: "了解如何使用 JSON 文档，以及如何使用 HDInsight 中的 Hive 来分析这些文档。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: ee7d40d2ff0ae1ac10b54f4c1f1dd704a70eb70c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/06/2017

---
# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>使用 HDInsight 中的 Hive 处理和分析 JSON 文档

了解如何使用 HDInsight 中的 Hive 处理和分析 JSON 文件。 本教程使用以下 JSON 文档：

    {
        "StudentId": "trgfg-5454-fdfdg-4346",
        "Grade": 7,
        "StudentDetails": [
            {
                "FirstName": "Peggy",
                "LastName": "Williams",
                "YearJoined": 2012
            }
        ],
        "StudentClassCollection": [
            {
                "ClassId": "89084343",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "High",
                "Score": 93,
                "PerformedActivity": false
            },
            {
                "ClassId": "78547522",
                "ClassParticipation": "NotSatisfied",
                "ClassParticipationRank": "None",
                "Score": 74,
                "PerformedActivity": false
            },
            {
                "ClassId": "78675563",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "Low",
                "Score": 83,
                "PerformedActivity": true
            }
        ]
    }

可以在 wasb://processjson@hditutorialdata.blob.core.windows.net/ 上找到该文件。 有关将 Azure Blob 存储与 HDInsight 配合使用的详细信息，请参阅[将 HDFS 兼容的 Azure Blob 存储与 HDInsight 中的 Hadoop 配合使用](hdinsight-hadoop-use-blob-storage.md)。 可以将该文件复制到群集的默认容器。

在本教程中，将使用 Hive 控制台。  有关打开 Hive 控制台的说明，请参阅[通过远程桌面将 Hive 与 Hadoop on HDInsight 配合使用](hdinsight-hadoop-use-hive-remote-desktop.md)。

## <a name="flatten-json-documents"></a>平展 JSON 文档
下一部分中所列的方法需要 JSON 文档在单一行中。 因此，必须将 JSON 文档平展成字符串。 如果 JSON 文档已平展，则可以跳过此步骤，直接转到有关分析 JSON 数据的下一部分。

    DROP TABLE IF EXISTS StudentsRaw;
    CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

    DROP TABLE IF EXISTS StudentsOneLine;
    CREATE EXTERNAL TABLE StudentsOneLine
    (
      json_body string
    )
    STORED AS TEXTFILE LOCATION '/json/students';

    INSERT OVERWRITE TABLE StudentsOneLine
    SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
          FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
          GROUP BY INPUT__FILE__NAME;

    SELECT * FROM StudentsOneLine

原始 JSON 文件位于 **wasb://processjson@hditutorialdata.blob.core.windows.net/**。 *StudentsRaw* Hive 表指向原始的未平展 JSON 文档。

*StudentsOneLine* Hive 表将数据存储在 HDInsight 默认文件系统中的 */json/students/* 路径下。

INSERT 语句使用平展的 JSON 数据填充 StudentOneLine 表。

SELECT 语句应只返回 1 行。

下面是 SELECT 语句的输出：

![平展 JSON 文档。][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>在 Hive 中分析 JSON 文档
Hive 提供了三种不同的机制用于对 JSON 文档运行查询：

* 使用 GET\_JSON\_OBJECT UDF（用户定义的函数）
* 使用 JSON_TUPLE UDF
* 使用自定义 SerDe
* 使用 Python 或其他语言编写自己的 UDF。 请参阅[本文][hdinsight-python]，了解如何使用 Hive 运行自己的 Python 代码。

### <a name="use-the-getjsonobject-udf"></a>使用 GET\_JSON_OBJECT UDF
Hive 提供了名为 [get json object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) 的内置 UDF，它可以在运行时执行 JSON 查询。 此方法采用两个参数 - 表名称和方法名称，具有平展的 JSON 文档和需要进行分析的 JSON 字段。 让我们来探讨一个示例，以了解此 UDF 的工作原理。

获取每个学生的名字和姓氏

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

这是在控制台窗口中运行此查询时的输出。

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

get-json_object UDF 有一些限制。

* 由于查询中的每个字段都需要重新分析查询，因此会影响性能。
* GET\_JSON_OBJECT() 返回数组的字符串表示形式。 要将此数组转换为 Hive 数组，必须使用正则表达式来替换方括号“[”和“]”，然后调用拆分来获取数组。

正因如此，Hive wiki 建议使用 json_tuple。  

### <a name="use-the-jsontuple-udf"></a>使用 JSON_TUPLE UDF
Hive 提供的另一个 UDF 称为 [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)，其性能比 [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) 要高。 此方法采用一组键和一个 JSON 字符串，并使用一个函数返回值的元组。 以下查询将从 JSON 文档返回学生 ID 和年级：

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

此脚本在 Hive 控制台中的输出：

![json_tuple UDF][image-hdi-hivejson-jsontuple]

JSON\_TUPLE 在 Hive 中使用了[横向视图](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView)语法，使 json\_tuple 能够通过将 UDT 函数应用于原始表的每一行来创建虚拟表。  由于重复使用横向视图，复杂的 JSON 会变得过于庞大。 此外，JSON_TUPLE 无法处理嵌套的 JSONs。

### <a name="use-custom-serde"></a>使用自定义 SerDe
SerDe 是用于分析嵌套 JSON 文档的最佳选择，它使你能够定义 JSON 架构，并使用该架构来分析文档。 有关说明，请参阅 [How to use a Custom JSON Serde with Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)（如何将自定义 JSON Serde 与 Microsoft Azure HDInsight 配合使用）。

## <a name="summary"></a>摘要
总之，在 Hive 中选择的 JSON 运算符类型取决于方案。 如果有一个简单的 JSON 文档，并只有一个要查找的字段，可以选择使用 Hive UDF get\_json\_object。 如果有多个键用于查找，则可以使用 json_tuple。 如果有嵌套的文档，则应使用 JSON SerDe。

## <a name="next-steps"></a>后续步骤

如需其他相关文章，请参阅

* [将 Hive 和 HiveQL 与 HDInsight 中的 Hadoop 配合使用以分析示例 Apache log4j 文件](hdinsight-use-hive.md)
* [使用 HDInsight 中的 Hive 分析航班延误数据](hdinsight-analyze-flight-delay-data.md)
* [使用 HDInsight 中的 Hive 分析 Twitter 数据](hdinsight-analyze-twitter-data.md)
* [使用 Azure Cosmos DB 和 HDInsight 运行 Hadoop 作业](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

