---
title: 使用 Istio 在 Azure Kubernetes 服务 (AKS) 中实现智能路由和 Canary 发布
description: 了解如何使用 Istio 在 Azure Kubernetes 服务 (AKS) 群集中提供智能路由并部署 Canary 发布
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: bd660a2b6ffb96478c3170cc7013ff22518b758f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702209"
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

> [!NOTE]
> 本方案已针对 Istio 版本 `1.1.3` 进行测试。

本文中详述的步骤假设已创建 AKS 群集（已启用 RBAC 的 Kubernetes `1.11` 及更高版本）并已与该群集建立 `kubectl` 连接。 此外，还需在群集内安装 Istio。

如果需要帮助你完成这些项目，请参阅 [AKS 快速入门][aks-quickstart]和[在 AKS 中安装 Istio][istio-install] 的指南。

## <a name="about-this-application-scenario"></a>关于此应用程序方案

AKS 投票应用示例向用户提供了两个投票选项（**猫**或**狗**）。 有一个用于保存各个选项的投票数的存储组件。 此外，还有一个用于提供各个选项的投票详细信息的分析组件。

在此应用方案中，首先将部署投票应用的 `1.0` 版本及分析组件的 `1.0` 版本。 此分析组件将对投票数进行简单计数。 投票应用和分析组件与由 Redis 支持的存储组件 `1.0` 版本进行交互。

将分析组件升级到提供计数和当前总数及百分比的 `1.1` 版本。

一部分用户将通过 Canary 发布测试应用的 `2.0` 版本。 该新版本使用 MySQL 数据库支持的存储组件。

确信该 `2.0` 版本按预期方式作用于该部分用户后，向所有用户推出 `2.0` 版本。

## <a name="deploy-the-application"></a>部署应用程序

首先将应用程序部署到 Azure Kubernetes 服务 (AKS) 群集。 下图说明了本部分结束时运行的内容 - 所有组件的 `1.0` 版本及由 Istio Ingress 网关维护的入站请求：

![AKS 投票应用组件和路由。](media/istio/components-and-routing-01.png)

按照本文操作所需的项目可在 [Azure-Samples/aks-voting-app][github-azure-sample] GitHub 存储库中获取。 下载这些项目或克隆该存储库，如下所示：

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

转到下载/克隆的存储库中的以下文件夹中，并从此文件夹中运行所有后续步骤：

```console
cd scenarios/intelligent-routing-with-istio
```

首先，在 AKS 群集中为 AKS 投票应用示例创建命名空间，并命名为 `voting`，如下所示：

```azurecli
kubectl create namespace voting
```

使用 `istio-injection=enabled` 标记此命名空间。 此标签会指示 Istio 自动将 Istio 代理作为挎斗注入到此命名空间中的所有 Pod 中。

```azurecli
kubectl label namespace voting istio-injection=enabled
```

现在我们将创建 AKS 投票应用的组件。 在上一个步骤所创建的 `voting` 命名空间中创建这些组件。

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

以下示例输出显示正在创建资源：

```console
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

```azurecli
kubectl get pods -n voting
```

以下示例输出说明有三个 `voting-app` Pod 实例，并且 `voting-analytics` 和 `voting-storage` Pod 实例各有一个。 每个 Pod 有两个容器。 两个容器的其中之一是组件，另一个是 `istio-proxy`：

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

使用 [kubectl describe pod][kubectl-describe] 查看 Pod 的相关信息。 将 Pod 名称替换为前面输出中的自己的 AKS 群集内的某个 Pod 的名称：

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

Istio 已自动注入 `istio-proxy` 容器，来管理组件的往返网络流量，如以下示例输出所示：

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

创建 Istio [网关][istio-reference-gateway]和[虚拟服务][istio-reference-virtualservice]后，才能连接到投票应用。 这些 Istio 资源将来自默认 Istio Ingress 网关的流量路由到应用程序。

> [!NOTE]
> 网关是位于服务网格边缘的组件，用于接收入站或出站 HTTP 和 TCP 流量。 
> 
> 虚拟服务将定义一组适用于一个或多个目标服务的路由规则。 

使用 `kubectl apply` 命令部署网关和虚拟服务 yaml。 请记得指定这些资源要部署到的命名空间。

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

以下示例输出显示正在创建新的网关和虚拟服务：

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

使用下面的命令获取 Istio Ingress 网关的 IP 地址：

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

以下示例输出显示 Ingress 网关的 IP 地址：

```
20.188.211.19
```

打开浏览器，并粘贴该 IP 地址。 将显示 AKS 投票应用示例。

![在已启用 Istio 的 AKS 群集中运行的 AKS 投票应用。](media/istio/deploy-app-01.png)

屏幕底部的信息显示应用使用 `voting-app` 的 `1.0` 版本，并使用 `voting-storage` (Redis) 的 `1.0` 版本。

## <a name="update-the-application"></a>更新应用程序

我们来部署分析组件的新版本。 除每个类别的计数外，新版本 `1.1` 还显示总数和百分比。

下图显示了此部分结束时要运行的内容 - 仅 `voting-analytics` 组件的 `1.1` 版本具有路由自 `voting-app` 组件的流量。 即使 `voting-analytics` 组件的 `1.0` 版本继续运行，并且 `voting-analytics` 服务引用了它，Istio 代理也禁用它的往返流量。

![AKS 投票应用组件和路由。](media/istio/components-and-routing-02.png)

让我们部署 `voting-analytics` 组件 `1.1` 版本。 在 `voting` 命名空间中创建此组件：

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

以下示例输出显示正在创建资源：

```console
deployment.apps/voting-analytics-1-1 created
```

使用在前面步骤中获取的 Istio Ingress 网关的 IP 地址，重新在浏览器中打开 AKS 投票应用示例。

浏览器在下面所示的两个视图之间交替。 由于将 Kubernetes [服务][kubernetes-service]用于了 `voting-analytics` 组件，并且仅有单个标签选择器 (`app: voting-analytics`)，因此 Kubernetes 在匹配该选择器的 Pod 之间使用轮询机制的默认行为。 在本例中，它是 `voting-analytics` Pod 的 `1.0` 和 `1.1` 版本。

![在 AKS 投票应用中运行的分析组件 1.0 版本。](media/istio/deploy-app-01.png)

![在 AKS 投票应用中运行的分析组件 1.1 版本。](media/istio/update-app-01.png)

可以将 `voting-analytics` 组件的两个版本之间的切换可视化，如下所示。 要记得使用自己的 Istio Ingress 网关的 IP 地址。

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Powershell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
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

现在我们仅将流量锁定到 `voting-analytics` 组件的 `1.1` 版本和 `voting-storage` 组件的 `1.0` 版本。 然后定义适用于所有其他组件的路由规则。

> * 虚拟服务将定义一组适用于一个或多个目标服务的路由规则。 
> * “目标规则”定义流量策略和特定于版本的策略。 
> * “策略”定义工作负载可以接受的身份验证方法。 

使用 `kubectl apply` 命令替换 `voting-app` 上的虚拟服务定义，并添加适用于其他组件的[目标规则][istio-reference-destinationrule]和[虚拟服务][istio-reference-virtualservice]。 将[策略][istio-reference-policy]添加到 `voting` 命名空间，以确保使用相互 TLS 和客户端证书保护服务之间的所有通信。

* 策略将 `peers.mtls.mode` 设置为 `STRICT`，以确保在 `voting` 命名空间内的服务之间执行相互 TLS。
* 此外，我们在所有目标规则中将 `trafficPolicy.tls.mode` 设置为 `ISTIO_MUTUAL`。 Istio 为服务提供强标识，并使用相互 TLS 和客户端证书保护 Istio 以透明方式管理的服务之间的通信。

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

以下示例输出显示正在更新/创建新策略、目标规则和虚拟服务：

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

若重新在浏览器中打开 AKS 投票应用，则 `voting-app` 组件仅使用 `voting-analytics` 组件的新版本 `1.1`。

![在 AKS 投票应用中运行的分析组件 1.1 版本。](media/istio/update-app-01.png)

现在路由到 `voting-analytics` 组件的 `1.1` 版本，可以更加轻松地将此可视化，如下所示。 要记得使用自己的 Istio Ingress 网关的 IP 地址：

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Powershell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

下面的示例输出显示了返回的网站的相关部分：

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

现在，让我们确认 Istio 使用相互 TLS 保护各个服务之间的通信。 为此，我们将在 `istioctl` 客户端二进制文件中使用以下格式的 [authn tls-check][istioctl-authn-tls-check] 命令。

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

此命令集提供有关从命名空间中的所有 Pod 访问指定服务的信息，并匹配一组标签：

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

Powershell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

以下示例输出显示对上述每个查询强制实施了相互 TLS。 输出中还显示了强制实施相互 TLS 的策略和目标规则：

```console
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>推出应用程序的 Canary 发布

现在，让我们部署 `voting-app`、`voting-analytics` 和 `voting-storage` 组件的新版本 `2.0`。 新的 `voting-storage` 组件使用 MySQL 而不是 Redis，`voting-app` 和 `voting-analytics` 组件将会更新，以便能够使用此新的 `voting-storage` 组件。

现在 `voting-app` 组件支持功能标志功能。 可使用此功能标记针对用户子集测试 Istio 的 Canary 发布功能。

下图演示了本部分结束时要运行的内容。

* `voting-app` 组件的 `1.0` 版本、`voting-analytics` 组件的 `1.1` 版本和 `voting-storage` 组件的 `1.0` 版本能够相互通信。
* `voting-app` 组件的 `2.0` 版本、`voting-analytics` 组件的 `2.0` 版本和 `voting-storage` 组件的 `2.0` 版本能够相互通信。
* 只有设置了特定功能标志的用户能够访问 `voting-app` 组件的 `2.0` 版本。 此更改通过 cookie 使用功能标记管理。

![AKS 投票应用组件和路由。](media/istio/components-and-routing-03.png)

首先，更新 Istio 目标规则和虚拟服务，以适用于这些新组件。 这些更新将确保不会以错误的方式将流量路由到新组件，并确保用户不会获得不需要的访问：

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

以下示例输出显示正在更新目标规则和虚拟服务：

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

接下来，为组件的新版本 `2.0` 添加 Kubernetes 对象。 此外，请更新 `voting-storage` 服务，以包含 MySQL 的 `3306` 端口：

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

下面的示例输出说明已成功更新或创建 Kubernetes 对象：

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

等到所有 `2.0` 版本的 Pod 均在运行。 使用 [kubectl get pods][kubectl-get] 命令查看 `voting` 命名空间中的所有 Pod：

```azurecli
kubectl get pods --namespace voting
```

现在应该能够在投票应用程序的 `1.0` 版本和 `2.0` 版本 (Canary) 之间切换。 屏幕底部的功能标记切换将设置 cookie。 `voting-app` 虚拟服务使用此 Cookie 将用户路由到新版本 `2.0`。

![AKS 投票应用的 1.0 版本 - 未设置功能标记。](media/istio/canary-release-01.png)

![AKS 投票应用的 2.0 版本 - 设置了功能标记。](media/istio/canary-release-02.png)

应用的各个版本的投票计数不同。 此差异突出了正在使用两个不同的存储后端。

## <a name="finalize-the-rollout"></a>完成推出

成功测试 Canary 发布后，更新 `voting-app` 虚拟服务，以将所有流量路由到 `voting-app` 组件的 `2.0` 版本。 然后，无论是否设置了功能标志，所有用户均会看到应用程序的 `2.0` 版本：

![AKS 投票应用组件和路由。](media/istio/components-and-routing-04.png)

更新所有的目标规则，以删除不想要激活的组件版本。 然后更新所有虚拟服务，以停止引用这些版本。

由于不再有任何流量到达这些组件的任何旧版本，因此现在可以安全地删除这些组件的所有部署。

![AKS 投票应用组件和路由。](media/istio/components-and-routing-05.png)

现在你已成功推出 AKS 投票应用的新版本。

## <a name="clean-up"></a>清理 

可以通过删除 `voting` 命名空间，来删除 AKS 群集中的、在本方案中使用的 AKS 投票应用，如下所示：

```azurecli
kubectl delete namespace voting
```

以下示例输出显示已从 AKS 群集中删除 AKS 投票应用的所有组件。

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>后续步骤

可以使用 [Istio Bookinfo 应用程序示例][istio-bookinfo-example]探究其他方案。

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
