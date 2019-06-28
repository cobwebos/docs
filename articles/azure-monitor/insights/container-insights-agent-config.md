---
title: 为容器数据收集代理配置 Azure Monitor |Microsoft Docs
description: 本主题介绍如何可以配置 Azure 容器代理来控制 stdout/stderr 的监视器和环境变量日志收集。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2019
ms.author: magoedte
ms.openlocfilehash: 1e7506e311c38d87371dd1b65440b6fb41a7ce78
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341651"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>配置容器的 Azure Monitor 的代理数据收集

用于容器的 azure Monitor 收集 stdout、 stderr 和环境变量从部署管理容器化的代理从托管在 Azure Kubernetes 服务 (AKS) 的 Kubernetes 群集的容器工作负荷。 可以通过创建自定义 Kubernetes ConfigMaps 来控制这种体验来配置代理数据收集设置。 本文演示如何创建 configmap 装载和配置数据收集基于你的要求。

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>使用自定义数据集合设置来配置你的群集

模板 configmap 装载文件是提供一个可轻松地编辑它与你的自定义而无需从头开始创建它。 在开始之前，应查看 Kubernetes 文档有关[ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)并熟悉如何创建、 配置和部署 ConfigMaps。 这样，您可以筛选 stderr 和 stdout 每个命名空间或跨整个群集和所有 pod/节点群集之间运行任何容器的环境变量。

>[!IMPORTANT]
>此功能支持的最低代理版本是 microsoft / oms:ciprod06142019 或更高版本。 

### <a name="overview-of-configurable-data-collection-settings"></a>可配置的数据集合设置的概述

以下是可以配置用于控制数据收集的设置。

|密钥 |数据类型 |值 |描述 |
|----|----------|------|------------|
|`schema-version` |字符串 （区分大小写） |v1 |这是分析此 configmap 装载时使用的代理的架构版本。 当前支持的架构版本为 v1。 修改此值不受支持，计算 configmap 装载时都将被拒绝。|
|`config-version` |String | | 支持功能来跟踪源控制系统/存储库中的此配置文件的版本。 允许的最大字符数是 10，并且所有其他字符会被截断。 |
|`[log_collection_settings.stdout] enabled =` |Boolean | true 或 false | 此设置控制启用 stdout 容器日志收集。 如果设置为`true`无命名空间将不会为 stdout 日志收集 (`log_collection_settings.stdout.exclude_namespaces`下面的设置)，在群集中的所有 pod/节点，将从所有容器都收集 stdout 日志。 如果未指定 ConfigMaps 中，默认值是`enabled = true`。 |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | 以逗号分隔的数组 |Kubernetes 命名空间的 stdout 的日志将不会收集的数组。 此设置才有效才`log_collection_settings.stdout.enabled`设置为`true`。 如果未指定 configmap 装载中，默认值是`exclude_namespaces = ["kube-system"]`。|
|`[log_collection_settings.stderr] enabled =` |Boolean | true 或 false |此设置控制启用 stderr 容器日志收集。 如果设置为`true`无命名空间将不会为 stdout 日志收集 (`log_collection_settings.stderr.exclude_namespaces`设置)，群集中的所有 pod/节点，将从所有容器都收集 stderr 日志。 如果未指定 ConfigMaps 中，默认值是`enabled = true`。 |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |以逗号分隔的数组 |Kubernetes 命名空间的 stderr 的日志将不会收集的数组。 此设置才有效才`log_collection_settings.stdout.enabled`设置为`true`。 如果未指定 configmap 装载中，默认值是`exclude_namespaces = ["kube-system"]`。 |
| `[log_collection_settings.env_var] enabled =` |Boolean | true 或 false | 此设置控制如果启用了环境变量集合。 如果设置为`false`，任何环境变量将收集有关所有 pod/节点群集之间运行任何容器。 如果未指定 configmap 装载中，默认值是`enabled = true`。 |

### <a name="configure-and-deploy-configmaps"></a>配置和部署 ConfigMaps

执行以下步骤，若要配置和将 configmap 装载配置文件部署到你的群集。

1. [下载](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)模板 configmap 装载 yaml 文件，并将其保存为容器 azm ms agentconfig.yaml。  
1. 编辑你的自定义使用 configmap 装载 yaml 文件。 

    - 若要排除特定的命名空间为 stdout 日志收集，你可以配置键/值使用下面的示例： `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`。
    - 若要禁用特定容器的环境变量集合，请设置键/值`[log_collection_settings.env_var] enabled = true`全局，启用变量集合，然后按照步骤[此处](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container)特定容器的完成配置。
    - 若要禁用 stderr 日志收集整个群集，配置键/值使用下面的示例： `[log_collection_settings.stderr] enabled = false`。

1. 通过运行以下 kubectl 命令来创建 ConfigMap: `kubectl apply -f <configmap_yaml_file.yaml>`。
    
    示例：`kubectl apply -f container-azm-ms-agentconfig.yaml`。 
    
    配置更改可能需要几分钟才能生效之前, 完成的操作，并将重新启动群集中的所有 omsagent pod。 重新启动，则为所有 omsagent pod 滚动重新启动，不是全部在同一时间重新启动。 完成重新启动后，显示一条消息是类似于以下和包括的结果： `configmap "container-azm-ms-agentconfig" created`。

若要验证是否已成功将配置应用，请使用以下命令来查看从代理 pod 日志： `kubectl logs omsagent-fdf58 -n=kube-system`。 如果有配置错误从 osmagent pod，输出将显示错误类似于下面：

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

错误阻止 omsagent 分析文件，使其重新启动，并使用默认配置。 更正中 configmap 装载错误后，保存 yaml 文件，并通过运行命令应用已更新的 ConfigMaps: `kubectl apply -f <configmap_yaml_file.yaml`。

## <a name="applying-updated-configmap"></a>应用更新 configmap 装载

如果已为群集部署 ConfigMap 并且你想要使用较新配置更新，只需编辑 configmap 装载文件之前使用过，然后将应用与前面一样，使用相同的命令`kubectl apply -f <configmap_yaml_file.yaml`。

配置更改可能需要几分钟才能生效之前, 完成的操作，并将重新启动群集中的所有 omsagent pod。 重新启动，则为所有 omsagent pod 滚动重新启动，不是全部在同一时间重新启动。 完成重新启动后，显示一条消息是类似于以下和包括的结果： `configmap "container-azm-ms-agentconfig" updated`。

## <a name="verifying-schema-version"></a>验证架构版本

受支持的配置架构版本都可用作 omsagent pod 上 pod 批注 （架构版本）。 可以使用以下 kubectl 命令查看它们： `kubectl describe pod omsagent-fdf58 -n=kube-system`

输出会显示类似于以下命令，并批注架构版本：

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>后续步骤

- 若要继续学习如何使用 Azure Monitor 以及如何监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。
- 视图[记录查询示例](container-insights-log-search.md#search-logs-to-analyze-data)若要查看预定义的查询和示例，以评估或自定义的警报、 可视化，或分析你的群集。