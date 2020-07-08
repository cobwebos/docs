---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: a4faab9ac6d5e1c39c1120e09dae792b95892d60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564404"
---
# <a name="release-notes"></a>发行说明

本文提供有关**最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源分析服务之一。

## <a name="release-date-06112020"></a>发布日期：06/11/2020

此版本适用于 HDInsight 3.6 和 4.0。 HDInsight 发行版在几天后即会在所有区域中推出。 此处的发行日期是指在第一个区域中的发行日期。 如果看不到下面的更改，请在你的区域中等待几天的发布。

## <a name="new-features"></a>新增功能
### <a name="moving-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 现在使用 Azure 虚拟机来设置群集。 在此版本中，新创建的 HDInsight 群集开始使用 Azure 虚拟机规模集。 更改将逐步推出。 预计不会有重大更改。 详细了解[Azure 虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。
 
### <a name="reboot-vms-in-hdinsight-cluster"></a>在 HDInsight 群集中重新启动 Vm
在此版本中，我们支持在 HDInsight 群集中重新启动 Vm，以重新启动无响应节点。 目前只能通过 API 完成此操作，而 PowerShell 和 CLI 支持就是这样。 有关 API 的详细信息，请参阅[此文档](https://github.com/Azure/azure-rest-api-specs/codeowners/master/specification/hdinsight/resource-manager/Microsoft.HDInsight/stable/2018-06-01-preview/virtualMachines.json)。
 
## <a name="deprecation"></a>弃用
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>弃用 HDInsight 3.6 Spark 群集中的 Spark 2.1 和 2.2
从7月 1 2020 开始，客户无法在 HDInsight 3.6 上创建新的 spark 2.1 和2.2 的 Spark 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑在 2020 年 6 月 30 日之前转移到 HDInsight 3.6 上的 Spark 2.3，以避免潜在的系统/支持中断。
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>弃用 HDInsight 4.0 Spark 群集中的 Spark 2.3
从7月 1 2020 开始，客户无法在 HDInsight 4.0 上利用 Spark 2.3 创建新的 Spark 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑在 2020 年 6 月 30 日之前转移到 HDInsight 4.0 上的 Spark 2.4，避免出现潜在的系统/支持中断。
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>弃用 HDInsight 4.0 Kafka 群集中的 Kafka 1.1
从 2020 年 7 月 1 日开始，客户将无法使用 HDInsight 4.0 上的 Kafka 1.1 创建新的 Kafka 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑在 2020 年 6 月 30 日之前转移到 HDInsight 4.0 上的 Spark 2.1，避免出现潜在的系统/支持中断。
 
## <a name="behavior-changes"></a>行为变更
### <a name="esp-spark-cluster-head-node-size-change"></a>ESP Spark 群集头节点大小更改 
将 ESP Spark 群集允许的最小头节点大小更改为 Standard_D13_V2。 具有低核心和内存作为头节点的 Vm 可能会导致 ESP 群集问题，因为 CPU 和内存容量相对较低。 从版本开始，使用 Sku 高于 Standard_D13_V2，并将 Standard_E16_V3 用作 ESP Spark 群集的头节点。
 
### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>提供至少有 4 个核心的 VM 作为头节点 
头节点至少需要 4 核 VM，以确保 HDInsight 群集的高可用性和可靠性。 从 2020 年 4 月 6 日开始，客户只能选择至少有 4 个核心的 VM 作为新 HDInsight 群集的头节点。 现有群集将继续按预期方式运行。 
 
### <a name="cluster-worker-node-provisioning-change"></a>群集工作节点设置更改
当80% 的辅助角色节点准备就绪时，群集将进入**操作**阶段。 在此阶段，客户可以执行所有数据平面操作，例如运行脚本和作业。 但客户不能执行任何控制平面操作，例如增加/减少。 仅支持删除。
 
在**操作**阶段后，群集将为剩余的20% 工作节点等待另一个60分钟。 在60分钟结束时，即使所有工作节点仍不可用，群集也会移到**正在运行**的阶段。 一旦群集进入**正在运行**的阶段，就可以使用它。 将接受控制计划操作（如向上扩展/向下缩放）和数据计划操作（如运行脚本和作业）。 如果某些请求的工作节点不可用，则该群集将被标记为部分成功。 你需要为已成功部署的节点付费。 
 
### <a name="create-new-service-principal-through-hdinsight"></a>通过 HDInsight 创建新的服务主体
以前，在创建群集的过程中，客户可以创建新的服务主体来访问 Azure 门户中连接的 ADLS 第1代帐户。 从 15 2020 年6月开始，客户无法在 HDInsight 创建工作流中创建新的服务主体，只支持现有的服务主体。 请参阅[使用 Azure Active Directory 创建服务主体和证书](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

### <a name="time-out-for-script-actions-with-cluster-creation"></a>创建群集时脚本操作的超时时间
HDInsight 支持在创建群集的过程中运行脚本操作。 在此版本中，群集创建的所有脚本操作都必须在**60 分钟**内完成，否则会超时。提交到正在运行的群集的脚本操作不会受到影响。 请访问[此处](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#script-action-in-the-cluster-creation-process)了解更多详细信息。
 
## <a name="upcoming-changes"></a>即将推出的更改
不需要注意的即将发生的重大更改。
 
## <a name="bug-fixes"></a>Bug 修复
HDInsight 会持续改善群集的可靠性和性能。 
 
## <a name="component-version-change"></a>组件版本更改
### <a name="hbase-20-to-216"></a>HBase 2.0 到 2.1.6
HBase 版本从版本2.0 升级到2.1.6。
 
### <a name="spark-240-to-244"></a>Spark 2.4.0 到 2.4.4
Spark 版本从版本2.4.0 升级到2.4.4。
 
### <a name="kafka-210-to-211"></a>Kafka 2.1.0 到 2.1.1
Kafka 版本从版本2.1.0 升级到2.1.1。
 
可以在[此文档](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)中找到 HDInsight 4.0 ad hdinsight 3.6 的当前组件版本

## <a name="known-issues"></a>已知问题

### <a name="hive-warehouse-connector-issue"></a>Hive 仓库连接器问题
此版本中的 Hive 仓库连接器存在问题。 此修补程序将包含在下一个版本中。 在此版本之前创建的现有群集不受影响。 如果可能，请避免 droping 和重新创建群集。 如果需要进一步的帮助，请打开支持票证。
