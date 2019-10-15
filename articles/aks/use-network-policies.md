---
title: 在 Azure Kubernetes 服务 (AKS) 中使用网络策略保护 Pod
description: 了解如何使用 Azure Kubernetes 服务（AKS）中的 Kubernetes 网络策略保护流入和流出 pod 的流量
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 6c7cf82381dfb895fdaa0f130e33b2dc9a6e7403
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169759"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中使用网络策略保护 Pod 之间的流量

在 Kubernetes 中运行最新的基于微服务的应用程序时，通常想要控制哪些组件可以相互通信。 最小特权原则应应用于 Azure Kubernetes 服务（AKS）群集中的 pod 之间流量如何流动。 假设你可能想要将流量直接阻止到后端应用程序。 使用 Kubernetes 中的*网络策略*功能，你可以为群集中的 pod 之间的入口和出口流量定义规则。

本文介绍如何安装网络策略引擎，以及如何创建 Kubernetes 网络策略来控制 AKS 中 pod 间的流量流。 网络策略应仅用于 AKS 中基于 Linux 的节点和 pod。

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 2.0.61 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

> [!TIP]
> 如果在预览期间使用了网络策略功能，我们建议你[创建一个新群集](#create-an-aks-cluster-and-enable-network-policy)。
> 
> 如果你想要继续使用在预览期间使用网络策略的现有测试群集，请将你的群集升级到最新 GA 版本的新 Kubernetes 版本，然后部署以下 YAML 清单来修复崩溃指标服务器和 Kubernetes板. 只有使用 Calico 网络策略引擎的群集才需要此修补程序。
>
> 作为安全方面的最佳做法，请[查看此 YAML 清单的内容][calico-aks-cleanup]，了解部署到 AKS 群集的内容。
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>网络策略概述

默认情况下，AKS 群集中的所有 pod 都可以发送和接收无限制的流量。 为了提高安全性，可定义用来控制流量流的规则。 例如，后端应用程序通常只公开给所需的前端服务。 或者，数据库组件只可供连接到它们的应用程序层访问。

网络策略是一种 Kubernetes 规范，用于定义 pod 之间通信的访问策略。 使用网络策略，您可以定义一组有序的规则来发送和接收流量，并将其应用到与一个或多个标签选择器匹配的 pod 集。

这些网络策略规则定义为 YAML 的清单。 网络策略可以作为更广泛清单的一部分包含，同时也会创建部署或服务。

### <a name="network-policy-options-in-aks"></a>AKS 中的网络策略选项

Azure 提供了两种实现网络策略的方式。 创建 AKS 群集时，可以选择网络策略选项。 创建群集后，无法更改策略选项：

* Azure 本身的实现，称为*Azure 网络策略*。
* *Calico 网络策略*，一种由[Tigera][tigera]构建的开源网络和网络安全解决方案。

这两个实现都使用 Linux *IPTables*来强制实施指定的策略。 策略被转换为允许和不允许的 IP 对集。 然后，这些对将作为 IPTable 筛选规则进行编程。

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Azure 和 Calico 策略及其功能之间的差异

| 功能                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| 受支持的平台                      | Linux                      | Linux                       |
| 支持的网络选项             | Azure CNI                  | Azure CNI 和 kubenet       |
| 符合 Kubernetes 规范 | 支持的所有策略类型 |  支持的所有策略类型 |
| 其他功能                      | 无                       | 扩展策略模型，由全局网络策略、全局网络集和主机终结点组成。 若要详细了解如何使用 `calicoctl` CLI 来管理这些扩展功能，请参阅[calicoctl 用户参考][calicoctl]。 |
| 支持                                  | 受 Azure 支持和工程团队支持 | Calico 社区支持。 有关其他付费支持的详细信息，请参阅[Project Calico 支持选项][calico-support]。 |
| 日志记录                                  | IPTables 中添加/删除的规则记录在 */var/log/azure-npm.log*下的每个主机上。 | 有关详细信息，请参阅[Calico 组件日志][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>创建 AKS 群集并启用网络策略

若要查看运行中的网络策略，请在定义流量流的策略上创建并展开：

* 拒绝流向 Pod 的所有流量。
* 允许基于 Pod 标签的流量。
* 允许基于命名空间的流量。

首先，让我们创建一个支持网络策略的 AKS 群集。 网络策略功能只能在创建群集时启用。 无法在现有 AKS 群集上启用网络策略。

若要使用 Azure 网络策略，必须使用[AZURE CNI 插件][azure-cni]，并定义自己的虚拟网络和子网。 有关如何规划所需子网范围的详细信息，请参阅[配置高级网络][use-advanced-networking]。 Calico 网络策略可与此同一 Azure CNI 插件或 Kubenet CNI 插件一起使用。

以下示例脚本：

* 创建虚拟网络和子网。
* 创建用于 AKS 群集的 Azure Active Directory （Azure AD）服务主体。
* 对虚拟网络的 AKS 服务主体授予“参与者”权限。
* 在定义的虚拟网络中创建 AKS 群集，并启用网络策略。
    * 使用*azure*网络策略选项。 若要改用 Calico 作为网络策略选项，请使用 @no__t 的参数。 注意:Calico 可以与 `--network-plugin azure` 或 `--network-plugin kubenet` 一起使用。

提供自己的安全 SP_PASSWORD。 可以替换*RESOURCE_GROUP_NAME*和*CLUSTER_NAME*变量：

```azurecli-interactive
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
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

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
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

创建群集需要几分钟时间。 群集准备就绪后，将 `kubectl` 配置为使用[az aks][az-aks-get-credentials]命令连接到 Kubernetes 群集。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据：

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>拒绝流向 Pod 的所有入站流量

定义规则以允许特定网络流量之前，请首先创建用于拒绝所有流量的网络策略。 此策略为你提供了开始仅对所需流量进行允许列表的起点。 此外，还可清楚看到，应用网络策略后，相关流量被丢弃。

对于应用程序环境和流量规则示例，让我们首先创建一个名为 "*开发*" 的命名空间来运行示例 pod：

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

创建运行 NGINX 的示例后端 pod。 此后端 pod 可用于模拟基于 web 的示例后端应用程序。 在 development 命名空间中创建此 Pod，并且打开端口 80，以提供 Web 流量。 将 Pod 贴上标签：app=webapp,role=backend，以便我们可在下一节中使用网络策略定向到它：

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

创建另一个 pod 并附加终端会话，以测试是否可以成功访问默认的 NGINX 网页：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 来确认你可以访问默认的 NGINX 网页：

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

退出附加的终端会话。 自动删除测试 pod。

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>创建并应用网络策略

现在，你已确认你可以使用示例后端 pod 上的基本 NGINX 网页，创建网络策略来拒绝所有流量。 创建名为 `backend-policy.yaml` 的文件并粘贴以下 YAML 清单。 此清单使用*podSelector*将策略附加到具有*app： webapp，role：后端*标签的 POD，如示例 NGINX pod。 入口下未定义任何规则，因此将拒绝流向 Pod 的所有入站流量：

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

使用[kubectl apply][kubectl-apply]命令应用网络策略，并指定 YAML 清单的名称：

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>测试网络策略


让我们看看是否可以在后端 pod 上再次使用 NGINX 网页。 创建另一个测试 Pod，并附加一个终端会话：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 查看是否可以访问默认的 NGINX 网页。 这一次，将超时值设为 2 秒。 网络策略现在会阻止所有入站流量，因此无法加载页面，如以下示例中所示：

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

退出附加的终端会话。 自动删除测试 pod。

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>允许基于 Pod 标签的入站流量

在上一部分中，计划了后端 NGINX pod，并创建了网络策略来拒绝所有流量。 让我们创建一个前端 pod，并更新网络策略以允许来自前端的流量。

更新网络策略，以允许来自具有标签 app:webapp,role:frontend 的 Pod 和任何命名空间的流量。 编辑上一个*后端-yaml*文件，并添加*matchLabels*入口规则，以便您的清单如以下示例所示：

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
> 此网络策略使用 namespaceSelector 和 podSelector 元素作为入口规则。 要使入口规则成为累加性，YAML 语法非常重要。 在此示例中，两个元素必须匹配要应用的入口规则。 *1.12*之前的版本 Kubernetes 可能不会正确解释这些元素，并按预期限制网络流量。 有关此行为的详细信息，请参阅[to 和 from 选择器的行为][policy-rules]。

使用[kubectl apply][kubectl-apply]命令应用更新后的网络策略，并指定 YAML 清单的名称：

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

计划一个标记为*app = webapp、role = 前端*并附加终端会话的 pod：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 查看是否可以访问默认的 NGINX 网页：

```console
wget -qO- http://backend
```

由于入口规则允许具有标签为*app： webapp、role：前端*的 pod 的流量，因此允许来自前端 pod 的流量。 以下示例输出显示了返回的默认 NGINX 网页：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

退出附加的终端会话。 自动删除 pod。

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>测试没有匹配标签的 Pod

网络策略允许来自标记为 app: webapp,role: frontend 的 Pod 的流量，但应拒绝其他所有流量。 让我们来看看是否有其他不带标签的 pod 可以访问后端 NGINX pod。 创建另一个测试 Pod，并附加一个终端会话：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 查看是否可以访问默认的 NGINX 网页。 网络策略阻止入站流量，因此无法加载页面，如以下示例中所示：

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

退出附加的终端会话。 自动删除测试 pod。

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>仅允许来自定义命名空间的流量

在前面的示例中，创建了拒绝所有流量的网络策略，然后将策略更新为允许来自特定标签的 pod 的流量。 另一种常见的需要是将流量仅限制在给定的命名空间内。 如果前面的示例针对的是*开发*命名空间中的流量，请创建网络策略，阻止来自其他命名空间（如*生产*）的流量到达 pod。

首先，创建新的命名空间，模拟生产命名空间：

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

在具有标签 app=webapp,role=frontend 的 production 命名空间中计划测试 Pod。 附加终端会话：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 来确认你可以访问默认的 NGINX 网页：

```console
wget -qO- http://backend.development
```

由于 pod 的标签与网络策略中当前允许的内容相匹配，因此允许流量。 网络策略不会查看命名空间，只有 Pod 标签会查看。 以下示例输出显示了返回的默认 NGINX 网页：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

退出附加的终端会话。 自动删除测试 pod。

```console
exit
```

### <a name="update-the-network-policy"></a>更新网络策略

让我们更新入站规则*namespaceSelector*部分，只允许来自*开发*命名空间内部的流量。 编辑 backend-policy.yaml 清单文件，如以下示例所示：

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

在更复杂的示例中，可以定义多个入站规则，例如*namespaceSelector*和*podSelector*。

使用[kubectl apply][kubectl-apply]命令应用更新后的网络策略，并指定 YAML 清单的名称：

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>测试更新后的网络策略

计划*生产*命名空间中的另一个 pod，并附加终端会话：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 查看网络策略现在是否拒绝流量：

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

退出测试 Pod：

```console
exit
```

从*生产*命名空间拒绝流量后，请在*开发*命名空间中计划一个测试盒，并附加一个终端会话：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

在 shell 提示符下，使用 `wget` 查看网络策略是否允许流量：

```console
wget -qO- http://backend
```

允许流量，因为已在命名空间中计划与网络策略允许的内容相匹配的 pod。 以下示例输出显示了返回的默认 NGINX 网页：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

退出附加的终端会话。 自动删除测试 pod。

```console
exit
```

## <a name="clean-up-resources"></a>清理资源

本文创建了两个命名空间，并应用了网络策略。 若要清理这些资源，请使用[kubectl delete][kubectl-delete]命令并指定资源名称：

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>后续步骤

有关网络资源的详细信息，请参阅[Azure Kubernetes 服务中的应用程序的网络概念（AKS）][concepts-network]。

若要了解有关策略的详细信息，请参阅[Kubernetes 网络策略][kubernetes-network-policies]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
