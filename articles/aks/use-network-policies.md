---
title: 在 Azure Kubernetes 服务 (AKS) 中使用网络策略保护 Pod
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用 Kubernetes 网络策略保护流入流出 Pod 的流量
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: 250c4fc6e51bacc68c965394b9fd430b1b75a52c
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447168"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中使用网络策略保护 Pod 之间的流量

在 Kubernetes 中运行最新的基于微服务的应用程序时，通常想要控制哪些组件可以相互通信。 最小特权原则应该应用于流量在 AKS 群集中的各 Pod 之间的流动方式。 例如，你可能想要阻止直接流向后端应用程序的流量。 在 Kubernetes 中，使用“网络策略”，可在群集中定义用于 Pod 之间的入口和出口流量的规则。

本文介绍如何在 AKS 中使用网络策略来控制 Pod 之间的流量流。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 2.0.56 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

要创建具有网络策略的 AKS，请先对订阅启用功能标志。 若要注册 EnableNetworkPolicy 功能标志，请使用 [az feature register][az-feature-register] 命令，如以下示例所示：

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

状态显示为“已注册”需要几分钟时间。 可以使用 [az feature list][az-feature-list] 命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-network-policy"></a>网络策略概述

默认情况下，AKS 群集中的所有 Pod 都可以无限制地发送和接收流量。 为了提高安全性，可定义用来控制流量流的规则。 例如，后端应用程序通常只向所需的前端服务公开，或者数据库组件仅可由连接到它们的应用程序层访问。

网络策略属于 Kubernetes 资源，可用于控制 Pod 之间的流量流。 可选择基于分配的标签、命名空间或流量端口等设置来允许或拒绝流量。 网络策略被定义为 YAML 清单，并可以作为也创建部署或服务的更大清单的一部分添加。

要查看发挥作用的网络策略，让我们进行创建，然后展开按如下方式定义流量流的策略：

* 拒绝流向 Pod 的所有流量。
* 允许基于 Pod 标签的流量。
* 允许基于命名空间的流量。

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>创建 AKS 群集并启用网络策略

创建群集后，才能启用网络策略。 无法在现有 AKS 群集上启用网络策略。 

若要对 AKS 使用网络策略，必须使用 [Azure CNI 插件][azure-cni]并定义自己的虚拟网络和子网。 如需详细了解如何规划所需的子网范围，请参阅[配置高级网络][use-advanced-networking]。

以下示例脚本：

* 创建虚拟网络和子网。
* 创建用于 AKS 群集的 Azure Active Directory (AD) 服务主体。
* 对虚拟网络的 AKS 服务主体授予“参与者”权限。
* 在定义的虚拟网络中创建 AKS 群集并启用网络策略。

提供自己的安全 SP_PASSWORD。 如果需要，替换 RESOURCE_GROUP_NAME 和 CLUSTER_NAME 变量：

```azurecli-interactive
SP_PASSWORD=mySecurePassword
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP_ID=$(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.4 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy calico
```

创建群集需要几分钟时间。 完成后，使用 [az aks get-credentials][az-aks-get-credentials] 命令将 `kubectl` 配置为连接到 Kubernetes 群集。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据：

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>拒绝流向 Pod 的所有入站流量

定义规则以允许特定网络流量之前，请首先创建用于拒绝所有流量的网络策略。 使用此策略，可为你提供起始点，仅将所需流量加入允许列表。 此外，还可清楚看到，应用网络策略后，相关流量被丢弃。

对于示例应用程序环境和流量规则，请首先创建名为 development 的命名空间，以运行我们的示例 Pod：

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

现在，创建运行 NGINX 的示例后端 Pod。 此后端 Pod 可用于模拟示例后端基于 Web 的应用程序。 在 development 命名空间中创建此 Pod，并且打开端口 80，以提供 Web 流量。 将 Pod 贴上标签：app=webapp,role=backend，以便我们可在下一节中使用网络策略定向到它：

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

若要测试可成功访问默认 NGINX 网页，请另外创建一个 Pod，并附加终端会话：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 确认可以访问默认的 NGINX 网页：

```console
wget -qO- http://backend
```

以下示例输出显示了返回的默认 NGINX 网页：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

退出附加的终端会话。 将自动删除测试 Pod：

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>创建并应用网络策略

现在，即已确认可以在示例后端 Pod 上访问基本的 NGINX 网页，请创建网络策略来拒绝所有流量。 创建名为 `backend-policy.yaml` 的文件并粘贴以下 YAML 清单。 此清单使用 podSelector，将策略附加到具有 app:webapp,role:backend 标签的 Pod，例如示例 NGINX Pod。 入口下未定义任何规则，因此将拒绝流向 Pod 的所有入站流量：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

使用 [kubectl apply][kubectl-apply] 命令应用网络策略，并指定 YAML 清单的名称：

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>测试网络策略

让我们看看是否可以在后端 Pod 上再次访问 NGINX 网页。 创建另一个测试 Pod，并附加一个终端会话：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 确认是否可以访问默认的 NGINX 网页。 这一次，将超时值设为 2 秒。 网络策略现在阻止所有入站流量，因此无法加载页面，如下面的示例中所示：

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

退出附加的终端会话。 将自动删除测试 Pod：

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>允许基于 Pod 标签的入站流量

在上一节中，计划了后端 NGINX Pod 并创建了拒绝用于所有流量的网络策略。 现在，请创建前端 Pod 并更新网络策略，以允许来自前端 Pod 的流量。

更新网络策略，以允许来自具有标签 app:webapp,role:frontend 的 Pod 和任何命名空间的流量。 编辑上一个 backend-policy.yaml 文件，并添加 matchLabels 入口规则，使清单如以下示例中所示：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> 此网络策略使用 namespaceSelector 和 podSelector 元素作为入口规则。 YAML 语法对于入口规则是否相加很重要。 在此示例中，两个元素必须匹配要应用的入口规则。 1.12 之前的 Kubernetes 版本可能无法正确解释这些元素并按预期限制网络流量。 有关详细信息，请参阅[针对选择器的行为和来自选择器的行为][policy-rules]。

使用 [kubectl apply][kubectl-apply] 命令应用已更新的网络策略，并指定 YAML 清单的名称：

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

现在，计划标记为 app=webapp,role=frontend 的 Pod，并附加终端会话：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 确认是否可以访问默认的 NGINX 网页：

```console
wget -qO- http://backend
```

由于入口规则允许具有标签 app: webapp,role: frontend 的 Pod 的流量，因此允许来自前端 Pod 的流量。 以下示例输出显示了返回的默认 NGINX 网页：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

退出附加的终端会话。 将自动删除该 Pod：

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>测试没有匹配标签的 Pod

网络策略允许来自标记为 app: webapp,role: frontend 的 Pod 的流量，但应拒绝其他所有流量。 让我们测试没有这些标签的其他 Pod 无法访问后端 NGINX Pod。 创建另一个测试 Pod，并附加一个终端会话：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 确认是否可以访问默认的 NGINX 网页。 网络策略将阻止入站流量，因此无法加载页面，如下面的示例中所示：

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

退出附加的终端会话。 将自动删除测试 Pod：

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>仅允许来自定义命名空间的流量

在上述示例中，创建了拒绝所有流量的网络策略，然后更新了策略以允许来自具有特定标签的 Pod 的流量。 一个常见的其他需求是将流量限制为仅在给定命名空间内。 如果上述示例用于 development 命名空间中的流量，则你可能想要创建一个网络策略，用于阻止来自其他命名空间（例如，production）的流量访问 Pod。

首先，创建新的命名空间，模拟生产命名空间：

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

在具有标签 app=webapp,role=frontend 的 production 命名空间中计划测试 Pod。 附加终端会话：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 确认可以访问默认的 NGINX 网页：

```console
wget -qO- http://backend.development
```

由于 Pod 的标签匹配网络策略中当前允许的内容，因此允许该流量。 网络策略不会查看命名空间，只有 Pod 标签会查看。 以下示例输出显示了返回的默认 NGINX 网页：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

退出附加的终端会话。 将自动删除测试 Pod：

```console
exit
```

### <a name="update-the-network-policy"></a>更新网络策略

现在，更新入口规则 namespaceSelector 部分，以仅允许来自 development 命名空间的流量。 编辑 backend-policy.yaml 清单文件，如以下示例所示：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

在更复杂的示例中，可以定义多个入口规则，如使用 namespaceSelector，然后使用 podSelector。

使用 [kubectl apply][kubectl-apply] 命令应用已更新的网络策略，并指定 YAML 清单的名称：

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>测试更新后的网络策略

现在，在 production 命名空间中计划另一个 Pod，并附加终端会话：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 查看目前拒绝流量的网络策略：

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

退出测试 Pod：

```console
exit
```

拒绝来自 production 命名空间的流量后，现在在 development 命名空间中再次计划一个测试 Pod，并附加终端会话：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 查看允许流量的网络策略：

```console
wget -qO- http://backend
```

由于该 Pod 在匹配网络策略中当前允许的内容的命名空间中计划，因此允许该流量。 以下示例输出显示了返回的默认 NGINX 网页：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

退出附加的终端会话。 将自动删除测试 Pod：

```console
exit
```

## <a name="clean-up-resources"></a>清理资源

在本文中，我们将创建两个命名空间并应用网络策略。 若要清理这些资源，请使用 [kubectl delete][kubectl-delete] 命令并指定资源名称，如下所示：

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>后续步骤

有关网络资源的详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 中应用程序的网络概念][concepts-network]。

若要了解有关使用策略的详细信息，请参阅 [Kubernetes 网络策略][kubernetes-network-policies]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
