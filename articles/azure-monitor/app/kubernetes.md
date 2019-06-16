---
title: 使用 Application Insights 监视 Azure Kubernetes 服务 (AKS) 或其他 Kubernetes 托管的应用程序的 Azure Monitor |Microsoft Docs
description: Azure Monitor 使用 Kubernetes 群集上的服务网格技术 Istio 提供针对任何 Kubernetes 托管应用程序的监视。 这样，就可以收集传入和传出群集中运行的 pod 的请求的 Application Insights 相关遥测数据。
services: application-insights
author: tokaplan
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: alkaplan
ms.openlocfilehash: c94d589875195207ec6f71c35ad077cac281fda5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65555837"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Kubernetes 托管应用程序的零检测应用程序监视

> [!IMPORTANT]
> 此功能目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Monitor 现在使用 Kubernetes 群集上的服务网格技术提供针对任何 Kubernetes 托管应用的现成应用程序监视。 默认的 Application Insight 功能包括用于为依赖项建模的[应用程序映射](../../azure-monitor/app/app-map.md)、用于实时监视的[实时指标流](../../azure-monitor/app/live-stream.md)，以及使用[默认仪表板](../../azure-monitor/app/overview-dashboard.md)、[指标资源管理器](../../azure-monitor/platform/metrics-getting-started.md)和[工作簿](../../azure-monitor/app/usage-workbooks.md)生成丰富的可视化效果。 此功能可帮助用户找出选定 Kubernetes 命名空间中所有 Kubernetes 工作负荷的性能瓶颈和故障热点。 Azure Monitor 可以通过 Istio 等技术将现有的服务网格投资变现，在不对应用程序代码进行任何修改的情况下实现自动检测的应用监视。

> [!NOTE]
> 这是在 Kubernetes 上执行应用程序监视的多种方法之一。 还可以使用 [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.md) 来检测 Kubernetes 中托管的任何应用，而无需使用服务网格。 若要监视 Kubernetes 且不使用 SDK 检测应用程序，可以使用以下方法。

## <a name="prerequisites"></a>必备组件

- 一个 [Kubernetes 群集](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads)。
- 可在控制台中访问群集，以运行 *kubectl*。
- 一个 [Application Insights 资源](create-new-resource.md)。
- 有一个服务网格 如果群集中未部署 Istio，你可以了解如何[在 Azure Kubernetes 服务中安装和使用 Istio](https://docs.microsoft.com/azure/aks/istio-install)。

## <a name="capabilities"></a>功能

借助 Kubernetes 托管应用的零检测应用程序监视，可以使用：

- [应用程序映射](../../azure-monitor/app/app-map.md)
- [实时流指标](../../azure-monitor/app/live-stream.md)
- [仪表板](../../azure-monitor/app/overview-dashboard.md)
- [指标资源管理器](../../azure-monitor/platform/metrics-getting-started.md)
- [分布式跟踪](../../azure-monitor/app/distributed-tracing.md)
- [端到端的事务监视](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>安装步骤

为了启用该解决方案，我们将执行以下步骤：
- 部署应用程序（如果尚未部署）。
- 确保该应用程序是服务网格的一部分。
- 观察收集的遥测数据。

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>将应用配置为与服务网格配合运行。

Istio 支持通过两种方法[检测 pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)。
在大多数情况下，最简单的方法是使用 *istio-injection* 标签来标记包含你的应用程序的 Kubernetes 命名空间：

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> 由于服务网格会从线路中提取数据，因此我们无法截获加密的流量。 对于不会离开群集的流量，可使用未加密的协议（例如 HTTP）。 对于必须加密的外部流量，请考虑在入口控制器上[设置 SSL 终止](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls)。

在服务网格外部运行的应用程序不受影响。

### <a name="deploy-your-application"></a>部署应用程序

- 将应用程序部署到 *my-app-namespace* 命名空间。 如果应用程序已部署，并且你已遵循上述自动挎斗式注入方法，则需要重新创建 pod，以确保 Istio 注入其挎斗；启动滚动更新，或删除各个 pod 并等待重新创建这些 pod。
- 确保应用程序符合 [Istio 要求](https://istio.io/docs/setup/kubernetes/prepare/requirements/)。

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>部署 Kubernetes 托管应用的零检测应用程序监视

1. 下载并提取 [Application Insights 适配器发行版](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)。 
2. 导航到 release 文件夹中的 */src/kubernetes/* 。
3. 编辑 *application-insights-istio-mixer-adapter-deployment.yaml*
    - 在 Azure 门户中编辑 *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* 环境变量的值，以包含 Application Insights 资源的检测密钥和遥测数据。
    - 如果需要，请编辑 *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* 环境变量的值，以包含要为其启用监视的命名空间的逗号分隔列表。 将此环境变量留空会监视所有命名空间。
4. 运行以下命令，应用 *src/kubernetes/* 下的每个 YAML 文件（必须仍位于 */src/kubernetes/* 中）： 

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>验证部署

- 确保已部署 Application Insights 适配器：

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> 在某些情况下，需要进行微调。 若要在收集项中包含或排除单个 pod 的遥测数据，请使用该 pod 上的 *appinsights/monitoring.enabled* 标签。 此设置优先于所有基于命名空间的配置。 将 *appinsights/monitoring.enabled* 设置为 *true* 会包含该 pod，设置为 *false* 会排除该 pod。

### <a name="view-application-insights-telemetry"></a>查看 Application Insights 遥测数据

- 针对应用程序生成示例请求，以确认监视是否正常运行。
- 在 3 到 5 分钟内，Azure 门户中应会开始显示遥测数据。 请务必在门户中查看 Application Insights 资源的“应用程序映射”部分。 

## <a name="troubleshooting"></a>故障排除

下面是遥测数据未按预期显示在 Azure 门户中时使用的故障排除流程。

1. 确保应用程序承受负载，并在发送/接收纯文本 HTTP 请求。 由于遥测数据会从线路中取出，因此不支持加密的流量。 如果没有任何传入或传出的请求，则也不会生成遥测数据。
2. 确保 *application-insights-istio-mixer-adapter-deployment.yaml* 中 *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* 环境变量内提供的检测密钥正确。 在 Azure 门户中 Application Insights 资源的“概述”选项卡上可以找到该检测密钥。 
3. 确保 *application-insights-istio-mixer-adapter-deployment.yaml* 中 *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* 环境变量内提供的 Kubernetes 命名空间正确。 将此环境变量留空会监视所有命名空间。
4. 确保 Istio 已挎斗式注入应用程序的 pod。 验证每个 pod 上是否存在 Istio 的挎斗。

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   验证 pod 上是否运行了一个名为 *istio-proxy* 的容器。

5. 查看 Application Insights 适配器的跟踪。

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   收到的遥测项计数每分钟更新一次。 如果该计数在每分钟后不会增大，则表示 Istio 未将任何遥测数据发送到适配器。
   查看日志中的任何错误。
6. 如果确定没有向 *Application Insight for Kubernetes* 适配器馈送遥测数据，请检查 Istio 的 Mixer 日志，找出未向适配器发送数据的原因：

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   查看任何错误，尤其是与 *applicationinsightsadapter* 适配器通信相关的错误。

## <a name="faq"></a>常见问题解答

有关此项目的进度的最新信息，请访问[用于 Istio Mixer 项目的 Application Insights 适配器 GitHub](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)。

## <a name="uninstall"></a>卸载

若要卸载此产品，请针对 *src/kubernetes/* 下的每个 YAML 文件运行： 

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Monitor 和容器如何协同工作，请访问[用于容器的 Azure Monitor 概述](../../azure-monitor/insights/container-insights-overview.md)