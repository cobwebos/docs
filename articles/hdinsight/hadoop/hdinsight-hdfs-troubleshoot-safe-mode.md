---
title: 本地 HDFS 在 Azure HDInsight 群集上的安全模式下停滞
description: 本地 HDFS 在 Azure HDInsight 群集上的安全模式下停滞
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/02/2019
ms.openlocfilehash: 2baef0d8c23a8da13c3640d85159dc17b3acc80f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68817155"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>方案:本地 HDFS 在 Azure HDInsight 群集上的安全模式下停滞

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

本地 HDFS 在 Azure HDInsight 群集上停滞在安全模式。 收到如下错误消息:

```
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>原因

HDInsight 群集已减少到非常少的节点数以下或接近 HDFS 复制因子。

## <a name="resolution"></a>解决

1. 通过以下命令报告 HDInsight 群集上的 HDFS 状态:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. 通过以下命令检查 HDInsight 群集上的 HDFS 的完整性:

    ```bash
    hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. 如果确定没有缺失、损坏或正在复制的块，或者这些块可以忽略，请运行以下命令使名称节点退出安全模式：

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系, 通过将 Azure 社区连接到适当的资源来改进客户体验: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
