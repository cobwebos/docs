---
title: 使用网络策略保护 Pod 流量
titleSuffix: Azure Kubernetes Service
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用 Kubernetes 网络策略保护流入流出 Pod 的流量
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 598747c0d64db2ae62f740dca4c3e4141f2562f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050474"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中使用网络策略保护 Pod 之间的流量

在 Kubernetes 中运行最新的基于微服务的应用程序时，通常想要控制哪些组件可以相互通信。 对于在 Azure Kubernetes 服务 (AKS) 群集中的 Pod 之间流量的流动方式，应该应用最低特权原则。 假设你要阻止流量直接流入后端应用程序。 在 Kubernetes 中，使用“网络策略”可在群集中定义用于 Pod 之间的入口和出口流量的规则。

本文介绍如何安装网络策略引擎，并创建 Kubernetes 网络策略来控制 AKS 中 Pod 之间的流量流动方式。 应该只对 AKS 中基于 Linux 的节点和 Pod 使用网络策略。

## <a name="before-you-begin"></a>准备阶段

需要安装并配置 Azure CLI 2.0.61 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

> [!TIP]
> 如果你在预览期使用了网络策略功能，则我们建议[创建新的群集](#create-an-aks-cluster-and-enable-network-policy)。
> 
> 如果你想要继续使用现有的测试群集，而这些群集在预览期使用了网络策略，请将群集升级到适用于最新正式版的新 Kubernetes 版本，然后部署以下 YAML 清单，以修复崩溃指标服务器和 Kubernetes 仪表板。 只有使用 Calico 网络策略引擎的群集才需要进行这种修复。
>
> 作为安全最佳做法，请[查看此 YAML 清单的内容][calico-aks-cleanup]，以了解 AKS 群集中部署的内容。
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>网络策略概述

默认情况下，AKS 群集中的所有 Pod 可以无限制地发送和接收流量。 为了提高安全性，可定义用来控制流量流的规则。 例如，后端应用程序通常只向所需的前端服务公开。 或者，数据库组件只能由连接到它们的应用层访问。

网络策略是一种 Kubernetes 规范，其中针对 Pod 之间的通信定义了访问策略。 使用网络策略可以定义有关发送和接收流量的有序规则集，并将其应用到与一个或多个标签选择器相匹配的 Pod 集合。

这些网络策略规则定义为 YAML 清单。 可以包含网络策略作为也会创建部署或服务的更丰富清单的一部分。

### <a name="network-policy-options-in-aks"></a>AKS 中的网络策略选项

Azure 提供两种方式来实现网络策略。 可以在创建 AKS 群集时选择一个网络策略选项。 创建群集后无法更改策略选项：

* Azure 自身的实现，称为“Azure 网络策略”。
* Calico 网络策略 - 由 [Tigera][tigera] 建立的开源网络和网络安全解决方案。

这两个实现都使用 Linux *IPTables* 来实施指定的策略。 策略将转换为一系列允许和禁止的 IP 对。 然后，这些对将编程为 IPTable 筛选规则。

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Azure 与 Calico 策略及其功能之间的差异

| 功能                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| 支持的平台                      | Linux                      | Linux                       |
| 支持的网络选项             | Azure CNI                  | Azure CNI 和 kubenet       |
| 符合 Kubernetes 规范 | 支持的所有策略类型 |  支持的所有策略类型 |
| 其他功能                      | 无                       | 扩展的策略模型，包括全局网络策略、全局网络集和主机终结点。 有关使用 `calicoctl` CLI 管理这些扩展功能的详细信息，请参阅 [calicoctl 用户参考][calicoctl]。 |
| 支持                                  | 由 Azure 支持部门和工程团队提供支持 | 由 Azure 社区提供支持。 有关其他付费支持的详细信息，请参阅 [Project Calico 支持选项][calico-support]。 |
| 日志记录                                  | 在 IPTables 中添加/删除的规则将记录到每个主机上的 */var/log/azure-npm.log* 下。 | 有关详细信息，请参阅 [Calico 组件日志][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>创建 AKS 群集并启用网络策略

为了了解网络策略的运行方式，让我们创建然后扩展一个定义流量流的策略：

* 拒绝流向 Pod 的所有流量。
* 允许基于 Pod 标签的流量。
* 允许基于命名空间的流量。

首先，让我们创建一个支持网络策略的 AKS 群集。 

> [!IMPORTANT]
>
> 只能在创建群集时启用网络策略功能。 无法在现有 AKS 群集上启用网络策略。

若要使用 Azure 网络策略，必须使用 [Azure CNI 插件][azure-cni]并定义自己的虚拟网络和子网。 有关如何规划所需子网范围的更多详细信息，请参阅[配置高级网络][use-advanced-networking]。 Calico 网络策略可与此 Azure CNI 插件配合使用，也可与 Kubenet CNI 插件配合使用。

以下示例脚本：

* 创建虚拟网络和子网。
* 创建用于 AKS 群集的 Azure Active Directory (Azure AD) 服务主体。
* 对虚拟网络的 AKS 服务主体授予“参与者”权限。
* 在定义的虚拟网络中创建 AKS 群集并启用网络策略。
    * 系统使用 Azure 网络策略选项。 若要改用 Calico 作为网络策略选项，请使用 `--network-policy calico` 参数。 注意：Calico 既可与 `--network-plugin azure` 一起使用，也可与 `--network-plugin kubenet` 一起使用。

请注意，可以使用托管标识而不是服务主体来获得权限。 有关详细信息，请参阅[使用托管标识](use-managed-identity.md)。

提供自己的安全 SP_PASSWORD。 可以替换 *RESOURCE_GROUP_NAME* 和 *CLUSTER_NAME* 变量：

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

创建群集需要几分钟时间。 群集准备就绪后，使用 [az aks get-credentials][az-aks-get-credentials] 命令将 `kubectl` 配置为连接到 Kubernetes 群集。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据：

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>拒绝流向 Pod 的所有入站流量

定义规则以允许特定网络流量之前，请首先创建用于拒绝所有流量的网络策略。 使用此策略，可为你提供起始点，仅为所需的流量创建允许列表。 此外，还可清楚看到，应用网络策略后，相关流量被丢弃。

对于示例应用程序环境和流量规则，让我们先创建名为 *development* 的命名空间，以运行示例 Pod：

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

创建运行 NGINX 的示例后端 Pod。 此后端 Pod 可用于模拟基于 Web 的示例后端应用程序。 在 development 命名空间中创建此 Pod，并且打开端口 80，以提供 Web 流量 。 将 Pod 贴上标签：app=webapp,role=backend，以便我们可在下一节中使用网络策略定向到它：

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80
```

创建另一个 Pod 并附加终端会话，以测试是否可以成功访问默认的 NGINX 网页：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development
```

在 shell 提示符下，使用 `wget` 确认是否可以访问默认的 NGINX 网页：

```console
wget -qO- http://backend
```

以下示例输出显示了返回的默认 NGINX 网页：

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

退出附加的终端会话。 测试 Pod 将自动删除。

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>创建并应用网络策略

确认可以在示例后端 Pod 上使用基本的 NGINX 网页后，接下来请创建一个拒绝所有流量的网络策略。 创建名为 `backend-policy.yaml` 的文件并粘贴以下 YAML 清单。 此清单使用 *podSelector* 将策略附加到具有 *app:webapp,role:backend* 标签的 Pod，类似于示例 NGINX Pod。 入口下未定义任何规则，因此将拒绝流向 Pod 的所有入站流量：

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

转到 [https://shell.azure.com](https://shell.azure.com)，以在浏览器中打开 Azure Cloud Shell。

使用 [kubectl apply][kubectl-apply] 命令应用网络策略，并指定 YAML 清单的名称：

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>测试网络策略

让我们看看是否可以在后端 Pod 上再次使用 NGINX 网页。 创建另一个测试 Pod，并附加一个终端会话：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development
```

在 shell 提示符下，使用 `wget` 确认是否可以访问默认的 NGINX 网页。 这一次，将超时值设为 2 秒。 网络策略现在会阻止所有入站流量，因此无法加载页面，如以下示例中所示：

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

退出附加的终端会话。 测试 Pod 将自动删除。

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>允许基于 Pod 标签的入站流量

在上一部分，我们已计划了一个后端 NGINX Pod，并创建了拒绝所有流量的网络策略。 让我们创建一个前端 Pod，并更新网络策略以允许来自前端 Pod 的流量。

更新网络策略，以允许来自具有标签 app:webapp,role:frontend 的 Pod 和任何命名空间的流量。 编辑前面所述的 *backend-policy.yaml* 文件，并添加 *matchLabels* 入口规则，使清单如以下示例所示：

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
> 此网络策略使用 namespaceSelector 和 podSelector 元素作为入口规则 。 YAML 语法对于入口规则的严格性非常重要。 在此示例中，两个元素必须匹配要应用的入口规则。 低于 *1.12* 的 Kubernetes 版本可能无法正确解释这些元素和按预期限制网络流量。 有关此行为的详细信息，请参阅[目标和来源选择器的行为][policy-rules]。

使用 [kubectl apply][kubectl-apply] 命令应用已更新的网络策略，并指定 YAML 清单的名称：

```console
kubectl apply -f backend-policy.yaml
```

计划带有 *app=webapp,role=frontend* 标签的 Pod，并附加终端会话：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development
```

在 shell 提示符下，使用 `wget` 确认是否可以访问默认的 NGINX 网页：

```console
wget -qO- http://backend
```

由于入口规则允许带有标签 *app: webapp,role: frontend* 的 Pod 的流量，因此允许来自前端 Pod 的流量。 以下示例输出显示了返回的默认 NGINX 网页：

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

退出附加的终端会话。 该 Pod 将自动删除。

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>测试没有匹配标签的 Pod

网络策略允许来自标记为 app: webapp,role: frontend 的 Pod 的流量，但应拒绝其他所有流量。 让我们看看不带这些标签的另一个 Pod 是否可以访问后端 NGINX Pod。 创建另一个测试 Pod，并附加一个终端会话：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development
```

在 shell 提示符下，使用 `wget` 确认是否可以访问默认的 NGINX 网页。 网络策略将阻止入站流量，因此无法加载页面，如以下示例所示：

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

退出附加的终端会话。 测试 Pod 将自动删除。

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>仅允许来自定义命名空间的流量

在前面的示例中，你已创建拒绝所有流量的网络策略，然后更新了该策略，以允许来自具有特定标签的 Pod 的流量。 另一个常见需求是将流量限制在给定的命名空间内。 如果前面的示例适用于 *development* 命名空间中的流量，请创建一个网络策略用于阻止来自另一命名空间（例如 *production*）的流量访问 Pod。

首先，创建新的命名空间，模拟生产命名空间：

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

在具有标签 app=webapp,role=frontend 的 production 命名空间中计划测试 Pod 。 附加终端会话：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production
```

在 shell 提示符下，使用 `wget` 确认是否可以访问默认的 NGINX 网页：

```console
wget -qO- http://backend.development
```

由于 Pod 的标签匹配网络策略中当前允许的内容，因此允许该流量。 网络策略不会查看命名空间，只有 Pod 标签会查看。 以下示例输出显示了返回的默认 NGINX 网页：

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

退出附加的终端会话。 测试 Pod 将自动删除。

```console
exit
```

### <a name="update-the-network-policy"></a>更新网络策略

让我们更新入口规则 *namespaceSelector* 节，以仅允许来自 *development* 命名空间内部的流量。 编辑 backend-policy.yaml 清单文件，如以下示例所示：

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

在更复杂的示例中，可以定义多个入口规则，例如，分别指定 *namespaceSelector* 和 *podSelector*。

使用 [kubectl apply][kubectl-apply] 命令应用已更新的网络策略，并指定 YAML 清单的名称：

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>测试更新后的网络策略

在 *production* 命名空间中计划另一个 Pod，并附加终端会话：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production
```

在 shell 提示符下，使用 `wget` 查看目前拒绝流量的网络策略：

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

退出测试 Pod：

```console
exit
```

拒绝来自 *production* 命名空间的流量后，在 *development* 命名空间中计划一个测试 Pod，并附加终端会话：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development
```

在 shell 提示符下，使用 `wget` 查看允许流量的网络策略：

```console
wget -qO- http://backend
```

之所以允许流量，是因为该 Pod 计划在匹配网络策略中允许的内容的命名空间中。 以下示例输出显示了返回的默认 NGINX 网页：

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

退出附加的终端会话。 测试 Pod 将自动删除。

```console
exit
```

## <a name="clean-up-resources"></a>清理资源

在本文中，我们创建了两个命名空间并应用了一个网络策略。 若要清理这些资源，请使用 [kubectl delete][kubectl-delete] 命令并指定资源名称：

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>后续步骤

有关网络资源的详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 中应用程序的网络概念][concepts-network]。

若要详细了解策略，请参阅 [Kubernetes 网络策略][kubernetes-network-policies]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/maintenance/troubleshoot/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
