---
title: 优化 Spark 作业的性能 - Azure HDInsight
description: 介绍实现 Azure HDInsight 中的 Apache Spark 群集最佳性能的常见策略。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 0b152f7882c7d7a3bab762253da0febc0257ceae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84117964"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>优化 HDInsight 中的 Apache Spark 作业

本文简要介绍了用于优化 Azure HDInsight 上的 Apache Spark 作业的策略。

## <a name="overview"></a>概述

Apache Spark 作业的性能由多种因素而定。 这些性能因素包括：数据的存储方式、群集的配置方式，以及处理数据时采用的操作。

你可能面临的常见挑战包括：由于未正确调整执行器的大小，长时间运行的操作以及导致笛卡尔运算的任务，导致内存约束。

还有很多优化可帮助您克服这些挑战，例如缓存，并允许数据歪斜。

在下面的每篇文章中，可以找到有关 Spark 优化的各个方面的信息。

* [优化 Apache Spark 的数据存储](optimize-data-storage.md)
* [优化 Apache Spark 的数据处理](optimize-data-processing.md)
* [优化 Apache Spark 的内存使用率](optimize-memory-usage.md)
* [优化 Apache Spark 的 HDInsight 群集配置](optimize-cluster-configuration.md)

## <a name="next-steps"></a>后续步骤

* [调试 Azure HDInsight 中运行的 Apache Spark 作业](apache-spark-job-debugging.md)
* [管理 HDInsight 上 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [配置 Apache Spark 设置](apache-spark-settings.md)
