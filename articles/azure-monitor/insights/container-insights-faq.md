---
title: 用于容器的 Azure Monitor 常见问题解答 | Microsoft Docs
description: 用于容器的 Azure Monitor 是用于监视 Azure 中的 AKS群集和容器实例的运行状况的解决方案。 本文将解答一些常见问题。
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/02/2019
ms.author: magoedte
ms.openlocfilehash: 3644b40311c037df800eb89ca26d1285fbf1e082
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68741510"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>用于容器的 Azure Monitor 常见问题解答

本 Microsoft 常见问题解答列出了用于容器的 Azure Monitor 的常见问题。 如果对该解决方案还有其他任何问题，请访问[论坛](https://feedback.azure.com/forums/34192--general-feedback)并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>是否可以监视包含 Azure Monitor 容器的 AKS-engine 群集？

容器 Azure Monitor 支持监视部署到 Azure 上托管的 AKS (以前称为 ACS 引擎) 群集的容器工作负荷。 有关为此方案启用监视所需步骤的详细信息和概述, 请参阅将[Azure Monitor 用于 AKS 的容器](https://github.com/microsoft/OMS-docker/tree/aks-engine)。

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>为何 Log Analytics 工作区中不显示数据？

如果在每天的某个时间，在 Log Analytics 工作区中看不到任何数据，则可能已达到 500 MB 的默认限制或为了控制每天要收集的数据量而指定的每日上限。 如果当天的限制已达到，则数据收集将停止，并且仅在第二天恢复。 若要查看数据使用情况，并根据预期的使用模式更新为不同的定价层，请参阅[日志数据使用情况和成本](../platform/manage-cost-storage.md)。 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory 表中指定的容器状态是什么？

ContainerInventory 表包含已停止和正在运行的容器的信息。 此表由代理中的一个工作流进行填充，该工作流查询 Docker 中的所有容器（正在运行的和已停止的），并将该数据转发到 Log Analytics 工作区。
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>如何解决“缺少订阅注册”错误？

如果收到“缺少 Microsoft.OperationsManagement 的订阅注册”错误，则可以通过在定义了工作区的订阅中注册资源提供程序 **Microsoft.OperationsManagement** 来解决该错误。 可以在[此处](../../azure-resource-manager/resource-manager-register-provider-errors.md)找到介绍如何执行此操作的文档。

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>是否支持启用了 RBAC 的 AKS 群集？

容器监视解决方案不支持 RBAC，但用于容器的 Azure Monitor 支持 RBAC。 在显示这些群集的数据的边栏选项卡上，解决方案详细信息页可能不会显示正确的信息。

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>如何通过 Helm 为 kube-system 命名空间中的容器启用日志收集？

默认情况下，kube-system 命名空间中的容器的日志收集被禁用。 可以通过在 omsagent 上设置一个环境变量来启用日志收集。 有关详细信息，请参阅[用于容器的 Azure Monitor](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub 页。 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>如何将 omsagent 更新为最新发布的版本？

若要了解如何升级代理，请参阅[代理管理](container-insights-manage-agent.md)。

## <a name="how-do-i-enable-multi-line-logging"></a>如何启用多行日志记录？

当前，用于容器的 Azure Monitor 不支持多行日志记录，但有可用的变通方法。 你可以将所有服务配置为以 JSON 格式进行写入，然后 Docker/Moby 会在单个行上写入它们。

例如，可以将你的日志包装为一个 JSON 对象，如示例 node.js 应用程序的以下示例中所示：

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

在你查询时，此数据将类似于用于日志的 Azure Monitor 中的以下示例：

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

有关此问题的详细状况，请查看以下 [github 链接](https://github.com/moby/moby/issues/22920)。

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>如何解决启用实时日志时遇到的 Azure AD 错误？ 

你可能会看到以下错误：**在请求中指定的回复 URL 与为应用程序“<应用程序 ID\>”配置的回复 URL 不匹配**。 此问题的解决方法可在[如何使用用于容器的 Azure Monitor 实时查看容器日志](container-insights-live-logs.md#configure-aks-with-azure-active-directory)一文中找到。 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>为何在载入后不能升级群集？

如果为 AKS 群集启用用于容器的 Azure Monitor 后，删除了该群集将其数据发送到的 Log Analytics 工作区，则尝试升级该群集时，该操作将会失败。 若要解决这一问题，必须禁用监视，然后重新启用该监视，同时引用订阅中的另一个有效工作区。 当你尝试再次升级群集时，该升级操作应进行处理并成功完成。  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>需要为代理打开哪些端口和域，或将哪些端口和域加入允许列表？
- *.ods.opinsights.azure.com   443
- *.oms.opinsights.azure.com   443
- *.blob.core.windows.net      443
- dc.services.visualstudio.com 443

## <a name="next-steps"></a>后续步骤

若要开始监视 AKS 群集，请查看[如何载入用于容器的 Azure Monitor](container-insights-onboard.md) 以了解启用监视的要求和可用方法。 
