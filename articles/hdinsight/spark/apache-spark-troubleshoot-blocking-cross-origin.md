---
title: Jupyter 服务器404由于 Azure HDInsight 中的 "阻止跨源 API" 而出错
description: 由于 Azure HDInsight 中的 "阻止跨源 API"，Jupyter server 404 "找不到" 错误
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 291fc7f385f652005f44c3e0cd52d4929e914989
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736193"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>场景：由于 Azure HDInsight 中的 "阻止跨源 API"，Jupyter server 404 "找不到" 错误

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

当你访问 HDInsight 上的 Jupyter 服务时，你会看到一个错误框，指出 "找不到"。 如果检查 Jupyter 日志，会看到如下所示的内容：

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: hn0-pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

你还可以在 Jupyter 日志中的 "源" 字段中看到一个 IP 地址。

## <a name="cause"></a>原因

此错误可能由以下几项导致：

- 如果已将网络安全组（NSG）规则配置为限制对群集的访问。 使用 NSG 规则限制访问仍可让你使用 IP 地址而不是群集名称直接访问 Apache Ambari 和其他服务。 但是，在访问 Jupyter 时，可能会看到 404 "找不到" 错误。

- 如果你已为 HDInsight 网关提供自定义的 DNS 名称，而`xxx.azurehdinsight.net`不是标准。

## <a name="resolution"></a>分辨率

1. 在以下两个位置修改 jupyter.py 文件：

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. 找到如下所示的行：`NotebookApp.allow_origin='\"https://{2}.{3}\"'`并将其更改为`NotebookApp.allow_origin='\"*\"'`：。

1. 从 Ambari 重新启动 Jupyter 服务。

1. 在`ps aux | grep jupyter`命令提示符下键入内容时，会显示它允许任何 URL 连接到它。

这比我们已有的设置更安全。 但假定对群集的访问受到限制，并且允许从外部访问群集，因为我们 NSG 了。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
