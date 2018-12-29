---
title: 使用 Istio 在 Azure Kubernetes 服务 (AKS) 中实现智能路由和 Canary 发布
description: 了解如何使用 Istio 在 Azure Kubernetes 服务 (AKS) 群集中提供智能路由并部署 Canary 发布
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: 0a4e5e7e310a9949ee59291c2032eafda46955a9
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892823"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>借助 Istio 在 Azure Kubernetes 服务 (AKS) 中使用智能路由和 Canary 发布

[Istio][istio-github] 是跨 Kubernetes 群集中的微服务提供关键功能集的开源服务网格。 这些功能包括流量管理、服务标识和安全性、策略执行以及可观察性。 有关 Istio 的详细信息，请参阅官方文档[什么是 Istio？][istio-docs-concepts]。

本文演示了如何使用 Istio 的流量管理功能。 将使用 AKS 投票应用示例探究智能路由和 Canary 发布。

在本文中，学习如何：

> [!div class="checklist"]
> * 部署应用程序
> * 更新应用程序
> * 推出应用程序的 Canary 发布
> * 完成推出

## <a name="before-you-begin"></a>开始之前

本文中详述的步骤假设已创建 AKS 群集（已启用 RBAC 的 Kubernetes 1.10 及更高版本）并已与该群集建立 `kubectl` 连接。 此外，还需在群集内安装 Istio。

如果需要帮助你完成这些项目，请参阅 [AKS 快速入门][aks-quickstart]和[在 AKS 中安装 Istio][istio-install]。

## <a name="about-this-application-scenario"></a>关于此应用程序方案

AKS 投票应用示例向用户提供了两个投票选项（猫或狗）。 有一个用于保存各个选项的投票数的存储组件。 此外，还有一个用于提供各个选项的投票详细信息的分析组件。

在本文中，首先将部署投票应用的 1.0 版本及分析组件的 1.0 版本。 此分析组件将对投票数进行简单计数。 投票应用和分析组件与由 Redis 支持的存储组件 1.0 版本进行交互。

将分析组件升级到提供计数和当前总数及百分比的 1.1 版本。

用户子集将通过 Canary 发布测试应用的 2.0 版本。 该新版本使用 MySQL 数据库支持的存储组件。

确信该 2.0 版本按预期方式作用于用户子集后，向所有用户推出 2.0 版本。

## <a name="deploy-the-application"></a>部署应用程序

首先将应用程序部署到 Azure Kubernetes 服务 (AKS) 群集。 下图说明了本部分结束时运行的内容 - 所有组件的 1.0 版本及由 Istio Ingress 网关维护的入站请求：

![AKS 投票应用组件和路由。](media/istio/components-and-routing-01.png)

按照本文操作所需的项目可在 [Azure-Samples/aks-voting-app][github-azure-sample] GitHub 存储库中获取。 下载这些项目或克隆该存储库，如下所示：

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

转到下载/克隆的存储库中的以下文件夹中，并从此文件夹中运行所有后续步骤：

```console
cd scenarios/intelligent-routing-with-istio
```

首先，在 AKS 群集中为 AKS 投票应用示例创建命名空间，并命名为“投票”，如下所示：

```console
kubectl create namespace voting
```

使用 `istio-injection=enabled` 标记此命名空间。 此标签会指示 Istio 自动将 Istio 代理作为挎斗注入到此命名空间中的所有 Pod 中。

```console
kubectl label namespace voting istio-injection=enabled
```

现在我们将创建 AKS 投票应用的组件。 在上一个步骤所创建的投票命名空间中创建这些组件。

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

下面的示例输出说明已成功创建资源：

```
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio 对 Pod 和服务有一些特定要求。 有关详细信息，请参阅[针对 Pod 和服务的 Istio 要求文档][istio-requirements-pods-and-services]。

使用 [kubectl get pods][kubectl-get] 命令查看已创建的 Pod，如下所示：

```console
kubectl get pods -n voting
```

下面的示例输出说明有三个投票应用 Pod 实例，并且投票分析和投票存储 Pod 实例各有一个。 每个 Pod 有两个容器。 两个容器的其中之一是组件，另一个是 Istio 代理：

```
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-669f99dcc8-lzh7k   2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-bdmld         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-gcrng         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-strzc         2/2       Running   0          1m
voting-storage-1-0-7954799d96-5fv9r     2/2       Running   0          1m
```

使用 [kubectl describe pod][kubectl-describe] 查看 Pod 的相关信息。 将 Pod 名称替换为前面输出中的自己的 AKS 群集内的某个 Pod 的名称：

```console
kubectl describe pod voting-app-1-0-6c65c4bdd4-bdmld --namespace voting
```

Istio 已自动注入 Istio 代理容器，来管理组件的往返网络流量，如下面的示例输出所示：

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.0.4
[...]
```

创建 Istio [网关][istio-reference-gateway]和[虚拟服务][istio-reference-virtualservice]后，才能连接到投票应用。 这些 Istio 资源将来自默认 Istio Ingress 网关的流量路由到应用程序。

> [!NOTE]
> 网关是位于服务网格边缘的组件，用于接收入站或出站 HTTP 和 TCP 流量。
>
> 虚拟服务将定义一组适用于一个或多个目标服务的路由规则。

使用 `istioctl` 客户端二进制文件部署网关和虚拟服务 yaml。 与 `kubectl apply` 命令一样，要记得指定这些资源要部署到的命名空间。

```console
istioctl create -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

使用下面的命令获取 Istio Ingress 网关的 IP 地址：

```console
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

以下示例输出显示 Ingress 网关的 IP 地址：

```
52.187.250.239
```

打开浏览器，并粘贴该 IP 地址。 将显示 AKS 投票应用示例。

![在已启用 Istio 的 AKS 群集中运行的 AKS 投票应用。](media/istio/deploy-app-01.png)

屏幕底部的信息显示应用使用投票应用的 1.0 版本，并使用版本 1.0 (Redis) 作为存储选项。

## <a name="update-the-application"></a>更新应用程序

我们来部署分析组件的新版本。 除每个类别的计数外，新版本 1.1 还显示总数和百分比。

下图显示了此部分结束时运行的内容 - 仅投票分析组件的 1.1 版本具有路由自投票应用组件的流量。 即使投票分析组件的 1.0 版本继续运行，并且投票分析服务引用了它，Istio 代理也禁用它的往返流量。

![AKS 投票应用组件和路由。](media/istio/components-and-routing-02.png)

我们来部署投票分析组件的 1.1 版本。 在投票命名空间中创建此组件：

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

使用在前面步骤中获取的 Istio Ingress 网关的 IP 地址，重新在浏览器中打开 AKS 投票应用示例。

浏览器在下面所示的两个视图之间交替。 由于将 Kubernetes [服务][kubernetes-service]用于了投票分析组件，并且仅有单个标签选择器 (`app: voting-analytics`)，因此 Kubernetes 在匹配该选择器的 Pod 之间使用轮询机制的默认行为。 在此示例中为投票分析 Pod 的 1.0 和 1.1 版本。

![在 AKS 投票应用中运行的分析组件 1.0 版本。](media/istio/deploy-app-01.png)

![在 AKS 投票应用中运行的分析组件 1.1 版本。](media/istio/update-app-01.png)

可以将投票分析组件的两个版本之间的切换可视化，如下所示。 要记得使用自己的 Istio Ingress 网关的 IP 地址。

```console
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

下面的示例输出显示了网站在两个版本之间切换时返回的网站的相关部分：

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>将流量锁定到应用程序 1.1 版本

现在我们仅将流量锁定到投票分析组件 1.1 版本和投票存储组件 1.0 版本。 然后定义适用于所有其他组件的路由规则。

> * 虚拟服务将定义一组适用于一个或多个目标服务的路由规则。
> * “目标规则”定义流量策略和特定于版本的策略。
> * “策略”定义工作负载可以接受的身份验证方法。

使用 `istioctl` 客户端二进制文件替换投票应用上的虚拟服务定义，并添加适用于其他组件的[目标规则][istio-reference-destinationrule]和[虚拟服务][istio-reference-virtualservice]。

此外，还将[策略][istio-reference-policy]添加到投票命名空间，以确保使用相互 TLS 和客户端证书保护服务之间的所有通信。

由于存在要替换的投票应用的现有虚拟服务定义，因此使用 `istioctl replace`命令，如下所示：

```console
istioctl replace -f istio/step-2a-update-voting-app-virtualservice.yaml --namespace voting
```

下面的示例输出说明已成功更新 Istio 虚拟服务：

```
Updated config virtual-service/voting/voting-app to revision 141902
```

接下来，使用 `istioctl create` 命令添加适用于所有其他组件的新策略以及新目标规则和虚拟服务。

* 策略将 `peers.mtls.mode` 设置为 `STRICT`，以确保在投票命名空间内的服务之间执行相互 TLS。
* 此外，还在所有目标规则中将 `trafficPolicy.tls.mode` 设置为 `ISTIO_MUTUAL`。 Istio 为服务提供强标识，并使用相互 TLS 和客户端证书保护 Istio 以透明方式管理的服务之间的通信。

```console
istioctl create -f istio/step-2b-add-routing-for-all-components.yaml --namespace voting
```

下面的示例输出说明已成功创建新策略、目标规则和虚拟服务：

```
Created config policy/voting/default to revision 142118
Created config destination-rule/voting/voting-app at revision 142119
Created config destination-rule/voting/voting-analytics at revision 142120
Created config virtual-service/voting/voting-analytics at revision 142121
Created config destination-rule/voting/voting-storage at revision 142122
Created config virtual-service/voting/voting-storage at revision 142123
```

若重新在浏览器中打开 AKS 投票应用，则投票应用组件仅使用投票分析组件的新版本 1.1.

![在 AKS 投票应用中运行的分析组件 1.1 版本。](media/istio/update-app-01.png)

现在仅路由到投票分析组件的 1.1 版本，你可以更加轻松地将此可视化，如下所示。 要记得使用 Istio Ingress 网关的 IP 地址。

现在路由到投票分析组件的 1.1 版本，可以更加轻松地将此可视化，如下所示。 要记得使用自己的 Istio Ingress 网关的 IP 地址：

```azurecli-interactive
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

下面的示例输出显示了返回的网站的相关部分：

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

确认 Istio 使用相互 TLS 保护各个服务之间的通信。 下面的命令将检查各个投票应用服务的 TLS 设置：

```console
istioctl authn tls-check voting-app.voting.svc.cluster.local
istioctl authn tls-check voting-analytics.voting.svc.cluster.local
istioctl authn tls-check voting-storage.voting.svc.cluster.local
```

下面的示例输出显示通过策略和目标规则对各个服务执行相互 TLS：

```
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>推出应用程序的 Canary 发布

现在我们来部署投票应用、投票分析和投票存储组件的新版本 2.0。 新投票存储组件使用 MySQL 来替代 Redis，投票应用和投票分析组件更新为允许使用此新投票存储组件。

现在投票应用组件支持功能标记功能。 可使用此功能标记针对用户子集测试 Istio 的 Canary 发布功能。

下图说明了此部分结束时运行的内容。

* 投票应用组件的 1.0 版本、投票分析组件的 1.1 版本和投票存储组件的 1.0 版本能够相互通信。
* 投票应用组件的 2.0 版本、投票分析组件的 2.0 版本和投票存储组件的 2.0 版本能够相互通信。
* 仅已设置特定功能标记的用户可以访问用户投票应用组件的 2.0 版本。 此更改通过 cookie 使用功能标记管理。

![AKS 投票应用组件和路由。](media/istio/components-and-routing-03.png)

首先，更新 Istio 目标规则和虚拟服务，以适用于这些新组件。 这些更新将确保不会以错误的方式将流量路由到新组件，并确保用户不会获得不需要的访问：

```console
istioctl replace -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

下面的示例输出说明已成功更新目标规则和虚拟服务：

```
Updated config destination-rule/voting/voting-app to revision 150930
Updated config virtual-service/voting/voting-app to revision 150931
Updated config destination-rule/voting/voting-analytics to revision 150937
Updated config virtual-service/voting/voting-analytics to revision 150939
Updated config destination-rule/voting/voting-storage to revision 150940
Updated config virtual-service/voting/voting-storage to revision 150941
```

接下来，为组件的新版本 2.0 添加 Kubernetes 对象。 此外，更新投票存储服务，以将 MySQL 的 3306 端口包括在内：

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

下面的示例输出说明已成功更新或创建 Kubernetes 对象：

```
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

等待至所有 2.0 版本的 Pod 均在运行。 使用 [kubectl get pods][kubectl-get] 命令查看投票命名空间中的所有 Pod：

```azurecli-interactive
kubectl get pods --namespace voting
```

现在应该能够在投票应用程序的 1.0 版本和 2.0 版本 (Canary) 之间切换。 屏幕底部的功能标记切换将设置 cookie。 投票应用虚拟服务使用此 cookie 将用户路由到新版本 2.0。

![AKS 投票应用的 1.0 版本 - 未设置功能标记。](media/istio/canary-release-01.png)

![AKS 投票应用的 2.0 版本 - 设置了功能标记。](media/istio/canary-release-02.png)

应用的各个版本的投票计数不同。 此差异突出了正在使用两个不同的存储后端。

## <a name="finalize-the-rollout"></a>完成推出

成功测试 Canary 发布后，更新投票应用虚拟服务，以将所有流量路由到投票应用组件的 2.0 版本。 然后无论是否设置了功能标记，所有用户均会看到应用程序的 2.0 版本：

![AKS 投票应用组件和路由。](media/istio/components-and-routing-04.png)

更新所有的目标规则，以删除不想要激活的组件版本。 然后更新所有虚拟服务，以停止引用这些版本。

由于不再有任何流量到达这些组件的任何旧版本，因此现在可以安全地删除这些组件的所有部署。

![AKS 投票应用组件和路由。](media/istio/components-and-routing-05.png)

现在你已成功推出 AKS 投票应用的新版本。

## <a name="next-steps"></a>后续步骤

可以使用 [Istio Bookinfo 应用程序示例][istio-bookinfo-example]探究其他方案。

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/spec-requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
