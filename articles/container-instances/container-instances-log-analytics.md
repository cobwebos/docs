---
title: 使用 Azure Log Analytics 进行容器实例日志记录
description: 了解如何将容器输出（STDOUT 和 STDERR）发送到 Azure Log Analytics。
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 06/06/2018
ms.author: marsma
ms.openlocfilehash: a0772d1009021ca64b448710c5353407a5492fae
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809857"
---
# <a name="container-instance-logging-with-azure-log-analytics"></a>使用 Azure Log Analytics 进行容器实例日志记录

Log Analytics 工作区可以集中存储和查询来自 Azure 资源、本地资源以及其他云中的资源的日志数据。 Azure 容器实例提供内置支持，支持将数据发送到 Log Analytics。

若要将容器实例数据发送到 Log Analytics，必须使用 Azure CLI（或 Cloud Shell）和 YAML 文件创建容器组。 以下部分介绍如何创建启用了日志记录的容器组和查询日志。

## <a name="prerequisites"></a>先决条件

若要在容器实例中启用日志记录，需具备以下条件：

* [Log Analytics 工作区](../log-analytics/log-analytics-quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli)（或 [Cloud Shell](/azure/cloud-shell/overview)）

## <a name="get-log-analytics-credentials"></a>获取 Log Analytics 凭据

Azure 容器实例需要权限才能向 Log Analytics 工作区发送数据。 若要授予该权限并启用日志记录，必须在创建容器组时提供 Log Analytics 工作区 ID 和一个密钥（主密钥或辅助密钥）。

若要获取 Log Analytics 工作区 ID 和主密钥，请执行以下操作：

1. 在 Azure 门户中导航到 Log Analytics 工作区
1. 选择“设置”下的“高级设置”
1. 选择“连接的源” > “Windows 服务器”（或“Linux 服务器”--二者的 ID 和密钥相同）
1. 记下以下内容：
   * **工作区 ID**
   * **主密钥**

## <a name="create-container-group"></a>创建容器组

有了 Log Analytics 工作区 ID 和主密钥以后，即可创建启用日志记录的容器组。 以下示例创建的容器组包含单个 [fluentd][fluentd] 容器。 Fluentd 容器在其默认配置中生成多行输出。 由于该输出发送到 Log Analytics 工作区，因此适用于演示如何查看和查询日志。

首先，将以下 YAML（用于定义包含单个容器的容器组）复制到新文件中。 将 `LOG_ANALYTICS_WORKSPACE_ID` 和 `LOG_ANALYTICS_WORKSPACE_KEY` 替换为在上一步获得的值，然后将文件另存为 **deploy-aci.yaml**。

```yaml
apiVersion: 2018-06-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

接下来执行以下命令，以便部署容器组；将 `myResourceGroup` 替换为订阅中的资源组（或者先创建名为“myResourceGroup”的资源组）：

```azurecli-interactive
az container create -g myResourceGroup -n mycontainergroup001 -f deploy-aci.yaml
```

在发出命令以后，很快就会收到来自 Azure 的响应，其中包含部署详细信息。

## <a name="view-logs-in-log-analytics"></a>在 Log Analytics 中查看日志

部署容器组以后，可能需要等待数分钟（最多 10 分钟），第一个日志条目才会显示在 Azure 门户中。 若要查看容器组的日志，请先打开 Log Analytics 工作区，然后执行以下操作：

1. 在“OMS 工作区”概览中，选择“日志搜索”
1. 在“尝试更多查询”下选择“所有收集的数据”链接。

此时会看到 `search *` 查询显示的多个结果。 如果一开始没有看到任何结果，请等待数分钟，然后选择“运行”按钮，再次执行查询。 默认情况下，日志条目以“列表”视图方式显示--选择“表”即可以更紧致的格式查看日志条目。 然后即可展开某一行来查看单个日志条目的内容。

![Azure 门户中的“日志搜索”结果][log-search-01]

## <a name="query-container-logs"></a>查询容器日志

Log Analytics 包含全面的[查询语言][query_lang]，用于从可能多达数千行的日志输出中拉取信息。

Azure 容器实例日志记录代理将条目发送到 Log Analytics 工作区中的 `ContainerInstanceLog_CL` 表。 查询的基本结构是一个源表 (`ContainerInstanceLog_CL`)，后跟一系列以竖线字符 (`|`) 隔开的运算符。 可以将多个运算符链接起来以优化结果和执行高级函数。

若要查看查询结果，请将以下查询粘贴到查询文本框（位于“显示旧式语言转换器”下面）中，然后选择“运行”按钮以执行该查询。 此查询显示其“消息”字段包含“warn”一词的所有日志条目：

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

此外还支持更复杂的查询。 例如，以下查询仅显示“mycontainergroup001”容器组在过去一小时生成的那些日志条目：

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>后续步骤

### <a name="log-analytics"></a>Log Analytics

若要详细了解如何在 Azure Log Analytics 中查询日志和配置警报，请参阅：

* [了解 Log Analytics 中的日志搜索](../log-analytics/log-analytics-log-search.md)
* [Azure Monitor 中的统一警报](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)

### <a name="monitor-container-cpu-and-memory"></a>监视容器 CPU 和内存

若要了解如何监视容器实例 CPU 和内存资源，请参阅：

* [监视 Azure 容器实例中的容器资源](container-instances-monitor.md)。

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://docs.loganalytics.io/

<!-- LINKS - Internal -->