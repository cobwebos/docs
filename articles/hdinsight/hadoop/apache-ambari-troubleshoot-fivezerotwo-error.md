---
title: Azure HDInsight 中的 Apache Ambari UI 502 错误
description: 尝试访问 Azure HDInsight 群集时 Apache Ambari UI 502 错误
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895756"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>方案： Azure HDInsight 中的 Apache Ambari UI 502 错误

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

尝试访问 HDInsight 群集的 Apache Ambari UI 时，会收到如下消息： "502-Web 服务器在充当网关或代理服务器时收到了无效响应"。

## <a name="cause"></a>原因

通常，HTTP 502 状态代码表示 Ambari 服务器未在活动头节点上正常运行。 有几种可能的根本原因。

## <a name="resolution"></a>分辨率

在大多数情况下，若要缓解此问题，可以重新启动活动的头节点。 或为头节点选择更大的 VM 大小。

### <a name="ambari-server-failed-to-start"></a>Ambari 服务器启动失败

你可以检查 ambari 日志以找出 Ambari 服务器启动失败的原因。 一个常见原因是数据库一致性检查错误。 你可以在此日志文件中找到以下内容： `/var/log/ambari-server/ambari-server-check-database.log`。

如果对群集节点进行了任何修改，请将其撤消。 始终使用 Ambari UI 来修改任何 Hadoop/Spark 相关配置。

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari 服务器占用100% 的 CPU 使用率

在极少数情况下，我们已看到 ambari 进程经常接近100% 的 CPU 使用率。 作为缓解措施，可以通过 ssh 连接到活动的头节点，并终止 Ambari 服务器进程并重新启动。

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Ambari 服务器终止 oom-killer

在某些情况下，头节点内存不足，Linux oom 将开始选取要终止的进程。 你可以通过搜索 AmbariServer 进程 ID （应找不到）来验证此情况。 然后查看 `/var/log/syslog`，并查找类似于下面的内容：

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

然后确定哪些进程正在占用内存，并尝试进一步的根本原因。

### <a name="other-issues-with-ambari-server"></a>Ambari 服务器的其他问题

很少 Ambari 服务器无法处理传入的请求，你可以通过查看 Ambari-服务器日志中的任何错误来找到详细信息。 这种情况如下所示：

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -通过将 Azure 社区连接到适当的资源来改进客户体验的官方 Microsoft Azure 帐户：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
