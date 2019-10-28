---
title: 使用 Application Insights 来监视 Azure Kubernetes 服务（AKS）或其他 Kubernetes 托管的应用程序-Azure Monitor |Microsoft Docs
description: Azure Monitor 使用 Kubernetes 群集上的服务网格技术 Istio 为任何 Kubernetes 托管的应用程序提供应用程序监视。 这使你可以收集与群集中正在运行的 pod 的传入和传出请求相关的 Application Insights 遥测数据。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 3056b6c56be32cf5c054c4526a88157650a3e30b
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820775"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Kubernetes 托管应用程序的零检测应用程序监视

> [!IMPORTANT]
> 此功能目前处于公共预览阶段。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Monitor 现在利用 Kubernetes 群集上的服务网格技术，为任何 Kubernetes 托管应用提供现成的应用程序监视。 利用默认的 Application insights 功能（如[应用程序映射](../../azure-monitor/app/app-map.md)）来模拟你的依赖项，[实时指标流](../../azure-monitor/app/live-stream.md)实时监视、功能强大的可视化效果以及[默认的仪表板](../../azure-monitor/app/overview-dashboard.md)、[指标资源管理器](../../azure-monitor/platform/metrics-getting-started.md)和[工作簿](../../azure-monitor/app/usage-workbooks.md)。 此功能可帮助用户发现所选 Kubernetes 命名空间中所有 Kubernetes 工作负荷的性能瓶颈和故障热点。 通过使用 Istio 等技术来利用现有的服务网格投资，Azure Monitor 可以在不修改应用程序代码的情况下启用自动检测的应用程序监视。

> [!NOTE]
> 这是在 Kubernetes 上执行应用程序监视的多种方式之一。 你还可以使用[APPLICATION INSIGHTS SDK](../../azure-monitor/azure-monitor-app-hub.md)来检测在 Kubernetes 中托管的任何应用，而无需服务网格。 若要在不使用 SDK 检测应用程序的情况下监视 Kubernetes，可以使用以下方法。

## <a name="prerequisites"></a>必备组件

- [Kubernetes 群集](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads)。
- 对要运行*kubectl*的群集的控制台访问。
- [应用程序见解资源](create-new-resource.md)
- 具有服务网格。 如果群集未部署 Istio，可以了解如何[在 Azure Kubernetes 服务中安装和使用 Istio](https://docs.microsoft.com/azure/aks/istio-install)。

## <a name="capabilities"></a>功能

通过对 Kubernetes 托管应用使用零检测应用程序监视，你将能够使用：

- [应用程序映射](../../azure-monitor/app/app-map.md)
- [实时流度量值](../../azure-monitor/app/live-stream.md)
- [仪表板](../../azure-monitor/app/overview-dashboard.md)
- [指标资源管理器](../../azure-monitor/platform/metrics-getting-started.md)
- [分布式跟踪](../../azure-monitor/app/distributed-tracing.md)
- [端到端事务监视](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>安装步骤

若要启用此解决方案，我们将执行以下步骤：
- 部署应用程序（如果尚未部署）。
- 确保该应用程序是服务网格的一部分。
- 观察收集的遥测。

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>将应用配置为使用服务网格

Istio 支持两种[检测 pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)的方法。
在大多数情况下，最简单的方法是将包含应用程序的 Kubernetes 命名空间标记为*istio*标记：

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> 由于服务网格会将数据放置在网络外，因此无法截获加密的流量。 对于不离开群集的流量，请使用未加密的协议（例如 HTTP）。 对于必须加密的外部通信，请考虑在入口控制器上[设置 SSL 终止](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls)。

在服务网格外运行的应用程序不会受到影响。

### <a name="deploy-your-application"></a>部署应用程序

- 将应用程序部署到*应用命名空间*命名空间。 如果已部署应用程序，并且已遵循上述自动挎斗注入方法，则需要重新创建 pod 以确保 Istio 注入其挎斗;启动滚动更新或删除各个 pod，并等待其重新创建。
- 确保应用程序符合[Istio 要求](https://istio.io/docs/setup/kubernetes/prepare/requirements/)。

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>部署 Kubernetes 托管应用的零检测应用程序监视

1. 下载并提取[ *Application Insights 适配器*版本](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)。
2. 在 release 文件夹内导航到 */src/kubernetes/* 。
3. 编辑*application-insights-istio-yaml*
    - 编辑*ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY*环境变量的值，以包含 Azure 门户中包含遥测数据的 Application Insights 资源的检测密钥。
    - 如有必要，请编辑*ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES*环境变量的值，以包含要为其启用监视的命名空间的逗号分隔列表。 将其留空可监视所有命名空间。
4. 通过运行以下内容*应用*位于*src/kubernetes/* 下的*每个*YAML 文件：

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>验证部署

- 请确保已部署 Application Insights 适配器：

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> 在某些情况下，需要进行微调优化。 若要包括或排除单个 pod 的遥测数据，请使用该 pod 上的*appinsights/monitoring*标签。 这将优先于所有基于命名空间的配置。 设置*appinsights/monitoring。* 如果为*true，则*包含 pod，并将其排除为*false* 。

### <a name="view-application-insights-telemetry"></a>查看 Application Insights 遥测

- 生成针对应用程序的示例请求，以确认监视是否正常运行。
- 在3-5 分钟内，你应开始查看 Azure 门户中显示的遥测数据。 请确保在门户中查看 Application Insights 资源的 "*应用程序映射*" 部分。

## <a name="troubleshooting"></a>故障排除

下面是遥测未按预期显示在 Azure 门户中时要使用的疑难解答流。

1. 确保应用程序处于负载下，并以纯 HTTP 发送/接收请求。 由于遥测数据是断开的，因此不支持加密的流量。 如果没有传入或传出请求，则不会有任何遥测。
2. 确保*ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY*环境变量中提供了正确的检测密钥 *。 yaml*中的 "ISTIO" 环境变量中提供了正确的检测密钥。 检测密钥可在 Azure 门户中 Application Insights 资源的 "*概述*" 选项卡上找到。
3. 确保*ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES*环境变量中提供了正确的 Kubernetes 命名空间，并在*yaml*中提供。 将其留空可监视所有命名空间。
4. 请确保应用程序的 pod 已由 Istio 注入挎斗。 验证每个 pod 上是否存在 Istio 的挎斗。

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   验证是否在 pod 上运行了名为*istio 的*容器。

5. 查看 Application Insights 适配器的跟踪。

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   每分钟更新一次接收的遥测项目的计数。 如果每分钟不会增长一分钟，则 Istio 不会将遥测发送到适配器。
   查找日志中的任何错误。
6. 如果已建立了 Kubernetes 适配器的*Application* insights 未送入遥测数据，请检查 Istio 的混音器日志，找出无法将数据发送到适配器的原因：

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   查找任何错误，尤其是与*applicationinsightsadapter*适配器的通信有关。

## <a name="faq"></a>常见问题解答

有关此项目上进度的最新信息，请访问适用于[Istio 混音器项目 GitHub 的 Application Insights 适配器](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)。

## <a name="uninstall"></a>卸载

若要卸载该产品，请针对*src/kubernetes/* run 下的*每个*YAML 文件：

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Monitor 和容器一起工作的方式，请访问[容器 Azure Monitor 概述](../../azure-monitor/insights/container-insights-overview.md)