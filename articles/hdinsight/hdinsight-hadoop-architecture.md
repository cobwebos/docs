---
title: Hadoop 体系结构 - Azure HDInsight | Microsoft Docs
description: 介绍 HDInsight 群集上的 Hadoop 存储和处理。
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 5ec5f1f24d3bf953115bfa5023faf81df132f510
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="hadoop-architecture-in-hdinsight"></a>HDInsight 中的 Hadoop 体系结构

Hadoop 包括两个核心组件，即提供存储的高密度文件系统 (HDFS)，以及提供处理功能的 Yet Another Resource Negotiator (YARN)。 有了存储和处理功能，群集就可以运行 MapReduce 程序来执行所需的数据处理。

> [!NOTE]
> 通常不会将 HDFS 部署在 HDInsight 群集中来提供存储， 而是由 Hadoop 组件来使用 HDFS 兼容接口层。 实际的存储功能由 Azure 存储或 Azure Data Lake Store 提供。 就 Hadoop 来说，在 HDInsight 群集上执行的 MapReduce 作业运行起来就像 HDFS 存在一样，因此不需更改即可满足其存储需求。 在 Hadoop on HDInsight 中，存储是外包的，但 YARN 处理仍为核心组件。 有关详细信息，请参阅 [Azure HDInsight 简介](hadoop/apache-hadoop-introduction.md)。

本文介绍 YARN，说明其如何协调应用程序在 HDInsight 上的执行。

## <a name="yarn-basics"></a>YARN 基础知识 

YARN 控制并协调 Hadoop 中的数据处理。 YARN 有两个核心服务，在群集的节点上作为进程运行： 

* ResourceManager 
* NodeManager

ResourceManager 将群集计算资源授予 MapReduce 作业之类的应用程序。 ResourceManager 将这些资源作为容器来授予，每个容器都分配有相应的 CPU 核心和 RAM 内存。 如果将群集中的所有可用资源组合起来，然后将其以块的形式分发，这些块包含给定数目的核心和内存，则每个资源块都是一个容器。 群集中的每个节点都有一个容量，只能存储特定数目的容器，因此群集对于可用容器的数目有一个固定的限制。 可以对资源在容器中的分配进行配置。 

当 MapReduce 应用程序在群集上运行时，ResourceManager 为应用程序提供可在其中执行操作的容器。 ResourceManager 可以跟踪运行的应用程序的状态、可用群集容量，还可以在应用程序完成并释放其资源时跟踪应用程序。 

ResourceManager 还运行一个 Web 服务器进程，该进程提供的 Web 用户界面可以用来监视应用程序的状态。 

当用户提交要在群集上运行的 MapReduce 应用程序时，该应用程序会提交给 ResourceManager。 反过来，ResourceManager 会在可用的 NodeManager 节点上分配一个容器。 NodeManager 节点是应用程序的实际执行位置。 第一个分配的容器运行名为 ApplicationMaster 的特殊应用程序。 该 ApplicationMaster 负责获取资源，这些资源采用后续容器的形式，是运行提交的应用程序所必需的。 ApplicationMaster 会 检查应用程序的阶段（映射阶段和化简阶段），并会将需要处理的数据量考虑进去。 ApplicationMaster 然后会代表应用程序从 ResourceManager 请求（协商）资源。 ResourceManager 反过来会将群集中 NodeManager 提供的资源授予 ApplicationMaster，供其在执行应用程序时使用。 

NodeManagers 先运行应用程序包含的任务，然后将其进度和状态回头报告给 ApplicationMaster。 ApplicationMaster 则将应用程序的状态报告给 ResourceManager。 ResourceManager 将任何结果返回给客户端。

## <a name="yarn-on-hdinsight"></a>YARN on HDInsight

所有 HDInsight 群集类型都部署 YARN。 ResourceManager 在进行高可用性部署时会使用一个主实例和一个辅助实例，二者分别运行在群集的第一个头节点和第二个头节点上。 一次只有一个 ResourceManager 实例处于活动状态。 NodeManager 实例跨群集的可用工作节点运行。

![YARN on HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>后续步骤

* [在 Hadoop on HDInsight 中使用 MapReduce](hadoop/hdinsight-use-mapreduce.md)
* [Azure HDInsight 简介](hadoop/apache-hadoop-introduction.md)
