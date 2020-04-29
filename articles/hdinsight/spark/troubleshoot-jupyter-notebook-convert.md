---
title: 无法在 Azure HDInsight 中创建 Jupyter 笔记本
description: 介绍了与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: 61e7cd8d37108b8f4eea88c4f6b6b2a8cdbfd605
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77186802"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>无法在 Azure HDInsight 中创建 Jupyter 笔记本

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

启动 Jupyter 笔记本时，会收到一条错误消息，其中包含：

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>原因

版本不匹配。

## <a name="resolution"></a>解决方法

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到群集。 编辑以下命令，将 CLUSTERNAME 替换为群集的名称，然后输入该命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 通过执行以下命令打开 `_version.py`：

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. 将 **5** 更改为 **4**，使修改后的行如下所示：

    ```python
    version_info = (4, 0, 3)
    ```

    通过输入 **Ctrl + X**、**Y**、**Enter** 来保存更改。

1. 在 Web 浏览器中导航到 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER`，其中的 `CLUSTERNAME` 是群集的名称。

1. 重启 Jupyter 服务。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
