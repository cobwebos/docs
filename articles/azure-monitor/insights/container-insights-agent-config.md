---
title: 为容器配置 Azure Monitor 代理数据收集 |Microsoft Docs
description: 本文介绍如何配置容器代理的 Azure Monitor，以控制 stdout/stderr 和环境变量日志收集。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 0d654dc05668a71b0fe69de32e5c09f8936951f8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951575"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>为容器 Azure Monitor 配置代理数据收集

容器 Azure Monitor 用于从容器化代理中收集从容器工作负载部署到 Azure Kubernetes 服务（AKS）上托管的 Kubernetes 群集的 stdout、stderr 和环境变量。 可以通过创建自定义 Kubernetes ConfigMaps 来配置代理数据收集设置，以控制此体验。 

本文演示如何基于要求创建 ConfigMap 和配置数据收集。

## <a name="configmap-file-settings-overview"></a>ConfigMap 文件设置概述

提供了一个模板 ConfigMap 文件，可让你轻松地使用自定义项对其进行编辑，而无需从头开始创建。 在开始之前，应查看有关[ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)的 Kubernetes 文档，并熟悉如何创建、配置和部署 ConfigMaps。 这将允许你筛选每个命名空间或整个群集中的 stderr 和 stdout，以及在群集中的所有 pod/节点上运行的任何容器的环境变量。

>[!IMPORTANT]
>从容器工作负载收集 stdout、stderr 和环境变量所支持的最低代理版本为 ciprod06142019 或更高版本。 若要验证您的代理版本，请从 "**节点**" 选项卡中选择一个节点，然后在 "**代理图像标记**" 属性的 "属性" 窗格中说明值。 有关代理版本和每个版本中包含的内容的其他信息，请参阅[代理发行说明](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)。

### <a name="data-collection-settings"></a>数据收集设置

下面是可以配置以控制数据收集的设置。

|密钥 |数据类型 |Value |描述 |
|----|----------|------|------------|
|`schema-version` |String （区分大小写） |v1 |这是分析此 ConfigMap 时代理使用的架构版本。 当前支持的架构版本为 v1。 在计算 ConfigMap 时，不支持修改此值，将拒绝此值。|
|`config-version` |字符串 | | 支持在源代码管理系统/存储库中跟踪此配置文件的版本。 允许的最大字符数为10，所有其他字符都将被截断。 |
|`[log_collection_settings.stdout] enabled =` |布尔 | true 或 false | 这会控制是否启用 stdout 容器日志收集。 如果设置为 `true` 并且没有为 stdout 日志收集排除命名空间（`log_collection_settings.stdout.exclude_namespaces` 设置为），则将从群集中的所有 pod/节点上的所有容器中收集 stdout 日志。 如果未在 ConfigMaps 中指定，则默认值为 `enabled = true`。 |
|`[log_collection_settings.stdout] exclude_namespaces =`|字符串 | 逗号分隔的数组 |将不收集其 stdout 日志的 Kubernetes 命名空间的数组。 仅当 `log_collection_settings.stdout.enabled` 设置为 `true`时，此设置才有效。 如果未在 ConfigMap 中指定，则默认值为 `exclude_namespaces = ["kube-system"]`。|
|`[log_collection_settings.stderr] enabled =` |布尔 | true 或 false |这会控制是否已启用 stderr 容器日志收集。 如果设置为 `true` 并且没有为 stdout 日志收集（`log_collection_settings.stderr.exclude_namespaces` 设置）排除命名空间，则将从群集中的所有 pod/节点上的所有容器中收集 stderr 日志。 如果未在 ConfigMaps 中指定，则默认值为 `enabled = true`。 |
|`[log_collection_settings.stderr] exclude_namespaces =` |字符串 |逗号分隔的数组 |将不收集 stderr 日志的 Kubernetes 命名空间的数组。 仅当 `log_collection_settings.stdout.enabled` 设置为 `true`时，此设置才有效。 如果未在 ConfigMap 中指定，则默认值为 `exclude_namespaces = ["kube-system"]`。 |
| `[log_collection_settings.env_var] enabled =` |布尔 | true 或 false | 此设置控制群集中的所有 pod/节点上的环境变量集合，如果 ConfigMaps 中未指定，则默认为 `enabled = true`。 如果已全局启用了环境变量的集合，则可以通过将环境变量设置 `AZMON_COLLECT_ENV` 为 False，将环境变量设置为**False** ，方法是使用 Dockerfile 设置，或在**env：** 节下的[Pod 的配置文件](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)中。 如果已全局禁用环境变量的集合，则无法为特定容器启用收集（也就是说，可在容器级别应用的唯一重写是在全局启用时禁用集合）。 |

ConfigMaps 是一个全局列表，只能有一个 ConfigMap 应用于代理。 不能将其他 ConfigMaps overruling 到集合中。

## <a name="configure-and-deploy-configmaps"></a>配置和部署 ConfigMaps

执行以下步骤，配置 ConfigMap 配置文件并将其部署到群集。

1. [下载](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)模板 ConfigMap yaml 文件并将其另存为容器 azm-agentconfig. yaml。  

2. 编辑包含自定义项的 ConfigMap yaml 文件，以收集 stdout、stderr 和/或环境变量。

    - 若要排除 stdout 日志收集的特定命名空间，可以使用以下示例配置键/值： `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`。
    
    - 若要禁用特定容器的环境变量集合，请将键/值 `[log_collection_settings.env_var] enabled = true` 设置为全局启用变量集合，然后按照[此处](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container)的步骤完成特定容器的配置。
    
    - 若要禁用 stderr 日志收集群集，可以使用以下示例配置密钥/值： `[log_collection_settings.stderr] enabled = false`。

3. 通过运行以下 kubectl 命令创建 ConfigMap： `kubectl apply -f <configmap_yaml_file.yaml>`。
    
    示例：`kubectl apply -f container-azm-ms-agentconfig.yaml`。 
    
    在生效之前，配置更改可能需要几分钟才能完成，并且群集中的所有 omsagent 箱都将重新启动。 重新启动是所有 omsagent pod 的滚动重启，而不是同时重新启动。 重新启动完成后，会显示一条类似于以下内容的消息，其中包括 result： `configmap "container-azm-ms-agentconfig" created`。

## <a name="verify-configuration"></a>验证配置 

若要验证是否已成功应用配置，请使用以下命令从代理 pod 查看日志： `kubectl logs omsagent-fdf58 -n=kube-system`。 如果 omsagent pod 出现配置错误，输出将显示类似于以下内容的错误：

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

还可以查看与应用配置更改相关的错误。 下列选项可用于对配置更改执行额外的故障排除：

- 使用同一个 `kubectl logs` 命令从代理 pod 日志。 

- 从实时日志。 实时日志显示类似于以下内容的错误：

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Log Analytics 工作区中的**KubeMonAgentEvents**表。 数据每小时发送一次，并出现配置错误的*错误*严重性。 如果没有错误，则表中的条目将包含带有严重性*信息*的数据，而不报告任何错误。 **Tags**属性包含有关在其上发生错误的 pod 和容器 ID 的详细信息，以及最后一个小时内的第一个匹配项和最后一个匹配项。

错误会阻止 omsagent 分析文件，从而导致重新启动并使用默认配置。 更正 ConfigMap 中的错误后，请通过运行以下命令，保存 yaml 文件并应用更新的 ConfigMaps： `kubectl apply -f <configmap_yaml_file.yaml`。

## <a name="applying-updated-configmap"></a>应用更新的 ConfigMap

如果已将 ConfigMap 部署到群集，并且想要使用较新的配置对其进行更新，则可以编辑以前使用的 ConfigMap 文件，然后使用与之前相同的命令进行应用，`kubectl apply -f <configmap_yaml_file.yaml`。

在生效之前，配置更改可能需要几分钟才能完成，并且群集中的所有 omsagent 箱都将重新启动。 重新启动是所有 omsagent pod 的滚动重启，而不是同时重新启动。 重新启动完成后，会显示一条类似于以下内容的消息，其中包括 result： `configmap "container-azm-ms-agentconfig" updated`。

## <a name="verifying-schema-version"></a>验证架构版本

支持的配置架构版本在 omsagent pod 上以 pod 注释（架构版本）的形式提供。 可以通过以下 kubectl 命令查看它们： `kubectl describe pod omsagent-fdf58 -n=kube-system`

输出将显示类似于以下内容的注释架构版本：

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

- 容器 Azure Monitor 不包含一组预定义的警报。 查看[使用容器 Azure Monitor 创建性能警报](container-insights-alerts.md)，了解如何为高 CPU 和内存使用率创建建议警报，以支持 DevOps 或操作过程和过程。

- 启用监视功能可收集 AKS 或混合群集的运行状况和资源利用率，以及在这些群集上运行的工作负荷，了解[如何使用](container-insights-analyze.md)容器 Azure Monitor。

- 查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，了解预定义的查询和示例，以便对群集进行警报、可视化或分析。
