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
ms.date: 02/18/2019
ms.author: magoedte
ms.openlocfilehash: 27a191bb62ae59aa154167a22c99d3e699f3eb5a
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418349"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>用于容器的 Azure Monitor 常见问题解答
本 Microsoft 常见问题解答列出了用于容器的 Azure Monitor 的常见问题。 如果对该解决方案还有其他任何问题，请访问[论坛](https://feedback.azure.com/forums/34192--general-feedback)并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

## <a name="why-am-i-not-seeing-data-if-log-analytics-workspace-is-configured-with-the-free-pricing-tier"></a>如果 Log Analytics 工作区是使用免费定价层配置的，为什么看不到数据？ 

你可能已经达到了默认的 500MB 限制，或者指定了一个每日上限来控制每天要收集的数据量。 若要查看和管理数据使用情况，请参阅[日志数据使用情况和成本](../platform/manage-cost-storage.md)。 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>ContainerInventory 表中指定了容器的哪些状态？
ContainerInventory 表包含已停止和正在运行的容器的信息。 此表由代理中的一个工作流进行填充，该工作流查询 Docker 中的所有容器（正在运行的和已停止的），并将该数据转发到 Log Analytics 工作区。
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>如何解决与**缺少 Microsoft.OperationsManagement 的订阅注册**相关的错误？
若要解决此错误，请在定义了工作区的订阅中注册资源提供程序 **Microsoft.OperationsManagement**。 可以在[此处](../../azure-resource-manager/resource-manager-register-provider-errors.md)找到介绍如何执行此操作的文档。

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>用于容器的 Azure Monitor 是否支持启用了 RBAC 的 AKS 群集？
此解决方案当前不支持启用了 RBAC 的 AKS 群集。 在显示这些群集的数据的边栏选项卡上，解决方案详细信息页可能不会显示正确的信息。

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>如何通过 Helm 为 kube-system 命名空间中的容器启用日志收集？
默认情况下，kube-system 命名空间中的容器的日志收集被禁用。 可以通过在 omsagent 上设置一个环境变量来启用日志收集。 有关详细信息，请参阅[用于容器的 Azure Monitor](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub 页面。 

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

当你查询该数据时，在用于日志的 Azure Monitor 中，该数据将如下所示：

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

有关此问题的详细状况，请查看以下 [github 链接](https://github.com/moby/moby/issues/22920)。

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>启用实时日志时，如何解决 Azure Active Directory 错误？ 
你可能会看到以下错误：**在请求中指定的回复 URL 与为以下应用程序配置的回复 URL 不匹配:'60b4dec7-5a69-4165-a211-12c40b5c0435'**。 可以在[如何使用用于容器的 Azure Monitor 实时查看容器日志](container-insights-live-logs.md#configure-aks-with-azure-active-directory)一文中找到此问题的修复方法。 

## <a name="next-steps"></a>后续步骤
若要开始监视 AKS 群集，请查看[如何载入用于容器的 Azure Monitor](container-insights-onboard.md) 以了解启用监视的要求和可用方法。 