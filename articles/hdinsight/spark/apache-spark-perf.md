---
title: 优化 Spark 作业的性能 - Azure HDInsight
description: 介绍实现 Azure HDInsight 中的 Apache Spark 群集最佳性能的常见策略。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780110"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>优化 HDInsight 中的 Apache Spark 作业

本文简要介绍了用于优化 Azure HDInsight 上的 Apache Spark 作业的策略。

## <a name="overview"></a>概述

Apache Spark 作业的性能由多种因素而定。 这些性能因素包括：数据的存储方式、群集的配置方式，以及处理数据时采用的操作。

可能面临的最常见的难题包括由于大小不恰当的执行程序、长时间运行的操作和引发笛卡尔操作的任务而导致的内存约束。

也有各种策略可帮助你克服这些难题，例如缓存和允许数据倾斜。

在下面的每篇文章中，你可找到 Spark 优化的不同方面的挑战及解决方案。

* [优化数据存储](optimize-data-storage.md)
* [优化数据处理](optimize-data-processing.md)
* [优化内存使用率](optimize-memory-usage.md)
* [优化群集配置](optimize-cluster-configuration.md)

## <a name="next-steps"></a>后续步骤

* [调试 Azure HDInsight 中运行的 Apache Spark 作业](apache-spark-job-debugging.md)
* [管理 HDInsight 上 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [配置 Apache Spark 设置](apache-spark-settings.md)
