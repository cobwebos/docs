---
title: 适用于 Azure Red Hat OpenShift 4.3 的 Azure Monitor 集成
description: 了解如何启用 Microsoft Azure Red Hat OpenShift 群集上的 Azure Monitor。
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082840"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>适用于 Azure Red Hat OpenShift 4.3 的 Azure Monitor 集成

> [!IMPORTANT] 
> 请注意，Azure Red Hat OpenShift 4.3 目前仅在美国东部的个人预览版中提供。 仅邀请接受个人预览版。 请确保在尝试启用此功能之前注册订阅： [Azure Red Hat OpenShift 个人预览版](https://aka.ms/aro-preview-register)

> [!NOTE]
> 预览功能是自助服务，按原样提供，并在服务级别协议（SLA）和有限担保中排除。 因此，这些功能并不用于生产。

本文介绍如何为 OpenShift 4.3 群集（托管在本地或任何云环境中）的容器启用 Azure Monitor 专用预览。 同样的说明也适用于启用对 Azure Red Hat OpenShift （ARO）4.3 群集的监视。  

## <a name="prerequisites"></a>必备条件

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3](https://helm.sh/docs/intro/install/)
- 访问 kubernetes 群集的 kubeconfig
- Azure 订阅的访问权限
- 访问 OpenShift 4.3 群集以安装容器的 Azure Monitor Helm 图表
- Azure 订阅的最小参与者 RBAC 角色权限  
- 监视代理要求使用以下出站端口和域将监视数据发送到 Azure Monitor 后端（如果被代理/防火墙阻止）：
  - *. ods.opinsights.azure.com 443
  - *. oms.opinsights.azure.com 443
  - *. blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>登记

> [!TIP]
> 此脚本使用 bash 4 功能，请确保 bash 是最新的。 可以通过 `bash --version`检查当前版本。

### <a name="download-the-onboarding-script"></a>下载载入脚本

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

通过 azureSubscriptionId、工作区区域、clusterName 和 Kubernetes 群集的上下文执行以下脚本。

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

例如：

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>配置代理数据收集

默认情况下，监视代理收集所有命名空间中运行的所有容器（kube 除外）的 {stdout; stderr} 容器日志。  如果要配置特定于特定命名空间或命名空间的容器日志集合，可以参阅[Container Insights 代理配置](../azure-monitor/insights/container-insights-agent-config.md)。 在此处，你可以使用配置映射通过所需的数据收集设置来配置监视代理。

## <a name="configure-scraping-of-prometheus-metrics"></a>配置 Prometheus 指标的抓取

容器的 Azure Monitor 擦除 Prometheus 度量值，并将其引入到 Azure Monitor 后端。 有关如何配置 Prometheus 抓取的说明，请参阅[Container Insights Prometheus 配置](../azure-monitor/insights/container-insights-prometheus-integration.md)。

成功载入后，导航到 "[混合监视](https://aka.ms/azmon-containers-hybrid)"，并选择 **"环境"** 以查看新的载入 OpenShift v4 群集。

## <a name="disable-monitoring"></a>禁用监视

若要禁用监视，可以使用以下命令删除 Helm 的容器的 Azure Monitor，以停止将监视数据收集到容器后端 Azure Monitor 并将其引入。

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>更新监视

如[载入](#onboarding)部分中所述，重新运行载入脚本，并将同一参数更新到最新的 Helm 图表。

## <a name="after-successful-onboarding"></a>成功载入后

导航到[混合监视](https://aka.ms/azmon-containers-hybrid)，你可以在 "**监视的群集**" 选项卡中看到新启用的 OpenShift/ARO v4 群集，其运行状况为运行状态。在那里，可以通过单击**群集**列来了解更深入的见解，如指标、清单和日志。

## <a name="supported-features"></a>支持的功能

有关支持的特性和功能的详细信息，请参阅[容器 Insights 概述](../azure-monitor/insights/container-insights-overview.md)。

有关反馈和问题，请通过 askcoin@microsoft.com 与我们联系。

## <a name="next-steps"></a>后续步骤

若要了解有关监视的详细信息，请参阅：
- [容器见解概述](../azure-monitor/insights/container-insights-overview.md)

- [日志查询概述](../azure-monitor/log-query/log-query-overview.md)
