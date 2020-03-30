---
title: 在 Azure HDInsight 中运行来自 Apache Ambari Hive 视图的查询时的异常
description: 在 Azure HDInsight 中通过 Apache Ambari Hive 视图运行 Apache Hive 查询时，需要执行故障排除步骤。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895045"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>在 Azure HDInsight 中运行来自 Apache Ambari Hive 视图的查询时的异常

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

从 Apache Ambari Hive 视图运行 Apache Hive 查询时，您间歇性地收到以下错误消息：

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>原因

网关超时。

网关超时值为 2 分钟。 来自 Ambari Hive 视图的查询通过`/hive2`网关提交到终结点。 成功编译和接受查询后，HiveServer 将返回 。 `queryid` 然后，客户端保留查询状态的轮询。 在此过程中，如果 HiveServer 在 2 分钟内未返回 HTTP 响应，则 HDI 网关会向呼叫者引发 502.3 网关超时错误。 当提交查询进行处理（更有可能）时，以及获取状态调用（不太可能）时，可能会发生错误。 用户可以看到其中任一。

http 处理程序线程应该是快速的：准备作业并返回 。 `queryid` 但是，由于多种原因，所有处理程序线程可能都很忙，从而导致新查询和 get 状态调用超时。

### <a name="responsibilities-of-the-http-handler-thread"></a>HTTP 处理程序线程的职责

当客户端向 HiveServer 提交查询时，它在前台线程中执行以下操作：

* 解析请求，进行语义验证
* 获取锁
* 如有必要，元存储查找
* 编译查询（DDL 或 DML）
* 准备查询计划
* 执行授权（在安全群集中运行所有适用的游侠策略）

## <a name="resolution"></a>解决方法

一些一般性的建议，你改善的情况：

* 如果使用外部配置单元元存储，请检查数据库指标并确保数据库未过载。 请考虑缩放元存储数据库层。

* 确保并行操作处于打开状态（这使 HTTP 处理程序线程能够并行运行）。 要验证该值，请启动[Apache Ambari](../hdinsight-hadoop-manage-ambari.md)并导航到**Hive** > **Configs** > **高级** > **自定义配置单元站点**。 的值`hive.server2.parallel.ops.in.session`应为`true`。

* 确保群集的 VM SKU 不会太小，无法进行负载。 请考虑在多个群集之间拆分工作。 有关详细信息，请参阅[选择群集类型](../hdinsight-capacity-planning.md#choose-a-cluster-type)。

* 如果群集上安装了 Ranger，请检查是否每个查询需要计算的 Ranger 策略太多。 查找重复或不需要的策略。

* 从 Ambari 检查**HiveServer2 堆大小**值。 导航到**蜂巢** > **配置** > **设置** > **优化**。 确保该值大于 10 GB。 根据需要进行调整以优化性能。

* 确保蜂巢查询已调整良好。 有关详细信息，请参阅在[Azure HDInsight 中优化 Apache Hive 查询](../hdinsight-hadoop-optimize-hive-query.md)。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport)- 用于改善客户体验的官方 Microsoft Azure 帐户连接。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
