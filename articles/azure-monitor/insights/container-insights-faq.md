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
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 70712b68cc26a461f702850dbf6064b65b4ff130
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494656"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>用于容器的 Azure Monitor 常见问题解答

本 Microsoft 常见问题解答列出了用于容器的 Azure Monitor 的常见问题。 如果对该解决方案还有其他任何问题，请访问[论坛](https://feedback.azure.com/forums/34192--general-feedback)并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>为什么没有看见数据在我的 Log Analytics 工作区中？

如果你不能以查看在特定时间每天的 Log Analytics 工作区中的任何数据，则表示可能达到 500 MB 的默认限制或每日上限指定若要控制要每日收集的数据量。 上一天达到了限制时，数据收集将停止，并仅在第二天恢复。 若要查看数据的使用情况和更新到根据预计使用情况模式的不同定价层，请参阅[日志数据使用情况和成本](../platform/manage-cost-storage.md)。 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory 表中指定的容器状态有哪些？

ContainerInventory 表包含已停止和正在运行的容器的信息。 此表由代理中的一个工作流进行填充，该工作流查询 Docker 中的所有容器（正在运行的和已停止的），并将该数据转发到 Log Analytics 工作区。
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>如何解决**缺少订阅注册**错误？

如果收到错误**缺少订阅注册为 Microsoft.OperationsManagement**，可以通过注册资源提供程序来解决**Microsoft.OperationsManagement**中在其中定义工作区的订阅。 可以在[此处](../../azure-resource-manager/resource-manager-register-provider-errors.md)找到介绍如何执行此操作的文档。

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>是否有支持 RBAC 启用 AKS 群集中的？

容器监视解决方案不支持 RBAC，但它与用于容器的 Azure Monitor 支持。 在显示这些群集的数据的边栏选项卡上，解决方案详细信息页可能不会显示正确的信息。

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>如何通过 Helm 为 kube-system 命名空间中的容器启用日志收集？

默认情况下，kube-system 命名空间中的容器的日志收集被禁用。 可以通过在 omsagent 上设置一个环境变量来启用日志收集。 有关详细信息，请参阅[容器的 Azure Monitor](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub 页面。 

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

在查询时，此数据将如日志在 Azure Monitor 中的以下示例所示：

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

有关此问题的详细状况，请查看以下 [github 链接](https://github.com/moby/moby/issues/22920)。

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>启用实时日志时，如何解决 Azure AD 错误？ 

你可能会看到以下错误：**答复请求中指定的 url 与应用程序配置的答复 url 不匹配: < 应用程序 ID\>**。 若要解决此问题的解决方案可以在文章中找到[如何查看容器日志实时 Azure monitor 容器](container-insights-live-logs.md#configure-aks-with-azure-active-directory)。 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>为什么不能升级群集载入后？

如果为 AKS 群集的容器启用 Azure Monitor 后，删除 Log Analytics 工作区群集正将其数据发送到，当尝试将群集升级，则将失败。 若要解决此问题，您将需要禁用监视，然后重新启用它引用在你的订阅不同的有效工作区。 当你尝试再次执行群集升级时，它应处理并成功完成。  

## <a name="next-steps"></a>后续步骤

若要开始监视 AKS 群集，请查看[如何载入用于容器的 Azure Monitor](container-insights-onboard.md) 以了解启用监视的要求和可用方法。 