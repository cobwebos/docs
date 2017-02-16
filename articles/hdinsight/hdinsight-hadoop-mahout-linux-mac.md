---
title: "使用 Mahout 和 HDInsight (SSH) 生成推荐 | Microsoft Docs"
description: "了解如何使用 Apache Mahout 机器学习库通过 HDInsight (Hadoop) 生成电影推荐。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c78ec37c-9a8c-4bb6-9e38-0bdb9e89fbd7
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d5b68d26d708a28edee13ff3d9a57588ce83e12
ms.openlocfilehash: be8146ae3dd34f4c8d5e02b06fd1b1f8d5d63dc1


---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight-ssh"></a>通过 HDInsight (SSH) 中基于 Linux 的 Hadoop 使用 Apache Mahout 生成电影推荐

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

了解如何使用 [Apache Mahout](http://mahout.apache.org) 机器学习库通过 Azure HDInsight 生成电影推荐。

Mahout 是适用于 Apache Hadoop 的[机器学习][ml]库。 Mahout 包含用于处理数据的算法，例如筛选、分类和群集。 本文介绍了如何使用推荐引擎根据用户的好友看过的电影为用户生成电影推荐。

## <a name="prerequisites"></a>先决条件

* 基于 Linux 的 HDInsight 群集。 有关创建该群集的信息，请参阅[开始在 HDInsight 中使用基于 Linux 的 Hadoop][getstarted]。

> [!IMPORTANT]
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上即将弃用](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。

## <a name="mahout-versioning"></a>Mahout 版本控制

若要深入了解 HDInsight 中的 Mahout 版本，请参阅 [HDInsight 版本和 Hadoop 组件](hdinsight-component-versioning.md)。

## <a name="a-namerecommendationsaunderstanding-recommendations"></a><a name="recommendations"></a>了解建议

由 Mahout 提供的功能之一是推荐引擎。 此引擎接受 `userID`、`itemId` 和 `prefValue` 格式（项的用户首选项）的数据。 然后，Mahout 将执行共现分析，以确定：偏好某个项的用户也偏好其他类似项。 Mahout 然后确定拥有类似项首选项的用户，这些首选项可用于做出推荐。

下面的工作流是使用电影数据的极其简单的示例：

* **共现**：Joe、Alice 和 Bob 都喜欢电影星球大战、帝国反击战和绝地大反击。 Mahout 可确定喜欢以上电影之一的用户也喜欢其他两部。

* **共现**：Bob 和 Alice 还喜欢电影幽灵的威胁、克隆人的进攻和西斯的复仇。 Mahout 可确定喜欢前面三部电影的用户也喜欢这三部电影。

* **类似性推荐**：由于 Joe 喜欢前三部电影，Mahout 会查看具有类似首选项的其他人喜欢的电影，但是 Joe 还未观看过（喜欢/评价）。 在这种情况下，Mahout 推荐幽灵的威胁、克隆人的进攻和西斯的复仇。

### <a name="understanding-the-data"></a>了解数据

为方便起见，[GroupLens 研究][movielens]以兼容 Mahout 的格式提供电影的评价数据。 此数据在 `/HdiSamples/HdiSamples/MahoutMovieData` 中你的群集的默认存储中可用。

存在两个文件，`moviedb.txt`（有关影片的信息）和 `user-ratings.txt`。 user-ratings.txt 文件在分析过程中使用，而 moviedb.txt 会在显示分析结果时，提供用户友好的文本信息。

user-ratings.txt 中包含的数据具有 `userID`、`movieID`、`userRating` 和 `timestamp` 结构，它将告诉我们每个用户对电影评级的情况。 下面是数据的示例：

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

2. 可使用该输出以及 moviedb.txt 显示更易于用户理解的信息。 首先，需使用以下命令本地复制文件：
    
    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    这会将输出数据复制到当前目录中名为 **recommendations.txt** 的文件以及电影数据文件。

3. 使用以下命令创建新的 Python 脚本，该脚本在推荐输出中查找数据的电影名称：
   
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
    
4. 使用以下命令以使该文件成为可执行文件：
   
    ```bash
    chmod +x show_recommendations.py
    ```

5. 运行 Python 脚本。 以下命令假设你已处于所有文件都已下载的目录中：
   
    ```bash
    ./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```
    
    此命令将查看为用户 ID 4 生成的建议。
   
   * **user-ratings.txt** 文件用于检索用户评价过的电影。

   * **moviedb.txt** 文件用于检索电影的名称。

   * **recommendations.txt** 用于检索此用户的电影建议。
     
     此命令的输出类似于以下文本：
     
     ```
       Reading Movies Descriptions
       Reading Rated Movies
       Reading Recommendations
     
     ##   Rated Movies
       Mimic (1997), rating=3
       Ulee's Gold (1997), rating=5
       Incognito (1997), rating=5
       One Flew Over the Cuckoo's Nest (1975), rating=4
       Event Horizon (1997), rating=4
       Client, The (1994), rating=3
       Liar Liar (1997), rating=5
       Scream (1996), rating=4
       Star Wars (1977), rating=5
       Wedding Singer, The (1998), rating=5
       Starship Troopers (1997), rating=4
       Air Force One (1997), rating=5
       Conspiracy Theory (1997), rating=3
       Contact (1997), rating=5
       Indiana Jones and the Last Crusade (1989), rating=3
       Desperate Measures (1998), rating=5
       Seven (Se7en) (1995), rating=4
       Cop Land (1997), rating=5
       Lost Highway (1997), rating=5
       Assignment, The (1997), rating=5
       Blues Brothers 2000 (1998), rating=5
       Spawn (1997), rating=2
       Wonderland (1997), rating=5
     
     ##   In & Out (1997), rating=5
     ##   Recommended Movies
       Seven Years in Tibet (1997), score=5.0
       Indiana Jones and the Last Crusade (1989), score=5.0
       Jaws (1975), score=5.0
       Sense and Sensibility (1995), score=5.0
       Independence Day (ID4) (1996), score=5.0
       My Best Friend's Wedding (1997), score=5.0
       Jerry Maguire (1996), score=5.0
       Scream 2 (1997), score=5.0
       Time to Kill, A (1996), score=5.0
     
     ##   Rock, The (1996), score=5.0
     ```

## <a name="delete-temporary-data"></a>删除临时数据

Mahout 作业不删除在处理作业时创建的临时数据。 在示例作业中指定 `--tempDir` 参数，以将临时文件隔离到特定路径中轻松删除。 若要删除临时文件，请使用以下命令：

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]
> 若要再次运行此命令，则必须删除输出目录。 使用以下命令删除此目录：
> 
> ```hdfs dfs -rm -f -r /example/data/mahoutout```


## <a name="next-steps"></a>后续步骤

现在，你已经学习了如何使用 Mahout，因此可以探索通过其他方式来使用 HDInsight 上的数据：

* [Hive 和 HDInsight 配合使用](hdinsight-use-hive.md)
* [Pig 和 HDInsight 配合使用](hdinsight-use-pig.md)
* [MapReduce 和 HDInsight 配合使用](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools




<!--HONumber=Jan17_HO3-->


