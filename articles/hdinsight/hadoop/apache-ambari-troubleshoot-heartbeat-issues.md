---
title: Azure HDInsight 中的 Apache Ambari 检测信号问题
description: 回顾 Azure HDInsight 中阿帕奇安巴里心跳问题的各种原因
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057067"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache Ambari 检测信号问题

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="scenario-high-cpu-utilization"></a>方案：CPU 利用率高

### <a name="issue"></a>问题

Ambari 代理的 CPU 利用率很高，这会导致 Ambari UI 发出警报，即对于某些节点，Ambari 代理检测信号丢失。 检测信号丢失警报通常是短暂的。

### <a name="cause"></a>原因

由于各种 Ambari 代理 bug，在极少数情况下，Ambari 代理可能具有很高的 CPU 利用率（接近 100%）。

### <a name="resolution"></a>解决方法

1. 确定 Ambari 代理的进程 ID (PID)：

    ```bash
    ps -ef | grep ambari_agent
    ```

1. 然后，运行以下命令来显示 CPU 利用率：

    ```bash
    top -p <ambari-agent-pid>
    ```

1. 重启 Ambari 代理以缓解问题：

    ```bash
    service ambari-agent restart
    ```

1. 如果重启不起作用，请终止 Ambari 代理进程，然后启动它：

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>方案：Ambari 代理未启动

### <a name="issue"></a>问题

Ambari 代理尚未启动，导致来自 Ambari UI 的警报，即对于某些节点，Ambari 代理检测信号将丢失。

### <a name="cause"></a>原因

这些警报是由未运行 Ambari 代理导致的。

### <a name="resolution"></a>解决方法

1. 确认 Ambari 代理的状态：

    ```bash
    service ambari-agent status
    ```

1. 确认故障转移控制器服务是否正在运行：

    ```bash
    ps -ef | grep failover
    ```

    如果故障转移控制器服务未运行，则可能是由于问题阻止 hdinsight 代理启动故障转移控制器。 通过 `/var/log/hdinsight-agent/hdinsight-agent.out` 文件检查 hdinsight 代理日志。

## <a name="scenario-heartbeat-lost-for-ambari"></a>场景：安巴里的心跳丢失

### <a name="issue"></a>问题

安巴里心跳剂丢失。

### <a name="cause"></a>原因

OMS 日志导致 CPU 利用率高。

### <a name="resolution"></a>解决方法

* 使用[禁用 AzHDInsight 监视](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)电源 Cmdlet 禁用 Azure 监视器日志记录。
* 删除`mdsd.warn`日志文件

---

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帐户连接 Azure 社区，以将 Azure 社区连接到正确的资源：答案、支持和专家，从而改善客户体验。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
