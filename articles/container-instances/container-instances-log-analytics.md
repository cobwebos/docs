---
title: 收集 & 分析资源日志
description: 了解如何从 Azure 容器实例中的容器组将资源日志和事件数据发送到 Azure Monitor 日志
ms.topic: article
ms.date: 01/08/2020
ms.author: danlep
ms.openlocfilehash: 304e98fff386911b878877d2f03d489d0eef5dd7
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770537"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>用 Azure Monitor 日志记录容器组和实例

Log Analytics 工作区提供了一个集中的位置，用于存储和查询仅来自 Azure 资源的日志数据，还提供了其他云中的本地资源和资源。 Azure 容器实例提供内置支持，支持将日志和事件数据发送到 Azure Monitor 日志。

若要将容器组日志和事件数据发送到 Azure Monitor 日志，请在创建容器组时指定现有 Log Analytics 工作区 ID 和工作区密钥。 以下部分介绍如何创建启用日志记录的容器组以及如何查询日志。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> 目前，只能将事件数据从 Linux 容器实例发送到 Log Analytics。

## <a name="prerequisites"></a>必备组件

若要在容器实例中启用日志记录，需具备以下条件：

* [Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli)（或 [Cloud Shell](/azure/cloud-shell/overview)）

## <a name="get-log-analytics-credentials"></a>获取 Log Analytics 凭据

Azure 容器实例需要权限才能向 Log Analytics 工作区发送数据。 若要授予该权限并启用日志记录，必须在创建容器组时提供 Log Analytics 工作区 ID 和一个密钥（主密钥或辅助密钥）。

若要获取 Log Analytics 工作区 ID 和主密钥，请执行以下操作：

1. 在 Azure 门户中导航到 Log Analytics 工作区
1. 在“设置”下，选择“高级设置”
1. 选择“连接的源” > “Windows 服务器”（或“Linux 服务器”--二者的 ID 和密钥相同）
1. 记下以下内容：
   * **工作区 ID**
   * **主密钥**

## <a name="create-container-group"></a>创建容器组

有了 Log Analytics 工作区 ID 和主密钥以后，即可创建启用日志记录的容器组。

下面的示例演示了两种创建容器组（由单个[fluentd][fluentd]容器组成）的方法： Azure CLI，以及使用 YAML 模板 Azure CLI。 Fluentd 容器将生成多行输出，其默认配置为。 由于该输出发送到 Log Analytics 工作区，因此适用于演示如何查看和查询日志。

### <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

若要使用 Azure CLI 进行部署，请在 [az container create][az-container-create] 命令中指定 `--log-analytics-workspace` 和 `--log-analytics-workspace-key` 参数。 在运行下面的命令之前，请将两个工作区值替换为在前面的步骤中获得的值（并更新资源组名称）。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>使用 YAML 进行部署

如果喜欢使用 YAML 部署容器组，请使用此方法。 下面的 YAML 定义包含单个容器的容器组。 将 YAML 复制到一个新文件中，然后将 `LOG_ANALYTICS_WORKSPACE_ID` 和 `LOG_ANALYTICS_WORKSPACE_KEY` 替换为在前面的步骤中获得的值。 将该文件保存为 **deploy-aci.yaml**。

```yaml
apiVersion: 2018-10-01
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

接下来，执行下面的命令，以部署该容器组。 将 `myResourceGroup` 替换为订阅中的资源组（或者先创建名为“myResourceGroup”的资源组）：

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

在发出命令以后，很快就会收到来自 Azure 的响应，其中包含部署详细信息。

## <a name="view-logs"></a>查看日志

部署容器组以后，可能需要等待数分钟（最多 10 分钟），第一个日志条目才会显示在 Azure 门户中。 若要查看 `ContainerInstanceLog_CL` 表中的容器组日志，请执行以下操作：

1. 在 Azure 门户中导航到 Log Analytics 工作区
1. 在“常规”下，选择“日志”  
1. 键入以下查询：`ContainerInstanceLog_CL | limit 50`
1. 选择“运行”

此时应看到查询显示了多个结果。 如果起初没有看到任何结果，请等待几分钟，然后选择“运行”按钮，再次执行查询。 默认情况下会以“表”的形式显示日志条目。 然后即可展开某一行来查看单个日志条目的内容。

![Azure 门户中的“日志搜索”结果][log-search-01]

## <a name="view-events"></a>查看活动

还可以在 Azure 门户中查看容器实例的事件。 事件包括实例的创建时间和启动时间。 若要查看 `ContainerEvent_CL` 表中的事件数据，请执行以下操作：

1. 在 Azure 门户中导航到 Log Analytics 工作区
1. 在“常规”下，选择“日志”  
1. 键入以下查询：`ContainerEvent_CL | limit 50`
1. 选择“运行”

此时应看到查询显示了多个结果。 如果起初没有看到任何结果，请等待几分钟，然后选择“运行”按钮，再次执行查询。 默认情况下会以“表”的形式显示条目。 然后即可展开某一行来查看单个条目的内容。

![Azure 门户中的“事件搜索”结果][log-search-02]

## <a name="query-container-logs"></a>查询容器日志

Azure Monitor 日志包含全面的[查询语言][query_lang]，用于从可能有数千行的日志输出中拉取信息。

查询的基本结构是一个源表（在本文中为 `ContainerInstanceLog_CL` 或 `ContainerEvent_CL`），后跟一系列以竖线字符 (`|`) 分隔的运算符。 可以将多个运算符链接起来以优化结果和执行高级函数。

若要查看示例查询结果，请将以下查询粘贴到 "查询" 文本框中，然后选择 "**运行**" 按钮以执行查询。 此查询显示其“消息”字段包含“warn”一词的所有日志条目：

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

此外还支持更复杂的查询。 例如，以下查询仅显示“mycontainergroup001”容器组在过去一小时生成的那些日志条目：

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>后续步骤

### <a name="azure-monitor-logs"></a>Azure Monitor 日志

若要详细了解如何在 Azure Monitor 日志中查询日志和配置警报，请参阅：

* [了解 Azure Monitor 日志中的日志搜索](../log-analytics/log-analytics-log-search.md)
* [Azure Monitor 中的统一警报](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>监视容器 CPU 和内存

若要了解如何监视容器实例 CPU 和内存资源，请参阅：

* [监视 Azure 容器实例中的容器资源](container-instances-monitor.md)。

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create