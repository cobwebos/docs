---
title: 在 Azure HDInsight 中配合使用 SCP 与 Apache Hadoop
description: 本文档介绍如何使用 ssh 和 scp 命令连接到 HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 559746a817442602c76ba91f12c195be1d7f3cc8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82188423"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>在 Azure HDInsight 中配合使用 SCP 与 Apache Hadoop

本文介绍如何安全地与 HDInsight 群集传输文件。

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

`scp` 只能访问群集中单个节点的文件系统。 它不能用来访问适用于群集且兼容 HDFS 的存储中的数据。

需要通过 SSH 会话上传要使用的资源时，请使用 `scp`。 例如，上传一个 Python 脚本，然后通过 SSH 会话运行该脚本。

若要了解如何将数据直接加载到兼容 HDFS 的存储中，请参阅以下文档：

* [使用 Azure 存储的 HDInsight](hdinsight-hadoop-use-blob-storage.md)。
* [使用 Azure Data Lake Storage 的 HDInsight](hdinsight-hadoop-use-data-lake-store.md)。

## <a name="next-steps"></a>后续步骤

* [将 SSH 与 HDInsight 配合使用](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [在 HDInsight 中使用边缘节点](hdinsight-apps-use-edge-node.md#access-an-edge-node)
