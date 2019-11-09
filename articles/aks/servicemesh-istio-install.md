---
title: 在 Azure Kubernetes 服务 (AKS) 中安装 Istio
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中安装和使用 Istio 来创建服务网格
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 245ac3b1fd88b8d2430e9ddefef3562efd16e6d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885383"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中安装和使用 Istio

[Istio][istio-github] 是跨 Kubernetes 群集中的微服务提供关键功能集的开源服务网格。 这些功能包括流量管理、服务标识和安全性、策略执行以及可观察性。 有关 Istio 的详细信息，请参阅官方文档[什么是 Istio？][istio-docs-concepts]。

本文介绍如何安装 Istio。 Istio `istioctl` 客户端二进制文件已安装到客户端计算机上，Istio 组件将安装到 AKS 上的 Kubernetes 群集中。

> [!NOTE]
> 这些说明引用 Istio 版本 `1.3.2`，并使用至少 Helm 版本 `2.14.2`。
>
> Istio 团队已针对 Kubernetes 版本 `1.3.x`、`1.13` 和 `1.14` 测试了 `1.15` 版本。 可在[GitHub-Istio 版本][istio-github-releases]中找到其他 Istio 版本、 [Istio 新闻][istio-release-notes]中每个版本的相关信息，以及[Istio][istio-faq]的支持 Kubernetes 版本。

在本文中，学习如何：

> [!div class="checklist"]
> * 下载并安装 Istio istioctl 客户端二进制文件
> * 在 AKS 上安装 Istio
> * 验证 Istio 安装
> * 访问外接程序
> * 从 AKS 中卸载 Istio

## <a name="before-you-begin"></a>开始之前

本文中详述的步骤假设已创建 AKS 群集（已启用 RBAC 的 Kubernetes `1.13` 及更高版本）并已与该群集建立 `kubectl` 连接。 如果需要帮助完成这些项目，请参阅 [AKS 快速入门][aks-quickstart]。

需要使用 [Helm][helm] 按照这些说明安装 Istio。 建议在群集中正确安装并配置最新的稳定版本。 安装 Helm 时如需帮助，请参阅 [AKS Helm 安装指南][helm-install]。 所有 Istio Pod 也必须按计划在 Linux 节点上运行。

确保你已阅读 [Istio 性能和可伸缩性](https://istio.io/docs/concepts/performance-and-scalability/)文档，以了解在 AKS 群集中运行 Istio 时的其他资源要求。 核心和内存要求将因特定工作负荷而异。 选择适当数量的节点和 VM 大小以适合你的设置。

本文将 Istio 安装指南分为多个独立步骤。 最终结果的结构与官方 Istio 安装[指南][istio-install-helm]相同。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="add-the-istio-helm-chart-repository"></a>添加 Istio Helm 图表存储库

添加 Istio 版本的 Istio Helm 图表存储库。 确保运行 `helm repo update` 以更新图表存储库的本地信息。

```azurecli
helm repo add istio.io https://storage.googleapis.com/istio-release/releases/$ISTIO_VERSION/charts/
helm repo update
```

## <a name="install-the-istio-crds-on-aks"></a>在 AKS 上安装 Istio CRD

Istio 使用[自定义资源定义 (CRD)][kubernetes-crd] 来管理其运行时配置。 首先需要安装 Istio CRD，因为 Istio 组件依赖于 CRD。 使用 Helm 和 `istio-init` 图表将 Istio CRD 安装到 AKS 群集的 `istio-system` 命名空间中：

```azurecli
helm install istio.io/istio-init --name istio-init --namespace istio-system
```

[作业][kubernetes-jobs]将部署为 `istio-init` Helm 图表的一部分用于安装 CRD。 这些作业的完成时间应小于 .20s，具体取决于群集环境。 可按如下所示验证作业是否已成功完成：

```azurecli
kubectl get jobs -n istio-system
```

以下示例输出显示作业已成功完成。

```console
NAME                      COMPLETIONS   DURATION   AGE
istio-init-crd-10-1.3.2   1/1           14s        14s
istio-init-crd-11-1.3.2   1/1           12s        14s
istio-init-crd-12-1.3.2   1/1           14s        14s
```

确认成功完成作业后，让我们验证是否安装了正确数目的 Istio CRD。 可以通过运行以下命令来验证是否已安装所有 23 Istio CRDs。 该命令应返回数字 `23`。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - check CRD count](includes/servicemesh/istio/install-check-crd-count-powershell.md)]

::: zone-end

如果达到了这一步，即表示你已成功安装 Istio CRD。 现在，请转到[在 AKS 上安装 Istio 组件](#install-the-istio-components-on-aks)部分。

## <a name="install-the-istio-components-on-aks"></a>在 AKS 上安装 Istio 组件

我们将安装 [Grafana][grafana] 和 [Kiali][kiali] 作为 Istio 安装的一部分。 Grafana 提供分析和监视仪表板，Kiali 提供服务网格观察仪表板。 在设置中，上述每个组件都需要凭据，必须以[机密][kubernetes-secrets]的形式提供这些凭据。

在安装 Istio 组件之前，必须为 Grafana 和 Kiali 创建机密。 

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

在 AKS 群集中成功创建 Grafana 和 Kiali 机密后，可以开始安装 Istio 组件了。 使用 Helm 和 `istio` 图表将 Istio 组件安装到 AKS 群集的 `istio-system` 命名空间中： 

> [!NOTE]
> **安装选项**
> 
> 我们将在安装过程中使用以下选项：
> - `global.controlPlaneSecurityEnabled=true` - 为控制平面启用相互 TLS
> - `global.mtls.enabled=true`-要求所有服务间通信使用 mtls
> - `grafana.enabled=true` - 为分析和监视仪表板启用 Grafana 部署
> - `grafana.security.enabled=true` - 为 Grafana 启用身份验证
> - `tracing.enabled=true` - 为跟踪启用 Jaeger 部署
> - `kiali.enabled=true` - 为服务网格观察仪表板启用 Kiali 部署
>
> **节点选择器**
>
> Istio 目前必须计划在 Linux 节点上运行。 如果群集中有 Windows Server 节点，则必须确保 Istio pod 仅计划在 Linux 节点上运行。 我们将使用[节点选择器][kubernetes-node-selectors]来确保将 pod 安排到正确的节点。

> [!CAUTION]
> [SDS （机密发现服务）][istio-feature-sds]和[Istio CNI][istio-feature-cni] Istio 功能当前采用[Alpha][istio-feature-stages]，因此应在启用之前提供。 此外，在当前 AKS 版本中未启用[服务帐户令牌卷投影][kubernetes-feature-sa-projected-volume]Kubernetes 功能（SDS 的要求）。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/istio/install-components-powershell.md)]

::: zone-end

`istio` Helm 图表会部署大量的对象。 上述 `helm install` 命令的输出会显示对象列表。 部署 Istio 组件应需要2分钟才能完成，具体取决于群集环境。

此时，已将 Istio 部署到 AKS 群集。 为确保成功部署 Istio，让我们转到[验证 Istio 安装](#validate-the-istio-installation)部分。

## <a name="validate-the-istio-installation"></a>验证 Istio 安装

首先，确认已创建所需的服务。 使用 [kubectl get svc][kubectl-get] 命令查看正在运行的服务。 查询 `istio-system` 命名空间，`istio` Helm 图表在其中已安装了 Istio 和加载项组件：

```console
kubectl get svc --namespace istio-system --output wide
```

以下示例输出显示了现在应正在运行的服务：

- `istio-*` 服务
- `jaeger-*`、`tracing` 和 `zipkin` 加载项跟踪服务
- `prometheus` 加载项指标服务
- `grafana` 加载项分析和监视仪表板服务
- `kiali` 加载项服务网格仪表板服务

如果 `istio-ingressgateway` 显示 `<pending>` 的外部 IP，请在 Azure 网络分配 IP 地址之前等待几分钟。

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.164.244   <none>           3000/TCP                                                                                                                                     53s   app=grafana
istio-citadel            ClusterIP      10.0.49.16     <none>           8060/TCP,15014/TCP                                                                                                                           53s   istio=citadel
istio-galley             ClusterIP      10.0.175.173   <none>           443/TCP,15014/TCP,9901/TCP                                                                                                                   53s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.226.151   20.188.221.111   15020:31128/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30817/TCP,15030:30436/TCP,15031:32485/TCP,15032:30980/TCP,15443:30124/TCP   53s   app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.102.158   <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       53s   istio=pilot
istio-policy             ClusterIP      10.0.234.53    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                                 53s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.216.8     <none>           443/TCP,15014/TCP                                                                                                                            53s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.154.215   <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       53s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                                   52s   app=jaeger
jaeger-collector         ClusterIP      10.0.26.109    <none>           14267/TCP,14268/TCP                                                                                                                          52s   app=jaeger
jaeger-query             ClusterIP      10.0.70.55     <none>           16686/TCP                                                                                                                                    52s   app=jaeger
kiali                    ClusterIP      10.0.36.206    <none>           20001/TCP                                                                                                                                    53s   app=kiali
prometheus               ClusterIP      10.0.236.99    <none>           9090/TCP                                                                                                                                     53s   app=prometheus
tracing                  ClusterIP      10.0.83.152    <none>           80/TCP                                                                                                                                       52s   app=jaeger
zipkin                   ClusterIP      10.0.25.86     <none>           9411/TCP                                                                                                                                     52s   app=jaeger
```

接下来，确认已创建所需的 Pod。 使用 [kubectl get pods][kubectl-get] 命令，然后再次查询 `istio-system` 命名空间：

```console
kubectl get pods --namespace istio-system
```

以下示例输出显示了正在运行的 Pod：

- `istio-*` pod
- `prometheus-*` 加载项指标 pod
- `grafana-*` 加载项分析和监视仪表板 pod
- `kiali` 加载项服务网格仪表板 pod

```console
NAME                                     READY   STATUS      RESTARTS   AGE
grafana-7c48555456-msl7b                 1/1     Running     0          88s
istio-citadel-566fc66db7-m8wgl           1/1     Running     0          87s
istio-galley-5746db8d56-pl5gg            1/1     Running     0          88s
istio-ingressgateway-6c94f7c9bf-f5lt5    1/1     Running     0          88s
istio-init-crd-10-1.3.2-xw9g2            0/1     Completed   0          92m
istio-init-crd-11-1.3.2-54rz8            0/1     Completed   0          92m
istio-init-crd-12-1.3.2-789qj            0/1     Completed   0          92m
istio-pilot-6748968b6d-rvdfx             2/2     Running     0          87s
istio-policy-7576bbbcf7-2stft            2/2     Running     0          87s
istio-sidecar-injector-76d79d494-7jk9n   1/1     Running     0          87s
istio-telemetry-74b7bf676d-tfrcl         2/2     Running     0          88s
istio-tracing-655d9588bc-d2htg           1/1     Running     0          86s
kiali-65d55bcfb8-tqrfk                   1/1     Running     0          88s
prometheus-846f9849bd-br8kp              1/1     Running     0          87s
```

应有三个 `istio-init-crd-*` 盒，状态为 `Completed`。 这些 pod 负责运行在前面步骤中创建 CRD 的作业。 其他所有 pod 应显示 `Running` 状态。 如果 Pod 没有这些状态，请在运行之前等待一两分钟。 如果任何 Pod 报告问题，请使用 [kubectl describe pod][kubectl-describe] 命令查看其输出和状态。

## <a name="accessing-the-add-ons"></a>访问加载项

在上面的设置中，Istio 安装了许多外接程序，它们提供附加功能。 外接程序的 web 应用程序**不**会通过外部 ip 地址公开公开。 

若要访问外接程序用户界面，请使用 `istioctl dashboard` 命令。 此命令利用[kubectl 端口转发][kubectl-port-forward]和随机端口，在你的客户端计算机与 AKS 群集中的相关 pod 之间创建安全连接。 然后，它会自动在默认浏览器中打开外接程序 web 应用程序。

在本文的前面部分，我们已通过为 Grafana 和 Kiali 指定凭据，为其添加了额外的安全层。

### <a name="grafana"></a>Grafana

Istio 的分析和监视仪表板由 [Grafana][grafana] 提供。 出现提示时，请记得使用前面通过 Grafana 机密创建的凭据。 安全地打开 Grafana 仪表板，如下所示：

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Istio 的指标由 [Prometheus][prometheus] 提供。 安全地打开 Prometheus 仪表板，如下所示：

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Istio 中的跟踪由 [Jaeger][jaeger] 提供。 安全地打开 Jaeger 仪表板，如下所示：

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

服务网格可观察性仪表板由 [Kiali][kiali] 提供。 出现提示时，请记得使用前面通过 Kiali 机密创建的凭据。 安全地打开 Kiali 仪表板，如下所示：

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

[Envoy][envoy]代理的简单接口可用。 它为在指定的 pod 中运行的 Envoy 代理提供配置信息和指标。 安全地打开 Envoy 接口，如下所示：

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>从 AKS 中卸载 Istio

> [!WARNING]
> 从正在运行的系统中删除 Istio 可能会导致服务之间出现流量相关的问题。 在继续之前，请确保对系统进行预配，以便在没有 Istio 的情况下系统仍可正常运行。

### <a name="remove-istio-components-and-namespace"></a>删除 Istio 组件和命名空间

若要从 AKS 群集中删除 Istio，请使用以下命令。 `helm delete` 命令将删除 `istio` 和 `istio-init` 图表，`kubectl delete namespace` 命令将删除 `istio-system` 命名空间。

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete namespace istio-system
```

### <a name="remove-istio-crds-and-secrets"></a>删除 Istio CRDs 和机密

上述命令将删除所有 Istio 组件和命名空间，但仍保留 Istio CRDs 和机密。 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>后续步骤

以下文档介绍了如何使用 Istio 提供智能路由，以推出 canary 版本：

> [!div class="nextstepaction"]
> [AKS Istio 智能路由方案][istio-scenario-routing]

若要了解 Istio 的更多安装和配置选项，请参阅以下官方 Istio 文章：

- [Istio - Helm 安装指南][istio-install-helm]
- [Istio - Helm 安装选项][istio-install-helm-options]

你还可以使用以下方案：

- [Istio Bookinfo 应用程序示例][istio-bookinfo-example]

若要了解如何使用 Application Insights 和 Istio 来监视 AKS 应用程序，请参阅以下 Azure Monitor 文档：

- [Kubernetes 托管应用程序的零检测应用程序监视][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
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
[helm-install]: ./kubernetes-helm.md
