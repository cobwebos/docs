---
title: AKS(Azure Kubernetes Service)에서 포털을 사용하여 가상 노드 만들기
description: Azure Portal을 통해 가상 노드를 사용하여 Pod를 실행하는 AKS(Azure Kubernetes Service) 클러스터를 만드는 방법을 알아봅니다.
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 6a50663fd0cc907e0dc97b50decd8b6edbaa42cb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713205"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Azure Portal에서 가상 노드를 사용하는 AKS(Azure Kubernetes Service) 클러스터 만들기 및 구성

가상 노드를 사용하면 AKS(Azure Kubernetes Service) 클러스터에서 워크로드를 신속하게 배포할 수 있습니다. 가상 노드를 사용하면 Pod를 신속하게 프로비전할 수 있으며, 실행 시간(초) 단위로 요금이 청구됩니다. 크기 조정 시나리오에서 Kubernetes 클러스터 자동 크기 조정기가 추가 Pod를 실행하는 VM 컴퓨팅 노드를 배포할 때까지 기다릴 필요가 없습니다. 只有 Linux pod 和节点支持虚拟节点。

이 문서에서는 가상 노드를 사용하도록 설정된 가상 네트워크 리소스 및 AKS 클러스터를 만들고 구성하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

虚拟节点在 Azure 容器实例（ACI）和 AKS 群集中运行的 pod 之间启用网络通信。 이 통신을 제공하기 위해 가상 네트워크 서브넷이 만들어지고 위임된 사용 권한이 할당됩니다. 가상 노드는 *고급* 네트워킹을 사용하여 만든 AKS 클러스터에만 작동합니다. 기본적으로 AKS 클러스터는 *기본* 네트워킹을 사용하여 만듭니다. 이 문서에서는 가상 네트워크 및 서브넷을 만든 다음, 고급 네트워킹을 사용하는 AKS 클러스터에 배포하는 방법을 보여 줍니다.

이전에 ACI를 사용하지 않은 경우 구독에서 서비스 공급자를 등록합니다. 你可以使用[az provider list][az-provider-list]命令检查 ACI 提供程序注册的状态，如以下示例中所示：

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* 공급자는 다음 예제 출력에 나온 대로 *Registered*로 보고됩니다.

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

如果提供程序显示为*NotRegistered*，请使用[az provider register][az-provider-register]注册提供程序，如以下示例中所示：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>지역별 가용성

虚拟节点部署支持以下区域：

* 澳大利亚东部（australiaeast）
* 美国中部（centralus）
* 미국 동부(eastus)
* 美国东部2（eastus2）
* 日本东部（japaneast）
* 북유럽(northeurope)
* 东南亚（southeastasia）
* 美国中部（westcentralus）
* 유럽 서부(westeurope)
* 미국 서부(westus)
* 미국 서부 2(westus2)

## <a name="known-limitations"></a>알려진 제한 사항
虚拟节点功能很大程度上依赖于 ACI 的功能集。 虚拟节点尚不支持以下方案

* 使用服务主体拉取 ACR 映像。 [解决方法](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry)是使用[Kubernetes 机密](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [虚拟网络限制](../container-instances/container-instances-vnet.md)，包括 VNet 对等互连、Kubernetes 网络策略和网络安全组发送到 internet 的出站流量。
* 初始化容器
* [主机别名](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* ACI 中 exec 的[参数](../container-instances/container-instances-exec.md#restrictions)
* [Daemonset](concepts-clusters-workloads.md#statefulsets-and-daemonsets)不会将 pod 部署到虚拟节点
* 虚拟节点不支持[Windows Server 节点（当前在 AKS 中为预览版）](windows-container-cli.md) 。 你可以使用虚拟节点来计划 Windows Server 容器，而无需在 AKS 群集中使用 Windows Server 节点。

## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com 에서 Azure Portal에 로그인합니다.

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** > **Kubernetes Service**를 선택합니다.

**기본** 페이지에서 다음 옵션을 구성합니다.

- *프로젝트 세부 정보*: Azure 구독을 선택하고 *myResourceGroup* 같은 Azure 리소스 그룹을 선택하거나 만듭니다. *myAKSCluster* 같은 **Kubernetes 클러스터 이름**을 입력합니다.
- *클러스터 세부 정보*: AKS 클러스터의 지역, Kubernetes 버전 및 DNS 이름 접두사를 선택합니다.
- *主节点池*：为 AKS 节点选择 VM 大小。 AKS 클러스터를 배포한 후에는 VM 크기를 변경할 수 **없습니다**.
     - 클러스터에 배포할 노드 수를 선택합니다. 이 문서에서는 **노드 수**를 *1*로 설정합니다. 클러스터를 배포한 후에 노드 수를 조정할 수 **있습니다**.

单击 "**下一步：缩放**"。

在 "**缩放**" 页上，选择 "**虚拟节点**" 下的 "*启用*"。

![AKS 클러스터를 만들고 가상 노드를 사용하도록 설정](media/virtual-nodes-portal/enable-virtual-nodes.png)

기본적으로 Azure Active Directory 서비스 사용자가 생성됩니다. 이 서비스 사용자는 클러스터 통신 및 다른 Azure 서비스와의 통합에 사용됩니다.

고급 네트워킹에 대한 클러스터도 구성됩니다. 가상 노드는 자체 Azure 가상 네트워크 서브넷을 사용하도록 구성됩니다. 이 서브넷은 AKS 클러스터 간에 Azure 리소스를 연결할 수 있는 위임된 권한을 갖습니다. 위임된 서브넷이 아직 없는 경우 Azure Portal에서는 가상 노드에 사용할 Azure 가상 네트워크 및 서브넷을 만들고 구성합니다.

**검토 + 만들기**를 선택합니다. 유효성 검사가 완료되면 **만들기**를 선택합니다.

AKS 클러스터를 만들고 사용 준비를 마칠 때까지 몇 분 정도 걸립니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. Kubernetes 클러스터를 관리하려면 Kubernetes 명령줄 클라이언트인 [kubectl][kubectl]을 사용하세요. `kubectl` 클라이언트가 Azure Cloud Shell에 사전 설치됩니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택합니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

使用[az aks get][az-aks-get-credentials]命令将 `kubectl` 配置为连接到 Kubernetes 群集。 다음 예제는 *myResourceGroup*이라는 리소스 그룹에서 *myAKSCluster*라는 클러스터의 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get][kubectl-get] 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```azurecli-interactive
kubectl get nodes
```

다음 예제 출력은 생성된 단일 VM 노드를 보여준 후 Linux용 가상 노드 *virtual-node-aci-linux*를 보여 줍니다.

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>샘플 앱 배포

Azure Cloud Shell에서 `virtual-node.yaml`이라는 파일을 만들고 다음 YAML에 복사합니다. 若要计划节点上的容器，请定义[nodeSelector][node-selector]和[toleration][toleration] 。 이러한 설정을 통해 가상 노드에서 Pod를 예약하고 기능이 성공적으로 활성화되었는지 확인할 수 있습니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

使用[kubectl apply][kubectl-apply]命令运行应用程序。

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

使用带有 `-o wide` 参数的[kubectl get][kubectl-get] pod 命令输出 pod 和计划节点的列表。 `virtual-node-helloworld` Pod는 `virtual-node-linux` 노드에서 예약되었습니다.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod에는 가상 노드에 사용하도록 위임된 Azure 가상 네트워크 서브넷의 내부 IP 주소가 할당됩니다.

> [!NOTE]
> 如果使用存储在 Azure 容器注册表中的映像，请[配置并使用 Kubernetes 机密][acr-aks-secrets]。 虚拟节点当前的限制是不能使用集成 Azure AD 服务主体身份验证。 비밀을 사용하지 않으면 가상 노드에서 예약된 Pod가 시작되지 않고 오류 `HTTP response status code 400 error code "InaccessibleImage"`가 보고됩니다.

## <a name="test-the-virtual-node-pod"></a>가상 노드 Pod 테스트

가상 노드에서 실행되는 Pod를 테스트하려면 웹 클라이언트를 사용하여 데모 애플리케이션으로 이동합니다. Pod에는 내부 IP 주소가 할당되므로 AKS 클러스터의 다른 Pod에서 이 연결을 신속하게 테스트할 수 있습니다. 테스트 Pod를 만들고 여기에 터미널 세션을 연결합니다.

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

`apt-get`을 사용하여 Pod에 `curl`을 설치합니다.

```azurecli-interactive
apt-get update && apt-get install -y curl
```

이제 *http://10.241.0.4* 같은 `curl` 을 사용하여 Pod 주소에 액세스합니다. 앞의 `kubectl get pods` 명령에서 본 내부 IP 주소를 입력합니다.

```azurecli-interactive
curl -L http://10.241.0.4
```

다음과 같이 축소된 예제 출력처럼 데모 애플리케이션이 표시됩니다.

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

`exit` 명령을 사용하여 테스트 Pod에 대한 터미널 세션을 종료합니다. 세션이 종료되면 Pod가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 노드에서 Pod를 예약하고 프라이빗 내부 IP 주소를 할당했습니다. 그 대신 서비스 배포를 만들고 부하 분산 장치 또는 수신 컨트롤러를 통해 Pod로 트래픽을 라우팅할 수도 있습니다. 有关详细信息，请参阅[在 AKS 中创建基本入口控制器][aks-basic-ingress]。

가상 노드는 AKS에서 크기 조정 솔루션의 구성 요소 중 하나입니다. 크기 조정 솔루션에 대한 자세한 내용은 다음 문서를 참조하세요.

- [使用 Kubernetes 横向 pod 自动缩放程序][aks-hpa]
- [使用 Kubernetes cluster 自动缩放程序][aks-cluster-autoscaler]
- [查看虚拟节点的自动缩放示例][virtual-node-autoscale]
- [阅读有关虚拟 Kubelet 开放源代码库的详细信息][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
