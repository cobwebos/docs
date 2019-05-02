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
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
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
> 这种情况下是否已经按照 Istio 版本测试`1.1.3`。

这篇文章中详述的步骤假定你已创建 AKS 群集 (Kubernetes`1.11`和更高版本，使用 RBAC 启用) 并已建立了`kubectl`与群集的连接。 您还需要安装在群集中的 Istio。

如果需要使用上述任何一项的帮助，请参阅[AKS 快速入门][ aks-quickstart]并[安装在 AKS 中的 Istio] [ istio-install]指南。

## <a name="about-this-application-scenario"></a>关于此应用程序方案

示例 AKS 投票应用程序提供了两个投票选项 (**猫**或**狗**) 向用户。 有一个用于保存各个选项的投票数的存储组件。 此外，还有一个用于提供各个选项的投票详细信息的分析组件。

在此应用程序方案中，你首先部署版本`1.0`的投票的应用程序和版本`1.0`的分析组件。 此分析组件将对投票数进行简单计数。 投票应用和分析组件与版本交互`1.0`的由 Redis 支持的存储组件。

分析组件升级到版本`1.1`，可提供计数，并且现在进行合计和百分比。

用户测试版本的子集`2.0`通过 canary 发布的应用程序。 该新版本使用 MySQL 数据库支持的存储组件。

在您确信该版本`2.0`用户的子集上按预期的工作原理，推出版本`2.0`向所有用户。

## <a name="deploy-the-application"></a>部署应用程序

首先将应用程序部署到 Azure Kubernetes 服务 (AKS) 群集。 下图显示了什么情况下本部分的末尾运行版本`1.0`Istio 入口网关通过提供服务的入站请求使用的所有组件：

![AKS 投票应用组件和路由。](media/istio/components-and-routing-01.png)

按照本文操作所需的项目可在 [Azure-Samples/aks-voting-app][github-azure-sample] GitHub 存储库中获取。 下载这些项目或克隆该存储库，如下所示：

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

转到下载/克隆的存储库中的以下文件夹中，并从此文件夹中运行所有后续步骤：

```console
cd scenarios/intelligent-routing-with-istio
```

首先，创建名为示例 AKS 投票应用在 AKS 群集中的命名空间`voting`，如下所示：

```azurecli
kubectl create namespace voting
```

使用 `istio-injection=enabled` 标记此命名空间。 此标签会指示 Istio 自动将 Istio 代理作为挎斗注入到此命名空间中的所有 Pod 中。

```azurecli
kubectl label namespace voting istio-injection=enabled
```

现在我们将创建 AKS 投票应用的组件。 创建这些组件中的`voting`上一步中创建命名空间。

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

以下示例输出显示正在创建的资源：

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

以下示例输出显示有三个实例`voting-app`pod 和这两者的单个实例`voting-analytics`和`voting-storage`pod。 每个 Pod 有两个容器。 这些容器之一是组件，以及另一个是`istio-proxy`:

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

`istio-proxy`容器具有自动已通过插入 Istio 来管理网络流量与您的组件，如以下示例输出中所示：

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

使用`kubectl apply`命令来部署网关和虚拟服务 yaml。 请记住要指定这些资源部署到的命名空间。

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

以下示例输出显示新的网关和要创建的虚拟服务：

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

在屏幕底部的信息将显示该应用使用版本`1.0`的`voting-app`和版本`1.0`的`voting-storage`(Redis)。

## <a name="update-the-application"></a>更新应用程序

我们来部署分析组件的新版本。 这一新版本`1.1`显示总计和除了每个类别的计数的百分比。

下图显示了将在本部分-仅版本末尾运行哪些`1.1`的我们`voting-analytics`组件已从路由流量`voting-app`组件。 即使版本`1.0`的我们`voting-analytics`组件将继续运行并由引用`voting-analytics`服务，Istio 代理不允许传入和从它的流量。

![AKS 投票应用组件和路由。](media/istio/components-and-routing-02.png)

让我们来部署版本`1.1`的`voting-analytics`组件。 创建此组件中的`voting`命名空间：

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

以下示例输出显示正在创建的资源：

```console
deployment.apps/voting-analytics-1-1 created
```

使用在前面步骤中获取的 Istio Ingress 网关的 IP 地址，重新在浏览器中打开 AKS 投票应用示例。

浏览器在下面所示的两个视图之间交替。 由于你使用 Kubernetes[服务][ kubernetes-service]有关`voting-analytics`组件与单标签选择器 (`app: voting-analytics`)，Kubernetes 使用轮循机制之间的默认行为与该选择器匹配的 pod。 在这种情况下，它是这两个版本`1.0`并`1.1`的你`voting-analytics`pod。

![在 AKS 投票应用中运行的分析组件 1.0 版本。](media/istio/deploy-app-01.png)

![在 AKS 投票应用中运行的分析组件 1.1 版本。](media/istio/update-app-01.png)

你可以可视化的两个版本之间切换`voting-analytics`组件，如下所示。 要记得使用自己的 Istio Ingress 网关的 IP 地址。

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

现在让我们来锁定发往仅版本流量`1.1`的`voting-analytics`组件和版本`1.0`的`voting-storage`组件。 然后定义适用于所有其他组件的路由规则。

> * 虚拟服务将定义一组适用于一个或多个目标服务的路由规则。
> * “目标规则”定义流量策略和特定于版本的策略。
> * “策略”定义工作负载可以接受的身份验证方法。

使用`kubectl apply`命令以将虚拟服务定义为在你`voting-app`并添加[目标规则][ istio-reference-destinationrule]并[虚拟服务][istio-reference-virtualservice]用于其他组件。 您将添加[策略][ istio-reference-policy]到`voting`使用相互 TLS 和客户端证书保护命名空间，以确保所有服务之间通信。

* 该策略具有`peers.mtls.mode`设置为`STRICT`以确保在服务之间实施相互 TLS`voting`命名空间。
* 我们还设置`trafficPolicy.tls.mode`到`ISTIO_MUTUAL`中我们所有的目标规则。 Istio 为服务提供强标识，并使用相互 TLS 和客户端证书保护 Istio 以透明方式管理的服务之间的通信。

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

下面的示例输出显示新的策略、 目标规则和正在更新/创建的虚拟服务：

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

如果您打开了 AKS 投票应用的浏览器中，只有新版本`1.1`的`voting-analytics`使用的组件`voting-app`组件。

![在 AKS 投票应用中运行的分析组件 1.1 版本。](media/istio/update-app-01.png)

你可以可视化，您现在仅路由到版本`1.1`的你`voting-analytics`组件，如下所示。 要记得使用自己的 Istio Ingress 网关的 IP 地址：

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

让我们现在确认 Istio 使用相互 TLS 来保护每项服务之间的通信。 为此，我们将使用[身份验证检查 tls] [ istioctl-authn-tls-check]命令`istioctl`客户端二进制文件，它采用以下形式。

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

此命令集的命名空间中和标签的一组匹配的所有 pod 从指定的服务，提供的访问权限的信息：

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

此以下示例输出显示相互 TLS 对每个我们上面的查询强制执行。 输出还显示的策略和强制实施相互 TLS 的目标规则：

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

现在让我们来部署新版本`2.0`的`voting-app`， `voting-analytics`，和`voting-storage`组件。 新`voting-storage`组件而不是 Redis，使用 MySQL 和`voting-app`并`voting-analytics`组件需要更新以允许它们使用此新`voting-storage`组件。

`voting-app`组件现在支持功能标志功能。 可使用此功能标记针对用户子集测试 Istio 的 Canary 发布功能。

下图显示了您将具有在本部分末尾运行。

* 版本`1.0`的`voting-app`组件、 版本`1.1`的`voting-analytics`组件和版本`1.0`的`voting-storage`组件可相互通信。
* 版本`2.0`的`voting-app`组件、 版本`2.0`的`voting-analytics`组件和版本`2.0`的`voting-storage`组件可相互通信。
* 版本`2.0`的`voting-app`组件才可以访问特定功能标志设置的用户。 此更改通过 cookie 使用功能标记管理。

![AKS 投票应用组件和路由。](media/istio/components-and-routing-03.png)

首先，更新 Istio 目标规则和虚拟服务，以适用于这些新组件。 这些更新将确保不会以错误的方式将流量路由到新组件，并确保用户不会获得不需要的访问：

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

以下示例输出显示了目标规则和要更新的虚拟服务：

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

接下来，让我们添加的新版本的 Kubernetes 对象`2.0`组件。 此外更新`voting-storage`服务，以包括`3306`MySQL 端口：

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

等待，直到所有的版本`2.0`pod 在运行。 使用[kubectl get pod] [ kubectl-get]命令来查看中的所有 pod`voting`命名空间：

```azurecli
kubectl get pods --namespace voting
```

现在应能够在版本之间切换`1.0`和版本`2.0`(canary) 的投票应用程序。 屏幕底部的功能标记切换将设置 cookie。 使用此 cookie`voting-app`将用户路由到新版本的虚拟服务`2.0`。

![AKS 投票应用的 1.0 版本 - 未设置功能标记。](media/istio/canary-release-01.png)

![AKS 投票应用的 2.0 版本 - 设置了功能标记。](media/istio/canary-release-02.png)

应用的各个版本的投票计数不同。 此差异突出了正在使用两个不同的存储后端。

## <a name="finalize-the-rollout"></a>完成推出

已成功测试 canary 发布后, 更新`voting-app`虚拟服务将所有流量都路由到版本`2.0`的`voting-app`组件。 所有用户然后都看到版本`2.0`的应用程序，而不考虑功能标志是否设置：

![AKS 投票应用组件和路由。](media/istio/components-and-routing-04.png)

更新所有的目标规则，以删除不想要激活的组件版本。 然后更新所有虚拟服务，以停止引用这些版本。

由于不再有任何流量到达这些组件的任何旧版本，因此现在可以安全地删除这些组件的所有部署。

![AKS 投票应用组件和路由。](media/istio/components-and-routing-05.png)

现在你已成功推出 AKS 投票应用的新版本。

## <a name="clean-up"></a>清理 

您可以删除 AKS 投票应用程序，我们使用在此方案中从 AKS 群集通过删除`voting`命名空间，如下所示：

```azurecli
kubectl delete namespace voting
```

以下示例输出显示 AKS 投票应用程序的所有组件，已从 AKS 群集中都删除。

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
