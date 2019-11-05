---
title: 配置用于容器的 Azure Monitor 的代理数据收集 | Microsoft Docs
description: 本主题介绍如何配置用于容器的 Azure Monitor 代理，以控制 stdout/stderr 和环境变量日志收集。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: deab16f3b80ada12a7167e90922dc38f3012be91
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73478692"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>配置用于容器的 Azure Monitor 的代理数据收集

用于容器的 Azure Monitor 通过容器化的代理，从托管在 Azure Kubernetes 服务 (AKS) 上的托管 Kubernetes 群集中部署的容器工作负荷收集 stdout、stderr 和环境变量。 可以创建一个自定义的 Kubernetes ConfigMap 用于控制此体验，以配置代理数据收集设置。 

本文演示如何根据要求创建 ConfigMap 和配置数据收集。

## <a name="configmap-file-settings-overview"></a>ConfigMap 文件设置概述

我们已提供一个模板 ConfigMap 文件，你可以使用自定义内容轻松编辑此文件，而无需从头开始创建。 在开始之前，请查看有关 [ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) 的 Kubernetes 文档，并熟悉如何创建、配置和部署 ConfigMap。 这样，就可以按命名空间或者在整个群集中筛选 stderr 和 stdout，以及筛选在所有群集 pod/节点中运行的任何容器的环境变量。

>[!IMPORTANT]
>支持从容器工作负荷收集 stdout、stderr 和环境变量的最低代理版本为 ciprod06142019 或以上。 若要验证代理版本，请在“节点”选项卡中选择一个节点，然后在属性窗格中记下“代理映像标记”属性的值。 有关代理版本和每个版本中包含的内容的其他信息，请参阅[代理发行说明](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)。

### <a name="data-collection-settings"></a>数据收集设置

下面是可以配置的用于控制数据收集的设置。

|键 |数据类型 |值 |说明 |
|----|----------|------|------------|
|`schema-version` |字符串（区分大小写） |v1 |这是代理在分析 ConfigMap 时使用的架构版本。 当前支持的架构版本为 v1。 不支持修改此值，评估 ConfigMap 时会拒绝修改的值。|
|`config-version` |String | | 支持在源代码管理系统/存储库中跟踪此配置文件的版本。 允许的最大字符数为 10，所有其他字符将会截掉。 |
|`[log_collection_settings.stdout] enabled =` |布尔值 | true 或 false | 此设置控制是否启用 stdout 容器日志收集。 如果设置为 `true` 且未在 stdout 日志收集中排除任何命名空间（下面的 `log_collection_settings.stdout.exclude_namespaces` 设置），则会从所有群集 pod/节点中的所有容器收集 stdout 日志。 如果未在 ConfigMap 中指定，默认值为 `enabled = true`。 |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | 逗号分隔的数组 |不收集其 stdout 日志的 Kubernetes 命名空间数组。 仅当 `log_collection_settings.stdout.enabled` 设置为 `true` 时，此设置才会生效。 如果未在 ConfigMap 中指定，默认值为 `exclude_namespaces = ["kube-system"]`。|
|`[log_collection_settings.stderr] enabled =` |布尔值 | true 或 false |此设置控制是否启用 stderr 容器日志收集。 如果设置为 `true` 且未在 stdout 日志收集中排除任何命名空间（`log_collection_settings.stderr.exclude_namespaces` 设置），则会从所有群集 pod/节点中的所有容器收集 stderr 日志。 如果未在 ConfigMap 中指定，默认值为 `enabled = true`。 |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |逗号分隔的数组 |不收集其 stderr 日志的 Kubernetes 命名空间数组。 仅当 `log_collection_settings.stdout.enabled` 设置为 `true` 时，此设置才会生效。 如果未在 ConfigMap 中指定，默认值为 `exclude_namespaces = ["kube-system"]`。 |
| `[log_collection_settings.env_var] enabled =` |布尔值 | true 或 false | 此设置控制群集中所有 pod/节点上的环境变量集合，如果 ConfigMaps 中未指定，则默认为 `enabled = true`。 如果已全局启用了环境变量的集合，则可以通过将环境变量设置 `AZMON_COLLECT_ENV` 为 False，将环境变量设置为**False** ，方法是使用 Dockerfile 设置，[或者在](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)**env：** 部分。 如果已全局禁用环境变量的集合，则无法为特定容器启用收集（也就是说，可在容器级别应用的唯一重写是在全局启用时禁用集合）。 |

ConfigMaps 是一个全局列表，只能有一个 ConfigMap 应用于代理。 不能将其他 ConfigMaps overruling 到集合中。

## <a name="configure-and-deploy-configmaps"></a>使用 ConfigMap 进行配置和部署

执行以下步骤，以配置 ConfigMap 配置文件并将其部署到群集。

1. [下载](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)模板 ConfigMap yaml 文件，并将其保存为 container-azm-ms-agentconfig.yaml。  

2. 编辑包含自定义项的 ConfigMap yaml 文件，以收集 stdout、stderr 和/或环境变量。

    - 若要排除特定命名空间的 stdout 日志收集，可以参考以下示例配置键/值：`[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`。
    
    - 若要禁用特定容器的环境变量收集，请设置键/值 `[log_collection_settings.env_var] enabled = true` 以全局启用变量收集，然后遵循[此处](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container)所述的步骤完成特定容器的配置。
    
    - 若要在群集范围禁用 stderr 日志收集，请参考以下示例配置键/值：`[log_collection_settings.stderr] enabled = false`。

3. 运行以下 kubectl 命令创建 ConfigMap：`kubectl apply -f <configmap_yaml_file.yaml>`。
    
    示例：`kubectl apply -f container-azm-ms-agentconfig.yaml`。 
    
    配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，系统会显示包含结果的消息，如下所示：`configmap "container-azm-ms-agentconfig" created`。

4. 运行以下 kubectl 命令创建 ConfigMap：`kubectl apply -f <configmap_yaml_file.yaml>`。
    
    示例：`kubectl apply -f container-azm-ms-agentconfig.yaml`。 
    
    配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，系统会显示包含结果的消息，如下所示：`configmap "container-azm-ms-agentconfig" created`。

## <a name="verify-configuration"></a>验证配置 

若要验证是否已成功应用配置，请使用以下命令查看代理 pod 的日志：`kubectl logs omsagent-fdf58 -n=kube-system`。 如果 omsagent pod 存在配置错误，输出中会显示如下所示的错误：

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

错误阻止了 omsagent 分析文件，导致其重启并使用默认配置。 更正 ConfigMap 中的错误后，保存 yaml 文件，并运行以下命令应用已更新的 ConfigMap：`kubectl apply -f <configmap_yaml_file.yaml`。

## <a name="applying-updated-configmap"></a>应用已更新的 ConfigMap

如果你已将 ConfigMap 部署到群集，但想要使用较新的配置更新 ConfigMap，可以编辑以前用过的 ConfigMap 文件，然后使用前面提到的相同命令 `kubectl apply -f <configmap_yaml_file.yaml` 应用该文件。

配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，系统会显示包含结果的消息，如下所示：`configmap "container-azm-ms-agentconfig" updated`。

## <a name="verifying-schema-version"></a>验证架构版本

omsagent pod 上以 pod 注释 (schema-versions) 的形式提供了支持的配置架构版本。 可以使用以下 kubectl 命令查看版本：`kubectl describe pod omsagent-fdf58 -n=kube-system`

输出中会显示如下所示的包含注释架构版本的内容：

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

- 用于容器的 Azure Monitor 不包含预定义的警报集。 请查看[使用用于容器的 Azure Monitor 创建性能警报](container-insights-alerts.md)，了解如何针对高 CPU 和内存利用率创建建议的警报以支持 DevOps 或操作流程和过程。

- 启用监视功能可收集 AKS 或混合群集的运行状况和资源利用率，以及在这些群集上运行的工作负荷，了解[如何使用](container-insights-analyze.md)容器 Azure Monitor。

- 请参阅[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看预定义的查询，以及用于发警报、可视化或分析群集的评估或自定义示例。
