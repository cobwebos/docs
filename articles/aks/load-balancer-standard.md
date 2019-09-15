---
title: 使用 Azure Kubernetes Service （AKS）中的标准 SKU 负载均衡器
description: 了解如何结合使用负载均衡器和标准 SKU 来向 Azure Kubernetes 服务公开服务（AKS）。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/05/2019
ms.author: zarhoads
ms.openlocfilehash: 42323af40ee18a965363321196a04aa75c00aa40
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996940"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes Service （AKS）中的标准 SKU 负载均衡器

若要在 Azure Kubernetes Service （AKS）中提供对应用程序的访问权限，可以创建和使用 Azure 负载均衡器。 在 AKS 上运行的负载均衡器可用作内部或外部负载均衡器。 内部负载均衡器使 Kubernetes 服务仅可用于在与 AKS 群集相同的虚拟网络中运行的应用程序。 外部负载均衡器接收一个或多个公共 Ip 用于入口，并使 Kubernetes 服务可以使用公共 Ip 进行外部访问。

Azure 负载均衡器以两种 SKU 提供：“基本”和“标准”。 默认情况下，使用服务清单在 AKS 上创建负载均衡器时，将使用基本 SKU。 使用*标准*SKU 负载均衡器可提供其他特性和功能，如更大的后端池大小和可用性区域。 在选择要使用的负载均衡器之前，请务必了解*标准*负载均衡器与*基本*负载均衡器之间的差异。 创建 AKS 群集后，将无法更改该群集的负载均衡器 SKU。 有关*基本*和*标准*sku 的详细信息，请参阅[Azure 负载均衡器 SKU 比较][azure-lb-comparison]。

本文介绍如何通过 Azure Kubernetes 服务（AKS）创建和使用带有*标准*SKU 的 Azure 负载均衡器。

本文假定你基本了解 Kubernetes 和 Azure 负载均衡器的概念。 有关详细信息，请参阅[Azure Kubernetes Service （AKS）的 Kubernetes 核心概念][kubernetes-concepts]和[什么是 Azure 负载均衡器？][azure-lb]。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 版本2.0.59 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="before-you-begin"></a>开始之前

如果使用现有子网或资源组，则 AKS 群集服务主体需要管理网络资源的权限。 通常，将“网络参与者”角色分配给委派资源上的服务主体。 有关权限的详细信息，请参阅[委派 AKS 访问其他 Azure 资源][aks-sp]。

必须创建 AKS 群集，将负载均衡器的 SKU 设置为*Standard* ，而不是使用默认的*基本*值。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要使用 Azure 负载均衡器标准版 SKU，需要*aks* CLI 扩展版本0.4.12 或更高版本。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展，然后使用[az extension update][az-extension-update]命令检查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>限制

当你创建和管理支持具有*标准*SKU 的负载均衡器的 AKS 群集时，以下限制适用：

* 需要至少一个公共 IP 或 IP 前缀才能允许来自 AKS 群集的传出流量。 还需要公共 IP 或 IP 前缀来维护控制平面和代理节点之间的连接，以及保持与早期版本的 AKS 的兼容性。 你可以使用以下选项，通过*标准*SKU 负载均衡器指定公共 IP 或 IP 前缀：
    * 提供自己的公共 Ip。
    * 提供自己的公共 IP 前缀。
    * 指定一个最大为100的数字，以允许 AKS 群集在创建为 AKS 群集的同一资源组中创建许多*标准*SKU 公共 ip，这通常在开头使用*MC_* 命名为。 AKS 将公共 IP 分配给*标准*SKU 负载均衡器。 默认情况下，如果未指定公共 IP、公共 IP 前缀或 Ip 数量，则会在与 AKS 群集相同的资源组中自动创建一个公共 IP。 还必须允许公用地址，并避免创建 ban IP 创建的任何 Azure 策略。
* 为负载均衡器使用*标准*SKU 时，必须使用 Kubernetes 版本1.13 或更高版本。
* 仅在创建 AKS 群集时，才能定义负载平衡器 SKU。 创建 AKS 群集后，无法更改负载平衡器 SKU。
* 单个群集中只能使用一个负载平衡器 SKU。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑组。 创建资源组时，系统会要求你指定一个位置， 此位置是资源组元数据的存储位置，如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。 使用 [az group create][az-group-create] 命令创建资源组。

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

以下示例输出显示已成功创建资源组：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>创建 AKS 群集
若要运行支持具有*标准*SKU 的负载均衡器的 AKS 群集，群集需要将*负载平衡器 SKU*参数设置为 "*标准*"。 创建群集时，此参数将使用*标准*SKU 创建负载均衡器。 在群集上运行*LoadBalancer*服务时，将使用该服务的配置来更新*标准*SKU 负载均衡器的配置。 使用[az aks create][az-aks-create]命令创建名为*myAKSCluster*的 aks 群集。

> [!NOTE]
> *负载平衡器 sku*属性只能在创建群集时使用。 创建 AKS 群集后，无法更改负载平衡器 SKU。 此外，在单个群集中只能使用一种类型的负载均衡器 SKU。
> 
> 如果要使用自己的公共 Ip，请使用*负载均衡器-出站* *ip 或负载平衡器-ip 前缀*参数。 在[更新群集](#optional---provide-your-own-public-ips-or-prefixes-for-egress)时，还可以使用这两个参数。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。

## <a name="connect-to-the-cluster"></a>连接至群集

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl][kubectl]。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 若要在本地安装 `kubectl`，请使用 [az aks install-cli][az-aks-install-cli] 命令：

```azurecli
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要验证到群集的连接，请使用 [kubectl get][kubectl-get] 命令返回群集节点的列表。

```azurecli-interactive
kubectl get nodes
```

以下示例输出显示在上一步创建的单个节点。 请确保节点的状态为 *Ready*：

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.10
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>验证群集是否使用*标准*SKU

使用[az aks show][az-aks-show]显示群集的配置。

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

验证*loadBalancerSku*属性是否显示为*标准*。

## <a name="use-the-load-balancer"></a>使用负载均衡器

若要在群集上使用负载均衡器，请使用服务类型*LoadBalancer*创建服务清单。 若要显示负载均衡器的工作情况，请创建另一个清单，其中包含要在群集上运行的示例应用程序。 此示例应用程序通过负载均衡器公开，并可通过浏览器进行查看。

创建一个名`sample.yaml`为的清单，如以下示例中所示：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
```

上面的清单配置了两个部署： *azure-投票*和*azure 投票*。 若要配置使用负载均衡器公开的*azure 投票前*部署，请创建一个名`standard-lb.yaml`为的清单，如以下示例中所示：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

服务*azure 投票*使用*LOADBALANCER*类型在 AKS 群集上配置负载均衡器，以连接到*azure 投票*的部署。

使用[kubectl apply][kubectl-apply]部署示例应用程序和负载均衡器，并指定 YAML 清单的名称：

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

*标准*SKU 负载均衡器现已配置为公开示例应用程序。 使用[kubectl get][kubectl-get]查看*azure*的服务详细信息，查看负载均衡器的公共 IP。 负载均衡器的公共 IP 地址显示在 "*外部 ip* " 列中。 IP 地址可能需要一到两分钟的时间才能从 *\<"挂起\>* " 更改为实际的外部 IP 地址，如以下示例中所示：

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

在浏览器中导航到公共 IP，并验证是否看到该示例应用程序。 在上面的示例中，公共 IP 为`52.179.23.131`。

![浏览到 Azure Vote 的图像](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> 你还可以将负载均衡器配置为内部负载均衡器，而不是公开公共 IP。 若要将负载均衡器配置为内部`service.beta.kubernetes.io/azure-load-balancer-internal: "true"`负载均衡器，请将作为批注添加到*LoadBalancer*服务。 可在[此处][internal-lb-yaml]查看示例 yaml 清单以及有关内部负载均衡器的更多详细信息。

## <a name="optional---scale-the-number-of-managed-public-ips"></a>可选-缩放托管的公共 Ip 数量

当使用*标准*SKU 负载平衡器以及默认情况下创建的托管出站公共 ip 时，可以使用*负载平衡器托管 ip 计数*参数来扩展托管出站公共 ip 的数量。

若要更新现有群集，请运行以下命令。 还可以在创建群集时将此参数设置为具有多个托管出站公共 Ip。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

上面的示例将*myResourceGroup*中*myAKSCluster*群集的托管出站公共 ip 数设置为*2* 。 

你还可以在创建群集时使用*负载平衡器托管 ip 计数*参数设置初始的托管出站公共 ip 数，方法是附加`--load-balancer-managed-outbound-ip-count`参数并将其设置为所需的值。 托管出站公共 Ip 的默认数量为1。

## <a name="optional---provide-your-own-public-ips-or-prefixes-for-egress"></a>可选-提供自己的公共 Ip 或传出的前缀

使用*标准*sku 负载平衡器时，AKS 群集会自动在为 AKS 群集创建的同一资源组中创建公共 ip，并将公共 ip 分配到*标准*SKU 负载均衡器。 或者，可以在创建群集时分配自己的公共 IP，也可以更新现有群集的负载均衡器属性。

通过引入多个 IP 地址或前缀，可以在定义单个负载均衡器对象后面的 IP 地址时定义多个支持服务。 特定节点的出口终结点将取决于与之关联的服务。

> [!IMPORTANT]
> 你必须将*标准*Sku 公共 Ip 与*标准*sku 一起用于你的负载均衡器。 可以使用[az network 公共 ip show][az-network-public-ip-show]命令验证公共 IP 的 SKU：
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

使用[az network 公共 ip show][az-network-public-ip-show]命令列出公共 Ip 的 id。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

上述命令显示*myResourceGroup*资源组中*myPublicIP*公共 IP 的 ID。

将*az aks update*命令与*负载均衡器--ip*参数一起使用，以使用公共 ip 更新群集。

下面的示例将*负载均衡器-出站 ip*参数与上一个命令的 id 结合使用。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

还可以通过*标准*SKU 负载均衡器将公共 IP 前缀用于传出。 下面的示例使用[az network 公共 ip prefix show][az-network-public-ip-prefix-show]命令列出公共 ip 前缀的 id：

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

上述命令显示*myResourceGroup*资源组中的*myPublicIPPrefix*公共 IP 前缀的 ID。

下面的示例将*负载均衡器--ip 前缀*参数与上一个命令的 id 结合使用。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> 公共 Ip 和 IP 前缀必须与 AKS 群集位于同一个订阅中。

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>在创建群集时定义自己的公共 IP 或前缀

你可能想要在创建群集时引入自己的 IP 地址或 IP 前缀，以支持允许列表出口终结点等方案。 将上面显示的相同参数附加到群集创建步骤，在群集的生命周期开始时定义自己的公共 Ip 和 IP 前缀。

在开始时，使用*az aks create*命令和*负载均衡器--ip*参数来创建一个具有公共 ip 的新群集。

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

使用*az aks create*命令和*负载均衡器-* ------------

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>清理标准 SKU 负载平衡器配置

若要删除示例应用程序和负载均衡器配置，请使用[kubectl 删除][kubectl-delete]：

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>后续步骤

在 [Kubernetes 服务文档][kubernetes-services]中详细了解 Kubernetes 服务。

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

