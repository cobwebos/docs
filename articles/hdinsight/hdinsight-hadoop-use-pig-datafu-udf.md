---
title: "在 HDInsight 上将 DataFu 与 Pig 配合使用 — Azure | Microsoft Docs"
description: "DataFu 是适用于 Hadoop 的库的集合。 了解如何在 HDInsight 群集上通过 Pig 使用 DataFu。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 0016721a-82be-4773-88ad-91e6b2c21cbb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/31/2017
ms.author: larryfr
ms.openlocfilehash: 4de55f5f6c5605e9c6c8dd7ccac902b811d1b062
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="use-datafu-with-pig-on-hdinsight"></a>在 HDInsight 上通过 pig 使用 DataFu

了解如何将 DataFu 与 HDInsight 配合使用。 DataFu 是适用于 Hadoop 上的 Pig 的开放源代码库集合。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。

* Azure HDInsight 群集（基于 Linux 或 Windows）

  > [!IMPORTANT]
  > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* 基本熟悉[在 HDInsight 上使用 Pig](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>在基于 Linux 的 HDInsight 上安装 DataFu

> [!IMPORTANT]
> DataFu 安装在基于 Linux 的群集版本 3.3 和更高版本上，以及基于 Windows 的群集上。 该程序未安装在早于 3.3 的基于 Linux 的群集上。
>
> 如果使用的是基于 Windows 的群集，或基于 Linux 的群集（3.3 以上版本），请跳过本部分。

可从 Maven 存储库下载和安装 DataFu。 使用以下步骤将 DataFu 添加到 HDInsight 群集：

1. 使用 SSH 连接到基于 Linux 的 HDInsight 群集。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 使用以下命令通过 wget 实用程序下载 DataFu jar 文件，或将链接复制并粘贴到浏览器以开始下载。

    ```
    wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar
    ```

3. 然后，将文件上传到 HDInsight 群集的默认存储。 通过将文件放置在默认存储中，使其可供群集中的所有节点使用。

    ```
    hdfs dfs -put datafu-1.2.0.jar /example/jars
    ```

    > [!NOTE]
    > 前一命令将 jar 存储在 `/example/jars` 中，因为此目录已存在于群集存储上。 可使用 HDInsight 群集存储上的任意位置。

## <a name="use-datafu-with-pig"></a>通过 Pig 使用 DataFu

本部分中的步骤假定你熟悉如何在 HDInsight 上使用 Pig。 有关将 Pig 与 HDInsight 配合使用的详细信息，请参阅[将 Pig 与 HDInsight 配合使用](hdinsight-use-pig.md)。

> [!IMPORTANT]
> 如果使用上一节中的步骤手动安装了 DataFu，则必须先注册，再使用。
>
> * 如果群集使用 Azure 存储，请使用 `wasb://` 路径。 例如，`register wasb:///example/jars/datafu-1.2.0.jar`。
>
> * 如果群集使用 Azure Data Lake Store，请使用 `adl://` 路径。 例如，`register adl://home/example/jars/datafu-1.2.0.jar`。

通常要为 DataFu 函数定义别名。 以下示例定义了 `SHA` 的别名：

```piglatin
DEFINE SHA datafu.pig.hash.SHA();
```

然后，可以在 Pig Latin 脚本中使用此别名生成输入数据的哈希值。 例如，以下代码将输入数据中的位置替换为哈希值：

```piglatin
raw = LOAD '/HdiSamples/HdiSamples/SensorSampleData/building/building.csv' USING
    org.apache.pig.piggybank.storage.CSVExcelStorage(',', 'NO_MULTILINE', 'UNIX', 'SKIP_INPUT_HEADER') AS
    (int1:int,
     id1:chararray,
     int2:int,
     id2:chararray,
     location:chararray);
mask = FOREACH raw GENERATE int1, id1, int2, id2, SHA(location);
DUMP mask;
```

这会生成以下输出：

    (1,M1,25,AC1000,aa5ab35a9174c2062b7f7697b33fafe5ce404cf5fecf6bfbbf0dc96ba0d90046)
    (2,M2,27,FN39TG,7a1ca4ef7515f7276bae7230545829c27810c9d9e98ab2c06066bee6270d5153)
    (3,M3,28,JDNS77,07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)
    (4,M4,17,GG1919,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (5,M5,3,ACMAX22,1ed8c7e56c947bebc0cfcf88c4ea0f02c944568f71df893a99970e4f0c78cddc)
    (6,M6,9,AC1000,c96dd81db196cca5f57bd4270bbb9d9e9d1b242d67f9364005ee1dfdc2632523)
    (7,M7,13,FN39TG,3e049d78d958038ac6bd5dcf038075cc73362b4956aaf7308c3a69c8eca76297)
    (8,M8,25,JDNS77,c1ef40ce0484c698eb4bd27fe56c1e7b68d74f9780ed674210d0e5013dae45e9)
    (9,M9,11,GG1919,a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)
    (10,M10,23,ACMAX22,10436829032f361a3de50048de41755140e581467bc1895e6c1a17f423e42d10)
    (11,M11,14,AC1000,348841ef53dbd5a64008a86be432973db790774fb28b59b0d99702a3188b3705)
    (12,M12,26,FN39TG,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (13,M13,25,JDNS77,ed9ad13611d7164839dd3feaf9a7f6a75a4138f389e0d45f8e07fa38da1116a2)
    (14,M14,17,GG1919,80db4ccdca106d37b920206331fcfe3e9e50a9e763d89b54ce3ad5ac8cf30f03)
    (15,M15,19,ACMAX22,3552ac0c032467fbf592cb4d10c5c9267800c01e343ad8ca557256d882ae9327)
    (16,M16,23,AC1000,07c67d76ef92ac9853588e098983fefba4ba5965f8fec95f42ab0d04c27865ba)
    (17,M17,11,FN39TG,557c1599d9a04895d3817d293e0806a4419a14de31958386182798d0d2ed3a56)
    (18,M18,25,JDNS77,dbc74a36d8e7439c45c64d856388506cc9b1218619cef979c3d605115a7a4546)
    (19,M19,14,GG1919,be55ef3f4c4e6c2d9c2afe2a33ac90ad0f50d4de7f9163999877e2a9ca5a54f8)
    (20,M20,19,ACMAX22,ea0b937ea317101ee2c26b03a4843a19ceced8a2b9673c3cf409a726ca2b0fd8)

## <a name="next-steps"></a>后续步骤

有关 DataFu 或 Pig 的详细信息，请参阅以下文档：

* [Apache DataFu Pig 指南](http://datafu.incubator.apache.org/docs/datafu/guide.html)。
* [将 Pig 与 HDInsight 配合使用](hdinsight-use-pig.md)
