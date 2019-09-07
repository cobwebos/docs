---
title: Azure HDInsight 中的 Apache Ambari 检测信号问题
description: 查看 Azure HDInsight 中 Apache Ambari 检测信号问题的各种原因
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/02/2019
ms.openlocfilehash: 0b20bb5265dd5fa139a691fcb21aaa1795ff352c
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735971"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache Ambari 检测信号问题

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="scenario-high-cpu-utilization"></a>场景：高 CPU 利用率

### <a name="issue"></a>问题

Ambari 代理的 CPU 使用率较高，这会导致来自某些节点的 Ambari UI 发出警报，Ambari 代理检测信号丢失。

### <a name="cause"></a>原因

由于各种 ambari 代理 bug，在极少数情况下，ambari 代理的 CPU 使用率高（100）。

### <a name="resolution"></a>分辨率

1. 标识 ambari 的进程 ID （pid）：

    ```bash
    ps -ef | grep ambari_agent
    ```

1. 然后，运行以下命令来显示 CPU 利用率：

    ```bash
    top -p <ambari-agent-pid>
    ```

1. 重新启动 ambari 以缓解问题：

    ```bash
    service ambari-agent restart
    ```

1. 如果重新启动不起作用，请终止 ambari 进程，然后启动它：

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>场景：Ambari 代理未启动

### <a name="issue"></a>问题

Ambari 代理尚未启动，这会导致 Ambari UI 发出的针对某些节点的警报丢失 Ambari 代理检测信号。

### <a name="cause"></a>原因

这些警报是由未运行 Ambari 代理导致的。

### <a name="resolution"></a>分辨率

1. 确认 ambari 的状态-代理：

    ```bash
    service ambari-agent status
    ```

1. 确认故障转移控制器服务是否正在运行：

    ```bash
    ps -ef | grep failover
    ```

    如果故障转移控制器服务未运行，则很可能是由于某个问题导致 hdinsight 代理无法启动故障转移控制器。 检查 hdinsight-来自`/var/log/hdinsight-agent/hdinsight-agent.out`文件的代理日志。

---

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
