---
title: 使用 Mahout 和 HDInsight (SSH) 生成推荐 - Azure
description: 了解如何使用 Apache Mahout 机器学习库通过 HDInsight (Hadoop) 生成电影推荐。
services: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: 4f29967890d51b894c04b93d8f24f0d6de892cfc
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591153"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight-ssh"></a>通过 HDInsight (SSH) 中基于 Linux 的 Hadoop 使用 Apache Mahout 生成电影推荐

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

了解如何使用 [Apache Mahout](http://mahout.apache.org) 机器学习库通过 Azure HDInsight 生成电影推荐。

Mahout 是适用于 Apache Hadoop 的[机器学习][ml]库。 Mahout 包含用于处理数据的算法，例如筛选、分类和群集。 在本文中，用户使用推荐引擎根据好友看过的电影生成电影推荐。

## <a name="prerequisites"></a>先决条件

* 基于 Linux 的 HDInsight 群集。 有关创建该群集的信息，请参阅[开始在 HDInsight 中使用基于 Linux 的 Hadoop][getstarted]。

> [!IMPORTANT]
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* SSH 客户端。 有关详细信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)文档。

## <a name="mahout-versioning"></a>Mahout 版本控制

若要深入了解 HDInsight 中的 Mahout 版本，请参阅 [HDInsight 版本和 Hadoop 组件](../hdinsight-component-versioning.md)。

## <a name="recommendations"></a>了解建议

由 Mahout 提供的功能之一是推荐引擎。 此引擎接受 `userID`、`itemId` 和 `prefValue` 格式（项的首选项）的数据。 然后，Mahout 将执行共现分析以确定：偏好某个项的用户也偏好其他类似项。 Mahout 然后确定拥有类似项首选项的用户，这些首选项可用于做出推荐。

下面的工作流是使用电影数据的简化示例：

* **共现**：Joe、Alice 和 Bob 都喜欢电影《星球大战》、《帝国反击战》和《绝地归来》。 Mahout 可确定喜欢以上电影之一的用户也喜欢其他两部。

* **共现**：Bob 和 Alice 还喜欢电影《幽灵的威胁》、《克隆人的进攻》和《西斯的复仇》。 Mahout 可确定喜欢前面三部电影的用户也喜欢这三部电影。

* **类似性推荐**：由于 Joe 喜欢前三部电影，Mahout 会查看具有类似首选项的其他人喜欢的电影，但是 Joe 还未观看过（喜欢/评价）。 在这种情况下，Mahout 推荐《幽灵的威胁》、《克隆人的进攻》和《西斯的复仇》。

### <a name="understanding-the-data"></a>了解数据

为方便起见，[GroupLens 研究][movielens]以兼容 Mahout 的格式提供电影的评价数据。 此数据在 `/HdiSamples/HdiSamples/MahoutMovieData` 中群集的默认存储中可用。

有两个文件，即 `moviedb.txt` 和 `user-ratings.txt`。 `user-ratings.txt` 文件在分析期间使用。 `moviedb.txt` 用于在查看结果时提供用户友好的文本信息。

user-ratings.txt 中包含的数据具有 `userID`、`movieID`、`userRating` 和 `timestamp` 结构，指示每个用户对电影评级的情况。 下面是数据的示例：

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>运行分析

与群集建立 SSH 连接后，使用以下命令运行推荐作业：

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]
> 该作业可能需要几分钟才能完成，并可能运行多个 MapReduce 作业。

## <a name="view-the-output"></a>查看输出

1. 作业完成后，使用以下命令查看生成的输出：

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    输出如下所示：

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    第一列是 `userID`。 “[”和“]”中包含的值为 `movieId`:`recommendationScore`。

2. 可使用该输出以及 moviedb.txt 提供有关建议的详细信息。 首先，使用以下命令在本地复制文件：

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    此命令会将输出数据复制到当前目录中名为 **recommendations.txt** 的文件以及电影数据文件。

3. 使用以下命令创建 Python 脚本，该脚本在推荐输出中查找数据的电影名称：

    ```bash
    nano show_recommendations.py
    ```

    编辑器打开后，使用以下文本作为该文件的内容：

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    按 **Ctrl-X**、**Y**，最后按 **Enter** 来保存数据。

4. 运行 Python 脚本。 以下命令假设已处于所有文件都已下载的目录中：

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    此命令将查看为用户 ID 4 生成的建议。

    * **user-ratings.txt** 文件用于检索评价过的电影。

    * **moviedb.txt** 文件用于检索电影的名称。

    * **recommendations.txt** 用于检索此用户的电影建议。

     此命令的输出类似于以下文本：

        西藏七年 (1997)，评分=5.0   夺宝奇兵 3 之圣战骑兵 (1989)，评分=5.0   大白鲨 (1975)，评分=5.0   理智与情感 (1995)，评分=5.0   独立日(ID4) (1996)，评分=5.0   我最好朋友的婚礼 (1997)，评分=5.0   甜心先生 (1996)，评分=5.0   惊声尖叫 2 (1997)，评分=5.0   杀戮时刻 (1996)，评分=5.0

## <a name="delete-temporary-data"></a>删除临时数据

Mahout 作业不删除在处理作业时创建的临时数据。 在示例作业中指定 `--tempDir` 参数，以将临时文件隔离到特定路径中轻松删除。 若要删除临时文件，请使用以下命令：

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]
> 若要再次运行此命令，则必须删除输出目录。 使用以下命令删除此目录：
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>后续步骤

现在，已经学习了如何使用 Mahout，因此可以探索通过其他方式来使用 HDInsight 上的数据：

* [Hive 和 HDInsight 配合使用](hdinsight-use-hive.md)
* [Pig 和 HDInsight 配合使用](hdinsight-use-pig.md)
* [MapReduce 和 HDInsight 配合使用](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
