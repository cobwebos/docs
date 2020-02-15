---
title: 在 Azure Kubernetes 服务 (AKS) 中安装 Istio
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中安装和使用 Istio 来创建服务网格
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d886205e88db780a7a09554391bd975f57eebfe7
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251730"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中安装和使用 Istio

[Istio][istio-github]是一种开放源代码服务网格，提供跨 Kubernetes 群集中微服务的一组关键功能。 这些功能包括流量管理、服务标识和安全性、策略执行以及可观察性。 有关 Istio 的详细信息，请参阅官方[Is Istio？][istio-docs-concepts]文档。

本文介绍如何安装 Istio。 Istio `istioctl` 客户端二进制文件安装在客户端计算机上，Istio 组件安装在 AKS 上的 Kubernetes 群集。

> [!NOTE]
> 以下说明引用 Istio 版本 `1.4.0`。
>
> Istio 团队针对 Kubernetes 版本（`1.13`、`1.14``1.15`）测试了 Istio `1.4.x` 版本。 可在[GitHub-Istio 版本][istio-github-releases]中找到其他 Istio 版本、 [Istio 新闻][istio-release-notes]中每个版本的相关信息，以及[Istio][istio-faq]的支持 Kubernetes 版本。

在本文中，学习如何：

> [!div class="checklist"]
> * 下载并安装 Istio istioctl 客户端二进制文件
> * 在 AKS 上安装 Istio
> * 验证 Istio 安装
> * 访问外接程序
> * 从 AKS 卸载 Istio

## <a name="before-you-begin"></a>开始之前

本文详细介绍了如何创建 AKS 群集（Kubernetes `1.13` 及更高版本，启用 RBAC），并已与群集建立了 `kubectl` 连接。 如果需要有关这些项的帮助，请参阅[AKS 快速入门][aks-quickstart]。

请确保已阅读[Istio 性能和可扩展性](https://istio.io/docs/concepts/performance-and-scalability/)文档，以了解在 AKS 群集中运行 Istio 所需的其他资源要求。 核心和内存要求将因你的特定工作负荷而异。 选择适当数量的节点和 VM 大小，以适合您的设置。

本文将 Istio 安装指南分为多个独立步骤。 最终结果在结构上与正式 Istio 安装[指南][istio-install-istioctl]相同。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>在 AKS 上安装 Istio 组件

我们会将[Grafana][grafana]和[Kiali][kiali]作为 Istio 安装的一部分进行安装。 Grafana 提供分析和监视仪表板，Kiali 提供服务网格可观察性仪表板。 在我们的设置中，其中每个组件都需要提供为[机密][kubernetes-secrets]的凭据。

在我们可以安装 Istio 组件之前，必须为 Grafana 和 Kiali 创建机密。 需要将这些机密安装到 Istio 将使用的 `istio-system` 命名空间中，因此我们还需要创建命名空间。 在通过 `kubectl create` 创建命名空间时，需要使用 `--save-config` 选项，以便 Istio 安装程序以后可以在此对象上运行 `kubectl apply`。

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>安装 Istio 组件

现在，我们已在 AKS 群集中成功创建了 Grafana 和 Kiali 机密，可以安装 Istio 组件了。 

以后将不再推荐使用 Istio 的[Helm][helm]安装方法。 新的 Istio 安装方法利用 `istioctl` 客户端二进制[文件、Istio 配置文件][istio-configuration-profiles]和新的[Istio 控制平面规范和 api][istio-control-plane]。 这种新方法是我们将用于安装 Istio 的方法。

> [!NOTE]
> Istio 目前必须计划在 Linux 节点上运行。 如果群集中有 Windows Server 节点，则必须确保 Istio pod 仅计划在 Linux 节点上运行。 我们将使用[节点选择器][kubernetes-node-selectors]来确保将 pod 安排到正确的节点。

> [!CAUTION]
> [SDS （机密发现服务）][istio-feature-sds]和[Istio CNI][istio-feature-cni] Istio 功能当前采用[Alpha][istio-feature-stages]，因此应在启用之前提供。 
>
> 请注意，现在已为 AKS 上的所有 Kubernetes 1.13 和更高版本**启用**了[服务帐户标记量预测][kubernetes-feature-sa-projected-volume]Kubernetes 功能（SDS 的要求）。

使用以下内容创建名为 `istio.aks.yaml` 的文件。 此文件将保存用于配置 Istio 的[Istio 控制平面规范][istio-control-plane]详细信息。

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

使用 `istioctl apply` 命令和上述 `istio.aks.yaml` Istio 控制平面规范文件安装 istio，如下所示：

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

安装程序将部署多个[CRDs][kubernetes-crd] ，然后管理依赖项，以便安装为 Istio 的此配置定义的所有相关对象。 应会看到类似于以下输出代码段的内容。

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

此时，已将 Istio 部署到 AKS 群集。 若要确保已成功部署 Istio，请转到下一节，[验证 Istio 安装](#validate-the-istio-installation)。

## <a name="validate-the-istio-installation"></a>验证 Istio 安装

首先，确认已创建所需的服务。 使用[kubectl 获取 svc][kubectl-get]命令查看正在运行的服务。 查询 `istio-system` 命名空间，其中 Istio 和外接程序组件由 `istio` Helm 图安装：

```console
kubectl get svc --namespace istio-system --output wide
```

以下示例输出显示了现在应正在运行的服务：

- `istio-*` 服务
- `jaeger-*`、`tracing`和 `zipkin` 外接程序跟踪服务
- `prometheus` 加载项指标服务
- `grafana` 加载项分析和监视面板服务
- `kiali` 外接程序服务网格面板服务

如果 `istio-ingressgateway` 显示 `<pending>` 的外部 IP，请在 Azure 网络分配 IP 地址之前等待几分钟。

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

接下来，确认已创建所需的 Pod。 使用[kubectl get][kubectl-get] pod 命令，并再次查询 `istio-system` 命名空间：

```console
kubectl get pods --namespace istio-system
```

以下示例输出显示了正在运行的 Pod：

- `istio-*` 箱
- `prometheus-*` 外接程序指标 pod
- `grafana-*` 加载项分析和监视仪表板 pod
- `kiali` 外接程序服务网格仪表板 pod

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

所有 pod 都应显示 `Running`状态。 如果 Pod 没有这些状态，请在运行之前等待一两分钟。 如果任何 pod 报告了问题，请使用[kubectl 说明 pod][kubectl-describe]命令查看其输出和状态。

## <a name="accessing-the-add-ons"></a>访问加载项

在上面的设置中，Istio 安装了许多外接程序，它们提供附加功能。 外接程序的 web 应用程序**不**会通过外部 ip 地址公开公开。 

若要访问外接程序用户界面，请使用 `istioctl dashboard` 命令。 此命令利用[kubectl 端口转发][kubectl-port-forward]和随机端口，在你的客户端计算机与 AKS 群集中的相关 pod 之间创建安全连接。 然后，它会自动在默认浏览器中打开外接程序 web 应用程序。

为 Grafana 和 Kiali 添加了额外的安全层，只需要在本文前面指定凭据即可。

### <a name="grafana"></a>Grafana

Istio 的分析和监视仪表板由[Grafana][grafana]提供。 请记住，在出现提示时，请使用通过 Grafana 机密创建的凭据。 安全地打开 Grafana 仪表板，如下所示：

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Istio 的指标由[Prometheus][prometheus]提供。 安全地打开 Prometheus 仪表板，如下所示：

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Istio 中的跟踪由[Jaeger][jaeger]提供。 安全地打开 Jaeger 仪表板，如下所示：

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

服务网格可观察性仪表板由[Kiali][kiali]提供。 请记住，在出现提示时，请使用通过 Kiali 机密创建的凭据。 安全地打开 Kiali 仪表板，如下所示：

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

[Envoy][envoy]代理的简单接口可用。 它为在指定的 pod 中运行的 Envoy 代理提供配置信息和指标。 安全地打开 Envoy 接口，如下所示：

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>从 AKS 卸载 Istio

> [!WARNING]
> 删除正在运行的系统中的 Istio 可能会导致服务之间出现与流量相关的问题。 在继续操作之前，请确保您的系统的预配仍可正常运行，而无需 Istio。

### <a name="remove-istio-components-and-namespace"></a>删除 Istio 组件和命名空间

若要从 AKS 群集中删除 Istio，请将 `istioctl manifest generate` 命令与 `istio.aks.yaml` Istio 控制平面规范文件一起使用。 这会生成已部署清单，我们将通过管道将其传递到 `kubectl delete`，以便删除所有已安装的组件和 `istio-system` 命名空间。

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>删除 Istio CRDs 和机密

上述命令将删除所有 Istio 组件和命名空间，但仍保留生成的 Istio 机密。 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>后续步骤

以下文档介绍了如何使用 Istio 来提供智能路由，以推出不确定的版本：

> [!div class="nextstepaction"]
> [AKS Istio 智能路由方案][istio-scenario-routing]

若要了解有关 Istio 的更多安装和配置选项，请参阅以下官方 Istio 指南：

- [Istio-安装指南][istio-installation-guides]

你还可以使用以下方案：

- [Istio Bookinfo 应用程序示例][istio-bookinfo-example]

若要了解如何使用 Application Insights 和 Istio 监视 AKS 应用程序，请参阅下面的 Azure Monitor 文档：

- [Kubernetes 托管应用程序的零检测应用程序监视][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha12.pb/#IstioControlPlane
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
