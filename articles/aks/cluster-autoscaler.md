---
title: AKS(Azure Kubernetes Service)에서 클러스터 자동 크기 조정기 사용
description: 클러스터 자동 크기 조정기를 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 애플리케이션 수요에 맞게 자동으로 클러스터 크기를 조정하는 방법을 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: 033cf88e29ba4a9f7ce9397fe216f7380e70be07
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713398"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 애플리케이션 수요에 맞게 자동으로 클러스터 크기 조정

AKS(Azure Kubernetes Service)에서 애플리케이션 수요에 맞추려면 워크로드를 실행하는 노드 수를 조정해야 할 수 있습니다. 클러스터 자동 크기 조정기 구성 요소는 리소스 제약 조건으로 인해 예약할 수 없는 클러스터의 Pod를 확인할 수 있습니다. 当检测到问题时，将增加节点池中的节点数，以满足应用程序的需求。 또한 실행 중인 Pod가 부족한지 노드를 주기적으로 확인하고 필요에 따라 노드 수가 감소합니다. AKS 클러스터에서 노드 수를 자동으로 강화하거나 규모 축소하는 이 기능을 통해 효율적이고 비용 효과적인 클러스터를 실행할 수 있습니다.

이 문서에서는 AKS 클러스터에서 클러스터 자동 크기 조정기를 사용하도록 설정하고 관리하는 방법을 보여 줍니다. 

## <a name="before-you-begin"></a>시작하기 전에

本文要求运行 Azure CLI 版本2.0.76 或更高版本。 `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="limitations"></a>제한 사항

创建和管理使用群集自动缩放程序的 AKS 群集时，有以下限制：

* 无法使用 HTTP 应用程序路由加载项。

## <a name="about-the-cluster-autoscaler"></a>클러스터 자동 크기 조정기 정보

업무 시간에서 야간 사이에 또는 주말에 변화하는 애플리케이션 수요에 맞게 조정하기 위해 일반적으로 클러스터에는 자동으로 크기를 조정하는 방법이 필요합니다. AKS 클러스터는 다음 두 가지 방법 중 하나로 크기를 조정할 수 있습니다.

* **클러스터 자동 크기 조정기**는 리소스 제약 조건으로 인해 노드에서 예약할 수 없는 Pod를 확인합니다. 然后，群集将自动增加节点数。
* **Horizontal Pod Autoscaler**는 Kubernetes 클러스터에서 메트릭 서버를 사용하여 Pod의 리소스 수요를 모니터링합니다. 如果应用程序需要更多资源，则会自动增加 pod 数量以满足需求。

![종종 클러스터 자동 크기 조정기 및 Horizontal Pod Autoscaler가 연동되어 필요한 애플리케이션 수요를 지원함](media/autoscaler/cluster-autoscaler.png)

水平 pod 自动缩放程序和群集自动缩放程序还可以根据需要减少箱和节点的数量。 클러스터 자동 크기 조정기는 일정 기간 사용되지 않은 용량이 있는 경우 노드 수를 줄입니다. 클러스터 자동 크기 조정기가 제거할 노드의 Pod는 클러스터의 다른 위치에서 안전하게 예약됩니다. 다음 상황에서처럼 Pod를 이동할 수 없는 경우 클러스터 자동 크기 조정기가 규모를 축소하지 못할 수 있습니다.

* Pod 是直接创建的，不受控制器对象（如部署或副本集）支持。
* PDB(Pod Disruption Budget)가 너무 제한적이고 이를 사용하면 Pod 수가 특정 임계값보다 낮아질 수 없습니다.
* Pod는 다른 노드에서 예약된 경우 적용할 수 없는 노드 선택기 또는 선호도 방지를 사용합니다.

有关群集自动缩放程序如何无法缩减的详细信息，请参阅[哪些类型的 pod 可以阻止群集自动缩放程序删除节点？][autoscaler-scaledown]

클러스터 자동 크기 조정기는 크기 조정 이벤트와 리소스 임계값 사이의 시간 간격 같은 항목에 시작 매개 변수를 사용합니다. 이러한 매개 변수는 Azure 플랫폼에 의해 정의되며 현재는 사용자가 조정하도록 표시되지 않습니다. 有关群集自动缩放程序使用的参数的详细信息，请参阅[什么是群集自动缩放程序参数？][autoscaler-parameters]。

群集和水平 pod autoscalers 可以一起使用，并且通常部署在群集中。 결합된 경우 Horizontal Pod Autoscaler는 애플리케이션 수요를 충족하는 데 필요한 개수의 Pod를 실행하는 데 중점을 둡니다. 클러스터 자동 크기 조정기는 예약된 Pod를 지원하는 데 필요한 개수의 노드를 실행하는 데 중점을 둡니다.

> [!NOTE]
> 클러스터 자동 크기 조정기를 사용하면 수동 크기 조정이 사용하지 않도록 설정됩니다. 클러스터 자동 크기 조정기가 필요한 노드 수를 결정하도록 해보겠습니다. 클러스터 크기를 수동으로 조정하려면 [클러스터 자동 크기 조정기를 사용하지 않도록 설정](#disable-the-cluster-autoscaler)합니다.

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS 클러스터를 만들고 클러스터 자동 크기 조정기를 사용하도록 설정

如果需要创建 AKS 群集，请使用[az AKS create][az-aks-create]命令。 若要在群集的节点池上启用并配置群集自动缩放程序，请使用 *--自动缩放程序*参数，并指定节点 *-最小计数*和 *--最大计数*。

> [!IMPORTANT]
> 클러스터 자동 크기 조정기는 Kubernetes 구성 요소입니다. AKS 클러스터는 가상 머신 확장 집합을 노드에 사용하지만, Azure Portal에서 또는 Azure CLI를 사용하여 확장 집합 자동 크기 조정에 대한 설정을 직접 설정하거나 편집하지 마세요. Kubernetes 클러스터 자동 크기 조정기가 필수 크기 조정 설정을 자동으로 관리하게 두세요. 有关详细信息，请参阅可否[修改节点资源组中的 AKS 资源？](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

以下示例创建一个 AKS 群集，其中包含一个虚拟机规模集支持的单一节点池。 它还会在群集的节点池中启用群集自动缩放程序，并将最小值设置为*1* ，最大值为*3*个节点：

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

클러스터를 만들고 클러스터 자동 크기 조정기 설정을 구성하는 데 몇 분 정도 걸립니다.

## <a name="change-the-cluster-autoscaler-settings"></a>클러스터 자동 크기 조정기 설정 변경

> [!IMPORTANT]
> 如果 AKS 群集中有多个节点池，请跳到 "[带多个代理池的自动缩放" 部分](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)。 具有多个代理池的群集要求使用 `az aks nodepool` 命令集来更改节点池特定属性，而不是 `az aks`。

在上一步中，若要创建 AKS 群集或更新现有节点池，请将 "群集自动缩放程序最小节点计数" 设置为 " *1*"，并将 "最大节点计数" 设置为*3*。 애플리케이션 수요가 변경되면 클러스터 자동 크기 조정기의 노드 수를 조정해야 할 수 있습니다.

若要更改节点计数，请使用[az aks update][az-aks-update]命令。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

上面的示例将*myAKSCluster*中单节点池上的 cluster 自动缩放程序更新为最小值为*1* ，最大值为*5*节点。

> [!NOTE]
> 不能设置比当前为节点池设置的最小节点计数。 예를 들어 현재 최소 개수가 *1*로 설정되어 있으면 최소 개수를 *3*으로 업데이트할 수 없습니다.

애플리케이션 및 서비스의 성능을 모니터링하고 클러스터 자동 크기 조정기 노드 수를 필요한 성능과 일치하도록 조정합니다.

## <a name="disable-the-cluster-autoscaler"></a>클러스터 자동 크기 조정기 사용 안 함

如果不再想要使用群集自动缩放程序，可以使用[az aks update][az-aks-update]命令禁用该群集，并指定 *--disable-自动缩放程序*参数。 클러스터 자동 크기 조정기가 사용하지 않도록 설정되면 노드가 제거되지 않습니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

使用[az aks scale][az-aks-scale]命令禁用群集自动缩放程序后，可以手动缩放群集。 如果使用水平 pod 自动缩放程序，则该功能将继续运行，同时禁用群集自动缩放程序，但如果所有节点资源都在使用中，则无法计划盒箱。

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>重新启用已禁用的群集自动缩放程序

如果你想要在现有群集上重新启用群集自动缩放程序，可以使用[az aks update][az-aks-update]命令重新启用该群集，并指定 *--自动缩放程序*、 *--min-* 和 *--max*参数。

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>检索群集自动缩放程序日志和状态

若要诊断和调试自动缩放程序事件，可以从自动缩放程序外接程序检索日志和状态。

AKS 代表您管理群集自动缩放程序并在托管控制平面中运行该群集。 必须将主节点日志配置为查看结果。

若要将日志配置为从群集自动缩放程序推送到 Log Analytics 执行以下步骤。

1. 设置诊断日志规则，以便将群集自动缩放程序日志推送 Log Analytics。 [此处详细说明](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs)，请确保在为 "日志" 选择选项时选中 `cluster-autoscaler` 的复选框。
1. 通过 Azure 门户单击群集上的 "日志" 部分。
1. 将以下示例查询输入 Log Analytics：

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

只要有要检索的日志，就会看到类似于下面返回的日志。

![Log Analytics 日志](media/autoscaler/autoscaler-logs.png)

群集自动缩放程序还会将运行状况状态写出到名为 `cluster-autoscaler-status`的 configmap 中。 若要检索这些日志，请执行以下 `kubectl` 命令。 将报告配置了群集自动缩放程序的每个节点池的运行状况状态。

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

若要详细了解自动缩放程序中记录的内容，请阅读[Kubernetes/自动缩放程序 GitHub 项目](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why)的常见问题解答。

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>使用启用了多个节点池的群集自动缩放程序

群集自动缩放程序可以与启用的[多个节点池](use-multiple-node-pools.md)一起使用。 按照该文档了解如何启用多个节点池，以及如何将其他节点池添加到现有群集。 同时使用这两个功能时，将在群集中的每个单独节点池中启用群集自动缩放程序，并可以将唯一自动缩放规则传递到每个节点。

以下命令假设你遵循本文档前面的[初始说明](#create-an-aks-cluster-and-enable-the-cluster-autoscaler)，并想要将现有节点池的最大计数从*3*更新为*5*。 使用[az aks nodepool update][az-aks-nodepool-update]命令更新现有节点池的设置。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

可以通过[az aks nodepool update][az-aks-nodepool-update]和自动缩放程序参数 `--disable-cluster-autoscaler` 传递来禁用群集。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

如果要在现有群集上重新启用群集自动缩放程序，可以使用[az aks nodepool update][az-aks-nodepool-update]命令重新启用群集，同时指定 *--enable-cluster-自动缩放程序*、 *--min*和 *--max*参数。

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 노드 수를 자동으로 조정하는 방법을 설명했습니다. Horizontal Pod Autoscaler를 사용하여 애플리케이션을 실행하는 Pod 수를 자동으로 조정할 수도 있습니다. 有关使用横向 pod 自动缩放程序的步骤，请参阅[在 AKS 中缩放应用程序][aks-scale-apps]。

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
