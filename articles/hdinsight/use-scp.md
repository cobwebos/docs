---
title: 在 Azure HDInsight 中将 SCP 与 Apache Hadoop 配合使用
description: 本文档提供有关使用 ssh 和 scp 命令连接到 HDInsight 的信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: bb90db8a15a872fc28b3d09183228a67edf290e3
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103225"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>在 Azure HDInsight 中将 SCP 与 Apache Hadoop 配合使用

本文介绍如何安全地将文件与 HDInsight 群集传输。

## <a name="copy-files"></a>复制文件

`scp` 实用工具可以用来将文件复制到群集中的单个节点，或者从单个节点进行复制。 例如，以下命令将 `test.txt` 目录从本地系统复制到头主节点：

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

由于未在 `:` 后指定路径，因此会将文件置于 `sshuser` 主目录。

以下示例将 `test.txt` 文件从头主节点上的 `sshuser` 主目录复制到本地系统：

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

`scp` 只能访问群集中单个节点的文件系统。 它不能用于访问群集的 HDFS 兼容存储中的数据。

需要通过 SSH 会话上传要使用的资源时，请使用 `scp`。 例如，上传一个 Python 脚本，然后通过 SSH 会话运行该脚本。

若要了解如何将数据直接加载到兼容 HDFS 的存储中，请参阅以下文档：

* [使用 Azure 存储的 HDInsight](hdinsight-hadoop-use-blob-storage.md)。
* [使用 Azure Data Lake Storage 的 HDInsight](hdinsight-hadoop-use-data-lake-store.md)。

## <a name="next-steps"></a>后续步骤

* [将 SSH 与 HDInsight 配合使用](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [在 HDInsight 中使用边缘节点](hdinsight-apps-use-edge-node.md#access-an-edge-node)
