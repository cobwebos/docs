---
title: "使用 Apache Storm on HDInsight 确定 Twitter 流行主题 | Microsoft Docs"
description: "了解如何使用 Trident 创建用于确定基于哈希标记的 Twitter 流行主题的 Apache Storm 拓扑。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 63b280ea-5c07-4dc8-a35f-dccf5a96ba93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: d588221586f151319436525c5098b0bb2694e5f9
ms.lasthandoff: 04/18/2017


---
# <a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>使用 Apache Storm on HDInsight 确定 Twitter 流行主题

了解如何使用 Trident 创建确定 Twitter 流行主题（哈希标记）的 Storm 拓扑。

Trident 是一种高级抽象，可提供联接、聚合、分组、函数和筛选器等工具。 此外，Trident 还添加了基元，用于执行有状态增量处理。 在本文档中使用的示例是包含自定义 Spout 和函数的 Trident 拓扑。 它还使用 Trident 提供的多个内置函数。

## <a name="requirements"></a>要求

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java 和 JDK 1.8</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Twitter 开发人员帐户

## <a name="download-the-project"></a>下载项目

使用以下代码在本地复制项目。

    git clone https://github.com/Blackmist/TwitterTrending

## <a name="understanding-the-topology"></a>了解拓扑

下图显示数据如何在此拓扑中流动：

![拓扑](./media/hdinsight-storm-twitter-trending/trident.png)

> [!NOTE]
> 该图是一个简化的拓扑视图。 组件的多个实例分散在群集的各个节点上。


实现拓扑的 Trident 代码如下：

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

此代码执行以下操作：

1. 在 Spout 中创建流。 Spout 检索 Twitter 中的推文，然后筛选特定关键字（在本示例中，为 love、music 和 coffee）。

2. 使用自定义函数 HashtagExtractor 提取每则推文中的哈希标记。 这些哈希标记将发送到流。

3. 按哈希标记将流分组，并将其传递给聚合器。 此聚合器计算每个哈希标记出现的次数。 此数据保存在内存中。 最后，发出包含哈希标记和计数的新流。

4. 应用 **FirstN** 程序集以根据计数字段仅返回前 10 个值。

> [!NOTE]
> 有关使用 Trident 的详细信息，请参阅 [Trident API 概述](http://storm.apache.org/releases/current/Trident-API-Overview.html)文档。

### <a name="the-spout"></a>Spout

Spout (**TwitterSpout**) 使用 [Twitter4j](http://twitter4j.org/en/) 从 Twitter 检索推文。 为单词 __love__、**music** 和 **coffee** 创建筛选器。 匹配该筛选器的传入推文（状态）存储在链接的阻止队列中。 最后，从队列中提取出项，并将其发送到拓扑。

### <a name="the-hashtagextractor"></a>HashtagExtractor

若要提取哈希标记，可以使用 [getHashtagEntities](http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--) 来检索推文中包含的所有哈希标记。 然后，将这些标记发送到流。

## <a name="configure-twitter"></a>配置 Twitter

使用以下步骤注册新的 Twitter 应用程序，并获取读取 Twitter 内容所需的使用者和访问令牌信息。

1. 转到 [Twitter 应用](https://apps.twitter.com)，然后单击“创建新应用”按钮。 填写表单时，请将“回调 URL”字段保留空白。

2. 创建应用后，单击“密钥和访问令牌”选项卡。

3. 复制“使用者密钥”和“使用者密码”信息。

4. 如果没有令牌，请在页底部选择“创建访问令牌”。 创建令牌后，复制“访问令牌”和“访问令牌密码”信息。

5. 在前面克隆的 **TwitterSpoutTopology** 项目中，打开 **resources/twitter4j.properties** 文件，并添加前面步骤中收集的信息，然后保存文件。

## <a name="build-the-topology"></a>生成拓扑

使用以下代码生成项目：

        cd [directoryname]
        mvn compile

## <a name="test-the-topology"></a>测试拓扑

使用以下命令在本地测试拓扑：

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

拓扑启动后，你应会看到包含拓扑发出的哈希标记和计数的调试信息。 输出应类似于以下文本：

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

## <a name="next-steps"></a>后续步骤

在本地测试拓扑后，请探索如何部署拓扑：[在 HDInsight 上部署和管理 Apache Storm 拓扑](hdinsight-storm-deploy-monitor-topology.md)。

用户可能会对以下 Storm 主题感兴趣：

* [使用 Maven 为 Storm on HDInsight 开发 Java 拓扑](hdinsight-storm-develop-java-topology.md)
* [使用 Visual Studio 开发 Storm on HDInsight 的 C# 拓扑](hdinsight-storm-develop-csharp-visual-studio-topology.md)

有关 HDInsight 的更多 Storm 示例：

* [Storm on HDInsight 的示例拓扑](hdinsight-storm-example-topology.md)


