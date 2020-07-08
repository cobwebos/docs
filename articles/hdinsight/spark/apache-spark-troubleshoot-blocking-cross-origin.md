---
title: Jupyter 404 错误-"阻止跨源 API"-Azure HDInsight
description: Azure HDInsight 中的“阻止跨源 API”导致 Jupyter 服务器 404“找不到”错误
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "75894416"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>方案：Azure HDInsight 中的“阻止跨源 API”导致 Jupyter 服务器 404“找不到”错误

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

访问 HDInsight 上的 Jupyter 服务时看到一个指出“找不到”的错误框。 如果检查 Jupyter 日志，会看到如下所示的内容：

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

此外，在 Jupyter 日志中的“源”字段中还可以看到一个 IP 地址。

## <a name="cause"></a>原因

此错误可能由以下几种原因导致：

- 如果已应用网络安全组 (NSG) 规则来限制对群集的访问： 使用 NSG 规则限制访问时，你仍可以使用 IP 地址（而不是群集名称）直接访问 Apache Ambari 和其他服务。 但是，在访问 Jupyter 时，可能会看到 404“找不到”错误。

- 如果为 HDInsight 网关指定了自定义的 DNS 名称而不是标准的 `xxx.azurehdinsight.net`：

## <a name="resolution"></a>解决方法

1. 在以下两个位置修改 jupyter.py 文件：

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. 找到显示了以下内容的行：`NotebookApp.allow_origin='\"https://{2}.{3}\"'` 将其更改为：`NotebookApp.allow_origin='\"*\"'`。

1. 从 Ambari 重启 Jupyter 服务。

1. 在命令提示符下键入 `ps aux | grep jupyter` 后，应会显示允许任何 URL 与该服务建立连接。

这种安全性比现有的设置更低。 但是，它会假设对群集的访问受到限制，并且允许外部的流量连接到群集，因为应用了 NSG。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区与适当的资源（解答、支持人员和专家）相关联来改善客户体验。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 在 Microsoft Azure 订阅中可以访问订阅管理和计费支持；通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
