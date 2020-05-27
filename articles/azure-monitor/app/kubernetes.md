---
title: 使用 Application Insights 监视 Azure Kubernetes 服务 (AKS) 或其他由 Kubernetes 托管的应用程序 - Azure Monitor | Microsoft Docs
description: Azure Monitor 使用 Kubernetes 群集上的服务网格技术 Istio 为任何由 Kubernetes 托管的应用程序提供应用程序监视功能。 因此，你可以收集与群集中正在运行的 Pod 的传入和传出请求相关的 Application Insights 遥测数据。
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: c3b2b4e8868b73e22706c684d94371fe37885b9a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119243"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>采用 Istio 对 Kubernetes 托管的应用程序进行零检测应用程序监视 - 已弃用

> [!IMPORTANT]
> 此功能当前正被弃用，在 2020 年 8 月 1 日之后将不再受支持。
> 当前推荐的版本仅适用于[通过独立代理提供的 Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)。

Azure Monitor 现在利用 Kubernetes 群集上的服务网格技术为任何由 Kubernetes 托管的应用提供现成的应用程序监视功能。 使用默认的 Application Insight 功能，例如使用[应用程序映射](../../azure-monitor/app/app-map.md)对依赖关系建模，使用[实时指标流](../../azure-monitor/app/live-stream.md)进行实时监视，使用[默认仪表板](../../azure-monitor/app/overview-dashboard.md)、[指标资源管理器](../../azure-monitor/platform/metrics-getting-started.md)和[工作簿](../../azure-monitor/app/usage-workbooks.md)进行强大的可视化操作。 此功能可帮助用户查明所选 Kubernetes 命名空间中所有 Kubernetes 工作负荷的性能瓶颈和故障热点。 利用包含 Istio 之类技术的现有服务网格投资，Azure Monitor 可以在不修改应用程序代码的情况下实现自动检测的应用监视。

> [!NOTE]
> 这是在 Kubernetes 上执行应用程序监视的多种方式之一。 还可以使用 [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.yml) 来检测托管在 Kubernetes 中的任何应用，不需要使用服务网格。 若要在不使用 SDK 来检测应用程序的情况下监视 Kubernetes，可以使用以下方法。

## <a name="prerequisites"></a>先决条件

- 一个 [Kubernetes 群集](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads)。
- 通过控制台访问群集以运行 *kubectl* 的权限。
- 一个 [Application Insight 资源](create-new-resource.md)
- 有一个服务网格。 如果群集未部署 Istio，可以了解如何[在 Azure Kubernetes 服务中安装和使用 Istio](https://docs.microsoft.com/azure/aks/istio-install)。

## <a name="capabilities"></a>功能

通过对 Kubernetes 托管的应用使用零检测应用程序监视，你将能够使用：

- [应用程序映射](../../azure-monitor/app/app-map.md)
- [实时流指标](../../azure-monitor/app/live-stream.md)
- [仪表板](../../azure-monitor/app/overview-dashboard.md)
- [指标资源管理器](../../azure-monitor/platform/metrics-getting-started.md)
- [分布式跟踪](../../azure-monitor/app/distributed-tracing.md)
- [端到端事务监视](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>安装步骤

若要启用此解决方案，请执行以下步骤：
- 部署应用程序（如果尚未部署）。
- 确保该应用程序是服务网格的一部分。
- 观察所收集的遥测数据。

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>配置你的应用以使用服务网格

Istio 支持两种[检测 Pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/) 的方法。
在大多数情况下，最简单的方法是使用 *istio-injection* 标签来标记包含你的应用程序的 Kubernetes 命名空间：

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> 由于服务网格将数据从线路上剥离，因此我们无法拦截加密的流量。 对于不离开群集的流量，请使用未加密的协议（例如 HTTP）。 对于必须加密的外部流量，请考虑在入口控制器处[设置 TLS 终止](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls)。

在服务网格外运行的应用程序不受影响。

### <a name="deploy-your-application"></a>部署应用程序

- 将应用程序部署到 my-app-namespace 命名空间。 如果应用程序已部署，并且你已经遵循了上面描述的自动跨斗注入方法，则需重新创建 Pod 以确保 Istio 注入其跨斗；或者启动滚动更新或删除各个 Pod 并等待它们重新创建。
- 请确保应用程序符合 [Istio 要求](https://istio.io/docs/setup/kubernetes/prepare/requirements/)。

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>为 Kubernetes 托管的应用部署零检测应用程序监视

1. 下载并提取某个 [Application Insights 适配器版本](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)。
2. 导航到 release 文件夹中的 /src/kubernetes/。
3. 编辑 application-insights-istio-mixer-adapter-deployment.yaml
    - 编辑 ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY 环境变量的值，使之包含 Azure 门户中 Application Insights 资源的检测密钥，这样就可以包含遥测数据。
    - 如果需要，请编辑 ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES 环境变量的值，使之包含你要为其启用监视功能的命名空间的逗号分隔列表。 将其留空即可监视所有命名空间。
4. 通过运行以下命令应用在 src/kubernetes/ 中找到的每个 YAML 文件（必须仍然在 /src/kubernetes/ 中  ）：

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>验证部署

- 确保已部署 Application Insights 适配器：

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> 在某些情况下，需要进行微调优化。 若要在收集中包括或排除对单个 Pod 的遥测，请在该 Pod 上使用 *appinsights/monitoring.enabled* 标签。 这将优先于所有基于命名空间的配置。 将 appinsights/monitoring.enabled 设置为 true 即可包括该 Pod，设置为 false 即可排除它。

### <a name="view-application-insights-telemetry"></a>查看 Application Insights 遥测

- 生成针对你的应用程序的示例请求，以确认监视功能是否正常运行。
- 在 3-5 分钟内，你应该会开始看到遥测数据出现在 Azure 门户中。 请确保在门户中查看 Application Insights 资源的“应用程序映射”部分。

## <a name="troubleshooting"></a>故障排除

下面是遥测数据未按预期出现在 Azure 门户中时要使用的故障排除流。

1. 确保应用程序有负荷，并以明文 HTTP 形式发送/接收请求。 由于遥测数据会从线路剥离，因此不支持加密的流量。 如果没有传入或传出请求，则不会有任何遥测数据。
2. 确保在 application-insights-istio-mixer-adapter-deployment.yaml  的环境变量 ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY 中提供正确的检测密钥。 检测密钥是在 Azure 门户中 Application Insights 资源的“概览”选项卡上找到的。
3. 确保在 application-insights-istio-mixer-adapter-deployment.yaml  的环境变量 ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES 中提供正确的 Kubernetes 命名空间。 将其留空即可监视所有命名空间。
4. 确保应用程序的 Pod 已由 Istio 通过跨斗注入。 验证每个 Pod 上是否存在 Istio 的跨斗。

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   验证是否有一个名为 istio-proxy 的容器正在 Pod 上运行。

5. 查看 Application Insights 适配器的跟踪。

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   收到的遥测项的计数每分钟更新一次。 如果此数目没有逐分钟变大，则表示 Istio 没有向适配器发送遥测数据。
   查看日志中是否存在任何错误。
6. 如果已经确定没有为 Application Insight for Kubernetes 适配器提供遥测数据，请检查 Istio 的 Mixer 日志，了解它为何没有向适配器发送数据：

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   查看是否存在任何错误，尤其是与 applicationinsightsadapter 适配器的通信有关的错误。

## <a name="faq"></a>常见问题解答

有关此项目的进度的最新信息，请访问 [Istio Mixer 项目的 GitHub 的 Application Insights 适配器](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)。

## <a name="uninstall"></a>卸载

若要卸载该产品，请针对 src/kubernetes/ 下的每个 YAML 文件运行以下命令：

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Monitor 和容器如何一起工作，请访问[用于容器的 Azure Monitor 概述](../../azure-monitor/insights/container-insights-overview.md)。
