---
title: Azure 红帽开放Shift 4.3 的 Azure 监视器集成
description: 了解如何在 Microsoft Azure 红帽开放Shift 群集上启用 Azure 监视器。
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082840"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Azure 红帽开放Shift 4.3 的 Azure 监视器集成

> [!IMPORTANT] 
> 请注意，Azure 红帽 OpenShift 4.3 目前仅在美国东部提供私人预览版。 专用预览接受仅通过邀请。 在尝试启用此功能之前，请务必注册订阅[：Azure 红帽开放移位专用预览注册](https://aka.ms/aro-preview-register)

> [!NOTE]
> 预览功能是自助服务，提供时和可用，不在服务级别协议 （SLA） 和有限保修中。 因此，这些功能不适合生产用途。

本文介绍如何为 OpenShift 4.3 群集启用 Azure 监视器的专用预览，这些群集托管在 prem 上或任何云环境中。 相同的说明也适用于启用 Azure 红帽 OpenShift （ARO） 4.3 群集的监视。  

## <a name="prerequisites"></a>先决条件

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [头盔 3](https://helm.sh/docs/intro/install/)
- 访问库贝内斯群集的库贝康格
- Azure 订阅的访问权限
- 访问 OpenShift 4.3 群集以安装容器头盔图的 Azure 监视器
- Azure 订阅上的最小参与者 RBAC 角色权限  
- 监视代理需要以下出站端口 - 域将监视数据发送到 Azure 监视器后端（如果代理/防火墙阻止）：
  - +.ods.opinsights.azure.com 443
  - +.oms.opinsights.azure.com 443
  - *.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>登记

> [!TIP]
> 该脚本使用 bash 4 功能，因此请确保您的 bash 是最新的。 您可以使用 检查当前版本。 `bash --version`

### <a name="download-the-onboarding-script"></a>下载载入脚本

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

使用库伯奈斯群集的 azure 订阅 Id、工作区区域、群集名称和上下文执行以下脚本。

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

例如：

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>配置代理数据收集

默认情况下，监视代理收集除 kube 系统之外在所有命名空间中运行的所有容器的 [stdout; sderr] 容器日志。  如果要配置特定于特定命名空间或命名空间的容器日志集合，可以参考[容器见解代理配置](../azure-monitor/insights/container-insights-agent-config.md)。 在这里，您可以使用配置映射配置具有所需数据收集设置的监视代理。

## <a name="configure-scraping-of-prometheus-metrics"></a>配置普罗米杜斯指标的刮擦

容器的 Azure 监视器会刮掉 Prometheus 指标并引入 Azure 监视器后端。 有关如何配置普罗米塞斯刮擦的说明，请参阅[容器见解普罗米塞斯配置](../azure-monitor/insights/container-insights-prometheus-integration.md)。

成功加入后，导航到[混合监视](https://aka.ms/azmon-containers-hybrid)，并将"**全部"** 选择"环境"以查看新加入的 OpenShift v4 群集。

## <a name="disable-monitoring"></a>禁用监视

如果要禁用监视，可以使用以下命令删除容器蠕默监视器图表，以停止将监视数据收集和引入到 Azure 监视器中，以访问容器后端。

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>更新监视

使用相同的参数重新运行[载入](#onboarding)脚本，以更新到最新的 Helm 图表。

## <a name="after-successful-onboarding"></a>成功加入后

导航到[混合监视](https://aka.ms/azmon-containers-hybrid)，您可以在 **"监视群集"** 选项卡中看到新启用的 OpenShift/ARO v4 群集状态。在那里，您可以通过单击 **"群集"** 列深入了解指标、库存和日志等。

## <a name="supported-features"></a>支持的功能

有关支持的特性和功能的详细信息，请参阅[容器见解概述](../azure-monitor/insights/container-insights-overview.md)。

联系我们，askcoin@microsoft.com了解反馈和问题。

## <a name="next-steps"></a>后续步骤

要了解有关监视的更多详细信息，请参阅：
- [容器洞察概述](../azure-monitor/insights/container-insights-overview.md)

- [日志查询概述](../azure-monitor/log-query/log-query-overview.md)
