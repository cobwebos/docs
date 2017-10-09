---
title: "在 Azure 上使用 Hive 进行 HDInsight Hadoop 数据科学演练 | Microsoft Docs"
description: "Team Data Science Process 的示例，演练如何在 Azure HDInsight Hadoop 上使用 Hive 来执行预测分析。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5dbfd043f1809f407baf6aa4ca5c5272af425012
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>在 Azure 上使用 Hive 进行 HDInsight Hadoop 数据科学演练 

这些演练在 HDInsight Hadoop 群集中使用 Hive 执行预测分析。 它们遵循 Team Data Science Process 中所述的步骤。 有关 Team Data Science Process 的概述，请参阅[数据科学过程](overview.md)。 有关 Azure HDInsight 的简介，请参阅 [Azure HDInsight、Hadoop 技术堆栈和 Hadoop 群集简介](../../hdinsight/hdinsight-hadoop-introduction.md)。

其他执行 Team Data Science Process 的数据科学演练按所使用的**平台**分组。 有关这些示例的明细，请参阅[执行 Team Data Science Process 的演练](walkthroughs.md)。


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>在 HDInsight Hadoop 中使用 Hive 预测出租车小费

[使用 HDInsight Hadoop 群集](hive-walkthrough.md)演练使用纽约出租车中的数据来预测： 

- 是否支付了小费 
- 小费金额的分布

该方案是在 [Azure HDInsight Hadoop 群集](https://azure.microsoft.com/services/hdinsight/)中使用 Hive 实现的。 本演练介绍如何存储、探索和特征化纽约市出租车行程与车费数据集中的工程数据。 还可以使用 Azure 机器学习来生成和部署模型。

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>在 HDInsight Hadoop 中使用 Hive 来预测广告点击量

[使用 Azure HDInsight Hadoop 群集处理 1-TB 数据集](hive-criteo-walkthrough.md)演练使用公用 [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) 点击量数据集来预测是否支付了小费以及预期的金额范围。 该方案的实现方式是在 [Azure HDInsight Hadoop 群集](https://azure.microsoft.com/services/hdinsight/)中使用 Hive 来存储、探索及特征化工程数据与下游样本数据。 它使用 Azure 机器学习来生成、训练和评分一个用于预测用户是否点击了某个广告的二元分类模型。 演练的最后展示了如何将这些模型之一发布为 Web 服务。


## <a name="next-steps"></a>后续步骤

有关构成 Team Data Science Process 的关键组件的讨论，请参阅 [Team Data Science Process 概述](overview.md)。

有关可用于构建数据科学项目的 Team Data Science Process 生命周期的讨论，请参阅 [Team Data Science Process 生命周期](lifecycle.md)。 生命周期概述了执行项目时，其从开始到结束所遵循的步骤。 


