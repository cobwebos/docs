---
title: 使用 Istio 在 Azure Kubernetes 服务 (AKS) 中实现智能路由和 Canary 发布
description: 了解如何使用 Istio 在 Azure Kubernetes 服务 (AKS) 群集中提供智能路由并部署 Canary 发布
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 4c29658473aaa50168175c76234dfca34fcdad83
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594090"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>借助 Istio 在 Azure Kubernetes 服务 (AKS) 中使用智能路由和 Canary 发布

[Istio][istio-github]是一种开放源代码服务网格，提供跨 Kubernetes 群集中微服务的一组关键功能。 这些功能包括流量管理、服务标识和安全性、策略执行以及可观察性。 有关 Istio 的详细信息，请参阅官方[Is Istio？][istio-docs-concepts]文档。

本文演示了如何使用 Istio 的流量管理功能。 将使用 AKS 投票应用示例探究智能路由和 Canary 发布。

在本文中，学习如何：

> [!div class="checklist"]
> * 部署应用程序
> * 更新应用程序
> * 推出应用程序的 Canary 发布
> * 完成推出

## <a name="before-you-begin"></a>开始之前

> [!NOTE]
> 已针对 Istio 版本 `1.3.2`测试了此方案。

本文详细介绍了如何创建 AKS 群集（Kubernetes `1.13` 及更高版本，启用 RBAC），并已与群集建立了 `kubectl` 连接。 此外，还需在群集内安装 Istio。

如果需要有关这些项的帮助，请参阅[AKS 快速入门][aks-quickstart]和[在 AKS 指南中安装 Istio][istio-install] 。

## <a name="about-this-application-scenario"></a>关于此应用程序方案

示例 AKS 投票应用程序向用户提供两个投票选项（**猫**或**狗**）。 有一个用于保存各个选项的投票数的存储组件。 此外，还有一个用于提供各个选项的投票详细信息的分析组件。

在此应用程序方案中，首先部署 `1.0` 的投票应用程序的版本，以及分析组件 `1.0` 版本。 此分析组件将对投票数进行简单计数。 投票应用和分析组件与 Redis 提供支持的存储组件版本 `1.0` 进行交互。

将分析组件升级到版本 `1.1`，该版本提供计数，并且现在汇总和百分比。

用户的子集通过不带上的版本测试应用 `2.0` 版本。 该新版本使用 MySQL 数据库支持的存储组件。

确信版本 `2.0` 在用户子集上按预期方式工作后，就会向所有用户推出 `2.0` 版本。

## <a name="deploy-the-application"></a>部署应用程序

首先将应用程序部署到 Azure Kubernetes 服务 (AKS) 群集。 下图显示了本部分结尾运行的内容-版本 `1.0` 所有组件，并通过 Istio 入口网关提供服务的入站请求：

![AKS 投票应用组件和路由。](media/servicemesh/istio/scenario-routing-components-01.png)

你需要遵循本文的项目可在[Azure 示例/aks][github-azure-sample] GitHub 存储库中找到。 下载这些项目或克隆该存储库，如下所示：

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

转到下载/克隆的存储库中的以下文件夹中，并从此文件夹中运行所有后续步骤：

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

首先，在 AKS 群集中为名为 `voting` 的示例 AKS 投票应用创建一个命名空间，如下所示：

```azurecli
kubectl create namespace voting
```

使用 `istio-injection=enabled` 标记此命名空间。 此标签会指示 Istio 自动将 Istio 代理作为挎斗注入到此命名空间中的所有 Pod 中。

```azurecli
kubectl label namespace voting istio-injection=enabled
```

现在我们将创建 AKS 投票应用的组件。 在上一步创建的 `voting` 命名空间中创建这些组件。

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
> Istio 对 Pod 和服务有一些特定要求。 有关详细信息，请参阅 pod[和服务的 Istio 要求文档][istio-requirements-pods-and-services]。

若要查看已创建的 pod，请使用[kubectl get][kubectl-get] pod 命令，如下所示：

```azurecli
kubectl get pods -n voting --show-labels
```

以下示例输出显示 `voting-app` pod 的三个实例，以及 `voting-analytics` 和 `voting-storage` 箱的一个实例。 每个 Pod 有两个容器。 其中一个容器是组件，另一个容器是 `istio-proxy`：

```console
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

若要查看有关 pod 的信息，我们将使用带有标签选择器的[kubectl 说明 pod][kubectl-describe]命令来选择 `voting-analytics` pod。 我们将筛选输出，以显示 pod 中的两个容器的详细信息：

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

在创建 Istio[网关][istio-reference-gateway]和[虚拟服务][istio-reference-virtualservice]之前，你无法连接到投票应用程序。 这些 Istio 资源将来自默认 Istio Ingress 网关的流量路由到应用程序。

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

![在已启用 Istio 的 AKS 群集中运行的 AKS 投票应用。](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

屏幕底部的信息显示该应用使用的版本 `1.0` `voting-app` 和版本 `1.0` `voting-storage` （Redis）。

## <a name="update-the-application"></a>更新应用程序

让我们部署一个新版本的分析组件。 此新版本 `1.1` 显示总计和百分比，以及每个类别的计数。

下图显示将在此部分结束时运行的内容-仅 `voting-analytics` 组件的 `1.1` 版本包含从 `voting-app` 组件路由的流量。 即使 `voting-analytics` 组件的版本 `1.0` 会继续运行并由 `voting-analytics` 服务引用，Istio 代理也不允许进出它的流量。

![AKS 投票应用组件和路由。](media/servicemesh/istio/scenario-routing-components-02.png)

让我们部署 `voting-analytics` 组件的版本 `1.1`。 在 `voting` 命名空间中创建此组件：

```azurecli
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

以下示例输出显示正在创建资源：

```console
deployment.apps/voting-analytics-1-1 created
```

使用在前面步骤中获取的 Istio Ingress 网关的 IP 地址，重新在浏览器中打开 AKS 投票应用示例。

浏览器在下面所示的两个视图之间交替。 由于仅使用单个标签选择器（`app: voting-analytics`）将 Kubernetes[服务][kubernetes-service]用于 `voting-analytics` 组件，因此 Kubernetes 将使用与该选择器匹配的 pod 之间的轮循机制的默认行为。 在这种情况下，它是 `voting-analytics` pod `1.0` 和 `1.1`。

![在 AKS 投票应用中运行的分析组件 1.0 版本。](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![在 AKS 投票应用中运行的分析组件 1.1 版本。](media/servicemesh/istio/scenario-routing-update-app-01.png)

可以按如下所示将 `voting-analytics` 组件的两个版本之间的切换可视化。 要记得使用自己的 Istio Ingress 网关的 IP 地址。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

下面的示例输出显示了网站在两个版本之间切换时返回的网站的相关部分：

```console
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>将流量锁定到应用程序 1.1 版本

现在，让我们将流量锁定为仅 `1.1` `voting-analytics` 组件的版本和 `voting-storage` 组件版本 `1.0`。 然后定义适用于所有其他组件的路由规则。

> * 虚拟服务将定义一组适用于一个或多个目标服务的路由规则。
> * “目标规则”定义流量策略和特定于版本的策略。
> * “策略”定义工作负载可以接受的身份验证方法。

使用 `kubectl apply` 命令来替换 `voting-app` 上的虚拟服务定义，并为其他组件添加[目标规则][istio-reference-destinationrule]和[虚拟服务][istio-reference-virtualservice]。 将[策略][istio-reference-policy]添加到 `voting` 命名空间，以确保使用相互 TLS 和客户端证书保护服务之间的所有通信。

* 策略 `peers.mtls.mode` 设置为 `STRICT`，以确保在 `voting` 命名空间内的服务之间强制执行相互 TLS。
* 我们还将 `trafficPolicy.tls.mode` 设置为 `ISTIO_MUTUAL` 所有目标规则。 Istio 为服务提供强标识，并使用相互 TLS 和客户端证书保护 Istio 以透明方式管理的服务之间的通信。

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

如果再次在浏览器中打开 AKS 投票应用程序，则 `voting-app` 组件仅使用 `voting-analytics` 组件 `1.1` 的新版本。

![在 AKS 投票应用中运行的分析组件 1.1 版本。](media/servicemesh/istio/scenario-routing-update-app-01.png)

你可以可视化，你现在只路由到 `voting-analytics` 组件的版本 `1.1`，如下所示。 要记得使用自己的 Istio Ingress 网关的 IP 地址：

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

下面的示例输出显示了返回的网站的相关部分：

```console
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

现在，让我们确认 Istio 使用相互 TLS 保护各个服务之间的通信。 为此，我们将使用身份验证的客户端二进制文件的 " [" tls 检查][istioctl-authn-tls-check]命令，该 `istioctl` 命令采用以下形式。

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

此命令集提供有关从命名空间中的所有 Pod 访问指定服务的信息，并匹配一组标签：

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

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

现在，让我们部署 `voting-app`、`voting-analytics`和 `voting-storage` 组件 `2.0` 的新版本。 新的 `voting-storage` 组件使用 MySQL 而不是 Redis，并更新 `voting-app` 和 `voting-analytics` 组件以允许它们使用这个新的 `voting-storage` 组件。

`voting-app` 组件现在支持功能标志功能。 可使用此功能标记针对用户子集测试 Istio 的 Canary 发布功能。

下图演示了本部分结束时要运行的内容。

* `voting-app` 组件的版本 `1.0`、`voting-analytics` 组件的版本 `1.1` 以及 `1.0` 组件的版本 `voting-storage` 可以相互通信。
* `voting-app` 组件的版本 `2.0`、`voting-analytics` 组件的版本 `2.0` 以及 `2.0` 组件的版本 `voting-storage` 可以相互通信。
* 只有设置了特定功能标志的用户才能访问 `voting-app` 组件的版本 `2.0`。 此更改通过 cookie 使用功能标记管理。

![AKS 投票应用组件和路由。](media/servicemesh/istio/scenario-routing-components-03.png)

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

接下来，让我们为新版本 `2.0` 组件添加 Kubernetes 对象。 还需要更新 `voting-storage` 服务，使其包括 MySQL 的 `3306` 端口：

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

等待，直到所有版本都在运行 `2.0` pod。 将[kubectl get][kubectl-get] pod 命令与 `-w` watch 开关结合使用，以监视 `voting` 命名空间中的所有 pod 的变化：

```azurecli
kubectl get pods --namespace voting -w
```

你现在应该能够在投票应用程序的 `1.0` 版本和版本 `2.0` （未应用）之间进行切换。 屏幕底部的功能标记切换将设置 cookie。 此 cookie 由 `voting-app` 虚拟服务用来将用户路由到新版本 `2.0`。

![AKS 投票应用的 1.0 版本 - 未设置功能标记。](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![AKS 投票应用的 2.0 版本 - 设置了功能标记。](media/servicemesh/istio/scenario-routing-canary-release-02.png)

应用的各个版本的投票计数不同。 此差异突出了正在使用两个不同的存储后端。

## <a name="finalize-the-rollout"></a>完成推出

成功测试了未完成测试的版本后，更新 `voting-app` 虚拟服务，将所有流量路由到 `voting-app` 组件的版本 `2.0`。 然后，所有用户都会看到该应用程序的版本 `2.0`，而不管功能标志是否已设置：

![AKS 投票应用组件和路由。](media/servicemesh/istio/scenario-routing-components-04.png)

更新所有的目标规则，以删除不想要激活的组件版本。 然后更新所有虚拟服务，以停止引用这些版本。

由于不再有任何流量到达这些组件的任何旧版本，因此现在可以安全地删除这些组件的所有部署。

![AKS 投票应用组件和路由。](media/servicemesh/istio/scenario-routing-components-05.png)

现在你已成功推出 AKS 投票应用的新版本。

## <a name="clean-up"></a>清除 

通过删除 `voting` 命名空间，可以从 AKS 群集中删除我们在此方案中使用的 AKS 投票应用程序，如下所示：

```azurecli
kubectl delete namespace voting
```

以下示例输出显示已从 AKS 群集中删除 AKS 投票应用的所有组件。

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>后续步骤

可以使用[Istio Bookinfo 应用程序示例][istio-bookinfo-example]浏览其他方案。

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
[istio-install]: ./servicemesh-istio-install.md
