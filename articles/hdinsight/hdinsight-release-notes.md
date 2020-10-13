---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 616e3e6c37faa3c085b8531173b557973e09fbf8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974560"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 发行说明

本文提供有关**最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源分析服务之一。

## <a name="release-date-10082020"></a>发布日期：10/08/2020

此版本适用于 HDInsight 3.6 和 HDInsight 4.0。 HDInsight 发行版在几天后即会在所有区域中推出。 此处的发行日期是指在第一个区域中的发行日期。 如果看不到以下更改，请耐心等待，几天后发行版会在你所在的区域推出。

## <a name="new-features"></a>新增功能
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>无公共 IP 和专用链接的 HDInsight 专用群集 (预览版) 
HDInsight 现在支持在预览中创建没有公共 IP 的群集和对群集的专用链接访问。 客户可以使用新的高级网络设置创建没有公共 IP 的完全隔离群集，并使用其自己的专用终结点来访问群集。 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 目前使用 Azure 虚拟机来预配群集。 从此版本开始，该服务将逐步迁移到 [Azure 虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。 整个过程可能需要几个月的时间。 迁移你的区域和订阅后，新创建的 HDInsight 群集将在无需客户操作的虚拟机规模集上运行。 不需要进行重大更改。

## <a name="deprecation"></a>弃用
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>弃用 HDInsight 3.6 ML 服务群集
HDInsight 3.6 ML 服务群集类型将在 Dec 31 2020 后结束支持。 此后，客户不会创建新的 3.6 ML 服务群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请在 [此处](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)查看 HDInsight 版本和群集类型的支持有效期。

## <a name="behavior-changes"></a>行为更改
此版本没有行为更改。

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将推出以下变更。

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>能够为 Spark、Hadoop 和 ML 服务选择不同的 Zookeeper 虚拟机大小
目前，HDInsight 不支持为 Spark、Hadoop 和 ML 服务群集类型自定义 Zookeeper 节点大小。 默认为 A2_v2/A2 的虚拟机大小，免费提供。 在即将发布的版本中，你可以选择最适合你的方案的 Zookeeper 虚拟机大小。 虚拟机大小不是 A2_v2/A2 的 Zookeeper 节点将进行收费。 A2_v2 和 A2 虚拟机仍免费提供。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 会持续改善群集的可靠性和性能。 

## <a name="component-version-change"></a>组件版本更改
此发行版未发生组件版本更改。 可以在[此文档](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)中查找 HDInsight 4.0 和 HDInsight 3.6 的当前组件版本。