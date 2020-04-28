---
title: 从 Azure HDInsight 中的 Apache Ambari Hive 视图运行查询时发生异常
description: 排查通过 Azure HDInsight 中的 Apache Ambari Hive 视图运行 Apache Hive 查询时出现的问题的步骤。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895045"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>从 Azure HDInsight 中的 Apache Ambari Hive 视图运行查询时发生异常

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

从 Apache Ambari Hive 视图运行 Apache Hive 查询时，不时地收到以下错误消息：

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>原因

网关超时。

网关超时值为 2 分钟。 来自 Ambari Hive 视图的查询将通过网关提交到 `/hive2` 终结点。 成功编译并接受查询后，HiveServer 将返回 `queryid`。 然后，客户端将不断轮询该查询的状态。 在此过程中，如果 HiveServer 在 2 分钟内未返回 HTTP 响应，则 HDI 网关会向调用方引发 502.3 网关超时错误。 当查询已提交进行处理（很有可能）并位于“获取状态”调用中（不太可能）时，可能会发生这些错误。 用户可以查看查询的任一状态。

HTTP 处理程序线程的速度预期很快：只需准备作业并返回 `queryid`。 但是，由于多种原因，所有处理程序线程可能非常繁忙，导致新查询和“获取状态”调用超时。

### <a name="responsibilities-of-the-http-handler-thread"></a>HTTP 处理程序线程的责任

当客户端将查询提交到 HiveServer 时，会在前台线程中执行以下操作：

* 分析请求，执行语义验证
* 获取锁
* 执行元存储查找（如有必要）
* 编译查询（DDL 或 DML）
* 准备查询计划
* 执行授权（在安全群集中运行所有适用的 Ranger 策略）

## <a name="resolution"></a>解决方法

可以遵循一些常规建议来改善这种情况：

* 如果使用外部 Hive 元存储，请检查数据库指标，并确保数据库未过载。 考虑缩放元存储数据库层。

* 确保已启用并行操作（使 HTTP 处理程序线程能够并行运行）。 若要验证该值，请启动 [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) 并导航到“Hive” **“配置”** “高级” > “自定义 hive-site”。   >    >   `hive.server2.parallel.ops.in.session` 的值应是 `true`。

* 确保群集的 VM SKU 对于负载而言不会太小。 考虑将工作负载分散到多个群集。 有关详细信息，请参阅[选择群集类型](../hdinsight-capacity-planning.md#choose-a-cluster-type)。

* 如果在群集上安装了 Ranger，请检查是否需要为每个查询评估过多的 Ranger 策略。 找出重复或不需要的策略。

* 在 Ambari 中检查“HiveServer2 堆大小”值。  导航到“Hive” **“配置”** “设置” > “优化”。   >    >   确保该值大于 10 GB。 根据需要进行调整，以优化性能。

* 确保 Hive 查询经过适当的优化。 有关详细信息，请参阅[在 Azure HDInsight 中优化 Apache Hive 查询](../hdinsight-hadoop-optimize-hive-query.md)。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
