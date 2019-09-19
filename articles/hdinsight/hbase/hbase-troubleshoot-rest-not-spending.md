---
title: Apache HBase REST 未响应 Azure HDInsight 中的请求
description: 解决 Apache HBase REST 未响应 Azure HDInsight 中的请求的问题。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 81126e0d2b87a84c81a3be8682ad41813991dc81
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091603"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>场景：Apache HBase REST 未响应 Azure HDInsight 中的请求

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Apache HBase REST 服务不响应 Azure HDInsight 中的请求。

## <a name="cause"></a>原因

此处可能的原因可能是 Apache HBase REST 服务正在泄漏套接字，这在服务长时间运行（例如，月）时尤其常见。 在客户端 SDK 中，你可能会看到类似于以下内容的错误消息：

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>分辨率

在 SSHing 到主机之后，使用以下命令重新启动 HBase REST。 你还可以使用脚本操作在所有辅助角色节点上重新启动此服务：

```bash
sudo service hdinsight-hbrest restart
```

此命令将停止同一主机上的 HBase 区域服务器。 可以通过 Ambari 手动启动 HBase 区域服务器，或让 Ambari 自动重新启动功能自动恢复 HBase 区域服务器。

如果此问题仍然存在，则可以安装以下缓解脚本作为 CRON 作业，该作业在每个工作节点上每5分钟运行一次。 此缓解脚本对 REST 服务进行 ping 操作，并在 REST 服务没有响应的情况下重启它。

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
