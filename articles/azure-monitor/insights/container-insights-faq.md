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
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: 250bf6ede3a54b26b6e9c68850f8e7260020002e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960492"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>用于容器的 Azure Monitor 常见问题解答
本 Microsoft 常见问题解答列出了用于容器的 Azure Monitor 的常见问题。 如果对该解决方案还有其他任何问题，请访问[论坛](https://feedback.azure.com/forums/34192--general-feedback)并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

## <a name="i-am-unable-to-see-any-data-in-the-log-analytics-workspace-at-a-certain-time-everyday-how-do-i-resolve-this"></a>我无法在特定时间日常查看 Log Analytics 工作区中的任何数据。 如何解决此问题？ 

你可能已达到 500 MB 的默认限制或每日上限指定若要控制要每日收集的数据量。 上一天达到了限制时，数据收集将停止，并仅在第二天恢复。 若要查看数据的使用情况和更新到根据预计使用情况模式的不同定价层，请参阅[日志数据使用情况和成本](../platform/manage-cost-storage.md)。 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>ContainerInventory 表中指定了容器的哪些状态？
ContainerInventory 表包含已停止和正在运行的容器的信息。 此表由代理中的一个工作流进行填充，该工作流查询 Docker 中的所有容器（正在运行的和已停止的），并将该数据转发到 Log Analytics 工作区。
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>如何解决与**缺少 Microsoft.OperationsManagement 的订阅注册**相关的错误？
若要解决此错误，请在定义了工作区的订阅中注册资源提供程序 **Microsoft.OperationsManagement**。 可以在[此处](../../azure-resource-manager/resource-manager-register-provider-errors.md)找到介绍如何执行此操作的文档。

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>用于容器的 Azure Monitor 是否支持启用了 RBAC 的 AKS 群集？
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

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>启用实时日志时，如何解决 Azure Active Directory 错误？ 
你可能会看到以下错误：**答复请求中指定的 url 与应用程序配置的答复 url 不匹配: < 应用程序 ID\>**。 若要解决此问题的解决方案可以在文章中找到[如何查看容器日志实时 Azure monitor 容器](container-insights-live-logs.md#configure-aks-with-azure-active-directory)。 

## <a name="next-steps"></a>后续步骤
若要开始监视 AKS 群集，请查看[如何载入用于容器的 Azure Monitor](container-insights-onboard.md) 以了解启用监视的要求和可用方法。 