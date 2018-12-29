---
title: 在 Azure Kubernetes 服务 (AKS) 中安装 Istio
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中安装和使用 Istio 来创建服务网格
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: 33a72b6e8fdd4a66425405ff15d7cc31461c0bf3
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892812"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中安装和使用 Istio

[Istio][istio-github] 是跨 Kubernetes 群集中的微服务提供关键功能集的开源服务网格。 这些功能包括流量管理、服务标识和安全性、策略执行以及可观察性。 有关 Istio 的详细信息，请参阅官方文档[什么是 Istio？][istio-docs-concepts]。

本文介绍如何安装 Istio。 Istio `istioctl` 客户端二进制文件已安装到客户端计算机上，则 Istio 组件将安装到 AKS 上的 Kubernetes 群集中。 这些说明引用 Istio 版本 1.0.4。 可以在 [GitHub - Istio 版本][istio-github-releases]中找到其他 Istio 版本。

在本文中，学习如何：

> [!div class="checklist"]
> * 下载 Istio
> * 安装 Istio 客户端二进制文件
> * 安装 Istio Kubernetes 组件
> * 验证安装

## <a name="before-you-begin"></a>开始之前

本文中详述的步骤假设已创建 AKS 群集（已启用 RBAC 的 Kubernetes 1.10 及更高版本）并已与该群集建立 `kubectl` 连接。 如果需要帮助完成这些项目，请参阅 [AKS 快速入门][aks-quickstart]。

若要安装 Istio，需要在群集中正确安装并配置 [Helm][helm] 版本 2.10.0 或更高版本。 如果需要帮助安装 Helm，请参阅 [AKS Helm 安装指南][helm-install]。 如果未安装至少版本 2.10.0 的 Helm，请进行升级或参阅 [Istio - Helm 安装指南][istio-install-helm]以获取其他安装选项。

本文将 Istio 安装指南分为多个独立步骤。 介绍其中每个步骤，以便你了解如何安装 Istio，并了解如何将 Istio 与 kubernetes 配合使用。 最终结果的结构与官方 Istio 安装[指南][istio-install-k8s-quickstart]相同。

## <a name="download-istio"></a>下载 Istio

首先，下载并解压缩最新的 Istio 版本。 MacOS、Linux 或适用于 Linux 的 Windows 子系统上的 bash shell 的步骤和 PowerShell shell 的步骤稍有不同。 为首选环境选择以下安装步骤之一：

* [MacOS、Linux 或适用于 Linux 的 Windows 子系统上的 Bash](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

在 MacOS 上，使用 `curl` 下载最新的 Istio 版本，然后使用 `tar` 进行解压缩，如下所示：

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

在 Linux 或适用于 Linux 的 Windows 子系统上，使用 `curl` 下载最新的 Istio 版本，然后使用 `tar` 进行解压缩，如下所示：

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

### <a name="powershell"></a>PowerShell

在 PowerShell 中，使用 [Invoke-WebRequest][Invoke-WebRequest] 下载最新的 Istio 版本，然后使用 [Expand-Archive][Expand-Archive] 进行解压缩，如下所示：

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.0.4"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

## <a name="install-the-istio-client-binary"></a>安装 Istio 客户端二进制文件

`istioctl` 客户端二进制文件在客户端计算机上运行，并允许你管理 Istio 路由规则和策略。 同样，各个客户端操作系统之间的安装步骤稍有不同。 为首选环境选择以下安装步骤之一。

> [!IMPORTANT]
> 运行已在上一部分中下载并解压缩的 Istio 版本的顶层文件夹中的所有剩余步骤。

### <a name="macos"></a>MacOS

若要在 MacOS 上的基于 bash 的 shell 中安装 Istio `istioctl` 客户端二进制文件，请使用以下命令。 这些命令可将 `istioctl` 客户端二进制文件复制到 `PATH` 中的标准用户程序位置。

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

如果想要通过命令行完成 `istioctl` 客户端二进制文件，则按如下所示进行设置：

```bash
# Generate the bash completion file and source it in your current shell
istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

现在，转到[安装 Istio Kubernetes 组件](#install-the-istio-kubernetes-components)部分。

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux 或适用于 Linux 的 Windows 子系统

使用以下命令在 Linux 或[适用于 Linux 的 Windows 子系统][install-wsl]上的基于 bash 的 shell 中安装 Istio `istioctl` 客户端二进制文件。 这些命令可将 `istioctl` 客户端二进制文件复制到 `PATH` 中的标准用户程序位置。

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

如果想要通过命令行完成 `istioctl` 客户端二进制文件，则按如下所示进行设置：

```bash
# Generate the bash completion file and source it in your current shell
istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

现在，转到[安装 Istio Kubernetes 组件](#install-the-istio-kubernetes-components)部分。

### <a name="windows"></a>Windows

若要在 Windows 上的基于 Powershell 的 shell 中安装 Istio `istioctl` 客户端二进制文件，请使用以下命令。 这些命令可将 `istioctl` 客户端二进制文件复制到新用户程序位置，并通过 `PATH` 使其可用。

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:/Program Files/Istio"
mv ./bin/istioctl.exe "C:/Program Files/Istio/"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:/Program Files/Istio/", "User")
```

## <a name="install-the-istio-kubernetes-components"></a>安装 Istio Kubernetes 组件

若要将 Istio 组件安装到 AKS 群集中，请使用 Helm。 将 Istio 资源安装到 `istio-system` 命名空间中并启用安全性和监视的其他选项，如下所示：

```azurecli
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set grafana.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Helm 图表部署了大量对象。 部署可能需要 2-3 分钟才能完成，具体取决于群集环境。

## <a name="validate-the-installation"></a>验证安装

若要确保已成功部署 Istio，让我们来验证安装。

首先，确认已创建所需的服务。 使用 [kubectl get svc][kubectl-get] 命令查看正在运行的服务。 查询 istio-system 命名空间，Helm 图表在其中已安装了 Istio 和加载项组件：

```console
kubectl get svc --namespace istio-system --output wide
```

以下示例输出显示了现在应正在运行的服务：

- istio-* 服务
- *jaeger-**、tracing 和 zipkin 加载项跟踪服务
- prometheus 加载项指标服务
- grafana 加载项分析和监视仪表板服务
- kiali 加载项服务网格仪表板服务

如果 `istio-ingressgateway` 显示 `<pending>` 的外部 IP，请在 Azure 网络分配 IP 地址之前等待几分钟。

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                   AGE       SELECTOR
grafana                  ClusterIP      10.0.26.60     <none>           3000/TCP                                                                                                                  3m        app=grafana
istio-citadel            ClusterIP      10.0.88.87     <none>           8060/TCP,9093/TCP                                                                                                         3m        istio=citadel
istio-egressgateway      ClusterIP      10.0.115.115   <none>           80/TCP,443/TCP                                                                                                            3m        app=istio-egressgateway,istio=egressgateway
istio-galley             ClusterIP      10.0.104.183   <none>           443/TCP,9093/TCP                                                                                                          3m        istio=galley
istio-ingressgateway     LoadBalancer   10.0.12.216    52.187.250.239   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30469/TCP,8060:31999/TCP,853:31235/TCP,15030:32000/TCP,15031:30293/TCP   3m        app=istio-ingressgateway,istio=ingressgateway
istio-pilot              ClusterIP      10.0.38.134    <none>           15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     3m        istio=pilot
istio-policy             ClusterIP      10.0.253.81    <none>           9091/TCP,15004/TCP,9093/TCP                                                                                               3m        istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.181.186   <none>           443/TCP                                                                                                                   3m        istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.177.113   <none>           9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     3m        istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                3m        app=jaeger
jaeger-collector         ClusterIP      10.0.112.81    <none>           14267/TCP,14268/TCP                                                                                                       3m        app=jaeger
jaeger-query             ClusterIP      10.0.179.193   <none>           16686/TCP                                                                                                                 3m        app=jaeger
kiali                    ClusterIP      10.0.211.63    <none>           20001/TCP                                                                                                                 3m        app=kiali
prometheus               ClusterIP      10.0.70.113    <none>           9090/TCP                                                                                                                  3m        app=prometheus
tracing                  ClusterIP      10.0.139.121   <none>           80/TCP                                                                                                                    3m        app=jaeger
zipkin                   ClusterIP      10.0.60.155    <none>           9411/TCP                                                                                                                  3m        app=jaeger
```

接下来，确认已创建所需的 Pod。 使用 [kubectl get pods][kubectl-get] 命令，然后再次查询 istio-system 命名空间：

```console
kubectl get pods --namespace istio-system
```

以下示例输出显示了正在运行的 Pod：

- istio-* Pod
- prometheus-* 加载项指标 Pod
- grafana-* 加载项分析和监视仪表板 Pod
- kiali 加载项服务网格仪表板 Pod

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-59b787b9b-cr6d7                  1/1       Running     0          6m
istio-citadel-78df8c67d9-9lfpf           1/1       Running     0          6m
istio-egressgateway-6b96cd7f5-k848h      1/1       Running     0          6m
istio-galley-58f566cb66-8mhbv            1/1       Running     0          6m
istio-ingressgateway-6cbbf596f6-6jz8g    1/1       Running     0          6m
istio-pilot-8449d555fc-sl6kp             2/2       Running     0          6m
istio-policy-6b99d88bc5-55s52            2/2       Running     0          6m
istio-sidecar-injector-b88dfb954-8m86s   1/1       Running     0          6m
istio-telemetry-675cb4cb9d-8s7wd         2/2       Running     0          6m
istio-tracing-7596597bd7-sbnt9           1/1       Running     0          6m
kiali-5fbd6ffb-4qcxw                     1/1       Running     0          6m
prometheus-76db5fddd5-2tkxs              1/1       Running     0          6m
```

所有 Pod 显示 `Running` 的状态。 如果 Pod 没有这些状态，请在运行之前等待一两分钟。 如果任何 Pod 报告问题，请使用 [kubectl describe pod][kubectl-describe] 命令查看其输出和状态。

## <a name="accessing-the-add-ons"></a>访问加载项

上述安装程序中安装了大量提供附加功能的 Istio 加载项。 加载项用户界面不会通过外部 IP 地址公开。 若要访问加载项用户界面，请使用 [kubectl port-forward][kubectl-port-forward] 命令。 此命令可在客户端计算机上的本地端口与 AKS 群集中的相关 Pod 之间建立安全连接。

### <a name="grafana"></a>Grafana

Istio 的分析和监视仪表板由 [Grafana][grafana] 提供。 将客户端计算机上的本地端口 3000 转发到正在 AKS 群集中运行 Grafana 的 Pod 上的端口 3000：

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

以下示例输出显示了正在为 Grafana 配置的端口转发：

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

现在可以在客户端计算机上通过以下 URL 访问 Grafana：[http://localhost:3000](http://localhost:3000)。

### <a name="prometheus"></a>Prometheus

指标的表达式浏览器由 [Prometheus][prometheus] 提供。 将客户端计算机上的本地端口 9090 转发到正在 AKS 群集中运行 Prometheus 的 Pod 上的端口 9090：

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

以下示例输出显示了正在为 Prometheus 配置的端口转发：

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

现在可以在客户端计算机上通过以下 URL 访问 Prometheus 表达式浏览器：[http://localhost:9090](http://localhost:9090)。

### <a name="jaeger"></a>Jaeger

跟踪用户界面由 [Jaeger][jaeger] 提供。 将客户端计算机上的本地端口 16686 转发到正在 AKS 群集中运行 Jaeger 的 Pod 上的端口 16686：

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

以下示例输出显示了正在为 Jaeger 配置的端口转发：

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

现在可以在客户端计算机上通过以下 URL 访问 Jaeger 跟踪用户界面：[http://localhost:16686](http://localhost:16686)。

### <a name="kiali"></a>Kiali

服务网格可观察性仪表板由 [Kiali][kiali] 提供。 将客户端计算机上的本地端口 20001 转发到正在 AKS 群集中运行 Kiali 的 Pod 上的端口 20001：

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

以下示例输出显示了正在为 Kiali 配置的端口转发：

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

现在可以在客户端计算机上通过以下 URL 访问 Kiali 服务网格可观察性仪表板：[http://localhost:20001](http://localhost:20001)。

Kiali 仪表板的默认用户名和密码是 username:admin/password:admin。可通过 kiali.dashboard.username 和 kiali.dashboard.passphrase Helm 值配置这些凭据。

## <a name="next-steps"></a>后续步骤

若要了解如何使用 Istio 提供多个应用程序版本之间的智能路由并推出 Canary 发布，请参阅以下文档：

> [!div class="nextstepaction"]
> [AKS Istio 智能路由方案][istio-scenario-routing]

若要了解 Istio 的更多安装和配置选项，请参阅以下官方 Istio 文章：

- [Istio - Kubernetes 安装快速入门][istio-install-k8s-quickstart]
- [Istio - Helm 安装指南][istio-install-helm]
- [Istio - Helm 安装选项][istio-install-helm-options]

也可以使用 [Istio Bookinfo 应用程序示例][istio-bookinfo-example]关注其他方案。

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-k8s-quickstart]: https://istio.io/docs/setup/kubernetes/quick-start/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/helm-install/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
[kubernetes-crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
[Invoke-WebRequest]: /powershell/module/microsoft.powershell.utility/invoke-webrequest
[Expand-Archive]: /powershell/module/Microsoft.PowerShell.Archive/Expand-Archive
